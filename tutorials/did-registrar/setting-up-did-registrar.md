# Setting up DID Registrar

## Setting up cheqd DID Registrar

This page describes how to use and set up the [cheqd DID Registrar](https://github.com/cheqd/did-registrar) software package.

If you do not want to install anything and just want to test it using our testnet, you can load the REST API endpoint for [registrar](https://did-registrar-staging.cheqd.net) in your browser.

You should run your own registrar to perform DID Operations on the Mainnet.

## Setup

### Environment variable configuration

Environment variables needed for the Registrar are

1. `FEE_PAYER_TESTNET_MNEMONIC` : The cosmos payer mnemonic for the Cheqd Mainnet
2. `FEE_PAYER_MAINNET_MNEMONIC` : The cosmos payer mnemonic for the Cheqd Tesnet, By default it's the Testnet Faucet
3. `LOCAL_STORE_TTL` (default: `600`): The time in seconds for the registrar to store data in cache
4. `PORT` (default: `3000`): The port number

Clone the repository

```bash
git clone git@github.com:cheqd/did-registrar.git
cd did-registrar
```

***

## Running a DID Registrar Using Docker

Build Docker container image using Dockerfile:

```bash
docker build --target cheqd-did-registrar . --tag did-registrar:local
```

Run the Docker container (modify according to your own build tags and other desired parameters):

```bash
docker run -it did-registrar:local
```

***

## Running a DID Registrar Locally

```bash
npm install
npm run build
npm start 
```
