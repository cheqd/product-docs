# Provide a Custom Fee

In all the transactions a custom cosmos transaction fee can be provided according to instructions below

## Parameters

* amount: An array of coins, coins are represented as an object with 2 fields
    * denom: Identifier of the coins
    * amount: The number of coins
* gas: Each transaction must specify the maximum amount of gas it may consume.
* payer (optional): The cosmos fee payer address
* granter (optional): The cosmos fee granter address, Provided the grantee has an allowance by the granter

## Example

```json
fee : {
    "amount": [
        {
            "denom": "ncheq",
            "amount": "5000000000"
        }
    ],
    "gas": "400000",
    "payer": "cheqd1rnr5jrt4exl0samwj0yegv99jeskl0hsxmcz96"
}
```
