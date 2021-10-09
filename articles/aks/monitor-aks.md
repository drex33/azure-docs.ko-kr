---
title: Azure Monitor를 사용하여 AKS(Azure Kubernetes Service) 모니터링
description: Azure Monitor를 사용하여 AKS 클러스터 및 해당 워크로드의 상태 및 성능을 모니터링하는 방법을 설명합니다.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2021
ms.openlocfilehash: 464e848814da046600b05a1feea632a1ceaa141a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713839"
---
# <a name="monitoring-azure-kubernetes-service-aks-with-azure-monitor"></a>Azure Monitor를 사용하여 AKS(Azure Kubernetes Service) 모니터링
이 시나리오에서는 Azure Monitor를 사용하여 AKS(Azure Kubernetes Service)의 상태 및 성능을 모니터링하는 방법을 설명합니다. 여기에는 추세를 식별하기 위해 수집된 데이터의 모니터링, 분석 및 시각화에 중요한 원격 분석 컬렉션과 중요한 문제에 대해 사전에 알림을 받도록 경고를 구성하는 방법이 포함됩니다.

[클라우드 모니터링 가이드](/azure/cloud-adoption-framework/manage/monitor/)에서는 Azure 리소스에 대해 중점을 두어야 하는 [기본 모니터링 목표](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements)를 정의합니다. 이 시나리오에서는 Azure Monitor를 사용한 상태 모니터링에 중점을 둡니다.

## <a name="scope-of-the-scenario"></a>시나리오의 범위
이 시나리오는 Azure Monitor를 사용하여 AKS를 모니터링하는 고객을 대상으로 합니다. 다음 콘텐츠는 포함되지 않으며, 다만 시나리오에 대한 후속 업데이트에서 추가될 수 있습니다.

- Azure Arc 지원 Kubernetes에 대한 기존 콘텐츠를 참조하는 경우를 제외하고 Azure 외부의 Kubernetes 클러스터 모니터링 
- Azure Monitor 이외의 도구로 AKS 모니터링(Azure Monitor 및 컨테이너 인사이트의 공백을 채우는 경우 제외)

> [!NOTE]
> Azure Monitor는 클라우드 리소스의 가용성과 성능을 모니터링하도록 설계되었습니다. Azure Monitor에 저장된 작동 데이터는 보안 인시던트를 조사하는 데 유용할 수 있지만 Azure의 다른 서비스는 보안을 모니터링하도록 설계되었습니다. AKS에 대한 보안 모니터링은 [Azure Sentinel](../sentinel/overview.md) 및 [Azure Security Center](../security-center/security-center-introduction.md)를 통해 수행됩니다. Azure의 보안 모니터링 도구 및 Azure Monitor와의 관계에 대한 설명은 [Azure Monitor를 사용하여 가상 머신 모니터링 - 보안 모니터링](../azure-monitor/vm/monitor-virtual-machine-security.md)을 참조하세요.
>
> 보안 서비스를 사용하여 AKS를 모니터링하는 방법에 대한 자세한 내용은 [Azure Defender for Kubernetes - 이점 및 특징](../security-center/defender-for-kubernetes-introduction.md)과 [AKS(Azure Kubernetes Service) 진단 로그를 Azure Sentinel에 연결](../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks)을 참조하세요.
## <a name="container-insights"></a>컨테이너 인사이트
AKS는 다른 Azure 리소스와 마찬가지로 기본 상태 및 성능을 모니터링하는 데 사용할 수 있는 [플랫폼 메트릭 및 리소스 로그](monitor-aks-reference.md)를 생성합니다. 모니터링을 확장하려면 [컨테이너 인사이트](../azure-monitor/containers/container-insights-overview.md)를 사용하도록 설정합니다. 컨테이너 인사이트는 다른 클러스터 구성 외에도 AKS에서 호스팅되는 관리되는 Kubernetes 클러스터의 상태 및 성능을 모니터링하는 Azure Monitor의 기능입니다. 컨테이너 인사이트는 다양한 모니터링 시나리오에 대해 수집된 데이터를 분석하는 대화형 보기와 통합 문서를 제공합니다. 

