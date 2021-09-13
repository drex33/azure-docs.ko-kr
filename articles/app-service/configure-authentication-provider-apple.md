---
title: Apple로 로그인 구성(미리 보기)
description: App Service 또는 Azure Functions 앱의 ID 공급자로 Apple로 로그인을 구성하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: 4b123b0711954e45108b934622f0fabab5be15e0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259340"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Apple로 로그인 공급자(미리 보기)를 사용하여 로그인하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Apple로 로그인을 인증 공급자로 사용하도록 Azure App Service 또는 Azure Functions를 구성하는 방법을 보여 줍니다. 

이 문서의 절차를 완료하려면 Apple 개발자 프로그램에 등록되어 있어야 합니다. Apple 개발자 프로그램에 등록하려면 [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/)로 이동합니다.

> [!CAUTION]
> Apple로 로그인을 사용하도록 설정하면 Azure Portal, Azure CLI, Azure PowerShell과 같은 일부 클라이언트를 통한 애플리케이션의 App Service 인증/권한 부여 기능의 관리가 사용되지 않습니다. 미리 보기 기간에는 이 기능에 필요한 새 API 표면이 일부 관리 환경에서 고려되지 않습니다.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Apple 개발자 포털에서 애플리케이션 만들기
Apple 개발자 포털에서 앱 ID와 서비스 ID를 만들어야 합니다.

