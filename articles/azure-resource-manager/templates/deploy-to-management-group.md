---
title: 관리 그룹에 리소스 배포
description: Azure Resource Manager 템플릿의 관리 그룹 범위에서 리소스를 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/14/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 5dbf94c6f99e875e3694b96a3f01474c89348397
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654179"
---
# <a name="management-group-deployments-with-arm-templates"></a>ARM 템플릿을 사용하여 관리 그룹 배포

조직이 성장하면 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하여 관리 그룹 수준에서 리소스를 만들 수 있습니다. 예를 들어 관리 그룹에 대한 [정책](../../governance/policy/overview.md)을 정의하고 할당하거나 [Azure RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 할당해야 할 수 있습니다. 관리 그룹 수준 템플릿을 사용하면 선언적으로 정책을 적용하고 관리 그룹 수준에서 역할을 할당할 수 있습니다.

## <a name="supported-resources"></a>지원되는 리소스

모든 리소스 종류를 관리 그룹 수준에 배포할 수 있는 것은 아닙니다. 이 섹션에서는 지원되는 리소스 종류를 나열합니다.

Azure Blueprints의 경우 다음을 사용합니다.

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [버전](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure Policy의 경우 다음을 사용합니다.

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Azure RBAC(Azure 역할 기반 액세스 제어)의 경우 다음을 사용합니다.

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

구독 또는 리소스 그룹에 배포하는 중첩된 템플릿의 경우 다음을 사용합니다.

* [deployments](/azure/templates/microsoft.resources/deployments)

리소스를 관리하려면 다음을 사용합니다.

* [태그](/azure/templates/microsoft.resources/tags)

관리 그룹은 테넌트 수준 리소스입니다. 그러나 새 관리 그룹의 범위를 테넌트로 설정하여 관리 그룹 배포에서 관리 그룹을 만들 수 있습니다. [관리 그룹](#management-group)을 참조하세요.

## <a name="schema"></a>스키마

관리 그룹 배포에 사용하는 스키마는 리소스 그룹 배포에 대한 스키마와 다릅니다.

템플릿의 경우 다음을 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  ...
}
```

매개 변수 파일에 대한 스키마는 모든 배포 범위에 대해 동일합니다. 매개 변수 파일의 경우 다음을 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  ...
}
```

## <a name="deployment-commands"></a>배포 명령

관리 그룹에 배포하려면 관리 그룹 배포 명령을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)를 사용합니다.

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell의 경우 [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment)를 사용합니다.

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

ARM 템플릿 배포를 위한 배포 명령 및 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [ARM 템플릿 및 Azure Portal을 사용하여 리소스 배포](deploy-portal.md)
* [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)
* [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)
* [ARM 템플릿 및 Azure Resource Manager REST API를 사용하여 리소스 배포](deploy-rest.md)
* [배포 단추를 사용하여 GitHub 리포지토리에서 템플릿 배포](deploy-to-azure-button.md)
* [Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

관리 그룹 수준 배포의 경우 배포할 위치를 제공해야 합니다. 배포 위치는 배포하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다. [구독](deploy-to-subscription.md) 및 [테넌트](deploy-to-tenant.md) 배포에도 위치가 필요합니다. [리소스 그룹](deploy-to-resource-group.md) 배포의 경우 리소스 그룹의 위치는 배포 데이터를 저장하는 데 사용됩니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 _azuredeploy.json_ 이라는 템플릿을 배포하면 **azuredeploy** 라는 기본 배포 이름을 만듭니다.

각 배포 이름의 경우 위치는 변경할 수 없습니다. 다른 위치의 이름이 동일한 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 예를 들어 **centralus** 에서 이름이 **deployment1** 인 관리 그룹 배포를 만드는 경우 나중에 이름은 **deployment1** 이지만 위치는 **westus** 인 다른 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="deployment-scopes"></a>배포 범위

관리 그룹에 배포하는 경우 다음에 리소스를 배포할 수 있습니다.

* 작업의 대상 관리 그룹
* 테넌트의 다른 관리 그룹
* 관리 그룹의 구독
* 관리 그룹의 리소스 그룹
* 리소스 그룹의 테넌트

[확장 리소스](scope-extension-resources.md)의 범위를 배포 대상과 다른 대상으로 지정할 수 있습니다.

템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

이 섹션에서는 다양한 범위를 지정하는 방법을 보여 줍니다. 단일 템플릿에서 여러 범위를 결합할 수 있습니다.

### <a name="scope-to-target-management-group"></a>대상 관리 그룹에 대한 범위

템플릿의 리소스 섹션 내에 정의된 리소스는 배포 명령에서 관리 그룹에 적용됩니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>다른 관리 그룹에 대한 범위

다른 관리 그룹을 대상으로 지정하려면 중첩된 배포를 추가하고 `scope` 속성을 지정합니다. `scope` 속성을 `Microsoft.Management/managementGroups/<mg-name>` 형식의 값으로 설정합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>구독으로 범위 지정

관리 그룹 내에서 구독을 대상으로 지정할 수도 있습니다. 템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

관리 그룹 내에서 구독을 대상으로 하려면 중첩된 배포 및 `subscriptionId`속성을 사용합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>리소스 그룹에 대한 범위

관리 그룹 내에서 리소스 그룹을 대상으로 지정할 수도 있습니다. 템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

관리 그룹 내에서 리소스 그룹을 대상으로 하려면 중첩된 배포를 사용합니다. `subscriptionId` 및 `resourceGroup` 속성을 설정합니다. 중첩된 배포는 리소스 그룹의 위치에 배포되므로 위치를 설정하지 마세요.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

구독 내에서 리소스 그룹을 만들고 해당 리소스 그룹에 스토리지 계정을 배포하는 데 관리 그룹 배포를 사용하려면 [구독 및 리소스 그룹에 배포](#deploy-to-subscription-and-resource-group)를 참조하세요.

### <a name="scope-to-tenant"></a>테넌트로 범위 지정

테넌트에서 리소스를 만들려면 `scope`을 `/`로 설정합니다. 템플릿을 배포하는 사용자에게는 [테넌트에서 배포하는 데 필요한 액세스 권한이 있어야 합니다](deploy-to-tenant.md#required-access).

중첩된 배포를 사용하려면 `scope`와 `location`을 설정합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

또는 관리 그룹과 같은 일부 리소스 종류에 대해 범위를 `/`로 설정할 수 있습니다. 새 관리 그룹을 만드는 방법에 대해서는 다음 섹션에서 설명합니다.

## <a name="management-group"></a>관리 그룹

관리 그룹 배포에서 관리 그룹을 만들려면 관리 그룹에 대한 범위를 `/`로 설정해야 합니다.

다음 예에서는 루트 관리 그룹에 새 관리 그룹을 만듭니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

다음 예에서는 부모로 지정된 관리 그룹에 새 관리 그룹을 만듭니다. 범위는 `/`로 설정됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    },
    "parentMG": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('mgName')]",
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2021-04-01",
      "scope": "/",
      "location": "eastus",
      "properties": {
        "details": {
          "parent": {
            "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
          }
        }
      }
    }
  ],
  "outputs": {
    "output": {
      "type": "string",
      "value": "[parameters('mgName')]"
    }
  }
}
```

## <a name="subscriptions"></a>Subscriptions

ARM 템플릿을 사용하여 관리 그룹에서 새 Azure 구독을 만들려면 다음을 참조하세요.

* [프로그래밍 방식으로 Azure 기업계약 구독 만들기](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [프로그래밍 방식으로 Microsoft 고객 계약에 대한 Azure 구독 만들기](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [프로그래밍 방식으로 Microsoft 파트너 계약에 대한 Azure 구독 만들기](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

기존 Azure 구독을 새 관리 그룹으로 이동하는 템플릿을 배포하려면 [ARM 템플릿에서 구독 이동](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)을 참조하세요.

## <a name="azure-policy"></a>Azure Policy

관리 그룹에 배포되는 사용자 지정 정책 정의는 관리 그룹의 확장입니다. 사용자 지정 정책 정의의 ID를 가져오려면 [Extensionresourceid()](template-functions-resource.md#extensionresourceid) 함수를 사용합니다. 기본 제공 정책 정의는 테넌트 수준 리소스입니다. 기본 제공 정책 정의의 ID를 가져오려면 [Tenantresourceid()](template-functions-resource.md#tenantresourceid) 함수를 사용합니다.

다음 예에서는 관리 그룹 수준에서 정책을 [정의](../../governance/policy/concepts/definition-structure.md)하고 할당하는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "targetMG": {
      "type": "string",
      "metadata": {
        "description": "Target Management Group"
      }
    },
    "allowedLocations": {
      "type": "array",
      "defaultValue": [
        "australiaeast",
        "australiasoutheast",
        "australiacentral"
      ],
      "metadata": {
        "description": "An array of the allowed locations, all other locations will be denied by the created policy."
      }
    }
  },
  "variables": {
    "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
    "policyDefinition": "LocationRestriction"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "name": "[variables('policyDefinition')]",
      "apiVersion": "2020-09-01",
      "properties": {
        "policyType": "Custom",
        "mode": "All",
        "parameters": {
        },
        "policyRule": {
          "if": {
            "not": {
              "field": "location",
              "in": "[parameters('allowedLocations')]"
            }
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "location-lock",
      "apiVersion": "2020-09-01",
      "dependsOn": [
        "[variables('policyDefinition')]"
      ],
      "properties": {
        "scope": "[variables('mgScope')]",
        "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
      }
    }
  ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>구독 및 리소스 그룹에 배포

관리 그룹 수준 배포에서 관리 그룹 내의 구독을 대상으로 지정할 수 있습니다. 다음 예에서는 구독 내에 리소스 그룹을 만들고 해당 리소스 그룹에 스토리지 계정을 배포합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2021-04-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedRG",
      "subscriptionId": "[parameters('nestedSubId')]",
      "resourceGroup": "[parameters('nestedRG')]",
      "dependsOn": [
        "nestedSub"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2021-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('nestedLocation')]",
              "kind": "StorageV2",
              "sku": {
                "name": "Standard_LRS"
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

* 역할 할당에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당을 참조하세요.](../../role-based-access-control/role-assignments-template.md)
* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
* [구독 수준](deploy-to-subscription.md) 및 [테넌트 수준](deploy-to-tenant.md)에서 템플릿을 배포할 수도 있습니다.
