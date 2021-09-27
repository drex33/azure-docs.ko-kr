---
title: OAuth 2.0 및 Azure Active Directory를 사용 하 여 API Management에서 API 백 엔드 보호
titleSuffix: Azure API Management
description: Azure API Management에서 web API 백 엔드에 대 한 사용자 액세스를 보호 하는 방법 및 OAuth 2.0 사용자 권한 부여 및 Azure Active Directory를 사용 하는 개발자 포털에 대해 알아봅니다.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 09/17/2021
ms.author: danlep
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6a6a83ef0e810880ff14ddc8125235229db75787
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676178"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-using-oauth-20-authorization-with-azure-active-directory"></a>Azure Active Directory에서 OAuth 2.0 권한 부여를 사용 하 여 Azure API Management에서 웹 API 백 엔드 보호 

이 문서에서는 [Azure Active Directory (Azure AD)에서 OAuth 2.0 프로토콜](../active-directory/develop/active-directory-v2-protocols.md)을 사용 하 여 API를 보호 하도록 [azure API Management](api-management-key-concepts.md) 인스턴스를 구성 하는 방법을 알아봅니다. 

> [!NOTE]
> 이 기능은 API Management의 **개발자**, **기본**, **표준** 및 **Premium** 계층에서 사용할 수 있습니다.  
> 
> 개발자 포털에서 API를 호출 하는 것을 제외 하 고 **소비** 계층에서 아래의 모든 단계를 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 수행 하기 전에 다음이 있어야 합니다.

- API Management 인스턴스
- API Management 인스턴스를 사용 하는 게시 된 API
- Azure AD 테넌트

## <a name="overview"></a>개요

:::image type="content" source="media/api-management-howto-protect-backend-with-aad/overview-graphic-2021.png" alt-text="다음 흐름을 시각적으로 표현 하는 개요 그래픽입니다.":::

1. API를 나타내기 위해 Azure AD에 애플리케이션(backend-app) 등록합니다.

1. API를 호출해야 하는 클라이언트 애플리케이션을 나타내기 위해 Azure AD에 다른 애플리케이션(client-app)을 등록합니다.

1. Azure AD에서 client-app에 backend-app을 호출할 수 있도록 권한을 부여합니다.

1. OAuth 2.0 사용자 권한 부여를 사용 하 여 API를 호출 하도록 개발자 포털에서 개발자 콘솔을 구성 합니다.

1. **validate-jwt** 정책을 추가하여 들어오는 요청마다 OAuth 토큰의 유효성을 검사합니다.

## <a name="1-register-an-application-in-azure-ad-to-represent-the-api"></a>1. API를 나타내기 위해 Azure AD에 응용 프로그램 등록

Azure Portal를 사용 하 여 azure ad에서 API를 나타내는 응용 프로그램을 등록 하 여 Azure AD로 API를 보호 합니다. 

앱 등록에 대한 자세한 내용은 [빠른 시작: 웹 API를 공개하도록 애플리케이션 구성](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 **앱 등록** 를 검색 하 고 선택 합니다.

1. **새 등록** 을 선택합니다. 

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.

   - **이름** 섹션에서 앱 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: *backend-app*)을 입력합니다. 
   - **지원되는 계정 유형** 섹션에서 시나리오에 적합한 옵션을 선택합니다. 

1. [**리디렉션 URI**](../active-directory/develop/reply-url.md) 섹션을 비워 둡니다.

1. **등록** 을 선택하여 애플리케이션을 만듭니다. 

1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다.

1. 측면 메뉴의 **관리** 섹션 아래에서 **API** 표시를 선택 하 고 **응용 프로그램 ID URI** 를 기본값으로 설정 합니다. 나중에 사용하기 위해 이 값을 기록해 둡니다.

1. 범위 **추가** 단추를 선택 하 여 **범위 추가** 페이지를 표시 합니다.
    1. 새 **범위 이름**, **관리자 승인 표시 이름** 및 **관리자 동의 설명을** 입력 합니다.
    1. **사용** 범위 상태가 선택 되어 있는지 확인 합니다.

