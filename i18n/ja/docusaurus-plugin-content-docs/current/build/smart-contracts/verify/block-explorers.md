---
sidebar_label: ブロック・エクスプローラーを使う
---

# ブロックエクスプローラーを使ったスマートコントラクトの検証方法

## はじめに

通常、スマート・コントラクトのデプロイする者は、実際にデプロイされたコードにアクセスできる唯一の当事者であり、デプロイ者が検証するまで、一般人はコントラクトのソースコードを読むことができない。 コントラクトの検証は、（ユーザーにとっての）透明性、（開発者にとっての）利便性、そしてデプロイされたコントラクトの安全性を向上させるのに役立つからだ。

とはいえ、スマート・コントラクトが検証されると、KaiascopeやKaiascanのようなブロック・エクスプローラーは、ブロック・エクスプローラーのユーザー・インターフェースを使用して、一般の人がコントラクトのパブリック・メソッドと対話することも可能にする。 これは、一般の人々が検証済みの契約ソースコードに直接アクセスできることに加えてのことである。 これは、一般の人々が検証済みの契約ソースコードに直接アクセスできることに加えてのことである。

このガイドでは、ブロックエクスプローラーを使用してKaiaネットワーク上でデプロイされたスマートコントラクトを検証する方法を見ていきます。

## 前提条件

