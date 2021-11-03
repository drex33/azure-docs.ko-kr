---
title: 작업 실행 명령을 사용하여 Azure의 Windows VM에서 스크립트 실행
description: 이 항목에서는 실행 명령 기능을 사용하여 Azure Windows 가상 머신 내에서 PowerShell 스크립트를 실행하는 방법을 설명합니다.
services: automation
ms.service: virtual-machines
ms.collection: windows
author: cynthn
ms.author: cynthn
ms.date: 10/28/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9501004578068f2c7841fd5f52091f50e97924b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441099"
---
# <a name="run-scripts-in-your-windows-vm-by-using-action-run-commands"></a>작업 실행 명령을 사용하여 Windows VM에서 스크립트 실행

실행 명령 기능은 VM(가상 머신) 에이전트를 사용하여 Azure Windows VM 내에서 PowerShell 스크립트를 실행합니다. 이러한 스크립트는 일반 머신 또는 애플리케이션 관리에 사용할 수 있습니다. 이를 통해 VM 액세스 및 네트워크 문제를 신속하게 진단 및 수정하고 VM을 정상 상태로 되돌릴 수 있습니다.


## <a name="benefits"></a>이점

여러 가지 방법으로 가상 머신에 액세스할 수 있습니다. 실행 명령은 VM 에이전트를 사용하여 원격으로 가상 머신에서 스크립트를 실행할 수 있습니다. Windows VM용 Azure Portal, [REST API](/rest/api/compute/virtual-machines-run-commands/run-command) 또는 [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand)을 통해 실행 명령을 사용합니다.

이 기능은 가상 머신 내에서 스크립트를 실행하려는 모든 시나리오에서 유용합니다. 네트워크 또는 관리 사용자 구성이 잘못되어 RDP 또는 SSH 포트가 열려 있지 않은 가상 머신의 문제를 해결 및 수정하는 유일한 방법 중 하나입니다.

## <a name="restrictions"></a>제한

다음 제한은 실행 명령을 사용할 때 적용됩니다.

* 출력은 마지막 4096바이트로 제한됩니다.
* 스크립트를 실행하는 최소 시간은 약 20초입니다.
* 스크립트는 Windows에서 시스템으로 실행됩니다.
* 한 번에 하나의 스크립트를 실행할 수 있습니다.
* 정보를 요청하는 스크립트(대화형 모드)는 지원되지 않습니다.
* 실행 중인 스크립트는 취소할 수 없습니다.
* 스크립트를 실행할 수 있는 최대 시간은 90분입니다. 그 후에는 시간이 초과됩니다.
* 스크립트의 결과를 반환하려면 VM에서의 아웃바운드 연결이 필요합니다.
* VM 에이전트를 중지하거나 업데이트하는 스크립트를 실행하는 것은 권장하지 않습니다. 그로 인해 확장이 전환 상태로 유지되어 시간 제한이 발생할 수 있습니다.

