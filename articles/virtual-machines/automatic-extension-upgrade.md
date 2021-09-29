---
title: Azure에서의 VM 및 확장 집합용 자동 확장 업그레이드
description: Azure에서의 가상 머신 및 가상 머신 확장 집합에 대해 자동 확장 업그레이드를 사용하도록 설정하는 방법을 알아봅니다.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: extensions
ms.workload: infrastructure
ms.topic: how-to
ms.date: 08/10/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a7368ee52808dc56e3532f2edfb41e00c7737a7e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216466"
---
# <a name="automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Azure에서의 VM 및 확장 집합용 자동 확장 업그레이드

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

자동 확장 업그레이드는 Azure VM 및 Azure Virtual Machine Scale Sets에 사용할 수 있습니다. VM 또는 확장 집합에서 자동 확장 업그레이드를 사용하도록 설정하면 확장 게시자가 해당 확장에 대한 새 버전을 릴리스할 때마다 확장이 자동으로 업그레이드됩니다.

 자동 확장 업그레이드의 기능은 다음과 같습니다.
- Azure VM 및 Azure Virtual Machine Scale Sets에 지원됩니다.
- 업그레이드는 (아래에서 자세하게 설명하는) 가용성 우선 배포 모델에 적용됩니다.
- Virtual Machine Scale Set의 경우 확장 집합 가상 머신의 20% 이하가 단일 일괄 처리로 업그레이드됩니다. 최소 일괄 처리 크기는 하나의 가상 머신입니다.
- 모든 VM 크기와 Windows 및 Linux 확장 모두에서 작동합니다.
- 자동 업그레이드는 언제든지 취소할 수 있습니다.
- 자동 확장 업그레이드는 모든 크기의 Virtual Machine Scale Sets에서 사용하도록 설정할 수 있습니다.
- 지원되는 각 확장은 개별적으로 등록되며, 자동으로 업그레이드할 확장을 선택할 수 있습니다.
- 모든 퍼블릭 클라우드 지역에서 지원됩니다.

## <a name="how-does-automatic-extension-upgrade-work"></a>자동 확장 업그레이드 작동 방식
확장 업그레이드 프로세스는 VM에서의 기존 확장 버전을 확장 게시자가 게시한 시점에 적용된 확장의 새 버전으로 대체합니다. 새 확장을 설치한 후에는 VM의 상태를 모니터링합니다. 업그레이드 완료 후 5 분 이내에 VM의 상태가 정상 상태를 벗어나면 확장 버전은 이전 버전으로 롤백됩니다.

실패한 확장 업데이트는 자동으로 다시 시도됩니다. 재시도는 사용자 개입 없이 며칠마다 자동으로 실행됩니다.

### <a name="availability-first-updates"></a>가용성 우선 업데이트
플랫폼 오케스트레이션 업데이트에 대한 가용성 우선 모델은 Azure의 가용성 구성이 여러 가용성 수준에서 적용되게 합니다.

Azure 플랫폼은 업데이트를 진행 중인 가상 머신 그룹을 대상으로 업데이트를 오케스트레이션합니다.

**지역 간:**
- Azure 전역에서의 배포 실패를 방지하기 위해 업데이트는 Azure 전역에서 단계적으로 이동합니다.
- '단계'에는 지역이 하나 이상 존재할 수 있으며, 업데이트는 이전 단계의 적격 VM의 업데이트가 성공했을 때만 다른 단계로 이동합니다.
- 지리적으로 쌍을 이루는 지역은 동시에 업데이트되지 않으며 동일한 지역적 단계에 존재할 수 없습니다.
- 업데이트 성공 여부는 VM 사후 업데이트의 상태를 추적하여 결정합니다. VM 상태는 VM에 대한 플랫폼 상태 표시기를 통해 추적합니다. Virtual Machine Scale Sets의 경우 VM 상태는 애플리케이션 상태 프로브 또는 (확장 집합에 적용된) 애플리케이션 상태 확장을 통해 추적합니다.

**지역 내에서:**
- 다른 가용성 영역에 있는 VM은 동시에 동일한 업데이트로 업데이트되지 않습니다.
- 단일 가용성 집합에 속하지 않는 VM은 최대한 일괄 처리되어 구독에 있는 모든 VM이 동시에 업데이트되지 않게 합니다.  

**'집합' 내에서:**
- 공통 가용성 집합 또는 확장 집합에 있는 모든 VM 은 동시에 업데이트되지 않습니다.  
- 공통 가용성 집합의 VM은 업데이트 도메인 경계 내에서 업데이트되며, 여러 업데이트 도메인의 VM은 동시에 업데이트되지 않습니다.  
- 공통 가상 머신 확장 집합에 있는 VM은 일괄적으로 그룹화되고 업데이트 도메인 경계 내에서 업데이트됩니다.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets의 업그레이드 프로세스
1. 업그레이드 프로세스를 시작하기 전에 오케스트레이터는 전체 확장 집합에서 어떠한 이유로든 비정상 상태인 VM 비율이 20% 이하인지를 확인합니다.

