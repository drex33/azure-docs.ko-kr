---
title: 공용 IP 만들기 - Azure CLI
titleSuffix: Azure Virtual Network
description: Azure CLI를 사용하여 공용 IP를 만드는 방법 알아보기
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/05/2021
ms.author: allensu
ms.openlocfilehash: 8b68597abaf4a715dc55a92f2445000c1aaed0d0
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435131"
---
# <a name="create-a-public-ip-address-using-azure-cli"></a>Azure CLI를 사용하여 공용 IP 주소 만들기

이 문서에서는 Azure CLI를 사용하여 공용 IP 주소 리소스를 만드는 방법을 소개합니다. 

공용 IP를 지원하는 리소스에 대한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **myResourceGroup** 이라는 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-standard-sku-public-ip-with-zones"></a>영역이 있는 표준 SKU 공용 IP 만들기

이 섹션에서는 영역이 있는 공용 IP를 만듭니다. 공용 IP 주소는 영역 중복 또는 영역일 수 있습니다.

### <a name="zone-redundant"></a>영역 중복

>[!NOTE]
>다음 명령은 API 버전 2020-08-01이상에 대해 작동합니다.  현재 사용 중인 API 버전에 대한 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 명령을 사용하여 **myResourceGroup** 에 **myStandardZRPublicIP** 라는 표준 영역 중복 공용 IPv4 주소를 만듭니다.  

IPv6 주소를 만들려면 **버전** 매개 변수를 **IPv6** 로 수정합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> 2020-08-01 이전 API 버전의 경우 영역 매개 변수를 지정하지 않고 명령을 실행하여 영역 중복 IP 주소를 만듭니다. 
>

### <a name="zonal"></a>영역

**myResourceGroup** 에서 **myStandardZonalPublicIP** 라는 영역 2에 표준 영역 공용 IPv4 주소를 만들려면 다음 명령을 사용합니다.

IPv6 주소를 만들려면 **버전** 매개 변수를 **IPv6** 로 수정합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.


## <a name="create-standard-public-ip-without-zones"></a>영역이 없는 표준 공용 IP 만들기

이 섹션에서는 비영역 IP 주소를 만듭니다.  

>[!NOTE]
>다음 명령은 API 버전 2020-08-01이상에 대해 작동합니다.  현재 사용 중인 API 버전에 대한 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **myResourceGroup** 에서 **myStandardPublicIP** 라는 비영역 리소스로 표준 공용 IPv4 주소를 만듭니다. 

IPv6 주소를 만들려면 **버전** 매개 변수를 **IPv6** 로 수정합니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard
```
명령에서 **zone** 매개 변수를 제거하는 것은 모든 지역에서 유효합니다.  

**zone** 매개 변수를 제거하는 것은 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 선택 항목입니다.

## <a name="create-a-basic-public-ip"></a>기본 공용 IP 만들기

이 섹션에서는 기본 IP를 만듭니다. 기본 공용 IP는 가용성 영역을 지원하지 않습니다.

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 **myResourceGroup** 에 **myBasicPublicIP** 라는 기본 고정 공용 IPv4 주소를 만듭니다.

IPv6 주소를 만들려면 **버전** 매개 변수를 **IPv6** 로 수정합니다. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
시간에 따라 IP 주소를 변경할 수 있는 경우 AllocationMethod를 **동적** 으로 변경하여 **동적** IP 할당을 선택할 수 있습니다. 

>[!NOTE]
> 기본 IPv6 주소는 항상 '동적'이어야 합니다.

## <a name="routing-preference-and-tier"></a>라우팅 기본 설정 및 계층

표준 SKU 정적 공용 IPv4 주소는 라우팅 기본 설정 또는 글로벌 계층 기능을 지원합니다.

### <a name="routing-preference"></a>라우팅 기본 설정

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 사용자에게 Microsoft의 글로벌 광역 네트워크를 통해 트래픽을 전달하는 "Microsoft 네트워크"로 설정됩니다.  

**인터넷** 을 선택하면 전송 ISP 네트워크를 사용하여 비용 최적화 속도로 트래픽을 전달하는 대신 Microsoft 네트워크에서의 이동이 최소화됩니다.  

라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](./routing-preference-overview.md)을 참조하세요.

이 명령은 **인터넷** 형식의 라우팅 기본 설정을 통해 새 표준 영역 중복 공용 IPv4 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

### <a name="tier"></a>서비스 계층

공용 IP 주소는 단일 지역과 연결됩니다. **글로벌** 계층은 여러 지역에 걸쳐 IP 주소를 확장합니다. 지역 간 부하 분산 장치의 프런트 엔드에는 **글로벌** 계층이 필요합니다.  

자세한 내용은 [지역 간 부하 분산 장치](../load-balancer/cross-region-overview.md)를 참조하세요.

다음 명령은 글로벌 IPv4 주소를 만듭니다. 이 주소는 지역 간 부하 분산 장치의 프런트 엔드와 연결될 수 있습니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>글로벌 계층 주소는 가용성 영역을 지원하지 않습니다.

## <a name="additional-information"></a>추가 정보 

이 방법에 나열된 개별 매개 변수에 대한 자세한 내용은 [공용 IP 주소 관리](./virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [공용 IP 주소를 가상 머신](./associate-public-ip-address-vm.md#azure-portal)에 연결합니다.
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
