---
title: 리소스 관리를 위한 프라이빗 링크 만들기 - Azure Portal
description: Azure Portal을 사용하여 리소스 관리를 위한 프라이빗 링크를 만듭니다.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 4d829dbc75aa384e57f706f3e689e8b36365a9c1
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137764"
---
# <a name="use-portal-to-create-private-link-for-managing-azure-resources"></a>포털을 사용하여 Azure 리소스 관리를 위한 프라이빗 링크를 만듭니다.

이 문서에서는 [Azure Private Link](../../private-link/index.yml)를 사용하여 구독의 리소스 관리에 대한 액세스를 제한하는 방법을 설명합니다. 프라이빗 액세스를 통해 리소스 관리를 설정하기 위해 Azure Portal을 사용하는 방법을 보여 줍니다.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>리소스 관리 프라이빗 링크 만들기

리소스 관리 프라이빗 링크를 만들면 프라이빗 링크 연결이 자동으로 만들어집니다.

1. [포털](https://portal.azure.com)에서 **리소스 관리 프라이빗 링크** 를 검색하고 사용 가능한 옵션 중에서 선택합니다.

   :::image type="content" source="./media/create-private-link-access-portal/search.png" alt-text="리소스 관리 프라이빗 링크 검색":::

1. 구독에 리소스 관리 프라이빗 링크가 아직 없는 경우 빈 페이지가 표시됩니다. **리소스 관리 프라이빗 링크 만들기** 를 선택합니다.

   :::image type="content" source="./media/create-private-link-access-portal/start-create.png" alt-text="리소스 관리 프라이빗 링크 만들기 선택":::

1. 새 리소스 관리 프라이빗 링크에 대한 값을 제공합니다. 선택한 디렉터리의 루트 관리 그룹이 새 리소스에 사용됩니다. **검토 + 만들기** 를 선택합니다.

   :::image type="content" source="./media/create-private-link-access-portal/provide-values.png" alt-text="리소스 관리 프라이빗 링크의 값 지정":::

1. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이제 리소스 관리 프라이빗 링크를 참조하는 프라이빗 엔드포인트를 만듭니다.

1. **Private Link 센터** 로 이동합니다. **개인 끝점 만들기** 를 선택 합니다.

   :::image type="content" source="./media/create-private-link-access-portal/private-link-center.png" alt-text="Private Link 센터 선택":::

1. **기본** 탭에서 프라이빗 엔드포인트에 대한 값을 제공합니다.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-basics.png" alt-text="기본 사항에 대한 값 제공":::

1. **리소스** 탭에서 **내 디렉터리의 Azure 리소스에 연결** 을 선택합니다. 리소스 종류으로 **Microsoft.Authorization/resourceManagementPrivateLinks** 를 선택합니다. 대상 하위 리소스로 **ResourceManagement** 를 선택합니다.

   :::image type="content" source="./media/create-private-link-access-portal/private-endpoint-resource.png" alt-text="리소스 값 제공":::

1. **구성** 탭에서 가상 네트워크를 선택합니다. 프라이빗 DNS 영역과 통합하는 것이 좋습니다. **검토 + 만들기** 를 선택합니다.

1. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

## <a name="verify-private-dns-zone"></a>프라이빗 DNS 영역 확인

환경이 올바르게 구성되었는지 확인하려면 DNS 영역의 로컬 IP 주소를 확인합니다.

1. 프라이빗 엔드포인트를 배포한 리소스 그룹에서 **privatelink.azure.com** 이라는 프라이빗 DNS 영역 리소스를 선택합니다.

1. **management** 라는 레코드 집합에 유효한 로컬 IP 주소가 있는지 확인합니다.

   :::image type="content" source="./media/create-private-link-access-portal/verify.png" alt-text="로컬 IP 주소 확인":::

## <a name="next-steps"></a>다음 단계

프라이빗 링크에 대해 자세히 알아보려면 [Azure Private Link](../../private-link/index.yml)를 참조하세요.
