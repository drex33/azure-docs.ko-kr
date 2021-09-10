---
title: Azure 가상 머신 확장 집합 인스턴스에 대한 종료 알림
description: Azure 가상 머신 확장 집합 인스턴스에 대한 종료 알림을 사용하도록 설정하는 방법 알아보기
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: terminate-notification
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 29466a1954e64b79614eec0afdd139e45a44df5b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698265"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Azure 가상 머신 확장 집합 인스턴스에 대한 종료 알림

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

확장 집합 인스턴스는 인스턴스 종료 알림을 받도록 허용하고 종료 작업에 미리 정의된 지연 제한 시간을 설정할 수 있습니다. 종료 알림은 재부팅 및 재배포와 같은 영향력이 강한 작업에 대한 알림을 제공하는 Azure Metadata Service – [Scheduled Events](../virtual-machines/windows/scheduled-events.md)를 통해 전송됩니다. 이 솔루션은 Scheduled Events 목록에 또 다른 이벤트(종료)를 추가하며, 종료 이벤트의 관련 지연 시간은 확장 집합 모델 구성에서 사용자가 지정한 지연 시간 제한에 따라 달라집니다.

기능에 등록된 확장 집합 인스턴스는 삭제되기 전에 지정된 제한 시간이 만료될 때까지 기다릴 필요가 없습니다. 인스턴스는 종료 알림을 받은 후 언제든지 종료 시간 제한이 만료되기 전에 삭제하도록 선택할 수 있습니다.

## <a name="enable-terminate-notifications"></a>종료 알림 사용
아래 예제에 자세히 설명된 대로, 확장 집합 인스턴스에 종료 알림을 사용하도록 설정하는 방법은 여러 가지가 있습니다.

### <a name="azure-portal"></a>Azure Portal

다음 단계에서는 새 확장 집합을 만들 때 종료 알림을 사용하도록 설정합니다. 

1. **가상 머신 확장 집합** 으로 이동합니다.
1. **+ 추가** 를 선택하여 새 확장 집합을 만듭니다.
1. **관리** 탭으로 이동합니다. 
1. **인스턴스 종료** 섹션을 찾습니다.
1. **인스턴스 종료 알림** 에서 **켬** 을 선택합니다.
1. **종료 지연 시간** 에서 원하는 기본 시간 제한을 설정합니다.
1. 새 확장 집합 만들기를 완료했으면 **검토 + 만들기** 단추를 선택합니다. 

> [!NOTE]
> Azure Portal에서 기존 확장 집합에 대한 종료 알림을 설정할 수 없습니다.

### <a name="rest-api"></a>REST API

다음 예에서는 확장 집합 모델에 종료 알림을 사용하도록 설정합니다.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

위의 블록은 확장 집합의 모든 인스턴스에 대한 모든 종료 작업에 5분의 제한 시간 지연(*PT5M* 으로 표시됨)을 지정합니다. *notBeforeTimeout* 필드는 ISO 8601 형식으로 5분과 15분 사이의 모든 값을 허용할 수 있습니다. 위에 설명된 *terminateNotificationProfile* 에서 *notBeforeTimeout* 속성을 수정하여 종료 작업의 기본 시간 제한을 변경할 수 있습니다.

확장 집합 모델에서 *scheduledEventsProfile* 을 사용하도록 설정하고 *notBeforeTimeout* 을 설정한 후 변경 내용을 반영하도록 개별 인스턴스를 [최신 모델](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)로 업데이트합니다.

> [!NOTE]
>확장 집합 인스턴스에 대한 종료 알림은 API 버전 2019-03-01 이상에서만 사용할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell
새 확장 집합을 만들 때 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet을 사용하여 확장 집합에 종료 알림을 사용하도록 설정할 수 있습니다.

