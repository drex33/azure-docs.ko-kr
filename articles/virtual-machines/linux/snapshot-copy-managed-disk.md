---
title: Azure CLI를 사용하여 VHD의 스냅샷 만들기
description: 백업 또는 문제 해결을 위해 Azure에서 VHD의 복사본을 만드는 방법을 알아봅니다.
author: roygara
ms.author: rogarana
manager: twooley
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.openlocfilehash: 2b6a9baba63a1e1f8cec93f059b31dd95a02e63a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692811"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>포털 또는 Azure CLI를 사용하여 스냅샷 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

백업 또는 VM 문제 해결을 위해 OS 또는 데이터 디스크의 스냅샷을 만듭니다. 스냅샷은 VHD의 전체 읽기 전용 복사본입니다. 

## <a name="use-azure-cli"></a>Azure CLI 사용 

다음 예제에서는 [Cloud Shell](https://shell.azure.com/bash)을 사용하거나 Azure CLI를 설치해야 합니다.

다음 단계에서는 **az snapshot create** 명령과 **--source-disk** 매개 변수를 사용하여 스냅샷을 만드는 방법을 보여줍니다. 다음 예제는 *myResourceGroup* 리소스 그룹의 *myVM* 이라는 VM이 있는 것으로 가정합니다.

[az vm show](/cli/azure/vm#az_vm_show)를 사용하여 디스크 ID를 가져옵니다.

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

[az snapshot create](/cli/azure/snapshot#az_snapshot_create)를 사용하여 *osDisk-backup* 이라는 스냅샷을 만듭니다.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> 스냅샷을 영역 중복 스토리지에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 **--sku Standard_ZRS** 매개 변수를 포함하는 지역에 만들어야 합니다.

[az snapshot list](/cli/azure/snapshot#az_snapshot_list)를 사용하여 스냅샷 목록을 볼 수 있습니다.

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Azure Portal 사용 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 위에서 **리소스 만들기** 를 클릭하고 **스냅샷** 을 검색합니다. 검색 결과에서 **스냅샷** 을 선택합니다.
3. **스냅샷** 블레이드에서 **만들기** 를 클릭합니다.
4. 스냅샷의 **이름** 을 입력합니다.
5. 기존 리소스 그룹을 선택하거나 새 리소스 그룹의 이름을 입력합니다. 
7. **원본 디스크** 에서 스냅샷을 만들 관리 디스크를 선택합니다.
8. 스냅샷 저장에 사용할 **계정 유형** 을 선택합니다. 고성능 SSD에 저장할 필요가 없다면 **표준 HDD** 를 사용합니다.
9. **만들기** 를 클릭합니다.


## <a name="next-steps"></a>다음 단계

 스냅샷에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅샷에서 가상 머신을 만듭니다. 자세한 내용은 [스냅샷에서 VM 만들기](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json) 스크립트를 참조하세요.