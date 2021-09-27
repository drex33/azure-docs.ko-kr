---
title: Azure에 대한 함수 앱 리소스 배포 자동화
description: 함수 앱을 배포하는 Azure Resource Manager 템플릿을 빌드하는 방법을 알아봅니다.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 62330b341d2aa59f33b5f780005042d114400748
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625680"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions의 함수 앱에 대한 리소스 배포 자동화

Azure Resource Manager 템플릿을 사용하여 함수 앱을 배포할 수 있습니다. 이 문서에서는 이 작업을 수행하는 데 필요한 리소스와 매개 변수를 간략히 설명합니다. 함수 앱의 [트리거 및 바인딩](functions-triggers-bindings.md)에 따라 다른 리소스를 배포해야 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성하기](../azure-resource-manager/templates/syntax.md)를 참조하십시오.

샘플 템플릿은 다음을 참조하세요.
- [소비 계획의 함수 앱]
- [Azure App Service 계획의 함수 앱]

## <a name="required-resources"></a>필요한 리소스

Azure Functions 배포는 일반적으로 다음 리소스로 구성됩니다.

| 리소스                                                                           | 요구 사항 | 구문 및 속성 참조                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| 함수 앱                                                                     | 필수    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| [Azure Storage](../storage/index.yml) 계정                                   | 필수    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) 구성 요소 | 선택 사항    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| [호스팅 계획](./functions-scale.md)                                             | 선택 사항<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup>호스팅 계획은 [프리미엄 계획](./functions-premium-plan.md) 또는 [App Service 계획](../app-service/overview-hosting-plans.md)에서 함수 앱을 실행하도록 선택하는 경우에만 필요합니다.

> [!TIP]
> 필수는 아니지만 앱에 대한 Application Insights를 구성하는 것이 좋습니다.

<a name="storage"></a>
### <a name="storage-account"></a>스토리지 계정

