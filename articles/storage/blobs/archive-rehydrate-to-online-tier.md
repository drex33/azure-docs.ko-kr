---
title: 보관된 Blob을 온라인 계층으로 리하이드레이션
titleSuffix: Azure Storage
description: 보관 계층에 있는 blob을 읽으려면 먼저 핫 또는 쿨 계층으로 리하이드레이션 해야 합니다. 보관 계층에서 온라인 계층으로 복사하거나 해당 계층을 아카이브에서 핫 또는 쿨로 변경하여 Blob을 재수화할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: c033920b88f2863d34f43bf0affe4b9165995a3a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258778"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>보관된 Blob을 온라인 계층으로 리하이드레이션

보관 계층에 있는 blob을 읽으려면 먼저 blob을 핫 또는 쿨 계층으로 리하이드레이션 해야 합니다. Blob을 리하이드레이션 하는 것은 두 가지 방법이 있습니다.

- [Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 blob 복사](/rest/api/storageservices/copy-blob-from-url)작업을 사용하여 핫 또는 쿨 계층의 새 blob에 복사합니다. 대부분의 시나리오에서는 Microsoft에서이 옵션을 권장합니다.
- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 사용하여 해당 계층을 보관에서 핫 또는 쿨로 변경합니다.

이 작업은 완료하는데 최대 15시간이 걸릴 수 있습니다. 리하이드레이션이 완료되면 이벤트를 실행할 수 있도록 Azure Event Grid를 구성하고 응답으로 응용 프로그램 코드를 실행할 수 있습니다. Blob 리하이드레이션 작업이 완료될 때 Azure 함수를 실행하는 이벤트를 처리하는 방법을 알아보려면 [blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)을 참조 하세요.

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>복사 작업을 해 blob 리하이드레이션을 수행하십시오

리하이드레이션을 온라인 계층으로 복사하여 보관 계층에서 blob을 다시 만들려면 PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용합니다. 보관 된 blob을 온라인 계층에 복사할 때 원본 및 대상 blob의 이름은 서로 달라야 합니다.

복사 작업이 완료되면 대상 blob이 보관 계층에 표시됩니다. 그러면 대상 blob이 복사 작업에서 지정한 온라인 계층으로 전환됩니다. 대상 blob이 완전히 리하이드레이션되면, 새 온라인 계층에서 사용할 수 있게 됩니다.

다음 예시에서는 PowerShell 또는 Azure CLI를 사용하여 보관된 blob을 복사하는 방법을 보여줍니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

해당 없음

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 보관된 blob을 온라인 계층으로 복사하려면 [AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) 명령을 호출하고 대상 계층 및 리하이드레이션 우선 순위를 지정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$srcContainerName = "<source-container>"
$destContainerName = "<dest-container>"
$srcBlobName = "<source-blob>"
$destBlobName = "<dest-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Copy the source blob to a new destination blob in hot tier with standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Hot `
    -RehydratePriority Standard `
    -Context $ctx
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 있는 보관된 blob을 온라인 계층으로 복사하려면 [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) 명령을 호출하고 대상 계층 및 리하이드레이션 우선 순위를 지정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --rehydrate-priority standard \
    --auth-mode login
```

---

## <a name="rehydrate-a-blob-by-changing-its-tier"></a>계층을 변경하여 blob 리하이드레이션을 수행하십시오

보관에서 핫 또는 쿨 작업으로 계층을 변경하여 blob을 리하이드레이션할 때는 Azure Portal, PowerShell 또는 Azure CLI를 사용합니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 핫 또는 쿨로 blob의 계층을 변경하려면 다음 단계를 수행합니다.

