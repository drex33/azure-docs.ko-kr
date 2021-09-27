---
title: 템플릿 함수 - 배포
description: Azure Resource Manager 템플릿(ARM 템플릿)에서 배포 정보를 검색하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 12f449cd70f0ccbeeb0d232299f38fb814a8cb24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733337"
---
# <a name="deployment-functions-for-arm-templates"></a>ARM 템플릿의 배포 함수

Resource Manager는 Azure Resource Manager 템플릿(ARM 템플릿)의 현재 배포와 관련된 값을 가져오기 위한 다음 함수를 제공합니다.

* [deployment](#deployment)
* [environment](#environment)(환경)
* [parameters](#parameters)
* [variables](#variables)

리소스, 리소스 그룹 또는 구독에서 값을 가져오려면 [리소스 함수](template-functions-resource.md)를 참조하세요.

## <a name="deployment"></a>배포

`deployment()`

현재 배포 작업에 대한 정보를 반환합니다.

### <a name="return-value"></a>반환 값

이 함수는 배포하는 동안 전달되는 개체를 반환합니다. 반환된 개체의 속성은 다음 여부에 따라 달라집니다.

* 템플릿 또는 템플릿 사양 배포
* 로컬 파일인 템플릿 배포 또는 URI를 통해 액세스할 수 있는 원격 파일인 템플릿 배포
* 리소스 그룹에 배포 또는 기타 범위([Azure 구독](deploy-to-subscription.md), [관리 그룹](deploy-to-management-group.md) 또는 [테넌트](deploy-to-tenant.md)) 중 하나에 배포

리소스 그룹에 로컬 템플릿을 배포하는 경우: 함수가 다음 형식을 반환합니다.

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

리소스 그룹에 원격 템플릿을 배포하는 경우: 함수가 다음 형식을 반환합니다.

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "uri": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

리소스 그룹에 템플릿 사양을 배포하는 경우: 함수가 다음 형식을 반환합니다.

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Azure 구독, 관리 그룹 또는 테넌트에 배포하는 경우 반환 개체에는 `location` 속성이 포함됩니다. 로컬 템플릿 또는 외부 템플릿을 배포할 때 위치 속성이 포함됩니다. 형식:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>설명

`deployment()`를 사용하여 부모 템플릿의 URI를 기반으로 하는 다른 템플릿에 연결할 수 있습니다.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

포털의 배포 기록에서 템플릿을 다시 배포하는 경우 템플릿은 로컬 파일로 배포됩니다. `templateLink` 속성은 배포 함수에 반환되지 않습니다. 템플릿이 `templateLink`를 사용하여 다른 템플릿과의 링크를 설정하는 경우 포털을 사용하여 다시 배포하지 마세요. 대신 처음에 템플릿을 배포하는 데 사용한 명령을 사용하세요.

### <a name="example"></a>예제

다음 예제에서는 배포 개체를 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deployment.json":::

앞의 예제에서는 다음 개체를 반환합니다.

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

구독 배포의 경우 다음 예제에서는 배포 개체를 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deploymentsubscription.json":::

## <a name="environment"></a>environment

`environment()`

배포에 사용되는 Azure 환경에 대한 정보를 반환합니다.

### <a name="return-value"></a>반환 값

이 함수는 현재 Azure 환경에 대한 속성을 반환합니다. 다음 예제는 전체 Azure에 대한 속성을 보여 줍니다. 소버린 클라우드가 반환하는 속성은 약간 다를 수 있습니다.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>예제

다음 예제 템플릿은 환경 개체를 반환합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/environment.json":::

위의 예제는 전체 Azure에 배포되는 경우 다음 개체를 반환합니다.

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>매개 변수

`parameters(parameterName)`

매개 변수 값을 반환합니다. 템플릿의 매개 변수 섹션에서 지정된 매개 변수 이름을 정의해야 합니다.

Bicep에서는 기호 이름을 사용하여 매개 변수를 직접 참조합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |예 |문자열 |반환할 매개 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정한 매개 변수의 값입니다.

### <a name="remarks"></a>설명

일반적으로 매개 변수를 사용하여 리소스 값을 설정합니다. 다음 예제에서는 웹 사이트의 이름을 배포 중에 전달된 매개 변수 값으로 설정합니다.

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

### <a name="example"></a>예제

다음 예에서는 매개 변수 함수의 간소화된 사용을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/parameters.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| stringOutput | String | 옵션 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {“one”: “a”, “two”: “b”} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | 옵션 1 |

매개 변수에 대한 자세한 내용은 [ARM 템플릿의 매개 변수](./parameters.md)를 참조하세요.

## <a name="variables"></a>variables

`variables(variableName)`

변수의 값을 반환합니다. 템플릿의 변수 섹션에서 지정된 변수 이름을 정의해야 합니다.

Bicep에서는 기호 이름을 사용하여 변수를 직접 참조합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| variableName |예 |String |반환할 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정한 변수의 값입니다.

### <a name="remarks"></a>설명

일반적으로 복잡한 값을 한 번만 구성하여 템플릿을 간소화하기 위해 변수를 사용합니다. 다음 예제에서는 스토리지 계정에 대한 고유한 이름을 생성합니다.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

### <a name="example"></a>예제

다음 예에서는 다른 변수 값을 반환 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/variables.json":::

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {“property1”: “value1”, “property2”: “value2”} |

변수를 사용하는 방법에 대한 자세한 내용은 [ARM 템플릿의 변수](./variables.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
