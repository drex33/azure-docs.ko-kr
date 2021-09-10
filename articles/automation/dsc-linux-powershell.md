---
title: PowerShell을 사용하여 Linux Azure Automation State Configuration 적용
description: 이 문서에서는 PowerShell에서 Azure Automation State Configuration을 사용하여 Linux 가상 머신을 원하는 상태로 구성하는 방법을 설명합니다.
ms.topic: conceptual
services: automation
ms.subservice: dsc
ms.date: 08/31/2021
ms.openlocfilehash: e4352c5c2cca0391e4e795b537af16c28ded6f6e
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123354547"
---
# <a name="configure-linux-desired-state-with-azure-automation-state-configuration-using-powershell"></a>PowerShell을 사용하여 Azure Automation State Configuration으로 Linux 원하는 상태 구성

이 자습서에서는 PowerShell을 사용하여 Azure Linux 가상 머신에 Azure Automation State Configuration을 적용하여 원하는 상태를 준수하는지 확인합니다. 원하는 상태는 apache2 서비스가 노드에 있는지 확인하는 것입니다.
Azure Automation State Configuration을 사용하면 컴퓨터의 구성을 지정하고 시간이 지남에 따라 해당 컴퓨터가 지정된 상태에 있는지 확인할 수 있습니다. State Configuration에 대한 자세한 내용은 [Azure Automation State Configuration 개요](./automation-dsc-overview.md)를 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.
> [!div class="checklist"]
> - Azure Automation DSC에서 관리할 Azure Linux VM 온보딩
> - 구성 작성
> - Automation용 PowerShell 모듈 설치
> - Azure Automation으로 구성 가져오기
> - 노드 구성으로 구성 컴파일
> - 관리되는 노드에 노드 구성 할당
> - 노드 구성 매핑 수정
> - 관리되는 노드에 대한 준수 상태 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Automation 계정. Automation 계정에 대한 자세한 내용은 [Automation 계정 인증 개요](./automation-security-overview.md)를 참조하세요.
- Ubuntu 18.04 LTS 이상에서 실행되는 Azure Resource Manager VM(가상 머신)입니다. Azure Linux VM을 만드는 방법에 대한 지침은 [PowerShell을 사용하여 Azure에서 Linux 가상 머신 만들기](../virtual-machines/windows/quick-create-powershell.md)를 참조하세요.
- 대상 Azure Linux VM에 State Configuration을 쓰고, 컴파일하고, 적용하는 데 사용할 컴퓨터에 설치된 PowerShell [Az 모듈](/powershell/azure/new-azureps-module-az)입니다. 최신 버전이 있는지 확인합니다. 필요한 경우 `Update-Module -Name Az`를 실행합니다.

## <a name="create-a-configuration"></a>구성 만들기

아래 코드를 검토하고 두 노드 [구성](/powershell/scripting/dsc/configurations/configurations)(`IsPresent` 및 `IsNotPresent`)이 있는지 확인합니다. 이 구성은 각 노드 블록에서 하나의 리소스([nxPackage 리소스](/powershell/scripting/dsc/reference/resources/linux/lnxpackageresource))를 호출합니다. 이 리소스는 **apache2** 패키지의 존재를 관리합니다. 그런 다음 텍스트 편집기에서 다음 코드를 로컬 파일에 복사하고 이름을 `LinuxConfig.ps1`으로 지정합니다.