1. **범위 추가** 단추를 선택하여 범위를 만듭니다. 

1. 8 단계와 9 단계를 반복 하 여 API에서 지 원하는 모든 범위를 추가 합니다.

1. 범위가 만들어지면 이후 단계에서 사용할 수 있도록 기록해 둡니다. 

## <a name="2-register-another-application-in-azure-ad-to-represent-a-client-application"></a>2. 클라이언트 응용 프로그램을 나타내기 위해 Azure AD에 다른 응용 프로그램 등록

API를 호출 하는 모든 클라이언트 응용 프로그램을 Azure AD의 응용 프로그램으로 등록 합니다. 이 예제에서 클라이언트 애플리케이션은 API Management 개발자 포털의 **개발자 콘솔** 입니다. 

개발자 콘솔을 나타내려면 Azure AD에 다른 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **앱 등록** 를 검색 하 고 선택 합니다.

1. **새 등록** 을 선택합니다.

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.

   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: *client-app*)을 입력합니다. 
   - **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트)** 를 선택합니다. 

1. **URI 리디렉션** 섹션에서 `Web`을 선택하고 URL 필드를 아직 비워둡니다.

1. **등록** 을 선택하여 애플리케이션을 만듭니다. 

1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다.

1. 후속 단계에서 사용하기 위해 이 애플리케이션에 대한 클라이언트 암호를 만듭니다.

   1. 측면 메뉴의 **관리** 섹션 아래에서 **인증서 & 암호** 를 선택 합니다.
   1. **클라이언트 암호** 아래에서 **새 클라이언트 암호** 를 선택합니다.
   1. **클라이언트 암호 추가** 에서 **설명을** 입력 하 고 키가 만료 되는 시점을 선택 합니다.
   1. **추가** 를 선택합니다.

비밀이 만들어지면 이후 단계에서 사용할 키 값을 적어 둡니다. 

## <a name="3-grant-permissions-in-azure-ad"></a>3. Azure AD에서 권한 부여

API 및 개발자 콘솔을 나타내는 두 개의 애플리케이션을 등록했으니 client-app이 backend-app을 호출할 수 있도록 권한을 부여합니다.  

