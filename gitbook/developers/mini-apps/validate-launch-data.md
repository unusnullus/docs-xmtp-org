---
description: "Verify Mini App launch data on your server with your bot token before you trust the user."
---

# Validate launch data

Your page receives signed launch data in `AERX.WebApp.initData`. Anyone can change what a page sends to your server, so verify the signature on your server before you use the user ID, the query ID or any other field. Verification uses your bot token, which stays on your server.

## Send launch data to your server <a href="#send-launch-data-to-your-server" id="send-launch-data-to-your-server"></a>

Send the exact `initData` string, without parsing or re-encoding it. Use HTTPS and a request body, not a URL, so the value stays out of logs.

```js
const app = window.AERX.WebApp;
await app.whenInitialized();
const response = await fetch("/api/session", {
  method: "POST",
  headers: { "Content-Type": "text/plain" },
  body: app.initData,
});
```

If the page has been open for a while, call `await app.refreshInitData()` first. It updates `initData` with a fresh signature. ARX allows one refresh per minute.

## How the signature works <a href="#how-the-signature-works" id="how-the-signature-works"></a>

`initData` is a URL-encoded list of `key=value` pairs. The `hash` field is an HMAC-SHA256 signature computed with a key derived from your bot token.

1. Split `initData` on `&`. Split each pair on its first `=`, then decode both sides as form data: `+` becomes a space, then percent escapes are decoded as UTF-8. Reject malformed escapes, invalid UTF-8, duplicate keys and keys that are not 1 to 64 lowercase letters, digits or underscores.
2. Check that `bot_id` equals the numeric part of your token before the colon.
3. Check that `auth_date` is not in the future and is at most your maximum age old. One hour (3,600 seconds) is a good default.
4. Build the data check string: remove `hash`, `signature` and `kid`, sort the remaining keys in byte order, write each pair as `key=value` with the decoded value, and join them with a newline (`\n`).
5. Derive the signing key from your full bot token:
   - `master = HMAC_SHA256(key = "AERX-BOT-SIGNING-v1", message = <bot token>)`
   - `key = HMAC_SHA256(key = master, message = "aerx/web_app/v1" + byte 0x01)`
6. Compute `HMAC_SHA256(key, data check string)` and compare it in constant time with the hex-decoded `hash`.

Only then parse JSON fields such as `user`. The `kid` and `signature` fields carry a platform signature that ARX uses internally. You do not need to verify them. Leave them out of the data check string. Keep unknown fields in the check string: new fields are signed too.

## Verification code <a href="#verification-code" id="verification-code"></a>

Each function returns the verified fields or raises an error. None of them needs a third-party library except where noted.

{% tabs %}
{% tab title="TypeScript" %}

Save as `launch-data.ts`. It uses only `node:crypto`.

