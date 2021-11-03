---
title: Blob 보관
titleSuffix: Azure Storage
description: 보관 계층에서 blob을 만들거나 기존 blob을 보관 계층으로 이동 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: d83cb752eaaa2ed3edf58d3d1f31d6d7c5af76cd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433140"
---
# <a name="archive-a-blob"></a>Blob 보관

보관 계층은 드물게 액세스 되는 blob 데이터를 저장 하기 위한 오프 라인 계층입니다. 보관 계층은 가장 낮은 저장소 비용을 제공 하지만 온라인 계층 (핫 및 쿨)과 비교 하 여 더 높은 데이터 검색 비용과 대기 시간을 제공 합니다. 데이터는 최소 180 일 동안 보관 계층에 유지 되거나 초기 삭제 요금이 적용 되어야 합니다. 보관 계층에 대 한 자세한 내용은 [보관 액세스 계층](access-tiers-overview.md#archive-access-tier)을 참조 하세요.

Blob은 보관 계층에 있지만 읽거나 수정할 수는 없습니다. 보관 계층에서 blob을 읽거나 다운로드 하려면 먼저 핫 또는 쿨 인 blob을 온라인 계층으로 리하이드레이션 해야 합니다. 보관 계층의 데이터는 리하이드레이션 작업에 대해 지정한 우선 순위에 따라 리하이드레이션 최대 15 시간이 걸릴 수 있습니다. Blob 리하이드레이션에 대 한 자세한 내용은 [보관 계층의 blob 리하이드레이션 개요](archive-rehydrate-overview.md)를 참조 하세요.

> [!CAUTION]
> 보관 계층의 blob은 오프 라인 상태 이므로 &mdash; , 다시 연결할 때까지 읽거나 수정할 수 없습니다 &mdash; . 리하이드레이션 프로세스에는 몇 시간이 걸릴 수 있으며 관련 비용이 포함 됩니다. 보관 계층으로 데이터를 이동 하기 전에 blob 데이터를 오프 라인 상태로 전환 하는 것이 워크플로에 영향을 줄 수 있는지 여부를 고려 합니다.

Azure Portal, PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용 하 여 데이터 보관을 관리할 수 있습니다.

## <a name="archive-blobs-on-upload"></a>업로드할 때 blob 보관

업로드할 때 blob을 하나 이상 보관 하려면 보관 계층에서 직접 blob을 만듭니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 업로드할 blob 또는 blob 집합을 보관 하려면 다음 단계를 수행 합니다.

1. 대상 컨테이너로 이동 합니다.
1. **업데이트** 단추를 선택합니다.
1. 업로드할 파일을 하나 이상 선택 합니다.
1. **고급** 섹션을 확장 하 고 **액세스 계층** 을 *보관* 으로 설정 합니다.
1. **업데이트** 단추를 선택합니다.

    :::image type="content" source="media/archive-blob/upload-blobs-archive-portal.png" alt-text="Azure Portal에서 보관 계층에 blob을 업로드 하는 방법을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 업로드할 때 blob 또는 blob 집합을 보관 하려면 다음 예제와 같이 [AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 명령을 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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

Azure CLI를 사용 하 여 업로드할 때 단일 blob을 보관 하려면 다음 예제와 같이 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 명령을 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Archive \
    --auth-mode login
```

Azure CLI를 사용 하 여 업로드할 때 blob 집합을 보관 하려면 다음 예제와 같이 [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) 명령을 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="archive-an-existing-blob"></a>기존 blob 보관

다음 두 가지 방법 중 하나로 기존 blob을 보관 계층으로 이동할 수 있습니다.

- Blob [계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 사용 하 여 blob 계층을 변경할 수 있습니다. **Blob 계층 설정** 은 한 계층에서 다른 계층으로 단일 blob을 이동 합니다.

    Blob을 blob **계층** 으로 이동 하는 경우 blob을 리하이드레이션 때까지 blob의 데이터를 읽거나 수정할 수 없습니다. 초기 삭제 간격이 경과 되기 전에 blob의 데이터를 읽거나 수정 해야 하는 경우 **Blob 복사** 작업을 사용 하 여 보관 계층에 blob의 복사본을 만드는 것이 좋습니다.

- [Blob 복사](/rest/api/storageservices/copy-blob) 작업을 사용 하 여 온라인 계층의 Blob을 보관 계층에 복사할 수 있습니다. **Blob 복사** 작업을 호출 하 여 온라인 계층 (핫 또는 쿨)에서 보관 계층으로 blob을 복사할 수 있습니다. 원본 blob은 온라인 계층에 남아 있으므로 온라인 계층에서 해당 데이터를 계속 읽거나 수정할 수 있습니다.

### <a name="archive-an-existing-blob-by-changing-its-tier"></a>계층을 변경 하 여 기존 blob 보관

**Blob 계층 설정** 작업을 사용 하 여 핫 또는 쿨 계층에서 보관 계층으로 blob을 이동 합니다. **Blob 계층 설정** 작업은 초기 삭제 간격이 경과할 때까지 보관 된 데이터에 액세스 하지 않아도 되는 시나리오에 가장 적합 합니다.

**Blob 계층 설정** 작업은 단일 Blob의 계층을 변경 합니다. 최적의 성능을 갖춘 보관 계층으로 blob 집합을 이동 하려면 대량 보관 작업을 수행 하는 것이 좋습니다. 대량 보관 작업은 단일 트랜잭션으로 **집합 Blob 계층** 호출의 일괄 처리를 서비스에 보냅니다. 자세한 내용은 [대량 보관](#bulk-archive)을 참조 하세요.  

#### <a name="portal"></a>[포털](#tab/azure-portal)

기존 blob을 Azure Portal 보관 계층으로 이동 하려면 다음 단계를 수행 합니다.

1. Blob의 컨테이너로 이동 합니다.
1. 보관할 blob을 선택 합니다.
1. **계층 변경** 단추를 선택 합니다.
1. **액세스 계층** 드롭다운에서 *보관* 을 선택 합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/archive-blob/set-blob-tier-archive-portal.png" alt-text="Azure Portal에서 보관할 blob 계층을 설정 하는 방법을 보여 주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 blob의 계층을 핫 또는 쿨에서 보관으로 변경 하려면 blob의 **Blobclient** 속성을 사용 하 여 blob에 대 한 .net 참조를 반환한 다음 해당 참조에 대해 **SetAccessTier** 메서드를 호출 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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

Blob의 계층을 핫 또는 쿨에서 Azure CLI를 사용 하 여 보관으로 변경 하려면 [az storage blob set 계층](/cli/azure/storage/blob#az_storage_blob_set_tier) 명령을 호출 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Archive \
    --auth-mode login
```

---

### <a name="archive-an-existing-blob-with-a-copy-operation"></a>복사 작업을 사용 하 여 기존 blob 보관

[Blob 복사](/rest/api/storageservices/copy-blob) 작업을 사용 하 여 핫 또는 쿨 계층에서 보관 계층으로 blob을 복사 합니다. 원본 blob은 핫 또는 쿨 계층에 남아 있으며 대상 blob은 보관 계층에 생성 됩니다.

**Blob 복사** 작업은 초기 삭제 간격이 경과할 때까지 보관 된 데이터를 읽거나 수정 해야 할 수 있는 시나리오에 가장 적합 합니다. 보관 된 blob을 리하이드레이션 않고도 원본 blob의 데이터에 액세스할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

해당 없음

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 온라인 계층에서 보관 계층으로 blob을 복사 하려면 [AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) 명령을 호출 하 고 보관 계층을 지정 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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

Azure CLI를 사용 하 여 온라인 계층에서 보관 계층으로 blob을 복사 하려면 [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) 명령을 호출 하 고 보관 계층을 지정 합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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

많은 수의 blob을 보관 계층으로 이동할 경우 최적의 성능을 위해 일괄 처리 작업을 사용 합니다. 일괄 처리 작업은 단일 요청으로 서비스에 여러 API 호출을 보냅니다. Blob [일괄 처리](/rest/api/storageservices/blob-batch) 작업에서 지원 되는 하위 작업에는 [Blob 삭제](/rest/api/storageservices/delete-blob) 및 [blob 계층 설정](/rest/api/storageservices/set-blob-tier)이 포함 됩니다.

일괄 처리 작업으로 blob을 보관 하려면 Azure Storage 클라이언트 라이브러리 중 하나를 사용 합니다. 다음 코드 예제에서는 .NET 클라이언트 라이브러리를 사용 하 여 기본 일괄 처리 작업을 수행 하는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/AccessTiers.cs" id="Snippet_BulkArchiveContainerContents":::

일괄 처리 작업을 사용 하 여 계층을 변경 하는 방법을 보여 주는 자세한 예제 응용 프로그램은 [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/)를 참조 하세요.

## <a name="see-also"></a>참조

- [Blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층](access-tiers-overview.md)
- [보관 계층의 Blob 리하이드레이션](archive-rehydrate-overview.md)
- [보관된 Blob을 온라인 계층으로 리하이드레이션](archive-rehydrate-to-online-tier.md)
