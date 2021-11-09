---
title: AKS(Azure Kubernetes Service)에서 시스템 노드 풀 사용
description: AKS(Azure Kubernetes Service)에서 시스템 노드 풀을 만들고 관리하는 방법 알아보기
services: container-service
author: georgewallace
ms.topic: article
ms.date: 06/18/2020
ms.author: gwallace
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: c164323f6a181dd29e525e698cdbc55b5cb03892
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057559"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 시스템 노드 풀 관리

AKS(Azure Kubernetes Service)에서 동일한 구성의 노드가 *노드 풀* 에 하나로 그룹화됩니다. 노드 풀에는 애플리케이션을 실행하는 기본 VM이 포함됩니다. 시스템 노드 풀과 사용자 노드 풀은 AKS 클러스터의 두 가지 서로 다른 노드 풀 모드입니다. 시스템 노드 풀은 `CoreDNS` 및 `metrics-server`와 같은 중요 시스템 Pod를 호스트하는 기본 목적을 제공합니다. 사용자 노드 풀은 애플리케이션 Pod를 호스트하는 기본 목적을 제공합니다. 하지만 AKS 클러스터에 풀을 하나만 포함하려는 경우 시스템 노드 풀에 애플리케이션 Pod를 예약할 수 있습니다. 모든 AKS 클러스터에는 노드가 하나 이상 있는 시스템 노드 풀이 하나 이상 포함되어야 합니다.

> [!Important]
> 프로덕션 환경에서 AKS 클러스터에 대해 단일 시스템 노드 풀을 실행할 경우 노드 풀에 대해 노드를 3개 이상 사용하는 것이 좋습니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure CLI 버전 2.3.1 이상이 설치 및 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="limitations"></a>제한 사항

시스템 노드 풀을 지원하는 AKS 클러스터를 만들고 관리할 때 다음 제한 사항이 적용됩니다.

