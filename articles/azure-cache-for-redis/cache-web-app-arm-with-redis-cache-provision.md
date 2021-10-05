---
title: Azure Cache for Redis가 포함된 웹앱 프로비전
description: Azure Resource Manager 템플릿을 사용하여 Azure Cache for Redis가 포함된 웹앱을 배포합니다.
services: app-service
author: curib
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: cauribeg
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71648781edd8d202add7ce2a7cc6584173f0451b
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537466"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>템플릿을 사용하여 Azure Cache for Redis가 포함된 웹앱 만들기

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에서는 Azure Cache for Redis가 포함된 Azure 웹앱을 배포하는 Azure Resource Manager 템플릿을 만드는 방법에 대해 알아봅니다. 다음 배포 세부 정보를 알아봅니다.

- 배포되는 리소스를 정의하는 방법 
- 배포가 실행될 때 지정되는 매개 변수를 정의하는 방법

배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../azure-resource-manager/templates/syntax.md)를 참조하십시오. 캐시 리소스 종류의 JSON 구문 및 속성에 대해 알아보려면 [Microsoft.Cache 리소스 종류](/azure/templates/microsoft.cache/allversions)를 참조하세요.

전체 템플릿은 [Azure Cache for Redis 템플릿이 포함된 웹앱](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json)을 참조하세요.

## <a name="what-you-will-deploy"></a>배포할 내용
이 템플릿에서 다음을 배포합니다.

* Azure 웹앱
* Azure Cache for Redis

배포를 자동으로 실행하려면 다음 단추를 선택합니다.

[![Azure에 배포](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fweb-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>지정할 매개변수
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>이름에 대한 변수
이 템플릿은 리소스 이름을 생성하기 위해 변수를 사용합니다. [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) 함수를 사용하여 리소스 그룹 ID에 기반한 값을 생성합니다.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>배포할 리소스
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
웹앱과 함께 사용되는 Azure Cache for Redis를 만듭니다. 캐시 이름은 **cacheName** 변수에 지정됩니다.

템플릿은 리소스 그룹과 동일한 위치에 캐시를 만듭니다.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app-azure-cache-for-redis"></a>웹앱(Azure Cache for Redis)
**webSiteName** 변수에 지정된 이름으로 웹앱을 만듭니다.

웹앱은 Azure Cache for Redis에서 작동할 수 있게 하는 앱 설정 속성으로 구성됩니다. 앱 설정은 배포 중에 제공된 값을 기반으로 동적으로 만들어집니다.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheHostName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```


### <a name="web-app-redisenterprise"></a>웹앱(RedisEnterprise)
RedisEnterprise의 경우 리소스 유형이 약간 다르기 때문에 **listKeys** 를 수행하는 방법이 다릅니다.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/RedisEnterprise/databases/', variables('cacheName'), "/default")]",
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheHostName'),abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/RedisEnterprise', variables('cacheName'), 'default'), '2020-03-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>배포 실행 명령
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```