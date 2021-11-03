---
title: AKS (Azure Kubernetes Service 용 클러스터 확장) (미리 보기)
description: Azure Kubernetes 서비스 (AKS)에서 확장의 수명 주기를 배포 하 고 관리 하는 방법을 알아봅니다.
ms.service: container-service
ms.date: 10/13/2021
ms.topic: article
author: nickomang
ms.author: nickoman
ms.openlocfilehash: d9ef2efbd4b77f70bb43830e59b7dee0ae8ad26a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102935"
---
# <a name="deploy-and-manage-cluster-extensions-for-azure-kubernetes-service-aks-preview"></a>AKS (Azure Kubernetes Service)에 대 한 클러스터 확장 배포 및 관리 (미리 보기)

클러스터 확장은 AKS 클러스터의 Azure Machine Learning (ML)와 같은 서비스의 설치 및 수명 주기 관리를 위한 Azure Resource Manager 기반 환경을 제공 합니다. 이 기능을 통해 다음을 수행할 수 있습니다.

* AKS 클러스터에 걸친 규모의 배포를 포함 하 여 확장의 Azure Resource Manager 기반 배포.
* Azure Resource Manager에서 확장 (업데이트, 삭제)의 수명 주기 관리

이 문서에서는 다음에 대해 알아봅니다.
> [!div class="checklist"]

> * 확장 인스턴스를 만드는 방법
> * AKS에서 사용 가능한 클러스터 확장.
> * 확장 인스턴스를 보고, 나열하고, 업데이트하고, 삭제하는 방법.

이 기능에 대 한 개념적인 개요는 [클러스터 확장-Azure Arc 사용 Kubernetes][arc-k8s-extensions] 문서에서 사용할 수 있습니다.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI](/cli/azure/install-azure-cli) 버전 >= 2.16.0가 설치 되어 있습니다.

### <a name="register-provider-for-cluster-extensions"></a>클러스터 확장에 대 한 공급자 등록

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 다음 명령을 입력합니다.

    ```azurecli-interactive
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ContainerService
    ```

2. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.

    ```azurecli-interactive
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ContainerService -o table
    ```

    등록되면 이러한 네임스페이스의 `RegistrationState` 상태가 `Registered`로 변경되는 것을 확인해야 합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 다음 명령을 입력합니다.

    ```azurepowershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

1. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.

    ```azurepowershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
    Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerService
    ```

    등록되면 이러한 네임스페이스의 `RegistrationState` 상태가 `Registered`로 변경되는 것을 확인해야 합니다.

---

### <a name="register-the-aks-extensionmanager-preview-features"></a>`AKS-ExtensionManager`미리 보기 기능 등록

클러스터 확장을 사용할 수 있는 AKS 클러스터를 만들려면 `AKS-ExtensionManager` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다.

`AKS-ExtensionManager`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *KubernetesConfiguration* 및 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>클러스터 확장에 대 한 Azure CLI 확장 설정

> [!NOTE]
> Azure CLI 확장에 대해 지원 되는 최소 버전 `k8s-extension` 은 `1.0.0` 입니다. 설치한 버전을 모를 경우를 실행 `az extension show --name k8s-extension` 하 고 필드를 찾습니다 `version` .

Azure CLI 확장도 필요 합니다 `k8s-extension` . 다음 명령을 실행 하 여이를 설치 합니다.
  
```azurecli-interactive
az extension add --name k8s-extension
```

`k8s-extension` 확장이 이미 설치되어 있는 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="currently-available-extensions"></a>현재 사용할 수 있는 확장

>[!NOTE]
> 클러스터 확장은 AKS 클러스터에서 다양 한 확장을 설치 하 고 관리할 수 있는 플랫폼을 제공 합니다. 이러한 확장을 사용 하는 동안 문제가 발생 하는 경우 각 서비스를 사용 하 여 지원 티켓을 여세요.

<!--
| Extension | Description |
| --------- | ----------- |
-->

현재는 확장을 사용할 수 없습니다.

## <a name="supported-regions-and-kubernetes-versions"></a>지원 되는 지역 및 Kubernetes 버전

클러스터 확장은 [Azure Arc Enabled Kubernetes 지역 지원][arc-k8s-regions]에 나열 된 지역의 AKS 클러스터에서 사용할 수 있습니다.

지원 되는 Kubernetes 버전은 각 확장에 대 한 해당 설명서를 참조 하세요.

## <a name="usage-of-cluster-extensions"></a>클러스터 확장 사용

> [!NOTE]
> 이 문서에서 제공 하는 샘플은 완전 하지 않으며 기능을 소개 하기 위한 것입니다. 명령 및 해당 매개 변수의 포괄적인 목록은 [az k8s-EXTENSION CLI reference][k8s-extension-reference]를 참조 하세요.

### <a name="create-extensions-instance"></a>확장 인스턴스 만들기

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension create`를 사용하여 새 확장 인스턴스를 만듭니다. 아래 명령은 AKS 클러스터에 Azure Machine Learning 확장 인스턴스를 만듭니다.

```azurecli
az k8s-extension create --name aml-compute --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters --configuration-settings enableInference=True allowInsecureConnections=True
```

