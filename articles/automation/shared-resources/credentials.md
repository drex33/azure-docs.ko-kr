---
title: Azure Automation에서 자격 증명 관리
description: 이 문서에서는 자격 증명 자산을 만들고 Runbook 또는 DSC 구성에 사용하는 방법을 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5a33bac9db69320df6e2e6d0e02361cb9aa0cdab
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432513"
---
# <a name="manage-credentials-in-azure-automation"></a>Azure Automation에서 자격 증명 관리

Automation 자격 증명 자산은 사용자 이름과 암호 등의 보안 자격 증명을 포함하는 개체를 보유합니다. Runbook 및 DSC 구성에서는 인증을 위해 [PSCredential](/dotnet/api/system.management.automation.pscredential) 개체를 수락하는 cmdlet을 사용합니다. 또는 인증이 필요한 일부 애플리케이션이나 서비스에 제공할 `PSCredential` 개체의 사용자 이름과 암호를 추출할 수 있습니다.

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. Azure Automation은 시스템 관리 키 자격 증명 모음에 키를 저장합니다. 보안 자산을 저장하기 전에 Automation이 Key Vault에서 키를 로드한 다음, 자산을 암호화하는 데 사용합니다. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>자격 증명에 액세스하는 데 사용되는 PowerShell cmdlet

다음 표에 나와 있는 cmdlet은 PowerShell을 사용하여 Automation 자격 증명을 만들고 관리합니다. Az 모듈의 일부로 제공됩니다.

| Cmdlet | Description |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |자격 증명에 대한 메타데이터를 포함하는 [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) 개체를 검색합니다. Cmdlet은 `PSCredential` 개체 자체를 검색하지 않습니다.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |새 Automation 자격 증명을 만듭니다. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Automation 자격 증명을 제거합니다. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |기존 Automation 자격 증명에 대한 속성을 설정합니다. |

## <a name="other-cmdlets-used-to-access-credentials"></a>자격 증명에 액세스하는 데 사용되는 기타 cmdlet

다음 표의 cmdlet은 Runbook 및 DSC 구성에서 자격 증명에 액세스하는 데 사용됩니다. 

