---
title: '빠른 시작: 공용 IP 주소 만들기 - Azure Portal'
titleSuffix: Azure Virtual Network
description: 이 빠른 시작에서는 표준 또는 기본 SKU 공용 IP 주소를 만드는 방법을 알아봅니다. 라우팅 기본 설정 및 계층에 대해서도 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.custom: template-quickstart
ms.openlocfilehash: d45e48120c69eeb41ec8bf741e32437f77d2ec69
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225027"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 공용 IP 주소 만들기

이 빠른 시작에서는 Azure 공용 IP 주소를 만드는 방법을 알아봅니다. Azure의 공용 IP 주소는 Azure 리소스에 대한 공용 연결에 사용됩니다. 공용 IP 주소는 기본 및 표준의 두 가지 SKU로 제공됩니다. 공용 IP 주소의 두 계층(지역 및 전역)을 사용할 수 있습니다.  공용 IP 주소의 라우팅 기본 설정은 생성 시 설정됩니다. 인터넷 라우팅 및 Microsoft 네트워크 라우팅은 사용 가능한 선택 항목입니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

---

# <a name="standard-sku"></a>[**표준 SKU**](#tab/option-1-create-public-ip-standard)

>[!NOTE]
>표준 SKU 공용 IP는 프로덕션 워크로드용으로 권장됩니다.  SKU에 대한 자세한 내용은 **[공용 IP 주소](public-ip-addresses.md)** 를 참조하세요.

## <a name="create-a-standard-sku-public-ip-address"></a>표준 SKU 공용 IP 주소 만들기

다음 단계를 사용하여 **myStandardPublicIP** 라는 표준 공용 IPv4 주소를 만듭니다. 

> [!NOTE]
>IPv6 주소를 만들려면 **IP 버전** 매개 변수에 대해 **IPv6** 을 선택합니다. 배포에 이중 스택 구성(IPv4 및 IPv6 주소)이 필요한 경우 **둘 다** 를 선택합니다.

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

2. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

3. **+ 만들기** 를 선택합니다.

4. **공용 IP 주소 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | IPv4를 선택합니다.              |    
    | SKU                     | **표준** 선택         |
    | 계층                   | **지역** 선택     |
    | Name                    | **myStandardPublicIP** 입력          |
    | IP 주소 할당   | **고정** 으로 잠금                |
    | 라우팅 기본 설정     | **Microsoft 네트워크** 를 선택합니다. |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.        |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독 선택   |
    | Resource group          | **새로 만들기** 를 선택하고 **QuickStartCreateIP-rg** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **(미국) 미국 동부 2** 선택     |
    | 가용성 영역       | **영역 없음** 선택 |

5. **만들기** 를 선택합니다.

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Azure Portal에서 표준 IP 주소 만들기 스크린샷" border="false":::

> [!NOTE]
> [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서는 영역 없음(기본 옵션), 특정 영역 또는 영역 중복을 선택하는 옵션이 있습니다. 선택은 특정 도메인 오류 요구 사항에 따라 달라집니다. 가용성 영역이 없는 지역에서는 이 필드가 표시되지 않습니다. </br> 가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](../../availability-zones/az-overview.md)를 참조하세요.

위에서 만든 공용 IP 주소를 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 자습서 페이지의 CLI 섹션을 사용합니다. [공용 IP 주소를 가상 머신에 연결](./associate-public-ip-address-vm.md#azure-cli)하여 공용 IP를 VM에 연결합니다. 위에서 만든 공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)와 연결할 수도 있습니다. 공용 IP 주소는 부하가 분산된 VIP(가상 IP 주소)로 사용됩니다.

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-1-create-public-ip-basic)

>[!NOTE]
>표준 SKU 공용 IP는 프로덕션 워크로드용으로 권장됩니다.  SKU에 대한 자세한 내용은 **[공용 IP 주소](public-ip-addresses.md)** 를 참조하세요.

## <a name="create-a-basic-sku-public-ip-address"></a>기본 SKU 공용 IP 주소 만들기

이 섹션에서는 **myBasicPublicIP** 라는 기본 공용 IPv4 주소를 만듭니다. 

> [!NOTE]
> 기본 공용 IP는 가용성 영역을 지원하지 않습니다.

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

2. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

3. **+ 만들기** 를 선택합니다.

4. **공용 IP 주소 만들기** 페이지에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | **IPv4** 를 선택합니다.                |    
    | SKU                     | **기본** 을 선택합니다.         |
    | 이름                    | **myBasicPublicIP** 입력          |
    | IP 주소 할당   | **고정** 선택            |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.       |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독을 선택합니다.   |
    | Resource group          | **새로 만들기** 를 선택하고 **QuickStartCreateIP-rg** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **(미국) 미국 동부 2** 선택      |

5. **만들기** 를 선택합니다.

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Azure Portal에서 기본 IP 주소 만들기 스크린샷" border="true":::

시간에 따라 IP 주소를 변경할 수 있는 경우 AllocationMethod를 **동적** 으로 변경하여 **동적** IP 할당을 선택할 수 있습니다. 

# <a name="routing-preference"></a>[**라우팅 기본 설정**](#tab/option-1-create-public-ip-routing-preference)

