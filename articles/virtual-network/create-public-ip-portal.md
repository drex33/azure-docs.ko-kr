---
title: 공용 IP 만들기 - Azure Portal
titleSuffix: Azure Virtual Network
description: Azure Portal을 사용하여 공용 IP를 만드는 방법 알아보기
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/04/2021
ms.author: allensu
ms.openlocfilehash: 6cf9e86222d98835ea5355440343df96794dbfd4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435074"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Azure Portal을 사용하여 공용 IP 주소 만들기

이 문서에서는 Azure Portal을 사용하여 공용 IP 주소 리소스를 만드는 방법을 소개합니다. 

이 공용 IP와 연결할 수 있는 리소스와 기본 및 표준 SKU 간의 차이점에 대한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)를 참조하세요. 

## <a name="create-a-standard-sku-public-ip-address"></a>표준 SKU 공용 IP 주소 만들기

다음 단계를 사용하여 **myStandardPublicIP** 라는 표준 공용 IPv4 주소를 만듭니다.  

> [!NOTE]
>IPv6 주소를 만들려면 **IP 버전** 매개 변수에 대해 **IPv6** 을 선택합니다. 배포에 이중 스택 구성(IPv4 및 IPv6 주소)이 필요한 경우 **둘 다** 를 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

3. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

4. **+ 만들기** 를 선택합니다.

5. **공용 IP 주소 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | IPv4를 선택합니다.              |    
    | SKU                     | **표준** 선택         |
    | 계층                   | **지역** 선택    </br> 자세한 내용은 [라우팅 기본 설정 및 계층](#routing-preference-and-tier)을 참조하세요.     |
    | Name                    | **myStandardPublicIP** 입력          |
    | IP 주소 할당   | **고정** 으로 잠금                |
    | 라우팅 기본 설정     | **Microsoft 네트워크** 를 선택합니다. </br> 자세한 내용은 [라우팅 기본 설정 및 계층](#routing-preference-and-tier)을 참조하세요. |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.        |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독 선택   |
    | Resource group          | **새로 만들기** 를 선택하고, **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **(미국) 미국 동부 2** 선택     |
    | 가용성 영역       | **영역 없음** 선택 |

6. **만들기** 를 선택합니다.

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Azure Portal의 표준 IP 주소 만들기" border="false":::

> [!NOTE]
> [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서는 영역 없음(기본 옵션), 특정 영역 또는 영역 중복을 선택하는 옵션이 있습니다. 선택은 특정 도메인 오류 요구 사항에 따라 달라집니다. 가용성 영역이 없는 지역에서는 이 필드가 표시되지 않습니다. </br> 가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

> [!NOTE]
> IPv6은 현재 라우팅 기본 설정 또는 지역 간 부하 분산(글로벌 계층)에서 지원되지 않습니다.

## <a name="create-a-basic-sku-public-ip-address"></a>기본 SKU 공용 IP 주소 만들기

이 섹션에서는 **myBasicPublicIP** 라는 기본 공용 IPv4 주소를 만듭니다. 

> [!NOTE]
> 기본 공용 IP는 가용성 영역을 지원하지 않습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** 를 선택합니다. 
3. 검색 상자에서 **공용 IP 주소** 를 입력합니다. 검색 결과에서 **공용 IP 주소** 를 선택합니다.
4. **공용 IP 주소** 페이지에서 **만들기** 를 선택합니다.
5. **공용 IP 주소 만들기** 페이지에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | IPv4를 선택합니다.                 |    
    | SKU                     | **기본** 을 선택합니다.         |
    | 이름                    | **myBasicPublicIP** 입력          |
    | IP 주소 할당   | **고정** 선택            |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.       |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독을 선택합니다.   |
    | Resource group          | **새로 만들기** 를 선택하고, **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **(미국) 미국 동부 2** 선택      |

6. **만들기** 를 선택합니다.

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Azure Portal에서 기본 IP 주소 만들기" border="false":::

시간에 따라 IP 주소를 변경할 수 있는 경우 AllocationMethod를 **동적** 으로 변경하여 **동적** IP 할당을 선택할 수 있습니다. 

---

## <a name="routing-preference-and-tier"></a>라우팅 기본 설정 및 계층

표준 SKU 정적 공용 IPv4 주소는 라우팅 기본 설정 또는 글로벌 계층 기능을 지원합니다. 새 공용 IP 주소를 만들 때 라우팅 기본 설정 및 계층을 선택합니다.

### <a name="routing-preference"></a>라우팅 기본 설정

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 사용자에게 Microsoft의 글로벌 광역 네트워크를 통해 트래픽을 전달하는 "Microsoft 네트워크"로 설정됩니다.  

**인터넷** 을 선택하면 전송 ISP 네트워크를 사용하여 비용 최적화 속도로 트래픽을 전달하는 대신 Microsoft 네트워크에서의 이동이 최소화됩니다.  

라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](./routing-preference-overview.md)을 참조하세요.

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Azure Portal에서 라우팅 기본 설정 구성" border="false":::

### <a name="tier"></a>서비스 계층

공용 IP 주소는 단일 지역과 연결됩니다. **글로벌** 계층은 여러 지역에 걸쳐 IP 주소를 확장합니다. 지역 간 부하 분산 장치의 프런트 엔드에는 **글로벌** 계층이 필요합니다.  

자세한 내용은 [지역 간 부하 분산 장치](../load-balancer/cross-region-overview.md)를 참조하세요.

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Azure Portal에서 계층 구성" border="false":::

## <a name="additional-information"></a>추가 정보 

위에 나열된 개별 필드에 대한 자세한 내용은 [공용 IP 주소 관리](./virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [공용 IP 주소를 가상 머신에](./associate-public-ip-address-vm.md#azure-portal) 연결
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.