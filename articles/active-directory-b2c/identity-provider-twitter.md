---
title: Twitter 계정을 사용하여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Twitter 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dc7e2a22e07f131019e701b54ca24b978454b9ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007156"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Twitter 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>애플리케이션 만들기

사용자가 Azure AD B2C에서 Twitter 계정을 사용하여 로그인하도록 하려면 Twitter 애플리케이션을 만들어야 합니다. Twitter 계정이 없는 경우 [`https://twitter.com/signup`](https://twitter.com/signup)에서 가입할 수 있습니다. 또한 [개발자 계정을 신청](https://developer.twitter.com/en/apply/user.html)해야 합니다. 자세한 내용은 [액세스 신청](https://developer.twitter.com/en/apply-for-access)을 참조하세요.

1. Twitter 계정 자격 증명을 사용하여 [Twitter 개발자 포털](https://developer.twitter.com/portal/projects-and-apps)에 로그인합니다.
1. **독립 실행형 앱** 에서 **+ 앱 만들기** 를 선택합니다.
1. **앱 이름** 을 입력한 다음 **완료** 를 선택합니다.
1. **앱 키** 및 **API 키 암호** 의 값을 복사합니다.  테넌트에서 Twitter를 ID 공급자로 구성하려면 둘 다 사용합니다. 
1. **앱 설정** 에서 **앱 설정** 을 선택합니다.
1. **인증 설정** 에서 **편집** 을 선택합니다.
    1. **3-레그 OAuth 사용** 확인란을 선택합니다.
    1. **사용자의 메일 주소 요청** 확인란을 선택합니다.
    1. **콜백 URL** 에 `https://your-tenant.b2clogin.com/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`를 입력합니다.  [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`를 입력합니다. Azure AD B2C에서 테넌트 이름 및 사용자 흐름 ID가 대문자로 정의되어 있더라도 입력할 때는 소문자만 사용합니다. 다음을
        - `your-tenant-name`을 테넌트 이름으로 바꿉니다.
        - `your-domain-name`을 사용자 지정 도메인으로 바꿉니다.
        - `your-user-flow-Id`를 사용자 흐름의 식별자로 바꿉니다. 예들 들어 `b2c_1a_signup_signin_twitter`입니다. 
    
    1. **웹 사이트 URL** 에 `https://your-tenant.b2clogin.com`을 입력합니다. `your-tenant`을 테넌트 이름으로 바꿉니다. 예들 들어 `https://contosob2c.b2clogin.com`입니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name`을 입력합니다.
    1. **서비스 약관** 의 URL(예: `http://www.contoso.com/tos`)을 입력합니다. 정책 URL은 애플리케이션에 대한 사용 약관을 제공하기 위해 유지 관리하는 페이지입니다.
    1. **개인정보처리방침** 의 URL(예: `http://www.contoso.com/privacy`)을 입력합니다. 정책 URL은 애플리케이션에 대한 개인 정보를 제공하기 위해 유지 관리하는 페이지입니다.
    1. **저장** 을 선택합니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter를 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **Twitter** 를 선택합니다.
1. **이름** 을 입력합니다. 예: *Twitter*
1. **클라이언트 ID** 에 대해 이전에 만든 Twitter 애플리케이션의 ‘API 키’를 입력합니다.
1. **클라이언트 암호** 에는 기록해 둔 ‘API 키 암호’를 입력합니다.
1. **저장** 을 선택합니다.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>사용자 흐름에 Twitter ID 공급자 추가 

이 시점에서 Twitter ID 공급자가 설정되었지만 아직 로그인 페이지에서 사용할 수는 없습니다. 사용자 흐름에 Twitter ID 공급자를 추가하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Twitter ID 공급자를 추가하려는 사용자 흐름을 선택합니다.
1. **소셜 ID 공급자** 에서 **Twitter** 를 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Twitter** 를 선택하여 Twitter 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에 기록한 비밀 키를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `TwitterSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Encryption`를 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter를 ID 공급자로 구성

사용자가 Twitter 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Twitter 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** 의 값을 이전에 기록해 둔 ‘*API 키 암호*’로 바꿉니다.
5. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Twitter** 를 선택하여 Twitter 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end
