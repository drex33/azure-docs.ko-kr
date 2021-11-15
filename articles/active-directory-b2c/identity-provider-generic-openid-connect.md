---
title: OpenID Connect로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 OpenID Connect IdP(ID 공급자)를 사용하여 등록 및 로그인을 설정합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 688210352385e95c7253ac82d95154eaf707d216
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066340"
---
# <a name="set-up-sign-up-and-sign-in-with-generic-openid-connect-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 일반 OpenID Connect를 통한 등록 및 로그인 설정

[OpenID Connect](openid-connect.md)는 보안 사용자 로그인에 사용할 수 있는 OAuth 2.0을 기반으로 빌드된 인증 프로토콜입니다. 이 프로토콜을 사용하는 대부분의 ID 공급자는 Azure AD B2C에서 지원됩니다. 

이 문서에서는 사용자 지정 OpenID Connect ID 공급 기업을 사용자 흐름에 추가하는 방법을 설명합니다.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="add-the-identity-provider"></a>ID 공급자 추가

::: zone pivot="b2c-user-flow"

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. **이름** 을 입력합니다. 예를 들어 *Contoso* 를 입력합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

정책의 확장 파일에서 **ClaimsProviders** 요소에 OpenId Connect ID 공급자를 추가하여 이를 정의합니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Login with Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://your-identity-provider.com/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <!-- <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
          </CryptographicKeys> -->
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

::: zone-end

## <a name="configure-the-identity-provider"></a>ID 공급자 구성

모든 OpenID Connect ID 공급자는 로그인을 수행하는 데 필요한 대부분의 정보를 포함하는 메타데이터 문서를 설명합니다. 메타데이터 문서에는 사용할 URL, 서비스의 공개 서명 키의 위치 등과 같은 정보가 포함됩니다. OpenID Connect 메타데이터 문서는 항상 `.well-known/openid-configuration`으로 끝나는 엔드포인트에 있습니다. 추가하려는 OpenID Connect ID 공급자의 경우 해당 메타데이터 URL을 입력합니다.

::: zone pivot="b2c-user-flow"

**메타데이터 URL** 에 OpenID Connect 메타데이터 문서의 URL을 입력합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="METADATA">` 기술 프로필 메타데이터에 OpenID Connect 메타데이터 문서의 URL을 입력합니다.

::: zone-end

## <a name="client-id-and-secret"></a>클라이언트 ID 및 비밀

사용자가 로그인할 수 있도록 허용하기 위해 ID 공급자는 개발자가 해당 서비스에 애플리케이션을 등록하도록 요청합니다. 이 애플리케이션에는 ID(**클라이언트 ID** 라고 함) 및 **클라이언트 비밀** 이 있습니다. 

클라이언트 비밀은 선택 사항입니다. 하지만 [응답 유형](#response-type)이 `code`인 경우 비밀을 사용하여 토큰과 코드를 교환하는 클라이언트 암호를 제공해야 합니다.

::: zone pivot="b2c-user-flow"

클라이언트 ID 및 클라이언트 암호를 추가하려면 ID 공급자에서 이러한 값을 복사하여 해당 필드에 입력합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="client_id">` 기술 프로필 메타데이터에 클라이언트 ID를 입력합니다.

### <a name="create-a-policy-key"></a>정책 키 만들기

클라이언트 암호가 필요한 경우 이전에 Azure AD B2C 테넌트에 기록한 클라이언트 암호를 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 필터를 선택합니다.
3. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
4. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
5. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
6. **정책 키**, **추가** 를 차례로 선택합니다.
7. **옵션** 으로는 `Manual`을 선택합니다.
8. 정책 키의 **이름** 을 입력합니다. 예들 들어 `ContosoSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
9. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
10. **키 사용** 에서 `Signature`를 선택합니다.
11. **만들기** 를 클릭합니다.
12. `CryptographicKeys` XML 요소에서 다음 요소를 추가합니다.
    
    ```xml
    <CryptographicKeys>
      <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
    </CryptographicKeys>
    ```

::: zone-end

## <a name="scope"></a>범위

범위는 ID 공급자로부터 수집하려는 정보 및 권한을 정의합니다(예: `openid profile`). ID 공급자로부터 ID 토큰을 받으려면 `openid` 범위를 지정해야 합니다. 

사용자는 ID 토큰이 없으면 사용자 지정 ID 공급자를 사용하여 Azure AD B2C에 로그인할 수 없습니다. 다른 범위를 공백으로 구분하여 추가할 수 있습니다. 사용할 수 있는 다른 범위를 확인하려면 사용자 지정 ID 공급자의 설명서를 참조하세요.

::: zone pivot="b2c-user-flow"

**범위** 에서 ID 공급자의 범위를 입력합니다. 예들 들어 `openid profile`입니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="scope">` 기술 프로필 메타데이터에 ID 공급자의 범위를 입력합니다. 예들 들어 `openid profile`입니다.

::: zone-end

## <a name="response-type"></a>응답 형식

응답 유형은 초기 호출 시 사용자 지정 ID 공급자의 `authorization_endpoint`로 반송되는 정보의 종류를 설명합니다. 다음과 같은 응답 유형을 사용할 수 있습니다.

* `code`: [권한 부여 코드 흐름](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)에 따라 코드가 Azure AD B2C로 다시 반환됩니다. 그러면 Azure AD B2C는 `token_endpoint` 호출을 진행하여 토큰용 코드를 교환합니다.
* `id_token`: ID 토큰이 사용자 지정 ID 공급자에서 Azure AD B2C로 다시 반환됩니다.

::: zone pivot="b2c-user-flow"

