---
title: Azure CLI를 사용하여 근접 배치 그룹 만들기
description: Azure에서 가상 머신에 대한 근접 배치 그룹을 만들고 사용하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: d37301d0c1bdde17f148c3aedb875d3a1c52d123
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698142"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Azure CLI를 사용하여 근접 배치 그룹에 VM 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

VM을 최대한 가깝게 유지하고, 대기 시간을 최대한 줄이려면, VM을 [근접 배치 그룹](../co-location.md#proximity-placement-groups) 내에 배포해야 합니다.

근접 배치 그룹은 Azure 컴퓨팅 리소스가 물리적으로 서로 가까운 위치에 있도록 하는 데 사용되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구하는 작업에 유용합니다.


## <a name="create-the-proximity-placement-group"></a>근접 배치 그룹 만들기
[`az ppg create`](/cli/azure/ppg#az_ppg_create)를 사용하여 근접 배치 그룹을 만듭니다. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 나열

[az ppg list](/cli/azure/ppg#az_ppg_list)를 사용하여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>VM 만들기

[new az vm](/cli/azure/vm#az_vm_create)을 사용하여 근접 배치 그룹 내에 VM을 만듭니다.

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

[az ppg show](/cli/azure/ppg#az_ppg_show)를 사용하여 근접 배치 그룹에서 VM을 확인할 수 있습니다.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>가용성 집합
또한 근접 배치 그룹에 가용성 집합을 만들 수 있습니다. [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create)와 동일한 `--ppg`매개 변수를 사용하여 가용성 집합을 만들면 가용성 집합의 모든 VM도 동일한 근접 배치 그룹에 만들어집니다.

## <a name="scale-sets"></a>확장 집합

또한 근접 배치 그룹에 확장 집합을 만들 수 있습니다. [az vmss create](/cli/azure/vmss#az_vmss_create)와 동일한 `--ppg`매개 변수를 사용하여 확장 집합을 만들면 모든 인스턴스가 동일한 근접 배치 그룹에 만들어집니다.

## <a name="next-steps"></a>다음 단계

근접 배치 그룹에 대한 [Azure CLI](/cli/azure/ppg) 명령에 대해 자세히 알아봅니다.