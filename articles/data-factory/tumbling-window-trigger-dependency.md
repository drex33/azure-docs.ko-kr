---
title: 연속 창 트리거 종속성 만들기
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics에서 연속 창 트리거에 대 한 종속성을 만드는 방법에 대해 알아봅니다.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 89a6db7fcfd78ec2e1115256cc162a91cc9180da
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805931"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>연속 창 트리거 종속성 만들기
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 항목에서는 연속 창 트리거에 대한 종속성을 만드는 단계를 제공합니다. 연속 창 트리거에 대한 자세한 내용은 [연속 창 트리거를 만드는 방법](how-to-create-tumbling-window-trigger.md)을 참조하세요.

종속성 체인을 작성 하 고 서비스 내에서 다른 트리거를 성공적으로 실행 한 후에만 트리거가 실행 되도록 하기 위해이 고급 기능을 사용 하 여 연속 창 종속성을 만듭니다.

연속 창 트리거를 사용 하 여 종속 파이프라인을 만드는 방법에 대 한 데모를 보려면 다음 비디오를 시청 하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-ui"></a>UI에서 종속성 만들기

트리거에 대한 종속성을 만들려면 **트리거 > 고급 > 새로 만들기** 를 선택한 다음, 적절한 오프셋 및 크기의 종속 대상 트리거를 선택합니다. **마침** 을 선택 하 고 종속성에 대 한 변경 내용을 게시 하 여 적용 합니다.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png" alt-text="종속성 만들기":::

## <a name="tumbling-window-dependency-properties"></a>연속 창 종속성 속성

연속 창 트리거 종속성의 속성은 다음과 같습니다.

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

다음 표에서는 연속 창 종속성을 정의하는 데 필요한 특성 목록을 제공합니다.

| **속성 이름** | **설명**  | **형식** | **필수** |
|---|---|---|---|
| type  | 이 드롭다운에는 기존의 연속 창 트리거가 모두 표시됩니다. 종속성을 적용할 트리거를 선택합니다.  | TumblingWindowTriggerDependencyReference 또는 SelfDependencyTumblingWindowTriggerReference | 예 |
| offset | 종속성 트리거의 오프셋입니다. 시간 범위 형식으로 값을 제공합니다. 음수 및 양수 오프셋이 둘 다 허용됩니다. 이 속성은 트리거가 자체 종속적인 경우에만 필수이고, 다른 모든 경우에는 선택 사항입니다. 자체 종속성은 항상 음수 오프셋이어야 합니다. 값을 지정하지 않으면 창은 트리거 자체와 동일합니다. | Timespan<br/>(hh:mm:ss) | 자체 종속성: 예<br/>기타: 아니요 |
| 크기 | 종속성 연속 창의 크기입니다. 양의 시간 범위 값을 제공합니다. 이 속성은 선택 사항입니다. | Timespan<br/>(hh:mm:ss) | 예  |

> [!NOTE]
> 연속 창 트리거는 최대 5가지의 트리거에 종속될 수 있습니다.

## <a name="tumbling-window-self-dependency-properties"></a>연속 창 자체 종속성 속성

이전 창이 성공적으로 완료될 때까지 트리거가 다음 창으로 진행되지 않는 시나리오에서는 자체 종속성을 빌드합니다. 이전 시간에서 자신의 실행 성공 여부에 종속되는 자체 종속성 트리거는 다음 코드에 표시된 속성을 갖습니다.

> [!NOTE]
> 트리거된 파이프라인이 이전에 트리거된 창의 파이프라인의 출력을 사용하는 경우에는 연속 창 트리거 자체 종속성만 사용하는 것이 좋습니다. 병렬 트리거 실행을 제한하려면 최대 트리거 동시성을 설정합니다.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>사용 시나리오 및 예제

아래에는 연속 창 종속성 속성의 시나리오 및 사용에 대한 예시가 나와 있습니다.

### <a name="dependency-offset"></a>종속성 오프셋

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png" alt-text="오프셋 예제":::

### <a name="dependency-size"></a>종속성 크기

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png" alt-text="크기 예제":::

### <a name="self-dependency"></a>자체 종속성

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png" alt-text="자체 종속성":::

### <a name="dependency-on-another-tumbling-window-trigger"></a>다른 연속 창 트리거에 대한 종속성

지난 7일간의 출력을 집계하고 7일 롤링 창 스트림을 생성하는 다른 일일 작업에 종속된 일일 원격 분석 처리 작업입니다.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png" alt-text="종속성 예제":::

### <a name="dependency-on-itself"></a>자체 종속성

작업의 출력 스트림에 간격이 없는 일일 작업입니다.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png" alt-text="자체 종속성 예제":::

## <a name="monitor-dependencies"></a>종속성 모니터링

트리거 실행 모니터링 페이지에서 종속성 체인 및 해당 창을 모니터링할 수 있습니다. **모니터링 > 트리거 실행** 으로 이동합니다. 연속 창 트리거에 종속성이 있는 경우 트리거 이름에 종속성 모니터링 보기에 대한 하이퍼링크가 포함됩니다.  

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png" alt-text="트리거 실행 모니터링":::

트리거 이름을 클릭하여 트리거 종속성을 확인합니다. 오른쪽 패널에 자세한 트리거 실행 정보가 표시됩니다(예: RunID, window time, status 등).

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png" alt-text="종속성 모니터링 목록 보기":::

종속성의 상태와 각 종속 트리거의 창을 확인할 수 있습니다. 종속성 트리거 중 하나에 오류가 발생할 경우 종속 트리거가 실행되려면 해당 트리거를 성공적으로 다시 실행해야 합니다.

연속 창 트리거는 시간 초과될 때까지 '7일' 동안 종속성을 기다립니다. 7일 후에는 트리거가 실행되지 않습니다.

보다 시각적으로 트리거 종속성 일정을 확인하려면 Gantt 보기를 선택합니다.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png" alt-text="종속성 모니터링 Gantt 차트":::

투명 상자에는 각 다운스트림 종속 트리거에 대한 종속성 창이 표시되고 위의 단색 상자에는 개별 창 실행이 표시됩니다. Gantt 차트 보기를 해석하기 위한 몇 가지 팁은 다음과 같습니다.

* 종속 창이 보류 중 또는 실행 중 상태에 있으면 투명 상자가 파란색으로 표시됩니다.
* 종속 트리거의 모든 창이 성공하면 투명 상자가 녹색으로 바뀝니다.
* 일부 종속 창이 실패할 경우 투명 상자가 빨간색으로 표시됩니다. 빨간색 상자를 찾아 오류 창이 실행되는지 확인합니다.

Gantt 차트 보기에서 창을 다시 실행하려면 창의 단색 상자를 선택합니다. 그러면 작업 패널이 나타나 세부 정보 및 다시 실행 옵션이 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [연속 창 트리거를 만드는 방법](how-to-create-tumbling-window-trigger.md)을 검토합니다.