**응답 유형** 에서 ID 공급자 설정에 따라 `code` 또는 `id_token`을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="response_types">` 기술 프로필 메타데이터에서 ID 공급자 설정에 따라 `code` 또는 `id_token`을 선택합니다.

::: zone-end

## <a name="response-mode"></a>응답 모드

응답 모드는 사용자 지정 ID 공급자에서 Azure AD B2C로 데이터를 다시 보내는 데 사용해야 하는 메서드를 정의합니다. 다음 응답 모드를 사용할 수 있습니다.

* `form_post`: 최상의 보안을 위해 이 응답 모드를 사용하는 것이 좋습니다. 응답이 `application/x-www-form-urlencoded` 형식을 사용하여 본문에서 인코딩되는 코드 또는 토큰으로 HTTP `POST` 메서드를 통해 전송됩니다.
* `query`: 코드 또는 토큰이 쿼리 매개 변수로 반환됩니다.

::: zone pivot="b2c-user-flow"

**응답 모드** 에서 ID 공급자 설정에 따라 `form_post` 또는 `query`를 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="response_mode">` 기술 프로필 메타데이터에서 ID 공급자 설정에 따라 `form_post` 또는 `query`를 선택합니다.

::: zone-end

## <a name="domain-hint"></a>도메인 힌트

[도메인 힌트](direct-signin.md)를 사용하면 사용자가 사용할 수 있는 ID 공급자의 목록 중에 선택하는 대신 지정된 ID 공급자의 로그인 페이지로 바로 건너뛸 수 있습니다. 

이러한 종류의 동작을 허용하려면 도메인 힌트에 대한 값을 입력합니다. 사용자 지정 ID 공급자로 점프하려면 로그인을 위해 Azure AD B2C를 호출할 때 매개 변수 `domain_hint=<domain hint value>`를 사용자 요청의 끝에 추가합니다.

::: zone pivot="b2c-user-flow"

**도메인 힌트** 에서 도메인 힌트에 사용되는 도메인 이름을 입력합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Domain>contoso.com</Domain>` 기술 프로필 XML 요소에서 도메인 힌트에 사용되는 도메인 이름을 입력합니다. 예들 들어 `contoso.com`입니다.

::: zone-end

## <a name="claims-mapping"></a>클레임 매핑

사용자 지정 ID가 ID 토큰을 Azure AD B2C로 다시 보낸 후, Azure AD B2C는 수신된 토큰의 클레임을 Azure AD B2C가 인식 및 사용하는 클레임으로 매핑할 수 있어야 합니다. 아래 각 매핑에 대해 ID 공급자 토큰에서 다시 반환되는 클레임을 파악하려면 사용자 지정 ID 공급자의 설명서를 참조하세요.

::: zone pivot="b2c-user-flow"

* **사용자 ID**: 로그인한 사용자에 대한 *고유 식별자* 를 제공하는 클레임을 입력합니다.
* **표시 이름**: 사용자에 대한 *표시 이름* 또는 *전체 이름* 을 제공하는 클레임을 입력합니다.
* **지정된 이름**: 사용자의 *이름* 을 제공하는 클레임을 입력합니다.
* **Surname(성)** : 사용자의 *성* 을 제공하는 클레임을 입력합니다.
* **이메일**: 사용자의 *이메일 주소* 를 제공하는 클레임을 입력합니다.


::: zone-end

::: zone pivot="b2c-custom-policy"

`OutputClaims` 요소는 ID 공급자가 반환한 클레임 목록을 포함합니다. 정책에 정의된 클레임의 이름을 ID 공급자에 정의된 이름에 매핑합니다. `<OutputClaims>` 요소 아래에서 ID 공급자가 정의한 해당 클레임 이름으로 `PartnerClaimType` 특성을 구성합니다. 

|ClaimTypeReferenceId  |PartnerClaimType  |
|---------|---------|
| `issuerUserId`| 로그인한 사용자에 대한 *고유 식별자* 를 제공하는 클레임을 입력합니다.|
| `displayName`| 사용자에 대한 *표시 이름* 또는 *전체 이름* 을 제공하는 클레임을 입력합니다. |
| `givenName`| 사용자의 *이름* 을 제공하는 클레임을 입력합니다.|
| `surName`| 사용자의 *성* 을 제공하는 클레임을 입력합니다.|
| `email`| 사용자의 *이메일 주소* 를 제공하는 클레임을 입력합니다.|
| `identityProvider` | 토큰 발급자 이름을 제공하는 클레임을 입력합니다. 예들 들어 `iss`입니다. ID 공급자가 토큰에 발급자 클레임을 포함하지 않는 경우 ID 공급자의 고유 식별자를 사용하여 `DefaultValue` 특성을 설정합니다. 예들 들어 `DefaultValue="contoso.com"`입니다.|


::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-the-identity-provider-to-a-user-flow"></a>사용자 흐름에 ID 공급자 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. ID 공급자를 추가할 사용자 흐름을 클릭합니다. 
1. **소셜 ID 공급자** 에서 추가한 ID 공급자를 선택합니다. 예를 들면 *Contoso* 입니다.
1. **저장** 을 선택합니다.

## <a name="test-your-user-flow"></a>사용자 흐름 테스트

1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 로그인할 ID 공급자를 선택합니다. 예를 들면 *Contoso* 입니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션됩니다. 이는 Azure AD B2C에서 반환된 토큰의 내용을 표시합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Contoso** 를 선택하여 Google 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [OpenId Connect 기술 프로필](openid-connect-technical-profile.md) 참조 가이드를 참조하세요.

::: zone-end
