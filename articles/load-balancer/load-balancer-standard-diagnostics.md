---
title: 메트릭, 경고 및 리소스 상태를 사용하는 진단 - Azure 표준 Load Balancer
description: 사용 가능한 메트릭, 경고 및 리소스 상태 정보를 사용하여 Azure 표준 Load Balancer를 진단합니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2021
ms.author: allensu
ms.openlocfilehash: d044ddbde293721e26ed491e237aa5b89075f72a
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531215"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>메트릭, 경고 및 리소스 상태를 사용하는 표준 Load Balancer 진단

Azure 표준 Load Balancer는 다음과 같은 진단 기능을 제공합니다.

* **다차원 메트릭 및 경고**: [Azure Monitor](../azure-monitor/overview.md)를 통해 표준 부하 분산 장치 구성에 대한 다차원 진단 기능을 제공합니다. 표준 부하 분산 장치 리소스를 모니터링 및 관리하고 관련 문제를 해결할 수 있습니다.

* **리소스 상태**: Load Balancer의 Resource Health 상태는 모니터의 Resource Health 페이지에서 확인할 수 있습니다. 이 자동 검사는 Load Balancer 리소스의 현재 가용성을 알려 줍니다.
이 문서에서는 이러한 기능에 대한 간단한 설명을 제공하고 표준 Load Balancer에 이러한 기능을 사용하는 방법을 제공합니다. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>다차원 메트릭

Azure Load Balancer는 Azure Portal에서 Azure Metrics를 통해 다차원 메트릭을 제공하고, 부하 분산 장치 리소스를 실시간으로 진단 분석하도록 도와줍니다. 

다양한 표준 Load Balancer 구성에서는 다음 메트릭을 제공합니다.

| 메트릭 | 리소스 유형 | Description | 권장 집계 |
| --- | --- | --- | --- |
| 데이터 경로 가용성 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 지역 내에서 부하 분산 장치 프런트 엔드로, 마지막으로 VM을 지원하는 SDN 스택으로 데이터 경로를 연속적으로 실행합니다. 정상 인스턴스가 남아 있는 한 측정은 애플리케이션 부하가 분산된 트래픽과 동일한 경로를 따릅니다. 고객이 사용하는 데이터 경로의 유효성도 검사합니다. 측정은 애플리케이션에 표시되지 않으며 다른 작업을 방해하지 않습니다.| 평균 |
| 상태 프로브 상태 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 구성 설정에 따라 애플리케이션 엔드포인트의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다. 이 메트릭은 부하 분산 장치 풀에서 각 인스턴스 엔드포인트의 집계 또는 엔드포인트당 필터링된 보기를 제공합니다. 상태 프로브 구성에 표시된 대로 Load Balancer에서 애플리케이션의 상태를 보는 방법을 확인할 수 있습니다. |  평균 |
| SYN(동기화) 수 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 TCP(Transmission Control Protocol) 연결을 종료하거나 TCP 또는 UDP 패킷 흐름을 조작하지 않습니다. 흐름 및 해당 핸드셰이크는 항상 원본과 VM 인스턴스 사이에 있습니다. TCP 프로토콜 시나리오의 문제를 잘 해결하기 위해 SYN 패킷 카운터를 사용하여 TCP 연결 시도 횟수를 파악할 수 있습니다. 메트릭은 수신된 TCP SYN 패킷 수를 보고합니다.| 합계 |
| SNAT 연결 수 | 공용 부하 분산 장치 |표준 Load Balancer는 공용 IP 주소 프런트 엔드로 위장되는 아웃바운드 흐름 수를 보고합니다. SNAT(원본 네트워크 주소 변환) 포트는 소모성 리소스입니다. 이 메트릭은 애플리케이션이 아웃바운드에서 시작된 흐름에 대해 SNAT에 얼마나 의존하는지를 나타낼 수 있습니다. 성공 및 실패한 아웃바운드 SNAT 흐름에 대한 카운터가 보고되고 아웃바운드 흐름의 상태를 이해하고 문제를 해결하는 데 사용할 수 있습니다.| 합계 |
| 할당된 SNAT 포트 | 공용 부하 분산 장치 | 표준 Load Balancer는 백 엔드 인스턴스당 할당된 SNAT 포트 수를 보고합니다. | 평균. |
| 사용된 SNAT 포트 | 공용 부하 분산 장치 | 표준 Load Balancer는 백 엔드 인스턴스당 활용되는 SNAT 포트 수를 보고합니다. | 평균 | 
| 바이트 수 |  공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 데이터를 보고합니다. 바이트가 백 엔드 인스턴스에서 균등하게 배포되지 않을 수 있습니다. 이는 Azure의 Load Balancer 알고리즘이 흐름을 기반으로 하기 때문입니다. | 합계 |
| 패킷 수 |  공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 패킷을 보고합니다.| 합계 |

  >[!NOTE]
  >NVA 또는 방화벽 Syn 패킷을 통해 내부 부하 분산 장치에서 트래픽을 분산하는 경우 바이트 수 및 패킷 수 메트릭을 사용할 수 없으며 0으로 표시됩니다. 
  
  >[!NOTE]
  >SYN 수, 패킷 수, SNAT 연결 수 및 바이트 수 메트릭에는 최대 및 최소 집계를 사용할 수 없습니다. 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Azure Portal에서 부하 분산 장치 메트릭 보기

