### List all Assets

> List all Assets

```
curl -X GET https://huoju.ink/api/pro/v1/assets
```

> Sample Response 

```json
{
    "code": 0,
    "data": [
        {
            "assetCode":        "BTC",
            "assetName":        "Bitcoin Token",
            "positionScale":     9,
            "nativeScale":       2,
            "withdrawalFee":     11,
            "minWithdrawalAmt":  22,
            "status":           "Normal"
        }
    ]
}
```

#### HTTP Request

`GET /api/pro/v1/assets`

You can obtain a list of all assets listed on the exchange through this API.

#### Response Content

 Name               | Type     | Description                                                                                 
------------------- | -------- | --------------------- 
 `assetCode`        | `String` | asset code. e.g. `"BTC"`
 `assetname`        | `String` | full name of the asset, e.g. `"Bitcoin Token"`
 `minWithdrawalAmt` | `String` | minimum amount required for the withdrawal request e.g. "10.0"
 `nativeScale`      | `Int`    | scale used in deposit/withdraw transaction from/to chain. 
 `positionScale`    | `Int`    | scale used in internal position keeping.
 `withdrawFee`      | `String` | fee charged for each withdrawal request. e.g. "0.01"
 `status`           | `String` | values: `Normal`, `NoDeposit`, `NoWithdraw`, `NoTransaction`