> [!NOTE]
> 클러스터 확장 서비스가 48 시간 넘게 중요 한 정보를 유지할 수 없습니다. 클러스터 확장 에이전트가 48 시간 이상 네트워크에 연결 되어 있지 않고 클러스터에 확장을 만들지 여부를 결정할 수 없는 경우에는 확장이 `Failed` 상태로 전환 됩니다. 상태가 된 후에는를 `Failed` 다시 실행 하 여 `k8s-extension create` 새 확장 인스턴스를 만들어야 합니다.

#### <a name="required-parameters"></a>필수 매개 변수

| 매개 변수 이름 | 설명 |
|----------------|------------|
| `--name` | 확장의 이름입니다. |
| `--extension-type` | 클러스터에 설치하려는 확장의 유형입니다. 예: Kubernetes | 
| `--cluster-name` | 확장 인스턴스를 만들어야 하는 AKS 클러스터의 이름입니다. |
| `--resource-group` | AKS 클러스터를 포함하는 리소스 그룹 |
| `--cluster-type` | 확장 인스턴스를 만들어야 하는 클러스터 유형입니다. `managedClusters`AKS 클러스터에 매핑될 때 지정 합니다.|

#### <a name="optional-parameters"></a>선택적 매개 변수

| 매개 변수 이름 | 설명 |
|--------------|------------|
| `--auto-upgrade-minor-version` | 확장 부 버전이 자동으로 업그레이드될지 여부를 지정하는 부울 속성입니다. 기본값: `true`.  이 매개 변수를 true로 설정하면 버전이 동적으로 업데이트되므로 `version` 매개 변수를 설정할 수 없습니다. `false`로 설정된 경우 확장은 패치 버전에 대해서도 자동으로 업그레이드되지 않습니다. |
| `--version` | 설치할 확장의 버전(확장 인스턴스를 고정할 특정 버전)입니다. auto-upgrade-minor-version이 `true`로 설정된 경우에는 제공되지 않아야 합니다. |
| `--configuration-settings` | 기능을 제어하기 위해 확장에 전달할 수 있는 설정입니다. 매개 변수 이름 뒤에 공백으로 구분된 `key=value` 쌍으로 전달됩니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-settings-file`을 사용할 수 없습니다. |
| `--configuration-settings-file` | 구성 설정을 확장에 전달하는 데 사용되는 키 값 쌍이 포함된 JSON 파일의 경로입니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-settings`를 사용할 수 없습니다. |
| `--configuration-protected-settings` | 이러한 설정은 `GET` API 호출 또는 `az k8s-extension show` 명령을 사용하여 검색할 수 없으므로 중요한 설정을 전달하는 데 사용됩니다. 매개 변수 이름 뒤에 공백으로 구분된 `key=value` 쌍으로 전달됩니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-protected-settings-file`을 사용할 수 없습니다. |
| `--configuration-protected-settings-file` | 중요한 설정을 확장에 전달하는 데 사용되는 키 값 쌍이 포함된 JSON 파일의 경로입니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-protected-settings`를 사용할 수 없습니다. |
| `--scope` | `cluster` 또는 `namespace` 확장에 대한 설치 범위 |
| `--release-namespace` | 이 매개 변수는 릴리스가 생성될 네임스페이스를 나타냅니다. 이 매개 변수는 `scope` 매개 변수가 `cluster`로 설정되어 있는 경우에만 적용됩니다. |
| `--release-train` |  확장 작성자는 `Stable`, `Preview` 등의 다양한 릴리스 학습으로 버전을 게시할 수 있습니다. 이 매개 변수를 명시적으로 설정하지 않으면 `Stable`이 기본값으로 사용됩니다. `autoUpgradeMinorVersion` 매개 변수가 `false`로 설정된 경우에는 이 매개 변수를 사용할 수 없습니다. |
| `--target-namespace` | 이 매개 변수는 릴리스가 생성되는 네임스페이스를 나타냅니다. 이 확장 인스턴스에 대해 만든 시스템 계정의 사용 권한은 이 네임스페이스로 제한됩니다. 이 매개 변수는 `scope` 매개 변수가 `namespace`로 설정되어 있는 경우에만 적용됩니다. |

### <a name="show-details-of-an-extension-instance"></a>확장 인스턴스의 세부 정보 표시

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension show`를 사용하여 현재 설치된 확장 인스턴스를 확인합니다.

```azurecli
az k8s-extension show --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>클러스터에 설치된 모든 확장 나열

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension list`를 사용하여 클러스터에 설치된 모든 확장을 나열합니다.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

### <a name="update-extension-instance"></a>확장 인스턴스 업데이트

> [!NOTE]
> 업데이트를 허용 하는 configurationsetting 및 ConfigurationProtectedSettings의 특정 설정에 대 한 자세한 내용은 확장 유형 (예: Azure ML)의 설명서를 참조 하세요. ConfigurationProtectedSettings의 경우에는 단일 설정을 업데이트 하는 동안 모든 설정이 제공 될 것으로 예상 됩니다. 일부 설정이 생략 되 면 이러한 설정은 사용 되지 않는 것으로 간주 되어 삭제 됩니다.

`k8s-extension update`필수 매개 변수에 대 한 값을 전달 하 여 기존 확장 인스턴스를로 업데이트 합니다. 아래 명령은 Azure Machine Learning 확장 인스턴스의 자동 업그레이드 설정을 업데이트 합니다.

```azurecli
az k8s-extension update --name azureml --extension-type Microsoft.AzureML.Kubernetes --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

