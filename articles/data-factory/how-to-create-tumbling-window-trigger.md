---
title: 연속 창 트리거 만들기
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics의 연속 창에서 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: b4a2e86c66584f555dd88dfd8e3d3b8b0fac5858
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822745"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>연속 창에 따라 파이프라인을 실행하는 트리거 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 연속 창 트리거를 만들고 시작 및 모니터링하는 단계를 제공합니다. 트리거 및 지원되는 형식에 대한 일반적인 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

연속 창 트리거는 상태를 유지하면서 지정된 시작 시간부터 주기적 시간 간격으로 실행되는 트리거 유형입니다. 연속 창은 고정된 크기의 겹치지 않고 연속적인 일련의 시간 간격입니다. 연속 창 트리거는 파이프라인과 1:1 관계이며 단일 파이프라인만 참조할 수 있습니다. 연속 창 트리거는 복잡한 시나리오에 대한 기능 모음을 제공하는 일정 트리거([다른 연속 창 트리거에 대한 종속성](#tumbling-window-trigger-dependency), [실패한 작업 재실행](tumbling-window-trigger-dependency.md#monitor-dependencies) 및 [파이프라인에 대한 사용자 재시도 설정](#user-assigned-retries-of-pipelines))의 보다 뛰어난 대안입니다. 일정 트리거와 연속 창 트리거의 차이점을 자세히 이해하려면 [여기](concepts-pipeline-execution-triggers.md#trigger-type-comparison)를 방문하세요.

## <a name="ui-experience"></a>UI 환경

1. UI에서 연속 창 트리거를 만들려면 **트리거** 탭을 선택한 다음, **새로 만들기** 를 선택합니다. 
1. 트리거 구성 창이 열리면 **연속 창** 을 선택한 다음 연속 창 트리거 속성을 정의합니다. 
1. 완료되면 **저장** 을 선택합니다.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png" alt-text="Azure Portal에서 연속 창 트리거 만들기":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
:::image type="content" source="media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger-synapse.png" alt-text="Azure Portal에서 연속 창 트리거 만들기":::

---

## <a name="tumbling-window-trigger-type-properties"></a>연속 창 트리거 형식 속성

연속 창에는 다음과 같은 트리거 형식 속성이 있습니다.

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
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
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

다음 테이블은 연속 창 트리거의 되풀이 및 일정 계획과 관련된 주요 JSON 요소의 대략적인 개요를 제공합니다.

| JSON 요소 | Description | Type | 허용되는 값 | 필수 |
|:--- |:--- |:--- |:--- |:--- |
| **type** | 트리거의 유형입니다. 형식은 고정된 값 “TumblingWindowTrigger”입니다. | String | "TumblingWindowTrigger" | 예 |
| **runtimeState** | 트리거 런타임의 현재 상태입니다.<br/>**참고**: 이 요소는 \<readOnly>입니다. | String | "시작된," "중지된," "사용 안 함" | 예 |
| **frequency** | 트리거를 되풀이하는 빈도 단위(시간 또는 분)를 나타내는 문자열입니다. **startTime** 날짜 값이 **frequency** 값보다 더 세부적인 경우 **startTime** 날짜는 시간 경계를 계산할 때 고려됩니다. 예를 들어, **frequency** 값이 시간이고 **startTime** 값이 2017-09-01T10:10:10Z이면 첫 번째 기간은 (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z)입니다. | String | "minute," "hour"  | 예 |
| **interval** | 트리거가 실행되는 빈도를 결정하는 **frequency** 값에 대한 간격을 나타내는 양의 정수입니다. 예를 들어 **interval** 이 3이고 **frequency** 가 "시간"인 경우 트리거가 세 시간마다 되풀이됩니다. <br/>**참고**: 최소 창 간격은 5분입니다. | 정수 | 양의 정수 | 예 |
| **startTime**| 첫 번째 발생이며 과거일 수 있습니다. 첫 번째 트리거 간격은 (**startTime**, **startTime** + **interval**)입니다. | DateTime | DateTime 값입니다. | 예 |
| **endTime**| 마지막 발생이며 과거일 수 있습니다. | DateTime | DateTime 값입니다. | 예 |
| **delay** | 시간에서 데이터 처리의 시작 지점을 지연하는 시간입니다. 예상 실행 시간 및 **delay** 시간 후에 파이프라인이 실행되기 시작됩니다. **delay** 는 새 실행을 트리거하기 전에 트리거가 기한 이후 대기하는 기간을 정의합니다. **delay** 는 **startTime** 시간을 변경하지 않습니다. 예를 들어 00:10:00이라는 **delay** 값은 10분을 지연한다는 의미입니다. | Timespan<br/>(hh:mm:ss)  | 기본값이 00:00:00인 시간 범위 값입니다. | 예 |
| **maxConcurrency** | 준비된 시간에 발생하는 동시 트리거 실행 수입니다. 예를 들어 채우기를 백업 하려면 24 windows에서 어제 결과 대 한 매시간 실행 됩니다. **maxConcurrency** = 10이면 처음 10개 시간(00:00-01:00 - 09:00-10:00)에만 트리거 이벤트가 발생합니다. 처음 10개의 트리거된 파이프라인 실행이 완료되면 다음 10개 시간(10:00-11:00 - 19:00-20:00)에 트리거 실행이 발생합니다. **maxConcurrency** = 10이라는 이 예제에서 10개 시간이 준비되면 총 10개의 파이프라인이 실행됩니다. 1개의 시간이 있는 경우 1개의 파이프라인만이 실행됩니다. | 정수 | 1~50 사이의 정수입니다. | 예 |
| **retryPolicy: Count** | 파이프라인 실행이 "실패함"으로 표시되기 전에 다시 시도한 횟수입니다.  | 정수 | 기본값이 0(다시 시도 안 함)인 정수입니다. | 예 |
| **retryPolicy: intervalInSeconds** | 시간(초)로 지정된 다시 시도 사이의 지연입니다. | 정수 | 기본값이 30인 시간(초)입니다. | 예 |
| **dependsOn: type** | TumblingWindowTriggerReference의 형식입니다. 종속성이 설정된 경우 필수 사항입니다. | String |  “TumblingWindowTriggerDependencyReference”, “SelfDependencyTumblingWindowTriggerReference” | 예 |
| **dependsOn: size** | 종속성 연속 창의 크기입니다. | Timespan<br/>(hh:mm:ss)  | 기본값이 자식 트리거의 창 크기인 양수 timespan 값입니다.  | 예 |
| **dependsOn: offset** | 종속성 트리거의 오프셋입니다. | Timespan<br/>(hh:mm:ss) |  자체 종속성에서 음수가 되어야 하는 timespan 값입니다. 값을 지정하지 않으면 창은 트리거 자체와 동일합니다. | 자체 종속성: 예<br/>기타: 아니요  |

> [!NOTE]
> 연속 창 트리거를 게시한 후에는 **간격** 및 **빈도** 를 편집할 수 없습니다.

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart 및 WindowEnd 시스템 변수

**파이프라인** 정의에서 연속 창 트리거의 **WindowStart** 및 **WindowEnd** 시스템 변수를 사용할 수 있습니다(즉, 쿼리 부분의 경우). **트리거** 정의에 있는 파이프라인에 매개 변수로 시스템 변수를 전달합니다. 다음 예제에서는 이러한 변수를 매개 변수로 전달하는 방법을 보여줍니다.

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

그런 다음 파이프라인 정의에서 **WindowStart** 및 **WindowEnd** 값을 사용하려면 "MyWindowStart" 및 "MyWindowEnd" 매개 변수를 적절하게 사용합니다.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>백필 시나리오에서 Windows 실행 순서

트리거의 startTime이 과거인 경우 수식 M=(CurrentTime - TriggerStartTime)/TumblingWindowSize를 기반으로 트리거가 병렬로 실행되는 {M} 백필(과거)을 생성하여 차후의 실행을 수행하기 전에 트리거 동시성을 보장합니다. 창의 실행 순서는 확정적이며 가장 오래된 간격부터 최신 간격 순으로 실행됩니다. 현재 이 동작을 수정할 수 없습니다.

### <a name="existing-triggerresource-elements"></a>기존 TriggerResource 요소

기존 **TriggerResource** 요소의 업데이트에는 다음 사항이 적용됩니다.

* 트리거를 만든 후에는 **간격** 요소와 함께 해당 트리거의 **빈도** 요소(또는 창 크기) 값을 변경할 수 없습니다. 이는 triggerRun 재실행 및 종속성 평가의 올바른 작동을 위해 필수적입니다.
* 트리거의 **endTime** 요소에 대한 값이 변경(추가 또는 업데이트)되는 경우 이미 처리된 시간 상태가 다시 설정되지 *않습니다*. 트리거는 새 **endTime** 값을 사용합니다. 시간 전에 새 **endTime** 값이 이미 실행되는 경우 트리거가 중지됩니다. 그렇지 않으면 새 **endTime** 값이 발견될 때 트리거가 중지됩니다.

### <a name="user-assigned-retries-of-pipelines"></a>사용자가 파이프라인 재시도 할당

파이프라인 실패가 발생하는 경우 연속 창 트리거에서 사용자의 개입 없이 동일한 입력 매개 변수를 사용하여 참조된 파이프라인의 실행을 자동으로 재시도할 수 있습니다. 트리거 정의에서 “retryPolicy” 속성을 사용하여 이를 지정할 수 있습니다.

### <a name="tumbling-window-trigger-dependency"></a>연속 창 트리거 종속성 만들기

데이터 팩터리에서 다른 연속 창 트리거를 성공적으로 실행한 후에만 연속 창 트리거가 실행되도록 하려면 [연속 창 트리거 종속성](tumbling-window-trigger-dependency.md)을 만듭니다.

### <a name="cancel-tumbling-window-run"></a>연속 창 실행 취소

특정 창이 ‘대기 중’, ‘종속성 대기 중’ 또는 ‘실행 중’ 상태에 있는 경우 연속 창 트리거에 대한 실행을 취소할 수 있습니다.  

* 창이 **실행 중** 상태에 있는 경우 연결된 ‘파이프라인 실행’을 취소하면 트리거 실행이 ‘취소됨’으로 표시됩니다. 
* 창이 **대기 중** 또는 **종속성 대기 중** 상태에 있는 경우 다음과 같이 모니터링에서 창을 취소할 수 있습니다.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png" alt-text="모니터링 페이지에서 연속 창 트리거 취소":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger-synapse.png" alt-text="모니터링 페이지에서 연속 창 트리거 취소":::

---

취소된 창을 다시 실행할 수도 있습니다. 재실행은 트리거의 ‘최신’ 게시된 정의를 사용하며, 지정된 창에 대한 종속성이 재실행 시에 ‘재평가’됩니다. 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png" alt-text="이전에 취소된 실행에 대한 연속 창 트리거 재실행":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger-synapse.png" alt-text="이전에 취소된 실행에 대한 연속 창 트리거 재실행":::

---

## <a name="sample-for-azure-powershell"></a>Azure PowerShell의 샘플

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 섹션에서는 Azure PowerShell을 사용하여 트리거를 만들고 시작하고 모니터링하는 방법을 보여 줍니다.

1. 다음 내용을 포함하는 **MyTrigger.json** 이라는 JSON 파일을 C:\ADFv2QuickStartPSH\ 폴더에 만듭니다.

    > [!IMPORTANT]
    > JSON 파일을 저장하기 전에 **startTime** 요소의 값을 현재 UTC 시간으로 설정합니다. **endTime** 요소의 값을 현재 UTC 시간의 한 시간 이후로 설정합니다.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. **Set-AzDataFactoryV2Trigger** cmdlet을 사용하여 트리거를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. **Get-AzDataFactoryV2Trigger** cmdlet을 사용하여 트리거의 상태가 **중지됨** 인지 확인합니다.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. **Start-AzDataFactoryV2Trigger** cmdlet을 사용하여 트리거를 시작합니다.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. **Get-AzDataFactoryV2Trigger** cmdlet을 사용하여 트리거의 상태가 **시작됨** 인지 확인합니다.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. **Get-AzDataFactoryV2TriggerRun** cmdlet을 사용하여 Azure PowerShell에서 트리거 실행을 가져옵니다. 트리거 실행에 대한 정보를 가져오려면 다음 명령을 주기적으로 실행합니다. **TriggerRunStartedAfter** 및 **TriggerRunStartedBefore** 값을 업데이트하여 트리거 정의의 값과 일치시킵니다.

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

Azure Portal에서 트리거 실행 및 파이프라인 실행을 모니터링하려면 [파이프라인 실행 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#trigger-execution)를 참조하세요.
* [연속 창 트리거 종속성을 만듭니다](tumbling-window-trigger-dependency.md).
* 파이프라인에서 트리거 메타데이터를 참조하는 방법에 대해 자세히 알아보려면 [파이프라인 실행의 트리거 메타데이터 참조](how-to-use-trigger-parameterization.md)를 참조하세요.
