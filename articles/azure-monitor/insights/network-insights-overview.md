---
title: Azure Monitor Network Insights
description: 구성없이 배포된 모든 네트워크 리소스에 대한 상태 및 메트릭에 대한 포괄적인 뷰를 제공하는 Azure Monitor Network Insights에 대한 개요입니다.
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: a4b433ed7a2eb0a2a9367f921e63091a823df37d
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567484"
---
# <a name="azure-monitor-network-insights"></a>Azure Monitor Network Insights

구성없이 배포된 모든 네트워크 리소스에 대한 [상태](../../service-health/resource-health-checks-resource-types.md) 및 [메트릭](../essentials/metrics-supported.md)에 대한 포괄적인 뷰를 제공하는 Azure Monitor Network Insights입니다. 또한 [연결 모니터](../../network-watcher/connection-monitor-overview.md), [NSG(네트워크 보안 그룹)에 대한 흐름 로깅](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [트래픽 분석](../../network-watcher/traffic-analytics.md)과 같은 네트워크 모니터링 기능에 액세스할 수 있습니다. 그리고 다른 네트워크 [진단](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) 기능을 제공합니다.

Azure Monitor Network Insights는 모니터링의 다음과 같은 주요 구성 요소를 중심으로 구성됩니다.
- [네트워크 상태 및 메트릭](#networkhealth)
- [연결](#connectivity)
- [트래픽](#traffic)
- [진단 도구 키트](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>네트워크 상태 및 메트릭

Azure Monitor Network Insights **개요** 페이지에서는 네트워킹 리소스의 인벤토리를 리소스 상태 및 경고와 함께 쉽게 시각화하는 방법을 제공합니다. 검색 및 필터링, 리소스 상태 및 메트릭, 경고, 종속성 보기의 4가지 주요 기능 영역으로 구분됩니다.

[![개요 페이지를 보여 주는 스크린샷](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>검색 및 필터링
**구독**, **리소스 그룹**, **유형** 과 같은 필터를 사용하여 리소스 상태 및 경고 보기를 사용자 지정할 수 있습니다.

검색 상자를 사용하여 리소스 및 관련 리소스를 검색할 수 있습니다. 예를 들어 공용 IP는 애플리케이션 게이트웨이와 연결됩니다. 공용 IP의 DNS 이름을 검색하면 공용 IP 및 연결된 애플리케이션 게이트웨이가 모두 반환됩니다.

[![Azure Monitor Network Insights 검색 결과를 보여 주는 스크린샷](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>리소스 상태 및 메트릭
다음 예제에서 각 타일은 리소스 종류를 나타냅니다. 타일은 선택한 모든 구독에서 배포된 해당 리소스 종류의 인스턴스 수를 표시합니다. 또한 리소스의 상태도 표시합니다. 이 예제에서는 105 ER 및 VPN 연결을 배포합니다. 103는 정상이며 2는 사용할 수 없습니다.

![Azure Monitor Network Insights의 리소스 상태 및 메트릭을 보여 주는 스크린샷](media/network-insights-overview/resource-health.png)

사용할 수 없는 ER 및 VPN 연결을 선택하면 메트릭 보기가 표시됩니다. 

![Azure Monitor Network Insights의 메트릭 보기를 보여 주는 스크린샷](media/network-insights-overview/metric-view.png)

그리드 보기에서 모든 항목을 선택할 수 있습니다. **상태** 열에서 아이콘을 선택하여 해당 연결에 대한 리소스 상태를 가져옵니다. **경고** 열에서 값을 선택하여 연결에 대한 경고 및 메트릭 페이지로 이동합니다. 

### <a name="alerts"></a>경고
페이지 오른쪽에 있는 **경고** 상자는 모든 구독에서 선택한 리소스에 대해 생성된 모든 경고 보기를 제공합니다. 경고 개수를 선택하여 세부 경고 페이지로 이동합니다.

### <a name="dependency-view"></a>종속성 보기
종속성 보기를 사용하면 리소스가 구성되는 방법을 시각화할 수 있습니다. 종속성 보기는 현재 Application Gateway, Azure Virtual WAN, Azure Load Balancer에 사용할 수 있습니다. 예를 들어 Application Gateway 경우 메트릭 그리드 보기에서 Application Gateway 리소스 이름을 선택하여 종속성 보기에 액세스할 수 있습니다. Virtual WAN 및 Load Balancer에 대해서도 동일한 작업을 수행할 수 있습니다.

![Azure Monitor Network Insights에서 Application Gateway 보기를 보여 주는 스크린샷](media/network-insights-overview/application-gateway.png)

Application Gateway의 종속성 보기를 사용하면 프런트 엔드 IP가 수신기, 규칙, 백 엔드 풀에 연결되는 방법에 대한 간단한 보기를 제공합니다. 연결선은 색으로 구분되며 백 엔드 풀 상태에 따라 추가 세부 정보를 제공합니다. 또한 이 보기에서는 가상 머신 확장 집합 및 VM 인스턴스와 같은 Application Gateway 메트릭과 모든 관련 백 엔드 풀의 메트릭에 대한 자세한 보기를 제공합니다.

[![Azure Monitor Network Insights의 종속성 보기를 보여 주는 스크린샷](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

종속성 그래프를 사용하면 구성 설정을 쉽게 탐색할 수 있습니다. 백 엔드 풀을 마우스 오른쪽 단추로 클릭하여 다른 정보에 액세스합니다. 예를 들어 백 엔드 풀이 VM인 경우 VM Insights 및 Azure Network Watcher 연결 문제 해결에 직접 액세스하여 연결 문제를 식별할 수 있습니다.

![Azure Monitor Network Insights의 종속성 보기 메뉴를 보여 주는 스크린샷](media/network-insights-overview/dependency-view-menu.png)

종속성 보기의 검색 및 필터 표시줄은 그래프를 통해 검색하는 쉽게 검색할 수 있는 방법을 제공합니다. 예를 들어 이전 예제에서 **AppGWTestRule** 을 검색하면 AppGWTestRule을 통해 연결된 모든 노드로 보기가 스케일 다운됩니다.

![Azure Monitor Network Insights의 검색에 대한 예제를 보여 주는 스크린샷](media/network-insights-overview/search-example.png)

다양한 필터를 통해 특정 경로 및 상태로 스케일 다운할 수 있습니다. 예를 들어 상태가 비정상인 모든 에지를 표시하려면 **상태** 목록에서 **비정상** 만 선택합니다.

**자세한 메트릭 보기** 를 선택하여 애플리케이션 게이트웨이, 모든 백 엔드 풀 리소스, 프런트 엔드 IP에 대해 자세한 메트릭을 제공하는 미리 구성된 통합 문서를 엽니다. 

## <a name="connectivity"></a><a name="connectivity"></a>연결

**연결** 탭에서는 선택한 구독 집합에 대해 [연결 모니터](../../network-watcher/connection-monitor-overview.md) 및 연결 모니터(클래식)를 통해 구성된 모든 테스트를 쉽게 시각화하는 방법을 제공합니다.

![Azure Monitor Network Insights의 연결 탭을 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

테스트는 **원본** 및 **대상** 타일로 그룹화되며 각 테스트에 대한 연결성 상태를 표시합니다. 연결 가능한 설정을 사용하면 실패한 검사(%) 및 RTT(ms)에 따라 연결성 기준에 대한 구성에 쉽게 액세스할 수 있습니다. 값을 설정한 후에는 각 테스트의 상태가 선택 기준에 따라 업데이트됩니다.

[![Azure Monitor Network Insights의 연결 테스트를 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

모든 원본 또는 대상 타일을 선택하여 메트릭 보기를 열 수 있습니다.

[![Azure Monitor Network Insights의 연결 메트릭을 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


그리드 보기에서 모든 항목을 선택할 수 있습니다. 연결 모니터 포털 페이지로 이동하여 식별된 문제에 영향을 미치는 홉 단위 토폴로지 및 연결을 보려면 **연결성** 열의 아이콘을 선택합니다. **경고** 열에서 값을 선택하여 경고로 이동합니다. **검사 실패 비율** 및 **왕복 시간(ms)** 열에서 그래프를 선택하여 선택한 연결 모니터에 대한 메트릭 페이지로 이동합니다.

페이지 오른쪽에 있는 **경고** 상자는 모든 구독에서 구성된 연결 테스트에 대해 생성된 모든 경고 보기를 제공합니다. 경고 개수를 선택하여 세부 경고 페이지로 이동합니다.

## <a name="traffic"></a><a name="traffic"></a>트래픽
**트래픽** 탭에서는 선택한 구독 집합에 대해 [NSG 흐름 로그](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) 및 [트래픽 분석](../../network-watcher/traffic-analytics.md)용으로 구성된 모든 NSG에 대한 액세스를 위치별로 그룹화하여 제공합니다. 이 탭에 제공되는 검색 기능을 사용하면 검색된 IP 주소에 대해 구성된 NSG를 식별할 수 있습니다. 사용자 환경에서 모든 IP 주소를 검색할 수 있습니다. 타일식 지역 보기는 NSG 흐름 로그 및 트래픽 분석 구성 상태와 함께 모든 NSG를 표시합니다.

[![Azure Monitor Network Insights의 트래픽 탭을 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

지역 타일을 선택하면 그리드 보기가 나타납니다. 그리드는 쉽게 읽고 구성할 수 있는 보기에서 NSG 흐름 로그 및 트래픽 분석을 제공합니다.  

[![Azure Monitor Network Insights의 트래픽 지역 보기를 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

그리드 보기에서 모든 항목을 선택할 수 있습니다. **흐름 로그 구성 상태** 열에서 아이콘을 선택하여 NSG 흐름 로그 및 트래픽 분석 구성을 편집합니다. **경고** 열에서 값을 선택하여 선택된 NSG에 대해 구성된 트래픽 경고로 이동합니다. 마찬가지로 **트래픽 분석 작업 영역** 을 선택하여 트래픽 분석 보기로 이동할 수 있습니다.  

페이지 오른쪽에 있는  **경고** 상자에서는 모든 구독에서 모든 트래픽 분석 작업 영역 기반 경고 보기를 제공합니다. 경고 개수를 선택하여 세부 경고 페이지로 이동합니다.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 진단 도구 키트
진단 도구 키트를 사용하면 네트워크 문제 해결에 사용할 수 있는 모든 진단 기능에 액세스할 수 있습니다. 이 드롭다운 목록을 사용하여 [패킷 캡처](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN 문제 해결](../../network-watcher/network-watcher-troubleshoot-overview.md), [연결 문제 해결](../../network-watcher/network-watcher-connectivity-overview.md), [다음 홉](../../network-watcher/network-watcher-next-hop-overview.md), [IP 흐름 확인](../../network-watcher/network-watcher-ip-flow-verify-overview.md)과 같은 기능에 액세스할 수 있습니다.

![진단 도구 키트를 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="availability-of-resources"></a>리소스 가용성 

기본적으로 모든 네트워킹 리소스를 네트워크 인사이트에서 볼 수 있습니다. 고객은 리소스 종류를 클릭하여 리소스 상태와 메트릭(사용 가능한 경우), 구독 세부 정보, 위치 등을 볼 수 있습니다. 네트워킹 리소스의 하위 집합이 _등록_ 되었습니다. 등록 리소스의 경우, 고객이 리소스별 토폴로지 보기 및 기본 제공 메트릭 통합 문서에 액세스할 수 있습니다. 이러한 기본 환경을 사용하면 더 쉽게 리소스 메트릭을 탐색하고 문제를 해결할 수 있습니다.  

등록된 리소스는 다음과 같습니다. 
* 가상 WAN
* Application Gateway
* Load Balancer
* ExpressRoute
* Private Link
* NAT Gateway
* 공용 IP
* NIC

## <a name="troubleshooting"></a>문제 해결 
일반적인 문제 해결 지침은 전용 통합 문서 기반 인사이트 [문제 해결 문서](troubleshoot-workbooks.md)를 참조하세요.
이 섹션은 Azure Monitor Network Insights를 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 진단하고 해결하는 데 도움이 됩니다. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>성능 문제나 오류는 어떻게 해결하나요?

Azure Monitor Network Insights에서 식별한 네트워킹 관련 문제 해결에 대한 자세한 내용은 오작동 리소스에 대한 문제 해결 설명서를 참조하세요. 

다음은 자주 사용하는 서비스에 대한 문제 해결 문서 링크입니다. 이러한 서비스에 대한 자세한 문제 해결 문서는 서비스 목차의 문제 해결 섹션에 있는 다른 문서를 참조하세요.
* [Azure Virtual Network](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>선택한 모든 구독에 대한 리소스를 볼 수 없는 이유는 무엇인가요?

Azure Monitor Network Insights는 한 번에 5개 구독에 대한 리소스만 표시할 수 있습니다. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-network-insights"></a>Azure Monitor Network Insights를 변경하거나 시각화를 추가하려면 어떻게 해야 하나요?

변경하려면 **편집 모드** 를 선택하여 통합 문서를 수정합니다. 그런 다음 지정된 구독 및 리소스 그룹에 연결된 새 통합 문서로 변경 내용을 저장할 수 있습니다.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>통합 문서의 일부를 고정한 다음 시간 조직은 어떻게 되나요?

Azure Monitor Network Insights는 **자동** 시간 조직을 사용하므로 시간 조직은 선택한 시간 범위를 기준으로 합니다.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>통합 문서의 일부분이 고정되었을 때 시간 범위는 어떻게 되나요?

시간 범위는 대시보드 설정에 따라 달라집니다.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-network-insights"></a>다른 데이터를 보거나 나만의 시각화를 만들려면 어떻게 해야 하나요? Azure Monitor Network Insights를 변경하려면 어떻게 하나요?

편집 모드를 사용하여 측면 패널 또는 자세한 메트릭 보기에 표시되는 통합 문서를 편집할 수 있습니다. 그런 다음 변경 내용을 새 통합 문서로 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 네트워크 모니터링에 대해 자세히 알아보기: [Azure Network Watcher란?](../../network-watcher/network-watcher-monitoring-overview.md)
- 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 지원하도록 디자인된 시나리오 통합 문서 알아보기: [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../visualize/workbooks-overview.md)
