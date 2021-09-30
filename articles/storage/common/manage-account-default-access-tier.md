---
title: Azure Storage 계정의 기본 액세스 계층 관리
titleSuffix: Azure Storage
description: 범용 v2 또는 Blob Storage 계정의 기본 액세스 계층을 변경하는 방법을 알아봅니다.
author: tamram
ms.author: tamram
ms.date: 09/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a9cb4a119447188202036cca4ed4d8580329d0cc
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272569"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Azure Storage 계정의 기본 액세스 계층 관리

각 Azure Storage 계정에는 핫 또는 쿨의 기본 액세스 계층이 있습니다. 스토리지 계정을 만들 때 액세스 계층을 할당합니다. 기본 액세스 계층은 핫입니다.

스토리지 계정에 **액세스 계층** 특성을 설정하여 기본 계정 계층을 변경할 수 있습니다. 계정 계층 변경은 명시적 계층 집합이 없는 계정에 저장된 모든 개체에 적용됩니다. 계정 계층을 핫에서 쿨로 전환하면 범용 v2 계정에만 설정된 계층이 없는 모든 Blob에 대한 쓰기 작업(10,000개당)이 발생하며 쿨에서 핫으로 전환하면 Blob Storage 및 범용 v2 계정의 모든 Blob에 대한 읽기 작업(10,000개당) 및 데이터 검색(GB당) 요금이 발생합니다.

개체 수준에서 계층이 설정된 Blob의 경우 계정 계층이 적용되지 않습니다. 보관 계층은 개체 수준에서만 적용할 수 있습니다. 언제든지 액세스 계층 간에 전환할 수 있습니다.

아래 단계에 따라 스토리지 계정을 만든 후에 기본 액세스 계층을 변경할 수 있습니다.

## <a name="change-the-default-account-access-tier-of-a-general-purpose-v2-or-blob-storage-account"></a>범용 v2 또는 Blob Storage 계정의 기본 계정 액세스 계층 변경

다음 시나리오에서는 Azure Portal 또는 PowerShell을 사용합니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **모든 리소스** 를 검색하여 선택합니다.

1. 사용자의 스토리지 계정을 선택합니다.

1. **설정** 에서 **구성** 을 선택하여 계정 구성을 보고 변경합니다.

1. 요구 사항에 적합한 액세스 계층 선택: **액세스 계층** 을 **쿨** 또는 **핫** 으로 설정합니다.

1. 위쪽에서 **저장** 을 클릭합니다.

![Azure Portal에서 기본 계정 계층 변경](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell 스크립트를 사용하여 계정 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다.

```powershell
# Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

# Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

다음 Azure CLI 스크립트를 사용하여 계정 계층을 변경할 수 있습니다. `$rgName` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다.

```azurecli
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
az storage account update --resource-group $rgName --name $accountName --access-tier Hot
```

---

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정에서 Blob 계층을 관리하는 방법](../blobs/manage-access-tier.md)
- [프리미엄 성능이 앱에 이익이 되는지 확인](../blobs/storage-blob-performance-tiers.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../blobs/monitor-blob-storage.md)
