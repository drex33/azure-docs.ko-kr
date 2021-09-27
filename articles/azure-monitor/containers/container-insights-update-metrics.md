---
title: 메트릭에 대한 컨테이너 인사이트를 업데이트하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트를 업데이트하여 집계된 메트릭에 대한 탐색 및 경고를 지원하는 사용자 지정 메트릭 기능을 사용하도록 설정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cff5933db1d74e9853120a07444e399005b2e498
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620828"
---
# <a name="how-to-update-container-insights-to-enable-metrics"></a>메트릭을 사용하도록 컨테이너 인사이트를 업데이트하는 방법

컨테이너 인사이트는 AKS (Azure Kubernetes Services) 및 Azure Arc 사용 Kubernetes 클러스터 노드 및 Pod에서 메트릭 수집에 대한 지원을 도입하고, 해당 메트릭을 Azure Monitor 메트릭 저장소에 기록합니다. 이러한 변경은 성능 차트에 집계 계산(Avg, Count, Max, Min, Sum)을 제공할 때 향상된 적시성을 제공하기 위한 것으로, Azure Portal 대시보드의 고정 성능 차트를 지원하고, 메트릭 경고를 지원합니다.

>[!NOTE]
>이 기능은 현재 Azure Red Hat OpenShift 클러스터를 지원하지 않습니다.
>

이 기능의 일부로 사용할 수 있는 메트릭은 다음과 같습니다.

| 메트릭 네임스페이스 | 메트릭 | Description |
|------------------|--------|-------------|
| Insights.container/nodes | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | *노드* 메트릭으로, 이것들에는 *호스트* 가 차원으로 포함됩니다. 또한 노드 이름이<br> *호스트* 차원에 대한 값으로 포함됩니다. |
| Insights.container/nodes | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | *Pod* 메트릭으로서, 이것들에는 ControllerName, Kubernetes namespace, name, phase 등이 차원으로 포함됩니다. |
| Insights.container/containers | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |
| Insights.container/persistentvolumes | pvUsageExceededPercentage | |

이러한 새 기능을 지원하기 위해 새로운 컨테이너화된 에이전트가 릴리스에 포함되어 있습니다(AKS용 버전 **microsoft/oms:ciprod05262020** 및 Azure Arc 사용 Kubernetes 클러스터용 버전 **microsoft/oms: Ciprod09252020**). AKS의 새 배포에는 이 구성 변경 및 기능이 자동으로 포함됩니다. 이 기능을 지원하기 위해 Azure Portal, Azure PowerShell에서 또는 Azure CLI을 사용하여 클러스터 업데이트를 수행할 수 있습니다. Azure PowerShell 및 CLI의 경우. 클러스터 당 또는 구독의 모든 클러스터에 대해 이를 사용으로 설정할 수 있습니다.

각 프로세스는 모니터링 추가 기능에 대한 클러스터의 서비스 주체 또는 사용자 할당 MSI에 **모니터링 메트릭 게시자** 역할을 할당하여, 에이전트에서 수집된 데이터를 클러스터 리소스에 게시할 수 있습니다. 모니터링 메트릭 게시자에는 리소스에 대해 메트릭을 푸시할 수 있는 권한만 있고, 모든 상태를 변경하거나, 리소스를 업데이트하거나, 데이터를 읽을 수는 없습니다. 역할에 대한 자세한 내용은 [Monitoring Metrics Publisher](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)를 참조하세요. 모니터링 메트릭 게시자 역할 요구 사항은 Azure Arc 사용 Kubernetes 클러스터에는 적용되지 않습니다.

> [!IMPORTANT]
> Azure Arc 사용 Kubernetes 클러스터에는 필요한 최소 에이전트 버전이 이미 있기 때문에 업그레이드가 필요하지 않습니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하는 경우 모니터링 추가 기능에 대한 클러스터의 서비스 주체 또는 사용자 할당 MSI에 **모니터링 메트릭 게시자** 역할이 자동으로 할당됩니다.

## <a name="prerequisites"></a>필수 구성 요소

클러스터를 업데이트하기 전에 다음을 확인합니다.

