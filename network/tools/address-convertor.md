# Cosmos Address Convertor

> 🛠️ **Github repository**: [cheqd/cosmjs-cli-converter](https://github.com/cheqd/cosmjs-cli-converter)

There is an assumption in the Cosmos Ecosystem that wallet addresses across different chains, such as, Cosmos (ATOM), Osmosis (OSMO) and cheqd (CHEQ) are all identical. This is because they all look very similar. However, each chain’s wallet address is actually unique.

Interestingly, each network’s wallet address can be derived using a common derivation path from the Cosmos Hub wallet address. Using one derivation path #BIP44 means that users that use one secret recovery phrase and core account to interact with multiple networks.

Our cross-chain address convertor is able to **automate the derivation of any chain address from one Cosmos address to another in bulk**. We’ve seen some examples of this previously, but they are mostly designed to do one-off conversions in a browser rather than large-scale batch conversions. Emphatically, our converter could do 200k+ addresses in a few minutes. Doing this using any existing CLI tools or shell scripts can take hours.

### **Why is this valuable?**

This is valuable since it can automate airdrops or distributions to any account, just from a Cosmos Hub address in bulk, making data calculations far more efficient.

For new chains in the Cosmos Ecosystem, this makes it much easier for the core team and Cosmonauts to discover and utilise their account addresses and carry out distributions.
