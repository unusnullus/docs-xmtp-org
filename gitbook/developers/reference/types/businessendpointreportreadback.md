# BusinessEndpointReportReadback

The endpoint report currently visible for a business connection. Reported capabilities describe the endpoint and do not grant permissions.

You receive one of the following forms. Required fields apply within each form.

**When `app_version` has the type null, `capabilities` has the type null, `report_revision` has the type null, and `state` is `"missing"`, `"stale"`, or `"unknown"`:**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `app_version` | null | Yes | The application version reported by the endpoint, or null when report data is unavailable. |
| `capabilities` | null | Yes | The capability names reported by the endpoint, or null when report data is unavailable. These names do not grant permissions. |
| `report_revision` | null | Yes | The recorded report revision as a decimal string, or null when report data is unavailable. |
| `state` | `"missing"` or `"stale"` or `"unknown"` | Yes | The report status: recorded for current data, missing when no report exists, stale when the report is no longer current, or unknown when its current status cannot be established. |

**When `app_version` has the type string, `capabilities` has the type Array of string, `report_revision` has the type string, and `state` is `"recorded"`, `"stale"`, or `"unknown"`:**

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `app_version` | string | Yes | The application version reported by the endpoint, or null when report data is unavailable. The value must match `^[!-~]+$`. The minimum length is 1 character. The maximum length is 128 characters. |
| `capabilities` | Array of string | Yes | The capability names reported by the endpoint, or null when report data is unavailable. These names do not grant permissions. The maximum is 128 items. Each item must be unique. |
| `capabilities[]` | string | Per item | The value must match `^[A-Za-z0-9_.-]+$`. The minimum length is 1 character. The maximum length is 64 characters. |
| `report_revision` | string | Yes | The recorded report revision as a decimal string, or null when report data is unavailable. The value must match `^[1-9][0-9]{0,19}$`. |
| `state` | `"recorded"` or `"stale"` or `"unknown"` | Yes | The report status: recorded for current data, missing when no report exists, stale when the report is no longer current, or unknown when its current status cannot be established. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
