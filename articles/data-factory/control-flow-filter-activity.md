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
ms.openlocfilehash: d5a78ca89841abc1d6f060a2f84b7db5ec3758e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831631"
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
