---
title: 개체 복제 구성
titleSuffix: Azure Storage
description: 한 스토리지 계정의 컨테이너에 있는 블록 blob을 다른 스토리지 계정과 비동기식으로 복사하도록 개체 복제를 구성하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 7f0c0928a32807844d57eb91a903994a0264e460
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534388"
---
# <a name="configure-object-replication-for-block-blobs"></a>블록 Blob에 대한 개체 복제 구성

개체 복제는 원본 스토리지 계정과 대상 계정 간에 블록 Blob을 비동기적으로 복사합니다. 개체 복제를 구성하는 경우 원본 스토리지 계정 및 대상 계정을 지정하는 복제 정책을 만듭니다. 복제 정책에는 원본 컨테이너와 대상 컨테이너를 지정하고 원본 컨테이너에서 복제할 블록 Blob을 나타내는 하나 이상의 규칙이 포함되어 있습니다. 개체 복제에 대 한 자세한 내용은 [블록 blob에 대 한 개체 복제](object-replication-overview.md)를 참조 하세요.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 개체 복제 정책을 구성 하는 방법을 설명 합니다. Azure Storage 리소스 공급자 클라이언트 라이브러리 중 하나를 사용하여 개체 복제를 구성할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

개체 복제를 구성하기 전에 원본 및 대상 스토리지 계정이 아직 없는 경우 만듭니다. 원본 및 대상 계정은 범용 v2 저장소 계정 또는 프리미엄 블록 blob 계정 (미리 보기) 중 하나일 수 있습니다. 자세한 내용은 [Azure Storage 계정 만들기](../common/storage-account-create.md)를 참조하세요.

개체를 복제하려면 Blob 버전 관리를 원본 및 대상 계정 모두에 사용하도록 설정하고, Blob 변경 피드를 원본 계정에 사용하도록 설정해야 합니다. Blob 버전 관리에 대한 자세한 정보는 [Blob 버전 관리](versioning-overview.md)를 참조하세요. 변경 피드에 대한 자세한 내용은 [Azure Blob Storage의 변경 피드 지원](storage-blob-change-feed.md)을 참조하세요. 이러한 기능을 사용하도록 설정하면 추가 비용이 발생할 수 있습니다.

스토리지 계정에 대한 개체 복제 정책을 구성하려면 스토리지 계정 수준 이상으로 범위가 지정된 Azure Resource Manager **기여자** 역할을 할당받아야 합니다. 자세한 내용은 Azure RBAC(역할 기반 액세스 제어) 설명서의 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

> [!IMPORTANT]
> 프리미엄 블록 blob 계정에 대 한 개체 복제는 현재 **미리 보기** 상태입니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="configure-object-replication-with-access-to-both-storage-accounts"></a>두 저장소 계정에 대 한 액세스를 사용 하 여 개체 복제 구성

원본 및 대상 스토리지 계정 모두에 액세스할 수 있는 경우 두 계정 모두에서 개체 복제 정책을 구성할 수 있습니다. 다음 예에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 개체 복제를 구성 하는 방법을 보여 줍니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 개체 복제를 구성 하는 경우 원본 계정에 대해서만 정책을 구성 하면 됩니다. Azure Portal은 원본 계정에 대해 구성한 후 대상 계정에 정책을 자동으로 만듭니다.

Azure Portal에서 복제 정책을 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 원본 스토리지 계정으로 이동합니다.
1. **데이터 관리** 에서 **개체 복제** 를 선택 합니다.
1. **복제 규칙 설정** 을 선택합니다.
1. 대상 구독 및 스토리지 계정을 선택합니다.
1. **컨테이너 쌍** 섹션에서 원본 계정에서 원본 컨테이너를 선택하고 대상 계정에서 대상 컨테이너를 선택합니다. 복제 정책에 따라 최대 10개의 컨테이너 쌍을 만들 수 있습니다.

    다음 이미지는 일련의 복제 규칙을 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Azure Portal의 복제 규칙을 보여 주는 스크린샷":::

