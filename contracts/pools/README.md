# curve-contract/contracts/pools

Deployed Curve pool contracts.

## Subdirectories

Each subdirectory holds contracts and other files specific to a single Curve pool.

* [`3pool`](3pool): [Tri-pool](https://www.curve.fi/3pool)
* [`busd`](busd): [BUSD pool](https://www.curve.fi/busd), with lending on [yearn.finance](https://yearn.finance/)
* [`compound`](compound): [Compound pool](https://www.curve.fi/compound), with lending on [Compound](https://compound.finance/)
* [`gusd`](3pool): [GUSD metapool](https://www.curve.fi/gusd)
* [`hbtc`](hbtc): [hBTC pool](https://www.curve.fi/hbtc)
* [`husd`](3pool): [HUSD metapool](https://www.curve.fi/husd)
* [`pax`](pax): [PAX pool](https://www.curve.fi/pax), with lending on [yearn.finance](https://yearn.finance/)
* [`ren`](ren): [RenBTC pool](https://www.curve.fi/ren)
* [`sbtc`](sbtc): [sBTC pool](https://www.curve.fi/sbtc)
* [`snow`](snow): [Snow pool](https://www.curve.fi/snow), for swaps between [yVault](https://feel-the-yearn.app/vaults) tokens
* [`susd`](susd): [sUSD pool](https://www.curve.fi/susdv2)
* [`usdk`](3pool): [USDK metapool](https://www.curve.fi/usdk)
* [`usdn`](3pool): [USDN metapool](https://www.curve.fi/usdn)
* [`usdt`](usdt): [USDT pool](https://www.curve.fi/usdt), with lending on [Compound](https://compound.finance/)
* [`y`](y): [Y pool](https://www.curve.fi/y), with lending on [yearn.finance](https://yearn.finance/)

## Development

### Adding a New Pool

* Contracts for a new pool should be placed in their own subdirectory using the same name as is given on the website.
* The LP token contract does not need to be included, unless it deviates from the standard contracts within [`contracts/testing`](../testing)
* Each subdirectory must contain a `pooldata.json` file using the structure given below. This is required in order to initialize fixtures when running the [test suite](../../tests).

```js
{
    "lp_contract": "CurveTokenV1",       // LP token contract to use with this pool, from `contracts/tokens`
    "wrapped_contract": "yERC20",        // mock wrapped coin contract to use, from `contracts/testing`

    // constructor settings for the LP token - required for deployment
    "lp_constructor": {
        "symbol": "",
        "name": ""
    },
    // constructor settings for the pool - required for deployment
    "swap_constructor": {
        "_A": 200,
        "_fee": 4000000,
        "_admin_fee": 0
    },
    // each list item represents 1 swappable coin within the pool
    "coins": [
        {
            // required fields
            "decimals": 18,               // number of decimal places for the underlying coin
            "tethered": false,            // does the token contract return `None` on a successful transfer/approve?
            "wrapped": true,              // is wrapping used for this coin?
            "wrapped_decimals": 18,       // decimal places for the wrapped coin - can be omitted if wrapped == false
            // optional fields
            "name": "",                   // underlying coin name
            "withdrawal_fee": 0,          // fee applied when converting wrapped to underlying, expressed in bps
            "underlying_address": "0x00", // underlying coin mainnet deployment address, used in forked tests
            "wrapped_address": "0x00"     // wrapped coin mainnet deployment address
        },
    ]
}
```

### Metapools

A metapool is a pool where a stablecoin is paired against the LP token from another pool.

The `pooldata.json` for a metapool is similar to that of a regular pool:

```js
{
    "lp_contract": "CurveContractV2",  // LP token contract to use with this pool, from `contracts/tokens`
    "base_pool": "3pool",              // Name for the related base pool
    "coins": [
        {
            // the first coin in the metapool is an unwrapped stablecoin
            "decimals": 18,
            "tethered": false,
            "wrapped": false
        },
        {
            // the second coin in the metapool is the LP token from the base pool
            "decimals": 18,
            "wrapped": false,
            "base_pool_token": true
        }
    ]

}
```
