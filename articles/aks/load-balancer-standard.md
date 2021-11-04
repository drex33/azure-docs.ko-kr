---
title: 공용 부하 분산 장치 사용
titleSuffix: Azure Kubernetes Service
description: 표준 SKU가 포함된 공용 부하 분산 장치를 사용하여 AKS(Azure Kubernetes Service)를 통해 서비스를 노출하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d290549baf39f11c495c1f028a6eea2aba75d701
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576666"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 공용 표준 Load Balancer 사용

Azure Load Balancer는 인바운드 및 아웃바운드 시나리오를 모두 지원하는 OSI(개방형 시스템 연결) 모델의 L4에 있습니다. 부하 분산 장치의 프런트 엔드에 도착하는 인바운드 흐름을 백 엔드 풀 인스턴스에 분산합니다.

AKS와 통합된 **공용** 부하 분산 장치는 두 가지 용도로 사용됩니다.

1. AKS 가상 네트워크 내의 클러스터 노드에 아웃바운드 연결을 제공합니다. 노드 프라이빗 IP 주소를 해당 아웃바운드 풀의 일부인 공용 IP로 변환하여 이 목표를 달성합니다.
2. Kubernetes 서비스 형식 `LoadBalancer`를 통해 애플리케이션에 대한 액세스를 제공합니다. 이를 통해 손쉽게 애플리케이션 크기를 조정하고 고가용 서비스를 만들 수 있습니다.

**내부(또는 개인)** 부하 분산 장치는 개인 IP만 프런트 엔드로 허용되는 경우에 사용됩니다. 내부 부하 분산 장치는 트래픽 부하를 가상 네트워크 내에 분산하는 데 사용됩니다. 하이브리드 시나리오의 온-프레미스 네트워크에서 부하 분산 장치 프런트 엔드에 액세스할 수도 있습니다.

