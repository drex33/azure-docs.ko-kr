---
title: AKS(Azure Kubernetes Service)에서 가용성 영역 업그레이드
description: AKS(Azure Kubernetes Service)의 가용성 영역에 노드를 배포하는 클러스터를 만드는 방법 알아보기
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 18d7cba9fe92f2021757fdb58c9e76cd0c413e70
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026834"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>가용성 영역을 사용하는 AKS(Azure Kubernetes Service) 클러스터 만들기

AKS(Azure Kubernetes Service) 클러스터는 기본 Azure 인프라의 논리적 섹션에 노드 및 스토리지와 같은 리소스를 배포합니다. 가용성 영역을 사용하는 경우 이 배포 모델은 지정된 가용성 영역의 노드가 다른 가용성 영역에 정의된 것과 물리적으로 분리되어 있는지 확인합니다. 클러스터에 구성된 여러 가용성 영역을 사용하여 배포된 AKS 클러스터는 하드웨어 오류 또는 계획된 유지 관리 이벤트를 방지할 수 있는 높은 수준의 가용성을 제공합니다.

여러 영역에 분산되도록 클러스터의 노드 풀을 정의하면 단일 영역이 중단된 경우에도 특정 노드 풀의 노드가 계속 작동할 수 있습니다. 노드 하위 집합의 오류를 허용하도록 오케스트레이션된 애플리케이션은 단일 데이터 센터에 물리적 오류가 있는 경우에도 계속 사용할 수 있습니다.

이 문서에서는 AKS 클러스터를 만들고 노드 구성 요소를 가용성 영역에 분산하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.76 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="limitations-and-region-availability"></a>제한 사항 및 지역 가용성

AKS 클러스터는 현재 다음 지역에서 가용성 영역을 사용하여 만들 수 있습니다.

* 오스트레일리아 동부
* 브라질 남부
* 캐나다 중부
* 인도 중부
* 미국 중부
* 동아시아
* 미국 동부 
* 미국 동부 2
* 프랑스 중부
* 독일 중서부
* 일본 동부
* 한국 중부
* 북유럽
* 노르웨이 동부
* 동남아시아
* 미국 중남부
* 스웨덴 중부
* 영국 남부
* US Gov 버지니아
* 서유럽
* 미국 서부 2
* 미국 서부 3

가용성 영역을 사용하여 AKS 클러스터를 만들 때는 다음과 같은 제한 사항이 적용됩니다.

* 클러스터 또는 노드 풀을 만들 때만 가용성 영역을 정의할 수 있습니다.
* 클러스터를 만든 후에는 가용성 영역 설정을 업데이트할 수 없습니다. 또한 가용성 영역을 사용하도록 기존의 비가용성 영역 클러스터를 업데이트할 수 없습니다.
* 선택한 노드 크기(VM SKU)를 선택한 모든 가용성 영역에서 사용할 수 있어야 합니다.
* 가용성 영역이 설정된 클러스터를 영역 간에 배포하려면 Azure 표준 Load Balancer를 사용해야 합니다. 이 부하 분산 장치 유형은 클러스터 생성 시에만 정의할 수 있습니다. 표준 부하 분산 장치에 대한 자세한 내용과 제한 사항은 [Azure Load Balancer 표준 SKU 제한 사항][standard-lb-limitations]을 참조하세요.

### <a name="azure-disks-limitations"></a>Azure 디스크 제한 사항

Azure 관리형을 사용하는 볼륨은 현재 영역 중복 리소스가 아닙니다. 볼륨은 영역 간에 연결될 수 없으며 대상 Pod를 호스트하는 지정된 노드와 같은 영역에 공동 배치되어야 합니다.

Kubernetes는 버전 1.12부터 Azure 가용성 영역을 인식합니다. Azure Managed Disk를 참조하는 PersistentVolumeClaim 개체를 다중 영역 AKS 클러스터에 배포할 수 있으며, [Kubernetes는 올바른 가용성 영역에서 이 PVC를 클레임하는 Pod를 예약하는 과정](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones)을 담당합니다.

