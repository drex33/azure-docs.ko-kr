---
title: 컨트롤 참조 표시
titleSuffix: Azure AD B2C
description: Azure AD B2C 표시 컨트롤에 대한 참조입니다. 표시 컨트롤을 사용하여 사용자 지정 정책에 정의된 사용자 경험을 사용자 지정합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4b6ea85cfaf32ab9ba2c04c40befbe1fbf8ce915
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445474"
---
# <a name="display-controls"></a>표시 컨트롤

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**표시 컨트롤** 은 특수 기능이 있고 Azure AD B2C(Azure Active Directory B2C) 백 엔드 서비스와 상호 작용하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 [유효성 검사 기술 프로필](validation-technical-profile.md)을 호출하는 페이지에서 작업을 수행할 수 있습니다. 표시 컨트롤은 페이지에 표시되며 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)에서 참조됩니다.

다음 이미지는 기본 및 보조 메일 주소의 유효성을 검사하는 두 개의 표시 컨트롤이 있는 자체 어설션된 등록 페이지를 보여 줍니다.

![렌더링된 표시 컨트롤 예제](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>필수 구성 요소

 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)의 [메타데이터](self-asserted-technical-profile.md#metadata) 섹션에서 참조된 [ContentDefinition](contentdefinitions.md)의 `DataUri`는 페이지 계약 버전 2.0.0 이상으로 설정되어야 합니다. 예를 들면 다음과 같습니다.

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>표시 컨트롤 정의

**DisplayControl** 요소는 다음 특성을 포함합니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 표시 컨트롤에 사용되는 식별자입니다. [참조](#referencing-display-controls)될 수 있습니다. |
| UserInterfaceControlType | 예 | 표시 컨트롤의 형식입니다. 현재 지원되는 형식은 [VerificationControl](display-control-verification.md)입니다. |

**DisplayControl** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **Inputclaims** 는 사용자로부터 수집할 클레임 값을 미리 채우는 데 사용됩니다. 자세한 내용은 [InputClaims](technicalprofiles.md#input-claims) 요소를 참조하세요. |
| DisplayClaims | 0:1 | **DisplayClaims** 는 사용자로부터 수집할 클레임을 나타내는 데 사용됩니다. 자세한 내용은 [DisplayClaim](technicalprofiles.md#displayclaim) 요소를 참조하세요.|
| OutputClaims | 0:1 | **OutputClaims** 는 이 **DisplayControl** 에 대해 일시적으로 저장되는 클레임을 나타내는 데 사용됩니다. 자세한 내용은 [OutputClaims](technicalprofiles.md#output-claims) 요소를 참조하세요.|
| 동작 | 0:1 | **Actions** 는 프런트 엔드에서 발생하는 사용자 작업에 대해 호출할 유효성 검사 기술 프로필을 나열하는 데 사용됩니다. |

### <a name="input-claims"></a>입력 클레임

표시 컨트롤에서 **InputClaims** 요소를 사용하여 페이지의 사용자로부터 수집할 클레임 값을 미리 채울 수 있습니다. 이 표시 컨트롤을 참조하는 자체 어설션된 기술 프로필에 **InputClaimsTransformations** 를 정의할 수 있습니다.

다음 예제에서는 주소가 이미 있는 것으로 확인되도록 메일 주소를 미리 채웁니다.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>표시 클레임

표시 컨트롤의 각 형식에는 다른 표시 클레임, [출력 클레임](#output-claims) 및 수행할 [작업](#display-control-actions) 세트가 필요합니다.

[자체 어설션된 기술 프로필](self-asserted-technical-profile.md#display-claims)에 정의된 **표시 클레임** 과 유사하게, 표시 클레임은 표시 컨트롤 내에서 사용자로부터 수집할 클레임을 나타냅니다. 참조된 **ClaimType** 요소는 `TextBox` 또는 `DropdownSingleSelect`와 같이 Azure AD B2C에서 지원하는 모든 사용자 입력 유형에 대해 **UserInputType** 요소를 지정해야 합니다. **Action** 에 표시 클레임 값이 필요한 경우에는 **Required** 특성을 `true`로 설정하여 강제로 사용자가 특정 표시 클레임 값을 제공하도록 합니다.

특정 형식의 표시 컨트롤에 대해 특정 표시 클레임이 필요합니다. 예를 들어, **VerificationControl** 은 **VerificationCode** 형식의 표시 컨트롤에 필요합니다. 특성 **ControlClaimType** 을 사용하여 해당 필수 클레임에 대해 지정된 DisplayClaim을 지정합니다. 예를 들면 다음과 같습니다.

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>출력 클레임

표시 컨트롤의 **출력 클레임** 은 다음 오케스트레이션 단계로 전송되지 않습니다. 현재 표시 컨트롤 세션에 대해서만 일시적으로 저장됩니다. 이러한 임시 클레임은 동일한 표시 컨트롤의 서로 다른 작업 간에 공유할 수 있습니다.

출력 클레임을 다음 오케스트레이션 단계로 버블링하려면 이 표시 컨트롤을 참조하는 자체 어설션된 실제 기술 프로필의 **OutputClaims** 를 사용합니다.

### <a name="display-control-actions"></a>표시 컨트롤 작업

표시 컨트롤의 **작업** 은 사용자가 클라이언트 쪽(브라우저)에서 특정 작업을 수행할 때 Azure AD B2C 백 엔드에서 발생하는 프로시저입니다. 사용자가 페이지에서 단추를 선택할 때 수행할 유효성 검사를 예로 들 수 있습니다.

작업은 **유효성 검사 기술 프로필** 의 목록을 정의합니다. 표시 컨트롤의 일부 또는 전체가 유효한지 검사하는 데 사용됩니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사하며, 사용자에게 오류를 반환할 수 있습니다. 자체 어설션된 기술 프로필에서는 [유효성 검사 기술 프로필](validation-technical-profile.md)에서 사용되는 것과 유사한 표시 컨트롤 작업에서 **ContinueOnError**, **ContinueOnSuccess** 및 **Preconditions** 를 사용할 수 있습니다.

#### <a name="actions"></a>동작

**Actions** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 작업 | 1:n | 실행할 작업의 목록입니다. |

#### <a name="action"></a>작업

**Action** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 작업의 유형입니다. 가능한 값은 `SendCode` 또는 `VerifyCode`입니다. `SendCode` 값은 사용자에게 코드를 보냅니다. 이 작업에는 코드를 생성하는 프로필과 전송하는 프로필의 두 가지 유효성 검사 기술 프로필이 포함될 수 있습니다. `VerifyCode` 값은 사용자가 입력 텍스트 상자에 입력한 코드를 확인합니다. |

**Action** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | 참조 기술 프로필의 일부 또는 모든 표시 클레임 유효성을 검사하는 데 사용되는 기술 프로필의 식별자입니다. 참조된 기술 프로필의 모든 입력 클레임이 참조 기술 프로필의 표시 클레임에 표시되어야 합니다. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

**ValidationClaimsExchange** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| ValidationClaimsExchangeTechnicalProfile | 1:n | 참조하는 기술 프로필의 표시 클레임 일부 또는 전부의 유효성을 검사하는 데 사용되는 기술 프로필입니다. |

**ValidationClaimsExchangeTechnicalProfile** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | 예 | 정책 또는 부모 정책에 이미 정의된 기술 프로필의 식별자입니다. |

**ValidationClaimsExchangeTechnicalProfile** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | 유효성 검사 기술 프로필을 실행하기 위해 충족해야 하는 사전 조건의 목록입니다. |

**Precondition** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| `Type` | 예 | 사전 조건에 대해 수행할 검사 또는 쿼리의 유형입니다. 가능한 값은 `ClaimsExist` 또는 `ClaimEquals`입니다. `ClaimsExist`는 지정된 클레임이 사용자의 현재 클레임 세트에 있는 경우 작업을 수행하도록 지정합니다. `ClaimEquals`는 지정된 클레임이 존재하고 해당 값이 지정된 값과 같은 경우 작업을 수행하도록 지정합니다. |
| `ExecuteActionsIf` | 예 | 테스트가 true 또는 false인 경우 사전 조건의 작업을 수행해야 하는지 여부를 나타냅니다. |

**Precondition** 요소에 포함되는 요소는 다음과 같습니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 값 | 1:n | 검사에 사용되는 데이터입니다. 이 검사 유형이 `ClaimsExist`인 경우 이 필드는 쿼리할 ClaimTypeReferenceId를 지정합니다. 검사 유형이 `ClaimEquals`인 경우 이 필드는 쿼리할 ClaimTypeReferenceId를 지정합니다. 다른 값 요소에서 확인할 값을 지정합니다.|
| 작업 | 1:1 | 오케스트레이션 단계 내의 사전 조건 검사가 true인 경우 수행해야 하는 작업입니다. **Action** 의 값은 연결된 유효성 검사 기술 프로필을 실행하지 않도록 지정하는 `SkipThisValidationTechnicalProfile`로 설정됩니다. |

다음 예제에서는 [Azure AD SSPR 기술 프로필](aad-sspr-technical-profile.md)을 사용하여 메일 주소를 보내고 확인합니다.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

다음 예제에서는 사전 조건이 있는 **mfaType** 클레임의 사용자 선택에 따라 메일 또는 SMS에서 코드를 보냅니다.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>표시 컨트롤 참조

표시 컨트롤은 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)의 [표시 클레임](self-asserted-technical-profile.md#display-claims)에서 참조됩니다.

예를 들면 다음과 같습니다.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>다음 단계

표시 컨트롤 사용에 대한 샘플은 다음을 참조하세요. 

- [Mailjet을 사용한 사용자 지정 메일 확인](custom-email-mailjet.md)
- [SendGrid를 사용한 사용자 지정 메일 확인](custom-email-sendgrid.md)
