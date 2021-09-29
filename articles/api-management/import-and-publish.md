---
title: 자습서 - Azure API Management에서 첫 번째 API 가져오기 및 게시
description: 이 자습서에서는 OpenAPI 사양 API를 Azure API Management로 가져온 다음, Azure Portal에서 API를 테스트하는 방법을 알아봅니다.
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: danlep
ms.openlocfilehash: 8ac58d354c5a92482f2cd47c59316fca9ba32025
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627105"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>자습서: 첫 번째 API 가져오기 및 게시

이 자습서에서는 JSON 형식의 OpenAPI 사양 백 엔드 API를 Azure API Management로 가져오는 방법을 보여 줍니다. Microsoft는 이 예제에 사용되는 백엔드 API를 제공하고, Azure에서 이 API를 [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)에 호스트합니다.

백엔드 API를 API Management로 가져오면 API Management API가 백엔드 API의 외관이 됩니다. 백엔드 API에 연결하지 않고도 API Management에서 요구 사항에 맞게 외관을 사용자 지정할 수 있습니다. 자세한 내용은 [API 변환 및 보호](transform-api.md)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * API Management로 API 가져오기
> * Azure Portal에서 API 테스트

가져온 후에는 Azure Portal에서 API를 관리할 수 있습니다.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="API Management의 새 API":::

## <a name="prerequisites"></a>필수 구성 요소

- [Azure API Management 용어](api-management-terminology.md)를 숙지합니다.
- [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>백 엔드 API 가져오기 및 게시

이 섹션에서는 OpenAPI 사양 백 엔드 API를 가져오고 게시하는 방법을 보여 줍니다.

1. API Management 인스턴스의 왼쪽 탐색 영역에서 **API** 를 선택합니다.
1. **OpenAPI** 타일을 선택합니다.
1. **OpenAPI 사양에서 만들기** 창에서 **전체** 를 선택합니다.
1. 다음 표의 값을 나열합니다. 그런 다음, **만들기** 를 선택하여 API를 만듭니다.

   만들기 중에 또는 나중에 **설정** 탭으로 이동하여 API 값을 설정할 수 있습니다.

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="API 만들기":::


   |설정|Value|Description|
   |-------|-----|-----------|
   |**OpenAPI 사양**|*https:\//conferenceapi.azurewebsites.net?format=json*|API를 구현하는 서비스입니다. API Management는 이 주소로 요청을 전달합니다. 서비스는 공개적으로 액세스 가능한 인터넷 주소에서 호스트되어야 합니다. |
   |**표시 이름**|이전 서비스 URL이 입력되면 API Management에서 JSON을 기반으로 하여 이 필드를 채웁니다.|[개발자 포털](api-management-howto-developer-portal.md)에 표시되는 이름입니다.|
   |**이름**|이전 서비스 URL이 입력되면 API Management에서 JSON을 기반으로 하여 이 필드를 채웁니다.|API에 대한 고유 이름입니다.|
   |**설명**|이전 서비스 URL이 입력되면 API Management에서 JSON을 기반으로 하여 이 필드를 채웁니다.|API에 대한 선택적 설명입니다.|
   |**URL 구성표**|**HTTPS**|API에 액세스하는 데 사용할 수 있는 프로토콜입니다.|
   |**API URL 접미사**|*conference*|API Management 서비스의 기준 URL에 추가된 접미사입니다. API Management는 해당 접미사로 API를 구분하므로 접미사는 지정된 게시자의 모든 API에 대해 고유해야 합니다.|
   |**태그**| |검색, 그룹화 또는 필터링을 위해 API를 구성하는 태그입니다.|
   |**제품**|**무제한**|하나 이상의 API에 대한 연결입니다. 각 API Management 인스턴스에는 두 개의 샘플 제품, 즉 **Starter** 및 **Unlimited** 입니다. 이 예제에서는 API를 **무제한** 제품과 연결하여 API를 게시합니다.<br/><br/> 여러 API를 제품에 포함시키고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 이 API를 다른 제품에 추가하려면 제품 이름을 입력하거나 선택합니다. API를 여러 제품에 추가하려면 이 단계를 반복합니다. 나중에 **설정** 페이지에서 API를 제품에 추가할 수도 있습니다.<br/><br/>  제품에 대한 자세한 내용은 [제품 만들기 및 게시](api-management-howto-add-products.md)를 참조하세요.|
   |**게이트웨이**|**관리**|API를 노출하는 API 게이트웨이입니다. 이 필드는 **Developer** 및 **Premium** 계층 서비스에서만 사용할 수 있습니다.<br/><br/>**관리** 는 API Management 서비스에 기본 제공되고 Azure에서 Microsoft가 호스트하는 게이트웨이를 나타냅니다. [자체 호스트 게이트웨이](self-hosted-gateway-overview.md)는 Premium 및 Developer 서비스 계층에서만 사용할 수 있습니다. 온-프레미스 또는 다른 클라우드에 배포할 수 있습니다.<br/><br/> 게이트웨이를 선택하지 않으면 API를 사용할 수 없으며 API 요청이 실패합니다.|
   |**API 버전 관리**|선택 또는 선택 취소|자세한 내용은 [여러 버전의 API 게시](api-management-get-started-publish-versions.md)를 참조하세요.|

   > [!NOTE]
   > API를 API 소비자에게 게시하려면 제품과 연결해야 합니다.

2. **만들기** 를 선택합니다.

API 정의를 가져오는 데 문제가 있는 경우 [알려진 문제 및 제한 사항 목록](api-management-api-import-restrictions.md)을 참조하세요.

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure Portal에서 새 API 테스트

Azure Portal에서 직접 API 작업을 호출합니다. 그러면 작업을 보고 테스트할 수 있는 편리한 방법이 제공됩니다.

1. API Management 인스턴스의 왼쪽 탐색 영역에서 **API** > **Demo Conference API** 를 선택합니다.
1. **테스트** 탭, **GetSpeakers** 를 차례로 선택합니다. 페이지에 **쿼리 매개 변수** 및 **헤더**(있는 경우)가 표시됩니다. 이 API와 연결된 구독 키에 대해 **Ocp-Apim-Subscription-Key** 가 자동으로 채워집니다.
1. **보내기** 를 선택합니다.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Azure Portal에서 API 테스트":::

   백 엔드는 **200 정상** 및 일부 데이터로 응답합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 첫 번째 API 가져오기
> * Azure Portal에서 API 테스트

제품을 만들고 게시하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
> [제품 생성 및 게시](api-management-howto-add-products.md)
