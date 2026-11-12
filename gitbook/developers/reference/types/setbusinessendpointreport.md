# SetBusinessEndpointReport

The endpoint version and capabilities to record for a business connection, conditional on the previous report revision.

Only the listed fields are allowed.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `app_version` | string | Yes | The application version reported by the endpoint. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `business_connection_id` | string | Yes | The business connection whose endpoint report is being recorded. The value must match `^[A-Za-z0-9_-]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `capabilities` | Array of string | Yes | The capability names reported by the endpoint. These names do not grant permissions. The maximum is 128 items. Each item must be unique. |
| `capabilities[]` | string | Per item | The value must match `^[A-Za-z0-9_.-]+$`. The minimum length is 1 character. The maximum length is 64 characters. |
| `expected_report_revision` | null or string | Yes | Required null for the first report, otherwise a canonical uint64 decimal below 18446744073709551615. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
