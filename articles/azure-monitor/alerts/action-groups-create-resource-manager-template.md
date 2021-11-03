---
title: Resource Manager 템플릿으로 작업 그룹 만들기
description: Azure 리소스 관리자 템플릿을 사용하여 작업 그룹을 만드는 방법을 알아봅니다.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: dukek
ms.openlocfilehash: 1680ff8d209fc2680b19d3d3afe8c2b6aded9678
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044232"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 작업 그룹 만들기
이 문서에서는 [Azure 리소스 관리자 템플릿](../../azure-resource-manager/templates/syntax.md)을 사용하여 작업 그룹을 구성하는 방법을 설명합니다. 템플릿을 사용하면 특정 유형의 경고에서 다시 사용할 수 있는 작업 그룹을 자동으로 설정할 수 있습니다. 이러한 작업 그룹은 경고가 트리거될 때 올바른 당사자가 모두 알림을 받을 수 있도록 합니다.

기본 단계는 다음과 같습니다.

1. 작업 그룹을 만드는 방법을 설명하는 JSON 파일로 템플릿을 만듭니다.

2. [배포 방법](../../azure-resource-manager/templates/deploy-powershell.md)을 사용하여 템플릿을 배포합니다.

## <a name="resource-manager-templates-for-an-action-group"></a>작업 그룹에 대한 리소스 관리자 템플릿

Resource Manager 템플릿을 사용하여 작업 그룹을 만들려면 `Microsoft.Insights/actionGroups` 형식의 리소스를 만듭니다. 그런 다음 모든 관련된 속성을 입력합니다. 다음은 작업 그룹을 만드는 두 가지 예제 템플릿입니다.

첫 번째 템플릿은 작업 정의가 템플릿에 하드 코딩된 작업 그룹에 대한 Resource Manager 템플릿을 만드는 방법을 설명합니다. 두 번째 템플릿은 템플릿을 배포할 때 입력된 매개 변수로 웹후크 구성 정보를 사용하는 템플릿을 만드는 방법을 설명합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2021-09-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com",
            "useCommonAlertSchema": true

          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com",
            "useCommonAlertSchema": true
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2",
            "useCommonAlertSchema": true
          }
        ],
        "eventHubReceivers": [
          {
            "name": "contosoeventhub1",
            "subscriptionId": "replace with subscription id GUID",
            "eventHubNameSpace": "contosoeventHubNameSpace",
            "eventHubName": "contosoeventHub",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2021-09-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>다음 단계
* [작업 그룹](./action-groups.md)에 대해 자세히 알아보세요.
* [경고](./alerts-overview.md)에 대해 자세히 알아보세요.
* [리소스 관리자 템플릿을 사용하여 경고](./alerts-activity-log.md)를 추가하는 방법을 알아보세요.