함수 앱에는 Azure Storage 계정이 필요합니다. Blob, 테이블, 큐 및 파일을 지원하는 일반 용도의 계정이 있어야 합니다. 자세한 내용은 [Azure Functions 스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

또한 사이트 구성에서 `AzureWebJobsStorage` 속성을 앱 설정으로 지정해야 합니다. 함수 앱이 Application Insights를 모니터링에 사용하지 않으면 `AzureWebJobsDashboard`도 앱 설정으로 지정되어야 합니다.

Azure Functions 런타임에서는 `AzureWebJobsStorage` 연결 문자열을 사용하여 내부 큐를 만듭니다.  Application Insights가 활성화되지 않은 경우 런타임은 `AzureWebJobsDashboard` 연결 문자열을 사용하여 Azure Table Storage에 로그온하고 포털의 **모니터** 탭에 전원을 공급합니다.

이러한 속성은 `siteConfig` 개체의 `appSettings` 컬렉션에서 지정됩니다.

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights는 함수 앱 모니터링에 권장됩니다. Application Insights 리소스는 **Microsoft.Insights/components** 유형과 **web** 종류를 사용하여 정의됩니다.

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

또한 `APPINSIGHTS_INSTRUMENTATIONKEY` 애플리케이션 설정을 사용하여 함수 앱에 계측 키를 제공해야 합니다. 이러한 속성은 `siteConfig` 개체의 `appSettings` 컬렉션에 지정됩니다.

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>호스팅 계획

호스팅 계획의 정의는 다양하며 다음 중 하나일 수 있습니다.
* [소비 계획](#consumption)(기본값)
* [프리미엄 계획](#premium)
* [App Service 계획](#app-service-plan)

### <a name="function-app"></a>함수 앱

함수 앱 리소스는 다음과 같이 **Microsoft.Web/sites** 유형과 **functionapp** 종류의 리소스를 사용하여 정의됩니다.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> 호스팅 계획을 명시적으로 정의하는 경우 dependsOn 배열 `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`에 추가 항목이 필요합니다.

함수 앱에는 다음과 같은 애플리케이션 설정이 포함되어야 합니다.

| 설정 이름                 | 설명                                                                               | 예제 값                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Functions 런타임에서 내부 큐에 사용하는 스토리지 계정에 대한 연결 문자열 | [스토리지 계정](#storage) 참조       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 런타임의 버전                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 이 앱에서 함수에 사용할 언어 스택                                   | `dotnet`, `node`, `java`, `python` 또는 `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | `node` 언어 스택을 사용하는 경우에만 필요하며 사용할 버전을 지정              | `10.14.1`                             |

이러한 속성은 `siteConfig` 속성의 `appSettings` 컬렉션에 지정됩니다.

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>소비 계획에 배포

소비 계획은 코드가 실행 중일 때 컴퓨팅 용량을 자동으로 할당하고, 로드를 처리하는 데 필요한 만큼 확장한 다음, 코드가 실행되지 않을 때 축소합니다. 유휴 VM에 대한 요금을 지불하고 용량을 미리 예약할 필요가 없습니다. 자세한 내용은 [Azure Functions 크기 조정 및 호스팅](consumption-plan.md)을 참조하세요.

샘플 Azure Resource Manager 템플릿은 [소비 계획의 함수 앱]을 참조하세요.

### <a name="create-a-consumption-plan"></a>소비 계획 만들기

소비 계획은 정의할 필요가 없습니다. 함수 앱 리소스 자체를 만들 때 지역별로 자동으로 생성되거나 선택됩니다.

소비 계획은 특수한 유형의 "서버 팜" 리소스입니다. Windows의 경우 `computeMode` 및 `sku` 속성에 `Dynamic` 값을 사용하여 지정할 수 있습니다.

```json
{
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Linux의 경우 소비 계획을 명시적으로 정의할 수 없습니다. 자동으로 생성됩니다.

소비 계획을 명시적으로 정의하는 경우 앱이 계획의 리소스 ID를 가리키도록 앱에 `serverFarmId` 속성을 설정해야 합니다. 함수 앱에 계획에 대한 `dependsOn` 설정이 있는지도 확인해야 합니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

소비 계획에서 실행되는 함수 앱에 필요한 설정은 Windows와 Linux 간에 다릅니다.

#### <a name="windows"></a>Windows

Windows에서 소비 계획을 사용하려면 사이트 구성에 기타 설정인 [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)이 필요합니다. 이 속성은 함수 앱 코드 및 구성이 저장되는 스토리지 계정을 구성합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare)배포 슬롯에서 설정을 설정 하지 마세요. 이 설정은 배포 슬롯에서 앱을 만들 때 생성 됩니다.

#### <a name="linux"></a>Linux

Linux에서 함수 앱의 `kind`는 `functionapp,linux`로 설정되어 있어야 하며 `reserved` 속성은 `true`로 설정되어 있어야 합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

[`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) 및 [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) 설정은 Linux에서 지원되지 않습니다.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>프리미엄 계획에 배포

프리미엄 계획은 소비 계획과 동일한 크기 조정을 제공하지만 전용 리소스 및 추가 기능을 포함합니다. 자세한 정보는 [Azure Functions 프리미엄 계획](./functions-premium-plan.md)을 참조하세요.

### <a name="create-a-premium-plan"></a>프리미엄 플랜 만들기

프리미엄 계획은 특수한 유형의 "서버 팜" 리소스입니다. `sku` [설명 개체](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)의 `Name` 속성 값에 `EP1`, `EP2` 또는 `EP3`을 사용하여 지정할 수 있습니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기

프리미엄 계획의 함수 앱에는 `serverFarmId` 속성이 이전에 만든 계획의 리소스 ID로 설정되어 있어야 합니다. 또한 프리미엄 계획을 사용하려면 사이트 구성에 추가 설정인 [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)이 필요합니다. 이 속성은 함수 앱 코드 및 구성이 저장되는 스토리지 계정을 구성합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) 설정은 사이트를 처음 만들 때 자동으로 생성되므로 지정하지 마세요.

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>App Service 계획에 배포

App Service 계획에서 함수 앱은 웹앱과 유사하게 기본, 표준, 프리미엄 SKU의 전용 VM에서 실행됩니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.

샘플 Azure Resource Manager 템플릿은 [Azure App Service 계획의 함수 앱]을 참조하세요.

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

App Service 계획은 "서버 팜" 리소스로 정의됩니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Linux에서 앱을 실행하려면 `kind`도 `Linux`로 설정해야 합니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기

App Service 계획의 함수 앱에는 `serverFarmId` 속성이 이전에 만든 계획의 리소스 ID로 설정되어 있어야 합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

또한 Linux 앱은 `siteConfig` 아래에 `linuxFxVersion` 속성도 포함되어야 합니다. 코드를 배포하기만 하는 경우 이 값은 원하는 런타임 스택에 따라 ```runtime|runtimeVersion``` 형식으로 결정됩니다.

| 스택            | 예제 값                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

[사용자 지정 컨테이너 이미지를 배포](./functions-create-function-linux-custom-image.md)하는 경우, `linuxFxVersion`을 사용하여 지정하고 [Web App for Containers](../app-service/index.yml) 같이 이미지를 끌어올 수 있는 구성을 포함해야 합니다. 또한 앱 콘텐츠가 컨테이너 자체에 제공되므로 `WEBSITES_ENABLE_APP_SERVICE_STORAGE`를 `false`로 설정합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="deploy-to-azure-arc"></a>Azure Arc로 배포

Azure Functions는 [Azure Arc 지원 Kubernetes](../app-service/overview-arc-integration.md)에 배포할 수 있습니다. 이 프로세스는 주로 [App Service 계획에 대한 배포](#deploy-on-app-service-plan)를 따르며, 몇 가지 차이점이 눈에 띕니다.

앱 및 계획 리소스를 만들려면 Azure Arc 지원 Kubernetes 클러스터에 대해 이미 [App Service Kubernetes 환경이 만들어져](../app-service/manage-create-arc-environment.md) 있어야 합니다. 이 예에서는 배포하려는 App Service Kubernetes 환경과 사용자 지정 위치의 리소스 ID가 있는 것으로 가정합니다. 대부분의 템플릿에서는 이를 매개 변수로 제공할 수 있습니다.

```json
{
    "parameters": {
        "kubeEnvironmentId" : {
            "type": "string"
        },
        "customLocationId" : {
            "type": "string"
        }
    }
}
```

사이트와 계획 둘 다 `extendedLocation` 필드를 통해 사용자 지정 위치를 참조해야 합니다. 이 블록은 `properties` 외부에서 `kind` 및 `location`에 피어링됩니다.

```json
{
    "extendedLocation": {
        "type": "customlocation",
        "name": "[parameters('customLocationId')]"
    },
}
```

계획 리소스는 Kubernetes(K1) SKU를 사용해야 하며 해당 `kind` 필드는 ‘inux,kubernetes’여야 합니다. `properties` 안에서 `reserved`는 ‘true’여야 하며 `kubeEnvironmentProfile.id`는 App Service Kubernetes 환경 리소스 ID로 설정되어야 합니다. 예제 계획은 다음과 유사할 수 있습니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "name": "[variables('hostingPlanName')]",
    "location": "[parameters('location')]",
    "apiVersion": "2020-12-01",
    "kind": "linux,kubernetes",
    "sku": {
        "name": "K1",
        "tier": "Kubernetes"
    },
    "extendedLocation": {
        "type": "customlocation",
        "name": "[parameters('customLocationId')]"
    },
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "location": "[parameters('location')]",
        "workerSizeId": "0",
        "numberOfWorkers": "1",
        "kubeEnvironmentProfile": {
            "id": "[parameters('kubeEnvironmentId')]"
        },
        "reserved": true
    }
}
```

함수 앱 리소스에는 코드를 통해 배포할지 또는 컨테이너를 통해 배포할지 여부에 따라 ‘functionapp,linux,kubernetes’ 또는 ‘functionapp,linux,kubernetes,container’로 설정되는 `kind` 필드가 있어야 합니다. 예제 함수 앱은 다음과 유사할 수 있습니다.

```json
 {
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "kind": "kubernetes,functionapp,linux,container",
    "location": "[parameters('location')]",
    "extendedLocation": {
        "type": "customlocation",
        "name": "[parameters('customLocationId')]"
    },
    "dependsOn": [
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[variables('hostingPlanId')]"
    ],
    "properties": {
        "serverFarmId": "[variables('hostingPlanId')]",
        "siteConfig": {
            "linuxFxVersion": "DOCKER|mcr.microsoft.com/azure-functions/dotnet:3.0-appservice-quickstart",
            "appSettings": [
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"

                },
                {
                    "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                    "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                }
            ],
            "alwaysOn": true
        }
    }
}
```

## <a name="customizing-a-deployment"></a>배포 사용자 지정

함수 앱에는 앱 설정 및 소스 제어 옵션을 포함하여 배포에 사용할 수 있는 자식 리소스가 많이 있습니다. 또한 **sourcecontrols** 자식 리소스를 제거하고 대신에 다른 [배포 옵션](functions-continuous-deployment.md)을 사용하도록 선택할 수 있습니다.

> [!IMPORTANT]
> Azure Resource Manager를 사용하여 애플리케이션을 성공적으로 배포하려면 Azure에서 리소스가 배포되는 방식을 이해하는 것이 중요합니다. 다음 예제에서는 **siteConfig** 를 사용하여 최상위 수준 구성을 적용합니다. Functions 런타임 및 배포 엔진에 정보를 전달하기 때문에 최상위 수준에서 이러한 구성을 설정하는 것이 중요합니다. **sourcecontrols/web** 자식 리소스를 적용하기 전에 최상위 수준 정보가 필요합니다. 자식 수준 **config/appSettings** 리소스에 이러한 설정을 구성할 수 있지만 **config/appSettings** 가 적용되기 *전에* 함수 앱이 배포되어야 하는 경우도 있습니다. 예를 들어 [Logic Apps](../logic-apps/index.yml)에서 함수를 사용하는 경우 함수는 다른 리소스의 종속성입니다.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 이 템플릿은 Functions 배포 엔진(Kudu)에서 배치할 수 있는 코드를 검색할 기본 디렉터리를 설정하는 [프로젝트](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 앱 설정 값을 사용합니다. 리포지토리에서 함수는 **src** 폴더의 하위 폴더에 있습니다. 따라서 앞의 예제에서 앱 설정 값은 `src`로 설정합니다. 함수가 리포지토리의 루트에 있거나 소스 제어에서 배포하지 않는 경우 이 앱 설정 값을 제거할 수 있습니다.

## <a name="deploy-your-template"></a>템플릿 배포

다음 방법 중 하나를 사용하여 템플릿을 배포할 수 있습니다.

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Azure 단추에 배포

```<url-encoded-path-to-azuredeploy-json>```을 GitHub에 있는 `azuredeploy.json` 파일의 원시 경로에 대한 [URL 인코딩](https://www.bing.com/search?q=url+encode) 버전으로 바꿉니다.

markdown을 사용하는 예는 다음과 같습니다.

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

HTML을 사용하는 예는 다음과 같습니다.

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>PowerShell을 사용하여 배포

다음 PowerShell 명령은 리소스 그룹을 만들고 필요한 리소스를 사용하여 함수 앱을 만드는 템플릿을 배포합니다. 로컬에서 실행하려면 [Azure PowerShell](/powershell/azure/install-az-ps)이 설치되어 있어야 합니다. 로그인하려면 [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount)를 실행합니다.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

이 배포를 테스트하려면 Windows에서 소비 계획에 함수 앱을 만드는 [이와 같은 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/function-app-create-dynamic/azuredeploy.json)을 사용하면 됩니다. `<function-app-name>`을 함수 앱의 고유한 이름으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

Azure Functions를 개발하고 구성하는 방법에 대해 자세히 알아봅니다.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure 함수 앱 설정을 구성하는 방법](functions-how-to-use-azure-function-app-settings.md)
* [첫 번째 Azure Function 만들기](./functions-get-started.md)

<!-- LINKS -->

[소비 계획의 함수 앱]: https://azure.microsoft.com/resources/templates/function-app-create-dynamic/
[Azure App Service 계획의 함수 앱]: https://azure.microsoft.com/resources/templates/function-app-create-dedicated/
