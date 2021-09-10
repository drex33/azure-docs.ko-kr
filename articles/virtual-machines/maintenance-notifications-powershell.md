---
title: PowerShell을 사용하여 Azure VM에 대한 유지 관리 알림 받기
description: PowerShell을 사용하여 Azure에서 실행 중인 가상 머신에 대한 유지 관리 알림을 확인하고 셀프 서비스 유지 관리를 시작합니다.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9c03280532a1296d49c26b8e7502c5cbac896f8e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687735"
---
# <a name="handling-planned-maintenance-using-powershell"></a>PowerShell을 사용한 계획된 유지 관리 처리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

또한 Azure PowerShell을 사용하여 VM의 [유지 관리](maintenance-notifications.md) 작업이 예약된 시기를 볼 수 있습니다. 계획된 유지 관리 정보는 `-status` 매개 변수를 사용하는 경우 [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet에서 확인할 수 있습니다.
  
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. VM에 영향을 미치는 유지 관리가 예약되지 않은 경우 cmdlet은 유지 관리 정보를 반환하지 않습니다. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

출력

```
MaintenanceRedeployStatus               : 
  IsCustomerInitiatedMaintenanceAllowed : True
  PreMaintenanceWindowStartTime         : 5/14/2018 12:30:00 PM
  PreMaintenanceWindowEndTime           : 5/19/2018 12:30:00 PM
  MaintenanceWindowStartTime            : 5/21/2018 4:30:00 PM
  MaintenanceWindowEndTime              : 6/4/2018 4:30
  LastOperationResultCode               : None 
```

MaintenanceRedeployStatus의 다음과 같은 속성이 반환됩니다. 

| 값 | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 이번에 VM에서 유지 관리를 시작할 수 있는지 여부를 나타냅니다. |
| PreMaintenanceWindowStartTime         | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 시작 시간입니다. |
| PreMaintenanceWindowEndTime           | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 종료 시간입니다. |
| MaintenanceWindowStartTime            | Azure가 VM에서 유지 관리를 시작하는 유지 관리 예약 기간의 시작 시간입니다. |
| MaintenanceWindowEndTime              | Azure가 VM에서 유지 관리를 시작하는 유지 관리 예약 기간의 종료 시간입니다. |
| LastOperationResultCode               | VM에서 유지 관리를 시작하는 마지막 시도의 결과입니다. |



또한 [Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용하고 VM을 지정하지 않고 리소스 그룹의 모든 VM에 대한 유지 관리 상태를 가져올 수 있습니다.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

다음 PowerShell 예시는 사용자의 구독 ID를 사용하고 유지 관리에 예약된 VM의 목록을 반환합니다.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>PowerShell을 사용하여 VM에서 유지 관리 시작

이전 섹션의 함수 정보를 사용하여 **IsCustomerInitiatedMaintenanceAllowed** 가 true로 설정되어 있는 경우 VM에서 유지 관리를 시작합니다.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>클래식 배포

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

클래식 배포 모델을 사용하여 배포된 레거시 VM이 아직 있는 경우 PowerShell을 사용하여 VM을 쿼리하고 유지 관리를 시작할 수 있습니다.

VM에 대한 유지 관리 상태를 가져오려면 다음을 입력합니다.

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

클래식 VM에서 유지 관리를 시작하려면 다음을 입력합니다.

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>다음 단계

[Azure CLI](maintenance-notifications-cli.md) 또는 [포털](maintenance-notifications-portal.md)을 사용하여 계획된 유지 관리를 처리할 수도 있습니다.
