---
title: App Service, 함수, 논리 앱용으로 Azure Arc 설정
description: Azure Arc 지원 Kubernetes 클러스터의 경우 App Service 앱, 함수 앱 및 논리 앱을 사용하도록 설정하는 방법을 알아봅니다.
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: a330d68ed556a60261ca91e6bfb32fdddf52dc14
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435589"
---
# <a name="set-up-an-azure-arc-enabled-kubernetes-cluster-to-run-app-service-functions-and-logic-apps-preview"></a>Azure Arc 지원 Kubernetes 클러스터를 설정하여 App Service, Functions 및 Logic Apps 실행(미리 보기)

Azure Arc 지원 [Kubernetes 클러스터가](../azure-arc/kubernetes/overview.md)있는 경우 이 클러스터를 사용하여 [App Service 사용하도록 설정된 사용자 지정 위치를](overview-arc-integration.md) 만들고 웹앱, 함수 앱 및 논리 앱을 배포할 수 있습니다.

Azure Arc 지원 Kubernetes를 사용하면 온-프레미스 또는 클라우드 Kubernetes 클러스터를 Azure의 App Service, Functions 및 Logic Apps 표시할 수 있습니다. 다른 Azure 지역처럼 앱을 만들고 배포할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 계정이 없다면 무료 계정에 [지금 바로 가입](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)하세요.

<!-- ## Prerequisites

- Create a Kubernetes cluster in a supported Kubernetes distribution and connect it to Azure Arc in a supported region. See [Public preview limitations](overview-arc-integration.md#public-preview-limitations).
- [Install Azure CLI](/cli/azure/install-azure-cli), or use the [Azure Cloud Shell](../cloud-shell/overview.md).
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/). It's also preinstalled in the Azure Cloud Shell.

## Obtain cluster information

Set the following environment variables based on your Kubernetes cluster deployment:

```bash
aksClusterGroupName="<name-resource-group-with-aks-cluster>"
groupName="<name-of-resource-group-with-the-arc-connected-cluster>"
clusterName="<name-of-arc-connected-cluster>"
geomasterLocation="TODO: Why so many different locations for different resources? Shouldn't we just say create everything in the connected cluster's resource group and location?"
``` -->

## <a name="add-azure-cli-extensions"></a>Azure CLI 확장 추가

[Azure Cloud Shell](../cloud-shell/quickstart.md)에서 Bash 환경을 시작합니다.