```ts
import { createHmac, timingSafeEqual } from "node:crypto";

export interface LaunchUser {
	id: string;
	name: string;
	username: string;
	language_code?: string;
	[field: string]: unknown;
}

export interface VerifiedLaunch {
	botId: string;
	launchKind: string;
	launchId: string;
	authDate: number;
	user: LaunchUser;
	queryId?: string;
	startParam?: string;
	fields: Record<string, string>;
}

const SIGNATURE_FIELDS = new Set(["hash", "signature", "kid"]);

function decode(part: string): string {
	// decodeURIComponent throws on malformed escapes and invalid UTF-8.
	return decodeURIComponent(part.replace(/\+/g, " "));
}

export function verifyLaunchData(
	initData: string,
	botToken: string,
	maxAgeSeconds = 3600,
	now = Math.floor(Date.now() / 1000),
): VerifiedLaunch {
	const botId = botToken.split(":", 1)[0];
	if (!/^[1-9][0-9]*$/.test(botId) || !botToken.includes(":")) {
		throw new Error("Invalid bot token");
	}
	if (initData.length === 0 || initData.length > 65536) {
		throw new Error("Invalid launch data");
	}
	const fields: Record<string, string> = Object.create(null);
	for (const part of initData.split("&")) {
		const separator = part.indexOf("=");
		if (separator < 0) throw new Error("Invalid launch data");
		const key = decode(part.slice(0, separator));
		const value = decode(part.slice(separator + 1));
		if (!/^[a-z0-9_]{1,64}$/.test(key) || /[\r\n]/.test(value) || Object.prototype.hasOwnProperty.call(fields, key)) {
			throw new Error("Invalid launch data");
		}
		fields[key] = value;
	}
	if (fields.bot_id !== botId) throw new Error("Launch data belongs to another bot");
	const authDate = /^[0-9]{1,16}$/.test(fields.auth_date ?? "") ? Number(fields.auth_date) : Number.NaN;
	const age = now - authDate;
	if (!Number.isSafeInteger(authDate) || age < 0 || age > maxAgeSeconds) {
		throw new Error("Launch data expired");
	}
	const supplied = fields.hash ?? "";
	if (!/^[0-9a-f]{64}$/i.test(supplied)) throw new Error("Invalid launch data");
	const master = createHmac("sha256", "AERX-BOT-SIGNING-v1").update(botToken).digest();
	const key = createHmac("sha256", master).update("aerx/web_app/v1\x01").digest();
	const canonical = Object.keys(fields)
		.filter((name) => !SIGNATURE_FIELDS.has(name))
		.sort()
		.map((name) => `${name}=${fields[name]}`)
		.join("\n");
	const expected = createHmac("sha256", key).update(canonical, "utf8").digest();
	if (!timingSafeEqual(expected, Buffer.from(supplied, "hex"))) {
		throw new Error("Invalid launch data signature");
	}
	const user = JSON.parse(fields.user ?? "null") as LaunchUser | null;
	if (!user || typeof user.id !== "string" || !fields.launch_kind || !fields.launch_id) {
		throw new Error("Invalid launch data");
	}
	return {
		botId,
		launchKind: fields.launch_kind,
		launchId: fields.launch_id,
		authDate,
		user,
		queryId: fields.query_id,
		startParam: fields.start_param,
		fields,
	};
}
```

{% endtab %}
{% tab title="Python" %}

Save as `launch_data.py`. It uses only the standard library.

```python
import hashlib
import hmac
import json
import re
import time
from dataclasses import dataclass
from typing import Any
from urllib.parse import unquote_plus

_SIGNATURE_FIELDS = {"hash", "signature", "kid"}
_KEY = re.compile(r"[a-z0-9_]{1,64}")

class LaunchDataError(ValueError):
    pass

@dataclass(frozen=True)
class VerifiedLaunch:
    bot_id: str
    launch_kind: str
    launch_id: str
    auth_date: int
    user: dict[str, Any]
    query_id: str | None
    start_param: str | None
    fields: dict[str, str]

def verify_launch_data(
    init_data: str, bot_token: str, max_age_seconds: int = 3600, now: int | None = None
) -> VerifiedLaunch:
    bot_id, separator, _ = bot_token.partition(":")
    if not separator or not re.fullmatch(r"[1-9][0-9]*", bot_id):
        raise LaunchDataError("Invalid bot token")
    if not 0 < len(init_data) <= 65536:
        raise LaunchDataError("Invalid launch data")
    fields: dict[str, str] = {}
    try:
        for part in init_data.split("&"):
            raw_key, equals, raw_value = part.partition("=")
            if not equals:
                raise LaunchDataError("Invalid launch data")
            if any(re.search(r"%(?![0-9A-Fa-f]{2})", raw) for raw in (raw_key, raw_value)):
                raise LaunchDataError("Invalid launch data")
            key = unquote_plus(raw_key, errors="strict")
            value = unquote_plus(raw_value, errors="strict")
            if not _KEY.fullmatch(key) or "\r" in value or "\n" in value or key in fields:
                raise LaunchDataError("Invalid launch data")
            fields[key] = value
    except UnicodeDecodeError:
        raise LaunchDataError("Invalid launch data") from None
    if fields.get("bot_id") != bot_id:
        raise LaunchDataError("Launch data belongs to another bot")
    raw_date = fields.get("auth_date", "")
    if not re.fullmatch(r"[0-9]{1,16}", raw_date):
        raise LaunchDataError("Invalid launch data")
    auth_date = int(raw_date)
    age = (int(time.time()) if now is None else now) - auth_date
    if age < 0 or age > max_age_seconds:
        raise LaunchDataError("Launch data expired")
    supplied = fields.get("hash", "")
    if not re.fullmatch(r"[0-9a-fA-F]{64}", supplied):
        raise LaunchDataError("Invalid launch data")
    master = hmac.new(b"AERX-BOT-SIGNING-v1", bot_token.encode(), hashlib.sha256).digest()
    key = hmac.new(master, b"aerx/web_app/v1\x01", hashlib.sha256).digest()
    canonical = "\n".join(
        f"{name}={fields[name]}" for name in sorted(fields) if name not in _SIGNATURE_FIELDS
    )
    expected = hmac.new(key, canonical.encode(), hashlib.sha256).hexdigest()
    if not hmac.compare_digest(expected, supplied.lower()):
        raise LaunchDataError("Invalid launch data signature")
    try:
        user = json.loads(fields["user"])
    except (KeyError, ValueError):
        raise LaunchDataError("Invalid launch data") from None
    if not isinstance(user, dict) or not isinstance(user.get("id"), str):
        raise LaunchDataError("Invalid launch data")
    if not fields.get("launch_kind") or not fields.get("launch_id"):
        raise LaunchDataError("Invalid launch data")
    return VerifiedLaunch(
        bot_id=bot_id,
        launch_kind=fields["launch_kind"],
        launch_id=fields["launch_id"],
        auth_date=auth_date,
        user=user,
        query_id=fields.get("query_id"),
        start_param=fields.get("start_param"),
        fields=fields,
    )
```

