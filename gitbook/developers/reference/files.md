# Files

Upload reusable files, inspect their processing state, and download accessible bytes.

Each route uses the configured API base URL. [Shared request rules and errors](../api.md#shared-request-rules) apply to every operation on this page.

| Operation | HTTP route |
| --- | --- |
| [`files.download`](#operation-files-download) | `GET /file_contents/{file_id}` |
| [`files.upload`](#operation-files-upload) | `POST /files` |
| [`files.get`](#operation-files-get) | `GET /files/{file_id}` |

## files.download <a href="#operation-files-download" id="operation-files-download"></a>

`GET /bot-api/v1/file_contents/{file_id}`

Download an accessible file with your bot token. The response contains the original file bytes.

The complete file must be at most 20 MiB (20,971,520 bytes), even when you request a small range. A full download returns HTTP 200. A single supported byte range returns HTTP 206 with `Content-Range`. An unsupported or unsatisfiable range returns HTTP 416 with an empty body and `Content-Range: bytes */size`. Do not send query parameters or a request body.

### Parameters <a href="#parameters" id="parameters"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `file_id` | path | string | Yes | Use an opaque file ID available to your bot. Supply file_id, rather than file_unique_id. The minimum length is 1 character. The maximum length is 2,048 characters. |
| `Range` | header | string | No | Request one byte range, an open-ended range, or a suffix. The complete original must be at most 20 MiB, even when you request a smaller range. The value must match `^bytes=(?:[0-9]+-[0-9]*\|-[0-9]+)$`. |

### Request <a href="#request" id="request"></a>

Do not send a request body.

### Response <a href="#response" id="response"></a>

| HTTP | Success body | Response headers |
| --- | --- | --- |
| 200 | [Binary](types/binary.md) | `Accept-Ranges`: The endpoint accepts a single byte range. `Cache-Control`: The response must not be stored in a cache. `Content-Disposition`: Attachment filename, when applicable. `Content-Length`: Number of bytes in the response body. The minimum is 0. `Content-Type`: MIME type of the original file. |
| 206 | [Binary](types/binary.md) | `Accept-Ranges`: The endpoint accepts a single byte range. `Cache-Control`: The response must not be stored in a cache. `Content-Disposition`: Attachment filename, when applicable. `Content-Length`: Number of bytes in the response body. The minimum is 0. `Content-Range`: Returned byte range and total original size, in the form bytes start-end/size. `Content-Type`: MIME type of the original file. |

### Errors <a href="#errors" id="errors"></a>

HTTP 400 `download_too_large` means you must use metadata-only access for this file. HTTP 404 `file_not_found` means the file is missing or inaccessible. See [Files](../files.md) for ranges and download helpers.

### Example <a href="#example" id="example"></a>

Set `ARX_BOT_TOKEN`, `DOWNLOAD_PATH`, `FILE_ID` before running this example. Set `DOWNLOAD_PATH` to the output file you want to create.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, DownloadTarget } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const downloadPath = process.env.DOWNLOAD_PATH;
const fileId = process.env.FILE_ID;
if (!arxBotToken || !downloadPath || !fileId) {
  throw new Error("Set ARX_BOT_TOKEN, DOWNLOAD_PATH, FILE_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const written = await bot.filesDownload(fileId, DownloadTarget.fromPath(downloadPath), { rangeHeader: "bytes=0-1023" });
  console.log(written);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, DownloadTarget

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    written = bot.files_download(os.environ["FILE_ID"], DownloadTarget.from_path(os.environ["DOWNLOAD_PATH"]), range_header="bytes=0-1023")
    print(written)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, DownloadTarget };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let download_path = std::env::var("DOWNLOAD_PATH")?;
    let file_id = std::env::var("FILE_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let written = bot.files_download(&file_id, DownloadTarget::from_path(&download_path), Some("bytes=0-1023"), None).await?;
    println!("{:?}", written);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 3600 -X GET \
  "https://signal.arx.pro/bot-api/v1/file_contents/${FILE_ID}" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Range: bytes=0-1023" \
  --output "${DOWNLOAD_PATH}"
```

{% endtab %}

{% endtabs %}

A supported range returns HTTP 206 with `Content-Range` and the requested file bytes. The SDK methods return the number of bytes written.

## files.upload <a href="#operation-files-upload" id="operation-files-upload"></a>

`POST /bot-api/v1/files`

Upload a file to your bot's library so you can reuse its file ID in messages.

Send a JSON `payload` part with the file's `kind` and optional `file_name`, plus a binary part named `file`. Uploads can require processing. Check `files.get` with `metadata_only: true` until the file is ready before sending it. Attach message thumbnails to the message request itself.

### Parameters <a href="#parameters-1" id="parameters-1"></a>

This operation has no additional parameters. See the [shared request rules](../api.md#shared-request-rules) for headers.

### Request <a href="#request-1" id="request-1"></a>

Content type: `multipart/form-data`. You must include the body.

Send the upload settings in the JSON `payload` part and the file bytes in a binary part named `file`. This operation does not accept thumbnails. See [Files](../files.md) for the limits for each file kind. Let your HTTP library set the multipart boundary.

The request deadline is 3,600 seconds.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `file` | string | Yes | The file bytes. Send this as a binary multipart part named file. The format is `binary`. |
| `payload` | [UploadFile](types/uploadfile.md) | Yes | Send the JSON operation body in the payload part of a multipart request. |

### Response <a href="#response-1" id="response-1"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [File](types/file.md) in `result` |

### Errors <a href="#errors-1" id="errors-1"></a>

HTTP 413 `file_too_large` includes the type's byte limit in `error.details.limit`. HTTP 400 `file_type_not_allowed` or `file_type_mismatch` means you should check the file's kind and actual content. HTTP 429 `file_quota_exceeded` or `file_capacity_exhausted` requires the recovery described in [Errors](../errors.md). See [Files](../files.md) for each kind's limits.

### Example <a href="#example-1" id="example-1"></a>

Set `ARX_BOT_TOKEN`, `FILE_PATH`, `REQUEST_KEY` before running this example. Set `FILE_PATH` to a PDF document you own.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client, type Models, UploadSource } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const filePath = process.env.FILE_PATH;
const requestKey = process.env.REQUEST_KEY;
if (!arxBotToken || !filePath || !requestKey) {
  throw new Error("Set ARX_BOT_TOKEN, FILE_PATH, REQUEST_KEY");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const body: Models.UploadFile = {
    "kind": "document",
    "file_name": "sample.pdf"
  };
  const result = await bot.filesUpload(UploadSource.fromPath(filePath, { contentType: "application/pdf" }), { body, key: requestKey });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config, models, UploadSource

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    body = models.UploadFile(kind='document', file_name='sample.pdf')
    result = bot.files_upload(UploadSource.from_path(os.environ["FILE_PATH"], content_type='application/pdf'), body=body, key=os.environ["REQUEST_KEY"])
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config, models, UploadSource };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let file_path = std::env::var("FILE_PATH")?;
    let request_key = std::env::var("REQUEST_KEY")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let body = models::UploadFile {
        file_name: Some("sample.pdf".to_owned()),
        ..models::UploadFile::new(models::UploadFileKind::Document)
    };
    let source = UploadSource::from_path(&file_path)?.with_content_type("application/pdf")?;
    let result = bot.files_upload(source, &body, Some(&request_key)).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 3600 -X POST \
  "https://signal.arx.pro/bot-api/v1/files" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}" \
  -H "Idempotency-Key: ${REQUEST_KEY}" \
  -F 'payload={"kind": "document", "file_name": "sample.pdf"};type=application/json' \
  -F "file=@${FILE_PATH};type=application/pdf"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "file_id": "rt1.c2FtcGxlLWRvY3VtZW50.Ir2wneC_Vb9figjCW6xqWn_HDRYeTJovqODOrRv9Owk",
    "file_size": 1024,
    "file_unique_id": "z_nxueD-UFpG2bnk2mxCZ3C4nfDl8JDiIqtBk4W5VYI",
    "kind": "document",
    "mime_type": "application/pdf",
    "state": "ready",
    "file_name": "sample.pdf"
  }
}
```

## files.get <a href="#operation-files-get" id="operation-files-get"></a>

`GET /bot-api/v1/files/{file_id}`

Read an accessible file's processing state and metadata. A ready file within the download limit can also include a signed download URL.

Use `metadata_only: true` when you only need state or metadata, including for files above 20 MiB. After an upload, poll metadata until the state is `ready`, or use your SDK's file-ready helper. A signed URL is valid for at least one hour; access can still end if your bot loses permission.

### Parameters <a href="#parameters-2" id="parameters-2"></a>

| Parameter | Location | Type | Required | Description and constraints |
| --- | --- | --- | --- | --- |
| `file_id` | path | string | Yes | Use an opaque file ID available to your bot. Supply file_id, rather than file_unique_id. The minimum length is 1 character. The maximum length is 2,048 characters. |
| `metadata_only` | query | boolean | No | Set to true to retrieve file state and metadata without a download URL. You can use this for files larger than 20 MiB. The default is `false`. |

### Request <a href="#request-2" id="request-2"></a>

Do not send a request body.

### Response <a href="#response-2" id="response-2"></a>

| HTTP | Success body |
| --- | --- |
| 200 | JSON envelope with [File](types/file.md) in `result` |

### Errors <a href="#errors-2" id="errors-2"></a>

HTTP 400 `download_too_large` means you should repeat the metadata request with `metadata_only: true`. HTTP 404 `file_not_found` means the file is missing or inaccessible. Continue with [Files](../files.md) for processing states and download methods.

### Example <a href="#example-2" id="example-2"></a>

Set `ARX_BOT_TOKEN`, `FILE_ID` before running this example.

{% tabs %}

{% tab title="TypeScript" %}

```ts
import { Client } from "@arx/nexo";

const arxBotToken = process.env.ARX_BOT_TOKEN;
const fileId = process.env.FILE_ID;
if (!arxBotToken || !fileId) {
  throw new Error("Set ARX_BOT_TOKEN, FILE_ID");
}

const bot = new Client({ token: arxBotToken, baseUrl: "https://signal.arx.pro/bot-api/v1" });
try {
  const result = await bot.filesGet(fileId, { query: {
    "metadata_only": true
  } });
  console.log(result);
} finally {
  await bot.close();
}
```

{% endtab %}

{% tab title="Python" %}

```python
import os
from arx_nexo import Client, Config

with Client(Config(token=os.environ["ARX_BOT_TOKEN"], base_url="https://signal.arx.pro/bot-api/v1")) as bot:
    result = bot.files_get(os.environ["FILE_ID"], metadata_only=True)
    print(result)
```

{% endtab %}

{% tab title="Rust" %}

```rust
use arx_nexo::{ Client, Config };

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let arx_bot_token = std::env::var("ARX_BOT_TOKEN")?;
    let file_id = std::env::var("FILE_ID")?;
    let bot = Client::new(Config::new(arx_bot_token, "https://signal.arx.pro/bot-api/v1", false)?)?;
    let query = [("metadata_only", "true")];
    let result = bot.files_get(&file_id, &query, None, None).await?;
    println!("{:?}", result);
    Ok(())
}
```

{% endtab %}

{% tab title="HTTP" %}

```sh
curl --fail-with-body --max-time 20 -X GET \
  "https://signal.arx.pro/bot-api/v1/files/${FILE_ID}?metadata_only=true" \
  -H "Authorization: Bearer ${ARX_BOT_TOKEN}"
```

{% endtab %}

{% endtabs %}

Example success response (HTTP 200). The SDK methods return `result`:

```json
{
  "ok": true,
  "result": {
    "file_id": "rt1.c2FtcGxlLWRvY3VtZW50.Ir2wneC_Vb9figjCW6xqWn_HDRYeTJovqODOrRv9Owk",
    "file_size": 1024,
    "file_unique_id": "z_nxueD-UFpG2bnk2mxCZ3C4nfDl8JDiIqtBk4W5VYI",
    "kind": "document",
    "mime_type": "application/pdf",
    "state": "ready",
    "file_name": "sample.pdf"
  }
}
```

Continue with the [API index](../api.md) to find another operation or the [type index](types.md) to inspect a field.
