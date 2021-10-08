---
title: OAuth 2.0 및 Azure Active Directory 사용하여 API Management API 백 엔드 보호
titleSuffix: Azure API Management
description: OAuth 2.0 사용자 권한 부여 및 Azure Active Directory 사용하여 Azure API Management 및 개발자 포털에서 웹 API 백 엔드에 대한 사용자 액세스를 보호하는 방법을 알아봅니다.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 09/17/2021
ms.author: danlep
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7739411364b187e437e9dda4c72dff85de26d741
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669571"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-using-oauth-20-authorization-with-azure-active-directory"></a>Azure Active Directory OAuth 2.0 권한 부여를 사용하여 Azure API Management Web API 백 엔드 보호 

이 문서에서는 Azure [AD(Azure Active Directory)와 함께 OAuth 2.0 프로토콜을](../active-directory/develop/active-directory-v2-protocols.md)사용하여 API를 보호하도록 Azure API Management 인스턴스를 구성하는 방법에 대해 알아봅니다. [](api-management-key-concepts.md) 

> [!NOTE]
> 이 기능은 **개발자,** **기본**, **표준** 및 **API Management Premium** 계층에서 사용할 수 있습니다.  
> 
> 개발자 포털에서 API를 호출하는 경우를 제외하고 **소비** 계층에서 아래의 모든 단계를 따를 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 수행하려면 다음이 있어야 합니다.

- API Management 인스턴스
- API Management 인스턴스를 사용하는 게시된 API
- Azure AD 테넌트

## <a name="overview"></a>개요

:::image type="content" source="media/api-management-howto-protect-backend-with-aad/overview-graphic-2021.png" alt-text="다음 흐름을 시각적으로 개념화하는 개요 그래픽입니다.":::

1. API를 나타내기 위해 Azure AD에 애플리케이션(backend-app) 등록합니다.

1. API를 호출해야 하는 클라이언트 애플리케이션을 나타내기 위해 Azure AD에 다른 애플리케이션(client-app)을 등록합니다.

1. Azure AD에서 client-app에 backend-app을 호출할 수 있도록 권한을 부여합니다.

1. OAuth 2.0 사용자 권한 부여를 사용하여 API를 호출하도록 개발자 포털에서 개발자 콘솔을 구성합니다.

1. **validate-jwt** 정책을 추가하여 들어오는 요청마다 OAuth 토큰의 유효성을 검사합니다.

## <a name="1-register-an-application-in-azure-ad-to-represent-the-api"></a>1. API를 나타내기 위해 Azure AD에 애플리케이션 등록

Azure Portal 사용하여 Azure AD에서 API를 나타내는 애플리케이션을 등록하여 Azure AD로 API를 보호합니다. 

앱 등록에 대한 자세한 내용은 [빠른 시작: 웹 API를 공개하도록 애플리케이션 구성](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 를 검색하고 **앱 등록** 선택합니다.

1. **새 등록** 을 선택합니다. 

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.

   - **이름** 섹션에서 앱 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: *backend-app*)을 입력합니다. 
   - **지원되는 계정 유형** 섹션에서 시나리오에 적합한 옵션을 선택합니다. 

1. 리디렉션 [**URI**](../active-directory/develop/reply-url.md) 섹션을 비워 둡니다.

1. **등록** 을 선택하여 애플리케이션을 만듭니다. 

1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다.

1. 측면 메뉴의 **관리** 섹션에서 **API 노출을** 선택하고 애플리케이션 **ID URI를** 기본값으로 설정합니다. 나중에 사용하기 위해 이 값을 기록해 둡니다.

1. 범위 **추가 단추를** 선택하여 **범위 추가** 페이지를 표시합니다.
    1. 새 **범위 이름,** **관리자 동의 표시 이름** 및 관리자 동의 설명을 **입력합니다.**
    1. **사용** 범위 상태가 선택되어 있는지 확인합니다.

1. **범위 추가** 단추를 선택하여 범위를 만듭니다. 

