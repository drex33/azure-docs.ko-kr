---
title: 템플릿 함수-범위
description: 배포 범위에 대 한 값을 검색 하기 위해 Azure Resource Manager 템플릿 (ARM 템플릿)에서 사용할 수 있는 함수에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 11/23/2021
ms.openlocfilehash: 4b7e9ae39fc87f0dac92e96892c177776383d034
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077523"
---
# <a name="scope-functions-for-arm-templates"></a>ARM 템플릿에 대 한 범위 함수

리소스 관리자는 Azure Resource Manager 템플릿 (ARM 템플릿)에서 배포 범위 값을 가져오기 위한 다음 함수를 제공 합니다.

* [managementGroup](#managementgroup)
* [resourceGroup](#resourcegroup)
* [subscription](#subscription)
* [테넌트](#tenant)

매개 변수, 변수 또는 현재 배포에서 값을 가져오려면 [배포 값 함수](template-functions-deployment.md)를 참조하세요.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

현재 배포의 관리 그룹에 있는 속성을 사용 하 여 개체를 반환 합니다.

### <a name="remarks"></a>설명

`managementGroup()`은 [관리 그룹 배포](deploy-to-management-group.md)에만 사용될 수 있습니다. 배포 작업에 대한 현재 관리 그룹을 반환합니다. 를 사용 하 여 현재 관리 그룹에 대 한 속성을 가져옵니다.

### <a name="return-value"></a>반환 값

현재 관리 그룹에 대 한 속성을 포함 하는 개체입니다.

### <a name="management-group-example"></a>관리 그룹의 예

다음 예에서는 현재 관리 그룹에 대 한 속성을 반환 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "mgInfo": "[managementGroup()]"
  },
  "resources": [],
  "outputs": {
    "mgResult": {
      "type": "object",
      "value": "[variables('mgInfo')]"
    }
  }
}
```

그러면 다음을 반환합니다.

```json
"mgResult": {
  "type": "Object",
  "value": {
    "id": "/providers/Microsoft.Management/managementGroups/examplemg1",
    "name": "examplemg1",
    "properties": {
      "details": {
        "parent": {
          "displayName": "Tenant Root Group",
          "id": "/providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000",
          "name": "00000000-0000-0000-0000-000000000000"
        },
        "updatedBy": "00000000-0000-0000-0000-000000000000",
        "updatedTime": "2020-07-23T21:05:52.661306Z",
        "version": "1"
      },
      "displayName": "Example MG 1",
      "tenantId": "00000000-0000-0000-0000-000000000000"
    },
    "type": "/providers/Microsoft.Management/managementGroups"
  }
}
```

다음 예에서는 새 관리 그룹을 만들고이 함수를 사용 하 여 부모 관리 그룹을 설정 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[format('mg-{0}', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2020-05-01",
      "scope": "/",
      "name": "[parameters('mgName')]",
      "properties": {
        "details": {
          "parent": {
            "id": "[managementGroup().id]"
          }
        }
      }
    }
  ],
  "outputs": {
    "newManagementGroup": {
      "type": "string",
      "value": "[parameters('mgName')]"
    }
  }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

현재 리소스 그룹을 나타내는 개체를 반환합니다.

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

**managedBy** 속성은 다른 서비스에서 관리하는 리소스를 포함하는 리소스 그룹에 대해서만 반환됩니다. 관리형 애플리케이션, Databricks 및 AKS의 경우 속성 값은 관리하는 리소스의 리소스 ID입니다.

### <a name="remarks"></a>설명

`resourceGroup()` 함수는 [구독 수준에서 배포](deploy-to-subscription.md)된 템플릿에서 사용할 수 없습니다. 리소스 그룹에 배포된 템플릿에서만 사용할 수 있습니다. 부모 템플릿이 구독에 배포되는 경우에도 리소스 그룹을 대상으로 하는 [연결된 템플릿 또는 중첩된 템플릿(내부 범위 포함)](linked-templates.md)에서 `resourceGroup()` 함수를 사용할 수 있습니다. 이 시나리오에서는 연결된 템플릿이나 중첩된 템플릿이 리소스 그룹 수준에서 배포됩니다. 구독 수준 배포에서 리소스 그룹을 대상으로 지정하는 방법에 대한 자세한 내용은 [둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포](./deploy-to-resource-group.md)를 참조하세요.

resourceGroup 함수는 일반적으로 리소스 그룹과 동일한 위치에 리소스를 만드는 데 사용됩니다. 다음 예제에서는 기본 매개 변수 값에 대해 리소스 그룹 위치를 사용합니다.

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

또한 `resourceGroup` 함수를 사용하여 리소스 그룹의 태그를 리소스에 적용할 수 있습니다. 자세한 내용은 [리소스 그룹에서 태그 적용](../management/tag-resources.md#apply-tags-from-resource-group)을 참조하세요.

중첩된 템플릿을 사용하여 여러 리소스 그룹에 배포하는 경우 `resourceGroup` 함수를 평가하는 범위를 지정할 수 있습니다. 자세한 내용은 [둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포](./deploy-to-resource-group.md)를 참조하세요.

### <a name="resource-group-example"></a>리소스 그룹 예제

다음 예에서는 리소스 그룹의 속성을 반환 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/resourcegroup.json":::

앞의 예제는 다음과 같은 형식의 개체를 반환합니다.

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="subscription"></a>subscription

`subscription()`

현재 배포에 대한 구독 관련 세부 정보를 반환합니다.

### <a name="return-value"></a>반환 값

이 함수는 다음 형식을 반환합니다.

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>설명

중첩된 템플릿을 사용하여 여러 구독에 배포하는 경우 subscription 함수를 평가하는 범위를 지정할 수 있습니다. 자세한 내용은 [둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포](./deploy-to-resource-group.md)를 참조하세요.

### <a name="subscription-example"></a>구독 예제

다음 예에서는 출력 섹션에서 호출되는 구독 함수를 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/resource/subscription.json":::

## <a name="tenant"></a>tenant

`tenant()`

현재 배포에 대 한 테 넌 트에 대 한 속성을 반환 합니다.

### <a name="remarks"></a>설명

`tenant()`는 모든 배포 범위에서 사용될 수 있습니다. 항상 현재 테넌트를 반환합니다. 이 함수를 사용 하 여 현재 테 넌 트의 속성을 가져옵니다.

연결 된 템플릿 또는 확장 리소스의 범위를 설정 하는 경우 구문을 사용 합니다 `"scope": "/"` .

### <a name="return-value"></a>반환 값

현재 테 넌 트에 대 한 속성을 포함 하는 개체입니다.

### <a name="tenant-example"></a>테넌트의 예

다음 예에서는 테 넌 트의 속성을 반환 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "tenantInfo": "[tenant()]"
  },
  "resources": [],
  "outputs": {
    "tenantResult": {
      "type": "object",
      "value": "[variables('tenantInfo')]"
    }
  }
}
```

그러면 다음을 반환합니다.

```json
"tenantResult": {
  "type": "Object",
  "value": {
    "countryCode": "US",
    "displayName": "Contoso",
    "id": "/tenants/00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000"
  }
}
```

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
* 여러 템플릿을 병합하려면 [Azure 리소스를 배포할 때 연결된 템플릿 및 중첩된 템플릿 사용](linked-templates.md)을 참조하세요.
* 리소스 형식을 만들 때 지정된 횟수만큼 반복하려면 [ARM 템플릿의 리소스 반복](copy-resources.md)을 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
