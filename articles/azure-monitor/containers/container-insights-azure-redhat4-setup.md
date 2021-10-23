---
title: 컨테이너 인사이트로 Azure Red Hat OpenShift v4.x 구성 | Microsoft Docs
description: 이 문서에서는 Azure Red Hat OpenShift 버전 4 이상에서 호스트되는 Azure Monitor를 사용하여 Kubernetes 클러스터에 대한 모니터링을 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 4f9bdaf07903294ede0b8530a28e716c27d9cd9e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231515"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>컨테이너 인사이트로 Azure Red Hat OpenShift v4.x 구성

컨테이너 인사이트는 AKS(Azure Kubernetes Service) 및 AKS 엔진 클러스터에 대한 풍부한 모니터링 환경을 제공합니다. 이 문서에서는 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 버전 4.x에서 호스트되는 Kubernetes 클러스터에 대한 모니터링을 사용하여 비슷한 모니터링 환경을 구현하는 방법을 설명합니다.

>[!NOTE]
>Azure Red Hat OpenShift에 대한 지원은 현재 공개 미리 보기의 기능입니다.
>

이 문서에 설명된 지원되는 메서드를 사용하여 Azure Red Hat OpenShift v4.x의 하나 이상의 기존 배포에 대한 컨테이너 인사이트를 사용하도록 설정할 수 있습니다.

