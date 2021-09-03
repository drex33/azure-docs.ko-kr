---
title: 사용자별 Multi-Factor Authentication 사용 - Azure Active Directory
description: 사용자 상태를 변경하여 사용자별 Azure AD Multi-Factor Authentication을 사용하도록 설정하는 방법을 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2e2212171f0be8d754ac1a86567641c2bad8a9a0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602775"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>사용자별 Azure Multi-Factor Authentication을 사용하여 로그인 이벤트 보호

Azure AD에서 사용자 로그인 이벤트를 보호하려면 MFA(다단계 인증)를 요구할 수 있습니다. 사용자를 보호하려면 조건부 액세스 정책을 사용하여 Azure AD Multi-Factor Authentication을 사용하도록 설정하는 것이 좋습니다. 조건부 액세스는 특정 시나리오에서 필요에 따라 MFA를 요구하는 규칙을 적용할 수 있도록 하는 Azure AD Premium P1 또는 P2 기능입니다. 조건부 액세스 사용을 시작하려면 [자습서: Azure AD Multi-Factor Authentication을 사용하여 사용자 로그인 이벤트 보호](tutorial-enable-azure-mfa.md)를 참조하세요.

조건부 액세스 권한이 없는 Azure AD 무료 테넌트의 경우 [보안 기본값을 사용하여 사용자를 보호](../fundamentals/concept-fundamentals-security-defaults.md)할 수 있습니다. 필요에 따라 MFA를 입력하라는 메시지가 표시되지만 동작을 제어하는 고유한 규칙을 정의할 수는 없습니다.

필요에 따라 사용자별 Azure AD Multi-Factor Authentication에 대해 각 계정을 사용하도록 설정할 수 있습니다. 사용자가 개별적으로 설정되면 신뢰할 수 있는 IP 주소에서 로그인하거나 _신뢰할 수 있는 디바이스에서 MFA 기억하기_ 기능이 설정된 경우와 같이 몇 가지 예외를 제외하고는, 로그인할 때마다 다단계 인증을 수행합니다.

