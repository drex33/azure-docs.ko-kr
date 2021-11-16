---
title: Azure Automation State Configuration 사용
description: 이 문서에서는 Azure Automation State Configuration을 사용하여 관리할 수 있도록 머신을 설정하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc14ccca588a51271f3cb52c90904375b6739802
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484449"
---
# <a name="enable-azure-automation-state-configuration"></a>Azure Automation State Configuration 사용

이 토픽에서는 Azure Automation State Configuration을 사용하여 관리할 수 있도록 머신을 설정하는 방법을 설명합니다. 이 서비스에 대한 자세한 내용은 [Azure Automation State Configuration 개요](automation-dsc-overview.md)를 참조하세요.

## <a name="enable-azure-vms"></a>Azure VM을 사용하도록 설정

Azure Automation State Configuration을 사용하면 Azure Portal, Azure Resource Manager 템플릿 또는 PowerShell을 사용하여 Azure VM의 구성을 관리할 수 있습니다. Azure VM Desired State Configuration은 내부적으로, 그리고 관리자가 VM에 원격으로 연결할 필요 없이 VM을 Azure Automation State Configuration에 등록합니다. Azure 확장은 비동기적으로 실행되므로, 진행 상황을 추적하는 단계가 [VM 설치 상태 확인](#check-status-of-vm-setup)에 제공됩니다.

> [!NOTE]
>Linux 노드에 DSC를 배포하면 **/tmp** 폴더가 사용됩니다. `nxautomation` 같은 모듈이 임시로 다운로드되어 확인된 후 적절한 위치에 설치됩니다. 모듈이 올바르게 설치되도록, Linux용 Log Analytics 에이전트에 **/tmp** 폴더에 대한 읽기/쓰기 권한이 있어야 합니다.<br><br>
>Linux용 Log Analytics 에이전트는 `omsagent` 사용자로 실행됩니다. `omsagent` 사용자에게 쓰기 권한을 부여 하려면 `setfacl -m u:omsagent:rwx /tmp` 명령을 실행합니다.

### <a name="enable-a-vm-using-azure-portal"></a>Azure Portal을 사용하여 VM을 사용하도록 설정

[Azure Portal](https://portal.azure.com/)을 통해 Azure VM에서 State Configuration을 사용하려면 다음을 수행합니다.

1. VM을 사용하도록 설정할 Azure Automation 계정으로 이동합니다. 

2. State Configuration 페이지에서 **노드** 탭을 선택한 다음, **추가** 를 클릭합니다.

3. 사용하도록 설정할 VM을 선택합니다.

4. 필요한 상태 확장이 설치된 PowerShell이 머신에 없고 전원 상태가 실행 중인 경우 **연결** 을 클릭합니다.

5. **등록** 에서 본인의 사용 사례에 필요한 [PowerShell DSC Local Configuration Manager 값](/powershell/scripting/dsc/managing-nodes/metaConfig)을 입력합니다. 원한다면 VM에 할당할 노드 구성을 입력할 수도 있습니다.

![VM을 사용하도록 설정](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 통해 VM을 사용하도록 설정

Azure Resource Manager 템플릿을 사용하여 State Configuration을 위한 VM을 설치하고 사용하도록 설정할 수 있습니다. State Configuration에 기존 VM을 사용하도록 설정하는 예제 템플릿은 [Desired State Configuration 서비스를 통해 관리되는 서버](https://azure.microsoft.com/resources/templates/automation-configuration/)를 참조하세요. 가상 머신 확장 집합을 관리하는 경우 [Azure Automation을 통해 관리되는 가상 머신 확장 집합 구성](https://azure.microsoft.com/resources/templates/vmss-automation-dsc/)의 예제 템플릿을 참조하세요.

### <a name="enable-machines-using-powershell"></a>PowerShell을 통해 머신을 사용하도록 설정

PowerShell에서 [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet을 사용하여 State Configuration에 사용할 VM을 설정할 수 있습니다. 

> [!NOTE]
>`Register-AzAutomationDscNode` cmdlet은 Windows 확장만 트리거하기 때문에 현재는 Windows를 실행하는 머신에 대해서만 구현됩니다.

### <a name="register-vms-across-azure-subscriptions"></a>여러 Azure 구독에서 VM 등록

다른 Azure 구독의 VM을 등록하는 가장 좋은 방법은 Azure Resource Manager 배포 템플릿에서 DSC 확장을 사용하는 것입니다. 예제는 [Desired State Configuration 확장과 Azure Resource Manager 템플릿](../virtual-machines/extensions/dsc-template.md)에 제공되어 있습니다.

템플릿에서 매개 변수로 사용할 등록 키와 등록 URL을 찾으려면 [등록을 통해 안전하게 머신을 사용하도록 설정](#enable-machines-securely-using-registration)을 참조하세요.

## <a name="enable-physicalvirtual-windows-machines"></a>물리적/가상 Windows 머신을 사용하도록 설정

온-프레미스 또는 다른 클라우드 환경에서 실행되는 Windows 서버(AWS EC2 인스턴스 포함)가 Azure Automation State Configuration을 사용하도록 설정할 수 있습니다. 서버에는 [Azure에 대한 아웃바운드 액세스 권한](automation-dsc-overview.md#network-planning)이 있어야 합니다.

1. State Configuration에 사용하려는 머신에 최신 버전의 [WMF 5](https://aka.ms/wmf5latest)가 설치되었는지 확인합니다. 또한 머신을 사용하도록 설정하는 데 사용할 컴퓨터에 WMF 5가 설치되어 있어야 합니다.
1. [DSC 메타 구성 생성](#generate-dsc-metaconfigurations)의 지침에 따라 필요한 DSC 메타 구성이 포함된 폴더를 만듭니다. 
1. 다음 cmdlet을 사용하여 PowerShell DSC 메타 구성을 머신에 원격으로 적용하여 사용하도록 설정합니다. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC 메타 구성을 원격으로 적용할 수 없는 경우 사용하도록 설정하려는 머신에 **metaconfigurations** 폴더를 복사합니다. 그런 다음, 머신에서 로컬로 [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager)를 호출하는 코드를 추가합니다.
1. Azure Portal 또는 cmdlet을 사용하여 머신이 Azure Automation 계정에 등록된 State Configuration 노드로 표시되는지 확인합니다.

## <a name="enable-physicalvirtual-linux-machines"></a>물리적/가상 Linux 머신을 사용하도록 설정

온-프레미스 또는 다른 클라우드 환경에서 실행되는 Linux 서버를 State Configuration에 사용하도록 설정할 수 있습니다. 서버에는 [Azure에 대한 아웃바운드 액세스 권한](automation-dsc-overview.md#network-planning)이 있어야 합니다.

1. State Configuration에 사용하려는 머신에 최신 버전의 [Linux용 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux)이 설치되었는지 확인합니다.
2. [PowerShell DSC Local Configuration Manager 기본값](/powershell/scripting/dsc/managing-nodes/metaConfig4)이 사용 사례와 일치하고, 머신이 State Configuration에서 끌어오고 State Configuration에 보고하도록 만들려면 다음을 수행합니다.

   - 사용하도록 설정할 각 Linux 머신에서, `Register.py`를 사용하여 PowerShell DSC Local Configuration Manager 기본값으로 머신을 사용하도록 설정합니다.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Automation 계정의 등록 키와 등록 URL을 찾으려면 [등록을 통해 안전하게 머신을 사용하도록 설정](#enable-machines-securely-using-registration)을 참조하세요.

3. PowerShell DSC LCM(Local Configuration Manager) 기본값이 본인의 사용자 사용 사례와 일치하지 않거나 Azure Automation State Configuration에만 보고하는 머신을 사용하려면 4-7단계를 따릅니다. 그렇지 않으면 바로 7단계를 진행합니다.

4. [DSC 메타 구성 생성](#generate-dsc-metaconfigurations) 섹션의 지침에 따라 필요한 DSC 메타 구성이 포함된 폴더를 만듭니다.

5. State Configuration에 사용할 머신을 설정하는 데 사용되는 컴퓨터에 최신 버전의 [WMF 5](https://aka.ms/wmf5latest)가 설치되었는지 확인합니다.

6. PowerShell DSC 메타 구성을 머신에 원격으로 적용하여 사용하도록 설정하는 다음과 같은 코드를 추가합니다.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. PowerShell DSC 메타 구성을 원격으로 적용할 수 없는 경우 4단계에서 설명한 폴더의 원격 머신에 해당하는 메타 구성을 Linux 머신에 복사합니다.

8. State Configuration에 사용할 각 Linux 머신에서 로컬로 `Set-DscLocalConfigurationManager.py`를 호출하는 코드를 추가합니다.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Azure Portal 또는 cmdlet을 통해, 사용하도록 설정하려는 머신이 Azure Automation 계정에 등록된 DSC 노드로 표시되는지 확인합니다.

## <a name="generate-dsc-metaconfigurations"></a>DSC 메타 구성 생성

State Configuration에 사용할 머신을 설정하려면 [DSC 메타 구성](/powershell/scripting/dsc/managing-nodes/metaConfig)을 생성하면 됩니다. 이 구성은 Azure Automation State Configuration에서 끌어오고 Azure Automation State Configuration에 보고하도록 DSC 에이전트에 지시합니다. Azure Automation State Configuration의 DSC 메타 구성은 PowerShell DSC 구성 또는 Azure Automation PowerShell cmdlet을 사용하여 생성할 수 있습니다.

> [!NOTE]
> DSC 메타 구성에는 Automation 계정에서 관리에 사용할 머신을 설정하는 데 필요한 비밀이 포함됩니다. 사용한 후에 만들거나 삭제한 DSC 메타 구성을 제대로 보호해야 합니다.

메타 구성에 대한 프록시 지원은 Windows PowerShell DSC 엔진인 [Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)를 통해 제어됩니다. LCM은 모든 대상 노드에서 실행되며 DSC 구성 스크립트에 포함된 구성 리소스를 호출하는 일을 담당합니다. `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` 및 `ReportServerWeb` 블록에 필요한 대로 `ProxyURL` 및 `ProxyCredential` 속성의 정의를 포함시켜 메타 구성에 프록시 지원을 포함할 수 있습니다. URL 설정은 `ProxyURL = "http://172.16.3.6:3128";` 형식입니다. `ProxyCredential` 속성은 [Azure Automation에서 자격 증명 관리](shared-resources/credentials.md)에 설명된 대로 `PSCredential` 개체로 설정됩니다. 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC 구성을 사용하여 DSC 메타 구성 생성

1. 로컬 환경의 머신에서 관리자 권한으로 VSCode(또는 선호하는 편집기)를 엽니다. 이 컴퓨터에는 최신 버전의 [WMF 5](https://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.
1. 다음 스크립트를 로컬로 복사합니다. 이 스크립트는 메타 구성을 만들기 위한 PowerShell DSC 구성 및 메타 구성 생성을 시작하는 명령을 포함합니다.

    > [!NOTE]
    > State Configuration Node Configuration 이름은 Azure Portal에서 대/소문자를 구분합니다. 대/소문자가 일치하지 않는 경우 **노드** 탭에 노드가 표시되지 않습니다.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Automation 계정의 등록 키 및 URL과 사용하도록 설정할 머신의 이름을 입력합니다. 모든 다른 매개 변수는 선택 사항입니다. Automation 계정의 등록 키와 등록 URL을 찾으려면 [등록을 통해 안전하게 머신을 사용하도록 설정](#enable-machines-securely-using-registration)을 참조하세요.

1. 머신이 Azure Automation State Configuration에 DSC 상태 정보를 보고하지만 구성 또는 PowerShell 모듈을 끌어오지 않도록 하려면 `ReportOnly` 매개 변수를 true로 설정합니다.

1. `ReportOnly`를 설정하지 않으면 머신이 Azure Automation State Configuration에 DSC 상태 정보를 보고하고 구성 또는 PowerShell 모듈을 끌어옵니다. `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` 및 `ReportServerWeb` 블록에서 매개 변수를 적절하게 설정합니다.

1. 스크립트를 실행합니다. 이제 **DscMetaConfigs** 라는 작업 디렉터리 폴더가 있고, 이 폴더에는 사용하도록 설정할(관리자 권한으로) 머신의 PowerShell DSC 메타 구성이 포함되어 있습니다.

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Azure Automation cmdlet을 사용하여 DSC 메타 구성 생성

PowerShell DSC LCM 기본값이 사용 사례와 일치하고 머신이 Azure Automation State Configuration에서 끌어오고 Azure Automation State Configuration에 보고하도록 설정하려는 경우 Azure Automation cmdlet을 사용하여 필요한 DSC 메타 구성을 보다 간단하게 생성할 수 있습니다.

1. 로컬 환경의 머신에서 관리자 권한으로 PowerShell 콘솔이나 VSCode를 엽니다.
2. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount)를 사용하여 Azure Resource Manager에 연결합니다.
3. 노드를 설정하는 Automation 계정에서 사용하도록 설정하려는 머신의 PowerShell DSC 메타 구성을 다운로드합니다.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 이제 **DscMetaConfigs** 라는 폴더가 있고, 이 폴더에는 사용하도록 설정할(관리자 권한으로) 머신의 PowerShell DSC 메타 구성이 포함되어 있습니다.

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>등록을 통해 안전하게 머신을 사용하도록 설정

WMF 5 DSC 등록 프로토콜을 통해 Azure Automation 계정에 안전하게 머신을 사용하도록 설정할 수 있습니다. 이 프로토콜을 사용하면 DSC 노드는 Azure Automation State Configuration을 포함하여 PowerShell DSC 끌어오기 또는 보고서 서버에 인증할 수 있습니다. 노드는 등록 URL에 있는 서버에 등록되고 등록 키를 사용하여 인증합니다. 등록하는 동안 DSC 노드와 DSC 끌어오기/보고 서버는 이 노드가 서버 게시-등록을 인증하는 데 사용할 고유 인증서를 협상합니다. 이 프로세스는 노드가 손상되어 악의적 동작을 수행하는 경우처럼 사용하도록 설정된 노드가 다른 노드를 가장하는 것을 방지합니다. 등록 후 해당 등록 키는 다시 인증에 사용되지 않으며 노드에서 삭제됩니다.

Azure Portal의 **계정 설정** 아래에 있는 **키** 에서 상태 구성 등록 프로토콜에 필요한 정보를 얻을 수 있습니다. 

![Azure Automation 키 및 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 등록 URL은 [키] 페이지의 URL 필드입니다.
- 등록 키는 [키] 페이지의 **기본 액세스 키** 필드 또는 **보조 액세스 키** 필드의 값입니다. 둘 중 하나를 사용할 수 있습니다.

언제든지 [키] 페이지에서 Automation 계정의 기본 및 보조 액세스 키를 다시 생성하여 보안을 강화할 수 있습니다. 키를 다시 생성하면 그 이후에 이루어지는 노드 등록에 이전 키를 사용할 수 없습니다.

## <a name="re-register-a-node"></a>노드 다시 등록

Azure Automation State Configuration에서 머신을 DSC 노드로 등록하고, 이후에 해당 노드를 다시 등록해야 하는 여러 가지 이유가 있습니다.

- **인증서 갱신.** Windows Server 2019 이전 Windows Server 버전의 경우 각 노드는 1년 후 만료되는 인증용 인증서의 고유 인증서를 자동으로 협상합니다. 갱신 없이 인증서가 만료되면 노드가 Azure Automation와 통신할 수 없으며 `Unresponsive`로 표시됩니다. 현재 PowerShell DSC 등록 프로토콜은 만료가 임박한 인증서를 자동으로 갱신할 수 없으며, 따라서 1년 기간 후 노드를 다시 등록해야 합니다. 다시 등록하기 전에, 각 노드에서 WMF 5 RTM을 실행하고 있는지 확인합니다. 

    인증서 만료 시점으로부터 90일 이내에 또는 인증서 만료 이후에 재등록을 수행하면 새 인증서가 생성 및 사용됩니다. 이 문제를 해결하는 방법은 Windows Server 2019 이상에 포함되어 있습니다.

- **DSC LCM 값 변경.** 노드를 처음 등록하는 동안 설정된 [PowerShell DSC LCM 값](/powershell/scripting/dsc/managing-nodes/metaConfig4)(예: `ConfigurationMode`)을 변경해야 할 수도 있습니다. 현재는 재등록을 통해서만 DSC 에이전트 값을 변경할 수 있습니다. 노드에 할당된 노드 구성 값은 예외입니다. 이 값은 Azure 자동화 DSC에서 직접 변경할 수 있습니다.

이 문서에서 설명한 방법 중 하나를 사용하여 처음에 노드를 등록할 때처럼 노드를 다시 등록할 수 있습니다. 다시 등록하기 전에 Azure Automation State Configuration에서 노드를 등록 취소할 필요가 없습니다.

## <a name="check-status-of-vm-setup"></a>VM 설정 상태 확인

State Configuration을 사용하면 간단하게 Azure Windows VM을 구성 관리에 사용할 수 있습니다. 내부적으로 Azure VM 필요 상태 구성 확장은 VM을 Azure Automation 상태 구성에 등록하는 데 사용됩니다. VM Desired State Configuration 확장은 비동기적으로 실행되므로 진행 상황 추적과 실행 문제 해결이 중요할 수 있습니다.

> [!NOTE]
> Azure VM Desired State Configuration 확장을 사용하는 State Configuration에 Azure Windows VM을 사용하도록 설정하는 방법 중 무엇을 사용하든, Azure Automation이 VM을 등록되었다고 표시할 때까지 최대 1시간이 소요될 수 있습니다. 이러한 지연은 State Configuration에 VM을 사용하도록 설정하는 데 필요한 Azure VM Desired State Configuration 확장이 VM에 WMF 5를 설치하는 시간 때문에 발생합니다.

Azure VM Desired State Configuration 확장의 상태를 보는 방법은 다음과 같습니다.

1. Azure Portal에서, 사용되는 VM으로 이동합니다.
2. **설정** 에서 **확장** 을 클릭합니다. 
3. 운영 체제에 따라 **DSC** 또는 **DSCForLinux** 를 선택합니다. 
4. 자세한 내용을 보려면 **자세한 상태 보기** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation State Configuration 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation State Configuration에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation State Configuration 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation State Configuration을 사용하는 예제는 [Chocolatey를 사용한 지속적인 배포 설정](automation-dsc-cd-chocolatey.md)을 참조하세요.
- 문제 해결 정보는 [Azure Automation State Configuration 문제 해결](./troubleshoot/desired-state-configuration.md)을 참조하세요.
