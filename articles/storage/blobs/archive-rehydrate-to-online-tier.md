---
title: 보관된 Blob을 온라인 계층으로 리하이드레이션
titleSuffix: Azure Storage
description: 보관 계층에 있는 Blob을 읽으려면 먼저 핫 또는 쿨 계층으로 리하디드해야 합니다. 보관 계층에서 온라인 계층으로 복사하거나 해당 계층을 보관에서 핫 또는 쿨로 변경하여 Blob을 리하디드할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: 0e24c058239d57cba1c66ebff06ba9d482bcb594
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433197"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>보관된 Blob을 온라인 계층으로 리하이드레이션

보관 계층에 있는 Blob을 읽으려면 먼저 Blob을 온라인 계층(핫 또는 쿨) 계층으로 리하우전해야 합니다. Blob을 리하이드레이션 하는 것은 두 가지 방법이 있습니다.

- Blob 복사 작업을 사용하여 핫 또는 쿨 계층의 새 [Blob에 복사합니다.](/rest/api/storageservices/copy-blob) 대부분의 시나리오에서는 Microsoft에서이 옵션을 권장합니다.
- [Blob](/rest/api/storageservices/set-blob-tier) 계층 설정 작업을 사용하여 계층을 보관에서 핫 또는 쿨로 변경합니다.

Blob을 리하딩할 때 작업의 우선 순위를 표준 우선 순위 또는 높은 우선 순위로 지정할 수 있습니다. 표준 우선 순위 리하일레이션 작업을 완료하는 데 최대 15시간이 걸릴 수 있습니다. 우선 순위가 높은 작업은 표준 우선 순위 요청보다 우선 순위가 높으며 크기가 10GB 미만인 개체의 경우 1시간 이내에 완료할 수 있습니다. 작업이 보류 중인 동안 리하위드레이션 우선 순위를 *표준에서* *높음으로* 변경할 수 있습니다.

리하이드레이션이 완료되면 이벤트를 실행할 수 있도록 Azure Event Grid를 구성하고 응답으로 응용 프로그램 코드를 실행할 수 있습니다. Blob 리하이드레이션 작업이 완료될 때 Azure 함수를 실행하는 이벤트를 처리하는 방법을 알아보려면 [blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)을 참조 하세요.

Blob 리하일레이션에 대한 자세한 내용은 [보관 계층의 Blob 리하이드레이션을 참조하세요.](archive-rehydrate-overview.md)

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>복사 작업을 해 blob 리하이드레이션을 수행하십시오

보관 계층에서 온라인 계층으로 복사하여 Blob을 리하우징하려면 PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용합니다. 보관 된 blob을 온라인 계층에 복사할 때 원본 및 대상 blob의 이름은 서로 달라야 합니다.

복사 작업이 완료되면 대상 Blob이 보관 계층에 표시됩니다. 그러면 대상 blob이 복사 작업에서 지정한 온라인 계층으로 전환됩니다. 대상 blob이 완전히 리하이드레이션되면, 새 온라인 계층에서 사용할 수 있게 됩니다.

다음 예시에서는 PowerShell 또는 Azure CLI를 사용하여 보관된 blob을 복사하는 방법을 보여줍니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

해당 없음

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 보관된 blob을 온라인 계층으로 복사하려면 [AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) 명령을 호출하고 대상 계층 및 리하이드레이션 우선 순위를 지정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
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

# Copy the source blob to a new destination blob in Hot tier with Standard priority.
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

계층을 보관에서 핫 또는 쿨로 변경하여 Blob을 리하우전하려면 Azure Portal, PowerShell 또는 Azure CLI 사용합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal Blob의 계층을 보관에서 핫 또는 쿨로 변경하려면 다음 단계를 수행합니다.

