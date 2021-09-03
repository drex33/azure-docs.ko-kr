---
title: Azure PowerShell을 통해 VM 확장 사용
description: 이 문서에서는 Azure PowerShell을 사용하여 하이브리드 클라우드 환경에서 실행되는 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 08/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a626e42b5447cafcf0fe99876eb0146a02c25f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536043"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell을 통해 Azure VM 확장 사용

이 문서에서는 Azure PowerShell을 사용하여 Azure Arc 지원 서버에서 지원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 머신에 배포하고 제거하는 방법을 보여 줍니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포 및 관리를 지원하지 않습니다. Azure VMs에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

Azure PowerShell을 사용하여 Arc 지원 서버에서 관리하는 하이브리드 서버에서 VM 확장을 관리하려면 먼저 `Az.ConnectedMachine` 모듈을 설치해야 합니다. Arc 지원 서버에서 다음 명령을 실행합니다.

`Install-Module -Name Az.ConnectedMachine`.

설치가 완료되면 다음 메시지가 반환됩니다.

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>확장 사용

Arc 지원 서버에서 VM 확장을 사용하려면 [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension)을 `-Name`, `-ResourceGroupName`, `-MachineName`, `-Location`, `-Publisher`, -`ExtensionType` 및 `-Settings` 매개 변수와 함께 사용합니다.

다음 예제에서는 Arc 지원 Linux 서버에서 Log Analytics VM 확장을 사용하도록 설정합니다.

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Arc 지원 Windows 서버에서 Log Analytics VM 확장을 사용하려면 이전 예제에서 `-ExtensionType` 매개 변수 값을 `"MicrosoftMonitoringAgent"`로 변경합니다.

다음 예제에서는 Arc 지원 서버에서 사용자 지정 스크립트 확장을 사용하도록 설정합니다.

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute"  -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension"></a>Key Vault VM 확장 

> [!WARNING]
> PowerShell 클라이언트는 오류와 함께 akvvm_service에 실패를 일으키는 settings.json에서 `"`에 `\`를 추가하는 경우가 많습니다.`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

다음 예제에서는 Arc 지원 서버에서 Key Vault VM 확장을 사용하도록 설정합니다.

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

Arc 지원 서버에서 VM 확장 목록을 가져오려면 [AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension)을 `-MachineName` 및 `-ResourceGroupName` 매개 변수와 함께 사용합니다.

예제:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>설치된 확장 제거

Arc 지원 서버에서 설치된 VM 확장을 제거하려면 [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension)을 `-Name`, `-MachineName` 및 `-ResourceGroupName` 매개 변수와 함께 사용합니다.

예를 들어, Linux용 Log Analytics VM 확장을 제거하려면 다음 명령을 실행합니다.

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>다음 단계

- [Azure Portal](manage-vm-extensions-portal.md)에서 [Azure CLI](manage-vm-extensions-cli.md)를 사용하거나 [Azure Resource Manager 템플릿](manage-vm-extensions-template.md)을 사용하여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
