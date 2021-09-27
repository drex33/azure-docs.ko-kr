---
title: AKS 데이터 모니터링 참조
description: AKS를 모니터링할 때 필요한 중요 참고 자료
ms.service: container-service
ms.custom: subject-monitoring
ms.date: 07/29/2021
ms.topic: reference
ms.openlocfilehash: 9e8a12f8d3075bade1619ce766c74dc8cac23481
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816061"
---
# <a name="monitoring-aks-data-reference"></a>AKS 데이터 모니터링 참조

AKS에 대한 모니터링 데이터의 수집 및 분석에 대한 세부 정보는 [AKS 모니터링](monitor-aks.md)을 참조하세요.

## <a name="metrics"></a>메트릭

다음 표에는 AKS에 대해 수집된 플랫폼 메트릭이 나열되어 있습니다.  각 특정 형식에 대한 메트릭의 상세 목록은 각각의 링크에서 확인합니다.

|메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| 관리형 클러스터 | [Microsoft.ContainerService/managedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters)
| 연결된 클러스터 | [microsoft.kubernetes/connectedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftkubernetesconnectedclusters)
| 가상 머신| [Microsoft.Compute/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
| 가상 머신 크기 집합 | [Microsoft.Compute/virtualMachineScaleSets](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
| 가상 머신 확장 집합 가상 머신 | [Microsoft.Compute/virtualMachineScaleSets/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)|

보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

## <a name="metric-dimensions"></a>메트릭 차원

다음 표는 AKS 메트릭의 [차원](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 나열합니다. 

<!-- listed here /azure/azure-monitor/essentials/metrics-supported#microsoftcontainerservicemanagedclusters-->

| 차원 이름 | Description |
| ------------------- | ----------------- |
| requestKind | *Inflight 요청* 등과 같은 메트릭에서 요청 형식에 따라 나누기 위해 사용합니다. |
| condition(조건) | *다양한 노드 조건의 상태*, *준비 상태인 Pod 수* 등과 같은 메트릭에서 조건 형식에 따라 나누기 위해 사용합니다. |
| 상태 | *다양한 노드 조건의 상태* 등과 같은 메트릭에서 조건 상태에 따라 나누기 위해 사용합니다. |
| status2 | *다양한 노드 조건의 상태* 등과 같은 메트릭에서 조건 상태에 따라 나누기 위해 사용합니다.  |
| node | *CPU 사용량 밀리코어* 등과 같은 메트릭에서 노드 이름에 따라 나누기 위해 사용합니다. |
| phase | *단계별 Pod 수* 와 같은 메트릭에서 Pod 단계에 따라 나누기 위해 사용합니다. |
| namespace | *단계별 Pod 수* 와 같은 메트릭에서 Pod 네임스페이스에 따라 나누기 위해 사용합니다. |
| Pod | *단계별 Pod 수* 와 같은 메트릭에서 Pod 이름에 따라 나누기 위해 사용합니다. |
| 노드 풀 | *디스크 사용 바이트* 등과 같은 메트릭에서 노드 풀 이름에 따라 나누기 위해 사용합니다. |
| 디바이스 | *디스크 사용 바이트* 등과 같은 메트릭에서 디바이스 이름에 따라 나누기 위해 사용합니다. |

## <a name="resource-logs"></a>리소스 로그

다음 표에서는 AKS에 대해 수집할 수 있는 리소스 로그 범주를 나열합니다. AKS 컨트롤 플레인 구성 요소에 대한 로그입니다. 이러한 로그를 수집하기 위한 진단 설정 만들기에 대한 정보와, 사용 가능하도록 설정할 항목에 대한 권장 사항은 [모니터링 구성](monitor-aks.md#configure-monitoring)을 참조하세요. [컨테이너 인사이트에서 로그를 쿼리하는 방법](../azure-monitor/containers/container-insights-log-query.md#resource-logs)을 참조하세요.

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](../azure-monitor/essentials/resource-logs-schema.md) 목록을 참조하세요. 

| 범주                | Description |
|:---|:---|
| cluster-autoscaler       | AKS 클러스터가 스케일 업 또는 다운되는 이유를 이해합니다. 예상과 다를 수 있습니다. 이 정보는 클러스터에서 흥미로운 일이 발생했을 수 있는 시간 간격을 상관시킬 때도 유용합니다. |
| 가드                   | 관리형 Azure Active Directory 및 Azure RBAC 감사 관리형 Azure AD의 경우 토큰 입력 및 사용자 정보 출력이 포함되며 Azure RBAC의 경우 액세스 리뷰 입출력이 포함됩니다. |
| kube-apiserver          | API 서버의 로그 |
| kube-audit              | get, list, create, update, delete, patch 및 post를 포함하여 모든 감사 이벤트의 감사 로그 데이터 |
| kube-audit-admin        | kube-audit 로그 범주의 하위 집합입니다. 로그에서 get 및 list 감사 이벤트를 제외하여 로그 수를 크게 줄입니다. |
| kube-controller-manager | Kubernetes와 Azure 컨트롤 플레인 간에 발생할 수 있는 문제에 대해 자세히 파악합니다. 일반적인 예는 Azure와의 상호 작용 권한이 없는 AKS 클러스터입니다. |
| kube-scheduler          | 스케줄러의 로그입니다. |
| AllMetrics              | 모든 플랫폼 메트릭을 포함합니다. 로그 쿼리를 사용하여 다른 데이터와 함께 평가할 수 있는 Log Analytics 작업 영역으로 이 값을 보냅니다. |

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

이 섹션은 AKS와 관련이 있고 Log Analytics의 쿼리에 사용할 수 있는 모든 Azure Monitor 로그 테이블을 참조합니다. 



|리소스 종류 | 참고 |
|-------|-----|
| [Kubernetes 서비스](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) | AKS에서 사용하는 모든 테이블 목록과 그 구조에 대한 설명은 다음 링크에서 확인합니다. |


모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.


## <a name="activity-log"></a>활동 로그

다음 표는 [활동 로그](../azure-monitor/essentials/activity-log.md)에 만들 수 있는 AKS와 관련한 몇 가지 작업 예를 나열합니다. 활동 로그를 사용하여 클러스터를 만든 시기, 구성 변경 여부 등과 같은 정보를 추적할 수 있습니다. 포털에서 이 정보를 보거나, 활동 로그 경고를 만들어 이벤트가 발생할 때 사전에 알림을 받을 수 있습니다.

| 작업(Operation) | Description |
|:---|:---|
| Microsoft.ContainerService/managedClusters/write | 관리형 클러스터 만들기 또는 업데이트 |
| Microsoft.ContainerService/managedClusters/delete | 관리형 클러스터 삭제 |
| Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | clusterMonitoringUser 자격 증명 나열 |
| Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | clusterAdmin 자격 증명 나열 |
| Microsoft.ContainerService/managedClusters/agentpools/write | 에이전트 풀 만들기 또는 업데이트 |

가능한 로그 항목의 전체 목록은 [Microsoft.ContainerService 리소스 공급자 옵션](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)을 참조하세요.

활동 로그 항목의 스키마에 대한 자세한 정보는 [활동 로그 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요. 

## <a name="see-also"></a>참고 항목

- Azure AKS 모니터링에 대한 설명은 [Azure AKS 모니터링](monitor-aks.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.