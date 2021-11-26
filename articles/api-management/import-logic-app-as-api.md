---
title: Azure Portal에서 논리 앱을 API로 가져오기 | Microsoft Docs
description: 이 문서에서는 APIM(API Management)을 사용하여 논리 앱을 API로 가져오는 방법을 보여줍니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: danlep
ms.openlocfilehash: ce15a6008ab5c9370389c220d1807e376e5f6118
ms.sourcegitcommit: 4298f71f502c70d601a7c58b28fc7bca62be3595
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133133299"
---
# <a name="import-a-logic-app-as-an-api"></a>논리 앱을 API로 가져오기

이 문서에서는 논리 앱을 API로 가져오고 가져온 API를 테스트하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> -   논리 앱을 API로 가져오기
> -   Azure Portal에서 API 테스트

## <a name="prerequisites"></a>사전 요구 사항

-   다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
-   HTTP 엔드포인트를 노출하는 구독에 Logic Apps가 있는지 확인합니다. 자세한 내용은 [HTTP 엔드포인트를 통해 워크플로 트리거](../logic-apps/logic-apps-http-endpoint.md)를 참조하세요.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>백 엔드 API 가져오기 및 게시

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API** 를 선택합니다.
1. **새 API 추가** 목록에서 **논리 앱** 을 선택합니다.

    :::image type="content" source="./media/import-logic-app-as-api/logic-app-select.png" alt-text="논리 앱 범주 선택":::

1. **찾아보기** 를 눌러 구독의 HTTP 트리거가 포함된 Logic Apps 목록을 표시합니다. 
    * HTTP 트리거가 *없는* 논리 앱은 목록에 표시되지 않습니다.

    :::image type="content" source="./media/import-logic-app-as-api/browse-logic-apps.png" alt-text="올바른 트리거를 사용하여 기존 논리 앱 찾아보기":::

1. 논리 앱을 선택합니다. 

    :::image type="content" source="./media/import-logic-app-as-api/select-logic-app-import-2.png" alt-text="논리 앱 선택":::

1. API Management는 선택한 앱과 연결된 swagger를 찾아서 페치하고 가져옵니다.
1. API URL 접미사를 추가합니다. 
    * 접미사는 이 API Management 인스턴스에서 이 특정 API를 고유하게 식별합니다.

    :::image type="content" source="./media/import-logic-app-as-api/create-from-logic-app.png" alt-text="필드 완료":::

1. API를 게시하여 개발자가 사용할 수 있도록 하려면 **전체** 보기로 전환하여 **제품** 과 연결합니다. 이 예제에서는 *"무제한"* 제품을 사용합니다. 
    * 생성 중에 또는 나중에 **설정** 탭을 통해 API를 제품에 추가할 수 있습니다.

    >[!NOTE]
    > 제품은 개발자 포털을 통해 개발자에게 제공되는 하나 이상의 API와 연결됩니다. 개발자는 먼저 제품을 구독해야 API에 액세스할 수 있습니다. 구독하면 해당 제품의 모든 API에 대한 구독 키를 얻게 됩니다. API Management 인스턴스 작성자는 관리자이며, 기본적으로 모든 제품이 구독 중 상태입니다.
    >
    > 각 API Management 인스턴스는 두 개의 기본 샘플 제품과 함께 제공됩니다.
    > - **Starter**
    > - **무제한**

1. 다른 API 설정을 입력합니다. 
    * 이러한 값은 생성 중 또는 나중에 **설정** 탭으로 이동하여 설정할 수 있습니다. 설정은 [첫 번째 API 가져오기 및 게시](import-and-publish.md#import-and-publish-a-backend-api) 자습서에 설명되어 있습니다.
1. **만들기** 를 선택합니다.

## <a name="test-the-api-in-the-azure-portal"></a>Azure Portal에서 API 테스트

dAzure Portal에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다.

:::image type="content" source="./media/import-logic-app-as-api/test-logic-app-api.png" alt-text="논리 앱 테스트":::

1. 이전 단계에서 만든 API를 선택합니다.
2. **테스트** 탭을 누릅니다.
3. 테스트할 작업을 선택합니다.

    * 페이지에 쿼리 매개 변수에 대한 필드와 헤더가 표시됩니다. 
    * 헤더 중 하나는 이 API와 연결된 제품 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. 
    * API Management 인스턴스 작성자는 이미 관리자이므로 키가 자동으로 채워집니다.

4. **보내기** 를 누릅니다.

    * 테스트가 성공하면 백 엔드에서 **200 OK** 및 데이터를 사용하여 응답합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>모든 논리 앱에는 **manual-invoke** 작업이 있습니다. 여러 논리 앱의 API를 구성하고 충돌을 방지하려면 함수 이름을 바꾸어야 합니다. 함수/API의 이름을 바꾸려면 OpenAPI 사양 편집기로 이동하여 제목 값을 변경하세요.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>
> [게시된 API 변환 및 보호](transform-api.md)