2. 업그레이드 오케스트레이터는 업그레이드할 VM 인스턴스의 일괄 처리를 식별합니다. 업그레이드 일괄 처리는 단일 가상 컴퓨터의 최소 일괄 처리 크기에 따라 총 VM 수의 최대 20%를 포함할 수 있습니다.

3. 애플리케이션 상태 프로브나 애플리케이션 상태 확장을 구성한 확장 집합의 경우 업그레이드 작업은 VM이 정상이 될 때까지 최대 5분 동안 (또는 정의된 상태 프로브 구성이 적용될 때까지) 기다렸다가 다음 배치를 업그레이드합니다. 업그레이드 후 VM의 상태가 정상이 되지 않는다면 기본적으로 VM의 이전 확장 버전이 다시 설치됩니다.

4. 업그레이드 오케스트레이터는 업그레이드 후 비정상 상태가 되는 VM의 백분율도 추적합니다. 업그레이드 프로세스 중에 업그레이드된 인스턴스의 20% 이상이 비정상 상태가 되면 업그레이드가 중지됩니다.

확장 집합의 모든 인스턴스가 업그레이드될 때까지 위의 프로세스가 계속됩니다.

확장 집합 업그레이드 오케스트레이터는 전체 확장 집합 상태를 확인한 후에 모든 배치를 업그레이드합니다. 배치 업그레이드 중에 확장 집합 가상 머신의 상태에 영향을 줄 수 있는, 계획되었거나 계획되지 않은 다른 동시 유지 관리 활동이 진행될 수 있습니다. 확장 집합 인스턴스의 20% 이상이 비정상 상태가 될 경우 현재 배치가 끝나면 확장 집합 업그레이드가 중지됩니다.

## <a name="supported-extensions"></a>지원되는 확장
자동 확장 업그레이드는 다음 확장을 지원합니다(추가 확장이 주기적으로 추가됩니다).
- 종속성 에이전트 – [Linux](./extensions/agent-dependency-linux.md) 및 [Windows](./extensions/agent-dependency-windows.md)
- [애플리케이션 상태 확장](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Linux 및 Windows
- [게스트 구성 확장](./extensions/guest-configuration.md) – Linux 및 Windows
- Key Vault – [Linux](./extensions/key-vault-linux.md) 및 [Windows](./extensions/key-vault-windows.md)


## <a name="enabling-automatic-extension-upgrade"></a>자동 확장 업그레이드 사용 설정

확장에 대해 자동 확장 업그레이드를 사용하도록 설정하려면 `enableAutomaticUpgrade` 속성이 `true`로 설정되고 모든 확장 정의에 개별적으로 추가되었는지 확인해야 합니다.

### <a name="rest-api-for-virtual-machines"></a>Virtual Machines용 REST API
Azure VM의 확장(이 예제에서는 Dependency Agent 확장)에 자동 확장 업그레이드를 사용하도록 설정하려면 다음을 사용하세요.

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets용 REST API
다음을 사용하여 확장을 확장 집합 모델에 추가하세요.

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>가상 머신용 Azure PowerShell
[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) cmdlet 사용:

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets용 Azure PowerShell
[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet을 사용하여 확장을 확장 집합 모델에 추가:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

확장을 추가한 후 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)를 상요하여 확장 집합을 업데이트합니다.


### <a name="azure-cli-for-virtual-machines"></a>Virtual Machines용 Azure CLI
[az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) cmdlet 사용:

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets용 Azure CLI
[az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) cmdlet을 사용하여 확장을 확장 집합 모델에 추가:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>여러 확장을 이용하는 확장 업그레이드

VM 또는 가상 머신 확장 집합에는 자동 확장 업그레이드를 사용하는 확장이 여러 개 존재할 수 있습니다. 또한 동일한 VM 또는 확장 집합에 자동 확장 업그레이드를 사용하지 않는 확장이 존재할 수도 있습니다.  

가상 컴퓨터에 여러 확장 업그레이드를 사용할 수 있는 경우, 업그레이드는 함께 일괄 처리될 수 있지만 각 확장 업그레이드는 가상 컴퓨터에 개별적으로 적용됩니다. 특정 확장이 실패해도 업그레이드 진행 중인 확장은 영향받지 않습니다. 예를 들어 확장 두 개의 업그레이드가 예약된 후 첫 번째 확장 업그레이드가 실패하더라도, 두 번째 확장은 계속 업그레이드됩니다.

또한 자동 확장 업그레이드는 VM 또는 가상 머신 확장 집합에 [확장 시퀀싱](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)으로 구성한 확장이 여러 개 있는 경우에도 적용할 수 있습니다. 확장 시퀀싱은 VM의 최초 배포에 적용되며, 확장에 대한 이후 확장 업그레이드는 개별적으로 적용됩니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [애플리케이션 상태 확장에 대해 알아 보기](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
