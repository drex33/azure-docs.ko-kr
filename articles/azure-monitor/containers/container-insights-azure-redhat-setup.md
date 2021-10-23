---
title: 컨테이너 인사이트로 Azure Red Hat OpenShift v3.x 구성 | Microsoft Docs
description: 이 문서에서는 Azure Red Hat OpenShift 버전 3 이상에서 호스트되는 Azure Monitor를 사용하여 Kubernetes 클러스터의 모니터링을 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4b0278a143366579915f3a4edf3d754025ca6f90
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262473"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>컨테이너 인사이트를 사용하여 Azure Red Hat OpenShift v3 구성

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11은 2022년 6월부터 사용이 중지됩니다.
>
> 2020년 10월부터 더 이상 새 3.11 클러스터를 만들 수 없습니다.
> 기존 3.11 클러스터는 2022년 6월까지 계속 작동하지만 해당 날짜 이후에는 더 이상 지원되지 않습니다.
>
> 이 가이드를 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](../../openshift/tutorial-create-cluster.md).
> 궁금한 점은 [Microsoft에 문의](mailto:aro-feedback@microsoft.com)하세요.

컨테이너 인사이트는 AKS(Azure Kubernetes Service) 및 AKS 엔진 클러스터에 대한 풍부한 모니터링 환경을 제공합니다. 이 문서에서는 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 버전 3 및 지원되는 최신 버전 3에서 호스트되는 Kubernetes 클러스터의 모니터링을 사용하도록 설정하여 비슷한 모니터링 환경을 구현하는 방법을 설명합니다.

>[!NOTE]
>Azure Red Hat OpenShift에 대한 지원은 현재 공개 미리 보기의 기능입니다.
>

컨테이너 인사이트는 다음과 같은 지원되는 방법을 사용하여 Azure Red Hat OpenShift의 새 배포 또는 하나 이상의 기존 배포에 사용할 수 있습니다.

