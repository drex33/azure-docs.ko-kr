---
title: CLI를 사용하여 유지 관리 알림 가져오기
description: Azure CLI를 사용하여 Azure에서 실행 중인 Linux 가상 머신에 대한 유지 관리 알림을 확인하고 셀프 서비스 유지 관리를 시작합니다.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 7b4f79724ee3105e9a64b44617762d71f4f6a158
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698802"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Azure CLI를 사용하여 계획된 유지 관리 알림 처리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

CLI를 사용하여 [유지 관리](maintenance-notifications.md)를 위해 VM이 예약되는 시기를 확인할 수 있습니다. 계획된 유지 관리 정보는 [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)에서 제공됩니다.
 
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

출력
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>유지 관리 시작

`IsCustomerInitiatedMaintenanceAllowed`가 true로 설정되는 경우 다음 호출이 VM에서 유지 관리를 시작합니다.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>클래식 배포

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

클래식 배포 모델을 사용하여 배포된 레거시 VM이 아직 있는 경우 Azure 클래식 CLI를 사용하여 VM을 쿼리하고 유지 관리를 시작할 수 있습니다.

다음을 입력하여 클래식 VM에서 작동하도록 올바른 모드에 있는지 확인합니다.

```
azure config mode asm
```

*myVM* 이라는 VM에 대한 유지 관리 상태를 가져오려면 다음을 입력합니다.

```
azure vm show myVM 
``` 

*myService* 서비스 및 *myDeployment* 배포에서 *myVM* 이라는 클래식 VM에 대한 유지 관리를 시작하려면 다음을 입력합니다.

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>다음 단계

[Azure PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md)을 사용하여 계획된 유지 관리를 처리할 수도 있습니다.
