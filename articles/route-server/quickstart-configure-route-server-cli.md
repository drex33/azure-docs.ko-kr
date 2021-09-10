---
title: '빠른 시작: Azure CLI를 사용하여 Route Server 만들기 및 구성'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Route Server를 만들고 구성하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 1682a1b80f3b2d85e0d263b9ea2648da2b57a561
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430903"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Route Server 만들기 및 구성 

이 문서는 Azure PowerShell을 사용하여 가상 네트워크의 NVA(네트워크 가상 어플라이언스)와 피어링되도록 Azure Route Server를 구성하는 데 유용합니다. Route Server는 NVA에서 경로를 학습하고 가상 네트워크의 가상 머신에 대해 프로그래밍합니다. 또한 Azure Route Server는 NVA에 가상 네트워크 경로를 보급합니다. 자세한 내용은 [Azure Route Server](overview.md)를 참조하세요.

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Azure CLI를 사용하는 Route Server 배포 환경의 다이어그램." border="false":::

##  <a name="prerequisites"></a>필수 구성 요소 

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* 최신 Azure CLI가 있는지 확인하거나 포털에서 Azure Cloud Shell을 사용할 수 있습니다. 
* [Azure Route Server에 대한 서비스 제한](route-server-faq.md#limitations)을 검토합니다. 

##  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다. 

구성을 시작하려면, Azure 계정에 로그인합니다. Cloud Shell의 "사용해 보세요"를 사용하는 경우 자동으로 로그인됩니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```azurecli-interactive
az login
```

계정에 대한 구독을 확인합니다.

```azurecli-interactive
az account list
```

ExpressRoute 회로를 만들려는 구독을 선택합니다.

```azurecli-interactive
az account set \
    --subscription "<subscription ID>"
```

## <a name="create-a-resource-group-and-a-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure Route Server를 만들려면 먼저 Route Server를 호스팅할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 **myRouteServerRG** 라는 리소스 그룹을 **westus** 위치에 만듭니다.

```azurecli-interactive
az group create \
    --name myRouteServerRG \
    --location westus
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 이 예제에서는 **myVirtualNetwork** 라는 기본 가상 네트워크를 만듭니다. 가상 네트워크가 이미 있는 경우 다음 섹션으로 건너뛸 수 있습니다.

```azurecli-interactive
az network vnet create \
    --name myVirtualNetwork \
    --resource-group myRouteServerRG \
    --address-prefix 10.0.0.0/16 
``` 

### <a name="add-a-dedicated-subnet"></a>전용 서브넷 추가 

Azure Route Server에는 *RouteServerSubnet* 이라는 전용 서브넷이 필요합니다. 서브넷 크기는 최소 /27 또는 짧은 접두사(예: /26 또는 /25)여야 합니다. 그렇지 않으면 Route Server를 배포할 때 오류 메시지가 표시됩니다. [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)로 **RouteServerSubnet** 이라는 서브넷 구성을 만듭니다.

1. 다음 명령을 실행하여 가상 네트워크에 *RouteServerSubnet* 을 추가합니다.

    ```azurecli-interactive 
    az network vnet subnet create \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --address-prefix 10.0.0.0/24)
    ``` 

1. RouteServerSubnet ID를 기록합니다. *RouteServerSubnet* 의 리소스 ID를 가져오고 `subnet_id` 변수에 저장하려면 [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show)를 사용합니다.

    ```azurecli-interactive 
    subnet_id=$(az network vnet subnet show \
        --name RouteServerSubnet \
        --resource-group myRouteServerRG \
        --vnet-name myVirtualNetwork \
        --query id -o tsv) 

    echo $subnet_id
    ```

## <a name="create-the-route-server"></a>Route Server 만들기 

1. Route Server 구성을 관리하는 백엔드 서비스에 연결되도록 보장하려면 공용 IP 주소를 할당해야 합니다. [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)로 **RouteServerIP** 라는 표준 공용 IP를 만듭니다.

    ```azurecli-interactive
    az network public-ip create \
        --name RouteServerIP \
        --resource-group myRouteServerRG \
        --version IPv4 \
        --sku Standard
    ```

2. [az network routeserver create](/cli/azure/network/routeserver#az_network_routeserver_create)로 Azure Route Server를 만듭니다. 이 예제는 **myRouteServer** 라는 Azure Route Server를 만듭니다. *hosted-subnet* 은 이전 섹션에서 만든 RouteServerSubnet의 리소스 ID입니다.

    ```azurecli-interactive
    az network routeserver create \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --hosted-subnet $subnet_id \
        --public-ip-address RouteServerIP
    ``` 

## <a name="create-bgp-peering-with-an-nva"></a>NVA와 BGP 피어링 만들기 

[az network routeserver peering create](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_create)를 사용하여 Route Server와 NVA 사이에 BGP 피어링을 설정합니다. 

`peer-ip`는 NVA에 할당된 가상 네트워크 IP입니다. `peer-asn`은 NVA에 구성된 ASN(자율 시스템 번호)입니다. ASN은 65515-65520 범위 이외의 16비트 숫자가 될 수 있습니다. 이 ASN 범위는 Microsoft에서 예약되어 있습니다.

```azurecli-interactive 
az network routeserver peering create \
    --name myNVA \
    --peer-ip 192.168.0.1 \
    --peer-asn 65501 \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
