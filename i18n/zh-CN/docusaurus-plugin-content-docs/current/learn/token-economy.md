# 代币经济

## 概述<a id="overview"></a>

Kaia 的代币经济旨在创建可持续的融资结构，以增强其生态系统、增长计划和战略投资的能力。 许多公共区块链项目的货币系统只激励节点操作员（矿工或区块生产者），只关注网络维护的技术层面。 然而，这种设计忽略了激励其他类型参与者的重要性，这些参与者为网络代币经济的增长做出了贡献，或投资于长期增长前景。 相比之下，Kaia 的代币经济旨在补偿来自更广泛参与者的更多样化形式的贡献，并具有内置的资金结构，除了维护其区块链节点外，还能获得持续的资源，以推动未来的发展计划和战略投资项目。

## 资金结构<a id="funding-structure"></a>

Kaia 的资金结构与 Kaia 网络的区块生成同步运行。 在每一个新区块中，新发行的 KAIA 和在该区块中使用的交易费总和（统称为 "区块奖励"）都会汇总，并按照预定比例分配给以下三个目标账户：

- 验证者和社区：50
  - 区块建议者奖励：50%的 20%（占总数的 10）
  - 投注奖励：50% 的 80%（占总数的 40）
- 凯亚生态系统基金 (KEF)： 25
- 凯亚基础设施基金 (KIF)：25

9.6 每个新区块都将铸造 KAIA。 这意味着每年将铸造约 3 亿枚 KAIA，相当于市场上 KAIA 代币总量的年通货膨胀率为 5.2%（年通货膨胀率可通过 Kaia 治理流程进行更改）。 交易费按照预先确定的收费表收取和计量。 有关交易费用的详细信息，请参阅 [交易费用](transaction-fees/transaction-fees.md)。

## 卡伊娅管理委员会奖励<a id="kaia-governance-council-reward"></a>

Kaia 治理委员会是核心小区运营商的集体组织。 理事会成员负责维护核心细胞（CCs\ ），这使得理事会成为 Kaia 生态系统中负责提供底层基础设施的重要机构。 要成为理事会成员，候选人必须通过 Kaia 治理程序的资格审查，并且必须持有至少 500 万 KAIA 的股份。 Kaia 治理委员会奖励是一种激励委员会成员继续为 Kaia 生态系统提供稳定基础的结构。

### Kaia 管理委员会奖励机制<a id="kaia-governance-council-reward-mechanism"></a>

对于 Kaia 区块链中的每个区块，都会从 Kaia 治理委员会（GC）中随机抽取成员组成一个委员会。 委员会由一名成员组成，该成员被指定为提案人，其余成员则担任审定人。 一旦一个区块被成功创建并附加到 Kaia 区块链上，区块奖励就会按照前面提到的比例在理事会成员和各种基金之间进行分配。

所有理事会成员被选为特定区块提案人的概率相同，区块提案人奖励在他们之间平均分配。 但是，GC 投注奖励是根据每位会员的 KAIA 投注金额减去最低要求的 500 万 KAIA 按比例分配的。 这意味着，与投注额较低的理事会成员相比，投注额超过最低限额的理事会成员将获得更大份额的投注奖励。 因此，理事会成员有动力投注更多的 KAIA，以最大限度地提高他们从区块奖励分配的 GC 投注奖励部分中获得的潜在奖励。

只要达到 500 万 KAIA 最低注资要求，Kaia 管理委员会成员就可以自由注资或取消注资自己的 KAIA。 定标信息每 86 400 个区块更新一次，新定标的 KAIA 在完成定标后的两个更新周期后生效。 撤销已下赌注的 KAIA 需要延迟一周，以防止恶意会员立即退出。

### 对行为不检议员的处罚<a id="penalty-for-misbehaving-council-members"></a>

安理会成员可能会因以下不当行为而受到处罚。 今后，还可以通过 Kaia 治理流程制定和完善更多的处罚规则。

导致安全故障：

- 被选为提案人的理事会成员不得在同一高度创建一个以上的街区
- 被选为投标人的理事会成员不得故意遗漏某些交易

导致 Liveness 故障：

- 被选为提案人的理事会成员必须创建一个有效的区块
- 被选为验证人的理事会成员必须验证投标人提出的区块

## 凯亚生态系统基金<a id="kaia-ecosystem-fund"></a>

Kaia 生态系统基金（KEF）的成立旨在支持 Kaia 实现更高的透明度和可验证性的使命。 其资金来自创建区块时发放的 KAIA 总额的 25%。

卡亚生态系统基金将用于资助改善卡亚生态系统的活动，例如