* [AKS(Azure Kubernetes Service)의 할당량, 가상 머신 크기 제한 및 지역 가용성][quotas-skus-regions]을 참조하세요
* AKS 클러스터는 가상 머신 확장 집합에 VM 유형 및 *표준* SKU 부하 분산 장치로 빌드되어 있어야 합니다.
* 노드 풀의 이름은 영숫자 소문자만 포함할 수 있고 소문자 문자로 시작해야 합니다. Linux 노드 풀의 경우 길이가 1~12자 사이여야 합니다. Windows 노드 풀의 경우 길이가 1~6자 사이여야 합니다.
* 2020-03-01 이상의 API 버전을 사용하여 노드 풀 모드를 설정해야 합니다. 2020-03-01 이전 API 버전으로 생성된 클러스터는 사용자 노드 풀만 포함하며 [풀 모드 업데이트 단계](#update-existing-cluster-system-and-user-node-pools)에 따라 시스템 노드 풀을 포함하도록 마이그레이션될 수 있습니다.
* 노드 풀의 모드는 필수 속성이며 ARM 템플릿 또는 직접 API 호출을 사용할 때 명시적으로 설정되어 있어야 합니다.

## <a name="system-and-user-node-pools"></a>시스템 및 사용자 노드 풀

시스템 노드 풀의 경우 AKS가 **kubernetes.azure.com/mode: system** 레이블을 해당 노드에 자동으로 할당합니다. 따라서 AKS에서는 이 레이블을 포함하는 노드 풀에서 시스템 Pod를 예약하는 방식이 선호됩니다. 이 레이블은 시스템 노드 풀에서 애플리케이션 Pod를 예약하지 못하도록 방지하지 않습니다. 하지만 잘못 구성된 또는 악의적인 애플리케이션 Pod가 시스템 Pod를 우연히 종료시키지 않도록 방지하기 위해 애플리케이션 Pod로부터 중요한 시스템 Pod를 격리시키는 것이 좋습니다. 전용 시스템 노드 풀을 만들어 이 동작을 적용할 수 있습니다. `CriticalAddonsOnly=true:NoSchedule` taint를 사용하여 애플리케이션 Pod가 시스템 노드 풀에 예약되지 않도록 방지할 수 있습니다.

시스템 노드 풀에 대한 제한은 다음과 같습니다.

* 시스템 풀 osType이 Linux여야 합니다.
* 사용자 노드 풀 osType이 Linux 또는 Windows여야 합니다.
* 시스템 풀에 하나 이상의 노드가 포함되어야 하고, 사용자 노드 풀에는 0개 이상의 노드가 포함될 수 있습니다.
* 시스템 노드 풀에는 최소 2개 이상의 vCPU 및 4GB 이상의 메모리가 포함된 VM SKU가 필요합니다. 하지만 버스트 가능 VM(B 시리즈)은 권장되지 않습니다.
* 특히 큰 클러스터(다중 CoreDNS Pod 복제본, 3-4 이상 추가 기능 등)의 경우 최소 2개 노드의 4개 vCPU가 권장됩니다(예: Standard_DS4_v2).
* 시스템 노드 풀은 [Pod의 최솟값 및 최댓값 수식][maximum-pods]에 설명된 대로 최소 30개 이상의 Pod를 지원해야 합니다.
* 스폿 노드 풀에는 사용자 노드 풀이 필요합니다.
* 추가 시스템 노드 풀 추가 또는 시스템 노드 풀인 노드 풀 변경 시에는 시스템 Pod가 자동으로 이동되지 *않습니다*. 사용자 노드 풀로 변경하더라도 시스템 Pod는 동일한 노드 풀에서 계속 실행될 수 있습니다. 이전에 시스템 노드 풀이었던 시스템 노드 Pod를 실행하는 노드 풀을 삭제하거나 축소하면 해당 시스템 노드 Pod가 선호되는 일정에 따라 새 시스템 노드 풀에 다시 배포됩니다.

노드 풀에는 다음 작업을 수행할 수 있습니다.

* 전용 시스템 노드 풀 만들기(`mode:system`의 노드 풀에 시스템 Pod 예약 선호)
* AKS 클러스터에서 수행할 다른 시스템 노드 풀이 있으면 시스템 노드 풀을 사용자 노드 풀로 변경합니다.
* 사용자 노드 풀을 시스템 노드 풀로 변경합니다.
* 사용자 노드 풀을 삭제합니다.
* AKS 클러스터에서 수행할 다른 시스템 노드 풀이 있으면 시스템 노드 풀을 삭제합니다.
* AKS 클러스터는 여러 시스템 노드 풀을 포함할 수 있으며 하나 이상의 시스템 노드 풀이 필요합니다.
* 기존 노드 풀에서 여러 변경할 수 없는 설정을 변경하려는 경우 새 노드 풀을 만들어 이를 대체할 수 있습니다. 한 가지 예시는 새 maxPods 설정의 새 노드 풀을 추가하고 이전 노드 풀을 삭제하는 것입니다.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>시스템 노드 풀로 새 AKS 클러스터 만들기

새 AKS 클러스터를 만들 때는 단일 노드가 포함된 시스템 노드 풀을 자동으로 만듭니다. 초기 노드 풀은 기본적으로 시스템 유형 모드로 지정됩니다. `az aks nodepool add`로 새 노드 풀을 만들 때 모드 매개변수를 명시적으로 지정하지 않으면 해당 노드 풀이 사용자 노드 풀입니다.

다음 예제에서는 *미국 동부* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예시에서는 노드 하나가 포함된 전용 시스템 풀로 *myAKSCluster* 라는 클러스터를 만듭니다. 프로덕션 워크로드의 경우 노드가 3개 이상인 시스템 노드 풀을 사용해야 합니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>기존 AKS 클러스터에 전용 시스템 노드 풀 추가

> [!Important]
> 노드 풀을 만든 후 CLI를 통해 노드 taint를 변경할 수 없습니다.

기존 AKS 클러스터에 하나 이상의 시스템 노드 풀을 추가할 수 있습니다. 사용자 노드 풀에 애플리케이션 Pod를 예약하고 중요 시스템 Pod에 대해서만 시스템 노드 풀을 전용으로 지정하는 것이 좋습니다. 이렇게 하면 악의적인 애플리케이션 Pod가 시스템 Pod를 우연히 종료시키는 것을 방지할 수 있습니다. 시스템 노드 풀에 대해 `CriticalAddonsOnly=true:NoSchedule` [taint][aks-taints]로 이 동작을 적용합니다. 

다음 명령은 기본 3개 노드가 포함된 시스템 유형 모드의 전용 노드 풀을 추가합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>노드 풀의 세부 정보 표시

다음 명령으로 노드 풀의 세부 정보를 확인할 수 있습니다.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

**시스템** 유형 모드는 시스템 노드 풀에 대해 정의되고, **사용자** 유형 모드는 사용자 노드 풀에 대해 정의됩니다. 시스템 풀의 경우 애플리케이션 Pod가 이 노드 풀에 예약되지 않도록 방지하기 위해 taint가 `CriticalAddonsOnly=true:NoSchedule`로 설정되었는지 확인합니다.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>기존 클러스터 시스템 및 사용자 노드 풀 업데이트

> [!NOTE]
> 2020-03-01 이상의 API 버전을 사용하여 시스템 노드 풀 모드를 설정해야 합니다. 2020-03-01보다 오래된 API 버전으로 생성된 클러스터는 사용자 노드 풀만 결과로 포함합니다. 이전 클러스터에서 시스템 노드 풀 기능 및 이점을 얻으려면 최신 Azure CLI 버전에서 다음 명령으로 기존 노드 풀의 모드를 업데이트합니다.

시스템 및 사용자 노드 풀 모두에 대해 모드를 변경할 수 있습니다. 다른 시스템 노드 풀이 AKS 클러스터에 이미 있는 경우에만 시스템 노드 풀을 사용자 풀로 변경할 수 있습니다.

이 명령은 시스템 노드 풀을 사용자 노드 풀로 변경합니다.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

이 명령은 사용자 노드 풀을 시스템 노드 풀로 변경합니다.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>시스템 노드 풀 삭제

> [!Note]
> 2020-03-02 이전 API 버전의 AKS 클러스터에서 시스템 노드 풀을 사용하려면 새 시스템 노드 풀을 추가한 후 원래 기본 노드 풀을 삭제합니다.

노드 풀을 삭제하려면 AKS 클러스터에 시스템 노드 풀이 2개 이상 있어야 합니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>리소스 정리

클러스터를 삭제하려면 [az group delete][az-group-delete] 명령을 사용하여 AKS 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에서 시스템 노드 풀을 만들고 관리하는 방법을 배웠습니다. 여러 노드 풀을 사용하는 방법에 대한 자세한 내용은 [여러 노드 풀 사용][use-multiple-node-pools]을 참조하세요.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
