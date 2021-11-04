---
title: Blob의 액세스 계층 설정
titleSuffix: Azure Storage
description: Blob의 액세스 계층을 업로드할 때 지정 하는 방법 또는 기존 blob에 대 한 액세스 계층을 변경 하는 방법에 대해 알아봅니다.
author: tamram
ms.author: tamram
ms.date: 10/25/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2e6a5cc63dceff6145f66fd5a23e4ffb89a3817f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102920"
---
# <a name="set-a-blobs-access-tier"></a>Blob의 액세스 계층 설정

다음 방법 중 하나를 사용 하 여 blob의 액세스 계층을 설정할 수 있습니다.

- 저장소 계정에 대 한 기본 온라인 액세스 계층 (핫 또는 쿨)을 설정 합니다. 개별 blob에 대 한 설정을 명시적으로 재정의 하지 않는 한 계정의 blob은이 액세스 계층을 상속 합니다.
- 업로드할 때 blob의 계층을 명시적으로 설정 합니다. 핫, 쿨 또는 보관 계층에서 blob을 만들 수 있습니다.
- 일반적으로 blob 계층 작업 집합을 사용 하 여 기존 blob의 계층을 변경 하 여 더울 계층에서 시작 계층으로 이동 하는 것을 말합니다.
- Blob 복사 작업을 사용 하 여 blob을 복사 하 여 일반적으로는 냉장고 계층에서 더울 1로 이동 합니다.

이 문서에서는 온라인 액세스 계층 (핫 또는 쿨)에서 blob을 관리 하는 방법을 설명 합니다. Blob을 보관 계층으로 이동 하는 방법에 대 한 자세한 내용은 [Blob 보관](archive-blob.md)을 참조 하세요. 보관 계층에서 blob을 리하이드레이션 하는 방법에 대 한 자세한 내용은 [보관 된 blob을 온라인 계층으로 리하이드레이션](archive-rehydrate-to-online-tier.md)를 참조 하세요.

Blob의 액세스 계층에 대 한 자세한 내용은 [blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층](access-tiers-overview.md)을 참조 하세요.

## <a name="set-the-default-access-tier-for-a-storage-account"></a>저장소 계정에 대 한 기본 액세스 계층 설정

범용 v2 저장소 계정에 대 한 기본 액세스 계층 설정은 새 blob이 기본적으로 만들어지는 온라인 계층을 결정 합니다. 계정을 만들 때 또는 기존 계정의 구성을 업데이트 하 여 범용 v2 저장소 계정에 대 한 기본 액세스 계층을 설정할 수 있습니다.

