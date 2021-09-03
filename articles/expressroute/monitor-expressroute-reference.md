---
title: ExpressRoute 데이터 참조 모니터링
description: ExpressRoute를 모니터링할 때 필요한 중요 참조 자료
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: cd5a4ef0196843069d50f333089f8bffca956acc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289801"
---
# <a name="monitoring-expressroute-data-reference"></a>ExpressRoute 데이터 참조 모니터링

이 문서에서는 ExpressRoute의 성능 및 가용성을 분석하기 위해 수집된 로그 및 메트릭 데이터에 대한 참조를 제공합니다.
ExpressRoute의 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 [ExpressRoute 모니터링](monitor-expressroute.md)을 참조하세요.

## <a name="metrics"></a>메트릭

이 섹션은 ExpressRoute에 대한 모든 자동 수집 플랫폼 메트릭을 나열합니다. 보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

| 메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| ExpressRoute 회로 | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
| ExpressRoute 회로 피어링 | [Microsoft.Network/expressRouteCircuits/peerings](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuitspeerings) |
| ExpressRoute 게이트웨이 | [Microsoft.Network/expressRouteGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutegateways) |
| ExpressRoute Direct | [Microsoft.Network/expressRoutePorts](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |

>[!NOTE]
> *GlobalGlobalReachBitsInPerSecond* 및 *GlobalGlobalReachBitsOutPerSecond* 사용은 하나 이상의 Global Reach 연결이 설정된 경우에만 표시됩니다.
>

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

ExpressRoute에는 해당 메트릭과 관련된 다음 차원이 있습니다.

### <a name="dimension-for-expressroute-circuit"></a>ExpressRoute 회로의 차원

| 차원 이름 | Description |
| ------------------- | ----------------- |
| **PeeringType** | 구성된 피어링 유형입니다. 지원되는 값은 Microsoft 및 Private 피어링입니다. |
| **피어링** | 지원되는 값은 Primary 및 Secondary입니다. |
| **PeeredCircuitSkey** | Global Reach를 사용하여 연결된 원격 ExpressRoute 회로 서비스 키입니다. |

### <a name="dimension-for-expressroute-gateway"></a>ExpressRoute 게이트웨이의 차원

| 차원 이름 | Description |
| ------------------- | ----------------- |
| **roleInstance** | 게이트웨이 인스턴스입니다. 각 ExpressRoute 게이트웨이는 여러 인스턴스로 구성되며 지원되는 값은 GatewayTenantWork_IN_X입니다(여기서 X는 최소 0, 최대 게이트웨이 인스턴스 수 -1). |

### <a name="dimension-for-express-direct"></a>Express Direct의 차원

| 차원 이름 | Description |
| ------------------- | ----------------- |
| **링크** | 물리적 링크입니다. 각 ExpressRoute Direct 포트 쌍은 중복성을 위한 두 개의 물리적 링크로 구성되며 지원되는 값은 link1 및 link2입니다. |

## <a name="resource-logs"></a>리소스 로그

이 섹션에는 ExpressRoute에 대해 수집할 수 있는 리소스 로그 유형이 나열되어 있습니다. 

|리소스 로그 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| ExpressRoute 회로 | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkexpressroutecircuits) |

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](../azure-monitor/essentials/resource-logs-schema.md) 목록을 참조하세요.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

Azure ExpressRoute는 Azure Monitor Logs의 Kusto 테이블을 사용합니다. Log Analytics를 사용하여 해당 테이블을 쿼리할 수 있습니다. 모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

## <a name="activity-log"></a>활동 로그

다음 테이블에는 활동 로그에서 만들어질 수 있는 ExpressRoute 관련 작업이 나열되어 있습니다.

| 작업(Operation) | Description |
|:---|:---|
| 모든 관리 작업 | ExpressRoute 회로의 만들기, 업데이트 및 삭제를 포함한 모든 관리 작업입니다. |
| ExpressRoute 회로 만들기 또는 업데이트 | ExpressRoute 회로가 만들어지거나 업데이트되었습니다. |
| ExpressRoute 회로 삭제 | ExpressRoute 회로가 삭제되었습니다.|

활동 로그 항목의 스키마에 대한 자세한 내용은 [활동 로그 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요.

## <a name="schemas"></a>스키마

최상위 수준 진단 로그 스키마에 대한 자세한 설명은 [Azure 진단 로그에 지원되는 서비스, 스키마 및 범주](../azure-monitor/essentials/resource-logs-schema.md)를 참조하세요.

Log Analytics를 통해 메트릭을 검토할 때 출력에는 다음 열이 포함됩니다.

|**열**|**형식**|**설명**|
| --- | --- | --- |
|TimeGrain|문자열|PT1M(메트릭 값은 1분마다 푸시됨)|
|개수|real|일반적으로 2와 같음(각 MSEE는 1분마다 단일 메트릭 값을 푸시함)|
|최소|real|두 MSEE에 의해 푸시되는 두 메트릭 값의 최소값|
|최대|real|두 MSEE에 의해 푸시되는 두 메트릭 값의 최대값|
|평균|real|(최소 + 최대)/2와 같음|
|합계|real|두 MSEE의 두 메트릭 값 합계(쿼리된 메트릭에 대해 초점을 맞출 주 값)|

## <a name="see-also"></a>참고 항목

- Azure ExpressRoute 모니터링에 대한 설명은 [Azure ExpressRoute 모니터링](monitor-expressroute.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.