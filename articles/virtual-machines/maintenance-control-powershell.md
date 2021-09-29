---
title: PowerShell을 사용하여 Azure 가상 머신에 대한 유지 관리 제어
description: 유지 관리 제어 및 PowerShell을 사용하여 Azure VM에 유지 관리가 적용되는 시점을 제어하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee4f01928c04d31dfafade9c62068ab608aa1e7b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215551"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>유지 관리 제어 및 Azure PowerShell을 사용하여 업데이트 제어

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

유지 관리 제어를 통해 격리된 VM 및 Azure 전용 호스트의 호스트 인프라에 플랫폼 업데이트를 적용하는 시기를 결정할 수 있습니다. 이 항목에서는 Azure PowerShell의 유지 관리 제어 옵션에 대해 설명합니다. 유지 관리 제어 사용의 이점, 제한 사항 및 기타 관리 옵션에 대한 자세한 내용은 [유지 관리 제어를 사용하여 플랫폼 업데이트 관리](maintenance-control.md)를 참조하세요.

확장 집합의 유지 관리 제어에 대한 정보를 찾으려면 [가상 머신 확장 집합에 대한 유지 관리 제어](virtual-machine-scale-sets-maintenance-control.md)를 참조하세요.
 
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

*신뢰할 수 없는 리포지토리* 에서 설치할 것인지 확인하는 메시지가 표시될 수도 있습니다. `Y`를 입력하거나 **모두 예** 를 선택하여 모듈을 설치합니다.


## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

구성의 컨테이너로 리소스 그룹을 만듭니다. 이 예제에서는 *myMaintenanceRG* 라는 리소스 그룹이 *eastus* 에 생성됩니다. 사용하려는 리소스 그룹이 이미 있는 경우 이 부분을 건너뛰고 나머지 예제에서는 리소스 그룹 이름을 고유한 이름으로 바꿀 수 있습니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

[New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration)을 사용하여 유지 관리 구성을 만듭니다. 이 예제에서는 호스트로 범위가 지정된 *myConfig* 라는 유지 관리 구성을 만듭니다. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

`-MaintenanceScope host`를 사용하면 유지 관리 구성이 호스트 업데이트를 제어하는 데 사용됩니다.

다른 위치에서 동일한 이름의 구성을 만들려고 하면 오류가 발생합니다. 구성 이름은 리소스 그룹에서 고유해야 합니다.

[Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)을 사용하여 사용 가능한 유지 관리 구성을 쿼리할 수 있습니다.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>예약된 기간을 사용하여 유지 관리 구성 만들기

Azure가 리소스에 대한 업데이트를 적용하는 예약된 기간을 선언할 수도 있습니다. 이 예제에서는 매달 네 번째 월요일에 5시간 동안 예약된 myConfig라는 유지 관리 구성을 만듭니다. 예약된 기간을 만든 후에는 더 이상 수동으로 업데이트를 적용할 필요가 없습니다.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> 유지 관리 **기간** 은 *2시간* 이상이어야 합니다. 유지 관리 **되풀이** 는 35일에 1회 이상으로 설정되어야 합니다.

유지 관리 **되풀이** 는 일별, 주별 또는 월별로 표시될 수 있습니다. 몇 가지 예는 다음과 같습니다.
 - **일별**- RecurEvery "Day" **또는** "3Days" 
 - **주별**- RecurEvery "3Weeks" **또는** "Week Saturday,Sunday" 
 - **월별**- RecurEvery "Month day23,day24" **또는** "Month Last Sunday" **또는** "Month Fourth Monday"  
      

## <a name="assign-the-configuration"></a>구성 할당

[New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment)를 사용하여 격리된 VM 또는 Azure Dedicated Host에 구성을 할당합니다.

### <a name="isolated-vm"></a>격리된 VM

구성의 ID를 사용하여 VM에 구성을 적용합니다. `-ResourceType VirtualMachines`를 지정하고 VM의 이름을 `-ResourceName`으로 제공하고, VM의 리소스 그룹을 `-ResourceGroupName`으로 제공합니다. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 구성을 적용하려면 `-ResourceType hosts`, `-ResourceParentName`에 호스트 그룹의 이름 및 `-ResourceParentType hostGroups`를 포함해야 합니다. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>보류 중인 업데이트 확인

[Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate)를 사용하여 보류 중인 업데이트가 있는지 확인합니다. `-subscription`을 사용하여 VM의 Azure 구독을 지정합니다(로그인한 것과 다른 경우).

표시할 업데이트가 없는 경우 이 명령은 아무 것도 반환하지 않습니다. 그렇지 않은 경우 PSApplyUpdate 개체를 반환합니다.

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 대해 보류 중인 업데이트를 확인합니다. 이 예제에서 가독성을 위해 출력이 테이블 형식으로 지정됩니다.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대한 보류 중인 업데이트를 확인합니다. 이 예제에서 가독성을 위해 출력이 테이블 형식으로 지정됩니다. 리소스에 대한 값을 고유한 값으로 대체합니다.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>업데이트 적용

[New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate)를 사용하여 보류 중인 업데이트를 적용합니다. 업데이트 호출 적용은 완료하는 데 최대 2시간이 걸릴 수 있습니다.

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 업데이트를 적용하는 요청을 만듭니다.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

성공하면 이 명령은 `PSApplyUpdate` 개체를 반환합니다. `Get-AzApplyUpdate` 명령의 Name 특성을 사용하여 업데이트 상태를 확인할 수 있습니다. [업데이트 상태 확인](#check-update-status)을 참조하세요.

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 업데이트를 적용합니다.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>업데이트 상태 확인
[Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate)를 사용하여 업데이트의 상태를 확인합니다. 아래에 표시된 명령은 `-ApplyUpdateName` 매개 변수에 대해 `default`를 사용하여 최신 업데이트의 상태를 보여줍니다. 업데이트의 이름([New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) 명령으로 반환됨)을 대체하여 특정 업데이트의 상태를 가져올 수 있습니다.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
셀프 유지 관리 기간을 사용하지 않는 경우 LastUpdateTime은 사용자 또는 플랫폼에 의해 시작된 업데이트가 완료된 시간입니다. 유지 관리 제어를 통해 업데이트를 적용한 적이 없는 경우 기본값을 보여줍니다.

### <a name="isolated-vm"></a>격리된 VM

특정 가상 머신에 대한 업데이트를 확인합니다.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대한 업데이트를 확인합니다.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>유지 관리 구성 제거

[Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration)을 사용하여 유지 관리 구성을 삭제합니다.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>다음 단계
자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조하세요.