1. [Azure Portal](https://portal.azure.com)에서 **앱 등록** 를 검색 하 고 선택 합니다.

1. 클라이언트 앱을 선택합니다. 앱의 페이지 목록에서 **API 권한** 을 선택합니다.

1. **권한 추가** 를 선택합니다.

1. **API 선택** 에서 **내 API** 를 선택한 다음, 백 엔드 앱을 찾아 선택합니다.

1. **위임 된 권한** 을 선택 하 고 백 엔드 앱에 대 한 적절 한 권한을 선택 합니다.

1. **권한 추가** 를 선택합니다.

필요에 따라 다음을 수행합니다.
1. 클라이언트 앱의 **API 권한** 페이지로 이동 합니다.

1. 이 디렉터리의 모든 사용자를 대신 하 여 동의를 부여 하려면 **에 대해 \<your-tenant-name> 관리자 동의 부여** 를 선택 합니다. 

## <a name="4-enable-oauth-20-user-authorization-in-the-developer-console"></a>4. 개발자 콘솔에서 OAuth 2.0 사용자 권한 부여를 사용 하도록 설정

이제 Azure AD에서 애플리케이션을 만들었으며 client-app에서 backend-app을 호출하도록 허용하는 적절한 권한을 부여했습니다. 

이 예제에서는 개발자 콘솔 (클라이언트 앱)에서 OAuth 2.0 사용자 권한 부여를 사용 하도록 설정 합니다.

1. Azure Portal에서 및 **권한 부여 끝점 url** 및 **토큰 끝점 url** 을 찾아 나중에 저장 합니다. 
    1. **앱 등록** 페이지를 엽니다. 
    1. **엔드포인트** 를 선택합니다.
    1. **Oauth 2.0 권한 부여 끝점** 및 **Oauth 2.0 토큰 끝점** 을 복사 합니다. 

1. API Management 인스턴스로 이동합니다.

1. 왼쪽 메뉴의 **개발자 포털** 섹션에서 **OAuth 2.0 + openid connect 커넥트** 를 선택 합니다. 

1. **OAuth 2.0** 탭에서 **추가** 를 선택 합니다.

1. **표시 이름** 및 **설명** 을 제공합니다.

1. **클라이언트 등록 페이지 URL** 에 대해 `http://localhost`와 같은 자리 표시자 값을 입력합니다. 
    * **클라이언트 등록 페이지 URL** 은 사용자가 OAuth 2.0 공급자가 지 원하는 고유한 계정을 만들고 구성 하는 페이지를 가리킵니다. 
    * 이 예에서는 사용자가 자신의 계정을 만들고 구성 하지 않으므로 자리 표시자를 사용 합니다.

1. **권한 부여 유형** 에 **인증 코드** 를 선택합니다.

1. 이전에 저장 한 **권한 부여 끝점 url** 및 **토큰 끝점 url** 을 지정 합니다. 
    1. **OAuth 2.0 권한** 부여 끝점을 복사 하 여 **권한 부여 끝점 URL** 텍스트 상자에 붙여 넣습니다. 
    1. 권한 부여 요청 메서드에서 **POST** 를 선택합니다.
    1. **OAuth 2.0 토큰 끝점** 을 입력 하 고 **토큰 끝점 URL** 텍스트 상자에 붙여넣습니다. 
        * **V1** 끝점을 사용 하는 경우:
          * **리소스** 라는 body 매개 변수를 추가 합니다.
          * 값의 백 엔드 앱 **응용 프로그램 ID** 를 입력 합니다.
        * **V2** 끝점을 사용 하는 경우:
          * **기본 범위** 필드에서 만든 백 엔드 앱 범위를 사용 합니다.
          * [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) `2` [응용 프로그램 매니페스트에서](../active-directory/develop/reference-app-manifest.md)속성의 값을로 설정 합니다.
          

   >[!IMPORTANT]
   > **V1** 또는 **v2** 끝점 중 하나를 사용할 수 있지만 v2 끝점을 사용 하는 것이 좋습니다. 

1. 클라이언트 앱 자격 증명을 지정 합니다.
    * **클라이언트 ID** 에는 클라이언트 앱의 **애플리케이션 ID** 를 사용합니다.
    * **클라이언트 암호** 로 이전에 client-app에 대해 만든 키를 사용합니다. 

1. 인증 코드 부여 형식에 대 한 **리디렉션 URI** 를 적어 둡니다.

1. **생성** 를 선택합니다.

1. 클라이언트 앱 등록으로 돌아갑니다. 
 
1. **관리** 에서 **인증** 을 선택합니다.

1. **플랫폼 구성** 에서:
    * **플랫폼 추가** 를 클릭 합니다.
    * 유형을 **웹** 으로 선택 합니다. 
    * 이전에 저장 한 리디렉션 URI를 **리디렉션** uri에 붙여넣습니다.
    * **구성** 단추를 클릭 하 여 저장 합니다.

   이제 개발자 콘솔이 OAuth 2.0 권한 부여 서버를 통해 Azure AD에서 액세스 토큰을 가져올 수 있으므로 API에 대해 OAuth 2.0 사용자 권한 부여를 사용 하도록 설정 합니다. 이를 통해 개발자 콘솔에서 API를 호출하기 전에 사용자를 대신하여 액세스 토큰을 확보해야 한다는 것을 알 수 있습니다.

15. API Management 인스턴스로 이동하고 **API** 로 이동합니다.

1. 보호하려는 API를 선택합니다. 예들 들어 `Echo API`입니다.

1. **설정** 으로 이동합니다.

1. **보안** 에서:
    1. **OAuth 2.0** 을 선택 합니다.
    1. 이전에 구성한 OAuth 2.0 서버를 선택 합니다. 

1. **저장** 을 선택합니다.

## <a name="5-successfully-call-the-api-from-the-developer-portal"></a>5. 개발자 포털에서 API를 호출 했습니다.

> [!NOTE]
> 이 섹션은 개발자 포털을 지원 하지 않으므로 **소비** 계층에는 적용 되지 않습니다.

OAuth 2.0 사용자 권한 부여를 API에서 사용하도록 설정했으므로, 개발자 콘솔은 API를 호출하기 전에 사용자 대신 액세스 토큰을 획득합니다.

1. 개발자 포털의 API에서 작업을 찾습니다. 
1. **체험** 을 선택 하 여 개발자 콘솔로 이동 합니다.

1. 방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.

1. 권한 부여 드롭다운 목록에서 **권한 부여 코드** 를 선택 합니다. 
1. Azure AD 테 넌 트에 로그인 하 라는 메시지가 표시 되 면 
    * 계정에 이미 로그인 한 경우 메시지가 표시 되지 않을 수 있습니다.

1. 성공적으로 로그인한 후 `Authorization` 헤더가 Azure AD의 액세스 토큰과 함께 요청에 추가됩니다. 다음은 샘플 토큰입니다(Base64로 인코딩됨).

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. **보내기** 를 선택하면 API를 성공적으로 호출할 수 있습니다.

## <a name="6-configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>6. 요청을 사전 인증 하도록 JWT 유효성 검사 정책 구성

지금까지:
* 개발자 콘솔에서 호출 하려고 했습니다.
* Azure AD 테 넌 트에 로그인 하 라는 메시지가 표시 되었습니다. 
* 개발자 콘솔은 사용자를 대신 하 여 액세스 토큰을 가져오고 API에 대 한 요청에 토큰을 포함 합니다.

그런데 토큰이 없거나 잘못된 토큰이 있는 상태에서 API를 호출하면 어떻게 되나요? 예를 들어 헤더 없이 API를 호출 하는 경우 `Authorization` API Management는 액세스 토큰의 유효성을 검사 하지 않으므로 호출은 계속 진행 됩니다. 백 엔드 API에 `Authorization` 헤더만 전달합니다.

들어오는 각 요청에 대 한 액세스 토큰의 유효성을 검사 하 여 [JWT 유효성 검사](./api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용 하 여 API Management에서 요청을 사전 인증 합니다. 요청에 유효한 토큰이 없으면 API Management에서 차단합니다. 

다음 예제 정책은 정책 섹션에 추가 될 때 `<inbound>` AZURE AD에서 가져온 액세스 토큰의 대상 그룹 클레임 값을 확인 하 고 토큰이 유효 하지 않은 경우 오류 메시지를 반환 합니다. 


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
> 위의 `openid-config` URL은 v2 끝점에 해당 합니다. V1 끝점의 경우 `openid-config` 를 사용 `https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration` 합니다.

> [!TIP] 
> 다음 중 하나를 수행 하 여 Azure Portal에서 Azure AD 테 넌 트 ID로 **{aad 테 넌 트}** 값을 찾습니다.
> * Azure AD 리소스의 개요 페이지 또는
> * Azure AD 리소스의 **관리 > 속성** 페이지입니다.

정책을 구성하는 방법에 대한 내용은 [정책 설정 또는 편집](./set-edit-policies.md)을 참조하세요.

## <a name="build-an-application-to-call-the-api"></a>API를 호출하는 애플리케이션 빌드

이 가이드에서는 OAuth 2.0으로 보호되는 `Echo API`를 호출하기 위해 API Management에서 개발자 콘솔을 샘플 클라이언트 애플리케이션으로 사용했습니다. 응용 프로그램을 빌드하고 OAuth 2.0을 구현 하는 방법에 대해 자세히 알아보려면 [AZURE AD 코드 샘플](../active-directory/develop/sample-v2-code.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [AZURE AD 및 oauth 2.0](../active-directory/develop/authentication-vs-authorization.md)에 대해 자세히 알아보세요.
- API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
- 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](./api-management-howto-mutual-certificates.md)을 참조하세요.
- [API Management 서비스 인스턴스 만들기](./get-started-create-service-instance.md)
- [첫 번째 API 관리](./import-and-publish.md)
