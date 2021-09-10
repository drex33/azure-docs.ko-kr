---
title: Windows VM 게스트 디스크에 Azure Disk를 매핑하는 방법
description: Windows VM의 게스트 디스크를 언더레이하는 Azure Disk를 결정하는 방법입니다.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 68941b2253e1b4655a9e0e61da3b0a4f0db27bdf
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697588"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Windows VM 게스트 디스크에 Azure Disk를 매핑하는 방법

**적용 대상:** :heavy_check_mark: Windows VM 


VM의 게스트 디스크를 백업하는 Azure 디스크를 결정해야 할 수도 있습니다. 일부 시나리오에서는 디스크 또는 볼륨 크기를 연결된 Azure Disk의 크기와 비교할 수 있습니다. VM에 동일한 크기의 Azure Disk가 여러 개 연결된 시나리오에서는 데이터 디스크의 LUN(논리 단위 번호)을 사용해야 합니다. 

## <a name="what-is-a-lun"></a>LUN이란?

LUN(논리 단위 번호)은 특정 스토리지 디바이스를 식별하는 데 사용되는 숫자입니다. 각 스토리지 디바이스에는 0에서 시작하는 고유한 숫자 식별자가 할당됩니다. 디바이스의 전체 경로는 버스 번호, 대상 ID 번호 및 LUN(논리 단위 번호)으로 표시됩니다. 

예: ***Bus Number 0, 대상 ID 0, LUN 3***

연습에서는 LUN만 사용하면 됩니다.

## <a name="finding-the-lun"></a>LUN 찾기

LUN을 찾는 방법에는 두 가지가 있습니다. 어떤 방법을 선택할지는 [스토리지 공간](/windows-server/storage/storage-spaces/overview)을 사용하는지 여부에 따라 다릅니다.

### <a name="disk-management"></a>디스크 관리

스토리지 풀을 사용하지 않는 경우에는 [디스크 관리](/windows-server/storage/disk-management/overview-of-disk-management)를 사용하여 LUN을 찾을 수 있습니다.

1. VM에 연결하고 디스크 관리 a를 엽니다. 시작 단추를 마우스 오른쪽 단추로 클릭하고 “디스크 관리”를 선택합니다. 검색 시작 상자에 `diskmgmt.msc`를 입력할 수도 있습니다.
1. 아래쪽 창에서 디스크를 마우스 오른쪽 단추로 클릭하고 “속성”을 선택합니다.
1. LUN은 “일반” 탭의 “위치” 속성에 나열됩니다.

### <a name="storage-pools"></a>저장소 풀

1. VM에 연결하고 서버 관리자를 엽니다.
1. “파일 및 스토리지 서비스”, “볼륨”, “스토리지 풀”을 선택합니다.
1. 서버 관리자의 오른쪽 아래 모서리에는 “실제 디스크” 섹션이 있습니다. 스토리지 풀을 구성하는 디스크는 각 디스크에 대한 LUN 및 여기에 나열됩니다.

## <a name="finding-the-lun-for-the-azure-disks"></a>Azure Disk에 대한 LUN 찾기

Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 Azure Disk에 대한 LUN을 찾을 수 있습니다.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure Portal에서 Azure Disk의 LUN 찾기

1. Azure Portal에서 “Virtual Machines”를 선택하여 Virtual Machines 목록을 표시
1. Virtual Machine을 선택합니다.
1. “디스크”를 선택합니다.
1. 연결된 디스크 목록에서 데이터 디스크를 선택합니다.
1. 디스크의 LUN이 디스크 세부 정보 창에 표시됩니다. 여기에 표시되는 LUN은 디바이스 관리자 또는 서버 관리자를 사용하여 게스트에서 조회한 LUN과 상관관계가 있습니다.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell을 사용하여 Azure Disk의 LUN 찾기

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---
