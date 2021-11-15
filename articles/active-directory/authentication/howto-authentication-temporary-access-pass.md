---
title: 암호 없는 인증 방법을 등록하기 위해 Azure AD에서 임시 액세스 패스 구성
description: 임시 액세스 패스를 사용하여 암호 없는 인증 방법을 구성하고 사용자가 등록할 수 있도록 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: justinha
author: inbarckMS
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef246412a172b6f5fcafcd409e4b6e29eb12c847
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471443"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>암호 없는 인증 방법을 등록하기 위해 Azure AD에서 임시 액세스 패스 구성(미리 보기)

Microsoft Authenticator 앱을 통해 FIDO2 및 암호 없는 휴대폰 로그인과 같은 암호 없는 인증 방법을 사용하면 암호 없이 안전하게 로그인할 수 있습니다. 사용자는 다음 두 가지 방법 중 하나로 암호 없는 방법을 부트스트랩할 수 있습니다.

- 기존 Azure AD Multi-Factor Authentication 방법 사용 
- TAP(임시 액세스 패스) 사용 

임시 액세스 패스는 강력한 인증 요구 사항을 충족하고 암호 없는 방법을 비롯한 다른 인증 방법을 등록하는 데 사용할 수 있는 관리자가 발급한 시간 제한형 암호입니다. 임시 액세스 패스를 사용하면 사용자가 FIDO2 보안 키 또는 Microsoft Authenticator 앱과 같은 강력한 인증 요소를 분실하거나 잊어버린 경우에도 좀 더 쉽게 복구할 수 있지만 새로운 강력한 인증 방법을 등록하려면 로그인해야 합니다.

이 문서에서는 Azure Portal을 사용하여 Azure AD에서 임시 액세스 패스를 사용하도록 설정하고 사용하는 방법을 보여 줍니다. REST API를 사용하여 이러한 작업을 수행할 수도 있습니다. 

>[!NOTE]
>임시 액세스 패스는 현재 퍼블릭 미리 보기로 제공됩니다. 특정 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 

## <a name="enable-the-temporary-access-pass-policy"></a>임시 액세스 패스 정책 사용

임시 액세스 패스 정책은 테넌트에서 생성된 패스의 수명, 임시 액세스 패스를 사용하여 로그인할 수 있는 사용자 및 그룹 등의 설정을 정의합니다. 모든 사용자가 임시 액세스 패스를 사용하여 로그인하도록 하려면 인증 방법 정책을 사용하도록 설정하고 임시 액세스 패스를 사용하여 로그인할 수 있는 사용자 및 그룹을 선택해야 합니다.
임의 사용자에 대해 임시 액세스 패스를 만들 수 있지만 정책에 포함된 사용자만 해당 패스를 사용하여 로그인할 수 있습니다.

전역 관리자 및 인증 방법 정책 관리자 역할 소유자는 임시 액세스 패스 인증 방법 정책을 업데이트할 수 있습니다.
임시 액세스 패스 인증 방법 정책을 구성하려면

1. 전역 관리자로 Azure Portal에 로그인하고 **Azure Active Directory** > **보안** > **인증 방법** > **임시 액세스 패스** 를 클릭합니다.
1. **예** 를 클릭하여 정책을 사용하도록 설정하고, 정책이 적용된 사용자를 선택한 후 **일반** 설정을 선택합니다.

   ![임시 액세스 패스 인증 방법 정책을 사용하도록 설정하는 방법을 보여 주는 스크린샷](./media/how-to-authentication-temporary-access-pass/policy.png)

   다음 표에서는 기본값 및 허용되는 값 범위에 대해 설명합니다.


   | 설정 | 기본값 | 허용되는 값 | 의견 |
   |---|---|---|---|
   | 최소 수명 | 1시간 | 10-43200분(30일) | 임시 액세스 패스가 유효한 최소 기간(분)입니다. |
   | 최대 수명 | 24시간 | 10-43200분(30일) | 임시 액세스 패스가 유효한 최대 기간(분)입니다. |
   | 기본 수명 | 1시간 | 10-43200분(30일) | 기본값은 정책으로 구성된 최소 및 최대 수명 내에서 개별 패스로 재정의될 수 있습니다. |
   | 일회성 사용 | 거짓 | True / False | 정책이 false로 설정된 경우 테넌트의 패스는 유효 기간(최대 수명) 동안 한 번 또는 여러 번 사용할 수 있습니다. 임시 액세스 패스 정책에서 일회성 사용을 적용하여 테넌트에 생성된 모든 패스가 일회성 사용으로 만들어집니다. |
   | 길이 | 8 | 8-48자 | 암호의 길이를 정의합니다. |

## <a name="create-a-temporary-access-pass"></a>임시 액세스 패스 만들기

정책을 사용하도록 설정한 후에는 Azure AD에서 사용자에 대한 임시 액세스 패스를 만들 수 있습니다. 이러한 역할은 임시 액세스 패스와 관련하여 다음과 같은 작업을 수행할 수 있습니다.

