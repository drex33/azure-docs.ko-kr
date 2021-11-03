---
title: API Management에서 OAuth 2.0을 사용하여 개발자 계정에 권한 부여
titleSuffix: Azure API Management
description: API Management에서 OAuth 2.0을 사용하여 사용자에게 권한을 부여하는 방법에 대해 알아봅니다. OAuth 2.0은 사용자가 자격이 있는 리소스에만 액세스할 수 있도록 API를 보호합니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: danlep
ms.openlocfilehash: 1d1e75f16c2431a5fad475233510e2c220a06d57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065725"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Azure API Management에서 OAuth 2.0을 사용하여 개발자 계정에 권한을 부여하는 방법

대부분의 API는 [OAuth 2.0](https://oauth.net/2/) 을 지원하여 API를 보호하고 유효한 사용자만 액세스 권한이 부여되고 자격이 있는 리소스에만 액세스할 수 있도록 합니다. 이러한 API와 함께 Azure API Management의 대화형 개발자 콘솔을 사용하기 위해 서비스에서 OAuth 2.0 사용 API로 작동하도록 서비스 인스턴스를 구성할 수 있습니다.

## <a name="prerequisites"></a><a name="prerequisites"> </a>필수 구성 요소

이 가이드에서는 개발자 계정에 대해 OAuth 2.0 권한 부여를 사용하도록 API Management 서비스 인스턴스를 구성하는 방법을 설명합니다. 그러나 OAuth 2.0 공급자를 구성하는 방법은 설명하지 않습니다. 각 OAuth 2.0 공급자의 구성은 서로 다르지만 구성 단계는 비슷하며, API Management 서비스 인스턴스에서 OAuth 2.0을 구성하는 데 사용되는 필수 정보도 동일합니다. 이 항목에서는 OAuth 2.0 공급자로서 Azure Active Directory를 사용하는 예제를 설명합니다.

> [!NOTE]
> Azure Active Directory를 사용하여 OAuth 2.0을 구성하는 방법에 대한 자세한 내용은 [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] 샘플을 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>API Management에서 OAuth 2.0 권한 부여 서버 구성

> [!NOTE]
> 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.

1. 왼쪽 메뉴에서 OAuth 2.0 탭을 클릭하고 **+추가** 를 클릭합니다.

    ![OAuth 2.0 메뉴](./media/api-management-howto-oauth2/oauth-01.png)

2. **이름** 필드에 이름을 입력하고 원하는 경우 **설명** 필드에 설명을 입력합니다.

    > [!NOTE]
    > 이러한 필드는 현재 API Management 서비스 인스턴스 내에서 OAuth 2.0 권한 부여 서버를 식별하는 데 사용되며, 해당 값은 OAuth 2.0 서버에서 제공되지 않습니다.

3. **클라이언트 등록 페이지 URL** 을 입력합니다. 사용자는 이 페이지에서 계정을 만들고 관리할 수 있습니다. 사용되는 OAuth 2.0 공급자에 따라 페이지는 달라집니다. **클라이언트 등록 페이지 URL** 은 사용자가 계정의 사용자 관리를 지원하는 OAuth 2.0 공급자에 대한 계정을 만들고 구성하는 데 사용할 수 있는 페이지를 가리킵니다(예: `https://contoso.com/login`). 일부 조직에서는 OAuth 2.0 공급자가 지원하는 경우에도 이 기능을 구성 또는 사용하지 않습니다. OAuth 2.0 공급자에 계정의 사용자 관리가 구성되지 않은 경우 회사의 URL 또는 `https://placeholder.contoso.com` 등의 URL과 같은 자리 표시자 URL을 여기에 입력합니다.

    ![OAuth 2.0 새 서버](./media/api-management-howto-oauth2/oauth-02.png)

4. 양식의 다음 섹션에는 **권한 부여 유형**, **권한 부여 엔드포인트 URL** 및 **권한 부여 요청 방법** 설정이 포함되어 있습니다.

    원하는 유형을 선택하여 **권한 부여 유형** 을 지정합니다. **인증 코드** 가 지정됩니다.

    **권한 부여 엔드포인트 URL** 을 입력합니다. Azure Active Directory의 경우이 URL은 다음 URL과 유사합니다. 여기서 `<tenant_id>`는 Azure AD 테넌트의 ID로 바뀝니다.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **권한 부여 요청 방법** 은 권한 부여 요청이 OAuth 2.0 서버로 전송되는 방법을 지정합니다. 기본적으로는 **GET** 이 선택됩니다.

5. 그런 후에 **토큰 엔드포인트 URL**, **클라이언트 인증 방법**, **액세스 토큰 전송 방법** 및 **기본 범위** 를 지정해야 합니다.

    ![OAuth2 추가 서비스 화면을 보여주는 스크린샷입니다.](./media/api-management-howto-oauth2/oauth-03.png)

    Azure Active Directory OAuth 2.0 서버의 경우 **토큰 엔드포인트 URL** 의 형식은 다음과 같습니다. `<TenantID>`의 형식은 `yourapp.onmicrosoft.com`입니다.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    **클라이언트 인증 방법** 의 기본 설정은 **기본** 이고 **액세스 토큰 전송 방법** 의 기본 설정은 **권한 부여 헤더** 입니다. 양식의 이 섹션에서 **기본 범위** 와 함께 이러한 값을 구성합니다.

6. **클라이언트 자격 증명** 섹션에는 **클라이언트 ID** 및 **클라이언트 암호** 가 포함되어 있습니다. OAuth 2.0 서버 만들기 및 구성 프로세스 중에 이러한 값을 가져옵니다. **클라이언트 ID** 및 **클라이언트 암호** 를 지정하고 나면 **인증 코드** 의 **redirect_uri** 가 생성됩니다. 이 URI를 사용하여 OAuth 2.0 서버 구성에서 회신 URL을 구성합니다.

    새 개발자 포털에서 URI 접미사 형식은 다음과 같습니다.

    - `/signin-oauth/code/callback/{authServerName}` 인증 코드 권한 부여 흐름
    - `/signin-oauth/implicit/callback` 임시적 권한 부여 흐름

    ![새 OAuth2 서비스에 대한 클라이언트 자격 증명을 추가할 위치를 보여주는 스크린샷입니다.](./media/api-management-howto-oauth2/oauth-04.png)

    **권한 부여 유형** 을 **리소스 소유자 암호** 로 설정한 경우 **리소스 소유자 암호 자격 증명** 섹션에서 해당 자격 증명을 지정합니다. 그렇지 않은 경우에는 자격 증명을 비워 두면 됩니다.

    양식에 필요한 정보를 모두 입력한 후 **만들기** 를 클릭하여 API Management OAuth 2.0 권한 부여 서버 구성을 저장합니다. 서버 구성을 저장한 후에는 다음 섹션에서 설명하는 것처럼 이 구성을 사용하도록 API를 구성할 수 있습니다.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>OAuth 2.0 사용자 권한 부여를 사용하도록 API 구성

1. 왼쪽의 **API Management** 메뉴에서 **API** 를 클릭합니다.

    ![OAuth 2.0 API](./media/api-management-howto-oauth2/oauth-05.png)

2. 원하는 API의 이름을 클릭하고 **설정** 을 클릭합니다. **보안** 섹션으로 스크롤한 후 **OAuth 2.0** 에 대한 확인란을 선택합니다.

    ![OAuth 2.0 설정](./media/api-management-howto-oauth2/oauth-06.png)

3. 드롭다운 목록에서 원하는 **권한 부여 서버** 를 선택하고 **저장** 을 클릭합니다.

    ![선택한 인증 서버와 저장 버튼을 강조 표시하는 스크린샷입니다.](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>레거시 개발자 포털 - OAuth 2.0 사용자 권한 부여 테스트

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
