---
title: Azure Arc 지원 Kubernetes 클러스터 모니터링
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Monitor를 사용하여 Azure Arc 지원 Kubernetes 클러스터의 메트릭 및 로그 수집
ms.openlocfilehash: eb1229e9ccccab3a8210531b7aa5dfba6b46abbf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613152"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 지원 Kubernetes 클러스터에 대한 Azure Monitor Container Insights

[Azure Monitor Container Insights](container-insights-overview.md)는 Azure Arc 지원 Kubernetes 클러스터에 대한 풍부한 모니터링 환경을 제공합니다.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>지원되는 구성

- Azure Monitor Container Insights는 라이브 데이터(미리 보기) 기능을 제외하고 [개요](container-insights-overview.md) 문서에 설명된 대로 Azure Arc 지원 Kubernetes(미리 보기) 모니터링을 지원합니다. 또한 사용자에게는 [메트릭을 사용하도록 설정](container-insights-update-metrics.md)하기 위한 [소유자](../../role-based-access-control/built-in-roles.md#owner) 권한이 필요하지 않습니다.
- `Docker`, `Moby` 및 CRI 호환 컨테이너 런타임(예: `CRI-O` 및 `containerd`)
- 인증이 없는 아웃바운드 프록시 및 기본 인증이 있는 아웃바운드 프록시가 지원됩니다. 신뢰할 수 있는 인증서가 필요한 아웃바운드 프록시는 현재 지원되지 않습니다.

>[!NOTE]
> Azure Monitor 컨테이너 Insights 현재 Kubernetes v1.22 이상은 지원하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [일반 클러스터 확장 설명서](../../azure-arc/kubernetes/extensions.md#prerequisites)에 나열된 필수 구성 요소를 충족했습니다.
- Log Analytics 작업 영역: Azure Monitor Container Insights는 [지역별 Azure 제품 페이지](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열된 지역에서 Log Analytics 작업 영역을 지원합니다. [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md) 또는 [Azure Portal](../logs/quick-create-workspace.md)을 통해 사용자 고유의 작업 영역을 만들 수 있습니다.
- Azure Arc 지원 Kubernetes 리소스를 포함하는 Azure 구독에 대한 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할 할당이 있어야 합니다. Log Analytics 작업 영역이 다른 구독에 있는 경우 Log Analytics 작업 영역에 [Log Analytics 기여자](../logs/manage-access.md#manage-access-using-azure-permissions) 역할 할당이 필요합니다.
- 모니터링 데이터를 보려면 Log Analytics 작업 영역에 대한 [Log Analytics 읽기 권한자](../logs/manage-access.md#manage-access-using-azure-permissions) 역할 할당이 있어야 합니다.
- [Kubernetes 클러스터를 Azure Arc에 연결](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements)에서 언급한 것 외에도 아웃바운드 액세스를 위해 다음 엔드포인트를 사용하도록 설정해야 합니다.

    | 엔드포인트 | 포트 |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Arc 지원 Kubernetes 리소스가 Azure US Government 환경에 있는 경우 아웃바운드 액세스를 위해 다음 엔드포인트를 사용하도록 설정해야 합니다.

    | 엔드포인트 | 포트 |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- 이전에 클러스터 확장 없이 스크립트를 사용하여 이 클러스터에 Azure Monitor Container Insights를 배포한 경우 이 Helm 차트를 삭제하려면 [여기](container-insights-optout-hybrid.md)에 나열된 지침을 따르세요. 그런 다음, Azure Monitor Container Insights에 대한 클러스터 확장 인스턴스를 계속 만들 수 있습니다.

    >[!NOTE]
    > Azure Monitor Container Insights(미리 보기)를 배포하는 스크립트 기반 버전은 배포의 [클러스터 확장](../../azure-arc/kubernetes/extensions.md) 형식으로 대체됩니다. 이전에 스크립트를 통해 배포된 Azure Monitor는 2021년 6월까지만 지원되므로 가장 빠른 배포의 클러스터 확장 형식으로 마이그레이션하는 것이 좋습니다.

### <a name="identify-workspace-resource-id"></a>작업 영역 리소스 ID 식별

다음 명령을 실행하여 Log Analytics 작업 영역의 전체 Azure Resource Manager 식별자를 찾습니다. 

1. 다음 명령을 사용하여 액세스 권한이 있는 모든 구독을 나열합니다.

    ```azurecli
    az account list --all -o table
    ```

2. 다음 명령을 사용하여 Log Analytics 작업 영역을 호스트하는 구독으로 전환합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 다음 예제는 기본 JSON 형식의 구독에 있는 작업 영역 목록을 표시합니다.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    출력에서 관심 있는 작업 영역 이름을 찾습니다. 해당 Log Analytics 작업 영역의 Azure Resource Manager 식별자를 나타내는 `id`필드입니다.

    >[!TIP]
    > 이 `id`는 Azure Portal을 통해 Log Analytics 작업 영역의 *개요* 블레이드에서도 찾을 수 있습니다.

## <a name="create-extension-instance-using-azure-cli"></a>Azure CLI를 사용하여 확장 인스턴스 만들기

### <a name="option-1---with-default-values"></a>옵션 1 - 기본값 사용

이 옵션은 다음 기본값을 사용합니다.

- 클러스터의 지역에 해당하는 기존 기본 로그 분석 작업 영역을 만들거나 사용
- Azure Monitor 클러스터 확장에 자동 업그레이드가 사용하도록 설정됨

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>옵션 2 - 기존 Azure Log Analytics 작업 영역 사용

*기여자* 또는 더 허용적인 역할 할당이 있는 모든 구독에서 기존 Azure Log Analytics 작업 영역을 사용할 수 있습니다.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>옵션 3 - 고급 구성 사용

기본 리소스 요청 및 제한을 조정하려는 경우 고급 구성 설정을 사용할 수 있습니다.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

사용 가능한 구성 설정에 대해 [Helm 차트의 리소스 요청 및 제한 섹션](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml)을 확인하세요.

### <a name="option-4---on-azure-stack-edge"></a>옵션 4 - Azure Stack Edge에서

Azure Arc 지원 Kubernetes 클러스터가 Azure Stack Edge에 있는 경우 사용자 지정 탑재 경로 `/home/data/docker`를 사용해야 합니다.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> create 명령에 설치할 확장의 버전을 명시적으로 지정하는 경우 지정된 버전이 >= 2.8.2인지 확인합니다.

## <a name="create-extension-instance-using-azure-portal"></a>Azure Portal을 사용하여 확장 인스턴스 만들기

>[!IMPORTANT]
>  Azure Stack Edge에서 실행되는 Kubernetes 클러스터에 Azure Monitor를 배포하는 경우 이러한 클러스터에 대해 사용자 지정 탑재 경로를 설정해야 하므로 Azure Portal 옵션 대신 Azure CLI 옵션을 따라야 합니다.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Azure Arc 사용 Kubernetes 리소스 블레이드에서 온보딩

1. Azure Portal에서 모니터링하려는 Arc 지원 Kubernetes 클러스터를 선택합니다.

2. 리소스 블레이드의 '모니터링' 섹션 아래에서 '인사이트(미리 보기)' 항목을 선택합니다.

3. 온보딩 페이지에서 'Azure Monitor 구성' 단추를 선택합니다.

4. 이제 [Log Analytics 작업 영역](../logs/quick-create-workspace.md)을 선택하여 메트릭 및 로그 데이터를 보낼 수 있습니다.

5. '구성' 단추를 선택하여 Azure Monitor Container Insights 클러스터 확장을 배포합니다.

### <a name="onboarding-from-azure-monitor-blade"></a>Azure Monitor 블레이드에서 온보딩

1. Azure Portal에서 '모니터' 블레이드로 이동하고 '인사이트' 메뉴 아래에서 '컨테이너' 옵션을 선택합니다.

2. 모니터링을 사용하도록 설정할 수 있는 Azure Arc 지원 Kubernetes 클러스터를 보려면 '모니터링 되지 않은 클러스터' 탭을 선택합니다.

3. 모니터링을 사용하도록 설정할 클러스터 옆의 '사용' 링크를 클릭합니다.

4. Log Analytics 작업 영역을 선택하고 '구성' 단추를 선택하여 계속합니다.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 확장 인스턴스 만들기

1. Azure Resource Manager 템플릿 및 매개 변수를 다운로드합니다.

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. arc-k8s-azmon-extension-arm-template-params.json 파일의 매개 변수 값을 업데이트합니다. Azure 퍼블릭 클라우드의 경우 `opinsights.azure.com`을 workspaceDomain 값으로 사용해야 합니다.

3. 템플릿을 배포하여 Azure Monitor Container Insights 확장 만들기 

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>확장 인스턴스 삭제

다음 명령은 확장 인스턴스만 삭제하고 Log Analytics 작업 영역을 삭제하지는 않습니다. Log Analytics 리소스 내의 데이터는 그대로 유지됩니다.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>연결이 끊긴 클러스터
> 48시간 동안 Azure에서 클러스터의 연결이 끊어지면 Azure Resource Graph에 클러스터에 대한 정보가 없습니다. 결과적으로 인사이트 블레이드에 클러스터 상태에 대한 잘못된 정보가 표시될 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Arc 지원 Kubernetes 클러스터 및 여기에서 실행되는 워크로드의 상태 및 리소스 사용률을 수집할 수 있도록 모니터링을 사용해서 Container Insights를 [사용하는 방법](container-insights-analyze.md)을 알아보세요.

- 기본적으로 컨테이너화된 에이전트는 kube 시스템을 제외한 모든 네임스페이스에서 실행 중인 모든 컨테이너의 stdout/stderr 컨테이너 로그를 수집합니다. 특정 네임스페이스 또는 네임스페이스와 관련된 컨테이너 로그 컬렉션을 구성하려면 [컨테이너 인사이트 에이전트 구성](container-insights-agent-config.md)을 검토하여 ConfigMap 구성 파일에 대해 원하는 데이터 수집 설정을 구성합니다.

- 클러스터에서 Prometheus 메트릭을 스크랩하고 분석하려면 [Prometheus 메트릭 스크래핑 구성](container-insights-prometheus-integration.md)을 검토합니다.