1. Azure Portal에서 리하이드레이션 blob을 찾습니다.
1. 페이지 오른쪽에서 **기타** 를 선택합니다.
1. **계층 변경** 을 선택합니다.
1. **액세스 계층** 드롭다운에서 대상 액세스 계층을 선택합니다.
1. **리하이드레이션 우선 순위** 드롭다운에서 원하는 리하이드레이션 우선 순위를 선택합니다. 리하이드레이션 우선 순위를 *높음* 로 설정하면 일반적으로 더 빠른 리하이드레이션이 발생 하지만 비용이 더 많이 듭니다.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Azure Portal의 보관 계층에서 blob을 리하이드레이션 하는 방법을 보여 주는 스크린샷 ":::

1. **저장** 단추를 선택합니다.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 blob의 계층을 보관에서 핫 또는 쿨로 변경 하려면 blob의 **ICloudBlob** 속성을 사용하여 blob에 대한 .NET 참조를 반환한 다음 해당 참조에 대해 **Setstandardblobtier** 메서드를 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob’s access tier to hot with standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "High")
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Blob의 계층을 보관에서 핫 또는 Azure CLI 쿨로 변경 하려면 [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob set-tier \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --account-name <account-name> \
    --rehydrate-priority High \
    --auth-mode login
```

---

## <a name="rehydrate-a-large-number-of-blobs"></a>많은 수의 Blob 리하이드레이션

한 번에 많은 수의 Blob을 리하이드레이션하려면 [Blob 일괄 처리](/rest/api/storageservices/blob-batch) 작업을 호출하여 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)을 대량 작업으로 호출합니다. 일괄 처리 작업을 수행하는 방법을 보여 주는 코드 예제는 [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/)를 참조하세요.

## <a name="check-the-status-of-a-rehydration-operation"></a>리하이드레이션 작업의 상태를 확인합니다.

Blob이 리하이드레이션 경우 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 해당 상태 및 리하이드레이션 우선 순위를 확인할 수 있습니다. 상태 속성은 리하이드레이션 작업의 대상 계층에 따라 *rehydrate-pending-to-hot* 또는 *rehydrate-pending-to-cool* 을 반환할 수 있습니다. 리하이드레이션 우선 순위 속성은 *표준* 또는 *높음* 중 하나를 반환합니다.

보관된 Blob의 복원에는 최대 15시간이 소요될 수 있으며 복원이 완료되었는지 여부를 확인하기 위해 Blob의 상태를 반복적으로 폴링하는 것은 비효율적입니다. Azure Event Grid를 사용하여 복원이 완료될 때 발생하는 이벤트를 캡처하면 더 나은 성능과 비용 최적화를 제공합니다. Blob 리하이드레이션에서 이벤트가 발생 하는 경우 Azure 함수를 실행하는 방법을 알아보려면 [blob 리하이드레이션 이벤트에 대 한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)을 참조하세요.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 보류 중인 리하이드레이션 작업의 상태 및 우선 순위를 확인 하려면 blob에 대한 **계층 변경** 대화 상자를 표시합니다.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Azure Portal blob에 대 한 리하이드레이션 상태를 보여 주는 스크린샷":::

리하이드레이션 완료되면 완전히 된 blob가 대상 온라인 계층에 표시되는 Azure Portal를 확인할 수 있습니다.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="쿨 계층에서 처리 된 blob 및 이벤트 처리기가 기록한 로그 blob을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 보류 중인 리하이드레이션 작업의 상태와 우선 순위를 확인하려면 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) 명령을 호출하고 Blob의 **ArchiveStatus** 및 **RehydratePriority** 속성을 확인합니다. 리하이드레이션 복사 작업인 경우 대상 blob에서 이러한 속성을 확인합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$rehydratingBlob.BlobProperties.ArchiveStatus
$rehydratingBlob.BlobProperties.RehydratePriority
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 보류중인 리하이드레이션 작업의 상태와 우선 순위를 확인하려면 [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) 명령을 호출하고 대상 Blob의 **rehydrationStatus** 및 **rehydratePriority** 속성을 확인합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <destination-blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md)
- [보관 계층의 blob 리하이드레이션 개요](archive-rehydrate-overview.md)
- [Blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)
- [Blob Storage 이벤트에 응답](storage-blob-event-overview.md)