**필수 매개 변수**

| 매개 변수 이름 | 설명 |
|----------------|------------|
| `--name` | 확장의 이름입니다. |
| `--extension-type` | 클러스터에 설치하려는 확장의 유형입니다. 예: Kubernetes | 
| `--cluster-name` | 확장 인스턴스를 만들어야 하는 AKS 클러스터의 이름입니다. |
| `--resource-group` | AKS 클러스터를 포함하는 리소스 그룹 |
| `--cluster-type` | 확장 인스턴스를 만들어야 하는 클러스터 유형입니다. `managedClusters`AKS 클러스터에 매핑될 때 지정 합니다.|

**선택적 매개 변수**

| 매개 변수 이름 | 설명 |
|--------------|------------|
| `--auto-upgrade-minor-version` | 확장 부 버전이 자동으로 업그레이드될지 여부를 지정하는 부울 속성입니다. 기본값: `true`.  이 매개 변수를 true로 설정하면 버전이 동적으로 업데이트되므로 `version` 매개 변수를 설정할 수 없습니다. `false`로 설정된 경우 확장은 패치 버전에 대해서도 자동으로 업그레이드되지 않습니다. |
| `--version` | 설치할 확장의 버전(확장 인스턴스를 고정할 특정 버전)입니다. auto-upgrade-minor-version이 `true`로 설정된 경우에는 제공되지 않아야 합니다. |
| `--configuration-settings` | 기능을 제어하기 위해 확장에 전달할 수 있는 설정입니다. 업데이트를 필요로 하는 설정만 제공 해야 합니다. 제공 된 설정은 제공 된 값으로 대체 됩니다. 매개 변수 이름 뒤에 공백으로 구분된 `key=value` 쌍으로 전달됩니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-settings-file`을 사용할 수 없습니다. |
| `--configuration-settings-file` | 구성 설정을 확장에 전달하는 데 사용되는 키 값 쌍이 포함된 JSON 파일의 경로입니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-settings`를 사용할 수 없습니다. |
| `--configuration-protected-settings` | 이러한 설정은 `GET` API 호출 또는 `az k8s-extension show` 명령을 사용하여 검색할 수 없으므로 중요한 설정을 전달하는 데 사용됩니다. 설정을 업데이트할 때 모든 설정이 제공 될 것으로 예상 됩니다. 일부 설정이 생략 되 면 이러한 설정은 사용 되지 않는 것으로 간주 되어 삭제 됩니다. 매개 변수 이름 뒤에 공백으로 구분된 `key=value` 쌍으로 전달됩니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-protected-settings-file`을 사용할 수 없습니다. |
| `--configuration-protected-settings-file` | 중요한 설정을 확장에 전달하는 데 사용되는 키 값 쌍이 포함된 JSON 파일의 경로입니다. 이 매개 변수를 명령에 사용하는 경우에는 동일한 명령에서 `--configuration-protected-settings`를 사용할 수 없습니다. |
| `--scope` | `cluster` 또는 `namespace` 확장에 대한 설치 범위 |
| `--release-train` |  확장 작성자는 `Stable`, `Preview` 등의 다양한 릴리스 학습으로 버전을 게시할 수 있습니다. 이 매개 변수를 명시적으로 설정하지 않으면 `Stable`이 기본값으로 사용됩니다. `autoUpgradeMinorVersion` 매개 변수가 `false`로 설정된 경우에는 이 매개 변수를 사용할 수 없습니다. |

### <a name="delete-extension-instance"></a>확장 인스턴스 삭제

필수 매개 변수에 대한 값을 전달하면서 `k8s-extension delete`를 사용하여 클러스터에서 확장 인스턴스를 삭제합니다.

```azurecli
az k8s-extension delete --name azureml --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type managedClusters
```

>[!NOTE]
> 이 확장을 나타내는 Azure 리소스는 즉시 삭제됩니다. 이 확장과 연결된 클러스터의 Helm 릴리스는 Kubernetes 클러스터에서 실행 중인 에이전트가 네트워크에 연결되어 있고 Azure 서비스에 다시 연결하여 원하는 상태를 가져올 수 있는 경우에만 삭제됩니다.

<!-- when extensions are available, add this section
## Next steps

Learn more about the cluster extensions currently available for AKS:

> [!div class="nextstepaction"]

-->

<!-- LINKS -->
<!-- INTERNAL -->
[arc-k8s-extensions]: ../azure-arc/kubernetes/conceptual-extensions.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-ml-overview]:  <!-- need link -->
[dapr-overview]: <!-- Not yet live -->
[k8s-extension-reference]: /cli/azure/k8s-extension

<!-- EXTERNAL -->
[arc-k8s-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc&regions=all