---
title: Filter 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: 필터 작업은 Azure Data Factory 및 Synapse Analytics 파이프라인에 대 한 입력을 필터링 합니다.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: d4cf128d32e99e796661decfe2547547915f8490
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133569986"
---
# <a name="filter-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Azure Data Factory 및 Synapse Analytics 파이프라인의 필터 작업
입력 배열에 필터 식을 적용하려면 파이프라인에서 필터 작업을 사용할 수 있습니다. 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="syntax"></a>구문

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```
## <a name="create-a-filter-activity-with-ui"></a>UI를 사용 하 여 필터 작업 만들기

파이프라인에서 필터 작업을 사용 하려면 다음 단계를 완료 합니다.

1. 모든 배열 형식 변수 또는 [다른 작업의 출력](how-to-expression-language-functions.md#examples-of-using-parameters-in-expressions) 을 필터 조건에 대 한 입력으로 사용할 수 있습니다.  배열 변수를 만들려면 파이프라인 캔버스의 배경을 선택 하 고 **변수** 탭을 선택 하 여 아래와 같이 배열 형식 변수를 추가 합니다.

   :::image type="content" source="media/control-flow-activities-common/pipeline-array-variable.png" alt-text="파이프라인에 배열 형식 변수가 추가 된 빈 파이프라인 캔버스를 표시 합니다.":::

1. 파이프라인 활동 창에서 _필터_ 를 검색 하 고 필터 활동을 파이프라인 캔버스로 끌어 옵니다.
1. 캔버스에서 새 필터 작업을 선택 하지 않은 경우 해당 작업을 선택 하 고 해당 **설정** 탭을 선택 하 여 세부 정보를 편집 합니다.

   :::image type="content" source="media/control-flow-filter-activity/filter-activity.png" alt-text="필터 작업에 대 한 UI를 표시 합니다.":::

1. **항목** 필드를 선택한 다음 **동적 콘텐츠 추가** 링크를 선택 하 여 동적 콘텐츠 편집기 창을 엽니다.

   :::image type="content" source="media/control-flow-filter-activity/add-dynamic-content-link.png" alt-text="&nbsp; &nbsp; 항목 속성에 대 한 동적 콘텐츠 추가 링크를 표시 합니다.":::

1. 동적 콘텐츠 편집기에서 필터링 할 입력 배열을 선택 합니다.  이 예에서는 첫 번째 단계에서 만든 변수를 선택 합니다.

   :::image type="content" source="media/control-flow-activities-common/add-dynamic-content-pane.png" alt-text="선택한 첫 번째 단계에서 만든 변수를 사용 하 여 동적 콘텐츠 편집기를 표시 합니다.":::

1. 위에서 설명한 것 처럼 동적 콘텐츠 편집기를 사용 하 여 Condition 속성에 대 한 필터 조건을 지정 합니다.
1. 필터 작업의 출력을 [ForEach 활동과](control-flow-for-each-activity.md)같은 다른 활동에 대 한 입력으로 사용할 수 있습니다.

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | `Filter` 작업의 이름입니다. | String | 예
type | **필터** 로 설정되어야 합니다. | String | 예
condition(조건) | 입력을 필터링하는 데 사용할 조건입니다. | 식 | Yes
items | 필터를 적용해야 하는 입력 배열입니다. | 식 | 예

## <a name="example"></a>예제

이 예제에서 파이프라인에는 **필터** 및 **ForEach** 라는 두 개의 작업이 있습니다. 필터 작업은 3보다 큰 값의 항목에 대한 입력 배열을 필터링하도록 구성됩니다. 그런 다음, ForEach 작업은 필터링된 값에 대해 반복하고 변수 **test** 를 현재 값으로 설정합니다.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>다음 단계
지원되는 다른 제어 흐름 작업을 참조하세요. 

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
