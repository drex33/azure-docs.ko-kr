---
title: 지역화 문자열 ID - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C의 사용자 지정 정책에서 api.signuporsignin의 ID를 사용하여 콘텐츠 정의의 ID를 지정합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7e0f3e2ab306c46851e4c2ffcb6acf5824326282
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564377"
---
# <a name="localization-string-ids"></a>지역화 문자열 ID

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 요소를 사용하면 사용자 경험용 정책에서 여러 로캘이나 언어를 지원할 수 있습니다. 이 문서에서는 정책에서 사용할 수 있는 지역화 ID 목록을 제공합니다. UI 지역화 개념을 숙지하려면 [지역화](localization.md)를 참조하세요.

## <a name="sign-up-or-sign-in-page-elements"></a>등록 또는 로그인 페이지 요소

다음 ID는 ID가 `api.signuporsignin`인 콘텐츠 정의 및 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)에 사용됩니다.

| ID | 기본값 | 페이지 레이아웃 버전 |
| -- | ------------- | ------ |
| **forgotpassword_link** | 암호를 잊으셨습니까? | `All` |
| **createaccount_intro** | 아직 계정이 없으세요? | `All` |
| **button_signin** | 로그인 | `All` |
| **social_intro** | 소셜 계정으로 로그인 | `All` |
| **remember_me** |로그인 유지. | `All` |
| **unknown_error** | 로그인하는 동안 문제가 발생했습니다. 나중에 다시 시도하세요. | `All` |
| **divider_title** | 또는 | `All` |
| **local_intro_email** | 기존 계정으로 로그인 | `< 2.0.0` |
| **logonIdentifier_email** | 메일 주소 | `< 2.0.0` |
| **requiredField_email** | 이메일을 입력하세요. | `< 2.0.0` |
| **invalid_email** | 올바른 이메일 주소를 입력하세요. | `< 2.0.0` |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&''\*+/=?^\_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)\*$ | `< 2.0.0` |
| **local_intro_username** | 사용자 이름으로 로그인 | `< 2.0.0` |
| **logonIdentifier_username** | 사용자 이름 | `< 2.0.0` |
| **requiredField_username** | 사용자 이름을 입력하세요. | `< 2.0.0` |
| **password** | 암호 | `< 2.0.0` |
| **requiredField_password** | 암호를 입력하세요. | `< 2.0.0` |
| **createaccount_link** | 지금 가입하십시오. | `< 2.0.0` |
| **cancel_message** | 사용자가 암호를 잊음 | `< 2.0.0` |
| **invalid_password** | 입력한 암호의 형식이 잘못되었습니다. | `< 2.0.0` |
| **createaccount_one_link** | 지금 가입하십시오. | `>= 2.0.0` |
| **createaccount_two_links** | {0} 또는 {1}로 가입 | `>= 2.0.0` |
| **createaccount_three_links** | {0}, {1} 또는 {2}로 가입 | `>= 2.0.0` |
| **local_intro_generic** | {0}으로 로그인 | `>= 2.1.0` |
| **requiredField_generic** | 사용자의 {0}을 입력하세요. | `>= 2.1.0` |
| **invalid_generic** | 유효한 {0}을 입력하세요. | `>= 2.1.1` |
| **제목** | 로그인 | `>= 2.1.1` |


