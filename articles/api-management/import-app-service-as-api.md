---
title: Azure API Management에 Azure 웹앱 가져오기 | Microsoft Docs
description: 이 문서에서는 Azure API Management를 사용하여 Azure App Service에서 호스트되는 웹 API를 가져오는 방법을 보여 줍니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/27/2021
ms.author: danlep
ms.openlocfilehash: 8d98a6c115b7f3d06b80a938c090bfa1b9c9794e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585093"
---
# <a name="import-an-azure-web-app-as-an-api"></a>Azure 웹앱을 API로 가져오기

이 문서에서는 Azure 웹앱을 Azure API Management로 가져오고 Azure Portal을 사용하여 가져온 API를 테스트하는 방법을 보여 줍니다.

> [!NOTE]
> Visual Studio Code용 API Management 확장을 사용하여 API를 가져오고 관리할 수 있습니다. [API Management 확장 자습서](visual-studio-code-tutorial.md)를 따라 설치하고 시작합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * App Service에서 호스트되는 웹앱 가져오기
> * Azure Portal에서 API 테스트

## <a name="expose-web-app-with-api-management"></a>API Management를 사용하여 웹앱 노출

[Azure App Service](../app-service/overview.md)는 웹 애플리케이션, REST API 및 모바일 백 엔드를 호스트하는 HTTP 기반 서비스입니다. API 개발자는 선호하는 기술 스택과 파이프라인을 사용하여 API를 개발하고, 안전하고 확장 가능한 환경에서 Web Apps API 백 엔드를 게시할 수 있습니다. 그런 다음 API Management를 사용하여 Web Apps를 노출하고, 수명 주기 동안 API를 관리 및 보호하고, 이를 소비자에게 게시합니다.

API Management는 다음과 같은 여러 가지 이유로 웹앱 호스트 API를 노출하는 데 권장되는 환경입니다.

* 백 엔드 웹앱 관리 및 모니터링에서 API 소비자에게 노출되는 프런트 엔드 관리 및 보안을 분리합니다.
* 다른 API와 동일한 환경에서 Web Apps로 호스트되는 웹 API 관리
* [정책](api-management-policies.md)을 적용하여 호출 속도 제한과 같은 API 동작 변경
* API를 알아보고 액세스를 요청하고 사용하도록 API 소비자를 API Management의 사용자 지정 가능한 [개발자 포털](api-management-howto-developer-portal.md)로 안내합니다.

자세한 내용은 [API Management 정보](api-management-key-concepts.md)를 참조하세요.

## <a name="openapi-specification-versus-wildcard-operations"></a>OpenAPI 사양과 와일드카드 작업 비교

API Management는 OpenAPI 사양(Swagger 정의)을 포함하는 App Service에서 호스트되는 Web Apps 가져오기를 지원합니다. 하지만 OpenAPI 사양은 필요하지 않습니다.

* 웹앱에 API 정의에 구성된 OpenAPI 사양이 있는 경우 API Management는 필수 경로, 매개 변수 및 응답 유형을 포함하여 정의로 직접 매핑되는 API 작업을 만듭니다. 

  높은 충실도로 API를 API Management로 가져오므로 OpenAPI 사양이 권장됩니다. 각 작업에 대해 개별적으로 구성을 유효성 검사, 관리, 보호 및 업데이트할 수 있습니다.

