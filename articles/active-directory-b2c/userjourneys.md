---
title: UserJourneys
description: Azure Active Directory B2C에서 사용자 지정 정책의 UserJourneys 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/31/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d297f2c9e32de22beba8c8bf63d4b62e73e8690c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131057498"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자 경험은 명시적 경로를 지정합니다. 이 경로를 통해 정책은 신뢰 당사자 애플리케이션이 사용자에 대해 원하는 클레임을 가져오도록 허용합니다. 이러한 경로를 통해 사용자를 가져와서 신뢰 당사자에게 표시할 클레임을 검색합니다. 즉, 사용자 경험은 Azure AD B2C ID 경험 프레임워크가 요청을 처리할 때 최종 사용자에게 적용되는 비즈니스 논리를 정의합니다.

이 사용자 경험은 관심 커뮤니티의 다양한 신뢰 당사자 요구를 충족하는 데 사용할 수 있는 템플릿으로 간주할 수 있습니다. 사용자 경험을 활용하면 정책의 신뢰 당사자 부분을 원활하게 정의할 수 있습니다. 정책 하나가 여러 사용자 경험을 정의할 수 있습니다. 각 사용자 경험은 오케스트레이션 단계 시퀀스입니다.

정책에서 지원하는 사용자 경험을 정의하기 위해 **UserJourneys** 요소가 정책 파일의 최상위 요소 아래에 추가됩니다.

**UserJourneys** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | 전체 사용자 흐름에 필요한 모든 구문을 정의하는 사용자 경험입니다. |

**UserJourney** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 정책의 다른 요소에서 사용자 경험을 참조하는 데 사용할 수 있는 사용자 경험의 식별자입니다. [신뢰 당사자 정책](relyingparty.md)의 **DefaultUserJourney** 요소는 이 특성을 가리킵니다. |
| DefaultCpimIssuerTechnicalProfileReferenceId| No | 기본 토큰 발급자 기술 프로필 참조 ID입니다. 예를 들어, [JWT 토큰 발급자](userjourneys.md), [SAML 토큰 발급자](saml-issuer-technical-profile.md) 또는 [OAuth2 사용자 지정 오류](oauth2-error-technical-profile.md)입니다. 사용자 경험 또는 하위 경험에 이미 다른 `SendClaims` 오케스트레이션 단계가 있는 경우 `DefaultCpimIssuerTechnicalProfileReferenceId` 특성을 사용자 경험의 토큰 발급자 기술 프로필로 설정합니다. |

**UserJourney** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | 권한 부여 기술 프로필 목록입니다. | 
| OrchestrationSteps | 1:n | 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스입니다. 모든 사용자 경험은 순서대로 실행되는 정렬된 오케스트레이션 단계 목록으로 구성됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

사용자가 UserJourney를 완료하고 액세스 또는 ID 토큰을 획득했다고 가정합니다. [UserInfo 엔드포인트](userinfo-endpoint.md) 같은 추가 리소스를 관리하려면 사용자가 식별되어야 합니다. 이 프로세스를 시작하려면 사용자는 유효한 Azure AD B2C 정책을 통해 원래 인증되었다는 증명으로 이전에 발급된 액세스 토큰을 제공해야 합니다. 사용자가 이 요청을 수행할 수 있게 하려면 이 프로세스 중에 항상 사용자에 대해 유효한 토큰이 있어야 합니다. 권한 부여 기술 프로필은 들어오는 토큰의 유효성을 검사하고 토큰에서 클레임을 추출합니다.

**AuthorizationTechnicalProfiles** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | 권한 부여 기술 프로필 목록입니다. | 

**AuthorizationTechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Yes | 실행할 기술 프로필의 식별자입니다. |

다음 예제에서는 권한 부여 기술 프로필이 있는 사용자 경험 요소를 보여 줍니다.

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. 이러한 오케스트레이션 단계는 정책 파일에서 허용되는 클레임 공급자와 구성 요소를 모두 참조합니다. 사용자 경험을 표시하거나 렌더링해야 하는 모든 오케스트레이션 단계에는 해당하는 콘텐츠 정의 식별자에 대한 참조도 포함됩니다.

