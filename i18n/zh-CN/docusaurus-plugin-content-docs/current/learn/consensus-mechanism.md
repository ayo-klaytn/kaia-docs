# 协商一致机制

共识机制（算法）是无信任实体之间达成共识的一种方式。 在区块链技术中，它用于就区块是否有效达成共识。 区块链网络的性能取决于所采用的共识机制的性能，它对区块链应用的可用性有重大影响。

Kaia Mainnet 具有以下性能。

- 每秒可处理 4,000 笔交易。
- 即时交易终结。
- 区块生成时间为一秒。
- 50 多个共识节点可参与共识进程。

在本文件中，我们将介绍 Kaia 如何实施高效共识流程。

## 背景介绍<a id="background"></a>

[比特币](https://en.wikipedia.org/wiki/Bitcoin) 使用的是[PoW](https://en.wikipedia.org/wiki/Proof_of_work)（Proof-of-Work），而以太坊最近转向了[PoS](https://en.wikipedia.org/wiki/Proof_of_stake)（Proof-of-Stake），即通过节点的赌注来决定区块生成节点。 通常，这些算法在确定块的有效性时不涉及节点之间的通信。

因此，在这些系统中，可能会出现分叉，这意味着在同一高度上可以做出两个或多个不同的区块。 通常采用 "最长链获胜 "规则来解决分叉条件。 这意味着这些分叉最终会合并成一条典型链，但也意味着区块列表在属于较短链的一段时间内可以被还原。 因此，在这些算法中，无法保证区块和交易的最终性。 终极性只能在一段时间后以概率的方式实现，而这仍然无法得到百分之百的保证。

在使用区块链平台的面向客户的服务中，这种缺乏终局性是一个非常棘手的问题。 因为它必须等到分叉解决，并且在转移后堆叠了足够多的区块，才能相信交易是不可逆转的。 这对用户和服务提供商都产生了负面影响。

金融服务就是一个简单的例子。 假设用户向某人转账，而服务在 30 到 60 分钟后才能验证转账是否有效。 因为它必须等到分叉合并成一条链，并在转移后堆叠几个区块，以确保交易不可逆转。

### PBFT（实用拜占庭容错技术） <a id="pbft-practical-byzantine-fault-tolerance"></a>

为了避免上述问题，我们需要其他能保证最终性的算法。 BFT 算法就是其中之一，由 Lamport、Shostak 和 Pease 于 1982 年首次[发表](https://dl.acm.org/citation.cfm?doid=357172.357176)。 1999 年，米格尔-卡斯特罗和芭芭拉-利斯科夫提出了 "实用拜占庭容错"（[PBFT](http://www.pmg.csail.mit.edu/papers/bft-tocs.pdf))，它提供高性能状态机复制。

在上述 PoW 算法中，虽然每个节点都会接收并验证区块，但节点之间并没有信息交换来达成共识。 但在 PBFT 中，每个节点都会与其他参与节点通信以达成共识，只要节点能够达成共识，就能保证区块的最终性。

节点之间的通信基本如下图所示。 但也有一些变体，反映了每个系统的特点。

![PBFT message flow](/img/learn/pbft.png)

如上所述，参与 PBFT 的节点基本上分几个阶段与网络中的所有节点进行通信。 这一特性限制了节点的数量，因为通信量会随着节点数量的增加而呈指数增长。

## Kaia 的共识机制<a id="consensus-mechanism-in-kaia"></a>

Kaia 的目标是成为一个企业就绪、以服务为中心的平台。 因此，我们需要解决上面所写的终结性问题，而且网络应该能够允许许多节点参与其中。 为了实现这一点，Kaia 使用了伊斯坦布尔 BFT 的优化版本，该版本实现了 PBFT，并针对区块链网络的特点进行了修改。

在 Kaia 中，有三种节点：CN（共识节点）、PN（代理节点）和 EN（端点节点）。 CN 由 CCO（核心小区运营商）管理，负责区块生成。 这些区块由网络中的所有节点验证。 请参阅 [here](learn.md#network-architecture) 了解有关该网络拓扑的更多信息。

![Network topology](/img/learn/klaytn_network_node.png)

通过采用和改进伊斯坦布尔 BFT，Kaia 实现了快速终结。 由于验证和共识是针对每个区块进行的，因此不会出现分叉，一旦达成共识，就能立即保证区块的最终性。

此外，BFT 算法中通信量增加的问题也通过利用随机选择的 "委员会 "得到了解决。 CN 共同组成一个 "委员会"，在每个区块生成时，使用 VRF（可验证随机函数）选出其中一部分作为 "委员会 "成员。

![Concept of council and committee](/img/learn/council-committee.png)

由于共识信息只在委员会成员之间交换，因此即使 CN 总数增加，通信量也能限制在设计水平之下。

目前，Kaia Mainnet 可以提供每秒 4000 笔交易的高吞吐量，区块生成间隔为一秒。 目前有 50 多个共识节点可以参与 CNN（共识节点网络），随着 Kaia 继续积极优化算法，这一数字还将不断增加。
