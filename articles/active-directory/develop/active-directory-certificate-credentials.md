---
title: Microsoft ID 플랫폼 인증서 자격 증명
titleSuffix: Microsoft identity platform
description: 이 문서에서는 애플리케이션 인증을 위한 인증서 자격 증명의 등록 및 사용에 대해 설명합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: contperf-fy21q4, aaddev
ms.openlocfilehash: 94abf8e362dabea48c0fa20c488d7b1ca3c70093
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451790"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft ID 플랫폼 애플리케이션 인증 인증서 자격 증명

Microsoft ID 플랫폼을 사용하면 애플리케이션이 클라이언트 암호를 사용할 수 있는 모든 위치(예: OAuth 2.0 [클라이언트 자격 증명 부여](v2-oauth2-client-creds-grant-flow.md) 흐름, [OBO](v2-oauth2-on-behalf-of-flow.md)(On-Behalf-Of) 흐름)에서 자체 자격 증명을 인증에 사용할 수 있습니다.

애플리케이션이 인증에 사용할 수 있는 자격 증명의 한 가지 형태는 애플리케이션에서 소유한 인증서로 서명된 [JWT](./security-tokens.md#json-web-tokens-and-claims)(JSON Web Token) 어설션입니다. `private_key_jwt` 클라이언트 인증 옵션에 대한 [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) 사양에 설명되어 있습니다.

다른 ID 공급자가 발급한 JWT를 애플리케이션에 대한 자격 증명으로 사용하는 데 관심이 있는 경우 페더레이션 정책을 설정 하는 방법에 대 한 [워크로드 ID 페더레이션](workload-identity-federation.md)을 참조하세요.

## <a name="assertion-format"></a>어설션 형식

어설션을 계산하려면 선택한 언어로 많은 JWT 라이브러리 중 하나를 사용할 수 있으며, [MSAL에서 `.WithCertificate()`](msal-net-client-assertions.md)를 사용하여 이를 지원할 수 있습니다. 이 정보는 헤더, 클레임, 서명으로 구성된 토큰으로 전달됩니다.

### <a name="header"></a>헤더

| 매개 변수 |  설명 |
| --- | --- |
| `alg` | **RS256** 이어야 함 |
| `typ` | **JWT** 여야 함 |
| `x5t` | Base64url 문자열 값으로 인코딩된 X.509 인증서 해시(인증서의 SHA-1 ‘지문’이라고도 함)의 16진수 표현입니다. 예를 들어 X.509 인증서 해시가 `84E05C1D98BCE3A5421D225B140B36E86A3D5534`(16진수)인 경우 `x5t` 클레임은 `hOBcHZi846VCHSJbFAs26Go9VTQ=`(Base64url)입니다. |

### <a name="claims-payload"></a>클레임(페이로드)

클레임 유형 | 값 | Description
---------- | ---------- | ----------
`aud` | `https://login.microsoftonline.com/{tenantId}/v2.0` | "aud"(대상) 클레임은 JWT가 대상으로 하는 수신자(여기서는 Azure AD)를 식별합니다. [RFC 7519, 섹션 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3)을 참조하세요.  이 경우 해당 수신자는 로그인 서버(login.microsoftonline.com)입니다.
`exp` | 1601519414 | "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. [RFC 7519, 섹션 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4)를 참조하세요.  이렇게 하면 그 때까지 어설션을 사용할 수 있으므로 `nbf` 이후 최대한 짧게 5~10분간 유지합니다.  Azure AD는 현재 `exp` 시간에 제한을 두지 않습니다. 
`iss` | {ClientID} | "Iss"(발급자) 클레임은 JWT를 발급한 보안 주체(이 경우 클라이언트 애플리케이션)를 식별합니다.  GUID 애플리케이션 ID를 사용합니다.
`jti` | (GUID) | "jti"(JWT ID) 클레임은 JWT에 대한 고유 식별자를 제공합니다. 식별자 값은 동일한 값이 실수로 다른 데이터 개체에 할당될 가능성이 무시되는 방식으로 할당되어야 합니다. 애플리케이션이 여러 발급자를 사용하는 경우 서로 다른 발급자가 생성한 값 간에도 충돌을 방지해야 합니다. "jti" 값은 대/소문자를 구분하는 문자열입니다. [RFC 7519, 섹션 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
`nbf` | 1601519114 | "nbf"(not before) 클레임은 JWT가 그 이전에는 처리를 허용하지 않아야 하는 시간을 식별합니다. [RFC 7519, 섹션 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5)  현재 시간을 사용하는 것이 적절합니다. 
`sub` | {ClientID} | "Sub"(주체) 클레임은 JWT의 주체(이 경우에는 애플리케이션)도 식별합니다. `iss`와 동일한 값을 사용합니다. 

### <a name="signature"></a>서명

서명은 [JSON Web Token RFC7519 사양](https://tools.ietf.org/html/rfc7519)에 설명된 대로 인증서를 적용하여 계산됩니다.

## <a name="example-of-a-decoded-jwt-assertion"></a>디코딩된 JWT 어설션 예제

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>인코딩된 JWT 어설션 예제

다음 문자열은 인코딩된 어설션의 예입니다. 자세히 살펴보면 세 개의 섹션이 점(`.`)으로 구분된 것을 알 수 있습니다.

* 첫 번째 섹션은 ‘헤더’를 인코딩합니다.
* 두 번째 섹션은 ‘클레임’(페이로드)을 인코딩합니다.
* 마지막 섹션은 처음 두 섹션 콘텐츠의 인증서로 계산된 ‘서명’입니다.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Microsoft ID 플랫폼에 인증서 등록

다음 방법 중 하나를 사용하여 Azure Portal을 통해 Microsoft ID 플랫폼에서 클라이언트 애플리케이션과 인증서 자격 증명을 연결할 수 있습니다.

### <a name="uploading-the-certificate-file"></a>인증서 파일 업로드

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. **인증서 및 비밀** > **인증서** 를 선택합니다.
2. **인증서 업로드** 를 클릭하고 업로드할 인증서 파일을 선택합니다.
3. **추가** 를 클릭합니다.
  인증서가 업로드되고 지문, 시작 날짜 및 만료 값이 표시됩니다.

### <a name="updating-the-application-manifest"></a>애플리케이션 매니페스트 업데이트

인증서를 획득한 후 다음 값을 계산합니다.

- `$base64Thumbprint` - Base64로 인코딩된 인증서 해시 값
- `$base64Value` - Base64로 인코딩된 인증서 원시 데이터

애플리케이션 매니페스트에서 키를 식별하는 GUID를 제공합니다(`$keyId`).

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. **매니페스트** 를 선택하여 애플리케이션 매니페스트를 엽니다.
2. 다음 스키마를 사용해서 *keyCredentials* 속성을 새 인증서 정보로 바꿉니다.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 애플리케이션 매니페스트에 편집 내용을 저장한 다음, 매니페스트를 Microsoft ID 플랫폼에 업로드합니다.

   `keyCredentials` 속성은 다중 값이므로 풍부한 키 관리를 위해 여러 인증서를 업로드할 수 있습니다.
   
## <a name="using-a-client-assertion"></a>클라이언트 어설션 사용

클라이언트 어설션은 클라이언트 암호를 사용하는 모든 위치에서 사용할 수 있습니다.  예를 들어 [인증 코드 흐름](v2-oauth2-auth-code-flow.md)에서 `client_secret`을 전달하여 요청이 앱에서 제공됨을 증명할 수 있습니다. 이를 `client_assertion` 및 `client_assertion_type` 매개 변수로 바꿀 수 있습니다. 

| 매개 변수 | 값 | 설명|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| 인증서 자격 증명을 사용 중임을 나타내는 고정 값입니다. |
|`client_assertion`| JWT |위에서 만든 JWT입니다. |

## <a name="next-steps"></a>다음 단계

[MSAL.NET 라이브러리는 단일 코드 줄로 이 시나리오를 처리](msal-net-client-assertions.md)합니다.

GitHub의 [Microsoft ID 플랫폼을 사용하는 .NET Core 디먼 콘솔 애플리케이션](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 코드 샘플은 인증에 자체 자격 증명을 사용하는 방법을 보여 줍니다. 또한 `New-SelfSignedCertificate` PowerShell cmdlet을 사용하여 [자체 서명된 인증서를 만드는](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) 방법을 보여 줍니다. 샘플 리포지토리에서 [앱 만들기 스크립트](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)를 사용하여 인증서를 만들고, 지문 등을 계산할 수도 있습니다.
