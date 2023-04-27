# Setup Keplr wallet

## Overview

In this guide we'll share how to setup a Keplr wallet which you can use for paying for transactions on the cheqd network. We'll also share how to add the cheqd mainnet and testnet to your Keplr wallet.

## Setting up a Keplr Wallet on PC/Mac

1. Go to the [Keplr website](https://www.keplr.app/) to find the relevant extension for your browser. If you’re using Chrome, which we’d recommend, [click here → browser extension](https://chrome.google.com/webstore/detail/keplr/dmkamcknogkgcdfhhbddcghachkejeap)
2. Create an Account via Secret Seed/mnemonic Phrase
   1. By clicking on the Keplr browser icon for the first time will take you to the accounts setup page. Choose option Create new account.
   2. The next page shows you your secret seed/mnemonic phrase. You can select a 12 or 24-word phrase.&#x20;
   3. Save this phrase in a secure place but also do not lose it. You’ll need to input this phrase into the following page.
   4. Next, enter a name for your account and password (you can change these later). Click Next.
   5. To confirm the creation of this new account, you’ll need to click on the words in the right order in which they appear in your seed/mnemonic phrase and press Register.
   6. Congratulations! 🎉 You are now the owner of a Keplr wallet account and are ready to explore the interchain.

## Setup up a Mainnet wallet & get CHEQ

### Adding the cheqd main network to your Keplr wallet

In order to see $CHEQ in your Keplr wallet, you’ll need to follow the steps below which enables Keplr to auto-discover the cheqd network. Sign in to your Keplr wallet on your browser (do not use the mobile app).

1. Go to our dashboard on [Omniflix](https://cheqd.omniflix.co/).
2. Click “Connect” in the top right corner to link your Keplr to the dashboard.
3. You will see a pop-up that asks you to approve adding cheqd-mainnet-1 to your Keplr wallet.
4. Click “Approve”.
5. On the Keplr extension in your browser, click the drop down menu at the top. Scroll down to below “ — — Beta support — — ” where you will find the cheqd wallet.
6. You will now be able to see your balance in $CHEQ.
7. See the link here for further instructions on sending $CHEQ, staking and other information, with helpful screenshots.

### Requesting CHEQ Mainnet tokens

1. To get CHEQ tokens to pay for transactions, head to ['Get CHEQ'](https://cheqd.io/get-involved/) and select one of the options available.
2. You can then send the tokens from wherever you choose to purchase to the wallet you have setup in the previous step.
3. Simple copy the wallet address, starting `cheqd1` into your chosen option.

## Setup up a Testnet wallet & get testnet tokens

### Adding the cheqd Testnet to your Keplr wallet

1. Go to the Axelar Netork documentation to [`Add a custom network to Keplr`](https://docs.axelar.dev/resources/keplr#add-your-custom-network)
2. Scroll to `Add your custom network`
3. In space provided, paste the full contents of cheqd chain testnet information listed below

    ```json
    {
        "chainId": "cheqd-testnet-6",
        "chainName": "cheqd Testnet",
        "rpc": "https://rpc.cheqd.network",
        "rest": "https://api.cheqd.network",
        "stakeCurrency": {
            "coinDenom": "CHEQ",
            "coinMinimalDenom": "ncheq",
            "coinDecimals": 9,
            "coinGeckoId": "cheqd-network"
        },
        "walletUrlForStaking": "https://cheqd.omniflix.co/",
        "bip44": {
            "coinType": 118
        },
        "bech32Config": {
            "bech32PrefixAccAddr": "cheqd",
            "bech32PrefixAccPub": "cheqdpub",
            "bech32PrefixValAddr": "cheqdvaloper",
            "bech32PrefixValPub": "cheqdvaloperpub",
            "bech32PrefixConsAddr": "cheqdvalcons",
            "bech32PrefixConsPub": "cheqdvalconspub"
        },
        "currencies": [
            {
                "coinDenom": "CHEQ",
                "coinMinimalDenom": "ncheq",
                "coinDecimals": 9,
                "coinGeckoId": "cheqd-network"
            }
        ],
        "feeCurrencies": [
            {
                "coinDenom": "CHEQ",
                "coinMinimalDenom": "ncheq",
                "coinDecimals": 9,
                "coinGeckoId": "cheqd-network",
                "gasPriceStep": {
                    "low": 50,
                    "average": 75,
                    "high": 100
                }
            }
        ],
        "coinType": 118,
        "beta": true
    }
    ```

4. Once you've pasted the contents above, select `Validate Input & Add to Keplr`
5. You have now added the cheqd testnet to Keplr. This network uses the SAME mnemonic that was used when setting up the account\\

### Requesting CHEQ Testnet tokens

1. Go to the [cheqd testnet faucet](https://testnet-faucet.cheqd.io/)
2. Select `start`
3. Enter the cheqd wallet address you'd like to use
4. Select `continue`
5. You'll now recieve cheqd testnet tokens
6. You can also go to the [cheqd testnet explorer](https://testnet-explorer.cheqd.io/) to confirm the tokens have been sent
