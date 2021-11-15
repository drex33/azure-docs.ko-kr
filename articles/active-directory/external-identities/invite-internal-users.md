---
title: 내부 사용자를 B2B 협업에 초대 - Azure AD
description: 파트너, 배포자, 공급자, 공급업체 및 기타 게스트에 대한 내부 사용자 계정이 있는 경우 자체 외부 자격 증명 또는 로그인을 사용하여 로그인하도록 초대하여 Azure AD B2B 협업으로 변경할 수 있습니다. PowerShell 또는 Microsoft Graph 초대 API를 사용합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/10/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b7e00a892398ed8a644d3a65e2d9b9338fd2fbd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780683"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>내부 사용자를 B2B 협업에 초대

Azure AD B2B 협업이 제공되기 전에는 조직이 배포자, 공급자, 공급업체 및 기타 게스트 사용자의 내부 자격 증명을 설정하여 이들과 협업할 수 있었습니다. 이와 같은 내부 게스트 사용자가 있는 경우 대신 B2B 협업을 사용하도록 초대할 수 있습니다. 이러한 B2B 게스트 사용자는 자체 ID와 자격 증명을 사용하여 로그인할 수 있기 때문에 귀사가 암호를 유지하거나 계정 수명 주기를 관리할 필요가 없습니다.

기존 내부 계정에 초대를 보내면 해당 사용자의 개체 ID, UPN, 그룹 멤버십 및 앱 할당을 유지할 수 있습니다. 사용자를 수동으로 삭제하고 다시 초대하거나 리소스를 재할당할 필요가 없습니다. 사용자를 초대하려면 초대 API를 사용하여 초대와 함께 내부 사용자 개체와 게스트 사용자의 이메일 주소를 둘 다 전달합니다. 사용자가 초대를 수락하면 B2B 서비스는 기존 내부 사용자 개체를 B2B 사용자로 변경합니다. 앞으로 사용자는 자신의 B2B 자격 증명을 사용하여 클라우드 리소스 서비스에 로그인해야 합니다.

## <a name="things-to-consider"></a>고려할 사항

- **온-프레미스 리소스에 대한 액세스**: 사용자가 B2B 협업에 초대된 후에도 자신의 내부 자격 증명을 사용하여 온-프레미스 리소스에 액세스할 수 있습니다. 내부 계정의 암호를 재설정하거나 변경하여 이를 방지할 수 있습니다. 단, [이메일 일회용 암호 인증](one-time-passcode.md)은 예외입니다. 사용자의 인증 방법이 일회성 암호로 변경되면 더 이상 내부 자격 증명을 사용할 수 없습니다.

- **청구**: 이 기능은 사용자의 UserType을 변경하지 않으므로 사용자의 청구 모델을 [외부 ID MAU(월간 활성 사용자) 가격 책정](external-identities-pricing.md)으로 자동 전환하지 않습니다. 사용자에 대한 MAU 가격 책정을 활성화하려면 사용자의 UserType을 `guest`로 변경합니다. 또한 MAU 청구를 활성화하려면 Azure AD 테넌트가 [Azure 구독에 연결](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription)되어야 합니다.

- **초대는 단방향임**: B2B 협업을 사용하도록 내부 사용자를 초대할 수 있지만 일단 추가되면 B2B 자격 증명을 제거할 수 없습니다. 사용자를 내부 전용 사용자로 다시 변경하려면 사용자 개체를 삭제하고 새로 만들어야 합니다.

- **Teams**: 사용자가 외부 자격 증명을 사용하여 Teams에 액세스할 때 처음에는 Teams 테넌트 선택기에서 해당 테넌트를 사용할 수 없습니다. 사용자는 테넌트 컨텍스트가 포함된 URL(예: `https://teams.microsoft.com/?tenantId=<TenantId>`)을 사용하여 Teams에 액세스할 수 있습니다. 그 후에 Teams 테넌트 선택기에서 테넌트를 사용할 수 있게 됩니다.

- **온-프레미스 동기화 사용자**: 온-프레미스와 클라우드 간에 동기화되는 사용자 계정의 경우, 온-프레미스 디렉터리는 B2B 협업을 사용하도록 초대된 후에도 권한 원본으로 유지됩니다. 온-프레미스 계정에 대한 모든 변경(계정 비활성화 또는 삭제 포함)은 클라우드 계정과 동기화됩니다. 따라서 온-프레미스 계정을 삭제하는 것만으로는 사용자가 자신의 클라우드 계정을 유지하면서 온-프레미스 계정에 로그인하는 것을 막을 수 없습니다. 대신 온-프레미스 계정 암호를 임의의 GUID 또는 기타 알 수 없는 값으로 설정할 수 있습니다.

> [!NOTE]
Azure AD Connect 동기화에는 사용자 개체에 [onPremisesUserPrincipalName 특성](../hybrid/reference-connect-sync-attributes-synchronized.md#notes)을 기록하는 기본 규칙이 있습니다. 이 특성이 있으면 사용자가 외부 자격 증명을 사용하여 로그인하지 못할 수 있으므로 이 특성을 사용하여 사용자 개체에 대한 내부에서 외부로의 변환을 차단합니다. Azure AD Connect를 사용 중이고 내부 사용자를 B2B 협업에 초대할 수 있도록 하려면 onPremisesUserPrincipalName 특성이 사용자 개체에 기록되지 않도록 [기본 규칙을 수정](../hybrid/how-to-connect-sync-change-the-configuration.md)해야 합니다.
## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>내부 사용자를 B2B 협업에 초대하는 방법

PowerShell 또는 초대 API를 사용하여 내부 사용자에게 B2B 초대를 보낼 수 있습니다. 초대에 사용할 이메일 주소가 내부 사용자 개체의 외부 이메일 주소로 설정되어 있는지 확인해야 합니다.

- 초대에는 User.Mail 속성의 이메일 주소를 사용해야 합니다.
- 사용자의 Mail 속성에 있는 도메인은 로그인에 사용하는 계정과 일치해야 합니다. 그렇지 않으면 Teams와 같은 일부 서비스에서 사용자를 인증할 수 없습니다.

기본적으로 초대는 사용자에게 초대되었음을 알리는 이메일을 보내지만 이 이메일을 표시하지 않고 대신 자체 이메일을 보낼 수 있습니다.

> [!NOTE]
> 자체 이메일 또는 기타 통신을 보내려면 `New-AzureADMSInvitation`을 `-SendInvitationMessage:$false`와 함께 사용하여 사용자를 자동으로 초대한 다음, 변환된 사용자에게 자체 이메일 메시지를 보낼 수 있습니다. [Azure AD B2B 협업 API 및 사용자 지정](customize-invitation-api.md)을 참조하세요.

## <a name="use-powershell-to-send-a-b2b-invitation"></a>PowerShell을 사용하여 B2B 초대 보내기

Azure AD PowerShell 모듈 버전 2.0.2.130 이상이 필요합니다. 다음 명령을 사용하여 최신 AzureAD PowerShell 모듈로 업데이트하고 내부 사용자를 B2B 협업에 초대합니다.

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>초대 API를 사용하여 B2B 초대 보내기

아래 샘플은 초대 API를 호출하여 내부 사용자를 B2B 사용자로 초대하는 방법을 보여줍니다.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

API에 대한 응답은 새 게스트 사용자를 디렉터리에 초대할 때 받는 것과 동일한 응답입니다.
## <a name="next-steps"></a>다음 단계

- [B2B 협업 초대 상환](redemption-experience.md)