1. Azure Portal에서 리하이드레이션 blob을 찾습니다.
1. 페이지 오른쪽에서 **기타** 를 선택합니다.
1. **계층 변경** 을 선택합니다.
1. **액세스 계층** 드롭다운에서 대상 액세스 계층을 선택합니다.
1. **리하이드레이션 우선 순위** 드롭다운에서 원하는 리하이드레이션 우선 순위를 선택합니다. 리하이드레이션 우선 순위를 *높음* 로 설정하면 일반적으로 더 빠른 리하이드레이션이 발생 하지만 비용이 더 많이 듭니다.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Azure Portal 보관 계층에서 Blob을 리하딩하는 방법을 보여주는 스크린샷 ":::

1. **저장** 단추를 선택합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob의 계층을 보관에서 핫 또는 쿨로 변경하려면 **Blob의 BlobClient** 속성을 사용하여 Blob에 대한 .NET 참조를 반환한 다음 해당 참조에서 **SetAccessTier** 메서드를 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Hot with Standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "Standard")
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 Blob의 계층을 보관에서 핫 또는 쿨로 변경하려면 [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --rehydrate-priority Standard \
    --auth-mode login
```

---

## <a name="bulk-rehydrate-a-set-of-blobs"></a>Blob 집합을 대량으로 리하디드합니다.

한 번에 많은 수의 Blob을 리하이드레이션하려면 [Blob 일괄 처리](/rest/api/storageservices/blob-batch) 작업을 호출하여 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)을 대량 작업으로 호출합니다. 일괄 처리 작업을 수행하는 방법을 보여 주는 코드 예제는 [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/)를 참조하세요.

## <a name="check-the-status-of-a-rehydration-operation"></a>리하이드레이션 작업의 상태를 확인합니다.

Blob이 리하이드레이션 경우 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 해당 상태 및 리하이드레이션 우선 순위를 확인할 수 있습니다. 상태 속성은 리하이드레이션 작업의 대상 계층에 따라 *rehydrate-pending-to-hot* 또는 *rehydrate-pending-to-cool* 을 반환할 수 있습니다. 리하이드레이션 우선 순위 속성은 *표준* 또는 *높음* 중 하나를 반환합니다.

보관된 Blob의 복원에는 최대 15시간이 소요될 수 있으며 복원이 완료되었는지 여부를 확인하기 위해 Blob의 상태를 반복적으로 폴링하는 것은 비효율적입니다. Azure Event Grid를 사용하여 복원이 완료될 때 발생하는 이벤트를 캡처하면 더 나은 성능과 비용 최적화를 제공합니다. Blob 리하이드레이션에서 이벤트가 발생 하는 경우 Azure 함수를 실행하는 방법을 알아보려면 [blob 리하이드레이션 이벤트에 대 한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)을 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 보류 중인 리하이드레이션 작업의 상태 및 우선 순위를 확인 하려면 blob에 대한 **계층 변경** 대화 상자를 표시합니다.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Azure Portal blob에 대 한 리하이드레이션 상태를 보여 주는 스크린샷":::

리하이드레이션 완료되면 완전히 된 blob가 대상 온라인 계층에 표시되는 Azure Portal를 확인할 수 있습니다.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="쿨 계층의 리하위드 Blob 및 이벤트 처리기에서 작성한 로그 Blob을 보여주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 보류 중인 리하이드레이션 작업의 상태와 우선 순위를 확인하려면 [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) 명령을 호출하고 Blob의 **ArchiveStatus** 및 **RehydratePriority** 속성을 확인합니다. 리하이드레이션 복사 작업인 경우 대상 blob에서 이러한 속성을 확인합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
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
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="change-the-rehydration-priority-of-a-pending-operation"></a>보류 중인 작업의 리하위드레이션 우선 순위 변경

표준 우선 순위 리하일레이션 작업이 보류 중인 동안 Blob에 대한 리하위드레이션 우선 순위 설정을 *표준에서* *높음으로* 변경하여 해당 Blob을 더 빠르게 리하우징할 수 있습니다.

보류 중인 작업에 대해 리하일레이션 우선 순위 설정을 *높음에서* *표준으로* 낮출 수 없습니다. 또한 리하일레이션 우선 순위를 변경하면 청구에 영향을 미칠 수 있습니다. 자세한 내용은 [보관 계층 에서 Blob 리하일레이션을 참조하세요.](archive-rehydrate-overview.md)

### <a name="change-the-rehydration-priority-for-a-pending-set-blob-tier-operation"></a>보류 중인 Blob 계층 설정 작업에 대한 리하위드레이션 우선 순위 변경

표준 우선 순위 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업이 보류 중인 동안 리하일레이션 우선 순위를 변경하려면 Azure Portal, PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용합니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal 보류 중인 작업의 리하위드레이션 우선 순위를 변경하려면 다음 단계를 수행합니다.

1. 리하일레이션 우선 순위를 변경하려는 Blob으로 이동하여 Blob을 선택합니다.
1. 계층 **변경** 단추를 선택합니다.
1. 계층 **변경** 대화 상자에서 리하우전 Blob(핫 또는 쿨)에 대한 대상 온라인 액세스 계층으로 액세스 계층을 설정합니다. **보관 상태** 필드에는 대상 온라인 계층이 표시됩니다.
1. **리하이드 전송률 우선 순위** 드롭다운에서 우선 순위를 *높음으로* 설정합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/update-rehydration-priority-portal.png" alt-text="Azure Portal 리하우전 Blob에 대한 리하위드레이션 우선 순위를 업데이트하는 방법을 보여주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 보류 중인 작업에 대한 리하위드레이션 우선 순위를 변경하려면 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈 버전 3.12.0 이상 버전을 설치했는지 확인합니다. 다음으로, 서비스에서 Blob의 속성을 얻습니다. 이 단계는 가장 최근의 속성 설정이 있는 개체가 있는지 확인하는 데 필요합니다. 마지막으로 **Blob의 BlobClient** 속성을 사용하여 Blob에 대한 .NET 참조를 반환한 다음 해당 참조에서 **SetAccessTier** 메서드를 호출합니다.

```azurepowershell
# Get the blob from the service.
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

# Verify that the current rehydration priority is Standard. 
if ($rehydratingBlob.BlobProperties.RehydratePriority -eq "Standard")
{
    # Change rehydration priority to High, using the same target tier.
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-hot")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Hot", $null, "High")
        "Changing rehydration priority to High for blob moving to Hot tier."
    }
    
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-cool")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Cool", $null, "High")
        "Changing rehydration priority to High for blob moving to Cool tier."
    }
}
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 보류 중인 작업의 리하일레이션 우선 순위를 변경하려면 먼저 Azure CLI 버전 2.29.2 이상 버전을 설치했는지 확인합니다. Azure CLI 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치하는 방법을](/cli/azure/install-azure-cli)참조하세요.

