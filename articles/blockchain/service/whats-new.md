---
title: 새로운 기능은 무엇입니까? 릴리스 정보 - Azure Blockchain Service
description: 최신 릴리스 정보, 버전, 알려진 문제 및 예정된 변경 사항과 같은 Azure Blockchain Service의 새로운 기능을 알아봅니다.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea44366dd88ae1b98865727c9a3d3e038c924120
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "122642097"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure Blockchain Service의 새로운 기능

> 이 URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us`를 RSS 피드 판독기 [![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522)에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 할 때 알림을 받습니다.

Azure Blockchain Service는 지속적으로 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 새로운 기능
- 버전 업그레이드
- 알려진 문제

---

## <a name="may-2021"></a>2021년 5월

[!INCLUDE [Retirement note](./includes/retirement.md)]

## <a name="june-2020"></a>2020년 6월

### <a name="version-upgrades"></a>버전 업그레이드

- Quorum 버전을 2.6.0으로 업그레이드합니다. 버전 2.6.0을 사용하여 서명된 프라이빗 트랜잭션을 보낼 수 있습니다. 프라이빗 트랜잭션 전송에 대한 자세한 내용은 [Quorum API 문서](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis)를 참조하세요.
- Tessera 버전을 0.10.5로 업그레이드합니다.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>계약 크기 및 트랜잭션 크기가 128KB로 증가됨

유형: 구성 변경

계약 크기(MaxCodeSize)가 128KB로 증가되어 더 큰 크기의 스마트 계약을 배포할 수 있습니다. 또한 트랜잭션 크기(txnSizeLimit)가 128KB로 증가되었습니다. 구성 변경 사항은 2020년 6월 19일 이후 Azure Blockchain Service에서 생성된 새로운 컨소시엄에 적용됩니다.

### <a name="trietimeout-value-reduced"></a>TrieTimeout 값 감소

유형: 구성 변경

메모리 내 상태가 디스크에 더 자주 기록되도록 TrieTimeout 값이 감소되었습니다. 노드 충돌이 드문 경우 값이 낮을수록 노드를 빠르게 복구할 수 있습니다.

## <a name="may-2020"></a>2020년 5월

### <a name="version-upgrades"></a>버전 업그레이드

- Ubuntu 버전을 18.04로 업그레이드
- 2\.5.0로 Quorum 버전 업그레이드
- Tessera 버전을 0.10.4로 업그레이드

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure Blockchain Service는 rawPrivate 트랜잭션 전송을 지원합니다.

형식: 기능

고객은 노드의 계정 외부에서 프라이빗 트랜잭션에 서명할 수 있습니다.

### <a name="two-phase-member-provisioning"></a>2단계 멤버 프로비저닝

형식: 향상

두 단계는 기존의 오랜 컨소시엄에서 구성원이 생성되는 시나리오를 최적화하는 데 도움이 됩니다. 멤버 인프라는 첫 번째 단계에서 프로비저닝됩니다. 두 번째 단계에서는 멤버가 블록체인과 동기화됩니다. 2단계 프로비저닝은 시간 초과로 인한 구성원 생성 실패를 방지하는 데 도움이 됩니다.

## <a name="known-issues"></a>알려진 문제

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>eth.estimateGas 함수가 Quorum v2.6.0에서 예외를 throw합니다.

Quorum v2.6.0에서 추가 *값* 매개 변수를 제공하지 않고 *eth.estimateGas* 함수를 호출하면 *메서드 처리기가 충돌됨* 예외가 발생합니다. Quorum 팀에 통보되었으며 2020년 7월 말에 수정될 예정입니다. 수정을 사용할 수 있을 때까지 다음 해결 방법을 사용할 수 있습니다.

- 성능에 영향을 미칠 수 있으므로 *eth.estimateGas* 를 사용하지 마세요. eth.estimateGas 성능 문제에 대한 자세한 내용은 [eth.estimateGas 함수를 호출하면 성능이 저하됨](#calling-ethestimategas-function-reduces-performance)을 참조하세요. 각 트랜잭션에 gas 값을 포함합니다. gas 값이 제공되지 않아 Quorum v2.6.0에 크래시가 발생한 경우 대부분의 라이브러리는 eth.estimateGas를 호출합니다.
- Quorum 팀의 제안 사항으로, *eth.estimateGas* 를 호출해야 하는 경우 해결 방법으로 추가 매개 변수 *값* 을 *0* 으로 전달하는 것이 좋습니다.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>유효성 검사기 노드가 4개 미만이면 채굴이 중지됩니다.

프로덕션 네트워크에는 최소 4개의 유효성 검사기 노드가 있어야 합니다. Quorum은 IBFT 크래시 내 결함성(3F+1)을 충족하기 위해 최소 4개의 유효성 검사기 노드가 필요하다고 권장합니다. 4개의 유효성 검사기 노드를 얻으려면 Azure Blockchain Service *표준* 계층 노드가 2개 이상 있어야 합니다. 표준 노드는 두 개의 유효성 검사기 노드로 프로비저닝됩니다.

Azure Blockchain Service의 블록체인 네트워크에 4개의 유효성 검사기 노드가 없으면 네트워크에서 채굴이 중지될 수 있습니다. 처리된 블록에 대한 경고를 설정하여 채굴이 중지된 것을 감지할 수 있습니다. 정상 네트워크에서 처리된 블록은 5분마다 노드당 60블록으로 처리됩니다.

완화하기 위해 Azure Blockchain Service 팀은 노드를 다시 시작해야 합니다. 고객은 노드를 다시 시작하려면 지원 요청을 열어야 합니다. Azure Blockchain Service 팀은 채굴 문제를 자동으로 검색하고 수정하는 작업을 수행합니다.

프로덕션 등급 배포에 *표준* 계층을 사용합니다. 개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain Data Manager에는 표준 계층 노드가 필요합니다.

Blockchain Data Manager를 사용하는 경우 *표준* 계층을 사용합니다. *기본* 계층에는 4GB 메모리만 있습니다. 따라서 Blockchain Data Manager 및 해당 서비스에서 실행되는 다른 서비스에 필요한 사용으로 확장할 수 없습니다.

개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>대량 잠금 해제 계정 호출로 인해 geth에서 크래시가 발생합니다.

트랜잭션을 제출하는 동안 많은 볼륨의 잠금 해제 계정 호출로 인해 geth의 트랜잭션 노드에 크래시가 발생할 수 있습니다. 따라서 수집 트랜잭션을 중지해야 합니다. 그러지 않으면 보류 중인 트랜잭션 큐가 늘어납니다.

geth는 1분 이내에 자동으로 다시 시작됩니다. 노드에 따라 동기화를 수행하는 데 시간이 오래 걸릴 수 있습니다. Azure Blockchain Service 팀은 동기화 시간을 단축하는 보관 기능을 사용하도록 설정합니다.

geth 크래시를 식별하기 위해 애플리케이션 로그의 블록체인 메시지에서 오류 메시지에 대한 로그를 확인할 수 있습니다. 보류 중인 트랜잭션이 증가하는 동안 처리된 블록이 감소하는지 확인할 수도 있습니다.

문제를 완화하려면 계정 잠금을 해제하는 명령을 사용하여 서명되지 않은 트랜잭션을 보내는 대신 서명된 트랜잭션을 보냅니다. 이미 외부에서 서명된 거래의 경우 계정을 잠금 해제할 필요가 없습니다.

서명되지 않은 트랜잭션을 보내려면 잠금 해제 명령에서 시간 매개 변수로 0을 전송하여 무한 시간 동안 계정을 잠금 해제합니다. 모든 트랜잭션이 전송된 후에 다시 계정을 잠글 수 있습니다.

다음은 Azure Blockchain Service에서 사용하는 geth 매개 변수입니다. 이 매개 변수는 조정할 수 없습니다.

- 이스탄불 블록 기간: 5초
- gas 하한: 700000000
- gas 상한: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>대용량의 프라이빗 트랜잭션이 있으면 성능이 저하됩니다.

Azure Blockchain Service 기본 계층 및 프라이빗 트랜잭션을 사용하는 경우 Tessera에 크래시가 발생할 수 있습니다.

Blockchain 애플리케이션 로그를 검토하고 `Tessera crashed. Restarting Tessera...` 메시지를 검색하여 Tessera 크래시를 감지할 수 있습니다.

Azure Blockchain Service는 크래시가 발생하면 Tessera를 다시 시작합니다. 다시 시작하는 데 약 1분이 걸립니다.

많은 양의 프라이빗 트랜잭션을 보내는 경우 *표준* 계층을 사용합니다. 개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

### <a name="calling-ethestimategas-function-reduces-performance"></a>eth.estimateGas 함수를 호출하면 성능이 저하됩니다.

*eth.estimateGas* 함수를 여러 번 호출하면 초당 트랜잭션 수가 크게 감소합니다. 각 트랜잭션 제출에 *eth.estimateGas* 함수를 사용하지 마세요. *eth.estimateGas* 함수는 메모리를 많이 사용합니다.

가능하면 트랜잭션 제출에 보수적인 gas 값을 사용하고 *eth.estimateGas* 사용을 최소화하세요.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>스마트 계약의 바인딩되지 않은 루프로 인한 성능 저하

성능을 저하시킬 수 있으므로 스마트 계약에 바인딩되지 않은 루프를 사용하지 마세요. 자세한 내용은 다음 자료를 참조하세요.

- [바인딩되지 않은 루프 방지](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [스마트 계약 보안 모범 사례](https://github.com/ConsenSys/smart-contract-best-practices)
- [Quorum에서 제공하는 스마트 계약 지침](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Solidity에서 제공하는 gas 제한 및 루프에 대한 지침](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)