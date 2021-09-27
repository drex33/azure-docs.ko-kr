---
title: Azure Automation에서 Azure AD를 사용하여 Azure에 인증
description: 이 문서에서는 Azure Automation 내에서 Azure 인증 공급자로서 Azure AD를 사용하는 방법을 설명합니다.
services: automation
ms.date: 09/23/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a5bb9b4d8235d48f47b34613c78d68f8bd3a2435
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080001"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Azure AD를 사용하여 Azure에 인증

[Azure AD(Active Directory)](../active-directory/fundamentals/active-directory-whatis.md) 서비스를 사용하면 사용자 관리, 도메인 관리, Single Sign-On 구성 등의 다양한 관리 작업을 수행할 수 있습니다. 이 문서에서는 Azure Automation 내에서 Azure 인증 공급자로서 Azure AD를 사용하는 방법을 설명합니다. 

## <a name="install-azure-ad-modules"></a>Azure AD 모듈 설치

다음 PowerShell 모듈을 통해 Azure AD를 사용하도록 설정할 수 있습니다.

* Graph용 Azure Active Directory PowerShell(AzureRM 및 Az 모듈). Azure Automation은 AzureRM 모듈 및 최신 업그레이드인 Az 모듈과 함께 제공됩니다. 기능으로는 Azure AD 사용자(OrgId) 자격 증명 기반 인증을 사용하는 Azure에 대한 비대화형 인증이 포함됩니다. [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)을 참조하세요.

* Windows PowerShell용 Microsoft Azure Active Directory(MSOnline 모듈). 이 모듈이 있으면 Microsoft 365를 포함하여 Microsoft Online을 사용할 수 있습니다.

>[!NOTE]
>PowerShell Core는 MSOnline 모듈을 지원하지 않습니다. 모듈 cmdlet을 사용하려면 Windows PowerShell에서 실행해야 합니다. MSOnline 모듈 대신 최신 Graph용 Azure Active Directory PowerShell 모듈을 사용하는 것이 좋습니다. 

### <a name="preinstallation"></a>사전 설치

컴퓨터에 Azure AD 모듈을 설치하기 전에 다음을 수행합니다.

* 이전 버전의 AzureRM/Az 모듈과 MSOnline 모듈을 제거합니다. 

* 새 PowerShell 모듈이 올바르게 작동할 수 있도록 Microsoft Online Services 로그인 도우미를 제거합니다.  

### <a name="install-the-azurerm-and-az-modules"></a>AzureRM 및 Az 모듈 설치

>[!NOTE]
>이러한 모듈로 작업하려면 64비트 버전 Windows에서 PowerShell 버전 5.1 이상을 사용해야 합니다. 

1. WMF(Windows Management Framework) 5.1을 설치합니다. [WMF 5.1 설치 및 구성](/powershell/scripting/wmf/setup/install-configure)을 참조하세요.

2. [PowerShellGet을 사용하여 Windows에 Azure PowerShell 설치](/powershell/azure/azurerm/install-azurerm-ps)의 지침에 따라 AzureRM 및/또는 Az 모듈을 설치합니다.

### <a name="install-the-msonline-module"></a>MSOnline 모듈 설치

>[!NOTE]
>MSOnline 모듈을 설치하려면 관리자 역할의 멤버여야 합니다. [관리자 역할 정보](/microsoft-365/admin/add-users/about-admin-roles)를 참조하세요.

1. 컴퓨터에서 Microsoft .NET Framework 3.5.x 기능을 사용하도록 설정되었는지 확인합니다. 컴퓨터에 최신 버전이 설치되었을 확률이 높지만, .NET Framework 이전 버전과의 호환성을 사용하도록 또는 사용하지 않도록 설정할 수 있습니다. 

