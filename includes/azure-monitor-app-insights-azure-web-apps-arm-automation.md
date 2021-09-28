---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b88ee4d53f137f98aa82ee736043943f844f36ca
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154599"
---
### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 App Service 애플리케이션 설정

[Azure Resource Manager 템플릿](../articles/azure-resource-manager/templates/syntax.md)을 사용하여 App Services의 애플리케이션 설정을 관리하고 구성할 수 있습니다. 이 방법은 Azure Resource Manager 자동화를 사용하여 새 App Service 리소스를 배포하거나 기존 리소스의 설정을 수정할 때 사용할 수 있습니다.

App Service에 대한 애플리케이션 설정 JSON의 기본 구조는 다음과 같습니다.

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Application Insights에 대해 구성된 애플리케이션 설정을 사용한 Azure Resource Manager 템플릿 예제의 경우 이 [템플릿](https://github.com/Andrew-MSFT/BasicImageGallery), 특히 [줄 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)에서 시작하는 섹션이 유용할 수 있습니다.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights 리소스 만들기를 자동화하고 새로 만든 App Service에 연결합니다.

구성된 모든 기본 Application Insights 설정을 사용하여 Azure Resource Manager 템플릿을 만들려면 Application Insights가 사용되는 새 웹앱을 만드는 것처럼 프로세스를 시작합니다. 

1. 원하는 웹 앱 정보를 사용 하 여 새 App Service 리소스를 만듭니다. **모니터링** 탭에서 Application Insights를 사용 하도록 설정 합니다.

2. **검토 + 만들기** 를 선택 하 고 맨 아래에 **automation을 위한 템플릿을 다운로드** 합니다.

    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/create-web-app.png" alt-text="웹 앱 만들기 메뉴 App Service의 스크린샷" :::

    이 옵션은 모든 필수 설정이 구성된 최신 Azure Resource Manager 템플릿을 생성합니다.
    
    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/arm-template.png" alt-text="웹 앱 템플릿 App Service의 스크린샷" border="false":::
    

다음은 `AppMonitoredSite`의 모든 인스턴스를 사이트 이름으로 바꾸는 샘플입니다.

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enable-through-powershell"></a>PowerShell을 통해 사용

PowerShell을 통해 애플리케이션 모니터링을 사용하도록 설정하려면 기본 애플리케이션 설정만 변경해야 합니다. 다음은 리소스 그룹 “AppMonitoredRG”의 “AppMonitoredSite”라는 웹 사이트에서 애플리케이션 모니터링을 사용하도록 설정하고 “012345678-abcd-ef01-2345-6789abcd” 계측 키로 전송할 데이터를 구성하는 샘플입니다.

[!INCLUDE [updated-for-az](updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```