Azure AD 라이선스에 조건부 액세스가 포함되지 않고 보안 기본값을 사용하지 않는 경우 [사용자 상태](#azure-ad-multi-factor-authentication-user-states)를 변경하지 않는 것이 좋습니다. MFA를 사용하도록 설정하는 다양한 방법에 대한 자세한 내용은 [Azure AD Multi-Factor Authentication의 기능 및 라이선스](concept-mfa-licensing.md)를 참조하세요.

> [!IMPORTANT]
>
> 이 문서는 사용자별 Azure AD Multi-Factor Authentication의 상태를 확인하고 변경하는 방법에 대해 자세히 설명합니다. 조건부 액세스 또는 보안 기본값을 사용할 경우에는 다음 단계를 사용하여 사용자 계정을 검토하거나 사용하도록 설정하지 않습니다.
>
> 조건부 액세스 정책을 통해 Azure AD Multi-Factor Authentication을 사용하도록 설정해도 사용자의 상태는 변경되지 않습니다. 사용자가 사용 안 함으로 나타나는 경우 놀라지 마십시오. 조건부 액세스는 상태를 변경하지 않습니다.
>
> **조건부 액세스 정책을 사용하는 경우 사용자별 Azure AD Multi-Factor Authentication을 사용하도록 설정하거나 적용하지 마세요.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Azure AD Multi-Factor Authentication 사용자 상태

사용자의 상태는 관리자가 사용자별 Azure AD Multi-Factor Authentication에 등록했는지 여부에 따라 다릅니다. Azure AD Multi-Factor Authentication의 사용자 계정은 다음과 같은 3가지 상태를 갖습니다.

| 시스템 상태 | 설명 | 영향을 받는 레거시 인증 | 영향 받는 브라우저 앱 | 영향 받는 최신 인증 |
|:---:| --- |:---:|:--:|:--:|
| 사용 안 함 | 사용자별 Azure AD Multi-Factor Authentication에 등록되지 않은 사용자에 대한 기본 상태입니다. | 예 | 아니요 | 예 |
| 사용 | 해당 사용자가 사용자별 Azure AD Multi-Factor Authentication에 등록되어 있지만 계속해서 레거시 인증에 암호를 사용할 수 있습니다. 사용자가 MFA 인증 방법을 아직 등록하지 않은 경우 다음에 최신 인증을 사용하여 로그인할 때 등록하라는 메시지가 표시됩니다(예: 웹 브라우저를 통해). | 아니요. 레거시 인증은 등록 프로세스가 완료될 때까지 계속 작동합니다. | 예. 세션이 만료되면 Azure AD Multi-Factor Authentication 등록이 필요합니다.| 예. 액세스 토큰이 만료되면 Azure AD Multi-Factor Authentication 등록이 필요합니다. |
| 적용 | 사용자가 사용자별 Azure AD Multi-Factor Authentication에 등록되어 있습니다. 사용자가 인증 방법을 아직 등록하지 않은 경우 다음에 최신 인증을 사용하여 로그인할 때 등록하라는 메시지가 표시됩니다(예: 웹 브라우저를 통해). *활성화* 된 상태에서 등록을 완료하는 사용자는 자동으로 *적용됨* 상태로 전환됩니다. | 예. 앱에 앱 암호가 필요합니다. | 예. 로그인 시에 Azure AD Multi-Factor Authentication이 필요 합니다. | 예. 로그인 시에 Azure AD Multi-Factor Authentication이 필요 합니다. |

모든 사용자는 *사용 안 함* 으로 시작합니다. 사용자별 Azure AD Multi-Factor Authentication에 사용자를 등록하면 상태가 *사용* 으로 변경됩니다. 사용된 사용자가 로그인하고 등록 프로세스를 완료하면 상태는 변경 *적용* 으로 변경됩니다. 관리자는 *적용* 부터 *사용* 또는 *사용하지 않음* 까지 포함하여 사용자의 상태를 변경할 수 있습니다.

> [!NOTE]
> 사용자에 대하여 사용자별 MFA를 다시 사용하도록 설정되고 해당 사용자가 다시 등록하지 않은 경우, MFA 상태는 MFA 관리 UI에서 *사용* 에서 *적용* 으로 전환되지 않습니다. 관리자가 직접 사용자를 *적용* 으로 이동해야 합니다.

## <a name="view-the-status-for-a-user"></a>사용자 상태 보기

사용자 상태를 확인하고 관리하려면 다음 단계를 완료하여 Azure Portal 페이지에 액세스합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Azure Active Directory* 를 검색하고 선택한 다음, **사용자** > **모든 사용자** 를 선택합니다.
1. **Multi-Factor Authentication** 을 선택합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 다음 예제 스크린샷을 선택하여 전체 Azure Portal 창 및 메뉴 위치를 확인합니다. [![Azure AD의 사용자 창에서 Multi-Factor Authentication을 선택합니다.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 다음 예제와 같이 사용자 상태를 표시하는 새 페이지가 열립니다.
   ![Azure AD Multi-Factor Authentication에 대한 예제 사용자 상태 정보를 보여 주는 스크린샷](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>사용자 상태 변경

사용자에 대한 사용자별 Azure AD Multi-Factor Authentication 상태를 변경하려면 다음 단계를 완료합니다.

1. 이전 단계를 사용하여 [사용자가 Azure AD Multi-Factor Authentication **사용자** 페이지로 이동하는 상태를 확인](#view-the-status-for-a-user)합니다.
1. 사용자별 Azure AD Multi-Factor Authentication에 대해 사용하도록 설정하려는 사용자를 찾습니다. 위쪽에서 보기를 **사용자** 로 변경해야 할 수도 있습니다.
   ![사용자 탭에서 상태를 변경할 사용자 선택](./media/howto-mfa-userstates/enable1.png)
1. 상태를 변경할 사용자의 이름 옆에 있는 확인란을 선택합니다.
1. 오른쪽의 **빠른 단계** 에서 **사용** 또는 **사용 안 함** 을 선택합니다. 다음 예제에서 사용자 *John Smith* 는 이름 옆에 확인이 있으며 사용할 수 있도록 설정되어 있습니다. ![빠른 단계 메뉴에서 사용을 클릭하여 선택한 사용자 사용](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Azure AD Multi-Factor Authentication에 등록할 시 *사용* 하는 것으로 설정된 사용자는 *적용* 으로 자동으로 전환됩니다. 사용자가 등록되어 있지 않거나 레거시 인증 프로토콜에 대한 연결을 중단하는 것이 허용되는 경우 사용자 상태를 수동으로 *적용* 으로 변경하지 마세요.

1. 열리는 팝업 창에서 선택한 내용을 확인합니다.

사용자를 사용으로 설정한 후 전자 메일을 통해 알립니다. 사용자에게 다음에 로그인할 때 등록하도록 요청하라는 메시지가 표시됨을 알려 줍니다. 또한 조직에서 최신 인증을 지원하지 않는 비브라우저 앱을 사용하는 경우 앱 암호를 만들어야 합니다. 자세한 내용은 [Azure AD Multi-Factor Authentication 최종 사용자 가이드](../user-help/multi-factor-authentication-end-user-first-time.md)를 참조하여 시작하는 데 도움을 받으세요.

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자별 MFA의 사용자를 조건부 액세스 기반 MFA로 변환

사용자가 사용자별로 적용된 Azure AD Multi-Factor Authentication을 사용하도록 설정했다면 다음 PowerShell을 사용하여 조건부 액세스 기반 Azure AD Multi-Factor Authentication으로 변환하는 데 도움을 받을 수 있습니다.

ISE 창에서 이 PowerShell을 실행하거나 로컬에 실행할 `.PS1` 파일로 저장합니다. [MSOnline 모듈](/powershell/module/msonline/?view=azureadps-1.0#msonline)을 통해서만 작업을 수행할 수 있습니다. 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="next-steps"></a>다음 단계

Azure AD Multi-Factor Authentication 설정을 구성하려면 [Azure AD Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md)을 참조하세요.

Azure AD Multi-Factor Authentication에 대한 사용자 설정을 관리하려면 [Azure AD Multi-Factor Authentication을 사용하여 사용자 설정 관리](howto-mfa-userdevicesettings.md)를 참조하세요.

사용자에게 MFA를 수행하라는 메시지가 표시되거나 또는 표시되지 않는 이유를 알아보려면 [Azure AD Multi-Factor Authentication 보고서](howto-mfa-reporting.md)를 참조하세요.