* OpenAPI 사양이 제공되지 않는 경우 API Management는 공통 HTTP 동사(GET, PUT 등)에 대한 [와일드카드 작업](add-api-manually.md#add-and-test-a-wildcard-operation)을 생성합니다. API 요청을 백 엔드 API로 전달하기 위해 와일드카드 작업에 필요한 경로 또는 매개 변수를 추가합니다.

  와일드카드 작업을 사용하면 동일한 API Management 기능을 활용할 수 있지만, 작업은 기본적으로 동일한 수준의 세부 정보로 정의되지 않습니다. [편집](edit-api.md) 또는 가져온 API로 작업을 [추가](add-api-manually.md)할 수 있습니다.
 
### <a name="example"></a>예제
백 엔드 웹앱은 다음과 같은 두 가지 GET 작업을 지원할 수 있습니다. 
*  `https://myappservice.azurewebsites.net/customer/{id}`
*  `https://myappservice.azurewebsites.net/customers`

`https://contosoapi.azureapi.net/store`와 같은 경로에서 API Management 서비스에 웹앱을 가져올 수 있습니다. 다음 표에서는 OpenAPI 사양 여부와 무관하게 API Management로 가져오는 작업을 보여 줍니다. 

| 형식 |가져온 작업  |샘플 요청 |
|---------|---------|---------|
|OpenAPI 사양    | `GET  /customer/{id}`<br/><br/> `GET  /customers`         |  `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`       |
|와일드카드     | `GET  /*`         | `GET https://contosoapi.azureapi.net/store/customer/1`<br/><br/>`GET https://contosoapi.azureapi.net/store/customers`  |

와일드카드 작업은 백 엔드에 대해 동일한 작업을 OpenAPI 사양의 작업으로 허용합니다. 하지만 OpenAPI 지정 작업은 API Management에서 별도로 관리할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ 구독에 App Service가 있는지 확인합니다. 자세한 내용은 [App Service 설명서](../app-service/index.yml)를 참조하세요.

  예제 웹 API를 만들고 Azure 웹앱으로 게시하는 단계는 다음을 참조하세요.

    * [자습서: ASP.NET Core를 사용하여 웹 API 만들기](/aspnet/core/tutorials/first-web-api)
    * [Visual Studio Code를 사용하여 Azure에 ASP.NET Core 앱 게시](/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>백 엔드 API 가져오기 및 게시

> [!TIP]
> 다음 단계는 Azure Portal에서 Azure API Management를 사용하여 가져오기를 시작합니다. 앱의 **API** 메뉴에서 **API Management** 를 선택하여 웹앱에서 직접 API Management에 연결할 수 있습니다.  

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API** 를 선택합니다.
1. 목록에서 **App Service** 를 선택합니다.

    :::image type="content" source="media/import-app-service-as-api/app-service.png" alt-text="App Service에서 만들기":::
1. **찾아보기** 를 선택하여 구독에서 App Service 목록을 확인합니다.
1. App Service를 선택합니다. OpenAPI 정의가 선택한 웹앱과 연결된 경우 API Management는 이를 검색하여 가져옵니다. 

    OpenAPI 정의를 찾을 수 없는 경우 API Management는 일반 HTTP 동사에 대한 와일드카드 작업을 생성하여 API를 노출합니다. 
1. API URL 접미사를 추가합니다. 접미사는 이 API Management 인스턴스에서 이 특정 API를 식별하는 이름입니다. 이 APIM 인스턴스 내에서 고유해야 합니다.
1. API를 제품에 연결하여 API를 게시합니다. 이 경우 "*Unlimited*" 제품이 사용됩니다. API를 게시하고 개발자가 사용할 수 있도록 제품에 추가합니다. API를 만드는 동안 이 작업을 수행할 수도 있고 나중에 설정할 수도 있습니다.

    > [!NOTE]
    > 제품은 하나 이상의 API와 연결됩니다. 여러 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독한 상태가 됩니다.
    >
    > 기본적으로 각 API Management 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.
    > * **Starter**
    > * **무제한**   
1. 다른 API 설정을 입력합니다. 생성 중에 값을 설정하거나 나중에 **설정** 탭으로 이동하여 값을 설정할 수 있습니다. 설정은 [첫 번째 API 가져오기 및 게시](import-and-publish.md#import-and-publish-a-backend-api) 자습서에 설명되어 있습니다.
1. **만들기** 를 선택합니다.
    :::image type="content" source="media/import-app-service-as-api/import-app-service.png" alt-text="App Service에서 API 만들기":::

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure Portal에서 새 API 테스트

dAzure Portal에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. [개발자 포털](api-management-howto-developer-portal.md)에서 또는 자체 REST 클라이언트 도구를 사용하여 API를 테스트할 수도 있습니다.

1. 이전 단계에서 만든 API를 선택합니다.
1. **테스트** 탭을 선택합니다.
1. 작업을 선택합니다.

    페이지에 쿼리 매개 변수에 대한 필드와 헤더 필드가 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. API Management 인스턴스를 만든 경우 사용자는 이미 관리자이므로 키가 자동으로 채워집니다. 
1. **보내기** 를 누릅니다.

    테스트에 성공하면 백 엔드가 **200 OK** 와 일부 데이터로 응답합니다.

### <a name="test-wildcard-operation-in-the-portal"></a>포털에서 와일드카드 작업 테스트

와일드카드 작업이 생성되면 작업은 백 엔드 API에 직접 매핑되지 않을 수 있습니다. 예를 들어 API Management에서 가져온 와일드카드 GET 작업은 기본적으로 `/` 경로를 사용합니다. 하지만 백 엔드 API는 다음 경로에서 GET 작업을 지원할 수 있습니다.

`/api/TodoItems`

다음과 같이 `/api/TodoItems` 경로를 테스트할 수 있습니다. 

1. 만든 API를 선택하고 작업을 선택합니다.
1. **테스트** 탭을 선택합니다.
1. **템플릿 매개 변수** 에서 와일드카드(*) 이름 옆의 값을 업데이트합니다. 예를 들어 다음과 같이 입력합니다. `api/TodoItems` 이 값은 와일드 카드 작업에 대해 `/` 경로에 추가됩니다.

    :::image type="content" source="media/import-app-service-as-api/test-wildcard-operation.png" alt-text="와일드카드 작업 테스트":::
1. **보내기** 를 선택합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
