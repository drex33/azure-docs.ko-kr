---
title: Application Insights 리소스에 대한 Resource Manager 템플릿 샘플
description: Azure Monitor에 Application Insights 리소스를 배포하기 위한 Azure Resource Manager 템플릿 샘플입니다.
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: d725c6f88b142a49a3ea4231c6ec6ea68c414284
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132116"
---
# <a name="resource-manager-template-samples-for-creating-application-insights-resources"></a>Application Insights 리소스 생성을 위한 Resource Manager 템플릿 샘플

이 문서에는 [클래식 Application Insights 리소스](../app/create-new-resource.md) 및 새 [미리 보기 작업 영역 기반 Application Insights 리소스](../app/create-workspace-resource.md)를 배포하고 구성하기 위한 샘플 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/syntax.md)이 포함되어 있습니다. 각 샘플에는 템플릿 파일 및 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="classic-application-insights-resource"></a>클래식 Application Insights 리소스

다음 샘플에서는 [클래식 Application Insights 리소스](../app/create-new-resource.md)를 만듭니다. 

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "metadata": {
            "description": "Name of Application Insights resource."
          }
        },
        "type": {
            "type": "string",
            "metadata": {
            "description": "Type of app you are deploying. This field is for legacy reasons and will not impact the type of App Insights resource you deploy."
          }
        },
        "regionId": {
            "type": "string",
            "metadata": {
            "description": "Which Azure Region to deploy the resource to. This must be a valid Azure regionId."
          }
        },
        "tagsArray": {
            "type": "object",
            "metadata": {
            "description": "See documentation on tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources."
          }
        },
        "requestSource": {
            "type": "string",
            "metadata": {
            "description": "Source of Azure Resource Manager deployment"
        }
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2014-08-01",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "my_app_insights_resource"
        },
        "regionId": {
            "value": "westus2"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "CustomDeployment"
        }
    }
}
```

## <a name="workspace-based-application-insights-resource"></a>작업 영역 기반 Application Insights 리소스 

다음 샘플에서는 [작업 영역 기반 Application Insights 리소스](../app/create-workspace-resource.md)를 만듭니다. 작업 영역 기반 Application Insights는 현재 **미리 보기** 로 제공합니다. 


### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "metadata": {
            "description": "Name of Application Insights resource."
          }
        },
        "type": {
            "type": "string",
            "metadata": {
            "description": "Type of app you are deploying. This field is for legacy reasons and will not impact the type of App Insights resource you deploy."
          }
        },
        "regionId": {
            "type": "string",
            "metadata": {
            "description": "Which Azure Region to deploy the resource to. This must be a valid Azure regionId."
          }
        },
        "tagsArray": {
            "type": "object",
            "metadata": {
            "description": "See documentation on tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources."
            }
        },
        "requestSource": {
            "type": "string",
            "metadata": {
            "description": "Source of Azure Resource Manager deployment"
        }
        },
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
            "description": "Log Analytics workspace ID to associate with your Application Insights resource."
        }
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "value": "my_workspace_based_resource"
    },
    "type": {
      "value": "web"
    },
    "regionId": {
      "value": "westus2"
    },
    "tagsArray": {
      "value": {}
    },
    "requestSource": {
      "value": "CustomDeployment"
    },
    "workspaceResourceId": {
      "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testxxxx/providers/microsoft.operationalinsights/workspaces/testworkspace"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에 대한 다른 샘플 템플릿을 가져옵니다](../resource-manager-samples.md).
* [클래식 Application Insights에 대해 자세히 알아봅니다](../app/create-new-resource.md).
* [작업 영역 기반 Application Insights에 대해 자세히 알아봅니다](../app/create-workspace-resource.md).