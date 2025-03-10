# Kaia 개요

Kaia is a highly optimized, <LinkWithTooltip to="../misc/glossary#bft-based-public-blockchain" tooltip="A blockchain that ensures consensus even if up to 1/3 of nodes act maliciously,<br /> using Byzantine Fault Tolerance (BFT) algorithms to maintain network integrity."> BFT-based public blockchain </LinkWithTooltip> designed to meet enterprise-grade reliability and performance standards. 이 개요에서는 Kaia의 아키텍처, 기능 및 에코시스템에 대해 자세히 설명합니다.

## 주요 설계 목표

Kaia 블록체인은 다음을 목표로 설계되었습니다.

- 트랜잭션의 즉각적 완결성 구현.
- 실사용이 가능한 빠른 트랜잭션 처리 속도 제공.
- 블록체인 애플리케이션 실행 비용 절감.
- 최종 사용자도 사용할 수 있는 낮은 진입 장벽.
- 다양한 산업 분야에서 손쉬운 기술 도입 지원.

## 핵심 사양

Kaia 블록체인의 주요 기능과 성능입니다.

- 1초의 블록 생성 및 확인 시간.
- 초당 4,000건의 트랜잭션 처리 능력.
- 이더리움의 1/10 정도에 불과한 낮은 가스 가격.
- EVM(이더리움 가상 머신) 호환 및 Solidity 컨트랙트 실행 지원.
- 전 세계 유명 기업들로 이루어진 <LinkWithTooltip to="../misc/glossary#kaia-governance-council-kgc" tooltip="A consortium governing Kaia blockchain development and operations.">Kaia 거버넌스 카운슬</LinkWithTooltip> 운영.

## 네트워크 아키텍처

Kaia 네트워크는 세 개의 논리적 하위 네트워크로 구성되어 있습니다.

![Kaia 생태계와 논리적 하위 네트워크 (CCN, ENN, SCN)](/img/learn/klaytn_network_overview.png)

1. **코어 셀 네트워크(CCN)**: 트랜잭션 검증, 실행, 블록 생성을 담당하는 [코어 셀(CC)](../nodes/core-cell)로 구성됩니다.

2. **엔드포인트 노드 네트워크(ENN)**: 서비스 체인의 RPC API 요청을 처리하고 데이터를 처리하는 [엔드포인트 노드(EN)](../nodes/endpoint-node)로 구성됩니다.

3. **[서비스 체인](../nodes/service-chain) 네트워크(SCN)**: dApp이 독립적으로 운영하는 보조 블록체인으로, EN을 통해 메인 체인에 연결됩니다.

### 노드 유형

![Kaia 메인체인 물리 토폴로지 및 계층화된 구조(CNN, PNN, ENN)](/img/learn/klaytn_network_node.png)

1. **코어 셀(CC)**: 하나의 컨센서스 노드(CN)와 두 개의 프록시 노드(PN)로 구성됩니다.

  - **컨센서스 노드(CN)**: 블록 생성에 참여합니다.
  - **프록시 노드(PN)**: 네트워크 인터페이스를 제공하고, 트랜잭션 요청을 전송하며, 블록을 전파합니다.

2. **엔드포인트 노드(EN)**: API 요청 및 데이터 프로세싱을 처리하는 네트워크 엔드포인트 역할을 합니다.

3. **부트노드**: 새로운 노드의 네트워크 가입을 돕기 위해 Kaia에서 운영하는 특별 노드입니다.

## 합의 알고리즘

Kaia는 최적화된 버전의 이스탄불 BFT를 사용하여 블록체인에 맞게 수정된 실용적 비잔틴 장애 허용(PBFT)을 구현합니다. 합의 프로세스는 다음과 같이 구성되어 있습니다.

1. 검증 가능한 랜덤 함수(VRF)를 사용하여 위원회(<LinkWithTooltip to="../misc/glossary#proposer" tooltip="A randomly chosen consensus node for block creation.">제안자</LinkWithTooltip> 및 <LinkWithTooltip to="../misc/glossary#validator" tooltip="A node verifying data, ensuring efficient block processing.">검증자</LinkWithTooltip>) 선출.
2. 선출된 제안자가 블록 생성.
3. 위원회가 블록을 검증하고 서명.