Azure Portal은 특정 리소스에 대한 부하 분산 장치 리소스 페이지 및 Azure Monitor 페이지에서 사용할 수 있는 메트릭 페이지를 통해 부하 분산 장치 메트릭을 표시합니다. 

표준 Load Balancer 리소스에 대한 메트릭을 보려면:
1. 메트릭 페이지로 이동하고 다음 중 하나를 수행합니다.
   * 부하 분산 장치 리소스 페이지의 드롭다운 목록에서 메트릭 유형을 선택합니다.
   * Azure Monitor 페이지에서 부하 분산 장치 리소스를 선택합니다.
2. 적절한 메트릭 집계 유형을 설정합니다.
3. 필요에 따라 필요한 필터링 및 그룹화를 구성합니다.
4. 필요에 따라 시간 범위와 집계를 구성합니다. 기본적으로 시간은 UTC로 표시됩니다.

  >[!NOTE] 
  >데이터가 분당 한 번 샘플링되므로 특정 메트릭을 해석할 때 시간 집계가 중요합니다. 시간 집계가 5분으로 설정되고 메트릭 집계 유형 Sum이 SNAT 할당과 같은 메트릭에 사용되는 경우 그래프에 할당된 총 SNAT 포트의 5배가 표시됩니다. 
  >
  >권장 사항: 메트릭 집계 유형 합계 및 개수를 분석할 때 1분보다 긴 시간 집계 값을 사용하는 것이 좋습니다.

