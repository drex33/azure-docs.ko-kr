---
title: 사용자 지정 정책의 클레임 해결 프로그램
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 클레임 해결 프로그램을 사용하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 50b386c353e947aadbc196d3040f5cec2b995f82
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037874"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C 사용자 지정 정책의 클레임 해결 프로그램 정보

Azure AD B2C(Azure Active Directory B2C) [사용자 지정 정책](custom-policy-overview.md)의 클레임 해결 프로그램은 정책 이름, 요청 상관관계 ID, 사용자 인터페이스 언어 등의 권한 부여 요청에 대한 컨텍스트 정보를 제공합니다.

입력 또는 출력 클레임에서 클레임 해결 프로그램을 사용하려면 [ClaimsSchema](claimsschema.md) 요소 아래에서 **ClaimType** 문자열을 정의한 후 입력 또는 출력 클레임 요소의 클레임 해결 프로그램에 **DefaultValue** 를 설정합니다. Azure AD B2C는 클레임 해결 프로그램의 값을 읽고 기술 프로필의 값을 사용합니다.

다음 예에서는 이름이 `correlationId`인 클레임 유형이 `string`의 **DataType** 으로 정의됩니다.

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

기술 프로필에서 클레임 해결 프로그램을 클레임 유형으로 매핑합니다. Azure AD B2C는 클레임 해결 프로그램 `{Context:CorrelationId}`의 값을 클레임 `correlationId`에 입력하고 클레임을 기술 프로필에 보냅니다.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>클레임 해결 프로그램 유형

다음 섹션은 사용 가능한 클레임 해결 프로그램을 나열합니다.

### <a name="culture"></a>문화권

| 클레임 | 설명 | 예제 |
| ----- | ----------- | --------|
| {Culture:LanguageName} | 언어에 대한 2자 ISO 코드 | en |
| {Culture:LCID}   | 언어 코드의 LCID | 1033 |
| {Culture:RegionName} | 지역에 대한 2자 ISO 코드 | US |
| {Culture:RFC5646} | RFC5646 언어 코드 | ko-KR |

### <a name="policy"></a>정책

| 클레임 | 설명 | 예제 |
| ----- | ----------- | --------|
| {Policy:PolicyId} | 신뢰 당사자 정책 이름입니다. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | 신뢰 당사자 정책의 테넌트 ID입니다. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | 신뢰 당사자 정책의 테넌트 개체 ID입니다. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | 보안 프레임워크의 테넌트 ID입니다. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| 클레임 | 설명 | 예제 |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |`acr_values` 쿼리 문자열 매개 변수입니다. | 해당 없음 |
| {OIDC:ClientId} |`client_id` 쿼리 문자열 매개 변수입니다. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |`domain_hint` 쿼리 문자열 매개 변수입니다. | facebook.com |
| {OIDC:LoginHint} |  `login_hint` 쿼리 문자열 매개 변수입니다. | someone@contoso.com |
| {OIDC:MaxAge} | `max_age` | 해당 없음 |
| {OIDC:Nonce} |`Nonce` 쿼리 문자열 매개 변수입니다. | defaultNonce |
| {OIDC:Password}| [리소스 소유자 암호 자격 증명 흐름](add-ropc-policy.md) 사용자의 암호입니다.| password1| 
| {OIDC:Prompt} | `prompt` 쿼리 문자열 매개 변수입니다. | 로그인 |
| {OIDC:RedirectUri} |`redirect_uri` 쿼리 문자열 매개 변수입니다. | https://jwt.ms |
| {OIDC:Resource} |`resource` 쿼리 문자열 매개 변수입니다. | 해당 없음 |
| {OIDC:Scope} |`scope` 쿼리 문자열 매개 변수입니다. | openid |
| {OIDC:Username}| [리소스 소유자 암호 자격 증명 흐름](add-ropc-policy.md) 사용자의 사용자 이름입니다.| emily@contoso.com| 

### <a name="context"></a>Context

| 클레임 | 설명 | 예제 |
| ----- | ----------- | --------|
| {Context:BuildNumber} | ID 경험 프레임워크 버전(빌드 번호)입니다.  | 1.0.507.0 |
| {Context:CorrelationId} | 상관관계 ID입니다.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |날짜 시간(UTC)입니다.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |정책 배포 모드입니다.  | 프로덕션 |
| {Context:HostName} | 현재 요청의 호스트 이름입니다.  | contoso.b2clogin.com |
| {Context:IPAddress} | 사용자 IP 주소입니다. | 11.111.111.11 |
| {Context:KMSI} | [로그인 상태 유지](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 확인란을 선택했는지 나타냅니다. |  true |

### <a name="claims"></a>클레임 

| 클레임 | 설명 | 예제 |
| ----- | ----------- | --------|
| {Claim:claim type} | 정책 파일 또는 상위 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다.  예를 들어 `{Claim:displayName}` 또는 `{Claim:objectId}`가 있습니다. | 클레임 유형 값.|


### <a name="oauth2-key-value-parameters"></a>OAuth2 키-값 매개 변수

OIDC 또는 OAuth2 요청의 일부로 포함된 모든 매개 변수 이름은 사용자 경험에서 클레임에 매핑될 수 있습니다. 예를 들어, 애플리케이션의 요청에는 이름이 `app_session`, `loyalty_number` 또는 사용자 지정 쿼리 문자열인 쿼리 문자열 매개 변수가 포함될 수 있습니다.

| 클레임 | 설명 | 예제 |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | 쿼리 문자열 매개 변수입니다. | Hawaii |
| {OAUTH-KV:app_session} | 쿼리 문자열 매개 변수입니다. | A3C5R |
| {OAUTH-KV:loyalty_number} | 쿼리 문자열 매개 변수입니다. | 1234 |
| {OAUTH-KV:any custom query string} | 쿼리 문자열 매개 변수입니다. | 해당 없음 |

### <a name="oauth2"></a>OAuth2

| 클레임 | 설명 | 예제 |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | 액세스 토큰. | 해당 없음 |
| {oauth2:refresh_token} | 토큰 새로 고침. | 해당 없음 |


### <a name="saml"></a>SAML

| 클레임 | 설명 | 예제 |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | SAML 요청의 `AuthnContextClassRef` 요소 값입니다. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | SAML 요청의 `NameIDPolicy` 요소에 있는 `Format` 특성입니다. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Issuer} |  SAML 요청의 SAML `Issuer` 요소 값입니다.| `https://contoso.com` |
| {SAML:AllowCreate} | SAML 요청의 `NameIDPolicy` 요소에 있는 `AllowCreate` 특성 값입니다. | True |
| {SAML:ForceAuthn} | SAML 요청의 `AuthnRequest` 요소에 있는 `ForceAuthN` 특성 값입니다. | True |
| {SAML:ProviderName} | SAML 요청의 `AuthnRequest` 요소에 있는 `ProviderName` 특성 값입니다.| Contoso.com |
| {SAML:RelayState} | `RelayState` 쿼리 문자열 매개 변수입니다.| 
| {SAML:Subject} | SAML AuthN 요청의 NameId 요소에서 가져온 `Subject`입니다.| 

