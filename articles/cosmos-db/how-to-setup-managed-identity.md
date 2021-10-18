---
title: Azure Cosmos DB 계정에 대해 Azure AD를 사용하여 관리 ID 구성
description: Azure Cosmos DB 계정에 대해 Azure Active Directory를 사용하여 관리 ID를 구성하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2021
ms.author: thweiss
ms.openlocfilehash: 826afef5d637278628146af8a35f78232e5b3531
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132924"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에 대해 Azure Active Directory를 사용하여 관리 ID 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 문서에서는 Azure Cosmos DB 계정에 대한 관리 ID를 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID에 익숙하지 않은 경우 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요. 관리 ID 형식에 대해 알아보려면 [관리 ID 형식](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)을 참조하세요.
- 관리 ID를 설정하려면 계정에 [DocumentDB 계정 기여자 역할](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)이 있어야 합니다.

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가

### <a name="using-the-azure-portal"></a>Azure Portal 사용

기존 Azure Cosmos DB 계정에서 시스템 할당 관리 ID를 사용하도록 설정하려면 Azure Portal 계정으로 이동하고 왼쪽 메뉴에서 **ID를** 선택합니다.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="ID 메뉴 항목" border="true":::

시스템 **할당** 섹션에서 **상태를** 켜기로 대칭 전환하고 **저장을** 선택합니다. 시스템 할당 관리 ID 생성을 확인하라는 메시지가 표시됩니다.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-system-assigned.png" alt-text="시스템 할당 ID 사용" border="true":::

ID를 만들고 할당한 후에는 해당 개체(보안 주체) ID를 검색할 수 있습니다.

:::image type="content" source="./media/how-to-setup-managed-identity/system-identity-enabled.png" alt-text="시스템 할당 ID의 개체 ID 검색" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿 사용

> [!IMPORTANT]
> 관리 ID로 작업할 때는 `2021-03-15` 이상의 `apiVersion`을 사용해야 합니다.

신규 또는 기존 Azure Cosmos DB 계정에서 시스템 할당 ID를 사용하도록 설정하려면 리소스 정의에 다음 속성을 포함합니다.

```json
"identity": {
    "type": "SystemAssigned"
}
```

ARM 템플릿의 `resources` 섹션은 다음과 같아야 합니다.

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
]
```

Azure Cosmos DB 계정이 만들어지거나 업데이트되면 다음 속성이 표시됩니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

새 Azure Cosmos DB 계정을 만드는 동안 시스템 할당 ID를 사용하도록 설정하려면 `--assign-identity` 옵션을 추가합니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity
```

`az cosmosdb identity assign` 명령을 사용하여 기존 계정에 시스템 할당 ID를 추가할 수도 있습니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
```

Azure Cosmos DB 계정이 만들어지거나 업데이트되면 명령을 통해 할당된 ID를 가져올 수 있습니다. `az cosmosdb identity show`

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="add-a-user-assigned-identity"></a>사용자 할당 ID 추가

### <a name="using-the-azure-portal"></a>Azure Portal 사용

기존 Azure Cosmos DB 계정에서 사용자 할당 관리 ID를 사용하도록 설정하려면 Azure Portal 계정으로 이동하고 왼쪽 메뉴에서 **ID를** 선택합니다.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="ID 메뉴 항목" border="true":::

사용자 **할당** 섹션에서 **+ 추가를** 선택합니다.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-1.png" alt-text="사용자 할당 ID 사용" border="true":::

Azure Cosmos DB 계정에 할당하려는 모든 ID를 찾아서 선택한 다음, **추가를** 선택합니다.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-2.png" alt-text="할당할 모든 ID 선택" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿 사용

> [!IMPORTANT]
> 관리 ID로 작업할 때는 `2021-03-15` 이상의 `apiVersion`을 사용해야 합니다.

새 또는 기존 Azure Cosmos DB 계정에서 사용자 할당 ID를 사용하도록 설정하려면 리소스 정의에 다음 속성을 포함합니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<identity-resource-id>": {}
    }
}
```

ARM 템플릿의 `resources` 섹션은 다음과 같아야 합니다.

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "<identity-resource-id>": {}
            }
        },
        // ...
    },
    // ...
]
```

Azure Cosmos DB 계정이 생성되거나 업데이트되면 다음 속성이 표시됩니다.

```json
"identity": {
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

새 Azure Cosmos DB 계정을 만드는 동안 사용자 할당 ID를 사용하도록 설정하려면 `--assign-identity` 옵션을 추가하고 할당하려는 ID의 리소스 ID를 전달합니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity <identity-resource-id>
```

명령을 사용하여 기존 계정에 사용자 할당 ID를 추가할 수도 있습니다. `az cosmosdb identity assign`

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
    --identities <identity-resource-id>
```

Azure Cosmos DB 계정이 만들어지거나 업데이트된 후에는 `az cosmosdb identity show` 명령을 사용하여 할당된 ID를 가져올 수 있습니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-or-user-assigned-identity"></a>시스템 할당 또는 사용자 할당 ID 제거

### <a name="using-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿 사용

> [!IMPORTANT]
> 관리 ID로 작업할 때는 `2021-03-15` 이상의 `apiVersion`을 사용해야 합니다.

Azure Cosmos DB 계정에서 시스템 할당 ID를 제거하려면 `identity` 속성의 `type`을 `None`으로 설정합니다.

```json
"identity": {
    "type": "None"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure Cosmos DB 계정에서 모든 관리 ID를 제거하려면 명령을 사용합니다. `az cosmosdb identity remove`

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity remove \
    -n $accountName \
    -g $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보기
- [Azure Cosmos DB의 고객 관리형 키](how-to-setup-cmk.md)에 대한 자세한 정보
