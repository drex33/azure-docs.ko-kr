---
title: 기술 프로필
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책의 TechnicalProfiles 요소를 지정합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: f9a85604825cc87fa0b77121502bf0901135ab67
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131036355"
---
# <a name="technical-profiles"></a>기술 프로필

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

*기술 프로필* 은 다른 유형의 당사자와 통신할 수 있는 기본 제공 메커니즘이 있는 프레임워크를 제공합니다. 기술 프로필은 Azure AD B2C(Azure Active Directory B2C) 테넌트와 통신하여 사용자를 만들거나 사용자 프로필을 읽는 데 사용됩니다. 기술 프로필은 사용자와 상호 작용이 가능하도록 자체 어설션될 수 있습니다. 예를 들어 기술 프로필은 사용자의 자격 증명을 수집하여 로그인한 다음, 가입 페이지 또는 암호 재설정 페이지를 렌더링할 수 있습니다.

## <a name="types-of-technical-profiles"></a>기술 프로필 유형

기술 프로필은 다음 유형의 시나리오를 가능하게 합니다.

- [Application Insights](analytics-with-application-insights.md): 이벤트 데이터를 [Application Insights](../azure-monitor/app/app-insights-overview.md)에 보냅니다.
- [Azure AD](active-directory-technical-profile.md): Azure AD B2C 사용자 관리를 지원합니다.
- [Azure AD 다단계 인증](multi-factor-auth-technical-profile.md): Azure AD 다단계 인증을 사용하여 전화 번호를 확인할 수 있도록 지원합니다.
- [클레임 변환](claims-transformation-technical-profile.md): 출력 클레임 변환을 호출하여 클레임 값을 조작하거나, 클레임의 유효성을 검사하거나, 출력 클레임 세트에 대한 기본값을 설정합니다.
- [ID 토큰 힌트](id-token-hint.md): :`id_token_hint` JWT 토큰 서명, 발급자 이름 및 토큰 대상 그룹의 유효성을 검사하고, 인바운드 토큰에서 클레임을 추출합니다.
- [JWT 토큰 발급자](jwt-issuer-technical-profile.md): 신뢰 당사자 애플리케이션으로 다시 반환되는 JWT 토큰을 내보냅니다.
- [OAuth1](oauth1-technical-profile.md): OAuth 1.0 프로토콜 ID 공급자와 페더레이션됩니다.
- [OAuth2](oauth2-technical-profile.md): OAuth 2.0 프로토콜 ID 공급자와 페더레이션됩니다.
- [일회용 암호](one-time-password-technical-profile.md): 일회용 암호 생성 및 확인 관리를 지원합니다.
- [OpenID Connect](openid-connect-technical-profile.md): OpenID Connect 프로토콜 ID 공급자와 페더레이션됩니다.
- [전화 계수](phone-factor-technical-profile.md): 전화 번호 등록 및 확인을 지원합니다.
- [RESTful 공급자](restful-technical-profile.md): 사용자 입력 유효성 검사, 사용자 데이터 보강 또는 기간 업무 애플리케이션과 통합과 같은 REST API 서비스를 호출합니다.
- [SAML ID 공급자](identity-provider-generic-saml.md): SAML 프로토콜 ID 공급자와 페더레이션됩니다.
- [SAML 토큰 발급자](saml-service-provider.md): 신뢰 당사자 애플리케이션으로 다시 반환되는 SAML 토큰을 내보냅니다.
- [자체 어설션](self-asserted-technical-profile.md): 사용자와 상호 작용합니다. 예를 들어 로그인할 사용자의 자격 증명을 수집하여 로그인하거나, 가입 페이지를 렌더링하거나, 암호를 다시 설정합니다.
- [세션 관리](custom-policy-reference-sso.md): 다양한 유형의 세션을 처리합니다.

## <a name="technical-profile-flow"></a>기술 프로필 흐름

