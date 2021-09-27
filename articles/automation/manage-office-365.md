---
title: Azure Automation을 사용하여 Office 365 서비스 관리
description: 이 문서에서는 Azure Automation을 사용하여 Office 365 구독 서비스를 관리하는 방법을 설명합니다.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: f61149fd9c35cc55a3ed55fa83e6031d895fb073
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737741"
---
# <a name="manage-office-365-services"></a>Office 365 서비스 관리

Microsoft Word, Microsoft Outlook 등의 제품에 Azure Automation을 사용하여 Office 365 구독 서비스를 관리할 수 있습니다. Office 365와의 상호 작용은 [Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md)를 사용하여 설정됩니다. [Azure Automation에서 Azure AD를 사용하여 Azure에 인증](automation-use-azure-ad.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure Automation에서 Office 365 구독 서비스를 관리하려면 다음이 필요합니다.

* Azure 구독 [구독 관련 결정 가이드](/azure/cloud-adoption-framework/decision-guides/subscriptions/)를 참조하세요.
* 사용자 계정 자격 증명과 Runbook을 보관할 Azure의 Automation 개체. [Azure Automation에 대한 소개](./automation-intro.md)를 참조하세요.
* Azure AD. [Azure Automation에서 Azure AD를 사용하여 Azure에 인증](automation-use-azure-ad.md)을 참조하세요.
* 계정을 사용하는 Office 365 테넌트. [Office 365 테넌트 설정](/sharepoint/dev/spfx/set-up-your-developer-tenant)을 참조하세요.

## <a name="install-the-msonline-and-msonlineext-modules"></a>MSOnline 및 MSOnlineExt 모듈 설치

Azure Automation 내에서 Office 365를 사용하려면 Windows PowerShell용 Microsoft Azure Active Directory(`MSOnline` 모듈)가 필요합니다. 단일 및 다중 테넌트 환경에서 Azure AD 관리를 간소화하는 [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) 모듈도 필요합니다. [Azure Automation에서 Azure AD를 사용하여 Azure에 인증](automation-use-azure-ad.md)에 설명된 대로 모듈을 설치합니다.

>[!NOTE]
>MSOnline PowerShell을 사용하려면 Azure AD의 구성원이어야 합니다. 게스트 사용자는 이 모듈을 사용할 수 없습니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기

이 문서의 단계를 완료하려면 Azure Automation 계정이 필요합니다. [Azure Automation 계정 만들기](./quickstarts/create-account-portal.md)를 참조하세요.
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>MSOnline 및 MSOnlineExt를 자산으로 추가

이제 설치된 MSOnline 및 MSOnlineExt 모듈을 추가하여 Office 365 기능을 사용하도록 설정합니다. [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조하세요.

1. Azure Portal에서 **Automation 계정** 을 선택합니다.
2. 자신의 Automation 계정을 선택합니다.
3. **공유 리소스** 에서 **모듈 갤러리** 를 선택합니다.
4. MSOnline을 검색합니다.
5. `MSOnline` PowerShell 모듈을 선택하고 **가져오기** 를 클릭하여 모듈을 자산으로 가져옵니다.
6. 4단계와 5단계를 반복하여 `MSOnlineExt` 모듈을 찾아서 가져옵니다.

## <a name="create-a-credential-asset-optional"></a>자격 증명 자산 만들기(선택 사항)

스크립트를 실행할 권한이 있는 Office 365 관리자에 대한 자격 증명 자산을 만드는 것은 선택 사항입니다. 하지만 이렇게 하면 PowerShell 스크립트 내에서 사용자 이름과 암호가 노출되지 않도록 방지하는 데 도움이 될 수 있습니다. 자세한 지침은 [자격 증명 자산 만들기](automation-use-azure-ad.md#create-a-credential-asset)를 참조하세요.

## <a name="create-an-office-365-service-account"></a>Office 365 서비스 계정 만들기

Office 365 구독 서비스를 실행하려면 원하는 작업을 수행할 수 있는 권한이 있는 Office 365 서비스 계정이 필요합니다. 전역 관리자 계정 하나(서비스당 계정 하나)를 사용할 수도 있고, 함수 또는 스크립트 하나를 실행할 수도 있습니다. 어떤 경우든, 서비스 계정의 암호는 복잡하고 안전해야 합니다. [비즈니스용 Office 365 설치](/microsoft-365/admin/setup/setup)를 참조하세요.

## <a name="connect-to-the-azure-ad-online-service"></a>Azure AD 온라인 서비스에 연결

>[!NOTE]
>MSOnline 모듈 cmdlet을 사용하려면 Windows PowerShell에서 실행해야 합니다. PowerShell Core는 이 cmdlet을 지원하지 않습니다.

MSOnline 모듈을 사용하여 Office 365 구독에서 Azure AD에 연결할 수 있습니다. 연결에는 Office 365 사용자 이름과 암호 또는 MFA(Multi-Factor Authentication)가 사용됩니다. Azure Portal 또는 Windows PowerShell 명령 프롬프트를 사용하여 연결할 수 있습니다(높은 권한이 아니어도 됨).

다음은 PowerShell 예제입니다. [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) cmdlet이 자격 증명을 요청하여 `Msolcred` 변수에 저장합니다. 그런 다음, [Connect-MsolService](/powershell/module/msonline/connect-msolservice) cmdlet이 자격 증명을 사용하여 Azure 디렉터리 온라인 서비스에 연결합니다. 특정 Azure 환경에 연결하려면 `AzureEnvironment` 매개 변수를 사용합니다.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

오류가 수신되지 않으면 성공적으로 연결된 것입니다. Office 365 cmdlet(예: `Get-MsolUser`)을 실행하고 결과를 확인하면 더 빨리 테스트할 수 있습니다. 오류가 수신되는 가장 흔한 이유는 잘못된 암호입니다.

>[!NOTE]
>AzureRM 모듈 또는 Az 모듈을 사용하여 Office 365 구독에서 Azure AD에 연결할 수도 있습니다. 기본 연결 cmdlet은 [Connect-AzureAD](/powershell/module/azuread/connect-azuread)입니다. 이 cmdlet은 특정 Office 365 환경에 `AzureEnvironmentName` 매개 변수를 지원합니다.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>기존 스크립트에서 PowerShell Runbook 만들기

Office 365 기능은 PowerShell 스크립트에서 액세스합니다. 다음 예제는 사용자 이름이 `admin@TenantOne.com`인 `Office-Credentials`라는 자격 증명에 대한 스크립트입니다. 이 예제에서는 `Get-AutomationPSCredential`을 사용하여 Office 365 자격 증명을 가져옵니다.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Runbook에서 스크립트 실행

Azure Automation Runbook에서 스크립트를 사용할 수 있습니다. 예시를 들기 위해 여기서는 PowerShell Runbook을 사용합니다.

1. 새 PowerShell Runbook을 만듭니다. [Azure Automation Runbook 만들기](./learn/powershell-runbook-managed-identity.md)를 참조하세요.
2. Automation 계정의 **프로세스 자동화** 에서 **Runbook** 을 선택합니다.
3. 새 Runbook을 선택하고 **편집** 을 클릭합니다.
4. 스크립트를 복사하여 Runbook의 텍스트 편집기에 붙여넣습니다.
5. **자산** 을 선택한 다음, **자격 증명** 을 확장하고 Office 365 자격 증명이 있는지 확인합니다.
6. **저장** 을 클릭합니다.
7. **테스트 창** 을 선택한 다음, **시작** 을 클릭하여 Runbook 테스트를 시작합니다. [Azure Automation에서 Runbook 관리](./manage-runbooks.md)를 참조하세요.
8. 테스트가 완료되면 [테스트] 창에서 나옵니다.

## <a name="publish-and-schedule-the-runbook"></a>Runbook 게시 및 예약

Runbook을 게시한 후 예약하려면 [Azure Automation에서 Runbook 관리](./manage-runbooks.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 자격 증명 사용에 대한 자세한 내용은 [Azure Automation에서 자격 증명 관리](shared-resources/credentials.md)를 참조하세요.
* 모듈에 대한 자세한 내용은 [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조하세요.
* Runbook을 시작하려면 [Azure Automation에서 Runbook 시작](start-runbooks.md)을 참조하세요.
* PowerShell에 대한 자세한 내용은 [PowerShell 설명서](/powershell/scripting/overview)를 참조하세요.