```powershell
Configuration LinuxConfig
{
    Import-DscResource -ModuleName 'nx'

    Node IsPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Present'
            PackageManager    = 'Apt'
        }
    }

    Node IsNotPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Absent'
        }
    }
}
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

컴퓨터에서 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) PowerShell cmdlet을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="initialize-variables"></a>변수 초기화

cmdlet을 실행할 때 효율성과 오류 감소를 위해 필요에 따라 아래의 PowerShell 코드를 수정한 다음 실행합니다.

| 변수 | 값 |
|---|---|
|$resourceGroup| `yourResourceGroup`을 리소스 그룹의 실제 이름으로 바꿉니다.|
|$automationAccount| `yourAutomationAccount`를 Automation 계정의 실제 이름으로 바꿉니다.|
|$VM| `yourVM`을 Azure Linux VM의 실제 이름으로 바꿉니다.|
|$configurationName| `LinuxConfig`를 있는 그대로 둡니다. 이 자습서에서 사용되는 구성의 이름입니다.|
|$nodeConfigurationName0|`LinuxConfig.IsNotPresent`를 있는 그대로 둡니다. 이 자습서에서 사용되는 노드 구성의 이름입니다.|
|$nodeConfigurationName1|`LinuxConfig.IsPresent`를 있는 그대로 둡니다. 이 자습서에서 사용되는 노드 구성의 이름입니다.|
|$moduleName|`nx`를 있는 그대로 둡니다. 이 자습서에서 DSC에 사용되는 PowerShell 모듈의 이름입니다.|
|$moduleVersion| [PowerShell 갤러리](https://www.powershellgallery.com/packages/nx)에서 `nx`의 최신 버전 번호를 가져옵니다. 이 자습서에서는 버전 `1.0`을 사용합니다.|

```powershell
$resourceGroup = "yourResourceGroup"
$automationAccount = "yourAutomationAccount"
$VM = "yourVM"
$configurationName = "LinuxConfig"
$nodeConfigurationName0 = "LinuxConfig.IsNotPresent"
$nodeConfigurationName1 = "LinuxConfig.IsPresent"
$moduleName = "nx"
$moduleVersion = "1.0"
```

## <a name="install-nx-module"></a>nx 모듈 설치

Azure Automation은 많은 PowerShell 모듈을 사용하여 DSC 구성에서 Runbook 및 DSC 리소스의 cmdlet을 사용하도록 설정합니다. **nx** 는 Linux용 DSC 리소스가 있는 모듈입니다. [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) cmdlet을 사용하여 **nx** 모듈을 설치합니다. 모듈에 대한 자세한 내용은 [Azure Automation에서 모듈 관리](./shared-resources/modules.md)를 참조하세요. 다음 명령을 실행합니다.

```powershell
New-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName `
    -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

출력은 아래와 같이 표시됩니다.

   :::image type="content" source="media/dsc-linux-powershell/new-azautomationmodule-output.png" alt-text="New-AzAutomationModule 명령의 출력입니다.":::

다음 명령을 실행하여 설치를 확인할 수 있습니다.

```powershell
Get-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName 
```

## <a name="import-configuration-to-azure-automation"></a>Azure Automation으로 구성 가져오기

[Import-AzAutomationDscConfiguration](/powershell/module/az.automation/import-azautomationdscconfiguration) cmdlet을 호출하여 구성을 Automation 계정에 업로드합니다. 실제 경로로 `-SourcePath`의 값을 수정한 후 다음 명령을 실행합니다.

```powershell
Import-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -SourcePath "path\LinuxConfig.ps1" `
   -Published
```

출력은 아래와 같이 표시됩니다.

   :::image type="content" source="media/dsc-linux-powershell/import-azautomationdscconfiguration-output.png" alt-text="Import-AzAutomationDscConfiguration 명령의 출력입니다.":::

다음 명령을 실행하는 Automation 계정에서 구성을 볼 수 있습니다.

```powershell
Get-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $configurationName
```

## <a name="compile-configuration-in-azure-automation"></a>Azure Automation에서 구성 컴파일

노드에 원하는 상태를 적용하려면 먼저 해당 상태를 정의하는 구성을 하나 이상의 노드 구성으로 컴파일해야 합니다.  [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) cmdlet을 호출하여 Azure Automation의 `LinuxConfig` 구성을 컴파일합니다. 구성에 대한 자세한 내용은 [DSC 컴파일 구성](./automation-dsc-compile.md)을 참조하세요. 다음 명령을 실행합니다.

```powershell
Start-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

출력은 아래와 같이 표시됩니다.

   :::image type="content" source="media/dsc-linux-powershell/start-azautomationdsccompilationjob-output.png" alt-text="Start-AzAutomationDscCompilationJob 명령의 출력입니다.":::

다음 명령을 사용하여 Automation 계정에서 컴파일 작업을 볼 수 있습니다.

```powershell
Get-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

컴파일 작업이 완료될 때까지 기다렸다가 계속 진행합니다. 구성을 노드에 할당하려면 먼저 노드 구성으로 컴파일해야 합니다. 다음 코드를 실행하여 5초마다 상태를 확인합니다.