### <a name="azure-resource-manager-templates-and-availability-zones"></a>Azure Resource Manager 템플릿 및 가용성 영역

AKS 클러스터를 *생성* 할 때 `"availabilityZones": null`와 같은 구문을 사용하여 [템플릿에 null 값][arm-template-null]을 명시적으로 정의하면 Resource Manager 템플릿은 속성이 존재하지 않는 것처럼 속성을 처리합니다. 이는 클러스터의 가용성 영역이 사용하도록 설정되지 않았음을 의미합니다. 또한 가용성 영역 속성을 생략하는 Resource Manager 템플릿을 사용하여 클러스터를 만드는 경우 가용성 영역이 사용하지 않도록 설정됩니다.

기존 클러스터의 가용성 영역에 대한 설정을 업데이트할 수 없으므로 Resource Manager 템플릿으로 AKS 클러스터를 업데이트할 때는 동작이 다릅니다.  가용성 영역에 대한 템플릿에서 명시적으로 null 값을 설정하고 클러스터를 *업데이트* 하는 경우 가용성 영역에 대한 클러스터 변경 사항이 없습니다. 그러나 `"availabilityZones": []`와 같은 구문으로 가용성 영역 속성을 생략하면 배포가 기존 AKS 클러스터에서 가용성 영역을 사용하지 않도록 하고 **실패** 합니다.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 클러스터의 가용성 영역 개요

가용성 영역은 데이터 센터 오류에서 애플리케이션 및 데이터를 보호하는 고가용성 기능입니다. 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하기 위해 모든 영역 사용 지역에는 항상 둘 이상의 영역이 있습니다. 한 지역 내에서 가용성 영역을 물리적으로 구분하면 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다.

자세한 내용은 [Azure에서 가용성 영역이란?][az-overview]을 참조하세요.

가용성 영역을 사용하여 배포된 AKS 클러스터는 단일 지역 내의 여러 영역에 노드를 배포할 수 있습니다. 예를 들어  *미국 동부 2* 지역의 클러스터는 *미국 동부 2* 의 모든 3개 가용성 영역에 노드를 만들 수 있습니다. 이 AKS 클러스터 리소스 배포는 특정 영역의 오류에 대한 복원력을 제공하므로 클러스터 가용성이 향상됩니다.

![가용성 영역 간 AKS 노드 배포](media/availability-zones/aks-availability-zones.png)

단일 영역을 사용할 수 없게 되면 클러스터가 여러 영역에 분산된 경우에도 애플리케이션이 계속 실행됩니다.

## <a name="create-an-aks-cluster-across-availability-zones"></a>가용성 영역에 AKS 클러스터 만들기

[az aks create][az-aks-create] 명령을 사용하여 클러스터를 만들 때 `--zones` 매개 변수는 배포할 영역 에이전트 노드를 정의합니다. 클러스터를 만들 때 `--zones` 매개 변수를 정의하면 etcd 또는 API와 같은 컨트롤 플레인 구성 요소가 지역에서 사용 가능한 영역에 분산됩니다. 컨트롤 플레인 구성 요소가 분산된 특정 영역은 초기 노드 풀에 선택되는 명시적 영역과 별개입니다.

AKS 클러스터를 만들 때 기본 에이전트 풀에 대한 영역을 정의하지 않으면 컨트롤 플레인 구성 요소가 가용성 영역에 분산되지 않을 수 있습니다. [az aks nodepool add][az-aks-nodepool-add] 명령을 사용하여 노드 풀을 더 추가하고 새 노드에 `--zones`를 지정할 수 있지만, 컨트롤 플레인이 영역에 분산된 방식은 변경되지 않습니다. 가용성 영역 설정은 클러스터 또는 노드 풀 생성 시에만 정의할 수 있습니다.

다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myAKSCluster* 라는 AKS 클러스터를 만듭니다. 총 *3* 개의 노드가 생성되는데, 영역 *1*, *2*, *3* 에 에이전트가 하나씩 생성됩니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

