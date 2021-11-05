---
title: 애플리케이션에 대한 SAML IdP로 Azure Active Directory B2C 구성
title-suffix: Azure Active Directory B2C
description: 애플리케이션(서비스 공급자)에 SAML 프로토콜 어설션을 제공하도록 Azure Active Directory B2C를 구성하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d0bc55f909fe019dedb92d20cce0584ea5d33768
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012817"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에 SAML 애플리케이션 등록

이 문서에서는 인증을 위해 SAML(Security Assertion Markup Language) 애플리케이션(서비스 공급자)을 Azure AD B2C(Azure Active Directory B2C)에 연결하는 방법을 알아봅니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>개요

Azure AD B2C를 고객 ID 및 액세스 관리 솔루션으로 사용하는 조직은 SAML 프로토콜을 사용하여 인증하는 애플리케이션과 통합이 필요할 수 있습니다. 다음 다이어그램에서는 Azure AD B2C가 SAML 기반 애플리케이션으로 SSO(Single-Sign-On)를 구현하기 위해 IdP(*ID 공급자*) 역할을 수행하는 방법을 보여줍니다.

![왼쪽에는 ID 공급자로 사용되고 오른쪽에는 서비스 공급자로 사용되는 Azure Active Directory B2C가 표시된 다이어그램입니다.](media/saml-service-provider/saml-service-provider-integration.png)

1. 애플리케이션이 Azure AD B2C의 SAML 로그인 엔드포인트로 전송되는 SAML AuthN 요청을 만듭니다.
2. 사용자는 Azure AD B2C 로컬 계정 또는 기타 페더레이션 ID 공급자(구성된 경우)를 사용하여 인증할 수 있습니다.
3. 사용자가 페더레이션 ID 공급자를 사용하여 로그인하면 토큰 응답이 Azure AD B2C로 전송됩니다.
4. Azure AD B2C는 SAML 어설션을 생성하고 애플리케이션에 보냅니다.

이 동영상을 시청하여 SAML 애플리케이션을 Azure AD B2C와 통합하는 방법을 알아봅니다. 

