---
title: AKS(Azure Kubernetes Service)에서 여러 노드 풀 사용
description: AKS(Azure Kubernetes Service)에서 클러스터에 대한 여러 노드 풀을 만들고 관리하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: 19e10b43fbf0dac05da531570376b77d22840312
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538819"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 클러스터에 대한 여러 노드 풀 만들기 및 관리

AKS(Azure Kubernetes Service)에서 동일한 구성의 노드가 *노드 풀* 에 하나로 그룹화됩니다. 이러한 노드 풀에는 애플리케이션을 실행하는 기본 VM이 포함됩니다. 노드의 초기 수와 크기(SKU)는 [시스템 노드 풀][use-system-pool]을 만드는 AKS 클러스터를 만들 때 정의됩니다. 컴퓨팅 또는 스토리지 수요가 다른 애플리케이션을 지원하기 위해 추가 *사용자 노드 풀* 을 만들 수 있습니다. 시스템 노드 풀의 기본 목적은 CoreDNS 및 tunnelfront 같은 중요한 시스템 Pod를 호스트하는 것입니다. 사용자 노드 풀은 애플리케이션 Pod를 호스트하는 기본 목적을 제공합니다. 하지만 AKS 클러스터에 풀을 하나만 포함하려는 경우 시스템 노드 풀에 애플리케이션 Pod를 예약할 수 있습니다. 사용자 노드 풀은 애플리케이션 관련 Pod를 배치하는 위치입니다. 예를 들어 추가 사용자 노드 풀을 사용하여 컴퓨팅 집약적인 애플리케이션을 위한 GPU를 제공하거나 고성능 SSD 스토리지에 액세스할 수 있습니다.

> [!NOTE]
> 이 기능을 사용하면 여러 노드 풀을 만들고 관리하는 방법을 보다 효과적으로 제어할 수 있습니다. 결과적으로 create/update/delete에 대한 별도의 명령이 필요합니다. 이전에는 `az aks create` 또는 `az aks update`를 통한 클러스터 작업에서 managedCluster API를 사용했으며 컨트롤 플레인과 단일 노드 풀을 변경하는 유일한 옵션이었습니다. 이 기능은 agentPool API를 통해 에이전트 풀에 대한 별도의 작업 세트를 노출하며 개별 노드 풀에서 작업을 실행하려면 `az aks nodepool` 명령 세트를 사용해야 합니다.

이 문서에서는 AKS 클러스터에서 여러 노드 풀을 만들고 관리하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 2.2.0 이상 버전을 설치하고 구성해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="limitations"></a>제한 사항

여러 노드 풀을 지원하는 AKS 클러스터를 만들고 관리하는 경우 다음과 같은 제한 사항이 적용됩니다.

* [AKS(Azure Kubernetes Service)의 할당량, 가상 머신 크기 제한 및 지역 가용성][quotas-skus-regions]을 참조하세요
* AKS 클러스터에서 수행할 다른 시스템 노드 풀이 있으면 시스템 노드 풀을 삭제합니다.
* 시스템 풀에 하나 이상의 노드가 포함되어야 하고, 사용자 노드 풀에는 0개 이상의 노드가 포함될 수 있습니다.
* AKS 클러스터는 표준 SKU 부하 분산 장치를 사용하여 여러 노드 풀을 사용해야 합니다. 이 기능은 기본 SKU 부하 분산 장치에서는 지원되지 않습니다.
* AKS 클러스터는 노드에 대한 가상 머신 확장 집합을 사용해야 합니다.
* 노드 풀의 이름은 영숫자 소문자만 포함할 수 있고 소문자 문자로 시작해야 합니다. Linux 노드 풀의 길이는 1~12자여야 하고, Windows 노드 풀의 길이는 1~6자여야 합니다.
* 모든 노드 풀은 동일한 가상 네트워크에 있어야 합니다.
* 클러스터를 만들 때 여러 노드 풀을 만드는 경우 노드 풀에서 사용하는 모든 Kubernetes 버전은 컨트롤 플레인에 대해 설정된 버전과 일치해야 합니다. 이는 클러스터를 프로비전한 후 노드별 풀 작업을 사용하여 업데이트할 수 있습니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

> [!Important]
> 프로덕션 환경에서 AKS 클러스터에 대해 단일 시스템 노드 풀을 실행할 경우 노드 풀에 대해 노드를 3개 이상 사용하는 것이 좋습니다.

시작하려면 노드 풀이 하나인 AKS 클러스터를 만듭니다. 다음 예제에서는 [az group create][az-group-create] 명령을 사용하여 *eastus* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 그런 다음, [az aks create][az-aks-create] 명령을 사용하여 *myAKSCluster* 라는 AKS 클러스터를 만듭니다.

