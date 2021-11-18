---
title: 카오스 실험을 위한 Azure Resource Manager 템플릿 샘플
description: Azure Chaos Studio 실험을 만드는 샘플 Azure Resource Manager 템플릿
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: dc0777380cf34465fc89a9e2e61d63aec0ca9e8c
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404442"
---
# <a name="resource-manager-template-samples-for-experiments-in-azure-chaos-studio"></a>Azure Chaos Studio에서 실험을 위한 Resource Manager 템플릿 샘플
이 문서에는 Azure Chaos Studio에서 [카오스 실험](chaos-studio-chaos-experiments.md)을 만들기 위한 샘플 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/syntax.md)이 포함되어 있습니다. 각 샘플에는 템플릿 파일과 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

## <a name="create-experiment-sample"></a>실험 만들기(샘플)

이 샘플에서는 단일 대상 리소스와 단일 CPU의 과도한 사용 오류가 있는 카오스 실험을 만듭니다. [REST API](/rest/api/chaosstudio/experiments/create-or-update) 및 [오류 라이브러리](chaos-studio-fault-library.md)를 참조하여 실험을 수정할 수 있습니다.

### <a name="template-file"></a>템플릿 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "experimentName": {
      "type": "string",
      "defaultValue": "simple-experiment",
      "metadata": {
        "description": "A name for the experiment."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "A region where the experiment will be deployed."
      }
    },
    "chaosTargetResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the chaos target. This is the resource ID of the resource being targeted plus the target proxy resource."
      }
    }
  },
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Chaos/experiments",
      "apiVersion": "2021-09-15-preview",
      "name": "parameters('experimentName')",
      "location": "parameters('location')",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "identity": {
          "properties": {
            "type": "SystemAssigned"
          }
        },
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "parameters('chaosTargetResourceId')"
              }
            ]
          }
        ],
        "startOnCreation": "false",
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ]
              }
            ]
          }
        ]
      }
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "experimentName": {
        "value": "my-first-experiment"
      },
      "location": {
        "value": "eastus"
      },
      "chaosTargetResourceId": {
        "value": "eastus"
      }
  }
}
```

## <a name="next-steps"></a>다음 단계

* [Chaos Studio에 대해 자세히 알아보세요](chaos-studio-overview.md).
* [카오스 실험에 대해 자세히 알아보세요](chaos-studio-chaos-experiments.md).
