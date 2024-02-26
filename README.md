# Guide to Atomic Swaps on DeFiChain

This guide aims to provide a comprehensive, step-by-step walkthrough for users looking to perform Atomic Swaps on DeFiChain. By following this guide, you'll be able to leverage DeFiChain to perform token swaps across dfferent blockchains.

## What is DeFiChain Atomic Swap?

Atomic Swaps are a feature of DeFiChain that allows for the trustless and decentralized exchange of cryptocurrencies between two different blockchains. This is achieved without the need for intermediaries.

With Atomic Swaps, DeFiChain users can exchange bitcoin, represented on the DeFiChain blockchain as DBTC (a DeFi Asset Token backed by bitcoin), for actual bitcoins on the Bitcoin blockchain. This is done directly in the DeFiChain app, eliminating the need for middlemen (such as a DEX) or centralized institutions.

This feature enhances the liquidity of all assets on DeFiChain, as they can be easily moved between DeFiChain and other blockchains.

## Getting Started

To get started with Atomic Swaps on DeFiChain, you need to have a DeFiChain wallet as well as the [DeFiChain Desktop Full-Node Wallet](https://defichain.com/explore/wallets) app.

You will also need a crypto wallet containing the tokens you wish to perform the swap with.

## Requirements

* A machine running Windows, MacOS, or Linux.
* The DeFiChain Desktop Full-Node Wallet app.
* Basic knowledge of command-line operations.
* Adequate BTC, UTXO DFI, and DFI token balances.

**Note:**

To exchange BTC for DFI, you need to pay 0.3% of the BTC amount in fees. This is to be paid in DFI.

Fees are pain in DFI, while transaction fees are paid in UTXO DFI.

## How to exchange BTC for DFI via an Atomic Swap

Here's a detailed guide on how to use DeFiChain's Atomic Swap feature to exchange BTC for DFI.

### <a id="step1" style="text-decoration:none; color:inherit;"> Step 1: Access your wallet via the DeFiChain Desktop Full-Node Wallet app

To perform Atomic Swaps, you first need to download and install the [DeFiChain Desktop Full-Node Wallet](https://defichain.com/explore/wallets) app for your operating system.

Once installed and running, access your exisitng DeFiChain wallet in the app using your seed phrase or `.dat` wallet backup file.

You may open a new wallet if you do not already have one.

### <a id="step2" style="text-decoration:none; color:inherit;"> Step 2: Fund BTC wallet on the DeFiChain Desktop Full-Node Wallet app

Since we're converting bitcoin to DFI we need to fund a BTC wallet with at least the desired amount of coins to be converted.

You may fund the DeFiChain Desktop Full-Node Wallet app's inbuilt Bitcoin Wallet for convenience.

![Fund with btc](<image (4).png>)

### <a id="step3" style="text-decoration:none; color:inherit;"> Step 3: Check for Maker orders via the inbuilt Command-line Interface (CLI)

To begin the swap process, you need to first check for open "Maker" orders.

Atomic Swap transactions require two parties - a Maker and a Taker (in this case, you). The Maker sets up an order while the Taker takes part or all of said order.

To list all open orders, open the CLI by clicking the CLI icon and run this command:

```bash
defi-cli icx_listorders
```

**Example output** (Each JSON object represents a distinct order):

```bash
{
  "WARNING": "ICX and Atomic Swap are experimental features. You might end up losing your funds. USE IT AT YOUR OWN RISK.",
    "fe87484fd39b7f66eb8214df811ddbaaf3b934219c9452af2cf09fdc3b065c1b": {
    "status": "OPEN",
    "type": "INTERNAL",
    "tokenFrom": "BTC",
    "chainTo": "BTC",
    "receivePubkey": "03668b5dc4f33dab92cd5b70c034a88e0d2510b14e39f3995245acb4d8723c2b35",
    "ownerAddress": "7Jw72Q9yGJ1UWCXdQcUwkwSX48mkvdV2sS",
    "amountFrom": 0.10000000,
    "amountToFill": 0.10000000,
    "orderPrice": 1.00000000,
    "amountToFillInToAsset": 0.10000000,
    "height": 476658,
    "expireHeight": 481658
  }
}
```

![Click CLI icon](<image (3).png>)

To exchange BTC for DFI, you need an order with the following parameters:

* `"status": "OPEN"`
* `"type": "INTERNAL"`
* `"tokenFrom": "BTC"`
* `"chainTo": "BTC"`
* `"amountFrom"` greater than or equal to the amount of bitcoins you wish to swap.
* Take note of the `receivePubkey` and `ownerAddress` parameters.
* Also take note of the `orderTx`, in this case `fe87484fd39b7f66eb8214df811ddbaaf3b934219c9452af2cf09fdc3b065c1b`.

### <a id="step4" style="text-decoration:none; color:inherit;"> Step 4: Obtain your Bitcoin `receivePubkey`

Obtain your your Bitcoin wallet's pubKey hash (not address), which will be required in the next step. Run:

```bash
defi-cli spv_getaddresspubkey <BTC_ADDRESS>
```

**Parameters:**

* `BTC_ADDRESS`: your Bitcoin wallet's address.

**Example output:**

```bash
0239ee85fff535273bc4d94ba1378029157a867107aa6248f55d8f9bf4217c5d68
```

Take note of the output value, which is your `receivePubkey`.

### <a id="step5" style="text-decoration:none; color:inherit;"> Step 5: Make an Offer</a>

Now you must make an offer to your preferred order, which the Maker of the order will then choose to accept or not.

To make an offer, run:

```bash
icx_makeoffer '{"orderTx":"fe87484fd39b7f66eb8214df811ddbaaf3b934219c9452af2cf09fdc3b065c1b","amount":0.00100,"ownerAddress":"dSdXC5bEXxLbeoLCHUgh6jVQiLbgp83Aiq","receivePubkey":"0239ee85fff535273bc4d94ba1378029157a867107aa6248f55d8f9bf4217c5d68","expiry":240}'
```

**Parameters:**

* `orderTx` is the id of the order from the **[Step 3](#step3)**'s output.
* `amount` is the amount of tokens you wish to swap.
* `ownerAddress` is your DFI wallet address.
* `expiry` is the amount of time in minutes before the offer expires.
* `receivePubkey` is your Bitcoin wallet's pubKey from **[Step 4](#step4)**'s output.

**Example output:**

```bash
{"txid": bb4823b1321179c99163223f53b8b22168d9430e8f71e17ab968e378e1bfe824}
```

Take note of the returned `txid`, which is the id for your new offer.

### <a id="step6" style="text-decoration:none; color:inherit;"> Step 6: Check if Maker accepts Offer

After a few minutes, check if your offer has been accepted by the Maker. To do so, run:

```bash
icx_listhtlcs '{"offerTx":"bb4823b1321179c99163223f53b8b22168d9430e8f71e17ab968e378e1bfe824"}'
```

**Parameters**:

* `offerTx`: The `txid` returned in the **[Step 5](#step5)**'s output.

**Example output** (if offer has been accepted):

```bash
{
  "WARNING": "ICX and Atomic Swap are experimental features. You might end up losing your funds. USE IT AT YOUR OWN RISK.",
  "2afa1a7c935548ffd6be9d550bb9c7f59da31b9a461bd6c3ad8ee11a50f8ac41": {
    "type": "DFC",
    "status": "OPEN",
    "offerTx": "bb4823b1321179c99163223f53b8b22168d9430e8f71e17ab968e378e1bfe824",
    "amount": 0.00010000,
    "amountInEXTAsset": 0.00010000,
    "hash": "a652273667e1de4af04ae580284be1994408c31461afc2936539260d491544af",
    "timeout": 1500,
    "height": 476766,
    "refundHeight": 478266
  }
}
```

Take note of the `hash`, as it will be required in the next command. Also note the string (`2afa...`) that encapsulates the main response and the `"timeout"` value.

**Note:**

If you get an empty response, it means your offer is yet to be accepted so you must wait.

Also note that the Maker may offer less than what your requested for, which means the `amount` field might be different from what was sent in the offer.

### <a id="step7" style="text-decoration:none; color:inherit;"> Step 7: Create a HTLC (Hashed Timelock Contract)

Now that the offer has been accepted, you need to lock your BTC in a HTLC contract. Start by creating the appropriate HTLC contract with this command:

```bash
spv_createhtlc 03668b5dc4f33dab92cd5b70c034a88e0d2510b14e39f3995245acb4d8723c2b35 03a4728fae10976ad7b2288782e083d2204b16c0b631f3c82f37e22cbeae18zy70 30 a652273667e1de4af04ae580284be1994408c31461afc2936539260d491544af
```

**Parameters** (separate with a space):

* Param 1 : Maker’s (the one who receives the BTC) `receivePubKey` from **[Step 3](#step3)**'s output.
* Param 2: Your `receivePubKey` from **[Step 4](#step4)**'s output. This is so that you can get a refund in case of a failed transaction.
* Param 3: Timeout, which should be less than the `"timeout"` value from **[Step 6](#step6)**'s output and larger than 24.
* Param 4: `hash` from the **[Step 6](#step6)**'s output.

**Note:** Ensure that each parameter is correct.

**Example output:**

```bash
{
  "address": "2N8uZ3s3P8GECXUV6rFukdkPzruRRhy4shf",
  "redeemScript": "63a820a652273667e1de4af04ae580284be1994408c31461afc2936539260d491544af882103668b5dc4f33dab92cd5b70c034a88e0d2510b14e39f3995245acb4d8723c2b3567011eb2752103a4728fae10976ad7b2288782e083d2204b16c0b631f3c82f37e22cbeae18ad6068ac"
}
```

Take note of the `address` parameter's value. This is your `htlcScriptAddress`.

### <a id="step8" style="text-decoration:none; color:inherit;"> Step 8: Fund HTLC

Now you need to fund the contract you just created. To do so, run:

```bash
spv_sendtoaddress 2N8uZ3s3P8GECXUV6rFukdkPzruRRhy4shf 0.0001
```

**Parameters** (separate with a space):

* Param 1: `address` from **[Step 6](#step6)**'s output.
* Param 2: `amount` from **[Step 5](#step5)**'s output.

**Example output:**

```bash
{
  "txid": "6a0249e3848159f061203f86f64e82065ca36e25d331eb21dfb342f1fd989506",
  "sendmessage": "No error"
}
```

The `txid` in the output is the hash of the transaction, which you can use to track the transaction via any [Bitcoin blockchain explorer](https://www.blockchain.com/explorer).

### <a id="step9" style="text-decoration:none; color:inherit;"> Step 9: Inform DeFiChain of HTLC

Next step is to inform DeFiChain that the HTLC contract has been funded. Once the bitcoin transaction is successful, run:

```bash
icx_submitexthtlc '{"offerTx":"bb4823b1321179c99163223f53b8b22168d9430e8f71e17ab968e378e1bfe824","hash":"a652273667e1de4af04ae580284be1994408c31461afc2936539260d491544af","amount":"0.0001","htlcScriptAddress":"2N8uZ3s3P8GECXUV6rFukdkPzruRRhy4shf","ownerPubkey":"03a4728fae10976ad7b2288782e083d2204b16c0b631f3c82f37e22cbeae18zy70","timeout":30}'
```

**Parameters:**

* `offerTx`: From **[Step 5](#step5)**'s command.
* `hash`: From **[Step 5](#step5)**'s output.
* `amount`: From **[Step 5](#step5)**'s output.
* `htlcScriptAddress`: From **[Step 6](#step6)**'s output.
* `ownerPubKey`: Your `receivePubKey` from **[Step 4](#step4)**.
* `timeout`: Timeout value from **[Step 6](#step6)**'s command.

**Example output:**

```bash
{
  "WARNING": "ICX and Atomic Swap are experimental features. You might end up losing your funds. USE IT AT YOUR OWN RISK.",
  "txid": "9fd8bd1e7b67afbfd5fe85dc06a9513278486517ac580af355075faca289825c"
}
```

### <a id="step10" style="text-decoration:none; color:inherit;"> Step 10: Check for fund claim

Taker (you) now needs to check from time to time whether the Maker has claimed their btc from the contract. To check, run:

```bash
spv_gethtlcseed 2N8uZ3s3P8GECXUV6rFukdkPzruRRhy4shf
```

**Parameters** (separate with a space):

* Param 1: `htlcScriptAddress` from **[Step 6](#step6)**'s output.

**Example output:**

```bash
26f5d9f68086e0bb3458d6f102a6b3bb66a1f70a4a439a3276362017bc1d5540
```

Take note of the output, which is known as the `seed`.

**Note**: If you do not get a response, then the Maker is yet to claim their bitcoin.

### <a id="step11" style="text-decoration:none; color:inherit;"> Step 11: Claim dBTC

Once the Maker has claimed their BTC, you may now claim your dBTC tokens with the following commad:

```bash
icx_claimdfchtlc '{"dfchtlcTx":"2afa1a7c935548ffd6be9d550bb9c7f59da31b9a461bd6c3ad8ee11a50f8ac41","seed":"26f5d9f68086e0bb3458d6f102a6b3bb66a1f70a4a439a3276362017bc1d5540"}'
```

**Parameters:**

`dfchtlcTx`: `2afa...` string that encapsulates main response from **[Step 5](#step5)**'s output; the string of numbers before `"type": "DFC"`.
`seed`: From **[Step 9](#step9)**'s output.

**Example output:**

```bash
{
  "WARNING": "ICX and Atomic Swap are experimental features. You might end up losing your funds. USE IT AT YOUR OWN RISK.",
  "txid": "c95a90a92bbc275818f9d149b93ed9542f41e8b8e871efc88f708d60cf73c4e9"
}
```

You may check your transaction on DeFiChain Testnet Explorer: <https://testnet.defichain.io/#/DFI/testnet/home>

<img width="775" alt="ICX #8 dBTC check txid" src="https://user-images.githubusercontent.com/65014479/126480494-2c252e62-c80e-4800-b3fc-1bfdb055a742.png">

Once the transaction is mined, you will receive your dBTC in your wallet.

### <a id="step12" style="text-decoration:none; color:inherit;"> Step 12: Exchange dBTC for BTC

Once you receive your dBTC you may now exchange them for BTC. This can be done using the DeFiChain Desktop wallet's GUI.
(Need GUI access to elaborate here)

#### Footnote

* **Warning**: ICX and Atomic Swap are experimental features. You might end up losing your funds. USE IT AT YOUR OWN RISK.