오케스트레이션 단계 요소에 정의된 사전 조건을 기준으로 오케스트레이션 단계를 조건부로 실행할 수 있습니다. 예를 들어, 특정 클레임이 있는 경우 또는 클레임이 지정한 값과 같거나 다른 경우에만 오케스트레이션 단계를 수행하도록 선택할 수 있습니다.

오케스트레이션 단계의 정렬된 목록을 지정하기 위해 **OrchestrationSteps** 요소가 정책의 일부로 추가됩니다. 이 요소는 필수 요소입니다.

**OrchestrationSteps** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | 정렬된 오케스트레이션 단계입니다. |

**OrchestrationStep** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Order` | 예 | 오케스트레이션 단계의 순서입니다. |
| `Type` | Yes | 오케스트레이션 단계의 유형입니다. 가능한 값은 다음과 같습니다. <ul><li>**ClaimsProviderSelection** - 오케스트레이션 단계에서 사용자가 하나를 선택할 수 있도록 여러 클레임 공급자가 표시됨을 나타냅니다.</li><li>**CombinedSignInAndSignUp** - 오케스트레이션 단계에서 소셜 공급자 로그인 및 로컬 계정 등록 페이지가 결합된 형태로 표시됨을 나타냅니다.</li><li>**ClaimsExchange** - 오케스트레이션 단계에서 클레임 공급자와 클레임을 교환함을 나타냅니다.</li><li>**GetClaims** - 오케스트레이션 단계가 `InputClaims` 구성을 통해 신뢰 당사자에서 Azure AD B2C로 전송된 클레임 데이터를 처리하도록 지정합니다.</li><li>**InvokeSubJourney** - 오케스트레이션 단계가 [하위 경험](subjourneys.md)과 클레임을 교환하도록 지정합니다(퍼블릭 미리 보기).</li><li>**SendClaims** - 오케스트레이션 단계에서 클레임 발급자가 발급한 토큰과 함께 클레임을 신뢰 당사자에게 전송함을 나타냅니다.</li></ul> |
| ContentDefinitionReferenceId | No | 이 오케스트레이션 단계와 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. 일반적으로 콘텐츠 정의 참조 식별자는 자체 어설션된 기술 프로필에서 정의됩니다. 그러나 Azure AD B2C가 기술 프로필을 사용하지 않고 콘텐츠를 표시해야 하는 경우도 있습니다. 두 가지 예제가 있습니다. 오케스트레이션 단계의 유형이 `ClaimsProviderSelection` 또는 `CombinedSignInAndSignUp` 중 하나인 경우 Azure AD B2C는 기술 프로필 없이 ID 공급자 선택을 표시해야 합니다. |
| CpimIssuerTechnicalProfileReferenceId | No | 오케스트레이션 단계의 유형이 `SendClaims`입니다. 이 속성은 신뢰 당사자용 토큰을 발급하는 클레임 공급자의 기술 프로필 식별자를 정의합니다.  해당 식별자가 없으면 신뢰 당사자 토큰이 생성되지 않습니다. |

**OrchestrationStep** 요소에는 다음과 같은 요소가 포함될 수 있습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Preconditions | 0:n | 오케스트레이션 단계를 실행하려면 충족해야 하는 전제 조건 목록입니다. |
| ClaimsProviderSelections | 0:n | 오케스트레이션 단계의 클레임 공급자 선택 항목 목록입니다. |
| ClaimsExchanges | 0:n | 오케스트레이션 단계의 클레임 교환 목록입니다. |
| JourneyList | 0:1 | 오케스트레이션 단계의 하위 경험 후보 목록입니다. |

### <a name="preconditions"></a>Preconditions

오케스트레이션 단계에 정의된 전제 조건에 따라 오케스트레이션 단계를 조건부로 실행하면 됩니다. `Preconditions` 요소에는 평가할 사전 조건 목록이 포함됩니다. 사전 조건 평가가 충족되면 연결된 오케스트레이션 단계는 다음 오케스트레이션 단계로 건너뜁니다. 

Azure AD B2C는 사전 조건을 목록 순서대로 평가합니다. 순서 기반 사전 조건을 사용하여 사전 조건이 적용되는 순서를 설정할 수 있습니다. 충족된 첫 번째 사전 조건은 모든 후속 사전 조건을 재정의합니다. 오케스트레이션 단계는 일부 사전 조건이 충족되지 않은 경우에만 실행됩니다. 

**Preconditions** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Precondition | 1:n | 평가할 사전 조건입니다. |

#### <a name="precondition"></a>Precondition

**Precondition** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Type` | 예 | 이 전제 조건에 대해 수행할 확인이나 쿼리의 유형입니다. 값은 **ClaimsExist**(지정한 클레임이 사용자의 현재 클레임 집합에 있으면 작업을 수행해야 함을 지정함) 또는 **ClaimEquals**(지정한 클레임이 있으며 해당 값이 지정된 값과 같으면 작업을 수행해야 함을 지정함)일 수 있습니다. |
| `ExecuteActionsIf` | Yes | 사전 조건이 충족되는 것으로 간주하는 방법을 결정합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. 값이 `true`로 설정된 경우 클레임이 사전 조건과 일치할 때 충족되는 것으로 간주합니다.  값이 `false`로 설정된 경우 클레임이 사전 조건과 일치하지 않을 때 충족되는 것으로 간주합니다.  |

