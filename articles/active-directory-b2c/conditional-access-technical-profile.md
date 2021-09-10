---
title: 사용자 지정 정책에서 조건부 액세스 기술 프로필
titleSuffix: Azure AD B2C
description: Azure AD B2C의 조건부 액세스 기술 프로필에 대한 사용자 지정 정책 참조입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/18/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 902d9251c7393d3f6e693ba37c587398136493e6
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112368951"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C의 사용자 지정 정책에서 조건부 액세스 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD(Azure Active Directory) 조건부 액세스는 Azure AD B2C에서 신호를 함께 가져오고, 결정을 내리고, 조직 정책을 적용하기 위해 사용하는 도구입니다. 정책 조건을 사용하여 위험 평가를 자동화하면 위험한 로그인을 한 번에 식별하여 수정하거나 차단할 수 있습니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다.

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예는 조건부 액세스 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>조건부 액세스 평가

Azure AD B2C는 모든 로그인에 대해 모든 정책을 평가하고 사용자 액세스 권한을 부여하기 전에 모든 요구 사항을 충족하는지 확인합니다. “액세스 차단”은 다른 모든 구성 설정보다 우선합니다. 조건부 액세스 기술 프로필의 **평가** 모드는 로컬 계정으로 로그인하는 동안 Azure AD B2C에 의해 수집된 신호를 평가합니다. 조건부 액세스 기술 프로필의 결과는 조건부 액세스 평가의 결과인 클레임 집합입니다. Azure AD B2C 정책은 다음 오케스트레이션 단계에서 이러한 클레임을 사용하여 사용자를 차단하거나 다단계 인증을 사용하여 사용자를 도전하는 등의 작업을 수행합니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| OperationType | 예 | **Evaluation** 이어야 합니다.  |

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 조건부 액세스로 보낼 클레임 목록을 포함합니다. 클레임 이름을 조건부 액세스 기술 프로필에서 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | 데이터 형식 | 설명 |
| --------- | -------- | ----------- |----------- |
| UserId | 예 | 문자열 | 로그인한 사용자의 식별자입니다. |
| AuthenticationMethodsUsed | 예 |stringCollection | 사용자가 로그인하는 데 사용한 방법의 목록입니다. 가능한 값: `Password` 및 `OneTimePasscode` |
| IsFederated | 예 |boolean | 사용자가 페더레이션된 계정으로 로그인했는지 여부를 나타냅니다. 값은 `false`여야 합니다. |
| IsMfaRegistered | 예 |boolean | 사용자가 다단계 인증에 이미 전화번호를 등록했는지 여부를 나타냅니다. |


**InputClaimsTransformations** 요소는 조건부 액세스 서비스로 보내기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 ConditionalAccessProtocolProvider에 의해 생성된 클레임 목록을 포함합니다. 클레임 이름을 아래에 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | 데이터 형식 | 설명 |
| --------- | -------- | ----------- |----------- |
| 과제 | 예 |stringCollection | 식별된 위협을 수정하는 작업 목록입니다. 가능한 값: `block`, `mfa` 및 `chg_pwd`. |
| MultiConditionalAccessStatus | 예 | stringCollection | 조건부 액세스 평가 상태입니다.  |

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="example-evaluation"></a>예: 평가

다음 예에서는 로그인 위협을 평가하는 데 사용되는 조건부 액세스 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>수정

조건부 액세스 기술 프로필의 **수정** 모드는 로그인 시 식별된 위협이 수정되었음을 Azure AD B2C에게 알립니다. 다음 옵션은 수정 모드에 대해 구성할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| OperationType | 예 | **Remediation** 이어야 합니다.  |

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 조건부 액세스로 보낼 클레임 목록을 포함합니다. 클레임 이름을 조건부 액세스 기술 프로필에서 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | 데이터 형식 | 설명 |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | 예 | stringCollection| 평가 모드의 도전 클레임에서 반환되는 것으로 확인된 위협을 수정하는 과정에서 충족된 도전의 목록입니다.|


**InputClaimsTransformations** 요소는 조건부 액세스 서비스를 호출하기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

조건부 액세스 프로토콜 공급자는 **OutputClaims** 를 반환하지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 `DefaultValue` 특성만 설정하면 조건부 액세스 프로토콜 공급자가 반환하지 않은 클레임도 포함할 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="example-remediation"></a>예: 수정

다음 예에서는 로그인 위협을 수정하는 데 사용되는 조건부 액세스 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2C에서 사용자 흐름에 조건부 액세스를 추가](conditional-access-user-flow.md)하는 방법을 알아봅니다.
