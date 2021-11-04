---
title: Azure 디스크 풀(미리 보기) 관리
description: Azure 디스크 풀에 관리 디스크를 추가하거나 디스크에서 iSCSI 지원을 사용하지 않도록 설정하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59f03b2484bed39a3c562efc6cfb4176b5173964
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083226"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Azure 디스크 풀(미리 보기) 관리

이 문서에서는 Azure 디스크 풀(미리 보기)에 관리 디스크를 추가하는 방법과 디스크 풀에 추가된 디스크에서 iSCSI 지원을 사용하지 않도록 설정하는 방법을 설명합니다.

## <a name="add-a-disk-to-a-pool"></a>풀에 디스크 추가

디스크가 디스크 풀에 추가되려면 다음 요구 사항을 충족해야 합니다.
- 프리미엄 SSD, 표준 SSD 또는 디스크 풀과 동일한 지역 및 가용성 영역에 있는 울트라 디스크여야 합니다.
    - 울트라 디스크의 디스크 섹터 크기는 512바이트여야 합니다.
- maxShares 값이 2 이상인 공유 디스크여야 합니다.
- [디스크 풀에 추가되는 디스크에 대해 RBAC 권한을 StoragePool 리소스 공급자에게 제공](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions)해야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 디스크 풀로 이동하고 **설정** 아래에서 **디스크를** 선택합니다.
1. **기존 디스크 연결 을** 선택하고 디스크를 선택합니다.
1. 연결하려는 모든 디스크를 선택한 경우 **저장을** 선택합니다.

    :::image type="content" source="media/disk-pools-manage/manage-disk-pool-add.png" alt-text="디스크 풀에 대한 디스크 블레이드의 스크린샷.":::

    디스크를 연결했으므로 LUNS를 사용하도록 설정해야 합니다.

1. **설정** 아래에서 **iSCSI** 를 선택합니다.
1. **iSCSI에 대해 사용하도록 설정된 디스크에서** **LUN 추가를** 선택합니다.
1. 이전에 연결한 디스크를 선택합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/disk-pools-manage/enable-disk-luns.png" alt-text="iSCSI 블레이드, 추가 및 활성화된 디스크 lun의 스크린샷.":::

디스크를 연결하고 LUN을 사용하도록 설정했으므로 iSCSI 데이터 저장소로 만들어 Azure VMware Solution 프라이빗 클라우드에 연결해야 합니다. 자세한 내용은 [iSCSI LUN 연결](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun) 을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="prerequisites"></a>필수 구성 요소

Azure PowerShell 모듈 [버전 6.1.0 이상](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)을 설치합니다.

다음 명령을 사용하여 디스크 풀 모듈을 설치합니다.

```azurepowershell
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery
```

### <a name="add-a-disk-pool"></a>디스크 풀 추가

다음 스크립트는 디스크 풀에 디스크를 더 추가하고 iSCSI를 통해 노출합니다. 디스크 풀의 기존 디스크는 변경하지 않고 유지합니다.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='<LunName>' #Provide the Lun name of the added disk
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

디스크를 연결하고 LUN을 사용하도록 설정했으므로 iSCSI 데이터 저장소로 만들어 Azure VMware Solution 프라이빗 클라우드에 연결해야 합니다. 자세한 내용은 [iSCSI LUN 연결](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun) 을 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>사전 요구 사항

[최신 버전의](/cli/azure/disk-pool) Azure CLI 설치합니다.

아직 설치하지 않은 경우 다음 명령을 사용하여 디스크 풀 확장을 설치합니다.

```azurecli
az extension add -n diskpool
```

### <a name="add-a-disk-pool---cli"></a>디스크 풀 추가 - CLI

다음 스크립트는 디스크 풀에 디스크를 더 추가하고 iSCSI를 통해 노출합니다. 디스크 풀의 기존 디스크는 변경하지 않고 유지합니다.

