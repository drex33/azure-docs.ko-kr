---
title: 파이프라인에 트리거 정보 전달
description: 파이프라인에서 트리거 메타데이터를 참조하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 4d51377daeeb16d7e3e74a77a4379b6fc13cb94b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642699"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>파이프라인 실행의 참조 트리거 메타데이터

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 파이프라인 실행에서 트리거 시작 시간 같은 트리거 메타데이터를 사용할 수 있는 방법을 설명합니다.

파이프라인은 경우에 따라 호출하는 트리거에서 메타데이터를 해석하고 읽어야 합니다. 예를 들어, 창 시작 및 종료 시간을 기반으로 하는 연속 창 트리거 실행을 사용하면 파이프라인이 다른 데이터 조각 또는 폴더를 처리합니다. Azure Data Factory에서 매개 변수화 및 [시스템 변수](control-flow-system-variables.md)를 사용하여 트리거에서 파이프라인으로 메타데이터를 전달합니다.

이 패턴은 [연속 창 트리거](how-to-create-tumbling-window-trigger.md)에서 특히 유용합니다. 여기서 트리거는 창 시작 및 종료 시간을 제공하고, [사용자 지정 이벤트 트리거](how-to-create-custom-event-trigger.md)는 트리거에서 [사용자 지정 정의된 _데이터_ 필드](../event-grid/event-schema.md)의 값을 구문 분석하고 처리합니다.

> [!NOTE]
> 다른 트리거 유형은 서로 다른 메타데이터 정보를 제공합니다. 자세한 내용은 [시스템 변수](control-flow-system-variables.md)를 참조 하세요.

## <a name="data-factory-ui"></a>Data Factory UI

이 섹션에서는 Azure Data Factory 사용자 인터페이스 내에서 트리거에서 파이프라인으로 메타데이터 정보를 전달하는 방법을 설명합니다.

1. **제작 캔버스** 로 이동하여 파이프라인 편집

1. 빈 캔버스를 클릭하여 파이프라인 설정을 가져옵니다. 아무 작업도 선택하지 마십시오. 축소됐을 수 있으므로 캔버스 맨 아래에서 설정 패널을 가져와야 할 수 있습니다.

1. **매개 변수** 섹션을 선택하고 **+ 새로 만들기** 를 선택해 매개 변수 추가

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="파이프라인에서 매개 변수를 정의하는 방법을 보여 주는 파이프라인 설정의 스크린샷":::

1. **+ 트리거** 를 클릭하여 파이프라인에 트리거를 추가합니다.

1. 트리거를 만들거나 파이프라인에 연결하고 **확인** 을 클릭합니다.

1. 다음 페이지에서 각 매개 변수에 대한 트리거 메타데이터를 입력합니다. [시스템 변수](control-flow-system-variables.md)에 정의된 형식을 사용하여 트리거 정보를 검색합니다. 모든 매개 변수에 정보를 입력할 필요는 없고 트리거 메타데이터 값을 가정하는 매개 변수만 입력하면 됩니다. 예를 들어 여기서는 *parameter_1* 에 트리거 실행 시작 시간을 할당합니다.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="파이프라인 매개 변수에 트리거 정보를 전달하는 방법을 보여 주는 트리거 정의 페이지의 스크린샷":::

1. 파이프라인에서 값을 사용하기 위해 파이프라인 정의에서 시스템 변수가 __아닌__ _@pipeline().parameters.parameterName_ 매개 변수를 활용합니다. 예를 들어, 이 경우에는 트리거 시작 시간을 읽기 위해 @pipeline().parameters.parameter_1을 참조합니다.

## <a name="json-schema"></a>JSON 스키마

파이프라인 실행에 트리거 정보를 전달하려면 트리거와 파이프라인 JSON 모두 _매개 변수_ 섹션으로 업데이트해야 합니다.

### <a name="pipeline-definition"></a>파이프라인 정의

**속성** 섹션 아래의 **매개 변수** 섹션에 매개 변수 정의를 추가합니다.

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>트리거 정의

**파이프라인** 섹션 **아래의 매개 변수 섹션에서** 매개 변수 값을 할당합니다. 모든 매개 변수에 정보를 입력할 필요는 없고 트리거 메타데이터 값을 가정하는 매개 변수만 입력하면 됩니다.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>파이프라인에서 트리거 정보 사용

파이프라인에서 값을 사용하기 위해 파이프라인 정의에서 시스템 변수가 __아닌__ _@pipeline().parameters.parameterName_ 매개 변수를 활용합니다.

## <a name="next-steps"></a>다음 단계

트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#trigger-execution)를 참조하세요.
