---
title: Azure Monitor 통합 문서와 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 통해 배포되는 미리 빌드되고 사용자 지정 매개 변수화된 Azure Monitor 통합 문서를 사용하여 복잡한 보고를 간소화합니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: d48cefcae16829bc0a58cdb4f4a43f52d78f8460
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567397"
---
# <a name="programmatically-manage-workbooks"></a>프로그래밍 방식으로 통합 문서 관리

리소스 소유자는 Resource Manager 템플릿을 통해 프로그래밍 방식으로 통합 문서를 만들고 관리하는 옵션이 있습니다.

이는 다음과 같은 시나리오에서 유용합니다.
* 리소스 배포와 함께 조직 또는 도메인별 분석 보고서 배포 예를 들어 새 앱 또는 가상 머신에 대한 조직별 성능 및 오류 통합 문서를 배포할 수 있습니다.
* 기존 리소스용 통합 문서를 사용하여 표준 보고서 또는 대시보드 배포

통합 문서는 원하는 하위 리소스 그룹에 Resource Manager 템플릿에 지정된 콘텐츠로 생성됩니다.

프로그래밍 방식으로 관리할 수 있는 두 가지 통합 문서 리소스는 다음과 같습니다.
* [통합 문서 템플릿](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [통합 문서 인스턴스](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>통합 문서 템플릿을 배포하기 위한 Azure Resource Manager 템플릿

1. 프로그래밍 방식으로 배포하려는 통합 문서를 엽니다.
2. _편집_ 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
3. 도구 모음의 _</>_ 단추를 사용하여 ‘고급 편집기’를 엽니다.
4. ‘갤러리 템플릿’ 탭에 있는지 확인합니다.

    ![갤러리 템플릿 탭](./media/workbooks-automate/gallery-template.png)
1. 갤러리 템플릿의 JSON을 클립보드에 복사합니다.
2. 다음은 통합 문서 템플릿을 Azure Monitor 통합 문서 갤러리에 배포하는 샘플 Azure Resource Manager 템플릿입니다. `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` 대신 복사한 JSON을 붙여넣습니다. 통합 문서 템플릿을 만드는 참조 Azure Resource Manager 템플릿은 [여기](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)에서 찾을 수 있습니다.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. `galleries` 개체에서 `name` 및 `category` 키에 값을 채웁니다. 다음 섹션의 [매개 변수 ](#parameters)를 참조하세요.
2. [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), [명령줄 인터페이스](../../azure-resource-manager/templates/deploy-cli.md), [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)등을 사용하여 이 Azure Resource Manager 템플릿을 배포합니다.
3. Azure Portal을 열고 Azure Resource Manager 템플릿에서 선택한 통합 문서 갤러리로 이동합니다. 예제 템플릿에서 Azure Monitor 통합 문서 갤러리로 이동합니다.
    1. Azure Portal을 열고 Azure Monitor로 이동
    2. 목차에서 `Workbooks` 열기
    3. 갤러리의 `Deployed Templates` 범주에서 템플릿을 찾습니다(자주색 항목 중 하나임).

### <a name="parameters"></a>매개 변수

|매개 변수                |설명                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure Resource Manager에 있는 통합 문서 템플릿 리소스의 이름입니다.                                  |
|`type`                    | 항상 microsoft insights/workbooktemplates                                                            |
| `location`               | 통합 문서가 생성될 Azure 위치                                               |
| `apiVersion`             | 2019-10-17 미리 보기                                                                                     |
| `type`                   | 항상 microsoft insights/workbooktemplates                                                            |
| `galleries`              | 이 통합 문서 템플릿을 표시할 갤러리 세트입니다.                                                |
| `gallery.name`           | 갤러리의 통합 문서 템플릿 식별 이름입니다.                                             |
| `gallery.category`       | 템플릿을 저장할 갤러리의 그룹입니다.                                                     |
| `gallery.order`          | 갤러리의 범주 내에서 템플릿을 표시하는 순서를 결정하는 번호입니다. 하위 순서는 더 높은 우선 순위를 나타냅니다. |
| `gallery.resourceType`   | 갤러리에 해당하는 리소스 종류입니다. 일반적으로 리소스에 해당하는 리소스 종류 문자열입니다(예: microsoft.operationalinsights/workspaces). |
|`gallery.type`            | 통합 문서 형식이라고 하며, 리소스 종류 내에서 갤러리를 구별하는 고유 키입니다. 예를 들어 Application Insights에는 다른 통합 문서 갤러리에 해당하는 `workbook` 및 `tsg` 유형이 있습니다. |

### <a name="galleries"></a>갤러리

| 갤러리                                        | 리소스 종류                                       | 통합 문서 형식 |
|:-----------------------------------------------|:----------------------------------------------------|:--------------|
| Azure Monitor의 통합 문서                     | `Azure Monitor`                                     | `workbook`    |
| Azure Monitor의 VM 인사이트                   | `Azure Monitor`                                     | `vm-insights` |
| Log Analytics 작업 영역의 통합 문서           | `microsoft.operationalinsights/workspaces`          | `workbook`    |
| Application Insights의 통합 문서              | `microsoft.insights/components`                     | `workbook`    |
| Application Insights의 문제 해결 가이드 | `microsoft.insights/components`                     | `tsg`         |
| Application Insights의 사용                  | `microsoft.insights/components`                     | `usage`       |
| Kubernetes 서비스의 통합 문서                | `Microsoft.ContainerService/managedClusters`        | `workbook`    |
| 리소스 그룹의 통합 문서                   | `microsoft.resources/subscriptions/resourcegroups`  | `workbook`    |
| Azure Active Directory의 통합 문서            | `microsoft.aadiam/tenant`                           | `workbook`    |
| 가상 머신의 VM 인사이트                | `microsoft.compute/virtualmachines`                 | `insights`    |
| 가상 머신 확장 집합의 VM 인사이트      | `microsoft.compute/virtualmachinescalesets`         | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>통합 문서 인스턴스를 배포용 Azure Resource Manager 템플릿

1. 프로그래밍 방식으로 배포하려는 통합 문서를 엽니다.
2. _편집_ 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
3. 도구 모음의 _</>_ 단추를 사용하여 ‘고급 편집기’를 엽니다.
4. 편집기에서 _템플릿 유형_ 을 _Resource Manager 템플릿_ 으로 전환합니다.
5. 만들기용 Resource Manager 템플릿이 편집기에 나타납니다. 콘텐츠를 복사하여 그대로 사용하거나 대상 리소스를 배포하는 더 큰 템플릿과 병합합니다.

    ![통합 문서 UI 내에서 Resource Manager 템플릿을 가져오는 방법을 보여 주는 이미지](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>샘플 Azure Resource Manager 템플릿
이 템플릿은 'Hello World!'를 표시하는 간단한 통합 문서를 배포하는 방법을 보여 줍니다.
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>템플릿 매개 변수

| 매개 변수 | 설명 |
| :------------- |:-------------|
| `workbookDisplayName` | 갤러리 또는 저장된 목록에 사용되는 통합 문서의 식별 이름입니다. 리소스 그룹 및 원본 범위에서 고유해야 합니다. |
| `workbookType` | 통합 문서가 표시되는 갤러리입니다. 지원되는 값에는 통합 문서, `tsg`, Azure Monitor 등이 있습니다. |
| `workbookSourceId` | 통합 문서를 연결할 리소스 인스턴스의 ID입니다. 새 통합 문서는 이 리소스 인스턴스와 관련되어 표시됩니다. 예를 들어 _통합 문서_ 에 있는 리소스의 목차에 있습니다. 통합 문서가 Azure Monitor의 통합 문서 갤러리에 표시되도록 하려면 리소스 ID 대신 문자열 _Azure Monitor_ 를 사용합니다. |
| `workbookId` | 이 통합 문서 인스턴스의 고유 guid _[newGuid()]_ 를 사용하여 새 guid를 자동으로 만듭니다. |
| `kind` | 만든 통합 문서가 공유 문서인지 지정하는 데 사용됩니다. 모든 새 통합 문서는 _shared_ 값을 사용합니다. |
| `location` | 통합 문서가 생성될 Azure 위치 _[resourceGroup().location]_ 을 사용하여 리소스 그룹과 같은 위치에 만듭니다. |
| `serializedData` | 통합 문서에 사용할 콘텐츠나 페이로드를 포함합니다. 통합 문서 UI의 Resource Manager 템플릿을 사용하여 값을 가져옵니다. |

### <a name="workbook-types"></a>통합 문서 형식
통합 문서 형식은 새 통합 문서 인스턴스가 표시 될 통합 문서 갤러리 유형을 지정합니다. 표시되는 옵션은 다음과 같습니다.

| 유형 | 갤러리 위치 |
| :------------- |:-------------|
| `workbook` | Application Insights, Azure Monitor 등의 통합 문서 갤러리를 비롯하여 대부분의 보고서에서 사용되는 기본값입니다.  |
| `tsg` | Application Insights의 문제 해결 가이드 갤러리 |
| `usage` | Application Insights의 ‘사용’ 아래의 ‘더 보기’ 갤러리  |

### <a name="working-with-json-formatted-workbook-data-in-the-serializeddata-template-parameter"></a>serializedData 템플릿 매개 변수에서 JSON 형식의 통합 문서 데이터 작업

Azure 통합 문서용 Azure Resource Manager 템플릿을 내보낼 때 내보낸 `serializedData` 템플릿 매개 변수 내에 포함된 고정 리소스 링크가 있는 경우가 많습니다. 여기에는 구독 ID, 리소스 그룹 이름 및 다른 유형의 리소스 ID와 같은 잠재적으로 중요한 값이 포함됩니다.

아래 예에서는 문자열 조작을 사용하지 않고 내보낸 통합 문서의 Azure Resource Manager 템플릿을 사용자 지정하는 방법을 보여 줍니다. 이 예에 표시된 패턴은 Azure Portal에서 내보낸 변경되지 않은 데이터를 사용하기 위한 것입니다. 또한 통합 문서를 프로그래밍 방식으로 관리하는 경우 포함된 중요한 값을 마스킹하는 것이 좋습니다. 따라서 구독 ID와 리소스 그룹이 여기에 마스킹되었습니다. 원시 수신 `serializedData` 값에 다른 수정 사항이 없습니다.

```json
{
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workbookDisplayName": {
      "type": "string"
    },
    "workbookSourceId": {
      "type": "string",
      "defaultValue": "[resourceGroup().id]"
    },
    "workbookId": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    // serializedData from original exported Azure Resource Manager template
    "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":{\"json\":\"Replace with Title\"},\"name\":\"text - 0\"},{\"type\":3,\"content\":{\"version\":\"KqlItem/1.0\",\"query\":\"{\\\"version\\\":\\\"ARMEndpoint/1.0\\\",\\\"data\\\":null,\\\"headers\\\":[],\\\"method\\\":\\\"GET\\\",\\\"path\\\":\\\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups\\\",\\\"urlParams\\\":[{\\\"key\\\":\\\"api-version\\\",\\\"value\\\":\\\"2019-06-01\\\"}],\\\"batchDisabled\\\":false,\\\"transformers\\\":[{\\\"type\\\":\\\"jsonpath\\\",\\\"settings\\\":{\\\"tablePath\\\":\\\"$..*\\\",\\\"columns\\\":[]}}]}\",\"size\":0,\"queryType\":12,\"visualization\":\"map\",\"tileSettings\":{\"showBorder\":false},\"graphSettings\":{\"type\":0},\"mapSettings\":{\"locInfo\":\"AzureLoc\",\"locInfoColumn\":\"location\",\"sizeSettings\":\"location\",\"sizeAggregation\":\"Count\",\"opacity\":0.5,\"legendAggregation\":\"Count\",\"itemColorSettings\":null}},\"name\":\"query - 1\"}],\"isLocked\":false,\"fallbackResourceIds\":[\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/XXXXXXX\"]}",

    // parse the original into a JSON object, so that it can be manipulated
    "parsedData": "[json(variables('serializedData'))]",

    // create new JSON objects that represent only the items/properties to be modified
    "updatedTitle": {
      "content":{
        "json": "[concat('Resource Group Regions in subscription \"', subscription().displayName, '\"')]"
      }
    },
    "updatedMap": {
      "content": {
        "path": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups')]"
      }
    },

    // the union function applies the updates to the original data
    "updatedItems": [
      "[union(variables('parsedData')['items'][0], variables('updatedTitle'))]",
      "[union(variables('parsedData')['items'][1], variables('updatedMap'))]"
    ],

    // copy to a new workbook object, with the updated items
    "updatedWorkbookData": {
      "version": "[variables('parsedData')['version']]",
      "items": "[variables('updatedItems')]",
      "isLocked": "[variables('parsedData')['isLocked']]",
      "fallbackResourceIds": ["[parameters('workbookSourceId')]"]
    },

    // convert back to an encoded string
    "reserializedData": "[string(variables('updatedWorkbookData'))]"
  },
  "resources": [
    {
      "name": "[parameters('workbookId')]",
      "type": "microsoft.insights/workbooks",
      "location": "[resourceGroup().location]",
      "apiVersion": "2018-06-17-preview",
      "dependsOn": [],
      "kind": "shared",
      "properties": {
        "displayName": "[parameters('workbookDisplayName')]",
        "serializedData": "[variables('reserializedData')]",
        "version": "1.0",
        "sourceId": "[parameters('workbookSourceId')]",
        "category": "workbook"
      }
    }
  ],
  "outputs": {
    "workbookId": {
      "type": "string",
      "value": "[resourceId( 'microsoft.insights/workbooks', parameters('workbookId'))]"
    }
  },
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#"
}
```

이 예에서는 다음 단계를 수행하여 내보낸 Azure Resource Manager 템플릿 사용자 지정을 용이하게 합니다.
1. 위의 섹션에 설명된 대로 통합 문서를 Azure Resource Manager 템플릿으로 내보냅니다.
2. 템플릿의 `variables` 섹션에서:
    1. `serializedData` 값을 JSON 개체 변수로 구문 분석하여 통합 문서의 콘텐츠를 나타내는 항목 배열을 포함하는 JSON 구조를 만듭니다.
    2. 수정할 항목/속성만 나타내는 새 JSON 개체를 만듭니다.
    3. `union()` 함수를 사용하여 원래 JSON 항목에 수정 사항을 적용하여 새 JSON 콘텐츠 항목(`updatedItems`) 집합을 프로젝션합니다.
    4. 원래 구문 분석된 데이터의 `updatedItems` 및 `version`/`isLocked` 데이터와 수정된 `fallbackResourceIds` 집합을 포함하는 새 통합 문서 개체 `updatedWorkbookData`를 만듭니다.
    5. 새 JSON 콘텐츠를 다시 새 문자열 변수 `reserializedData`로 직렬화합니다.
3. 원래 `serializedData` 특성 대신 새 `reserializedData` 변수를 사용합니다.
4. 업데이트된 Azure Resource Manager 템플릿을 사용하여 새 통합 문서 리소스를 배포합니다.

### <a name="limitations"></a>제한 사항
기술적인 이유로 이 메커니즘을 사용하여 Application Insights의 _통합 문서_ 갤러리에서 통합 문서 인스턴스를 만들 수 없습니다. 이 제한 사항을 해결하기 위해 노력하고 있습니다. 한편 문제 해결 가이드 갤러리(통합 문서 형식: `tsg`)를 사용하여 Application Insights 관련 통합 문서를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

통합 문서를 사용하여 새로운 [Storage 인사이트 환경](../../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)을 강화하는 방법을 살펴보세요.