## <a name="using-claim-resolvers"></a>클레임 해결 프로그램 사용

클레임 해결 프로그램은 다음 요소와 함께 사용할 수 있습니다.

| 항목 | 요소 | 설정 |
| ----- | ----------------------- | --------|
|Application Insights 기술 프로필 |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[클레임 변환](claims-transformation-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful 공급자](restful-technical-profile.md) 기술 프로필| `InputClaim`| 1, 2|
|[SAML ID 공급자](identity-provider-generic-saml.md) 기술 프로필| `OutputClaim`| 1, 2|
|[자체 어설션](self-asserted-technical-profile.md) 기술 프로필| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) 기술 프로필| `OutputClaim`| 2 |

설정:
1. `IncludeClaimResolvingInClaimsHandling` 메타데이터를 `true`로 설정해야 합니다.
1. 입력 또는 출력 클레임 특성 `AlwaysUseDefaultValue`를 `true`로 설정해야 합니다.

## <a name="claim-resolvers-samples"></a>클레임 해결 프로그램 샘플

### <a name="restful-technical-profile"></a>RESTful 기술 프로필

[RESTful](restful-technical-profile.md) 기술 프로필에서 사용자 언어, 정책 이름, 범위 및 클라이언트 ID를 전송할 수 있습니다. 클레임을 기반으로 하여 REST API가 사용자 지정 비즈니스 논리를 실행할 수 있고 필요한 경우 지역화된 오류 메시지를 발생합니다.

다음 예제에서는 해당 시나리오의 RESTful 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>직접 로그인

클레임 해결 프로그램을 사용하여 로그인 이름을 미리 채우거나 Facebook, LinkedIn 또는 Microsoft 계정 같은 특정 소셜 ID 공급자에 직접 로그인할 수 있습니다. 자세한 내용은 [Azure Active Directory B2C를 사용하여 직접 로그인 설정](direct-signin.md)을 참조하세요.

### <a name="dynamic-ui-customization"></a>동적 UI 사용자 지정

Azure AD B2C를 사용하면 HTML 콘텐츠 정의 엔드포인트에 쿼리 문자열 매개 변수를 전달하여 페이지 콘텐츠를 동적으로 렌더링할 수 있습니다. 예를 들어 해당 기능을 사용하면 웹 또는 모바일 애플리케이션에서 전달한 사용자 지정 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지에서 배경 이미지를 수정할 수 있습니다. 자세한 내용은 [Dynamically configure the UI by using custom policies in Azure Active Directory B2C](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri)(Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 동적으로 UI 구성)를 참조하세요. 언어 매개 변수를 기반으로 HTML 페이지를 지역화하거나 클라이언트 ID를 기반으로 콘텐츠를 변경할 수도 있습니다.

다음 예제는 값이 `Hawaii`고, **언어** 코드가 `en-US`인 **campaignId** 라는 쿼리 문자열 매개 변수와 클라이언트 ID를 나타내는 **앱** 을 전달합니다.

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

결과적으로 Azure AD B2C는 위의 매개 변수를 HTML 콘텐츠 페이지에 보냅니다.

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>콘텐츠 정의

[ContentDefinition](contentdefinitions.md) `LoadUri`에서 클레임 해결 프로그램을 전송하여 사용된 매개 변수에 따라 여러 위치에서 콘텐츠를 풀할 수 있습니다.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Application Insights 기술 프로필

Azure Application Insights 및 클레임 해결 프로그램을 사용하면 사용자 동작에 대한 인사이트를 얻을 수 있습니다. Application Insights 기술 프로필에서 Azure Application Insights에 지속되는 입력 클레임을 보냅니다. 자세한 내용은 [Application Insights를 사용하여 Azure AD B2C 경험의 사용자 동작 추적](analytics-with-application-insights.md)을 참조하세요. 다음 예는 정책 ID, 상관 관계 ID, 언어 및 클라이언트 ID를 Azure Application Insights에 보냅니다.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>신뢰 당사자 정책

[신뢰 당사자](relyingparty.md) 정책 기술 프로필에서는 테넌트 ID 또는 상관관계 ID를 JWT 내의 신뢰 당사자 애플리케이션에 보낼 수 있습니다.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
