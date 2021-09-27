---
title: AKS(Azure Kubernetes Service) 클러스터에 스폿 노드 풀 추가
description: AKS(Azure Kubernetes Service) 클러스터에 스폿 노드 풀을 추가하는 방법을 알아봅니다.
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c943db2577de8fc1e4adc0e9cfbf408bffb5f2c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585375"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터에 스폿 노드 풀 추가

스폿 노드 풀은 [스폿 가상 머신 확장 집합][vmss-spot]으로 지원되는 노드 풀입니다. AKS 클러스터에서 노드에 스폿 VM을 사용하면 상당한 비용 절감으로 Azure에서 사용되지 않는 용량을 활용할 수 있습니다. 사용 가능한 미사용 용량의 양은 노드 크기, 지역 및 하루 중 시간을 비롯한 여러 요인에 따라 달라집니다.

스폿 노드 풀을 배포할 때 Azure는 사용 가능한 용량이 있는 경우 스폿 노드를 할당합니다. 그러나 스폿 노드에 대한 SLA는 없습니다. 스폿 노드 풀을 지원하는 스폿 확장 집합은 단일 장애 도메인에서 배포되며 고가용성 보장하지 않습니다. Azure에 용량이 다시 필요할 때마다 Azure 인프라에서 스폿 노드를 제거합니다.

스폿 노드는 중단, 조기 종료 또는 제거를 처리할 수 있는 워크로드에 적합합니다. 예를 들어 일괄 처리 작업, 개발 및 테스트 환경과 같은 워크로드, 대규모 컴퓨팅 워크로드는 스폿 노드 풀에서 예약되는 좋은 후보가 될 수 있습니다.

이 문서에서는 기존 AKS(Azure Kubernetes Service) 클러스터에 보조 스폿 노드 풀을 추가합니다.

이 문서에서는 Kubernetes 및 Azure Load Balancer 개념을 기본적으로 이해하고 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

스폿 노드 풀을 사용하기 위해 클러스터를 만들 때 해당 클러스터는 노드 풀 및 *표준* SKU 부하 분산 장치에도 Virtual Machine Scale Sets를 사용해야 합니다. 스폿 노드 풀을 사용하려면 클러스터를 만든 후 추가 노드 풀도 추가해야 합니다. 추가 노드 풀 추가는 이후 단계에서 다룹니다.

이 문서를 진행하려면 Azure CLI 버전 2.14 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="limitations"></a>제한 사항

스폿 노드 풀을 사용하여 AKS 클러스터를 만들고 관리할 때 다음 제한 사항이 적용됩니다.

* 스폿 노드 풀은 클러스터의 기본 노드 풀이 될 수 없습니다. 스폿 노드 풀은 보조 풀에만 사용할 수 있습니다.
* 스폿 노드 풀은 차단 및 드레이닝을 보장할 수 없으므로 스폿 노드 풀을 업그레이드할 수 없습니다. Kubernetes 버전 업그레이드와 같은 작업을 수행하려면 기존 스폿 노드 풀을 새로 교체해야 합니다. 스폿 노드 풀을 교체하려면 다른 버전의 Kubernetes로 새 스폿 노드 풀을 만들고 *준비* 상태가 될 때까지 기다린 다음 이전 노드 풀을 제거합니다.
* 컨트롤 플레인과 노드 풀은 동시에 업그레이드할 수 없습니다. 컨트롤 플레인과 나머지 노드 풀을 동시에 업그레이드하려면 별도로 업그레이드하거나 스폿 노드 풀을 제거해야 합니다.
* 스폿 노드 풀은 Virtual Machine Scale Sets를 사용해야 합니다.
* 생성 후에는 ScaleSetPriority 또는 SpotMaxPrice를 변경할 수 없습니다.
* SpotMaxPrice를 설정할 때 이 값은 -1이거나 소수점 이하 5자리까지의 양수 값이어야 합니다.
* 스폿 노드 풀에는 레이블 *kubernetes.azure.com/scalesetpriority:spot*, taint *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* 이 있고,시스템 Pod에는 선호도 방지가 있습니다.
* 스폿 노드 풀에서 워크로드를 예약하려면 [해당 toleration][spot-toleration]을 추가해야 합니다.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>AKS 클러스터에 스폿 노드 풀 추가

여러 노드 풀이 사용 설정된 기존 클러스터에 스폿 노드 풀을 추가해야 합니다. 여러 노드 풀이 사용 설정된 AKS 클러스터를 만드는 방법에 대한 자세한 내용은 [여기][use-multiple-node-pools]를 참조하세요.

[az aks nodepool add][az-aks-nodepool-add]를 사용하여 노드 풀을 만듭니다.
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

