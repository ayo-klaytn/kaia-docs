# ブロック同期

ブロック同期とは、Kaiaブロックチェーンの最新のブロックとステートでノードを更新するプロセスです。 ノードオペレータは、ハードウェアの仕様やサービス要件に応じて、さまざまな同期方法を選択できる。

## フルシンク

フルシンクはKaiaのデフォルトの同期方法で、`--syncmode full`フラグを使うか、フラグを完全に省略することで有効になります。 この方法では、p2pピアからすべてのブロック（ヘッダーとトランザクション）をダウンロードして実行し、ブロックの状態を生成する。

### ステート・パーシステンス・オプション

フル・シンクがすべてのブロックを処理するのに対し、カイアは状態データをどの程度ディスクに永続的に保存するかについて柔軟性を提供する。 これにより、ノードオペレータは、データへのアクセス性とストレージ容量のバランスをとることができる。 以下の図は、これらのオプションを示している：

![Block sync options](/img/learn/block_sync.png)

- **アーカイブ・モード**：このモードはすべてのブロックの状態をディスクに永続化する。 これを有効にするには、`--gcmode archive`フラグを使う。 このモードで動作するノードは**アーカイブ・ノード**と呼ばれる。
- **フル・モード**：このモードは、ディスク使用量を最適化するために、特定の間隔でブロック状態を持続させる。 これを有効にするには、`--gcmode full`フラグを使うか、`--gcmode`フラグを完全に省略する。 このモードで動作するノードは**フルノード**と呼ばれる。 一般的な「完全同期」の方法と混同しないように。

フルノードでは、`--state.block-interval NNN` で指定された数（デフォルト：128）の倍数ごとにブロック状態がディスクに永続化される。 また、最近の`--state.tries-in-memory NNN`（デフォルト：128）ブロックのブロック状態は、APIに提供するためにメモリに保持される。 したがって、ブロックの状態は、ブロック間隔の倍数か、最近処理された場合にのみ利用可能である。

```js
// 状態あり
> eth.getBalance('0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266', 150000000)
735000000000002

// 状態なし
> eth.getBalance('0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266', 150000001)
Error: missing trie node 64380a8de7bd83a6421c9ad45ae596a0eebbc7b504d061f4a57c61742eadc804 (path )
	at web3.js:6812:9(39)
	at send (web3.js:5223:62(29))
	at<eval>:1:15(4)
```

:::info

アプリケーションは128の固定ブロック間隔を想定すべきではない。 これはデフォルトだが、ノードは異なる間隔を使用するように設定できる。

:::

#### 正しいオプションの選択

通常、アプリケーションは、nonces、残高、コントラクト・ストレージを含む最新のステート・データにアクセスする必要がある。 アプリや開発者は時折、履歴ブロックのデバッグトレースAPIを利用するかもしれないが、これらのブロックは一般に、最も近い保存状態（例えば、デフォルトのブロック間隔では最大127ブロック前）からトランザクションを再実行することで再作成できる。 そのため、フルノードを稼働させることは、ほとんどのアプリケーションにとって費用対効果の高い選択となる。

しかし、データ分析にはしばしばアーカイブ・ノードが必要になる。 バリデータ情報や報酬など、過去のコンセンサスデータを照会する場合でも、アーカイブノードが必要であることに注意することが重要だ。 なぜなら、コンセンサスデータは特定のブロックの高さにおけるブロックチェーンの状態から得られるからである。

要約すると

- フルノード：最新の状態データへのアクセスや、トレースAPIを介した履歴データへのアクセスを必要とするほとんどのアプリケーションに適しています。
- アーカイブ・ノード：データ分析ツールなど、包括的な履歴状態へのアクセスを必要とするアプリケーションに不可欠。

### ハイブリッド・オプション上流のEN

お使いのノードが主に最新データを配信し、たまに履歴データを配信する場合は、[Upstream EN](../../misc/operation/upstream-en.md)機能をお試しください。 この機能により、アーカイブノードのストレージ要件とフルノードのパフォーマンスのバランスをとることができます。

## チェーンデータ・スナップショット

Chaindata スナップショットは、フル・シンクより高速な代替手段を提供します。 スナップショットとは、同期されたノードのデータ・ディレクトリの圧縮アーカイブ（`.tar.gz`ファイルなど）である。 スナップショットをダウンロードして抽出することで、新しいノードはすべてのブロックを個別に処理することなく、ブロックチェーンに素早く追いつくことができる。 詳しくは[Chaindata Snapshotsを使用する](../../misc/operation/chaindata-snapshot.md)を参照してください。

## スナップ・シンク

現在、Kaia ノードは [Snap Sync](https://geth.ethereum.org/docs/fundamentals/sync-modes) メソッドをサポートしていません。 しかし、chaindata snapshotを使用することで、初期同期の高速化という点で同等の利点が得られる。