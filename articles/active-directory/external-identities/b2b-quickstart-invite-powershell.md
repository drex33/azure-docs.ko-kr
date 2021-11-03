---
title: '빠른 시작: PowerShell을 사용하여 게스트 사용자 추가 - Azure AD'
description: 이 빠른 시작에서는 PowerShell을 사용하여 외부 Azure AD B2B 협업 사용자에게 초대를 보내는 방법을 알아봅니다.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/28/2018
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom: it-pro, seo-update-azuread-jan, mode-api
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42dd773bd88b7f51aa23fda8463a31ec2d41a6bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052487"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>빠른 시작: PowerShell을 사용하여 게스트 사용자 추가

Azure Active Directory B2B 협업을 사용하여 앱 및 서비스에 외부 파트너를 초대할 수 있는 다양한 방법이 있습니다. 이전 빠른 시작에서는 Azure Active Directory 관리 포털에서 게스트 사용자를 직접 추가하는 방법을 살펴보았습니다. PowerShell을 사용하여 게스트 사용자를 한 번에 한 명씩 또는 일괄로 추가할 수 있습니다. 이 빠른 시작에서는 New-AzureADMSInvitation 명령을 사용하여 게스트 사용자 한 명을 Azure 테넌트에 추가합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>필수 구성 요소

### <a name="powershell-module"></a>PowerShell 모듈
[Graph용 Azure AD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 Azure AD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)을 설치합니다.

### <a name="get-a-test-email-account"></a>테스트 메일 계정 가져오기

초대를 보낼 수 있는 테스트 메일 계정이 필요합니다. 계정은 조직 외부에서 가져와야 합니다. gmail.com 또는 outlook.com 주소와 같은 소셜 계정을 포함하여 모든 계정 유형을 사용할 수 있습니다.

## <a name="sign-in-to-your-tenant"></a>테넌트에 로그인

다음 명령을 실행하여 테넌트 도메인에 연결합니다.

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
`Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`)을 입력합니다.

메시지가 표시되면 자격 증명을 입력합니다.

## <a name="send-an-invitation"></a>초대 보내기

1. 테스트 메일 계정에 초대를 보내려면 다음 PowerShell 명령을 실행합니다(**“Sanda”** 및 **sanda\@fabrikam.com** 을 테스트 메일 계정 이름 및 메일 주소로 바꾸기). 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. 이 명령은 지정된 메일 주소에 초대를 보냅니다. 다음과 같이 표시되어야 하는 출력을 확인합니다.

   ![보류 중인 사용자 수락을 보여 주는 PowerShell 출력](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>사용자가 디렉터리에 있는지 확인

1. 초대된 사용자가 Azure AD에 추가되었는지 확인하려면 다음 명령을 실행합니다.
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. 출력에서 초대한 사용자가 *emailaddress*#EXT#\@*domain* 형식의 UPN(사용자 계정 이름)과 함께 나열되는지 확인합니다. 예를 들어 *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com* 에서 contoso.onmicrosoft.com은 초대를 보낸 조직입니다.

   ![추가된 게스트 사용자를 보여 주는 PowerShell 출력](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디렉터리에서 테스트 사용자 계정을 삭제할 수 있습니다. 다음 명령을 실행하여 사용자 계정을 삭제합니다.

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
예: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 PowerShell을 사용하여 디렉터리에 단일 게스트 사용자를 초대하고 추가했습니다. 다음으로, PowerShell을 사용하여 게스트 사용자를 일괄 초대하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Azure AD B2B 협업 사용자 일괄 초대](tutorial-bulk-invite.md)
