---
title: PowerShell을 사용한 Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어
description: 유지 관리 제어 및 PowerShell을 사용하여 Azure 가상 머신 확장 집합에 자동 OS 이미지 업그레이드가 출시되는 시기를 제어하는 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 312acb2715e76587ac213f73c86e785afe2eadcb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695274"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>PowerShell을 사용한 Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어

**적용 대상:** :heavy_check_mark: 균일한 확장 집합

유지 관리 제어를 통해 가상 머신 확장 집합에 자동 게스트 OS 이미지 업그레이드를 적용하는 시기를 결정할 수 있습니다. 이 항목에서는 Azure PowerShell의 유지 관리 제어 옵션에 대해 설명합니다. 유지 관리 제어를 사용하는 방법에 대한 자세한 내용은 [Azure 가상 머신 확장 집합에 대한 유지 관리 제어](virtual-machine-scale-sets-maintenance-control.md)를 참조하세요.


## <a name="enable-the-powershell-module"></a>PowerShell 모듈 사용

`PowerShellGet`이 최신 상태인지 확인합니다.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

`Az.Maintenance` PowerShell 모듈을 설치합니다.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

로컬에 설치하는 경우 관리자 권한으로 PowerShell 프롬프트를 열어야 합니다.

‘신뢰할 수 없는 리포지토리’에서 설치할 것인지 확인하는 메시지가 표시될 수도 있습니다. `Y`를 입력하거나 **모두 예** 를 선택하여 모듈을 설치합니다.

## <a name="connect-to-an-azure-account"></a>Azure 계정에 연결

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 및 [Set-AzAccount](/powershell/module/az.accounts/set-azcontext)를 사용하여 원하는 Azure 계정에 연결합니다.

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

구성의 컨테이너로 리소스 그룹을 만듭니다. 이 예제에서는 *myMaintenanceRG* 라는 리소스 그룹이 *eastus2* 에 생성됩니다. 사용할 리소스 그룹이 이미 있는 경우 이 부분은 건너뛰어도 됩니다. 예제의 나머지 부분에서 리소스 그룹 이름을 사용자 고유의 이름으로 바꾸기만 하면 됩니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

[New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration)을 사용하여 유지 관리 구성을 만듭니다. 이 예제에서는 OS 이미지로 범위가 지정된 *myConfig* 라는 유지 관리 구성을 만듭니다. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> 유지 관리 **기간** 은 ‘5시간’ 이상이어야 합니다. 유지 관리 **되풀이** 는 ‘일’로 설정해야 합니다.

`-MaintenanceScope OSImage`를 사용하면 유지 관리 구성이 게스트 OS 업데이트를 제어하는 데 사용됩니다.

다른 위치에서 동일한 이름의 구성을 만들려고 하면 오류가 발생합니다. 구성 이름은 리소스 그룹에서 고유해야 합니다.

[Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)을 사용하여 사용 가능한 유지 관리 구성을 쿼리할 수 있습니다.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>유지 관리 구성에 가상 머신 확장 집합 연결

유지 관리 구성의 지역 및 구독에 관계없이 모든 유지 관리 구성에 가상 머신 확장 집합을 연결할 수 있습니다. 유지 관리 구성에 옵트인하면 확장 집합의 새 OS 이미지 업데이트가 사용 가능한 다음 유지 관리 기간에 자동으로 예약됩니다.

가상 머신 확장 집합을 유지 관리 구성에 연결하려면 [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment)를 사용합니다.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>자동 OS 업그레이드 사용

유지 관리 제어를 사용하려는 각 가상 머신 확장 집합에 대해 자동 OS 업그레이드를 사용하도록 설정할 수 있습니다. 가상 머신 확장 집합에서 자동 OS 업그레이드를 사용하도록 설정하는 방법에 관한 자세한 내용은 [Azure 가상 머신 확장 집합 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 실행되는 가상 머신의 유지 관리 및 업데이트에 대해 알아보기](maintenance-and-updates.md)