1. 원할 경우 접두사 패턴과 일치하는 blob만 복사하도록 하나 이상의 필터를 지정합니다. 예를 들어, `b` 접두사를 지정하면 해당 문자로 시작하는 이름의 blob만 복제됩니다. 가상 디렉터리를 접두사의 일부로 지정할 수 있습니다. 접두사 문자열은 와일드카드 문자를 지원하지 않습니다.

    다음 이미지는 복제 규칙의 일부로 복사할 blob을 제한하는 필터를 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="복제 규칙에 대한 필터를 보여 주는 스크린샷":::

1. 기본적으로 복사 범위는 새 개체만 복사하도록 설정됩니다. 컨테이너의 모든 개체를 복사하거나 사용자 지정 날짜 및 시간에 시작된 개체를 복사하려면 **변경** 링크를 선택하고 컨테이너 쌍의 복사 범위를 구성합니다.

    다음 이미지에서는 개체를 지정된 날짜 및 시간 이후부터 복사하는 사용자 지정 복사 범위를 보여 줍니다.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="개체 복제에 대한 사용자 지정 복사 범위를 보여 주는 스크린샷":::

1. **저장 및 적용** 을 선택하여 복제 정책을 만들고 데이터 복제를 시작합니다.

개체 복제가 구성되면 다음 이미지와 같이 Azure Portal에 복제 정책 및 규칙이 표시됩니다.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Azure Portal의 개체 복제 정책을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 복제 정책을 만들려면 먼저 Az.Storage PowerShell 모듈 버전 [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) 이상을 설치합니다. Azure PowerShell을 설치하는 방법에 대한 자세한 내용은 [PowerShellGet을 사용하여 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

다음 예에서는 대상 계정에 먼저 복제 정책을 만든 다음 원본 계정에 복제 정책을 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2021-09-01T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 복제 정책을 만들려면 먼저 Azure CLI 버전 2.11.1 이상을 설치합니다. 자세한 내용은 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

다음으로, [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 명령을 호출하여 원본 및 대상 스토리지 계정에서 Blob 버전 관리를 사용하도록 설정하고, 원본 계정에서 변경 피드를 사용하도록 설정합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

각 스토리지 계정에 원본 및 대상 컨테이너를 만듭니다.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-2 \
    --auth-mode login
```

[az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create)를 호출하여 대상 계정에 대한 새 복제 정책 및 관련 규칙을 만듭니다.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2021-09-01T00:00:00Z' \
    --prefix-match a

```

새 정책이 만들어지면 Azure Storage에서 이 새 정책에 대한 정책 ID를 설정합니다. 규칙을 정책에 추가하려면 [az storage account or-policy rule add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add)를 호출하고 정책 ID를 제공합니다.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

다음으로, 정책 ID를 사용하여 원본 계정에 대한 정책을 만듭니다.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

## <a name="configure-object-replication-with-access-to-only-the-destination-account"></a>대상 계정에 대 한 액세스 권한으로 개체 복제 구성

원본 스토리지 계정에 대한 권한이 없는 경우 대상 계정에서 개체 복제를 구성하고, 정책 정의가 포함된 JSON 파일을 다른 사용자에게 제공하여 동일한 정책을 원본 계정에 만들 수 있습니다. 예를 들어 원본 계정이 대상 계정과 다른 Azure AD 테넌트에 있는 경우 이 방법을 사용하여 개체 복제를 구성할 수 있습니다.

> [!NOTE]
> 저장소 계정에 대 한 테 넌 트 간 개체 복제는 기본적으로 허용 됩니다. 테 넌 트 간 복제를 방지 하기 위해 저장소 계정에 대 한 테 넌 트 간 개체 복제를 허용 하지 않도록 **AllowCrossTenantReplication** 속성 (미리 보기)을 설정할 수 있습니다. 자세한 내용은 [Azure Active Directory 테 넌 트 간 개체 복제 방지](object-replication-prevent-cross-tenant-policies.md)를 참조 하세요.

이 섹션의 예제에서는 대상 계정에 대 한 개체 복제 정책을 구성 하 고 다른 사용자가 원본 계정에서 정책을 구성 하는 데 사용할 수 있는 해당 정책에 대 한 JSON 파일을 가져오는 방법을 보여 줍니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 JSON 파일을 사용하여 대상 계정에 대한 개체 복제를 구성하려면 다음 단계를 수행합니다.

1. 대상 계정에 대한 복제 정책을 정의하는 로컬 JSON 파일을 만듭니다. Azure Storage에서 정책 ID를 정의하도록 **policyId** 필드를 *default* 로 설정합니다.

    복제 정책을 정의하는 JSON 파일을 쉽게 만드는 방법은 먼저 Azure Portal에서 두 스토리지 계정 간의 테스트 복제 정책을 만드는 것입니다. 그런 다음, 복제 규칙을 다운로드하고, 필요에 따라 JSON 파일을 수정할 수 있습니다.

1. Azure Portal에서 대상 계정에 대한 **개체 복제** 설정으로 이동합니다.
1. **복제 규칙 업로드** 를 선택합니다.
1. JSON 파일을 업로드합니다. 다음 이미지와 같이 Azure Portal에 만드는 정책과 규칙이 표시됩니다.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="복제 정책을 정의할 JSON 파일을 업로드하는 방법을 보여 주는 스크린샷":::

1. **업로드** 를 선택하여 대상 계정에 대한 복제 정책을 만듭니다.

그런 다음, 원본 계정을 구성하기 위해 다른 사용자에게 제공할 수 있는 정책 정의가 포함된 JSON 파일을 다운로드할 수 있습니다. 이 JSON 파일을 다운로드하려면 다음 단계를 수행합니다.

1. Azure Portal에서 대상 계정에 대한 **개체 복제** 설정으로 이동합니다.
1. 다음 이미지와 같이 다운로드할 정책 옆에 있는 **자세히** 단추를 선택하고, **규칙 다운로드** 를 선택합니다.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="복제 규칙을 JSON 파일로 다운로드하는 방법을 보여 주는 스크린샷":::

1. 다른 사용자와 공유하여 원본 계정에 대한 정책을 구성할 수 있도록 JSON 파일을 로컬 컴퓨터에 저장합니다.

다운로드한 JSON 파일에는 Azure Storage에서 대상 계정에 대한 정책에 대해 만든 정책 ID가 포함되어 있습니다. 원본 계정에서 개체 복제를 구성하려면 동일한 정책 ID를 사용해야 합니다.

Azure Portal을 통해 대상 계정에 대한 복제 정책을 만들기 위해 JSON 파일을 업로드하면 동일한 정책이 원본 계정에 자동으로 만들어지지 않습니다. Azure Storage에서 개체 복제를 시작하기 전에 다른 사용자가 원본 계정에 대한 정책을 만들어야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 대상 계정에 대한 복제 정책 정의가 포함된 JSON 파일을 다운로드하려면 [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) 명령을 호출하여 정책을 반환합니다. 그런 다음, 다음 예제와 같이 정책을 JSON으로 변환하고, 로컬 파일로 저장합니다. 꺾쇠 괄호로 묶인 값 및 파일 경로를 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

JSON 파일을 사용하여 PowerShell에서 원본 계정에 대한 복제 정책을 정의하려면 로컬 파일을 검색하고 JSON에서 개체로 변환합니다. 그런 다음, 다음 예제와 같이 [Set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) 명령을 호출하여 원본 계정에 대한 정책을 구성합니다.

예제를 실행 하는 경우 `-ResourceGroupName` 매개 변수를 원본 계정에 대 한 리소스 그룹으로 설정 하 고 `-StorageAccountName` 매개 변수를 원본 계정 이름으로 설정 해야 합니다. 또한 대괄호와 파일 경로의 값을 고유한 값으로 바꿔야 합니다.

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 대상 계정에 대한 복제 정책 정의를 JSON 파일에 쓰려면 [az storage account or-policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) 명령을 호출하여 파일에 출력합니다.

다음 예제에서는 정책 정의를 *policy.json* 이라는 JSON 파일에 씁니다. 꺾쇠 괄호로 묶인 값 및 파일 경로를 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Azure CLI에서 JSON 파일을 사용하여 원본 계정에 대한 복제 정책을 구성하려면 [az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) 명령을 호출하여 *policy.json* 파일을 참조합니다. 꺾쇠 괄호로 묶인 값 및 파일 경로를 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Blob의 복제 상태 확인

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 원본 계정의 Blob에 대한 복제 상태를 확인할 수 있습니다. 복제가 완료되거나 실패할 때까지 개체 복제 속성이 채워지지 않습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 원본 계정의 Blob에 대한 복제 상태를 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에서 원본 계정으로 이동합니다.
1. 원본 Blob이 포함된 컨테이너를 찾습니다.
1. Blob을 선택하여 해당 속성을 표시합니다. Blob이 성공적으로 복제되면 **개체 복제** 섹션에서 상태가 *완료* 로 설정되어 있음을 확인할 수 있습니다. 복제 정책 ID 및 이 컨테이너에 대한 개체 복제를 제어하는 규칙에 대한 ID도 나열됩니다.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="원본 계정의 Blob에 대한 복제 상태를 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 원본 계정의 Blob에 대한 복제 상태를 확인하려면 다음 예제와 같이 개체 복제 **ReplicationStatus** 속성의 값을 가져옵니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 원본 계정의 Blob에 대한 복제 상태를 확인하려면 다음 예제와 같이 개체 복제 **status** 속성 값을 가져옵니다.

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

원본 계정의 Blob에 대한 복제 상태가 실패로 표시되는 경우 다음과 같은 가능한 원인을 조사합니다.

- 개체 복제 정책이 대상 계정에 구성되어 있는지 확인합니다.
- 대상 컨테이너가 여전히 있는지 확인합니다.
- 원본 Blob이 쓰기 작업의 일부로 고객이 제공한 키로 암호화된 경우 개체 복제가 실패합니다. 고객이 제공한 키에 대한 자세한 내용은 [Blob 스토리지 요청 시 암호화 키 제공](encryption-customer-provided-keys.md)을 참조하세요.

## <a name="remove-a-replication-policy"></a>복제 정책 제거

복제 정책 및 관련 규칙을 제거하려면 Azure Portal, PowerShell 또는 CLI를 사용합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 복제 정책을 제거하려면 다음 단계를 수행합니다.

1. Azure Portal의 원본 스토리지 계정으로 이동합니다.
1. **설정** 에서 **개체 복제** 를 선택합니다.
1. 정책 이름 옆에 있는 **더 보기** 단추를 클릭합니다.
1. **규칙 삭제** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

복제 정책을 제거하려면 원본 계정 및 대상 계정 둘 다에서 정책을 삭제합니다. 또한 정책을 삭제하면 연결된 규칙도 모두 삭제됩니다.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

복제 정책을 제거하려면 원본 계정 및 대상 계정 둘 다에서 정책을 삭제합니다. 또한 정책을 삭제하면 연결된 규칙도 모두 삭제됩니다.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>다음 단계

- [블록 Blob에 대한 개체 복제](object-replication-overview.md)
- [Azure Active Directory 테넌트에서 개체 복제 방지](object-replication-prevent-cross-tenant-policies.md)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)
- [Azure Blob Storage에서 변경 피드 처리](storage-blob-change-feed-how-to.md)