> [!NOTE]
> 여러 노드 풀을 사용하는 경우 *기본* 부하 분산 장치 SKU가 **지원되지 않습니다**. 기본적으로 AKS 클러스터는 Azure CLI 및 Azure Portal에서 *표준* 부하 분산 장치 SKU를 사용하여 생성됩니다.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

클러스터를 만드는 데 몇 분이 걸립니다.

> [!NOTE]
> 클러스터가 안정적으로 작동하려면 기본 노드 풀에서 2개 이상의 노드를 실행해야 합니다. 이 노드 풀에서 필수 시스템 서비스가 실행되고 있기 때문입니다.

클러스터가 준비되면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 `kubectl`에 사용할 클러스터 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>노드 풀 추가

이전 단계에서 만든 클러스터에는 노드 풀이 하나 있습니다. [az aks nodepool add][az-aks-nodepool-add] 명령을 사용하여 두 번째 노드 풀을 추가해 보겠습니다. 다음 예제에서는 *3개* 노드를 실행하는 *mynodepool* 이라는 노드 풀을 만듭니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> 노드 풀의 이름은 소문자로 시작해야 하며 영숫자 문자만 포함할 수 있습니다. Linux 노드 풀의 길이는 1~12자여야 하고, Windows 노드 풀의 길이는 1~6자여야 합니다.

노드 풀의 상태를 보려면 [az aks node pool list][az-aks-nodepool-list] 명령을 사용하여 리소스 그룹 및 클러스터 이름을 지정합니다.

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

다음 예제 출력은 노드 풀에 3개의 노드가 있는 *mynodepool* 을 성공적으로 만들었음을 보여줍니다. 이전 단계에서 AKS 클러스터를 만들 때 노드가 *2* 개인 기본 *nodepool1* 이 만들어졌습니다.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> 노드 풀을 추가할 때 *VmSize* 를 지정하지 않으면 Windows 노드 풀의 기본 크기는 *Standard_D2s_v3* 이고 Linux 노드 풀의 기본 크기는 *Standard_DS2_v2* 입니다. *OrchestratorVersion* 을 지정하지 않으면 컨트롤 플레인과 동일한 버전이 기본값으로 지정됩니다.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>고유한 서브넷을 사용하는 노드 풀 추가(미리 보기)

논리적 격리를 위해 워크로드에서 클러스터의 노드를 별도의 풀로 분할해야 하는 경우가 있습니다. 이러한 격리를 지원하려면 클러스터의 각 노드 풀 전용으로 사용되는 별도의 서브넷이 필요합니다. 이렇게 하면 불연속 가상 네트워크 주소 공간을 노드 풀 간에 분할해야 하는 등의 요구 사항을 해결할 수 있습니다.

#### <a name="limitations"></a>제한 사항

* 노드 풀에 할당된 모든 서브넷은 동일한 가상 네트워크에 속해야 합니다.
* 시스템 Pod는 클러스터의 모든 노드/Pod에 대한 액세스 권한이 있어야만 DNS 확인 및 터널링 kubectl logs/exec/port-forward 프록시와 같은 중요한 기능을 제공할 수 있습니다.
* 클러스터를 만든 후 VNET을 확장하는 경우 원래 cidr 외부에 서브넷을 추가하기 전에 클러스터를 업데이트해야 합니다(관리형 클러스터 작업을 수행하지만 노드 풀 작업은 카운트하지 않음). 이제 AKS는 처음에 허용했던 에이전트 풀 추가에 대해 오류를 표시합니다. 클러스터 파일을 조정하는 방법을 모르는 경우 지원 티켓을 제출하세요. 
* Calico 네트워크 정책은 지원되지 않습니다. 
* Azure 네트워크 정책은 지원되지 않습니다.
* Kube-proxy는 단일 연속 cidr을 필요로 하며 다음과 같은 세 가지 최적화에 단일 연속 cidr을 사용합니다. 자세한 내용은 [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) 및 --cluster-cidr 관련 정보가 있는 [여기](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/)서 확인할 수 있습니다. Azure CNI에서 첫 번째 노드 풀의 서브넷이 kube-proxy에 제공됩니다. 

전용 서브넷을 사용하여 노드 풀을 만들려면 노드 풀을 만들 때 서브넷 리소스 ID를 추가 매개 변수로 전달합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>노드 풀 업그레이드

> [!NOTE]
> 클러스터 또는 노드 풀에서 업그레이드 및 스케일링 작업을 동시에 수행할 수 없으며, 시도하면 오류가 반환됩니다. 대상 리소스에서 각 작업 유형이 완료되어야만 동일한 리소스에 대한 다음 요청이 수행됩니다. 이에 대한 자세한 내용은 [문제 해결 가이드](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)를 참조하세요.