모든 유형의 기술 프로필은 동일한 개념을 공유합니다. 입력 클레임 읽기를 시작하고, 클레임 변환을 실행합니다. 그런 다음, ID 공급자, REST API 또는 Azure AD 디렉터리 서비스와 같은 구성된 당사자와 통신합니다. 프로세스가 완료되면 기술 프로필에서 출력 클레임을 반환하고 출력 클레임 변환을 실행할 수 있습니다. 다음 다이어그램은 기술 프로필에서 참조되는 변환 및 매핑이 처리되는 방법을 보여 줍니다. 클레임 변환이 실행되면 기술 프로필이 상호 작용하는 당사자에 관계없이 출력 클레임이 클레임 모음에 즉시 저장됩니다.

![기술 프로필 흐름을 보여 주는 다이어그램](./media/technical-profiles/technical-profile-flow.png)

1. **SSO(Single Sign-On) 세션 관리**: [SSO 세션 관리](custom-policy-reference-sso.md)를 사용하여 기술 프로필의 세션 상태를 복원합니다.
1. **입력 클레임 변환**: 기술 프로필이 시작되기 전에 Azure AD B2C에서 입력 [클레임 변환](claimstransformations.md)을 실행합니다.
1. **입력 클레임**: 기술 프로필에 사용되는 클레임 모음에서 클레임이 선택됩니다.
1. **기술 프로필 실행**: 기술 프로필에서 구성된 당사자와 클레임을 교환합니다. 예를 들면 다음과 같습니다.
    - 사용자를 ID 공급자로 리디렉션하여 로그인을 완료합니다. 로그인에 성공하면 사용자가 다시 돌아가고 기술 프로필 실행이 계속됩니다.
    - 매개 변수를 InputClaims로 보내고, 정보를 OutputClaims로 다시 가져오는 동안 REST API를 호출합니다.
    - 사용자 계정을 만들거나 업데이트합니다.
    - 다단계 인증 문자 메시지를 보내고 확인합니다.
1. **유효성 검사 기술 프로필**: [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)에서 [유효성 검사 기술 프로필](validation-technical-profile.md)을 호출하여 사용자가 프로파일링한 데이터의 유효성을 검사할 수 있습니다.
1. **출력 클레임**: 클레임이 클레임 모음으로 다시 반환됩니다. 이러한 클레임은 다음 오케스트레이션 단계 또는 출력 클레임 변환에서 사용할 수 있습니다.
1. **출력 클레임 변환**: 기술 프로필이 완료되면 Azure AD B2C에서 출력 [클레임 변환](claimstransformations.md)을 실행합니다.
1. **SSO 세션 관리**: [SSO 세션 관리](custom-policy-reference-sso.md)를 사용하여 세션에 대한 기술 프로필의 데이터를 유지합니다.

**TechnicalProfiles** 요소에는 클레임 공급자가 지원하는 기술 프로필 세트가 포함됩니다. 모든 클레임 공급자에는 하나 이상의 기술 프로필이 있어야 합니다. 기술 프로필은 클레임 공급자와 통신하는 데 필요한 엔드포인트 및 프로토콜을 결정합니다. 하나의 클레임 공급자가 여러 개의 기술 프로필을 포함할 수 있습니다.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
|---------|---------|---------|
| Id | 예 | 기술 프로필의 고유 식별자입니다. 기술 프로필은 정책 파일의 다른 요소에서 이 식별자를 사용하여 참조할 수 있습니다. 예를 들어 **OrchestrationSteps** 및 **ValidationTechnicalProfile** 입니다. |

