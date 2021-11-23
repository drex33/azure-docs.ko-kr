---
title: Openid connect 커넥트 공급자 구성
description: OpenID Connect 공급자를 App Service 또는 Azure Functions 앱의 ID 공급자로 구성하는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/20/2021
ms.reviewer: mahender
ms.openlocfilehash: 4050e76789253cad9f63f52fc569a483fd082238
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132956655"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider"></a>Openid connect 커넥트 공급자를 사용 하 여 로그인 하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 [OpenID Connect 사양](https://openid.net/connect/)을 준수하는 사용자 지정 인증 공급자를 사용하도록 Azure App Service 또는 Azure Functions를 구성하는 방법을 보여 줍니다. OIDC(OpenID Connect)는 여러 IDP(ID 공급자)에서 사용되는 업계 표준입니다. 준수하는 IDP를 사용하도록 앱을 구성하기 위해 사양의 세부 정보를 이해할 필요는 없습니다.

하나 이상의 OIDC 공급자를 사용 하도록 앱을 구성할 수 있습니다. 각각은 구성에서 고유한 영숫자 이름을 제공 해야 하며, 하나만 기본 리디렉션 대상으로 사용할 수 있습니다.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>ID 공급자에 애플리케이션 등록

애플리케이션의 세부 정보를 공급자에 등록해야 합니다. 다음 단계 중 하나에는 리디렉션 URI를 지정 하는 작업이 포함 됩니다. 이 리디렉션 URI는 형식입니다 `<app-url>/.auth/login/<provider-name>/callback` . 각 id 공급자는 이러한 단계를 완료 하는 방법에 대 한 추가 지침을 제공 해야 합니다.

> [!NOTE]
> 일부 공급자는 구성 및 제공된 값을 사용하는 방법에 대한 추가 단계가 필요할 수 있습니다. 예를 들어 Apple은 OIDC 클라이언트 암호로 사용되지 않는 프라이빗 키를 제공하며 해당 키를 대신 사용하여 앱 구성에 제공하는 암호로 처리되는 JWT를 작성해야 합니다([Apple 설명서를 사용하여 로그인](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)의 “클라이언트 암호 만들기” 섹션 참조).
>

애플리케이션의 **클라이언트 ID** 및 **클라이언트 암호** 를 수집해야 합니다.

> [!IMPORTANT]
> 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
>

또한 공급자에 대한 OpenID Connect 메타데이터가 필요합니다. 이 메타데이터는 일반적으로 공급자의 발급자 URL에 `/.well-known/openid-configuration`이 접미사로 추가된 [구성 메타데이터 문서](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)를 통해 노출됩니다. 이 구성 URL을 수집합니다.

구성 메타데이터 문서를 사용할 수 없는 경우 다음 값을 별도로 수집해야 합니다.

- 발급자 URL(`issuer`로 표시되는 경우도 있음)
- [OAuth 2.0 권한 부여 엔드포인트](https://tools.ietf.org/html/rfc6749#section-3.1)(`authorization_endpoint`로 표시되는 경우도 있음)
- [OAuth 2.0 토큰 엔드포인트](https://tools.ietf.org/html/rfc6749#section-3.2)(`token_endpoint`로 표시되는 경우도 있음)
- [OAuth 2.0 JSON Web Key 집합](https://tools.ietf.org/html/rfc8414#section-2) 문서의 URL(`jwks_uri`로 표시되는 경우도 있음)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>애플리케이션에 공급자 정보 추가

1. [Azure Portal]에 로그인 하 고 앱으로 이동 합니다.
1. 왼쪽의 메뉴에서 **인증** 을 선택합니다. **ID 공급자 추가** 를 클릭합니다.
1. id 공급자 드롭다운에서 **openid connect 커넥트** 를 선택 합니다.
1. **Openid connect 공급자 이름** 에 대해 이전에 선택한 고유 영숫자 이름을 제공 합니다.
1. Id 공급자의 **메타 데이터 문서** 에 대 한 url이 있는 경우 **메타 데이터 url** 에 대 한 해당 값을 제공 합니다. 그렇지 않으면 끝점을 **별도로 제공** 옵션을 선택 하 고 id 공급자에서 수집한 각 URL을 적절 한 필드에 배치 합니다.
1. 적절 한 필드에 이전에 수집 된 **클라이언트 ID** 및 **클라이언트 암호** 를 제공 합니다.
1. 클라이언트 암호의 응용 프로그램 설정 이름을 지정 합니다. 클라이언트 암호는 보안이 안전 하 게 저장 되도록 앱 설정으로 저장 됩니다. Azure Key Vault에서 비밀을 관리하려는 경우 나중에 [Key Vault 참조](./app-service-key-vault-references.md)를 사용하도록 해당 설정을 업데이트할 수 있습니다.
1. **추가** 단추를 클릭 하 여 id 공급자 설정을 마칩니다. 

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