이 섹션에서는 공용 IP 주소에 대해 ISP 네트워크(**인터넷** 옵션)를 통해 [라우팅 기본 설정](routing-preference-overview.md)을 구성하는 방법을 보여줍니다. 공용 IP 주소를 만든 후에는 다음 Azure와 연결할 수 있습니다.

* 가상 머신
* 가상 머신 크기 집합
* AKS(Azure Kubernetes Service)
* 인터넷 연결 부하 분산 장치
* Application Gateway
* Azure Firewall

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 모든 Azure 서비스에 대해 Microsoft 글로벌 네트워크로 설정되며 모든 Azure 서비스에 연결할 수 있습니다.

> [!NOTE]
>IPv6 주소를 만들려면 **IP 버전** 매개 변수에 대해 **IPv6** 을 선택합니다. 배포에 이중 스택 구성(IPv4 및 IPv6 주소)이 필요한 경우 **둘 다** 를 선택합니다.

## <a name="create-a-public-ip-with-internet-routing"></a>인터넷 라우팅을 사용하여 공용 IP 만들기

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

2. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

3. **+ 만들기** 를 선택합니다.

4. **공용 IP 주소 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | IPv4를 선택합니다.              |    
    | SKU                     | **표준** 선택         |
    | 계층                   | **지역** 선택     |
    | 이름                    | **myStandardPublicIP-RP** 입력          |
    | IP 주소 할당   | **고정** 으로 잠금                |
    | 라우팅 기본 설정     | **인터넷** 을 선택합니다. |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.        |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독 선택   |
    | Resource group          | **새로 만들기** 를 선택하고 **QuickStartCreateIP-rg** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **(미국) 미국 동부 2** 선택     |
    | 가용성 영역       | **영역 중복** 을 선택합니다. |

5. **생성** 를 선택합니다.

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Azure Portal에서 라우팅 기본 설정 구성의 스크린샷" border="true":::

> [!NOTE]
> 공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 그러나 라우팅 기본 설정은 현재 IPV4만 지원합니다.

> [!NOTE]
> [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서는 영역 없음(기본 옵션), 특정 영역 또는 영역 중복을 선택하는 옵션이 있습니다. 선택은 특정 도메인 오류 요구 사항에 따라 달라집니다. 가용성 영역이 없는 지역에서는 이 필드가 표시되지 않습니다. </br> 가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](../../availability-zones/az-overview.md)를 참조하세요.

위에서 만든 공용 IP 주소를 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 자습서 페이지의 CLI 섹션을 사용합니다. [공용 IP 주소를 가상 머신에 연결](./associate-public-ip-address-vm.md#azure-cli)하여 공용 IP를 VM에 연결합니다. 위에서 만든 공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)와 연결할 수도 있습니다. 공용 IP 주소는 부하가 분산된 VIP(가상 IP 주소)로 사용됩니다.

# <a name="tier"></a>[**서비스 계층**](#tab/option-1-create-public-ip-tier)

공용 IP 주소는 단일 지역과 연결됩니다. **글로벌** 계층은 여러 지역에 걸쳐 IP 주소를 확장합니다. 지역 간 부하 분산 장치의 프런트 엔드에는 **글로벌** 계층이 필요합니다.  

자세한 내용은 [지역 간 부하 분산 장치](../../load-balancer/cross-region-overview.md)를 참조하세요.

## <a name="create-a-global-tier-public-ip"></a>글로벌 계층 공용 IP 만들기

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

2. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

3. **+ 만들기** 를 선택합니다.

4. **공용 IP 주소 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | IPv4를 선택합니다.              |    
    | SKU                     | **표준** 선택         |
    | 계층                   | **글로벌** 을 선택합니다.     |
    | Name                    | **myStandardPublicIP-Global** 입력          |
    | IP 주소 할당   | **고정** 으로 잠금                |
    | 라우팅 기본 설정     | **Microsoft** 를 선택합니다. |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.        |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독 선택   |
    | Resource group          | **새로 만들기** 를 선택하고 **QuickStartCreateIP-rg** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **(미국) 미국 동부 2** 선택     |
    | 가용성 영역       | **영역 중복** 을 선택합니다. |

5. **생성** 를 선택합니다.

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Azure Portal에서 계층 구성의 스크린샷" border="true":::

위에서 만든 IP 주소를 지역 간 부하 분산 장치에 연결할 수 있습니다. 자세한 내용은 [자습서: Azure Portal을 사용하여 지역 간 부하 분산 장치 만들기](../../load-balancer/tutorial-cross-region-portal.md)를 참조하세요.

---

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 공용 IP 주소를 삭제합니다.

1. 포털 맨 위에 있는 검색 상자에 **리소스 그룹** 을 입력합니다.

2. 검색 결과에서 **리소스 그룹** 을 선택합니다.

3. **QuickStartCreateIP-rg** 를 선택합니다.

4. **리소스 그룹 삭제** 를 선택합니다.

5. **리소스 그룹 이름 입력** 에 대해 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

공용 IP 접두사를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 공용 IP 접두사 만들기](create-public-ip-prefix-portal.md)