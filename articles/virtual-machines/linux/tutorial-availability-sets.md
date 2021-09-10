---
title: Azure CLI를 사용하여 가용성 집합에 VM 배포
description: Azure CLI를 사용하여 가용성 집합에서 고가용성 가상 머신을 배포하는 방법을 알아보세요.
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 784adc6829a66269fb863cce7e1e103a45fde7d9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114455677"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Azure CLI를 사용하여 가용성 집합에서 가상 머신 만들기 및 배포

이 자습서에서는 가용성 집합이라는 기능을 사용하여 Azure에서 VM(Virtual Machine) 솔루션의 가용성과 안정성을 향상시키는 방법에 대해 알아봅니다. 가용성 집합을 사용하면 Azure에 배포한 VM이 격리된 여러 하드웨어 클러스터에 분산되도록 할 수 있습니다. 이렇게 하면 Azure 내의 하드웨어 또는 소프트웨어 오류가 발생할 때, VM의 하위 집합에만 영향을 주며 전체 솔루션은 사용 가능한 운영 상태로 계속 유지됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인

이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 [Azure Cloud Shell](../../cloud-shell/overview.md) 내의 CLI를 사용합니다. Cloud Shell을 열려면 코드 블록 상단에서 **사용해 보세요** 를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-an-availability-set"></a>가용성 집합 만들기

[az vm availability-set create](/cli/azure/vm/availability-set)를 사용하여 가용성 집합을 만들 수 있습니다. 이 예제에서는 *myResourceGroupAvailability* 리소스 그룹의 *myAvailabilitySet* 이라는 가용성 집합에 대한 업데이트 및 장애 도메인 수가 *2* 로 설정됩니다.

먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만든 다음, 가용성 집합을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

가용성 집합을 사용하면 장애 도메인 및 업데이트 도메인에서 리소스를 격리할 수 있습니다. **장애 도메인** 은 서버 + 네트워크 + 스토리지 리소스의 격리된 컬렉션을 나타냅니다. 위의 예제에서는 VM을 배포할 때 가용성 집합이 적어도 두 개의 장애 도메인으로 분산됩니다. 가용성 집합도 두 개의 **업데이트 도메인** 으로 분산됩니다. 두 개의 업데이트 도메인은 Azure에서 소프트웨어 업데이트를 수행할 때 VM 리소스가 격리되어 VM에서 실행되는 모든 소프트웨어가 동시에 업데이트되지 않도록 합니다.


## <a name="create-vms-inside-an-availability-set"></a>가용성 집합에 포함된 VM 만들기

하드웨어 전체에 올바르게 배포되도록 하려면 VM을 가용성 집합 내에 만들어야 합니다. VM을 만든 후에는 가용성 집합에 기존 VM을 추가할 수 없습니다.

[az vm create](/cli/azure/vm)를 사용하여 VM을 만들 때 `--availability-set` 매개 변수를 사용하여 가용성 집합의 이름을 지정합니다.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

이제 가용성 집합 내에 두 개의 가상 머신이 있습니다. 동일한 가용성 집합에 있기 때문에 Azure에서는 VM 및 관련된 모든 리소스(데이터 디스크 포함)가 격리된 물리적 하드웨어에 분산되도록 합니다. 이렇게 분산되면 전체 VM 솔루션의 가용성을 훨씬 높여줍니다.

리소스 그룹 > myResourceGroupAvailability > myAvailabilitySet로 이동하여 포털에서 가용성 집합 분포를 볼 수 있습니다. 다음 예제에 표시된 것처럼 VM은 두 개의 장애 및 업데이트 도메인으로 분산됩니다.

![포털의 가용성 집합](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>사용 가능한 VM 크기 확인

VM 크기를 하드웨어에서 사용할 수 있는 가용성 집합에 나중에 추가 VM을 추가할 수 있습니다. [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes)를 사용하여 하드웨어 클러스터에서 가용성 집합에 대한 사용 가능한 모든 크기를 나열합니다.

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인

가상 머신 확장 집합에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [가상 머신 확장 집합 만들기](tutorial-create-vmss.md)

* 가용성 영역에 대한 자세한 내용은 [가용성 영역 설명서](../../availability-zones/az-overview.md)를 참조하세요.
* 가용성 집합 및 가용성 영역에 대한 추가 설명은 [Azure Virtual Machines에 대한 가용성 옵션](../availability.md)에도 제공됩니다.
* 가용성 영역을 사용해보려면 [Azure CLI를 사용하여 가용성 영역에서 Linux 가상 머신 만들기](./create-cli-availability-zone.md)를 방문하세요.