1. Apple 개발자 포털에서 **인증서, 식별자 및 프로필** 로 이동합니다.
2. **식별자** 탭에서 **(+)** 단추를 선택합니다.
3. **새 식별자 등록** 페이지에서 **앱 ID** 를 선택하고 **계속** 을 선택합니다. (앱 ID에는 서비스 ID가 하나 이상 포함되어 있습니다.) ![Apple 개발자 포털에서 새 앱 식별자 등록](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. **앱 ID 등록** 페이지에서 설명과 번들 ID를 제공하고 기능 목록에서 **Apple로 로그인** 을 선택합니다. 그런 다음, **계속** 을 선택합니다. 이 단계에서 **앱 ID 접두사(팀 ID)** 를 기록해 둡니다. 나중에 필요할 것입니다.
![Apple 개발자 포털에서 새 앱 식별자 구성](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. 앱 등록 정보를 검토하고 **등록** 을 선택합니다.
6. 다시 **식별자** 탭에서 **(+)** 단추를 선택합니다.
![Apple 개발자 포털에서 새 서비스 식별자 만들기](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. **새 식별자 등록** 페이지에서 **서비스 ID** 를 선택하고 **계속** 을 선택합니다.
![Apple 개발자 포털에서 새 서비스 식별자 등록](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. **서비스 ID 등록** 페이지에서 설명과 식별자를 제공합니다. 설명은 동의 화면에서 사용자에게 표시되는 내용입니다. 식별자는 App Service를 사용하여 Apple 공급자를 구성하는 데 사용되는 클라이언트 ID가 됩니다. 그런 다음, **구성** 을 선택합니다.
![설명과 식별자 제공](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. 팝업 창에서 기본 앱 ID를 앞에서 생성한 앱 ID로 설정합니다. 도메인 섹션에서 애플리케이션의 도메인을 지정합니다. 반환 URL의 경우 `<app-url>/.auth/login/apple/callback`URL을 사용합니다. 예들 들어 `https://contoso.azurewebsites.net/.auth/login/apple/callback`입니다. 그런 다음, **추가** 와 **저장** 을 선택합니다.
![등록을 위한 도메인과 반환 URL 지정](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. 서비스 등록 정보를 검토하고 **저장** 을 선택합니다.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>클라이언트 암호 생성
Apple에서는 애플리케이션 개발자가 JWT 토큰을 클라이언트 암호 값으로 만들고 서명해야 합니다. 이 암호를 생성하려면 먼저 Apple 개발자 포털에서 타원 곡선 프라이빗 키를 생성하고 다운로드합니다. 그런 다음, 해당 키를 사용하여 [특정 페이로드](#structure-the-client-secret-jwt)로 [JWT를 서명](#sign-the-client-secret-jwt)합니다.

### <a name="create-and-download-the-private-key"></a>프라이빗 키 만들기와 다운로드
1. Apple 개발자 포털의 **키** 탭에서 **키 만들기** 를 선택하거나 **(+)** 단추를 선택합니다.
2. **새 키 등록** 페이지에서 키 이름을 지정하고 **Applefh 로그인** 옆의 확인란을 선택한 후 **구성** 을 선택합니다.
3. **키 구성** 페이지에서 이전에 만든 기본 앱 ID에 키를 연결하고 **저장** 을 선택합니다.
4. 정보를 확인하고 **계속** 을 선택한 다음 정보를 검토하고 **등록** 을 선택하여 키 만들기를 완료합니다.
5. **키 다운로드** 페이지에서 키를 다운로드합니다. `.p8`(PKCS#8) 파일로 다운로드됩니다. - 파일 콘텐츠를 사용하여 클라이언트 암호 JWT에 서명합니다.

### <a name="structure-the-client-secret-jwt"></a>클라이언트 암호 JWT 구조
Apple에서는 base64 인코딩된 JWT 토큰을 클라이언트 암호로 요구합니다. 디코딩된 JWT 토큰의 페이로드는 다음 예제와 같이 구성되어야 합니다.
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**: Apple 클라이언트 ID(서비스 ID이기도 함)
- **iss**: Apple 개발자 팀 ID
- **aud**: Apple에서 토큰을 수신하므로 사용자는 Apple입니다.
- **exp**: **nbf** 후 6개월 이내

위의 페이로드의 base64로 인코딩된 버전은 다음과 같습니다. ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

‘참고: Apple에서는 만료일이 생성(또는 nbf)일로부터 6개월을 넘는 클라이언트 암호 JWT를 허용하지 않습니다. 즉, 최소 6개월마다 클라이언트 암호를 순환해야 합니다.’

토큰 생성과 유효성 검사에 대한 자세한 내용은 [Apple 개발자 설명서](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)에서 확인할 수 있습니다. 

### <a name="sign-the-client-secret-jwt"></a>클라이언트 암호 JWT 서명
`.p8`이전에 다운로드한 파일을 사용하여 클라이언트 암호 JWT에 서명합니다. 이 파일은 PEM 형식의 프라이빗 서명 키를 포함하는 [PCKS#8 파일](https://en.wikipedia.org/wiki/PKCS_8)입니다. JWT를 만들고 서명할 수 있는 많은 라이브러리가 있습니다. 

JWT 토큰을 만들고 서명하기 위해 온라인에서 사용할 수 있는 다양한 종류의 오픈 소스 라이브러리가 있습니다. JWT 토큰 생성에 관한 자세한 내용은 [JWT(JSON Web Token)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)를 참조하세요. 예를 들어 클라이언트 암호를 생성하는 한 가지 방법은 [Microsoft.IdentityModel.Tokens NuGet 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/)를 가져와 아래와 같이 약간의 C# 코드를 실행하는 것입니다.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: Apple 개발자 팀 ID
- **clientId**: Apple 클라이언트 ID(서비스 ID이기도 함)
- **p8key**: PEM 서식 키- 텍스트 편집기에서 `.p8` 파일을 열고 `-----BEGIN PRIVATE KEY-----` `-----END PRIVATE KEY-----` 사이의 모든 항목을 줄 바꿈 없이 복사하여 키를 가져올 수 있습니다.
- **keyId**: 다운로드한 키의 ID

반환된 이 토큰은 Apple 공급자를 구성하는 데 사용할 클라이언트 암호 값입니다.

> [!IMPORTANT]
> 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
>

선택한 설정 이름을 사용하여 앱에 대한 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 클라이언트 암호를 추가합니다. 나중을 위해 해당 이름을 적어 둡니다.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>애플리케이션에 공급자 정보 추가

> [!NOTE]
> 필요한 구성은 새 API 형식으로, 현재 [파일 기반 구성(미리 보기)](configure-authentication-file-based.md)에서만 지원됩니다. 이러한 파일을 사용하여 아래 단계를 수행해야 합니다.

이 섹션에서는 새 IDP를 포함하도록 구성을 업데이트하는 과정을 안내합니다. 예제 구성은 다음과 같습니다.

1. 개체가 없는 경우 `identityProviders` 개체 내에 `apple` 개체를 추가합니다.
2. 해당 키 내의 `registration` 개체에 개체를 할당하고 선택적으로 `login` 개체에도 개체를 할당합니다.
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client ID>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. `registration` 개체 내에서 `clientId`를 수집한 클라이언트 ID에 설정합니다.
    
    b. `registration` 개체 내에서 `clientSecretSettingName`을 클라이언트 암호를 저장한 애플리케이션 설정의 이름으로 설정합니다.
    
    다. `login` 개체 내에서 Apple로 인증할 때 사용되는 범위 목록(예: “이름”과 “메일”)을 포함하도록 `scopes` 배열을 설정할 수도 있습니다. 범위가 구성되면 사용자가 처음으로 로그인할 때 동의 화면에서 명시적으로 요청됩니다.

이 구성을 설정했으면 앱에서 Apple 공급자를 인증에 사용할 준비가 된 것입니다.

전체 구성은 다음 예제와 같이 보일 것입니다. (여기서 APPLE_GENERATED_CLIENT_SECRET 설정은 생성된 JWT를 포함하는 애플리케이션 설정을 가리킵니다.)

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
