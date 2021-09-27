---
title: AKS (Azure Kubernetes Service) 클러스터에 대해 축소 모드 사용 (미리 보기)
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes 서비스 (AKS)에서 축소 모드를 사용 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 09/01/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 6bd1f4c70d9c427d7f6487040453e7ecae199717
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800121"
---
# <a name="use-scale-down-mode-to-deletedeallocate-nodes-in-azure-kubernetes-service-aks-preview"></a>확장 모드를 사용 하 여 Azure Kubernetes 서비스 (AKS)에서 노드를 삭제/할당 취소 (미리 보기)

기본적으로 수동으로 또는 클러스터 autoscaler 수행 하는 확장 작업을 수행 하려면 새 노드를 할당 하 고 프로 비전 하 고 확장 작업에서 노드를 삭제 해야 합니다. 규모 축소 모드를 사용 하면 규모를 축소 하 여 AKS (Azure Kubernetes Service) 클러스터에서 노드를 삭제 하거나 할당을 취소할 것인지 여부를 결정할 수 있습니다. 

Azure VM이 `Stopped` (할당 취소 됨) 상태인 경우 vm 계산 리소스에 대 한 요금이 청구 되지 않습니다. 그러나 VM에 연결 된 OS 및 데이터 저장소 디스크에 대해서도 비용을 지불 해야 합니다. 이는 컨테이너 이미지가 해당 노드에 유지 됨을 의미 하기도 합니다. 자세한 내용은 [Azure Virtual Machines의 상태 및 청구][state-billing-azure-vm]를 참조 하세요. 이 동작을 통해 배포에서 캐시 된 이미지를 활용 하므로 더 빠른 작업 속도를 사용할 수 있습니다. 규모 축소 모드를 사용 하면 더 이상 노드를 사전 프로 비전 할 필요가 없으며 컨테이너 이미지를 미리 끌어올 수 있어 계산 비용을 절감할 수 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

> [!WARNING]
> 할당 취소 된 Vm을 유지 하려면 확대/축소 모드를 할당 취소로 설정 해야 합니다. 여기에는 IaaS Api (가상 머신 확장 집합 Api)를 사용 하 여 할당 취소 된 Vm이 포함 됩니다. 확대/축소 모드를 삭제 하도록 설정 하면 할당 되지 않은 Vm이 모두 제거 됩니다.

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

### <a name="limitations"></a>제한 사항

- [삭제 된 OS][ephemeral-os] 디스크는 지원 되지 않습니다. `--node-osdisk-type Managed`클러스터 또는 노드 풀을 만들 때를 통해 관리 되는 OS 디스크를 지정 해야 합니다.
- [스폿 노드 풀][spot-node-pool] 은 지원 되지 않습니다.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

*Aks-preview* Azure CLI 확장 버전 0.5.30 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-aks-scaledownmodepreview-preview-feature"></a>`AKS-ScaleDownModePreview` 미리 보기 기능 등록

기능을 사용하려면 구독에서도 `AKS-ScaleDownModePreview` 기능 플래그를 사용하도록 설정해야 합니다.

`AKS-ScaleDownModePreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ScaleDownModePreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ScaleDownModePreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="using-scale-down-mode-to-deallocate-nodes-on-scale-down"></a>축소 모드를 사용 하 여 축소 시 노드 할당 취소

를 설정 하 여 `--scale-down-mode Deallocate` 클러스터/노드 풀의 규모를 축소 하는 동안 노드 할당을 취소 합니다. 할당 취소 된 모든 노드가 중지 됩니다. 클러스터/노드 풀을 확장 해야 하는 경우 새 노드를 프로 비전 하기 전에 할당 취소 된 노드가 먼저 시작 됩니다.

이 예에서는 20 개의 노드가 포함 된 새 노드 풀을 만들고, 규모 축소 시 노드를 통해 할당을 취소 하도록 지정 `--scale-down-mode Deallocate` 합니다.

```azurecli-interactive
az aks nodepool add --node-count 20 --scale-down-mode Deallocate --node-osdisk-type Managed --max-pods 10 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

노드 풀의 크기를 조정 하 고 노드 수를 5로 변경 하 여 15 개 노드의 할당을 취소 합니다.

```azurecli-interactive
az aks nodepool scale --node-count 5 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="deleting-previously-deallocated-nodes"></a>이전에 할당 취소 한 노드 삭제

할당 취소 된 노드를 삭제 하려면를 설정 하 여 확대/축소 모드를로 변경할 수 있습니다 `Delete` `--scale-down-mode Delete` . 이제 할당 취소 된 15 개의 노드가 삭제 됩니다.

```azurecli-interactive
az aks nodepool update --scale-down-mode Delete --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

> [!NOTE]
> 확대/축소 모드를에서로 변경 하면 `Deallocate` `Delete` `Deallocate` 노드 풀을 축소 모드로 유지 하면서 할당 되지 않은 모든 노드를 삭제 합니다 `Deallocate` .

## <a name="using-scale-down-mode-to-delete-nodes-on-scale-down"></a>확대/축소 모드를 사용 하 여 축소에서 노드 삭제

축소 모드를 사용 하지 않고 AKS의 기본 동작은 클러스터를 확장할 때 노드를 삭제 하는 것입니다. 확대/축소 모드를 사용 하면을 설정 하 여 명시적으로이를 달성할 수 있습니다 `--scale-down-mode Delete` .

이 예에서는 새 노드 풀을 만들고를 통해 축소 하 여 노드를 삭제 하도록 지정 `--scale-down-mode Delete` 합니다. 크기 조정 작업은 클러스터 autoscaler를 통해 처리 됩니다.

```azurecli-interactive
az aks nodepool add --enable-cluster-autoscaler --min-count 1 --max-count 10 --max-pods 10 --node-osdisk-type Managed --scale-down-mode Delete --name nodepool3 --cluster-name myAKSCluster --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- AKS 클러스터를 업그레이드 하는 방법에 대해 자세히 알아보려면 [AKS 클러스터 업그레이드][aks-upgrade] 를 참조 하세요.
- 클러스터 autoscaler에 대 한 자세한 내용은 [AKS에서 응용 프로그램 요구를 충족 하도록 클러스터 크기 자동 조정][cluster-autoscaler] 을 참조 하세요.

<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[cluster-autoscaler]: cluster-autoscaler.md
[ephemeral-os]: cluster-configuration.md#ephemeral-os
[state-billing-azure-vm]: ../virtual-machines/states-billing.md
[spot-node-pool]: spot-node-pool.md