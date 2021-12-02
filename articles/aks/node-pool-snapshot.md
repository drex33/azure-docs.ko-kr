---
title: AKS(스냅샷 Azure Kubernetes Service) 노드 풀(미리 보기)
description: AKS 클러스터 노드 풀을 스냅샷으로 만들고 스냅샷에서 클러스터 및 노드 풀을 만드는 방법을 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 09/11/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 353db8009a7bb1e60599742a21272265967c0204
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481635"
---
# <a name="azure-kubernetes-service-aks-node-pool-snapshot-preview"></a>Azure Kubernetes Service(AKS) 노드 풀 스냅샷(미리 보기)

AKS는 매주 새 노드 이미지를 릴리스하고 모든 새 클러스터, 새 노드 풀 또는 업그레이드 클러스터는 항상 환경을 일관되게 유지하고 반복 가능한 환경을 유지하기 어려울 수 있는 최신 이미지를 받습니다.

노드 풀 스냅샷을 사용하면 노드 풀의 구성 스냅샷을 만든 다음 해당 구성 및 kubernetes 버전이 지원되는 한 해당 스냅샷을 기반으로 새 노드 풀 또는 새 클러스터를 만들 수 있습니다. 지원 가능성 창에 대한 자세한 내용은 [AKS에서 지원되는 Kubernetes 버전을 참조하세요.][supported-versions]

스냅샷은 노드 이미지 버전, kubernetes 버전, OS 유형 및 OS SKU와 같은 원본 노드 풀의 구성 정보를 포함하는 Azure 리소스입니다. 그런 다음 이 스냅샷 리소스와 해당 구성의 해당 값을 참조하여 이를 기반으로 새 노드 풀 또는 클러스터를 만들 수 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

### <a name="limitations"></a>제한 사항

- 스냅샷에서 만든 모든 노드 풀 또는 클러스터는 스냅샷과 동일한 가상 머신 제품군의 VM을 사용해야 합니다. 예를 들어 이러한 경우 노드 이미지가 구조적으로 다르므로 D 시리즈 노드 풀에서 캡처된 스냅샷을 기반으로 새 N 시리즈 노드 풀을 만들 수 없습니다.
- 미리 보기 중에는 원본 노드 풀과 동일한 지역에서 스냅샷을 만들고 사용해야 합니다.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

*또한 aks-preview* Azure CLI 확장 버전 0.5.40 이상도 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-snapshotpreview-preview-feature"></a>`SnapshotPreview` 미리 보기 기능 등록

기능을 사용하려면 구독에서도 `SnapshotPreview` 기능 플래그를 사용하도록 설정해야 합니다.

`SnapshotPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "SnapshotPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/SnapshotPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="take-a-node-pool-snapshot"></a>노드 풀 스냅샷 생성

노드 풀에서 스냅샷을 먼저 만들려면 아래 명령에서 얻을 수 있는 노드 풀 리소스 ID가 필요합니다.

```azurecli-interactive
NODEPOOL_ID=$(az aks nodepool show --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --query id -o tsv)
```

> [!IMPORTANT]
> 스냅샷을 만들려면 2021년 11월 10일 이후에 AKS 노드 풀을 만들거나 업그레이드해야 합니다.

이제 이전 노드 풀에서 스냅샷을 생성하려면 `az aks snapshot` CLI 명령을 사용합니다.

```azurecli-interactive
az aks snapshot create --name MySnapshot --resource-group MyResourceGroup --nodepool-id $NODEPOOL_ID --location eastus
```

## <a name="create-a-node-pool-from-a-snapshot"></a>스냅샷에서 노드 풀 만들기

먼저 이전에 만든 스냅샷의 리소스 ID가 필요합니다. 이 ID는 아래 명령에서 얻을 수 있습니다.

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

이제 아래 명령을 사용하여 이 스냅샷을 기반으로 새 노드 풀을 추가할 수 있습니다.

```azurecli-interactive
az aks nodepool add --name np2 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="upgrading-a-node-pool-to-a-snapshot"></a>노드 풀을 스냅샷으로 업그레이드

스냅샷 kubernetes 버전 및 노드 이미지 버전이 현재 노드 풀의 버전보다 최신 버전인 경우 노드 풀을 스냅샷 구성으로 업그레이드할 수 있습니다.

먼저 이전에 만든 스냅샷의 리소스 ID가 필요합니다. 이 ID는 아래 명령에서 얻을 수 있습니다.

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

이제 이 명령을 사용하여 이 노드 풀을 이 스냅샷 구성으로 업그레이드할 수 있습니다.

```azurecli-interactive
az aks nodepool upgrade --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

> [!NOTE]
> 노드 풀 이미지 버전은 스냅샷에 포함된 버전과 동일하며 모든 크기 조정 작업에서 동일하게 유지됩니다. 그러나 이 노드 풀이 업그레이드되거나 스냅샷 ID를 제공하지 않고 노드 이미지 업그레이드가 수행되는 경우 노드 이미지는 최신 버전으로 업그레이드됩니다.

## <a name="create-a-cluster-from-a-snapshot"></a>스냅샷에서 클러스터 만들기

스냅샷에서 클러스터를 만들면 스냅샷 구성에서 클러스터 원래 시스템 풀이 만들어집니다.

먼저 이전에 만든 스냅샷의 리소스 ID가 필요합니다. 이 ID는 아래 명령에서 얻을 수 있습니다.

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

이제 이 명령을 사용하여 스냅샷 구성에서 이 클러스터를 만들 수 있습니다.

```azurecli-interactive
az aks cluster create --name myAKSCluster2 --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="next-steps"></a>다음 단계

- 최신 노드 이미지에 대한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases)를 참조하세요.
- [AKS 클러스터를 업그레이드][upgrade-cluster]하여 Kubernetes 버전을 업그레이드하는 방법을 알아봅니다.
- 노드 이미지 업그레이드를 사용하여 노드 이미지 버전을 [업그레이드하는][node-image-upgrade] 방법 알아보기
- 다양한 노드 풀 및 [여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 사용하여 노드 풀을 업그레이드하는 방법에 대해 자세히 알아보세요.

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[supported-versions]: supported-kubernetes-versions.md
[upgrade-cluster]: upgrade-cluster.md
[node-image-upgrade]: node-image-upgrade.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
