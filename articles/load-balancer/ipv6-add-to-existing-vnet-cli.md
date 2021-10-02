---
title: Azure 가상 네트워크에서 IPv4 애플리케이션에 IPv6 추가 - Azure CLI
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure CLI를 사용하여 Azure 가상 네트워크의 기존 애플리케이션에 IPv6 주소를 배포하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1077cac7bd464d20076929ba68d2a3c834cff536
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372835"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Azure 가상 네트워크에서 IPv4 애플리케이션에 IPv6 추가 - Azure CLI

이 문서에서는 Azure CLI를 사용하여 표준 Load Balancer에 대해 Azure 가상 네트워크의 IPv4 공용 IP 주소를 사용하고 있는 애플리케이션에 IPv6 주소를 추가하는 방법을 보여 줍니다. 현재 위치 업그레이드에는 가상 네트워크 및 서브넷, IPv4 + IPV6 프런트 엔드 구성을 사용하는 표준 Load Balancer, IPv4 + IPv6 구성이 있는 NIC를 사용하는 VM, 네트워크 보안 그룹 및 공용 IP가 포함됩니다.

## <a name="prerequisites"></a>필수 요건

- 이 문서에서는 [빠른 시작: 표준 Load Balancer 만들기 - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)에 설명된 대로 표준 Load Balancer를 배포했다고 가정합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-ipv6-addresses"></a>IPv6 주소 만들기

표준 Load Balancer에 대해 [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IPv6 주소를 만듭니다. 다음 예제에서는 *myResourceGroupSLB* 리소스 그룹에 *PublicIP_v6* 이라는 IPv6 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>IPv6 부하 분산 장치 프런트 엔드 구성

다음과 같이 [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create)를 사용하여 새 IPv6 IP 주소가 있는 부하 분산 장치를 구성합니다.

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6 부하 분산 장치 백 엔드 풀 구성

다음과 같이 [az network lb address pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create)를 사용하여 IPv6 주소가 있는 NIC의 백 엔드 풀을 만듭니다.

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6 부하 분산 장치 규칙 구성

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create)를 사용하여 IPv6 부하 분산 장치 규칙을 만듭니다.

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6 주소 범위 추가

다음과 같이 부하 분산 장치를 호스트하는 가상 네트워크 및 서브넷에 IPv6 주소 범위를 추가합니다.

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>NIC에 IPv6 구성 추가

다음과 같이 [az network nic ip config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create)를 사용하여 IPv6 주소가 있는 VM NIC를 구성합니다.

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure Portal에서 IPv6 이중 스택 가상 네트워크 보기

다음과 같이 Azure Portal에서 IPv6 이중 스택 가상 네트워크를 볼 수 있습니다.
1. 포털의 검색 창에 *myVnet* 을 입력합니다.
2. **myVnet** 이 검색 결과에 표시되면 이를 선택합니다. 그러면 **개요** 페이지가 *myVNet* 이라는 이중 스택 가상 네트워크에서 시작됩니다. 이중 스택 가상 네트워크에는 *mySubnet* 이라는 이중 스택 서브넷에 IPv4 및 IPv6 구성이 둘 다 있는 세 개의 NIC가 표시됩니다.

  ![Azure의 IPv6 이중 스택 가상 네트워크](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 IPv4 프런트 엔드 IP 구성을 사용하여 기존 표준 Load Balancer를 이중 스택(IPv4 및 IPv6) 구성으로 업데이트했습니다. 또한 백 엔드 풀에서 VM의 NIC에 IPv6 구성을 추가했습니다. Azure 가상 네트워크의 IPv6 지원에 관해 자세히 알아보려면 [Azure 가상 네트워크용 IPv6란?](../virtual-network/ip-services/ipv6-overview.md)을 참조하세요.
