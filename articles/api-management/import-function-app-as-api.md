---
title: API Management에서 Azure 함수 앱을 API로 가져오기
titleSuffix: Azure API Management
description: 이 문서에서는 Azure API Management에서 Azure 함수 앱을 API로 가져오는 방법을 보여줍니다.
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
ms.openlocfilehash: f4a90656164931a6d625fb486737eb970c08e89a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667809"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure API Management에서 Azure 함수 앱을 API로 가져오기

Azure API Management는 Azure 함수 앱을 새 API로 가져오거나 기존 API에 추가할 수 있도록 지원합니다. 이 프로세스는 Azure Function App에서 호스트 키를 자동으로 생성한 다음, Azure API Management에서 명명된 값에 할당합니다.

이 문서에서는 Azure API Management에서 Azure Function 앱을 API로 가져와서 테스트하는 방법을 단계별로 안내합니다. 

다음 방법을 알게 됩니다.

> [!div class="checklist"]
> * Azure 함수 앱을 API로 가져오기
> * Azure 함수 앱을 API에 추가
> * 새 Azure 함수 앱 호스트 키 및 Azure API Management 명명된 값 보기
> * Azure Portal에서 API 테스트

## <a name="prerequisites"></a>사전 요구 사항

* [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md) 빠른 시작을 완료합니다.
* 구독에 Azure Functions 앱이 있는지 확인합니다. 자세한 내용은 [Azure 함수 앱 만들기](../azure-functions/functions-get-started.md)를 참조하세요. Functions에 HTTP 트리거 및 *익명* 또는 *함수* 로 설정된 권한 부여 수준이 있어야 합니다.

> [!NOTE]
> Visual Studio Code용 API Management 확장을 사용하여 API를 가져오고 관리할 수 있습니다. [API Management 확장 자습서](visual-studio-code-tutorial.md)를 따라 설치하고 시작합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Azure 함수 앱을 새 API로 가져오기

아래 단계에 따라 Azure 함수 앱에서 새 API를 만듭니다.

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API** 를 선택합니다.

2. **새 API 추가** 목록에서 **함수 앱** 을 선택합니다.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="함수 앱 타일을 보여주는 스크린샷.":::

3. **찾아보기** 를 클릭하여 가져올 Functions를 선택합니다.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="찾아보기 단추를 강조 표시하는 스크린샷.":::

4. 사용 가능한 함수 앱 목록에서 선택하려면 **함수 앱** 섹션을 클릭합니다.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="함수 앱 섹션을 강조 표시하는 스크린샷.":::

5. Functions를 가져오려는 함수 앱을 찾아서 클릭하고, **선택** 을 누릅니다.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="함수를 가져오려는 함수 앱과 선택 단추를 강조 표시하는 스크린샷.":::

6. 가져오려는 Functions를 선택하고, **선택** 을 클릭합니다.
    * *익명* 또는 *함수* 권한 부여 수준에서 HTTP 트리거를 기반으로 함수를 가져올 수만 있습니다.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="가져올 함수와 선택 단추를 강조 표시하는 스크린샷.":::

