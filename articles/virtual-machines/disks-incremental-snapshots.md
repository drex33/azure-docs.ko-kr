---
title: 증분 스냅샷 만들기
description: Azure Portal, Azure PowerShell 모듈 및 Azure Resource Manager를 사용하여 스냅샷을 만드는 방법을 비롯한 관리 디스크의 증분 스냅샷에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell, ignite-fall-2021
ms.openlocfilehash: ba03ec11522ea5a4e4a011d1e62fa09b25aec749
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022307"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>관리 디스크에 대한 증분 스냅샷 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 증분 스냅샷을 만들 수 있습니다. 최신 버전의 Azure CLI가 필요합니다. Azure CLI를 [설치](/cli/azure/install-azure-cli)하거나 [업데이트](/cli/azure/update-azure-cli)하는 방법을 알아보려면 다음 문서를 참조하세요.

다음 스크립트는 특정 디스크의 증분 스냅샷을 만듭니다.

```azurecli
# Declare variables
diskName="yourDiskNameHere"
resourceGroupName="yourResourceGroupNameHere"
snapshotName="desiredSnapshotNameHere"

# Get the disk you need to backup
yourDiskID=$(az disk show -n $diskName -g $resourceGroupName --query "id" --output tsv)

# Create the snapshot
az snapshot create -g $resourceGroupName -n $snapshotName --source $yourDiskID --incremental true
```

스냅샷의 `SourceResourceId` 속성을 사용하여 동일한 디스크에서 증분 스냅샷을 식별할 수 있습니다. `SourceResourceId`는 부모 디스크의 Azure Resource Manager 리소스 ID입니다.

`SourceResourceId`를 사용하여 특정 디스크와 연결된 모든 스냅샷 목록을 만들 수 있습니다. `yourResourceGroupNameHere`를 값으로 바꾸고 다음 예를 사용하여 기존 증분 스냅샷을 나열할 수 있습니다.


```azurecli
# Declare variables and create snapshot list
subscriptionId="yourSubscriptionId"
resourceGroupName="yourResourceGroupNameHere"
diskName="yourDiskNameHere"

az account set --subscription $subscriptionId

diskId=$(az disk show -n $diskName -g $resourceGroupName --query [id] -o tsv)

az snapshot list --query "[?creationData.sourceResourceId=='$diskId' && incremental]" -g $resourceGroupName --output table
```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell 모듈을 사용하여 증분 스냅샷을 만들 수 있습니다. Azure PowerShell 모듈의 최신 버전이 필요합니다. 다음 명령은 이를 설치하거나 기존 설치를 최신으로 업데이트합니다.

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

설치가 완료되면 `Connect-AzAccount`를 사용하여 PowerShell 세션에 로그인합니다.

Azure PowerShell을 사용하여 증분 스냅샷을 만들려면 `-Incremental` 매개 변수를 사용하여 [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig)로 구성을 설정한 다음 `-Snapshot` 매개 변수를 사용하여 [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot)에 변수로 전달합니다.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

스냅샷의 `SourceResourceId` 및 `SourceUniqueId` 속성을 사용하여 동일한 디스크에서 증분 스냅샷을 식별할 수 있습니다. `SourceResourceId`는 부모 디스크의 Azure Resource Manager 리소스 ID입니다. `SourceUniqueId`는 디스크의 `UniqueId` 속성에서 상속된 값입니다. 디스크를 삭제한 다음 동일한 이름으로 새 디스크를 만드는 경우 `UniqueId` 속성 값이 변경됩니다.

`SourceResourceId` 및 `SourceUniqueId`를 사용하여 특정 디스크와 연결된 모든 스냅샷 목록을 만들 수 있습니다. `yourResourceGroupNameHere`를 값으로 바꾸고 다음 예를 사용하여 기존 증분 스냅샷을 나열할 수 있습니다.

```PowerShell
$resourceGroupName = "yourResourceGroupNameHere"
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[포털](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

Azure Resource Manager 템플릿을 사용하여 증분 스냅샷을 만들 수도 있습니다. apiVersion이 **2019-03-01** 로 설정되어 있고 증분 속성도 true로 설정되어 있는지 확인해야 합니다. 다음 조각은 Resource Manager 템플릿을 사용하여 증분 스냅샷을 만드는 방법의 예입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="cross-region-snapshot-copy-preview"></a>지역 간 스냅샷 복사(미리 보기)

CopyStart 옵션(미리 보기)을 사용하여 한 지역에서 선택한 지역으로 증분 스냅샷의 복사본을 시작할 수 있습니다. Azure는 증분 스냅샷을 복사하는 프로세스를 처리하고 마지막 스냅샷 이후의 델타 변경 내용만 대상 지역에 복사하여 데이터 공간을 줄입니다. 고객은 대상 스냅샷이 대상 지역에서 디스크를 복원할 준비가 된 시기를 알 수 있도록 복사본의 진행 상황을 확인할 수 있습니다. 이 프로세스를 사용하여 장기 보존을 위해 스냅샷을 다른 구독에 복사할 수 있습니다. 또한 이 기능을 사용하여 동일한 지역의 스냅샷을 복사하여 [영역 중복 스토리지에서](disks-redundancy.md#zone-redundant-storage-for-managed-disks) 스냅샷이 완전히 강화되도록 하고 영역 오류가 발생할 경우 스냅샷을 사용할 수 있도록 할 수 있습니다.

:::image type="content" source="media/disks-incremental-snapshots/cross-region-snapshot.png" alt-text="복제 옵션을 통해 증분 스냅샷의 Azure 오케스트레이션된 지역 간 복사본 다이어그램" lightbox="media/disks-incremental-snapshots/cross-region-snapshot.png":::

### <a name="pre-requisites"></a>필수 구성 요소

미리 보기 기능을 사용하려면 구독에서 기능을 사용하도록 설정해야 합니다. 다음 명령을 사용하여 기능을 등록합니다.

```azurecli
az feature register --namespace Microsoft.Compute --name CreateOptionClone
```

등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 다음 명령을 사용하여 상태를 확인할 수 있습니다.

```azurecli
az feature show --namespace Microsoft.Compute --name CreateOptionClone
```

### <a name="restrictions"></a>제한

- 지역 간 스냅샷 복사는 현재 미국 동부 2 및 미국 중서부에서만 사용할 수 있습니다.
- 2020-12-01 이상 버전의 Azure Compute Rest API를 사용해야 합니다.

### <a name="get-started"></a>시작

```azurecli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
name=<targetSnapshotName>
sourceSnapshotResourceId=<sourceSnapshotResourceId>
targetRegion=<validRegion>

az login
az account set --subscription $subscriptionId
az group deployment create -g $resourceGroupName \
--template-uri https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/CrossRegionCopyOfSnapshots/CopyStartIncrementalSnapshots.json \
--parameters "name=$name" "sourceSnapshotResourceId=$sourceSnapshotResourceId" "targetRegion=$targetRegion"
az resource show -n $name -g $resourceGroupName --namespace Microsoft.Compute --resource-type snapshots --api-version 2020-12-01 --query [properties.completionPercent] -o tsv
```

## <a name="next-steps"></a>다음 단계

.NET을 사용하여 증분 스냅샷의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅샷의 차등 기능이 있는 다른 지역에 Azure Managed Disks 백업 복사](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)를 참조하세요.