> [!NOTE]
> 제대로 작동하려면 실행 명령이 Azure 공용 IP 주소에 연결(포트 443)되어야 합니다. 확장이 이러한 엔드포인트에 대해 액세스 권한이 없는 경우 스크립트는 성공적으로 실행되지만 결과를 반환하지는 않습니다. 가상 머신에서 트래픽을 차단하는 경우 `AzureCloud` 태그를 사용하여 Azure 공용 IP 주소로 트래픽을 허용하도록 [서비스 태그](../../virtual-network/network-security-groups-overview.md#service-tags)를 사용할 수 있습니다.
> 
> VM 에이전트 상태가 준비되지 않은 경우 명령 실행 기능이 작동하지 않습니다. Azure Portal의 VM 속성에서 에이전트 상태를 확인합니다.

## <a name="available-commands"></a>사용 가능한 명령

이 표에서는 Windows VM에 대해 사용할 수 있는 명령 목록을 보여줍니다. **RunPowerShellScript** 명령을 사용하여 원하는 모든 사용자 지정 스크립트를 실행할 수 있습니다. Azure CLI 또는 PowerShell을 사용하여 명령을 실행하는 경우 `--command-id` 또는 `-CommandId` 매개 변수에 대해 제공하는 값은 다음 목록 값 중 하나여야 합니다. 사용할 수 있는 명령이 아닌 값을 지정하면 다음과 같은 오류가 표시됩니다.

```error
The entity was not found in this Azure location
```
<br>

| **이름** | **설명** |
|---|---|
| **RunPowerShellScript** | PowerShell 스크립트 실행 |
| **DisableNLA** | 네트워크 수준 인증 사용 안 함 |
| **DisableWindowsUpdate** | Windows 업데이트 자동 업데이트 사용 안 함 |
| **EnableAdminAccount** | 로컬 관리자 계정이 비활성화됐는지 확인하여 그렇다면 활성화합니다. |
| **EnableEMS** | EnableS EMS |
| **EnableRemotePS** | 원격 PowerShell을 사용하도록 설정하려면 컴퓨터를 구성합니다. |
| **EnableWindowsUpdate** | Windows 업데이트 자동 업데이트 사용 |
| **IPConfig** | TCP/IP에 바인딩된 각 어댑터의 IP 주소, 서브넷 마스크 및 기본 게이트웨이에 대한 자세한 정보를 표시합니다. |
| **RDPSetting** | 레지스트리 설정 및 도메인 정책 설정을 확인합니다. 머신이 도메인의 일부이거나 설정을 기본값으로 수정하는 경우 정책 작업을 제안합니다. |
| **ResetRDPCert** | RDP 수신기에 연결된 TLS/SSL 인증서를 제거하고 RDP 수신기 보안을 기본값으로 복원합니다. 인증서에 문제가 있는 경우 이 스크립트를 사용합니다. |
| **SetRDPPort** | 원격 데스크톱 연결에 대한 기본 또는 사용자 지정 포트 번호를 설정합니다. 포트에 인바운드 액세스를 위한 방화벽 규칙을 사용하도록 설정합니다. |

## <a name="azure-cli"></a>Azure CLI

다음 예제는 [az vm run-command](/cli/azure/vm/run-command#az_vm_run_command_invoke) 명령을 사용하여 Azure Windows VM에서 셸 스크립트를 실행합니다.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)의 VM으로 이동하여 왼쪽 메뉴의 **작업** 아래에서 **실행 명령** 을 선택합니다. VM에서 실행에 사용할 수 있는 명령 목록이 표시됩니다.

![명령 목록](./media/run-command/run-command-list.png)

실행할 명령을 선택하세요. 명령 중 일부에는 선택 또는 필수 입력 매개 변수가 있을 수 있습니다. 이러한 명령의 경우 매개 변수는 입력 값을 제공하기 위한 텍스트 필드로 표시됩니다. 각 명령의 경우 **스크립트 보기** 를 확장하여 실행되는 스크립트를 볼 수 있습니다. **RunPowerShellScript** 는 사용자 고유의 사용자 지정 스크립트를 제공할 수 있으므로 다른 명령과 다릅니다.

> [!NOTE]
> 기본 제공된 명령은 편집할 수 없습니다.

명령을 선택한 후 **실행** 을 선택하여 스크립트를 실행합니다. 스크립트가 완료되면 출력 창에 출력 및 오류가 반환됩니다. 다음 스크린샷은 **RDPSettings** 명령을 실행하는 작업의 예제 출력을 보여줍니다.

![명령 실행 스크립트 출력](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

다음 예제에서는 [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) cmdlet을 사용하여 Azure VM에서 PowerShell 스크립트를 실행합니다. cmdlet에서는 `-ScriptPath` 매개 변수에서 참조되는 스크립트가 cmdlet이 실행되는 위치의 로컬이어야 합니다.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>명령 실행에 대한 액세스 제한

실행 명령을 나열하거나 명령의 세부 정보를 표시하려면 구독 수준에서 `Microsoft.Compute/locations/runCommands/read` 권한이 필요합니다. 기본 제공 [Reader](../../role-based-access-control/built-in-roles.md#reader) 역할 및 상위 수준에 이 권한이 있습니다.

명령을 실행하려면 `Microsoft.Compute/virtualMachines/runCommand/action` 권한이 필요합니다. [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 및 상위 수준에 이 권한이 있습니다.

[기본 제공](../../role-based-access-control/built-in-roles.md) 역할 중 하나를 사용하거나 [사용자 지정](../../role-based-access-control/custom-roles.md) 역할을 생성하여 실행 명령을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

VM에서 스크립트 및 명령을 원격으로 실행하는 다른 방법에 대해 알아보려면 [Windows VM에서 스크립트 실행](run-scripts-in-vm.md)을 참조하세요.
