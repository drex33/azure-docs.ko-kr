---
title: Microsoft Azure Maps Creator 관리
description: 이 문서에서는 Microsoft Azure Maps Creator를 관리하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 124b75aa0a80f84299fd0e113f2d4d7f1a427213
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400209"
---
# <a name="manage-azure-maps-creator"></a>Azure Maps Creator 관리

Azure Maps Creator를 사용하면 프라이빗 실내 맵 데이터를 만들 수 있습니다. Azure Maps API 및 실내 맵 모듈을 사용하여 대화형 및 동적 실내 지도 웹 애플리케이션을 개발할 수 있습니다. 가격 책정 정보는 Azure [지도 가격 책정의](https://aka.ms/CreatorPricing) *작성자* 섹션을 참조하세요.

이 문서에서는 Azure Maps 계정에서 Creator 리소스를 만들고 삭제하는 단계를 안내합니다.

## <a name="create-creator-resource"></a>Creator 리소스 만들기

1. [Azure 포털](https://portal.azure.com)

2. Azure Portal 메뉴로 이동합니다. **모든 리소스** 를 선택한 다음, Azure Maps 계정을 선택합니다.

      :::image type="content" border="true" source="./media/how-to-manage-creator/select-all-resources.png" alt-text="Azure Maps 계정 선택":::

3. 탐색 창에서 **Creator 개요** 를 선택한 다음, **만들기** 를 선택합니다.

    :::image type="content" border="true" source="./media/how-to-manage-creator/creator-blade-settings.png" alt-text="Azure Maps Creator 만들기 페이지":::

4. Creator 리소스의 이름, 위치 및 맵 프로비저닝 스토리지 단위를 입력한 다음, **검토 + 만들기를** 선택합니다.

   :::image type="content" source="./media/how-to-manage-creator/creator-creation-dialog.png" alt-text="Creator 계정 정보 페이지 입력":::

5. 설정을 검토한 다음, **만들기** 를 선택합니다.

    :::image type="content" source="./media/how-to-manage-creator/creator-create-dialog.png" alt-text="Creator 계정 설정 페이지 확인":::

    배포가 완료되면 성공 또는 실패 메시지가 포함된 페이지가 표시됩니다.

    :::image type="content" source="./media/how-to-manage-creator/creator-resource-created.png" alt-text="리소스 배포 상태 페이지":::

6. **리소스로 이동** 을 선택합니다. Creator 리소스 보기 페이지에는 Creator 리소스의 상태와 선택된 인구 통계 지역이 표시됩니다.
      :::image type="content" source="./media/how-to-manage-creator/creator-resource-view.png" alt-text="Creator 상태 페이지":::

   >[!NOTE]
   >Azure Maps 계정으로 돌아가려면 탐색 창에서 **Azure Maps 계정** 을 선택합니다.

## <a name="delete-creator-resource"></a>Creator 리소스 삭제

Creator 리소스를 삭제하려면:

1. Azure Maps 계정의 **Creator** 에서 **개요** 를 선택합니다.

2. **삭제** 를 선택합니다.

    >[!WARNING]
    >Azure Maps 계정의 Creator 리소스를 삭제하는 경우 Creator 서비스를 사용하여 만든 변환, 데이터 세트, 타일 세트 및 기능 상태 세트도 삭제됩니다. Creator 리소스가 삭제되면 실행 취소할 수 없습니다.

     :::image type="content" source="./media/how-to-manage-creator/creator-delete.png" alt-text="삭제 단추가 있는 Creator 페이지":::

3. Creator 리소스의 이름을 입력하여 삭제를 확인하라는 메시지가 표시됩니다. 리소스가 삭제되면 다음과 같은 확인 페이지가 표시됩니다.

     :::image type="content" source="./media/how-to-manage-creator/creator-confirm-delete.png" alt-text="삭제 확인이 있는 Creator 페이지":::

## <a name="authentication"></a>인증

Creator는 IAM(Azure Maps Access Control) 설정을 상속합니다. 데이터 액세스에 대한 모든 API 호출은 인증 및 권한 부여 규칙과 함께 전송되어야 합니다.

Creator 사용량 현황 데이터는 Azure Maps 사용량 차트 및 활동 로그에 통합됩니다.  자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

>[!Important]
>다음을 사용하는 것이 좋습니다.
>
> * Creator 서비스를 사용하여 Azure Maps 계정으로 빌드된 모든 솔루션에서 Azure Active Directory(Azure AD). Azure AD에 대한 자세한 내용은 [Azure AD 인증](azure-maps-authentication.md#azure-ad-authentication)을 참조하세요.
>
>* RBAC(역할 기반 액세스 제어) 설정. 이러한 설정을 사용하여 맵 제작자는 Azure Maps Data Contributor 역할을 할 수 있고, Creator 맵 데이터 사용자는 Azure Maps Data Reader 역할을 할 수 있습니다. 자세한 내용은 [역할 기반 액세스 제어를 통해 권한 부여](azure-maps-authentication.md#authorization-with-role-based-access-control)를 참조하세요.

## <a name="access-to-creator-services"></a>Creator 서비스에 대한 액세스

Creator 서비스 및 Creator에서 호스트되는 데이터를 사용하는 서비스(예: Render Service)는 지리적 URL에서 액세스할 수 있습니다. 지리적 URL은 만드는 동안 선택한 위치에 따라 결정됩니다. 예를 들어, 미국 지리적 위치의 한 지역에서 Creator를 만든 경우 Conversion 서비스에 대한 모든 호출을 `us.atlas.microsoft.com/conversions`로 전송해야 합니다. 지리적 위치에 대한 지역의 매핑을 보려면 [Creator 서비스 지리적 범위](creator-geographic-scope.md)를 참조하세요.

또한 Creator로 가져온 모든 데이터는 Creator 리소스와 동일한 지리적 위치에 업로드해야 합니다. 예를 들어 Creator가 미국에서 프로비저닝되는 경우 모든 원시 데이터를 `us.atlas.microsoft.com/mapData/upload`를 통해 업로드해야 합니다.

## <a name="next-steps"></a>다음 단계

실내 매핑용 Creator 서비스 소개:

> [!div class="nextstepaction"]
> [데이터 업로드](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 변환](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 세트](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [타일 세트](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [기능 상태 집합](creator-indoor-maps.md#feature-statesets)

Creator 서비스를 사용하여 애플리케이션에서 실내 맵을 렌더링하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps Creator 리소스](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [실내 맵 동적 스타일 지정](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Indoor Maps 모듈 사용](how-to-use-indoor-module.md)