기존 범용 v2 저장소 계정에 대 한 기본 액세스 계층 설정을 변경 하는 경우 변경 내용은 액세스 계층을 명시적으로 설정 하지 않은 계정의 모든 blob에 적용 됩니다. 기본 액세스 계층을 변경 하면 청구에 영향을 줄 수 있습니다. 자세한 내용은 [기본 계정 액세스 계층 설정](access-tiers-overview.md#default-account-access-tier-setting)을 참조 하세요.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal 만든 시간에 저장소 계정에 대 한 기본 액세스 계층을 설정 하려면 다음 단계를 수행 합니다.

1. **Storage 계정** 페이지로 이동 하 고 **만들기** 단추를 선택 합니다.
1. **기본** 탭을 입력 합니다.
1. **고급** 탭의 **Blob storage** 에서 **액세스 계층** 을 *핫* 또는 *쿨* 로 설정 합니다. 기본 설정은 *Hot* 입니다.
1. **검토 + 만들기** 를 선택 하 여 설정의 유효성을 검사 하 고 저장소 계정을 만듭니다.

    :::image type="content" source="media/access-tiers-online-manage/set-default-access-tier-create-portal.png" alt-text="저장소 계정을 만들 때 기본 액세스 계층을 설정 하는 방법을 보여 주는 스크린샷":::

Azure Portal의 기존 저장소 계정에 대 한 기본 액세스 계층을 업데이트 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **설정** 에서 **구성** 을 선택합니다.
1. **Blob 액세스 계층 (기본값)** 설정을 찾아 *핫* 또는 *쿨* 중 하나를 선택 합니다. 이 속성을 이전에 설정 하지 않은 경우 기본 설정은 *Hot* 입니다.
1. 변경 내용을 저장합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 저장소 계정에 대 한 기본 액세스 계층 설정을 변경 하려면 새 기본 액세스 계층을 지정 하 여 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 호출 합니다.

```azurepowershell
$rgName = <resource-group>
$accountName = <storage-account>

# Change the storage account tier to Cool
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Cool
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell을 사용 하 여 저장소 계정에 대 한 기본 액세스 계층 설정을 변경 하려면 새 기본 액세스 계층을 지정 하 여 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 호출 합니다.

```azurecli
# Change the storage account tier to Cool
az storage account update \
    --resource-group <resource-group> \
    --name <storage-account> \
    --access-tier Cool
```

---

## <a name="set-a-blobs-tier-on-upload"></a>업로드할 때 blob 계층 설정

Azure Storage에 blob을 업로드 하는 경우 업로드 시 blob의 계층을 설정 하는 두 가지 옵션이 있습니다.

- Blob이 생성 될 계층을 명시적으로 지정할 수 있습니다. 이 설정은 저장소 계정에 대 한 기본 액세스 계층을 재정의 합니다. 핫, 쿨 또는 보관으로 업로드할 때 blob 또는 blob 집합의 계층을 설정할 수 있습니다.
- 계층을 지정 하지 않고 blob을 업로드할 수 있습니다. 이 경우 blob은 저장소 계정에 대해 지정 된 기본 액세스 계층 (핫 또는 쿨)에 생성 됩니다.

다음 섹션에서는 핫 또는 쿨 계층에 blob을 업로드 하도록 지정 하는 방법을 설명 합니다. 업로드할 때 blob을 보관 하는 방법에 대 한 자세한 내용은 [업로드 시 Blob 보관](archive-blob.md#archive-blobs-on-upload)을 참조 하세요.

### <a name="upload-a-blob-to-a-specific-online-tier"></a>특정 온라인 계층에 blob 업로드

핫 또는 쿨 계층 계층에서 blob을 만들려면 blob을 만들 때 해당 계층을 지정 합니다. 업로드에 지정 된 액세스 계층은 저장소 계정에 대 한 기본 액세스 계층을 재정의 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 특정 계층에 blob 또는 blob 집합을 업로드 하려면 다음 단계를 수행 합니다.

1. 대상 컨테이너로 이동 합니다.
1. **업데이트** 단추를 선택합니다.
1. 업로드할 파일을 하나 이상 선택 합니다.
1. **고급** 섹션을 확장 하 고 **액세스 계층** 을 *핫* 또는 *쿨* 로 설정 합니다.
1. **업데이트** 단추를 선택합니다.

    :::image type="content" source="media/access-tiers-online-manage/upload-blob-to-online-tier-portal.png" alt-text="Azure Portal에서 온라인 계층에 blob을 업로드 하는 방법을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 blob 또는 blob 집합을 특정 계층에 업로드 하려면 다음 예제와 같이 [AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 명령을 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Cool tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Cool

# Upload the contents of a sample-blobs directory to the Cool tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Cool
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 특정 계층에 blob을 업로드 하려면 다음 예제와 같이 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 명령을 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Cool \
    --auth-mode login
```

Azure CLI를 사용 하 여 특정 계층에 blob 집합을 업로드 하려면 다음 예제와 같이 [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) 명령을 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="upload-a-blob-to-the-default-tier"></a>기본 계층에 blob 업로드

Storage 계정에는 새 blob을 만든 온라인 계층을 나타내는 기본 액세스 계층 설정이 있습니다. 기본 액세스 계층 설정은 핫 또는 쿨로 설정할 수 있습니다. 이 설정의 동작은 저장소 계정 유형에 따라 약간 다릅니다.

- 새 범용 v2 저장소 계정에 대 한 기본 액세스 계층은 기본적으로 핫 계층으로 설정 됩니다. 저장소 계정을 만들 때 또는 만든 후에 기본 액세스 계층 설정을 변경할 수 있습니다.
- 레거시 Blob Storage 계정을 만들 때 저장소 계정을 만들 때 기본 액세스 계층 설정을 핫 또는 쿨로 지정 해야 합니다. 저장소 계정에 대 한 기본 액세스 계층 설정은 생성 된 후 변경할 수 있습니다.

명시적으로 할당 된 계층이 없는 blob는 기본 계정 액세스 계층 설정에서 계층을 유추 합니다. Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 blob의 액세스 계층을 유추할 지 여부를 결정할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Blob의 액세스 계층이 기본 계정 액세스 계층 설정에서 유추 되는 경우 Azure Portal는 액세스 계층을 **핫 (유추)** 또는 **쿨 (유추)** 로 표시 합니다.

:::image type="content" source="media/access-tiers-online-manage/default-access-tier-portal.png" alt-text="Azure Portal의 기본 액세스 계층을 사용 하는 blob을 보여 주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

blob의 액세스 계층을 확인 하 고 Azure PowerShell에서 유추할 지 여부를 확인 하려면 blob를 검색 한 다음 해당 **AccessTier** 및 **AccessTierInferred** 속성을 확인 합니다.

```azurepowershell
$rgName = "<resource-group>"
$storageAccount = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context.
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Get the blob from the service.
$blob = Get-AzStorageBlob -Context $ctx -Container $containerName -Blob $blobName

# Check the AccessTier and AccessTierInferred properties.
# If the access tier is inferred, that property returns true.
$blob.BlobProperties.AccessTier
$blob.BlobProperties.AccessTierInferred
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Blob의 액세스 계층을 확인 하 고 Azure CLI에서 유추할 지 여부를 확인 하려면 blob를 검색 한 다음 **Blobtier** 나 **blobTierInferred** 속성을 확인 합니다.

```azurecli
az storage blob show \
    --container-name <container> \
    --name <blob> \
    --account-name <storage-account> \
    --query '[properties.blobTier, properties.blobTierInferred]' \
    --output tsv \
    --auth-mode login 
```

---

## <a name="move-a-blob-to-a-different-online-tier"></a>다른 온라인 계층으로 blob 이동

다음 두 가지 방법 중 하나로 기존 blob의 계층을 변경할 수 있습니다.

- 직접 또는 [수명 주기 관리](access-tiers-overview.md#blob-lifecycle-management) 정책을 통해 [blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출 하 여 blob의 계층을 변경 합니다.
- Blob [복사](/rest/api/storageservices/copy-blob) 작업을 호출 하 여 한 계층에서 다른 계층으로 blob을 복사 합니다. 이 경우 원본 blob은 원본 계층에 유지 되 고 새 blob은 대상 계층에 생성 됩니다.

이러한 각 옵션에 대 한 자세한 내용은 [blob의 계층 설정 또는 변경](access-tiers-overview.md#setting-or-changing-a-blobs-tier)을 참조 하세요.

PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용 하 여 blob을 다른 계층으로 이동 합니다.

### <a name="change-a-blobs-tier"></a>Blob 계층 변경

Blob의 계층을 변경 하는 경우 해당 blob 및 모든 데이터를 대상 계층으로 이동 합니다. [Set Blob 계층](/rest/api/storageservices/set-blob-tier) 을 호출 하는 것은 일반적으로 blob 계층을 더울 계층에서 냉각기로 변경할 때 가장 적합 한 옵션입니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 blob의 계층을 핫에서 쿨로 변경 하려면 다음 단계를 수행 합니다.

1. 계층을 변경할 blob로 이동 합니다.
1. Blob을 선택한 다음 **계층 변경** 단추를 선택 합니다.
1. **계층 변경** 대화 상자에서 대상 계층을 선택 합니다.
1. **저장** 단추를 선택합니다.

    :::image type="content" source="media/access-tiers-online-manage/change-blob-tier-portal.png" alt-text="Azure Portal에서 blob의 계층을 변경 하는 방법을 보여 주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob의 계층을 핫에서 쿨로 변경하려면 **Blob의 BlobClient** 속성을 사용하여 Blob에 대한 .NET 참조를 반환한 다음 해당 참조에서 **SetAccessTier** 메서드를 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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

# Change the blob's access tier to Cool.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Cool", $null, "Standard")
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 Blob의 계층을 핫에서 쿨로 변경하려면 [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="copy-a-blob-to-a-different-online-tier"></a>Blob을 다른 온라인 계층에 복사

Blob을 다른 계층에 복사하는 경우 해당 Blob 및 모든 데이터를 대상 계층으로 이동합니다. Blob 복사 호출은 [Blob을](/rest/api/storageservices/copy-blob) 쿨에서 핫으로 이동하거나 보관 계층에서 Blob을 리하우전하는 대부분의 시나리오에서 권장됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

해당 없음

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob을 쿨에서 핫으로 복사하려면 [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) 명령을 호출하고 대상 계층을 지정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하여 쿨에서 핫으로 Blob을 복사하려면 [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) 명령을 호출하고 대상 계층을 지정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --auth-mode login
```

---

## <a name="next-steps"></a>다음 단계

- [Blob Storage 계정의 기본 계정 액세스 계층을 관리하는 방법](../common/manage-account-default-access-tier.md)
- [보관된 Blob을 온라인 계층으로 리하이드레이션](archive-rehydrate-to-online-tier.md)
