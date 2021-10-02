---
title: 공용 IP 주소 업그레이드 - Azure Portal
description: 이 문서에서는 Azure Portal을 사용하여 기본 SKU 공용 IP 주소를 업그레이드하는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: f436cd2d113909a65b62891f9fbfb5965d06a981
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373052"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-portal"></a>Azure Portal을 사용하여 공용 IP 주소 업그레이드

Azure 공용 IP 주소는 기본 또는 표준 SKU를 사용하여 만듭니다. SKU는 할당 방법, 기능 지원, 연결할 수 있는 리소스를 포함해 관련 기능을 결정합니다. 

이 문서에서는 고정 기본 SKU 공용 IP 주소를 Azure Portal의 표준 SKU로 업그레이드하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* 구독의 고정 기본 SKU 공용 IP 주소입니다. 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](../../virtual-network/create-public-ip-portal.md#create-a-basic-sku-public-ip-address)을 참조하세요.

## <a name="upgrade-public-ip-address"></a>공용 IP 주소 업그레이드

이 섹션에서는 Azure Portal에 로그인하여 고정 기본 SKU 공용 IP를 표준 SKU로 업그레이드합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

3. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

4. **공용 IP 주소** 에서 **myBasicPublicIP** 또는 업그레이드하려는 IP 주소를 선택합니다.

5. **myBasicPublicIP** 의 개요 섹션 맨 위에 있는 업그레이드 배너를 선택합니다.

    :::image type="content" source="./media/public-ip-upgrade-portal/upgrade-ip-portal.png" alt-text="Azure Portal의 기본 IP 주소 업그레이드" border="true":::

    > [!NOTE]
    > 업그레이드할 기본 공용 IP에는 고정 할당 형식이 있어야 합니다. 동적으로 할당된 IP 주소를 업그레이드하려고 하면 IP를 업그레이드할 수 없다는 경고가 표시됩니다.

6.  **승인** 확인란을 선택합니다. **업그레이드** 를 선택합니다.

    > [!WARNING]
    > 기본 공용 IP를 표준 SKU로 업그레이드하는 작업은 되돌릴 수 없습니다. 기본에서 표준 SKU로 업그레이드된 공용 IP는 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)을 보장하지 않습니다.

## <a name="verify-upgrade"></a>업그레이드 확인

이 섹션에서는 공용 IP 주소가 이제 표준 SKU인지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

3. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

4. **공용 IP 주소** 에서 **myBasicPublicIP** 또는 업그레이드한 IP 주소를 선택합니다.

5. SKU가 **개요** 섹션에서 **표준** 으로 나열되는지 확인합니다.

    :::image type="content" source="./media/public-ip-upgrade-portal/verify-upgrade-ip.png" alt-text="공용 IP 주소가 표준 SKU인지 확인합니다." border="true":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 SKU 공용 IP 주소를 표준 SKU로 업그레이드합니다.

Azure의 공용 IP 주소에 대한 자세한 내용은 다음을 참조하세요.

- [Azure의 공용 IP 주소](public-ip-addresses.md)
- [공용 IP 만들기 - Azure Portal](../../virtual-network/create-public-ip-portal.md)

