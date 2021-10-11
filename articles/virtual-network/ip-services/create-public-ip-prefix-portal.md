---
title: '빠른 시작: 공용 IP 주소 접두사 만들기 - Azure Portal'
titlesuffix: Azure Virtual Network
description: Azure Portal을 사용하여 공용 IP 주소 접두사를 만드는 방법을 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0d792a6e2fc83c16575fab59a52010b29d3d1282
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369378"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 공용 IP 주소 접두사 만들기

공용 IP 주소 접두사 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소 접두사는 표준 SKU 공용 IP 주소의 연속 범위입니다. 

공용 IP 주소 리소스를 만들 때 접두사에서 고정 공용 IP 주소를 할당하고 주소를 가상 머신, 부하 분산 장치 또는 기타 리소스에 연결할 수 있습니다. 자세한 내용은 [공용 IP 주소 접두사 개요](public-ip-address-prefix.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기

이 섹션에서는 Azure Portal을 사용하여 공용 IP 접두사를 만듭니다.

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

2. 검색 결과에서 **공용 IP 접두사** 를 선택합니다.

3. **+ 만들기** 를 선택합니다.

4. **공용 IP 접두사 만들기** 의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | 구독 | 구독 선택 |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **QuickStartCreateIPPrefix-rg** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 이름 | **myPublicIPPrefix** 를 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | IP 버전 | **IPv4**(기본값)를 그대로 둡니다. |
    | 접두사 크기 | 접두사 크기를 선택합니다. |

    :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ip-prefix.png" alt-text="Azure Portal에서 공용 IP 주소 접두사 만들기 스크린샷":::
    
    > [!NOTE]
    >IPv6 접두사를 만들려면 **IP 버전** 에 대해 **IPv6** 을 선택합니다.

     :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ipv6-prefix.png" alt-text="Azure Portal에서 공용 IPv6 주소 접두사 만들기 스크린샷":::

5. **검토 + 만들기** 탭이나 페이지 아래쪽에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.

6. **만들기** 를 선택합니다.

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>접두사로 고정 공용 IP 주소 만들기

접두사를 만들었으면 접두사로 고정 IP 주소를 만들어야 합니다. 이 섹션에서는 앞에서 만든 접두사에서 고정 IP 주소를 만듭니다.

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

2. 검색 결과에서 **공용 IP 접두사** 를 선택합니다.

3. **공용 IP 접두사** 에서 **myPublicIPPrefix** 를 선택합니다.

4. **myPublicIPPrefix** 의 **개요** 에서 **+ IP 주소 추가** 를 선택합니다.

    :::image type="content" source="./media/create-public-ip-prefix-portal/add-ip-address.png" alt-text="Azure Portal의 접두사에서 공용 IP 주소 만들기 스크린샷":::

5. **이름** 에 **myPublicIP** 를 입력합니다. 

6. 나머지 선택 항목은 기본값을 유지합니다.

7. **추가** 를 선택합니다.

    >[!NOTE]
    >표준 SKU로 만든 고정 공용 IP 주소만 접두사의 범위에서 할당할 수 있습니다. 공용 IP 주소 SKU에 대해 자세히 알아보려면 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)를 참조하세요.

8. **설정** 에서 **공용 IP 주소** 를 선택하여 생성된 IP 주소를 확인합니다.

## <a name="delete-a-prefix"></a>접두사 삭제

이 섹션에서는 접두사를 보거나 삭제하는 방법을 알아봅니다.

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다.

2. 검색 결과에서 **공용 IP 접두사** 를 선택합니다.

3. **공용 IP 접두사** 에서 **myPublicIPPrefix** 를 선택합니다.

4. **개요** 섹션에서 **삭제** 를 선택합니다.

    >[!NOTE]
    >접두사 내 주소가 공용 IP 주소 리소스에 연결되어 있으면 공용 IP 주소 리소스를 먼저 삭제해야 합니다. [공용 IP 주소 삭제](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 공용 IP 접두사와 해당 접두사의 공용 IP를 만들었습니다. 

공용 IP 접두사 작업을 마쳤으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

1. **QuickStartCreateIPPrefix-rg** 를 검색하여 선택합니다.

2. **리소스 그룹 삭제** 를 선택합니다.

3. **리소스 그룹 이름 입력** 에 대해 **QuickStartCreateIPPrefix-rg** 를 입력하고 **삭제** 를 선택합니다.
## <a name="next-steps"></a>다음 단계

공용 IP 주소를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 공용 IP 주소 만들기](create-public-ip-portal.md)
