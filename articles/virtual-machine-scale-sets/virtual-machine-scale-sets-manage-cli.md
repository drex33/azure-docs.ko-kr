---
title: Azure CLI를 사용하여 가상 머신 확장 집합 관리
description: 인스턴스를 시작하고 중지하는 방법 및 확장 집합 용량을 변경하는 방법과 같은 Virtual Machine Scale Sets를 관리하는 공통 Azure CLI 명령입니다.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 883ff0695538d54360fc06a36ee80acb33d54afb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690390"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI를 사용하여 가상 머신 확장 집합 관리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일 확장 집합

가상 머신 확장 집합의 수명 주기 동안 하나 이상의 관리 작업을 실행해야 합니다. 또한 다양한 수명 주기 작업을 자동화하는 스크립트를 만들어야 하는 경우가 있습니다. 이 문서에서는 이러한 작업을 수행할 수 있는 공통 Azure CLI 명령의 일부를 설명합니다.

이러한 관리 작업을 완료하려면 최신 Azure CLI가 필요합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 가상 머신 확장 집합을 만들어야 하는 경우 [Azure CLI에서 확장 집합을 만들](quick-create-cli.md) 수 있습니다.


## <a name="view-information-about-a-scale-set"></a>확장 집합에 대한 정보 보기
확장 집합에 대한 전체 정보를 보려면 [az vmss show](/cli/azure/vmss)를 사용합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myScaleSet* 이라는 확장 집합에 대한 정보를 가져옵니다. 다음과 같이 고유한 이름을 입력합니다.

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>확장 집합의 VM 보기
확장 집합에서 VM 인스턴스 목록을 보려면 [az vmss list-instances](/cli/azure/vmss)를 사용합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myScaleSet* 이라는 확장 집합의 모든 VM 인스턴스를 나열합니다. 다음과 같은 이름에 고유한 값을 제공합니다.

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

특정 VM 인스턴스에 대한 추가 정보를 보려면 `--instance-id` 매개 변수를 [az vmss get-instance-view](/cli/azure/vmss)에 추가하고 보려는 인스턴스를 지정합니다. 다음 예제에서는 *myScaleSet* 이라는 확장 집합 및 *myResourceGroup* 이라는 리소스 그룹에서 VM 인스턴스 *0* 에 대한 정보를 봅니다. 다음과 같이 고유한 이름을 입력합니다.

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

단일 API 호출로 모든 인스턴스의 자세한 *instanceView* 정보를 가져올 수도 있습니다. 이 기능은 대규모 설치에서 API 제한을 방지하는 데 도움이 됩니다. `--resource-group`, `--subscription`, `--name`에 대해 사용자 고유의 값을 지정합니다.

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>VN의 연결 정보 나열
확장 집합의 VM에 연결하려면 할당된 공용 IP 주소와 포트 번호에 RDP 또는 SSH합니다. NAT(네트워크 주소 변환) 규칙은 기본적으로 각 VM에 원격 연결 트래픽을 전달하는 Azure Load Balancer에 추가됩니다. 주소 및 포트를 나열하여 확장 집합의 VM 인스턴스에 연결하려면 [az vmss list-instance-connection-info](/cli/azure/vmss)를 사용합니다. 다음 예제에서는 *myScaleSet* 이라는 확장 집합 및 *myResourceGroup* 리소스 그룹에서 VM 인스턴스에 대한 연결 정보를 나열합니다. 다음과 같은 이름에 고유한 값을 제공합니다.

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>확장 집합의 용량 변경
이전 명령은 확장 집합 및 VM 인스턴스에 대한 정보를 표시했습니다. 확장 집합에서 인스턴스 수를 늘리거나 줄이려면 용량을 변경할 수 있습니다. 확장 집합은 필요한 수의 VM을 만들거나 제거한 후 애플리케이션 트래픽을 받도록 VM을 구성합니다.

현재 확장 집합의 인스턴스 수를 보려면 [az vmss show](/cli/azure/vmss)를 사용하여 *sku.capacity* 를 쿼리합니다.

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

