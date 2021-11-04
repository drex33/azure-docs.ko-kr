---
title: Azure PowerShell을 통해 VM 확장 사용
description: 이 문서에서는 Azure PowerShell을 사용하여 하이브리드 클라우드 환경에서 실행되는 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 03fb598989c7f308b3e481824e17d423f6d31824
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470626"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell을 통해 Azure VM 확장 사용

이 문서에서는 Azure PowerShell를 사용 하 여 azure Arc 사용 서버에서 지 원하는 azure VM 확장을 Linux 또는 Windows 하이브리드 컴퓨터에 배포, 업데이트 및 제거 하는 방법을 보여 줍니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포와 관리를 지원하지 않습니다. Azure VMs에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

Azure PowerShell를 사용 하 여 Azure Arc 사용 서버에서 관리 하는 하이브리드 서버에서 VM 확장을 관리 하려면 먼저 모듈을 설치 해야 `Az.ConnectedMachine` 합니다. 이러한 관리 작업은 워크스테이션에서 수행할 수 있으며, Azure Arc 사용 서버에서 실행할 필요가 없습니다.

Azure Arc 사용 서버에서 다음 명령을 실행 합니다.

`Install-Module -Name Az.ConnectedMachine`.

설치가 완료되면 다음 메시지가 반환됩니다.

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>확장 사용

Azure Arc 사용 서버에서 VM 확장을 사용 하도록 설정 하려면,, [](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) ,, `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` ,- `ExtensionType` 및 `-Settings` 매개 변수와 함께 AzConnectedMachineExtension를 사용 합니다.

다음 예제에서는 Azure Arc 사용 Linux 서버에서 Log Analytics VM 확장을 사용 하도록 설정 합니다.

```powershell
$Setting = @{ "workspaceId" = "workspaceId" }
$protectedSetting = @{ "workspaceKey" = "workspaceKey" }
New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Azure Arc 사용 Windows 서버에서 Log Analytics VM 확장을 사용 하도록 설정 하려면 `-ExtensionType` 이전 예제에서 매개 변수의 값을로 변경 합니다 `"MicrosoftMonitoringAgent"` .

다음 예제에서는 Azure Arc 사용 서버에서 사용자 지정 스크립트 확장을 사용 하도록 설정 합니다.

```powershell
$Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
New-AzConnectedMachineExtension -Name "custom" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

다음 예에서는 Azure Arc 사용 Windows 서버에서 Microsoft Antimalware 확장을 사용 하도록 설정 합니다.

```powershell
$Setting = @{ "AntimalwareEnabled" = $true }
New-AzConnectedMachineExtension -Name "IaaSAntimalware" -ResourceGroupName "myResourceGroup" -MachineName "myMachineName" -Location "regionName" -Publisher "Microsoft.Azure.Security" -Settings $Setting -ExtensionType "IaaSAntimalware"
```

### <a name="key-vault-vm-extension"></a>Key Vault VM 확장

> [!WARNING]
> PowerShell 클라이언트는 오류와 함께 akvvm_service에 실패를 일으키는 settings.json에서 `"`에 `\`를 추가하는 경우가 많습니다.`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

다음 예제에서는 Azure Arc 사용 서버에서 Key Vault VM 확장을 사용 하도록 설정 합니다.

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

Azure Arc 사용 서버에서 VM 확장 목록을 가져오려면 및 매개 변수와 함께 [AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) 를 사용 `-MachineName` `-ResourceGroupName` 합니다.

예제:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="update-extensions"></a>확장 업데이트

설치 된 확장을 다시 구성 하기 위해,, [](/powershell/module/az.connectedmachine/update-azconnectedmachineextension) `-Name` `-MachineName` `-ResourceGroupName` 및 `-Settings` 매개 변수와 함께 AzConnectedMachineExtension cmdlet을 사용할 수 있습니다.

확장에 대 한 변경 내용을 제공 하는 다양 한 방법을 이해 하려면 cmdlet에 대 한 참조 문서를 참조 하세요.

## <a name="remove-extensions"></a>확장 제거

Azure Arc 사용 서버에서 설치 된 VM 확장을 제거 하려면 [](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) `-Name` , 및 매개 변수와 함께 AzConnectedMachineExtension를 사용 `-MachineName` `-ResourceGroupName` 합니다.

예를 들어, Linux용 Log Analytics VM 확장을 제거하려면 다음 명령을 실행합니다.

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>다음 단계

- [Azure Portal](manage-vm-extensions-portal.md)에서 [Azure CLI](manage-vm-extensions-cli.md)를 사용하거나 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
