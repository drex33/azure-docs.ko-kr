---
title: 수명 주기 관리 정책 구성
titleSuffix: Azure Storage
description: 데이터 수명 주기 동안 핫, 쿨, 보관 계층 간에 데이터를 자동으로 이동하도록 수명 주기 관리 정책을 구성합니다.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 507077913a672da6f9572a283367c5713a9d5e39
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603508"
---
# <a name="configure-a-lifecycle-management-policy"></a>수명 주기 관리 정책 구성

Azure Storage 수명 주기 관리는 Blob 데이터를 적절한 액세스 계층으로 전환하거나 수명 주기가 끝나면 데이터를 만료하는 데 사용할 수 있는 규칙 기반 정책을 제공합니다. 수명 주기 정책은 기본 Blob, 필요에 따라 Blob 버전이나 스냅샷에 적용됩니다. 수명 주기 관리 정책에 관한 자세한 내용은 [데이터 수명 주기를 자동으로 관리하여 비용 최적화](lifecycle-management-overview.md)를 참조하세요.

수명 주기 관리 정책은 충족되는 조건에 따라 수행할 작업 세트를 정의하는 하나 이상의 규칙으로 구성됩니다. 기본 Blob의 경우 다음 두 조건 중 하나를 확인하도록 선택할 수 있습니다.

