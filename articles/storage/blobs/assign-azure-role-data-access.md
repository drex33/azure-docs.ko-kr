---
title: Blob 데이터에 액세스하기 위한 Azure 역할 할당
titleSuffix: Azure Storage
description: Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 Azure Active Directory 보안 주체에 Blob 데이터에 대한 권한을 할당하는 방법을 알아봅니다. Azure Storage는 Azure AD를 통해 인증 및 권한 부여에 대한 기본 제공 역할과 Azure 사용자 지정 역할을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b30bb21369e75b76c5aba299d38b2ccfc3259803
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651601"
---
# <a name="assign-an-azure-role-for-access-to-blob-data"></a>Blob 데이터에 액세스하기 위한 Azure 역할 할당

Azure AD(Azure Active Directory)는 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Storage는 Blob 데이터에 액세스하는 데 사용되는 공통 권한 집합을 포함하는 Azure 기본 제공 역할 집합을 정의합니다.

Azure AD 보안 주체에 Azure 역할을 할당하는 경우 Azure는 해당 보안 주체의 해당 리소스에 대한 액세스 권한을 부여합니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 사용자 또는 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

Azure AD를 사용하여 Blob 데이터에 대한 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [Azure Active Directory 사용하여 Blob에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)를 참조하세요.

> [!NOTE]
> 이 문서에서는 스토리지 계정의 Blob 데이터에 액세스하기 위해 Azure 역할을 할당하는 방법을 보여줍니다. Azure Storage 관리 작업에 대한 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Storage 리소스 공급자를 사용하여 관리 리소스에 액세스](../common/authorization-resource-provider.md)를 참조하세요.

## <a name="assign-an-azure-role"></a>Azure 역할 할당

Azure Portal, PowerShell, Azure CLI, Azure Resource Manager 템플릿을 사용하여 데이터 액세스에 대한 역할을 할당할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure AD 자격 증명을 사용하여 Azure Portal의 Blob 데이터에 액세스하려면 사용자에게 다음 역할 할당이 있어야 합니다.

- 데이터 액세스 역할(예: **Storage Blob 데이터 기여자**)
- Azure Resource Manager **읽기 권한자** 역할

사용자에게 이러한 역할을 할당하는 방법을 알아보려면 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)에 제공된 지침을 따르세요.

[읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할은 사용자가 스토리지 계정 리소스를 볼 수 있지만 수정할 수는 없도록 허용하는 Azure Resource Manager 역할입니다. Azure Storage에서 데이터에 대한 읽기 권한은 제공하지 않고 계정 관리 리소스에 대해서만 제공합니다. 사용자가 Azure Portal의 Blob 컨테이너로 이동할 수 있도록 하는 데 **읽기 권한자** 역할이 필요합니다.

예를 들어 **샘플 컨테이너** 라는 컨테이너 수준에서 사용자 Mary에게 **스토리지 Blob 데이터 기여자** 역할을 할당하면 Mary에게 해당 컨테이너의 모든 Blob에 대한 읽기, 쓰기 및 삭제 권한이 부여됩니다. 그러나 Mary가 Azure Portal Blob을 보려는 경우에는 **스토리지 Blob 데이터 기여자** 역할 자체에서 포털을 통해 Blob으로 이동하는 데 필요한 권한이 제공되지 않습니다. 포털을 탐색하고 여기에 표시되는 다른 리소스를 보려면 추가 권한이 필요합니다.

Azure AD 자격 증명으로 Azure Portal을 사용하려면 사용자에게 **읽기 권한자** 역할을 할당해야 합니다. 그러나 사용자에게 **Microsoft.Storage/storageAccounts/listKeys/action** 권한이 있는 역할이 할당된 경우 사용자는 공유 키 권한 부여를 통해 스토리지 계정 키와 함께 Portal을 사용할 수 있습니다. 스토리지 계정 키를 사용하려면 스토리지 계정에 공유 키 액세스가 허용되어야 합니다. 공유 키 액세스를 허용하거나 허용하지 않는 것에 대한 자세한 내용은 [Azure Storage 계정에 대한 공유 키 권한 부여 방지](../common/shared-key-authorization-prevent.md)를 참조하세요.

**읽기 권한자** 역할 이외의 추가 권한을 제공하는 Azure Resource Manager 역할을 할당할 수도 있습니다. 보안 모범 사례로 가능한 최소 사용 권한을 할당하는 것이 좋습니다. 자세한 내용은 [Azure RBAC에 대한 모범 사례](../../role-based-access-control/best-practices.md)를 참조하세요.

> [!NOTE]
> Azure Portal에서 데이터 액세스를 위한 계정 키를 사용할 수도 있으므로 사용자가 데이터 액세스 역할을 자신에게 할당하기 전에 Azure Portal을 통해 스토리지 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택](../blobs/authorize-data-operations-portal.md)을 참조하세요.
>
> Azure Portal의 Storage Explorer 미리 보기 버전은 Azure AD 자격 증명을 사용한 Blob 데이터 확인 및 수정 기능을 지원하지 않습니다. Azure Portal의 Storage Explorer는 항상 계정 키를 사용하여 데이터에 액세스합니다. Azure Portal의 Storage Explorer를 사용하려면 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함된 역할을 할당받아야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 Azure 역할을 보안 주체에 할당하려면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 명령을 호출합니다. 명령의 형식은 할당 범위에 따라 다를 수 있습니다. 이 명령을 실행하려면 해당 범위 이상에서 사용자에게 할당된 **Microsoft.Authorization/roleAssignments/write** 권한을 포함하는 역할이 있어야 합니다.