{% endtab %}
{% tab title="Rust" %}

Save as `src/launch_data.rs`. Add `hmac = "0.12"`, `sha2 = "0.10"`, `percent-encoding = "2"` and `serde_json = "1"` to your dependencies.

```rust
use hmac::{Hmac, Mac};
use percent_encoding::percent_decode_str;
use serde_json::{Map, Value};
use sha2::Sha256;
use std::collections::BTreeMap;
use std::time::{SystemTime, UNIX_EPOCH};

#[derive(Debug)]
pub struct VerifiedLaunch {
    pub bot_id: String,
    pub launch_kind: String,
    pub launch_id: String,
    pub auth_date: u64,
    pub user: Map<String, Value>,
    pub query_id: Option<String>,
    pub start_param: Option<String>,
    pub fields: BTreeMap<String, String>,
}

#[derive(Debug)]
pub enum LaunchDataError {
    InvalidToken,
    Invalid,
    WrongBot,
    Expired,
    Signature,
}

fn decode(part: &str) -> Result<String, LaunchDataError> {
    let bytes = part.as_bytes();
    for (i, byte) in bytes.iter().enumerate() {
        if *byte == b'%'
            && (i + 2 >= bytes.len()
                || !bytes[i + 1].is_ascii_hexdigit()
                || !bytes[i + 2].is_ascii_hexdigit())
        {
            return Err(LaunchDataError::Invalid);
        }
    }
    let spaced = part.replace('+', " ");
    percent_decode_str(&spaced)
        .decode_utf8()
        .map(|value| value.into_owned())
        .map_err(|_| LaunchDataError::Invalid)
}

fn hmac(key: &[u8], data: &[u8]) -> Hmac<Sha256> {
    let mut mac = Hmac::<Sha256>::new_from_slice(key).expect("HMAC accepts any key length");
    mac.update(data);
    mac
}

pub fn verify_launch_data(
    init_data: &str,
    bot_token: &str,
    max_age_seconds: u64,
) -> Result<VerifiedLaunch, LaunchDataError> {
    let (bot_id, _) = bot_token
        .split_once(':')
        .ok_or(LaunchDataError::InvalidToken)?;
    if bot_id.is_empty() || bot_id.starts_with('0') || !bot_id.bytes().all(|b| b.is_ascii_digit()) {
        return Err(LaunchDataError::InvalidToken);
    }
    if init_data.is_empty() || init_data.len() > 65536 {
        return Err(LaunchDataError::Invalid);
    }
    let mut fields = BTreeMap::new();
    for part in init_data.split('&') {
        let (raw_key, raw_value) = part.split_once('=').ok_or(LaunchDataError::Invalid)?;
        let key = decode(raw_key)?;
        let value = decode(raw_value)?;
        let valid_key = (1..=64).contains(&key.len())
            && key
                .bytes()
                .all(|b| b.is_ascii_lowercase() || b.is_ascii_digit() || b == b'_');
        if !valid_key || value.contains(['\r', '\n']) || fields.contains_key(&key) {
            return Err(LaunchDataError::Invalid);
        }
        fields.insert(key, value);
    }
    if fields.get("bot_id").map(String::as_str) != Some(bot_id) {
        return Err(LaunchDataError::WrongBot);
    }
    let raw_date = fields.get("auth_date").ok_or(LaunchDataError::Invalid)?;
    if raw_date.is_empty() || raw_date.len() > 16 || !raw_date.bytes().all(|b| b.is_ascii_digit()) {
        return Err(LaunchDataError::Invalid);
    }
    let auth_date: u64 = raw_date.parse().map_err(|_| LaunchDataError::Invalid)?;
    let now = SystemTime::now()
        .duration_since(UNIX_EPOCH)
        .map_err(|_| LaunchDataError::Expired)?
        .as_secs();
    match now.checked_sub(auth_date) {
        Some(age) if age <= max_age_seconds => {}
        _ => return Err(LaunchDataError::Expired),
    }
    let supplied = fields.get("hash").ok_or(LaunchDataError::Invalid)?;
    if supplied.len() != 64 || !supplied.bytes().all(|b| b.is_ascii_hexdigit()) {
        return Err(LaunchDataError::Invalid);
    }
    let supplied: Vec<u8> = (0..64)
        .step_by(2)
        .map(|i| u8::from_str_radix(&supplied[i..i + 2], 16))
        .collect::<Result<_, _>>()
        .map_err(|_| LaunchDataError::Invalid)?;
    let master = hmac(b"AERX-BOT-SIGNING-v1", bot_token.as_bytes()).finalize().into_bytes();
    let key = hmac(&master, b"aerx/web_app/v1\x01").finalize().into_bytes();
    let canonical = fields
        .iter()
        .filter(|(name, _)| !matches!(name.as_str(), "hash" | "signature" | "kid"))
        .map(|(name, value)| format!("{name}={value}"))
        .collect::<Vec<_>>()
        .join("\n");
    hmac(&key, canonical.as_bytes())
        .verify_slice(&supplied)
        .map_err(|_| LaunchDataError::Signature)?;
    let user: Map<String, Value> = fields
        .get("user")
        .and_then(|raw| serde_json::from_str(raw).ok())
        .filter(|user: &Map<String, Value>| user.get("id").is_some_and(Value::is_string))
        .ok_or(LaunchDataError::Invalid)?;
    let required = |name: &str| {
        fields
            .get(name)
            .filter(|value| !value.is_empty())
            .cloned()
            .ok_or(LaunchDataError::Invalid)
    };
    Ok(VerifiedLaunch {
        bot_id: bot_id.to_owned(),
        launch_kind: required("launch_kind")?,
        launch_id: required("launch_id")?,
        auth_date,
        user,
        query_id: fields.get("query_id").cloned(),
        start_param: fields.get("start_param").cloned(),
        fields,
    })
}
```

