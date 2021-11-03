---
title: Blob 보관
titleSuffix: Azure Storage
description: 보관 계층에서 Blob을 만들거나 기존 Blob을 보관 계층으로 이동하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 5b779df91c4f0347e329348b7bdccd49691d0d5d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052955"
---
# <a name="archive-a-blob"></a>Blob 보관

보관 계층은 거의 액세스하지 않는 Blob 데이터를 저장하기 위한 오프라인 계층입니다. 보관 계층은 스토리지 비용이 가장 낮지만 온라인 계층(핫 및 쿨)에 비해 데이터 검색 비용과 대기 시간이 더 높습니다. 데이터는 적어도 180일 동안 보관 계층에 남아 있거나 초기 삭제 요금이 부과되어야 합니다. 보관 계층에 대한 자세한 내용은 [보관 액세스 계층을 참조하세요.](access-tiers-overview.md#archive-access-tier)

Blob이 보관 계층에 있는 동안에는 읽거나 수정할 수 없습니다. 보관 계층에서 Blob을 읽거나 다운로드하려면 먼저 핫 또는 쿨 온라인 계층으로 리하이딩해야 합니다. 보관 계층의 데이터는 리하일레이션 작업에 대해 지정한 우선 순위에 따라 리하우징하는 데 최대 15시간이 걸릴 수 있습니다. Blob 리하일레이션에 대한 자세한 내용은 [보관 계층의 Blob 리하일레이션 개요를 참조하세요.](archive-rehydrate-overview.md)

> [!CAUTION]
> 보관 계층의 Blob은 오프라인입니다. &mdash; 즉, 리하위드될 때까지 읽거나 수정할 수 &mdash; 없습니다. 리하일레이션 프로세스에는 몇 시간이 걸릴 수 있으며 관련 비용이 있습니다. 데이터를 보관 계층으로 이동하기 전에 Blob 데이터를 오프라인으로 전환하면 워크플로에 영향을 줄 수 있는지 여부를 고려해야 합니다.

Azure Portal, PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 데이터 보관을 관리할 수 있습니다.

## <a name="archive-blobs-on-upload"></a>업로드할 때 Blob 보관

업로드 시 하나 이상의 Blob을 보관하려면 보관 계층에서 직접 Blob을 만듭니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal 업로드 시 Blob 또는 Blob 집합을 보관하려면 다음 단계를 수행합니다.

1. 대상 컨테이너로 이동합니다.
1. **업데이트** 단추를 선택합니다.
1. 업로드할 파일을 선택합니다.
1. **고급** 섹션을 확장하고 **액세스 계층을** *보관으로* 설정합니다.
1. **업데이트** 단추를 선택합니다.

    :::image type="content" source="media/archive-blob/upload-blobs-archive-portal.png" alt-text="Azure Portal 보관 계층에 Blob을 업로드하는 방법을 보여주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 업로드 시 Blob 또는 Blob 집합을 보관하려면 다음 예제와 같이 [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 명령을 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Archive tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Archive

# Upload the contents of a sample-blobs directory to the Archive tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Archive
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 업로드 시 단일 Blob을 보관하려면 다음 예제와 같이 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 명령을 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Archive \
    --auth-mode login
```

Azure CLI 사용하여 업로드 시 Blob 집합을 보관하려면 다음 예제와 같이 [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) 명령을 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="archive-an-existing-blob"></a>기존 Blob 보관

다음 두 가지 방법 중 하나로 기존 Blob을 보관 계층으로 이동할 수 있습니다.

- Blob 계층 설정 작업을 통해 [Blob의 계층을](/rest/api/storageservices/set-blob-tier) 변경할 수 있습니다. **Blob 계층 설정은** 단일 Blob을 한 계층에서 다른 계층으로 이동합니다.

    Blob 계층 설정을 사용하여 Blob을 보관 계층으로 이동하면 **Blob을** 리하우전할 때까지 Blob의 데이터를 읽거나 수정할 수 없습니다. 초기 삭제 간격이 경과하기 전에 Blob의 데이터를 읽거나 수정해야 하는 경우 **Blob 복사** 작업을 사용하여 보관 계층에 Blob 복사본을 만드는 것이 좋습니다.

- Blob 복사 작업을 사용하여 온라인 계층의 [Blob을](/rest/api/storageservices/copy-blob) 보관 계층에 복사할 수 있습니다. **Blob 복사** 작업을 호출하여 온라인 계층(핫 또는 쿨)에서 보관 계층으로 Blob을 복사할 수 있습니다. 원본 Blob은 온라인 계층에 남아 있으며 온라인 계층에서 해당 데이터를 계속 읽거나 수정할 수 있습니다.

### <a name="archive-an-existing-blob-by-changing-its-tier"></a>계층을 변경하여 기존 Blob 보관

Blob 계층 설정 작업을 사용하여 **Blob을** 핫 또는 쿨 계층에서 보관 계층으로 이동합니다. **Blob 계층 설정** 작업은 초기 삭제 간격이 경과하기 전에 보관된 데이터에 액세스할 필요가 없는 시나리오에 가장 적합합니다.

Blob 계층 설정 작업은 단일 **Blob의** 계층을 변경합니다. 최적의 성능으로 Blob 집합을 보관 계층으로 이동하려면 대량 보관 작업을 수행하는 것이 좋습니다. 대량 보관 작업은 단일 트랜잭션에서 **서비스로 Blob 계층 설정** 호출의 일괄 처리를 보냅니다. 자세한 내용은 [대량 보관을 참조하세요.](#bulk-archive)  

#### <a name="portal"></a>[포털](#tab/azure-portal)

기존 Blob을 Azure Portal 보관 계층으로 이동하려면 다음 단계를 수행합니다.

1. Blob의 컨테이너로 이동합니다.
1. 보관할 Blob을 선택합니다.
1. 계층 **변경** 단추를 선택합니다.
1. **액세스 계층** 드롭다운에서 *보관을* 선택합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/archive-blob/set-blob-tier-archive-portal.png" alt-text="blob의 계층을 Azure Portal 보관으로 설정하는 방법을 보여주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob의 계층을 핫 또는 쿨에서 보관으로 변경하려면 **Blob의 BlobClient** 속성을 사용하여 Blob에 대한 .NET 참조를 반환한 다음 해당 참조에서 **SetAccessTier** 메서드를 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Archive.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Archive", $null)
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 Blob의 계층을 핫 또는 쿨에서 보관으로 변경하려면 [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Archive \
    --auth-mode login
```

---

### <a name="archive-an-existing-blob-with-a-copy-operation"></a>복사 작업을 통해 기존 Blob 보관

[Blob 복사](/rest/api/storageservices/copy-blob) 작업을 사용하여 핫 또는 쿨 계층에서 보관 계층으로 Blob을 복사합니다. 원본 Blob은 핫 또는 쿨 계층에 유지되고 대상 Blob은 보관 계층에 만들어집니다.

**Blob 복사** 작업은 초기 삭제 간격이 경과하기 전에 보관된 데이터를 읽거나 수정해야 하는 시나리오에 가장 적합합니다. 보관된 Blob을 리하우징할 필요 없이 원본 Blob의 데이터에 액세스할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

해당 없음

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 온라인 계층에서 보관 계층으로 Blob을 복사하려면 [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) 명령을 호출하고 보관 계층을 지정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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
    -StandardBlobTier Archive `
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 온라인 계층에서 보관 계층으로 Blob을 [복사하려면 az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) 명령을 호출하고 보관 계층을 지정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="bulk-archive"></a>대량 보관

많은 수의 Blob을 보관 계층으로 이동하는 경우 최적의 성능을 위해 일괄 처리 작업을 사용합니다. 일괄 처리 작업은 단일 요청으로 서비스에 여러 API 호출을 보냅니다. [Blob Batch](/rest/api/storageservices/blob-batch) 작업에서 지원하는 하위 작업에는 [Blob 삭제](/rest/api/storageservices/delete-blob) 및 [Blob 계층 설정이](/rest/api/storageservices/set-blob-tier)포함됩니다.

일괄 처리 작업으로 Blob을 보관하려면 Azure Storage 클라이언트 라이브러리 중 하나를 사용합니다. 다음 코드 예제에서는 .NET 클라이언트 라이브러리를 사용하여 기본 일괄 처리 작업을 수행하는 방법을 보여줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/AccessTiers.cs" id="Snippet_BulkArchiveContainerContents":::

일괄 처리 작업을 사용하여 계층을 변경하는 방법을 보여 주는 심층 샘플 애플리케이션은 [AzBulkSetBlobTier 를 참조하세요.](/samples/azure/azbulksetblobtier/azbulksetblobtier/)

## <a name="see-also"></a>참고 항목

- [Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층](access-tiers-overview.md)
- [보관 계층의 Blob 리하일레이션](archive-rehydrate-overview.md)
- [보관된 Blob을 온라인 계층으로 리하이드레이션](archive-rehydrate-to-online-tier.md)