``` 

중복성을 위해 다른 NVA 또는 동일한 NVA의 다른 인스턴스와 피어링을 설정하려면 다른 *PeerName*, *PeerIp* 및 *PeerAsn* 을 사용하여 위와 동일한 명령을 사용합니다.

## <a name="complete-the-configuration-on-the-nva"></a>NVA에 대한 구성 완료 

NVA에 대한 구성을 완료하고 BGP 세션을 사용하도록 설정하려면 Azure Route Server의 IP 및 ASN이 필요합니다. [az network routeserver show](/cli/azure/network/routeserver#az_network_routeserver_show)를 사용하여 이 정보를 가져올 수 있습니다.

```azurecli-interactive 
az network routeserver show \
    --name myRouteServer \
    --resource-group myRouteServerRG 
``` 

출력은 다음과 같이 표시됩니다.

``` 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>경로 교환 구성 

ExpressRoute 및 Azure VPN 게이트웨이가 동일한 가상 네트워크에 있고 이를 사용해서 경로를 교환하려면 Azure Route Server에서 경로 교환을 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> 최적의 배포를 위해서는 Azure Route Server를 만들기 전에 Azure VPN Gateway를 만들어야 합니다. 그러지 않으면 Azure VPN Gateway 배포가 실패합니다.
> 

1. Azure Route Server와 게이트웨이 사이의 경로 교환을 사용하도록 설정하려면 `--allow-b2b-traffic` 플래그를 **true** 로 설정해서 [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update)를 사용합니다.

    ```azurecli-interactive 
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic true 
    ``` 

2. Azure Route Server와 게이트웨이 사이의 경로 교환을 사용하지 않도록 설정하려면 `--allow-b2b-traffic` 플래그를 **false** 로 설정해서 [az network routerserver update](/cli/azure/network/routeserver#az_network_routeserver_update)를 사용합니다.

    ```azurecli-interactive
    az network routeserver update \
        --name myRouteServer \
        --resource-group myRouteServerRG \
        --allow-b2b-traffic false 
    ``` 

## <a name="troubleshooting"></a>문제 해결 

[az network routeserver peering list-advertised-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_advertised_routes)를 사용하여 Azure Route Server가 보급한 경로를 확인합니다.

```azurecli-interactive 
az network routeserver peering list-advertised-routes \
    --name myNVA \
    --routeserver myRouteServer \
    --resource-group myRouteServerRG
```

[az network routeserver peering list-learned-routes](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_list_learned_routes)를 사용하여 Azure Route Server가 학습한 경로를 확인합니다.

```azurecli-interactive
az network routeserver peering list-learned-routes \
    --name myNVA \
    --routeserver myRouteServer
    --resource-group myRouteServerRG \
``` 

## <a name="clean-up-resources"></a>리소스 정리

Azure Route Server가 더 이상 필요하지 않으면 첫 번째 명령을 사용하여 BGP 피어링을 제거한 후 두 번째 명령을 사용하여 Route Server를 제거합니다. 

1. [az network routeserver peering delete](/cli/azure/network/routeserver/peering#az_network_routeserver_peering_delete)를 사용하여 Azure Route Server와 NVA 사이의 BGP 피어링을 제거합니다.

    ```azurecli-interactive
    az network routeserver peering delete \
        --name myNVA \
        --routeserver myRouteServer \
        --resource-group myRouteServerRG
    ``` 

2. [az network routeserver delete](/cli/azure/network/routeserver#az_network_routeserver_delete)로 Azure Route Server를 제거합니다. 

    ```azurecli-interactive 
    az network routeserver delete \
        --name myRouteServer \
        --resource-group myRouteServerRG
    ``` 

## <a name="next-steps"></a>다음 단계

Azure Route Server를 만든 후 계속해서 Azure Route Server가 ExpressRoute 및 VPN Gateway와 상호 작용하는 방법을 알아봅니다. 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 및 Azure VPN 지원](expressroute-vpn-support.md)
 