```azurecli
# Add a disk to a disk pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --Name $diskPoolName)

diskIds=$(echo $(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json) | sed -e 's/\[ //g' -e 's/\ ]//g' -e 's/\,//g')
for disk in $diskIds; do
    diskPoolUpdateArgs+=(--disks $(echo $disk | sed 's/"//g'))
done

diskId=$(az disk show --resource-group $resourceGroupName --name $diskName --query id | sed 's/"//g')
diskPoolUpdateArgs+=(--disks $diskId)

az disk-pool update "${diskPoolUpdateArgs[@]}"

# Get existing iSCSI LUNs and expose added disk as a new LUN
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunsCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
    targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
done

targetUpdateArgs+=(--luns name=$lunName managed-disk-azure-resource-id=$diskId)

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"
```

디스크를 연결하고 LUN을 사용하도록 설정했으므로 iSCSI 데이터 저장소로 만들어 Azure VMware Solution 프라이빗 클라우드에 연결해야 합니다. 자세한 내용은 [iSCSI LUN 연결](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun) 을 참조하세요.

---

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>디스크에서 iSCSI를 사용하지 않도록 설정하고 풀에서 디스크 제거

디스크에서 iSCSI 지원을 사용하지 않도록 설정하기 전에 디스크가 노출되는 iSCSI LUN에 대해 처리 중인 iSCSI 연결이 없는지 확인합니다. 디스크 풀에서 디스크를 제거할 때 디스크가 자동으로 삭제되지는 않습니다. 이렇게 하면 데이터가 손실되지 않지만 데이터 저장 요금이 계속 청구됩니다. 디스크에 저장된 데이터가 필요하지 않은 경우 디스크를 수동으로 삭제할 수 있습니다. 그러면 디스크와 디스크에 저장된 모든 데이터가 삭제되고 더 이상 요금이 부과되지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 디스크 풀로 이동하고 **설정** 아래에서 **iSCSI를** 선택합니다.
1. **iSCSI에 대해 사용하도록 설정된 디스크에서** 제거할 디스크를 선택하고 **LUN 제거를** 선택합니다.
1. **저장을** 선택하고 작업이 완료되기를 기다립니다.

    :::image type="content" source="media/disk-pools-manage/remove-disk-lun.png" alt-text="디스크 LUN을 제거하는 디스크 풀 iSCSI 블레이드의 스크린샷.":::

    LUN을 사용하지 않도록 설정했으므로 디스크 풀에서 디스크를 제거할 수 있습니다.

1. **설정** 아래에서 **디스크를** 선택합니다.
1. **디스크 풀에서 디스크 제거를** 선택하고 디스크를 선택합니다.
1. **저장** 을 선택합니다.

작업이 완료되면 디스크 풀에서 디스크가 완전히 제거됩니다.

:::image type="content" source="media/disk-pools-manage/remove-disks-from-pool.png" alt-text="디스크 풀의 디스크 블레이드 스크린샷. 풀에서 제거되는 디스크":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LunForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# Disable iSCSI on a disk and remove it from the pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

# Get existing iSCSI LUNs and remove it from iSCSI target
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    if [ $tmpLunName != $lunName ]; then
        tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
        targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
    fi
done

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"

# Remove disk from pool
diskId=$(az disk show --resource-group $resourceGroupName --name $diskName -- query id | sed 's/"//g')

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --name $diskPoolName)

diskIds=$(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json)
diskLength=$(echo diskIds | jq length)

for (( i=0; i < $diskLength; i++ )); do
    tmpDiskId=$(echo $diskIds | jq .[$i] | sed 's/"//g')

    if [ $tmpDiskId != $diskId ]; then
        diskPoolUpdateArgs+=(--disks $tmpDiskId)
    fi
done

az disk-pool update "${diskPoolUpdateArgs[@]}"
```

---
## <a name="next-steps"></a>다음 단계

- 디스크 풀을 다른 구독으로 이동하는 방법을 알아보려면 [디스크 풀을 다른 구독으로 이동](disks-pools-move-resource.md)을 참조하세요.
- 디스크 풀의 프로비전을 해제하는 방법을 알아보려면 [Azure 디스크 풀의 프로비전 해제](disks-pools-deprovision.md)를 참조하세요.