1. 8단계와 9단계를 반복하여 API에서 지원하는 모든 범위를 추가합니다.

1. 범위가 만들어지면 후속 단계에서 사용할 수 있도록 기록해 둡니다. 

## <a name="2-register-another-application-in-azure-ad-to-represent-a-client-application"></a>2. 클라이언트 애플리케이션을 나타내기 위해 Azure AD에 다른 애플리케이션 등록

API를 호출하는 모든 클라이언트 애플리케이션을 Azure AD에서 애플리케이션으로 등록합니다. 이 예제에서 클라이언트 애플리케이션은 API Management 개발자 포털의 **개발자 콘솔** 입니다. 

개발자 콘솔을 나타내려면 Azure AD에 다른 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 를 검색하고 **앱 등록** 선택합니다.

1. **새 등록** 을 선택합니다.

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.

   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: *client-app*)을 입력합니다. 
   - **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트)** 를 선택합니다. 

1. **URI 리디렉션** 섹션에서 `Web`을 선택하고 URL 필드를 아직 비워둡니다.

1. **등록** 을 선택하여 애플리케이션을 만듭니다. 

1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다.

1. 후속 단계에서 사용하기 위해 이 애플리케이션에 대한 클라이언트 암호를 만듭니다.

   1. 측면 메뉴의 **관리** 섹션에서 **인증서 & 비밀을** 선택합니다.
   1. **클라이언트 암호** 아래에서 **새 클라이언트 암호** 를 선택합니다.
   1. **클라이언트 암호 추가 아래에서** **설명을** 입력하고 키가 만료되어야 하는 시기를 선택합니다.
   1. **추가** 를 선택합니다.

비밀이 만들어지면 이후 단계에서 사용할 키 값을 적어 둡니다. 

## <a name="3-grant-permissions-in-azure-ad"></a>3. Azure AD에서 권한 부여

API 및 개발자 콘솔을 나타내는 두 개의 애플리케이션을 등록했으니 client-app이 backend-app을 호출할 수 있도록 권한을 부여합니다.  

