---
title: Linux VM 게스트 디스크에 Azure Disk를 매핑하는 방법
description: Linux VM의 게스트 디스크를 언더레이하는 Azure Disk를 결정하는 방법.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: ca606d2c73952316a269d5ac8663823ffb249bf4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692242"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Linux VM 게스트 디스크에 Azure Disk를 매핑하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

VM의 게스트 디스크를 백업하는 Azure 디스크를 결정해야 할 수도 있습니다. 일부 시나리오에서는 디스크 또는 볼륨 크기를 연결된 Azure Disk의 크기와 비교할 수 있습니다. VM에 동일한 크기의 Azure Disk가 여러 개 연결된 시나리오에서는 데이터 디스크의 LUN(논리 단위 번호)을 사용해야 합니다. 

## <a name="what-is-a-lun"></a>LUN이란?

LUN(논리 단위 번호)은 특정 스토리지 디바이스를 식별하는 데 사용되는 숫자입니다. 각 스토리지 디바이스에는 0에서 시작하는 고유한 숫자 식별자가 할당됩니다. 디바이스의 전체 경로는 버스 번호, 대상 ID 번호 및 LUN(논리 단위 번호)으로 표시됩니다. 

예: ***Bus Number 0, 대상 ID 0, LUN 3***

연습에서는 LUN만 사용하면 됩니다.

## <a name="finding-the-lun"></a>LUN 찾기

아래에는 Linux에서 디스크의 LUN을 찾는 두 가지 방법이 나와 있습니다.

### <a name="lsscsi"></a>lsscsi

1. VM에 연결
1. `sudo lsscsi`

나열된 첫 번째 열에는 LUN이 포함되고 형식은 [Host:Channel:Target:**LUN**]입니다.

### <a name="listing-block-devices"></a>블록 디바이스 나열

1. VM에 연결
1. `sudo ls -l /sys/block/*/device`

나열된 마지막 열에는 LUN이 포함되고 형식은 [Host:Channel:Target:**LUN**]입니다.

## <a name="finding-the-lun-for-the-azure-disks"></a>Azure Disk에 대한 LUN 찾기

Azure CLI, Azure Portal을 사용하여 Azure Disk에 대한 LUN을 찾을 수 있습니다.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure Portal에서 Azure Disk의 LUN 찾기

1. Azure Portal에서 “Virtual Machines”를 선택하여 Virtual Machines 목록을 표시
1. Virtual Machine을 선택합니다.
1. “디스크”를 선택합니다.
1. 연결된 디스크 목록에서 데이터 디스크를 선택합니다.
1. 디스크의 LUN이 디스크 세부 정보 창에 표시됩니다. 여기에 표시된 LUN은 lsscsi를 사용하여 게스트에서 조회한 LUN 또는 블록 디바이스를 나열한 LUN과 관련이 있습니다.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Azure CLI를 사용하여 Azure Disk의 LUN 찾기

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
