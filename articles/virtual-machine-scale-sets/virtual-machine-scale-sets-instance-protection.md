---
title: Azure 가상 머신 확장 집합 인스턴스에 대한 인스턴스 보호
description: 규모 감축 및 확장 집합 작업에서 Azure 가상 머신 확장 집합 인스턴스를 보호하는 방법을 알아봅니다.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 60d7b1c2a869d22e3d8220e0f3537b2882ea0db4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690426"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 가상 머신 확장 집합 인스턴스에 대한 인스턴스 보호
**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Azure 가상 머신 확장 집합을 사용하면 [자동 크기 조정](virtual-machine-scale-sets-autoscale-overview.md)을 통해 워크로드에 대해 더 나은 탄력성을 사용할 수 있으므로 인프라가 스케일 아웃될 때와 규모가 감축될 때를 구성할 수 있습니다. 또한 확장 집합을 사용하면 다양한 [업그레이드 정책](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 설정을 통해 많은 수의 VM을 중앙에서 관리, 구성 및 업데이트할 수 있습니다. 업그레이드 정책을 자동 또는 롤링으로 설정한 경우 확장 집합 모델에 대해 업데이트를 구성할 수 있으며 새 구성은 모든 확장 집합 인스턴스에 자동으로 적용됩니다.

애플리케이션에서 트래픽을 처리할 때 특정 인스턴스를 확장 집합 인스턴스의 나머지 부분과 다르게 처리하려는 상황이 있을 수 있습니다. 예를 들어, 확장 집합의 특정 인스턴스는 장기 실행 작업을 수행할 수 있으며, 작업이 완료될 때까지 이러한 인스턴스의 규모를 감축하지 않으려고 할 수 있습니다. 확장 집합의 일부 인스턴스는 확장 집합의 다른 멤버와는 다른 작업을 수행하기 위해 확장 집합에서 몇 가지 인스턴스를 특수화했을 수도 있습니다. 이러한 '특별' VM은 확장 집합의 다른 인스턴스로 수정하지 않아도 됩니다. 인스턴스 보호는 애플리케이션에 대해 이러한 시나리오와 기타 시나리오를 사용할 수 있도록 하는 추가 컨트롤을 제공합니다.

이 문서에서는 확장 집합 인스턴스를 사용하여 다양 한 인스턴스 보호 기능을 적용하고 사용하는 방법을 설명합니다.

## <a name="types-of-instance-protection"></a>인스턴스 보호의 유형
확장 집합은 다음과 같은 두 가지 유형의 인스턴스 보호 기능을 제공합니다.

-   **규모 감축으로부터 보호**
    - 확장 집합 인스턴스에서 **protectFromScaleIn** 속성을 통해 사용하도록 설정됨
    - 자동 크기 조정으로 시작된 규모 감축으로부터 인스턴스 보호
    - 사용자가 시작한 인스턴스 작업(인스턴스 삭제 포함)이 **차단되지 않음**
    - 확장 집합에서 시작된 작업(업그레이드, 이미지로 다시 설치, 할당 취소 등)은 **차단되지 않음**

-   **확장 집합 작업으로부터 보호**
    - 확장 집합 인스턴스에서 **protectFromScaleSetActions** 속성을 통해 사용하도록 설정됨
    - 자동 크기 조정으로 시작된 규모 감축으로부터 인스턴스 보호
    - 확장 집합에서 시작된 작업(예: 업그레이드, 이미지로 다시 설치, 할당 취소 등)에서 인스턴스 보호
    - 사용자가 시작한 인스턴스 작업(인스턴스 삭제 포함)이 **차단되지 않음**
    - 전체 확장 집합의 삭제는 **차단되지 않음**

## <a name="protect-from-scale-in"></a>규모 감축으로부터 보호
인스턴스를 만든 후에 확장 집합 인스턴스에 인스턴스 보호를 적용할 수 있습니다. 보호는 [인스턴스 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)에만 적용되고 수정되며 [확장 집합 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)에는 적용되지 않습니다.

아래 예제에 자세히 설명된 대로 확장 집합 인스턴스에 규모 감축 보호를 적용하는 여러 가지 방법이 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal를 통해 확장 집합의 인스턴스에 규모 감축 보호를 적용할 수 있습니다. 한 번에 두 개 이상의 인스턴스를 조정할 수 없습니다. 보호하려는 각 인스턴스에 대해 이러한 단계를 반복합니다.
 
1. 기존 가상 머신 확장 집합으로 이동합니다.
1. 왼쪽의 메뉴에서 **설정** 아래의 **인스턴스** 를 선택합니다.
1. 보호하려는 인스턴스의 이름을 선택합니다.
1. **보호 정책** 탭을 선택합니다.
1. **보호 정책** 블레이드에서 **규모 감축으로부터 보호** 옵션을 선택합니다.
1. **저장** 을 선택합니다. 

### <a name="rest-api"></a>REST API

다음 예제에서는 확장 집합의 인스턴스에 규모 감축 보호를 적용합니다.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>인스턴스 보호는 API 버전 2019-03-01 이상에서만 지원됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet을 사용하여 확장 집합 인스턴스에 규모 감축 보호를 적용할 수 있습니다.

다음 예제에서는 인스턴스 ID가 0인 확장 집합의 인스턴스에 규모 감축 보호를 적용합니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

[az vmss update](/cli/azure/vmss#az_vmss_update)를 사용하여 확장 집합 인스턴스에 규모 감축 보호를 적용할 수 있습니다.

다음 예제에서는 인스턴스 ID가 0인 확장 집합의 인스턴스에 규모 감축 보호를 적용합니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>확장 집합 작업으로부터 보호
인스턴스를 만든 후에 확장 집합 인스턴스에 인스턴스 보호를 적용할 수 있습니다. 보호는 [인스턴스 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)에만 적용되고 수정되며 [확장 집합 모델](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)에는 적용되지 않습니다.

확장 집합 작업에서 인스턴스를 보호하면 자동 크기 조정으로 시작된 규모 감축에서 인스턴스를 보호할 수도 있습니다.

아래 예제에 자세히 설명된 대로 확장 집합 인스턴스에 확장 집합 작업 보호를 적용하는 여러 가지 방법이 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal를 통해 확장 집합 작업에서 확장 집합의 인스턴스로 보호를 적용할 수 있습니다. 한 번에 두 개 이상의 인스턴스를 조정할 수 없습니다. 보호하려는 각 인스턴스에 대해 이러한 단계를 반복합니다.
 
1. 기존 가상 머신 확장 집합으로 이동합니다.
1. 왼쪽의 메뉴에서 **설정** 아래의 **인스턴스** 를 선택합니다.
1. 보호하려는 인스턴스의 이름을 선택합니다.
1. **보호 정책** 탭을 선택합니다.
1. **보호 정책** 블레이드에서 **확장 집합 작업으로부터 보호** 옵션을 선택합니다.
1. **저장** 을 선택합니다. 

### <a name="rest-api"></a>REST API

다음 예제에서는 확장 집합 작업에서 확장 집합의 인스턴스로 보호를 적용합니다.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>인스턴스 보호는 API 버전 2019-03-01 이상에서만 지원됩니다.</br>
확장 집합 작업에서 인스턴스를 보호하면 자동 크기 조정으로 시작된 규모 감축에서 인스턴스를 보호할 수도 있습니다. "protectFromScaleSetActions": true를 설정하는 경우 "protectFromScaleIn": false를 지정할 수 없습니다.

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet을 사용하여 확장 집합 작업에서 확장 집합 인스턴스로 보호를 적용할 수 있습니다.

다음 예제에서는 확장 집합 작업에서 인스턴스 ID가 0인 확장 집합의 인스턴스로 보호를 적용합니다.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

[az vmss update](/cli/azure/vmss#az_vmss_update)를 사용하여 확장 집합 작업에서 확장 집합 인스턴스로 보호를 적용할 수 있습니다.

다음 예제에서는 확장 집합 작업에서 인스턴스 ID가 0인 확장 집합의 인스턴스로 보호를 적용합니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>문제 해결
### <a name="no-protectionpolicy-on-scale-set-model"></a>확장 집합 모델에 protectionPolicy가 없음
인스턴스 보호는 확장 집합에만 적용되고 확장 집합 모델에는 적용되지 않습니다.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>확장 집합 인스턴스 모델에 protectionPolicy가 없음
기본적으로 보호 정책이 생성될 때 인스턴스에 적용되지 않습니다.

인스턴스를 만든 후 인스턴스 보호를 확장 집합 인스턴스에 적용할 수 있습니다.

### <a name="not-able-to-apply-instance-protection"></a>인스턴스 보호를 적용할 수 없음
인스턴스 보호는 API 버전 2019-03-01 이상에서만 지원됩니다. 사용 중인 API 버전을 확인하고 필요에 따라 업데이트합니다. PowerShell 또는 CLI를 최신 버전으로 업데이트해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