1. **服务贡献奖励（SCR）**：KEF 将向在综合生态系统上运营的服务开发商或用户提供奖励，作为对其直接或间接提升生态系统价值的补偿。
2. **建设我们的开发者社区**：KEF 将支持各种活动，包括黑客马拉松、开发教育计划、与学术界的合作研究，以及与各种 DAO 的合作，以培养和发展 Kaia 开发者社区。
3. **促进生态系统服务和基础设施**：基金将支持重要的生态系统基础设施，同时开发具有明显效用的服务，并提供营销支持。
4. \*\* 凯亚生态基金间接投资\*\*：凯亚生态基金将委托专门的加密风险投资公司进行中长期间接投资。 后续投资回收产生的部分利润将被焚烧或返还给卡亚生态系统。
5. **管理委员会预算**：该预算用于游戏、DeFi 和社区等特定部门委员会的运作。 这些委员会旨在发展各自领域的 Kaia 区块链生态系统。
6. **其他生态系统和社区建设活动**

卡亚生态系统基金的管理遵循一个程序，即治理委员会 (GC) 在 [卡亚广场](https://square.klaytn.foundation/Home) 的公共论坛上审查和批准资金的使用。 基金会将向理事会提交每个类别的季度预算提案，供其批准。 在批准的预算范围内，每项具体用途都将由理事会再次审查和批准。 所有执行细节都透明公开。

使用科索沃能源基金的新建议可通过全球理事会提出，但需要个别批准。 计划建立一个结构，使更多的生态系统参与者能够有效地提议和参与使用 KEF。 对于需要专业化和快速决策的类别，可设立单独的治理委员会。

## 凯亚基础设施基金<a id="kaia-infrastructure-fund"></a>

Kaia 基础设施基金 (KIF) 是一个业务基金，将重点关注这些主要类别：

1. **主网和基本基础设施研发**：这包括推进与主网和基础设施相关的最新技术研究、基金会主导的服务开发和基础设施建设。
2. **生态系统加速**：这包括代币交换、为小型 Kaia 区块链生态系统合作伙伴提供资金支持、吸引新的 GC 成员以及提供市场流动性。
3. \*\* 基金会运作\*\*：包括发展、会计、法律事务、IT 基础设施运营、市场营销和人工成本等运营费用，以及财务管理和筹款费用。

KIF 的资金来自创建区块时发放的 KAIA 总额的 25%。 在事先公布每个详细类别的预算计划之后，基金会通过内部控制系统执行预算。 所有执行细节都透明公开。

基金会直接为 KIF 制定预算计划和执行资金。 为确保透明执行，基金会事先和事后都会公布预算计划和执行细节。

## 国库再平衡

为了确保 Kaia 代币经济的灵活性和适应性，我们实施了库房再平衡机制。 这一过程允许根据不断变化的生态系统需求和市场条件对国库资金进行调整。

### 国库再平衡合同

财务再平衡合约是一种智能合约，旨在以透明、安全的方式管理再平衡过程。 合同的核心是以有限状态机的方式运行，经过初始化、注册、批准和最终确定等阶段。 这种结构化的方法可确保所有利益相关方都有机会在变更实施前对其进行审查和批准。

该合同的主要特点包括

1. **灵活的余额调整**：合同支持资金总余额的增加和减少，从而实现全面的资金管理。
2. **统一再平衡时间**：重新平衡区块编号可设置为与相关硬分叉区块编号一致，从而更好地与网络升级同步。
3. **透明执行**：整个再平衡过程通过 TreasuryRebalance 合约记录在区块链上，保持了生态系统对开放性和可验证性的承诺。
4. **验证者共识**：只有在区块验证者达成共识后，重新平衡事件才会发生，以确保整个网络对更改达成一致。
5. **不可更改的记录**：执行后，重新平衡事件的详细备忘录将上传到合约中，提供不可更改的更改记录，供今后参考和审计。

这种财务再平衡机制使 Kaia 能够维持一个动态的、反应灵敏的代币经济，更好地满足其生态系统不断变化的需求，同时坚持透明和安全的原则。 通过利用智能合约技术和基于共识的方法，它提供了一种结构化、可验证的方式来调整国库资金，确保 Kaia 区块链能够适应不断变化的环境，同时保持社区的信任。

有关国库再平衡合同地址的信息，请参阅 [KIP-160](./governance/governance-by-kip.md#kip-160-an-update-on-treasury-fund-rebalancing-)和 [KIP-103](./governance/governance-by-kip.md#kip-103-treasury-fund-rebalancing-)。