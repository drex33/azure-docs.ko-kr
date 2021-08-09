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
ms.openlocfilehash: 3687eeb09473a5281ddcde98f5b9b7b11472589f
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110666761"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Azure 파일 공유에서 일시 삭제 사용

이제 Azure Storage는 이제 애플리케이션 또는 다른 스토리지 계정 사용자에 의해 잘못 삭제될 때 데이터를 보다 쉽게 복구할 수 있도록 파일 공유에 대한 일시 삭제를 제공합니다. 일시 삭제에 대해 자세히 알아보려면 [Azure 파일 공유의 우발적 삭제를 방지하는 방법](storage-files-prevent-file-share-deletion.md)을 참조하세요.

다음 섹션에서는 기존 스토리지 계정에서 Azure 파일 공유에 대해 일시 삭제를 사용하도록 설정하고 사용하는 방법을 보여 줍니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

## <a name="getting-started"></a>시작

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 스토리지 계정으로 이동하고 **데이터 스토리지** 에서 **파일 공유** 를 선택합니다.
1. **일시 삭제** 옆에 있는 **사용** 을 선택합니다.
1. **모든 파일 공유 일시 삭제** 에서 **사용** 을 선택합니다.
1. **파일 공유 보존 기간(일)** 을 선택하고 원하는 기간을 입력합니다.
1. **저장** 을 선택하여 데이터 보존 설정을 확인합니다.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="스토리지 계정 일시 삭제 설정 창 스크린샷. 파일 공유 일시 삭제 섹션, 사용 토글, 보존 기간 설정 및 저장 강조 표시 그러면 스토리지 계정의 모든 파일 공유에 대해 일시 삭제가 사용하도록 설정됩니다.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

일시 삭제 cmdlet은 [Azure CLI 모듈](/cli/azure/install-azure-cli)의 버전 2.1.3 이상에서 사용할 수 있습니다.

## <a name="getting-started-with-cli"></a>CLI 시작

일시 삭제를 사용하도록 설정하려면 파일 클라이언트의 서비스 속성을 업데이트해야 합니다. 다음 예제에서는 스토리지 계정의 모든 파일 공유에 대해 일시 삭제를 사용하도록 설정합니다.

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

일시 삭제가 사용하도록 설정되어 있는지 확인하고 다음 명령을 사용하여 보존 정책을 볼 수 있습니다.

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>필수 요소

일시 삭제 cmdlet은 Az.Storage 모듈의 4.8.0 이상 버전에서 사용할 수 있습니다. 

## <a name="getting-started-with-powershell"></a>PowerShell 시작

일시 삭제를 사용하도록 설정하려면 파일 클라이언트의 서비스 속성을 업데이트해야 합니다. 다음 예제에서는 스토리지 계정의 모든 파일 공유에 대해 일시 삭제를 사용하도록 설정합니다.

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

일시 삭제가 사용하도록 설정되어 있는지 확인하고 다음 명령을 사용하여 보존 정책을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

일시 삭제 cmdlet은 Azure CLI 2.1.3 버전에서 사용할 수 있습니다. 일시 삭제된 파일 공유를 복원하려면 먼저 공유의 `--deleted-version` 값을 가져와야 합니다. 해당 값을 가져오려면 다음 명령을 사용하여 스토리지 계정에 대해 삭제된 모든 공유를 나열합니다.

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

복원할 공유를 확인한 후 다음 명령과 함께 사용하여 복원할 수 있습니다.

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

일시 삭제 cmdlet은 Az.Storage 모듈의 4.8.0 이상 버전에서 사용할 수 있습니다. 일시 삭제된 파일 공유를 복원하려면 먼저 공유의 `-DeletedShareVersion` 값을 가져와야 합니다. 해당 값을 가져오려면 다음 명령을 사용하여 스토리지 계정에 대해 삭제된 모든 공유를 나열합니다.

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

복원할 공유를 확인한 후 다음 명령과 함께 사용하여 복원할 수 있습니다.

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

일시 삭제 cmdlet은 Azure CLI 2.1.3 버전에서 사용할 수 있습니다. 다음 명령을 사용하여 스토리지 계정에서 일시 삭제를 사용하지 않도록 설정할 수 있습니다.

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

일시 삭제 cmdlet은 Az.Storage 모듈의 4.8.0 이상 버전에서 사용할 수 있습니다. 다음 명령을 사용하여 스토리지 계정에서 일시 삭제를 사용하지 않도록 설정할 수 있습니다.

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>다음 단계

다른 형태의 데이터 보호 및 복구에 관해 알아보려면 [Azure Files용 공유 스냅샷 개요](storage-snapshots-files.md) 문서를 참조하세요.