이 문서는 공용 부하 분산 장치와의 통합에 대해 설명합니다. 내부 부하 분산 장치 통합은 [AKS 내부 부하 분산 장치 설명서](internal-lb.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다.  기본적으로 표준 SKU는 AKS 클러스터를 만들 때 사용됩니다. 더 큰 백엔드 풀, [**여러 노드 풀**](use-multiple-node-pools.md), [**가용성 영역**](availability-zones.md)과 같이 추가된 기능에 대한 액세스 권한을 갖도록 표준 SKU를 사용합니다. 권장 AKS용 부하 분산 장치 SKU입니다.

‘기본’ 및 ‘표준’ SKU에 대한 자세한 정보는 [Azure 부하 분산 장치 SKU 비교][azure-lb-comparison]를 참조하세요. 

이 문서에서는 사용자에게 표준 SKU Azure Load Balancer 지원 AKS 클러스터가 있다고 가정하고, 부하 분산 장치의 일부 기능의 사용 및 구성 방법을 안내합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

> [!IMPORTANT]
> Azure Load Balancer를 활용하여 아웃바운드 연결을 제공하지 않고 대신 사용자 고유의 게이트웨이, 방화벽 또는 프록시를 사용하려는 경우, [**UDR(UserDefinedRouting) 아웃바운드 유형**](egress-outboundtype.md)을 사용하여 부하 분산 장치 아웃바운드 풀 및 해당 프런트엔드 IP를 만드는 것을 건너뛸 수 있습니다. 아웃바운드 유형은 클러스터에 대한 송신 메서드를 정의하며 기본값은 유형: 부하 분산 장치입니다.

## <a name="use-the-public-standard-load-balancer"></a>공용 표준 부하 분산 장치 사용

아웃바운드 유형: 부하 분산 장치(기본값)로 AKS 클러스터를 만들고 나면 클러스터에서 부하 분산 장치를 사용하여 서비스를 노출할 준비가 된 것입니다.

이를 위해 다음 예제와 같이 `LoadBalancer` 형식의 공용 서비스를 만들 수 있습니다. 먼저 `public-svc.yaml`이라는 서비스 매니페스트를 만듭니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

[kubectl apply][kubectl-apply]를 사용하여 공용 서비스 매니페스트를 배포하고 YAML 매니페스트의 이름을 지정합니다.

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure Load Balancer는 이 새로운 서비스에 대한 신규 공용 IP로 구성됩니다. Azure Load Balancer에는 여러 프런트엔드 IP가 있을 수 있으므로 새 서비스를 배포할 때마다 고유하게 액세스할 수 있는 새로운 전용 프런트엔드 IP가 제공됩니다.

다음 예제와 같이 실행하여 서비스를 만들고 부하 분산 장치를 구성했는지 확인할 수 있습니다.

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

서비스 세부 정보에서 볼 수 있듯 부하 분산 장치의 이 서비스에 대해 생성된 공용 IP 주소는 *EXTERNAL-IP* 열에 표시됩니다. 위의 예제와 같이 IP 주소가 *\<pending\>* 에서 실제 공용 IP 주소로 변경되는 데 1~2분이 소요될 수 있습니다.

## <a name="configure-the-public-standard-load-balancer"></a>공용 표준 부하 분산 장치 구성

표준 SKU 공용 부하 분산 장치를 사용하는 경우 만드는 시점에 사용자 지정하거나 클러스터를 업데이트해 사용자 지정할 수 있는 옵션 집합이 있습니다. 이러한 옵션을 사용하여 워크로드 요구 사항에 맞게 부하 분산 장치를 사용자 지정할 수 있으며 그에 따라 검토해야 합니다. 표준 부하 분산 장치를 사용하여 다음을 수행할 수 있습니다.

* 관리되는 아웃바운드 IP 수 설정 또는 크기 조정
* 사용자 지정 [아웃바운드 IP 또는 아웃바운드 IP 접두사](#provide-your-own-outbound-public-ips-or-prefixes) 가져오기
* 클러스터의 각 노드에 할당된 아웃바운드 포트 수 사용자 지정
* 유휴 연결에 대한 시간 제한 설정 구성

> [!IMPORTANT]
> 지정된 시간에 하나의 아웃바운드 IP 옵션(관리되는 IP, 사용자 자체 IP 또는 IP 접두사)만 사용할 수 있습니다.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>관리되는 아웃바운드 공용 IP 수 크기 조정

Azure Load Balancer는 가상 네트워크에서 인바운드 연결 외에도 아웃바운드 연결을 제공합니다. 아웃바운드 규칙을 사용하면 공용 표준 Load Balancer의 아웃바운드 네트워크 주소 변환을 쉽게 구성할 수 있습니다.

모든 Load Balancer 규칙과 마찬가지로, 아웃바운드 규칙은 부하 분산 및 인바운드 NAT 규칙과 동일한 친숙한 구문을 따릅니다.

***프런트엔드 IP + 매개 변수 + 백엔드 풀***

아웃바운드 규칙은 백 엔드 풀에서 식별된 모든 가상 머신에 대한 아웃바운드 NAT를 프런트 엔드로 변환하도록 구성합니다. 그리고 매개 변수는 아웃바운드 NAT 알고리즘에 대해 세분화된 제어를 추가로 제공합니다.

하나의 아웃바운드 규칙은 하나의 공용 IP 주소에만 사용할 수 있지만, 아웃바운드 규칙은 아웃바운드 NAT를 확장하기 위한 구성에 대한 부담을 덜어줍니다. 여러 IP 주소를 사용하여 대규모 시나리오를 계획하고, 아웃바운드 규칙을 사용하여 SNAT 고갈 가능성이 높은 경향의 패턴을 완화할 수 있습니다. 프런트 엔드에서 제공하는 추가 IP 주소 각각은 SNAT 포트로 사용할 Load Balancer에 대해 64,000개의 사용 후 삭제 포트를 제공합니다. 

기본적으로 생성된 관리 아웃바운드 공용 IP를 사용하는 표준 SKU 부하 분산 장치를 사용하는 경우 **`load-balancer-managed-ip-count`** 매개 변수를 사용하여 관리 아웃바운드 공용 IP 개수를 스케일링할 수 있습니다.

기존 클러스터를 업데이트하려면 다음 명령을 실행합니다. 이 매개 변수는 클러스터를 만들 때 여러 개의 관리 아웃바운드 공용 IP를 갖도록 설정될 수도 있습니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

위의 예제에서는 관리 아웃바운드 공용 IP 개수를 *myResourceGroup* 의 *myAKSCluster* 클러스터에 대해 *2* 로 설정합니다. 

또한 **`--load-balancer-managed-outbound-ip-count`** 매개 변수를 추가하고 원하는 값으로 설정하여 클러스터를 만들 때 **`load-balancer-managed-ip-count`** 매개 변수를 사용하여 관리 아웃바운드 공용 IP의 초기 개수를 설정할 수도 있습니다. 관리 아웃바운드 공용 IP의 기본 개수는 1입니다.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>사용자 고유 아웃바운드 공용 IP 또는 접두사 제공

표준 SKU 부하 분산 장치를 사용하는 경우, AKS 클러스터는 기본적으로 AKS 관리 인프라 리소스 그룹에 자동으로 공용 IP를 만들어 부하 분산 장치 아웃바운드 풀에 할당합니다.

AKS에서 만든 공용 IP는 AKS 관리 리소스로 간주됩니다. 즉, 공용 IP의 수명 주기는 AKS에서 관리하도록 만들어졌으며 공용 IP 리소스에서 직접 사용자 작업을 필요로 하지 않습니다. 또는 클러스터 생성 시점에 고유한 사용자 지정 공용 IP 또는 공용 IP 접두사를 할당할 수 있습니다. 기존 클러스터의 부하 분산 장치 속성에서 사용자 지정 IP를 업데이트할 수도 있습니다.

사용자 고유의 공용 IP 또는 접두사를 사용하기 위한 요구 사항:

- 사용자 지정 공용 IP 주소를 사용자가 만들고 소유해야 합니다. AKS에서 생성한 관리 공용 IP는 사용자 고유 IP로 재사용할 수 없습니다. 관리 충돌 문제를 일으킬 수 있기 때문입니다.
- AKS 클러스터 ID(서비스 주체 또는 관리 ID)에 아웃바운드 IP에 액세스할 수 있는 권한이 있는지 확인해야 합니다. [필요한 공용 IP 권한 목록](kubernetes-service-principal.md#networking)에 따릅니다.
- 아웃바운드 IP 또는 아웃바운드 IP 접두사를 구성하는 데 필요한 [필수 조건 및 제약 조건](../virtual-network/ip-services/public-ip-address-prefix.md#limitations)을 충족하는지 확인합니다.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>사용자 고유 아웃바운드 공용 IP로 클러스터를 업데이트합니다.

[az network public-ip show][az-network-public-ip-show] 명령을 사용하여 공용 IP의 ID를 나열합니다.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

위의 명령은 *myResourceGroup* 리소스 그룹의 *myPublicIP* 공용 IP에 대한 ID를 표시합니다.

**`load-balancer-outbound-ips`** 매개 변수로 `az aks update` 명령을 사용하여 공용 IP로 클러스터를 업데이트합니다.

다음 예제에서는 이전 명령의 ID를 사용하여 `load-balancer-outbound-ips` 매개 변수를 사용합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>사용자 고유 아웃바운드 공용 IP 접두사로 클러스터 업데이트

‘표준’ SKU 부하 분산 장치를 사용하여 송신하는 데 공용 IP 접두사를 사용할 수도 있습니다. 다음 예제에서는 [az network public-ip prefix show][az-network-public-ip-prefix-show] 명령을 사용하여 공용 IP 접두사의 ID를 나열합니다.

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

위의 명령은 *myResourceGroup* 리소스 그룹의 *myPublicIPPrefix* 공용 IP 접두사에 대한 ID를 표시합니다.

다음 예제에서는 이전 명령의 ID를 사용하여 *load-balancer-outbound-ip-prefixes* 매개 변수를 사용합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>사용자 고유 공용 IP 또는 접두사로 클러스터 만들기

허용 목록에 송신 엔드포인트 추가하는 등의 시나리오를 지원하기 위해 클러스터를 만들 때 송신용 고유 IP 주소 또는 IP 접두사를 가져오려는 경우가 있을 수 있습니다. 클러스터 생성 단계에 위에 표시된 것과 동일한 매개 변수를 추가하여 클러스터의 수명 주기 시작 부분에 고유한 공용 IP 및 IP 접두사를 정의합니다.

*az aks create* 명령을 *load-balancer-outbound-ips* 매개 변수를 사용하여 시작 시 공용 IP로 새 클러스터를 만듭니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*az aks create* 명령을 *load-balancer-outbound-ip-prefixes* 매개 변수와 함께 사용하여 시작 시 공용 IP 접두사로 새 클러스터를 만듭니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>할당된 아웃바운드 포트 구성

> [!IMPORTANT]
> 작은 대상 집합에 다수의 연결을 설정할 것으로 예상되는 애플리케이션이 클러스터에 있는 경우(예: SQL DB에 많은 프런트엔드 인스턴스가 연결됨).이러한 시나리오에서는 SNAT 포트 고갈(연결할 포트가 없음)이 발생할 가능성이 매우 높습니다. 이러한 시나리오에서는 부하 분산 장치에서 할당된 아웃바운드 포트 및 아웃바운드 프런트엔드 IP를 늘리는 것이 좋습니다. 증가 시에는 하나(1)의 추가 IP 주소로 64,000개 포트를 추가하여 모든 클러스터 노드에 분산하는 것을 고려해야 합니다.


별도로 지정하지 않는 한 AKS는 할당된 아웃바운드 포트의 기본값을 사용하여 이를 구성할 때 표준 Load Balancer를 정의합니다. 이 값은 아래 명령에 표시된 것처럼 AKS API에서 **null** 또는 SLB API에서 **0** 입니다.

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

이전 명령에는 부하 분산 장치에 대한 아웃바운드 규칙이 나열됩니다. 예를 들면 다음과 같습니다.

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

이 출력은 사용자가 포트가 0개인 상황지만 [백 엔드 풀 크기에 따라 자동 아웃바운드 포트 할당][azure-lb-outbound-preallocatedports]을 활용한다는 것을 의미하지 않습니다. 예를 들어 클러스터에 50개 이하의 노드가 있는 경우 각 노드에 대한 1024 포트가 할당되며, 여기서 노드 수를 늘리면 노드당 포트 수가 점차 줄어듭니다.


할당된 아웃바운드 포트의 수를 정의하거나 늘리려면 아래 예제를 따르면 됩니다.


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

이 예제에서는 내 클러스터의 노드마다 4,000개의 아웃바운드 포트를 할당하므로, 7개의 IP를 사용하면 노드당 포트 4,000개 * 노드 100개 = 총 노드 400,000개 <  = 총 포트 448,000개 = 7개 IP * IP당 포트 64,000개가 됩니다. 이렇게 하면 100개 노드로 안전하게 확장하고 기본 업그레이드 작업을 수행할 수 있습니다. 업그레이드 및 기타 작업에 필요한 추가 노드에 충분한 포트를 할당하는 것이 중요합니다. AKS는 기본적으로 업그레이드에 버퍼 노드 하나를 사용합니다. 이 예제에서는 지정된 특정 시점에 4,000개의 빈 포트가 필요합니다. [maxSurge 값](upgrade-cluster.md#customize-node-surge-upgrade)을 사용할 경우 노드당 아웃바운드 포트 수에 maxSurge 값을 곱합니다.

100개 노드를 안전하게 진행하려면 더 많은 IP를 추가해야 합니다.


> [!IMPORTANT]
> 연결 또는 스케일링 문제를 방지하기 위해 *allocatedOutboundPorts* 를 사용자 지정하기 전에 [필요한 할당량을 계산하고 요구 사항을 확인][requirements]해야 합니다.

클러스터를 만들 때 **`load-balancer-outbound-ports`** 매개 변수를 사용해도 되지만 **`load-balancer-managed-outbound-ip-count`** , **`load-balancer-outbound-ips`** 또는 **`load-balancer-outbound-ip-prefixes`** 도 지정해야 합니다.  예를 들면 다음과 같습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>부하 분산 장치 유휴 시간 제한 구성

SNAT 포트 리소스가 고갈되면 기존 흐름에서 SNAT 포트를 릴리스할 때까지 아웃바운드 흐름이 실패합니다. 부하 분산 장치는 흐름이 닫히면 SNAT 포트를 회수하고, AKS 구성 부하 분산 장치는 유휴 흐름에서 SNAT 포트를 회수하기 위해 30분의 유휴 시간 제한을 사용합니다.
전송(예: **`TCP keepalives`** ) 또는 **`application-layer keepalives`** 를 사용하여 유휴 흐름을 새로 고치고, 필요한 경우 이 유휴 시간 제한을 다시 설정할 수도 있습니다. 다음 예제에 따라 이 시간 제한을 구성할 수 있습니다. 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

수명이 짧은 연결이 많을 것으로 예상되며, 수명이 길고 대기 시간도 긴 연결이 없는 경우(예: `kubectl proxy` 또는 `kubectl port-forward` 활용) 4분과 같이 짧은 시간 제한 값을 사용하는 것이 좋습니다. 또한 TCP Keepalive를 사용하는 경우 연결의 한 쪽에서 사용하도록 설정하는 것으로도 충분합니다. 예를 들어 서버 쪽에서만 사용하도록 설정해도 흐름의 유휴 타이머는 다시 설정되며 양쪽에서 TCP Keepalive를 시작하지 않아도 됩니다. 데이터베이스 클라이언트 서버 구성을 포함하여 애플리케이션 계층에 대한 유사한 개념이 있습니다. 서버 쪽에서 사용 가능한 애플리케이션 전용 Keepalive 옵션을 확인합니다.

> [!IMPORTANT]
> AKS는 기본적으로 유휴 상태에서 TCP 재설정을 사용하도록 설정하며, 이 구성을 유지하고 시나리오에서 보다 예측 가능한 애플리케이션 동작에 활용할 것을 권장합니다.
> TCP RST는 ESTABLISHED 상태에서 TCP 연결 중에만 전송됩니다. 자세한 내용은 [여기](../load-balancer/load-balancer-tcp-reset.md)서 확인할 수 있습니다.

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>할당된 아웃바운드 포트 및 유휴 시간 제한 사용자 지정을 위한 요구 사항

- *allocatedOutboundPorts* 에 대해 지정하는 값도 8의 배수여야 합니다.
- 노드 VM 수 및 필요한 할당된 아웃바운드 포트를 기반으로 아웃바운드 IP 용량이 충분해야 합니다. 아웃바운드 IP 용량이 충분한지 유효성을 검사하려면 다음 수식을 사용합니다. 
 
*outboundIPs* \* 64,000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
예를 들어 3개의 *nodeVMs* 및 50,000개의 *desiredAllocatedOutboundPorts* 가 있는 경우 최소 3개 이상의 *outboundIPs* 가 있어야 합니다. 필요한 것보다 더 많은 추가 아웃바운드 IP 용량을 통합하는 것이 좋습니다. 또한 아웃바운드 IP 용량을 계산할 때 클러스터 자동 크기 조정기 및 노드 풀 업그레이드의 가능성을 고려해야 합니다. 클러스터 자동 크기 조정기의 경우 현재 노드 수와 최대 노드 수를 검토하고 더 높은 값을 사용합니다. 업그레이드하는 경우 업그레이드를 허용하는 모든 노드 풀에 대해 추가 노드 VM을 고려합니다.

- *IdleTimeoutInMinutes* 를 기본값 30분이 아닌 다른 값으로 설정할 때는 워크로드에 아웃바운드 연결이 필요한 기간을 고려합니다. 또한 AKS 외부에서 사용되는 ‘표준’ SKU 부하 분산 장치에 대한 기본 시간 제한 값은 4분입니다. 특정 AKS 워크로드를 더 정확하게 반영하는 *IdleTimeoutInMinutes* 값을 지정하면 더 이상 사용되지 않는 연결로 인한 SNAT 고갈을 줄이는 데 도움이 될 수 있습니다.

> [!WARNING]
> *AllocatedOutboundPorts* 및 *IdleTimeoutInMinutes* 의 값을 변경하면 부하 분산 장치에 대한 아웃바운드 규칙의 동작이 크게 변할 수 있으므로, 장단점과 애플리케이션의 연결 패턴을 이해하지 않은 상태로 가볍게 변경해서는 안 됩니다. 변경의 영향을 완전히 이해하려면 이 값을 업데이트하기 전에 [아래의 SNAT 문제 해결 섹션][troubleshoot-snat]을 확인하고 [Load Balancer 아웃바운드 규칙][azure-lb-outbound-rules-overview] 및 [Azure의 아웃바운드 연결][azure-lb-outbound-connections]을 검토하세요.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>인바운드 트래픽을 특정 IP 범위로 제한

다음 매니페스트는 *loadBalancerSourceRanges* 를 사용하여 인바운드 외부 트래픽에 대한 새 IP 범위를 지정합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

이 예에서는 범위에서 인바운드 외부 트래픽만 허용 하도록 규칙을 업데이트 합니다 `MY_EXTERNAL_IP_RANGE` . 를 `MY_EXTERNAL_IP_RANGE` 내부 서브넷 IP 주소로 바꾸면 트래픽이 클러스터 내부 ip로만 제한 됩니다. 트래픽이 클러스터 내부 Ip로 제한 되는 경우 Kubernetes 클러스터 외부의 클라이언트에서 부하 분산 장치에 액세스할 수 없습니다.

> [!NOTE]
> 인바운드, 외부 트래픽은 부하 분산 장치에서 AKS 클러스터의 가상 네트워크로 흐릅니다. 가상 네트워크에는 부하 분산 장치의 모든 인바운드 트래픽을 허용하는 NSG(네트워크 보안 그룹)가 있습니다. 이 NSG는 *LoadBalancer* 형식의 [서비스 태그][service-tags]를 사용하여 부하 분산 장치의 트래픽을 허용합니다.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>인바운드 연결에서 클라이언트의 IP 유지 관리

기본적으로 [Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) 및 AKS의 `LoadBalancer` 서비스 형식은 Pod에 연결된 클라이언트의 IP 주소를 유지하지 않습니다. 포드에 전달되는 패킷의 원본 IP는 노드의 프라이빗 IP입니다. 클라이언트의 IP 주소를 유지하려면 서비스 정의에서 `service.spec.externalTrafficPolicy`를 `local`로 설정해야 합니다. 다음 매니페스트에는 예제가 나와 있습니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Kubernetes 주석을 통한 추가 사용자 지정

다음은 `LoadBalancer` 유형으로 Kubernetes 서비스에 대해 지원되는 주석의 목록이며, 이러한 주석은 **인바운드** 흐름에만 적용됩니다.

| Annotation | 값 | 설명
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` 또는 `false`                     | 부하 분산 장치가 내부여야 하는지 여부를 지정합니다. 설정하지 않은 경우 기본적으로 공용입니다.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | 서브넷 이름                    | 내부 부하 분산 장치가 바인딩되어야 하는 서브넷을 지정합니다. 설정하지 않은 경우 기본적으로 클라우드 구성 파일에 구성된 서브넷으로 설정됩니다.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | 공용 IP의 DNS 레이블 이름   | **공용** 서비스에 대한 DNS 레이블 이름을 지정합니다. 빈 문자열로 설정된 경우 공용 IP의 DNS 항목은 사용되지 않습니다.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` 또는 `false`                     | 다른 서비스와 공유할 수 있는 Azure 보안 규칙을 사용하여 서비스를 노출하고, 노출될 수 있는 서비스 수를 증가시키기 위한 규칙의 특이성을 거래하도록 지정합니다. 이 주석은 네트워크 보안 그룹의 Azure [보강된 보안 규칙](../virtual-network/network-security-groups-overview.md#augmented-security-rules) 기능을 사용합니다. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | 리소스 그룹의 이름            | 클러스터 인프라와 동일한 리소스 그룹에 있지 않은 부하 분산 장치 공용 IP의 리소스 그룹(노드 리소스 그룹)을 지정합니다.
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | 허용되는 서비스 태그 목록          | 허용되는 [서비스 태그][service-tags]를 쉼표로 구분한 목록을 지정합니다.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | TCP 유휴 시간 제한(분)          | 부하 분산 장치에서 TCP 연결 유휴 시간 제한이 발생하는 시간을 분 단위로 지정합니다. 기본값 및 최솟값은 4입니다. 최댓값은 30입니다. 정수여야 합니다.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | SLB에 대해 사용 하지 않도록 설정 `enableTcpReset` 합니다. Kubernetes 1.18에서 사용 되지 않으며 1.20에서 제거 되었습니다. 


## <a name="troubleshooting-snat"></a>SNAT 문제 해결

동일한 대상 IP 주소 및 포트에 대해 많은 아웃바운드 TCP 또는 UDP 연결을 시작할 것임을 알고 있으며, 실패하는 아웃바운드 연결을 확인하거나 지원 서비스에서 SNAT 포트(PAT에서 사용하는 미리 할당된 삭제 포트)가 고갈될 것이라는 알림을 받는 경우 일반적인 완화 옵션 몇 가지를 사용할 수 있습니다. 다음 옵션을 검토하고 시나리오에 가장 적합한 옵션을 결정합니다. 한 가지 이상의 옵션이 이 시나리오를 관리하는 데 도움이 될 수 있습니다. 자세한 내용은 [아웃바운드 연결 문제 해결 가이드](../load-balancer/troubleshoot-outbound-connection.md)를 참조하세요.

SNAT 소모의 근본 원인은 아웃바운드 연결의 설정, 관리 또는 구성 가능한 타이머가 기본값에서 변경되는 방식에 대한 앤티 패턴인 경우가 많습니다. 이 섹션을 주의 깊게 검토하세요.

### <a name="steps"></a>단계
1. 연결이 오랫동안 유휴 상태를 유지하고, 해당 포트를 해제하는 데 기본 유휴 시간 제한을 사용하는지 확인합니다. 그런 경우 시나리오에 대한 30분의 기본 제한 시간을 줄여야 할 수 있습니다.
2. 애플리케이션에서 아웃바운드 연결을 만드는 방법(예: 코드 검토 또는 패킷 캡처)을 조사합니다.
3. 이 활동이 예상되는 동작인지, 아니면 애플리케이션이 잘못 작동하는지를 확인합니다. Azure Monitor에서 [메트릭](../load-balancer/load-balancer-standard-diagnostics.md) 및 [로그](../load-balancer/monitor-load-balancer.md)를 사용하여 검색 결과를 구체화합니다. 예를 들어 "실패" 범주를 SNAT 연결 메트릭에 사용합니다.
4. 적절한 [패턴](#design-patterns)을 따르는지 여부를 평가합니다.
5. [추가 아웃바운드 IP 주소 + 추가 할당된 아웃바운드 포트](#configure-the-allocated-outbound-ports)를 사용하여 SNAT 포트 소모를 완화해야 하는지 평가합니다.

### <a name="design-patterns"></a>디자인 패턴
가능한 경우 항상 연결 다시 사용 및 연결 풀링을 활용합니다. 이러한 패턴은 리소스 소모 문제를 방지하여 예측 가능한 동작을 수행합니다. 이러한 패턴의 기본 형식은 다양한 개발 라이브러리 및 프레임워크에서 찾을 수 있습니다.

- 원자성 요청(연결당 요청 하나)은 보통 좋지 않은 디자인 선택입니다. 이러한 안티패턴은 규모를 제한하고, 성능을 저하시키고, 안정성을 떨어뜨립니다. 그 대신, HTTP/S 연결을 다시 사용하여 연결 수와 연결된 SNAT 포트 수를 줄입니다. TLS를 사용하면 핸드셰이크, 오버헤드 및 암호화 작업 비용이 감소하므로 애플리케이션 규모가 증가하고 성능이 향상됩니다.
- 클러스터/사용자 지정 DNS를 사용하거나 coreDNS의 사용자 지정 업스트림 서버를 사용하는 경우 클라이언트가 DNS 확인자 결과를 캐싱하지 않으면 DNS가 개별 흐름을 대량으로 도입할 수 있다는 점에 유의해야 합니다. 사용자 지정 DNS 서버를 사용하는 대신 coreDNS를 먼저 사용자 지정하고 적절한 캐싱 값을 정의해야 합니다.
- UDP 흐름(예: DNS 조회)은 유휴 시간 제한 동안 SNAT 포트를 할당합니다. 유휴 시간 제한이 길수록 SNAT 포트가 받는 압력이 높아집니다. 유휴 시간 제한을 짧게(예: 4분) 설정하세요.
연결 풀을 사용하여 연결 볼륨을 셰이핑하세요.
- 절대로 TCP 흐름을 자동으로 중단하고 TCP 타이머를 사용하여 흐름을 정리하지 마세요. TCP에서 연결을 명시적으로 닫지 않는 경우 상태는 중간 시스템 및 엔드포인트에서 할당된 상태를 유지하고 다른 연결에서 SNAT 포트를 사용할 수 없게 만듭니다. 이 패턴은 애플리케이션 오류 및 SNAT 고갈이 트리거할 수 있습니다.
- 영향에 대한 전문 지식이 없으면 OS 수준 TCP 닫기 관련 타이머 값을 변경하지 마세요. TCP 스택이 복구되는 동안 연결의 엔드포인트에 예상과 다른 부분이 있으면 애플리케이션 성능에 부정적인 영향을 미칠 수 있습니다. 타이머를 변경하고 싶다는 생각이 들면 일반적으로 디자인에 근본적인 문제가 있다는 신호입니다. 다음 권장 사항을 검토하세요.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>기본 SKU 부하 분산 장치에서 표준 SKU로 이동

기본 SKU 부하 분산 장치를 사용하는 기존 클러스터가 있는 경우, 표준 SKU 부하 분산 장치를 사용하는 클러스터를 사용하도록 마이그레이션할 때 유의해야 할 중요한 동작상의 차이점이 있습니다.

예를 들어 클러스터의 `load-balancer-sku` 유형을 클러스터 생성 시점에만 정의할 수 있는 경우 클러스터를 마이그레이션하기 위한 파란색/녹색 배포를 수행하는 것이 일반적인 방법입니다. 그러나 기본 SKU 부하 분산 장치는 표준 SKU 부하 분산 장치(표준 SKU IP 주소가 필요함)와 호환되지 않는 기본 SKU IP 주소를 사용합니다. 클러스터를 마이그레이션하여 부하 분산 장치 SKU를 업그레이드하는 경우 호환되는 IP 주소 SKU가 포함된 새 IP 주소가 필요합니다.

클러스터를 마이그레이션하는 방법에 대한 자세한 내용은 [마이그레이션 고려 사항에 대한 설명서](aks-migration.md)를 참조하여 마이그레이션할 때 고려해야 하는 중요한 토픽의 목록을 확인하세요. AKS에서 표준 SKU 부하 분산 장치를 사용하는 경우 아래 제한 사항에 유의해야 하는 중요한 동작상의 차이점이 있습니다.

## <a name="limitations"></a>제한 사항

‘표준’ SKU를 포함한 부하 분산 장치를 지원하는 AKS 클러스터를 만들고 관리하는 경우 다음과 같은 제한 사항이 적용됩니다.

* AKS 클러스터에서 송신 트래픽을 허용하려면 하나 이상의 공용 IP 또는 IP 접두사가 필요합니다. 공용 IP 또는 IP 접두사는 컨트롤 플레인과 에이전트 노드 간의 연결을 유지하는 것뿐만 아니라 AKS의 이전 버전과의 호환성을 유지하는 데도 필요합니다. ‘표준’ SKU 부하 분산 장치를 사용하여 공용 IP 또는 IP 접두사를 지정하는 다음과 같은 옵션이 있습니다.
    * 사용자 고유의 공용 IP를 제공합니다.
    * 사용자 고유의 공용 IP 접두사를 제공합니다.
    * 최대 100까지 숫자를 지정하여 AKS 클러스터가 해당하는 숫자의 ‘표준’ SKU 공용 IP를 AKS 클러스터에서 만들어진 동일한 리소스 그룹(일반적으로 처음에 *MC_* 로 이름이 지정됨)에 만들 수 있도록 합니다. AKS는 ‘표준’ SKU 부하 분산 장치에 공용 IP를 할당합니다. 공용 IP, 공용 IP 접두사 또는 IP 수가 지정되지 않은 경우 기본적으로 하나의 공용 IP가 AKS 클러스터와 동일한 리소스 그룹에 자동으로 만들어집니다. 또한 공용 주소를 허용하고 IP 생성을 금지하는 Azure Policy를 만들지 않도록 해야 합니다.
* AKS에서 만든 공용 IP는 사용자 지정 공용 IP 주소 가져오기로 재사용할 수 없습니다. 사용자가 모든 사용자 지정 IP 주소를 만들고 관리해야 합니다.
* 부하 분산 장치 SKU를 정의하는 것은 AKS 클러스터를 만들 때만 수행할 수 있습니다. AKS 클러스터를 만든 후에는 부하 분산 장치 SKU를 변경할 수 없습니다.
* 단일 클러스터에서는 한 가지 유형의 부하 분산 장치 SKU(기본 또는 표준)만 사용할 수 있습니다.
* ‘표준’ SKU 부하 분산 장치는 ‘표준’ SKU IP 주소만 지원합니다. 


## <a name="next-steps"></a>다음 단계

[Kubernetes 서비스 설명서][kubernetes-services]에서 Kubernetes 서비스에 대해 자세히 알아봅니다.

[AKS 내부 부하 분산 장치 설명서](internal-lb.md)에서 인바운드 트래픽에 대한 내부 부하 분산 장치 사용에 대해 자세히 알아보세요.

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags