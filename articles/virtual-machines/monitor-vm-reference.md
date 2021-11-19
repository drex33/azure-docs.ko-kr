---
title: Azure Virtual Machines 데이터 참조 모니터링
description: Azure 가상 머신을 모니터링할 때 필요한 중요한 참조 자료입니다.
ms.service: virtual-machines
ms.custom: subject-monitoring
ms.date: 11/17/2021
ms.topic: reference
ms.openlocfilehash: c2222121f611d1c9183070ded7e042ee3ceef26c
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132817884"
---
# <a name="monitoring-azure-virtual-machines-data-reference"></a>Azure Virtual Machines 데이터 참조 모니터링

가상 [머신에](monitor-vm.md) 대한 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 Azure Virtual Machines 모니터링을 참조하세요.

## <a name="metrics"></a>메트릭

이 섹션에서는 Azure Virtual Machines 및 가상 머신 확장 집합에 대해 수집된 모든 자동으로 수집된 플랫폼 메트릭을 나열합니다.  

|메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| 가상 머신 | [Microsoft.Compute/virtualMachines](/azure/azure-monitor/essentials/metrics-supported#microsoftcomputevirtualmachines) |
| 가상 머신 크기 집합 | [Microsoft.Compute/virtualMachineScaleSets](/azure/azure-monitor/essentials/metrics-supported#microsoftcomputevirtualmachinescalesets)|
| 가상 머신 확장 집합 가상 머신 | [Microsoft.Compute/virtualMachineScaleSets/virtualMachines](/azure/azure-monitor/essentials/metrics-supported#microsoftcomputevirtualmachinescalesetsvirtualmachines)|

보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](/azure/azure-monitor/platform/metrics-supported)을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)을 참조하세요.


Azure Virtual Machines 및 가상 머신 확장 집합에는 메트릭과 연결된 다음과 같은 차원이 있습니다.

| 차원 이름 | Description |
| ------------------- | ----------------- |
| LUN | 논리 단위 번호 |
| VMName | 가상 머신 확장 집합과 함께 사용됩니다. |

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

이 섹션에서는 가상 머신 및 가상 머신 확장 집합과 관련되고 Log Analytics에서 쿼리에 사용할 수 있는 모든 Azure Monitor Logs 테이블을 참조합니다. 

|리소스 종류 | 참고 |
|-------|-----|
| [가상 머신](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-machines) | |
| [가상 머신 확장 집합](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-machine-scale-sets) | |

모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

## <a name="activity-log"></a>활동 로그

다음 표에는 활동 로그에서 만들 수 있는 가상 머신과 관련된 몇 가지 예제 작업이 나와 있습니다. 가능한 로그 전체의 전체 목록은 [Microsoft.Compute 리소스 공급자 옵션](/azure/role-based-access-control/resource-provider-operations#compute)을 참조하세요.

| 작업(Operation) | Description |
|:---|:---|
| Microsoft.Compute/virtualMachines/start/action | 가상 머신을 시작합니다. |
| Microsoft.Compute/virtualMachines/restart/action | 관리형 클러스터 삭제 |
| Microsoft.Compute/virtualMachines/write | 새 가상 머신을 만들거나 기존 가상 머신을 업데이트합니다. |
| Microsoft.Compute/virtualMachines/deallocate/action | 가상 머신을 끄고 Compute 리소스를 해제합니다. |
| Microsoft.Compute/virtualMachines/extensions/write | 새 가상 머신 확장을 만들거나 기존 가상 머신 확장을 업데이트합니다. |
| Microsoft.Compute/virtualMachines/start/action | 가상 머신을 시작합니다. |
| Microsoft.Compute/virtualMachines/restart/action | 관리형 클러스터 삭제 |
| Microsoft.Compute/virtualMachineScaleSets/Write | 가상 머신 확장 집합의 인스턴스를 시작합니다. |
| Microsoft.Compute/virtualMachines/deallocate/action | 가상 머신을 끄고 Compute 리소스를 해제합니다. |
| Microsoft.Compute/virtualMachines/extensions/write | 새 가상 머신 확장을 만들거나 기존 가상 머신 확장을 업데이트합니다. |

활동 로그 항목의 스키마에 대한 자세한 정보는 [활동 로그 스키마](/azure/azure-monitor/essentials/activity-log-schema)를 참조하세요. 


## <a name="see-also"></a>참고 항목

- Azure 가상 머신 [모니터링에](../virtual-machines/monitor-vm.md) 대한 설명은 Azure Virtual Machines 모니터링을 참조하세요.
