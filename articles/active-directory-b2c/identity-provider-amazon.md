---
title: Amazon 계정으로 가입 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Amazon 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c986f76d812c42caeb03ed3548d03552b8ef1fd3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130228095"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Amazon 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>먼저 Amazon 개발자 콘솔에서 앱을 만듭니다.

Azure AD B2C(Azure Active Directory B2C)에서 Amazon 계정을 통한 사용자 로그인을 지원하려면 [Amazon Developer Services and Technologies](https://developer.amazon.com)(Amazon 개발자 서비스 및 기술)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [Register for Login with Amazon](https://developer.amazon.com/docs/login-with-amazon/register-web.html)(Amazon을 통한 로그인 등록)을 참조하세요. Amazon 계정이 없는 경우 [https://www.amazon.com/](https://www.amazon.com/)에서 등록할 수 있습니다.

1. Amazon 계정 자격 증명을 사용하여 [Amazon 개발자 콘솔](https://developer.amazon.com/dashboard)에 로그인합니다.
1. 아직 수행하지 않은 경우 **등록** 을 클릭하고 개발자 등록 단계를 수행한 후 정책에 동의합니다.
1. 대시보드에서 **Login with Amazon**(Amazon으로 로그인)을 선택합니다.
1. **Create a New Security Profile**(새 보안 프로필 만들기)을 선택합니다.
1. **Security Profile Name**(보안 프로필 이름), **Security Profile Description**(보안 프로필 설명), **Consent Privacy Notice URL**(개인정보처리방침 URL 동의)을 입력합니다. 예를 들어 `https://www.contoso.com/privacy` 개인정보처리방침 URL은 사용자에게 개인정보처리방침 관련 정보를 제공하기 위해 관리하는 페이지입니다. 그런 다음 **Save** 를 클릭합니다.
1. **Login with Amazon Configurations**(Amazon 구성으로 로그인) 섹션에서 사용자가 만든 **Security Profile Name**(보안 프로필 이름)을 선택하고 **Manage**(관리) 아이콘을 선택한 다음 **Web Settings**(웹 설정)를 선택합니다.
1. **웹 설정** 섹션에서 **클라이언트 ID** 값을 복사합니다. **Show Secret**(비밀 표시)을 선택하여 클라이언트 암호를 표시한 후 복사합니다. 테넌트에서 Amazon 계정을 ID 공급자로 구성하려면 두 값이 모두 필요합니다. **클라이언트 보안 비밀** 은 중요한 보안 자격 증명이므로
1. **Web Settings**(웹 설정) 섹션에서 **Edit**(편집)를 선택합니다. 
    1. **Allowed Origins**(허용된 원본)에 `https://your-tenant-name.b2clogin.com`을 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name`을 입력합니다.
    1.  **Allowed Return URLs**(허용된 반환 URL)에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다.  [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다.  `your-tenant-name`을 테넌트 이름으로, `your-domain-name`을 사용자 지정 도메인으로 바꿉니다.
1. **저장** 을 선택합니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **Amazon** 을 선택합니다.
1. **이름** 을 입력합니다. 예를 들면 *Amazon* 입니다.
1. **클라이언트 ID** 에 이전에 만든 Amazon 애플리케이션의 클라이언트 ID를 입력합니다.
1. **클라이언트 암호** 에는 기록한 클라이언트 암호를 입력합니다.
1. **저장** 을 선택합니다.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>사용자 흐름에 Amazon ID 공급자 추가 

이 시점에서 Amazon ID 공급자가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. 사용자 흐름에 Amazon ID 공급자를 추가하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Amazon ID 공급자를 추가하려는 사용자 흐름을 클릭합니다.
1. **소셜 ID 공급자** 에서 **Amazon** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Amazon** 을 선택하여 Amazon 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `AmazonSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon을 ID 공급자로 구성

사용자가 Amazon 계정을 사용하여 로그인하도록 하려면 해당 계정을 클레임 공급자로 정의해야 합니다. 그러면 엔드포인트를 통해 Azure AD B2C와 통신할 수 있습니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Amazon 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.


1. *TrustFrameworkExtensions.xml* 을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
5. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Amazon** 을 선택하여 Amazon 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end
