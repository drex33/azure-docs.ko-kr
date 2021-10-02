---
title: OpenID Connect 공급자(미리 보기) 구성
description: OpenID Connect 공급자를 App Service 또는 Azure Functions 앱의 ID 공급자로 구성하는 방법을 알아봅니다.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: 84a8ce0017b9aac6e2a1c1eb75ef78dff89721a9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355249"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>OpenID Connect 공급자를 사용하여 로그인하도록 App Service 또는 Azure Functions 앱 구성(미리 보기)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 [OpenID Connect 사양](https://openid.net/connect/)을 준수하는 사용자 지정 인증 공급자를 사용하도록 Azure App Service 또는 Azure Functions를 구성하는 방법을 보여 줍니다. OIDC(OpenID Connect)는 여러 IDP(ID 공급자)에서 사용되는 업계 표준입니다. 준수하는 IDP를 사용하도록 앱을 구성하기 위해 사양의 세부 정보를 이해할 필요는 없습니다.

하나 이상의 OIDC 공급자를 사용 하도록 앱을 구성할 수 있습니다. 구성에서 각 공급자에 고유 이름을 지정해야 하며, 하나의 공급자만 기본 리디렉션 대상 역할을 할 수 있습니다.

> [!CAUTION]
> OpenID Connect 공급자를 사용하도록 설정하면 Azure Portal, Azure CLI, Azure PowerShell 등의 일부 클라이언트를 통해 애플리케이션의 App Service 인증/권한 부여 기능을 관리할 수 없습니다. 미리 보기 기간에는 이 기능에 필요한 새 API 표면이 일부 관리 환경에서 고려되지 않습니다.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>ID 공급자에 애플리케이션 등록

애플리케이션의 세부 정보를 공급자에 등록해야 합니다. 해당 공급자와 관련된 지침을 참조하세요. 애플리케이션의 **클라이언트 ID** 및 **클라이언트 암호** 를 수집해야 합니다.

> [!IMPORTANT]
> 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
>

> [!NOTE]
> 일부 공급자는 구성 및 제공된 값을 사용하는 방법에 대한 추가 단계가 필요할 수 있습니다. 예를 들어 Apple은 OIDC 클라이언트 암호로 사용되지 않는 프라이빗 키를 제공하며 해당 키를 대신 사용하여 앱 구성에 제공하는 암호로 처리되는 JWT를 작성해야 합니다([Apple 설명서를 사용하여 로그인](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)의 “클라이언트 암호 만들기” 섹션 참조).
>

선택한 설정 이름을 사용하여 클라이언트 암호를 앱의 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 추가합니다. 나중을 위해 해당 이름을 적어둡니다.

또한 공급자에 대한 OpenID Connect 메타데이터가 필요합니다. 이 메타데이터는 일반적으로 공급자의 발급자 URL에 `/.well-known/openid-configuration`이 접미사로 추가된 [구성 메타데이터 문서](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)를 통해 노출됩니다. 이 구성 URL을 수집합니다.

구성 메타데이터 문서를 사용할 수 없는 경우 다음 값을 별도로 수집해야 합니다.

- 발급자 URL(`issuer`로 표시되는 경우도 있음)
- [OAuth 2.0 권한 부여 엔드포인트](https://tools.ietf.org/html/rfc6749#section-3.1)(`authorization_endpoint`로 표시되는 경우도 있음)
- [OAuth 2.0 토큰 엔드포인트](https://tools.ietf.org/html/rfc6749#section-3.2)(`token_endpoint`로 표시되는 경우도 있음)
- [OAuth 2.0 JSON Web Key 집합](https://tools.ietf.org/html/rfc8414#section-2) 문서의 URL(`jwks_uri`로 표시되는 경우도 있음)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>애플리케이션에 공급자 정보 추가

> [!NOTE]
> 필요한 구성은 새 API 형식으로, 현재 [파일 기반 구성(미리 보기)](configure-authentication-file-based.md)에서만 지원됩니다. 이러한 파일을 사용하여 아래 단계를 수행해야 합니다.

이 섹션에서는 새 IDP를 포함하도록 구성을 업데이트하는 과정을 안내합니다. 예제 구성은 다음과 같습니다.

1. 아직 없는 경우 `identityProviders` 개체 내에 `openIdConnectProviders` 개체를 추가합니다.
1. `openIdConnectProviders` 개체 내에서 새 공급자의 키를 추가합니다. 이 키는 나머지 구성에서 해당 공급자를 참조하는 데 사용되는 식별 이름입니다. 예를 들어 이 공급자를 사용하여 모든 요청을 인증하도록 하려는 경우 `globalValidation.unauthenticatedClientAction`을 “RedirectToLoginPage”로 설정하고 `redirectToProvider`를 동일한 식별 이름으로 설정합니다.
1. 해당 키에 개체를 할당하고 `registration` 개체와 선택적으로 `login` 개체를 포함합니다.
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scopes": [],
             "loginParameterNames": [],
       }
    }
    ```

1. registration 개체 내에서 `clientId`를 수집한 클라이언트 ID로 설정하고 `clientCredential.secretSettingName`을 클라이언트 암호가 저장된 애플리케이션 설정의 이름으로 설정한 다음, `openIdConnectConfiguration` 개체를 만듭니다.

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. `openIdConnectConfiguration` 개체 내에서 앞서 수집한 OpenID Connect 메타데이터를 제공합니다. 수집한 정보에 따라 다음 두 가지 옵션을 사용할 수 있습니다.

    - `wellKnownOpenIdConfiguration` 속성을 앞서 수집한 구성 메타데이터 URL로 설정합니다.
    - 또는 수집된 네 개의 개별 값을 다음과 같이 설정합니다.
        - `issuer`를 발급자 URL로 설정
        - `authorizationEndpoint`를 권한 부여 엔드포인트로 설정
        - `tokenEndpoint`을 토큰 엔드포인트로 설정
        - `certificationUri`를 JSON Web Key 집합 문서의 URL로 설정

    두 옵션을 함께 사용할 수는 없습니다.

이 구성을 설정했으면 앱에서 OpenID Connect 공급자를 인증에 사용할 준비가 된 것입니다.

예제 구성은 다음과 같이 표시될 수 있습니다(Apple로 로그인을 예제로 사용). 여기서 APPLE_GENERATED_CLIENT_SECRET 설정은 [Apple 설명서](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)에 따라 생성된 JWT를 가리킵니다.

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
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scopes": [],
                    "loginParameterNames": []
                }
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
