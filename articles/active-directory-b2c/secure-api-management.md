---
title: Azure Active Directory B2C를 사용하여 Azure API Management API 보호
description: Azure Active Directory B2C에서 발급한 액세스 토큰을 사용하여 Azure API Management API 엔드포인트를 보호하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4d1aab07514f4a42d011cf75a9a22b34246a5653
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566684"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Azure AD B2C를 사용하여 Azure API Management API 보호

Azure AD B2C(Azure Active Directory B2C)에서 인증을 받은 클라이언트로 Azure API Management API에 대한 액세스를 제한하는 방법을 알아봅니다. 이 문서의 지침에 따라 유효한 Azure AD B2C에서 발급된 액세스 토큰을 포함하는 요청으로만 액세스를 제한하는 Azure API Management에서 인바운드 정책을 만들고 테스트합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 먼저 다음 리소스가 있는지 확인하세요.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* [테넌트에 등록된 애플리케이션](tutorial-register-applications.md)
* [테넌트에서 만든 사용자 흐름](tutorial-create-user-flows.md)
* Azure API Management의 [게시된 API](../api-management/import-and-publish.md)
* (선택 사항) 보안 액세스를 테스트하기 위한 [Postman 플랫폼](https://www.getpostman.com/)

## <a name="get-azure-ad-b2c-application-id"></a>Azure AD B2C 애플리케이션 ID 가져오기

Azure AD B2C를 사용하여 Azure API Management에서 API를 보호하는 경우 Azure API Management에서 만든 [인바운드 정책](../api-management/api-management-howto-policies.md)에 대한 몇 가지 값이 필요합니다. 먼저 Azure AD B2C 테넌트에서 이전에 만든 애플리케이션의 애플리케이션 ID를 적어 둡니다. 필수 조건을 충족하기 위해 만든 애플리케이션을 사용하는 경우 *webapp1* 에 대한 애플리케이션 ID를 사용합니다.

Azure AD B2C 테넌트에 애플리케이션을 등록하려면 새로운 통합 ‘앱 등록’ 환경 또는 레거시 ‘애플리케이션’ 환경을 사용하면 됩니다.  [세 등록 환경](./app-registrations-training-guide.md)에 대해 자세히 알아보세요.

# <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 창에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택할 수 있습니다.
1. **앱 등록** 을 선택하고 **소유한 애플리케이션** 탭을 선택합니다.
1. *webapp1* 또는 이전에 만든 다른 애플리케이션에 대한 **애플리케이션(클라이언트) ID** 열의 값을 기록해 둡니다.

# <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 창에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택할 수 있습니다.
1. **관리** 아래에서 **애플리케이션(레거시)** 를 선택합니다.
1. *webapp1* 또는 이전에 만든 다른 애플리케이션에 대한 **애플리케이션 ID** 열의 값을 기록해 둡니다.

* * *

## <a name="get-a-token-issuer-endpoint"></a>토큰 발급자 엔드포인트 가져오기

다음으로 Azure AD B2C 사용자 흐름 중 하나에 대한 잘 알려진 구성 URL을 가져옵니다. 또한 Azure API Management에서 지원하려는 토큰 발급자 엔드포인트 URI가 필요합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure AD B2C 테넌트로 이동합니다.
1. **정책** 아래에서 **사용자 흐름** 을 선택합니다.
1. 기존 정책(예: *B2C_1_signupsignin1*)을 선택하고 **사용자 흐름 실행** 을 선택합니다.
1. 페이지 맨 위의 **사용자 흐름 실행** 제목 아래에 표시되는 하이퍼링크의 URL을 기록해 둡니다. 이 URL은 사용자 흐름에 대한 OpenID Connect의 잘 알려진 검색 엔드포인트로, 다음 섹션에서 Azure API Management에서 인바운드 정책을 구성할 때 이 URL을 사용합니다.

    ![Azure Portal의 “사용자 흐름 실행” 페이지에 있는 잘 알려진 URI 하이퍼링크의 스크린샷](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 하이퍼링크를 선택하여 OpenID Connect의 잘 알려진 구성 페이지로 이동합니다.
1. 브라우저에서 페이지가 열리면 `issuer` 값을 기록해 둡니다. 예를 들면 다음과 같습니다.

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    다음 섹션에서 Azure API Management에서 API를 구성할 때 이 값을 사용합니다.

이제 다음 섹션에서 사용할 2개의 URL인 OpenID Connect의 잘 알려진 구성 엔드포인트 URL과 발급자 URI를 적어 두었을 것입니다. 다음은 그 예입니다.

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-the-inbound-policy-in-azure-api-management"></a>Azure API Management에서 인바운드 정책 구성

이제 API 호출의 유효성을 검사하는 Azure API Management에서 인바운드 정책을 추가할 준비가 되었습니다. 액세스 토큰에서 대상 그룹 및 발급자를 확인하는 [JWT(JSON Web Token) 유효성 검사](../api-management/api-management-access-restriction-policies.md#ValidateJWT) 정책을 추가하여 유효한 토큰이 있는 API 호출만 허용되도록 할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure API Management 인스턴스로 이동합니다.
1. **API** 를 선택합니다.
1. Azure AD B2C를 사용하여 보안을 유지할 API를 선택합니다.
1. **디자인** 탭을 선택합니다.
1. **인바운드 처리** 에서 **\</\>** 를 선택하여 정책 코드 편집기를 엽니다.
1. `<inbound>` 정책 내에 다음 `<validate-jwt>` 태그를 배치하고 다음을 수행합니다.

    a. `<openid-config>` 요소의 `url` 값을 정책의 잘 알려진 구성 URL로 업데이트합니다.  
    b. `<audience>` 요소를 B2C 테넌트에서 이전에 만든 애플리케이션의 애플리케이션 ID(예: *webapp1*)로 업데이트합니다.  
    다. `<issuer>` 요소를 앞에서 적어 둔 토큰 발급자 엔드포인트로 업데이트합니다.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>보안 API 액세스의 유효성 검사

인증된 호출자만 API에 액세스할 수 있도록 하려면 [Postman](https://www.getpostman.com/)으로 API를 호출하여 Azure API Management 구성의 유효성을 검사할 수 있습니다.

API를 호출하려면 Azure AD B2C에서 발급한 액세스 토큰과 Azure API Management 구독 키가 모두 필요합니다.

### <a name="get-an-access-token"></a>액세스 토큰 가져오기

먼저 Postman의 `Authorization` 헤더에서 사용하기 위해 Azure AD B2C에서 발급한 토큰이 필요합니다. 필수 조건 중 하나로 만든 가입/로그인 사용자 흐름의 ‘지금 실행’ 기능을 사용하여 토큰을 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure AD B2C 테넌트로 이동합니다.
1. **정책** 아래에서 **사용자 흐름** 을 선택합니다.
1. 기존 가입/로그인 사용자 흐름(예: *B2C_1_signupsignin1*)을 선택합니다.
1. **애플리케이션** 으로 *webapp1* 을 선택합니다.
1. **회신 URL** 로 `https://jwt.ms`를 선택합니다.
1. **사용자 흐름 실행** 을 선택합니다.

    ![Azure Portal의 가입/로그인 사용자 흐름에 대한 “사용자 흐름 실행” 창의 스크린샷](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 로그인 프로세스를 완료합니다. `https://jwt.ms`으로 리디렉션됩니다.
1. 브라우저에 표시되는 인코딩된 토큰 값을 기록해 둡니다. Postman의 인증 헤더에 이 토큰 값을 사용합니다.

    ![jwt.ms에 표시되는 인코딩된 토큰 값의 스크린샷](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-an-api-subscription-key"></a>API 구독 키 가져오기

게시된 API를 호출하는 클라이언트 애플리케이션(이 경우 Postman)에는 API에 대한 HTTP 요청에 유효한 API Management 구독 키가 포함되어야 합니다. Postman HTTP 요청에 포함할 구독 키를 가져오려면

1. [Azure Portal](https://portal.azure.com)에서 Azure API Management 서비스 인스턴스로 이동합니다.
1. **구독** 을 선택합니다.
1. **제품: 제한 없음** 옆에 있는 줄임표( **...** )를 선택하고 **키 표시/숨기기** 를 선택합니다.
1. 제품의 **기본 키** 를 기록해 둡니다. Postman의 HTTP 요청에서 `Ocp-Apim-Subscription-Key` 헤더에 이 키를 사용합니다.

![“키 표시/숨기기”가 선택된 Azure Portal의 “구독 키” 페이지 스크린샷](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>보안 API 호출 테스트

액세스 토큰 및 Azure API Management 구독 키를 기록해 두었으므로 API에 대한 보안 액세스를 올바르게 구성했는지 테스트할 준비가 되었습니다.

1. [Postman](https://www.getpostman.com/)에서 새 `GET` 요청을 만듭니다. 요청 URL에 대해 게시한 API의 스피커 목록 엔드포인트를 필수 조건 중 하나로 지정합니다. 다음은 그 예입니다.

    `https://contosoapim.azure-api.net/conference/speakers`

1. 그런 후 다음 헤더를 추가합니다.

    | 키 | 값 |
    | --- | ----- |
    | `Authorization` | 앞에서 기록해 둔 인코딩된 토큰 값으로, `Bearer ` 접두사가 붙습니다(“Bearer” 뒤에 공백 포함). |
    | `Ocp-Apim-Subscription-Key` | 앞에서 기록해 둔 Azure API Management 구독 키입니다. |
    | | |

    **GET** 요청 URL 및 **헤더** 는 다음 이미지와 유사하게 표시됩니다.

    ![GET 요청 URL 및 헤더를 보여 주는 Postman UI의 스크린샷](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Postman에서 **보내기** 단추를 선택하여 요청을 실행합니다. 모든 항목을 올바르게 구성한 경우에는 컨퍼런스 발표자 컬렉션을 포함하는 JSON 응답이 제공됩니다(여기서는 잘려서 표시됨).

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>비보안 API 호출 테스트

이제 요청이 성공적으로 완료되었으므로 실패 사례를 테스트하여 *잘못된* 토큰을 사용한 API 호출이 예상대로 거부되는지 확인합니다. 테스트를 수행하는 한 가지 방법은 토큰 값에서 몇 개의 문자를 추가하거나 변경하고 이전과 동일한 `GET` 요청을 실행하는 것입니다.

1. 토큰 값에 여러 문자를 추가하여 잘못된 토큰을 시뮬레이트합니다. 예를 들어 다음과 같이 토큰 값에 “INVALID”를 추가할 수 있습니다.

    ![토큰에 추가된 INVALID 문자열을 보여 주는 Postman UI의 헤더 섹션 스크린샷](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. **보내기** 단추를 선택하여 요청을 실행합니다. 잘못된 토큰을 사용하는 경우 예상되는 결과는 `401` 권한 없는 상태 코드입니다.

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

`401` 상태 코드가 표시되면 Azure AD B2C에서 발급한 유효한 액세스 토큰이 있는 호출자만 Azure API Management API에 대해 성공적인 요청을 수행할 수 있는 것입니다.

## <a name="support-multiple-applications-and-issuers"></a>여러 애플리케이션 및 발급자 지원

일부 애플리케이션은 일반적으로 단일 REST API와 상호 작용합니다. API에서 여러 애플리케이션용으로 발급된 토큰을 허용하도록 하려면 Azure API Management 인바운드 정책의 `<audiences>` 요소에 애플리케이션 ID를 추가합니다.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

마찬가지로, 여러 토큰 발급자를 지원하려면 Azure API Management 인바운드 정책의 `<issuers>` 요소에 엔드포인트 URI를 추가합니다.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>b2clogin.com으로 마이그레이션

레거시 `login.microsoftonline.com` 엔드포인트에서 발급한 토큰의 유효성을 검사하는 Azure API ManagementM API가 있는 경우 해당 API와 [b2clogin.com](b2clogin.md)에서 발급한 토큰을 사용하기 위해 이 API를 호출하는 애플리케이션을 마이그레이션해야 합니다.

다음과 같은 일반적인 프로세스에 따라 단계적 마이그레이션을 수행할 수 있습니다.

1. b2clogin.com 및 login.microsoftonline.com 둘 다에서 발급된 토큰에 대한 Azure API Management 인바운드 정책에 지원을 추가합니다.
1. 애플리케이션을 한 번에 하나씩 업데이트하여 b2clogin.com 엔드포인트에서 토큰을 가져옵니다.
1. 모든 애플리케이션이 b2clogin.com에서 토큰을 제대로 가져오면 API에서 login.microsoftonline.com 발급 토큰에 대한 지원을 제거합니다.

다음 예제 Azure API Management 인바운드 정책은 b2clogin.com 및 login.microsoftonline.com 둘 다에서 발급된 토큰을 허용하는 방법을 보여 줍니다. 또한 이 정책은 두 애플리케이션에서 API 요청을 지원합니다.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>다음 단계

Azure API Management 정책에 대한 자세한 내용은 [Azure API Management 정책 참조 인덱스](../api-management/api-management-policies.md)를 참조하세요.

OWIN 기반 웹 API 및 해당 애플리케이션을 b2clogin.com으로 마이그레이션하는 방법은 [OWIN 기반 웹 API를 b2clogin.com으로 마이그레이션](multiple-token-endpoints.md)을 참조하세요.