2. 64비트 버전의 [Microsoft Online Services 로그인 도우미](/microsoft-365/enterprise/connect-to-microsoft-365-powershell?view=o365-worldwide&preserve-view=true#step-1-install-the-required-software-1)를 설치합니다.

3. 관리자 권한으로 Windows PowerShell을 실행하여 높은 권한 Windows PowerShell 명령 프롬프트를 만듭니다.

4. [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0)의 Azure Active Directory를 배포합니다.

5. NuGet 공급자를 설치할 것인지 묻는 메시지가 표시되면 Y를 입력하고 ENTER 키를 누릅니다.

6. [PSGallery](https://www.powershellgallery.com/)의 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 ENTER 키를 누릅니다.

### <a name="install-support-for-pscredential"></a>PSCredential에 대한 지원 설치

Azure Automation은 [PSCredential](/dotnet/api/system.management.automation.pscredential) 클래스를 사용하여 자격 증명 자산을 표시합니다. 스크립트는 `Get-AutomationPSCredential` cmdlet을 사용하여 `PSCredential` 개체를 검색합니다. 자세한 내용은 [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)을 참조하세요.

## <a name="assign-a-subscription-administrator"></a>구독 관리자 할당

Azure 구독의 관리자를 할당해야 합니다. 이 사람은 구독 범위에서 소유자 역할을 갖습니다. [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요. 

## <a name="change-the-azure-ad-users-password"></a>Azure AD 사용자 암호 변경

Azure AD 사용자 암호를 변경하는 방법은 다음과 같습니다.

1. Azure에서 로그아웃합니다.

2. 관리자에게 방금 만든 Azure AD 사용자로 전체 사용자 이름(도메인 포함)과 임시 암호를 사용하여 Azure에 로그인해 달라고 요청합니다. 

3. 메시지가 표시되면 관리자에게 암호를 변경해 달라고 요청합니다.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Azure 구독을 관리하도록 Azure Automation 구성

Azure Automation이 Azure AD와 통신하려면 Azure AD에 대한 Azure 연결과 관련된 자격 증명을 검색해야 합니다. 이러한 자격 증명의 예로는 테넌트 ID, 구독 ID 등이 있습니다. Azure와 Azure AD 간의 연결에 대한 자세한 내용은 [조직을 Azure Active Directory에 연결](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)을 참조하세요.

## <a name="create-a-credential-asset"></a>자격 증명 자산 만들기

Azure AD에 사용할 Azure 자격 증명이 생겼으므로, 이번에는 Runbook 및 DSC(Desired State Configuration) 스크립트에서 액세스할 수 있도록 Azure AD 자격 증명을 안전하게 저장하는 Azure Automation 자격 증명 자산을 만들 차례입니다. 이 작업은 Azure Portal 또는 PowerShell cmdlet을 사용하여 수행할 수 있습니다.

### <a name="create-the-credential-asset-in-azure-portal"></a>Azure Portal에서 자격 증명 자산 만들기

Azure Portal을 사용하여 자격 증명 자산을 만들 수 있습니다. Automation 계정에서 **공유 리소스** 아래의 **자격 증명** 을 사용하여 이 작업을 수행합니다. [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)을 참조하세요.

### <a name="create-the-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용하여 자격 증명 자산 만들기

Windows PowerShell에서 새 자격 증명 자산을 준비하기 위해, 스크립트에서는 할당된 사용자 이름 및 암호를 사용하여 `PSCredential` 개체부터 만듭니다. 그 후 스크립트에서 이 개체를 사용하여 [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) cmdlet을 호출하는 방법으로 자산을 만듭니다. 또는 스크립트에서 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) cmdlet을 호출하여 사용자에게 이름 및 암호를 입력하라는 메시지를 표시할 수도 있습니다. [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)을 참조하세요. 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Azure Automation Runbook에서 Azure 리소스 관리

Azure Automation Runbook에서 자격 증명 자산을 사용하여 Azure 리소스를 관리할 수 있습니다. 다음은 Azure 구독에서 가상 머신을 중지하고 시작하는 데 사용할 자격 증명 자산을 수집하는 PowerShell Runbook 예제입니다. 이 Runbook은 먼저 `Get-AutomationPSCredential`을 사용하여 Azure 인증에 사용할 자격 증명을 검색합니다. 그런 다음, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 호출하고 자격 증명을 사용하여 Azure에 연결합니다. 

```powershell
Workflow Workflow
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process

    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext 

    # get credential
    $credential = Get-AutomationPSCredential -Name "AzureCredential"

    # Connect to Azure with credential
    $AzureContext = (Connect-AzAccount -Credential $credential -TenantId $AzureContext.Subscription.TenantId).context 

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
        -TenantId $AzureContext.Subscription.TenantId `
        -DefaultProfile $AzureContext
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM -DefaultProfile $AzureContext).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM -DefaultProfile $AzureContext | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM -DefaultProfile $AzureContext | ? {$_.Name -eq $AzureVM} | Stop-AzVM -DefaultProfile $AzureContext -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM -DefaultProfile $AzureContext | ? {$_.Name -eq $AzureVM} | Start-AzVM -DefaultProfile $AzureContext
        } 
    } 
}
```  

## <a name="next-steps"></a>다음 단계

* 자격 증명 사용에 대한 자세한 내용은 [Azure Automation에서 자격 증명 관리](shared-resources/credentials.md)를 참조하세요.
* 모듈에 대한 자세한 내용은 [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조하세요.
* Runbook을 시작하려면 [Azure Automation에서 Runbook 시작](start-runbooks.md)을 참조하세요.
* PowerShell에 대한 자세한 내용은 [PowerShell 설명서](/powershell/scripting/overview)를 참조하세요.
