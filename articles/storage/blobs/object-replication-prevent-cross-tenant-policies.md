---
title: Azure Active Directory 테넌트에서 개체 복제 방지(미리 보기)
titleSuffix: Azure Storage
description: 교차 테넌트 개체 복제 방지
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a44ac9aa7e7f8a924621345c3b7316012eeddae1
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452762"
---
# <a name="prevent-object-replication-across-azure-active-directory-tenants-preview"></a>Azure Active Directory 테넌트에서 개체 복제 방지(미리 보기)

개체 복제는 한 스토리지 계정의 컨테이너에서 다른 스토리지 계정의 컨테이너로 블록 Blob을 비동기적으로 복사합니다. 개체 복제 정책을 구성할 때 원본 계정 및 컨테이너와 대상 계정 및 컨테이너를 지정합니다. 정책이 구성되면 Azure Storage 원본 개체에 대한 만들기, 업데이트 및 삭제 작업의 결과를 대상 개체에 자동으로 복사합니다. Azure Storage 개체 복제에 대한 자세한 내용은 [블록 Blob에 대한 개체 복제를 참조하세요.](object-replication-overview.md)

기본적으로 권한 있는 사용자는 원본 계정이 Azure AD(한 Azure Active Directory) 테넌트 안에 있고 대상 계정이 다른 테넌트인 개체 복제 정책을 구성할 수 있습니다. 보안 정책에서 개체 복제를 동일한 테넌트 내에만 있는 스토리지 계정으로 제한해야 하는 경우 원본 및 대상 계정이 서로 다른 테넌트(미리 보기)에 있는 정책 생성을 거부할 수 있습니다. 기본적으로 스토리지 계정에 대해 명시적으로 허용되지 않는 한 테넌트 간 개체 복제가 사용됩니다.

이 문서에서는 스토리지 계정에 대한 테넌트 간 개체 복제를 수정하는 방법을 설명합니다. 또한 새 스토리지 계정과 기존 스토리지 계정에 대해 테넌트 간 개체 복제를 강제로 적용하는 정책을 만드는 방법에 대해서도 설명합니다.

테넌트 간 정책을 포함하여 개체 복제 정책을 구성하는 방법에 대한 자세한 내용은 [블록 Blob에 대한 개체 복제 구성을 참조하세요.](object-replication-configure.md)

## <a name="remediate-cross-tenant-object-replication"></a>교차 테넌트 개체 복제 수정

Azure AD 테넌트 간에 개체 복제를 방지하려면 스토리지 계정에 대한 **AllowCrossTenantReplication** 속성을 **false** 로 설정합니다. 스토리지 계정이 현재 테넌트 간 개체 복제 정책에 참여하지 않는 경우 **AllowCrossTenantReplication** 속성을 *false로* 설정하면 이 스토리지 계정을 원본 또는 대상으로 사용하여 테넌트 간 개체 복제 정책을 나중에 구성할 수 없습니다. 그러나 스토리지 계정이 현재 하나 이상의 교차 테넌트 개체 복제 정책에 참여하는 경우 기존 테넌트 간 정책을 삭제할 때까지 **AllowCrossTenantReplication** 속성을 *false로* 설정하는 것은 허용되지 않습니다.

테넌트 간 정책은 스토리지 계정에 대해 기본적으로 허용됩니다. 그러나 **AllowCrossTenantReplication** 속성은 새 스토리지 계정 또는 기존 스토리지 계정에 대해 기본적으로 설정되지 않으며 명시적으로 설정할 때까지 값을 반환하지 않습니다. 속성 값이 **null** 또는 true인 경우 스토리지 계정은 테넌트 전체에서 개체 복제 정책에 참여할 수 **있습니다.**

### <a name="remediate-cross-tenant-replication-for-a-new-account"></a>새 계정에 대한 테넌트 간 복제 수정

새 스토리지 계정에 대해 테넌트 간 복제를 사용하지 못하게 하려면 Azure Portal, PowerShell 또는 Azure CLI 사용합니다.

#### <a name="portal"></a>[포털](#tab/portal)

새 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 경우 다음 단계를 수행합니다.

1. Azure Portal Storage 계정 페이지로 이동하고 **만들기를** 선택합니다. 
1. 새 스토리지 계정에 대한 **기본 사항** 탭을 작성합니다.
1. **고급** 탭의 **Blob Storage** 섹션에서 **테넌트 간 복제 허용** 설정을 찾아서 확인란의 선택을 취소합니다.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-create-account.png" alt-text="새 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 방법을 보여주는 스크린샷":::

1. 계정을 만드는 프로세스를 완료합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

새 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 경우 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 호출하고 `AllowCrossTenantReplication` 매개 변수를 *$false* 값으로 포함합니다.

```azurepowershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account and disallow cross-tenant replication.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_LRS
    -AllowCrossTenantReplication $false

# Read the property for the new storage account
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowCrossTenantReplication
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

새 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 경우 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 호출하고 `allow-cross-tenant-replication` *false* 값으로 매개 변수를 포함합니다.

```azurecli
# Create a storage account with cross-tenant replication disallowed.
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS 
    --allow-cross-tenant-replication false

# Read the property for the new storage account
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowCrossTenantReplication \
    --output tsv 
```

---

### <a name="remediate-cross-tenant-replication-for-an-existing-account"></a>기존 계정에 대한 테넌트 간 복제 수정

기존 스토리지 계정에 대해 테넌트 간 복제를 사용하지 못하게 하려면 Azure Portal, PowerShell 또는 Azure CLI 사용합니다.

#### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

현재 테넌트 간 정책에 참여하지 않는 기존 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않도록 하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **데이터 관리에서** **개체 복제를** 선택합니다.
1. **고급 설정** 을 선택합니다.
1. **테넌트 간 복제 허용을 선택 취소합니다.** 스토리지 계정에 대해 명시적으로 허용하지 않는 한 테넌트 간 개체 복제가 허용되므로 기본적으로 이 확인란이 선택되어 있습니다.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-update-account.png" alt-text="기존 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 방법을 보여주는 스크린샷":::

1. **확인** 을 선택하여 변경 내용을 저장합니다.

스토리지 계정이 현재 하나 이상의 테넌트 간 복제 정책에 참여하는 경우 해당 정책을 삭제할 때까지 테넌트 간 개체 복제를 허용되지 않습니다. 이 시나리오에서는 다음 이미지와 같이 Azure Portal 설정을 사용할 수 없습니다.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/cross-tenant-object-replication-policies-in-effect-portal.png" alt-text="스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

현재 테넌트 간 정책에 참여하지 않는 기존 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않도록 하려면 먼저 [Az.Storage PowerShell 모듈](https://www.powershellgallery.com/packages/Az.Storage) \, 버전 3.7.0 이상 버전을 설치합니다. 다음으로 스토리지 계정에 **대해 AllowCrossTenantReplication** 속성을 구성합니다.

다음 예제에서는 PowerShell을 사용하여 기존 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 방법을 보여 있습니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowCrossTenantReplication $false
```

스토리지 계정이 현재 하나 이상의 테넌트 간 복제 정책에 참여하는 경우 해당 정책을 삭제할 때까지 테넌트 간 개체 복제를 허용되지 않습니다. PowerShell은 기존 테넌트 간 복제 정책으로 인해 작업이 실패했음을 나타내는 오류를 제공합니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

현재 테넌트 간 정책에 참여하지 않는 기존 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않도록 하려면 먼저 버전 2.24.0 이상에 Azure CLI 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 다음으로 새 스토리지 계정 또는 기존 스토리지 계정에 대해 **allowCrossTenantReplication** 속성을 구성합니다.

