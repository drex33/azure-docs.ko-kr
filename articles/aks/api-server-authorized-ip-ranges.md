---
title: AKS(Azure Kubernetes Service)에서 API 서버 권한이 있는 IP 범위
description: AKS(Azure Kubernetes Service)에서 API 서버에 액세스할 수 있는 IP 주소 범위를 사용하여 클러스터를 보호하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: c64d2e683ade2413e9c548ea548b2e1a6e0efef6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542965"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 권한 있는 IP 주소 범위를 사용하는 안전한 API 서버 액세스

Kubernetes에서 API 서버는 클러스터에서 리소스를 만들거나 노드 수를 스케일링하는 등의 작업을 수행하라는 요청을 수신합니다. API 서버는 클러스터와의 상호 작용 및 관리가 수행되는 중심 통로입니다. 클러스터 보안을 개선하고 공격을 최소화하기 위해서는 제한된 IP 주소 범위에서만 API 서버에 액세스할 수 있어야 합니다.

이 문서에서는 API 서버 권한이 있는 IP 주소 범위를 사용하여 컨트롤 플레인에 액세스할 수 있는 IP 주소 및 CIDR을 제한하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI를 사용하여 AKS 클러스터를 만드는 방법을 보여 줍니다.

Azure CLI 버전 2.0.76 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="limitations"></a>제한 사항

API 서버 권한이 있는 IP 범위 기능에는 다음과 같은 제한 사항이 있습니다.
- API 서버 권한이 있는 IP 주소 범위의 미리 보기 기간이 2019년 10월에 종료된 이후 생성된 클러스터에 대해서는 API 서버 권한이 있는 IP 주소 범위는 ‘표준’ SKU 부하 분산 장치에서만 지원됩니다. ‘기본’ SKU 부하 분산 장치 및 API 서버 권한이 있는 IP 주소 범위가 구성되어 있는 기존 클러스터는 계속 작동하지만 ‘표준’ SKU 부하 분산 장치로 마이그레이션할 수는 없습니다.  기존 클러스터는 Kubernetes 버전이나 컨트롤 플레인이 업그레이드되는 경우에도 계속 작동합니다. API 서버 권한이 있는 IP 주소 범위는 프라이빗 클러스터에 대해서는 지원되지 않습니다.
- 관련 기능은 [노드당 공용 IP](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools)를 사용하는 클러스터와 호환되지 않습니다.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 서버 권한이 있는 IP 범위의 개요

Kubernetes API 서버는 기본 Kubernetes API를 공개하는 방법입니다. 이 구성 요소는 `kubectl` 또는 Kubernetes 대시보드와 같은 관리 도구에 대한 상호 작용을 제공합니다. AKS는 전용 API 서버가 있는 단일 테넌트 클러스터 컨트롤 플레인을 제공합니다. 기본적으로 API 서버에는 공용 IP 주소가 할당되며 Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어) 또는 Azure RBAC를 사용하여 액세스를 제어해야 합니다.

그 외에 공개적으로 액세스할 수 있는 AKS 컨트롤 플레인/API 서버에 대한 액세스를 보호하려면 권한 있는 IP 범위를 사용하도록 설정하고 사용하면 됩니다. 권한 있는 IP 범위는 정의된 IP 주소 범위만 API 서버와 통신하도록 허용합니다. 권한 있는 IP 범위에 속하지 않는 IP 주소에서 API 서버에 보낸 요청은 차단됩니다. 계속 Kubernetes RBAC 또는 Azure RBAC를 계속 사용하여 사용자와 사용자가 요청하는 작업에 권한을 부여하세요.

API 서버 및 기타 클러스터 구성 요소에 대한 자세한 내용은 [AKS에 대한 Kubernetes 핵심 개념][concepts-clusters-workloads]을 참조하세요.

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>API 서버 권한이 있는 IP 범위를 사용하도록 설정된 AKS 클러스터 만들기

