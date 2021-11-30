---
title: Azure Arc 지원 Kubernetes 클러스터 모니터링
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Monitor 사용하여 Azure Arc 지원 Kubernetes 클러스터의 메트릭 및 로그 수집
ms.openlocfilehash: e9ca3d0c3de46e16ebf02f7471884c86a10bd176
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133287501"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Arc 지원 Kubernetes 클러스터에 대한 컨테이너 Insights Azure Monitor

[Azure Monitor Container Insights](container-insights-overview.md) Azure Arc 지원 Kubernetes 클러스터에 대한 풍부한 모니터링 환경을 제공합니다.


## <a name="supported-configurations"></a>지원되는 구성

- Azure Monitor Container Insights 라이브 데이터 기능을 제외하고 [개요](container-insights-overview.md) 문서에 설명된 대로 Azure Arc 지원 Kubernetes 모니터링을 지원합니다. 또한 사용자에게는 [메트릭을 사용하도록 설정](container-insights-update-metrics.md)하기 위한 [소유자](../../role-based-access-control/built-in-roles.md#owner) 권한이 필요하지 않습니다.
- `Docker`, `Moby` 및 CRI 호환 컨테이너 런타임(예: `CRI-O` 및 `containerd`)
- 인증이 없는 아웃바운드 프록시 및 기본 인증이 있는 아웃바운드 프록시가 지원됩니다. 신뢰할 수 있는 인증서가 필요한 아웃바운드 프록시는 현재 지원되지 않습니다.

>[!NOTE]
>Azure Red Hat OpenShift v4.x의 Container Insights 마이그레이션하는 경우 설치 문제를 방지하기 위해 Azure Arc 사용하도록 설정된 Kubernetes에서 컨테이너 Insights 구성을 계속하기 전에 [모니터링을](./container-insights-optout-openshift-v4.md) 사용하지 않도록 설정했는지도 확인하세요.
>


## <a name="prerequisites"></a>필수 구성 요소

- [일반 클러스터 확장 설명서](../../azure-arc/kubernetes/extensions.md#prerequisites)에 나열된 필수 구성 요소를 충족했습니다.
- Log Analytics 작업 영역: Azure Monitor Container Insights는 [지역별 Azure 제품 페이지](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열된 지역에서 Log Analytics 작업 영역을 지원합니다. [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-workspace-configuration.md) 또는 [Azure Portal](../logs/quick-create-workspace.md)을 통해 사용자 고유의 작업 영역을 만들 수 있습니다.
- Azure Arc 지원 Kubernetes 리소스를 포함하는 Azure 구독에 [대한 기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할 할당이 있어야 합니다. Log Analytics 작업 영역이 다른 구독에 있는 경우 Log Analytics 작업 영역에 [Log Analytics 기여자](../logs/manage-access.md#manage-access-using-azure-permissions) 역할 할당이 필요합니다.
- 모니터링 데이터를 보려면 Log Analytics 작업 영역에 대한 [Log Analytics 읽기 권한자](../logs/manage-access.md#manage-access-using-azure-permissions) 역할 할당이 있어야 합니다.
- [Kubernetes 클러스터를 Azure Arc에 연결](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements)에서 언급한 것 외에도 아웃바운드 액세스를 위해 다음 엔드포인트를 사용하도록 설정해야 합니다.

    | 엔드포인트 | 포트 |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Azure Arc 지원 Kubernetes 리소스가 Azure 미국 정부 환경에 있는 경우 아웃바운드 액세스에 대해 다음 엔드포인트를 사용하도록 설정해야 합니다.

    | 엔드포인트 | 포트 |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- 이전에 클러스터 확장 없이 스크립트를 사용하여 이 클러스터에 Azure Monitor Container Insights를 배포한 경우 이 Helm 차트를 삭제하려면 [여기](container-insights-optout-hybrid.md)에 나열된 지침을 따르세요. 그런 다음, Azure Monitor Container Insights에 대한 클러스터 확장 인스턴스를 계속 만들 수 있습니다.


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

Azure Arc 지원 Kubernetes 클러스터가 Azure Stack Edge 경우 사용자 지정 탑재 경로를 사용해야 `/home/data/docker` 합니다.

```azurecli
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> create 명령에 설치할 확장의 버전을 명시적으로 지정하는 경우 지정된 버전이 >= 2.8.2인지 확인합니다.

## <a name="create-extension-instance-using-azure-portal"></a>Azure Portal을 사용하여 확장 인스턴스 만들기

>[!IMPORTANT]
>  Azure Stack Edge에서 실행되는 Kubernetes 클러스터에 Azure Monitor를 배포하는 경우 이러한 클러스터에 대해 사용자 지정 탑재 경로를 설정해야 하므로 Azure Portal 옵션 대신 Azure CLI 옵션을 따라야 합니다.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Azure Arc 지원 Kubernetes 리소스 블레이드에서 온보딩

1. Azure Portal 모니터링하려는 Azure Arc 지원 Kubernetes 클러스터를 선택합니다.

2. 리소스 블레이드의 '모니터링' 섹션에서 'Insights' 항목을 선택합니다.

3. 온보딩 페이지에서 'Azure Monitor 구성' 단추를 선택합니다.

4. 이제 [Log Analytics 작업 영역](../logs/quick-create-workspace.md)을 선택하여 메트릭 및 로그 데이터를 보낼 수 있습니다.

5. '구성' 단추를 선택하여 Azure Monitor Container Insights 클러스터 확장을 배포합니다.

### <a name="onboarding-from-azure-monitor-blade"></a>Azure Monitor 블레이드에서 온보딩

1. Azure Portal에서 '모니터' 블레이드로 이동하고 '인사이트' 메뉴 아래에서 '컨테이너' 옵션을 선택합니다.

2. 모니터링을 사용하도록 설정할 수 있는 Azure Arc 지원 Kubernetes 클러스터를 보려면 '모니터링되지 않는 클러스터' 탭을 선택합니다.

3. 모니터링을 사용하도록 설정할 클러스터 옆의 '사용' 링크를 클릭합니다.

4. Log Analytics 작업 영역을 선택하고 '구성' 단추를 선택하여 계속합니다.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 확장 인스턴스 만들기

1. Azure Resource Manager 템플릿 및 매개 변수를 다운로드합니다.

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. arc-k8s-azmon-extension-arm-template-params.json 파일에서 매개 변수 값을 업데이트합니다. Azure 퍼블릭 클라우드의 경우 을 `opinsights.azure.com` workspaceDomain 값으로 사용하고 AzureUSGovernment의 경우 `opinsights.azure.us` 을 workspaceDomain 값으로 사용해야 합니다.

3. 템플릿을 배포하여 Azure Monitor Container Insights 확장 만들기 

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="verify-extension-installation-status"></a>확장 설치 상태 확인
Azure Arc 지원 Kubernetes 클러스터에 대한 Azure Monitor 확장을 성공적으로 만든 후에는 Azure Portal 또는 CLI를 사용하여 설치 상태를 추가로 확인할 수 있습니다. 설치에 성공하면 상태가 '설치 중'으로 표시됩니다. 상태가 '실패'로 표시되거나 장기간 '보류 중' 상태로 유지되는 경우 아래 문제 해결 섹션으로 진행합니다.

### <a name="azure-portal"></a>Azure Portal
1. Azure Portal 확장이 설치된 Azure Arc 지원 Kubernetes 클러스터를 선택합니다.
2. 리소스 블레이드의 '설정' 섹션 아래에서 '확장' 항목을 선택합니다.
3. 이름이 'azuremonitor-containers'인 확장이 표시되고 '설치 상태' 열에 상태가 나열됩니다.
### <a name="azure-cli"></a>Azure CLI
다음 명령을 실행하여 확장의 최신 상태를 표시합니다. `Microsoft.AzureMonitor.Containers`
```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters -n azuremonitor-containers
```

## <a name="delete-extension-instance"></a>확장 인스턴스 삭제

다음 명령은 확장 인스턴스만 삭제하고 Log Analytics 작업 영역을 삭제하지는 않습니다. Log Analytics 리소스 내의 데이터는 그대로 유지됩니다.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>연결이 끊긴 클러스터
> 48시간 동안 Azure에서 클러스터의 연결이 끊어지면 Azure Resource Graph에 클러스터에 대한 정보가 없습니다. 결과적으로 인사이트 블레이드에 클러스터 상태에 대한 잘못된 정보가 표시될 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
모니터링 사용과 관련된 문제의 경우 문제를 [진단하는](https://aka.ms/azmon-ci-troubleshooting) 데 도움이 되는 문제 해결 스크립트를 제공했습니다.

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용하여 Azure Arc 지원 Kubernetes 클러스터 및 해당 클러스터에서 실행되는 워크로드의 상태 및 리소스 사용률을 수집하도록 설정하면 컨테이너 인사이트를 [사용하는 방법을](container-insights-analyze.md) 알아봅니다.

- 기본적으로 컨테이너화된 에이전트는 kube 시스템을 제외한 모든 네임스페이스에서 실행 중인 모든 컨테이너의 stdout/stderr 컨테이너 로그를 수집합니다. 특정 네임스페이스 또는 네임스페이스와 관련된 컨테이너 로그 컬렉션을 구성하려면 [컨테이너 인사이트 에이전트 구성](container-insights-agent-config.md)을 검토하여 ConfigMap 구성 파일에 대해 원하는 데이터 수집 설정을 구성합니다.

- 클러스터에서 Prometheus 메트릭을 스크랩하고 분석하려면 [Prometheus 메트릭 스크래핑 구성](container-insights-prometheus-integration.md)을 검토합니다.