- Blob을 마지막으로 수정한 후 경과된 기간(일)입니다.
- Blob에 마지막으로 액세스한 후 경과된 기간(일)입니다. 작업에서 이 조건을 사용하려면 먼저 [필요에 따라 액세스 시간 추적을 사용하도록 설정](#optionally-enable-access-time-tracking)해야 합니다.

선택한 조건이 true이면 관리 정책은 지정된 작업을 수행합니다. 예를 들어, 30일 동안 수정되지 않은 경우 핫 계층에서 쿨 계층으로 Blob을 이동하도록 작업을 정의했다면 수명 주기 관리 정책은 해당 Blob에 대한 마지막 쓰기 작업 후 30일이 지나면 Blob을 이동합니다.

Blob 스냅샷 또는 버전의 경우 확인된 조건은 스냅샷이나 버전이 만들어진 후 경과된 기간(일)입니다.

## <a name="optionally-enable-access-time-tracking"></a>필요에 따라 액세스 시간 추적 사용

수명 주기 관리 정책을 구성하기 전에 Blob 액세스 시간 추적을 사용하도록 선택할 수 있습니다. 액세스 시간 추적이 사용되는 경우 수명 주기 관리 정책에는 읽기 또는 쓰기 작업을 통해 Blob에 마지막으로 액세스한 시간에 따른 작업이 포함될 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 마지막 액세스 시간 추적을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **데이터 관리** 섹션에서 **액세스 추적 사용** 을 선택합니다.

    :::image type="content" source="media/lifecycle-management-policy-configure/last-access-tracking-enable.png" alt-text="Azure Portal에서 마지막 액세스 추적을 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 마지막 액세스 시간 추적을 사용하도록 설정하려면 다음 예제와 같이 [Enable-AzStorageBlobLastAccessTimeTracking](/powershell/module/az.storage/enable-azstoragebloblastaccesstimetracking) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Enable-AzStorageBlobLastAccessTimeTracking  -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -PassThru
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 마지막 액세스 시간 추적을 사용하도록 설정하려면 다음 예제와 같이 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --enable-last-access-tracking true
```

# <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용하여 신규 또는 기존 스토리지 계정의 마지막 액세스 시간 추적을 사용하도록 설정하려면 템플릿 정의에 **lastAccessTimeTrackingPolicy** 개체를 포함합니다. 자세한 내용은 [Microsoft.Storage/storageAccounts/blobServices 2021-02-01 - Bicep 및 ARM 템플릿 참조](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/blobservices?tabs=json)를 참조하세요. **lastAccessTimeTrackingPolicy** 개체는 버전 2019-06-01 이상의 Azure Storage Resource Provider REST API에서 사용할 수 있습니다.

---

**daysAfterLastAccessTimeGreaterThan** 속성을 사용하여 Blob에서 수행되어야 하는 작업 이후 마지막 액세스부터 경과된 기간(일)을 지정합니다.

## <a name="create-or-manage-a-policy"></a>정책 만들기 또는 관리

Azure Portal, PowerShell, Azure CLI 또는 Azure Resource Manager 템플릿을 사용하여 수명 주기 관리 정책을 추가, 편집 또는 제거할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 통해 정책을 추가하는 방법에는 두 가지가 있습니다.

- [목록 보기](#list-view)
- [코드 보기](#code-view)

#### <a name="list-view"></a>목록 뷰

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **데이터 관리** 에서 **수명 주기 관리** 를 선택하여 수명 주기 관리 정책을 보거나 변경합니다.
1. **목록 보기** 탭을 선택합니다.

1. **규칙 추가** 를 선택하고 **세부 정보** 양식에서 규칙의 이름을 지정합니다. **규칙 범위**, **Blob 유형** 및 **Blob 하위 유형** 값을 설정할 수도 있습니다. 다음 예에서는 Blob을 필터링할 범위를 설정합니다. 이렇게 하면 **필터 집합** 탭이 추가됩니다.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-details.png" alt-text="Azure Portal의 수명 주기 관리 규칙 추가 세부 정보 페이지":::

1. **기본 Blob** 을 선택하여 규칙에 대한 조건을 설정합니다. 다음 예에서는 30일 동안 수정되지 않은 Blob이 쿨 스토리지로 이동됩니다.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-base-blobs.png" alt-text="Azure Portal의 수명 주기 관리 기본 Blob 페이지":::

   **마지막으로 액세스한 날짜** 옵션은 액세스 시간 추적을 사용하도록 설정한 경우에만 사용할 수 있습니다. 액세스 추적을 사용하도록 설정하는 방법을 알아보려면 [필요에 따라 액세스 시간 추적 사용](#optionally-enable-access-time-tracking)을 참조하세요.

1. **세부 정보** 페이지에서  **필터를 사용하여 Blob 제한** 을 선택한 경우 **필터 집합** 을 선택하여 필터(선택 사항)를 추가합니다. 다음 예제에서는 *sample-container* 라는 컨테이너에서 이름이 *log* 로 시작하는 Blob을 필터링합니다.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-filter-set.png" alt-text="Azure Portal의 수명 주기 관리 필터 집합 페이지":::

1. **추가** 를 선택하여 새 정책을 추가합니다.

#### <a name="code-view"></a>코드 보기

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **데이터 관리** 에서 **수명 주기 관리** 를 선택하여 수명 주기 관리 정책을 보거나 변경합니다.
1. **코드 보기** 탭을 선택합니다. 이 탭에서 JSON으로 수명 주기 관리 정책을 정의할 수 있습니다.

다음 샘플 JSON은 Blob이 수정된 후 경과된 기간이 30일을 초과한 경우 이름이 *log* 로 시작하는 블록 Blob을 쿨 계층으로 이동하는 수명 주기 정책을 정의합니다.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "sample-container/log"
             ]
           }
         }
       }
     ]
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 수명 주기 관리 정책을 추가하려면 다음 명령을 사용합니다.

- [Add-AzStorageAccountManagementPolicyAction](/powershell/module/az.storage/add-azstorageaccountmanagementpolicyaction) 명령을 호출하여 규칙을 구성하는 작업을 정의합니다.
- [New-AzStorageAccountManagementPolicyFilter](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyfilter) 명령을 호출하여 규칙에 대해 하나 이상의 필터를 지정합니다.
- [New-AzStorageAccountManagementPolicyRule](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyrule) 명령을 호출하여 작업과 선택적 필터를 포함하는 정책 규칙을 만듭니다.
- [Set-AzStorageAccountManagementPolicy](/powershell/module/az.storage/set-azstorageaccountmanagementpolicy) 명령을 호출하여 스토리지 계정에 대한 정책을 만듭니다.

다음 예제에서는 이러한 각 명령을 사용하여 수명 주기 정책을 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Initialize the following variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Create a new action object.
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete `
    -daysAfterModificationGreaterThan 180
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToArchive `
    -daysAfterModificationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToCool `
    -daysAfterModificationGreaterThan 30
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -SnapshotAction Delete `
    -daysAfterCreationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BlobVersionAction TierToArchive `
    -daysAfterCreationGreaterThan 90

# Create a new filter object.
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd `
    -BlobType blockBlob

# Create a new rule object.
$rule1 = New-AzStorageAccountManagementPolicyRule -Name sample-rule `
    -Action $action `
    -Filter $filter

# Create the policy.
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Rule $rule1
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 수명 주기 관리 정책을 추가하려면 JSON 파일에 정책을 쓴 다음, [az storage account management-policy create](/cli/azure/storage/account/management-policy#az_storage_account_management_policy_create) 명령을 호출하여 정책을 만듭니다.

다음 예제에서는 이러한 각 명령을 사용하여 수명 주기 정책을 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage account management-policy create \
    --account-name <storage-account> \
    --policy @policy.json \
    --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용하여 수명 주기 관리 정책을 정의하려면 템플릿에 **Microsoft.Storage/storageAccounts/managementPolicies** 개체를 포함합니다. 구성에 관한 자세한 내용은 [Microsoft.Storage/storageAccounts/managementPolicies 2021-02-01 - Bicep 및 ARM 템플릿 참조](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/managementpolicies?tabs=json)를 참조하세요. **Microsoft.Storage/storageAccounts/managementPolicies** 개체는 버전 2018-11-01 이상의 Azure Storage 리소스 공급자 REST API에서 사용할 수 있습니다.

---

수명 주기 관리 정책은 전체적으로 읽거나 써야 합니다. 부분 업데이트는 지원되지 않습니다.

> [!NOTE]
> 스토리지 계정에 방화벽 규칙을 사용하도록 설정하면 수명 주기 관리 요청이 차단될 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대한 예외를 제공하여 이러한 요청의 차단을 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](../common/storage-network-security.md#exceptions)의 **예외** 섹션을 참조하세요.

## <a name="see-also"></a>참고 항목

- [데이터 수명 주기를 자동으로 관리하여 비용 최적화](lifecycle-management-overview.md)
- [Azure Blob Storage의 액세스 계층 - 핫, 쿨 및 보관](storage-blob-storage-tiers.md)