이 섹션의 명령은 단일 특정 노드 풀을 업그레이드하는 방법을 설명합니다. Kubernetes 버전의 컨트롤 플레인과 노드 풀 업그레이드 간의 관계는 [아래 섹션](#upgrade-a-cluster-control-plane-with-multiple-node-pools)에 설명되어 있습니다.

> [!NOTE]
> 노드 풀 OS 이미지 버전은 클러스터의 Kubernetes 버전에 연결됩니다. 클러스터 업그레이드 후에는 OS 이미지 업그레이드만 받게 됩니다.

이 예제에서는 두 개의 노드 풀이 있으므로 [az aks nodepool upgrade][az-aks-nodepool-upgrade]를 사용하여 노드 풀을 업그레이드해야 합니다. 사용 가능한 업그레이드를 확인하려면 [az aks get-upgrades][az-aks-get-upgrades]를 사용합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

*mynodepool* 을 업그레이드하겠습니다. 다음 예제와 같이 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 명령을 사용하여 노드 풀을 업그레이드합니다.

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

[az aks node pool list][az-aks-nodepool-list] 명령을 사용하여 노드 풀의 상태를 다시 나열합니다. 다음 예제에서는 *mynodepool* 이 *KUBERNETES_VERSION* 으로 *업그레이드 중* 이라는 것을 보여줍니다.

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

노드를 지정된 버전으로 업그레이드하는 데 몇 분 정도 걸립니다.

AKS 클러스터의 모든 노드 풀을 동일한 Kubernetes 버전으로 업그레이드하는 것이 가장 좋습니다. `az aks upgrade`의 기본 동작은 모든 노드 풀을 컨트롤 플레인과 함께 업그레이드하여 이렇게 맞추는 것입니다. 개별 노드 풀을 업그레이드하는 기능을 사용하면 롤링 업그레이드를 수행하고 노드 풀 간에 Pod 일정을 예약하여 위에서 언급한 제약 조건 내에서 애플리케이션 작동 시간을 유지할 수 있습니다

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>클러스터 컨트롤 플레인을 여러 노드 풀로 업그레이드

> [!NOTE]
> Kubernetes는 표준 [유의적 버전](https://semver.org/) 버전 관리 체계를 사용합니다. 버전 번호는 *x.y.z* 로 표시되며 *x* 는 주 버전, *y* 는 부 버전, *z* 는 패치 버전입니다. 예를 들어 버전 *1.12.6* 에서 1은 주 버전, 12는 부 버전, 6은 패치 버전입니다. Kubernetes 버전의 컨트롤 플레인과 초기 노드 풀은 클러스터를 만드는 동안 설정됩니다. 모든 추가 노드 풀은 클러스터에 추가될 때 Kubernetes 버전이 설정됩니다. Kubernetes 버전은 노드 풀 간에 그리고 노드 풀과 컨트롤 플레인 간에 서로 다를 수 있습니다.

AKS 클러스터에는 Kubernetes 버전이 연결된 두 개의 클러스터 리소스 개체가 있습니다.

1. 클러스터 컨트롤 플레인 Kubernetes 버전
2. Kubernetes 버전을 사용하는 노드 풀

컨트롤 플레인은 하나 이상의 노드 풀에 매핑됩니다. 업그레이드 작업의 동작은 사용하는 Azure CLI 명령에 따라 달라집니다.

AKS 컨트롤 플레인을 업그레이드하려면 `az aks upgrade`를 사용해야 합니다. 이 명령은 컨트롤 플레인과 클러스터의 모든 노드 풀을 업그레이드합니다.

`--control-plane-only` 플래그를 사용하여 `az aks upgrade` 명령을 실행하면 클러스터 컨트롤 플레인만 업그레이드됩니다. 클러스터의 연결된 노드 풀은 하나도 변경되지 않았습니다.

개별 노드 풀을 업그레이드하려면 `az aks nodepool upgrade` 명령을 사용해야 합니다. 이 명령은 지정된 Kubernetes 버전을 사용하여 대상 노드 풀만 업그레이드합니다.

### <a name="validation-rules-for-upgrades"></a>업그레이드 유효성 검사 규칙

클러스터의 컨트롤 플레인과 노드 풀의 유효한 Kubernetes 업그레이드는 다음 규칙 세트를 통해 유효성이 검사됩니다.

* 노드 풀을 업그레이드하는 데 유효한 버전에 대한 규칙:
   * 노드 풀 버전은 컨트롤 플레인과 동일한 *주* 버전이어야 합니다.
   * 노드 풀 *부* 버전은 컨트롤 플레인 버전의 두 가지 *부* 버전 내에 있어야 합니다.
   * 노드 풀 버전은 컨트롤 `major.minor.patch` 버전보다 크면 안 됩니다.

* 업그레이드 작업 제출에 대한 위한 규칙:
   * 컨트롤 플레인 또는 노드 풀 Kubernetes 버전을 다운그레이드할 수 없습니다.
   * 노드 풀 Kubernetes 버전을 지정하지 않으면 사용하는 클라이언트에 따라 동작이 달라집니다. Resource Manager 템플릿의 선언은 노드 풀에 대해 정의된 기존 버전(사용한 경우)으로 대체됩니다. 설정된 항목이 없으면 컨트롤 플레인 버전을 사용하여 대체됩니다.
   * 지정된 시간에 컨트롤 플레인 또는 노드 풀을 업그레이드하거나 스케일링할 수 있으며, 단일 컨트롤 플레인 또는 노드 풀 리소스에서 동시에 여러 작업을 제출할 수 없습니다.

## <a name="scale-a-node-pool-manually"></a>노드 풀 수동 스케일링

애플리케이션 워크로드 수요가 변하면 그에 따라 노드 풀의 노드 수를 스케일링해야 할 수 있습니다. 노드 수를 스케일 업 또는 다운할 수 있습니다.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

노드 풀의 노드 수를 스케일링하려면 [az aks node pool scale][az-aks-nodepool-scale] 명령을 사용합니다. 다음 예제에서는 *mynodepool* 의 노드 수를 *5* 로 스케일링합니다.

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

[az aks node pool list][az-aks-nodepool-list] 명령을 사용하여 노드 풀의 상태를 다시 나열합니다. 다음 예제는 *mynodepool* 의 노드 수를 *5개* 로 *스케일링 중* 임을 보여줍니다.

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

스케일링 작업이 완료될 때까지 몇 분 정도 걸립니다.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>클러스터 자동 스케일링을 사용하여 특정 노드 풀을 자동으로 스케일링

AKS는 [클러스터 자동 스케일링](cluster-autoscaler.md)이라는 기능을 사용하여 노드 풀을 자동으로 스케일링하는 별도의 기능을 제공합니다. 노드 풀마다 고유한 최소 및 최대 스케일링 수를 지정하여 이 기능을 사용할 수 있습니다. [노드 풀마다 클러스터 자동 스케일링 사용](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) 방법을 알아보세요.

## <a name="delete-a-node-pool"></a>노드 풀 삭제

풀이 더 이상 필요 없으면 풀을 삭제하고 기본 VM 노드를 제거할 수 있습니다. 노드 풀을 삭제하려면 [az aks node pool delete][az-aks-nodepool-delete] 명령을 사용하여 노드 풀 이름을 지정합니다. 다음 예제에서는 이전 단계에서 만든 *mynoodepool* 을 삭제합니다.

> [!CAUTION]
> 노드 풀을 삭제할 때 발생할 수 있는 데이터 손실을 복구하는 옵션은 없습니다. 다른 노드 풀에서 Pod를 예약할 수 없는 경우 해당 애플리케이션을 사용할 수 없습니다. 사용 중인 애플리케이션에 데이터 백업이 없거나 클러스터의 다른 노드 풀에서 실행할 수 없는 경우 노드 풀을 삭제하면 안 됩니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

다음 예제의 [az aks node pool list][az-aks-nodepool-list] 명령 출력은 *mynodepool* 이 *삭제 중* 상태임을 보여줍니다.

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

노드 및 노드 풀을 삭제하는 데 몇 분 정도 걸립니다.

## <a name="specify-a-vm-size-for-a-node-pool"></a>노드 풀의 VM 크기 지정

이전 예제에서 노드 풀을 만들 때 기본 VM 크기를 사용하여 클러스터에 노드를 만들었습니다. 보다 일반적인 시나리오는 VM 크기 및 기능이 서로 다른 노드 풀을 만드는 것입니다. 예를 들어 노드의 CPU 또는 메모리 양이 많은 노드 풀 또는 GPU 지원을 제공하는 노드 풀을 만들 수 있습니다. 다음 단계에서는 [taints 및 tolerations를 사용](#setting-nodepool-taints)하여 이러한 노드에서 실행할 수 있는 Pod에 대한 액세스를 제한하는 방법을 Kubernetes 스케줄러에 알립니다.

다음 예제에서는 *Standard_NC6* VM 크기를 사용하는 GPU 기반 노드 풀을 만듭니다. 이러한 VM은 NVIDIA Tesla K80 카드로 구동됩니다. 사용 가능한 VM 크기에 대한 자세한 내용은 [Azure에서 Linux 가상 머신에 대한 크기][vm-sizes]를 참조하세요.

[az aks node pool add][az-aks-nodepool-add] 명령을 다시 사용하여 노드 풀을 만듭니다. 이번에는 *gpunodepool* 을 이름으로 지정하고, `--node-vm-size` 매개 변수를 사용하여 *Standard_NC6* 크기를 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

다음 예제의 [az aks node pool list][az-aks-nodepool-list] 명령 출력은 *gpunodepool* 에서 지정된 *VmSize* 를 사용하여 노드를 *만드는 중* 임을 보여줍니다.

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

*gpunodepool* 을 만드는 데 몇 분 정도 걸립니다.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>노드 풀의 taint, 레이블 또는 태그 지정

노드 풀을 만들 때 해당 노드 풀에 taint, 레이블 또는 태그를 추가할 수 있습니다. taint, 레이블 또는 태그를 추가하면 해당 노드 풀의 모든 노드에도 해당 taint, 레이블 또는 태그가 지정됩니다.

> [!IMPORTANT]
> `az aks nodepool`을 사용하여 전체 노드 풀에 대해 taint, 레이블 또는 태그를 노드에 추가해야 합니다. `kubectl`을 사용하여 노드 풀의 개별 노드에 taint, 레이블 또는 태그를 적용하지 않는 것이 좋습니다.  

### <a name="setting-nodepool-taints"></a>노드 풀 taint 설정

taint를 사용하여 노드 풀을 만들려면 [az aks nodepool add][az-aks-nodepool-add] 명령을 사용합니다. 이름으로 *taintnp* 를 지정하고 `--node-taints` 매개 변수를 사용하여 taint에 대해 *sku=gpu:NoSchedule* 을 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> 노드 풀을 만드는 동안 노드 풀에 대해서만 taint를 설정할 수 있습니다.

다음 예제의 [az aks nodepool list][az-aks-nodepool-list] 명령 출력은 *taintnp* 에서 지정된 *nodeTaints* 를 사용하여 노드를 *만드는 중* 임을 보여줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

taint 정보는 노드의 예약 규칙을 처리하기 위해 Kubernetes에 표시됩니다. Kubernetes 스케줄러는 taint 및 toleration을 사용하여 노드에서 실행할 수 있는 워크로드를 제한할 수 있습니다.

* **Taint** 는 노드에서 특정 Pod만 예약할 수 있음을 나타내는 노드에 적용됩니다.
* **Toleration** 은 노드의 오류를 ‘허용’할 수 있도록 하는 Pod에 적용됩니다.

고급 Kubernetes 예약 기능을 사용하는 방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능에 대한 모범 사례][taints-tolerations]를 참조하세요.

이전 단계에서는 노드 풀을 만들 때 *sku=gpu:NoSchedule* taint를 적용했습니다. 다음 기본 예제 YAML 매니페스트는 toleration을 사용하여 Kubernetes 스케줄러가 해당 노드 풀의 노드에서 NGINX Pod를 실행할 수 있도록 허용합니다.

`nginx-toleration.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

`kubectl apply -f nginx-toleration.yaml` 명령을 사용하여 Pod를 예약합니다.

```console
kubectl apply -f nginx-toleration.yaml
```

Pod를 예약하고 NGINX 이미지를 끌어오는 데 몇 초 정도 걸립니다. [kubectl describe pod][kubectl-describe] 명령을 사용하여 Pod 상태를 살펴봅니다. 압축된 다음 예제 출력은 *sku=gpu:NoSchedule* toleration이 적용된 것을 보여줍니다. 이벤트 섹션에서 스케줄러가 *aks-taintnp-28993262-vmss000000* 노드에 Pod를 할당했습니다.

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

이 toleration이 적용된 Pod만 *taintnp* 의 노드에서 예약할 수 있습니다. 그 외의 Pod는 *nodepool1* 노드 풀에 예약됩니다. 추가 노드 풀을 만드는 경우 추가 taint 및 toleration을 사용하여 해당 노드 리소스에서 예약할 수 있는 Pod를 제한할 수 있습니다.

### <a name="setting-nodepool-labels"></a>노드 풀 레이블 설정

노드 풀을 만드는 동안 노드 풀에 레이블을 추가할 수도 있습니다. 노드 풀에 설정된 레이블은 노드 풀의 각 노드에 추가됩니다. 이러한 레이블은 노드에 대한 일정 규칙을 처리하기 위해 [Kubernetes에 표시][kubernetes-labels]됩니다.

레이블을 사용하여 노드 풀을 만들려면 [az aks nodepool add][az-aks-nodepool-add] 명령을 사용합니다. 이름을 *labelnp* 로 지정하고 `--labels` 매개 변수를 사용하여 레이블에 대해 *dept=IT* 및 *costcenter=9999* 를 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> 노드 풀을 만드는 동안 노드 풀에 대해서만 레이블을 설정할 수 있습니다. 또한 레이블은 키/값 쌍이어야 하며 [유효한 구문][kubernetes-label-syntax]을 포함해야 합니다.

다음 예제의 [az aks nodepool list][az-aks-nodepool-list] 명령 출력은 *labelnp* 에서 지정된 *nodeLabels* 를 사용하여 노드를 *만드는 중* 임을 보여줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>노드 풀 Azure 태그 설정

AKS 클러스터의 노드 풀에 Azure 태그를 적용할 수 있습니다. 노드 풀에 적용되는 태그는 노드 풀 내의 각 노드에도 적용되며 업그레이드를 통해 유지됩니다. 태그는 스케일 아웃 작업 중에 노드 풀에 추가된 새 노드에도 적용됩니다. 태그를 추가하면 정책 추적 또는 비용 예측과 같은 작업에 도움이 됩니다.

Azure 태그에는 대/소문자를 구분하지 않는 키가 있으며, 키를 선택하여 태그를 검색하는 경우와 비슷한 작업에 사용됩니다. 이 경우 대/소문자에 관계 없이 지정된 키를 가진 태그가 업데이트 또는 검색됩니다. 태그 값은 대/소문자를 구분합니다.

AKS에서 여러 태그가 동일한 키로 설정되고 대/소문자가 다른 경우 태그는 사전순으로 사용됩니다. 예를 들어 `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}`은 `Key1` 및 `val1`이 설정됩니다.

[az aks nodepool add][az-aks-nodepool-add]를 사용하여 노드 풀을 만듭니다. 이름을 *tagnodepool* 로 지정하고 `--tag` 매개 변수를 사용하여 태그에 대해 *dept=IT* 및 *costcenter=9999* 를 지정합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> [az aks nodepool update][az-aks-nodepool-update] 명령을 사용할 때와 클러스터를 만들 때에도 `--tags` 매개 변수를 사용할 수 있습니다. 클러스터를 만드는 동안 `--tags` 매개 변수는 클러스터를 사용하여 만든 초기 노드 풀에 태그를 적용합니다. 모든 태그 이름은 [태그를 사용하여 Azure 리소스 구성][tag-limitation]의 제한 사항을 준수해야 합니다. `--tags` 매개 변수를 사용하여 노드 풀을 업데이트하면 기존 태그 값이 업데이트되고 새 태그가 추가됩니다. 예를 들어 노드 풀의 태그에 대해 *dept=IT* 및 *costcenter=9999* 가 설정되었는데 이를 *team=dev* 및 *costcenter=111* 로 업데이트한 경우 노드 풀의 태그는 *dept=IT*, *costcenter=111*, *team=dev* 로 설정됩니다.

다음 예제의 [az aks nodepool list][az-aks-nodepool-list] 명령 출력은 *tagnodepool* 에서 지정된 *tag* 를 사용하여 노드를 *만드는 중* 임을 보여줍니다.

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="add-a-fips-enabled-node-pool-preview"></a>FIPS 사용 노드 풀 추가(미리 보기)

FIPS(Federal Information Processing Standard) 140-2는 정보 기술 제품 및 시스템에서 암호화 모듈에 대한 최소 보안 요구 사항을 정의하는 미국 정부 표준입니다. AKS를 사용하면 FIPS 140-2를 사용하는 Linux 기반 노드 풀을 만들 수 있습니다. FIPS 사용 노드 풀에서 실행되는 배포는 이러한 암호화 모듈을 사용하여 향상된 보안을 제공하고 FedRAMP 준수의 일환으로 보안 제어를 지원할 수 있습니다. FIPS 140-2에 대한 자세한 내용은 [FIPS(Federal Information Processing Standard) 140-2][fips]를 참조하세요.

FIPS 사용 노드 풀은 현재 미리 보기로 제공됩니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

*aks-preview* Azure CLI 확장 버전 *0.5.11* 이상이 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

기능을 사용하려면 구독에서도 `FIPSPreview` 기능 플래그를 사용하도록 설정해야 합니다.

`FIPSPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "FIPSPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/FIPSPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

FIPS 사용 노드 풀에는 다음과 같은 제한이 있습니다.

* 현재 Ubuntu 18.04에서 실행되는 FIPS 사용 Linux 기반 노드 풀만 사용할 수 있습니다.
* FIPS 사용 노드 풀에는 Kubernetes 버전 1.19 이상이 필요합니다.
* FIPS에 사용되는 기본 패키지 또는 모듈을 업데이트하려면 [노드 이미지 업그레이드][node-image-upgrade]를 사용해야 합니다.

> [!IMPORTANT]
> FIPS 사용 Linux 이미지는 Linux 기반 노드 풀에 사용되는 기본 Linux 이미지와 다른 이미지입니다. 노드 풀에서 FIPS를 사용하도록 설정하려면 새 Linux 기반 노드 풀을 만들어야 합니다. 기존 노드 풀에서는 FIPS를 사용하도록 설정할 수 없습니다.
> 
> FIPS 사용 노드 이미지는 FIPS를 사용하지 않는 이미지와 같은 다른 버전 번호(예: 커널 버전)를 포함할 수 있습니다. 또한 FIPS 사용 노드 풀 및 노드 이미지의 업데이트 주기는 FIPS를 사용하지 않는 노드 풀 및 이미지와 다를 수 있습니다.

FIPS 지원 노드 풀을 만들려면 노드 풀을 만들 때 *--enable-fips-image* 매개 변수와 함께 [az aks nodepool add][az-aks-nodepool-add]를 사용합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name fipsnp \
    --enable-fips-image
```

> [!NOTE]
> 기본 노드 풀에서 FIPS를 사용하도록 클러스터를 만들 때 [az aks create][az-aks-create]와 함께 *--enable-fips-image* 매개 변수를 사용할 수도 있습니다. 이러한 방식으로 생성된 클러스터에 노드 풀을 추가할 때 FIPS 지원 노드 풀을 생성하기 위해 노드 풀을 추가할 때 *--enable-fips-image* 매개 변수를 사용해야 합니다.

노드 풀이 FIPS를 사용하는지 확인하려면 [az aks show][az-aks-show]를 사용하여 *agentPoolProfiles* 에서 *enableFIPS* 값을 확인하세요.

```azurecli-interactive
az aks show --resource-group myResourceGroup --cluster-name myAKSCluster --query="agentPoolProfiles[].{Name:name enableFips:enableFips}" -o table
```

다음 출력 예는 *fipsnp* 노드 풀이 FIPS를 사용하고 *nodepool1* 이 사용되지 않음을 보여 줍니다.

```output
Name       enableFips
---------  ------------
fipsnp     True
nodepool1  False  
```

또한 배치가 FIPS 사용 노드 풀의 노드에서 `kubectl debug`를 사용하여 FIPS 암호화 라이브러리에 액세스할 수 있는지 확인할 수 있습니다. `kubectl get nodes`를 사용하여 노드를 나열합니다.

```output
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE     VERSION
aks-fipsnp-12345678-vmss000000      Ready    agent   6m4s    v1.19.9
aks-fipsnp-12345678-vmss000001      Ready    agent   5m21s   v1.19.9
aks-fipsnp-12345678-vmss000002      Ready    agent   6m8s    v1.19.9
aks-nodepool1-12345678-vmss000000   Ready    agent   34m     v1.19.9
```

위의 예에서 `aks-fipsnp`로 시작하는 노드는 FIPS 사용 노드 풀의 일부입니다. `kubectl debug`를 사용하여 FIPS 사용 노드 풀의 해당 노드 중 하나에서 대화형 세션으로 배포를 실행합니다.

```azurecli-interactive
kubectl debug node/aks-fipsnp-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

대화형 세션에서 FIPS 암호화 라이브러리를 사용할 수 있는지 확인할 수 있습니다.

```output
root@aks-fipsnp-12345678-vmss000000:/# cat /proc/sys/crypto/fips_enabled
1
```

FIPS 지원 노드 풀에는 *kubernetes.azure.com/fips_enabled=true* 레이블도 있으며, 해당 노드 풀을 대상으로 하는 배포에 사용할 수 있습니다.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 노드 풀 관리

Azure Resource Manager 템플릿을 사용하여 리소스를 만들고 관리할 때 일반적으로 템플릿에서 설정을 업데이트하고 다시 배포하여 리소스를 업데이트할 수 있습니다. AKS의 노드 풀을 사용하는 경우 AKS 클러스터가 생성된 후에는 초기 노드 풀 프로필을 업데이트할 수 없습니다. 이 동작은 기존 Resource Manager 템플릿을 업데이트하고, 노드 풀을 변경하고, 다시 배포할 수 없음을 의미합니다. 대신 기존 AKS 클러스터의 노드 풀만 업데이트하는 별도의 Resource Manager 템플릿을 만들어야 합니다.

`aks-agentpools.json`과 같은 템플릿을 만들고 다음 예제 매니페스트를 붙여넣습니다. 이 예제 템플릿은 다음 설정을 구성합니다.

* 3개의 노드를 실행하도록 *myagentpool* 이라는 *Linux* 노드 풀을 업데이트합니다.
* Kubernetes 버전 *1.15.7* 을 실행하도록 노드 풀의 노드를 설정합니다.
* 노드 크기를 *Standard_DS2_v2* 로 정의합니다.

이러한 값을 필요한 대로 편집하여 노드 풀을 필요에 따라 업데이트, 추가 또는 삭제합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

다음 예제와 같이 [az deployment group create][az-deployment-group-create] 명령을 사용하여 이 템플릿을 배포합니다. 기존 AKS 클러스터 이름 및 위치를 묻는 메시지가 표시됩니다.

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 다음 예제와 같이 템플릿에서 *tag* 속성을 추가하여 노드 풀에 태그를 추가할 수 있습니다.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Resource Manager 템플릿에서 정의하는 노드 풀 설정 및 작업에 따라 AKS 클러스터를 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>노드 풀의 노드마다 공용 IP 할당

AKS 노드는 통신에 사용할 고유한 공용 IP 주소가 필요하지 않습니다. 그러나 시나리오에 따라 고유의 전용 공용 IP 주소를 수신하기 위해 노드 풀의 노드가 필요할 수 있습니다. 홉을 최소화하기 위해 콘솔에서 클라우드 가상 머신에 직접 연결해야 하는 게임 워크로드가 대표적인 시나리오입니다. 이 시나리오는 AKS에서 노드 공용 IP를 사용하여 구현할 수 있습니다.

먼저, 새로운 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

새 AKS 클러스터를 만들고 노드의 공용 IP를 연결합니다. 노드 풀의 각 노드는 고유한 공용 IP를 수신합니다. Virtual Machine Scale Set 인스턴스를 살펴보면 이를 확인할 수 있습니다.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

기존 AKS 클러스터의 경우 새 노드 풀을 추가하고 노드의 공용 IP를 연결할 수도 있습니다.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>공용 IP 접두사 사용

[공용 IP 접두사를 사용하면 여러 가지 이점][public-ip-prefix-benefits]이 있습니다. AKS에서는 새 클러스터를 만들거나 노드 풀을 추가할 때 `node-public-ip-prefix` 플래그를 사용하여 리소스 ID를 전달하면 노드에 기존 공용 IP 접두사의 주소를 사용할 수 있습니다.

먼저 [az network public-ip prefix create][az-public-ip-prefix-create]를 사용하여 공용 IP 접두사를 만듭니다.

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

출력을 확인하고 접두사의 `id`를 적어 둡니다.

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

마지막으로 새 클러스터를 만들거나 새 노드 풀을 추가할 때 `node-public-ip-prefix` 플래그를 사용하고 접두사의 리소스 ID를 전달합니다.

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>노드의 공용 IP 찾기

다음과 같은 다양한 방법으로 노드의 공용 IP를 찾을 수 있습니다.

* Azure CLI 명령 [az vmss list-instance-public-ips][az-list-ips]를 사용합니다.
* [PowerShell 또는 Bash 명령][vmss-commands]을 사용합니다. 
* Azure Portal에서 Virtual Machine Scale Set의 인스턴스를 확인하여 공용 IP를 보는 방법도 있습니다.

> [!Important]
> [노드 리소스 그룹][node-resource-group]에는 노드 및 노드의 공용 IP가 포함됩니다. 노드의 공용 IP를 찾는 명령을 실행할 때 노드 리소스 그룹을 사용합니다.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 GPU 기반 노드를 포함하는 AKS 클러스터를 만들었습니다. 불필요한 비용을 줄이기 위해 *gpunodepool* 또는 전체 AKS 클러스터를 삭제할 수 있습니다.

GPU 기반 노드 풀을 삭제하려면 다음 예제와 같이 [az aks nodepool delete][az-aks-nodepool-delete] 명령을 사용합니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

클러스터 자체를 삭제하려면 [az group delete][az-group-delete] 명령을 사용하여 AKS 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

노드 풀의 공용 IP 시나리오에 대해 만든 추가 클러스터도 삭제할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

[시스템 노드 풀][use-system-pool]에 대해 자세히 알아봅니다.

이 문서에서는 AKS 클러스터에서 여러 노드 풀을 만들고 관리하는 방법을 배웠습니다. 노드 풀에서 Pod를 제어하는 ​​방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능에 대한 모범 사례][operator-best-practices-advanced-scheduler]를 참조하세요.

Windows Server 컨테이너 노드 풀을 만들고 사용하려면 [AKS에서 Windows server 컨테이너 만들기][aks-windows]를 참조하세요.

[근접 배치 그룹][reduce-latency-ppg]을 사용하여 AKS 애플리케이션의 대기 시간을 줄입니다.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create
[node-image-upgrade]: node-image-upgrade.md
[fips]: /azure/compliance/offerings/offering-fips-140-2