- 기존 클러스터의 경우 Azure Portal 또는 Azure Resource Manager 템플릿 사용
- 새 클러스터의 경우 Azure Resource Manager 템플릿 사용 또는 새 클러스터를 만드는 동안 [Azure CLI](/cli/azure/openshift#az_openshift_create) 사용

## <a name="supported-and-unsupported-features"></a>지원되는/지원되지 않는 기능

컨테이너 인사이트는 다음 기능을 제외하고 [개요](container-insights-overview.md)에 설명된 대로 Azure Red Hat OpenShift 모니터링을 지원합니다.

- 라이브 데이터(미리 보기)
- 클러스터 노드 및 Pod에서 [메트릭 수집](container-insights-update-metrics.md) 및 Azure Monitor 메트릭 데이터베이스에 저장

## <a name="prerequisites"></a>필수 조건

- [Log Analytics 작업 영역](../logs/design-logs-deployment.md)

    컨테이너 인사이트는 Azure [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열된 지역에서 Log Analytics 작업 영역을 지원합니다. 사용자 고유의 작업 영역을 만들려면 [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../logs/quick-create-workspace.md)에서 만들 수 있습니다.

- 컨테이너 인사이트에서 기능을 사용하도록 설정하고 액세스하려면 최소한 Azure 구독에 Azure *기여자* 역할의 멤버여야 하고, 컨테이너 인사이트로 구성된 Log Analytics 작업 영역의 [*Log Analytics 기여자*](../logs/manage-access.md#manage-access-using-azure-permissions) 역할의 멤버여야 합니다.

- 모니터링 데이터를 보려면 컨테이너 인사이트를 사용하여 구성된 Log Analytics 작업 영역이 있는 [*Log Analytics 읽기 권한자*](../logs/manage-access.md#manage-access-using-azure-permissions) 역할 권한의 멤버여야 합니다.

## <a name="identify-your-log-analytics-workspace-id"></a>Log Analytics 작업 영역 ID 식별

 기존 Log Analytics 작업 영역과 통합하려면 먼저 Log Analytics 작업 영역의 전체 리소스 ID를 식별합니다. 작업 영역의 리소스 ID는 Azure Resource Manager 템플릿 메서드를 사용하여 모니터링을 사용하도록 설정하는 경우 `workspaceResourceId` 매개 변수에 필요합니다.

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 새 클러스터에 사용

모니터링이 사용하도록 설정된 Azure Red Hat OpenShift 클러스터를 배포하려면 다음 단계를 수행합니다. 계속하기 전에 [Azure Red Hat OpenShift 클러스터 만들기](../../openshift/tutorial-create-cluster.md) 자습서를 검토하여 환경이 올바르게 설정되도록 구성해야 하는 종속성을 이해합니다.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링이 사용하도록 설정된 클러스터를 배포하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- Azure Red Hat OpenShift 클러스터 리소스 ID

- 클러스터가 배포된 리소스 그룹

- 테넌트 ID를 만드는 단계를 수행하거나 이미 만들어진 후에 표시된 [Azure Active Directory 테넌트 ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant)

- 클라이언트 애플리케이션 ID를 만드는 단계를 수행하거나 이미 만들어진 후에 표시된 [Azure Active Directory 클라이언트 애플리케이션 ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration)

- 클라이언트 암호를 만드는 단계를 수행하거나 이미 만들어진 후에 표시된 [Azure Active Directory 클라이언트 암호](../../openshift/howto-aad-app-configuration.md#create-a-client-secret)

- 보안 그룹을 만드는 단계를 수행하거나 이미 만들어진 후에 표시된 [Azure AD 보안 그룹](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group)

- 기존 Log Analytics 작업 영역의 리소스 ID 이 정보를 가져오는 방법에 대해 알아보려면 [Log Analytics 작업 영역 ID 식별](#identify-your-log-analytics-workspace-id)을 참조하세요.

- 클러스터에 만들 노드의 수

- 에이전트 풀 프로필의 컴퓨팅 노드 수

- 에이전트 풀 프로필의 인프라 노드 수입니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.65 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 다음 명령을 사용하여 모니터링 추가 기능으로 클러스터를 만들려면 Azure Resource Manager 템플릿 및 매개 변수 파일을 다운로드하여 로컬 폴더에 저장합니다.

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Azure에 로그인

    ```azurecli
    az login
    ```

    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

3. 클러스터에 대한 리소스 그룹이 아직 없는 경우 새로 만듭니다. Azure에서 OpenShift를 지원하는 Azure 지역 목록은 [지원되는 지역](../../openshift/supported-resources.md#azure-regions)을 참조하세요.

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. **newClusterWithMonitoringParam.json** JSON 매개 변수 파일을 편집하고 다음 값을 업데이트합니다.

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. 다음 단계에서는 Azure CLI를 사용하여 모니터링을 사용하도록 설정된 클러스터를 배포합니다.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    출력은 다음과 유사합니다.

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>기존 클러스터에 대해 활성화

Azure에 배포된 Azure Red Hat OpenShift 클러스터의 모니터링을 사용하도록 설정하려면 다음 단계를 수행합니다. Azure Portal에서 또는 제공된 템플릿을 사용하여 이를 수행할 수 있습니다.

### <a name="from-the-azure-portal"></a>Azure Portal에서

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal 메뉴 또는 홈페이지에서 **Azure Monitor** 를 선택합니다. **인사이트** 섹션에서 **컨테이너** 를 선택합니다.

3. **모니터 - 컨테이너** 페이지에서 **모니터링되지 않는 클러스터** 를 선택합니다.

4. 모니터링되지 않는 클러스터 목록에서 이 클러스터를 찾아 **사용** 을 클릭합니다. **클러스터 유형** 열 아래의 **ARO** 값을 찾아 목록에서 결과를 식별할 수 있습니다.

5. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너 인사이트에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    구독에서 클러스터가 배포된 기본 작업 영역 및 위치가 미리 선택됩니다.

    ![모니터링되지 않은 클러스터에 대해 모니터링 사용](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../logs/quick-create-workspace.md)를 참조하세요. RedHat OpenShift 클러스터가 배포된 구독과 동일한 구독에 작업 영역을 만들어야 합니다.

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

### <a name="enable-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 활성화

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- Azure RedHat OpenShift 클러스터 리소스 ID

- 클러스터가 배포된 리소스 그룹

- Log Analytics 작업 영역. 이 정보를 가져오는 방법에 대해 알아보려면 [Log Analytics 작업 영역 ID 식별](#identify-your-log-analytics-workspace-id)을 참조하세요.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.65 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 다음 명령을 사용하여 모니터링 추가 기능으로 클러스터를 업데이트하려면 템플릿 및 매개 변수 파일을 다운로드합니다.

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Azure에 로그인

    ```azurecli
    az login
    ```

    여러 구독에 액세스할 수 있으면 `az account set -s {subscription ID}`에서 `{subscription ID}`를 사용하려는 구독으로 바꿔서 실행합니다.

3. Azure RedHat OpenShift 클러스터의 구독을 지정합니다.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. 다음 명령을 실행하여 클러스터 위치 및 리소스 ID를 식별합니다.

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. **existingClusterParam.json** JSON 매개 변수 파일을 편집하고 *aroResourceId* 및 *aroResourceLocation* 값을 업데이트합니다. **workspaceResourceId** 값은 Log Analytics 작업 영역의 전체 리소스 ID 이며, 작업 영역 이름을 포함합니다.

6. Azure CLI를 사용하여 배포하려면 다음 명령을 실행합니다.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    출력은 다음과 유사합니다.

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용하여 RedHat OpenShift 클러스터의 상태 및 리소스 사용률을 수집하고 여기에서 실행되는 작업을 수집합니다. 컨테이너 인사이트 [사용 방법](container-insights-analyze.md)을 알아보세요.

- 기본적으로 컨테이너화된 에이전트는 kube 시스템을 제외한 모든 네임스페이스에서 실행 중인 모든 컨테이너의 stdout/stderr 컨테이너 로그를 수집합니다. 특정 네임스페이스 또는 네임스페이스와 관련된 컨테이너 로그 컬렉션을 구성하려면 [컨테이너 인사이트 에이전트 구성](container-insights-agent-config.md)을 검토하여 ConfigMap 구성 파일에 대해 원하는 데이터 수집 설정을 구성합니다.

- 클러스터에서 Prometheus 메트릭을 스크랩하고 분석하려면 [Prometheus 메트릭 스크래핑 구성](container-insights-prometheus-integration.md)을 검토합니다.

- 컨테이너 인사이트를 사용하여 클러스터 모니터링을 중지하는 방법을 알아보려면 [Azure Red Hat OpenShift 클러스터 모니터링을 중지하는 방법](./container-insights-optout-openshift-v3.md)을 참조하세요.