```powershell
while ((Get-AzAutomationDscCompilationJob `
         -ResourceGroupName $resourceGroup `
         -AutomationAccountName $automationAccount `
         -ConfigurationName $configurationName).Status -ne "Completed") 
{
   Write-Output "Wait"
   Start-Sleep -Seconds 5
}
Write-Output "Compilation complete"
```

컴파일 작업이 완료되면 다음 명령을 사용하여 노드 구성 메타데이터를 볼 수도 있습니다.

```powershell
Get-AzAutomationDscNodeConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount
```

## <a name="register-the-azure-linux-vm-for-an-automation-account"></a>Automation 계정에 대한 Azure Linux VM 등록

Azure Linux VM을 Azure Automation 계정의 DSC(Desired State Configuration) 노드로 등록합니다. [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet은 Windows OS를 실행하는 VM만 지원합니다. Azure Linux VM은 먼저 DSC에 대해 구성되어야 합니다. 자세한 단계는 [Linux용 DSC(Desired State Configuration) 시작](/powershell/scripting/dsc/getting-started/lnxgettingstarted)을 참조하세요.

1. 다음 코드를 실행하여 Azure Linux VM에서 나중에 실행하도록 PowerShell을 사용하여 등록 명령으로 Python 스크립트를 구성합니다.

   ```powershell
    $primaryKey = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).PrimaryKey
    
    $URL = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).Endpoint
    
    Write-Output "sudo /opt/microsoft/dsc/Scripts/Register.py $primaryKey $URL"
   ```

   이러한 명령은 Automation 계정의 기본 액세스 키와 URL을 가져오고 등록 명령에 연결합니다. 출력에서 캐리지 리턴을 제거해야 합니다. 이 명령은 이후 단계에서 사용됩니다.

1. Azure Linux VM에 연결합니다. 암호를 사용한 경우 아래 구문을 사용할 수 있습니다. 공개-프라이빗 키 쌍을 사용한 경우 자세한 단계는 [Linux의 SSH](./../virtual-machines/linux/mac-create-ssh-keys.md)를 참조하세요. 다른 명령은 현재 설치된 패키지에 대한 업데이트를 포함하여 설치할 수 있는 패키지에 대한 정보를 검색하고 Python을 설치합니다.

   ```cmd
   ssh user@IP

   sudo apt-get update
   sudo apt-get install -y python
   ```

1. OMI(개방형 관리 인프라)를 설치합니다. OMI에 대한 자세한 내용은 [개방형 관리 인프라](https://github.com/Microsoft/omi)를 참조하세요. 최신 [릴리스](https://github.com/Microsoft/omi/releases)를 확인합니다. 필요에 따라 아래 릴리스 버전을 수정한 다음 ssh 세션에서 명령을 실행합니다.

   ```bash
   wget https://github.com/microsoft/omi/releases/download/v1.6.8-0/omi-1.6.8-0.ssl_110.ulinux.x64.deb

   sudo dpkg -i ./omi-1.6.8-0.ssl_110.ulinux.x64.deb
   ```

1. Linux용 PowerShell Desired State Configuration을 설치합니다. 자세한 내용은 [Linux의 DSC](https://github.com/microsoft/PowerShell-DSC-for-Linux)를 참조하세요. 최신 [릴리스](https://github.com/microsoft/PowerShell-DSC-for-Linux/releases)를 확인합니다. 필요에 따라 아래 릴리스 버전을 수정한 다음 ssh 세션에서 명령을 실행합니다.

   ```bash
   wget https://github.com/microsoft/PowerShell-DSC-for-Linux/releases/download/v1.2.1-0/dsc-1.2.1-0.ssl_110.x64.deb

   sudo dpkg -i ./dsc-1.2.1-0.ssl_110.x64.deb
   ```

1. 이제 1단계에서 만든 `sudo /opt/microsoft/dsc/Scripts/Register.py <Primary Access Key> <URL>` Python 스크립트를 사용하여 노드를 등록할 수 있습니다. ssh 세션에서 명령을 실행하면 다음 출력이 아래와 비슷하게 표시됩니다.

   ```output
   instance of SendConfigurationApply
   {
        ReturnValue=0
   }
   
   ```

1. 다음 명령을 사용하여 PowerShell에서 등록을 확인할 수 있습니다.

   ```powershell
     Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM  
   ```

   출력은 아래와 같이 표시됩니다.

      :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnode-output.png" alt-text="Get-AzAutomationDscNode 명령의 출력입니다.":::

## <a name="assign-a-node-configuration"></a>노드 구성 할당

[Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) cmdlet을 호출하여 노드 구성 매핑을 설정합니다. 다음 명령을 실행합니다.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $VM

# Set node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName0 `
   -NodeId $node.Id `
   -Force
```