* Azure 지역의 하위 집합에서만 사용자 지정 메트릭을 사용할 수 있습니다. 지원되는 지역 목록은 [여기](../essentials/metrics-custom-overview.md#supported-regions)를 참조하세요.

* 사용자는 AKS 클러스터 리소스에 대한 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** 역할의 멤버로서, 노드 및 pod 사용자 지정 성능 메트릭을 수집하도록 설정합니다. 이 요구 사항은 Azure Arc 사용 Kubernetes 클러스터에 적용되지 않습니다.

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Azure Portal에서 클러스터 업그레이드

컨테이너 insights를 통해 모니터링되는 기존 AKS 클러스터의 경우, Azure Monitor의 다중 클러스터 보기에서, 또는 왼쪽 창에서 **Insights** 를 선택하여 클러스터에서 직접 해당 상태를 볼 수 있도록 클러스터를 선택한 후, 포털 상단에 배너를 표시해야 합니다.

![Azure Portal에서 AKS 클러스터 배너 업그레이드](./media/container-insights-update-metrics/portal-banner-enable-01.png)

**사용** 을 클릭하면 클러스터를 업그레이드하는 프로세스가 시작됩니다. 이 프로세스는 완료하는 데 수 초가 소요되며 메뉴의 알림에서 진행 상황을 추적할 수 있습니다.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Azure Command Shell에서 Bash를 사용하여 모든 클러스터 업그레이드

Azure Command Shell에서 Bash를 사용하여 구독에서 모든 클러스터를 업데이트하려면 다음 단계를 수행합니다.

1. Azure CLI를 사용하여 다음 명령을 실행합니다.  AKS 클러스터에 대한 **AKS 개요** 페이지의 값을 사용하여 **subscriptionId** 값을 편집합니다.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Azure CLI를 사용하여 클러스터 당 업그레이드

Azure CLI를 사용하여 구독에서 특정 클러스터를 업데이트하려면 다음 단계를 수행합니다.

1. Azure CLI를 사용하여 다음 명령을 실행합니다. AKS 클러스터에 대한 **AKS 개요** 페이지의 값을 사용하여 **subscriptionId**, **resourceGroupName** 및 **clusterName** 의 값을 편집합니다.  다음 예제에 표시된 것처럼, **clientIdOfSPN** 값을 가져오기 위해 명령을 실행하면 해당 값이 반환됩니다 `az aks show` .


    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```


    다음 예제에 표시된 것처럼, **clientIdOfSPNOrMsi** 값을 가져오기 위해, 명령 `az aks show`을 실행할 수 있습니다. **ServicePrincipalProfile** 개체에 유효한 *clientid* 값이 있으면 해당 값을 사용할 수 있습니다. 그렇지 않으면, *msi* 로 설정된 경우 `addonProfiles.omsagent.identity.clientId`에서 clientid를 전달해야 합니다.


    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```



>[!NOTE]
>사용자 계정을 사용 하 여 역할 할당을 수행 하려는 경우 아래 예제와 같이--담당자 매개 변수를 사용 합니다. 또는 SPN을 사용 하 여 로그인 하 고 역할 할당을 수행 하려는 경우--담당자 매개 변수 대신--담당자-id--principal-principal-type 매개 변수를 사용 합니다.

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Azure PowerShell를 사용하여 모든 클러스터 업그레이드

Azure PowerShell을 사용하여 구독에서 모든 클러스터를 업데이트하려면 다음 단계를 수행합니다.

1. **mdm_onboarding_atscale.ps1** 스크립트를 [다운로드](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1)하고, 이를 GitHub 리포지토리의 로컬 폴더에 저장합니다.
2. Azure PowerShell을 사용하여 다음 명령을 실행합니다.  AKS 클러스터에 대한 **AKS 개요** 페이지의 값을 사용하여 **subscriptionId** 값을 편집합니다.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Azure PowerShell을 사용하여 클러스터 당 업그레이드

Azure PowerShell을 사용하여 특정 클러스터를 업데이트하려면 다음 단계를 수행합니다.

1. **mdm_onboarding.ps1** 스크립트를 [다운로드](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1)하고, 이를 GitHub 리포지토리의 로컬 폴더에 저장합니다.

2. Azure PowerShell을 사용하여 다음 명령을 실행합니다. AKS 클러스터에 대한 **AKS 개요** 페이지의 값을 사용하여 **subscriptionId**, **resourceGroupName** 및 **clusterName** 의 값을 편집합니다.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>업데이트 확인

앞서 설명한 방법 중 하나를 사용하여 업데이트를 시작한 후, Azure Monitor 메트릭 탐색기를 사용하여 **인사이트** 가 열거된 **메트릭 네임스페이스** 에서 확인할 수 있습니다. 이 경우, 계속 진행하여 [메트릭 경고](../alerts/alerts-metric.md)를 설정하거나 [대시보드에](../../azure-portal/azure-portal-dashboards.md) 차트를 고정하는 것을 시작할 수 있습니다.  
