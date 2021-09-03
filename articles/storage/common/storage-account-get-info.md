---
title: 스토리지 계정 구성 정보 가져오기
titleSuffix: Azure Storage
description: Azure Portal, PowerShell 또는 Azure CLI를 사용하여 Azure Resource Manager 리소스 ID, 계정 위치, 계정 유형 또는 복제 SKU를 포함한 스토리지 계정 구성 속성을 검색합니다.
services: storage
author: tamram
ms.author: tamram
ms.date: 06/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: f3f94dda19f11b1a0aad9a84e7ae624e41c5015c
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573655"
---
# <a name="get-storage-account-configuration-information"></a>스토리지 계정 구성 정보 가져오기

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 Azure Storage 계정의 구성 정보 및 속성을 가져오는 방법을 보여 줍니다.

## <a name="get-the-resource-id-for-a-storage-account"></a>스토리지 계정의 리소스 ID 가져오기

모든 Azure Resource Manager 리소스에는 해당 리소스를 고유하게 식별하는 연결된 리소스 ID가 있습니다. 특정 작업을 수행하려면 리소스 ID를 제공해야 합니다. Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정의 리소스 ID를 가져올 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 스토리지 계정의 Azure Resource Manager 리소스 ID를 표시하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **개요** 페이지의 **기본 정보** 섹션에서 **JSON 보기** 링크를 선택합니다.
1. 스토리지 계정의 리소스 ID가 페이지 맨 위에 표시됩니다.

    :::image type="content" source="media/storage-account-get-info/resource-id-portal.png" alt-text="포털에서 스토리지 계정의 리소스 ID를 복사하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정의 Azure Resource Manager 리소스 ID를 반환하려면 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈을 설치했는지 확인합니다. 다음으로, [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출하여 스토리지 계정을 반환하고 해당 리소스 ID를 가져옵니다.

```azurepowershell
(Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정의 Azure Resource Manager 리소스 ID를 반환하려면 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 호출하고 리소스 ID를 쿼리합니다.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query id \
    --output tsv
```

---

REST API에서 [스토리지 계정 - 속성 가져오기](/rest/api/storagerp/storage-accounts/get-properties) 작업을 호출하여 스토리지 계정의 리소스 ID를 가져올 수도 있습니다.

Azure Resource Manager에서 관리하는 리소스 종류에 관한 자세한 내용은 [리소스 공급자 및 리소스 종류](../../azure-resource-manager/management/resource-providers-and-types.md)를 참조하세요.

## <a name="get-the-account-type-location-or-replication-sku-for-a-storage-account"></a>스토리지 계정의 계정 유형, 위치 또는 복제 SKU 가져오기

계정 유형, 위치 및 복제 SKU는 스토리지 계정에서 사용할 수 있는 속성 중 일부입니다. Azure Portal, PowerShell 또는 Azure CLI를 사용하여 해당 값을 볼 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 스토리지 계정의 계정 유형, 위치 또는 복제 SKU를 보려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **개요** 페이지의 **기본 정보** 섹션에서 이 속성을 찾습니다.

    :::image type="content" source="media/storage-account-get-info/account-configuration-portal.png" alt-text="포털의 계정 구성을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정의 계정 유형, 위치 또는 복제 SKU를 보려면 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출하여 스토리지 계정을 반환한 다음, 속성을 확인합니다.

```azurepowershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>
$account.Location
$account.Sku
$account.Kind
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell을 사용하여 스토리지 계정의 계정 유형, 위치 또는 복제 SKU를 보려면 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 호출하여 속성을 쿼리합니다.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query '[location,sku,kind]' \
    --output tsv
```

---

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](storage-account-overview.md)
- [스토리지 계정을 만드는](storage-account-create.md)
