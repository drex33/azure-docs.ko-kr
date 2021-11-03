---
title: 사용자 지정 정책의 Azure AD MFA 기술 정책
titleSuffix: Azure AD B2C
description: Azure AD B2C의 Azure AD 다단계 인증(MFA) 기술 프로필에 대한 사용자 지정 정책 참조입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 3570804af6dd59027907afa03e8ee432f1ed718f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131044896"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 Azure AD MFA 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)는 Azure AD MFA(Multi-Factor Authentication)를 사용하여 전화 번호를 확인하기 위한 지원을 제공합니다. 이 기술 프로필을 사용하여 코드를 생성하여 전화 번호로 보낸 다음 코드를 확인합니다. Azure AD MFA 기술 프로필도 오류 메시지를 반환할 수 있습니다.  유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 오류 메시지가 자체 어설션된 페이지에 표시됩니다.

이 기술 프로필은 다음과 같은 특징이 있습니다.

- 사용자와 상호 작용하기 위한 인터페이스를 제공하지 않습니다. 대신 사용자 인터페이스가 [자체 표명된](self-asserted-technical-profile.md) 기술 프로필에서 호출되거나 [디스플레이 컨트롤](display-controls.md) 이 [유효성 검사 기술 프로필](validation-technical-profile.md)로 호출됩니다.
- Azure AD MFA 서비스를 사용하여 코드를 생성하고 전화번호로 보낸 다음 코드를 확인합니다.  
- 문자 메시지를 통해 전화번호의 유효성을 검사합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함해야 합니다.

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예제는 Azure AD MFA 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS 보내기

이 기술 프로필의 첫 번째 모드는 코드를 생성하고 보내는 것입니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 Azure AD MFA로 보낼 클레임 목록을 포함합니다. 클레임 이름을 MFA 기술 프로필에서 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | 설명 |
| --------- | -------- | ----------- |
| userPrincipalName | 예 | 전화번호를 소유하는 사용자의 식별자입니다. |
| phoneNumber | 예 | SMS 코드를 보낼 전화번호입니다. |
| companyName | 예 |SMS의 회사 이름입니다. 지정하지 않으면 응용 프로그램의 이름이 사용됩니다. |
| locale | 예 | SMS의 로캘입니다. 입력되지 않으면 사용자의 브라우저 로캘이 사용됩니다. |

**InputClaimsTransformations** 요소는 Azure AD MFA 서비스로 보내기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure AD MFA 프로토콜 공급자는 **OutputClaims** 를 반환하지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 `DefaultValue` 특성만 설정하면 Azure AD MFA ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 작업 | 예 | 반드시 **OneWaySMS** 여야 합니다.  |

#### <a name="ui-elements"></a>UI 요소

다음 메타데이터는 SMS 전송 실패 시 표시되는 오류 메시지를 구성하는 데 사용할 수 있습니다. 메타데이터는 [자체 어설션](self-asserted-technical-profile.md) 기술 프로필에서 구성해야 합니다. 오류 메시지는 [지역화](localization-string-ids.md#azure-ad-mfa-error-messages)될 수 있습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | 예 | 제공된 전화번호가 SMS를 수락하지 않는 경우의 사용자 오류 메시지입니다. |
| UserMessageIfInvalidFormat | 예 | 제공된 전화번호가 유효한 번호가 아닌 경우의 사용자 오류 메시지입니다. |
| UserMessageIfServerError | 예 | 서버에 내부 오류가 발생한 경우의 사용자 오류 메시지입니다. |
| UserMessageIfThrottled| 예 | 요청이 제한된 경우의 사용자 오류 메시지입니다.|

### <a name="example-send-an-sms"></a>예: SMS 보내기

다음 예제에서는 SMS를 통해 코드를 보내는 데 사용 되는 Azure AD MFA 기술 프로필을 보여줍니다.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>코드 확인

이 기술 프로필의 두 번째 모드는 코드를 확인하는 것입니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 Azure AD MFA로 보낼 클레임 목록을 포함합니다. 클레임 이름을 MFA 기술 프로필에서 정의된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | 설명 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| 예 | 이전에 코드를 전송하는 데 사용한 것과 동일한 전화번호입니다. 전화 확인 세션을 찾는 데도 사용 됩니다. |
| verificationCode  | 예 | 확인을 위해 사용자가 제공한 확인 코드입니다. |

**InputClaimsTransformations** 요소는 Azure AD MFA 서비스를 호출하기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure AD MFA 프로토콜 공급자는 **OutputClaims** 를 반환하지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 `DefaultValue` 특성만 설정하면 Azure AD MFA ID 공급자가 반환하지 않은 클레임도 포함할 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| 작업 | 예 | 반드시 **확인** 해야 합니다 |

#### <a name="ui-elements"></a>UI 요소

코드 확인 실패 시 표시되는 오류 메시지를 구성하는 데 다음 메타데이터를 사용할 수 있습니다. 메타데이터는 [자체 어설션](self-asserted-technical-profile.md) 기술 프로필에서 구성해야 합니다. 오류 메시지는 [지역화](localization-string-ids.md#azure-ad-mfa-error-messages)될 수 있습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| 예 | 사용자가 확인 코드를 너무 많이 시도하는 경우의 사용자 오류 메시지입니다. |
| UserMessageIfServerError | 예 | 서버에 내부 오류가 발생한 경우의 사용자 오류 메시지입니다. |
| UserMessageIfThrottled| 예 | 요청을 제한하는 경우의 사용자 오류 메시지입니다.|
| UserMessageIfWrongCodeEntered| 예| 확인을 위해 입력한 코드가 잘못된 경우의 사용자 오류 메시지입니다.|

### <a name="example-verify-a-code"></a>예제: 코드 확인

다음 예제에서는 코드를 확인하는 데 사용되는 Azure AD MFA 기술 프로필을 보여줍니다.

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
