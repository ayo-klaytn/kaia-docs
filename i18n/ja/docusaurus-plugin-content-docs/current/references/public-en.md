# パブリックJSON RPCエンドポイント

一般に公開されているJSON-RPCエンドポイントでは、独自のノードを実行することなく、Kaiaネットワークとのインタラクションを提供することで、ブロックチェーン製品のテストや実行が可能です。

独自のカイア・エンドポイント・ノード（EN）を運用するのは簡単ではなく、技術的な専門知識、モニタリング、コンピューティング・リソースが必要です。 ストレージやネットワーク帯域幅を維持するためのコストがかかるだけでなく、エンジニアリングの時間とリソースを割かなければならないです。

したがって、既存のパブリックENを利用する主なメリットは、Kaiaネットワークに接続し、相互作用するためのインフラを維持することに気を取られることなく、ブロックチェーン製品の構築とテストのみに集中できることです。

## 考慮すべきこと

- ノードプロバイダーは、トラフィックやノードとの相互作用に関連して発生した損害や損失について責任を負いません。
- 特定のノードにトラフィックが集中すると、サービスの遅延が発生することがあります。
- リクエストが集中するのを防ぐため、ノードごとに料金の制限が適用される場合があります。

## パブリックJSON-RPCエンドポイント

以下は、カイアのパブリックノードプロバイダーが提供するネットワークドメインのリストです。

