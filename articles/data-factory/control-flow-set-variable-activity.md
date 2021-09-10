---
title: 변수 작업 설정
titleSuffix: Azure Data Factory & Azure Synapse
description: 변수 설정 작업을 사용하여 Azure Data Factory 또는 Azure Synapse Analytics 파이프라인에 정의된 기존 변수의 값을 설정하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.openlocfilehash: c9c489ab15f7b6a44e4aadeef8cd98b0d935e1ba
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821647"
---
# <a name="set-variable-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics 변수 작업 설정
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory 또는 Synapse 파이프라인에서 정의된 String, Bool 또는 Array 형식의 기존 변수 값을 설정하려면 변수 설정 작업을 사용합니다.

## <a name="type-properties"></a>형식 속성

속성 | Description | 필수
-------- | ----------- | --------
name | 파이프라인의 작업 이름 | 예
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트 | 아니요
type | **SetVariable** 로 설정해야 합니다. | 예
값 | 변수가 할당된 문자열 리터럴 또는 식 개체 값 | 예
variableName | 이 작업에 의해 설정된 변수의 이름 | 예

## <a name="incrementing-a-variable"></a>변수 증분

변수와 관련된 일반적인 시나리오는 until 또는 foreach 활동 내에서 변수를 반복기로 사용하는 것입니다. 변수 설정 작업에서는 `value` 필드에 설정 중인 변수를 참조할 수 없습니다. 이 제한을 해결하려면 임시 변수를 설정한 다음, 두 번째 변수 설정 작업을 만듭니다. 두 번째 변수 설정 작업은 반복기의 값을 임시 변수로 설정합니다. 

이 패턴의 예는 다음과 같습니다.

![변수 증가](media/control-flow-set-variable-activity/increment-variable.png "변수 증가")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```

변수는 현재 파이프라인 수준에서 범위가 결정됩니다. 즉, 스레드로부터 안전하지 않으며 foreach 루프와 같은 병렬 반복 작업 내에서 액세스하는 경우, 특히 해당 foreach 작업 내에서 값이 수정되는 경우, 원치 않는 동작이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계
관련 제어 흐름 작업에 대해 알아봅니다. 

- [변수 추가 작업](control-flow-append-variable-activity.md)