기본적으로 여러 노드 풀이 있는 클러스터를 만들 때 AKS 클러스터에서 *우선 순위* 가 *일반* 인 노드 풀을 만듭니다. 위의 명령은 *priority* 가 *Spot* 인 기존 AKS 클러스터에 보조 노드 풀을 추가합니다. *priority* 를 *Spot* 으로 지정하면 노드 풀이 스폿 노드 풀로 됩니다. 위의 예에서 *제거 정책* 매개 변수는 기본값인 *삭제* 로 설정됩니다. [제거 정책][eviction-policy]을 *삭제* 로 설정하면 노드 풀의 기본 확장 집합에 있는 노드가 제거될 때 삭제됩니다. 제거 정책을 *할당 취소* 로 설정할 수도 있습니다. 제거 정책을 *할당 취소* 로 설정하면 기본 확장 집합의 노드가 제거 시 중지-할당 취소 상태로 설정됩니다. 중지-할당 취소 상태의 노드는 컴퓨팅 할당량에 포함되며 클러스터 확장 또는 업그레이드에 문제를 일으킬 수 있습니다. *우선 순위* 및 *제거 정책* 값은 노드 풀 생성 중에만 설정할 수 있습니다. 이러한 값은 나중에 업데이트할 수 없습니다.

이 명령은 또한 [클러스터 자동 크기 조정기][cluster-autoscaler]를 사용하도록 설정하며, 스폿 노드 풀과 함께 사용하는 것이 좋습니다. 클러스터에서 실행되는 워크로드에 따라 클러스터 자동 크기 조정기는 노드 풀의 노드 수를 확장하고 축소합니다. 스폿 노드 풀의 경우 추가 노드가 여전히 필요한 경우 클러스터 자동 크기 조정기가 제거 후 노드 수를 확장합니다. 노드 풀에 포함될 수 있는 최대 노드 수를 변경하는 경우 클러스터 자동 크기 조정기와 관련된 `maxCount` 값도 조정해야 합니다. 클러스터 자동 크기 조정기를 사용하지 않는 경우 제거 시 스폿 풀은 결국 0으로 감소하고 추가 스폿 노드를 수신하려면 수동 작업이 필요합니다.

> [!Important]
> 일괄 처리 작업 및 테스트 환경과 같이 중단을 처리할 수 있는 스폿 노드 풀에서만 워크로드를 예약합니다. 스폿 노드 풀에서 노드 제거를 처리할 수 있는 워크로드만 예약되도록 스폿 노드 풀에 [taint 및 toleration][taints-tolerations]을 설정하는 것이 좋습니다. 예를 들어 위의 명령은 기본적으로 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* 테인트(테인트)를 추가하므로 이 노드에서 해당 toleration이 있는 Pod만 예약됩니다.

## <a name="verify-the-spot-node-pool"></a>스폿 노드 풀 확인

노드 풀이 스폿 노드 풀로 추가되었는지 확인하려면 다음을 수행합니다.

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

*scaleSetPriority* 가 *Spot* 인지 확인합니다.

스폿 노드에서 실행되도록 Pod를 예약하려면 스폿 노드에 적용된 taint에 해당하는 toleration을 추가합니다. 다음 예는 이전 단계에서 사용된 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* taint에 해당하는 toleration을 정의하는 yaml 파일의 일부를 보여 줍니다.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

이 toleration이 있는 Pod가 배포되면 Kubernetes는 taint가 적용된 노드에서 Pod를 성공적으로 예약할 수 있습니다.

## <a name="max-price-for-a-spot-pool"></a>스폿 풀의 최대 가격
[스폿 인스턴스 가격은 지역 및 SKU에 따라 변동됩니다][pricing-spot]. 자세한 내용은 [Linux][pricing-linux] 및 [Windows][pricing-windows]에 대한 가격 책정을 참조하세요.

가변 가격 책정을 사용하는 경우 최대 5개의 소수 자릿수를 사용하여 미국 달러(USD)로 최대 가격을 설정하는 옵션을 사용할 수 있습니다. 예를 들어 *0.98765* 값은 시간당 $0.98765 USD의 최대 가격입니다. 최대 가격을 *-1* 로 설정하면 가격에 따라 인스턴스가 제거되지 않습니다. 인스턴스 가격은 사용 가능한 용량과 할당량이 있는 한 스폿의 현재 가격과 표준 인스턴스의 가격 중 더 낮은 가격이 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 스폿 노드 풀을 추가하는 방법을 알아보았습니다. 노드 풀에서 Pod를 제어하는 ​​방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능에 대한 모범 사례][operator-best-practices-advanced-scheduler]를 참조하세요.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md