| Cmdlet | Description |
|:--- |:--- |
| `Get-AutomationPSCredential` |Runbook 또는 DSC 구성에 사용하는 `PSCredential` 개체를 가져옵니다. `Get-AzAutomationCredential` cmdlet은 자격 증명 정보만 검색하기 때문에 대개 이 [내부 cmdlet](modules.md#internal-cmdlets)을 대신 사용합니다. 이 정보는 일반적으로 다른 cmdlet에 전달하는 데 유용하지 않습니다. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |사용자 이름 및 암호에 대한 프롬프트로 자격 증명을 가져옵니다. 이 cmdlet은 기본 Microsoft.PowerShell.Security 모듈의 일부입니다. [기본 모듈](modules.md#default-modules)을 참조하세요.|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | 자격 증명을 만듭니다. 이 cmdlet은 기본 Azure 모듈의 일부입니다. [기본 모듈](modules.md#default-modules)을 참조하세요.|

코드에서 `PSCredential` 개체를 검색하려면 `Orchestrator.AssetManagement.Cmdlets` 모듈을 가져와야 합니다. 자세한 내용은 [Azure Automation에서 모듈 관리](modules.md)를 참조하세요.

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> `Get-AutomationPSCredential`의 `Name` 매개 변수에는 변수를 사용하면 안 됩니다. 변수를 사용하면 디자인 타임에 Runbook 또는 DSC 구성과 자격 증명 자산 간의 종속성 검색이 복잡해질 수 있습니다.

## <a name="python-functions-that-access-credentials"></a>자격 증명에 액세스하는 Python 함수

다음 표의 함수는 Python 2 및 3 Runbook의 자격 증명에 액세스하는 데 사용됩니다. Python 3 Runbook은 현재 미리 보기로 제공됩니다.

| 함수 | Description |
|:---|:---|
| `automationassets.get_automation_credential` | 자격 증명 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 함수에 액세스하려면 Python Runbook 맨 위에서 `automationassets` 모듈을 가져옵니다.

## <a name="create-a-new-credential-asset"></a>새 자격 증명 자산 만들기

Microsoft Azure Portal 또는 Windows PowerShell을 사용하여 새 자격 증명 자산을 만들 수 있습니다.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Microsoft Azure Portal을 사용하여 새 자격 증명 자산 만들기

1. Automation 계정의 왼쪽 창에서 **공유 리소스** 아래에 있는 **자격 증명** 을 선택합니다.
2. **자격 증명** 페이지에서 **자격 증명 추가** 를 선택합니다.
3. 새 자격 증명 창에서 이름 지정 표준에 따라 적절한 자격 증명 이름을 입력합니다.
4. **사용자 이름** 필드에 액세스 ID를 입력합니다.
5. 두 암호 필드 모두에 비밀 액세스 키를 입력합니다.

    ![새 자격 증명 만들기](../media/credentials/credential-create.png)

6. 다단계 인증 확인란이 선택 되어 있으면 선택을 취소 합니다.
7. **만들기** 를 클릭하여 새 자격 증명 자산을 저장합니다.

> [!NOTE]
> Azure Automation는 다단계 인증을 사용 하는 사용자 계정을 지원 하지 않습니다.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 자격 증명 자산 만들기

다음 예제에서는 새 Automation 자격 증명 자산을 만드는 방법을 보여줍니다. 먼저 이름 및 암호를 사용하여 `PSCredential` 개체를 만든 다음, 이를 사용하여 자격 증명 자산을 만듭니다. 대신 `Get-Credential` cmdlet을 사용하여 사용자에게 이름과 암호를 입력하라는 메시지를 표시할 수 있습니다.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>자격 증명 자산 가져오기

Runbook 또는 DSC 구성은 내부 `Get-AutomationPSCredential` cmdlet을 사용하여 자격 증명 자산을 검색합니다. 이 cmdlet은 자격 증명이 필요한 cmdlet에 사용할 수 있는 `PSCredential` 개체를 가져옵니다. 자격 증명 개체의 속성을 검색하여 개별적으로 사용할 수도 있습니다. 개체에는 사용자 이름과 보안 암호에 대한 속성이 있습니다. 

> [!NOTE]
> `Get-AzAutomationCredential` cmdlet은 인증에 사용할 수 있는 `PSCredential` 개체를 검색하지 않습니다. 자격 증명에 대한 정보만 제공합니다. Runbook에서 자격 증명을 사용해야 하는 경우 `Get-AutomationPSCredential`을 사용하여 `PSCredential` 개체로 검색해야 합니다.

또는 [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) 메서드를 사용하여 보안되지 않은 버전의 암호를 나타내는 [NetworkCredential](/dotnet/api/system.net.networkcredential) 개체를 검색할 수 있습니다.

### <a name="textual-runbook-example"></a>텍스트 Runbook 예제

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 예제에서는 Runbook에서 PowerShell 자격 증명을 사용하는 방법을 보여줍니다. 자격 증명을 검색하고 해당 사용자 이름과 암호를 변수에 할당합니다.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

자격 증명을 사용 하 여 먼저 [관리 id](../automation-security-overview.md#managed-identities)로 연결한 후에 [커넥트-AzAccount](/powershell/module/az.accounts/connect-azaccount) 로 Azure에 인증할 수도 있습니다. 이 예에서는 [시스템 할당 관리 id](../enable-managed-identity-for-automation.md)를 사용 합니다.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Get credential
$myCred = Get-AutomationPSCredential -Name "MyCredential"
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

# Connect to Azure with credential
$AzureContext = (Connect-AzAccount -Credential $myPsCred -TenantId $AzureContext.Subscription.TenantId).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -TenantId $AzureContext.Subscription.TenantId `
    -DefaultProfile $AzureContext
```

# <a name="python-2"></a>[Python 2](#tab/python2)

다음 예제에서는 Python 2 Runbook의 자격 증명에 액세스하는 예를 보여줍니다.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

다음 예제에서는 Python 3 Runbook(미리 보기)의 자격 증명에 액세스하는 예를 보여줍니다.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>그래픽 Runbook 예제

그래픽 편집기의 라이브러리 창에서 자격 증명을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가** 를 선택하여 내부 `Get-AutomationPSCredential` cmdlet에 대한 활동을 그래픽 Runbook에 추가할 수 있습니다.

![캔버스에 자격 증명 cmdlet 추가](../media/credentials/credential-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 자격 증명을 사용하는 예제를 보여 줍니다. 이 경우 자격 증명은 [Azure Automation에서 Microsoft Azure Active Directory를 사용하여 Azure에 인증](../automation-use-azure-ad.md)에 설명된 대로 Runbook에 대한 인증을 Azure 리소스에 제공합니다. 첫 번째 활동에서는 Azure 구독에 액세스할 수 있는 자격 증명을 검색합니다. 그런 다음, 계정 연결 활동에서 이 자격 증명을 사용하여 이후의 모든 활동에 대한 인증을 제공합니다. `Get-AutomationPSCredential`에는 단일 개체가 필요하기 때문에 [파이프라인 링크](../automation-graphical-authoring-intro.md#use-links-for-workflow)가 여기에 사용됩니다.  

![파이프라인 링크를 사용하는 자격 증명 워크플로 예제](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>DSC 구성에서 자격 증명 사용

Azure Automation의 DSC 구성은 `Get-AutomationPSCredential`을 사용하여 자격 증명 자산에서 작동할 수 있지만 매개 변수를 통해 자격 증명 자산을 전달할 수도 있습니다. 자세한 내용은 [Azure Automation DSC에서 구성을 컴파일](../automation-dsc-compile.md#credential-assets)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 인증서에 액세스하는 데 사용되는 cmdlet에 대한 자세한 내용은 [Azure Automation에서 모듈 관리](modules.md)를 참조하세요.
* Runbook에 대한 일반적인 정보는 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)을 참조하세요.
* DSC 구성에 대한 자세한 내용은 [Azure Automation State Configuration 개요](../automation-dsc-overview.md)를 참조하세요.
