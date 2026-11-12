# BusinessEndpointReportRecorded

Confirmation that the endpoint report was recorded, with its new revision.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `report_revision` | string | Yes | The newly recorded report revision as a decimal string. The value must match `^[1-9][0-9]{0,19}$`. |
| `state` | `"recorded"` | Yes | Always recorded after the endpoint report is accepted. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