그런 다음[az vmss scale](/cli/azure/vmss)을 사용하여 확장 집합의 Virtual Machines 수를 수동으로 증가 또는 감소시킬 수 있습니다. 다음 예제는 확장 집합의 VM 수를 *5* 로 설정합니다.

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

확장 집합의 용량을 업데이트하는 데 몇 분 정도가 걸립니다. 확장 집합의 용량을 줄이는 경우 가장 높은 인스턴스 ID를 포함하는 VM을 먼저 제거합니다.


## <a name="stop-and-start-vms-in-a-scale-set"></a>확장 집합에서 VM 중지 및 시작
확장 집합에서 하나 이상의 VM을 중지하려면 [az vmss stop](/cli/azure/vmss#az_vmss_stop)을 사용합니다. `--instance-ids` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 중지하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 중지됩니다. 여러 VM을 중지하려면 각 인스턴스 ID를 공백으로 구분합니다.

다음 예제에서는 *myScaleSet* 이라는 확장 집합 및 *myResourceGroup* 이라는 리소스 그룹에서 인스턴스 *0* 을 중지합니다. 다음과 같이 사용자 고유의 값을 제공합니다.

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

중지된 VM은 할당된 상태를 유지하고 계속 컴퓨팅 요금을 부과합니다. 대신 VM의 할당을 취소하고 스토리지 요금만을 부과하려는 경우 [az vmss deallocate](/cli/azure/vmss)를 사용합니다. 여러 VM의 할당을 취소하려면 각 인스턴스 ID를 공백으로 구분합니다. 다음 예제에서는 *myScaleSet* 이라는 확장 집합 및 *myResourceGroup* 이라는 리소스 그룹에서 인스턴스 *0* 을 중지하고 할당을 취소합니다. 다음과 같이 사용자 고유의 값을 제공합니다.

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>확장 집합의 VM 시작
확장 집합에서 하나 이상의 VM을 시작하려면 [az vmss start](/cli/azure/vmss)를 사용합니다. `--instance-ids` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 시작하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 시작됩니다. 여러 VM을 시작하려면 각 인스턴스 ID를 공백으로 구분합니다.

다음 예제에서는 *myScaleSet* 이라는 확장 집합 및 *myResourceGroup* 이라는 리소스 그룹에서 인스턴스 *0* 을 시작합니다. 다음과 같이 사용자 고유의 값을 제공합니다.

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>확장 집합의 VM 다시 시작
확장 집합에서 하나 이상의 VM을 다시 시작하려면 [az vmss restart](/cli/azure/vmss)를 사용합니다. `--instance-ids` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 다시 시작하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 다시 시작됩니다. 여러 VM을 다시 시작하려면 각 인스턴스 ID를 공백으로 구분합니다.

다음 예제에서는 *myScaleSet* 이라는 확장 집합 및 *myResourceGroup* 이라는 리소스 그룹에서 인스턴스 *0* 을 다시 시작합니다. 다음과 같이 사용자 고유의 값을 제공합니다.

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>확장 집합에서 VM 제거
확장 집합에서 하나 이상의 VM을 제거하려면 [az vmss delete-instances](/cli/azure/vmss)를 사용합니다. `--instance-ids` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 제거하도록 지정할 수 있습니다. 인스턴스 ID에 *를 지정하면 확장 집합에서 모든 VM이 제거됩니다. 여러 VM을 제거하려면 각 인스턴스 ID를 공백으로 구분합니다.

다음 예제에서는 *myScaleSet* 이라는 확장 집합 및 *myResourceGroup* 이라는 리소스 그룹에서 인스턴스 *0* 을 제거합니다. 다음과 같이 사용자 고유의 값을 제공합니다.

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>다음 단계
확장 집합에 대한 다른 공통 작업에는 [애플리케이션을 배포](virtual-machine-scale-sets-deploy-app.md)하고 [VM 인스턴스를 업그레이드](virtual-machine-scale-sets-upgrade-scale-set.md)하는 방법이 포함됩니다. Azure CLI를 사용하여 [자동 크기 조정 규칙을 구성](virtual-machine-scale-sets-autoscale-overview.md)할 수도 있습니다.