- 전역 관리자는 모든 사용자(자기 자신 제외)에 대한 임시 액세스 패스를 만들고 삭제하고 볼 수 있습니다.
- 권한 있는 인증 관리자는 관리자 및 멤버(자기 자신 제외)에 대한 임시 액세스 패스를 만들고 삭제하고 볼 수 있습니다.
- 인증 관리자는 멤버(자기 자신 제외)에 대한 임시 액세스 패스를 만들고 삭제하고 볼 수 있습니다.
- 전역 관리자는 사용자에 대한 임시 액세스 패스 세부 정보를 볼 수 있습니다(코드 자체를 읽지 않음).

1. 전역 관리자, 권한 있는 인증 관리자 또는 인증 관리자 권한으로 Azure Portal에 로그인합니다. 
1. **Azure Active Directory** 를 클릭하고 사용자(예: *Chris Green*)를 찾아 선택한 다음, **인증 방법** 을 선택합니다.
1. 필요한 경우 **새 사용자 인증 방법 환경을 시도하는** 옵션을 선택합니다.
1. **인증 방법을 추가** 하는 옵션을 선택합니다.
1. **방법 선택** 아래에서 **임시 액세스 패스(미리 보기)** 를 클릭합니다.
1. 사용자 지정 활성화 시간 또는 기간을 정의하고 **추가** 를 클릭합니다.

   ![임시 액세스 패스를 만드는 방법을 보여 주는 스크린샷](./media/how-to-authentication-temporary-access-pass/create.png)

1. 일단 추가되면 임시 액세스 패스에 대한 세부 정보가 표시됩니다. 실제 임시 액세스 패스 값을 기록해 둡니다. 사용자에게 이 값을 제공합니다. **확인** 을 클릭한 후에는 이 값을 볼 수 없습니다.
   
   ![임시 액세스 패스 세부 정보를 보여 주는 스크린샷](./media/how-to-authentication-temporary-access-pass/details.png)

다음 명령은 PowerShell을 사용하여 임시 액세스 패스를 만들고 가져오는 방법을 보여 줍니다.

```powershell
# Create a Temporary Access Pass for a user
$properties = @{}
$properties.isUsableOnce = $True
$properties.startDateTime = '2021-03-11 06:00:00'
$propertiesJSON = $properties | ConvertTo-Json

New-MgUserAuthenticationTemporaryAccessPassMethod -UserId user2@contoso.com -BodyParameter $propertiesJSON

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM TAPRocks!

# Get a user's Temporary Access Pass
Get-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM

```

## <a name="use-a-temporary-access-pass"></a>임시 액세스 패스 사용

