---
title: 고객 지정 정책을 통한 Single Sign-On 세션 관리
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 사용자 지정 정책을 사용하여 SSO 세션을 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: da47b14774d49a51ffcf574584f82ac0938bd372
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007375"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 Single Sign-On 세션 관리

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[SSO(Single Sign-On) 세션](session-behavior.md) 관리는 사용자 지정 정책에서 다른 기술 프로필과 동일한 의미 체계를 사용합니다. 오케스트레이션 단계를 실행하면 이 단계와 관련된 기술 프로필을 `UseTechnicalProfileForSessionManagement` 참조에 대해 쿼리합니다. 참조된 SSO 세션 공급자가 있는 경우 사용자가 세션 참가자인지를 확인합니다. 그렇다면 SSO 세션 공급자를 사용하여 세션을 다시 채웁니다. 마찬가지로 오케스트레이션 단계의 실행이 완료되면 SSO 세션 공급자가 지정된 경우 이 공급자를 사용하여 세션에서 정보를 저장합니다.

Azure AD B2C는 사용할 수 있는 SSO 세션 공급자 수를 정의합니다.

|세션 공급자  |범위  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  없음       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C 내부 세션 관리자.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Azure AD B2C 및 OAuth1, OAuth2 또는 OpenId Connect ID 공급자 사이.        | 
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | OAuth2 또는 OpenId Connect 신뢰 당사자 애플리케이션 및 Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Azure AD B2C 및 SAML ID 공급자. 그리고 SAML 서비스 공급자(신뢰 당사자 애플리케이션)와 Azure AD B2C 사이.  |        




기술 프로필의 `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 요소를 사용하여 SSO 관리 클래스를 지정합니다.

## <a name="input-claims"></a>입력 클레임

`InputClaims` 요소가 비어 있거나 없습니다.

## <a name="persisted-claims"></a>지속형 클레임

애플리케이션에 반환되거나 후속 단계의 사전 조건에서 사용되어야 하는 클레임은 세션에 저장되거나 디렉터리의 사용자 프로필에서 읽은 내용으로 보강되어야 합니다. 지속형 클레임을 사용하면 인증 경험이 누락된 클레임에 실패하지 않습니다. 세션에서 클레임을 추가하려면 기술 프로필의 `<PersistedClaims>` 요소를 사용합니다. 세션을 다시 채우는 데 공급자를 사용하는 경우 유지되는 클레임은 클레임 모음에 추가됩니다.

## <a name="output-claims"></a>출력 클레임

`<OutputClaims>`는 세션에서 클레임을 검색하는 데 사용됩니다.

## <a name="session-providers"></a>세션 공급자

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

이름이 지정하는 대로 이 공급자는 아무 작업도 수행하지 않습니다. 특정 기술 프로필에 대한 SSO 동작을 무시하기 위해 이 공급자를 사용할 수 있습니다. 다음 `SM-Noop` 기술 프로필은 [사용자 지정 정책 스타터 팩](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)에 포함되어 있습니다.

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

세션에서 클레임을 저장하는 데 이 공급자를 사용할 수 있습니다. 이 공급자는 로컬 계정 및 페더레이션 계정을 관리하기 위해 사용되는 기술 프로필에서 일반적으로 참조됩니다. 다음 `SM-AAD` 기술 프로필은 [사용자 지정 정책 스타터 팩](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)에 포함되어 있습니다.

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


다음 `SM-MFA` 기술 프로필은 [사용자 지정 정책 스타터 팩](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)`SocialAndLocalAccountsWithMfa`에 포함되어 있습니다. 이 기술 프로필은 다단계 인증 세션을 관리합니다.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

이 공급자는 "ID 공급자 선택" 화면을 표시하지 않고 페더레이션 ID 공급자에서 로그아웃하는 데 사용됩니다. 일반적으로 Facebook 또는 Azure Active Directory 등의 페더레이션 ID 공급자를 위해 구성된 기술 프로필에서 참조됩니다. 다음 `SM-SocialLogin` 기술 프로필은 [사용자 지정 정책 스타터 팩](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)에 포함되어 있습니다.

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>메타데이터

| attribute | 필수 | Description|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | No | 현재 사용되지 않습니다. 무시해도 됩니다. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

이 공급자는 OAuth2 또는 OpenID Connect 신뢰 당사자와 Azure AD B2C 간의 Azure AD B2C 세션을 관리하는 데 사용됩니다.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

이 공급자는 신뢰 당사자 애플리케이션 또는 페더레이션된 SAML ID 공급자 간의 Azure AD B2C SAML 세션을 관리하는 데 사용됩니다. SSO 공급자를 사용하여 SAML ID 공급자 세션을 저장할 때 `RegisterServiceProviders`를 `false`로 설정해야 합니다. 다음 `SM-Saml-idp` 기술 프로필은 [SAML ID 공급자](identity-provider-generic-saml.md)가 사용합니다.

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

B2C SAML 세션을 저장하는 데 공급자를 사용하는 경우 `RegisterServiceProviders`는 `true`로 설정되어야 합니다. SAML 세션 로그아웃을 완료하는 데 `SessionIndex` 및 `NameID`가 필요합니다.

다음 `SM-Saml-issuer` 기술 프로필은 [SAML 발급자 기술 프로필](saml-service-provider.md)이 사용합니다.

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>메타데이터

| attribute | 필수 | Description|
| --- | --- | --- |
| IncludeSessionIndex | No | 현재 사용되지 않습니다. 무시해도 됩니다.|
| RegisterServiceProviders | No | 공급자가 어설션을 발급한 모든 SAML 서비스 공급자를 등록해야 함을 의미합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다.|


## <a name="next-steps"></a>다음 단계

[세션 동작](session-behavior.md)에 관해 알아봅니다.
