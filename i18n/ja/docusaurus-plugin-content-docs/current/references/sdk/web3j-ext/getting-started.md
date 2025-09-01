# kaia用Web3j拡張

## 必要条件

### Javaの設定

- Javaバージョン: 11 `<=` v `<=` 18
- https://adoptopenjdk.net。
- OpenJDKのダウンロード

## Web3j kaiaエクステンションをインストールする

Gradle ライブラリ](https://docs.gradle.org/current/userguide/getting_started.html)をプロジェクトに追加します：

```shell

repositories { 
    mavenCentral() 
}

dependencies {
    implementation "io.kaia:web3j-ext:v1.0.0 "の実装
    implementation "io.kaia:web3rpc-java:v1.0.0 "の実装
    implementation "org.web3j:core:4.9.8 "の実装
}.
```

## 使用方法

例](https://github.com/kaiachain/kaia-sdk/tree/main/web3j-ext/web3j-ext/src/main/java/org/web3j/example)を参照。

## クイックスタート

Web3jの基本的な使い方については、[Web3jチュートリアル](https://docs.web3j.io/4.10.0/quickstart/)で学ぶことができます。

### Kairos Testnetで手数料の委任取引を送信する

kaiaネットワークがサポートする手数料委任トランザクションの概念についてさらに詳しく知りたい場合は、[kaiadocs] を参照してください。(https://docs.klaytn.foundation/content/klaytn/design/transactions)をご参照ください。

web3j-ext [examples](https://github.com/kaiachain/kaia-sdk/tree/dev/web3j-ext/web3j-ext/src/main/java/org/web3j/example) の FeeDelegatedValueTransferExample.java ファイルを確認してください。

```file
package org.web3j.example;

import java.io.IOException;
import java.math.BigInteger;
import org.web3j.crypto.KlayCredentials;
import org.web3j.crypto.KlayRawTransaction;
import org.web3j.crypto.KlayTransactionEncoder;
import org.web3j.crypto.transaction.type.TxType;
import org.web3j.crypto.transaction.type.TxTypeValueTransfer;
import org.web3j.crypto.transaction.type.TxType.Type;
import org.web3j.protocol.core.DefaultBlockParameterName;
import org.web3j.protocol.core.methods.response.EthChainId;
import org.web3j.protocol.core.methods.response.EthSendTransaction;
import org.web3j.protocol.http.HttpService;
import org.web3j.protocol.kaia.Web3j;
import org.web3j.utils.Numeric;
import org.web3j.protocol.kaia.core.method.response.TransactionReceipt;


public class FeeDelegatedValueTransferExample implements keySample {

    public static void run() throws Exception {
        Web3j web3j = Web3j.build(new HttpService(keySample.BAOBAB_URL));
        KlayCredentials credentials = KlayCredentials.create(keySample.LEGACY_KEY_privkey);
        KlayCredentials credentials_feepayer = KlayCredentials.create(keySample.LEGACY_KEY_FEEPAYER_privkey);

        BigInteger GAS_PRICE = BigInteger.valueOf(50000000000L);
        BigInteger GAS_LIMIT = BigInteger.valueOf(6721950);
        String from = credentials.getAddress();
        EthChainId EthchainId = web3j.ethChainId().send();
        long chainId = EthchainId.getChainId().longValue();
        String to = "0x000000000000000000000000000000000000dead";
        BigInteger nonce = web3j.ethGetTransactionCount(from, DefaultBlockParameterName.LATEST).send()
                .getTransactionCount();
        BigInteger value = BigInteger.valueOf(100);

        TxType.Type type = Type.FEE_DELEGATED_VALUE_TRANSFER;

        KlayRawTransaction raw = KlayRawTransaction.createTransaction(
                type,
                nonce,
                GAS_PRICE,
                GAS_LIMIT,
                to,
                value,
                from);

        // Sign as sender
        byte[] signedMessage = KlayTransactionEncoder.signMessage(raw, chainId, credentials);

        // Sign same message as Fee payer
        signedMessage = KlayTransactionEncoder.signMessageAsFeePayer(raw, chainId, credentials_feepayer);

        String hexValue = Numeric.toHexString(signedMessage);
        EthSendTransaction transactionResponse = web3j.ethSendRawTransaction(hexValue).send();
        System.out.println("TxHash : \n " + transactionResponse.getResult());
        String txHash = transactionResponse.getResult();

        int DEFAULT_POLLING_ATTEMPTS_PER_TX_HASH = 40;
        int DEFAULT_BLOCK_TIME = 1 * 1000;
        long DEFAULT_POLLING_FREQUENCY = DEFAULT_BLOCK_TIME;
        TransactionReceiptProcessor transactionReceiptProcessor = new PollingTransactionReceiptProcessor(web3j,
                DEFAULT_POLLING_FREQUENCY, DEFAULT_POLLING_ATTEMPTS_PER_TX_HASH);
        org.web3j.protocol.core.methods.response.TransactionReceipt ethReceipt = transactionReceiptProcessor
                .waitForTransactionReceipt(txHash);
        System.out.println("Receipt from eth_getTransactionReceipt : \n" + ethReceipt);
        TransactionReceipt receipt = web3j.klayGetTransactionReceipt(txHash).send().getResult();
        System.out.println("Receipt from klay_getTransactionReceipt : \n" + receipt);
        web3j.shutdown();

        TxTypeFeeDelegatedValueTransfer rawTransaction = TxTypeFeeDelegatedValueTransfer
                .decodeFromRawTransaction(hexValue);
        System.out.println("TxType : " + rawTransaction.getKlayType());
    }
}
```

実行例

```file
import org.web3j.example.FeeDelegatedValueTransferExample;

public class quickstart {
        public static void main(String[] args) throws Exception {
            FeeDelegatedValueTransferExample.run();
        }
}
```

詳しくは[Web3j kaia extension GitHubリポジトリ](https://github.com/kaiachain/kaia-sdk/tree/main/web3j-ext)をご参照ください。