:::info[Outdated エンドポイントの動作停止］

なお、以下のURLは2024年9月末日をもって廃止となりました。 サービスが中断しないよう、適宜設定を更新することをお勧めします：

**メインネット**

- `https://public-en-cypress.klaytn.net` (`https://public-en.node.kaia.io` に置き換わる)
- `https://archive-en.cypress.klaytn.net` (`https://archive-en.node.kaia.io` に置き換わる)

**テストネット**

- `https://public-en-baobab.klaytn.net` (`https://public-en-kairos.node.kaia.io` に置き換わる)
- `https://archive-en.baobab.klaytn.net` (`https://archive-en-kairos.node.kaia.io` に置き換わる)

:::

### メインネット公開JSON-RPCエンドポイント

これらのエンドポイントは、テストと開発の目的でコミュニティに提供されていることに留意してください。
エンドポイントの稼働時間や安定性は保証できませんので、営利目的での使用はご遠慮ください。

**HTTPS**

| サービスプロバイダー                            | エンドポイント                                                                                     | 名前空間           | タイプ   |
| ------------------------------------- | ------------------------------------------------------------------------------------------- | -------------- | ----- |
| [カイア財団](https://www.kaia.io)          | `https://public-en.node.kaia.io`                                                            | カイア、クレイ、エス、ネット | フル    |
|                                       | `https://archive-en.node.kaia.io`                                                           | カイア、クレイ、エス、ネット | アーカイブ |
| [クイックノード](https://quicknode.com/)     | `https://alpha-hardworking-orb.kaia-mainnet.quiknode.pro/`                                  | カイア、クレイ、エス、ネット | フル    |
| [BlockPIネットワーク](https://blockpi.io/)  | `https://kaia.blockpi.network/v1/rpc/public`                                                | カイア、クレイ、エス、ネット | フル    |
| [OnFinality](https://onfinality.io/)  | `https://klaytn.api.onfinality.io/public`                                                   | カイア、クレイ、エス、ネット | フル    |
| [ポクト・ネットワーク](https://pocket.network/) | `https://kaia-mainnet.rpc.grove.city/v1/803ceedf`                                           | カイア、クレイ、エス、ネット | フル    |
| [GetBlock](https://getblock.io/)      | `https://go.getblock.io/d7094dbd80ab474ba7042603fe912332`                                   | カイア、クレイ、エス、ネット | フル    |
| [dRPC](https://drpc.org/)             | `https://klaytn.drpc.org`                                                                   | カイア、クレイ、エス、ネット | フル    |
| [ニルヴァーナ・ラボ](https://nirvanalabs.io/)  | `https://kaia.nirvanalabs.xyz/kaiaennode-499hw?apikey=2b4f3ffc4668c6df22c8b09e8dab80ff5eb2` | カイア、クレイ、エス、ネット | アーカイブ |

**ウェブソケット**

| サービスプロバイダー                          | エンドポイント                                                                                                                                   | 名前空間           | タイプ   |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ----- |
| [カイア財団](https://www.kaia.io)        | `wss://public-ja.node.kaia.io/ws`。                                                                                                        | カイア、クレイ、エス、ネット | フル    |
|                                     | `wss://archive-ja.node.kaia.io/ws`。                                                                                                       | カイア、クレイ、エス、ネット | アーカイブ |
| [クイックノード](https://quicknode.com/)   | wss://alpha-hardworking-orb.kaia-mainnet.quiknode.pro/\`. | カイア、クレイ、エス、ネット | フル    |
| [オンファイナリティ](https://onfinality.io/) | `wss://klaytn.api.onfinality.io/public-ws`。                                                                                               | カイア、クレイ、エス、ネット | フル    |
| [dRPC](https://drpc.org/)           | wss://klaytn.drpc.org\`。                                                                  | カイア、クレイ、エス、ネット | フル    |

### Testnet (Kairos) パブリックJSON-RPCエンドポイント

**HTTPS**

| サービスプロバイダー                           | エンドポイント                                                      | 名前空間           | タイプ   |
| ------------------------------------ | ------------------------------------------------------------ | -------------- | ----- |
| [カイア財団](https://www.kaia.io)         | `https://public-en-kairos.node.kaia.io`                      | カイア、クレイ、エス、ネット | フル    |
|                                      | `https://archive-en-kairos.node.kaia.io/`                    | カイア、クレイ、エス、ネット | アーカイブ |
| [クイックノード](https://quicknode.com/)    | `https://responsive-green-emerald.kaia-kairos.quiknode.pro/` | カイア、クレイ、エス、ネット | フル    |
| [BlockPIネットワーク](https://blockpi.io/) | `https://kaia-kairos.blockpi.network/v1/rpc/public`          | カイア、クレイ、エス、ネット | フル    |

**ウェブソケット**

| サービスプロバイダー                        | エンドポイント                                                    | 名前空間           | タイプ   |
| --------------------------------- | ---------------------------------------------------------- | -------------- | ----- |
| [カイア財団](https://www.kaia.io)      | `wss://public-en-kairos.node.kaia.io/ws`。                  | カイア、クレイ、エス、ネット | フル    |
|                                   | `wss://archive-ja-kairos.node.kaia.io/ws`。                 | カイア、クレイ、エス、ネット | アーカイブ |
| [クイックノード](https://quicknode.com/) | `wss://responsive-green-emerald.kaia-kairos.quiknode.pro/` | カイア、クレイ、エス、ネット | フル    |

## RPCサービスプロバイダー

以下はカイアのパブリック・ノード・プロバイダーのリストです。

### Klaytn API Service (KAS)

KASは、より簡単で迅速なブロックチェーンアプリケーション開発をサポートする様々なAPIを提供している。 開発期間を大幅に短縮し、安定したサービスを運用し、コストを削減することができます。

#### 特徴

- 無料プランの場合、10,000リクエスト/日（100リクエスト/秒
- 無料プランのコミュニティサポート、有料プラン（Starter、Pro、Pro Plus）のチケットサポート
- Klaytn Node API, Token History API, Wallet API, Anchor API, KIP-7, 17, 37 API and Metadata API

#### 参考文献

- [ドキュメント](https://www.klaytnapi.com/en/resource/docs/readme)
- [購読](https://www.klaytnapi.com/en/landing/pricings)
- [ウェブサイト](https://www.klaytnapi.com/en/landing/main)

### チェーンスタック

[Chainstack](https://chainstack.com/) は、Web3インフラストラクチャのリーディングプロバイダーであり、Kaia用の無料および有料のエンドポイントを提供している。 無料のDeveloperプランは、月間300万リクエスト、毎秒25リクエスト（RPS）からスタートします。 有料プランで簡単に拡張できる。

無料のDeveloperプランのエンドポイントを使い始めるには、電子メール、またはGitHubやX（Twitter）などのソーシャルアカウントでサインアップするだけです。

#### 特徴

- 無料デベロッパープランに対応
- 有料プランへの拡張をサポート
- 従量制
- 25以上のボックチェーンに対応
- アップタイム99.9%以上
- ロードバランシング
- 無限のスケーラビリティ
- ソーシャルアカウントで登録
- カイアトークンを含む暗号でトップアップする
- Telegram、Eメール、豊富なドキュメントなどによるサポート
- Web3専属コピーライターによるカスタマーストーリー

#### 参考文献

- [ドキュメント](https://chainstack.com/build-better-with-kaia/)
- [購読](https://chainstack.com/pricing/)
- [ウェブサイト](https://chainstack.com/)

### All That Node

All That Nodeは、Web3インフラへの信頼できるゲートウェイとなることを目指しており、ビルダーはブロックチェーン・ネットワークに関する問題に惑わされることはない。 オール・ザット・ノードは、RPCノードへの高速かつ堅牢な接続を最低のレイテンシー性能で保証します。

#### 特徴

- エコシステムのためのパブリック・ノードと蛇口
- さらに必要な場合は、従量制プランに対応
- 専用ノード
- 24以上のブロックチェーンをサポート
- アーカイブデータあり
- 利用可能なWebsocket API
- トレース/デバッグAPIが利用可能
- アップタイム99.9%以上
- ロードバランシングの実施
- 無限のスケーラビリティ
- Discordコミュニティによる24時間365日のサポート

#### 参考文献

- [ドキュメント](https://docs.allthatnode.com/)
- [購読](https://www.allthatnode.com/pricing.dsrv)
- [ウェブサイト](https://www.allthatnode.com/main.dsrv)

### Tatum

Tatumは、ブロックチェーンアプリを構築、テスト、実行する最速の方法です。 開発者がブロックチェーンのアイデアを素早く実現できるよう、最も柔軟なプラットフォームを提供しています。

#### 特徴

- 無料プランは毎秒5リクエスト、有料プラン（スタート、ベーシック）は毎秒200リクエスト
- 地域支援

#### 参考文献

- [ドキュメント](https://apidoc.tatum.io/tag/Kaia?_gl=1*1dhfv8u*_ga*MzY5NDMyNzg5LjE2NDQ1NTk1MzA.*_ga_BH6F6RKJW6*MTY2MjAxNDQ0OS4xNy4xLjE2NjIwMTQ2MTQuMjQuMC4w)
- [価格設定](https://tatum.io/pricing)
- [ウェブサイト](https://tatum.io/)

### BlockPI

BlockPI Networkは、高品質で堅牢かつ効率的なRPCサービスの提供を目指しています。 一点障害とスケーラビリティの制限を避けるため、ネットワークは拡張可能なRPCノードを持つ分散構造となるように設計されている。

BlockPIは、Kaiaコミュニティには無料のパブリックエンドポイントを、有料ユーザには高度な機能を提供しています。  BlockPIは2つの有料パッケージを設計し、柔軟なユーザーニーズを満たすためにPay As You Goをサポートしています。 各パッケージの料金詳細 (https://docs.blockpi.io/documentations/pricing) およびカイアの個別メソッド費用 (https://docs.blockpi.io/documentations/request-unit-ru) を確認できます。

#### 特徴

- 無料サービスでは毎秒20リクエスト、有料パッケージでは無制限。
- Kaiaアーカイブノードとエンドポイントノードの選択
- エンドポイントノードのホワイトリストが可能
- WSSが利用可能で、近日中にサブスクリプションが開始される
- トレースをサポート

#### 参考文献

- [ドキュメント](https://docs.blockpi.io/)
- [購読](https://dashboard.blockpi.io/wallet/overview)
- [ウェブサイト](https://blockpi.io/)

### グローブ 🌿 - ポケットネットワークで構築

グローブはポケットネットワークの構築者であり、保守者でもあるチームだ。 ポケットネットワークは、Web3ノードのインフラストラクチャのTCP/IPであり、RPCノードにDAppsとそのユーザーにWeb3アクセスを止められないように提供するインセンティブを与えるマルチチェーン中継プロトコルです。

Groveは50以上のブロックチェーンをサポートしており、随時追加されている。

#### 特徴

- 分散型RPCプロトコルとマーケットプレイス
- 150,000リクエスト/日 無料ティア（最大2アプリケーション、エンドポイント数無制限）
- パブリック・エンドポイント
- 無制限、有料プラン（1日あたり15万件以上のリクエストが必要な場合）
- 50以上のブロックチェーンをサポート
- 25,000 + アプリケーションの提供でPOKTを獲得するノード
- アーカイバル・ノード、トレース・サポート付きアーカイバル・ノード
- 単一障害点なし
- ダウンタイムゼロ
- プロトコルに組み込まれた負荷分散
- 1日あたりのリクエスト数と1時間あたりのノード数を無限に拡張できます。
- 最もプライベートで検閲に強いオプション
- ハンズオン開発者サポート

#### 参考文献

- [ドキュメント](https://docs.grove.city/kaia-api/intro)
- [グローブウェブサイト](https://grove.city/)
- [ポケットネットワークウェブサイト](https://pocket.network)
- [グローブポータル](https://portal.grove.city)

### ANKR

Ankrの分散ノード・ネットワークは強力な相乗効果を生み出し、開発者はパブリック・エンドポイントに簡単かつ安全に接続できる。 リソースの使用を最適化するきめ細かなキャッシングにより、Ankrは分散型アプリケーションを構築する際の優れた効率性を実現する低レイテンシー性能とともに、高速なRPCリクエストを保証します。

#### 特徴

- 無料プランでは毎秒500リクエスト、プレミアムでは1,500リクエスト。 リクエストに応じてアップグレードも可能だ。
- 無料プランではDiscordとサポートポータル、プレミアムでは専用サポートが利用できる。
- WebSocketはプレミアムプランでご利用いただけます。

#### 参考文献

- [ドキュメント](https://www.ankr.com/docs/build-blockchain/overview)
- [購読](https://www.ankr.com/rpc/pricing/)
- [ウェブサイト](https://www.ankr.com/rpc/)

### NodeReal

NodeRealはブロックチェーンのインフラとサービスのプロバイダーである。 NodeRealは、開発者や投資家が最も信頼できるソリューションでブロックチェーンを探求するのを支援します。

#### 特徴

- 無料ティア、APIキー3個、月間3億5,000万コンピュート・ユニット（CU）、月間300コンピュート・ユニット/秒（CUPS）、アーカイブ・データ
- グロース・ティア、APIキー15個、月間5億コンピュート・ユニット(CU)、月間700コンピュート・ユニット/秒(CUPS)、アーカイブ・データ、デバッグ＆トレースAPI
- エンタープライズ・ティア、カスタムAPIキー数、カスタム月間使用量、専用サポート、サービス・レベル・アグリーメント(SLA)およびその他の要件
- 50クエリー/秒（QPS）/メソッド

#### 参考文献

- [ドキュメント](https://docs.nodereal.io/docs/getting-started)
- [購読](https://nodereal.io/api-marketplace/klaytn-rpc)
- [ウェブサイト](https://nodereal.io)

### Nodit

Noditは、誰もがアクセスできるエンタープライズグレードのWeb3インフラを提供することを目指している。 99.9%の稼働率を誇る堅牢なノード・インフラと、信頼性の高いすぐに照会可能なブロックチェーン・データを手頃な価格で提供することで、開発者のWeb3の世界への参入を促進しています。

#### 特徴

- カイヤテストネット公式蛇口 [https://kaiafaucet.com](https://kaiafaucet.com)
- 99.9%以上のアップタイム
- Datasquareのインデックス付きアーカイブデータに無料でアクセス - ダッシュボードとSQLに対応
- データパイプラインの統合サポート
- 上位デベロッパー・ティアでオートスケーリングに対応
- NFT、トークン、統計などのための100以上のWeb3データAPI
- WebhookとStream(WebSocket)が利用可能
- 専用ノード
- 350,000,000コンピュート・ユニット(CU)/月、無料ティア付き
- ログ監視ダッシュボード

#### 参考文献

- [ウェブサイト](https://nodit.io)
- [データスクエア ウェブサイト](https://datasquare.nodit.io)
- [ドキュメント](https://developer.nodit.io)
- [ブログ](https://blog.nodit.io)

### GetBlock

GetBlockは、Kaiaを含む50以上の主要ブロックチェーンネットワークのフルRPCノードへの高速で信頼性の高いAPIアクセスを提供します。 GetBlockがノードのメンテナンスを行うことで、開発者や企業はインフラに頭を悩ませることなく、dAppsやブロックチェーン・ソリューションの構築に集中することができる。

#### 特徴

- 毎日40,000件のリクエストがあり、60RPSまで無料で利用できる。 また、より良いパフォーマンスとサポートのために、スタータープランと無制限プランがある。
- 99.9%以上のアップタイム
- RPCノードによる50以上のブロックチェーン・プロトコルへの無料アクセス
- 高度なモニタリングと統計
- 専用ノード

#### 参考文献

- [ウェブサイト](https://getblock.io/)
- [ドキュメント](https://getblock.io/docs/getblock-explorer/get-started/)

### QuickNode

Quicknodeは、安全で分散型のイノベーションを促進するブロックチェーンインフラストラクチャを提供しています。 そのすべては、比類のないグローバルバランスのインフラ、保証された信頼性とセキュリティ、ユーザーフレンドリーなインターフェイス、そしてエンドツーエンドのカスタマーサポートによって支えられています。

#### 特徴

- 15リクエスト/秒、無料プランで10M APIクレジット。
- プロバイダーのスタータープラン、グロースプラン、ビジネスプランでは、より多くのクレジットとIPFSストレージをご利用いただけます。
- 実績のあるRPCおよびAPIインフラストラクチャは、パフォーマンスと信頼性をアットスケールで提供します。
- 超強力なdAppsをこれまで以上に簡単に構築できる数十のアドオンがある。
- 最も強力なリアルタイム・データ・パイプラインであるストリームを提供します。
- イベントアラート

#### 参考文献

- [ウェブサイト](https://www.quicknode.com/)
- [ドキュメント](https://www.quicknode.com/docs/welcome)

### dRPC

Kaia、Ethereum、Polygon、Arbitrumなどへの分散型RPCノードプロバイダ。 信頼できるWeb3のインフラパートナーです。

#### 特徴

- GeneralサポートのPublicノードはリクエスト制限なし。
- 高性能ノード向けには、グロースプランとエンタープライズプランもご用意しています。
- 100以上のネットワークにまたがる90以上のブロックチェーン
- AIロードバランサー
- 洞察に満ちた分析。

#### 参考文献

- [ウェブサイト](https://drpc.org/)
- [ドキュメント](https://drpc.org/docs)

## 有用なリソース

- ウォレット[カイアウォレット](../build/tools/wallets/kaia-wallet.md)は、カイアネットワークのブラウザ拡張ウォレットです。

- 蛇口：[KAIA[Faucet](https://faucet.kaia.io)からKairosテストネットワーク用のテストKAIAを入手できます。

- エクスプローラ[KaiaScan](../build/tools/block-explorers/kaiascan.md)はカイアネットワークのブロックエクスプローラーです。

- ChainID : Kairos: 1001 (0x3E9), Mainnet：8217 (0x2019)

- ガス価格：[25, 750]の範囲内で動的に調整される。 範囲はオンチェーンガバナンスで変更できる。 詳細については、[ガバナンス](https://docs.kaia.io/references/json-rpc/governance/chain-config/) および
  [取引手数料](../learn/transaction-fees/transaction-fees.md) をご参照ください。
