---
title: 일시 삭제 사용 - Azure 파일 공유
description: 데이터 복구를 수행하고 실수로 삭제하는 경우를 방지하기 위해 Azure 파일 공유에서 일시 삭제를 사용하도록 설정하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
services: storage
ms.openlocfilehash: 314d1ee8ee957b21679735594ad2f7d7f50f4d38
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118319"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Azure 파일 공유에서 일시 삭제 사용
이제 Azure Files는 이제 애플리케이션 또는 다른 스토리지 계정 사용자에 의해 잘못 삭제될 때 데이터를 보다 쉽게 복구할 수 있도록 파일 공유에 대한 일시 삭제를 제공합니다. 일시 삭제에 대해 자세히 알아보려면 [Azure 파일 공유의 우발적 삭제를 방지하는 방법](storage-files-prevent-file-share-deletion.md)을 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>필수 구성 요소
- Azure PowerShell을 사용하려면 [최신 버전을 설치](/powershell/azure/install-az-ps)하세요.
- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli)하세요.

## <a name="getting-started"></a>시작
다음 섹션에서는 기존 스토리지 계정에서 Azure 파일 공유에 대해 일시 삭제를 사용하도록 설정하고 사용하는 방법을 보여 줍니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 스토리지 계정으로 이동하고 **데이터 스토리지** 에서 **파일 공유** 를 선택합니다.
1. **일시 삭제** 옆에 있는 **사용** 을 선택합니다.
1. **모든 파일 공유 일시 삭제** 에서 **사용** 을 선택합니다.
1. **파일 공유 보존 기간(일)** 을 선택하고 원하는 기간을 입력합니다.
1. **저장** 을 선택하여 데이터 보존 설정을 확인합니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="스토리지 계정 일시 삭제 설정 창 스크린샷. 파일 공유 일시 삭제 섹션, 사용 토글, 보존 기간 설정 및 저장 강조 표시 그러면 스토리지 계정의 모든 파일 공유에 대해 일시 삭제가 사용하도록 설정됩니다.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
일시 삭제를 사용하도록 설정하려면 `FileService` 속성이라고도 하는 모든 Azure 파일 공유에 대한 설정을 업데이트해야 합니다. 다음 예제에서는 스토리지 계정의 모든 파일 공유에 대해 일시 삭제를 사용하도록 설정합니다. `<resource-group>` 및 `<storage-account>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $true `
    -ShareRetentionDays 7
```

일시 삭제가 사용하도록 설정되어 있는지 확인하고 다음 명령을 사용하여 보존 정책을 볼 수 있습니다.

```PowerShell
Get-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
일시 삭제를 사용하도록 설정하려면 파일 클라이언트의 서비스 속성을 업데이트해야 합니다. 다음 예제에서는 스토리지 계정의 모든 파일 공유에 대해 일시 삭제를 사용하도록 설정합니다. `<resource-group>` 및 `<storage-account>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-delete-retention true \
    --delete-retention-days 7
```

일시 삭제가 사용하도록 설정되어 있는지 확인하고 다음 명령을 사용하여 보존 정책을 볼 수 있습니다.

```bash
az storage account file-service-properties show \
    -resource-group $resourceGroupName \
    -account-name $storageAccountName
```
---

## <a name="restore-soft-deleted-file-share"></a>일시 삭제된 파일 공유 복원

# <a name="portal"></a>[포털](#tab/azure-portal)
일시 삭제된 파일 공유를 복원하려면

1. 스토리지 계정으로 이동하여 **파일 공유** 를 선택합니다.
1. 파일 공유 블레이드에서 **삭제된 공유 표시** 를 사용하도록 설정하여 일시 삭제된 모든 공유를 표시합니다.

    현재 **삭제됨** 상태인 모든 공유가 표시됩니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="이름 열 옆에 있는 상태 열이 삭제됨으로 설정되면 파일 공유가 일시 삭제된 상태입니다. 지정된 보존 기간 후에 영구적으로 삭제됩니다.":::

1. 공유를 선택하고 **삭제 취소** 를 선택하면 공유가 복원됩니다.

    공유가 **활성** 상태로 전환되므로 공유가 복원되었는지 확인할 수 있습니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="이름 열 옆에 있는 상태 열이 활성으로 설정되면 파일 공유가 복원된 것입니다.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
일시 삭제된 파일 공유를 복원하려면 먼저 공유의 `-DeletedShareVersion` 값을 가져와야 합니다. 해당 값을 가져오려면 다음 명령을 사용하여 스토리지 계정에 대해 삭제된 모든 공유를 나열합니다.

```PowerShell
Get-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -IncludeDeleted
```

복원할 공유를 확인한 후 다음 명령과 함께 사용하여 복원할 수 있습니다.

```PowerShell
Restore-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -DeletedShareVersion 01D5E2783BDCDA97 # replace with your deleted version number
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
일시 삭제된 파일 공유를 복원하려면 먼저 공유의 `--deleted-version` 값을 가져와야 합니다. 해당 값을 가져오려면 다음 명령을 사용하여 스토리지 계정에 대해 삭제된 모든 공유를 나열합니다.

```bash
az storage share-rm list \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --include-deleted
```

복원할 공유를 확인한 후 다음 명령과 함께 사용하여 복원할 수 있습니다.

```bash
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

---

## <a name="disable-soft-delete"></a>일시 삭제 사용 안 함
일시 삭제 사용을 중지하려는 경우 다음 참고 자료를 따르세요. 일시 삭제된 파일 공유를 영구적으로 삭제하려면 삭제를 취소하고 일시 삭제를 사용하지 않게 설정한 후, 다시 삭제해야 합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 스토리지 계정으로 이동하고 **데이터 스토리지** 에서 **파일 공유** 를 선택합니다.
1. **일시 삭제** 옆에 있는 링크를 선택합니다.
1. **모든 파일 공유 일시 삭제** 에서 **사용하지 않음** 을 선택합니다.
1. **저장** 을 선택하여 데이터 보존 설정을 확인합니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="일시 삭제를 사용하지 않도록 설정하면 원할 때 스토리지 계정의 모든 파일 공유를 즉시 영구적으로 삭제할 수 있습니다.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 명령을 사용하여 스토리지 계정에서 일시 삭제를 사용하지 않도록 설정할 수 있습니다.

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
다음 명령을 사용하여 스토리지 계정에서 일시 삭제를 사용하지 않도록 설정할 수 있습니다.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --enable-delete-retention false
```

---

## <a name="next-steps"></a>다음 단계
다른 형태의 데이터 보호 및 복구에 관해 알아보려면 [Azure Files용 공유 스냅샷 개요](storage-snapshots-files.md) 문서를 참조하세요.
