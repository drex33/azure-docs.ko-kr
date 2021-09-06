---
title: Load Balancer 모니터링 데이터 참조
titleSuffix: Azure Load Balancer
description: Load Balancer를 모니터링할 때 필요한 중요 참조 자료
author: asudbring
ms.topic: reference
ms.author: allensu
ms.service: load-balancer
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: ac5a7394b13ac34ad9d398458627765316369de9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296506"
---
# <a name="monitoring-load-balancer-data-reference"></a>Load Balancer 모니터링 데이터 참조

Load Balancer에 대한 모니터링 데이터의 수집 및 분석 방법 관련 세부 정보는 [Load Balancer 모니터링](monitor-load-balancer.md)을 참조하세요.

## <a name="metrics"></a>메트릭

### <a name="load-balancer-metrics"></a>부하 분산 장치 메트릭 

| 메트릭 | 리소스 유형 | Description | 권장 집계 |
| ------ | ------------- | ----------- | ----------------------- |
| 데이터 경로 가용성 | 공용 및 내부 부하 분산 장치 |  표준 Load Balancer는 지역 내에서 부하 분산 장치 프런트 엔드로, 마지막으로 VM을 지원하는 SDN 스택으로 데이터 경로를 연속적으로 실행합니다. 정상 인스턴스가 남아 있는 한 측정은 애플리케이션 부하가 분산된 트래픽과 동일한 경로를 따릅니다. 고객이 사용하는 데이터 경로의 유효성도 검사합니다. 측정은 애플리케이션에 표시되지 않으며 다른 작업을 방해하지 않습니다. | 평균 |
| 상태 프로브 상태 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 구성 설정에 따라 애플리케이션 엔드포인트의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다. 이 메트릭은 부하 분산 장치 풀에서 각 인스턴스 엔드포인트의 집계 또는 엔드포인트당 필터링된 보기를 제공합니다. 상태 프로브 구성에 표시된 대로 Load Balancer에서 애플리케이션의 상태를 보는 방법을 확인할 수 있습니다. | 평균 |
| SYN(동기화) 수 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 구성 설정에 따라 애플리케이션 엔드포인트의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다. 이 메트릭은 부하 분산 장치 풀에서 각 인스턴스 엔드포인트의 집계 또는 엔드포인트당 필터링된 보기를 제공합니다. 상태 프로브 구성에 표시된 대로 Load Balancer에서 애플리케이션의 상태를 보는 방법을 확인할 수 있습니다. | 평균 |
| SNAT 연결 수 | 공용 부하 분산 장치 | 표준 Load Balancer는 공용 IP 주소 프런트 엔드로 위장되는 아웃바운드 흐름 수를 보고합니다. SNAT(원본 네트워크 주소 변환) 포트는 소모성 리소스입니다. 이 메트릭은 애플리케이션이 아웃바운드에서 시작된 흐름에 대해 SNAT에 얼마나 의존하는지를 나타낼 수 있습니다. 성공 및 실패한 아웃바운드 SNAT 흐름에 대한 카운터가 보고되고 아웃바운드 흐름의 상태를 이해하고 문제를 해결하는 데 사용할 수 있습니다. | 합계 |
| 할당된 SNAT 포트 | 공용 부하 분산 장치 | 표준 Load Balancer는 백 엔드 인스턴스당 할당된 SNAT 포트 수를 보고합니다. | 평균 |
| 사용된 SNAT 포트 | 공용 부하 분산 장치 | 표준 Load Balancer는 백 엔드 인스턴스당 활용되는 SNAT 포트 수를 보고합니다. | 평균 |
| 바이트 수 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 데이터를 보고합니다. 바이트가 백 엔드 인스턴스에서 균등하게 배포되지 않을 수 있습니다. 이는 Azure의 Load Balancer 알고리즘이 흐름을 기반으로 하기 때문입니다. | 합계 |
| 패킷 수 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 패킷을 보고합니다. | 합계 |

자세한 내용은 [부하 분산 장치에 대해 Azure Monitor에서 지원되는 모든 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkloadbalancers) 목록을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

Load Balancer에는 해당 메트릭과 관련된 다음 차원이 있습니다.

