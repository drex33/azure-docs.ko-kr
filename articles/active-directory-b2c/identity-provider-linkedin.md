---
title: LinkedIn 계정으로 가입 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 LinkedIn 계정으로 등록 및 로그인을 제공합니다.
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
ms.openlocfilehash: 036ce852c542064949b9d3199f99af7b9a6c0786
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130227814"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 LinkedIn 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>LinkedIn 애플리케이션 만들기

Azure Active Directory B2C(Azure AD B2C)에서 사용자가 LinkedIn 계정으로 로그인할 수 있도록 설정하려면 [LinkedIn 개발자 웹 사이트](https://www.developer.linkedin.com/)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [권한 부여 코드 흐름](/linkedin/shared/authentication/authorization-code-flow)을 참조하세요. LinkedIn 계정이 없는 경우 [https://www.linkedin.com/](https://www.linkedin.com/)에서 가입할 수 있습니다.

1. LinkedIn 계정 자격 증명을 사용하여 [LinkedIn 개발자 웹 사이트](https://www.developer.linkedin.com/)에 로그인합니다.
1. **내 앱** 을 선택하고 **앱 만들기** 를 클릭합니다.
1. **앱 이름**, **LinkedIn 페이지**, **개인정보처리방침 URL**, **앱 로고** 를 입력합니다.
1. LinkedIn **API 사용 조건** 에 동의하고 **앱 만들기** 를 클릭합니다.
1. **인증** 탭을 선택합니다. **인증 키** 에서 **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에서 LinkedIn을 ID 공급자로 구성하려면 둘 다 필요합니다. **클라이언트 보안 비밀** 은 중요한 보안 자격 증명이므로
1. **앱에 대한 권한이 부여된 리디렉션 URL** 옆에 있는 연필 아이콘을 선택한 후 **리디렉션 URL 추가** 를 선택합니다. `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로, `your-domain-name`을 사용자 지정 도메인으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다. **업데이트** 를 선택합니다.
1. 기본적으로 LinkedIn 앱은 로그인과 관련된 범위에 대해 승인되지 않습니다. 검토를 요청하려면 **제품** 탭을 선택한 후 **LinkedIn으로 로그인** 을 선택합니다. 검토가 완료되면 필요한 범위가 애플리케이션에 추가됩니다.
   > [!NOTE]
   > **OAuth 2.0 범위** 섹션의 **인증** 탭에서 앱에 현재 허용되는 범위를 볼 수 있습니다.

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>LinkedIn을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음 **LinkedIn** 을 선택합니다.
1. **이름** 을 입력합니다. 예: LinkedIn.
1. **클라이언트 ID** 에 이전에 만든 LinkedIn 애플리케이션의 클라이언트 ID를 입력합니다.
1. **클라이언트 암호** 에는 기록한 클라이언트 암호를 입력합니다.
1. **저장** 을 선택합니다.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>사용자 흐름에 LinkedIn ID 공급자 추가 

현재 LinkedIn ID 공급자가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. 사용자 흐름에 LinkedIn ID 공급자를 추가하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. LinkedIn ID 공급자를 추가하려는 사용자 흐름을 클릭합니다.
1. **소셜 ID 공급자** 에서 **LinkedIn** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **LinkedIn** 을 선택하여 LinkedIn 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

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
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `LinkedInSecret`입니다. *B2C_1A_* 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-linkedin-as-an-identity-provider"></a>LinkedIn을 ID 공급자로 구성

사용자가 LinkedIn 계정을 사용하여 로그인하도록 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 LinkedIn 계정을 추가하여 해당 계정을 클레임 공급자로 정의합니다.

1. 편집기에서 *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** 파일을 엽니다. 이 파일은 필수 구성 요소 중 하나로 다운로드한 [사용자 지정 정책 시작 팩][starter-pack]에 있습니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

1. **client_id** 의 값을 이전에 기록한 LinkedIn 애플리케이션의 클라이언트 ID로 바꿉니다.
1. 파일을 저장합니다.

### <a name="add-the-claims-transformations"></a>클레임 변환 추가

LinkedIn 기술 프로필의 경우 **ExtractGivenNameFromLinkedInResponse** 및 **ExtractSurNameFromLinkedInResponse** 클레임 변환이 ClaimsTransformations 목록에 추가되어야 합니다. 파일에 **ClaimsTransformations** 요소가 정의되지 않은 경우 아래와 같이 부모 XML 요소를 추가합니다. 클레임 변환에 **nullStringClaim** 이라는 새로운 클레임 형식도 정의되어야 합니다.

*TrustFrameworkExtensions.xml* 파일 상단 근처에 **BuildingBlocks** 요소를 추가합니다. 예제는 *TrustFrameworkBase.xml* 을 참조하세요.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **LinkedIn** 을 선택하여 LinkedIn 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

## <a name="migration-from-v10-to-v20"></a>v1.0에서 v2.0으로 마이그레이션

LinkedIn에서 최근에 [API를 v1.0에서 v2.0으로 업데이트](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)했습니다. 기존 구성을 새 구성으로 마이그레이션하려면 다음 섹션의 정보를 사용하여 기술 프로필의 요소를 업데이트합니다.

### <a name="replace-items-in-the-metadata"></a>메타데이터의 항목 바꾸기

**TechnicalProfile** 의 기존 **메타데이터** 요소에서 다음 **항목** 요소를 바꿉니다.

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

아래와 같이 변경합니다.

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>메타데이터에 항목 추가

**TechnicalProfile** 의 **메타데이터** 에서 다음 **항목** 요소를 추가합니다.

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>OutputClaims 업데이트

**TechnicalProfile** 의 기존 **OutputClaims** 에서 다음 **OutputClaim** 요소를 업데이트합니다.

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

아래와 같이 변경합니다.

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>새 OutputClaimsTransformation 요소 추가

**TechnicalProfile** 의 **OutputClaimsTransformations** 에서 다음 **OutputClaimsTransformation** 요소를 추가합니다.

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>새 클레임 변환 및 클레임 유형 정의

마지막 단계에서 정의해야 하는 새 클레임 변환을 추가했습니다. 클레임 변환을 정의하려면 **ClaimsTransformations** 의 목록에 추가 합니다. 파일에 **ClaimsTransformations** 요소가 정의되지 않은 경우 아래와 같이 부모 XML 요소를 추가합니다. 클레임 변환에 **nullStringClaim** 이라는 새로운 클레임 형식도 정의되어야 합니다.

**BuildingBlocks** 요소는 파일의 상단 근처에 추가해야 합니다. 예제는 *TrustframeworkBase.xml* 을 참조하세요.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>이메일 주소 가져오기

v1.0에서 v2.0으로의 LinkedIn 마이그레이션 과정의 일환으로 이메일 주소를 가져오려면 다른 API에 대한 추가 호출이 필요합니다. 등록하는 동안 이메일 주소를 가져와야 하는 경우 다음을 수행합니다.

1. 사용자가 로그인할 수 있도록 Azure AD B2C와 LinkedIn의 페더레이션을 허용하려면 위의 단계를 완료합니다. 페더레이션의 일부로 Azure AD B2C는 LinkedIn에 대한 액세스 토큰을 받습니다.
1. LinkedIn 액세스 토큰을 클레임에 저장합니다. [여기에서 지침을 참조하세요](idp-pass-through-user-flow.md).
1. LinkedIn의 `/emailAddress` API에 요청을 수행하는 다음 클레임 공급자를 추가합니다. 이 요청에 권한을 부여하려면 LinkedIn 액세스 토큰이 필요합니다.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 사용자가 LinkedIn을 사용하여 로그인할 때 API 클레임 공급자가 트리거될 수 있도록 다음 오케스트레이션 단계를 사용자 경험에 추가합니다. `Order` 수를 적절하게 업데이트해야 합니다. 이 단계를 LinkedIn 기술 프로필을 트리거하는 오케스트레이션 단계 바로 뒤에 추가합니다.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

등록하는 동안 LinkedIn에서 이메일 주소를 가져오는 것은 선택 사항입니다. LinkedIn에서 이메일을 받지 않지만 등록 중에는 이메일을 받아야 하는 경우 사용자는 수동으로 이메일 주소를 입력하고 유효성을 검사해야 합니다.

LinkedIn ID 공급자를 사용하는 정책의 전체 샘플은 [사용자 지정 정책 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)을 참조하세요.

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>v1.0에서 v2.0으로 마이그레이션

LinkedIn에서 최근에 [API를 v1.0에서 v2.0으로 업데이트](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)했습니다. 마이그레이션의 일부로 Azure AD B2C는 등록 중에 LinkedIn 사용자의 전체 이름만 가져올 수 있습니다. 이메일 주소가 등록하는 동안 수집되는 특성 중 하나에 해당하는 경우 사용자는 이메일 주소를 수동으로 입력하고 유효성을 검사해야 합니다.

::: zone-end