1. [Azure Portal](https://portal.azure.com)에서 를 검색하고 **앱 등록** 선택합니다.

1. 클라이언트 앱을 선택합니다. 앱의 페이지 목록에서 **API 권한** 을 선택합니다.

1. **권한 추가** 를 선택합니다.

1. **API 선택** 에서 **내 API** 를 선택한 다음, 백 엔드 앱을 찾아 선택합니다.

1. **위임된 권한을** 선택한 다음, 백 엔드 앱에 대한 적절한 권한을 선택합니다.

1. **권한 추가** 를 선택합니다.

필요에 따라 다음을 수행합니다.
1. 클라이언트 앱의 **API 권한 페이지로** 이동합니다.

1. 에 **대한 관리자 \<your-tenant-name> 동의 부여를** 선택하여 이 디렉터리에 있는 모든 사용자를 대신하여 동의를 부여합니다. 

## <a name="4-enable-oauth-20-user-authorization-in-the-developer-console"></a>4. 개발자 콘솔에서 OAuth 2.0 사용자 권한 부여 사용

이제 Azure AD에서 애플리케이션을 만들었으며 client-app에서 backend-app을 호출하도록 허용하는 적절한 권한을 부여했습니다. 

이 예제에서는 개발자 콘솔(클라이언트 앱)에서 OAuth 2.0 사용자 권한 부여를 사용하도록 설정합니다.

1. Azure Portal **권한 부여 엔드포인트 URL** 및 **토큰 엔드포인트 URL을** 찾아 나중에 사용할 수 있도록 저장합니다. 
    1. **앱 등록** 페이지를 엽니다. 
    1. **엔드포인트** 를 선택합니다.
    1. **OAuth 2.0 권한 부여 엔드포인트** 및 **OAuth 2.0 토큰 엔드포인트 를** 복사합니다. 

1. API Management 인스턴스로 이동합니다.

1. 측면 메뉴의 **개발자 포털** 섹션에서 **OAuth 2.0 + OpenID 커넥트** 선택합니다. 

1. **OAuth 2.0** 탭에서 **추가를** 선택합니다.

1. **표시 이름** 및 **설명** 을 제공합니다.

1. **클라이언트 등록 페이지 URL** 에 대해 `http://localhost`와 같은 자리 표시자 값을 입력합니다. 
    * **클라이언트 등록 페이지 URL은** 사용자가 OAuth 2.0 공급자가 지원하는 고유한 계정을 만들고 구성하는 페이지를 가리킵니다. 
    * 이 예제에서는 사용자가 자신의 계정을 만들고 구성하지 않기 때문에 자리 표시자를 사용합니다.

1. **권한 부여 유형** 에 **인증 코드** 를 선택합니다.

1. 이전에 저장한 **권한 부여 엔드포인트 URL** 및 토큰 **엔드포인트 URL을** 지정합니다. 
    1. **OAuth 2.0 권한 부여 엔드포인트를** 복사하여 **권한 부여 엔드포인트 URL** 텍스트 상자에 붙여넣습니다. 
    1. 권한 부여 요청 메서드에서 **POST** 를 선택합니다.
    1. **OAuth 2.0 토큰 엔드포인트** 를 입력하고 **토큰 엔드포인트 URL** 텍스트 상자에 붙여넣습니다. 
        * **v1** 엔드포인트를 사용하는 경우:
          * 리소스 라는 본문 매개 변수를 **추가합니다.**
          * 값에 백 엔드 앱 **애플리케이션 ID를** 입력합니다.
        * **v2** 엔드포인트를 사용하는 경우:
          * 기본 범위 필드에서 만든 백 엔드 앱 **범위를** 사용합니다.
          * 애플리케이션 매니페스트 에서 속성의 값을 [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) `2` 로 설정합니다. [](../active-directory/develop/reference-app-manifest.md)
          

   >[!IMPORTANT]
   > **v1** 또는 **v2** 엔드포인트를 사용할 수 있지만 v2 엔드포인트를 사용하는 것이 좋습니다. 

1. 클라이언트 앱 자격 증명을 지정합니다.
    * **클라이언트 ID** 에는 클라이언트 앱의 **애플리케이션 ID** 를 사용합니다.
    * **클라이언트 암호** 로 이전에 client-app에 대해 만든 키를 사용합니다. 

1. 권한 부여 코드 부여 형식에 대한 **리디렉션 URI를** 기록해 둡다.

1. **만들기** 를 선택합니다.

1. 클라이언트 앱 등록으로 돌아갑니다. 
 
1. **관리** 에서 **인증** 을 선택합니다.

1. **플랫폼 구성에서:**
    * 플랫폼 **추가를** 클릭합니다.
    * 형식을 **웹** 으로 선택합니다. 
    * 이전에 저장한 리디렉션 URI를 **리디렉션 URI** 아래에 붙여넣습니다.
    * **구성** 단추를 클릭하여 저장합니다.

   이제 개발자 콘솔이 OAuth 2.0 권한 부여 서버를 통해 Azure AD에서 액세스 토큰을 가져올 수 있게 되었으므로 API에 대해 OAuth 2.0 사용자 권한 부여를 사용하도록 설정합니다. 이를 통해 개발자 콘솔에서 API를 호출하기 전에 사용자를 대신하여 액세스 토큰을 확보해야 한다는 것을 알 수 있습니다.

15. API Management 인스턴스로 이동하고 **API** 로 이동합니다.

1. 보호하려는 API를 선택합니다. 예들 들어 `Echo API`입니다.

1. **설정** 으로 이동합니다.

1. **보안에서:**
    1. **OAuth 2.0을** 선택합니다.
    1. 이전에 구성한 OAuth 2.0 서버를 선택합니다. 

1. **저장** 을 선택합니다.

## <a name="5-successfully-call-the-api-from-the-developer-portal"></a>5. 개발자 포털에서 API를 호출했습니다.

> [!NOTE]
> 이 섹션은 개발자 포털을 지원하지 않으므로 **소비** 계층에는 적용되지 않습니다.

OAuth 2.0 사용자 권한 부여를 API에서 사용하도록 설정했으므로, 개발자 콘솔은 API를 호출하기 전에 사용자 대신 액세스 토큰을 획득합니다.

1. 개발자 포털의 API에서 모든 작업을 찾습니다. 
1. **사용해 보세요를** 선택하여 개발자 콘솔로 가져옵니다.

1. 방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.

1. **권한 부여** 드롭다운 목록에서 권한 부여 코드를 선택합니다. 
1. Azure AD 테넌트 로그인하라는 메시지가 표시되면 
    * 계정에 이미 로그인한 경우 메시지가 표시되지 않을 수 있습니다.

1. 성공적으로 로그인한 후 `Authorization` 헤더가 Azure AD의 액세스 토큰과 함께 요청에 추가됩니다. 다음은 샘플 토큰입니다(Base64로 인코딩됨).

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. **보내기** 를 선택하면 API를 성공적으로 호출할 수 있습니다.

## <a name="6-configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>6. 요청에 미리 권한을 부여하도록 JWT 유효성 검사 정책 구성

회:
* 개발자 콘솔에서 전화를 걸려고 했습니다.
* 메시지가 표시되고 Azure AD 테넌트에서 로그인했습니다. 
* 개발자 콘솔은 사용자 대신 액세스 토큰을 가져오고 API에 대한 요청에 토큰을 포함합니다.

그런데 토큰이 없거나 잘못된 토큰이 있는 상태에서 API를 호출하면 어떻게 되나요? 예를 들어 헤더 없이 API를 호출하는 경우 `Authorization` API Management 액세스 토큰의 유효성을 검사하지 않으므로 호출이 계속 진행됩니다. 백 엔드 API에 `Authorization` 헤더만 전달합니다.

들어오는 각 요청의 액세스 토큰의 유효성을 [검사하여 JWT 유효성 검사](./api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 API Management 요청의 권한을 미리 부여합니다. 요청에 유효한 토큰이 없으면 API Management에서 차단합니다. 

다음 정책 예제는 정책 섹션에 추가될 때 `<inbound>` Azure AD에서 가져온 액세스 토큰의 대상 클레임 값을 확인하고 토큰이 유효하지 않으면 오류 메시지를 반환합니다. 


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/v2.0/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>insert aud claim value expected in the token</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> 위의 `openid-config` URL은 v2 엔드포인트에 해당합니다. v1 `openid-config` 엔드포인트의 경우 를 `https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration` 사용합니다.

> [!TIP] 
> **{aad-tenant}** 값을 Azure Portal Azure AD 테넌트 ID로 찾습니다.
> * Azure AD 리소스의 개요 페이지 또는
> * Azure AD 리소스의 **> 속성 관리** 페이지

정책을 구성하는 방법에 대한 내용은 [정책 설정 또는 편집](./set-edit-policies.md)을 참조하세요.

## <a name="build-an-application-to-call-the-api"></a>API를 호출하는 애플리케이션 빌드

이 가이드에서는 OAuth 2.0으로 보호되는 `Echo API`를 호출하기 위해 API Management에서 개발자 콘솔을 샘플 클라이언트 애플리케이션으로 사용했습니다. 애플리케이션을 빌드하고 OAuth 2.0을 구현하는 방법에 대한 자세한 내용은 [Azure AD 코드 샘플을 참조하세요.](../active-directory/develop/sample-v2-code.md)

## <a name="next-steps"></a>다음 단계

- [Azure AD 및 OAuth2.0에](../active-directory/develop/authentication-vs-authorization.md)대해 자세히 알아봅니다.
- API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
- 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](./api-management-howto-mutual-certificates.md)을 참조하세요.
- [API Management 서비스 인스턴스 만들기](./get-started-create-service-instance.md)
- [첫 번째 API 관리](./import-and-publish.md)
