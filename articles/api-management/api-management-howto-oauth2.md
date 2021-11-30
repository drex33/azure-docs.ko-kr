---
title: API Management에서 OAuth 2.0을 사용하여 개발자 계정에 권한 부여
titleSuffix: Azure API Management
description: API Management에서 OAuth 2.0을 사용하여 사용자에게 권한을 부여하는 방법에 대해 알아봅니다. OAuth 2.0은 사용자가 자격이 있는 리소스에만 액세스할 수 있도록 API를 보호합니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/16/2021
ms.author: danlep
ms.openlocfilehash: 9233472a8b400722ad80a6a8aee61c0883b2a709
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133286617"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Azure API Management에서 OAuth 2.0을 사용하여 개발자 계정에 권한을 부여하는 방법

대부분의 API는 [OAuth 2.0](https://oauth.net/2/) 을 지원하여 API를 보호하고 유효한 사용자만 액세스 권한이 부여되고 자격이 있는 리소스에만 액세스할 수 있도록 합니다. 이러한 API와 함께 Azure API Management 대화형 개발자 콘솔을 사용하려면 서비스를 사용하여 OAuth 2.0 사용 API에서 작동하도록 서비스 인스턴스를 구성할 수 있습니다.

개발자 포털의 테스트 콘솔에서 OAuth 2.0 사용자 권한 부여를 구성하면 개발자가 OAuth 2.0 액세스 토큰을 편리하게 획득할 수 있습니다. 테스트 콘솔에서 토큰은 단순히 API 호출을 사용하여 백 엔드에 전달됩니다. 토큰 유효성 검사는 [JWT 유효성 검사 정책](api-management-access-restriction-policies.md#ValidateJWT)를 사용하거나 백 엔드 서비스에서 별도로 구성해야 합니다.


## <a name="prerequisites"></a>사전 요구 사항

이 가이드에서는 개발자 계정에 대해 OAuth 2.0 권한 부여를 사용하도록 API Management 서비스 인스턴스를 구성하는 방법을 설명합니다. 그러나 OAuth 2.0 공급자를 구성하는 방법은 설명하지 않습니다. 

단계가 비슷하지만 각 OAuth 2.0 공급자에 대한 구성은 다르며 API Management 서비스 인스턴스에서 OAuth 2.0을 구성하는 데 사용되는 필수 정보는 동일합니다. 이 항목에서는 OAuth 2.0 공급자로서 Azure Active Directory를 사용하는 예제를 설명합니다.

아직 API Management 서비스 인스턴스를 만들지 않은 경우 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.

> [!NOTE]
> Azure Active Directory 사용하여 OAuth 2.0을 구성하는 방법에 대한 자세한 내용은 Azure Active Directory [OAuth 2.0 권한 부여를 사용하여 Azure API Management 웹 API 백 엔드 보호를](api-management-howto-protect-backend-with-aad.md)참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authorization-grant-types"></a>권한 부여 유형
 
Azure API Management 다음과 같은 OAuth 2.0 권한 부여 형식(흐름)을 지원합니다. 권한 부여 유형은 클라이언트 애플리케이션(이 컨텍스트에서 개발자 포털의 테스트 콘솔)이 백 엔드 API에 대한 액세스 토큰을 가져오는 방법을 나타냅니다. OAuth 2.0 공급자 및 시나리오에 따라 하나 이상의 권한 부여 유형을 구성할 수 있습니다. 

다음은 간략한 요약입니다. 권한 부여 유형에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크](https://datatracker.ietf.org/doc/html/rfc6749) 및 [OAuth 권한 부여 형식을 참조하세요.](https://oauth.net/2/grant-types/)


|권한 부여 유형  |Description  |시나리오  |
|---------|---------|---------|
|인증 코드     | 토큰에 대한 Exchange 인증 코드         |  웹앱과 같은 서버 쪽 앱      |
|암시적     | 추가 인증 코드 교환 단계 없이 액세스 토큰을 즉시 반환합니다.       |  모바일 앱 및 단일 페이지 앱과 같은 비밀 또는 토큰을 보호할 수 없는 클라이언트<br/><br/>일반적으로 클라이언트에서 수신되었다는 확인 없이 HTTP 리디렉션에서 액세스 토큰을 반환하는 내재된 위험 때문에 권장되지 않습니다.     |
|리소스 소유자 암호  | 일반적으로 대화형 양식을 사용하여 사용자 자격 증명(사용자 이름 및 암호)을 요청합니다. |    신뢰할 수 있는 애플리케이션과 함께 사용<br/><br/>더 안전한 다른 흐름을 사용할 수 없는 경우에만 사용해야 합니다.        |
|클라이언트 자격 증명     | 사용자가 아닌 앱 인증 및 권한 부여       |  백 엔드에서 실행되는 CLI, 디먼 또는 서비스와 같은 데이터에 액세스하기 위해 특정 사용자의 권한이 필요하지 않은 컴퓨터-컴퓨터 애플리케이션       |

### <a name="security-considerations"></a>보안 고려 사항

권한 부여 형식이 토큰을 생성하는 방법, 토큰의 [범위](https://oauth.net/2/scope/)및 토큰을 노출하는 방법을 고려합니다. 손상된 토큰은 악의적인 행위자가 토큰 범위 내의 추가 리소스에 액세스하는 데 사용할 수 있습니다.

개발자 포털의 테스트 콘솔에서 OAuth 2.0 사용자 권한 부여를 구성하는 경우:

* **토큰의 범위를** 개발자가 API를 테스트하는 데 필요한 최소값으로 제한합니다. 범위를 테스트 콘솔 또는 영향을 받는 API로 제한합니다. 토큰 범위를 구성하는 단계는 OAuth 2.0 공급자에 따라 달라집니다.

  시나리오에 따라 백 엔드 API에 액세스하기 위해 만드는 다른 클라이언트 애플리케이션에 대해 더 많거나 덜 제한적인 토큰 범위를 구성할 수 있습니다.
* **클라이언트 자격 증명 흐름을 사용하도록 설정하는 경우 주의해야** 합니다. 개발자 포털의 테스트 콘솔은 클라이언트 자격 증명 흐름을 사용할 때 자격 증명을 요청하지 않습니다. 액세스 토큰은 개발자 또는 개발자 콘솔의 익명 사용자에게 실수로 노출될 수 있습니다. 

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a>API Management에서 OAuth 2.0 권한 부여 서버 구성

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.

1. 측면 메뉴의 개발자 포털 섹션에서 **OAuth 2.0 + OpenID 커넥트** 선택합니다.

1. **OAuth 2.0 탭** 아래에서 **+추가를** 선택합니다.

   :::image type="content" source="media/api-management-howto-oauth2/oauth-01.png" alt-text="OAuth 2.0 메뉴":::

1. **이름** 필드에 이름을 입력하고 원하는 경우 **설명** 필드에 설명을 입력합니다.

    > [!NOTE]
    > 이러한 필드는 현재 API Management 서비스 내에서 OAuth 2.0 권한 부여 서버를 식별합니다. 해당 값은 OAuth 2.0 서버에서 오는 것이 아닙니다.

1. 클라이언트 **등록 페이지** URL(예: )을 입력합니다. `https://contoso.com/login` 이 페이지에서는 OAuth 2.0 공급자가 계정의 사용자 관리를 지원하는 경우 사용자가 계정을 만들고 관리할 수 있습니다. 페이지는 사용되는 OAuth 2.0 공급자에 따라 달라집니다. 

   OAuth 2.0 공급자에 계정의 사용자 관리가 구성되지 않은 경우 회사의 URL 또는 `https://placeholder.contoso.com` 등의 URL과 같은 자리 표시자 URL을 여기에 입력합니다.

    :::image type="content" source="media/api-management-howto-oauth2/oauth-02.png" alt-text="OAuth 2.0 새 서버":::

1. 양식의 다음 섹션에는 **권한 부여 유형**, **권한 부여 엔드포인트 URL** 및 **권한 부여 요청 방법** 설정이 포함되어 있습니다.

    * 원하는 유형을 선택하여 **권한 부여 유형** 을 지정합니다. **인증 코드** 가 지정됩니다. [자세한 정보](#authorization-grant-types).

    * **권한 부여 엔드포인트 URL** 을 입력합니다. Azure Active Directory의 경우이 URL은 다음 URL과 유사합니다. 여기서 `<tenant_id>`는 Azure AD 테넌트의 ID로 바뀝니다.

       `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    * **권한 부여 요청 방법** 은 권한 부여 요청이 OAuth 2.0 서버로 전송되는 방법을 지정합니다. 기본적으로는 **GET** 이 선택됩니다.

    :::image type="content" source="media/api-management-howto-oauth2/oauth-03.png" alt-text="권한 부여 설정 지정":::

1. **토큰 엔드포인트 URL,** **클라이언트 인증 방법,** 액세스 토큰 전송 **방법** 및 **기본 범위를** 지정합니다.

    * Azure Active Directory OAuth 2.0 서버의 경우 **토큰 엔드포인트 URL의** 형식은 다음과 입니다. 여기서 `<TenantID>` 은 `yourapp.onmicrosoft.com` 형식입니다.

        `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    * 클라이언트 인증 **방법에** 대한 기본 설정은 **본문에** 이고  **액세스 토큰 전송 방법은** 권한 부여 **헤더** 입니다. 양식의 이 섹션에서 **기본 범위** 와 함께 이러한 값을 구성합니다.

6. **클라이언트 자격 증명** 섹션에는 **클라이언트 ID** 및 **클라이언트 암호** 가 포함되어 있습니다. OAuth 2.0 서버 만들기 및 구성 프로세스 중에 이러한 값을 가져옵니다. 
  
    클라이언트 **ID** 및 **클라이언트 암호가** 지정되면 권한 부여 **코드에** 대한 **redirect_uri** 생성됩니다. 이 URI를 사용하여 OAuth 2.0 서버 구성에서 회신 URL을 구성합니다.

    개발자 포털에서 URI 접미사는 다음과 같은 형식입니다.

    - `/signin-oauth/code/callback/{authServerName}` 인증 코드 권한 부여 흐름
    - `/signin-oauth/implicit/callback` 임시적 권한 부여 흐름

    :::image type="content" source="media/api-management-howto-oauth2/oauth-04.png" alt-text="OAuth 2.0 서비스에 대한 클라이언트 자격 증명 추가":::

1. **권한 부여 유형** 을 **리소스 소유자 암호** 로 설정한 경우 **리소스 소유자 암호 자격 증명** 섹션에서 해당 자격 증명을 지정합니다. 그렇지 않은 경우에는 자격 증명을 비워 두면 됩니다.

1. **만들기를** 선택하여 API Management OAuth 2.0 권한 부여 서버 구성을 저장합니다. 

서버 구성이 저장되면 다음 섹션에 표시된 대로 이 구성을 사용하도록 API를 구성할 수 있습니다.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a>OAuth 2.0 사용자 권한 부여를 사용하도록 API 구성

1. 왼쪽의 API Management 메뉴에서 **API를** 선택합니다. 

1. 원하는 API의 이름을 선택하고 **설정** 탭을 선택합니다. **보안** 섹션으로 스크롤한 다음 **OAuth 2.0을** 선택합니다.

1. 드롭다운 목록에서 원하는 **권한 부여 서버를** 선택하고 **저장을** 선택합니다.

    :::image type="content" source="./media/api-management-howto-oauth2/oauth-07.png" alt-text="OAuth 2.0 권한 부여 서버 구성":::


## <a name="developer-portal---test-the-oauth-20-user-authorization"></a>개발자 포털 - OAuth 2.0 사용자 권한 부여 테스트

[!INCLUDE [api-management-test-oauth-authorization](../../includes/api-management-test-oauth-authorization.md)]

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a>레거시 개발자 포털 - OAuth 2.0 사용자 권한 부여 테스트

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

OAuth 2.0 권한 부여 서버를 구성하고 해당 서버를 사용하도록 API를 구성한 후에는 개발자 포털로 이동하여 API를 호출하는 방법으로 권한 부여를 테스트할 수 있습니다. Azure API Management 인스턴스 **개요** 페이지의 맨 위 메뉴에서 **개발자 포털(레거시)** 을 클릭합니다.

상단 메뉴에서 **API** 를 클릭하고 **Echo API** 를 선택합니다.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> API 한 개만 구성했거나 계정에 표시한 경우에는 API를 클릭하면 해당 API에 대한 작업으로 직접 연결됩니다.

**GET 리소스** 작업을 선택하고 **콘솔 시작** 을 클릭한 후에 드롭다운에서 **인증 코드** 를 선택합니다.

![콘솔 시작][api-management-open-console]

**인증 코드** 를 선택하면 OAuth 2.0 공급자의 로그인 양식이 포함된 팝업 창이 표시됩니다. 이 예제에서는 Azure Active Directory에서 로그인 양식을 제공합니다.

> [!NOTE]
> 팝업을 사용하지 않도록 설정한 경우 브라우저에서 사용하도록 설정하라는 메시지가 표시됩니다. 팝업을 사용하도록 설정한 후 **인증 코드** 를 다시 선택하면 로그인 양식이 표시됩니다.

![로그인][api-management-oauth2-signin]

로그인하고 나면 **요청 헤더** 에 요청 권한을 부여하는 `Authorization : Bearer` 헤더가 채워집니다.

![요청 헤더 토큰][api-management-request-header-token]

이제 나머지 매개 변수에 대해 원하는 값을 구성하고 요청을 제출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

OAuth 2.0 및 API Management 사용에 대한 자세한 내용은 다음 비디오 및 제공되는 [문서](api-management-howto-protect-backend-with-aad.md)를 참조하세요.

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
