---
title: 프라이빗 Azure Kubernetes Service 클러스터 만들기
description: 프라이빗 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 8/30/2021
ms.openlocfilehash: ea8fe1866540bd7bb5a10a9217f92d50340010cf
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133109950"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>프라이빗 Azure Kubernetes Service 클러스터 만들기

프라이빗 클러스터의 컨트롤 플레인 또는 API 서버에는 [RFC1918 - 프라이빗 인터넷용 주소 할당](https://tools.ietf.org/html/rfc1918) 문서에 정의된 내부 IP 주소가 있습니다. 프라이빗 클러스터를 사용하면 API 서버와 노드 풀 사이의 네트워크 트래픽이 개인 네트워크에만 유지되는지 확인할 수 있습니다.

컨트롤 플레인 또는 API 서버는 AKS(Azure Kubernetes Service)에서 관리되는 Azure 구독에 있습니다. 고객의 클러스터 또는 노드 풀은 고객의 구독에 있습니다. 서버와 클러스터 또는 노드 풀은 API 서버 가상 네트워크의 [Azure Private Link 서비스][private-link-service]를 통해 서로 통신할 수 있으며, 고객의 AKS 클러스터의 서브넷에 노출되는 프라이빗 엔드포인트입니다.

## <a name="region-availability"></a>지역 가용성

프라이빗 클러스터는 [AKS가 지원](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)되는 퍼블릭 지역, Azure Government, Azure 중국 21Vianet 지역에서 사용할 수 있습니다.

> [!NOTE]
> Azure Government 사이트는 지원되지만 US Gov 텍사스는 프라이빗 링크 지원이 없으므로 현재 지원되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure CLI 버전 2.2.0 이상
* Private Link 서비스는 표준 Azure Load Balancer에서만 지원됩니다. 기본 Azure Load Balancer는 지원되지 않습니다.  
* 사용자 지정 DNS 서버를 사용하려면 Azure DNS IP 168.63.129.16을 사용자 지정 DNS 서버에 업스트림 DNS 서버로 추가합니다.

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI 설치

*aks-preview* Azure CLI 확장이 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-a-private-aks-cluster"></a>프라이빗 AKS 클러스터 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들거나 AKS 클러스터를 위한 기존 리소스 그룹을 사용합니다.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>기본값으로 설정된 기본 네트워킹 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
여기서 `--enable-private-cluster`는 프라이빗 클러스터에 대한 필수 플래그에 속합니다. 

### <a name="advanced-networking"></a>고급 네트워킹  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
여기서 `--enable-private-cluster`는 프라이빗 클러스터에 대한 필수 플래그에 속합니다. 

> [!NOTE]
> Docker 브리지 주소 CIDR(172.17.0.1/16)이 서브넷 CIDR과 충돌하는 경우, Docker 브리지 주소를 적절하게 변경합니다.

## <a name="configure-private-dns-zone"></a>프라이빗 DNS 영역 구성 

다음 매개 변수를 활용하여 프라이빗 DNS 영역을 구성할 수 있습니다.

- 기본값이기도 한 "system"입니다. --private-dns-zone 인수를 생략하면 AKS는 노드 리소스 그룹에 프라이빗 DNS 영역을 만듭니다.
- "none"은 기본적으로 공용 DNS로 설정되며, 이는 AKS가 프라이빗 DNS 영역을 만들지 않음을 의미합니다.  
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID". Azure 글로벌 클라우드에 대해 또는 형식으로 프라이빗 DNS 영역을 만들어야 `privatelink.<region>.azmk8s.io` `<subzone>.privatelink.<region>.azmk8s.io` 합니다. 앞으로 프라이빗 DNS 영역의 리소스 ID가 필요합니다.  또한 적어도 `private dns zone contributor` 및 `vnet contributor` 역할이 있는 사용자 할당 ID 또는 서비스 주체가 필요합니다.
  - 프라이빗 DNS 영역이 AKS 클러스터와 다른 구독에 있는 경우 두 구독 모두에 Microsoft.ContainerServices를 등록해야 합니다.
  - “fqdn-subdomain”은 “CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID”와 함께 사용되어 `privatelink.<region>.azmk8s.io`에 하위 도메인 기능을 제공할 수 있습니다.

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>프라이빗 DNS 영역을 사용하여 프라이빗 AKS 클러스터 만들기

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```
### <a name="create-a-private-aks-cluster-with-a-byo-private-dns-subzone-preview"></a>BYO 프라이빗 DNS SubZone(미리 보기)을 사용하여 프라이빗 AKS 클러스터 만들기

필수 조건:

* Azure CLI >= 2.29.0 또는 aks-preview 확장 0.5.34 이상에서 Azure CLI.

### <a name="register-the-enableprivateclustersubzone-preview-feature"></a>`EnablePrivateClusterSubZone` 미리 보기 기능 등록

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

SubZone을 사용하여 AKS 프라이빗 클러스터를 만들려면 구독에서 기능 플래그를 사용하도록 설정해야 `EnablePrivateClusterSubZone` 합니다.

`EnablePrivateClusterSubZone`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnablePrivateClusterSubZone"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnablePrivateClusterSubZone')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="create-a-private-aks-cluster-with-custom-private-dns-zone"></a>사용자 지정 프라이빗 DNS 영역을 사용하여 프라이빗 AKS 클러스터 만들기

```azurecli-interactive
# Custom Private DNS Zone name should be in format "privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId>
```

### <a name="create-a-private-aks-cluster-with-custom-private-dns-subzone"></a>사용자 지정 프라이빗 DNS SubZone을 사용하여 프라이빗 AKS 클러스터 만들기

```azurecli-interactive
# Custom Private DNS Zone name should be in format "<subzone>.privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId>
```

### <a name="create-a-private-aks-cluster-with-custom-private-dns-zone-and-custom-subdomain"></a>사용자 지정 프라이빗 DNS 영역 및 사용자 지정 하위 도메인을 사용하여 프라이빗 AKS 클러스터 만들기

```azurecli-interactive
# Custom Private DNS Zone name could be in formats "privatelink.<region>.azmk8s.io" or "<subzone>.privatelink.<region>.azmk8s.io"
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain>
```

### <a name="create-a-private-aks-cluster-with-a-public-fqdn"></a>공용 FQDN을 사용하여 프라이빗 AKS 클러스터 만들기

필수 조건:

* Azure CLI >= 2.28.0 또는 aks-preview 확장 0.5.29 이상에서 Azure CLI.
* ARM 또는 나머지 API를 사용하는 경우 AKS API 버전은 2021-05-01 이상이어야 합니다.

퍼블릭 DNS 옵션을 활용하여 프라이빗 클러스터에 대한 라우팅 옵션을 간소화할 수 있습니다.  

![공용 DNS](https://user-images.githubusercontent.com/50749048/124776520-82629600-df0d-11eb-8f6b-71c473b6bd01.png)

1. 프라이빗 AKS 클러스터를 프로비전하는 경우 AKS는 기본적으로 Azure 공용 DNS에 추가 공용 FQDN 및 해당 A 레코드를 만듭니다. 에이전트 노드는 프라이빗 DNS 영역의 A 레코드를 사용하여 API 서버와 통신할 프라이빗 엔드포인트의 개인 IP 주소를 확인합니다.

2. 를 사용하는 경우 `--private-dns-zone none` 클러스터에는 공용 FQDN만 있습니다. 이 옵션을 사용하는 경우 API 서버의 FQDN 이름 확인을 위해 프라이빗 DNS 영역을 만들거나 사용할 수 없습니다. API의 IP는 개인 IP이며 공개적으로 라우팅할 수 없습니다.

3. 공용 FQDN을 사용하지 않으려면 를 사용하여 사용하지 않도록 설정할 수 `--disable-public-fqdn` 있습니다("none" 프라이빗 dns 영역은 공용 FQDN을 사용하지 않도록 설정할 수 없음).

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <private-dns-zone-mode> --disable-public-fqdn
az aks update -n <private-cluster-name> -g <private-cluster-resource-group> --disable-public-fqdn
```

## <a name="options-for-connecting-to-the-private-cluster"></a>프라이빗 클러스터에 연결하기 위한 옵션

API 서버 엔드포인트에 공용 IP 주소가 없습니다. API 서버를 관리하려면 AKS 클러스터의 VNet(Azure Virtual Network)에 액세스 권한이 있는 VM을 사용해야 합니다. 프라이빗 클러스터에 대한 네트워크 연결을 설정할 경우, 몇 가지 옵션이 있습니다.

* AKS 클러스터와 동일한 Azure Virtual Network(VNet)에서 VM을 만듭니다.
* 별도의 네트워크에서 VM을 사용하고 [가상 네트워크 피어링][virtual-network-peering]을 설정합니다.  이 옵션에 관한 자세한 내용은 아래의 해당 섹션을 참조하세요.
* [Express Route 또는 VPN][express-route-or-VPN] 연결을 사용합니다.
* [AKS 실행 명령 기능](#aks-run-command)을 사용합니다.

AKS 클러스터와 동일한 VNET에 VM을 만드는 것이 가장 쉬운 옵션입니다.  Express Route 및 VPN은 비용을 증가시키며 추가적인 네트워킹 복잡성을 요구합니다.  가상 네트워크 피어링을 사용하려면 중첩되는 범위가 없도록 네트워크 CIDR 범위를 계획해야 합니다.

### <a name="aks-run-command"></a>AKS 실행 명령

현재 프라이빗 클러스터에 액세스하려면 클러스터 가상 네트워크나 피어링 네트워크 또는 클라이언트 컴퓨터 내에서 이 작업을 수행 해야 합니다. 이를 위해서는 일반적으로 VPN 또는 기본 경로를 통해 클러스터 가상 네트워크에 연결하거나 클러스터 가상 네트워크에서 만들 jumpbox를 사용하여 컴퓨터를 연결해야 합니다. AKS 실행 명령을 사용하여 AKS API를 통해 AKS 클러스터의 명령을 원격으로 호출할 수 있습니다. 이 기능은 예를 들어 프라이빗 클러스터의 원격 노트북에서 Just-In-Time 명령을 실행할 수 있는 API를 제공합니다. 이렇게 하면 클라이언트 컴퓨터가 클러스터 개인 네트워크에 있지 않고도 동일한 RBAC 컨트롤과 프라이빗 API 서버를 유지하고 적용하면서 프라이빗 클러스터에 신속하게 Just-In-Time 액세스할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

* Azure CLI 버전 2.24.0 이상

### <a name="use-aks-run-command"></a>AKS 실행 명령 사용

간단한 명령

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

특정 파일을 연결하여 매니페스트 배포

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

전체 폴더를 연결하여 매니페스트 배포

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Helm 설치 및 특정 값 매니페스트 패스

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```
> [!NOTE]
> "Microsoft.ContainerService/managedClusters/runcommand/action", "Microsoft.ContainerService/managedclusters/commandResults/read" 권한으로 사용자 지정 역할을 만들어 AKS 실행 명령 대한 액세스를 보호하고 Just-In-Time 액세스 또는 조건부 액세스 정책과 함께 특정 사용자 및/또는 그룹에 할당합니다. 

## <a name="virtual-network-peering"></a>가상 네트워크 피어링

앞서 언급했듯이 가상 네트워크 피어링은 프라이빗 클러스터에 액세스하는 한 가지 방법입니다. 가상 네트워크 피어링을 사용하려면 가상 네트워크와 프라이빗 DNS 영역 간에 링크를 설정해야 합니다.
    
1. Azure Portal에서 노드 리소스 그룹으로 이동합니다.  
2. 프라이빗 DNS 영역을 선택합니다.   
3. 왼쪽 창에서 **가상 네트워크** 링크를 선택합니다.  
4. VM의 가상 네트워크를 프라이빗 DNS 영역에 추가하는 새 링크를 만듭니다. DNS 영역 링크를 사용할 수 있을 때까지 몇 분 정도 걸립니다.  
5. Azure Portal에서 클러스터의 가상 네트워크를 포함하는 리소스 그룹으로 이동합니다.  
6. 오른쪽 창에서 가상 네트워크를 선택합니다. 가상 네트워크 이름은 *aks-vnet-\** 형식으로 되어 있습니다.  
7. 왼쪽 창에서 **피어링** 을 선택합니다.  
8. **추가** 를 선택하고 VM의 가상 네트워크를 추가한 다음, 피어링을 만듭니다.  
9. VM이 있는 가상 네트워크로 이동하여 **피어링** 을 선택하고 AKS 가상 네트워크를 선택한 후 피어링을 만듭니다. AKS 가상 네트워크의 주소 범위와 VM의 가상 네트워크가 충돌하는 경우, 피어링이 실패합니다. 자세한 내용은 [가상 네트워크 피어링][virtual-network-peering]을 참조하세요.

## <a name="hub-and-spoke-with-custom-dns"></a>사용자 지정 DNS를 사용하는 허브 및 스포크

[허브 및 스포크 아키텍처](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)는 일반적으로 Azure에서 네트워크를 배포하는 데 사용됩니다. 이러한 배포 중 상당수에서 스포크 VNet의 DNS 설정은 중앙 DNS 전달자를 참조하여 온-프레미스 및 Azure 기반 DNS 확인을 허용하도록 구성됩니다. 이러한 네트워킹 환경에 AKS 클러스터를 배포하는 경우, 고려해야 할 몇 가지 특별한 사항들이 있습니다.

![프라이빗 클러스터 허브 및 스포크](media/private-clusters/aks-private-hub-spoke.png)

1. 기본값으로 프라이빗 클러스터가 프로비저닝되면 프라이빗 엔드포인트(1)와 프라이빗 DNS 영역(2)이 클러스터 관리형 리소스 그룹에서 생성됩니다. 클러스터는 프라이빗 영역의 A 레코드를 사용하여 API 서버와 통신할 프라이빗 엔드포인트의 IP를 확인합니다.

2. 프라이빗 DNS 영역은 클러스터 노드가 (3)과 연결된 VNet에만 연결됩니다. 즉, 연결된 VNet의 호스트만 프라이빗 엔드포인트를 확인할 수 있습니다. VNet에 사용자 지정 DNS가 구성되지 않은 시나리오(기본값)에서 이 작업은 연결로 인해 프라이빗 DNS 영역에 있는 레코드를 확인할 수 있는 DNS의 168.63.129.16에 위치한 호스트 지점으로서 문제 없이 작동합니다.

3. 클러스터를 포함하는 VNet에 사용자 지정 DNS 설정(4)이 있는 시나리오에서는 프라이빗 DNS 영역이 사용자 지정 DNS 확인자(5)를 포함하는 VNet에 연결되어 있지 않으면 클러스터 배포가 실패합니다. 이 연결은 클러스터를 프로비저닝하는 동안 프라이빗 영역이 생성된 후에 또는 이벤트 기반 배포 메커니즘(예: Azure Event Grid 및 Azure Functions)을 사용하여 영역 생성을 감지하여 자동화로 영역이 생성된 후에 수동으로 만들 수 있습니다.

> [!NOTE]
> [kubenet을 사용하여 사용자 고유의 경로 테이블 가져오기](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet)와 프라이빗 클러스터를 사용하여 자체 DNS 가져오기를 사용하는 경우 클러스터 만들기가 실패합니다. 클러스터 만들기를 실패한 후에는 노드 리소스 그룹의 [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet)을 서브넷에 연결하여 성공적으로 클러스터를 만듭니다.

## <a name="limitations"></a>제한 사항 
* IP 권한이 부여된 범위는 프라이빗 API 서버 엔드포인트에 적용할 수 없으며 퍼블릭 API 서버에만 적용됩니다.
* [Azure Private Link 서비스 제한][private-link-service]은 프라이빗 클러스터에 적용됩니다.
* 프라이빗 클러스터를 사용하는 Azure DevOps Microsoft 호스팅 에이전트를 지원하지 않습니다. [자체 호스팅 에이전트](/azure/devops/pipelines/agents/agents?tabs=browser)를 사용하는 것이 좋습니다. 
* Azure Container Registry를 프라이빗 AKS와 함께 사용해야 하는 고객의 경우, Container Registry 가상 네트워크는 에이전트 클러스터 가상 네트워크와 피어링되어야 합니다.
* 기존 AKS 클러스터를 프라이빗 클러스터로 변환하는 기능이 지원되지 않습니다.
* 고객 서브넷에서 프라이빗 엔드포인트를 삭제하거나 수정하면 클러스터의 작동이 중지됩니다. 

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
