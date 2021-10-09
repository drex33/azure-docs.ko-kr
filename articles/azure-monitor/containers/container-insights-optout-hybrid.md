---
title: 하이브리드 Kubernetes 클러스터 모니터링을 중지하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트를 사용하여 하이브리드 Kubernetes 클러스터의 모니터링을 중지할 수 있는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b8f548b8e9440804ae6a7ff293c35e7107cfc4c8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708930"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>하이브리드 클러스터 모니터링 중지 방법

Kubernetes 클러스터에 대한 모니터링을 사용하도록 설정한 후 더 이상 모니터링하지 않기로 결정한 경우 컨테이너 인사이트를 사용하여 클러스터 모니터링을 중지할 수 있습니다. 이 문서에서는 다음 환경에 대해 이 작업을 수행하는 방법을 보여줍니다.

- Azure 및 Azure Stack의 AKS 엔진
- OpenShift 버전 4 이상
- Azure Arc 사용 Kubernetes (미리 보기)

## <a name="how-to-stop-monitoring-using-helm"></a>Helm을 사용하여 모니터링을 중지하는 방법

다음 단계는 다음 환경에 적용됩니다.

- Azure 및 Azure Stack의 AKS 엔진
- OpenShift 버전 4 이상

1. 클러스터에 설치된 컨테이너 인사이트 helm 차트 릴리스를 먼저 식별하려면 다음 helm 명령을 실행합니다.

    ```
    helm list
    ```

    출력은 다음과 유사합니다.

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* 는 컨테이너 인사이트에 대한 helm 차트 릴리스를 나타냅니다.

2. 차트 릴리스를 삭제하려면 다음 helm 명령을 실행합니다.

    `helm delete <releaseName>`

    예제:

    `helm delete azmon-containers-release-1`

    이렇게 하면 클러스터에서 릴리스가 제거됩니다. `helm list` 명령을 실행하여 확인할 수 있습니다.

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. Helm은 릴리스를 삭제한 후에도 해당 릴리스를 추적하므로 클러스터의 기록을 감사할 수 있으며, `helm rollback`으로 릴리스를 삭제 취소할 수도 있습니다.

## <a name="how-to-stop-monitoring-on-azure-arc-enabled-kubernetes"></a>Azure Arc 사용 Kubernetes에서 모니터링을 중지 하는 방법

### <a name="using-powershell"></a>PowerShell 사용

1. 다음 명령을 사용하여 모니터링 추가 항목으로 클러스터를 구성하는 로컬 폴더에 스크립트를 다운로드하고 저장합니다.

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. `$azureArcClusterResourceId` 변수는 Azure Arc 사용 Kubernetes 클러스터 리소스의 리소스 ID를 나타내는 `subscriptionId`, `resourceGroupName` 및 `clusterName`에 해당하는 값을 설정하여 구성합니다.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubectl config get-contexts` 명령을 실행하여 클러스터의 **kube-context** 로 `$kubeContext` 변수를 구성합니다. 현재 컨텍스트를 사용하려면 값을 `""`로 설정합니다.

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 다음 명령을 실행하여 클러스터 모니터링을 중단합니다.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>서비스 주체 사용
*disable-monitoring.ps1* 스크립트는 대화형 디바이스 로그인을 사용합니다. 비 대화형 로그인을 선호하는 경우 [사전 요구 사항](container-insights-enable-arc-enabled-clusters.md#prerequisites)에 설명된 대로 기존 서비스 주체를 사용하거나 필요한 권한이 있는 새 서비스 주체를 생성할 수 있습니다. 서비스 주체를 사용하려면 enable-monitoring.ps1 스크립트에 사용하려는 서비스 주체의 값을 $servicePrincipalClientId, $servicePrincipalClientSecret 및 $tenantId 매개 변수를 전달해야 합니다.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc-connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

예를 들면 다음과 같습니다.

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>bash 사용

1. 다음 명령을 사용하여 모니터링 추가 항목으로 클러스터를 구성하는 로컬 폴더에 스크립트를 다운로드하고 저장합니다.

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. `azureArcClusterResourceId` 변수는 Azure Arc 사용 Kubernetes 클러스터 리소스의 리소스 ID를 나타내는 `subscriptionId`, `resourceGroupName` 및 `clusterName`에 해당하는 값을 설정하여 구성합니다.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubectl config get-contexts` 명령을 실행하여 클러스터의 **kube-context** 로 `kubeContext` 변수를 구성합니다.

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 클러스터 모니터링을 중지하려면 배포 시나리오에 따라 여러 가지 명령이 제공됩니다.

    다음 명령을 실행하여 현재 컨텍스트를 사용하는 클러스터의 모니터링을 중지합니다.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    다음 명령을 실행하여 컨텍스트를 지정하여 클러스터의 모니터링을 중지합니다.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>서비스 주체 사용
*disable-monitoring.sh* bash 스크립트는 대화형 디바이스 로그인을 사용합니다. 비 대화형 로그인을 선호하는 경우 [사전 요구 사항](container-insights-enable-arc-enabled-clusters.md#prerequisites)에 설명된 대로 기존 서비스 주체를 사용하거나 필요한 권한이 있는 새 서비스 주체를 생성할 수 있습니다. 서비스 주체를 사용하려면 사용하려는 서비스 주체의 --client-id, --client-secret 및 --tenant-id 값을 *enable-monitoring.sh* bash 스크립트에 전달해야 합니다.

```bash
subscriptionId="<subscription Id of the Azure Arc-connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

예를 들면 다음과 같습니다.

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>다음 단계

클러스터 모니터링을 지원하기 위해 Log Analytics 작업 영역을 생성했지만 더 이상 필요하지 않은 경우에는 수동으로 삭제해야 합니다. 작업 영역을 삭제하는 방법을 모를 경우 [Azure Log Analytics 작업 영역 삭제](../logs/delete-workspace.md)를 참조하세요.
