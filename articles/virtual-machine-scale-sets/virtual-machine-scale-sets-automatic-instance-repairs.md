---
title: Azure 가상 머신 확장 집합을 사용한 자동 인스턴스 복구
description: 확장 집합에서 VM 인스턴스에 대한 자동 복구 정책을 구성하는 방법 알아보기
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 6134e1f4b4823b2198a1ecc316644ef0cd11ca33
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690538"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합에 대한 자동 인스턴스 복구

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

Azure 가상 머신 확장 집합에 대해 자동 인스턴스 복구를 활성화하면 정상적인 인스턴스 집합을 유지 관리하여 애플리케이션의 고가용성을 달성할 수 있습니다. [애플리케이션 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)에서 보고한 대로 확장 집합의 인스턴스가 비정상인 것으로 확인되면 이 기능은 비정상 인스턴스를 삭제하고 교체할 새 인스턴스를 만들어 인스턴스 복구를 자동으로 수행합니다.

## <a name="requirements-for-using-automatic-instance-repairs"></a>자동 인스턴스 복구를 사용하기 위한 요구 사항

**확장 집합에 애플리케이션 상태 모니터링 활성화**

확장 집합에 인스턴스에 대한 애플리케이션 상태 모니터링이 활성화되어 있어야 합니다. 이 작업은 [애플리케이션 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 사용하여 수행할 수 있습니다. 한 번에 하나만 활성화할 수 있습니다. 애플리케이션 상태 확장 또는 부하 분산 장치는 가상 머신 인스턴스에 구성된 애플리케이션 엔드포인트를 ping하여 애플리케이션 상태를 확인합니다. 이 상태는 확장 집합 오케스트레이터에서 인스턴스 상태를 모니터링하고 필요한 경우 복구를 수행하는 데 사용됩니다.

**상태를 제공하도록 엔드포인트 구성**

자동 인스턴스 복구 정책을 활성화하기 전에 확장 집합 인스턴스에 애플리케이션 상태를 내보내도록 구성된 애플리케이션 엔드포인트가 있는지 확인합니다. 인스턴스가 이 애플리케이션 엔드포인트에서 상태 200(정상)을 반환하면 인스턴스가 ‘정상’으로 표시됩니다. 다른 모든 경우에는 인스턴스가 다음 시나리오를 포함하여 ‘비정상’으로 표시됩니다.

- 애플리케이션 상태를 제공하기 위해 가상 머신 인스턴스 내에 구성된 애플리케이션 엔드포인트가 없는 경우
- 애플리케이션 엔드포인트가 잘못 구성된 경우
- 애플리케이션 엔드포인트에 연결할 수 없는 경우

‘비정상’으로 표시된 인스턴스의 경우 자동 복구는 확장 집합에 의해 트리거됩니다. 엔드 포인트가 구성되는 동안 의도하지 않은 인스턴스 복구를 방지하기 위해 자동 복구 정책을 활성화하기 전에 애플리케이션 엔드 포인트가 올바르게 구성되었는지 확인합니다.

**확장 집합의 최대 인스턴스 수**

이 기능은 현재 최대 500개의 인스턴스가 있는 확장 집합에만 사용할 수 있습니다. 확장 집합은 단일 배치 그룹 또는 다중 배치 그룹으로 배포할 수 있지만 확장 집합에 자동 인스턴스 복구를 사용할 수 있는 경우에는 인스턴스 수가 500개를 초과할 수 없습니다.

**API 버전**

자동 복구 정책은 컴퓨팅 API 버전 2018-10-01 이상에서 지원됩니다.

**리소스 또는 구독 이동에 대한 제한**

자동 복구 기능이 활성화된 경우에는 리소스 또는 구독 이동이 현재 확장 집합에 대해 지원되지 않습니다.

**Service fabric 확장 집합에 대한 제한**

이 기능은 현재 서비스 패브릭 확장 집합에 대해 지원되지 않습니다.

## <a name="how-do-automatic-instance-repairs-work"></a>자동 인스턴스 복구 작동 방법

자동 인스턴스 복구 기능은 확장 집합에 있는 개별 인스턴스의 상태 모니터링에 의존합니다. [애플리케이션 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 사용하여 애플리케이션 상태를 내보내도록 확장 집합의 VM 인스턴스를 구성할 수 있습니다. 인스턴스가 비정상인 것으로 확인되면 확장 집합은 비정상 인스턴스를 삭제하고 새 인스턴스를 만들어 대체하는 방법으로 복구 작업을 수행합니다. 최신 가상 머신 확장 집합 모델을 사용하여 새 인스턴스를 만듭니다. *AutomaticRepairsPolicy* 개체를 사용하여 가상 머신 확장 집합 모델에서 이 기능을 활성화할 수 있습니다.

### <a name="batching"></a>일괄 처리

자동 인스턴스 복구 작업은 일괄 처리로 수행됩니다. 지정된 시간에 자동 복구 정책을 통해 확장 집합의 인스턴스 중 5% 이하만 복구됩니다. 이렇게 하면 동시에 비정상 상태를 발견한 경우 많은 수의 인스턴스를 동시에 삭제하고 다시 만들지 않아도 됩니다.

### <a name="grace-period"></a>유예 기간

확장 집합에서 수행된 PUT, PATCH 또는 POST 작업(예: 이미지 다시 설치, 다시 배포, 업데이트 등)으로 인해 인스턴스가 상태 변경 작업을 거치는 경우 해당 인스턴스에 대한 복구 작업은 유예 기간을 기다린 후에만 수행됩니다. 유예 기간은 인스턴스가 정상 상태로 돌아갈 수 있는 시간입니다. 유예 기간은 상태 변경이 완료된 후에 시작됩니다. 이렇게 하면 조기 또는 우발적인 복구 작업을 방지할 수 있습니다. 확장 집합에서 새로 만든 인스턴스(복구 작업의 결과로 만든 인스턴스 포함)에 대해 유예 기간이 설정됩니다. 유예 기간은 ISO 8601 형식으로 분 단위로 지정되며 *automaticRepairsPolicy.gracePeriod* 속성을 사용하여 설정할 수 있습니다. 유예 기간은 10분에서 90분 사이로 설정할 수 있으며 기본값은 30분입니다.

### <a name="suspension-of-repairs"></a>복구 일시 중단

가상 머신 확장 집합은 필요한 경우 자동 인스턴스 복구를 일시적으로 일시 중단하는 기능을 제공합니다. 가상 머신 확장 집합의 인스턴스 보기에서 *orchestrationServices* 속성 아래의 자동 복구에 대한 *serviceState* 는 자동 복구의 현재 상태를 표시합니다. 확장 집합이 자동 복구로 옵트인되면 *serviceState* 매개 변수 값이 *실행 중* 으로 설정됩니다. 확장 집합에 대해 자동 복구가 일시 중단되면 *serviceState* 매개 변수가 *일시 중단됨* 으로 설정됩니다. 확장 집합에 *automaticRepairsPolicy* 가 정의되어 있지만 자동 복구 기능이 활성화되어 있지 않은 경우 *serviceState* 매개 변수가 *실행 중 아님* 으로 설정됩니다.

확장 집합의 비정상 인스턴스를 교체하기 위해 새로 만든 인스턴스가 복구 작업을 반복적으로 수행한 후에도 계속 비정상 상태로 유지되는 경우 안전 측정값으로 플랫폼은 자동 복구를 위해 *serviceState를* *일시 중단됨* 으로 업데이트합니다. 자동 복구에 대한 *serviceState* 값을 *실행 중* 으로 설정하여 자동 복구를 다시 시작할 수 있습니다. 자세한 지침은 확장 집합에 대한 [자동 복구 정책의 서비스 상태를 보기 및 업데이트](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) 섹션에 나와 있습니다.

자동 인스턴스 복구 프로세스는 다음과 같이 작동합니다.

1. [애플리케이션 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)는 확장 집합의 각 가상 머신 내에서 애플리케이션 엔드포인트를 ping하여 각 인스턴스에 대한 애플리케이션 상태를 가져옵니다.
2. 엔드포인트가 상태 200(정상)으로 응답하는 경우 인스턴스는 ‘정상’으로 표시됩니다. 다른 모든 경우(엔드포인트에 연결할 수 없는 경우 포함)에는 인스턴스가 ‘비정상’으로 표시됩니다.
3. 인스턴스가 비정상인 것으로 확인되면 확장 집합은 비정상 인스턴스를 삭제하고 새 인스턴스를 만들어 대체하는 방법으로 복구 작업을 트리거합니다.
4. 인스턴스 복구는 일괄 처리로 수행됩니다. 지정된 시간에 확장 집합의 총 인스턴스 중 5% 이하만 복구됩니다. 확장 집합에 20개 미만의 인스턴스가 있는 경우 한 번에 하나의 비정상 인스턴스에 대한 복구가 수행됩니다.
5. 위의 프로세스는 확장 집합의 모든 비정상 인스턴스가 복구될 때까지 계속됩니다.

## <a name="instance-protection-and-automatic-repairs"></a>인스턴스 보호 및 자동 복구

확장 집합의 인스턴스가 [보호 정책](./virtual-machine-scale-sets-instance-protection.md) 중 하나를 적용하여 보호되는 경우 해당 인스턴스에서 자동 복구가 수행되지 않습니다. 이는 *확장으로부터 보호* 및 *확장 집합으로부터 보호* 작업과 같은 보호 정책 모두에 적용됩니다.

## <a name="terminatenotificationandautomaticrepairs"></a>종료 알림 및 자동 복구

확장 집합에서 [종료 알림](./virtual-machine-scale-sets-terminate-notification.md) 기능을 사용하는 경우 자동 복구 작업 중에 비정상 인스턴스 삭제는 종료 알림 구성을 따릅니다. 종료 알림은 Azure 메타데이터 서비스(예약된 이벤트)를 통해 전송되며, 구성된 지연 제한 시간 동안 인스턴스 삭제가 지연됩니다. 그러나 비정상 항목을 대체하기 위해 새 인스턴스를 만드는 경우 지연 제한 시간이 완료될 때까지 기다리지 않습니다.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>새 확장 집합을 만들 때 자동 복구 정책 활성화

새 확장 집합을 만드는 동안 자동 복구 정책을 활성화하려면 이 기능을 옵트인하기 위한 모든 [요구 사항](#requirements-for-using-automatic-instance-repairs)이 충족되는지 확인합니다. 엔드포인트가 구성되는 동안 의도하지 않은 복구가 트리거되지 않도록 확장 집합 인스턴스에 대해 애플리케이션 엔드포인트를 올바르게 구성해야 합니다. 새로 만든 확장 집합의 경우 유예 기간 동안 대기한 후에만 인스턴스 복구가 수행됩니다. 확장 집합에서 자동 인스턴스 복구를 활성화하려면 가상 머신 확장 집합 모델에서 *automaticRepairsPolicy* 개체를 사용합니다.

또한 이 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vmss-automatic-repairs-slb-health-probe)을 사용하여 유예 기간이 30분으로 활성화된 부하 분산 장치 상태 프로브 및 자동 인스턴스 복구가 있는 가상 머신 확장 집합을 배포할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

다음 단계는 새 확장 집합을 만들 때 자동 복구 정책을 활성화하는 방법입니다.

1. **가상 머신 확장 집합** 으로 이동합니다.
1. **+ 추가** 를 선택하여 새 확장 집합을 만듭니다.
1. **상태** 탭으로 이동합니다.
1. **상태** 섹션을 찾습니다.
1. **애플리케이션 상태 모니터링** 옵션을 사용하도록 설정합니다.
1. **자동 복구 정책** 섹션을 찾습니다.
1. **자동 복구** 옵션을 **켬** 으로 설정합니다.
1. **유예 기간(분)** 에서 유예 기간을 분으로 지정합니다. 허용되는 값은 30분에서 90분 사이입니다.
1. 새 확장 집합 만들기를 완료했으면 **검토 + 만들기** 단추를 선택합니다.

### <a name="rest-api"></a>REST API

다음 예제에서는 확장 집합 모델에서 자동 인스턴스 복구를 사용하도록 설정하는 방법을 보여 줍니다. API 버전 2018-10-01 이상을 사용합니다.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet을 사용하여 새 확장 집합을 만드는 동안 자동 인스턴스 복구 기능을 사용하도록 설정할 수 있습니다. 이 샘플 스크립트는 구성 파일을 사용하여 확장 집합 및 연결된 리소스를 만드는 과정을 안내합니다([전체 가상 머신 확장 집합 만들기](./scripts/powershell-sample-create-complete-scale-set.md)). 확장 집합을 만들기 위한 구성 개체에 *EnableAutomaticRepair* 및 *AutomaticRepairGracePeriod* 매개 변수를 추가하여 자동 인스턴스 복구 정책을 구성할 수 있습니다. 다음 예제에서는 유예 기간이 30분인 기능을 사용하도록 설정합니다.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예제에서는 *[az vmss create](/cli/azure/vmss#az_vmss_create)* 를 사용하여 새 확장 집합을 만드는 동안 자동 복구 정책을 사용하도록 설정합니다. 먼저 리소스 그룹을 만든 다음 자동 복구 정책 유예 기간을 30분으로 설정하여 새 확장 집합을 만듭니다.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

위의 예제에서는 기존 부하 분산 장치 및 상태 프로브를 사용하여 인스턴스의 애플리케이션 상태를 모니터링합니다. 대신 모니터링을 위해 애플리케이션 상태 확장을 사용하려는 경우에는 확장 집합을 만들고, 애플리케이션 상태 확장을 구성한 후, 다음 섹션에 설명된 대로 *az vmss 업데이트* 를 사용하여 자동 인스턴스 복구 정책을 사용하도록 설정할 수 있습니다.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>기존 확장 집합을 업데이트할 때 자동 복구 정책을 사용하도록 설정

기존 확장 집합에서 자동 복구 정책을 활성화하기 전에 이 기능을 옵트인하기 위한 모든 [요구 사항](#requirements-for-using-automatic-instance-repairs)이 충족되는지 확인합니다. 엔드포인트가 구성되는 동안 의도하지 않은 복구가 트리거되지 않도록 확장 집합 인스턴스에 대해 애플리케이션 엔드포인트를 올바르게 구성해야 합니다. 확장 집합에서 자동 인스턴스 복구를 활성화하려면 가상 머신 확장 집합 모델에서 *automaticRepairsPolicy* 개체를 사용합니다.

기존 확장 집합의 모델을 업데이트한 후에는 최신 모델이 모든 규모의 인스턴스에 적용되는지 확인합니다. [최신 확장 집합 모델을 사용하여 VM을 최신 상태로 전환하는 방법](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)에 대한 지침을 참조하세요.

### <a name="azure-portal"></a>Azure Portal

Azure Portal을 통해 기존 확장 집합의 자동 복구 정책을 수정할 수 있습니다.

1. 기존 가상 머신 확장 집합으로 이동합니다.
1. 왼쪽 메뉴의 **설정** 에서 **상태 및 복구** 를 선택합니다.
1. **애플리케이션 상태 모니터링** 옵션을 사용하도록 설정합니다.
1. **자동 복구 정책** 섹션을 찾습니다.
1. **자동 복구** 옵션을 **켬** 으로 설정합니다.
1. **유예 기간(분)** 에서 유예 기간을 분으로 지정합니다. 허용되는 값은 30분에서 90분 사이입니다.
1. 작업이 완료되면 **저장** 을 선택합니다.

### <a name="rest-api"></a>REST API

다음 예제에서는 유예 기간이 40분인 정책을 사용하도록 설정합니다. API 버전 2018-10-01 이상을 사용합니다.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용하여 기존 확장 집합에서 자동 인스턴스 복구 기능의 구성을 수정합니다. 다음 예에서는 유예 기간을 40분으로 업데이트합니다.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예제에서는 *[az vmss update](/cli/azure/vmss#az_vmss_update)* 를 사용하여 기존 확장 집합의 자동 인스턴스 복구 정책을 업데이트합니다.

```azurecli-interactive
az vmss update \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>자동 인스턴스 복구 정책의 서비스 상태 보기 및 업데이트

### <a name="rest-api"></a>REST API

가상 머신 확장 집합에 대해 API 버전 2019-12-01 이상과 함께 [인스턴스 뷰 가져오기](/rest/api/compute/virtualmachinescalesets/getinstanceview)를 사용하여 *orchestrationServices* 속성에서 자동 복구에 대한 *serviceState* 를 확인합니다.

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

가상 머신 확장 집합에서 API 버전 2019-12-01 이상과 함께 *setOrchestrationServiceState* API를 사용하여 자동 복구 상태를 설정합니다. 확장 집합이 자동 복구 기능으로 옵트인되면 이 API를 사용하여 확장 집합에 대한 자동 복구를 일시 중단하거나 다시 시작할 수 있습니다.

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI

[get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) cmdlet을 사용하여 자동 인스턴스 복구에 대한 *serviceState* 를 확인합니다.

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

[set-orchestration-service-state](/cli/azure/vmss#az_vmss_set_orchestration_service_state) cmdlet을 사용하여 자동 인스턴스 복구에 대한 *serviceState* 를 업데이트합니다. 확장 집합이 자동 복구 기능으로 옵트인되면 이 cmdlet을 사용하여 확장 집합에 대한 자동 복구를 일시 중단하거나 다시 시작할 수 있습니다.

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

*InstanceView* 매개 변수와 함께 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet을 사용하여 자동 인스턴스 복구에 대한 *ServiceState* 를 확인합니다.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Set-AzVmssOrchestrationServiceState cmdlet을 사용하여 자동 인스턴스 복구에 대한 *serviceState* 를 업데이트합니다. 확장 집합이 자동 복구 기능으로 옵트인되면 이 cmdlet을 사용하여 확장 집합에 대한 자동 복구를 일시 중단하거나 다시 시작할 수 있습니다.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>문제 해결

**자동 복구 정책 활성화 실패**

"'속성' 형식의 개체에서 'automaticRepairsPolicy' 멤버를 찾을 수 없습니다."라는 메시지와 함께 'BadRequest' 오류가 발생하는 경우 가상 머신 확장 집합에 사용되는 API 버전을 확인하세요. 이 기능을 수행하려면 API 버전 2018-10-01 이상이 필요합니다.

**정책이 활성화된 경우에도 인스턴스가 복구되지 않음**

인스턴스가 유예 기간일 수 있습니다. 인스턴스의 상태가 변경된 후 복구를 수행하기 전에 대기하는 시간입니다. 이는 조기 또는 우발적인 복구 작업을 방지하기 위한 것입니다. 인스턴스에 대한 유예 기간이 완료되면 복구 작업이 수행되어야 합니다.

**확장 집합 인스턴스에 대한 애플리케이션 상태 보기**

가상 머신 확장 집합의 인스턴스에 대한 [인스턴스 뷰 가져오기 API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)를 사용하여 애플리케이션 상태를 볼 수 있습니다. Azure PowerShell에서 *-InstanceView* 플래그와 함께 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) cmdlet을 사용할 수 있습니다. 애플리케이션 상태는 *vmHealth* 속성 아래에 제공됩니다.

Azure Portal에서도 상태를 볼 수 있습니다. 기존 확장 집합으로 이동하여 왼쪽 메뉴에서 **인스턴스** 를 선택한 다음 **상태 열** 에서 각 확장 집합 인스턴스의 상태를 확인합니다.

## <a name="next-steps"></a>다음 단계

확장 집합에 대한 [애플리케이션 상태 확장](./virtual-machine-scale-sets-health-extension.md) 또는 [부하 분산 장치 상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md)를 구성하는 방법에 대해 알아봅니다.