다음으로, 매개 변수를 높음으로 설정하여 [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) 명령을 호출합니다. `--rehydrate-priority`  대상 계층(핫 또는 쿨)은 리하일레이션 작업에 대해 원래 지정한 계층과 동일해야 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
# Update the rehydration priority for a blob moving to the Hot tier.
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Hot \
    --rehydrate-priority High \
    --auth-mode login

# Show the updated property values.
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

### <a name="change-the-rehydration-priority-for-a-pending-copy-blob-operation"></a>보류 중인 Blob 복사 작업의 리하위드레이션 우선 순위 변경

보관된 Blob을 온라인 계층에 복사하여 Blob을 리하디드하는 경우 Azure Storage 즉시 보관 계층에 대상 Blob을 만듭니다. 그런 다음 대상 Blob은 복사 작업에 지정된 우선 순위를 사용하여 대상 계층으로 리하우징됩니다. 복사 작업을 사용하여 보관된 Blob을 리하디드하는 자세한 내용은 [온라인 계층에 보관된 Blob 복사를 참조하세요.](archive-rehydrate-overview.md#copy-an-archived-blob-to-an-online-tier)

보관 계층에서 표준 우선 순위가 있는 온라인 계층으로 복사 작업을 수행하려면 PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용합니다. 자세한 내용은 [복사 작업을 통해 Blob 리하이딩을 참조하세요.](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation) 다음으로 보류 중인 리하일레이션에 대한 리하위드레이션 우선 순위를 *표준에서* *높음으로* 변경하려면 대상 **Blob에서 Blob 계층 설정을** 호출하고 대상 계층을 지정합니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

복사 작업을 시작한 후 Azure Portal 원본 및 대상 Blob이 모두 보관 계층에 있음을 확인할 수 있습니다. 대상 Blob은 표준 우선 순위로 리하우징하고 있습니다.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-standard-priority.png" alt-text="보관 계층의 대상 Blob 및 표준 우선 순위가 있는 리하우징을 보여주는 스크린샷":::

대상 Blob의 리하위드레이션 우선 순위를 변경하려면 다음 단계를 수행합니다.

1. 대상 Blob을 선택합니다.
1. 계층 **변경** 단추를 선택합니다.
1. 계층 **변경** 대화 상자에서 리하우전 Blob(핫 또는 쿨)에 대한 대상 온라인 액세스 계층으로 액세스 계층을 설정합니다. **보관 상태** 필드에는 대상 온라인 계층이 표시됩니다.
1. **리하이드 전송률 우선 순위** 드롭다운에서 우선 순위를 *높음으로* 설정합니다.
1. **저장** 을 선택합니다.

이제 대상 Blob의 속성 페이지에 우선 순위가 높은 리하우징 중이 표시됩니다.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-high-priority.png" alt-text="보관 계층의 대상 Blob 및 높은 우선 순위의 리하이드레이션을 보여주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

복사 작업을 시작한 후 대상 Blob의 속성을 확인합니다. 대상 Blob이 보관 계층에 있고 표준 우선 순위로 리하우징하는 것을 볼 수 있습니다.

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$destContainerName = "<container>"
$destBlobName = "<destination-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Get properties for the destination blob.
$destinationBlob = Get-AzStorageBlob -Container $destContainerName `
    -Blob $destBlobName `
    -Context $ctx

$destinationBlob.BlobProperties.AccessTier
$destinationBlob.BlobProperties.ArchiveStatus
$destinationBlob.BlobProperties.RehydratePriority
```

다음으로, 보류 중인 Blob 계층 설정 작업의 리하위드레이션 우선 순위 변경에 설명된 대로 PowerShell을 통해 **SetAccessTier** [메서드를 호출하여 대상 Blob의 리하위드레이션 우선 순위를](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation) *높음으로* 변경합니다. 대상 계층(핫 또는 쿨)은 리하일레이션 작업에 대해 원래 지정한 계층과 동일해야 합니다. 속성을 다시 확인하여 Blob이 높은 우선 순위로 리하이드되는지 확인합니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

복사 작업을 시작한 후 대상 Blob의 속성을 확인합니다. 대상 Blob이 보관 계층에 있고 표준 우선 순위로 리하우징하는 것을 볼 수 있습니다.

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

다음으로, 보류 중인 [Blob 계층 설정](/cli/azure/storage/blob#az_storage_blob_set_tier) `--rehydrate-priority` 작업의 [리하일레이션 우선 순위 변경에](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation)설명된 대로 매개 변수를 *높음* 으로 설정하여 az storage blob set-tier 명령을 호출합니다. 대상 계층(핫 또는 쿨)은 리하일레이션 작업에 대해 원래 지정한 계층과 동일해야 합니다. 속성을 다시 확인하여 Blob이 높은 우선 순위로 리하이드되는지 확인합니다.

---

## <a name="see-also"></a>참조

- [Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층입니다.](access-tiers-overview.md)
- [보관 계층의 Blob 리하일레이션 개요](archive-rehydrate-overview.md)
- [Blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)
- [Blob Storage 이벤트에 응답](storage-blob-event-overview.md)