기존 클러스터의 경우 [Azure CLI에서 이 Bash 스크립트](/cli/azure/openshift#az_openshift_create&preserve-view=true)를 실행합니다.

## <a name="supported-and-unsupported-features"></a>지원되는/지원되지 않는 기능

컨테이너 인사이트는 다음 기능을 제외하고 [컨테이너 인사이트 개요](container-insights-overview.md)에 설명된 대로 Azure Red Hat OpenShift v4.x 모니터링을 지원합니다.

- 라이브 데이터(미리 보기)
- 클러스터 노드 및 Pod에서 [메트릭 수집](container-insights-update-metrics.md) 및 Azure Monitor 메트릭 데이터베이스에 저장

## <a name="prerequisites"></a>사전 요구 사항

- Azure CLI 버전 2.0.72 이상  

- [Helm 3](https://helm.sh/docs/intro/install/) CLI 도구

- [OpenShift CLI](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)의 최신 버전

- [Bash 버전 4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 명령줄 도구

- [Log Analytics 작업 영역](../logs/design-logs-deployment.md)

    컨테이너 인사이트는 Azure [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열된 지역에서 Log Analytics 작업 영역을 지원합니다. 사용자 고유의 작업 영역을 만들려면 [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../logs/quick-create-workspace.md)에서 만들 수 있습니다.

- 컨테이너 인사이트에서 기능을 사용하도록 설정하고 액세스하려면 최소한 Azure 구독에 Azure *기여자* 역할이 있어야 하고, 컨테이너 인사이트로 구성된 Log Analytics 작업 영역에 [*Log Analytics 기여자*](../logs/manage-access.md#manage-access-using-azure-permissions) 역할이 있어야 합니다.

- 모니터링 데이터를 보려면 컨테이너 인사이트를 사용하여 구성된 Log Analytics 작업 영역에 [*Log Analytics 읽기 권한자*](../logs/manage-access.md#manage-access-using-azure-permissions) 역할이 있어야 합니다.

## <a name="enable-monitoring-for-an-existing-cluster"></a>기존 클러스터에 대한 모니터링 사용

제공된 Bash 스크립트를 사용하여 Azure에 배포된 Azure Red Hat OpenShift 버전 4 이상 클러스터에 대한 모니터링을 사용하도록 설정하려면 다음을 수행합니다.

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurecli
    az login
    ```

1. 다음 명령을 실행하여 모니터링 추가 기능을 사용하여 클러스터를 구성하는 스크립트를 로컬 폴더에 다운로드하여 저장합니다.

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. [자습서: Azure Red Hat OpenShift 4 클러스터에 연결](../../openshift/tutorial-connect-cluster.md)의 지침을 사용하여 ARO v4 클러스터에 연결합니다.


### <a name="integrate-with-an-existing-workspace"></a>기존 작업 영역과 통합

이 섹션에서는 이전에 다운로드한 Bash 스크립트를 사용하여 클러스터의 모니터링을 사용하도록 설정합니다. 기존 Log Analytics 작업 영역과 통합하려면 먼저 `logAnalyticsWorkspaceResourceId` 매개 변수에 필요한 Log Analytics 작업 영역의 전체 리소스 ID를 확인한 다음, 명령을 실행하여 지정된 작업 영역에 대해 모니터링 추가 기능을 사용하도록 설정합니다.

지정할 작업 영역이 없는 경우 [기본 작업 영역으로 통합](#integrate-with-the-default-workspace) 섹션으로 건너뛰고 스크립트가 새 작업 영역을 만들도록 할 수 있습니다.

1. 다음 명령을 실행하여 액세스 권한이 있는 모든 구독을 나열합니다.

    ```azurecli
    az account list --all -o table
    ```

    출력은 다음과 같이 표시됩니다.

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. **SubscriptionId** 에 대한 값을 복사합니다.

1. 다음 명령을 실행하여 Log Analytics 작업 영역을 호스팅하는 구독으로 전환합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. 다음 명령을 실행하여 구독의 작업 영역 목록을 기본 JSON 형식으로 표시합니다.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. 출력에서 작업 영역 이름을 찾은 다음, 해당 Log Analytics 작업 영역의 전체 리소스 ID를 필드 **ID** 로 복사합니다.

1. 모니터링을 사용하도록 설정하려면 다음 명령을 실행합니다. `azureAroV4ClusterResourceId` 및 `logAnalyticsWorkspaceResourceId` 매개 변수의 값을 바꿉니다.

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    다음은 내보내기 명령을 사용하여 3개의 변수를 채운 후 실행해야 하는 명령입니다.

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="integrate-with-the-default-workspace"></a>기본 작업 영역과 통합

이 섹션에서는 다운로드한 Bash 스크립트를 사용하여 Azure Red Hat OpenShift v4.x 클러스터에 대한 모니터링을 사용하도록 설정합니다.

이 예제에서는 기존 작업 영역을 미리 만들거나 지정할 필요가 없습니다. 이 명령은 해당 지역에서 클러스터 구독의 기본 리소스 그룹에 기본 작업 공간이 아직 없는 경우 기본 작업 공간을 만들어서 프로세스를 간소화합니다.

만들어진 기본 작업 영역은 *DefaultWorkspace-\<GUID>-\<Region>* 의 형식입니다.  

`azureAroV4ClusterResourceId` 매개 변수 값으로 바꿉니다.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

예를 들면 다음과 같습니다.

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId 

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="enable-monitoring-from-the-azure-portal"></a>Azure Portal에서 모니터링을 사용하도록 설정

컨테이너 인사이트의 다중 클러스터 뷰는 모니터링되지 않는 **클러스터** 탭에서 모니터링을 사용하도록 설정하지 않은 Azure Red Hat OpenShift 클러스터를 강조 표시합니다. 클러스터 옆의 **사용** 옵션은 포털에서 모니터링 온보딩을 시작하지 않습니다. 이 문서 앞부분에 설명된 단계를 수행하여 수동으로 모니터링을 사용하도록 설정하도록 이 문서로 리디렉션됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 창이나 홈페이지에서 **Azure Monitor** 를 선택합니다.

1. **인사이트** 섹션에서 **컨테이너** 를 선택합니다.

1. **모니터 - 컨테이너** 페이지에서 **모니터링되지 않은 클러스터** 를 선택합니다.

1. 모니터링되지 않은 클러스터 목록에서 클러스터를 선택한 다음, **사용** 을 선택합니다.

    **클러스터 유형** 열에서 **ARO** 값을 찾아 목록에서 결과를 식별할 수 있습니다. **사용** 을 선택하고 나면 이 문서로 리디렉션됩니다.

## <a name="next-steps"></a>다음 단계

- 이제 모니터링을 사용하도록 설정하여 RedHat OpenShift 버전 4.x 클러스터와 해당 클러스터에서 실행 중인 작업의 상태 및 리소스 사용률을 수집합니다. 컨테이너 인사이트 [사용 방법](container-insights-analyze.md)을 알아보세요.

- 기본적으로 컨테이너화된 에이전트는 kube 시스템을 제외한 모든 네임스페이스에서 실행 중인 모든 컨테이너의 *stdout* 및 *stderr* 컨테이너 로그를 수집합니다. 특정 네임스페이스 또는 네임스페이스와 관련된 컨테이너 로그 컬렉션을 구성하려면 [컨테이너 인사이트 에이전트 구성](container-insights-agent-config.md)을 검토하여 *ConfigMap* 구성 파일에 대해 원하는 데이터 수집 설정을 구성합니다.

- 클러스터에서 Prometheus 메트릭을 스크랩하고 분석하려면 [Prometheus 메트릭 스크래핑 구성](container-insights-prometheus-integration.md)을 검토합니다.

- 컨테이너 인사이트를 사용하여 클러스터 모니터링을 중지하는 방법을 알아보려면 [Azure Red Hat OpenShift 클러스터 모니터링을 중지하는 방법](./container-insights-optout-openshift-v3.md)을 참조하세요.