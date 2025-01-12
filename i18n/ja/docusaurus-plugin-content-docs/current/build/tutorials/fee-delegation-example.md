# Build Fee Delegation Example

## Table of Contents <a href="#table-of-contents" id="table-of-contents"></a>

- [1. Introduction](#1-introduction)
- [2. How fee delegation works](#2-how-fee-delegation-works)
  - 2.1 Transaction signing by the sender
  - 2.2 Transaction signing by the fee payer
- [3. Simple server and client for fee delegation](#3-simple-server-and-client-for-fee-delegation)
  - 3.1 Sender's client
  - 3.2 Fee payer's server
- [4. Run example](#4-run-example)
  - 4.1 Run `feepayer_server.js`
  - 4.2 Run `sender_client.js`
  - 4.3 Check `feepayer_server.js`
  - 4.4 Klaytn scope

## 1. Introduction <a href="#1-introduction" id="1-introduction"></a>

This tutorial helps you to write a simple server-client example using caver-js SDK to illustrate how fee delegated value transfer transaction works in Klaytn. This tutorial and the example code is using the Baobab testnet.

## 2. How fee delegation works <a href="#2-how-fee-delegation-works" id="2-how-fee-delegation-works"></a>

Let's skim through how fee delegation works.

### 2.1 Transaction signing by the sender <a href="#2-1-transaction-signing-by-the-sender" id="2-1-transaction-signing-by-the-sender"></a>

`Sender` always should sign the transaction before sending a transaction.

To sign a transaction, use [signTransaction](../../references/sdk/caver-js-1.4.1/api/caver.klay.accounts.md#signtransaction) which signs a transaction with given private key.

```
// using the event emitter
const senderAddress = "SENDER_ADDRESS";
const senderPrivateKey = "SENDER_PRIVATEKEY";
const toAddress = "TO_ADDRESS";

    // Create a new transaction
    const tx = caver.transaction.feeDelegatedValueTransfer.create({
      from: keyring.address,
      to: toAddress,
      value: caver.utils.toPeb("0.0001", "KAIA"),
      gas: 100000,
      gasPrice: await caver.rpc.klay.getGasPrice(), // Get current gas price
      chainId: await caver.rpc.klay.getChainId(), // Get current chain ID
    });

        // Sign the transaction
    const signed = await caver.wallet.sign(keyring.address, tx);

    const senderRawTransaction = tx.getRLPEncoding();
```

If there are no errors, then `senderRawTransaction` will have a signed transaction which is signed by the `senderPrivateKey`.

Now, you need to send the `senderRawTransaction` to the fee payer. There will be various ways to implement this. In this tutorial, we will provide you a simple server-client code as an example of sending a `senderRawTransaction` to the fee payer.

### 2.2 Transaction signing by the fee payer <a href="#2-2-transaction-signing-by-the-fee-payer" id="2-2-transaction-signing-by-the-fee-payer"></a>

When `fee payer` receives the `senderRawTransaction`, `fee payer` signs the `senderRawTransaction` again with their private key and sends the transaction to Klaytn. The below code snippet illustrates the process. `kaia.sendRawTransaction` method signs the transaction with the given account's private key before sending the transaction. Before running the code, please replace `"FEEPAYER_ADDRESS"` and `"PRIVATE_KEY"` with the actual values.

Note that when the `fee payer` submits the transaction to Kaia on behalf of the `sender`, the `senderRawTransaction` type must be a `FEE_DELEGATED` type of transaction. In the below example, [sendTransaction(FEE_DELEGATED_VALUE_TRANSFER)](../../references/sdk/caver-js-1.4.1/api/caver.klay/transaction/sendtx-value-transfer.md#sendtransaction-fee_delegated_value_transfer) method is invoked, because the original `senderRawTransaction` generated by the sender was [TxTypeFeeDelegatedValueTransfer](../../learn/transactions/fee-delegation.md#txtypefeedelegatedvaluetransfer).

```
const feePayerAddress = "FEEPAYER_ADDRESS";
const feePayerPrivateKey = "PRIVATE_KEY"

const tx = caver.transaction.decode(senderRawTransaction);
    console.log("Decoded transaction:", tx);

    tx.feePayer = keyring.address;

    const signed = await caver.wallet.signAsFeePayer(keyring.address, tx);

    // Send the transaction
    const receipt = await caver.rpc.klay.sendRawTransaction(
      signed.getRLPEncoding()
    )
.on('transactionHash', function(hash){
    ...
})
.on('receipt', function(receipt){
    ...
})
.on('error', console.error); // If an out-of-gas error, the second parameter is the receipt.
```

## 3. Simple server and client for fee delegation <a href="#3-simple-server-and-client-for-fee-delegation" id="3-simple-server-and-client-for-fee-delegation"></a>

Let's write a simple server and client using above fee delegation code.

### 3.1 Environment setup <a href="#3-1-environment-setup" id="3-1-environment-setup"></a>

We will use `npm` and [caver-js](../../references/sdk/caver-js-1.4.1/get-started-1.4.1.md) to setup environment for this example as below.

```
$ mkdir example
$ cd example
$ npm init
$ npm install caver-js@latest
```

### 3.1 Sender's client <a href="#3-1-sender-s-client" id="3-1-sender-s-client"></a>

First, we are going to write a `sender_client.js` as below.

In the example, please replace `"SENDER_ADDRESS"`, `"SENDER_PRIVATEKEY"` and `"TO_ADDRESS"` with the actual values.

```javascript
import { Socket } from "net";
import Caver from "caver-js";

const client = new Socket();
const caver = new Caver("https://public-en-kairos.node.kaia.io");

const senderAddress = "SENDER_ADDRESS";
const senderPrivateKey =
  "SENDER_PRIVATE_KEY";
const toAddress = "TO_ADDRESS";

const sendFeeDelegateTx = async () => {
  try {
    // Add sender's keyring to wallet
    const keyring = caver.wallet.newKeyring(senderAddress, senderPrivateKey);

    // Create a new transaction
    const tx = caver.transaction.feeDelegatedValueTransfer.create({
      from: keyring.address,
      to: toAddress,
      value: caver.utils.toPeb("0.0001", "KAIA"),
      gas: 100000,
      gasPrice: await caver.rpc.klay.getGasPrice(), // Get current gas price
      chainId: await caver.rpc.klay.getChainId(), // Get current chain ID
    });

    // Sign the transaction
    const signed = await caver.wallet.sign(keyring.address, tx);

    const senderRawTransaction = tx.getRLPEncoding();

    if (!senderRawTransaction) {
      throw new Error("Failed to generate raw transaction");
    }

    // Send signed raw transaction to fee payer's server
    client.connect(1337, "127.0.0.1", () => {
      console.log("Connected to fee delegated service");
      client.write(senderRawTransaction);
    });

    client.on("data", (data) => {
      console.log("Received data from server:", data.toString());
    });

    client.on("error", (error) => {
      console.error("Connection error:", error);
      s;
    });

    client.on("close", () => {
      console.log("Connection closed");
    });
  } catch (error) {
    console.error("Transaction error:", error);
    client.end();
    process.exit(1);
  }
};

sendFeeDelegateTx();

```

The above code signs a fee delegated value transfer transaction with `senderPrivateKey` and sends the signed `senderRawTransaction` to the fee payer's server which is running on port `1337` on `127.0.0.1`, i.e. localhost.

### 3.2 Fee payer's server <a href="#3-2-fee-payer-s-server" id="3-2-fee-payer-s-server"></a>

Now let's write the fee payer's server, `feepayer_server.js`, which signs received `senderRawTransaction` with `feePayerPrivateKey` and sends it to Baobab testnet.

In the below example, please replace `"FEEPAYER_ADDRESS"` and `"FEEPAYER_PRIVATEKEY"` with actual values.

```javascript
import { createServer } from "net";
import Caver from "caver-js";

const caver = new Caver("https://public-en-kairos.node.kaia.io");
const feePayerAddress = "FEEPAYER_ADDRESS";
const feePayerPrivateKey =
  "FEEPAYER_PRIVATE_KEY";

// add fee payer's keyring to wallet

const keyring = caver.wallet.newKeyring(feePayerAddress, feePayerPrivateKey);

const feePayerSign = async (senderRawTransaction, socket) => {
  try {
    const tx = caver.transaction.decode(senderRawTransaction);
    console.log("Decoded transaction:", tx);

    tx.feePayer = keyring.address;

    const signed = await caver.wallet.signAsFeePayer(keyring.address, tx);

    // Send the transaction
    const receipt = await caver.rpc.klay.sendRawTransaction(
      signed.getRLPEncoding()
    );
    console.log("Transaction receipt:", receipt);

    if (receipt.transactionHash) {
      socket.write(`Tx hash: ${receipt.transactionHash}\n`);
      socket.write(`Sender Tx hash: ${receipt.senderTxHash || ""}\n`);
    }
  } catch (error) {
    console.error("Error in feePayerSign:", error);
    socket.write(`Error: ${error.message}\n`);
  }
};

var server = createServer(function (socket) {
  console.log("Client is connected ...");
  socket.write("This is fee delegating service");
  socket.write("Fee payer is " + feePayerAddress);
  socket.on("data", function (data) {
    console.log("Received data from client:", data.toString());
    feePayerSign(data.toString(), socket);
  });
  socket.on("error", (error) => {
    console.error("Socket error:", error);
  });

  socket.on("end", () => {
    console.log("Client disconnected");
  });
});

server.listen(1337, "127.0.0.1");
console.log("Fee delegate service started ...");

```

The server listens on port `1337`.

When there is incoming `data`, it signs the `data` with `feePayerPrivateKey` and sends it to the Klaytn blockchain. It assumes that the `data` is `senderRawTransaction` from the `sender_client.js`.

## 4. Run example <a href="#4-run-example" id="4-run-example"></a>

Prepare two terminals, one for `sender_client.js` and another for `feepayer_server.js`.

### 4.1 Run `feepayer_server.js` <a href="#4-1-run-feepayer_server-js" id="4-1-run-feepayer_server-js"></a>

Below the command will start the fee payer's server.

```
$ node feepayer_server.js
Fee delegate service started ...
```

The server starts and is now listening on port 1337.

### 4.2 Run `sender_client.js` <a href="#4-2-run-sender_client-js" id="4-2-run-sender_client-js"></a>

Let's run `sender_client.js` to send a fee delegated transaction.

```
$ node sender_client.js
Signed a fee delegated value transfer transaction.
Sending a signed transaction to fee delegated service.
Connected to fee delegated service
Received data from server: This is fee delegating service
Received data from server: Fee payer is 0x811CE345DB9D8aD17513Cc77d76a1ace9eC46F02
Received data from server: Tx hash is 0x1e6a019bb9c6cf156a6046ca33f0c810fb9fb6fdcb6df32b2e34a1d50f7f8a9d
Received data from server: Sender Tx hash is 0x7263d2dc5b36abc754726b220b7ad243dd789934109c6874e539ada5c7e9f193
```

It will sign a transaction with the `sender` private key and send the signed transaction to the fee delegated service (i.e., fee payer's server). Then it will receive the response from the fee delegate service including the `Fee payer` address, `Tx hash`, and `Sender Tx hash`. `Tx hash` is hash of a transaction submitted to the Klaytn network, while `Sender Tx hash` is hash of a transaction without the fee payer's address and signature. For more details, please take a look at [SenderTxHash](../../learn/transactions/transactions.md#sendertxhash).

### 4.3 Check `feepayer_server.js` <a href="#4-3-check-feepayer_server-js" id="4-3-check-feepayer_server-js"></a>

On the server's console, you will see below outputs. It prints the transaction receipt from the Klaytn.

```
$ node feepayer_server.js
Fee delegate service started ...
Client is connected ...
Received data from client: 0x09f89f0485066720b300830186a094811ce345db9d8ad17513cc77d76a1ace9ec46f02865af3107a400094213eb97cc74af77b78d1cfd968bc89ab816872daf847f8458207f5a0cefe267a80c014d1750c31aa312843b3696a14abebc1be88c63d0b63d6b6f714a0512abfe3533f2cfd924e7decdd21e05f22a22f04b35db09f39839708043daac3940000000000000000000000000000000000000000c4c3018080
Decoded transaction: FeeDelegatedValueTransfer {
  _type: 'TxTypeFeeDelegatedValueTransfer',
  _from: '0x213eb97cc74af77b78d1cfd968bc89ab816872da',
  _gas: '0x186a0',
  _nonce: '0x4',
  _signatures: [
    SignatureData {
      _v: '0x07f5',
      _r: '0xcefe267a80c014d1750c31aa312843b3696a14abebc1be88c63d0b63d6b6f714',
      _s: '0x512abfe3533f2cfd924e7decdd21e05f22a22f04b35db09f39839708043daac3'
    }
  ],
  _klaytnCall: {
    getChainId: [Function (anonymous)] {
      method: [Method],
      request: [Function: bound request],
      call: 'klay_chainID',
      getMethod: [Function (anonymous)]
    },
    getGasPrice: [Function (anonymous)] {
      method: [Method],
      request: [Function: bound request],
      call: 'klay_gasPrice',
      getMethod: [Function (anonymous)]
    },
    getTransactionCount: [Function (anonymous)] {
      method: [Method],
      request: [Function: bound request],
      call: 'klay_getTransactionCount',
      getMethod: [Function (anonymous)]
    },
    getHeaderByNumber: [Function (anonymous)] {
      method: [Method],
      request: [Function: bound request],
      call: 'klay_getHeaderByNumber',
      getMethod: [Function (anonymous)]
    },
    getAccountKey: [Function (anonymous)] {
      method: [Method],
      request: [Function: bound request],
      call: 'klay_getAccountKey',
      getMethod: [Function (anonymous)]
    },
    getTransactionByHash: [Function (anonymous)] {
      method: [Method],
      request: [Function: bound request],
      call: 'klay_getTransactionByHash',
      getMethod: [Function (anonymous)]
    },
    getMaxPriorityFeePerGas: [Function (anonymous)] {
      method: [Method],
      request: [Function: bound request],
      call: 'klay_maxPriorityFeePerGas',
      getMethod: [Function (anonymous)]
    }
  },
  _feePayer: '0x0000000000000000000000000000000000000000',
  _feePayerSignatures: [ SignatureData { _v: '0x01', _r: '0x', _s: '0x' } ],
  _to: '0x811ce345db9d8ad17513cc77d76a1ace9ec46f02',
  _value: '0x5af3107a4000',
  _gasPrice: '0x66720b300'
}
Transaction receipt: {
  blockHash: '0xb2727edaa2ffc8a8fece0ce54154b469887a9f6725bac6811ac610131c135046',
  blockNumber: '0xa45da40',
  contractAddress: null,
  effectiveGasPrice: '0x66720b300',
  feePayer: '0x811ce345db9d8ad17513cc77d76a1ace9ec46f02',
  feePayerSignatures: [
    {
      V: '0x7f6',
      R: '0x6207f1c3c8c75f1a57ff3d1c87a51b7067f6076b1bf37c3a1ad296e441cfa9db',
      S: '0x7f086233c6d99f92d78bd1d3292127c1bda7fc41bab670a9e8a38302a742eb11'
    }
  ],
  from: '0x213eb97cc74af77b78d1cfd968bc89ab816872da',
  gas: '0x186a0',
  gasPrice: '0x66720b300',
  gasUsed: '0x7918',
  logs: [],
  logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  nonce: '0x4',
  senderTxHash: '0x7263d2dc5b36abc754726b220b7ad243dd789934109c6874e539ada5c7e9f193',
  signatures: [
    {
      V: '0x7f5',
      R: '0xcefe267a80c014d1750c31aa312843b3696a14abebc1be88c63d0b63d6b6f714',
      S: '0x512abfe3533f2cfd924e7decdd21e05f22a22f04b35db09f39839708043daac3'
    }
  ],
  status: '0x1',
  to: '0x811ce345db9d8ad17513cc77d76a1ace9ec46f02',
  transactionHash: '0x1e6a019bb9c6cf156a6046ca33f0c810fb9fb6fdcb6df32b2e34a1d50f7f8a9d',
  transactionIndex: '0x1',
  type: 'TxTypeFeeDelegatedValueTransfer',
  typeInt: 9,
  value: '0x5af3107a4000'
}
```

### 4.4 Klaytn scope <a href="#4-4-klaytn-scope" id="4-4-klaytn-scope"></a>

You can also find the above transaction on the [Kaiascope](https://kairos.kaiascope.com).

It shows that the transaction is `TxTypeFeeDelegatedValueTransfer` and `Fee payer` is `0x811CE345DB9D8aD17513Cc77d76a1ace9eC46F02` or `feepayerAddress` that you entered, while `From` is a different address which should be the `senderAddress` in above example.

![Fee delegated Tx](/img/build/tutorials/fee-delegation-example-kaia.png)
