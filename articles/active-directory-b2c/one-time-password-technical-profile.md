---
title: OTP(일회용 암호) 확인 사용
titleSuffix: Azure AD B2C
description: Azure AD B2C 사용자 지정 정책을 사용하여 OTP(일회용 암호) 시나리오를 설정하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: c54f5636ee7af8142bf5fcd401a2160a69cf550c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436900"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 일회용 암호 기술 프로필을 정의합니다

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)는 일회용 암호의 생성 및 유효성 검사를 관리하는 기능을 제공합니다. 기술 프로필을 사용하여 코드를 생성한 다음 나중에 해당 코드를 확인합니다.

일회용 암호 기술 프로필은 코드 확인 중에 오류 메시지를 반환할 수도 있습니다. **유효성 검사 기술 프로필** 을 사용하여 일회용 암호 통합을 설계합니다. 유효성 검사 기술 프로필은 일회용 암호 기술 프로필을 호출하여 코드를 확인합니다. 유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 오류 메시지가 자체 어설션된 페이지에 표시됩니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다.

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예제는 일회용 암호의 기술 프로필을 보여줍니다.

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>코드 생성

이 기술 프로필의 첫 번째 모드는 코드 생성입니다. 이 모드에 관해 구성할 수 있는 옵션은 다음과 같습니다. 생성된 코드와 시도는 세션 내에서 추적됩니다. 

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 일회용 암호 프로토콜 공급자에게 보내는 데 필요한 클레임 목록을 포함합니다. 클레임 이름을 아래에 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| identifier | 예 | 코드를 나중에 확인해야 할 사용자를 식별하는 식별자입니다. 일반적으로 이메일 주소 또는 전화번호와 같이 코드가 전달되는 대상의 식별자로 사용됩니다. |

**InputClaimsTransformations** 요소는 일회용 암호 프로토콜 공급자한테 보내기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 일회용 암호 프로토콜 공급자에 의해 생성된 클레임 목록을 포함합니다. 클레임 이름을 아래에 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| otpGenerated | 예 | Azure AD B2C가 세션을 관리하는 생성된 코드입니다. |

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

다음 설정은 코드 생성 모드를 구성하는 데 사용할 수 있습니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | 예 | 코드 만료까지의 시간(초)입니다. 최소: `60`; 최대: `1200`; 기본값: `600`. 코드를 제공할 때마다(새 코드, 또는 `ReuseSameCode`를 사용하는 동일한 코드) 코드 만료가 연장됩니다. 이 시간은 재시도 시간 제한(최대 시도 횟수에 도달하면 사용자가 이 시간이 만료될 때까지 새 코드를 가져오지 못하도록 잠김)을 설정하는 데도 사용됩니다 |
| CodeLength | 예 | 코드의 길이입니다. 기본값은 `6`입니다. |
| CharacterSet | 예 | 정규식에 사용하기 위해 서식이 지정된 코드에 대한 문자 집합입니다. 정의합니다(예: `a-z0-9A-Z`). 기본값은 `0-9`입니다. 문자 집합에는 지정된 집합에 최소 10개의 다른 문자가 포함되어야 합니다. |
| NumRetryAttempts | 예 | 코드가 잘못되었다고 간주되기 전의 확인 시도 횟수입니다. 기본값은 `5`입니다. 예를 들어 NumRetryAttempts를 2로 설정하면 총 2번의 시도만 허용됩니다(첫 번째 + 1회 재시도). 세 번째 시도의 경우 코드가 올바른지 여부에 관계없이 도달한 최대 시도를 throw합니다.|
| NumCodeGenerationAttempts | 예 | 식별자당 최대 코드 생성 시도 횟수입니다. 지정하지 않으면 기본값은 10입니다. |
| 작업 | 예 | 수행할 작업입니다. 가능한 값: `GenerateCode`. |
| ReuseSameCode | 예 | 지정된 코드가 만료되지 않고 여전히 유효한 경우 새 코드를 생성하는 대신 동일한 코드를 제공해야 하는지 여부입니다. 기본값은 `false`입니다.  |



### <a name="example"></a>예제

다음 `TechnicalProfile` 예제는 코드를 생성하는 데 사용됩니다.

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>코드 확인

이 기술 프로필의 두 번째 모드는 코드 확인입니다. 이 모드에 관해 구성할 수 있는 옵션은 다음과 같습니다.

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 일회용 암호 프로토콜 공급자에게 보내는 데 필요한 클레임 목록을 포함합니다. 클레임 이름을 아래에 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| identifier | 예 | 이전에 코드를 생성한 사용자를 식별하는 식별자입니다. 일반적으로 이메일 주소 또는 전화번호와 같이 코드가 전달되는 대상의 식별자로 사용됩니다. |
| otpToVerify | 예 | 사용자가 제공한 확인 코드입니다. |

**InputClaimsTransformations** 요소는 일회용 암호 프로토콜 공급자한테 보내기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

이 프로토콜 공급자의 코드를 확인하는 동안 제공된 출력 클레임이 없습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

다음 설정을 사용하여 코드 확인 모드를 설정할 수 있습니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 작업 | 예 | 수행할 작업입니다. 가능한 값: `VerifyCode`. |


### <a name="ui-elements"></a>UI 요소

코드 확인 실패 시 표시되는 오류 메시지를 구성하는 데 다음 메타데이터를 사용할 수 있습니다. 메타데이터는 [자체 어설션](self-asserted-technical-profile.md) 기술 프로필에서 구성해야 합니다. 오류 메시지는 [지역화](localization-string-ids.md#one-time-password-error-messages)될 수 있습니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | 예 | 코드 확인 세션이 만료된 경우 사용자에게 표시할 메시지입니다. 코드가 만료되었거나 지정된 식별자에 대한 코드가 생성되지 않았습니다. |
| UserMessageIfMaxRetryAttempted | 예 | 허용되는 최대 확인 시도 횟수를 초과하는 경우 사용자에게 표시할 메시지입니다. |
| UserMessageIfMaxNumberOfCodeGenerated | 예 | 코드 생성이 허용되는 최대 횟수를 초과하는 경우 사용자에게 표시할 메시지입니다. |
| UserMessageIfInvalidCode | 예 | 잘못된 코드를 제공한 경우 사용자에게 표시할 메시지입니다. |
| UserMessageIfVerificationFailedRetryAllowed | 예 | 사용자가 잘못된 코드를 제공하였으며, 사용자가 올바른 코드를 제공할 수 있는 경우 사용자에게 표시할 메시지입니다.  |
|UserMessageIfSessionConflict|예| 코드를 확인할 수 없는 경우 사용자에게 표시할 메시지입니다.|

### <a name="example"></a>예제

다음 `TechnicalProfile` 예제는 코드를 확인하는 데 사용됩니다.

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>다음 단계

사용자 지정 이메일 확인과 함께 일회용 암호 기술 프로필을 사용하는 예는 다음 문서를 참조하세요.

- Azure Active Directory B2C에서 사용자 지정 이메일 확인([Mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))

