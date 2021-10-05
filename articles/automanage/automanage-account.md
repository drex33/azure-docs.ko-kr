---
title: Azure Automanage 계정
description: Automanage 계정의 작동 방법과 해당 계정을 만드는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d418cd8c6ad764ee68cf8defd697ed168ee4136c
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455098"
---
# <a name="automanage-accounts"></a>Automanage 계정

Automanage 계정은 Automanage 서비스에서 자동화된 작업을 수행하는 데 사용하는 ID입니다.

Azure Portal 환경에서 VM에 대해 Automanage를 사용하도록 설정하는 경우 Automanage 계정을 할당하거나 수동으로 만들 수 있는 **Azure VM 모범 사례 사용** 블레이드에 고급 드롭다운이 있습니다.

Automanage 계정에는 Automanage에 온보딩한 컴퓨터가 포함된 구독에 대한 **기여자** 및 **리소스 정책 기여자** 역할이 모두 부여됩니다. 여러 구독에서 컴퓨터에 동일한 Automanage 계정을 사용할 수 있습니다. 그러면 모든 구독에 대해 해당 Automanage 계정 **기여자** 및 **리소스 정책 기여자** 권한이 부여됩니다.

VM이 다른 구독의 Log Analytics 작업 영역에 연결되어 있는 경우에는 그 다른 구독에서도 Automanage 계정에 **기여자** 와 **리소스 정책 기여자** 가 모두 부여됩니다.

새 Automanage 계정으로 Automanage를 사용하도록 설정하는 경우 구독에 대해 **소유자** 역할 또는 **사용자 액세스 관리자** 역할과 함께 **기여자** 역할이 필요합니다.

기존 Automanage 계정으로 Automanage를 사용하도록 설정하려면 VM이 포함된 리소스 그룹에 대한 **기여자** 역할이 있어야 합니다.

> [!NOTE]
> Automanage 모범 사례를 사용하지 않도록 설정하면 연결된 구독에 대한 Automanage 계정의 사용 권한이 유지됩니다. 구독의 IAM 페이지로 이동하여 사용 권한을 수동으로 제거하거나 Automanage 계정을 삭제합니다. 컴퓨터를 계속 관리하는 경우 Automanage 계정을 삭제할 수 없습니다.

## <a name="create-an-automanage-account"></a>Automanage 계정 만들기
포털을 사용하거나 ARM 템플릿을 사용하여 Automanage 계정을 만들 수 있습니다.

### <a name="portal"></a>포털
1. 포털에서 **Automanage** 블레이드로 이동합니다.
1. **기존 컴퓨터에서 사용** 을 클릭합니다.
1. **고급** 에서 "새 계정 만들기"를 클릭합니다.
1. 필수 필드를 입력하고 **만들기** 를 클릭합니다.

### <a name="arm-template"></a>ARM 템플릿
ARM 템플릿을 사용하여 Automanage 계정을 만들려면 다음 두 단계를 따라야 합니다.
1. Automanage 계정 만들기
1. 계정에 작업을 수행할 수 있는 충분한 권한 부여
    1. 이 단계에 대해 만든 계정의 개체 ID가 필요합니다.
        1. 계정 서비스 주체(개체 ID 포함)의 세부 정보를 찾는 단계는 [여기](../active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal.md#view-the-service-principal)에서 확인할 수 있습니다.
    1. 서비스 주체를 찾았으면 **개체 ID** 를 복사합니다. 아래에서 권한을 위임하는 데 필요하므로 저장해 둡니다.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Automanage 계정을 만듭니다(이 계정에는 권한을 부여하지 않음).
Automanage 계정을 만들려면 다음 ARM 템플릿을 `azuredeploy.json`으로 저장하고 아래의 Azure CLI 명령을 실행합니다. 작업이 완료되면 아래의 두 번째 템플릿으로 이동하여 충분한 사용 권한을 계정에 위임합니다.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Automanage 계정에 사용 권한 부여
Automanage 계정에 충분한 사용 권한을 부여하려면 다음을 수행해야 합니다.
1. 다음 ARM 템플릿을 `azuredeploy2.json`으로 저장하고 아래의 Azure CLI 명령을 실행합니다.
1. 메시지가 표시되면 사용자가 만들고 저장한 Automanage 계정의 개체 ID를 입력합니다.

```azurecli-interactive
az deployment sub create --location <location> --template-file azuredeploy2.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "dateTime": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        }
    },
    "variables": {
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('contributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('resourcePolicyContributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계
* [Linux](./automanage-linux.md) 및 [Windows](./automanage-windows-server.md)용 Automanage 서비스에 대해 알아보기