# MerchantBalance

Current holdings at the merchant's payout wallet, observed on chain within the last 30 seconds.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `balances` | Array of [MerchantAssetBalance](merchantassetbalance.md) | Yes | Observed chain balances by network and asset. Missing observations are never replaced with zeros. The maximum is 128 items. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).