> [!NOTE]
> * {0}과 같은 자리 표시자는 `ClaimType`의 `DisplayName` 값을 사용하여 자동으로 채워집니다. 
> * `ClaimType`을 지역화하는 방법에 대한 자세한 내용은 [가입 또는 로그인 예](#signupsigninexample)를 참조하세요.

다음 예제에서는 등록 또는 로그인 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="가입 또는 로그인 페이지 U X 요소를 표시하는 스크린샷.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>가입 또는 로그인 ID 공급자

ID 공급자의 ID는 사용자 경험 **ClaimsExchange** 요소에서 구성됩니다. ID 공급자의 제목을 지역화하려는 경우 **ElementType** 은 `ClaimsProvider`로 설정하고 **StringId** 는 `ClaimsExchange`의 ID로 설정합니다.

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
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

다음 예제에서는 Facebook ID 공급자를 아랍어로 지역화합니다.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>등록 또는 로그인 오류 메시지

| ID | 기본값 |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | 암호가 올바르지 않습니다. |
| **UserMessageIfPasswordExpired**| 암호가 만료되었습니다.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | 계정을 찾을 수 없는 것 같습니다. |
| **UserMessageIfOldPasswordUsed** | 이전 암호를 사용한 것 같습니다. |
| **DefaultMessage** | 잘못된 사용자 이름 또는 암호 |
| **UserMessageIfUserAccountDisabled** | 계정이 잠겨 있습니다. 잠금 해제하려면 고객 지원 담당자에게 문의한 다음 다시 시도하세요. |
| **UserMessageIfUserAccountLocked** | 권한 없는 사용을 방지하기 위해 계정이 일시적으로 잠겨 있습니다. 나중에 다시 시도하십시오. |
| **AADRequestsThrottled** | 현재 요청이 너무 많습니다. 잠시 기다린 후에 다시 시도하세요. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>가입 또는 로그인 예

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>등록 및 자체 어설션된 페이지 사용자 인터페이스 요소

아래에는 ID가 `api.localaccountsignup`인 콘텐츠 정의 또는 `api.selfasserted.profileupdate`과 `api.localaccountpasswordreset`, [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)과 같이 `api.selfasserted`로 시작하는 모든 콘텐츠 정의의 ID가 나와 있습니다.

| ID | 기본값 |
| -- | ------------- |
| **ver_sent** | 확인 코드를 보낸 대상: |
| **ver_but_default** | 기본값 |
| **cancel_message** | 사용자가 자체 어설션된 정보의 입력을 취소함 |
| **preloader_alt** | 기다려 주세요. |
| **ver_but_send** | 확인 코드 보내기 |
| **alert_yes** | 예 |
| **error_fieldIncorrect** | 하나 이상의 필드가 잘못 채워졌습니다. 항목을 확인하고 다시 시도하세요. |
| **year** | Year |
| **verifying_blurb** | 정보를 처리하는 동안 잠시 기다려 주세요. |
| **button_cancel** | 취소 |
| **ver_fail_no_retry** | 여러 번 틀렸습니다. 나중에 다시 시도하세요. |
| **month** | Month |
| **ver_success_msg** | 이메일 주소를 확인했습니다. 이제 계속할 수 있습니다. |
| **months** | 1월, 2월, 3월, 4월, 5월, 6월, 7월, 8월, 9월, 10월, 11월, 12월 |
| **ver_fail_server** | 이메일 주소를 확인하는 동안 문제가 발생했습니다. 유효한 이메일 주소를 입력하고 다시 시도하세요. |
| **error_requiredFieldMissing** | 필수 필드가 누락되어 있습니다. 모든 필수 필드를 작성하고 다시 시도하세요. |
| **initial_intro** | 다음 세부 정보를 제공하세요. |
| **ver_but_resend** | 새 코드 전송 |
| **button_continue** | 생성 |
| **error_passwordEntryMismatch** | 암호 입력 필드가 일치하지 않습니다. 두 필드에 같은 암호를 입력하고 다시 시도하세요. |
| **ver_incorrect_format** | 잘못된 형식 |
| **ver_but_edit** | 이메일 변경 |
| **ver_but_verify** | 코드 확인 |
| **alert_no** | 예 |
| **ver_info_msg** | 받은 편지함으로 확인 코드를 보냈습니다. 확인 코드를 아래 입력란에 복사하세요. |
| **day** | 일 |
| **ver_fail_throttled** | 이 이메일 주소를 확인해 달라는 요청이 너무 많습니다. 잠시 기다렸다가 다시 시도하세요. |
| **helplink_text** | 이것은 무엇인가요? |
| **ver_fail_retry** | 해당 코드가 잘못되었습니다. 나중에 다시 시도하세요. |
| **alert_title** | 세부 정보 입력 취소 |
| **required_field** | 이 정보는 필수 항목입니다. |
| **alert_message** | 세부 정보 입력을 취소하시겠습니까? |
| **ver_intro_msg** | 확인이 필요합니다. [보내기] 단추를 클릭하세요. |
| **ver_input** | 확인 코드 |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>등록 및 자체 어설션된 페이지 오류 메시지

| ID | 기본값 |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | 지정한 ID를 사용하는 사용자가 이미 있습니다. 다른 ID를 선택하세요. |
| **UserMessageIfClaimNotVerified** | 확인되지 않은 클레임: {0} |
| **UserMessageIfIncorrectPattern** | {0}에 대한 잘못된 패턴 |
| **UserMessageIfMissingRequiredElement** | 필요한 {0} 요소가 없습니다. |
| **UserMessageIfValidationError** | {0}의 유효성 검사 오류 |
| **UserMessageIfInvalidInput** | {0}에 잘못된 입력이 있습니다. |
| **ServiceThrottled** | 현재 요청이 너무 많습니다. 잠시 기다린 후에 다시 시도하세요. |

다음 예제에서는 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![레이블이 지정된 UI 요소 이름이 있는 등록 페이지](./media/localization-string-ids/localization-sign-up.png)

다음 예제에서는 사용자가 확인 코드 보내기 단추를 클릭한 후 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![등록 페이지 이메일 확인 UX 요소](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>가입 및 자체 어설션된 페이지 예

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>전화 인증 페이지 사용자 인터페이스 요소

아래에는 ID가 `api.phonefactor`인 콘텐츠 정의 및 [전화 단계 기술 프로필](phone-factor-technical-profile.md)에 대한 ID가 나와 있습니다.

| ID | 기본값 | 페이지 레이아웃 버전 |
| -- | ------------- | ------ |
| **button_verify** | 내게 전화 걸기 | `All` |
| **country_code_label** | 국가 코드 | `All` |
| **cancel_message** | 사용자가 다단계 인증을 취소함 | `All` |
| **text_button_send_second_code** | 새 코드 보내기 | `All` |
| **code_pattern** | \\d{6} | `All` |
| **intro_mixed** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS 또는 전화로 코드를 보내겠습니다. | `All` |
| **intro_mixed_p** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 받거나 SMS로 코드를 받을 번호를 선택하세요. | `All` |
| **button_verify_code** | 코드 확인 | `All` |
| **requiredField_code** | 받은 확인 코드를 입력하세요. | `All` |
| **invalid_code** | 받은 6자리 코드를 입력하세요. | `All` |
| **button_cancel** | 취소 | `All` |
| **local_number_input_placeholder_text** | 전화 번호 | `All` |
| **button_retry** | 다시 시도 | `All` |
| **alternative_text** | 내 휴대폰이 없음 | `All` |
| **intro_phone_p** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 받을 번호를 선택하세요. | `All` |
| **intro_phone** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 전화를 걸겠습니다. | `All` |
| **enter_code_text_intro** | 아래에 인증 코드를 입력하거나  | `All` |
| **intro_entry_phone** | 전화를 걸어 인증할 수 있도록 아래에 숫자를 입력하세요. | `All` |
| **intro_entry_sms** | SMS를 통해 인증 코드를 보낼 수 있도록 아래에 숫자를 입력하세요. | `All` |
| **button_send_code** | 코드 보내기 | `All` |
| **invalid_number** | 유효한 전화 번호를 입력하세요. | `All` |
| **intro_sms** | 다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS로 코드를 보내겠습니다. | `All` |
| **intro_entry_mixed** | SMS 또는 전화를 통해 인증 코드를 보낼 수 있도록 아래에 숫자를 입력하세요. | `All` |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ | `All` |
| **intro_sms_p** |다음 번호가 기록에 남아 있습니다. 본인 인증을 위해 SMS로 코드를 받을 번호를 선택하세요. | `All` |
| **requiredField_countryCode** | 국가 번호를 선택하세요. | `All` |
| **requiredField_number** | 전화 번호를 입력하세요. | `All` |
| **country_code_input_placeholder_text** |국가 또는 지역 | `All` |
| **number_label** | 전화 번호 | `All` |
| **error_tryagain** | 제공한 전화 번호가 통화 중이거나 사용될 수 없습니다. 번호를 확인 후 다시 시도하세요. | `All` |
| **error_sms_throttled** | 문자 메시지 수에 대한 한도에 도달했습니다. 잠시 후 다시 시도하세요. | `>= 1.2.3` |
| **error_phone_throttled** | 호출 시도 횟수에 대한 한도에 도달했습니다. 잠시 후 다시 시도하세요. | `>= 1.2.3` |
| **error_throttled** | 확인 시도 횟수에 대한 한도에 도달했습니다. 잠시 후 다시 시도하세요. | `>= 1.2.3` |
| **error_incorrect_code** | 입력한 확인 코드가 레코드와 일치하지 않습니다. 다시 시도하거나 새 코드를 요청하세요. | `All` |
| **countryList** | [국가 목록을](#phone-factor-authentication-page-example)참조하세요. | `All` |
| **error_448** | 제공한 전화 번호에 연결할 수 없습니다. | `All` |
| **error_449** | 사용자가 다시 시도 횟수를 초과했습니다. | `All` |
| **verification_code_input_placeholder_text** | 확인 코드 | `All` |

다음 예제에서는 MFA 등록 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![전화 단계 인증 등록 UX 요소](./media/localization-string-ids/localization-mfa1.png)

다음 예제에서는 MFA 유효성 검사 페이지의 몇 가지 사용자 인터페이스 요소 사용 방법을 보여 줍니다.

![전화 단계 인증 유효성 검사 UX 요소](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>전화 단계 인증 페이지 예제

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_sms_throttled">You hit the limit on the number of text messages. Try again shortly.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_phone_throttled">You hit the limit on the number of call attempts. Try again shortly.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_throttled">You hit the limit on the number of verification attempts. Try again shortly.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>확인 표시 컨트롤 사용자 인터페이스 요소

아래에는 [페이지 레이아웃 버전](page-layout.md) 2.1.0 이상인 [확인 표시 컨트롤](display-control-verification.md)에 대한 ID가 나와 있습니다.

| ID | 기본값 |
| -- | ------------- |
|intro_msg <sup>*</sup>| 확인이 필요합니다. [보내기] 단추를 클릭하세요.|
|success_send_code_msg | 확인 코드를 보냈습니다. 확인 코드를 아래 입력란에 복사하세요.|
|failure_send_code_msg | 이메일 주소를 확인하는 동안 문제가 발생했습니다. 유효한 이메일 주소를 입력하고 다시 시도하세요.|
|success_verify_code_msg | 이메일 주소를 확인했습니다. 이제 계속할 수 있습니다.|
|failure_verify_code_msg | 이메일 주소를 확인하는 동안 문제가 발생했습니다. 다시 시도하세요.|
|but_send_code | 확인 코드 보내기|
|but_verify_code | 코드 확인|
|but_send_new_code | 새 코드 전송|
|but_change_claims | 이메일 변경|

참고: `intro_msg` 요소는 숨겨져 있어서 자체 어설션된 페이지에 표시되지 않습니다. 표시하려면 CSS 스타일시트로 [HTML 사용자 지정](customize-ui-with-html.md)을 사용합니다. 예를 들면 다음과 같습니다.
    
```css
.verificationInfoText div{display: block!important}
```

### <a name="verification-display-control-example"></a>확인 표시 컨트롤 예제

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>확인 표시 컨트롤 사용자 인터페이스 요소(사용되지 않음)

아래에는 [페이지 레이아웃 버전](page-layout.md) 2.0.0을 사용하는 [확인 표시 컨트롤](display-control-verification.md)에 대한 ID가 나와 있습니다.

| ID | 기본값 |
| -- | ------------- |
|verification_control_but_change_claims |변경 |
|verification_control_fail_send_code |코드를 전송하지 못했습니다. 나중에 다시 시도하세요. |
|verification_control_fail_verify_code |코드를 확인하지 못했습니다. 나중에 다시 시도하세요. |
|verification_control_but_send_code |코드 보내기 |
|verification_control_but_send_new_code |새 코드 보내기 |
|verification_control_but_verify_code |코드 확인 |
|verification_control_code_sent| 확인 코드를 보냈습니다. 확인 코드를 아래 입력란에 복사하세요. |

### <a name="verification-display-control-example-deprecated"></a>확인 표시 컨트롤 예제(사용되지 않음)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Restful 서비스 오류 메시지

다음은 [Restful 서비스 기술 프로필](restful-technical-profile.md) 오류 메시지에 대한 ID입니다.

| ID | 기본값 |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Restful 서비스 엔드포인트에 대한 연결을 설정하지 못했습니다. Restful 서비스 URL: {0} |
|UserMessageIfCircuitOpen | {0} Restful 서비스 URL: {1} |
|UserMessageIfDnsResolutionFailed | Restful 서비스 엔드포인트의 호스트 이름을 확인하지 못했습니다. Restful 서비스 URL: {0} |
|UserMessageIfRequestTimeout | 시간 제한 {0}초 내에 Restful 서비스 엔드포인트에 대한 연결을 설정하지 못했습니다. Restful 서비스 URL: {1} |


### <a name="restful-service-example"></a>Restful 서비스 예제

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Azure AD MFA 오류 메시지

아래에는 [Azure AD MFA 기술 프로필](multi-factor-auth-technical-profile.md) 오류 메시지에 대한 ID가 나와 있습니다.

| ID | 기본값 |
| -- | ------------- |
|UserMessageIfCouldntSendSms | 휴대폰으로 SMS를 보낼 수 없습니다. 다른 전화 번호를 사용해 보세요. |
|UserMessageIfInvalidFormat | 전화 번호 형식이 잘못되었습니다. 전화 번호를 수정하고 다시 시도하세요.|
|UserMessageIfMaxAllowedCodeRetryReached | 잘못된 코드를 너무 많이 입력했습니다. 나중에 다시 시도하세요.|
|UserMessageIfServerError | MFA 서비스를 사용할 수 없습니다. 나중에 다시 시도하세요.|
|UserMessageIfThrottled | 요청이 제한되었습니다. 나중에 다시 시도하세요.|
|UserMessageIfWrongCodeEntered|잘못된 코드를 입력했습니다. 다시 시도하세요.|

### <a name="azure-ad-mfa-example"></a>Azure AD MFA 예제

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

아래에는 [Azure AD SSPR 기술 프로필](aad-sspr-technical-profile.md) 오류 메시지에 대한 ID가 나와 있습니다.

| ID | 기본값 |
| -- | ------------- |
|UserMessageIfChallengeExpired | 만료된 코드입니다.|
|UserMessageIfInternalError | 메일 서비스에서 내부 오류가 발생했습니다. 나중에 다시 시도하세요.|
|UserMessageIfThrottled | 요청이 너무 많습니다. 나중에 다시 시도하세요.|
|UserMessageIfVerificationFailedNoRetry | 최대 확인 시도 횟수를 초과했습니다.|
|UserMessageIfVerificationFailedRetryAllowed | 확인하지 못했습니다. 다시 시도하세요.|


### <a name="azure-ad-sspr-example"></a>Azure AD SSPR 예제

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>일회용 암호 오류 메시지

[일회용 암호 기술 프로필](one-time-password-technical-profile.md) 오류 메시지에 대한 ID는 다음과 같습니다.

| ID | 기본값 |
| -- | ------------- |
| UserMessageIfSessionDoesNotExist | 예 | 코드 확인 세션이 만료된 경우 사용자에게 표시할 메시지입니다. 코드가 만료되었거나 지정된 식별자에 대한 코드가 생성되지 않았습니다. |
| UserMessageIfMaxRetryAttempted | 예 | 허용되는 최대 확인 시도 횟수를 초과하는 경우 사용자에게 표시할 메시지입니다. |
| UserMessageIfMaxNumberOfCodeGenerated | 예 | 코드 생성이 허용되는 최대 횟수를 초과하는 경우 사용자에게 표시할 메시지입니다. |
| UserMessageIfInvalidCode | 예 | 잘못된 코드를 제공한 경우 사용자에게 표시할 메시지입니다. |
| UserMessageIfVerificationFailedRetryAllowed | 예 | 사용자가 잘못된 코드를 제공하였으며, 사용자가 올바른 코드를 제공할 수 있는 경우 사용자에게 표시할 메시지입니다.  |
|UserMessageIfSessionConflict|예| 코드를 확인할 수 없는 경우 사용자에게 표시할 메시지입니다.|

### <a name="one-time-password-example"></a>일회성 암호 예제

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxNumberOfCodeGenerated">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>클레임 변환 오류 메시지

클레임 변환 오류 메시지에 대한 ID는 다음과 같습니다.

| ID | 클레임 변환 | 기본값 |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | 클레임 유형 "inputClaim"에 대한 부울 클레임 값을 비교하지 못했습니다.| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | 클레임 값을 비교하지 못했습니다. 제공된 왼쪽 피연산자가 오른쪽 피연산자보다 큽니다.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | StringComparison "OrdinalIgnoreCase"를 사용하여 클레임 값을 비교하지 못했습니다.|

### <a name="claims-transformations-example"></a>클레임 변환 예제

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>다음 단계

지역화 예제는 다음 문서를 참조하세요.

- [Azure Active Directory B2C에서 사용자 지정 정책으로 언어 사용자 지정](language-customization.md)
- [Azure Active Directory B2C에서 사용자 흐름으로 언어 사용자 지정](language-customization.md)
