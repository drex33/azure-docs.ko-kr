---
title: Uptime SLA가 포함된 AKS(Azure Kubernetes Service)
description: AKS(Azure Kubernetes Service) API Server에 대한 선택적 작동 시간 SLA 제품에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 77bdfb4ed75b9287b911a6b0d2742c235b37e297
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122568023"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>AKS(Azure Kubernetes Service) 작동 시간 SLA

작동 시간 SLA는 AKS 클러스터에 대해 재정적 지원을 받는 상위 SLA를 설정하는 계층입니다. AKS REST API에서 유료 계층으로도 간주되는 작동 시간 SLA가 있는 클러스터에는 더 많은 양의 컨트롤 플레인 리소스가 있으며 클러스터 부하에 맞게 자동으로 스케일링됩니다. 작동 시간 SLA는 [가용성 영역][availability-zones]을 사용하는 클러스터에 Kubernetes API 서버 엔드포인트의 99.95% 가용성을 보장하고, 가용성 영역을 사용하지 않는 클러스터에는 99.9%의 가용성을 보장합니다. AKS는 업데이트 및 장애 도메인에서 마스터 노드 복제본을 사용하여 SLA 요구 사항을 충족하는지 확인합니다.

AKS에서는 컨트롤 플레인 구성 요소의 가용성을 보장하기 위해 프로덕션 워크로드에서 작동 시간 SLA를 사용하는 것이 좋습니다. 반면 무료 계층의 클러스터는 컨트롤 플레인에 대한 복제본 수가 더 적고 리소스가 제한되므로 프로덕션 워크로드에 적합하지 않습니다.

고객은 여전히 SLO(서비스 수준 목표)가 99.5%인 무제한 무료 클러스터를 만들고, 기본 SLO를 선택할 수 있습니다. 

> [!IMPORTANT]
> 송신 잠금이 있는 클러스터의 경우 [송신 트래픽 제한](limit-egress-traffic.md)을 참조하여 적절한 포트를 여세요.

## <a name="region-availability"></a>지역 가용성

* 작동 시간 SLA는 [AKS가 지원되는](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service) 공용 지역 및 Azure Government 지역에서 사용할 수 있습니다.
* 작동 시간 SLA는 AKS가 지원되는 모든 공용 지역의 [프라이빗 AKS 클러스터][private-clusters]에 사용할 수 있습니다.

## <a name="sla-terms-and-conditions"></a>SLA 사용 약관

작동 시간 SLA는 유료 기능이며 클러스터별로 사용하도록 설정됩니다. 작동 시간 SLA 가격은 개별 클러스터의 크기가 아니라 개별 클러스터의 수에 따라 결정됩니다. 자세한 내용은 [작동 시간 SLA 가격 정보](https://azure.microsoft.com/pricing/details/kubernetes-service/)에서 확인할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

* [Azure CLI](/cli/azure/install-azure-cli) 버전 2.8.0 이상 설치

## <a name="creating-a-new-cluster-with-uptime-sla"></a>작동 시간 SLA를 사용하여 새 클러스터 만들기

작동 시간 SLA를 사용하여 새 클러스터를 만들려면 Azure CLI를 사용합니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

[`az aks create`][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 *myAKSCluster* 라는 클러스터를 만듭니다. 이 작업을 완료하는 데 몇 분이 걸립니다.

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```

몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다. 다음 JSON 코드 조각은 SKU의 유료 계층을 보여 줍니다. 이는 클러스터에 작동 시간 SLA가 설정되어 있음을 나타냅니다.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>작동 시간 SLA를 사용하도록 기존 클러스터 수정

필요에 따라 기존 클러스터를 업데이트하여 작동 시간 SLA를 사용할 수 있습니다.

이전 단계에서 AKS 클러스터를 만든 경우 다음과 같이 리소스 그룹을 삭제합니다.

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

새 리소스 그룹 만들기:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

다음과 같이 새 클러스터를 만들고 작동 시간 SLA를 사용하지 않습니다.

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

[`az aks update`][az-aks-update] 명령을 사용하여 기존 클러스터 업데이트:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
```

다음 JSON 코드 조각은 SKU의 유료 계층을 보여 줍니다. 이는 클러스터에 작동 시간 SLA가 설정되어 있음을 나타냅니다.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="opt-out-of-uptime-sla"></a>작동 시간 SLA 옵트아웃

클러스터를 업데이트하여 무료 계층으로 변경하고 작동 시간 SLA를 옵트아웃할 수 있습니다.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
```

## <a name="clean-up"></a>정리

요금 부과를 막으려면 만든 리소스를 모두 정리합니다. 클러스터를 삭제하려면 [`az group delete`][az-group-delete] 명령을 사용하여 AKS 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

[가용성 영역][availability-zones]을 사용하여 AKS 클러스터 워크로드를 통해 고가용성을 개선합니다.

[송신 트래픽을 제한](limit-egress-traffic.md)하도록 클러스터를 구성합니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
