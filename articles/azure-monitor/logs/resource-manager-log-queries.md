---
title: 로그 쿼리에 대한 Resource Manager 템플릿 샘플
description: Azure Monitor 로그 쿼리를 배포하기 위한 Azure Resource Manager 템플릿 샘플입니다.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: 39d8661f38854f8b3fccf60cad73067aabc4c0e2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231630"
---
# <a name="resource-manager-template-samples-for-log-queries-in-azure-monitor"></a>Azure Monitor의 로그 쿼리에 대한 Resource Manager 템플릿 샘플
이 문서에는 Azure Monitor에서 로그 쿼리를 만들고 구성하기 위한 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/syntax.md) 샘플이 포함되어 있습니다. 각 샘플에는 템플릿 파일 및 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>템플릿 참조

- [Microsoft.OperationalInsights workspaces/savedSearches](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces/savedsearches)

## <a name="simple-log-query"></a>단순 로그 쿼리
다음 샘플에서는 Log Analytics 작업 영역에 로그 쿼리를 추가합니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2015-03-20",
          "name": "VMSS query",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "eTag": "*",
            "displayName": "VMSS Instance Count",
            "category": "VMSS",
            "query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "version": 1
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="log-query-as-a-function"></a>함수로 로그 쿼리
다음 샘플에서는 Log Analytics 작업 영역에 함수로 로그 쿼리를 추가합니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2017-03-15-preview",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Cross workspace query",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Failed Logon Events",
              "category": "Security",
              "FunctionAlias": "failedlogonsecurityevents",
              "query": "
                union withsource=SourceWorkspace
                workspace('workspace1').SecurityEvent,
                workspace('workspace2').SecurityEvent,
                workspace('workspace3').SecurityEvent,
                | where EventID == 4625",
              "version": 1
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="parameterized-function"></a>매개 변수화된 함수
다음 샘플에서는 Log Analytics 작업 영역에 매개 변수를 사용하는 함수로 로그 쿼리를 추가합니다. 매개 변수화된 함수를 사용하는 두 번째 로그 쿼리가 포함되어 있습니다.

> [!NOTE]
> 리소스 템플릿은 현재 매개 변수화된 함수에 사용할 수 있는 유일한 메서드입니다. 모든 로그 쿼리는 작업 영역에 설치된 후 함수를 사용할 수 있습니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Parameterized function",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Unavailable computers function",
              "category": "Samples",
              "FunctionAlias": "UnavailableComputers",
              "FunctionParameters": "argSpan: timespan",
              "query": " Heartbeat | summarize LastHeartbeat=max(TimeGenerated) by Computer| where LastHeartbeat < ago(argSpan)"
          }
        },
        {
          "type": "savedSearches",
          "apiVersion": "2020-08-01",
          "name": "Query using function",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "properties": {
              "etag": "*",
              "displayName": "Unavailable computers",
              "category": "Samples",
              "query": "UnavailableComputers(7days)"
          }
        }
      ]
    }
  ]
}

```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="next-steps"></a>다음 단계

* [Azure Monitor에 대한 다른 샘플 템플릿을 가져옵니다](../resource-manager-samples.md).
* [로그 쿼리에 대해 자세히 알아보세요](../logs/log-query-overview.md).
* [함수에 대해 자세히 알아보세요.](../logs/functions.md)