[![새 창에서 Cloud Shell 시작](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

이러한 CLI 명령은 아직 핵심 CLI 집합의 일부가 아니므로 다음 명령을 통해 추가합니다.

```azurecli-interactive
az extension add --upgrade --yes --name connectedk8s
az extension add --upgrade --yes --name k8s-extension
az extension add --upgrade --yes --name customlocation
az provider register --namespace Microsoft.ExtendedLocation --wait
az provider register --namespace Microsoft.Web --wait
az provider register --namespace Microsoft.KubernetesConfiguration --wait
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```

## <a name="create-a-connected-cluster"></a>연결된 클러스터 만들기

> [!NOTE]
> 이 자습서에서는 [AKS(Azure Kubernetes Service)](../aks/index.yml)를 사용하여 처음부터 환경을 설정하기 위한 구체적인 지침을 제공합니다. 그러나 프로덕션 워크로드의 경우 Azure에서 이미 관리되는 Azure Arc를 AKS 클러스터에서 사용하도록 설정하지 않는 것이 좋습니다. 아래 단계는 서비스를 이해하는 데 도움이 되지만 프로덕션 배포의 경우 조치가 아닌 설명으로 간주되어야 합니다. Azure Arc 지원 Kubernetes 클러스터 만들기에 대한 일반적인 [지침은 빠른 시작: 기존 Kubernetes 클러스터](../azure-arc/kubernetes/quickstart-connect-cluster.md) 커넥트 Azure Arc 참조하세요.

1. 공용 IP 주소를 사용하여 Azure Kubernetes Service에서 클러스터를 만듭니다. `<group-name>`을 원하는 리소스 그룹 이름으로 바꿉니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    infra_rg=$(az aks show --resource-group $aksClusterGroupName --name $aksName --output tsv --query nodeResourceGroup)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $aksClusterGroupName="<group-name>" # Name of resource group for the AKS cluster
    $aksName="${aksClusterGroupName}-aks" # Name of the AKS cluster
    $resourceLocation="eastus" # "eastus" or "westeurope"

    az group create -g $aksClusterGroupName -l $resourceLocation
    az aks create --resource-group $aksClusterGroupName --name $aksName --enable-aad --generate-ssh-keys
    ```

    ---
    
2. [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) 파일을 가져온 다음 클러스터에 대한 연결을 테스트합니다. 기본적으로 kubeconfig 파일은 `~/.kube/config`에 저장됩니다.

    ```azurecli-interactive
    az aks get-credentials --resource-group $aksClusterGroupName --name $aksName --admin
    
    kubectl get ns
    ```
    
3. Azure Arc 리소스를 포함할 리소스 그룹을 만듭니다. `<group-name>`을 원하는 리소스 그룹 이름으로 바꿉니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $groupName="<group-name>" # Name of resource group for the connected cluster

    az group create -g $groupName -l $resourceLocation
    ```

    ---
    
4. 생성한 클러스터를 Azure Arc에 연결합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)


    ```powershell
    $clusterName="${groupName}-cluster" # Name of the connected cluster resource

    az connectedk8s connect --resource-group $groupName --name $clusterName
    ```

    ---
    
5. 다음 명령을 사용하여 연결의 유효성을 검사합니다. `provisioningState` 속성이 `Succeeded`로 표시되어야 합니다. 그렇지 않은 경우 1분 후에 명령을 다시 실행합니다.

    ```azurecli-interactive
    az connectedk8s show --resource-group $groupName --name $clusterName
    ```
    
## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

Log [Analytic 작업 영역은](../azure-monitor/logs/quick-create-workspace.md) Azure Arc App Service 실행할 필요는 없지만, 개발자가 Azure Arc 지원 Kubernetes 클러스터에서 실행 중인 앱에 대한 애플리케이션 로그를 얻을 수 있는 방법입니다. 

1. 간단한 설명을 위해 지금 작업 영역을 만듭니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    workspaceName="$groupName-workspace" # Name of the Log Analytics workspace
    
    az monitor log-analytics workspace create \
        --resource-group $groupName \
        --workspace-name $workspaceName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $workspaceName="$groupName-workspace"

    az monitor log-analytics workspace create `
        --resource-group $groupName `
        --workspace-name $workspaceName
    ```

    ---
    
2. 다음 명령을 실행하여 기존 Log Analytics 작업 영역에 대한 인코딩된 작업 영역 ID와 공유 키를 가져옵니다. 두 항목은 다음 단계에서 필요합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query customerId \
        --output tsv)
    logAnalyticsWorkspaceIdEnc=$(printf %s $logAnalyticsWorkspaceId | base64 -w0) # Needed for the next step
    logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys \
        --resource-group $groupName \
        --workspace-name $workspaceName \
        --query primarySharedKey \
        --output tsv)
    logAnalyticsKeyEnc=$(printf %s $logAnalyticsKey | base64 -w0) # Needed for the next step
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $logAnalyticsWorkspaceId=$(az monitor log-analytics workspace show `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query customerId `
        --output tsv)
    $logAnalyticsWorkspaceIdEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsWorkspaceId))# Needed for the next step
    $logAnalyticsKey=$(az monitor log-analytics workspace get-shared-keys `
        --resource-group $groupName `
        --workspace-name $workspaceName `
        --query primarySharedKey `
        --output tsv)
    $logAnalyticsKeyEnc=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($logAnalyticsKey))
    ```
    
    ---

## <a name="install-the-app-service-extension"></a>App Service 확장 설치

1. [App Service 확장](overview-arc-integration.md)의 원하는 이름, 리소스를 프로비전해야 하는 클러스터 네임스페이스 및 App Service Kubernetes 환경의 이름에 대해 다음 환경 변수를 설정합니다. `<kube-environment-name>`의 고유한 이름을 선택합니다. App Service Kubernetes 환경에서 만든 앱의 도메인 이름에 포함되기 때문입니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    extensionName="appservice-ext" # Name of the App Service extension
    namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionName="appservice-ext" # Name of the App Service extension
    $namespace="appservice-ns" # Namespace in your cluster to install the extension and provision resources
    $kubeEnvironmentName="<kube-environment-name>" # Name of the App Service Kubernetes environment resource
    ```

    ---
    
2. Log Analytics를 사용하도록 설정된 Azure Arc 연결된 클러스터에 App Service 확장을 설치합니다. 이번에도 Log Analytics가 필요하지는 않지만 나중에는 확장에 추가할 수 없으니 지금 하는 것이 더 편합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az k8s-extension create \
        --resource-group $groupName \
        --name $extensionName \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --extension-type 'Microsoft.Web.Appservice' \
        --release-train stable \
        --auto-upgrade-minor-version true \
        --scope cluster \
        --release-namespace $namespace \
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" \
        --configuration-settings "appsNamespace=${namespace}" \
        --configuration-settings "clusterName=${kubeEnvironmentName}" \
        --configuration-settings "keda.enabled=true" \
        --configuration-settings "buildService.storageClassName=default" \
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" \
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" \
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" \
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" \
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    az k8s-extension create `
        --resource-group $groupName `
        --name $extensionName `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --extension-type 'Microsoft.Web.Appservice' `
        --release-train stable `
        --auto-upgrade-minor-version true `
        --scope cluster `
        --release-namespace $namespace `
        --configuration-settings "Microsoft.CustomLocation.ServiceAccount=default" `
        --configuration-settings "appsNamespace=${namespace}" `
        --configuration-settings "clusterName=${kubeEnvironmentName}" `
        --configuration-settings "keda.enabled=true" `
        --configuration-settings "buildService.storageClassName=default" `
        --configuration-settings "buildService.storageAccessMode=ReadWriteOnce" `
        --configuration-settings "customConfigMap=${namespace}/kube-environment-config" `
        --configuration-settings "envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group=${aksClusterGroupName}" `
        --configuration-settings "logProcessor.appLogs.destination=log-analytics" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.customerId=${logAnalyticsWorkspaceIdEnc}" `
        --configuration-protected-settings "logProcessor.appLogs.logAnalyticsConfig.sharedKey=${logAnalyticsKeyEnc}"
    ```

    ---

    > [!NOTE]
    > Log Analytics를 통합하지 않고 확장을 설치하려면 명령에서 마지막 세 개의 `--configuration-settings` 매개 변수를 제거하세요.
    >

    다음 표에서는 명령을 실행할 때의 다양한 `--configuration-settings` 매개 변수를 설명합니다.
    
    | 매개 변수 | 설명 |
    | - | - |
    | `Microsoft.CustomLocation.ServiceAccount` | 생성할 사용자 지정 위치에 대해 만들어야 하는 서비스 계정입니다. `default` 값으로 설정하는 것이 좋습니다. |
    | `appsNamespace` | 앱 정의 및 Pod를 프로비전하는 네임스페이스입니다. 확장 릴리스 네임스페이스의 해당 네임스페이스와 **일치해야 합니다.** |
    | `clusterName` | 이 확장에 대해 생성되는 App Service Kubernetes 환경의 이름입니다. |
    | `keda.enabled` | [KEDA](https://keda.sh/)를 Kubernetes 클러스터에 설치해야 하는지 여부입니다. `true` 또는 `false`를 수락합니다. |
    | `buildService.storageClassName` | 빌드 아티팩트를 저장하는 빌드 서비스를 위한 [스토리지 클래스의 이름](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class)입니다. `default` 같은 값은 `default`라는 클래스를 지정하며 [기본으로 표시되는 클래스](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)는 지정하지 않습니다.  기본값은 AKS 및 AKS HCI에 유효한 스토리지 클래스이지만 다른 배포판/플랫폼에는 사용할 수 없습니다. |
    | `buildService.storageAccessMode` | 위에 나오는 이름의 스토리지 클래스와 함께 사용할 [액세스 모드](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)입니다. `ReadWriteOnce` 또는 `ReadWriteMany`를 수락합니다. |
    | `customConfigMap` | App Service Kubernetes 환경에서 설정할 구성 맵의 이름입니다. 현재는 `<namespace>/kube-environment-config`여야 하며 `<namespace>`를 위의 `appsNamespace`로 바꿔야 합니다. |
    | `envoy.annotations.service.beta.kubernetes.io/azure-load-balancer-resource-group` | Azure Kubernetes Service 클러스터가 있는 리소스 그룹의 이름입니다. 기본 클러스터가 Azure Kubernetes Service인 경우에만 유효 하며 필수 요소입니다.  |
    | `logProcessor.appLogs.destination` | 선택 사항입니다. 또는 를 수락하고 `log-analytics` `none` 없음을 선택하면 플랫폼 로그가 비활성화됩니다. |
    | `logProcessor.appLogs.logAnalyticsConfig.customerId` | `logProcessor.appLogs.destination`이 `log-analytics`로 설정된 경우에만 필요합니다. base64로 인코딩된 Log Analytics 작업 영역 ID입니다. 이 매개 변수는 보호 설정으로 구성해야 합니다. |
    | `logProcessor.appLogs.logAnalyticsConfig.sharedKey` | `logProcessor.appLogs.destination`이 `log-analytics`로 설정된 경우에만 필요합니다. base64로 인코딩된 Log Analytics 작업 영역 공유 키입니다. 이 매개 변수는 보호 설정으로 구성해야 합니다. |
    | | |
        
3. 나중을 위해 App Service 확장의 `id` 속성을 저장합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    extensionId=$(az k8s-extension show \
        --cluster-type connectedClusters \
        --cluster-name $clusterName \
        --resource-group $groupName \
        --name $extensionName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $extensionId=$(az k8s-extension show `
        --cluster-type connectedClusters `
        --cluster-name $clusterName `
        --resource-group $groupName `
        --name $extensionName `
        --query id `
        --output tsv)
    ```

    ---

4. 확장이 완전히 설치될 때까지 기다렸다가 계속 진행합니다. 다음 명령을 실행하여 설치가 완료될 때까지 터미널 세션이 대기하게 할 수 있습니다.

    ```azurecli-interactive
    az resource wait --ids $extensionId --custom "properties.installState!='Pending'" --api-version "2020-07-01-preview"
    ```

`kubectl`을 사용하면 Kubernetes 클러스터에서 만든 Pod를 볼 수 있습니다.

```bash
kubectl get pods -n $namespace
```

[App Service 확장에서 만든 Pod](overview-arc-integration.md#pods-created-by-the-app-service-extension)에서 이러한 Pod와 관련 역할을 자세히 확인할 수 있습니다.

## <a name="create-a-custom-location"></a>사용자 지정 위치 만들기

Azure에서 [사용자 지정 위치](../azure-arc/kubernetes/custom-locations.md)는 App Service Kubernetes 환경을 할당하는 데 사용합니다.

<!-- https://github.com/MicrosoftDocs/azure-docs-pr/pull/156618 -->

1. 사용자 지정 위치의 원하는 이름 및 Azure Arc 연결된 클러스터의 ID에 대해 다음 환경 변수를 설정합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    customLocationName="my-custom-location" # Name of the custom location
    
    connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    $customLocationName="my-custom-location" # Name of the custom location
    
    $connectedClusterId=$(az connectedk8s show --resource-group $groupName --name $clusterName --query id --output tsv)
    ```

    ---
    
2. 사용자 지정 위치 만들기:

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az customlocation create \
        --resource-group $groupName \
        --name $customLocationName \
        --host-resource-id $connectedClusterId \
        --namespace $namespace \
        --cluster-extension-ids $extensionId
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az customlocation create `
        --resource-group $groupName `
        --name $customLocationName `
        --host-resource-id $connectedClusterId `
        --namespace $namespace `
        --cluster-extension-ids $extensionId
    ```

    ---
    
    <!-- --kubeconfig ~/.kube/config # needed for non-Azure -->

3. 다음 명령을 이용하면 사용자 지정 위치가 성공적으로 생성되었는지 확인할 수 있습니다. 출력에서 `provisioningState` 속성이 `Succeeded`로 표시되어야 합니다. 그렇지 않은 경우 1분 후에 다시 실행합니다.

    ```azurecli-interactive
    az customlocation show --resource-group $groupName --name $customLocationName
    ```
    
4. 다음 단계를 위해 사용자 지정 위치 ID를 저장합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    customLocationId=$(az customlocation show \
        --resource-group $groupName \
        --name $customLocationName \
        --query id \
        --output tsv)
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    $customLocationId=$(az customlocation show `
        --resource-group $groupName `
        --name $customLocationName `
        --query id `
        --output tsv)
    ```

    ---
    
## <a name="create-the-app-service-kubernetes-environment"></a>App Service Kubernetes 환경 만들기

사용자 지정 위치에서 앱 만들기를 시작하기 전에 [App Service Kubernetes 환경](overview-arc-integration.md#app-service-kubernetes-environment)을 마련해야 합니다.

1. App Service Kubernetes 환경 만들기:
    
    # <a name="bash"></a>[bash](#tab/bash)

    ```azurecli-interactive
    az appservice kube create \
        --resource-group $groupName \
        --name $kubeEnvironmentName \
        --custom-location $customLocationId \
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```azurecli-interactive
    az appservice kube create `
        --resource-group $groupName `
        --name $kubeEnvironmentName `
        --custom-location $customLocationId `      
    ```

    ---
    
2. 다음 명령을 사용하여 App Service Kubernetes 환경이 성공적으로 생성되었는지 확인합니다. 출력에서 `provisioningState` 속성이 `Succeeded`로 표시되어야 합니다. 그렇지 않은 경우 1분 후에 다시 실행합니다.

    ```azurecli-interactive
    az appservice kube show --resource-group $groupName --name $kubeEnvironmentName
    ```
    

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Arc에서 웹 앱 만들기](quickstart-arc.md)
- [Azure Arc에서 첫 번째 함수 만들기](../azure-functions/create-first-function-arc-cli.md)
- [Azure Arc에서 첫 번째 논리 앱 만들기](../logic-apps/azure-arc-enabled-logic-apps-create-deploy-workflows.md)