![표준 Load Balancer에 대한 메트릭](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*그림: 표준 Load Balancer에 대한 데이터 경로 가용성 메트릭*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API를 통해 프로그래밍 방식으로 다차원 메트릭 검색

다차원 메트릭 정의 및 값을 검색하기 위한 API 지침은 [Azure Monitoring REST API 연습](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api)을 참조하세요. '모든 메트릭' 범주에 대한 [진단 설정](../azure-monitor/essentials/diagnostic-settings.md)을 추가하여 스토리지 계정에 이러한 메트릭을 쓸 수 있습니다. 

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>일반적인 진단 시나리오 및 권장 보기

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>이 데이터 경로가 작동하며 Load Balancer 프런트 엔드에 사용할 수 있나요?
<details><summary>Expand</summary>

데이터 경로 가용성 메트릭은 지역 내에서 VM이 있는 컴퓨팅 호스트로의 데이터 경로 상태를 설명합니다. 메트릭은 Azure 인프라의 상태를 반영한 것입니다. 이 메트릭으로 다음을 수행할 수 있습니다.
- 서비스의 외부 가용성 모니터링
- 서비스가 배포된 플랫폼이 정상 상태인지 여부 또는 게스트 OS 또는 애플리케이션 인스턴스가 정상 상태인지 여부를 심층 분석 및 이해
- 이벤트가 서비스와 관련된 것인지 또는 기본 데이터 평면과 관련된 것인지를 구분합니다. 이 메트릭을 상태 프로브 상태("백 엔드 인스턴스 가용성")와 혼동하지 마세요.

표준 Load Balancer 리소스에 대한 데이터 경로 가용성을 가져오려면 다음을 수행합니다.
1. 올바른 부하 분산 장치 리소스를 선택했는지 확인합니다. 
2. **메트릭** 드롭다운 목록에서 **데이터 경로 가용성** 을 선택합니다. 
3. **집계** 드롭다운 목록에서 **Avg** 를 선택합니다. 
4. 또한 필요한 프런트 엔드 IP 주소 또는 프런트 엔드 포트를 갖는 차원으로 프런트 엔드 IP 주소 또는 프런트 엔드 포트에 대한 필터를 추가한 다음, 선택한 차원에 따라 그룹화합니다.

![VIP 검색](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*그림: Load Balancer 프런트 엔드 검색 세부 정보*

이 메트릭은 활성, 대역 내 측정을 통해 생성됩니다. 지역 내의 검색 서비스는 측정에 대한 트래픽을 발생합니다. 서비스는 공용 프런트 엔드로 배포를 만드는 즉시 활성화되고, 프런트 엔드를 제거할 때까지 계속됩니다. 

배포의 프런트 엔드 및 규칙과 일치하는 패킷이 주기적으로 생성됩니다. 원본에서 백 엔드 풀의 VM이 있는 호스트까지 해당 지역을 트래버스합니다. 부하 분산 장치 인프라는 다른 모든 트래픽의 경우와 동일하게 부하 분산 및 변환 작업을 수행합니다. 이 프로브는 대역 내의 부하 분산된 엔드포인트에 있습니다. 프로브가 백 엔드 풀의 정상 VM이 있는 Compute 호스트에 도착하면, 컴퓨팅 호스트는 검색 서비스에 대한 응답을 생성합니다. VM에는 이 트래픽이 표시되지 않습니다.

다음과 같은 이유로 데이터 경로 가용성이 실패합니다.
- 배포의 백 엔드 풀에 정상 상태의 VM이 남아 있지 않습니다. 
- 인프라 중단이 발생했습니다.

진단 목적으로 [상태 프로브 상태와 함께 데이터 경로 가용성 메트릭](#vipavailabilityandhealthprobes)을 사용할 수 있습니다.

대부분의 시나리오에서 집계로 **Average** 를 사용합니다.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Load Balancer에 대한 백 엔드 인스턴스가 프로브에 응답하고 있나요?
<details>
  <summary>Expand</summary>
상태 프로브 상태 메트릭은 부하 분산 장치의 상태 프로브를 구성할 때 사용자가 구성한 애플리케이션 배포의 상태를 설명합니다. 부하 분산 장치는 상태 프로브의 상태를 사용하여 새 흐름을 보낼 위치를 결정합니다. 상태 프로브는 Azure 인프라 주소에서 시작되며, VM의 게스트 OS 내에서 볼 수 있습니다.

표준 Load Balancer 리소스에 대한 상태 프로브 상태를 가져오려면 다음을 수행합니다.
1. **Avg** 집계 유형을 갖는 **상태 프로브 상태** 메트릭을 선택합니다. 
2. 필요한 프런트 엔드 IP 주소 또는 포트(또는 둘 다)에 필터를 적용합니다.

다음과 같은 이유로 상태 프로브가 실패합니다.
- 수신 또는 응답하지 않거나 잘못된 프로토콜을 사용하는 포트로 상태 프로브를 구성하는 경우. 서비스가 DSR(Direct Server Return 또는 부동 IP) 규칙을 사용하는 경우 서비스가 프런트 엔드 IP 주소로 구성된 루프백이 아니라 NIC의 IP 구성에서 수신 대기하고 있는지 확인해야 합니다.
- 프로브가 네트워크 보안 그룹, VM의 게스트 OS 방화벽 또는 애플리케이션 계층 필터에서 허용되지 않는 경우.

대부분의 시나리오에서 집계로 **Average** 를 사용합니다.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>내 아웃바운드 연결 통계는 어떻게 확인할 수 있나요? 
<details>
  <summary>Expand</summary>
SNAT 연결 메트릭은 [아웃바운드 흐름](./load-balancer-outbound-connections.md)에 대한 성공 및 실패 연결의 볼륨을 설명합니다.

실패한 연결 볼륨이 0보다 크면 SNAT 포트가 고갈된 것을 나타냅니다. 추가로 조사하여 이러한 실패의 원인을 확인해야 합니다. SNAT 포트 고갈은 [아웃바운드 흐름](./load-balancer-outbound-connections.md) 설정 실패를 나타냅니다. 아웃바운드 연결에 대한 문서를 검토하여 시나리오 및 작동 메커니즘을 이해하고, SNAT 포트 고갈 방지를 위한 완화 및 설계 방법을 알아봅니다. 

SNAT 연결 통계를 가져오려면:
1. **SNAT 연결** 메트릭 유형을 선택하고 집계로 **Sum** 을 선택합니다. 
2. 성공한 SNAT 연결 수와 실패한 SNAT 연결 수가 다른 줄로 표시되도록 **연결 상태** 별로 그룹화합니다. 

![SNAT 연결](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*그림: Load Balancer SNAT 연결 수*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>SNAT 포트 사용량과 할당을 어떻게 확인하나요?
<details>
  <summary>Expand</summary>
사용된 SNAT 포트 메트릭은 아웃바운드 흐름을 유지하기 위해 사용되는 SNAT 포트 수를 추적합니다. 이는 부하 분산 장치 뒤에 있고 공용 IP 주소가 없는 백 엔드 VM 또는 가상 머신 확장 세트와 인터넷 원본 간에 설정된 고유 흐름 수를 나타냅니다. 사용 중인 SNAT 포트 수를 할당된 SNAT 포트 메트릭과 비교하여 서비스가 SNAT 소모를 겪고 있거나 그럴 위험이 있는지 그리고 서비스로 인해 아웃바운드 흐름이 실패하는지 확인할 수 있습니다. 

메트릭이 [아웃바운드 흐름](./load-balancer-outbound-connections.md) 실패 위험을 나타내는 경우 문서를 참조하고 서비스 상태 보장을 위한 완화 조치를 취하세요.

SNAT 포트 사용량과 할당을 보려면 다음을 수행합니다.
1. 원하는 데이터가 표시되도록 그래프의 시간 집계를 1분으로 설정합니다.
1. 메트릭 유형으로 **사용된 SNAT 포트** 및/또는 **할당된 SNAT 포트** 를 선택하고 집계로 **Average** 를 선택합니다.
    * 기본적으로 이러한 메트릭은 TCP 및 UDP를 통해 집계된 Load Balancer에 매핑된 모든 프런트 엔드 공용 IP에 해당하는 각 백 엔드 VM 또는 VMSS에 할당되거나 사용되는 평균 SNAT 포트 수입니다.
    * 부하 분산 장치에 사용되거나 할당되는 총 SNAT 포트 수를 보려면 메트릭 집계 **Sum** 을 사용합니다.
1. 특정 **프로토콜 유형**, **백 엔드 IP** 및/또는 **프런트 엔드 IP** 세트로 필터링합니다.
1. 백 엔드 또는 프런트 엔드 인스턴스당 상태를 모니터링하려면 분리를 적용합니다. 
    * 분리를 사용하면 한 번에 하나의 메트릭만 표시할 수 있습니다. 
1. 예를 들어 컴퓨터당 TCP 흐름에 대한 SNAT 사용량을 모니터링하려면 **Average** 로 집계하고 **백 엔드 IP** 로 분리하고 **프로토콜 유형** 으로 필터링합니다. 

![SNAT 할당 및 사용량](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*그림: 백 엔드 VM 세트의 평균 TCP SNAT 포트 할당 및 사용량*

![백 엔드 인스턴스별 SNAT 사용량](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*그림: 백 엔드 인스턴스당 TCP SNAT 포트 사용량*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>내 서비스에 대한 인바운드/아웃바운드 연결 시도를 확인하려면 어떻게 해야 하나요?
<details>
  <summary>Expand</summary>
SYN 패킷 메트릭은 특정 프런트 엔드와 관련해서 도착했거나 전송된 ([아웃바운드 흐름](../load-balancer-outbound-connections.md) 관련) TCP SYN 패킷 볼륨을 설명합니다. 이 메트릭을 사용하여 서비스에 대한 TCP 연결 시도를 이해할 수 있습니다.

대부분의 시나리오에서 집계로 **Sum** 을 사용합니다.

![SYN 연결](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*그림: Load Balancer SYN 수*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>내 네트워크 대역폭 소비를 어떻게 확인할 수 있나요? 
<details>
  <summary>Expand</summary>
바이트 및 패킷 카운터 메트릭은 프런트 엔드 기준으로 서비스에서 전송 또는 수신된 바이트 및 패킷 볼륨을 설명합니다.

대부분의 시나리오에서 집계로 **Sum** 을 사용합니다.

바이트 또는 패킷 수 통계를 가져오려면:
1. 집계가 **Sum** 인 **바이트 수** 및/또는 **패킷 수** 메트릭 유형을 선택합니다. 
2. 다음 중 하나를 수행합니다.
   * 특정 프런트 엔드 IP, 프런트 엔드 포트, 백 엔드 IP 또는 백 엔드 포트에 필터를 적용합니다.
   * 필터링 없이 부하 분산 장치 리소스에 대한 전체 통계를 가져옵니다.

![바이트 수](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*그림: Load Balancer 바이트 수*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>내 부하 분산 장치 배포를 진단하려면 어떻게 해야 하나요?
<details>
  <summary>Expand</summary>
단일 차트에 데이터 경로 가용성 및 상태 프로브 메트릭의 조합을 사용하여 문제를 확인하고 해결할 위치를 식별할 수 있습니다. Azure가 제대로 작동하고 있는지 확신하고, 이 정보를 토대로 구성 또는 애플리케이션이 근본 원인이라는 결론을 얻을 수 있습니다.

상태 프로브 메트릭을 사용하여 Azure에서 사용자가 제공한 구성을 기준으로 배포의 상태를 확인하는 방법을 이해할 수 있습니다. 상태 프로브를 확인하는 것은 항상 모니터링 또는 원인 파악에 도움이 되는 첫 단계입니다.

추가 단계를 수행하고 데이터 경로 가용성 메트릭을 사용하여 Azure가 특정 배포를 담당하는 기본 데이터 평면의 상태를 확인하는 방법을 이해할 수 있습니다. 두 메트릭에 함께 사용하면 이 예제에 설명된 대로 오류 위치를 격리시킬 수 있습니다.

![데이터 경로 가용성 및 상태 프로브 상태 메트릭 결합](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*그림: 데이터 경로 가용성 및 상태 프로브 상태 메트릭 결합*

차트에는 다음 정보가 표시됩니다.
- VM을 호스팅하는 인프라를 사용할 수 없었으며 차트 시작 부분에서 0%였습니다. 나중에 인프라가 정상이었고 VM에 연결할 수 있었고 여러 VM이 백 엔드에 배치되었습니다. 이 정보는 나중에 100%였던 데이터 경로 가용성에 대한 파랑 추적으로 표시됩니다. 
- 보라색 추적으로 표시되는 상태 프로브 상태는 차트 시작 부분에서 0%입니다. 녹색 원으로 표시된 영역은 상태 프로브 상태가 정상이 된 지점과 고객의 배포에서 새 흐름을 수락할 수 있었던 지점을 강조 표시합니다.

이 차트를 통해 고객은 다른 문제 발생 여부에 관계없이, 추측을 하거나 지원을 요청하지 않고 직접 배포 문제를 해결할 수 있습니다. 잘못된 구성 또는 실패한 애플리케이션 때문에 상태 프로브가 실패했으므로 서비스는 사용할 수 없었습니다.
</details>

## <a name="configure-alerts-for-multi-dimensional-metrics"></a>다차원 메트릭에 대한 경고 구성 ###

Azure 표준 Load Balancer는 다차원 메트릭에 대해 쉽게 구성할 수 있는 경고를 지원합니다. 다양한 심각도 수준으로 경고를 트리거하도록 특정 메트릭에 대한 사용자 지정 임계값을 구성하여 터치리스 리소스 모니터링 환경을 강화합니다.

경고를 구성하려면
1. 부하 분산 장치에 대한 경고 하위 블레이드로 이동
1. 새 경고 규칙 만들기
    1.  경고 조건 구성
    1.  (선택 사항) 자동 복구를 위한 작업 그룹 추가
    1.  직관적인 반응을 가능하게 하는 경고 심각도, 이름 및 설명 할당

### <a name="inbound-availability-alerting"></a>인바운드 가용성 경고
인바운드 가용성에 대해 경고하기 위해 데이터 경로 가용성 및 상태 프로브 상태 메트릭을 사용하여 2개의 개별 경고를 만들 수 있습니다. 고객은 특정 경고 논리가 필요한 여러 시나리오를 가질 수 있지만 아래 예는 대부분의 구성에 유용합니다.

데이터 경로 가용성을 사용하여 특정 부하 분산 규칙을 사용할 수 없게 될 때마다 경고를 발생시킬 수 있습니다. 데이터 경로 가용성에 대한 경고 조건을 설정하고 프런트 엔드 포트와 프런트 엔드 IP 주소에 대한 모든 현재 값과 미래 값으로 분리하여 이 경고를 구성할 수 있습니다. 경고 논리를 0보다 작거나 같게 설정하면 부하 분산 규칙이 응답하지 않을 때마다 이 경고가 발생합니다. 원하는 평가에 따라 집계 세분성과 평가 빈도를 설정합니다. 

상태 프로브 상태를 사용하면 지정된 백 엔드 인스턴스가 상당한 시간 동안 상태 프로브에 응답하지 못하는 경우 경고할 수 있습니다. 상태 프로브 상태 메트릭을 사용하고 백 엔드 IP 주소와 백 엔드 포트로 분할하도록 경고 조건을 설정합니다. 이렇게 하면 개별 백 엔드 인스턴스의 기능에 대해 개별적으로 경고하여 특정 포트에서 트래픽을 처리할 수 있습니다. **Average** 집계 유형을 사용하고 백 엔드 인스턴스가 검색되는 빈도와 정상 임계값으로 간주되는 항목에 따라 임계값을 설정합니다. 

또한 차원으로 분할하지 않고 **Average** 집계 유형을 사용하여 백 엔드 풀 수준에 대해 경고할 수 있습니다. 이렇게 하면 백 엔드 풀 멤버의 50%가 비정상 상태인 경우 경고와 같은 경고 규칙을 설정할 수 있습니다.

### <a name="outbound-availability-alerting"></a>아웃바운드 가용성 경고
아웃바운드 가용성에 대한 구성을 위해 SNAT 연결 수와 사용된 SNAT 포트 메트릭으로 2개의 개별 경고를 구성할 수 있습니다.

아웃바운드 연결 실패를 감지하려면 SNAT 연결 수를 사용하여 경고를 구성하고 연결 상태 = 실패로 필터링합니다. **Total** 집계를 사용합니다. 그런 다음, 모든 현재 및 미래 값으로 설정된 백 엔드 IP 주소로 이를 분할하여 연결이 실패한 각 백 엔드 인스턴스에 대해 별도로 경고할 수 있습니다. 아웃바운드 연결 실패를 확인하려는 경우 임계값을 0보다 크게 설정하거나 더 큰 값으로 설정합니다.

사용된 SNAT 포트를 통해 SNAT 고갈 및 아웃바운드 연결 실패의 높은 위험에 대해 경고할 수 있습니다. 이 경고를 사용할 때 백 엔드 IP 주소 및 프로토콜로 분할하고 **Average** 집계를 사용하는지 확인합니다. 안전하지 않은 것으로 간주되는 인스턴스당 할당한 포트 수의 백분율보다 크게 임계값을 설정합니다. 예를 들어 백 엔드 인스턴스가 할당된 포트의 75%를 사용할 때 낮은 심각도 경고를 구성하고, 할당된 포트의 90% 또는 100%를 사용할 때 높은 심각도 경고를 구성할 수 있습니다.  
## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Resource Health 상태

표준 Load Balancer **리소스에 대한 상태는****모니터 > 서비스 상태** 아래의 기존 를 통해 표시됩니다. 이는 프런트 엔드 부하 분산 엔드포인트를 사용할 수 있는지 결정하는 데이터 경로 가용성을 측정하여 **2분** 마다 평가됩니다.

| Resource Health 상태 | Description |
| --- | --- |
| 사용 가능 | 표준 부하 분산기 리소스는 정상 상태이며 사용 가능합니다. |
| 성능 저하됨 | 표준 부하 분산기에는 성능에 영향을 미치는 플랫폼 또는 사용자 시작 이벤트가 있습니다. 데이터 경로 가용성 메트릭은 최소 2분 동안 상태가 90% 미만이지만 25% 이상이라고 보고했습니다. 중간 정도에서 심각한 정도의 성능 영향을 경험하게 됩니다. [문제 해결 RHC 가이드에 따라](./troubleshoot-rhc.md) 가용성에 영향을 주는 사용자 시작 이벤트가 있는지 확인합니다.
| Unavailable | 표준 부하 분산 장치 리소스는 정상 상태가 아닙니다. 데이터 경로 가용성 메트릭이 최소 2분 동안 25% 미만 상태를 보고했습니다. 인바운드 연결에 대한 상당한 성능 영향 또는 가용성 부족을 경험하게 됩니다. 사용 불가를 유발하는 사용자 또는 플랫폼 이벤트가 있을 수 있습니다. [문제 해결 RHC 가이드에 따라](./troubleshoot-rhc.md) 가용성에 영향을 주는 사용자 시작 이벤트가 있는지 확인합니다. |
| 알 수 없음 | 표준 Load Balancer 리소스에 대한 Resource Health 상태가 아직 업데이트되지 않았거나 지난 10분 동안 데이터 경로 가용성 정보를 받지 못했습니다. 이 상태는 일시적이어야 하며 데이터가 수신되는 즉시 올바른 상태를 반영합니다. |

공용 표준 Load Balancer 리소스의 상태를 보려면:
1. **모니터** > **Service Health** 를 선택합니다.

   ![모니터 페이지](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *그림: Azure Monitor의 Service Health 링크*

2. **Resource Health** 를 선택한 다음, **구독 ID** 및 **리소스 유형 = Load Balancer** 를 선택했는지 확인합니다.

   ![Resource Health 상태](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *그림: 상태 보기에 대한 리소스 선택*

3. 목록에서 Load Balancer 리소스를 선택하여 기록 상태를 확인합니다.

    ![Load Balancer 상태](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *그림: Load Balancer 리소스 상태 보기*
 
일반 리소스 상태 설명은 [RHC 설명서](../service-health/resource-health-overview.md)에서 확인할 수 있습니다. Azure Load Balancer에 대한 특정 상태는 아래 표에 나와 있습니다. 


## <a name="next-steps"></a>다음 단계

- Load Balancer에 대해 미리 구성된 이러한 메트릭을 보려면 [인사이트](./load-balancer-insights.md) 사용에 대해 알아보세요.
- [Standard Load Balancer](./load-balancer-overview.md)에 대해 자세히 알아보세요.
- [Load Balancer 아웃바운드 연결](./load-balancer-outbound-connections.md)에 대해 자세히 알아봅니다.
- [Azure Monitor](../azure-monitor/overview.md)에 대해 자세히 알아봅니다.
- [Azure Monitor REST API](/rest/api/monitor/) 및 [REST API를 통해 메트릭을 검색하는 방법](/rest/api/monitor/metrics/list)에 대해 자세히 알아봅니다.