[az aks create][az-aks-create]를 사용하여 클러스터를 만들고 *`--api-server-authorized-ip-ranges`* 매개 변수를 지정하여 권한 있는 IP 주소 범위 목록을 제공합니다. IP 주소 범위는 일반적으로 온-프레미스 네트워크 또는 공용 IP에서 사용되는 주소 범위입니다. CIDR 범위를 지정할 때 범위의 첫 번째 IP 주소부터 시작합니다. 예를 들어 *137.117.106.90/29* 는 유효한 범위이지만 *137.117.106.88/29* 와 같이 범위의 첫 번째 IP 주소를 지정해야 합니다.

> [!IMPORTANT]
> 기본적으로 클러스터는 아웃바운드 게이트웨이를 구성하는 데 사용할 수 있는 [표준 SKU 부하 분산 장치][standard-sku-lb]를 사용합니다. 클러스터를 만드는 동안 API 서버 권한이 있는 IP 범위를 사용하도록 설정하면 내가 지정하는 범위 외에도 사용자의 클러스터에 공용 IP가 기본적으로 허용됩니다. *`--api-server-authorized-ip-ranges`* 에 *“”* 를 지정하거나 아무 값도 지정하지 않으면 API 서버 권한이 있는 IP 범위가 사용되지 않습니다. PowerShell을 사용 중인 경우 구문 분석 문제를 방지하기 위해 *`--api-server-authorized-ip-ranges=""`* (등호 포함)를 사용합니다.

다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹에 API 서버 권한이 있는 IP 범위를 사용하도록 설정된 *myAKSCluster* 라는 단일 노드 클러스터를 만듭니다. 허용되는 IP 주소 범위는 *73.140.245.0/24* 입니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> 해당 범위를 허용 목록에 추가해야 합니다.
> - 방화벽 공용 IP 주소
> - 클러스터를 관리할 네트워크를 나타내는 모든 범위
>
> 지정할 수 있는 IP 범위의 최대 개수는 200개입니다.
>
> 규칙을 전파하는 데 최대 2분이 걸릴 수 있습니다. 연결을 테스트할 때 2분까지는 기다리시기 바랍니다.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>표준 SKU 부하 분산 장치에 아웃바운드 IP를 지정합니다.

AKS 클러스터를 만들 때 클러스터에 아웃바운드 IP 주소 또는 접두사를 지정하면 해당 주소 또는 접두사도 허용됩니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

위 예제에서 매개 변수 *`--load-balancer-outbound-ip-prefixes`* 에 제공된 모든 IP는 *`--api-server-authorized-ip-ranges`* 매개 변수의 IP와 함께 허용됩니다.

대신 아웃바운드 부하 분산 장치 IP 접두사를 허용하도록 *`--load-balancer-outbound-ip-prefixes`* 매개 변수를 지정할 수 있습니다.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>표준 SKU 부하 분산 장치의 아웃바운드 공용 IP만 허용

클러스터를 만드는 동안 API 서버 권한이 있는 IP 범위를 사용하도록 설정하면 사용자가 지정하는 범위 외에도 클러스터에 표준 SKU 부하 분산 장치의 아웃바운드 공용 IP가 기본적으로 허용됩니다. 표준 SKU 부하 분산 장치의 아웃바운드 공용 IP만 허용하려면 *`--api-server-authorized-ip-ranges`* 매개 변수를 지정할 때 *0.0.0.0/32* 를 사용합니다.

다음 예제에서는 표준 SKU 부하 분산 장치의 아웃바운드 공용 IP만 허용되며 클러스터 내의 노드에서만 API 서버에 액세스할 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>클러스터의 API 서버 권한이 있는 IP 범위 업데이트

기존 클러스터에서 API 서버 권한이 있는 IP 범위를 업데이트하려면 [az aks update][az-aks-update] 명령을 사용하고 *`--api-server-authorized-ip-ranges`* , --load-balancer-outbound-ip-prefixes *, *`--load-balancer-outbound-ips`* 또는 --load-balancer-outbound-ip-prefixes* 매개 변수를 사용합니다.