컨테이너에 범위가 지정된 역할을 할당하려면 `--scope` 매개 변수의 컨테이너 범위를 포함하는 문자열을 지정합니다. 컨테이너의 범위는 다음과 같은 형식입니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

다음 예시에서는 *sample-container* 라는 컨테이너에 범위가 지정된 사용자에게 **Storage Blob 데이터 참가자** 역할을 할당합니다. 샘플 값과 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

구독, 리소스 그룹 또는 스토리지 계정 범위에서 PowerShell을 사용하여 역할을 할당하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-powershell.md)을 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 Azure 역할을 보안 주체에 할당하려면 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용합니다. 명령의 형식은 할당 범위에 따라 다를 수 있습니다. 명령의 형식은 할당 범위에 따라 다를 수 있습니다. 이 명령을 실행하려면 해당 범위 이상에서 사용자에게 할당된 **Microsoft.Authorization/roleAssignments/write** 권한을 포함하는 역할이 있어야 합니다.

컨테이너에 범위가 지정된 역할을 할당하려면 `--scope` 매개 변수의 컨테이너 범위를 포함하는 문자열을 지정합니다. 컨테이너의 범위는 다음과 같은 형식입니다.

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

다음 예제에서는 컨테이너 수준에 범위가 지정된 사용자에게 **스토리지 Blob 데이터 기여자** 역할을 할당합니다. 샘플 값과 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

구독, 리소스 그룹 또는 스토리지 계정 범위에서 PowerShell을 사용하여 역할을 할당하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-cli.md)을 참조하세요.

# <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용하여 Azure 역할을 할당하는 방법을 알아보려면, [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-template.md)을 참조하세요.

---

Azure Storage의 Azure 역할 할당에 대한 다음 사항에 유의하세요.

- Azure Storage 계정을 만들면 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지 않습니다. Azure Storage에 Azure 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너 수준으로 지정할 수 있습니다.
- 스토리지 계정이 Azure Resource Manager 읽기 전용 잠금으로 잠긴 경우, 잠금으로 인해 스토리지 계정 또는 컨테이너로 범위가 지정된 Azure 역할을 할당할 수 없습니다.
- Azure AD를 통해 데이터에 액세스할 수 있는 적절한 허용 권한을 설정했으며 데이터에 액세스할 수 없는 경우, "AuthorizationPermissionMismatch" 오류가 발생합니다. Azure AD에서 변경한 사용 권한을 복제하는 데 충분한 시간을 허용하고 액세스를 차단하는 거부 할당이 없는지 확인해야 합니다. [Azure 거부 할당 이해](../../role-based-access-control/deny-assignments.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)
- [Azure RBAC에 대한 모범 사례](../../role-based-access-control/best-practices.md)