임시 액세스 패스의 가장 일반적인 용도는 사용자가 추가 보안 프롬프트를 완료할 필요 없이 처음 로그인하는 동안 인증 세부 정보를 등록할 수 있도록 하는 것입니다. 인증 방법은 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)에 등록됩니다. 사용자는 여기에서 기존 인증 방법을 업데이트할 수도 있습니다.

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)로 웹 브라우저를 엽니다.
1. 임시 액세스 패스를 만든 계정의 UPN(예: *tapuser@contoso.com* )을 입력합니다.
1. 사용자가 임시 액세스 패스 정책에 포함되어 있으면 임시 액세스 패스를 입력할 수 있는 화면이 표시됩니다.
1. Azure Portal에 표시된 임시 액세스 패스를 입력합니다.

   ![임시 액세스 패스를 입력하는 방법을 보여 주는 스크린샷](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>페더레이션된 도메인의 경우 페더레이션보다 임시 액세스 패스가 우선적으로 사용됩니다. 임시 액세스 패스가 있는 사용자는 Azure AD에서 인증을 완료하고 IdP(페더레이션된 ID 공급자)로 리디렉션되지 않습니다.

이제 사용자가 로그인되어 FIDO2 보안 키와 같은 방법을 업데이트하거나 등록할 수 있습니다. 자격 증명이나 디바이스가 손실되어 인증 방법을 업데이트하는 사용자는 이전 인증 방법을 제거해야 합니다.
사용자는 암호를 사용하여 계속 로그인할 수도 있습니다. TAP는 사용자의 암호를 대체하지 않습니다.

### <a name="passwordless-phone-sign-in"></a>암호 없는 휴대폰 로그인

사용자는 자신의 임시 액세스 패스를 사용하여 Authenticator 앱에서 직접 암호 없는 휴대폰 로그인을 등록할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱에 회사 또는 학교 계정 추가](https://support.microsoft.com/account-billing/add-your-work-or-school-account-to-the-microsoft-authenticator-app-43a73ab5-b4e8-446d-9e54-2a4cb8e4e93c)를 참조하세요.

![회사 또는 학교 계정을 사용하여 임시 액세스 패스를 입력하는 방법을 보여 주는 스크린샷](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

### <a name="guest-access"></a>게스트 액세스

게스트 사용자는 임시 액세스 패스에서 홈 테넌트 인증 요구 사항을 충족하는 경우 홈 테넌트에서 발급한 임시 액세스 패스를 사용하여 리소스 테넌트로 로그인할 수 있습니다. 리소스 테넌트에서 MFA가 필요한 경우 게스트 사용자는 리소스에 대한 액세스 권한을 얻기 위해 MFA를 수행해야 합니다.

### <a name="expiration"></a>만료

만료되거나 삭제된 임시 액세스 패스는 대화형 또는 비대화형 인증에 사용할 수 없습니다. 사용자는 임시 액세스 패스가 만료되거나 삭제된 후 다른 인증 방법으로 다시 인증해야 합니다. 

## <a name="delete-an-expired-temporary-access-pass"></a>만료된 임시 액세스 패스 삭제

사용자에 대한 **인증 방법** 에서 **세부 정보** 열에는 임시 액세스 패스가 만료된 시간이 표시됩니다. 다음 단계를 사용하여 만료된 임시 액세스 패스를 삭제할 수 있습니다.

1. Azure AD 포털에서 **사용자** 로 이동한 후 사용자(예: *Tap 사용자*)를 선택하고 **인증 방법** 을 선택합니다.
1. 목록에 표시된 **임시 액세스 패스(미리 보기)** 인증 방법의 오른쪽에서 **삭제** 를 선택합니다.

다음과 같이 PowerShell을 사용할 수도 있습니다.

```powershell
# Remove a user's Temporary Access Pass
Remove-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com -TemporaryAccessPassAuthenticationMethodId c5dbd20a-8b8f-4791-a23f-488fcbde3b38
```

## <a name="replace-a-temporary-access-pass"></a>임시 액세스 패스 바꾸기 

- 사용자는 임시 액세스 패스를 하나만 보유할 수 있습니다. 암호는 임시 액세스 패스의 시작 및 종료 시간 동안 사용할 수 있습니다.
- 사용자에게 새 임시 액세스 패스가 필요한 경우:
  - 기존 임시 액세스 패스가 유효한 경우 관리자는 기존 임시 액세스 패스를 삭제하고 사용자를 위한 새 패스를 만들어야 합니다. 
  - 기존 임시 액세스 패스가 만료된 경우 새 임시 액세스 패스가 기존 임시 액세스 패스를 재정의합니다.

온보딩 및 복구를 위한 NIST 표준에 대한 자세한 내용은 [NIST 특수 게시물 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4)를 참조하세요.

## <a name="limitations"></a>제한 사항

다음 제한 사항에 유의하세요.

- 일회용 임시 액세스 패스를 사용하여 FIDO2 또는 휴대폰 로그인과 같은 암호 없는 방법을 등록하는 경우 사용자는 일회성 임시 액세스 패스를 사용하여 10분 이내에 등록을 완료해야 합니다. 이 제한은 두 번 이상 사용할 수 있는 임시 액세스 패스에는 적용되지 않습니다.
- 임시 액세스 패스는 퍼블릭 미리 보기로 제공되며 현재 미국 정부용 Azure에서 사용할 수 없습니다.
- SSPR(셀프 서비스 암호 재설정) 등록 정책 *또는* [ID 보호 Multi-Factor Authentication 등록 정책](../identity-protection/howto-identity-protection-configure-mfa-policy.md) 범위에 있는 사용자는 임시 액세스 패스를 사용하여 로그인한 후 인증 방법을 등록해야 합니다. 이러한 정책에 대한 범위에 있는 사용자는 [결합된 등록의 인터럽트 모드](concept-registration-mfa-sspr-combined.md#combined-registration-modes)로 리디렉션됩니다. 이 환경은 현재 FIDO2 및 휴대폰 로그인 등록을 지원하지 않습니다. 
- 임시 액세스 패스는 NPS(네트워크 정책 서버) 확장 및 AD FS(Active Directory Federation Services) 어댑터와 함께 사용하거나 Windows 설치/OOBE(Out-of-Box-Experience), Autopilot 또는 비즈니스용 Windows Hello를 배포하는 데 사용할 수 없습니다. 
- 테넌트에서 Seamless SSO를 사용하도록 설정하면 암호를 입력하라는 메시지가 표시됩니다. 사용자가 임시 액세스 패스를 사용하여 로그인할 수 있도록 하는 **대신 임시 액세스 패스 사용** 링크를 사용할 수 있습니다.

  ![대신 임시 액세스 패스 사용 스크린샷](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>문제 해결    

- 로그인 중에 사용자에게 임시 액세스 패스가 제공되지 않으면 다음을 확인합니다.
  - 사용자가 임시 액세스 패스 인증 방법 정책 범위 내에 있습니다.
  - 사용자에게 유효한 임시 액세스 패스가 있으며 일회성 사용인 경우에는 아직 사용되지 않은 것입니다.
- 임시 액세스 패스를 사용하여 로그인하는 동안 **사용자 자격 증명 정책으로 인해 임시 액세스 패스 로그인이 차단됨** 이 표시됩니다.
  - 인증 방법 정책에는 일회성 임시 액세스 단계가 필요하지만 사용자에게 다용도 임시 액세스 패스가 있습니다.
  - 일회성 임시 액세스 패스는 이미 사용되었습니다.
- 사용자 자격 증명 정책으로 인해 임시 액세스 패스 로그인이 차단된 경우 사용자가 TAP 정책의 범위 내에 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory에서 암호 없는 인증 배포 계획](howto-authentication-passwordless-deployment.md)