**TechnicalProfile** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 도메인 | 0:1 | 기술 프로필의 도메인 이름입니다. 예를 들어 기술 프로필이 Facebook ID 공급자를 지정하는 경우 도메인 이름은 Facebook.com입니다. |
| DisplayName | 1:1 | 기술 프로필에 대한 표시 이름입니다. |
| Description | 0:1 | 기술 프로필에 대한 설명입니다. |
| 프로토콜 | 1:1 | 다른 당사자와의 통신에 사용되는 프로토콜입니다. |
| 메타데이터 | 0:1 | 기술 프로필의 동작을 제어하는 키 및 값 세트입니다. |
| InputTokenFormat | 0:1 | 입력 토큰의 형식입니다. 가능한 값은 `JSON`, `JWT`, `SAML11` 및 `SAML2`입니다. `JWT` 값은 IETF 사양에 따른 JSON Web Token을 나타냅니다. `SAML11` 값은 OASIS 사양에 따른 SAML 1.1 보안 토큰을 나타냅니다. `SAML2` 값은 OASIS 사양에 따른 SAML 2.0 보안 토큰을 나타냅니다. |
| OutputTokenFormat | 0:1 | 출력 토큰의 형식입니다. 가능한 값은 `JSON`, `JWT`, `SAML11` 및 `SAML2`입니다. |
| CryptographicKeys | 0:1 | 기술 프로필에 사용되는 암호화 키 목록입니다. |
| InputClaimsTransformations | 0:1 | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환에 대한 이전에 정의된 참조 목록입니다. |
| InputClaims | 0:1 | 기술 프로필에서 입력으로 사용되는 클레임 유형에 대해 이전에 정의된 참조 목록입니다. |
| PersistedClaims | 0:1 | 기술 프로필에서 유지되는 클레임 유형에 대해 이전에 정의된 참조 목록입니다. |
| DisplayClaims | 0:1 | [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)에서 유지되는 클레임 유형에 대해 이전에 정의된 참조 목록입니다. DisplayClaims 기능은 현재 미리 보기에 있습니다. |
| OutputClaims | 0:1 | 기술 프로필에서 출력으로 사용되는 클레임 유형에 대해 이전에 정의된 참조 목록입니다. |
| OutputClaimsTransformations | 0:1 | 클레임 공급자로부터 클레임이 수신된 후에 실행해야 하는 클레임 변환에 대한 이전에 정의된 참조 목록입니다. |
| ValidationTechnicalProfiles | 0:n | 기술 프로필이 유효성 검사를 위해 사용하는 다른 기술 프로필에 대한 참조 목록입니다. 자세한 내용은 [유효성 검사 기술 프로필](validation-technical-profile.md)을 참조하세요.|
| SubjectNamingInfo | 0:1 | 주체 이름이 클레임과 별도로 지정되는 토큰에서 주체 이름 생성을 제어합니다. 예를 들어 OAuth 또는 SAML입니다. |
| IncludeInSso | 0:1 | 이 기술 프로필을 사용하여 SSO 동작을 세션에 적용해야 하는지, 아니면 명시적 상호 작용이 대신 필요한지 여부입니다. 이 요소는 유효성 검사 기술 프로필 내에서 사용되는 SelfAsserted 프로필에서만 유효합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| IncludeClaimsFromTechnicalProfile | 0:1 | 모든 입력 및 출력 클레임을 이 기술 프로필에 추가하려는 기술 프로필의 식별자입니다. 참조된 기술 프로필은 동일한 정책 파일에 정의되어야 합니다. |
| IncludeTechnicalProfile |0:1 | 모든 데이터를 이 기술 프로필에 추가하려는 기술 프로필의 식별자입니다. |
| UseTechnicalProfileForSessionManagement | 0:1 | 세션 관리에 사용할 다른 기술 프로필입니다. |
|EnabledForUserJourneys| 0:1 |사용자 경험에서 기술 프로필을 실행할지 여부를 제어합니다. |

## <a name="protocol"></a>프로토콜