>[!Video https://www.youtube.com/embed/r2TIVBCm7v4]

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 시나리오에는 다음이 필요합니다.

* 문서에 설명된 사용자 지정 정책 시작 팩의 *SocialAndLocalAccounts* 사용자 지정 정책. [Azure AD B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)의 단계를 완료합니다. 
* SAML 프로토콜에 대한 기본적인 이해와 애플리케이션의 SAML 구현에 대한 지식.
* SAML 애플리케이션으로 구성된 웹 애플리케이션 SAML AuthN 요청을 보내고 Azure AD B2C에서 SAML 응답을 수신, 디코드 및 확인하는 기능이 있어야 합니다. SAML 애플리케이션은 신뢰 당사자 애플리케이션 또는 서비스 공급자라고도 합니다. 
* SAML 애플리케이션의 공개적으로 사용 가능한 SAML *메타데이터 엔드포인트* 또는 XML 문서
* [Azure AD B2C 테넌트](tutorial-create-tenant.md).

SAML 애플리케이션 및 연결된 메타데이터 엔드포인트가 아직 없는 경우 Microsoft에서 테스트용으로 제공한 [SAML 테스트 애플리케이션][samltest]을 사용할 수 있습니다.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="set-up-certificates"></a>인증서 설정

애플리케이션과 Azure AD B2C 간에 신뢰 관계를 구축하려면 두 서비스 모두 서로의 서명을 만들고 유효성을 검사할 수 있어야 합니다. 애플리케이션 및 Azure AD B2C에서 X509 인증서를 구성합니다.

**애플리케이션 인증서**

| 사용량 | 필수 | Description |
| --------- | -------- | ----------- |
| SAML 요청 서명  | 아니요 | 프라이빗 키가 웹앱에 저장된 인증서입니다. 애플리케이션에서 인증서를 사용하여 Azure AD B2C에 전송된 SAML 요청에 서명합니다. 웹앱은 SAML 메타데이터 엔드포인트를 통해 공개 키를 노출해야 합니다. Azure AD B2C는 애플리케이션 메타데이터의 공개 키를 사용하여 SAML 요청 서명의 유효성을 검사합니다.|
| SAML 어설션 암호화  | 아니요 | 프라이빗 키가 웹앱에 저장된 인증서입니다. 웹앱은 SAML 메타데이터 엔드포인트를 통해 공개 키를 노출해야 합니다. Azure AD B2C는 공개 키를 사용하여 애플리케이션에 대한 어설션을 암호화할 수 있습니다. 그러면 애플리케이션은 프라이빗 키를 사용하여 어설션 암호를 해독합니다.|

**Azure AD B2C 인증서**

| 사용량 | 필수 | Description |
| --------- | -------- | ----------- |
| SAML 응답 서명 | 예  | 프라이빗 키가 Azure AD B2C에 저장된 인증서입니다. Azure AD B2C는 이 인증서를 사용하여 애플리케이션에 전송되는 SAML 응답에 서명합니다. 애플리케이션은 Azure AD B2C의 메타데이터 공개 키를 읽어서 SAML 응답 서명의 유효성을 검사합니다. |
| SAML 어설션 서명 | 예 | 프라이빗 키가 Azure AD B2C에 저장된 인증서입니다. Azure AD B2C는 이 인증서를 사용하여 SAML 응답의 `<saml:Assertion>` 부분에 서명합니다.  |

프로덕션 환경에서는 공용 인증 기관에서 발급한 인증서를 사용하는 것이 좋습니다. 그러나 자체 서명된 인증서를 통해 이 절차를 완료할 수도 있습니다.

### <a name="create-a-policy-key"></a>정책 키 만들기

애플리케이션과 Azure AD B2C 간에 트러스트 관계를 유지하려면 SAML 응답 서명 인증서를 만듭니다. Azure AD B2C는 이 인증서를 사용하여 애플리케이션에 전송되는 SAML 응답에 서명합니다. 애플리케이션은 Azure AD B2C의 메타데이터 공개 키를 읽어서 SAML 응답 서명의 유효성을 검사합니다. 

> [!TIP]
> [SAML 어설션](saml-service-provider-options.md#check-the-saml-assertion-signature)에 서명하는 등 다른 용도로 이 정책 키를 사용할 수 있습니다. 

### <a name="obtain-a-certificate"></a>인증서 받기

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>인증서 업로드

Azure AD B2C 테넌트에 인증서를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 위에서 **전체 서비스** 를 선택한 다음, **Azure AD B2C** 를 검색하여 선택합니다.
1. **개요** 페이지에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 에서 **업로드** 를 선택합니다.
1. **이름** 에서 정책 키의 이름을 입력합니다. 예를 들어 **SamlIdpCert** 를 입력합니다. **B2C_1A_** 접두사가 키의 이름에 자동으로 추가됩니다.
1. 프라이빗 키가 있는 인증서 .pfx 파일을 찾아 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>정책이 SAML 애플리케이션과 연결되도록 설정

SAML 애플리케이션에 연결하려면 Azure AD B2C가 SAML 응답을 만들 수 있어야 합니다.

사용자 지정 정책 시작 팩에서 *SocialAndLocalAccounts\TrustFrameworkExtensions.xml* 을 엽니다.

`<ClaimsProviders>` 섹션을 찾고 다음 XML 조각을 추가하여 SAML 응답 생성기를 구현합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML-based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issuer-uri-of-the-saml-response"></a>SAML 응답의 발급자 URI 구성

SAML 토큰 발급자 기술 프로필의 `IssuerUri` 메타데이터 항목 값을 변경할 수 있습니다. 이 변경 사항은 Azure AD B2C의 SAML 응답에 반환된 `issuerUri` 특성에 반영됩니다. SAML 응답 유효성 검사에 동일한 `IssuerUri` 값을 수락하도록 애플리케이션을 구성해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>SAML 응답을 발급하도록 정책 구성

이제 정책에서 SAML 응답을 생성할 수 있으므로 애플리케이션에 대한 기본 JWT 응답 대신 SAML 응답을 발행하도록 정책을 구성해야 합니다.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>SAML에 대해 구성된 등록 또는 로그인 정책 만들기

1. 시작 팩의 작업 디렉터리에 *SignUpOrSignin.xml* 파일의 복사본을 만들고 새 이름으로 저장합니다. 이 문서에서는 *SignUpOrSigninSAML.xml* 을 예로 사용합니다. 이 파일은 신뢰 당사자에 대한 정책 파일입니다. 기본적으로 JWT 응답을 발급하도록 구성되어 있습니다.

1. 원하는 편집기에서 *SignUpOrSigninSAML.xml* 파일을 엽니다.

1. 정책의 `PolicyId` 및 `PublicPolicyUri` 값을 `B2C_1A_signup_signin_saml` 및 `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml`로 변경할 수 있습니다.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 사용자 경험이 끝나면 Azure AD B2C에 `SendClaims` 단계가 포함됩니다. 이 단계에서는 토큰 발급자 기술 프로필을 참조합니다. 기본 JWT 응답이 아닌 SAML 응답을 발급하려면 `SendClaims`새 SAML 토큰 발급자 기술 프로필을 참조하는 단계를 수정`Saml2AssertionIssuer`합니다.

`<RelyingParty>` 요소 바로 앞에 다음 XML 코드 조각을 추가합니다. 이 XML은 _SignUpOrSignIn_ 사용자 경험의 오케스트레이션 단계 7을 덮어씁니다. 

시작 팩의 다른 폴더에서 시작했거나 오케스트레이션 단계를 추가 또는 제거하여 사용자 경험을 사용자 지정한 경우 `order` 요소의 번호가 토큰 발급자 단계의 사용자 경험에 지정된 번호와 일치하는지 확인합니다. 예를 들어 다른 시작 팩 폴더에서 해당 단계 번호는 `LocalAccounts`의 경우 4, `SocialAccounts`의 경우 6, `SocialAndLocalAccountsWithMfa`의 9입니다.

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

신뢰 당사자 요소는 애플리케이션에서 사용하는 프로토콜을 결정합니다. 기본값은 `OpenId`입니다. `Protocol` 요소는 `SAML`로 변경해야 합니다. 출력 클레임은 SAML 어설션에 대한 클레임 매핑을 만듭니다.

`<RelyingParty>` 요소의 전체 `<TechnicalProfile>` 요소를 다음 기술 프로필 XML로 바꿉니다. `tenant-name`을 Azure AD B2C 테넌트의 이름으로 업데이트합니다.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

신뢰 당사자의 최종 정책 파일은 다음 XML 코드처럼 표시됩니다.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> 이와 동일한 프로세스에 따라 다른 형식의 사용자 흐름(예: 로그인, 암호 재설정 또는 프로필 편집 흐름)을 구현할 수 있습니다.

### <a name="upload-your-policy"></a>정책 업로드

변경 내용을 저장하고 새 *TrustFrameworkExtensions.xml* 및 *SignUpOrSigninSAML.xml* 정책 파일을 Azure Portal에 업로드합니다.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Azure AD B2C IdP SAML 메타데이터 테스트

정책 파일이 업로드된 후 Azure AD B2C는 구성 정보를 사용하여 애플리케이션에서 사용할 ID 공급자의 SAML 메타데이터 문서를 생성합니다. SAML 메타데이터 문서에는 로그인 방법, 로그아웃 방법 및 인증서 등의 서비스 위치가 포함되어 있습니다.

Azure AD B2C 정책 메타데이터는 다음 URL에서 사용할 수 있습니다.

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

`<tenant-name>`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. `<policy-name>`을(를) 정책의 이름(ID)으로 바꿉니다. 예를 들면 다음과 같습니다.

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에 SAML 애플리케이션 등록

Azure AD B2C에서 애플리케이션을 신뢰하려면 Azure AD B2C 애플리케이션 등록을 만듭니다. 이 등록에는 애플리케이션의 메타데이터 엔드포인트와 같은 구성 정보가 포함됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **전체 서비스** 를 선택한 다음 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 **SAMLApp1** 을 입력합니다.
1. **지원되는 계정 유형** 에서 **이 조직 디렉터리의 계정만** 을 선택합니다.
1. **리디렉션 URI** 에서 **웹** 을 선택한 다음, `https://localhost`를 입력합니다. 이 값은 나중에 애플리케이션 등록 매니페스트에서 수정합니다.
1. **등록** 을 선택합니다.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Azure AD B2C에서 애플리케이션 구성

SAML 앱의 경우 애플리케이션 등록 매니페스트에서 여러 속성을 구성해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서, 이전 섹션에서 만든 애플리케이션 등록으로 이동합니다.
1. **관리** 에서 **매니페스트** 를 선택하여 매니페스트 편집기를 엽니다. 그런 다음, 다음 섹션에 설명된 속성을 수정합니다.

#### <a name="add-the-identifier"></a>식별자 추가

SAML 애플리케이션이 Azure AD B2C에 요청하면 SAML AuthN 요청에 `Issuer` 특성이 포함됩니다. 이 특성의 값은 일반적으로 애플리케이션의 메타데이터 `entityID` 값과 같습니다. Azure AD B2C는 이 값을 사용하여 디렉터리에서 애플리케이션 등록을 조회하고 구성을 읽습니다. 이 조회가 성공하려면 애플리케이션 등록의 `identifierUri`가 `Issuer` 특성과 일치하는 값으로 채워져야 합니다.

등록 매니페스트에서 `identifierURIs` 매개 변수를 찾아 적절한 값을 추가합니다. 이 값은 애플리케이션에서 `EntityId`에 대해 SAML AuthN 요청에 구성된 값 및 애플리케이션의 메타데이터에 있는 `entityID` 값과 동일합니다. 또한 `accessTokenAcceptedVersion` 매개 변수를 찾아 값을 `2`로 설정해야 합니다.

> [!IMPORTANT]
> `accessTokenAcceptedVersion`을 `2`로 업데이트하지 않으면 확인된 도메인이 필요한 오류 메시지가 표시됩니다.

다음 예는 SAML 메타데이터의 `entityID` 값을 보여줍니다.

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris` 속성은 도메인 `tenant-name.onmicrosoft.com`의 URL만 허용합니다.

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Azure AD B2C와 애플리케이션의 메타데이터 공유

`identifierUri` 값에 의해 애플리케이션 등록이 로드된 후 Azure AD B2C는 애플리케이션의 메타데이터를 사용하여 SAML AuthN 요청의 유효성을 검사하고 응답 방법을 결정합니다.

애플리케이션에서 공개적으로 액세스할 수 있는 메타데이터 엔드포인트를 노출하는 것이 좋습니다.

SAML 메타데이터 URL과 애플리케이션 등록 매니페스트에 *둘 다* 지정된 속성은 *병합* 됩니다. 메타데이터 URL에 지정된 속성이 먼저 처리되고 우선 적용됩니다.

SAML 테스트 애플리케이션을 예로 사용하면 애플리케이션 매니페스트에서 `samlMetadataUrl`에 다음 값을 사용합니다.

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>어설션 소비자 URL 재정의 또는 설정(선택 사항)

Azure AD B2C가 SAML 응답을 보내는 회신 URL을 구성할 수 있습니다. 회신 URL은 애플리케이션 매니페스트에서 구성할 수 있습니다. 이 구성은 애플리케이션에서 공개적으로 액세스 가능한 메타데이터 엔드포인트를 노출하지 않는 경우 유용합니다.

SAML 애플리케이션에 대한 회신 URL은 애플리케이션이 SAML 응답을 수신해야 하는 엔드포인트입니다. 다음 예와 같이 애플리케이션은 일반적으로 메타데이터 문서에서 이 URL을 `AssertionConsumerService` 요소의 `Location` 특성으로 제공합니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

애플리케이션의 메타데이터 `AssertionConsumerService` 요소가 누락되었거나 이를 재정의하려는 경우 애플리케이션 등록 매니페스트 `replyUrlsWithType` 속성을 구성합니다. Azure AD B2C는 `replyUrlsWithType`을 사용하여 사용자가 `HTTP-POST` 바인딩 유형을 사용하여 로그인한 후 리디렉션합니다.

SAML 테스트 애플리케이션을 예로 들어 `replyUrlsWithType`의 `url` 속성을 다음 JSON 조각에 표시된 값으로 설정합니다.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>로그아웃 URL 재정의 또는 설정(선택 사항)

로그아웃 URL은 로그아웃 요청 후 사용자를 리디렉션할 위치를 정의합니다. 다음 예와 같이 애플리케이션은 일반적으로 메타데이터 문서에서 이 URL을 `SingleLogoutService` 요소의 `Location` 특성으로 제공합니다.

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</SPSSODescriptor>
```

애플리케이션의 메타데이터 `SingleLogoutService` 요소가 누락된 경우 애플리케이션 등록 매니페스트 `logoutUrl` 속성을 구성합니다. Azure AD B2C는 `logoutURL`을 사용하여 사용자가 `HTTP-Redirect` 바인딩 유형을 사용하여 로그아웃한 후 리디렉션합니다.

SAML 테스트 애플리케이션을 예로 사용하여 `logoutUrl` 속성을 `https://samltestapp2.azurewebsites.net/logout`로 설정합니다.

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> `samlMetadataUrl` 속성을 통해 애플리케이션의 메타데이터 엔드포인트를 채우지 않고 애플리케이션 매니페스트에서 응답 URL과 로그아웃 URL을 구성하도록 선택하면 Azure AD B2C가 SAML 요청 서명의 유효성을 검사하지 않습니다. SAML 응답도 암호화하지 않습니다.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>SAML 애플리케이션에서 Azure AD B2C를 SAML IdP로 구성

마지막 단계는 SAML 애플리케이션에서 Azure AD B2C를 SAML IdP로 사용하도록 설정하는 것입니다. 각 애플리케이션은 다르며 단계도 다릅니다. 자세한 내용은 앱 설명서를 참조하세요.

메타데이터는 애플리케이션에서 *정적 메타데이터* 나 *동적 메타데이터* 로 구성할 수 있습니다. 정적 모드에서 Azure AD B2C 정책 메타데이터 전체 또는 일부를 복사합니다. 동적 모드에서 메타데이터에 대한 URL을 제공하고 애플리케이션에서 메타데이터를 동적으로 읽을 수 있도록 합니다.

일반적으로 다음 중 일부 또는 전부가 필요합니다.

* **메타데이터**: `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` 형식을 사용합니다.
* **발급자**: SAML 요청의 `issuer` 값이 애플리케이션 등록 매니페스트의 `identifierUris` 요소에 구성된 URI 중 하나와 일치해야 합니다. SAML 요청의 `issuer` 이름이 `identifierUris` 요소에 없으면 [애플리케이션 등록 매니페스트에 이를 추가](#add-the-identifier)합니다. 예: `https://contoso.onmicrosoft.com/app-name`. 
* **로그인 URL, SAML 엔드포인트, SAML URL**: `<SingleSignOnService>` XML 요소에 대한 Azure AD B2C SAML 정책 메타데이터 파일의 값을 확인합니다.
* **인증서**: 이 인증서는 *B2C_1A_SamlIdpCert* 이며, 프라이빗 키가 없습니다. 인증서의 퍼블릭 키를 가져오려면:

    1. 앞에서 지정한 메타데이터 URL로 이동합니다.
    1. `<X509Certificate>` 요소에서 값을 복사합니다.
    1. 텍스트 파일에 붙여넣습니다.
    1. 텍스트 파일을 *.cer* 파일로 저장합니다.

### <a name="test-with-the-saml-test-app"></a>SAML 테스트 앱을 사용하여 테스트

[SAML 테스트 애플리케이션][samltest]을 사용하여 구성을 테스트할 수 있습니다.

* 테넌트 이름을 업데이트합니다.
* 정책 이름을 업데이트합니다. 예를 들어 *B2C_1A_signup_signin_saml* 을 사용합니다.
* 발급자 URI를 지정합니다. 애플리케이션 등록 매니페스트의 `identifierUris` 요소에 있는 URI 중 하나를 사용합니다. 예를 들면 `https://contoso.onmicrosoft.com/app-name`를 사용합니다.

**로그인** 을 선택하면 사용자 로그인 화면이 표시됩니다. 로그인하면 SAML 응답이 다시 샘플 애플리케이션에 발급됩니다.

## <a name="supported-and-unsupported-saml-modalities"></a>지원되는 SAML 형식 및 지원되지 않는 SAML 형식

다음 SAML 애플리케이션 시나리오는 고유한 메타데이터 엔드포인트를 통해 지원됩니다.

* 애플리케이션 또는 서비스 주체 개체에서 여러 로그아웃 URL 또는 로그아웃 URL의 POST 바인딩을 지정합니다.
* 애플리케이션 또는 서비스 주체 개체에서 신뢰 당사자 요청을 확인하기 위한 서명 키를 지정합니다.
* 애플리케이션 또는 서비스 주체 개체에서 토큰 암호화 키를 지정합니다.
* IdP 시작 로그온을 지정합니다. 여기서 ID 공급자는 Azure AD B2C입니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C GitHub 커뮤니티 리포지토리](https://github.com/azure-ad-b2c/saml-sp-tester)에서 SAML 테스트 웹앱을 가져옵니다.
- [Azure AD B2C에서 SAML 애플리케이션을 등록하는 옵션](saml-service-provider-options.md)을 참조하세요.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