이 [합의 메커니즘](consensus-mechanism.md)을 통해 Kaia는 초당 4,000건의 트랜잭션을 처리하는 고성능을 달성하고 즉각적인 트랜잭션 완결성을 확보할 수 있었습니다.

## 블록 생성 및 전파

- 블록은 1초 간격을 목표로 라운드 단위로 생성됩니다.
- 제안자 및 위원회 선정은 무작위로 이루어지지만 결정론적입니다.
- 블록 확정에는 위원회 위원 3분의 2 이상의 서명이 필요합니다.
- 블록과 트랜잭션에 대한 별도의 전파 채널(멀티채널 접근 방식)로 네트워크 정체를 관리합니다.

## Kaia 가상 머신 (KVM)

Kaia 가상 머신(KVM)은 스마트 컨트랙트 실행을 위한 강력한 환경을 제공합니다:

- 이더리움 가상 머신(EVM) 기반.
- 모든 EVM opcode와 추가로 Kaia 전용으로 사전 컴파일된 컨트랙트를 지원합니다.
- 솔리디티 및 이더리움 개발 도구(예: Remix, Hardhat, Foundry)와 호환됩니다.
- 개발자는 최소한의 수정만으로 이더리움 스마트 컨트랙트를 Kaia로 이식할 수 있습니다.

## 보안 대책

Kaia는 몇 가지 보안 대책을 시행하고 있습니다:

- 블록 제안자를 무작위로 선정하여 프로세스에 예측 불가능성을 더하는 VRF.
- 검증자 키와 보상 키를 분리하여 잠재적인 키 도난으로부터 검증자 보호.
- 모든 위원회 구성원이 제안된 블록의 서명을 검증하는 투명한 블록 검증 프로세스.

## 상호 운용성

Kaia는 다른 블록체인 네트워크와 원활하게 상호작용할 수 있도록 설계되었습니다:

- <LinkWithTooltip tooltip="A blockchain that can run smart contracts and <br/> interact with the Ethereum Virtual Machine(EVM)">EVM-compatible</LinkWithTooltip>, allowing easy deployment of Ethereum smart contracts.
- 다른 EVM-SDK 기반 체인과 상호 운용 가능하도록 설계되었습니다.
- 크로스 플랫폼 트랜잭션과 스마트 컨트랙트 실행을 지원합니다.

## 토큰 이코노미

카이아의 네이티브 토큰인 [카이아](./token-economics/kaia-native-token.md)는 블록체인 경제에서 중심적인 역할을 합니다:

- KAIA 토큰은 새로운 블록이 생성될 때마다 자동으로 발행됩니다.
- 초기 연간 인플레이션율: 5.2%.
- 블록 보상은 다음과 같이 배분됩니다:
  - CCO 및 커뮤니티: 50% (블록 생성자 보상 20%, 스테이킹 보상 80%)
  - KEF (Kaia Ecosystem Fund): 25%
  - KIF (Kaia Infrastructure Fund): 25%

이 분배 모델은 네트워크 참여를 장려하는 동시에 Kaia 생태계의 성장과 발전을 지원합니다.

## 거버넌스

Kaia는 공정하고 포용적으로 설계된 온체인 거버넌스 시스템을 구현합니다:

- 투표권은 스테이킹한 KAIA 토큰 수량에 비례합니다.
- 투표권에 상한을 두어 소수의 의견을 억압하는 것을 방지합니다.
- 투표권 위임이 허용됩니다.
- 모든 거버넌스 제안은 온체인에 기록되어 투명성을 보장합니다.

## 감사 기능 및 투명성

Kaia는 투명성과 감사 가능성을 우선시합니다:

- 모든 트랜잭션은 변경 불가능하고 검증 가능한 상태 변경 기록을 제공합니다.
- 블록체인 탐색을 위한 두 가지 기본 도구:
  - [Kaiascope](https://kaiascope.com/): 통합형 블록체인 탐색기.
  - [Kaiascan](http://kaiascan.io/): 사용자 친화적인 인터페이스를 가진 블록체인 데이터 간편 조회 탐색기.
- “Square” 투표 플랫폼은 모든 비용과 분기별로 알려진 거래 내역을 공개합니다.

## 네트워크 모니터링

최적의 성능과 안정성을 보장하기 위해 Kaia는 다음을 구현합니다:

- 네트워크 정체를 관리하기 위한 멀티채널 접근 방식.
- 모든 검증자를 위한 전용 네트워크 모니터링.