**Protocol** 요소는 다른 당사자와 통신하는 데 사용할 프로토콜을 지정합니다. **Protocol** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 속성 | 예 | 기술 프로필의 일부로 사용되는 Azure AD B2C에서 지원하는 유효한 프로토콜의 이름입니다. 가능한 값은 `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `Proprietary` 또는 `None`입니다. |
| Handler | 예 | 프로토콜 이름이 `Proprietary`로 설정된 경우 Azure AD B2C에서 프로토콜 처리기를 확인하는 데 사용하는 어셈블리의 이름을 지정합니다. |

## <a name="metadata"></a>메타데이터

**Metadata** 요소에는 특정 프로토콜에 대한 관련 구성 옵션이 포함됩니다. 지원되는 메타데이터 목록은 해당 [기술 프로필](#types-of-technical-profiles) 사양에 설명되어 있습니다. **Metadata** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 항목 | 0:n | 기술 프로필과 관련된 메타데이터입니다. 기술 프로필 유형마다 다른 메타데이터 항목 집합이 있습니다. 자세한 내용은 기술 프로필 유형 섹션을 참조하세요. |

### <a name="item"></a>항목

**Metadata** 요소의 **Item** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 키 | 예 | 메타데이터 키입니다. 메타데이터 항목 목록은 각 [기술 프로필 유형](#types-of-technical-profiles)을 참조하세요. |

다음 예제에서는 [OAuth2 기술 프로필](oauth2-technical-profile.md#metadata)과 관련된 메타데이터를 사용하는 방법을 보여 줍니다.

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

다음 예제에서는 [REST API 기술 프로필](restful-technical-profile.md#metadata)과 관련된 메타데이터를 사용하는 방법을 보여 줍니다.

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>암호화 키

통합되는 서비스와의 신뢰를 설정하기 위해 Azure AD B2C는 비밀 및 인증서를 [정책 키](policy-keys-overview.md) 형식으로 저장합니다. 기술 프로필을 실행하는 동안 Azure AD B2C는 Azure AD B2C 정책 키에서 암호화 키를 검색합니다. 그런 다음, Azure AD B2C는 키를 사용하여 신뢰를 설정하거나 토큰을 암호화하거나 서명합니다. 이러한 신뢰는 다음으로 구성됩니다.

- [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys) 및 [SAML](identity-provider-generic-saml.md) 공급자와의 페더레이션
- [REST API 서비스](secure-rest-api.md)와의 연결 보호
- [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) 및 [SAML](saml-service-provider.md) 토큰 서명 및 암호화

**CryptographicKeys** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 키 | 1:n | 이 기술 프로필에서 사용되는 암호화 키입니다. |

### <a name="key"></a>키

**Key** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 정책 파일의 다른 요소에서 참조되는 특정 키 쌍의 고유 식별자입니다. |
| StorageReferenceId | 예 | 정책 파일의 다른 요소에서 참조되는 스토리지 키 컨테이너의 식별자입니다. |

## <a name="input-claims-transformations"></a>입력 클레임 변환

**InputClaimsTransformations** 요소에는 입력 클레임을 수정하거나 새 클레임을 생성하는 데 사용되는 입력 클레임 변환 요소 컬렉션이 포함될 수 있습니다.

클레임 변환 컬렉션에서 이전 클레임 변환의 출력 클레임은 후속 입력 클레임 변환의 입력 클레임이 될 수 있습니다. 이러한 방식으로 서로 종속된 클레임 변환의 시퀀스를 사용할 수 있습니다.

**InputClaimsTransformations** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환의 식별자입니다. 클레임 변환을 사용하여 기존 ClaimsSchema 클레임을 수정하거나 새 클레임을 생성할 수 있습니다. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 클레임 변환의 식별자입니다. |

다음 기술 프로필은 **CreateOtherMailsFromEmail** 클레임 변환을 참조합니다. 클레임 변환은 데이터를 디렉터리에 유지하기 전에 `email` 클레임의 값을 `otherMails` 컬렉션에 추가합니다.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 기술 프로필에 사용되는 클레임 모음에서 클레임을 선택합니다. 예를 들어, [자체 어설션 기술 프로필](self-asserted-technical-profile.md)은 입력 클레임을 사용하여 사용자가 제공하는 출력 클레임을 미리 채웁니다. REST API 기술 프로필은 입력 클레임을 사용하여 입력 매개 변수를 REST API 엔드포인트에 보냅니다. Azure AD는 입력 클레임을 고유 식별자로 사용하여 계정을 읽거나, 업데이트하거나, 삭제합니다.

**InputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 필요한 입력 클레임 유형입니다. |

### <a name="inputclaim"></a>InputClaim

**InputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 클레임 유형의 식별자입니다. 클레임은 정책 파일 또는 부모 정책 파일의 클레임 스키마 섹션에 이미 정의되어 있습니다. |
| DefaultValue | 예 | ClaimTypeReferenceId로 표시되는 클레임이 없는 경우 클레임을 만드는 데 사용할 기본값이며, 이에 따른 결과 클레임이 기술 프로필에서 InputClaim 요소로 사용될 수 있습니다. |
| PartnerClaimType | 예 | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. PartnerClaimType 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름이 *givenName* 이고 파트너가 *first_name* 이라는 클레임을 사용하는 경우입니다. |

## <a name="display-claims"></a>표시 클레임

**DisplayClaims** 요소에는 사용자로부터 데이터를 수집하기 위해 화면에 표시되는 클레임 목록이 포함됩니다. 표시 클레임 컬렉션에서는 사용자가 만든 [클레임 유형](claimsschema.md) 또는 [표시 컨트롤](display-controls.md)에 대한 참조를 포함할 수 있습니다.

- 클레임 유형은 화면에 표시할 클레임에 대한 참조입니다.
  - 사용자가 특정 클레임에 대한 값을 입력하도록 강제 지정하려면 **DisplayClaim** 요소의 **Required** 특성을 `true`로 설정합니다.
  - 표시 클레임 값을 미리 채우려면 앞에서 설명한 입력 클레임을 사용합니다. 요소에는 기본값이 포함될 수도 있습니다.
  - Azure AD B2C에서 지원하는 모든 사용자 입력 유형에 대해 **DisplayClaims** 컬렉션의 **ClaimType** 요소는 **UserInputType** 요소로 설정해야 합니다. 예를 들어 `TextBox` 또는 `DropdownSingleSelect`입니다.
- 표시 컨트롤은 특수 기능이 있고 Azure AD B2C 백 엔드 서비스와 상호 작용하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 유효성 검사 기술 프로필을 호출하는 페이지에서 작업을 수행할 수 있습니다. 예를 들어 이메일 주소, 전화 번호 또는 고객 충성도 번호를 확인합니다.

**DisplayClaims** 의 요소 순서는 Azure AD B2C가 화면에서 클레임을 렌더링하는 순서를 지정합니다.

**DisplayClaims** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | 필요한 입력 클레임 유형입니다. |

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DisplayControlReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 [표시 컨트롤](display-controls.md)의 식별자입니다. |
| 필수 | 예 | 표시 클레임이 필요한지 여부를 나타냅니다. |

다음 예제에서는 자체 어설션된 기술 프로필에서 표시 클레임 및 표시 컨트롤을 사용하는 방법을 보여 줍니다.

![표시 클레임이 있는 자체 어설션된 기술 프로필을 보여 주는 스크린샷](./media/technical-profiles/display-claims.png)

다음 기술 프로필에서

- 첫 번째 표시 클레임은 메일 주소를 수집하고 확인하는 `emailVerificationControl` 표시 컨트롤을 참조합니다.
- 다섯 번째 표시 클레임은 전화 번호를 수집하고 확인하는 `phoneVerificationControl` 표시 컨트롤을 참조합니다.
- 다른 표시 클레임은 사용자로부터 수집할 ClaimType 요소입니다.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

## <a name="persisted-claims"></a>지속형 클레임

**PersistedClaims** 요소에는 정책의 [ClaimsSchema](claimsschema.md) 섹션에 이미 정의된 클레임 유형과 Azure AD 특성 이름 간에 가능한 매핑 정보를 사용하여 [Azure AD 기술 프로필](active-directory-technical-profile.md)에서 유지해야 하는 모든 값이 포함됩니다.

Azure AD 특성 이름을 포함하는 **PartnerClaimType** 특성이 지정되지 않은 경우 클레임 이름은 [Azure AD 특성](user-profile-attributes.md) 이름입니다.

**PersistedClaims** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 저장할 클레임 유형입니다. |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DefaultValue | 예 | 클레임이 없는 경우 클레임을 만드는 데 사용할 기본값입니다. |
| PartnerClaimType | 예 | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. PartnerClaimType 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름이 *givenName* 이고 파트너가 *first_name* 이라는 클레임을 사용하는 경우입니다. |

다음 예제에서는 새 로컬 계정을 만드는 **AAD-UserWriteUsingLogonEmail** 기술 프로필 또는 [스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)에서 다음 클레임을 유지합니다.

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 기술 프로필이 완료되면 클레임 모음으로 다시 반환되는 클레임의 컬렉션입니다. 이러한 클레임은 다음 오케스트레이션 단계 또는 출력 클레임 변환에서 사용할 수 있습니다. **OutputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 필요한 출력 클레임 유형입니다. |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DefaultValue | 예 | 클레임이 없는 경우 클레임을 만드는 데 사용할 기본값입니다. |
|AlwaysUseDefaultValue |예 |기본값을 사용하도록 강제합니다. |
| PartnerClaimType | 예 | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. 파트너 클레임 유형 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름이 *givenName* 이고 파트너가 *first_name* 이라는 클레임을 사용하는 경우입니다. |

## <a name="output-claims-transformations"></a>출력 클레임 변환

**OutputClaimsTransformations** 요소에는 **OutputClaimsTransformation** 요소의 컬렉션이 포함될 수 있습니다. 출력 클레임 변환은 출력 클레임을 수정하거나 새 클레임을 생성하는 데 사용됩니다. 실행 후 출력 클레임이 다시 청구 모음에 저장됩니다. 이러한 클레임은 다음 오케스트레이션 단계에서 사용할 수 있습니다.

클레임 변환 컬렉션에서 이전 클레임 변환의 출력 클레임은 후속 입력 클레임 변환의 입력 클레임이 될 수 있습니다. 이러한 방식으로 서로 종속된 클레임 변환의 시퀀스를 사용할 수 있습니다.

**OutputClaimsTransformations** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환의 식별자입니다. 클레임 변환을 사용하여 기존 ClaimsSchema 클레임을 수정하거나 새 클레임을 생성할 수 있습니다. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 클레임 변환의 식별자입니다. |

다음 기술 프로필은 AssertAccountEnabledIsTrue 클레임 변환을 참조하여 디렉터리에서 `accountEnabled` 클레임을 읽은 후 계정이 사용하도록 설정되어 있는지 여부를 평가합니다.

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>유효성 검사 기술 프로필

유효성 검사 기술 프로필은 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md#validation-technical-profiles)에서 출력 클레임의 유효성을 검사하는 데 사용됩니다. 유효성 검사 기술 프로필은 [Azure AD](active-directory-technical-profile.md) 또는 [REST API](restful-technical-profile.md)와 같은 모든 프로토콜의 일반적인 기술 프로필입니다. 유효성 검사 기술 프로필에서 출력 클레임을 반환하거나 오류 코드를 반환합니다. 오류 메시지는 화면에서 사용자에게 렌더링되어 사용자가 다시 시도할 수 있습니다.

다음 다이어그램에서는 Azure AD B2C에서 유효성 검사 기술 프로필을 사용하여 사용자 자격 증명의 유효성을 검사하는 방법을 보여 줍니다.

![유효성 검사 기술 프로필 흐름을 보여 주는 다이어그램](./media/technical-profiles/validation-technical-profile.png)

**ValidationTechnicalProfiles** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 참조 기술 프로필의 일부 또는 모든 출력 클레임 유효성을 검사하는 데 사용되는 기술 프로필의 식별자입니다. 참조된 기술 프로필의 모든 입력 클레임이 참조 기술 프로필의 출력 클레임에 표시되어야 합니다. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** 요소는 [신뢰 당사자 정책](relyingparty.md#subjectnaminginfo)의 토큰에 사용되는 주체 이름을 정의합니다. **SubjectNamingInfo** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimType | 예 | 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |

## <a name="include-technical-profile"></a>기술 프로필 포함

기술 프로필에는 설정을 변경하거나 새 기능을 추가하는 다른 기술 프로필이 포함될 수 있습니다. **IncludeTechnicalProfile** 요소는 기술 프로필이 파생된 일반 기술 프로필에 대한 참조입니다. 정책 요소의 중복성과 복잡성을 줄이려면 핵심 요소를 공유하는 여러 기술 프로필이 있을 때 포함을 사용합니다. 일반 기술 프로필이 포함된 특정 작업 기술 프로필과 함께 일반적인 구성 세트가 있는 일반 기술 프로필을 사용합니다.

여러 시나리오에 대해 서로 다른 클레임 세트를 보내야 하는 단일 엔드포인트가 포함된 [REST API 기술 프로필](restful-technical-profile.md)이 있다고 가정합니다. REST API 엔드포인트 URI, 메타데이터, 인증 유형 및 암호화 키와 같은 공유 기능을 사용하여 일반 기술 프로필을 만듭니다. 일반 기술 프로필이 포함된 특정 작업 기술 프로필을 만듭니다. 그런 다음, 입력 및 출력 클레임을 추가하거나 해당 기술 프로필과 관련된 REST API 엔드포인트 URI를 덮어씁니다.

**IncludeTechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

다음 예제에서는 포함을 사용하는 방법을 보여 줍니다.

- **REST-API-Common**: 기본 구성을 포함한 일반 기술 프로필입니다.
- **REST-ValidateProfile**: **REST-API-Common** 기술 프로필을 포함하고, 입력 및 출력 클레임을 지정합니다.
- **REST-UpdateProfile**: **REST-API-Common** 기술 프로필을 포함하고, 입력 클레임을 지정하며, `ServiceUrl` 메타데이터를 덮어씁니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
      <DisplayName>Update the user profile</DisplayName>  
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multilevel-inclusion"></a>다단계 포함

기술 프로필에는 단일 기술 프로필이 포함될 수 있습니다. 포함 수준의 수는 제한되지 않습니다. 예를 들어 **AAD-UserReadUsingAlternativeSecurityId-NoError** 기술 프로필에는 **AAD-UserReadUsingAlternativeSecurityId** 가 포함됩니다. 이 기술 프로필은 `RaiseErrorIfClaimsPrincipalDoesNotExist` 메타데이터 항목을 `true`로 설정하고, 소셜 계정이 디렉터리에 없는 경우 오류를 발생시킵니다. **AAD-UserReadUsingAlternativeSecurityId-NoError** 는 이 동작을 재정의하고 해당 오류 메시지를 사용하지 않도록 설정합니다.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** 에는 `AAD-Common` 기술 프로필이 포함됩니다.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId-NoError** 및 **AAD-UserReadUsingAlternativeSecurityId** 는 모두 **AAD-Common** 기술 프로필에 지정되어 있으므로 필수 **Protocol** 요소를 지정하지 않습니다.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>세션 관리에 기술 프로필 사용

**UseTechnicalProfileForSessionManagement** 요소는 [SSO 세션 기술 프로필](custom-policy-reference-sso.md)을 참조합니다. **UseTechnicalProfileForSessionManagement** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 참조 | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

## <a name="enabled-for-user-journeys"></a>사용자 경험에 사용

사용자 경험의 [ClaimsProviderSelections](userjourneys.md#claims-provider-selection)는 클레임 공급자 선택 옵션과 해당 순서를 정의합니다. **EnabledForUserJourneys** 요소를 사용하여 사용자가 사용할 수 있는 클레임 공급자를 필터링합니다. **EnabledForUserJourneys** 요소에는 다음 값 중 하나가 포함됩니다.

- **Always**: 기술 프로필을 실행합니다.
- **Never**: 기술 프로필을 건너뜁니다.
- **OnClaimsExistence**: 기술 프로필에 지정된 특정 클레임이 있는 경우에만 실행합니다.
- **OnItemExistenceInStringCollectionClaim**: 문자열 컬렉션 클레임에 항목이 있는 경우에만 실행합니다.
- **OnItemAbsenceInStringCollectionClaim**: 문자열 컬렉션 클레임에 항목이 없는 경우에만 실행합니다.

**OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** 또는 **OnItemAbsenceInStringCollectionClaim** 을 사용하려면 다음 메타데이터를 제공해야 합니다.

- **ClaimTypeOnWhichToEnable**: 평가할 클레임 유형을 지정합니다.
- **ClaimValueOnWhichToEnable**: 비교할 값을 지정합니다.

다음 기술 프로필은 **identityProviders** 문자열 컬렉션에 `facebook.com` 값이 포함된 경우에만 실행됩니다.

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
