---
title: Azure Automation State Configuration에서 DSC 구성 컴파일
description: 이 문서에서는 Azure Automation에 대한 DSC(Desired State Configuration) 구성을 컴파일하는 방법을 설명합니다.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 636bb2c91330cb147c367ca7143f988986076d73
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493536"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation State Configuration에서 DSC 구성 컴파일

Azure Automation State Configuration을 사용하여 다음 방법으로 DSC(Desired State Configuration) 구성을 컴파일할 수 있습니다.

- Azure State Configuration 컴파일 서비스
  - 대화형 사용자 인터페이스를 사용하는 초급자 방법
   - 작업 상태를 쉽게 추적

- Windows PowerShell
  - 로컬 워크스테이션 또는 빌드 서비스의 Windows PowerShell에서 호출
  - 개발 테스트 파이프라인과 통합
  - 복잡한 매개 변수 값 제공
  - 대규모 노드 및 비노드 데이터 작업
  - 뛰어난 성능 향상

Azure DSC(Desired State Configuration) 확장으로 Azure Resource Manager 템플릿을 사용하여 Azure VM에 구성을 푸시할 수도 있습니다. Azure DSC 확장은 Azure VM 에이전트 프레임워크를 사용하여 Azure VM에서 실행되는 DSC 구성을 제공하고 적용하며 보고합니다. Azure Resource Manager 템플릿 사용에 대한 자세한 내용은 [Desired State Configuration 확장과 Azure Resource Manager 템플릿](../virtual-machines/extensions/dsc-template.md#details)을 참조하세요. 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Azure State Configuration에서 DSC 구성 컴파일

### <a name="portal"></a>포털

1. Automation 계정에서 **State Configuration(DSC)** 을 클릭합니다.
1. **구성** 탭 및 컴파일할 구성 이름을 차례로 클릭합니다.
1. **컴파일** 을 클릭합니다.
1. 구성에 매개 변수가 없는 경우 컴파일할지 확인하라는 메시지가 표시됩니다. 구성에 매개 변수가 있는 경우 **컴파일 구성** 블레이드를 열어 매개 변수 값을 제공할 수 있습니다.
1. 컴파일 작업 상태를 추적할 수 있도록 컴파일 작업 페이지가 열립니다. 또한 이 페이지를 사용하여 Azure Automation State Configuration 끌어오기 서버에 배치된 노드 구성(MOF 구성 문서)을 추적할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

[Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob)을 사용하여 Windows PowerShell로 컴파일을 시작할 수 있습니다. 다음 샘플 코드는 **SampleConfig** 라는 DSC 구성의 컴파일을 시작합니다.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`은 작업 상태를 추적하는 데 사용할 수 있는 컴파일 작업 개체를 반환합니다. 그런 다음, 이 컴파일 작업 개체를 [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob)과 함께 사용하여 컴파일 작업의 상태를 확인하고, [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration)을 사용하여 스트림(출력)을 볼 수 있습니다. 다음 샘플은 SampleConfig 구성의 컴파일을 시작하고 완료될 때까지 대기한 다음, 해당 스트림을 표시합니다.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>기본 매개 변수 선언

매개 변수 형식 및 속성을 포함하는 DSC 구성의 매개 변수 선언은 Azure Automation runbook과 동일하게 작동합니다. [Azure Automation에서 runbook 시작](./start-runbooks.md) 을 참조하여 runbook 매개 변수에 대한 자세한 내용을 알아봅니다.

다음 예제에서는 `FeatureName` 및 `IsPresent` 매개 변수를 사용하여 컴파일하는 동안 생성되는 **ParametersExample.sample** 노드 구성에서 속성의 값을 결정합니다.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Azure Automation State Configuration 포털에서 또는 Azure PowerShell로 기본 매개 변수를 사용하는 DSC 구성을 컴파일할 수 있습니다.

#### <a name="portal"></a>포털

포털에서 **컴파일** 을 클릭한 후에 매개 변수 값을 입력할 수 있습니다.

![구성 컴파일 매개 변수](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell은 키와 매개 변수 이름이 일치하고 매개 변수 값과 값이 같은 [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables)에 매개 변수가 필요합니다.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

`PSCredential` 개체를 매개 변수로 전달하는 방법에 대한 정보는 아래의 [자격 증명 자산](#credential-assets)을 참조하세요.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Azure Automation의 복합 리소스를 포함하는 구성 컴파일

**복합 리소스** 기능을 사용하면 구성 내에서 중첩된 리소스로 DSC 구성을 사용할 수 있습니다. 이 기능을 사용하면 단일 리소스에 여러 구성의 애플리케이션을 적용할 수 있습니다. [복합 리소스: DSC 구성을 리소스로 사용](/powershell/scripting/dsc/resources/authoringresourcecomposite)에서 복합 리소스에 대해 자세히 알아보세요.

> [!NOTE]
> 복합 리소스가 포함된 구성이 올바르게 컴파일되기 위해서는 먼저 복합 리소스가 의존하는 DSC 리소스를 Azure Automation으로 가져와야 합니다. DSC 복합 리소스 추가는 Azure Automation에 PowerShell 모듈을 추가하는 것과 다르지 않습니다. 이 프로세스는 [Azure Automation에서 모듈 관리](./shared-resources/modules.md)에 설명되어 있습니다.

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Azure Automation에서 구성을 컴파일할 때 ConfigurationData 관리

`ConfigurationData`는 PowerShell DSC를 사용하는 동안 구조적 구성을 환경별 구성과 구분하게 해주는 기본 제공 DSC 매개 변수입니다. 자세한 내용은 [PowerShell DSC의 “위치”에서 ”대상” 분리](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)를 참조하세요.

> [!NOTE]
> Azure Automation State Configuration에서 컴파일하는 경우 Azure Portal이 아닌 Azure PowerShell에서 `ConfigurationData`를 사용할 수 있습니다.

다음 예제 DSC 구성은 `$ConfigurationData` 및 `$AllNodes` 키워드를 통해 `ConfigurationData`를 사용합니다. 또한 이 예제에는 [xWebAdministration 모듈](https://www.powershellgallery.com/packages/xWebAdministration/)이 필요합니다.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Windows PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 스크립트는 Azure Automation State Configuration 끌어오기 서비스에 두 개의 노드 구성 **ConfigurationDataSample.MyVM1** 및 **ConfigurationDataSample.MyVM3** 를 추가합니다.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>컴파일하는 동안 Azure Automation의 자산 작업

자산 참조는 Azure Automation State Configuration 및 Runbook 둘 다에서 동일합니다. 자세한 내용은

- [인증서](./shared-resources/certificates.md)
- [연결](automation-connections.md)
- [자격 증명](./shared-resources/credentials.md)
- [변수](./shared-resources/variables.md)

#### <a name="credential-assets"></a>자격 증명 자산

Azure Automation에서 DSC 구성은 `Get-AutomationPSCredential` cmdlet를 사용하여 Automation 자격 증명 자산을 참조할 수 있습니다. 구성에 `PSCredential` 개체를 지정하는 매개 변수가 있는 경우 자격 증명을 검색하는 cmdlet에 Azure Automation 자격 증명 자산의 문자열 이름을 전달하여 `Get-AutomationPSCredential`을 사용할 수 있습니다. 그런 다음, `PSCredential` 개체를 필요로 하는 매개 변수에 해당 개체를 사용합니다. 내부적으로 해당 이름을 가진 Azure Automation 자격 증명 자산은 검색되고 구성에 전달됩니다. 아래 예제에서는 작업 중인 이 시나리오를 보여 줍니다.

자격 증명을 노드 구성(MOF 구성 문서)에서 안전하게 유지하려면 노드 구성 MOF 파일에 자격 증명을 암호화해야 합니다. 현재는 노드 구성 MOF 생성 중에 일반 텍스트로 자격 증명을 출력하려면 PowerShell DSC 권한을 제공해야 합니다. PowerShell DSC는 컴파일 작업을 통해 생성된 후 Azure Automation이 전체 MOF 파일을 암호화하는 것을 인식하지 못합니다.

구성 데이터를 사용하여 생성된 노드 구성 MOF에 일반 텍스트로 자격 증명을 출력해도 된다고 PowerShell DSC에 지시할 수 있습니다. DSC 구성에 표시되고 자격 증명을 사용하는 각 노드 블록 이름의 경우 `ConfigurationData`를 통해 `PSDscAllowPlainTextPassword = $true`를 전달해야 합니다.

다음 예제에서는 Automation 자격 증명 자산을 사용하는 DSC 구성을 보여줍니다.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 PowerShell 코드는 Azure Automation State Configuration 끌어오기 서버에 두 개의 노드 구성 **CredentialSample.MyVM1** 및 **CredentialSample.MyVM2** 를 추가합니다.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 컴파일이 완료되면 `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` 오류 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 괜찮습니다.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Windows PowerShell에서 DSC 구성 컴파일

Windows PowerShell에서 DSC 구성을 컴파일하는 프로세스는 PowerShell DSC 설명서 [구성의 작성, 컴파일 및 적용](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)에 포함되어 있습니다.
개발자 워크스테이션 또는 빌드 서비스(예: [Azure DevOps](https://dev.azure.com)) 내에서 프로세스를 실행할 수 있습니다. 그런 다음, 구성을 컴파일하여 생성된 MOF 파일을 Azure State Configuration 서비스로 가져올 수 있습니다.

Windows PowerShell에서 컴파일하면 구성 콘텐츠에 서명하는 옵션도 제공됩니다. DSC 에이전트는 관리형 노드에서 로컬로 서명된 노드 구성을 확인합니다. 확인을 하면 노드에 적용된 구성이 권한 있는 원본에서 제공되도록 할 수 있습니다.

Azure 외부에서 컴파일한 노드 구성(MOF 파일)을 가져올 수도 있습니다. 가져오기에는 개발자 워크스테이션 또는 [Azure DevOps](https://dev.azure.com)와 같은 서비스에서의 컴파일이 포함됩니다. 이 방법은 성능 및 안정성을 비롯한 여러 가지 이점을 제공합니다.

> [!NOTE]
> 노드 구성 파일은 Azure Automation으로 가져오기 위해 1MB보다 크지 않아야 합니다.

노드 구성의 서명에 대한 자세한 내용은 [WMF 5.1의 향상된 기능 - 구성 및 모듈에 서명하는 방법](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)을 참조하세요.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Azure Portal에서 노드 구성 가져오기

1. Automation 계정 페이지에서 **구성 관리** 아래에 있는 **State Configuration(DSC)** 을 클릭합니다.
1. State Configuration(DSC) 페이지에서 **구성** 탭을 클릭한 다음, **추가** 를 클릭합니다.
1. 가져오기 페이지에서 **노드 구성 파일** 필드 옆의 폴더 아이콘을 클릭하여 로컬 컴퓨터에서 노드 구성 MOF 파일을 찾습니다.

   ![로컬 파일 찾기](./media/automation-dsc-compile/import-browse.png)

1. **구성 이름** 필드에 이름을 입력합니다. 이 이름은 노드 구성이 컴파일된 구성 이름과 일치해야 합니다.
1. **확인** 을 클릭합니다.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Azure PowerShell을 사용하여 노드 구성 가져오기

[Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) cmdlet을 사용하여 노드 구성을 Automation 계정으로 가져올 수 있습니다.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation State Configuration 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation State Configuration에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation State Configuration 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 State Configuration을 사용하는 예제는 [Chocolatey를 사용한 지속적인 배포 설정](automation-dsc-cd-chocolatey.md)을 참조하세요.
