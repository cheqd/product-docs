# Cheqd x Cosmos Data APIs

## Summary

Cosmos SDK offers [APIs for built-in modules using gRPC, REST, and Tendermint RPC](https://docs.cosmos.network/v0.47/core/grpc_rest#comparison-table). This project aims to provide simple REST APIs for data that default Cosmos SDK APIs can't provide.

This collection of custom APIs can be deployed as a [Cloudflare Worker](https://workers.cloudflare.com/) or compatible serverless platforms.

## API Endpoints & Features

### Total Supply

#### Endpoints

[`data-api.cheqd.io/supply/total`](https://data-api.cheqd.io/supply/total) (also has an API endpoint alias on `/`)

#### Response

_Just_ total supply of tokens, in main token denomination (CHEQ instead of `ncheq` in our case)

#### Rationale

Cryptocurrency tracking websites such as [CoinMarketCap](https://coinmarketcap.com/currencies/cheqd/) and [CoinGecko](https://www.coingecko.com/en/coins/cheqd-network) require an API endpoint for reporting the total supply of tokens in the main/primary token denomination.

While this figure is available from Cosmos SDK's built-in [`/cosmos/bank/v1beta1/supply/ncheq`](https://api.cheqd.net/cosmos/bank/v1beta1/supply/ncheq) REST endpoint, this returns a JSON object in the lowest token denomination, which cannot be parsed by CoinMarketCap / CoinGecko.

### Circulating Supply

#### Endpoint

[`data-api.cheqd.io/supply/circulating`](https://data-api.cheqd.io/supply/circulating)

#### Response

Circulating token supply, in main token denomination (CHEQ instead of _ncheq_ in our case)

#### Rationale

Cryptocurrency tracking websites such as [CoinMarketCap](https://coinmarketcap.com/currencies/cheqd/) and [CoinGecko](https://www.coingecko.com/en/coins/cheqd-network) require an API endpoint for reporting the circulating supply of tokens in the main/primary token denomination.

This figure is _not_ available from any Cosmos SDK API, because the [criteria for determining circulating vs "non-circulating" accounts is defined by CoinMarketCap](https://support.coinmarketcap.com/hc/en-us/articles/360043396252-Supply-Circulating-Total-Max-).

This API calculates the circulating supply by **subtracting** the account balances of a defined list of wallet addresses ("circulating supply watchlist") from the total supply.

### Total staked supply

#### Endpoints

[`data-api.cheqd.io/supply/staked`](https://data-api.cheqd.io/supply/staked)

#### Response

Overall tokens staked, in CHEQ.

#### Rationale

Provides the overall amount staked pulled from the block explorer.

### Vesting Account Balance

#### Endpoint

[`data-api.cheqd.io/balances/vesting/<address>`](https://data-api.cheqd.io/balances/vesting/cheqd17wrwqxsfwy4rqlltjhj6jxtz68tvxm0ykge5dr)

#### Response

Tokens that are still vesting for continuous/delayed vesting accounts, in CHEQ.

#### Rationale

There is no Cosmos SDK API that returns balances that are yet to be vested for [continuous or delayed vesting accounts](https://docs.cosmos.network/v0.45/modules/auth/05_vesting.html#vesting-account-types).

### Vested Account Balance

#### Endpoint

[`data-api.cheqd.io/balances/vested/<address>`](https://data-api.cheqd.io/balances/vesting/cheqd17wrwqxsfwy4rqlltjhj6jxtz68tvxm0ykge5dr)

#### Response

Tokens that have already vested for continuous/delayed vesting accounts, in CHEQ.

#### Rationale

There is no Cosmos SDK API that returns balances that are already vested for [continuous or delayed vesting accounts](https://docs.cosmos.network/v0.45/modules/auth/05_vesting.html#vesting-account-types).

### Liquid Account Balance

#### Endpoint

[`data-api.cheqd.io/balances/liquid/<address>`](https://data-api.cheqd.io/balances/vesting/cheqd17wrwqxsfwy4rqlltjhj6jxtz68tvxm0ykge5dr)

#### Response

Tokens in continuous/delayed vesting accounts that can be converted to liquid balances, in CHEQ.

#### Rationale

Tokens in [continuous or delayed vesting accounts](https://docs.cosmos.network/v0.45/modules/auth/05_vesting.html#vesting-account-types) that can be converted to liquid balances. This is calculated as the sum of the following figures:

1. "Delegated free" balance (from the `/cosmos/auth/v1beta1/accounts/<address>` REST API) _or_ vested balance, whichever is higher
2. "Available" balance (if applicable)
3. "Reward" balance (if applicable)

### Total Account Balance

#### Endpoint

[`data-api.cheqd.io/balances/total/<address>`](https://data-api.cheqd.io/balances/total/cheqd17wrwqxsfwy4rqlltjhj6jxtz68tvxm0ykge5dr)

#### Response

Total account balance for specified account, in CHEQ.

#### Rationale

The standard Cosmos SDK REST API for account balances returns JSON with the account balances along with its denomination, usually the lowest denomination. This is hard to parse in applications such as Google Sheets (e.g., to monitor the account balance by fetching a response from a REST API directly in Google Sheets). This API returns a plain number that can be directly plugged into such applications, without having to parse JSON.

## Developer Guide

### Architecture

This frontend site was developed to work with [Cloudflare Workers](https://workers.cloudflare.com/), a serverless and highly-scalable platform.

Originally, this project was discussed as potentially being deployed using a serverless platform such as [AWS Lambda](https://aws.amazon.com/lambda/). However, [AWS Lambda has a cold-start problem](https://mikhail.io/serverless/coldstarts/aws/) if the API doesn't receive too much traffic or is only accessed infrequently. This can lead to start times ranging into single/double digit seconds, which would be considered an API timeout by many client applications.

Using Cloudflare Workers, these APIs can be served in a highly-scalable fashion and have much lower cold-start times, i.e., in the range of less than 10 milliseconds.
