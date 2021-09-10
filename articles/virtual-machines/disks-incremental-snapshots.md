---
title: 증분 스냅샷 만들기
description: Azure Portal, Azure PowerShell 모듈 및 Azure Resource Manager를 사용하여 스냅샷을 만드는 방법을 비롯한 관리 디스크의 증분 스냅샷에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8f00a0f69bf00c42ef120250cbc4a770fb1b7a09
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689419"
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

## <a name="next-steps"></a>다음 단계

.NET을 사용하여 증분 스냅샷의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅샷의 차등 기능이 있는 다른 지역에 Azure Managed Disks 백업 복사](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)를 참조하세요.