| 차원 이름 | Description |
| -------------- | ----------- |
| 프런트 엔드 IP | 관련 부하 분산 규칙의 프런트 엔드 IP 주소 |
| 프런트 엔드 포트 | 관련 부하 분산 규칙의 프런트 엔드 포트 | 
| 백 엔드 IP | 관련 부하 분산 규칙의 백 엔드 IP 주소 |
| 백 엔드 포트 | 관련 부하 분산 규칙의 백 엔드 포트 |
| 프로토콜 종류 | 적절한 부하 분산 규칙의 프로토콜(TCP 또는 UDP일 수 있음) |
| Direction | 트래픽 흐름의 방향입니다. 이는 인바운드 또는 아웃바운드일 수 있습니다. | 
| 연결 상태 | SNAT 연결 상태이며, 이는 보류 중, 성공 또는 실패일 수 있습니다. | 

## <a name="resource-logs"></a>리소스 로그

리소스 로그는 현재 Azure Load Balancer에서 지원되지 않습니다.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 로그 테이블
### <a name="diagnostics-tables"></a>진단 테이블

진단 테이블은 현재 Azure Load Balancer에서 지원되지 않습니다.
## <a name="activity-log"></a>활동 로그

다음 테이블에는 활동 로그에서 만들어질 수 있는 Load Balancer 관련 작업이 나열되어 있습니다.

| 작업(Operation) | Description |
| --- | --- |
| Microsoft.Network/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
| Microsoft.Network/loadBalancers/write | 부하 분산 장치를 만들거나 기존 부하 분산 장치를 업데이트합니다. |
| Microsoft.Network/loadBalancers/delete | 부하 분산 장치를 삭제합니다. |
| Microsoft.Network/loadBalancers/backendAddressPools/queryInboundNatRulePortMapping/action | 인바운드 Nat 규칙 포트 매핑을 쿼리합니다. |
| Microsoft.Network/loadBalancers/backendAddressPools/read | 부하 분산 장치 백 엔드 주소 풀 정의를 가져옵니다. |
| Microsoft.Network/loadBalancers/backendAddressPools/write | 부하 분산 장치 백엔드 주소 풀을 만들거나 기존 부하 분산 장치 백엔드 주소 풀을 업데이트합니다. |
| Microsoft.Network/loadBalancers/backendAddressPools/delete | 부하 분산 장치 백엔드 주소 풀을 삭제합니다. |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인합니다. 경고할 수 없습니다. |
| Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | 부하 분산 장치 백엔드 주소 풀의 백엔드 주소를 나열합니다. |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 부하 분산 장치 프런트 엔드 IP 구성 정의를 가져옵니다. |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 부하 분산 장치 프런트 엔드 IP 구성을 조인합니다. 경고할 수 없습니다. |
| Microsoft.Network/loadBalancers/inboundNatPools/read | 부하 분산 장치 인바운드 NAT 풀 정의를 가져옵니다. |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | 부하 분산 장치 인바운드 NAT 풀을 조인합니다. 경고할 수 없습니다. |
| Microsoft.Network/loadBalancers/inboundNatRules/read | 부하 분산 장치 인바운드 NAT 규칙 정의를 가져옵니다. |
| Microsoft.Network/loadBalancers/inboundNatRules/write | 부하 분산 장치 인바운드 NAT 규칙을 만들거나 기존 부하 분산 장치의 인바운드 NAT 규칙을 업데이트합니다. |
| Microsoft.Network/loadBalancers/inboundNatRules/delete | 부하 분산 장치 인바운드 NAT 규칙을 삭제합니다. |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙을 조인합니다. 경고할 수 없습니다. |
| Microsoft.Network/loadBalancers/loadBalancingRules/read | 부하 분산 장치 부하 분산 규칙 정의를 가져옵니다. |
| Microsoft.Network/loadBalancers/networkInterfaces/read | 부하 분산 장치의 모든 네트워크 인터페이스에 대한 참조를 가져옵니다. |
| Microsoft.Network/loadBalancers/outboundRules/read | 부하 분산 장치 아웃바운드 규칙 정의를 가져옵니다. |
| Microsoft.Network/loadBalancers/probes/read | 부하 분산 장치 프로브를 가져옵니다. |
| Microsoft.Network/loadBalancers/probes/join/action | 부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 가상 머신 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다. 경고할 수 없습니다. |
| Microsoft.Network/loadBalancers/virtualMachines/read | 부하 분산 장치의 모든 가상 머신에 대한 참조를 가져옵니다. |

활동 로그 항목의 스키마에 대한 자세한 내용은 [활동 로그 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요. 

## <a name="see-also"></a>참고 항목

- Azure Load Balancer 모니터링에 대한 설명은 [Azure Load Balancer 모니터링](./monitor-load-balancer.md)을 참조하세요.