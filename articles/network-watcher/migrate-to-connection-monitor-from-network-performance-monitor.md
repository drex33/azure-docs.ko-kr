---
title: 네트워크 성능 모니터에서 연결 모니터로 마이그레이션
titleSuffix: Azure Network Watcher
description: 네트워크 성능 모니터에서 연결 모니터로 마이그레이션하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 0ec16b16c8e71d764fb0fe21520eb407493ed8d7
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105366"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>네트워크 성능 모니터에서 연결 모니터로 마이그레이션

> [!IMPORTANT]
> 2021년 7월 1일부터는 기존 작업 영역에 새 테스트를 추가하거나 네트워크 성능 모니터에 새 작업 영역을 사용할 수 없습니다. 2021년 7월 1일 이전에 만든 테스트는 계속 사용할 수 있습니다. 현재 워크로드에 대한 서비스 중단의 영향을 최소화하려면 2024년 2월 29일 이전에 테스트를 네트워크 성능 모니터에서 Azure Network Watcher의 새로운 연결 모니터로 마이그레이션합니다.

가동 중지 시간 없이 한 번의 클릭으로 NPM(네트워크 성능 모니터)에서 새롭게 향상된 연결 모니터로 테스트를 마이그레이션할 수 있습니다. 이점에 대한 자세한 내용은 [연결 모니터](./connection-monitor-overview.md)를 참조하세요.


## <a name="key-points-to-note"></a>주의할 주요 사항

마이그레이션하면 다음과 같은 결과가 발생합니다.

