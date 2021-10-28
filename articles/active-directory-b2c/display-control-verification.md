---
title: 디스플레이 컨트롤을 사용하여 클레임 확인
titleSuffix: Azure AD B2C
description: Azure AD B2C 디스플레이 컨트롤을 사용하여 사용자 지정 정책에서 제공하는 사용자 경험의 클레임을 확인하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 2a6a7a22d5d9ee19d396739b508de6afe9484c8e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037294"
---
# <a name="verification-display-control"></a>디스플레이 컨트롤 확인

[디스플레이 컨트롤](display-controls.md) 확인을 사용하여 이메일 주소나 전화번호 같은 클레임을 사용자에게 전송된 확인 코드와 함께 확인합니다.

## <a name="verificationcontrol-actions"></a>VerificationControl 작업

디스플레이 컨트롤 확인은 다음 두 단계의 작업으로 구성됩니다.

1. 확인 코드를 보내야 하는 이메일 주소 또는 전화번호와 같은 대상을 사용자에게 요청합니다. 사용자가 **코드 보내기** 단추를 선택하면 디스플레이 컨트롤 확인의 **SendCode 동작이** 실행됩니다. **SendCode 작업** 은 코드를 생성하고, 전송할 콘텐츠를 구축하여 사용자에게 보냅니다. 주소 값은 미리 채워질 수 있으며 2단계 인증으로 작용할 수 있습니다.

    ![코드 보내기 작업 예제 페이지](media/display-control-verification/display-control-verification-email-action-01.png)

1. 코드를 보낸 후 사용자는 메시지를 읽고, 디스플레이 컨트롤에서 제공하는 컨트롤에 확인 코드를 입력하고, **코드 확인** 을 선택합니다. **코드 확인** 을 선택하면 해당 주소와 연결된 코드를 확인하기 위해 **VerifyCode 작업** 이 실행됩니다. 사용자가 **새 코드 보내기** 를 선택하면 첫 번째 작업이 다시 실행됩니다.

    ![코드 확인 작업 예제 페이지](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>VerificationControl 필수 요소

**VerificationControl** 에는 다음 요소가 포함되어야 합니다.

- `DisplayControl`의 형식은 `VerificationControl`이어야 합니다.
- `DisplayClaims`
  - **Send to** - 확인 코드를 보낼 위치를 지정하는 하나 이상의 클레임입니다. 예를 들어 *이메일* 또는 *국가 코드* 와 *전화번호* 를 사용할 수 있습니다.
  - **Verification code** - 코드를 보낸 후 사용자가 제공하는 확인 코드 클레임입니다. 이 클레임은 필수로 설정해야 하며, `ControlClaimType` 을 `VerificationCode`로 설정해야 합니다.
- 사용자가 확인 프로세스를 완료한 후 자체 어설션된 페이지로 반환될 출력 클레임(선택 사항)입니다. 예를 들어 *이메일* 또는 *국가 코드* 와 *전화번호* 를 사용할 수 있습니다. 자체 어설션된 기술 프로필은 클레임을 사용하여 데이터를 유지하거나 출력 클레임을 다음 오케스트레이션 단계로 버블 업합니다.
- 다음 이름을 가진 두 개의 `Action`:
  - **SendCode** - 사용자에게 코드를 보냅니다. 이 작업에는 일반적으로 코드를 생성하고 보내는 두 가지 유효성 검사 기술 프로필이 포함됩니다.
  - **VerifyCode** - 코드를 확인합니다. 이 작업에는 일반적으로 단일 유효성 검사 기술 프로필이 포함됩니다.

아래 예제에서는 **이메일** 텍스트 상자가 페이지에 표시됩니다. 사용자가 이메일 주소를 입력하고 **SendCode** 를 선택하면 Azure AD B2C 백 엔드에서 **SendCode** 작업이 트리거됩니다.

그런 다음, 사용자가 **verificationCode** 를 입력하고 **VerifyCode** 를 선택하여 백 엔드에서 **VerifyCode** 작업을 트리거합니다. 모든 유효성 검사를 통과하면 **VerificationControl** 이 완료된 것으로 간주되며 사용자는 다음 단계를 계속할 수 있습니다.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