- [Remix IDE](https://ide.kaia.io/) と [Kaia Wallet](https://docs.kaiawallet.io/getting_started/quick_start#install-kaia-wallet)
- [Faucet](https://faucet.kaia.io)から十分なテストKAIA

## はじめに

このガイドでは、Kaiaエコシステムに存在するブロックエクスプローラーで、シングルコントラクトとマルチパートコントラクトの検証について説明します：

- [カイアスコープ](https://kaiascope.com/)
- [カイアスカン](https://www.kaiascan.io/)

さっそく始めよう！

## 単一契約の展開

スマート・コントラクトを検証するには、まずターゲット・ネットワーク上にコントラクトをデプロイする必要がある。 したがって、このガイドでは、Kaia Kairos Testnetにコントラクトをデプロイすることにする。 また、このチュートリアルでは、Remix IDE上に`Counter.sol`というシンプルなカウンターのコントラクトをデプロイする。 コードを以下に示す：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
contract Counter {
    uint256 public count;
    constructor(uint256 _initialCount) {
        count = _initialCount;
    }
    function incrementCounter() public {
        count++;
    }
    function decrementCounter() public {
        count--;
    }
    function resetCounter() public {
        count = 0;
    }
}
```

:::note

Kaia Kairos Testnetの[libraries](../../../references/sdk/sdk.md)を使ったスマートコントラクトのデプロイに関するチュートリアルは、このページで確認できます。 スマートコントラクトをKaia Kairos Testnetにデプロイするには、[Hardhat](../../get-started/hardhat.md)、[Foundry](../deploy/foundry.md)、[Remix](../deploy/deploy.md#remix-ide)などの開発者ツールを使用することもできます。

:::

## 単一契約検証のパラメータ

ブロック・エクスプローラーでコントラクトを検証するには、いくつかのパラメータが必要で、スマート・コントラクトをデプロイする際に考慮しなければならない。 以下は、コントラクトをうまく検証するための、コントラクトのコンパイラとデプロイメントに関する詳細である：

Remix IDE :

- Remix IDEで、**Solidityコンパイラタブ**に移動します。

    - 契約のコンパイルとデプロイに使用された**コンパイラのバージョン**を確認してください。
    - 契約で使用されている**オープンソースライセンスの種類**を確認してください。 これは、Solidity ソース ファイルの先頭で使用される SPDX ライセンス識別子を意味します。 `Counter.sol`ファイルでは、`// SPDX-License-Identifier：MIT`
    - コントラクトのデプロイに使用される**EVMバージョン**を確認してください。
    - (オプション）コンパイル時に**最適化**が有効になっている場合、最適化実行パラメータの値に注意する。

    ![](/img/build/tutorials/counter-veri-parameters.png)

- Remix IDEで**Kaiaタブ**に移動します。

    - (オプション) コンストラクタのメソッドが引数を受け付ける場合は、コンストラクタの引数の [ABI エンコード形式](https://docs.soliditylang.org/en/develop/abi-spec.html) に注意してください。
    - デプロイに成功したら、**Deployed Contracts**タブでスマートコントラクトのコントラクトアドレスをメモしてください。

    ![](/img/build/tutorials/counter-veri-parametersII.png)

## マルチパートコントラクトのデプロイ

マルチパートのコントラクトのデプロイメントには、単一のコントラクトのデプロイメントと同じ手順が必要であることに注意することが重要である。 このガイドでは、`airdropToken.sol`という名前のシンプルなKIP7のエアドロップコントラクトをデプロイする。 コードを以下に示す：

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;
import "@kaiachain/contracts/KIP/token/KIP7/KIP7.sol";
import "@kaiachain/contracts/access/Ownable.sol";
// the creator of the project mints certian amount of fungible tokens directly to a certain selection of wallets.
contract TokenAirdrop is KIP7, Ownable {
    constructor() KIP7("Token Aidrop Demo", "TAD") {
    }
    // Airdrop Token
    function airdropTokens(address[] calldata wAddresses, uint[] calldata tAmount) public onlyOwner {
        require(wAddresses.length == tAmount.length, "Must be same lenght");
        for (uint256 i = 0; i < wAddresses.length; i++) {
            _mintSingleTokens(wAddresses[i], tAmount[i]);
        }
    }
    function _mintSingleTokens(address wAddress, uint amount) private {
        _mint(wAddress, amount);
    }
    function supportsInterface(bytes4 interfaceId)
        public
        view
        virtual
        override
        returns (bool)
    {
        return
            super.supportsInterface(interfaceId);
    }
}
```

## マルチパートコントラクト検証のパラメータ

マルチパートコントラクトを検証するためのパラメータは、単一契約の場合と同じである。 しかし、それらは複数の依存するコントラクトで構成されているため、コントラクトのすべての依存関係を単一のsolidityファイルに前処理する必要があります。 この前処理は通常、スマート・コントラクトのフラット化と呼ばれる。

このため、コントラクトをフラット化し、ブロックエクスプローラーで新しいフラット化されたSolidityファイルを使用して検証できるようにする必要があります。

Remix IDE:

- Remix IDEで、**ファイルエクスプローラタブ**に移動します。

    - **contracts**フォルダの下に新しく作成した契約を選択します。
    - 2本指でクリックまたはタップすると、コントラクトで利用可能なすべてのコマンドが表示されます。
    - **flatten**を選択

    ![](/img/build/tutorials/airdropToken-flattened.png)

    - コードが平坦化されると、`airdropTokens_flattened.sol`という名前の新しいコントラクトが表示されます。

    ![](/img/build/tutorials/airdropToken-flattened-file.png)

:::note

マルチパートのスマートコントラクトを単一のSolidityファイルにフラット化するためのさまざまなツールがあります。たとえば、[Hardhat Flattener](https://hardhat.org/hardhat-runner/docs/advanced/flattening)があります。 スマート・コントラクト・フラットニング・ツールの詳細な使用方法については、それぞれのスマート・コントラクト・フラットニング・ツールのドキュメントを参照してください。 スマート・コントラクト・フラットニング・ツールの詳細な使用方法については、それぞれのスマート・コントラクト・フラットニング・ツールのドキュメントを参照してください。

:::

## 契約の確認

検証パラメータをすべて取得したので、このセクションでは、ブロック・エクスプローラ上で単一のスマート・コントラクト（Counter.sol）と複数パートのスマート・コントラクト（airdropTokens.sol）を検証する手順を説明します。

### 1. Kaiascope

Kaiascopeで単一契約および複数パート契約を検証するには、以下の手順に従ってください：

#### 1.1 単一契約の検証

1. [Kaiascope](https://kairos.kaiascope.com)の検索バーに、デプロイされた契約書のアドレスを貼り付ける。
2. そのページの**契約タブ**に移動する。
3. **Match Contract Source Code** リンクをクリックして、確認のために契約コードを送信します。

![](/img/build/tutorials/counter-contract-tab.png)

4. 契約確認ページで、アカウントがKaia WalletまたはMetamaskのいずれかに接続されていることを確認します。 このガイドでは、カイア・ウォレットを使用します。 このガイドでは、カイア・ウォレットを使用します。
5. 契約住所欄\*\*に契約住所を記入する。 注：このフィールドには通常、契約住所が自動的に入力されます。 注：このフィールドには通常、契約住所が自動的に入力されます。
6. `Counter.sol`の例で使用した**コンパイラのバージョン**を選択する。
7. `Counter.sol`の例で使用した**オープン・ソース・ライセンス・タイプ**を選択します。 Counter.solの例では、\*\*MIT License (MIT)\*\*を選択してください。 使用されていない場合は、\*\*ライセンスなし（None）\*\*を選択します。
8. **Source Code field**で、**Source Text**を選択し、テキストフィールドに`Counter.sol`のソースコードを貼り付けます。
9. **最適化**がコンパイル時に有効になっている場合は**True**を選択し、**Optimization Runs**の実行回数を**200**になるように入力します。
10. 契約の**EVMバージョン**を選択します。 `Counter.sol`の例では、**Istanbul**を選択する。
11. 下部のCAPTCHAと**Sign and Submit**ボタンをクリックして確認し、認証を開始します。

![](/img/build/tutorials/counter-verification-page.png)

12. 検証リクエストに署名した後、検証ステータスの通知が届きます。

![](/img/build/tutorials/counter-success-popup.png)

13. 検証が完了すると、検証結果がブラウザに表示され、契約先が記載された成功結果ページが表示される。 契約アドレスをクリックすると、**契約ソースコード**、**契約ABI**、**バイトコード**が表示されます。

![](/img/build/tutorials/counter-success-popup-I.png)

![](/img/build/tutorials/counter-full-verification.png)

#### 1.2 複数パート契約の検証

Kaiascopeでの複数パート契約の検証は、いくつかの追加ステップが必要なことを除けば、単一契約の検証と同じくらい簡単です。 このセクションでは、`airdropToken.sol`コントラクトを以下のステップを追加して検証する：

- ソースコード**の下にある**ソーステキスト\*\*(Counter.solの例のステップ3)、または**ソースコード**フィールドの下にある\*\*ソリディティファイル(s)\*\*を選択することができます。  **Source Text**の場合、`airdropToken_flattened.sol`内のコードをコピーしてテキストフィールドに貼り付けます。 \*\*Solidity File(s)\*\*の場合、Remix IDEで`airdropToken_flattened.sol`ファイルをダウンロードし、フィールドにアップロードします。

a. ソース・テキスト

![](/img/build/tutorials/airdrop-veri-field-I.png)

b. ソリディティファイル

![](/img/build/tutorials/airdrop-veri-field-II.png)

この後、他のすべてのステップは、単一の契約を検証するのと同じである。 検証パラメータを入力したら、**Sign and Submit** ボタンをクリックして確認し、検証を開始します。 検証パラメータを入力したら、**Sign and Submit** ボタンをクリックして確認し、検証を開始します。

検証が完了すると、検証結果がブラウザに表示され、契約先が記載された成功結果ページが表示される。 契約アドレスをクリックすると、**契約ソースコード**、**契約ABI**、**バイトコード**が表示されます。

![](/img/build/tutorials/airdrop-success-popup.png)

![](/img/build/tutorials/airdrop-success-popup-I.png)

![](/img/build/tutorials/airdrop-full-verification.png)

### 2. カイアスカン

To verify a single contract and multi-part contracts on Kaiascan, navigate to the [contract submission request page](https://kairos.kaiascan.io/contracts).

:::note

Kaiascanでの契約の検証は現在ベータ版です。

:::

![](/img/build/tutorials/kaiascan-con-sub-page.png)

#### 2.1 単一契約の検証

1. 配置された契約（Counter.sol）の**契約アドレス**を記入してください。
2. `Counter.sol`の例で使用されている**コンパイラのバージョン**を選択してください。
3. `Counter.sol`の例で使用した**オープン・ソース・ライセンス・タイプ**を選択します。 `Counter.sol`の例で使用した**オープン・ソース・ライセンス・タイプ**を選択します。 Counter.solの例では、\*\*MIT License (MIT)\*\*を選択してください。 使用されていない場合は、\*\*ライセンスなし（None）\*\*を選択します。 使用されたものがない場合は、\*\*ライセンスなし（None）\*\*を選択する。
4. 必ずRemix IDEから`Counter.sol`をダウンロードし、\*\*Source Code (Solidity File)\*\*フィールドにアップロードしてください。
5. 契約の**EVMバージョン**を選択します。 `Counter.sol`の例では、**Istanbul**を選択する。
6. **最適化**がコンパイル時に有効になっている場合は**True**を選択し、**Optimization Runs**の実行回数を**200**になるように入力します。
7. (オプション) このフィールドのABIエンコードされたコンストラクタ引数を取得するには、[abi.hashex.org](http://abi.hashex.org) にアクセスして、以下の画像に従ってエンコードされたデータを取得します：

![](/img/build/tutorials/abi-hashex.png)

8. Click on the **Sign and Submit** button to confirm and begin verification.

![](/img/build/tutorials/counter-k-verification-page.png)

9. 認証が完了すると、**Submission Successful**というメッセージが表示されます。 これで、エクスプローラーの検索バーに契約書アドレスを貼り付けて、**契約書ソースコード**、**契約書ABI**、**作成コード**および**ABIエンコード値**を表示できる。

> ![](/img/build/tutorials/counter-k-full-verification.png)

### 2.2 複数パート契約の検証

Kaiascanで複数パートにまたがる契約を検証する場合は、単一の契約を検証する場合と同じ手順を踏みます。 ただし、Kaiascan は現在検証用ファイルのアップロードをサポートしていないため、`airdropToken_flattened.sol` ファイルをコピーして **Enter the Solidity Contract Code below** フィールドに貼り付けることに注意してください。 ただし、Kaiascan は現在検証用ファイルのアップロードをサポートしていないため、`airdropToken_flattened.sol` ファイルをコピーして **Enter the Solidity Contract Code below** フィールドに貼り付けることに注意してください。

![](/img/build/tutorials/airdrop-k-verification-page.png)

検証パラメータを入力したら、**Verify and Publish** ボタンをクリックして検証を開始します。 認証が完了すると、認証ページが更新されます。 これで、エクスプローラーの検索バーに契約書のアドレスを貼り付けて、**契約書のソースコード**、**契約書のABI**、**作成コード**を表示できる。

![](/img/build/tutorials/airdrop-k-full-verification.png)

## 結論

このガイドに従ったことを祝福する！ このチュートリアルでは、KaiascopeとKaiascanのみを使用してコントラクト（シングル・パートとマルチ・パートの両方）を検証し、（ユーザーにとっての）透明性、（開発者にとっての）利便性、およびデプロイされたコントラクトの安全性を高める方法を学びました。 より詳しい情報は[Kaia Docs](https://docs.kaia.io/)を、質問があれば[Kaia Forum](https://devforum.kaia.io/)をご覧ください。