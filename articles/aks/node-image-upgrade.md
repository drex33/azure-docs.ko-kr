---
title: AKS(Azure Kubernetes Service) 노드 이미지
description: AKS 클러스터 노드 및 노드 풀의 이미지를 업그레이드하는 방법을 알아봅니다.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 05d957fac23f1fd9d33a44fa74642eb162550880
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529419"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>AKS(Azure Kubernetes Service) 노드 이미지 업그레이드

AKS는 노드 이미지 업그레이드를 지원하여 최신 OS와 런타임 업데이트를 최신 상태로 유지합니다. AKS는 일주일에 하나의 최신 업데이트가 포함된 새 이미지를 제공하므로 Linux 또는 Windows 패치를 비롯하여 최신 기능을 위해 노드의 이미지를 정기적으로 업그레이드하는 것이 유용합니다. AKS 릴리스 노트를 통해 이미지 업그레이드에 대한 알림이 고객에게 제공되더라도 모든 지역에서 업데이트가 롤아웃되는 데 최대 1주일이 걸릴 수 있습니다. 이 문서에서는 AKS 클러스터 노드 이미지를 업그레이드하는 방법 및 Kubernetes 버전을 업그레이드하지 않고 노드 풀 이미지를 업데이트하는 방법을 보여줍니다.

AKS에서 제공하는 최신 이미지에 대한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases)를 확인하세요.

클러스터의 Kubernetes 버전을 업그레이드하는 방법에 대한 자세한 내용은 [AKS 클러스터 업그레이드][upgrade-cluster]를 참조하세요.

> [!NOTE]
> AKS 클러스터는 노드에 대한 가상 머신 확장 집합을 사용해야 합니다.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>노드 풀이 최신 노드 이미지에 있는지 확인

다음 명령을 사용하여 노드 풀에 사용할 수 있는 최신 노드 이미지 버전을 확인할 수 있습니다. 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

출력에서 아래 예제와 같은 `latestNodeImageVersion`을 볼 수 있습니다.

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

따라서 `nodepool1`에 대해 사용할 수 있는 최신 노드 이미지는 `AKSUbuntu-1604-2020.10.28`입니다. 이제 다음을 실행하여 노드 풀에서 사용 중인 현재 노드 이미지 버전과 비교할 수 있습니다.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

출력의 예제는 다음과 같을 수 있습니다.

```output
"AKSUbuntu-1604-2020.10.08"
```

따라서 이 예제에서는 현재 `AKSUbuntu-1604-2020.10.08` 이미지 버전에서 최신 버전 `AKSUbuntu-1604-2020.10.28`로 업그레이드할 수 있습니다. 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>모든 노드 풀에서 모든 노드 업그레이드

노드 이미지 업그레이드는 `az aks upgrade`를 통해 수행됩니다. 노드 이미지를 업그레이드하려면 다음 명령을 사용합니다.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

업그레이드하는 동안 다음 `kubectl` 명령을 사용하여 노드 이미지의 상태를 확인하고 현재 노드 이미지 정보를 필터링합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

업그레이드가 완료되면 `az aks show`를 사용하여 업데이트된 노드 풀 세부 정보를 가져옵니다. 현재 노드 이미지는 `nodeImageVersion` 속성에서 볼 수 있습니다.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>특정 노드 풀 업그레이드

노드 풀에서 이미지를 업그레이드하는 것은 클러스터에서 이미지를 업그레이드하는 것과 비슷합니다.

Kubernetes 클러스터 업그레이드를 수행하지 않고 노드 풀의 OS 이미지를 업데이트하려면 다음 예제에서 `--node-image-only` 옵션을 사용합니다.

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

업그레이드하는 동안 다음 `kubectl` 명령을 사용하여 노드 이미지의 상태를 확인하고 현재 노드 이미지 정보를 필터링합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

업그레이드가 완료되면 `az aks nodepool show`를 사용하여 업데이트된 노드 풀 세부 정보를 가져옵니다. 현재 노드 이미지는 `nodeImageVersion` 속성에서 볼 수 있습니다.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>노드 서지로 노드 이미지 업그레이드

노드 이미지 업그레이드 프로세스의 속도를 높이려면 사용자 지정 가능한 노드 서지 값을 사용하여 노드 이미지를 업그레이드할 수 있습니다. 기본값으로 AKS는 업그레이드를 구성하는 데 하나의 추가 노드를 사용합니다.

업그레이드 속도를 높이려면 `--max-surge` 값을 사용하여 업그레이드에 사용할 노드 수를 구성하여 더 빨리 완료할 수 있습니다. 다양한 `--max-surge` 설정의 장단점에 대해 자세히 알아보려면 [노드 서지 업그레이드 사용자 지정][max-surge]을 참조하세요.

다음 명령을 사용하면 노드 이미지 업그레이드를 수행하는 데 필요한 최대 서지 값을 설정할 수 있습니다.

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

업그레이드하는 동안 다음 `kubectl` 명령을 사용하여 노드 이미지의 상태를 확인하고 현재 노드 이미지 정보를 필터링합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

`az aks nodepool show`를 사용하여 업데이트된 노드 풀 세부 정보를 가져옵니다. 현재 노드 이미지는 `nodeImageVersion` 속성에서 볼 수 있습니다.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>다음 단계

- 최신 노드 이미지에 대한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases)를 참조하세요.
- [AKS 클러스터를 업그레이드][upgrade-cluster]하여 Kubernetes 버전을 업그레이드하는 방법을 알아봅니다.
- [GitHub 작업을 사용하여 클러스터 및 노드 풀 업그레이드 자동 적용][github-schedule]
- 다양한 노드 풀 및 [여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 사용하여 노드 풀을 업그레이드하는 방법에 대해 자세히 알아보세요.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
