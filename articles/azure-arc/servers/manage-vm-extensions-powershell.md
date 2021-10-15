---
title: Azure PowerShell을 통해 VM 확장 사용
description: 이 문서에서는 Azure PowerShell을 사용하여 하이브리드 클라우드 환경에서 실행되는 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 10/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c759510c3ab81b15b65315015a16507dadf2658a
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074573"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell을 통해 Azure VM 확장 사용

이 문서에서는 Azure PowerShell을 사용하여 Azure Arc 지원 서버에서 지원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 머신에 배포하고 제거하는 방법을 보여 줍니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포 및 관리를 지원하지 않습니다. Azure VMs에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

Azure PowerShell 사용하여 Azure Arc 지원 서버에서 관리되는 하이브리드 서버에서 VM 확장을 관리하기 전에 모듈을 설치해야 `Az.ConnectedMachine` 합니다. 이러한 관리 작업은 워크스테이션에서 수행할 수 있으며 Azure Arc 지원 서버에서 실행할 필요가 없습니다.

Azure Arc 지원 서버에서 다음 명령을 실행합니다.

`Install-Module -Name Az.ConnectedMachine`.

설치가 완료되면 다음 메시지가 반환됩니다.

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>확장 사용

Azure Arc 지원 서버에서 VM 확장을 사용하도록 설정하려면 , - 및 매개 변수와 함께 [New-AzConnectedMachineExtension을](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` `-Settings` 사용합니다.

다음 예제에서는 Azure Arc 지원 Linux 서버에서 Log Analytics VM 확장을 사용하도록 설정합니다.

```powershell
$Setting = @{ "workspaceId" = "workspaceId" }
$protectedSetting = @{ "workspaceKey" = "workspaceKey" }
New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Azure Arc 지원 Windows 서버에서 Log Analytics VM 확장을 사용하도록 설정하려면 `-ExtensionType` 이전 예제에서 매개 변수의 값을 로 변경합니다. `"MicrosoftMonitoringAgent"`

다음 예제에서는 Azure Arc 지원 서버에서 사용자 지정 스크립트 확장을 사용하도록 설정합니다.

```powershell
$Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
New-AzConnectedMachineExtension -Name "custom" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

다음 예제에서는 Azure Arc 지원 Windows 서버에서 Microsoft Antimalware 확장을 사용하도록 설정합니다.

```powershell
$Setting = @{ "AntimalwareEnabled" = $true }
New-AzConnectedMachineExtension -Name "IaaSAntimalware" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Azure.Security" -Settings $Setting -ExtensionType "IaaSAntimalware"
```

### <a name="key-vault-vm-extension"></a>Key Vault VM 확장

> [!WARNING]
> PowerShell 클라이언트는 오류와 함께 akvvm_service에 실패를 일으키는 settings.json에서 `"`에 `\`를 추가하는 경우가 많습니다.`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

다음 예제에서는 Azure Arc 지원 서버에서 Key Vault VM 확장을 사용하도록 설정합니다.

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>설치된 확장 나열

Azure Arc 지원 서버에서 VM 확장 목록을 얻으려면 및 매개 변수와 함께 [Get-AzConnectedMachineExtension을](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) `-MachineName` `-ResourceGroupName` 사용합니다.

예제:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>설치된 확장 제거

Azure Arc 지원 서버에서 설치된 VM 확장을 제거하려면 및 매개 변수와 함께 [Remove-AzConnectedMachineExtension을](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) `-Name` `-MachineName` `-ResourceGroupName` 사용합니다.

예를 들어, Linux용 Log Analytics VM 확장을 제거하려면 다음 명령을 실행합니다.

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>다음 단계

- [Azure Portal](manage-vm-extensions-portal.md)에서 [Azure CLI](manage-vm-extensions-cli.md)를 사용하거나 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
