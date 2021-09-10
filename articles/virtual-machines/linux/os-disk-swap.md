---
title: Azure CLI를 사용하여 OS 디스크 간 전환
description: Azure CLI를 사용하여 Azure 가상 머신에서 사용하는 운영 체제 디스크를 변경합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: f186052117f40167c9e4e72e350bf689f43bd8b1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692842"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure VM에서 사용하는 OS 디스크 변경

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

기존의 VM이 있지만 백업 디스크 또는 다른 OS 디스크에 대한 디스크를 교체하려는 경우 Azure CLI를 사용하여 OS 디스크를 교체할 수 있습니다. VM을 삭제하고 다시 만들 필요가 없습니다. 이미 사용 중이 아니면 관리 디스크를 다른 리소스에서 사용할 수도 있습니다.

VM은 반드시 중지/할당 취소되어야 합니다. 그래야만 관리 디스크의 리소스 ID를 다른 관리 디스크의 리소스 ID로 바꿀 수 있습니다. 

VM 크기 및 스토리지 유형이 연결할 디스크와 호환되어야 합니다. 예를 들어, 사용하려는 디스크가 Premium Storage에 있으면, VM이 Premium Storage를 지원할 수 있어야 합니다(예: DS 시리즈 크기).

이 문서에는 Azure CLI 버전 2.0.25 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요. 


[az disk list](/cli/azure/disk)를 사용하여 사용자의 리소스 그룹에서 디스크 목록을 가져옵니다.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


[az vm stop](/cli/azure/vm)을 사용하여 디스크를 교체하기 전에 VM을 중지/할당 취소합니다.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


`--osdisk` 매개 변수에 대한 새로운 디스크의 전체 리소스 ID와 함께 [az vm update](/cli/azure/vm#az_vm_update)를 사용합니다. 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
[az vm start](/cli/azure/vm)를 사용하여 VM을 다시 시작합니다.

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**다음 단계**

디스크의 복사본을 만들려면 [디스크 스냅샷](snapshot-copy-managed-disk.md)을 참조하세요.
