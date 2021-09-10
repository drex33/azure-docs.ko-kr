---
title: Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 조건 추가(미리 보기) - Azure ABAC
description: Azure Resource Manager 템플릿 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 역할 할당에서 ABAC(특성 기반 액세스 제어) 조건을 추가하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 06/29/2021
ms.author: rolyon
ms.openlocfilehash: 6e64afaab3b367ed807f77e5ebc0214904ed763f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094953"
---
# <a name="add-azure-role-assignment-conditions-using-azure-resource-manager-templates-preview"></a>Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 조건 추가(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 역할 할당 조건](conditions-overview.md)은 더 세분화된 액세스 제어를 제공하기 위해 선택적으로 역할 할당에 추가할 수 있는 추가 검사입니다. 예를 들어 개체를 읽을 특정 태그를 포함하는 개체를 필요로 하는 조건을 추가할 수 있습니다. 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 역할 할당에 대한 조건을 추가하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

역할 할당 조건을 추가하기 위한 사전 요구 사항은 [조건 및 사전 요구 사항](conditions-prerequisites.md)을 참조하세요.

## <a name="add-a-condition"></a>조건 추가

다음 템플릿에서는 조건을 사용하여 [스토리지 Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader) 역할을 할당하는 방법을 보여 줍니다. 조건은 컨테이너 이름이 'blobs-example-container'와 같은지 여부를 확인합니다.

템플릿을 사용하려면 다음 입력을 지정해야 합니다.

- 역할을 할당할 사용자, 그룹, 관리 ID 또는 애플리케이션의 ID
- `User`, `Group` 또는 `ServicePrincipal` 등의 보안 주체 유형. 자세한 내용은 [새 서비스 주체](role-assignments-template.md#new-service-principal)를 참조하세요.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "Principal ID to assign the role to"
            }
        },
        "principalType": {
            "type": "string",
            "metadata": {
                "description": "Type of principal"
            }
        },
        "roleAssignmentGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "New GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "StorageBlobDataReader": "[concat(subscription().Id, '/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1')]" // ID for Storage Blob Data Reader role, but can be any valid role ID
    },
    "resources": [
        {
            "name": "[parameters('roleAssignmentGuid')]",
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview", // API version to call the role assignment PUT.
            "properties": {
                "roleDefinitionId": "[variables('StorageBlobDataReader')]",
                "principalId": "[parameters('principalId')]",
                "principalType": "[parameters('principalType')]",
                "description": "Role assignment condition created with an ARM template",
                "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))", // Role assignment condition
                "conditionVersion": "2.0"
            }
        }
    ]
}
```

역할 할당의 범위는 배포 수준에서 결정됩니다. 리소스 그룹 범위에서 배포를 시작하는 방법에 대한 예제 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 명령은 다음과 같습니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName example-group -TemplateFile rbac-test.json -principalId $principalId -principalType "User"
```

```azurecli
az deployment group create --resource-group example-group --template-file rbac-test.json --parameters principalId=$principalId principalType="User"
```

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예(미리 보기)](../storage/common/storage-auth-abac-examples.md)
- [Azure 역할 할당 조건 문제 해결(미리 보기)](conditions-troubleshoot.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](role-assignments-template.md)
