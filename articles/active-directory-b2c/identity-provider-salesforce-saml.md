---
title: SAML 프로토콜을 사용하여 Salesforce SAML 공급자로 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 SAML 프로토콜을 사용하여 Salesforce SAML 공급자로 로그인을 설정합니다.
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
ms.openlocfilehash: 911ca309da9df2631e08ff353636877403be710d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007181"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 SAML 프로토콜을 사용하여 Salesforce SAML 공급자로 로그인을 설정합니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)의 [사용자 지정 정책](custom-policy-overview.md)을 사용하여 사용자가 Salesforce 조직에서 로그인할 수 있도록 설정하는 방법을 설명합니다. 사용자 지정 정책에 [SAML ID 공급자](identity-provider-generic-saml.md)를 추가하여 로그인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- [Developer Edition 평가판 계정](https://developer.salesforce.com/signup)을 아직 등록하지 않았으면 등록합니다. 이 문서에서는 [Salesforce Lightning 환경](https://developer.salesforce.com/page/Lightning_Experience_FAQ)을 사용합니다.
- Salesforce 조직에 [내 도메인을 설정](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)합니다.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce를 ID 공급자로 설정

1. [Salesforce에 로그인](https://login.salesforce.com/)합니다.
2. 왼쪽 메뉴의 **설정** 아래에서 **ID** 를 확장하고 **ID 공급자** 를 선택합니다.
3. **ID 공급자 사용** 을 선택합니다.
4. **인증서 선택** 아래에서 Salesforce가 Azure AD B2C와 통신할 때 사용할 인증서를 선택합니다. 기본 인증서를 사용할 수 있습니다.
5. **저장** 을 클릭합니다.

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce에서 연결된 앱 만들기

1. **ID 공급자** 페이지에서 **Service Providers are now created via Connected Apps. Click here.** (서비스 공급자가 연결된 앱을 통해 생성됩니다. 여기를 클릭하세요.)를 클릭합니다.
2. **기본 정보** 아래에서 연결된 앱에 대한 필수 값을 입력합니다.
3. **웹앱 설정** 아래에서 **SAML 사용** 확인란을 선택합니다.
4. **엔터티 ID** 필드에 다음 URL을 입력합니다. `your-tenant` 값을 Azure AD B2C 테넌트의 이름으로 바꾸어야 합니다.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 다음 형식을 사용합니다.

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. **ACS URL** 필드에 다음 URL을 입력합니다. `your-tenant` 값을 Azure AD B2C 테넌트의 이름으로 바꾸어야 합니다.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 다음 형식을 사용합니다.

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. 목록 아래쪽으로 스크롤한 다음 **저장** 을 클릭합니다.

### <a name="get-the-metadata-url"></a>메타데이터 URL 가져오기

1. 연결된 앱의 개요 페이지에서 **관리** 를 클릭합니다.
2. **메타데이터 검색 엔드포인트** 값을 복사하고 저장합니다. 이 문서의 뒷부분에서 사용합니다.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce 사용자가 페더레이션하도록 설정

1. 연결된 앱의 **관리** 페이지에서 **프로필 관리** 를 클릭합니다.
2. Azure AD B2C와 페더레이션하려는 프로필(또는 사용자 그룹)을 선택합니다. 시스템 관리자 권한으로 Salesforce 계정을 사용하여 페더레이션할 수 있도록 **시스템 관리자** 확인란을 선택합니다.

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 ADFS 인증서를 Azure AD B2C 테넌트에 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Upload`을 선택합니다.
1. 정책에 대한 **이름** 을 입력합니다. 예: SAMLSigningCert 키의 이름에 `B2C_1A_` 접두사가 자동으로 추가됩니다.
1. 만든 B2CSigningCert.pfx 인증서로 이동한 후 선택합니다.
1. 인증서의 **암호** 를 입력합니다.
1. **만들기** 를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Salesforce 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Salesforce 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다. 자세한 내용은 [SAML ID 공급자 정의](identity-provider-generic-saml.md)를 참조하세요.

1. *TrustFrameworkExtensions.xml* 을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **PartnerEntity** 값을 앞에서 복사한 Salesforce 메타데이터 URL로 업데이트합니다.
1. **StorageReferenceId** 의 두 인스턴스 값을 서명 인증서의 키 이름으로 업데이트합니다. 예: B2C_1A_SAMLSigningCert.
1. `<ClaimsProviders>` 섹션을 찾은 후 다음 XML 코드 조각을 추가합니다. 정책에 이미 `SM-Saml-idp` 기술 프로필이 포함되어 있는 경우 다음 단계로 건너뜁니다. 자세한 내용은 [Single Sign-On 세션 관리](custom-policy-reference-sso.md)를 참조하세요.

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. 파일을 저장합니다.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Salesforce** 를 선택하여 Salesforce 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.

::: zone-end