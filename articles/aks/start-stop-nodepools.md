---
title: AKS (Azure Kubernetes Service)에서 노드 풀 시작 및 중지 (미리 보기)
description: Azure Kubernetes 서비스 (AKS)에서 노드 풀을 시작 하거나 중지 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 10/25/2021
author: qpetraroia
ms.author: qpetraroia
ms.openlocfilehash: 4bf2eeda7b13d520bca769e9698e4ea5abebd584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102914"
---
# <a name="start-and-stop-an-azure-kubernetes-service-aks-node-pool-preview"></a>AKS (Azure Kubernetes Service) 노드 풀 시작 및 중지 (미리 보기)

특정 작업을 실행 하는 노드 풀을 포함 하는 개발 클러스터와 같이 AKS 워크 로드를 지속적으로 실행할 필요가 없을 수도 있습니다. 비용을 최적화 하기 위해 AKS 클러스터에서 노드 풀을 완전히 해제 (중지) 하 여 계산 비용을 절감할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli], [Azure PowerShell 사용][kubernetes-walkthrough-powershell] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

*Aks-미리 보기* Azure CLI 확장도 필요 합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-previewstartstopagentpool-preview-feature"></a>`PreviewStartStopAgentPool` 미리 보기 기능 등록

기능을 사용하려면 구독에서도 `PreviewStartStopAgentPool` 기능 플래그를 사용하도록 설정해야 합니다.

`PreviewStartStopAgentPool`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PreviewStartStopAgentPool"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PreviewStartStopAgentPool')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-node-pool"></a>AKS node 풀 중지

> [!IMPORTANT]
> 노드 풀 시작/중지를 사용 하는 경우 다음과 같은 동작이 예상 됩니다.
>
> * 시스템 풀을 중지할 수 없습니다.
> * 스폿 노드 풀을 지원 합니다.
> * 중지 된 노드 풀을 업그레이드할 수 있습니다.
> * 클러스터 및 노드 풀이 실행 되 고 있어야 합니다.

`az aks nodepool stop`실행 중인 AKS 노드 풀을 중지 하는 데 사용 합니다. 다음 예에서는 *testnodepool* 노드 풀을 중지 합니다.

```azurecli-interactive
az aks nodepool stop --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

[Az aks show][az-aks-show] 명령을 사용 하 여 노드 풀이 중지 된 시기를 확인 하 고 `powerState` `Stopped` 아래 출력에 표시 된 것 처럼 표시 되는지 확인할 수 있습니다.

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code": "Stopped"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> 이 `provisioningState` 표시 `Stopping` 되 면 노드 풀이 아직 완전히 중지 되지 않은 것입니다.

## <a name="start-a-stopped-aks-node-pool"></a>중지 된 AKS 노드 풀 시작

`az aks nodepool start`중지 된 AKS 노드 풀을 시작 하는 데 사용 합니다. 다음 예제에서는 *testnodepool* 이라는 중지 된 노드 풀을 시작 합니다.

```azurecli-interactive
az aks nodepool start --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

노드 풀이 [az aks show 및 show][az-aks-show] 를 사용 하 여 시작 되었는지 확인할 수 `powerState` 있습니다 `Running` . 예:

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code": "Running"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> 이 `provisioningState` 표시 `Starting` 되 면 노드 풀이 아직 완전히 시작 되지 않은 것입니다.

---

## <a name="next-steps"></a>다음 단계

- 풀 크기를 0으로 조정 하는 방법에 대한 자세한 `User` 내용은 [ `User` 풀 크기 조정](scale-cluster.md#scale-user-node-pools-to-0)을 참조 하세요.
- 클러스터를 중지 하는 방법에 대 한 자세한 내용은 [클러스터 시작/중지](start-stop-cluster.md)를 참조 하세요.
- 스폿 인스턴스를 사용하여 비용을 절감 하는 방법을 알아보려면 [AKS에 스폿 노드 풀 추가](spot-node-pool.md)를 참조 하세요.
- AKS 지원 정책에 대한 자세한 내용은 [AKS 지원 정책](support-policies.md)을 참조 하세요.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
[kubernetes-walkthrough-powershell]: kubernetes-walkthrough-powershell.md
[stop-azakscluster]: /powershell/module/az.aks/stop-azakscluster
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[start-azakscluster]: /powershell/module/az.aks/start-azakscluster