7. **전체** 보기로 전환하고 새 API에 **제품** 을 할당합니다. 
8. 필요한 경우 생성 중에 다른 필드를 지정하거나 나중에 **설정** 탭을 통해 필드를 구성합니다. 
    * 설정은 [첫 번째 API 가져오기 및 게시](import-and-publish.md#import-and-publish-a-backend-api) 자습서에 설명되어 있습니다.

    >[!NOTE]
    > 제품은 개발자 포털을 통해 개발자에게 제공되는 하나 이상의 API와 연결됩니다. 개발자는 먼저 제품을 구독해야 API에 액세스할 수 있습니다. 구독하면 해당 제품의 모든 API에 대한 구독 키를 얻게 됩니다. API Management 인스턴스 작성자는 관리자이며, 기본적으로 모든 제품이 구독 중 상태입니다.
    >
    > 각 API Management 인스턴스는 두 개의 기본 샘플 제품과 함께 제공됩니다.
    > - **Starter**
    > - **무제한**

9. **만들기** 를 클릭합니다.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Azure 함수 앱을 기존 API에 추가

아래 단계에 따라 Azure 함수 앱을 기존 API에 추가합니다.

1. **Azure API Management**  서비스 인스턴스의 왼쪽 메뉴에서 **API** 를 선택합니다.

2. Azure 함수 앱을 가져올 API를 선택합니다. **...** 를 클릭하고, 상황에 맞는 메뉴에서 **가져오기** 를 선택합니다.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="가져오기 메뉴 옵션을 강조 표시하는 스크린샷.":::

3. **함수 앱** 타일을 클릭합니다.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="함수 앱 타일을 강조 표시하는 스크린샷.":::

4. 팝업 창에서 **찾아보기** 를 클릭합니다.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="찾아보기 단추를 보여주는 스크린샷.":::

5. 사용 가능한 함수 앱 목록에서 선택하려면 **함수 앱** 섹션을 클릭합니다.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="함수 앱 목록을 강조 표시하는 스크린샷.":::

6. Functions를 가져오려는 함수 앱을 찾아서 클릭하고, **선택** 을 누릅니다.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="함수를 가져오려는 함수 앱을 강조 표시하는 스크린샷.":::

7. 가져오려는 Functions를 선택하고, **선택** 을 클릭합니다.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="가져오려는 함수가 강조 표시된 스크린샷.":::

8. **가져오기** 를 클릭합니다.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="함수 앱에서 추가":::

## <a name="authorization"></a><a name="authorization"></a> 권한 부여

Azure 함수 앱 가져오기에서 자동으로 생성되는 항목은 다음과 같습니다.

* 함수 앱 내에 apim-{*Azure API Management 서비스 인스턴스 이름*} 이름이 있는 호스트 키,
* Azure API Management 인스턴스 내에 {*Azure 함수 앱 인스턴스 이름*}-key 이름(만든 호스트 키 포함)이 있는 명명된 값.

2019년 4월 4일 이후 만든 API의 경우 호스트 키는 HTTP 요청 시 API Management에서 헤더의 함수 앱으로 전달됩니다. 기존 API는 호스트 키를 [쿼리 매개 변수](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization)로 전달합니다. 이 동작은 함수 앱과 연결된 *백 엔드* 엔터티의 `PATCH Backend`[REST API 호출](/rest/api/apimanagement/2020-12-01/backend/update#backendcredentialscontract)을 통해 변경할 수 있습니다.

> [!WARNING]
> Azure Function 앱 호스트 키 값 또는 Azure API Management 명명된 값을 제거하거나 변경하면 서비스 간 통신이 중단됩니다. 값은 자동으로 동기화되지 않습니다.
>
> 호스트 키를 회전해야 하는 경우 Azure API Management에서 명명된 값도 수정되었는지 확인합니다.

### <a name="access-azure-function-app-host-key"></a>Azure 함수 앱 호스트 키 액세스

1. Azure 함수 앱 인스턴스로 이동합니다.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="함수 앱 인스턴스 선택이 강조 표시된 스크린샷.":::

2. 측면 탐색 메뉴의 **함수** 섹션에서 **앱 키** 를 선택합니다.

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="함수 앱 설정 옵션을 강조 표시하는 스크린샷.":::

3. **호스트 키** 섹션에서 키를 찾습니다.

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="호스트 키 섹션을 강조 표시하는 스크린샷.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Azure API Management에서 명명된 값 액세스

Azure API Management 인스턴스로 이동하고, 왼쪽 메뉴에서 **명명된 값** 을 선택합니다. Azure 함수 앱 키는 여기에 저장됩니다.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="함수 앱에서 추가":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Azure Portal에서 새 API 테스트

Azure Portal에서 작업을 직접 호출할 수 있습니다. Azure Portal을 사용하는 것은 API의 작업을 보고 테스트하는 편리한 방법입니다.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="테스트 절차가 강조 표시된 스크린샷.":::

1. 이전 섹션에서 만든 API를 선택합니다.

2. **테스트** 탭을 선택합니다.

3. 테스트할 작업을 선택합니다.

    * 페이지에 쿼리 매개 변수에 대한 필드와 헤더가 표시됩니다. 
    * 헤더 중 하나는 이 API와 연결된 제품 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. 
    * API Management 인스턴스 작성자는 이미 관리자이므로 키가 자동으로 채워집니다. 

4. **보내기** 를 선택합니다.

    * 테스트가 성공하면 백 엔드에서 **200 OK** 및 일부 데이터를 사용하여 응답합니다.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