* 온-프레미스 에이전트 및 방화벽 설정이 그대로 작동합니다. 변경할 필요가 없습니다. Azure 가상 머신에 설치된 Log Analytics 에이전트를 [Network Watcher 확장](../virtual-machines/extensions/network-watcher-windows.md)으로 바꾸어야 합니다.
* 기존 테스트는 연결 모니터 > 테스트 그룹 > 테스트 형식으로 매핑됩니다. **편집** 을 선택하여 새 연결 모니터의 속성을 보고 수정하고, 템플릿을 다운로드하여 변경 내용을 적용하고, Azure Resource Manager를 통해 템플릿을 제출할 수 있습니다.
* 에이전트는 Log Analytics 작업 영역 및 메트릭 모두에 데이터를 보냅니다.
* 데이터 모니터링:
   * **Log Analytics 데이터**: 마이그레이션 전에는 NetworkMonitoring 테이블에 NPM이 구성된 작업 영역에 데이터가 남아 있습니다. 마이그레이션 후에 데이터는 동일한 작업 영역에 있는 NetworkMonitoring 테이블, NWConnectionMonitorTestResult 테이블, NWConnectionMonitorPathResult 테이블로 이동합니다. NPM에서 테스트를 사용하지 않도록 설정하면 데이터는 NWConnectionMonitorTestResult 테이블 및 NWConnectionMonitorPathResult 테이블에만 저장됩니다.
   * **로그 기반 경고, 대시보드, 통합**: 새 NWConnectionMonitorTestResult 테이블 및 NWConnectionMonitorPathResult 테이블을 기반으로 쿼리를 수동으로 편집해야 합니다. 메트릭에 대한 경고를 다시 만들려면 [연결 모니터를 사용하여 네트워크 연결 모니터링](./connection-monitor-overview.md#metrics-in-azure-monitor)을 참조하세요.
* ExpressRoute 모니터링의 경우:
    * **엔드투엔드 손실 및 대기 시간**: 연결 모니터에서 이를 지원하고, 사용자가 모니터링할 회로 및 피어링을 구성할 필요가 없기 때문에 NPM보다 쉽습니다. 경로의 회로가 자동으로 검색되며, 데이터를 메트릭에 사용할 수 있습니다(NPM이 결과를 저장하는 곳인 LA보다 빠름). 토폴로지도 그대로 작동합니다.
    * **대역폭 측정**: 대역폭 관련 메트릭이 출시되면서 NPM의 로그 분석 기반 접근 방식은 ExpressRoute 고객의 대역폭 모니터링에 적합하지 않습니다. 이제 이 기능은 연결 모니터에서 사용할 수 없습니다.
    
## <a name="prerequisites"></a>필수 구성 요소

* 구독 및 Log Analytics 작업 영역의 지역에서 Network Watcher를 사용하도록 설정했는지 확인합니다. 그렇지 않으면 “마이그레이션을 시도하기 전에 선택 구독 및 선택된 LA 작업 영역 위치에서 Network Watcher 확장을 사용하도록 설정하세요"라는 오류가 표시됩니다.
* Log Analytics 작업 영역의 지역/구독이 아닌 다른 지역/구독에 속한 Azure VM이 엔드포인트로 사용되는 경우 해당 구독 및 지역에서 Network Watcher를 사용하도록 설정해야 합니다.   
* Log Analytics 에이전트가 설치된 Azure 가상 머신은 Network Watcher 확장과 함께 사용하도록 설정해야 합니다.

## <a name="migrate-the-tests"></a>테스트 마이그레이션

네트워크 성능 모니터에서 연결 모니터로 테스트를 마이그레이션하려면 다음을 수행합니다.

1. Network Watcher에서 **연결 모니터** 를 선택하고 **NPM에서 테스트 가져오기** 탭을 선택합니다. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="네트워크 성능 모니터에서 연결 모니터로 테스트 마이그레이션" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. 드롭다운 목록에서 구독 및 작업 영역을 선택한 다음 마이그레이션할 NPM 기능을 선택합니다. 
1. **가져오기** 를 선택하여 테스트를 마이그레이션합니다.
* 작업 영역에서 NPM를 사용하도록 설정하지 않은 경우 "올바른 NPM 구성을 찾을 수 없습니다."라는 오류가 표시됩니다. 
* 2 단계에서 선택한 기능에 테스트가 없으면 "선택한 작업 영역에 <feature> 구성이 없습니다.”라는 오류가 표시됩니다.
* 유효한 테스트가 없으면 "선택한 작업 영역에 유효한 테스트가 없습니다."라는 오류가 표시됩니다.
* 테스트에 더 이상 활성 상태가 아니나 과거에 활성 상태였던 에이전트가 포함될 수 있습니다. "일부 테스트에 더 이상 활성 상태가 아닌 에이전트가 포함되어 있습니다. 비활성 에이전트 목록- {0}. 이러한 에이전트는 이전에 실행되었을 수 있지만 종료되었거나 더 이상 실행되지 않습니다. 에이전트를 사용하도록 설정하고 연결 모니터로 마이그레이션합니다. 활성 상태가 아닌 에이전트 없이 테스트를 마이그레이션하려면 계속을 클릭하십시오.”라는 오류 메시지가 표시될 수 있습니다.

마이그레이션이 시작된 후에는 다음 변경 내용이 적용됩니다. 
* 새 연결 모니터 리소스가 생성됩니다.
   * 지역당 및 구독당 하나의 연결 모니터가 생성됩니다. 온-프레미스 에이전트를 사용하는 테스트의 경우에는 새 연결 모니터 이름이 `<workspaceName>_"workspace_region_name"`으로 지정됩니다. Azure 에이전트를 사용하는 테스트의 경우 새 연결 모니터 이름이 `<workspaceName>_<Azure_region_name>`으로 지정됩니다.
   * 이제 NPM을 사용하는 동일한 Log Analytics 작업 영역의 NWConnectionMonitorTestResult 테이블 및 NWConnectionMonitorPathResult 테이블이라는 새로운 테이블에 모니터링 데이터가 저장됩니다. 
   * 테스트 이름은 테스트 그룹 이름으로 전달됩니다. 테스트 설명은 마이그레이션되지 않습니다.
   * 원본 및 대상 엔드포인트는 새 테스트 그룹에서 만들어지고 사용됩니다. 온-프레미스 에이전트의 경우 엔드포인트의 형식은 `<workspaceName>_<FQDN of on-premises machine>`으로 지정됩니다. 에이전트 설명은 마이그레이션되지 않습니다.
   * 대상 포트 및 프로브 간격이 `TC_<protocol>_<port>` 및 `TC_<protocol>_<port>_AppThresholds`라는 테스트 구성으로 이동합니다. 해당 프로토콜은 포트 값을 기반으로 설정됩니다. ICMP의 경우 테스트 구성의 이름은 `TC_<protocol>` 및 `TC_<protocol>_AppThresholds`로 지정됩니다. 설정이 마이그레이션되면 성공 임계값 및 기타 선택적 속성이 적용되며, 그러지 않으면 공란으로 남습니다.
   * 마이그레이션 테스트에 실행 중이 아닌 에이전트가 포함되어 있는 경우 에이전트를 사용하도록 설정하고 다시 마이그레이션해야 합니다.
* NPM이 사용 중지 설정되어 있지 않으므로 마이그레이션된 테스트는 NetworkMonitoring 테이블, NWConnectionMonitorTestResult 테이블, NWConnectionMonitorPathResult 테이블로 데이터를 계속해서 보낼 수 있습니다. 이 방법을 사용하면 기존 로그 기반 경고 및 통합이 영향을 받지 않습니다.
* 새로 만든 연결 모니터는 연결 모니터에서 볼 수 있습니다.

마이그레이션 후 다음을 수행해야 합니다.
* 수동으로 NPM에서 테스트를 사용하지 않도록 설정합니다. 그렇지 않으면 요금이 계속 청구됩니다. 
* NPM을 사용하지 않도록 설정하는 한편 NWConnectionMonitorTestResult 및 NWConnectionMonitorPathResult 테이블 또는 메트릭을 사용하여 경고를 다시 만듭니다. 
* 모든 외부 통합을 NWConnectionMonitorTestResult 및 NWConnectionMonitorPathResult 테이블로 마이그레이션합니다. 외부 통합의 예로는 Power BI 및 Grafana의 대시보드와 SIEM(보안 정보 및 이벤트 관리) 시스템과의 통합이 있습니다.

## <a name="common-errors-encountered"></a>일반적인 오류 발생

다음은 마이그레이션 중에 발생하는 몇 가지 일반적인 오류입니다. 

| Error  |    이유   |
|---|---|
| 유효한 NPM 구성을 찾을 수 없습니다. NPM UI로 이동하여 구성을 확인합니다     |     사용자가 NPM에서 테스트 가져오기를 선택하여 테스트를 마이그레이션하지만 작업 영역에서 NPM을 사용하도록 설정하지 않은 경우에 이 오류가 발생합니다   |
|선택한 작업 영역에 '서비스 연결 모니터' 구성이 없습니다    |       사용자가 NPM의 서비스 연결 모니터 연결 모니터 테스트를 마이그레이션하지만 서비스 연결 모니터에 구성된 테스트가 없는 경우에 이 오류가 발생합니다 |
|선택한 작업 영역에 'ExpressRoute 모니터' 구성이 없습니다    |     사용자가 NPM의 ExpressRoute 모니터에서 연결 모니터 테스트를 마이그레이션하지만 ExpressRoute 모니터에 구성된 테스트가 없는 경우에 이 오류가 발생합니다  |
|선택한 작업 영역에 '성능 모니터' 구성이 없습니다    |      사용자가 NPM의 성능 모니터에서 연결 모니터 테스트를 마이그레이션하지만 성능 모니터에 구성된 테스트가 없는 경우에 이 오류가 발생합니다 |
|선택한 작업 영역에 유효한 '{0}' 테스트가 없습니다    |      사용자가 NPM에서 연결 모니터 테스트를 마이그레이션하지만 마이그레이션할 사용자가 선택한 기능에 유효한 테스트가 없는 경우에 이 오류가 발생합니다  |
|마이그레이션을 시도하기 전에 구독 선택 및 선택한 LA 작업 영역의 위치에서 Network Watcher 확장을 사용하도록 설정하세요      |      사용자가 NPM에서 연결 모니터 테스트를 마이그레이션하고 선택한 LA 작업 영역에서 Network Watcher 확장을 사용하도록 설정하지 않은 경우에 이 오류가 발생합니다. 테스트를 마이그레이션하기 전에 사용자가 NW 확장을 사용하도록 설정해야 합니다 |
|일부 {1} 테스트에 더 이상 활성 상태가 아닌 에이전트가 포함되어 있습니다. 비활성 에이전트 목록- {0}. 이러한 에이전트는 이전에 실행되었을 수 있지만 종료되었거나 더 이상 실행되지 않습니다. 에이전트를 사용하도록 설정하고 연결 모니터로 마이그레이션합니다. 활성 상태가 아닌 에이전트 없이 테스트를 마이그레이션하려면 계속을 클릭하십시오       |    이 오류는 사용자가 NPM에서 연결 모니터 테스트를 마이그레이션하고 선택한 일부 테스트에 비활성 Network Watcher 에이전트 또는 더 이상 활성화되지 않았지만 과거에 활성화되어 종료된 NW 에이전트가 포함된 경우에 발생합니다. 사용자는 이러한 테스트를 선택 취소하고 이러한 비활성 에이전트를 포함하지 않는 테스트를 선택하여 마이그레이션할 수 있습니다  |
|{1} 테스트에는 더 이상 활성화되지 않는 에이전트가 포함됩니다. 비활성 에이전트 목록 - {0}. 이러한 에이전트는 이전에 실행되었을 수 있지만 종료되었거나 더 이상 실행되지 않습니다. 에이전트를 사용하도록 설정하고 연결 모니터로 마이그레이션합니다     | 이 오류는 사용자가 NPM에서 연결 모니터 테스트를 마이그레이션하고 선택한 일부 테스트에 비활성 Network Watcher 에이전트 또는 더 이상 활성화되지 않았지만 과거에 활성화되어 종료된 NW 에이전트가 포함된 경우에 발생합니다. 사용자는 에이전트를 사용하도록 설정한 다음, 이러한 테스트를 연결 모니터로 마이그레이션해야 합니다    |
|테스트를 연결 모니터로 가져오는 동안 오류가 발생했습니다     |    이 오류는 사용자가 NPM에서 CM으로 테스트를 마이그레이션하려고 하지만 오류로 인해 마이그레이션에 성공하지 못한 경우에 발생합니다 |



## <a name="next-steps"></a>다음 단계

연결 모니터에 대한 자세한 내용은 다음을 참조하세요.
* [연결 모니터(클래식)에서 연결 모니터로 마이그레이션](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Azure Portal을 사용하여 연결 모니터 만들기](./connection-monitor-create-using-portal.md)
