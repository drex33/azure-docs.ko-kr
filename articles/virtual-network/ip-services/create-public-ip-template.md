---
title: '빠른 시작: Resource Manager 템플릿을 사용하여 공용 IP 만들기'
titleSuffix: Azure Virtual Network
description: Resource Manager 템플릿을 사용하여 공용 IP 만드는 방법 알아보기
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: cb90b716351802271a12fc72c1ead9e4fac2968e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456426"
---
# <a name="quickstart-create-a-public-ip-address-using-a-resource-manager-template"></a>빠른 시작: Resource Manager 템플릿을 사용하여 공용 IP 주소 만들기

이 문서에는 Resource Manager 템플릿 내에서 공용 IP 주소 리소스를 만드는 방법이 나와 있습니다.

이 공용 IP와 연결할 수 있는 리소스와 기본 및 표준 SKU 간의 차이점에 대한 자세한 내용은 [공용 IP 주소](public-ip-addresses.md)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* Azure 구독의 리소스 그룹.
* 공용 IP 섹션에 대한 Azure Resource Manager 템플릿.

## <a name="create-standard-sku-public-ip-with-zones"></a>영역이 있는 표준 SKU 공용 IP 만들기

이 섹션에서는 영역이 있는 공용 IP를 만듭니다. 공용 IP 주소는 영역 중복 또는 영역일 수 있습니다.

### <a name="zone-redundant"></a>영역 중복

이 섹션의 코드는 **myStandardPublicIP** 라는 표준 영역 중복 공용 IPv4 주소를 만듭니다.

IPv6 주소를 만들려면 **`publicIPAddressVersion`** 매개 변수를 **IPv6** 로 수정합니다.

추가할 템플릿 섹션:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "1",
    "2",
    "3"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```
> [!IMPORTANT]
> 2020년 8월 1일 이전 API 버전의 경우, 표준 SKU에 대한 영역 매개 변수를 지정하지 않고 위의 코드를 사용하여 영역 중복 IP 주소를 만듭니다. 
>

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

### <a name="zonal"></a>영역

이 섹션의 코드는 **myStandardPublicIP-zonal** 이라는 표준 영역 공용 IPv4 주소를 만듭니다. 

영역 2 표준 영역 공용 IP 주소를 만들려면 **‘zones’** 속성에 ‘2’를 포함합니다.

추가할 템플릿 섹션:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-zonal",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "2"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

## <a name="create-standard-public-ip-without-zones"></a>영역이 없는 표준 공용 IP 만들기

이 섹션에서는 비영역 IP 주소를 만듭니다. 

이 섹션의 코드는 **myStandardPublicIP** 라는 표준 비영역 공용 IPv4 주소를 만듭니다. 코드 섹션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있든, 없든 모든 지역에서 유효합니다.

IPv6 주소를 만들려면 **publicIPAddressVersion** 매개 변수를 **IPv6** 으로 수정합니다.

추가할 템플릿 섹션:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-nozone",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```
> [!IMPORTANT]
> 2020년 8월 1일 이전 API 버전의 경우, 표준 SKU에 대한 영역 매개 변수를 지정하지 않으면 영역 중복 IP 주소가 만들어집니다. 
>


## <a name="create-a-basic-public-ip"></a>기본 공용 IP 만들기

이 섹션에서는 기본 IP를 만듭니다. 기본 공용 IP는 가용성 영역을 지원하지 않습니다. 

이 섹션의 코드는 **myBasicPublicIP** 라는 기본 공용 IP 주소를 만듭니다.

IPv6 주소를 만들려면 **publicIPAddressVersion** 매개 변수를 **IPv6** 으로 수정합니다. 

추가할 템플릿 섹션:

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myBasicPublicIP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Basic"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

시간이 흐름에 따라 IP 주소를 변경할 수 있는 경우 AllocationMethod를 **Dynamic** 으로 변경하여 **publicIPAllocationMethod** IP 할당을 선택할 수 있습니다. 

>[!NOTE]
> 기본 IPv6 주소는 항상 'Dynamic'이어야 합니다.

## <a name="routing-preference-and-tier"></a>라우팅 기본 설정 및 계층

표준 SKU 고정 공용 IPv4 주소는 라우팅 기본 설정 또는 글로벌 계층 기능을 지원합니다.

### <a name="routing-preference"></a>라우팅 기본 설정

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 사용자에게 Microsoft의 글로벌 광역 네트워크를 통해 트래픽을 전달하는 "Microsoft 네트워크"로 설정됩니다.  

**인터넷** 을 선택하면 전송 ISP 네트워크를 사용하여 비용 최적화 속도로 트래픽을 전달하는 대신 Microsoft 네트워크에서의 이동이 최소화됩니다.  

라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](routing-preference-overview.md)을 참조하세요.

표준 영역 중복 공용 IPv4 주소에 대해 인터넷 라우팅 기본 설정을 사용하려면 템플릿 섹션이 다음과 유사해야 합니다.

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardZRPublicIP-RP",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard"
  },
  "zones": [
    "1",
    "2",
    "3"
  ],
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4",
    "ipTags": [
      {
        "ipTagType": "RoutingPreference",
        "tag": "Internet"
      }
    ]
  }
}
```

### <a name="tier"></a>서비스 계층

공용 IP 주소는 단일 지역과 연결됩니다. **글로벌** 계층은 여러 지역에 걸쳐 IP 주소를 확장합니다. 지역 간 부하 분산 장치의 프런트 엔드에는 **글로벌** 계층이 필요합니다.  

자세한 내용은 [지역 간 부하 분산 장치](../../load-balancer/cross-region-overview.md)를 참조하세요.

표준 글로벌 공용 IPv4 주소를 사용하려면 템플릿 섹션이 다음과 유사해야 합니다.

```JSON
{
  "apiVersion": "2020-08-01",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "myStandardPublicIP-Global",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "Standard",
    "tier": "Global"
  },
  "properties": {
    "publicIPAllocationMethod": "Static",
    "publicIPAddressVersion": "IPv4"
  }
```

## <a name="additional-information"></a>추가 정보 

이 문서에 나열된 공용 IP 속성에 대한 자세한 내용은 [공용 IP 주소 관리](virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [공용 IP 주소를 가상 머신에](./associate-public-ip-address-vm.md#azure-portal) 연결
- Azure에서 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
