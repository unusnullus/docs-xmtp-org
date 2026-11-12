# InvoiceLink

The original reusable invoice link returned after acceptance.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `invoice_id` | string | Yes | The invoice identifier assigned to this link. |
| `slug` | string | Yes | The opaque link identifier. |
| `url` | string | Yes | The reusable checkout link for this invoice. The format is `uri`. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