[Prometheus](https://prometheus.io/) 및 [Grafana](https://www.prometheus.io/docs/visualization/grafana/)는 Kubernetes 모니터링을 위해 널리 사용되는 CNCF 지원 오픈 소스 도구입니다. AKS는 Prometheus 형식으로 많은 메트릭을 노출하기 때문에 Prometheus가 모니터링에 널리 사용됩니다. [컨테이너 인사이트](../azure-monitor/containers/container-insights-overview.md)는 AKS와 기본적으로 통합되어 중요한 메트릭 및 로그를 수집하고, 식별된 문제에 대해 경고하고, 통합 문서를 통해 시각화를 제공합니다. 또한 특정 Prometheus 메트릭을 수집하고 많은 네이티브 Azure Monitor 인사이트가 Prometheus 메트릭을 기반으로 빌드됩니다. 컨테이너 인사이트는 독립 실행형 도구인 Prometheus가 제공하지 않는 로그 수집을 포함하여 AKS의 E2E 모니터링을 보완하고 완료합니다. 많은 고객이 E2E 모니터링을 위해 Prometheus 통합과 Azure Monitor를 함께 사용합니다.

[컨테이너 인사이트 개요](../azure-monitor/containers/container-insights-overview.md)에서 컨테이너 인사이트 사용에 대해 자세히 알아보세요. 아래 [컨테이너 인사이트를 사용하여 AKS 계층 모니터링](#monitor-layers-of-aks-with-container-insights)에는 컨테이너 인사이트의 다양한 기능과 지원되는 모니터링 시나리오가 설명되어 있습니다.

:::image type="content" source="media/monitor-aks/container-insights.png" alt-text="컨테이너 인사이트" lightbox="media/monitor-aks/container-insights.png":::


## <a name="configure-monitoring"></a>모니터링 구성
다음 섹션에서는 Azure Monitor를 사용하여 AKS 클러스터의 전체 모니터링을 구성하는 데 필요한 단계에 대해 설명합니다.
### <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
컨테이너 인사이트를 지원하고 AKS 클러스터에 대한 다른 원격 분석을 수집하고 분석하려면 Log Analytics 작업 영역이 하나 이상 필요합니다. 작업 영역에 대한 비용은 없지만 데이터를 수집할 때 수집 및 보존 비용이 발생합니다. 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../azure-monitor/logs/manage-cost-storage.md)를 참조하세요.

Azure Monitor를 시작하는 경우에는 단일 작업 영역으로 시작하고 요구 사항이 발전함에 따라 추가 작업 영역을 만드는 것이 좋습니다. 많은 환경에서 모니터링하는 모든 Azure 리소스에 대해 단일 작업 영역을 사용합니다. 가용성 및 성능 원격 분석을 보안 데이터와 분리하도록 선택하는 고객이 많지만 [Azure Security Center 및 Azure Sentinel](../azure-monitor/vm/monitor-virtual-machine-security.md)에 사용되는 작업 영역을 공유할 수도 있습니다. 

[Azure Monitor 로그 배포 디자인](../azure-monitor/logs/design-logs-deployment.md)에서 작업 영역 구성을 디자인할 때 고려해야 하는 논리에 대한 자세한 내용을 참조하세요.

### <a name="enable-container-insights"></a>컨테이너 인사이트 사용
AKS 클러스터에 대해 컨테이너 인사이트를 사용하도록 설정하면 Azure Monitor에 데이터를 보내는 [Log Analytics 에이전트](../agents/../azure-monitor/agents/log-analytics-agent.md)의 컨테이너화된 버전이 배포됩니다. 새 AKS 클러스터로 작업하는지 아니면 기존 AKS 클러스터로 작업하는지에 따라 이 기능을 사용하도록 설정하는 방법이 여러 가지 있습니다. [컨테이너 인사이트를 사용하도록 설정](../azure-monitor/containers/container-insights-onboard.md)에서 필수 구성 요소 및 구성 옵션을 참조하세요.


### <a name="configure-collection-from-prometheus"></a>Prometheus의 컬렉션 구성
컨테이너 인사이트를 사용하면 Prometheus 서버 없이도 Log Analytics 작업 영역에서 특정 Prometheus 메트릭을 수집할 수 있습니다. 이러한 데이터를 Azure Monitor 기능을 사용하여 컨테이너 인사이트에서 수집한 다른 데이터와 함께 분석할 수 있습니다. 이 구성에 대한 자세한 내용은 [컨테이너 인사이트를 사용하여 Prometheus 메트릭 스크래핑 구성](../azure-monitor/containers/container-insights-prometheus-integration.md)을 참조하세요.


### <a name="collect-resource-logs"></a>리소스 로그 수집
AKS 컨트롤 플레인 구성 요소에 대한 로그는 Azure에서 [리소스 로그](../azure-monitor/essentials/resource-logs.md)로 구현됩니다. 컨테이너 인사이트는 현재 이러한 로그를 사용하지 않으므로 로그를 보고 분석하려면 사용자가 직접 로그 쿼리를 생성해야 합니다. 이러한 로그의 구조에 대한 자세한 내용 및 쿼리를 작성하는 방법은 [컨테이너 인사이트에서 로그를 쿼리하는 방법](../azure-monitor/containers/container-insights-log-query.md#resource-logs)을 참조하세요.

리소스 로그를 수집하려면 진단 설정을 만들어야 합니다. 다양한 로그 집합을 다른 위치로 보내려면 여러 진단 설정을 만듭니다. AKS 클러스터에 대한 진단 설정을 만들려면 [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 

리소스 로그를 작업 영역으로 보내는 데는 비용이 있으므로 사용하려는 로그 범주만 수집해야 합니다. 정보를 유지해야 하지만 분석에 즉시 사용할 수 있어야 하는 경우가 아니면 Azure Storage 계정으로 로그를 보내서 비용을 줄일 수 있습니다.  AKS에 사용할 수 있는 범주에 대한 설명은 [리소스 로그](monitor-aks-reference.md#resource-logs)를 참조하고 로그 데이터 수집 및 유지 비용에 대한 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../azure-monitor/logs/manage-cost-storage.md)를 참조하세요. 최소한의 범주를 수집하여 시작한 다음, 요구 사항이 증가하고 관련 비용을 이해하게 되면 추가 범주를 수집하도록 진단 설정을 수정합니다.

어떤 리소스 로그를 처음에 사용하도록 설정할지 확실하지 않으면 가장 일반적인 고객 요구 사항을 기반으로 하는 다음 표의 권장 사항을 사용하세요. 나중에 정보가 필요하다는 것을 알게 되면 다른 범주를 사용하도록 설정합니다.

| 범주 | 사용 여부 | 대상 |
|:---|:---|:---|
| cluster-autoscaler      | 자동 크기 조정을 사용하는 경우 사용하도록 설정 | Log Analytics 작업 영역 |
| 가드                   | Azure Active Directory를 사용하는 경우 사용하도록 설정 | Log Analytics 작업 영역 |
| kube-apiserver          | 사용 | Log Analytics 작업 영역 |
| kube-audit              | 사용 | Azure Storage의 Blob을 나타냅니다. 이렇게 하면 비용을 최소화하면서도 감사자가 요구하는 경우 감사 로그가 유지됩니다. |
| kube-audit-admin        | 사용 | Log Analytics 작업 영역 |
| kube-controller-manager | 사용 | Log Analytics 작업 영역 |
| kube-scheduler          | 사용 중지 | |
| AllMetrics              | 사용 | Log Analytics 작업 영역 |





## <a name="access-azure-monitor-features"></a>Azure Monitor 기능에 액세스

Azure Monitor 기능은 모든 AKS 클러스터의 경우, Azure Portal에 있는 **모니터링** 메뉴의 구독에서 액세스하거나 단일 AKS 클러스터의 경우, **Kubernetes 서비스** 메뉴의 **모니터** 섹션에서 액세스할 수 있습니다. 아래 스크린샷은 클러스터의 **모니터** 메뉴를 보여줍니다.

:::image type="content" source="media/monitor-aks/monitoring-menu.png" alt-text="AKS 모니터링 메뉴" lightbox="media/monitor-aks/monitoring-menu.png":::

| 메뉴 옵션 | Description |
|:---|:---|
| 자세한 정보 | 현재 클러스터에 대한 컨테이너 인사이트를 엽니다. 모든 클러스터에 대한 컨테이너 인사이트를 열려면 **모니터** 메뉴에서 **컨테이너** 를 선택합니다.  |
| 경고 | 현재 클러스터에 대한 경고를 봅니다. |
| 메트릭 | 범위가 현재 클러스터로 설정된 메트릭 탐색기를 엽니다. |
| 진단 설정 | 리소스 로그를 수집할 클러스터에 대한 진단 설정을 만듭니다. |
| Advisor | 권장 사항: Azure Advisor의 현재 클러스터에 대한 권장 사항입니다. |
| 로그 | 범위가 현재 클러스터로 설정된 Log Analytics를 열어서 로그 데이터를 분석하고 미리 빌드된 쿼리에 액세스합니다. |
| 통합 문서 | Kubernetes 서비스에 대한 통합 문서 갤러리를 엽니다. |




## <a name="monitor-layers-of-aks-with-container-insights"></a>컨테이너 인사이트를 사용하여 AKS 계층 모니터링
Kubernetes 구현에는 편차가 크기 때문에, 각 고객마다 AKS 모니터링에 대한 고유한 요구 사항이 있습니다. 규모, 토폴로지, 조직 역할 및 다중 클러스터 테넌시를 포함한 요소를 기반으로 접근 방식을 선택해야 합니다. 이 섹션에서는 인프라에서 시작하여 애플리케이션에 이르는 상향식 접근 방식인 일반적인 전략을 제시합니다. 각 계층에는 고유한 모니터링 요구 사항이 있습니다. 이러한 계층은 다음 다이어그램에 설명되어 있으며 다음 섹션에 더 자세히 설명되어 있습니다.

:::image type="content" source="media/monitor-aks/layers.png" alt-text="AKS 계층"  border="false":::

### <a name="level-1---cluster-level-components"></a>수준 1 - 클러스터 수준 구성 요소
클러스터 수준에는 다음 구성 요소가 포함됩니다.

| 구성 요소 | 모니터링 요구 사항 |
|:---|:---|
| 노드 |  각 노드에 대한 CPU, 메모리 및 디스크의 준비 상태와 성능을 이해하고 워크로드를 배포하기 전에 사용량 추세를 사전에 모니터링합니다. |


컨테이너 인사이트의 기존 보기 및 보고서를 사용하여 클러스터 수준 구성 요소를 모니터링합니다. **클러스터** 보기에서는 클러스터에 있는 노드의 성능(CPU 및 메모리 사용률 포함)을 빠르게 확인할 수 있습니다. **노드** 보기를 사용하면 각 노드의 상태는 물론 각 노드에서 실행되는 Pod의 상태와 성능을 볼 수 있습니다. 이 보기를 사용하고 노드 상태 및 성능을 분석하는 방법에 대한 자세한 내용은 [컨테이너 인사이트를 사용하여 Kubernetes 클러스터 성능 모니터링](../azure-monitor/containers/container-insights-analyze.md)을 참조하세요.

:::image type="content" source="media/monitor-aks/container-insights-cluster-view.png" alt-text="컨테이너 인사이트 클러스터 보기" lightbox="media/monitor-aks/container-insights-cluster-view.png":::

컨테이너 인사이트의 **노드** 통합 문서를 사용하면 GPU 사용량 외에도 디스크 용량 및 IO를 분석할 수 있습니다. 이러한 통합 문서에 대한 설명은 [노드 통합 문서](../azure-monitor/containers/container-insights-reports.md#node-workbooks)를 참조하세요.

:::image type="content" source="media/monitor-aks/container-insights-node-workbooks.png" alt-text="컨테이너 인사이트 노드 통합 문서" lightbox="media/monitor-aks/container-insights-node-workbooks.png":::


문제 해결 시나리오의 경우 유지 관리 또는 즉각적인 로그 수집을 위해 AKS 노드에 직접 액세스해야 할 수 있습니다. 보안을 위해 AKS 노드는 인터넷에 노출되지 않지만 `kubectl debug`를 사용하여 SSH로 AKS 노드에 연결할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [유지 관리 또는 문제 해결을 위해 SSH를 사용하여 AKS(Azure Kubernetes Service) 클러스터 노드에 연결](ssh.md)을 참조하세요.



### <a name="level-2---managed-aks-components"></a>수준 2 - 관리되는 AKS 구성 요소
관리되는 AKS 수준에는 다음 구성 요소가 포함됩니다.

| 구성 요소 | 모니터링 |
|:---|:---|
| API 서버 | API 서버의 상태를 모니터링하여 서비스가 다운된 경우 요청 로드의 증가 및 병목 상태 여부를 식별합니다. |
| kubelet | Kubelet 모니터링은 Pod 관리 문제, Pod가 시작되지 않거나, 노드가 준비되지 않거나, Pod가 종료되는 문제를 해결하는 데 유용합니다.  |

Azure Monitor 및 컨테이너 인사이트는 아직 API 서버에 대한 전체 모니터링을 제공하지 않습니다. 메트릭 탐색기를 사용하여 **Inflight 요청** 카운터를 볼 수 있지만 API 서버 성능에 대한 전체 보기는 Prometheus의 메트릭을 참조해야 합니다. 여기에는 요청 대기 시간 및 작업 큐 처리 시간과 같은 값이 포함됩니다. API 서버에 대한 중요한 메트릭의 보기를 제공하는 Grafana 대시보드는 [Grafana Labs](https://grafana.com/grafana/dashboards/12006)에서 사용할 수 있습니다. 기존 Grafana 서버에서 이 대시보드를 사용하거나 [Grafana에서 Azure 서비스 모니터링](../azure-monitor/visualize/grafana-plugin.md)을 사용하여 Azure에서 새 Grafana 서버를 설정합니다.

:::image type="content" source="media/monitor-aks/grafana-api-server.png" alt-text="Grafana API 서버" lightbox="media/monitor-aks/grafana-api-server.png":::

각 kubelet의 상태와 성능을 확인하려면 **Kubelet** 통합 문서를 사용합니다. 이 통합 문서에 대한 자세한 내용은 [리소스 모니터링 통합 문서](../azure-monitor/containers/container-insights-reports.md#resource-monitoring-workbooks)를 참조하세요. 문제 해결 시나리오의 경우 [AKS(Azure Kubernetes Service) 클러스터 노드에서 kubelet 로그 가져오기](kubelet-logs.md)에 설명된 프로세스를 사용하여 kubelet 로그에 액세스할 수 있습니다.

:::image type="content" source="media/monitor-aks/container-insights-kubelet-workbook.png" alt-text="컨테이너 인사이트 kubelet 통합 문서" lightbox="media/monitor-aks/container-insights-kubelet-workbook.png":::

### <a name="resource-logs"></a>리소스 로그
[리소스 로그와 함께 로그 쿼리](../azure-monitor/containers/container-insights-log-query.md#resource-logs)를 사용하여 AKS 구성 요소에서 생성된 컨트롤 플레인 로그를 분석합니다. 

### <a name="level-3---kubernetes-objects-and-workloads"></a>수준 3 - Kubernetes 개체 및 워크로드
Kubernetes 개체 및 워크로드 수준에는 다음 구성 요소가 포함됩니다.

| 구성 요소 | 모니터링 요구 사항 |
|:---|:---|
| 배포 | 배포의 실제 상태와 필요한 상태 및 여기에서 실행되는 Pod의 상태 및 리소스 사용률을 모니터링합니다.  | 
| Pod | AKS 클러스터에서 실행되는 Pod의 상태 및 리소스 사용률(CPU 및 메모리 포함)을 모니터링합니다. |
| 컨테이너 | AKS 클러스터에서 실행되는 컨테이너의 리소스 사용률(CPU 및 메모리 포함)을 모니터링합니다. |


컨테이너 인사이트의 기존 보기 및 보고서를 사용하여 컨테이너 및 Pod를 모니터링합니다. **노드** 및 **컨트롤러** 보기를 사용하여 여기에서 실행되는 Pod의 상태 및 성능을 확인하고 컨테이너의 상태 및 성능으로 드릴다운합니다. **컨테이너** 보기에서 직접 컨테이너의 상태 및 성능을 확인합니다. 이 보기를 사용하고 컨테이너 상태 및 성능을 분석하는 방법에 대한 자세한 내용은 [컨테이너 인사이트를 사용하여 Kubernetes 클러스터 성능 모니터링](../azure-monitor/containers/container-insights-analyze.md)을 참조하세요.

:::image type="content" source="media/monitor-aks/container-insights-containers-view.png" alt-text="컨테이너 인사이트 컨테이너 보기" lightbox="media/monitor-aks/container-insights-containers-view.png":::

컨테이너 인사이트의 **배포** 통합 문서를 사용하여 배포에 대해 수집된 메트릭을 볼 수 있습니다. 자세한 내용은 [컨테이너 인사이트를 사용하는 배포 및 HPA 메트릭](../azure-monitor/containers/container-insights-deployment-hpa-metrics.md)을 참조하세요.

> [!NOTE]
> 컨테이너 인사이트의 배포 보기는 현재 공개 미리 보기로 제공됩니다.

:::image type="content" source="media/monitor-aks/container-insights-deployments-workbook.png" alt-text="컨테이너 인사이트 배포 통합 문서" lightbox="media/monitor-aks/container-insights-deployments-workbook.png":::

#### <a name="live-data"></a>사용 중인 데이터
문제 해결 시나리오에서 컨테이너 인사이트는 라이브 AKS 컨테이너 로그(stdout/stderror), 이벤트 및 Pod 메트릭에 대한 액세스를 제공합니다. 이 기능을 사용하는 방법에 대한 자세한 내용은 [Kubernetes 로그, 이벤트 및 Pod 메트릭을 실시간으로 보는 방법](../azure-monitor/containers/container-insights-livedata-overview.md)을 참조하세요.

:::image type="content" source="media/monitor-aks/container-insights-live-data.png" alt-text="컨테이너 인사이트 사용 중인 데이터" lightbox="media/monitor-aks/container-insights-live-data.png":::

### <a name="level-4--applications"></a>수준 4 - 애플리케이션
애플리케이션 수준에는 AKS 클러스터에서 실행되는 애플리케이션 워크로드가 포함됩니다.

| 구성 요소 | 모니터링 요구 사항 |
|:---|:---|
| 애플리케이션 | 마이크로 서비스 애플리케이션 배포를 모니터링하여 애플리케이션 오류 및 대기 시간 문제를 식별합니다. 요청 속도, 응답 시간 및 예외와 같은 정보를 포함합니다. |

Application Insights는 AKS 및 기타 환경에서 실행되는 애플리케이션에 대한 전체 모니터링을 제공합니다. Java 애플리케이션이 있는 경우 [Kubernetes에 대한 제로 계측 애플리케이션 모니터링 - Azure Monitor Application Insights](../azure-monitor/app/kubernetes-codeless.md)에 따라 코드를 계측하지 않고 모니터링을 제공할 수 있습니다. 하지만 전체 모니터링을 위해서는 애플리케이션에 따라 코드 기반 모니터링을 구성해야 합니다.

- [ASP.NET 애플리케이션](../azure-monitor/app/asp-net.md)
- [ASP.NET Core 애플리케이션](../azure-monitor/app/asp-net-core.md)
- [.NET 콘솔 애플리케이션](../azure-monitor/app/console.md)
- [Java](../azure-monitor/app/java-in-process-agent.md)
- [Node.JS](../azure-monitor/app/nodejs.md)
- [Python](../azure-monitor/app/opencensus-python.md)
- [기타 플랫폼](../azure-monitor/app/platforms.md)

[Application Insights란?](../azure-monitor/app/app-insights-overview.md)을 참조하세요. 

### <a name="level-5--external-components"></a>수준 5 - 외부 구성 요소
AKS 외부 구성 요소에는 다음이 포함됩니다.

| 구성 요소 | 모니터링 요구 사항 |
|:---|:---|
| 서비스 메시, 수신, 송신 | 구성 요소 기반 메트릭입니다. |
| 데이터베이스 및 작업 큐 | 구성 요소 기반 메트릭입니다. |

Prometheus 및 Grafana 또는 기타 전용 도구를 사용하여 서비스 메시, 수신, 송신과 같은 외부 구성 요소를 모니터링합니다. Azure Monitor의 다른 기능을 사용하여 데이터베이스 및 기타 Azure 리소스를 모니터링합니다.

## <a name="analyze-metric-data-with-metrics-explorer"></a>메트릭 탐색기를 사용하여 메트릭 데이터 분석
컨테이너에 대해 수집된 메트릭 데이터의 사용자 지정 분석을 수행하려면 메트릭 탐색기를 사용합니다. 메트릭 탐색기를 사용하면 차트를 그리고, 추세 간 상관 관계를 시각적으로 표시하고, 메트릭 값의 급등 및 급락을 조사할 수 있습니다. 메트릭 값이 임계값을 초과하는 경우 사전에 알려주는 메트릭 경고를 만들고 조직의 다른 구성원이 사용할 수 있도록 대시보드에 차트를 고정합니다.

이 기능을 사용하는 방법에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. AKS에 대해 수집되는 플랫폼 메트릭 목록은 [AKS 데이터 참조 메트릭 모니터링](monitor-aks-reference.md#metrics)을 참조하세요. 클러스터에 대해 컨테이너 인사이트를 사용하도록 설정하면 [추가 메트릭 값](../azure-monitor/containers/container-insights-update-metrics.md)을 사용할 수 있습니다.

:::image type="content" source="media/monitor-aks/metrics-explorer.png" alt-text="메트릭 탐색기" lightbox="media/monitor-aks/metrics-explorer.png":::



## <a name="analyze-log-data-with-log-analytics"></a>Log Analytics를 사용하여 로그 데이터 분석
리소스 로그를 분석하거나 컨테이너 인사이트에서 보기를 만드는 데 사용되는 데이터를 자세히 분석하려는 경우 Log Analytics를 사용합니다. Log Analytics를 사용하면 로그 데이터에 대한 사용자 지정 분석을 수행할 수 있습니다. 

로그 쿼리를 사용하여 컨테이너 인사이트에서 수집한 데이터를 분석하는 방법에 대한 자세한 내용은 [컨테이너 인사이트에서 로그를 쿼리하는 방법](../azure-monitor/containers/container-insights-log-query.md)을 참조하세요. 이러한 쿼리를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor Log Analytics에서 쿼리 사용](../azure-monitor/logs/queries.md)을 참조하고 Log Analytics를 사용하여 쿼리를 실행하고 결과로 작업하는 방법에 대한 전체 자습서는 [Log Analytics 자습서](../azure-monitor/logs/log-analytics-tutorial.md) 참조하세요.

메트릭 탐색기에서 분석할 수 있는 AKS에 대해 수집된 테이블 목록은 [AKS 데이터 참조 로그 모니터링](monitor-aks-reference.md#azure-monitor-logs-tables)을 참조하세요.

:::image type="content" source="media/monitor-aks/log-analytics-queries.png" alt-text="Kubernetes에 대한 Log Analytics 쿼리" lightbox="media/monitor-aks/log-analytics-queries.png":::

컨테이너 인사이트 데이터 외에도 로그 쿼리를 사용하여 AKS의 리소스 로그를 분석할 수 있습니다. 사용 가능한 로그 범주 목록은 [AKS 데이터 참조 리소스 로그](monitor-aks-reference.md#resource-logs)를 참조하세요. 데이터가 수집되기 전에 [모니터링 구성](#configure-monitoring)의 설명에 따라 각 범주를 수집하기 위한 진단 설정을 만들어야 합니다. 




## <a name="alerts"></a>경고
[Azure Monitor의 경고](../azure-monitor/alerts/alerts-overview.md)는 모니터링 데이터의 패턴과 흥미로운 데이터를 사전에 알려줍니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. AKS 클러스터에 대해 미리 구성된 경고 규칙은 없지만 컨테이너 인사이트에서 수집한 데이터를 기반으로 직접 만들 수 있습니다.

> [!IMPORTANT]
> 대부분의 경고 규칙에는 비용이 있으며, 규칙 유형, 규칙에 포함된 차원 수 및 실행 빈도에 따라 달라집니다. 경고 규칙을 만들기 전에 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에서 **경고 규칙** 을 참조하세요.


### <a name="choosing-the-alert-type"></a>경고 유형 선택
Azure Monitor에서 가장 일반적인 유형의 경고 규칙은 [메트릭 경고](../azure-monitor/alerts/alerts-metric.md) 및 [로그 쿼리 경고](../azure-monitor/alerts/alerts-log-query.md)입니다. 특정 시나리오에 대해 만드는 경고 규칙의 유형은 경고하려는 데이터가 있는 위치에 따라 달라집니다. 특정 경고 시나리오에 대한 데이터를 메트릭과 로그 모두에서 사용할 수 있어서 어떤 규칙 유형을 사용할지 결정해야 하는 경우가 있을 수 있습니다. 

일반적으로 가능하면 로그 경고보다는 메트릭 경고를 사용하는 것이 가장 좋은 전략입니다. 메트릭 경고가 응답성이 높고 상태 저장이기 때문입니다. 메트릭 탐색기에서 분석할 수 있는 모든 값에 대해 메트릭 경고를 만들 수 있습니다. 경고 규칙의 논리에 로그의 데이터가 필요하거나 더 복잡한 논리가 필요한 경우 로그 쿼리 경고 규칙을 사용할 수 있습니다.

예를 들어 애플리케이션 워크로드가 과도한 CPU를 사용할 때 경고하려는 경우 CPU 메트릭을 사용하여 메트릭 경고를 만들 수 있습니다. 컨트롤 플레인 로그에서 특정 메시지가 발견되는 경우 경고가 필요하면 로그 경고가 필요합니다.
### <a name="metric-alert-rules"></a>메트릭 경고 규칙
메트릭 경고 규칙은 메트릭 탐색기와 동일한 메트릭 값을 사용합니다. 실제로 현재 분석 중인 데이터를 사용하여 메트릭 탐색기에서 직접 경고 규칙을 만들 수 있습니다. [AKS 데이터 참조 메트릭](monitor-aks-reference.md#metrics)의 모든 값을 메트릭 경고 규칙에 사용할 수 있습니다.

컨테이너 인사이트에는 AKS 클러스터에 대한 권장 메트릭 경고 규칙 집합을 만드는 공개 미리 보기 기능이 포함되어 있습니다. 이 기능은 메트릭 탐색기에서도 사용할 수 있는 경고 규칙에 사용되는 새 메트릭 값(미리 보기)을 만듭니다. 이 기능 및 AKS에 대한 메트릭 경고 만들기에 대한 자세한 내용은 [컨테이너 인사이트의 권장 메트릭 경고(미리 보기)](../azure-monitor/containers/container-insights-metric-alerts.md)를 참조하세요.


### <a name="log-alerts-rules"></a>로그 경고 규칙
로그 경고 규칙을 사용하여 로그 쿼리 결과에서 경고를 생성합니다. 이러한 결과는 컨테이너 인사이트 또는 AKS 리소스 로그에서 수집된 데이터일 수 있습니다. AKS에 대한 로그 경고 규칙 및 경고 규칙을 위해 설계된 샘플 쿼리 세트에 대한 자세한 내용은 [컨테이너 인사이트에서 로그 경고를 만드는 방법](../azure-monitor/containers/container-insights-log-alerts.md)을 참조하세요. [컨테이너 인사이트에서 로그를 쿼리하는 방법](../azure-monitor/containers/container-insights-log-query.md)에서 경고 규칙에 대해 수정할 수 있는 로그 쿼리에 대한 자세한 내용을 참조할 수도 있습니다.

### <a name="virtual-machine-alerts"></a>가상 머신 경고
AKS는 AKS 워크로드를 실행하려면 정상이어야 하는 가상 머신 확장 집합에 의존합니다. [Azure Monitor를 사용하여 가상 머신 모니터링: 경고](../azure-monitor/vm/monitor-virtual-machine-alerts.md)의 지침을 사용하여 가상 머신의 CPU, 메모리 및 스토리지와 같은 중요한 메트릭에 대해 경고할 수 있습니다.

### <a name="prometheus-alerts"></a>Prometheus 경고
경고 조건에 필요한 데이터가 Azure Monitor에 없거나 경고가 충분히 응답하지 않을 수 있는 조건이면 Prometheus에서 경고를 구성해야 합니다. 한 가지 예는 API 서버에 대한 경고입니다. Azure Monitor는 API 서버에 대한 중요한 정보(사용 가능 여부 또는 병목 상태 발생 여부를 포함)를 수집하지 않습니다. kube-apiserver 리소스 로그 범주의 데이터를 사용하여 로그 쿼리 경고를 생성할 수 있지만 경고를 받기까지 최대 몇 분 정도가 소요될 수 있어서 요구 사항을 충족하지 못할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

- AKS에서 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조는 [AKS 데이터 참조 모니터링](monitor-aks-reference.md)를 참조하세요.