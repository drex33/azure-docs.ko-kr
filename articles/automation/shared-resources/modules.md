---
title: Azure Automation에서 모듈 관리
description: 이 문서에서는 PowerShell 모듈을 사용하여 DSC 구성의 Runbook 및 DSC 리소스에서 cmdlet을 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9fc7a8d5b27da251f13f2c9dfeffa03f7cdbd149
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452562"
---
# <a name="manage-modules-in-azure-automation"></a>Azure Automation에서 모듈 관리

Azure Automation은 많은 PowerShell 모듈을 사용하여 DSC 구성에서 Runbook 및 DSC 리소스의 cmdlet을 사용하도록 설정합니다. 지원되는 모듈은 다음과 같습니다.

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az)
* [Azure PowerShell AzureRM.Automation](/powershell/module/azurerm.automation/)
* 기타 Azure PowerShell 모듈
* 내부 `Orchestrator.AssetManagement.Cmdlets` 모듈
* Python 2 모듈
* 사용자가 만든 사용자 지정 모듈

Automation 계정을 만들 때 Azure Automation은 기본적으로 일부 모듈을 가져옵니다. [기본 모듈](#default-modules)을 참조하세요.

## <a name="sandboxes"></a>샌드박스

Automation은 Runbook 및 DSC 컴파일 작업을 실행할 때 Runbook을 실행할 수 있고 DSC 구성을 컴파일할 수 있는 샌드박스에 모듈을 로드합니다. 또한 Automation은 DSC 끌어오기 서버에 있는 모듈에 모든 DSC 리소스를 자동으로 배치합니다. 머신은 DSC 구성을 적용할 때 리소스를 끌어올 수 있습니다.

>[!NOTE]
>Runbook 및 DSC 구성에 필요한 모듈만 가져와야 합니다. 루트 Az 모듈은 가져오지 않는 것이 좋습니다. 여기에는 필요하지 않은 많은 다른 모듈이 포함되어 성능 문제가 발생할 수 있습니다. 대신 Az.Compute와 같은 개별 모듈을 가져옵니다.

클라우드 샌드박스는 최대 48개의 시스템 호출을 지원하고 다른 모든 호출은 보안상의 이유로 제한합니다. 자격 증명 관리 및 일부 네트워킹 등의 기타 기능은 클라우드 샌드박스에서 지원되지 않습니다.

포함된 모듈 및 cmdlet의 수로 인해 지원되지 않는 호출을 만드는 cmdlet을 미리 파악하기가 어렵습니다. 일반적으로 권한이 상승된 액세스를 필요로 하거나 매개 변수로 자격 증명이 필요하거나 네트워킹과 관련된 cmdlet에 대한 문제가 있었습니다. AIPService PowerShell 모듈의 [Connect-AipService](/powershell/module/aipservice/connect-aipservice) 및 DNSClient 모듈의 [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname)을 포함하여 완전한 스택 네트워크 작업을 수행하는 모든 cmdlet은 샌드박스에서 지원되지 않습니다.

이는 샌드박스와 관련하여 알려진 제한 사항입니다. 권장 해결 방법은 [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md)를 배포하거나 [Azure Functions](../../azure-functions/functions-overview.md)를 사용하는 것입니다.

> [!IMPORTANT] 
> Az 모듈을 통해 실행되도록 설계된 스크립트에는 "AzureRm" 키워드를 포함하지 마세요. 주석에도 키워드를 포함하면 AzureRm이 로드된 다음, Az 모듈과 충돌할 수 있습니다.

## <a name="default-modules"></a>기본 모듈

다음 표에서는 Automation 계정을 만들 때 기본적으로 Azure Automation에서 가져오는 모듈을 나열합니다. Automation은 이러한 모듈의 최신 버전을 가져올 수 있습니다. 그러나 최신 버전을 삭제하는 경우에도 Automation 계정에서 원래 버전을 제거할 수 없습니다. 참고로 이러한 기본 모듈에는 여러 AzureRM 모듈이 포함됩니다.

기본 모듈은 전역 모듈이라고도 합니다. Azure Portal에서 **전역 모듈** 속성은 계정을 만들 때 가져온 모듈을 볼 때 **True** 가 됩니다.

![Azure Portal에서 전역 모듈 속성의 스크린샷](../media/modules/automation-global-modules.png)

Automation은 루트 Az 모듈을 신규 또는 기존 Automation 계정으로 자동으로 가져오지 않습니다. 이러한 모듈을 사용하는 방법에 대한 자세한 내용은 [Az 모듈로 마이그레이션](#migrate-to-az-modules)을 참조하세요.

> [!NOTE]
> [작업 시간 외 VM 시작/중지](../automation-solution-vm-management.md) 기능을 배포하는 데 사용되는 Automation 계정에서는 모듈 및 Runbook을 변경하지 않는 것이 좋습니다.

|모듈 이름|버전|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az 모듈

Az.Automation의 경우 `AzureRM` 접두사가 `Az`로 변경된 경우를 제외하고 대부분의 cmdlet은 AzureRM 모듈에 사용되는 것과 동일한 이름을 갖습니다. 이 명명 규칙을 따르지 않는 Az 모듈 목록은 [예외 목록](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)을 참조하세요.

## <a name="internal-cmdlets"></a>내부 cmdlet

Azure Automation은 Azure 샌드박스 환경 또는 Windows Hybrid Runbook Worker에서 Runbook을 실행하는 경우에만 사용할 수 있는 내부 cmdlet을 지원합니다. 내부 모듈 `Orchestrator.AssetManagement.Cmdlets`는 기본적으로 Automation 계정에 설치되며 Windows Hybrid Runbook Worker 역할이 컴퓨터에 설치될 때 설치됩니다. 

다음 표에서는 내부 cmdlet을 정의합니다. 이러한 cmdlet은 Azure PowerShell cmdlet 대신 사용하여 Automation 계정 리소스와 상호 작용하도록 설계되었습니다. 암호화된 변수, 자격 증명 및 암호화된 연결에서 비밀을 검색할 수 있습니다.

|이름|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

참고로 내부 cmdlet은 Az 및 AzureRM cmdlet과는 이름을 다르게 지정합니다. 내부 cmdlet 이름에는 명사에 `Azure` 또는 `Az`와 같은 단어가 포함되지 않지만 `Automation`이라는 단어는 사용합니다. Azure 샌드박스 또는 Windows Hybrid Runbook Worker에서 Runbook을 실행하는 동안 필요한 매개 변수의 수가 더 적으며 실행 중에 작업의 컨텍스트에서 실행되므로 Az 또는 AzureRM cmdlet을 사용하는 것이 좋습니다.

Runbook의 컨텍스트를 벗어난 Automation 리소스를 조작하는 데 Az 또는 AzureRM cmdlet을 사용합니다. 

## <a name="python-modules"></a>Python 모듈

Azure Automation에서 Python 2 Runbook을 만들 수 있습니다. Python 모듈 정보는 [Azure Automation에서 Python 2 패키지 관리](../python-packages.md)를 참조하세요.

## <a name="custom-modules"></a>사용자 지정 모듈

Azure Automation은 Runbook 및 DSC 구성에서 사용하기 위해 만드는 사용자 지정 PowerShell 모듈을 지원합니다. 사용자 지정 모듈의 한 가지 유형은 모듈 cmdlet에 대한 사용자 지정 기능을 정의하는 메타데이터 파일을 선택적으로 포함하는 통합 모듈입니다. 통합 모듈 사용에 대한 예제는 [연결 형식을 추가](../automation-connections.md#add-a-connection-type)에 제시되어 있습니다.

Azure Automation에서는 cmdlet을 사용할 수 있도록 사용자 지정 모듈을 가져올 수 있습니다. 내부적으로 다른 모듈에 하듯이 모듈을 저장하고 Azure 샌드박스에서 사용합니다.

## <a name="migrate-to-az-modules"></a>Az 모듈로 마이그레이션

이 섹션에서는 Automation에서 Az 모듈로 마이그레이션하는 방법에 대해 설명합니다. 자세한 내용은 [Azure PowerShell을 AzureRM에서 Az로 마이그레이션](/powershell/azure/migrate-from-azurerm-to-az)을 참조하세요.

Az 모듈 및 AzureRM 모듈을 같은 Automation 계정에서 실행하는 것은 좋지 않습니다. AzureRM에서 Az로 마이그레이션하려는 경우 전체 마이그레이션에 완전히 커밋하는 것이 가장 좋습니다. Automation은 종종 Automation 계정 내에서 샌드박스를 재사용하여 시작 시간에 저장합니다. 전체 모듈 마이그레이션을 수행하지 않는 경우 AzureRM 모듈만 사용하는 작업을 시작한 다음, Az 모듈만 사용하는 다른 작업을 시작하는 경우가 있습니다. 그러면 샌드박스가 곧 충돌하고 모듈이 호환되지 않는다는 오류 메시지가 표시됩니다. 이 경우 특정 Runbook 또는 구성에 대해 무작위 충돌이 발생합니다.

>[!NOTE]
>새 Automation 계정을 만들 때 Az 모듈로 마이그레이션한 후에라도 Automation은 기본적으로 AzureRM 모듈을 설치합니다. AzureRM cmdlet을 사용하여 자습서 Runbook을 계속 업데이트할 수 있습니다. 그러나 이러한 Runbook은 실행해서는 안 됩니다.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>모듈 마이그레이션 전에 Runbook 및 DSC 구성 테스트

Az 모듈로 마이그레이션하기 전에 별도의 Automation 계정에서 모든 Runbook 및 DSC 구성을 신중하게 테스트해야 합니다. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>AzureRM 모듈을 사용하는 모든 Runbook 중지 및 예약 취소

AzureRM 모듈을 사용하는 기존 Runbook 또는 DSC 구성을 실행하지 않도록 하려면 영향을 받는 모든 Runbook 및 구성을 중지하고 예약을 취소해야 합니다. 먼저 각 Runbook 또는 DSC 구성과 해당 일정을 개별적으로 검토하여 필요한 경우 나중에 항목을 다시 예약할 수 있는지 확인합니다.

일정을 제거할 준비가 되면 Azure Portal 또는 [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) cmdlet 중 하나를 사용할 수 있습니다. [일정 제거](schedules.md#remove-a-schedule)를 참조하세요.

### <a name="remove-azurerm-modules"></a>AzureRM 모듈 제거

Az 모듈을 가져오기 전에 AzureRM 모듈을 제거할 수 있습니다. 그러나 이렇게 하면 소스 제어 동기화가 중단되어 아직 예약된 모든 스크립트가 실패할 수 있습니다. 모듈을 제거하려는 경우 [AzureRM 제거](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)를 참조하세요.

### <a name="import-az-modules"></a>Az 모듈 가져오기

Automation 계정으로 Az 모듈을 가져와도 Runbook이 사용하는 PowerShell 세션으로 모듈을 자동으로 가져오지 않습니다. 다음과 같은 상황에서 모듈을 PowerShell 세션으로 가져올 수 있습니다.

* Runbook이 모듈에서 cmdlet을 호출하는 경우
* Runbook이 [Import-module](/powershell/module/microsoft.powershell.core/import-module) cmdlet을 사용하여 모듈을 명시적으로 가져오는 경우
* Runbook에서 [using module](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) 문을 사용하여 모듈을 명시적으로 가져오는 경우. using 문은 Windows PowerShell 5.0부터 지원되며 클래스 및 열거형 형식 가져오기를 지원합니다.
* Runbook이 다른 종속 모듈을 가져오는 경우

Azure Portal에서 Az 모듈을 Automation 계정으로 가져올 수 있습니다. 사용 가능한 모든 Az 모듈이 아니라 필요한 Az 모듈만 가져와야 합니다. [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)는 다른 Az 모듈에 대한 종속성이기 때문에 이 모듈을 다른 모듈보다 먼저 가져와야 합니다.

1. Azure [Portal](https://portal.azure.com)에 로그인합니다.
1. **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. Automation 계정의 **공유 리소스** 아래에서 **모듈** 을 선택합니다.
1. **갤러리 찾아보기** 를 선택합니다.  
1. 검색 표시줄에서 모듈 이름(예: `Az.Accounts`)을 입력합니다.
1. PowerShell 모듈 페이지에서 **가져오기** 를 선택하여 Automation 계정으로 모듈을 가져옵니다.

    ![Automation 계정으로 모듈을 가져오는 스크린샷](../media/modules/import-module.png)

가져올 모듈을 검색하여 [PowerShell 갤러리](https://www.powershellgallery.com)를 통해 이 가져오기를 수행할 수도 있습니다. 모듈을 찾아 선택하고 **Azure Automation** 탭을 선택합니다. **Azure Automation에 배포** 를 선택합니다.

![PowerShell 갤러리에서 직접 모듈을 가져오는 스크린샷](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Runbook 테스트

Az 모듈을 Automation 계정으로 가져온 후에는 Runbook 및 DSC 구성 편집을 시작하여 새 모듈을 사용할 수 있습니다. 새로운 cmdlet을 사용하도록 Runbook 수정을 테스트하는 한 가지 방법은 Runbook 맨 처음에 `Enable-AzureRmAlias -Scope Process` 명령을 사용하는 것입니다. 이 명령을 Runbook에 추가하면 스크립트는 변경 없이 실행될 수 있습니다.

## <a name="author-modules"></a>작성자 모듈

Azure Automation에서 사용할 사용자 지정 PowerShell 모듈을 작성하는 경우 이 섹션의 고려 사항을 따르는 것이 좋습니다. 모듈을 가져오기 위해 준비하려면 모듈 폴더와 동일한 이름으로 적어도 .psd1, .psm1 또는 PowerShell 모듈 **.dll** 파일을 하나 이상 만들어야 합니다. 그런 다음, 모듈 폴더를 압축하여 Azure Automation에서 단일 파일로 가져올 수 있도록 합니다. **.zip** 패키지의 이름이 포함된 모듈 폴더와 같아야 합니다.

PowerShell 모듈을 작성하는 방법에 대한 자세한 내용은 [PowerShell 스크립트 모듈을 작성하는 방법](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)을 참조하세요.

### <a name="version-folder"></a>버전 폴더

PowerShell 병렬 모듈 버전 관리를 사용하면 PowerShell 내에서 모듈의 여러 버전을 사용할 수 있습니다. 이전 스크립트가 테스트되었으며 특정 버전의 PowerShell 모듈에만 작동하는 경우에는 유용할 수 있지만 다른 스크립트에는 동일한 PowerShell 모듈의 최신 버전이 필요합니다.

여러 버전을 포함하도록 PowerShell 모듈을 구성하려면 모듈 폴더를 만든 다음 사용하려는 모듈의 각 버전에 대해 해당 모듈 폴더 내에 폴더를 만듭니다. 다음 예제에서 *TestModule* 이라는 모듈은 1.0.0 및 2.0.0의 두 가지 버전을 제공합니다.

```dos
TestModule
   1.0.0
   2.0.0
```

각 버전 폴더 내에서 모듈을 구성하는 PowerShell .psm1, .psd1 또는 PowerShell 모듈 **.dll** 파일을 해당 버전 폴더로 복사합니다. 그런 다음 모듈 폴더를 압축하여 Azure Automation에서 단일 .zip 파일로 가져올 수 있도록 합니다. Automation은 가져온 모듈의 가장 높은 버전만 표시하지만 모듈 패키지에 모듈의 병렬 버전이 포함되어 있으면 모두 Runbook 또는 DSC 구성에서 사용할 수 있습니다.  

Automation은 동일한 패키지 내에서 병렬 버전을 포함하는 모듈을 지원하지만 모듈 패키지 가져오기에서 여러 버전의 모듈을 사용하는 기능은 지원하지 않습니다. 예를 들어 버전 1과 2가 포함된 **모듈 A** 를 Automation 계정으로 가져올 수 있습니다. 나중에 버전 3과 4를 포함하도록 **모듈 A** 를 업데이트하는 경우 Automation 계정으로 가져올 때 버전 3 및 4만 Runbook 또는 DSC 구성 내에서 사용할 수 있습니다. 모든 버전(1, 2, 3, 4)을 사용해야 하면 가져오는 .zip 파일에 버전 1, 2, 3, 4가 포함되어야 합니다.

Runbook 사이에 동일한 모듈의 서로 다른 버전을 사용하려는 경우에는 `Import-Module` cmdlet을 사용하여 Runbook에서 사용하려는 버전을 항상 선언하고 `-RequiredVersion <version>` 매개 변수를 포함해야 합니다. 사용하려는 버전이 최신 버전인 경우에도 마찬가지입니다. Runbook 작업은 동일한 샌드박스에서 실행될 수 있기 때문입니다. 샌드박스에서 특정 버전 번호의 모듈을 이미 명시적으로 로드한 경우 해당 샌드박스의 이전 작업에서 그렇게 하라고 했으므로 해당 샌드박스의 이후 작업에서는 해당 모듈의 최신 버전을 자동으로 로드하지 않습니다. 이는 일부 버전이 이미 샌드박스에서 로드되었기 때문입니다.

DSC 리소스의 경우 다음 명령을 사용하여 특정 버전을 지정합니다.

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>도움말 정보

모듈에서 모든 cmdlet에 대한 개요, 설명 및 도움말 URI를 포함합니다. PowerShell에서 `Get-Help` cmdlet을 사용하여 cmdlet에 대한 도움말 정보를 정의할 수 있습니다. 다음 예제에서는 **.psm1** 모듈 파일에서 개요 및 도움말 URI를 정의하는 방법을 보여 줍니다.

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  이 정보를 제공하면 PowerShell 콘솔에서 `Get-Help` cmdlet을 통해 도움말 텍스트를 표시합니다. 이 텍스트는 Azure Portal에도 표시됩니다.

  ![통합 모듈 도움말의 스크린샷](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>연결 형식

모듈이 외부 서비스에 연결하는 경우 [사용자 지정 통합 모듈](#custom-modules)을 사용하여 연결 형식을 정의합니다. 모듈의 각 cmdlet은 해당 연결 형식(연결 개체)의 인스턴스를 매개 변수로 허용해야 합니다. 사용자는 cmdlet을 호출할 때마다 cmdlet의 해당 매개 변수에 연결 자산의 매개 변수를 매핑합니다.

![Azure Portal에서 사용자 지정 연결 사용](../media/modules/connection-create-new.png)

다음 Runbook 예제에서는 `ContosoConnection`이라는 Contoso 연결 자산을 사용하여 Contoso 리소스에 액세스하고 외부 서비스에서 데이터를 반환합니다. 이 예제에서 필드는 `PSCredential` 개체의 `UserName` 및 `Password` 속성에 매핑되고 cmdlet에 전달됩니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

이 동작에 접근하는 더 쉽고 나은 방법은 cmdlet에 직접 연결 개체를 전달하는 것입니다.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

매개 변수에 대한 연결 필드 대신에 매개 변수인 연결 개체를 직접 수용할 수 있도록 하여 cmdlet에 유사한 동작을 설정할 수 있습니다. 일반적으로 각각에 대해 매개 변수를 설정하여 Automation을 사용하지 않는 사용자가 해시 테이블을 생성하지 않고 cmdlet을 호출하여 연결 개체의 역할을 할 수 있도록 하는 것이 좋습니다. 매개 변수 집합 `UserAccount`는 연결 필드 속성을 전달하는 데 사용됩니다. `ConnectionObject`를 통해 연결을 직접 전달할 수 있습니다.

### <a name="output-type"></a>출력 형식

모듈에서 모든 cmdlet에 대한 출력 형식을 정의합니다. cmdlet에 대한 출력 형식을 정의하면 디자인 타임 IntelliSense에서 작성 중에 cmdlet의 출력 속성을 확인할 수 있습니다. 이 방법은 디자인 타임 지식이 모듈을 사용하는 쉬운 사용자 환경의 핵심인 그래픽 Runbook을 작성하는 동안 특히 유용합니다.

`[OutputType([<MyOutputType>])]`을 추가합니다. 여기서 `MyOutputType`은 유효한 형식입니다. `OutputType`에 대한 자세한 내용은 [함수 OutputTypeAttribute 정보](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)를 참조하세요. 다음 코드는 cmdlet에 `OutputType`을 추가하는 예입니다.

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![그래픽 Runbook 출력 형식의 스크린샷](../media/modules/runbook-graphical-module-output-type.png)

  이 동작은 PowerShell 통합 서비스 환경에서 cmdlet의 출력을 실행하지 않는 “사전 입력” 기능과 비슷합니다.

  ![POSH IntelliSense의 스크린샷](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>cmdlet 상태

모듈의 모든 cmdlet을 상태 비저장으로 만듭니다. 여러 Runbook 작업은 동일한 `AppDomain`과 동일한 프로세스 및 샌드박스에서 동시에 실행할 수 있습니다. 이러한 수준에서 공유된 상태가 있으면 작업은 서로 영향을 줄 수 있습니다. 이 동작으로 인해 진단이 어려운 일시적인 문제가 발생할 수 있습니다. 다음은 수행해서는 안 되는 작업의 예입니다.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>모듈 종속성

모듈이 xcopy를 사용하여 복사할 수 있는 패키지에 완전히 포함되어 있는지 확인합니다. Automation 모듈은 Runbook을 실행할 때 Automation 샌드박스에 배포됩니다. 모듈은 실행하는 호스트와 독립적으로 작동해야 합니다.

모듈 패키지를 Zip으로 압축한 후 이동하여 다른 호스트의 PowerShell 환경으로 가져왔을 때 정상적으로 작동하게 만들 수 있어야 합니다. 이렇게 하려면 모듈이 Automation으로 가져올 때 압축된 모듈 폴더 외부의 어떤 파일에도 종속되지 않도록 해야 합니다.

모듈은 호스트의 어떠한 고유한 레지스트리 설정에도 종속되어서는 안 됩니다. 제품이 설치될 때 적용되는 설정의 예시입니다.

### <a name="module-file-paths"></a>모듈 파일 경로

모듈의 모든 파일에서 경로가 140자 미만인지 확인합니다. 140자를 초과하는 경로가 있으면 Runbook을 가져오는 데 문제가 발생합니다. Automation은 경로 크기가 140자인 파일을 `Import-Module`을 사용하여 PowerShell 세션으로 가져올 수 없습니다.

## <a name="import-modules"></a>모듈 가져오기

이 섹션에서는 Automation 계정으로 모듈을 가져올 수 있는 몇 가지 방법을 정의합니다.

### <a name="import-modules-in-the-azure-portal"></a>Azure Portal에서 모듈 가져오기

Azure Portal에서 모듈을 가져오려면 다음을 수행합니다.

1. 포털에서 **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. **공유 리소스** 에서 **모듈** 을 선택합니다.
1. **모듈 추가** 를 선택합니다.
1. 모듈을 포함하는 **.zip** 파일을 선택합니다.
1. **확인** 을 선택하여 가져오기 프로세스를 시작합니다.

### <a name="import-modules-by-using-powershell"></a>PowerShell을 사용하여 모듈 가져오기

[New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) cmdlet을 사용하여 모듈을 Automation 계정으로 가져올 수 있습니다. cmdlet은 모듈 .zip 패키지에 대한 URL을 사용합니다.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

동일한 cmdlet을 사용하여 PowerShell 갤러리에서 모듈을 직접 가져올 수도 있습니다. [PowerShell 갤러리](https://www.powershellgallery.com)에서 `ModuleName` 및 `ModuleVersion`을 가져와야 합니다.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>PowerShell 갤러리에서 모듈 가져오기

갤러리 또는 Automation 계정에서 직접 [PowerShell 갤러리](https://www.powershellgallery.com) 모듈을 가져올 수 있습니다.

PowerShell 갤러리에서 직접 모듈을 가져오려면 다음을 수행합니다.

1. https://www.powershellgallery.com 으로 이동하여 가져올 모듈을 검색합니다.
2. **설치 옵션** 아래의 **Azure Automation** 탭에서 **Azure Automation에 배포** 를 선택합니다. 이 작업을 수행하면 Azure Portal이 열립니다. 
3. 가져오기 페이지에서 Automation 계정을 선택하고 **확인** 을 선택합니다.

![PowerShell 갤러리 가져오기 모듈의 스크린샷](../media/modules/powershell-gallery.png)

Automation 계정에서 직접 PowerShell 갤러리 모듈을 가져오려면 다음을 수행합니다.

1. 포털에서 **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. **공유 리소스** 에서 **모듈** 을 선택합니다. 
1. **갤러리 찾아보기** 를 선택하고 갤러리에서 모듈을 검색합니다. 
1. 가져올 모듈을 선택하고 **가져오기** 를 선택합니다. 
1. **확인** 을 선택하여 가져오기 프로세스를 시작합니다.

![Azure Portal에서 PowerShell 갤러리 모듈을 가져오는 스크린샷](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>모듈 삭제

모듈에 문제가 있거나 이전 버전의 모듈로 롤백해야 하는 경우 Automation 계정에서 삭제할 수 있습니다. Automation 계정을 만들 때 가져온 [기본 모듈](#default-modules)의 원래 버전은 삭제할 수 없습니다. 삭제할 모듈이 [기본 모듈](#default-modules) 중 하나의 최신 버전인 경우 Automation 계정과 함께 설치된 버전으로 롤백됩니다. 그렇지 않으면 Automation 계정에서 삭제한 모든 모듈이 제거됩니다.

### <a name="delete-modules-in-the-azure-portal"></a>Azure Portal에서 모듈 삭제

Azure Portal에서 모듈을 제거하려면 다음을 수행합니다.

1. 포털에서 **Automation 계정** 을 검색하여 선택합니다.
1. **Automation 계정** 페이지의 목록에서 Automation 계정을 선택합니다.
1. **공유 리소스** 에서 **모듈** 을 선택합니다.
1. 제거하려는 모듈을 선택합니다.
1. 모듈 페이지에서 **삭제** 를 선택합니다. 이 모듈이 [기본 모듈](#default-modules) 중 하나인 경우 Automation 계정을 만들 때 있었던 버전으로 롤백됩니다.

### <a name="delete-modules-by-using-powershell"></a>PowerShell을 사용하여 모듈 삭제

PowerShell을 통해 모듈을 제거하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>다음 단계

* Azure PowerShell 모듈 사용에 대한 자세한 내용은 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 참조하세요.

* PowerShell 모듈 만들기에 대한 자세한 내용은 [Windows PowerShell 모듈 작성](/powershell/scripting/developer/module/writing-a-windows-powershell-module)을 참조하세요.
