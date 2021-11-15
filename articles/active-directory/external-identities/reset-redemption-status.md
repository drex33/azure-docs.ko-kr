---
title: 게스트 사용자의 상환 상태 다시 설정 - Azure AD
description: Azure AD 외부 ID에서 Azure Active Directory B2B 게스트 사용자에 대한 초대 상환 상태를 다시 설정하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e19c4e6e13f4abcb8143f789dc1a7aa8de47e82
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129998963"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>게스트 사용자에 대한 상환 상태 다시 설정(미리 보기)

게스트 사용자가 B2B 협업에 대한 초대를 사용한 후에는 다음과 같은 경우에 로그인 정보를 업데이트해야 하는 경우가 있을 수 있습니다.

- 사용자가 다른 이메일 및 ID 공급자를 사용하여 로그인하려고 합니다.
- 해당 홈 테넌트에 있는 사용자 계정이 삭제되었다가 다시 생성되었습니다.
- 사용자가 다른 회사로 이동했지만 여전히 리소스에 대한 동일한 액세스 권한이 있어야 합니다.
- 사용자의 책임이 다른 사용자에게 전달되었습니다.

이전에 이러한 시나리오를 관리하려면 디렉터리에서 게스트 사용자의 계정을 수동으로 삭제하고 사용자를 다시 초대해야 했습니다. 이제 PowerShell 또는 Microsoft Graph 초대 API를 사용하여 사용자의 상환 상태를 다시 설정하고 사용자의 개체 ID, 그룹 멤버 자격 및 앱 할당을 유지하면서 사용자를 다시 초대할 수 있습니다. 사용자가 새 초대를 사용할 때 사용자의 UPN은 변경되지 않지만 사용자의 로그인 이름은 새 이메일로 변경됩니다. 그런 다음, 사용자는 새 이메일 또는 사용자 개체의 `otherMails` 속성에 추가한 이메일을 사용하여 로그인할 수 있습니다.

## <a name="use-the-azure-portal-to-reset-redemption-status"></a>Azure Portal을 사용하여 상환 상태 다시 설정

1. 디렉터리에 대한 전역 관리자 계정 또는 사용자 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **사용자** 를 선택합니다.
1. 목록에서 사용자의 이름을 선택하여 사용자의 프로필을 엽니다.
1. 사용자가 다른 이메일을 사용하여 로그인하려는 경우:
   - 페이지 맨 위에서 **편집** 아이콘을 선택합니다.
   - 연락처 **정보** 섹션의 **이메일** 아래에 새 이메일을 입력합니다.
   - **대체 이메일** 옆에 있는 **편집** 을 선택합니다. 목록의 대체 이메일을 새 이메일로 업데이트한 다음, **업데이트** 를 선택합니다.
   - 페이지 상단에서 **저장** 아이콘을 선택합니다.
1. **ID** 섹션의 **초대 수락됨** 에서 **(관리)** 를 선택합니다.
1. **상환 상태** 에서 **초대 상태를 다시 설정하시겠습니까?(미리 보기)** 옆에 있는 **예** 를 선택합니다.
1. **예** 를 선택하여 확인합니다.


## <a name="use-powershell-or-microsoft-graph-api-to-reset-redemption-status"></a>PowerShell 또는 Microsoft Graph API를 사용하여 상환 상태 다시 설정

### <a name="reset-the-email-address-used-for-sign-in"></a>로그인에 사용되는 이메일 주소 다시 설정

사용자가 다른 이메일을 사용하여 로그인하려는 경우:

1. 새 이메일 주소가 사용자 개체의 `mail` 또는 `otherMails` 속성에 추가되었는지 확인합니다. 
1.  `InvitedUserEmailAddress` 속성의 이메일 주소를 새 이메일 주소로 바꿉니다.
1. 아래 방법 중 하나를 사용하여 사용자의 상환 상태를 다시 설정합니다.

> [!NOTE]
>공개 미리 보기 중에는 두 가지 권장 사항이 있습니다.
>- 사용자의 이메일 주소를 새 주소로 다시 설정하는 경우 `mail` 속성을 설정하는 것이 좋습니다. 이렇게 하면 사용자는 초대에 있는 상환 링크를 사용하는 것 외에도 디렉터리에 로그인하여 초대를 상환할 수 있습니다.
>- B2B 게스트 사용자의 상태를 다시 설정하는 경우 사용자 컨텍스트에서 이 작업을 수행해야 합니다. 앱 전용 호출은 현재 지원되지 않습니다.
>
### <a name="use-powershell-to-reset-redemption-status"></a>PowerShell을 사용하여 상환 상태 다시 설정

최신 AzureADPreview PowerShell 모듈을 설치하고, `InvitedUserEmailAddress`를 새 이메일 주소로 설정하고, `ResetRedemption`을 `true`로 설정하여 새 초대를 만듭니다.

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

### <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Microsoft Graph API를 사용하여 상환 상태 다시 설정

[Microsoft Graph 초대 API](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)를 사용하여 `resetRedemption` 속성을 `true`로 설정하고 `invitedUserEmailAddress` 속성에 새 이메일 주소를 지정합니다.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용하여 Azure Active Directory B2B 협업 사용자 추가](customize-invitation-api.md#powershell)
- [Azure AD B2B 게스트 사용자의 속성](user-properties.md)