이 샘플 스크립트는 구성 파일을 사용하여 확장 집합 및 연결된 리소스를 만드는 과정을 안내합니다([전체 가상 머신 확장 집합 만들기](./scripts/powershell-sample-create-complete-scale-set.md)). 크기 집합을 만들기 위한 구성 개체에 *TerminateScheduledEvents* 및 *TerminateScheduledEventNotBeforeTimeoutInMinutes* 매개 변수를 추가하여 구성 종료 알림을 제공할 수 있습니다. 다음 예제에서는 지연 제한 시간이 10분인 기능을 사용하도록 설정합니다.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

[Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용하여 기존 확장 집합에 대한 종료 알림을 사용하도록 설정합니다.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
위의 예제에서는 기존 확장 집합에 대한 종료 알림을 사용하도록 설정하고 종료 이벤트에 대해 15분의 제한 시간을 설정합니다.

확장 집합 모델에서 예약된 이벤트를 사용하도록 설정하고 제한 시간을 설정한 후 변경 내용을 반영하도록 개별 인스턴스를 [최신 모델](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)로 업데이트합니다.

### <a name="azure-cli-20"></a>Azure CLI 2.0

다음 예제는 새 확장 집합을 만드는 동안 종료 알림을 사용하도록 설정하기 위한 것입니다.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

위의 예제에서는 먼저 리소스 그룹을 만든 다음, 10분의 기본 제한 시간으로 종료 알림이 설정된 새 확장 집합을 만듭니다.

다음 예제는 기존 확장 집합에서 종료 알림을 사용하도록 설정하기 위한 것입니다.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>종료 알림 받기

종료 알림은 Azure Metadata Service인 [Scheduled Events](../virtual-machines/windows/scheduled-events.md)를 통해 전달됩니다. Azure 메타데이터 서비스는 VM 내에서 액세스할 수 있는 REST 엔드포인트를 사용하여 Virtual Machines 실행에 대한 정보를 공개합니다. 이 정보는 라우팅할 수 없는 IP를 통해 볼 수 있으므로 VM 외부에 공개되지 않습니다.

Scheduled Events는 이벤트에 대한 요청을 처음 수행하는 확장 집합에 대해 사용할 수 있습니다. 최대 2분인 첫 번째 호출에서 지연된 응답을 예상할 수 있습니다. 정기적으로 엔드포인트를 쿼리하여 예정된 유지 관리 이벤트와 진행 중인 유지 관리 작업의 상태를 검색합니다.

확장 집합 인스턴스가 24시간 동안 요청을 보내지 않을 경우 확장 집합에 대한 Scheduled Events가 사용하지 않도록 설정됩니다.

### <a name="endpoint-discovery"></a>엔드포인트 검색
VNET 사용 VM의 경우 경로 조정 불가능 IP인 169.254.169.254에서 Metadata Service를 사용할 수 있습니다.

예약된 이벤트의 최신 버전에 대한 전체 엔드포인트는 다음과 같습니다.
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>쿼리 응답
응답에는 예약된 이벤트의 배열이 포함됩니다. 빈 배열은 현재 예약된 이벤트가 없음을 의미합니다.

예약된 이벤트가 있는 경우 응답에 다음과 같은 이벤트의 배열이 포함됩니다. "종료" 이벤트의 경우 응답이 다음과 같이 표시됩니다.
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentIncarnation* 은 ETag로, 이벤트 페이로드가 지난 번 쿼리 후 변경되었는지 검사하는 간편한 방법을 제공합니다.

위의 각 필드에 대한 자세한 내용은 [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) 및 [Linux](../virtual-machines/linux/scheduled-events.md#event-properties)에 대한 Scheduled Events 설명서를 참조하세요.

### <a name="respond-to-events"></a>이벤트에 응답
예정된 이벤트에 대해 알게 되고 정상 종료를 위한 논리를 완료하면 EventId로 메타데이터 서비스에 대한 POST 호출을 실행하여 처리 중인 이벤트를 승인할 수 있습니다. POST 호출은 VM 삭제를 계속할 수 있음을 Azure에 알려줍니다.

다음은 POST 요청 본문에 필요한 json입니다. 요청에 StartRequests 목록이 포함되어야 합니다. 각 StartRequest는 빠르게 처리할 이벤트의 EventId를 포함합니다.
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

확장 집합의 모든 VM이 해당 VM과 관련된 EventID만 승인하는지 확인합니다. VM은 [인스턴스 메타데이터를 통해](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)자체 VM 이름을 가져올 수 있습니다. 이 이름은 "{scale-set-name}_{instance-id}" 형식을 사용하며, 위에서 설명한 쿼리 응답의 'Resources' 섹션에 표시됩니다.

또한 쿼리하고 [Python](../virtual-machines/linux/scheduled-events.md#python-sample) 이벤트에 응답하는 샘플 스크립트를 참조할 수도 있습니다.

## <a name="tips-and-best-practices"></a>팁과 모범 사례
-   '삭제' 작업에 대한 알림만 종료 – 확장 집합에 *scheduledEventsProfile* 이 설정된 경우 모든 삭제 작업(수동 삭제 또는 자동 크기 조정으로 시작된 스케일 인)에서 종료 이벤트를 생성합니다. 다시 부팅, 이미지로 다시 설치, 다시 배포 및 중지/할당 취소와 같은 다른 작업은 종료 이벤트를 생성하지 않습니다. 우선 순위가 낮은 VM에 대해서는 종료 알림을 사용하도록 설정할 수 없습니다.
-   제한 시간에 대한 필수 대기 없음 – 이벤트를 받은 후 이벤트의 *NotBefore* 시간이 만료되기 전에 언제든지 종료 작업을 시작할 수 있습니다.
-   시간 초과 시 필수 삭제 – 이벤트가 생성된 후 제한 시간 값을 확장하는 기능이 없습니다. 제한 시간이 만료되면 보류 중인 종료 이벤트가 처리되고 VM이 삭제됩니다.
-   수정 가능한 제한 시간 값 – 인스턴스가 삭제되기 전에 언제든지 확장 집합 모델에서 *notBeforeTimeout* 속성을 수정하고 VM 인스턴스를 최신 모델로 업데이트하여 제한 시간 값을 수정할 수 있습니다.
-   보류 중인 삭제 모두 승인 – 승인되지 않은 VM_1에 보류 중인 삭제가 있고 VM_2에서 다른 종료 이벤트를 승인한 경우, VM_1에 대한 종료 이벤트가 승인되거나 제한 시간이 경과할 때까지 VM_2가 삭제되지 않습니다. VM_1에 대한 종료 이벤트를 승인하면 VM_1 및 VM_2가 모두 삭제됩니다.
-   모든 동시 삭제 승인 – 위의 예제를 확대하면, VM_1 및 VM_2의 *NotBefore* 시간이 동일할 경우 제한 시간이 만료되기 전에 종료 이벤트를 모두 승인하거나 VM을 모두 삭제하지 않아야 합니다.

## <a name="troubleshoot"></a>문제 해결
### <a name="failure-to-enable-scheduledeventsprofile"></a>scheduledEventsProfile을 사용하도록 설정하지 못함
"'VirtualMachineProfile' 형식의 개체에서 'scheduledEventsProfile' 구성원을 찾을 수 없습니다."라는 오류 메시지와 함께 'BadRequest' 오류가 발생하면 확장 집합 작업에 사용된 API 버전을 확인합니다. 컴퓨팅 API 버전 **2019-03-01** 이상이 필요합니다. 

### <a name="failure-to-get-terminate-events"></a>종료 이벤트를 가져오지 못함
Scheduled Events를 통해 **종료** 이벤트를 가져오지 못한 경우 이벤트를 가져오는 데 사용된 API 버전을 확인합니다. 종료 이벤트를 가져오려면 Metadata Service API 버전 **2019-01-01** 이상이 필요합니다.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>NotBefore 시간이 잘못된 종료 이벤트 가져오기  
확장 집합 모델에서 *scheduledEventsProfile* 을 사용하도록 설정하고 *notBeforeTimeout* 을 설정한 후 변경 내용을 반영하도록 개별 인스턴스를 [최신 모델](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)로 업데이트합니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
