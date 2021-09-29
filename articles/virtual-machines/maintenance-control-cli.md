---
title: CLI를 사용하여 Azure 가상 머신에 대한 유지 관리 제어
description: 유지 관리 제어 및 CLI를 사용하여 Azure VM에 유지 관리가 적용되는 시기를 제어하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: 3ee2fa74e8d030f12bd177999e5a2f146fa9b5c0
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215570"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>유지 관리 제어 및 Azure CLI를 사용하여 업데이트 제어

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

유지 관리 제어를 통해 격리된 VM 및 Azure 전용 호스트 인프라에 플랫폼 업데이트를 적용하는 시기를 결정할 수 있습니다. 이 항목에서는 Azure CLI의 유지 관리 제어 옵션에 관해 설명합니다. 유지 관리 제어 사용의 이점, 제한 사항 및 기타 관리 옵션에 관한 자세한 내용은 [유지 관리 제어를 사용하여 플랫폼 업데이트 관리](maintenance-control.md)를 참조하세요.

## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

`az maintenance configuration create`를 사용하여 유지 관리 구성을 만듭니다. 이 예제에서는 호스트로 범위가 지정된 *myConfig* 라는 유지 관리 구성을 만듭니다. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

나중에 사용할 수 있도록 출력에서 구성 ID를 복사합니다.

`--maintenance-scope host`를 사용하면 유지 관리 구성이 호스트 인프라 업데이트를 제어하는 데 사용됩니다.

다른 위치에서 동일한 이름의 구성을 만들려고 하면 오류가 발생합니다. 구성 이름은 리소스 그룹에서 고유해야 합니다.

`az maintenance configuration list`를 사용하여 사용 가능한 유지 관리 구성을 쿼리할 수 있습니다.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>예약된 기간을 사용하여 유지 관리 구성 만들기
Azure가 리소스에 대한 업데이트를 적용하는 예약된 기간을 선언할 수도 있습니다. 이 예제에서는 매달 네 번째 월요일에 5시간 동안 예약된 myConfig라는 유지 관리 구성을 만듭니다. 예약된 기간을 만든 후에는 더 이상 수동으로 업데이트를 적용할 필요가 없습니다.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> 유지 관리 **기간** 은 *2시간* 이상이어야 합니다. 유지 관리 **되풀이** 는 35일에 한 번 이상 수행되도록 설정해야 합니다.

유지 관리 되풀이는 매일, 매주 또는 매월로 표시될 수 있습니다. 몇 가지 예는 다음과 같습니다.
- **매일** - maintenance-window-recur-every: “일” **또는** “3일”
- **매주** - maintenance-window-recur-every: “3주” **또는** “주 토요일, 일요일”
- **매월** - maintenance-window-recur-every: “월 23일, 24일” **또는** “월 마지막 일요일” **또는** “월 4번째 월요일”


## <a name="assign-the-configuration"></a>구성 할당

`az maintenance assignment create`를 사용하여 격리된 VM 또는 Azure Dedicated Host에 구성을 할당합니다.

### <a name="isolated-vm"></a>격리된 VM

구성 ID를 사용하여 VM에 구성을 적용합니다. `--resource-type virtualMachines`를 지정하고, `--resource-name`에 대해 VM 이름을 제공하고, `--resource-group`의 VM에 대해 리소스 그룹을 제공하고, `--location`에 대해 VM 위치를 제공합니다. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 구성을 적용하려면 `--resource-type hosts`, `--resource-parent-name`(호스트 그룹 이름 포함) 및 `--resource-parent-type hostGroups`를 포함해야 합니다. 

`--resource-id` 매개 변수는 호스트 ID입니다. [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view)를 사용하여 전용 호스트의 ID를 가져올 수 있습니다.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>구성 확인

구성이 올바르게 적용되었는지 확인하거나 `az maintenance assignment list`를 사용하여 현재 적용된 구성을 확인할 수 있습니다.

### <a name="isolated-vm"></a>격리된 VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>전용 호스트 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>보류 중인 업데이트 확인

`az maintenance update list`를 사용하여 보류 중인 업데이트가 있는지 확인합니다. --subscription을 VM이 포함된 구독의 ID로 업데이트합니다.

업데이트가 없는 경우 명령은 `Resource not found...StatusCode: 404` 텍스트를 포함하는 오류 메시지를 반환합니다.

업데이트가 있는 경우 보류 중인 업데이트가 여러 개 있더라도 하나만 반환됩니다. 이 업데이트의 데이터는 개체로 반환됩니다.

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 대해 보류 중인 업데이트를 확인합니다. 이 예제에서 가독성을 위해 출력이 테이블 형식으로 지정됩니다.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대한 보류 중인 업데이트를 확인합니다. 이 예제에서 가독성을 위해 출력이 테이블 형식으로 지정됩니다. 리소스에 대한 값을 고유한 값으로 대체합니다.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>업데이트 적용

`az maintenance apply update`를 사용하여 보류 중인 업데이트를 적용합니다. 성공할 경우 이 명령은 업데이트의 세부 정보를 포함하는 JSON을 반환합니다. 업데이트 호출 적용은 완료하는 데 최대 2시간이 걸릴 수 있습니다.

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 업데이트를 적용하는 요청을 만듭니다.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 업데이트를 적용합니다.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>업데이트 적용 상태를 확인합니다. 

`az maintenance applyupdate get`을 사용하여 업데이트 진행률을 확인할 수 있습니다. 

`default`를 업데이트 이름으로 사용하여 마지막 업데이트의 결과를 확인하거나, `myUpdateName`을 `az maintenance applyupdate create`를 실행할 때 반환된 업데이트 이름으로 바꿀 수 있습니다.

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
LastUpdateTime은 셀프 유지 관리 기간이 사용되지 않은 경우 사용자 또는 플랫폼에 의해 시작된 업데이트가 완료된 시간입니다. 유지 관리 제어를 통해 업데이트를 적용한 적이 없는 경우 기본값을 보여줍니다.

### <a name="isolated-vm"></a>격리된 VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>전용 호스트

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>유지 관리 구성 삭제

`az maintenance configuration delete`를 사용하여 유지 관리 구성을 삭제합니다. 구성을 삭제하면 연결된 리소스에서 유지 관리 제어가 제거됩니다.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>다음 단계
자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조하세요.
