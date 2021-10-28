---
title: Azure AD Multi-Factor Authentication용 인증 방법 관리 - Azure Active Directory
description: Azure AD Multi-Factor Authentication에 대한 Azure Active Directory 사용자 설정을 구성할 수 있는 방법에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd69e65f5c280eaac8acb41138cf265be0af113
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763028"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication용 사용자 인증 방법 관리

Azure AD의 사용자에게는 두 가지 연락처 정보 집합이 있습니다.  

- 퍼블릭 프로필 연락처 정보는 사용자 프로필에서 관리되며 조직의 구성원이 볼 수 있습니다. 온-프레미스 Active Directory에서 동기화된 사용자의 경우 온-프레미스 Windows Server Active Directory Domain Services에서 정보가 관리됩니다.
- 인증 방법은 항상 프라이빗 상태로 유지되며 MFA(다단계 인증)를 비롯하여 인증에만 사용됩니다. 관리자는 사용자의 인증 방법 블레이드에서 인증 방법을 관리할 수 있으며, 사용자는 내 계정의 보안 정보 페이지에서 인증 방법을 관리할 수 있습니다.

사용자의 Azure AD Multi-Factor Authentication 방법을 관리할 때 인증 관리자는 다음을 수행할 수 있습니다. 

1. MFA에 사용되는 전화번호를 포함하여 특정 사용자에 대한 인증 방법을 추가합니다.
1. 사용자 암호를 다시 설정합니다.
1. 사용자가 MFA에 다시 등록해야 합니다.
1. 기존 MFA 세션을 철회합니다.
1. 사용자의 기존 앱 암호를 삭제합니다.  

## <a name="add-authentication-methods-for-a-user"></a>사용자에 대한 인증 방법 추가 

Azure Portal 또는 Microsoft Graph를 통해 사용자 인증 방법을 추가할 수 있습니다.  

> [!NOTE]
> 보안상의 이유로 퍼블릭 사용자 연락처 정보 필드는 MFA를 수행하는 데 사용할 수 없습니다. 대신 사용자는 MFA에 사용할 인증 방법 번호를 채워야 합니다.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Azure Portal에서 인증 방법 추가":::

Azure Portal을 통해 사용자 인증 방법을 추가하는 방법은 다음과 같습니다.  

1. **Azure Portal** 에 로그인합니다. 
1. **Azure Active Directory** > **사용자** > **모든 사용자** 로 차례로 이동합니다. 
1. 인증 방법을 추가하려는 사용자를 선택하고 **인증 방법** 을 선택합니다.  
1. 창 맨 위에서 **+ 인증 방법 추가** 를 선택합니다.
   1. 방법(전화번호 또는 메일)을 선택합니다. 메일은 자동 암호 재설정에는 사용할 수 있지만 인증에는 사용할 수 없습니다. 전화번호를 추가할 때는 전화 유형을 선택하고 유효한 형식의 전화번호(예: + 1 4255551234)를 입력합니다.
   1. **추가** 를 선택합니다.

> [!NOTE]
> 관리자는 미리 보기 환경을 통해 사용자에게 사용 가능한 인증 방법을 추가할 수 있으며 원래 환경에서는 전화와 대체 전화 방법의 업데이트만 허용됩니다.

### <a name="manage-methods-using-powershell"></a>PowerShell 사용을 통한 ACL 관리  

다음 명령을 사용하여 Microsoft.Graph.Identity.Signins PowerShell 모듈을 설치합니다. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

특정 사용자에 대한 전화 기반 인증 방법을 나열합니다.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

특정 사용자에 대한 휴대폰 인증 방법을 만듭니다.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

사용자에 대한 특정 전화 방법을 제거합니다.

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Microsoft Graph API를 사용하여 인증 방법을 관리할 수도 있습니다. 자세한 내용은 [Azure AD 인증 방법 API 개요](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) 문서에서 찾을 수 있습니다.

## <a name="manage-user-authentication-options"></a>사용자 인증 옵션 관리

‘인증 관리자’ 역할이 할당된 경우 사용자에게 암호를 재설정하고 MFA에 다시 등록하거나 사용자 개체에서 기존 MFA 세션을 철회하도록 요구할 수 있습니다. 사용자 설정을 관리하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 선택합니다.
1. 작업 대상 사용자를 선택하고 **인증 방법** 을 선택합니다. 그런 다음, 창의 맨 위에서 사용자에 대해 다음 옵션 중 하나를 선택합니다.
   - **암호 재설정** 사용자의 암호를 재설정하고 다음 로그인 시 변경해야 할 임시 암호를 할당합니다.
   - **MFA 재등록 요구** 사용자가 다음번에 로그인할 때 새 MFA 인증 방법을 설정하도록 요청합니다.
   
      > [!NOTE]
      > 관리자가 MFA 재등록을 요구하는 경우 사용자의 현재 등록된 인증 방법은 삭제되지 않습니다. 사용자가 MFA에 다시 등록한 후에는 보안 정보를 검토하고, 이전에 등록되어 더 이상 사용할 수 없는 인증 방법을 삭제하는 것이 좋습니다.
   
   - **MFA 세션 철회** 사용자가 기억하고 있는 MFA 세션을 지우고 디바이스에서 정책에 따라 다음에 필요할 때 MFA를 수행하도록 요구합니다.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Azure Portal에서 인증 방법 관리":::

## <a name="delete-users-existing-app-passwords"></a>사용자 기존 앱 암호 삭제

앱 암호를 정의한 사용자의 경우 관리자는 암호를 삭제하여 레거시 인증이 해당 애플리케이션에서 실패하도록 할 수도 있습니다. 사용자에게 지원을 제공하거나 인증 방법을 재설정해야 하는 경우에 필요할 수 있는 작업입니다. 앱 암호와 연결되었던 브라우저가 아닌 앱은 새 앱 암호가 생성될 때까지 작동 중지됩니다. 

사용자의 앱 암호를 삭제하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 선택합니다.
1. **Multi-Factor Authentication** 을 선택합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 아래의 예제 스크린샷을 선택하면 전체 Azure Portal 창과 메뉴 위치를 확인할 수 있습니다. [ ![ Azure AD의 사용자 창에서 Multi-Factor Authentication을 선택합니다.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 빠른 단계 옵션 목록이 오른쪽에 표시됩니다.
1. **사용자 설정 관리** 를 선택한 다음, 다음 예제와 같이 **선택한 사용자가 생성한 모든 기존 앱 암호 삭제** 상자를 선택합니다. ![모든 기존 앱 암호 삭제](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. **저장**, **닫기** 를 차례로 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 개별 사용자 설정을 구성하는 방법을 살펴봤습니다. 전체 Azure AD Multi-Factor Authentication 서비스 설정을 구성하려면 [Azure AD Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md)을 참조하세요.

사용자에게 도움이 필요한 경우 [Azure AD Multi-Factor Authentication 사용자 가이드](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)를 참조하세요.