**Precondition** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 값 | 1:2 | 클레임 유형의 식별자입니다. 클레임은 정책 파일 또는 부모 정책 파일의 클레임 스키마 섹션에 이미 정의되어 있습니다. 사전 조건이 `ClaimEquals` 유형인 경우 두 번째 `Value` 요소에는 확인할 값이 포함됩니다. |
| 작업 | 1:1 | 사전 조건 평가가 충족되는 경우 수행해야 하는 작업입니다. 가능한 값: `SkipThisOrchestrationStep`. 연결된 오케스트레이션 단계는 다음 단계로 건너뜁니다. |
  
각 사전 조건은 단일 클레임을 평가합니다. 다음과 같은 두 가지 유형의 사전 조건이 있습니다.
 
- **ClaimsExist** - 지정된 클레임이 사용자의 현재 클레임 모음에 있는 경우 작업을 수행하도록 지정합니다.
- **ClaimEquals** - 지정된 클레임이 있고 해당 값이 지정된 값과 같은 경우 작업을 수행하도록 지정합니다. 검사는 대/소문자 구분 서수 비교를 수행합니다. 부울 클레임 유형을 확인하는 경우 `True` 또는 `False`를 사용합니다. 

    클레임이 null이거나 초기화되지 않은 경우 `ExecuteActionsIf`가 `true`인지 또는 `false`인지 관계없이 사전 조건이 무시됩니다. 모범 사례로 클레임이 존재하고 값과 같은지 확인하는 것이 좋습니다.

예를 들어 사용자가 `MfaPreference`를 `Phone`으로 설정한 경우 사용자에게 MFA 챌린지를 확인합니다. 이 조건부 논리를 수행하려면 `MfaPreference` 클레임이 있는지 확인하고 클레임 값이 `Phone`과 같은지 확인합니다. 다음 XML에서는 사전 조건을 사용하여 이 논리를 구현하는 방법을 보여 줍니다. 
  
```xml
<Preconditions>
  <!-- Skip this orchestration step if MfaPreference doesn't exist. -->
  <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
  <!-- Skip this orchestration step if MfaPreference doesn't equal to Phone. -->
  <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
    <Value>MfaPreference</Value>
    <Value>Phone</Value>
    <Action>SkipThisOrchestrationStep</Action>
  </Precondition>
</Preconditions>
```

#### <a name="preconditions-examples"></a>Preconditions 예제

다음 Preconditions는 사용자의 objectId가 있는지를 확인합니다. 사용자 경험에서 사용자는 로컬 계정을 사용하여 로그인하도록 선택했습니다. objectId가 있으면 이 오케스트레이션 단계를 건너뜁니다.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

다음 Preconditions는 사용자가 소셜 계정을 사용하여 로그인했는지를 확인합니다. 이를 위해 디렉터리에서 사용자 계정 찾기를 시도합니다. 사용자가 로컬 계정을 사용하여 로그인하거나 등록하는 경우에는 이 오케스트레이션 단계를 건너뜁니다.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Preconditions는 여러 전제 조건을 확인할 수 있습니다. 다음 예제에서는 'objectId' 또는 'email'이 있는지 여부를 확인합니다. 첫 번째 조건이 true이면 경험은 다음 오케스트레이션 단계로 건너뜁니다.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>클레임 공급자 선택