AKS 클러스터를 만드는 데 몇 분이 걸립니다.

새 노드가 속해야 하는 영역을 결정할 때 지정된 AKS 노드 풀은 [기본 Azure Virtual Machine Scale Sets에서 제공하는 최상의 영역 분산][vmss-zone-balancing]을 사용합니다. 지정된 AKS 노드 풀은 각 영역에 동일한 수의 VM이 있거나 확장 집합의 다른 모든 영역에서 VM이 +\- 1이면 "균형"으로 간주됩니다.

## <a name="verify-node-distribution-across-zones"></a>영역 간 노드 배포 확인

클러스터가 준비되면 확장 집합의 에이전트 노드를 나열하여 해당 노드가 배포된 가용성 영역을 확인합니다.

먼저 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 AKS 클러스터의 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

그런 다음 [kubectl 설명][kubectl-describe] 명령을 사용하여 클러스터의 노드를 나열하고 *failure-domain.beta.kubernetes.io/zone* 값을 필터링합니다. 다음은 Bash 셸에 대한 예입니다.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

다음 예제 출력은 첫 번째 가용성 영역에 대한 *eastus2-1*, 두 번째 가용성 영역에 대한 *eastus2-2* 와 같이 지정된 지역 및 가용성 영역에 분산된 세 개의 노드를 보여 줍니다.

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

에이전트 풀에 노드를 더 추가하면 Azure 플랫폼에서 기본 VM을 지정된 가용성 영역에 자동으로 배포합니다.

최신 Kubernetes 버전(1.17.0 이상)에서 AKS는 더 이상 사용되지 않는 `failure-domain.beta.kubernetes.io/zone` 외에 최신 레이블 `topology.kubernetes.io/zone`도 사용합니다. 다음 스크립트를 실행하여 위와 동일한 결과를 얻을 수 있습니다.

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

더 간결한 출력을 제공합니다.

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>영역 간 Pod 배포 확인

[잘 알려진 레이블, 주석 및 Taint][kubectl-well_known_labels]에 설명된 대로, Kubernetes는 `failure-domain.beta.kubernetes.io/zone` 레이블을 사용하여 사용 가능한 여러 영역에 복제 컨트롤러 또는 서비스에 Pod를 자동으로 배포합니다. 이를 테스트하려면 클러스터를 3개 노드에서 5개 노드로 스케일 업하여 올바른 Pod 분산을 확인하면 됩니다.

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

몇 분 후에 스케일링 작업이 완료되면 Bash 셸의 `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` 명령이 이 샘플과 유사한 출력을 제공합니다.

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

이제 영역 1과 2에 두 개의 추가 노드가 있습니다. 3개의 복제본으로 구성된 애플리케이션을 배포할 수 있습니다. NGINX를 예로 들어 보겠습니다.

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

Pod가 실행되는 노드를 보면 3개의 다른 가용성 영역에 해당하는 노드에서 Pod가 실행되고 있는 것을 볼 수 있습니다. 예를 들어 Bash 셸의 `kubectl describe pod | grep -e "^Name:" -e "^Node:"` 명령을 사용하여 다음과 유사한 출력을 얻을 수 있습니다.

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

이전 출력에서 볼 수 있듯이, 첫 번째 Pod는 가용성 영역 `eastus2-1`에 있는 노드 0에서 실행됩니다. 두 번째 Pod는 `eastus2-3`에 해당하는 노드 2에서 실행되며, 세 번째 Pod는 `eastus2-2`의 노드 4에서 실행됩니다. 추가 구성 없이도 Kubernetes는 세 가지 가용성 영역 모두에 Pod를 올바르게 분산합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역을 사용하는 AKS 클러스터를 만드는 방법을 자세히 설명했습니다. 고가용성 클러스터의 추가 고려 사항은 [AKS의 비즈니스 연속성 및 재해 복구를 위한 모범 사례][best-practices-bc-dr]를 참조하세요.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing
[arm-template-null]: ../azure-resource-manager/templates/template-expressions.md#null-values

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
