---
title: Azure 디스크 풀(미리 보기) 관리
description: Azure 디스크 풀에 관리 디스크를 추가하거나 디스크에서 iSCSI 지원을 사용하지 않도록 설정하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4811894a3483bbfce1724b744d904fa328c51ded
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436990"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Azure 디스크 풀(미리 보기) 관리

이 문서에서는 Azure 디스크 풀(미리 보기)에 관리 디스크를 추가하는 방법과 디스크 풀에 추가된 디스크에서 iSCSI 지원을 사용하지 않도록 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure PowerShell 모듈 [버전 6.1.0 이상](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)을 설치합니다.

## <a name="add-a-disk-to-a-pool"></a>풀에 디스크 추가

디스크가 디스크 풀에 추가되려면 다음 요구 사항을 충족해야 합니다.
- 디스크 풀과 동일한 지역과 가용성 영역에 있는 프리미엄 SSD 또는 울트라 디스크여야 합니다.
    - 울트라 디스크의 디스크 섹터 크기는 512바이트여야 합니다.
- maxShares 값이 2 이상인 공유 디스크여야 합니다.
- [디스크 풀에 추가되는 디스크에 대해 RBAC 권한을 StoragePool 리소스 공급자에게 제공](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions)해야 합니다.

다음 스크립트는 디스크 풀에 디스크를 더 추가하고 iSCSI를 통해 노출합니다. 디스크 풀의 기존 디스크는 변경하지 않고 유지합니다.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='LunName>' #Provide the Lun name of the added disk
$diskIds = @()

#Add the disk to disk pool
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
$diskIds += ($Id)
}

$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$diskIds += ,($disk.Id)
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds

#Get the existing iSCSI LUNs and add the new disk
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}

$newlun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $disk.Id -Name $lunName
$luns += ,($newlun)
Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns
```

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>디스크에서 iSCSI를 사용하지 않도록 설정하고 풀에서 디스크 제거

디스크에서 iSCSI 지원을 사용하지 않도록 설정하기 전에 디스크가 노출되는 iSCSI LUN에 대해 처리 중인 iSCSI 연결이 없는지 확인합니다. 디스크 풀에서 디스크를 제거할 때 디스크가 자동으로 삭제되지는 않습니다. 이렇게 하면 데이터가 손실되지 않지만 데이터 저장 요금이 계속 청구됩니다. 디스크에 저장된 데이터가 필요하지 않은 경우 디스크를 수동으로 삭제할 수 있습니다. 그러면 디스크와 디스크에 저장된 모든 데이터가 삭제되고 더 이상 요금이 부과되지 않습니다.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LUNForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
$diskIds = @()

#Get the existing iSCSI LUNs and remove it from iSCS target
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
if ($lun.Name -notlike $lunName)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}
}

Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns

#Remove the disk from disk pool
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
if ($Id -notlike $disk.Id)
{
$diskIds += ($Id)
}
}

Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds
```

## <a name="next-steps"></a>다음 단계

- 디스크 풀을 다른 구독으로 이동하는 방법을 알아보려면 [디스크 풀을 다른 구독으로 이동](disks-pools-move-resource.md)을 참조하세요.
- 디스크 풀의 프로비전을 해제하는 방법을 알아보려면 [Azure 디스크 풀의 프로비전 해제](disks-pools-deprovision.md)를 참조하세요.