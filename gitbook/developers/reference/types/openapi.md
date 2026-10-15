# OpenApi

The public OpenAPI document returned by openapi.get, without the usual result envelope.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `components` | object | Yes | This contains reusable schemas and security definitions for the OpenAPI document. |
| `components.schemas` | object | No | This defines reusable JSON schemas for API requests and responses. |
| `components.securitySchemes` | object | No | This defines the authentication schemes supported by the OpenAPI document. |
| `info` | object | Yes | This contains the title and version of the OpenAPI document. |
| `info.title` | string | Yes | This is the title displayed for the object. |
| `info.version` | string | Yes | This is the version of the API described by this OpenAPI document. |
| `openapi` | `"3.1.0"` | Yes | This identifies the OpenAPI specification version used by the document. |
| `paths` | object | Yes | This defines the HTTP paths and operations published by the OpenAPI document. |
| `servers` | Array of object | No | These are the base URLs to which operation paths are relative. |
| `servers[].url` | string | Yes | The base URL of this API server. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