ID 공급자 선택을 통해 사용자는 옵션 목록에서 작업을 선택할 수 있습니다. ID 공급자 선택은 두 개의 오케스트레이션 단계 쌍으로 구성됩니다. 

1. **단추** - 사용자가 선택할 수 있는 옵션 목록을 포함하는 `ClaimsProviderSelection` 또는 `CombinedSignInAndSignUp` 유형으로 시작합니다. `ClaimsProviderSelections` 요소 내의 옵션 순서는 사용자에게 제공된 단추의 순서를 제어합니다.
2. **작업** - 뒤에 `ClaimsExchange` 유형이 나옵니다. ClaimsExchange에는 작업 목록이 포함됩니다. 작업은 [OAuth2](oauth2-technical-profile.md), [OpenID Connect](openid-connect-technical-profile.md), [클레임 변환](claims-transformation-technical-profile.md) 또는 [자체 어설션](self-asserted-technical-profile.md)과 같은 기술 프로필에 대한 참조입니다. 사용자가 단추 중 하나를 클릭하면 해당 작업이 실행됩니다.

**ClaimsProviderSelections** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1:n | 선택할 수 있는 클레임 공급자의 목록을 제공합니다.|

**ClaimsProviderSelections** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| DisplayOption| No | 단일 클레임 공급자 선택을 사용할 수 있는 경우의 동작을 제어합니다. 가능한 값: `DoNotShowSingleProvider`(기본값), 사용자가 페더레이션 ID 공급자로 즉시 리디렉션됩니다. 또는 `ShowSingleProvider` Azure AD B2C가 단일 ID 공급자 선택이 있는 로그인 페이지를 제공합니다. 이 특성을 사용하려면 [콘텐츠 정의 버전](page-layout.md)이 `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` 이상이어야 합니다.|

**ClaimsProviderSelection** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | No | 클레임 공급자 선택 항목의 다음 오케스트레이션 단계에서 실행되는 클레임 교환의 식별자입니다. 이 특성 또는 ValidationClaimsExchangeId 특성 중 하나만 지정해야 하며 둘 다 지정해서는 안 됩니다. |
| ValidationClaimsExchangeId | No | 클레임 공급자 선택 항목의 유효성을 검사하기 위해 현재 오케스트레이션 단계에서 실행되는 클레임 교환의 식별자입니다. 이 특성 또는 TargetClaimsExchangeId 특성 중 하나만 지정해야 하며 둘 다 지정해서는 안 됩니다. |

### <a name="claims-provider-selection-example"></a>클레임 공급자 선택 예제

다음 오케스트레이션 단계에서 사용자는 Facebook, LinkedIn, Twitter, Google 또는 로컬 계정으로 로그인하도록 선택할 수 있습니다. 사용자가 소셜 ID 공급자 중 하나를 선택하면 `TargetClaimsExchangeId` 특성에 지정된 선택한 클레임 교환을 사용하여 두 번째 오케스트레이션 단계가 실행됩니다. 두 번째 오케스트레이션 단계에서는 로그인 프로세스를 완료할 수 있도록 사용자를 소셜 ID 공급자로 리디렉션합니다. 사용자가 로컬 계정을 사용하여 로그인하도록 선택하는 경우 Azure AD B2C는 같은 오케스트레이션 단계(같은 등록 또는 로그인 페이지)로 유지되며 두 번째 오케스트레이션 단계는 건너뜁니다.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | 사용 중인 기술 프로필별로 선택한 ClaimsProviderSelection에 따라 클라이언트를 리디렉션하거나 교환 클레임에 대한 서버 호출을 수행합니다. |

**ClaimsExchange** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 클레임 교환 단계의 식별자입니다. 식별자는 정책의 클레임 공급자 선택 단계에서 클레임 교환을 참조하는 데 사용됩니다. |
| TechnicalProfileReferenceId | Yes | 실행할 기술 프로필의 식별자입니다. |

## <a name="journeylist"></a>JourneyList

**JourneyList** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Candidate(후보) | 1:1 | 호출할 하위 경험에 대한 참조입니다. |

### <a name="candidate"></a>Candidate(후보)

**Candidate** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Yes | 실행할 [하위 경험](subjourneys.md)의 식별자입니다. |