다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹의 *myAKSCluster* 라는 클러스터에서 API 서버 권한이 있는 IP 범위를 업데이트합니다. 권한을 부여할 IP 주소 범위는 *73.140.245.0/24* 입니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

또한 *`--api-server-authorized-ip-ranges`* 매개 변수를 지정할 때 *0.0.0.0/32* 를 사용하여 표준 SKU 부하 분산 장치의 공용 IP만 허용할 수 있습니다.

## <a name="disable-authorized-ip-ranges"></a>권한 있는 IP 범위 사용 해제

권한 있는 IP 범위를 사용하지 않도록 설정하려면 [az aks update][az-aks-update]를 사용하고 빈 범위를 지정하여 API 서버 권한이 있는 IP 범위를 사용하지 않도록 설정합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>기존의 권한 있는 IP 범위 찾기

권한 있는 IP 범위를 찾으려면 [az aks show][az-aks-show]를 사용하고 클러스터의 이름 및 리소스 그룹을 지정합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Azure Portal을 사용하여 권한 있는 IP 범위 업데이트, 사용 해제, 찾기

권한 있는 IP 범위를 추가하고, 업데이트하고, 찾고, 사용 해제하는 위 작업은 Azure Portal에서도 수행할 수 있습니다. 액세스하려면 클러스터 리소스의 메뉴 블레이드에서 **설정** 아래에 있는 **네트워킹** 으로 이동합니다.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="브라우저에서 클러스터 리소스의 네트워킹 설정 Azure Portal 페이지가 표시됩니다. ‘지정된 IP 범위 설정’ 및 ‘지정된 IP 범위’ 옵션이 강조 표시됩니다.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>`--api-server-authorized-ip-ranges`에 포함할 IP를 찾는 방법

개발 머신, 도구 또는 자동화 IP 주소에서 API 서버에 액세스하기 위해서는 이들을 승인된 IP 범위의 AKS 클러스터 목록에 추가해야 합니다. 

또 다른 옵션은 Firewall의 가상 네트워크에서 별도의 서브넷 내에 필요한 도구를 사용하여 jumpbox를 구성하는 것입니다. 이렇게 하면 사용자의 환경에 네트워크별로 방화벽이 있고 사용자가 방화벽 IP를 권한 있는 범위에 추가한 것으로 가정됩니다. 마찬가지로 AKS 서브넷에서 방화벽 서브넷으로 강제 터널링을 사용한 경우 jumpbox를 클러스터 서브넷에 두는 것도 괜찮습니다.

다음 명령을 사용하여 승인된 범위에 또 다른 IP 주소를 추가합니다.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig +short "myip.opendns.com" "@resolver1.opendns.com")
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

> [!NOTE]
> 위의 예는 클러스터에 API 서버 권한이 있는 IP 범위를 추가합니다. 권한 있는 IP 범위를 사용하지 않도록 설정하려면 az aks update를 사용하고 빈 범위 “”를 지정합니다. 

또 다른 옵션은 Windows 시스템에서 아래 명령을 사용하여 공용 IPv4 주소를 가져오는 것입니다. 또는 [IP 주소 찾기](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)의 단계를 따를 수 있습니다.

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

인터넷 브라우저에서 “내 IP 주소 찾기”를 검색하여 이 주소를 찾을 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 API 서버 권한이 있는 IP 범위를 사용하도록 설정하는 방법을 살펴봤습니다. 이 방법은 보안 AKS 클러스터를 실행하는 방법의 일부입니다.

자세한 내용은 [AKS에서 애플리케이션 및 클러스터에 대한 보안 개념][concepts-security] 및 [AKS의 클러스터 보안 및 업그레이드를 위한 모범 사례][operator-best-practices-cluster-security]를 참조하세요.

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: /previous-versions/azure/dev-spaces/#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