다음 예제에서는 Azure CLI 사용하여 기존 스토리지 계정에 대해 테넌트 간 개체 복제를 허용되지 않는 방법을 보여 있습니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-cross-tenant-replication false
```

스토리지 계정이 현재 하나 이상의 테넌트 간 복제 정책에 참여하는 경우 해당 정책을 삭제할 때까지 테넌트 간 개체 복제를 허용되지 않습니다. Azure CLI 기존 테넌트 간 복제 정책으로 인해 작업이 실패했음을 나타내는 오류를 제공합니다.

---

테넌트 간 복제를 허용되지 않으면 스토리지 계정을 원본 또는 대상으로 사용하여 테넌트 간 정책을 구성하려고 하면 실패합니다. Azure Storage 스토리지 계정에 대해 테넌트 간 개체 복제가 허용되지 않음을 나타내는 오류를 반환합니다.

스토리지 계정에 대해 테넌트 간 개체 복제가 허용되지 않는 경우 해당 계정으로 만든 새 개체 복제 정책에 원본 및 대상 계정에 대한 전체 Azure Resource Manager ID가 포함되어야 합니다. Azure Storage 원본 및 대상 계정이 동일한 테넌트 내에 있는지 확인하기 위해 전체 리소스 ID가 필요합니다. 자세한 내용은 [원본 및 대상 계정에 대한 전체 리소스 ID 지정을 참조하세요.](object-replication-overview.md#specify-full-resource-ids-for-the-source-and-destination-accounts)

**AllowCrossTenantReplication** 속성은 Azure Resource Manager 배포 모델만 사용하는 스토리지 계정에 대해 지원됩니다. 어떤 스토리지 계정이 Azure Resource Manager 배포 모델을 사용하는지에 대한 자세한 내용은 [스토리지 계정 유형](../common/storage-account-overview.md#types-of-storage-accounts)을 참조하세요.

## <a name="permissions-for-allowing-or-disallowing-cross-tenant-replication"></a>테넌트 간 복제를 허용하거나 허용하지 않는 권한

스토리지 계정에 **대해 AllowCrossTenantReplication** 속성을 설정하려면 사용자에게 스토리지 계정을 만들고 관리할 수 있는 권한이 있어야 합니다. 이러한 권한을 제공하는 Azure RBAC(Azure 역할 기반 액세스 제어) 역할에는 **Microsoft.Storage/storageAccounts/write** 또는 **Microsoft.Storage/storageAccounts/\*** 작업이 포함됩니다. 이 작업이 포함된 기본 제공 역할은 다음과 같습니다.

- Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할
- Azure Resource Manager [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할
- [스토리지 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 역할

이러한 역할은 Azure AD(Azure Active Directory)를 통해 스토리지 계정의 데이터에 대한 액세스 권한을 제공하지 않습니다. 그러나 계정 액세스 키에 대한 액세스 권한을 부여하는 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함되어 있습니다. 이 권한이 있는 사용자는 계정 액세스 키를 사용하여 스토리지 계정의 모든 데이터에 액세스할 수 있습니다.

사용자가 스토리지 계정에 대한 테넌트 간 개체 복제를 허용하거나 허용하지 않도록 하려면 역할 할당의 범위를 스토리지 계정 수준 이상으로 지정해야 합니다. 역할 범위에 대한 자세한 내용은 [Azure RBAC의 범위 이해](../../role-based-access-control/scope-overview.md)를 참조하세요.

이러한 역할은 스토리지 계정을 만들거나 해당 속성을 업데이트하는 기능이 필요한 사용자에게만 제한적으로 할당해야 합니다. 최소 권한의 원칙을 사용하여 사용자에게 작업을 수행하는 데 필요한 최소 권한을 부여합니다. Azure RBAC를 사용하여 액세스를 관리하는 방법에 대한 자세한 내용은 [Azure RBAC 모범 사례](../../role-based-access-control/best-practices.md)를 참조하세요.

> [!NOTE]
> 클래식 구독 관리자 역할인 서비스 관리자 및 공동 관리자에는 Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할에 해당하는 항목이 포함됩니다. **소유자** 역할은 모든 작업을 포함하므로 이러한 관리 역할 중 하나가 있는 사용자는 스토리지 계정을 만들고 관리할 수도 있습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)을 참조하세요.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy를 사용하여 규정 준수 감사

스토리지 계정이 많은 경우 감사를 수행하여 테넌트 간 개체 복제를 방지하도록 해당 계정이 구성되었는지 확인할 수 있습니다. 규정 준수를 위해 스토리지 계정 집합을 감사하려면 Azure Policy를 사용합니다. Azure Policy는 Azure 리소스에 규칙을 적용하는 정책을 만들고 할당하고 관리하는 데 사용할 수 있는 서비스입니다. Azure Policy는 해당 리소스 가 회사 표준 및 서비스 수준 계약을 준수하도록 유지하는 데 도움이 됩니다. 자세한 내용은 [Azure Policy 개요](../../governance/policy/overview.md)를 참조하세요.

### <a name="create-a-policy-with-an-audit-effect"></a>감사 효과를 사용하여 정책 만들기

Azure Policy는 리소스에 대해 정책 규칙을 평가할 때 발생하는 작업을 결정하는 효과를 지원합니다. 감사 효과는 리소스가 정책을 준수하지 않는 경우 경고를 생성하지만 요청을 중지하지는 않습니다. 효과에 대한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조하세요.

Azure Portal 사용하여 스토리지 계정에 대한 테넌트 간 개체 복제 설정에 대한 감사 효과가 있는 정책을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **작성** 섹션에서 **정의** 를 선택합니다.
1. **정책 정의 추가** 를 선택하여 새 정책 정의를 만듭니다.
1. **정의 위치** 필드의 경우 **자세히** 단추를 선택하여 감사 정책 리소스가 있는 위치를 지정합니다.
1. 정책의 이름을 지정합니다. 설명 및 범주를 선택적으로 지정할 수 있습니다.
1. **정책 규칙** 에서 다음 정책 정의를 **policyRule** 섹션에 추가합니다.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 해당 정책을 저장합니다.

### <a name="assign-the-policy"></a>정책 할당

다음으로 리소스에 정책을 할당합니다. 정책의 범위는 해당 리소스 및 그 아래에 있는 모든 리소스에 해당합니다. 정책 할당에 대한 자세한 내용은 [Azure Policy 할당 구조](../../governance/policy/concepts/assignment-structure.md)를 참조하세요.

Azure Portal에서 정책을 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **작성** 섹션에서 **할당** 을 선택합니다.
1. **정책 할당** 을 선택하여 새 정책 할당을 만듭니다.
1. **범위** 필드에서 정책 할당의 범위를 선택합니다.
1. **정책 정의** 필드에서 **자세히** 단추를 선택한 다음, 이전 섹션에서 정의한 정책을 목록에서 선택합니다.
1. 정책 할당의 이름을 제공합니다. 설명은 선택 사항입니다.
1. **정책 적용** 을 *사용 가능* 으로 유지합니다. 이 설정은 감사 정책에 영향을 주지 않습니다.
1. **검토 + 만들기** 를 선택하여 할당을 만듭니다.

### <a name="view-compliance-report"></a>규정 준수 보고서 보기

정책을 할당한 후에는 규정 준수 보고서를 볼 수 있습니다. 감사 정책에 대한 준수 보고서는 테넌트 간 개체 복제 정책을 허용하는 스토리지 계정에 대한 정보를 제공합니다. 자세한 내용은 [정책 규정 준수 데이터 가져오기](../../governance/policy/how-to/get-compliance-data.md)를 참조하세요.

정책 할당을 만든 후 규정 준수 보고서를 사용할 수 있게 되는 데 몇 분 정도 걸릴 수 있습니다.

Azure Portal에서 규정 준수 보고서를 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **규정 준수** 를 선택합니다.
1. 이전 단계에서 만든 정책 할당의 이름에 대한 결과를 필터링합니다. 보고서에는 정책을 준수하지 않는 리소스가 표시됩니다.
1. 정책을 준수하지 않는 스토리지 계정 목록을 포함하여 추가 세부 정보에 대해 보고서를 드릴다운할 수 있습니다.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/compliance-report-cross-tenant-audit-effect-policy.png" alt-text="Blob 교차 테넌트 개체 복제에 대한 감사 정책에 대한 준수 보고서를 보여주는 스크린샷":::

## <a name="use-azure-policy-to-enforce-same-tenant-replication-policies"></a>Azure Policy 사용하여 동일한 테넌트 복제 정책 적용

Azure Policy는 Azure 리소스가 요구 사항 및 표준을 준수하도록 하여 클라우드 거버넌스를 지원합니다. 조직의 스토리지 계정이 테넌트 간 복제를 허용하지 않도록 하기 위해 테넌트 간 개체 복제 정책을 허용하는 새 스토리지 계정을 만들지 못하게 하는 정책을 만들 수 있습니다. 적용 정책은 거부 효과를 사용하여 테넌트 간 개체 복제를 허용하도록 스토리지 계정을 만들거나 수정하는 요청을 방지합니다. 또한 거부 정책은 해당 계정에 대한 테넌트 간 개체 복제 설정이 정책을 준수하지 않는 경우 기존 계정에 대한 모든 구성 변경을 방지합니다. 거부 효과에 대한 자세한 내용은 [Azure Policy 효과 이해를 참조하세요.](../../governance/policy/concepts/effects.md)

테넌트 간 개체 복제에 대한 거부 효과가 있는 정책을 만들려면 [Azure Policy 사용하여 규정 준수 감사에](#use-azure-policy-to-audit-for-compliance)설명된 것과 동일한 단계를 수행하지만 정책 정의의 **policyRule** 섹션에서 다음 JSON을 제공합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

거부 효과를 사용하여 정책을 만들고 범위에 할당한 후에는 사용자가 교차 테넌트 개체 복제를 허용하는 스토리지 계정을 만들 수 없습니다. 또한 사용자는 현재 테넌트 간 개체 복제를 허용하는 기존 스토리지 계정에 대한 구성을 변경할 수 없습니다. 그렇게 하면 오류가 발생합니다. 정책을 준수하여 계정 만들기 또는 구성 업데이트를 진행하려면 스토리지 계정에 대한 **AllowCrossTenantReplication** 속성을 **false로** 설정해야 합니다.

다음 이미지에서는 거부 효과가 있는 정책에서 테넌트 간 개체 복제가 허용되지 않도록 요구하는 경우 테넌트 간 개체 복제(새 계정의 기본값)를 허용하는 스토리지 계정을 만들려고 할 때 발생하는 오류를 보여 있습니다.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-replication-deny-policy-error-portal.png" alt-text="정책을 위반하여 스토리지 계정을 만들 때 발생하는 오류를 보여 주는 스크린샷":::

## <a name="see-also"></a>참고 항목

- [블록 Blob에 대한 개체 복제](object-replication-overview.md)
- [블록 Blob에 대한 개체 복제 구성](object-replication-configure.md)