{% endtab %}
{% endtabs %}

## After verification <a href="#after-verification" id="after-verification"></a>

- **Use the verified user.** Take the user ID from the verified `user` field, never from another field the page sends.
- **Redeem each launch once.** `launch_id` identifies one opening. `refreshInitData()` returns new launch data with the same `launch_id` and a newer `auth_date`. Keep the highest `auth_date` you accepted for each `launch_id` until your maximum age has passed. Accept launch data only when its `auth_date` is newer than that value, or when the request belongs to the session you already issued for that `launch_id`. This stops someone from replaying a captured `initData`. The signature does not do this for you.
- **Check `launch_kind`.** Accept only the entry points your operation expects. For example, an order checkout opened from a link should not accept a `keyboard_button` launch if you never send that button.
- **Treat `start_param` as input.** It is signed, but anyone can choose its value when writing a link.
- **Issue your own session.** After verification, give the page a short-lived session token from your server, and use it for later requests instead of sending `initData` every time.

If verification fails, return HTTP 401 and do not reveal which check failed.

## Token rotation <a href="#token-rotation" id="token-rotation"></a>

The signing key derives from your bot token. When you rotate the token in NexoCreator, new launches are signed with the new token, and launch data signed with the old token stops verifying. Deploy the new token to your Mini App server at the same time as your bot. See [Security](../security.md#rotate-a-token).
