---
description: "Follow user-visible changes to Nexo documentation and SDK integration."
---

# Changelog

Follow changes that affect how you build and run your bot. The Atom feed links to these entries. API versions and SDK package versions are separate. Read [Versioning & deprecation](versioning.md) before upgrading a dependency.

## November 11, 2026 <a href="#november-11-2026" id="november-11-2026"></a>

### Groups <a href="#groups" id="groups"></a>

Bots can join Supergroups and forums. The new [Bots in groups](groups.md) section covers adding your bot, group privacy, administrator rights, moderation, invite links, join requests (including the join request guard), forum topics and group history, with examples in all three SDKs.

### Mini Apps <a href="#mini-apps" id="mini-apps"></a>

Mini Apps are available. Register origins and apps in NexoCreator, open them from the menu button, keyboard buttons and `https://www.arx.pro/bot/<HANDLE>?startapp=` links, and validate launch data on your server. The [Mini Apps](mini-apps.md) section also documents the JavaScript bridge, `window.AERX.WebApp` version 1.0.

### Payments <a href="#payments" id="payments"></a>

Payments are available: sell one-time purchases and manually renewed subscriptions paid by wallet transfer. The former single page is now the [Payments](payments.md) section. Bots can opt in to `renewal_due` updates for subscription renewal reminders.

### Advanced modes <a href="#advanced-modes" id="advanced-modes"></a>

New [Advanced modes](managed-bots.md) guides cover Business connections, the Business endpoint runtime with automatic replies, managed bots and guest queries. They document the `request_managed_bot` reply button and `https://www.arx.pro/newbot/...` creation links.

### Messaging guides <a href="#messaging-guides" id="messaging-guides"></a>

Six new guides cover [sending messages](messages.md), [albums](messages/albums.md), [stickers and dice](messages/stickers-and-dice.md), [replies and quotes](messages/replies.md), [editing and deleting](messages/edit-and-delete.md), and [reactions and pins](messages/reactions-and-pins.md). Bots can send WebP, TGS and WebM stickers and dice. The guides document `mini_app` inline, reply and menu buttons, the video options `cover`, `start_seconds` and `supports_streaming`, and what Supergroups accept.

### Reference and delivery <a href="#reference-and-delivery" id="reference-and-delivery"></a>

The [API reference](api.md) now covers group moderation, topics, inline and Mini App queries, payments, managed bots and business connections, adding 67 operations. [Polling and webhooks](updates.md) lists the update types for groups, membership, join requests, payments, managed bots and connected accounts, and documents the webhook signature algorithm with a test vector. The [error catalog](errors.md) covers every public error code, and [Limits](limits.md) adds webhook, delivery, group and topic limits. The [capability map](features.md) and [Not available yet](not-available-yet.md) reflect what bots can use today. The Rust quickstart uses the `IncomingMessage` enum.

## October 15, 2026 <a href="#october-15-2026" id="october-15-2026"></a>

### Developer guides <a href="#developer-guides" id="developer-guides"></a>

You can follow the quickstart in TypeScript, Python or Rust, then use the local emulator with any of the three clients. The guides explain token reveal, request envelopes, idempotency and choosing an update transport.

Use [SDKs and CLI](sdks.md) to install the package for your language:

- TypeScript uses `@arx/nexo`.
- Python uses the distribution `arx-nexo`, imported as `arx_nexo`.
- Rust uses the crate `arx-nexo`.

### Standalone CLI <a href="#standalone-cli" id="standalone-cli"></a>

The `arx-nexo` command is a Rust application in the repository's `CLI/` directory. Install it with `cargo install --path "$ARX_SDKS_ROOT/CLI"`, or run it with `cargo run --release --manifest-path "$ARX_SDKS_ROOT/CLI/Cargo.toml" -- whoami`. It includes YAML commands and profiles, webhook tools, signature verification and the local emulator. Python is no longer required to run the CLI or emulator.

Use `arx-nexo new DIR --lang typescript`, `--lang python` or `--lang rust` to create an echo project configured for the emulator. `arx-nexo emulator-token` prints its synthetic token. Python tests can keep using `Emulator(port=0)` after installing the CLI; the helper finds the executable on `PATH` or through `ARX_NEXO_CLI`.

The Python package no longer provides the `cli` extra, the `arx-nexo` console entry point or `python -m arx_nexo`. Install the [standalone CLI](sdks.md#cli) to use those tools.

The [capability map](features.md) points to supported features, and [Not available yet](not-available-yet.md) collects features outside the current Bot API. The [API reference](api.md) groups operations and links to their types.

## What appears here <a href="#what-appears-here" id="what-appears-here"></a>

Entries describe changes to public behavior, request or response fields, SDK integration and developer guidance. A breaking change or deprecation entry identifies the affected feature and the action you need to take.

Continue with [Versioning & deprecation](versioning.md) to plan an upgrade, or return to the [quickstart](quickstart.md) to build a bot.
