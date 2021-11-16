---
title: 리소스 소유자 암호 자격 증명 권한 부여를 사용하여 로그인 | Azure
titleSuffix: Microsoft identity platform
description: 리소스 소유자 암호 자격 증명(ROPC) 권한 부여를 사용하여 브라우저 없는 인증 흐름을 지원합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bbfb5923228b3f581981ad23d933e047cee7212a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435982"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft ID 플랫폼 및 OAuth 2.0 리소스 소유자 암호 자격 증명

Microsoft ID 플랫폼은 [OAuth 2.0 ROPC(리소스 소유자 암호 자격 증명) 권한 부여](https://tools.ietf.org/html/rfc6749#section-4.3)를 지원하므로 애플리케이션에서 암호를 직접 처리하여 사용자 로그인을 실행할 수 있습니다.  이 문서에서는 애플리케이션에서 프로토콜에 대해 직접 프로그래밍을 수행하는 방법을 설명합니다.  가능하면 [토큰을 획득하고 보안 Web API를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신, 지원되는 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다.  [MSAL을 사용하는 샘플 앱](sample-v2-code.md)도 살펴봅니다.

> [!WARNING]
> Microsoft 권장 사항: ROPC 흐름을 사용하지 _않는_ 것이 좋습니다. 대부분의 시나리오에서는 더 안전한 대체 방법을 사용하며 권장합니다. 이 흐름은 애플리케이션에서 매우 높은 신뢰 수준을 요구하며, 다른 흐름에는 없는 위험을 전달합니다. 보다 안전한 다른 흐름을 사용할 수 없는 경우에만 이 흐름을 사용해야 합니다.


> [!IMPORTANT]
>
> * Microsoft ID 플랫폼은 Azure AD 테넌트 내의 ROPC만 지원하고 개인 계정은 지원하지 않습니다. 이는 테넌트별 엔드포인트(`https://login.microsoftonline.com/{TenantId_or_Name}`) 또는 `organizations` 엔드포인트를 사용해야 함을 의미합니다.
> * Azure AD 테넌트에 초대된 개인 계정은 ROPC를 사용할 수 없습니다.
> * 암호가 없는 계정은 ROPC로 로그인할 수 없습니다. 즉, SMS 로그인, FIDO 및 Authenticator 앱과 같은 기능이 해당 흐름에서 작동하지 않습니다. 앱 또는 사용자에게 이러한 기능이 필요한 경우 ROPC가 아닌 다른 흐름을 사용합니다.
> * 사용자가 [MFA(Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)를 사용하여 애플리케이션에 로그인해야 하는 경우, 사용자가 차단됩니다.
> * ROPC는 [하이브리드 ID 페더레이션](../hybrid/whatis-fed.md) 시나리오에서 지원되지 않습니다(예: 온-프레미스 계정을 인증하는 데 사용되는 Azure AD 및 ADFS). 사용자가 전체 페이지에서 온-프레미스 ID 공급자로 리디렉션될 경우, Azure AD는 해당 ID 공급자에 대해 사용자 이름 및 암호를 테스트할 수 없습니다. 그러나 [통과 인증](../hybrid/how-to-connect-pta.md)은 ROPC에서 지원됩니다.
> * 하이브리드 ID 페더레이션 시나리오의 예외는 다음과 같습니다. AllowCloudPasswordValidation이 TRUE로 설정된 홈 영역 검색 정책을 사용하면 온-프레미스 암호가 클라우드와 동기화될 때 ROPC 흐름이 페더레이션된 사용자에 대해 작동할 수 있습니다. 자세한 내용은 [레거시 애플리케이션에 페더레이션된 사용자의 직접 ROPC 인증 사용](../manage-apps/home-realm-discovery-policy.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications)을 참조하세요.

[!INCLUDE [try-in-postman-link](includes/try-in-postman-link.md)]

## <a name="protocol-diagram"></a>프로토콜 다이어그램

다음 다이어그램은 ROPC 흐름을 보여 줍니다.

![리소스 소유자 암호 자격 증명 흐름을 보여주는 다이어그램](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>권한 부여 요청

ROPC 흐름은 단일 요청임: 클라이언트 식별 및 사용자의 자격 증명을 IDP에 보내 다음, 반환으로 토큰을 수신합니다. 클라이언트는 작업을 수행하기 전에 사용자의 메일 주소(UPN) 및 암호를 요청해야 합니다. 요청이 성공한 즉시 클라이언트는 메모리에서 사용자의 자격 증명을 안전하게 해제해야 합니다. 이를 저장하면 안 됩니다.

```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| 매개 변수 | 조건 | Description |
| --- | --- | --- |
| `tenant` | 필수 | 사용자를 로그인할 디렉터리 테넌트입니다. 이는 GUID 또는 친숙한 이름 형식일 수 있습니다. 이 매개 변수는 `common` 또는 `consumers`로 설정할 수 없고 `organizations`로 설정할 수 있습니다. |
| `client_id` | 필수 | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당한 애플리케이션(클라이언트) ID |
| `grant_type` | 필수 | `password`로 설정해야 합니다. |
| `username` | 필수 | 사용자의 메일 주소입니다. |
| `password` | 필수 | 사용자 암호입니다. |
| `scope` | 권장 | 앱에 필요한 [범위](v2-permissions-and-consent.md) 또는 권한의 공백으로 구분된 목록입니다. 대화형 흐름에서 관리자 또는 사용자는 이러한 범위에 대해 사전에 동의해야 합니다. |
| `client_secret`| 때때로 필요 | 앱이 퍼블릭 클라이언트인 경우, `client_secret` 또는 `client_assertion`은 포함될 수 없습니다.  앱이 기밀 클라이언트인 경우, 이는 반드시 포함되어야 합니다.|
| `client_assertion` | 때때로 필요 | 인증서를 사용하여 생성되는 다른 형식의 `client_secret`입니다.  자세한 내용은 [인증서 자격 증명](active-directory-certificate-credentials.md)을 참조하세요. |

> [!WARNING]
> 이 흐름을 사용하도록 권장하지 않는 것의 일환으로 공식 SDK는 비밀 또는 어설션을 사용하는 기밀 클라이언트에 대해 이 흐름을 지원하지 않습니다. 사용하려는 SDK가 ROPC를 사용하는 동안 비밀을 추가하는 것을 허용하지 않을 수 있습니다. 

### <a name="successful-authentication-response"></a>성공적인 인증 응답

다음 예제에서는 성공적인 토큰 응답을 보여줍니다.

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| 매개 변수 | 형식 | Description |
| --------- | ------ | ----------- |
| `token_type` | String | 항상 `Bearer`로 설정합니다. |
| `scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우 이 매개 변수는 액세스 토큰이 유효한 범위를 나열합니다. |
| `expires_in`| int | 포함된 액세스 토큰이 유효한 시간(초)입니다. |
| `access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다. |
| `id_token` | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다. |
| `refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다. |

새로 고침 토큰은 [OAuth 코드 흐름 문서](v2-oauth2-auth-code-flow.md#refresh-the-access-token)에 설명된 동일한 흐름을 사용하여 새 액세스 토큰과 새로 고침 토큰을 가져올 수 있습니다.

[!INCLUDE [remind-not-to-validate-access-tokens](includes/remind-not-to-validate-access-tokens.md)]

### <a name="error-response"></a>오류 응답

사용자가 올바른 사용자 이름 또는 암호를 입력하지 않았거나 클라이언트가 요청된 동의를 수신하지 못한 경우 인증에 실패합니다.

| Error | Description | 클라이언트 작업 |
|------ | ----------- | -------------|
| `invalid_grant` | 인증에 실패했습니다. | 자격 증명이 올바르지 않거나 클라이언트에 요청된 범위에 대한 동의가 없습니다. 범위에 권한이 부여되지 않으면 `consent_required` 오류가 반환됩니다. 이 오류가 발생하면 클라이언트는 WebView 또는 브라우저를 사용하여 대화형 프롬프트로 사용자를 전송해야 합니다. |
| `invalid_request` | 요청이 잘못 구성되었습니다. | 권한 부여 유형은 `/common` 또는 `/consumers` 인증 컨텍스트에서 지원되지 않습니다.  그 대신 `/organizations` 또는 테넌트 ID를 사용합니다. |

## <a name="learn-more"></a>자세한 정보

ROPC 사용 예제는 GitHub의 [.NET Core 콘솔 애플리케이션](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) 코드 샘플을 참조하세요.