출력은 아래와 같이 표시됩니다.

   :::image type="content" source="media/dsc-linux-powershell/set-azautomationdscnode-output.png" alt-text="Set-AzAutomationDscNode 명령의 출력입니다.":::

## <a name="modify-the-node-configuration-mapping"></a>노드 구성 매핑 수정

[Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) cmdlet을 호출하여 노드 구성 매핑을 수정합니다. 여기서는 `LinuxConfig.IsNotPresent`에서 `LinuxConfig.IsPresent`로의 현재 노드 구성 매핑을 수정합니다. 다음 명령을 실행합니다.

```powershell
# Modify node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName1 `
   -NodeId $node.Id `
   -Force
```

## <a name="check-the-compliance-status-of-a-managed-node"></a>관리되는 노드에 대한 준수 상태 확인

State Configuration이 관리 노드에 대한 일관성 확인을 수행할 때마다 노드는 끌어오기 서버에 상황 보고서를 보냅니다. 다음 예제에서는 [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) cmdlet을 사용하여 관리 노드의 준수 상태를 보고합니다.

```powershell
Get-AzAutomationDscNodeReport `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeId $node.Id `
   -Latest
```

출력은 아래와 같이 표시됩니다.

   :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnodereport-output.png" alt-text="Get-AzAutomationDscNodeReport 명령의 출력입니다.":::

첫 번째 보고서는 즉시 사용할 수 없으며 노드를 사용하도록 설정한 후 최대 30분이 걸릴 수 있습니다. 해당 데이터 사용에 대한 자세한 내용은 [DSC 보고서 서버 사용](/powershell/scripting/dsc/pull-server/reportserver)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

다음 단계는 이 자습서용으로 만들어 더 이상 필요하지 않은 리소스를 삭제하는 데 도움이 됩니다.

1. Automation 계정으로 관리에서 DSC 노드를 제거합니다. PowerShell을 통해 노드를 등록할 수는 없지만 PowerShell을 통해 노드를 등록 취소할 수는 있습니다. 다음 명령을 실행합니다.

    ```powershell
    # Get the ID of the DSC node
    $NodeID = (Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM).Id
    
    Unregister-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Id $NodeID `
       -Force

    # Verify using the same command from Register the Azure Linux VM for an Automation account. A blank response indicates success.
    Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM
    ```

1. Automation의 DSC 노드 구성에서 메타데이터를 제거합니다. 다음 명령을 실행합니다.

    ```powershell
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0 `
       -IgnoreNodeMappings `
       -Force
    
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1 `
       -IgnoreNodeMappings `
       -Force

    # Verify using the same command from Compile configuration in Azure Automation.
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0
    
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1
    ```

      성공적으로 제거되면 `Get-AzAutomationDscNodeConfiguration : NodeConfiguration LinuxConfig.IsNotPresent not found`와 유사한 출력으로 표시됩니다.

1. Automation에서 DSC 구성을 제거합니다. 다음 명령을 실행합니다.

    ```powershell
    Remove-AzAutomationDscConfiguration `
       -AutomationAccountName $automationAccount `
       -ResourceGroupName $resourceGroup `
       -Name $configurationName `
       -Force

    # Verify using the same command from Import configuration to Azure Automation.
    Get-AzAutomationDscConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $configurationName
    ```

   성공적으로 제거되면 `Get-AzAutomationDscConfiguration : Operation returned an invalid status code 'NotFound'`와 유사한 출력으로 표시됩니다.

1. Automation에서 nx 모듈을 제거합니다. 다음 명령을 실행합니다.

    ```powershell
    Remove-AzAutomationModule `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $moduleName -Force

    # Verify using the same command from Install nx module.
    Get-AzAutomationModule `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $moduleName
    ```

   성공적으로 제거되면 `Get-AzAutomationModule : The module was not found. Module name: nx.`와 유사한 출력으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 PowerShell을 사용하여 Azure Linux VM에 Azure Automation State Configuration을 적용하여 원하는 상태를 준수하는지 확인했습니다. 구성 컴퍼지션에 대한 자세한 설명은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [DSC 구성 작성](./compose-configurationwithcompositeresources.md)