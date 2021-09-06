---
title: Synapse Notebook을 실행하여 데이터 변환
description: 이 문서에서는 Synapse Notebook 활동과 Synapse 파이프라인을 만들고 개발하는 방법을 알아봅니다.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: d103e52ec4a2ba105c6eb5c756ac450a15d7538e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536573"
---
# <a name="transform-data-by-running-a-synapse-notebook"></a>Synapse Notebook을 실행하여 데이터 변환

[!INCLUDE[appliesto-adf-xxx-md](../data-factory/includes/appliesto-xxx-asa-md.md)]

[Synapse 파이프라인](../data-factory/concepts-pipelines-activities.md)의 Azure Synapse Notebook 활동은 Azure Synapse 활동 영역에서 Synapse Notebook을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](../data-factory/transform-data.md) 문서에서 작성합니다. 

## <a name="create-a-synapse-notebook-activity"></a>Synapse Notebook 활동 만들기

Synapse 파이프라인 캔버스 또는 Notebook 편집기에서 직접 Synapse Notebook 활동을 만들 수 있습니다. Synapse Notebook에서 선택한 Spark 풀에서 Synapse Notebook 활동이 실행됩니다. 

### <a name="add-a-synapse-notebook-activity-from-pipeline-canvas"></a>파이프라인 캔버스에서 Synapse Notebook 활동 추가

**활동** 의 **Synapse Notebook** 을 끌어서 Synapse 파이프라인 캔버스에 놓습니다. Synapse Notebook 활동 상자를 선택하고 **설정\** 에서 현재 활동에 대한 Notebook 콘텐츠를 구성합니다. 현재 작업 영역에서 기존 Notebook을 선택하거나 새로 추가할 수 있습니다. 

![screenshot-showing-create-notebook-activity](./media/synapse-notebook-activity/create-synapse-notebook-activity.png)

### <a name="add-a-notebook-to-synapse-pipeline"></a>Synapse 파이프라인에 Notebook 추가

오른쪽 위 모서리에서 **파이프라인에 추가** 단추를 선택하여 기존 파이프라인에 Notebook을 추가하거나 새 파이프라인을 만듭니다.

![screenshot-showing-add-notebook-to-pipeline](./media/synapse-notebook-activity/add-to-pipeline.png)

## <a name="passing-parameters"></a>매개 변수 전달

### <a name="designate-a-parameters-cell"></a>매개 변수 셀 지정

# <a name="classic-notebook"></a>[클래식 Notebook](#tab/classical)

Notebook을 매개 변수화하려면 줄임표(...)를 선택하여 오른쪽 끝에 있는 다른 셀 작업 메뉴에 액세스하면 됩니다. 그런 다음, **매개 변수 셀 설정/해제** 를 선택하여 셀을 매개 변수 셀로 지정합니다.

[![screenshot-showing-toggle-parameter](./media/synapse-notebook-activity/toggle-parameter-cell.png)](./media/synapse-notebook-activity/toggle-parameter-cell.png#lightbox)

# <a name="preview-notebook"></a>[Notebook 미리 보기](#tab/preview)

Notebook을 매개 변수화하려면 줄임표(...)를 선택하여 셀 도구 모음에서 **기타 명령** 에 액세스합니다. 그런 다음, **매개 변수 셀 설정/해제** 를 선택하여 셀을 매개 변수 셀로 지정합니다.

[![screenshot-showing-azure-notebook-toggle-parameter](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png)](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png#lightbox)

---

Azure Data Factory는 매개 변수 셀을 찾고 실행 시 전달되는 매개 변수의 기본값으로 이 값을 사용합니다. 실행 엔진은 기본값을 덮어쓰기 위해 입력 매개 변수를 사용하여 매개 변수 셀 아래에 새 셀을 추가합니다. 매개 변수 셀이 지정되지 않은 경우 삽입된 셀이 Notebook의 맨 위에 삽입됩니다.


### <a name="assign-parameters-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수를 사용하여 Notebook을 만든 후에는 Synapse Notebook 활동을 사용하여 파이프라인에서 이를 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가한 후에는 **설정** 탭의 **기본 매개 변수** 섹션에서 매개 변수 값을 설정할 수 있습니다. 

[![screenshot-showing-assign-a-parameter](./media/synapse-notebook-activity/assign-parameter.png)](./media/synapse-notebook-activity/assign-parameter.png#lightbox)

매개 변수 값을 할당하는 경우 [파이프라인 식 언어](../data-factory/control-flow-expression-language-functions.md) 또는 [시스템 변수](../data-factory/control-flow-system-variables.md)를 사용할 수 있습니다.


## <a name="read-synapse-notebook-cell-output-value"></a>Synapse Notebook 셀 출력 값 읽기

아래 단계에 따라 다음 활동의 Notebook 셀 출력 값을 읽을 수 있습니다.
1. Synapse Notebook 활동에서 [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) API를 호출하여 활동 출력에 표시하려는 값을 반환합니다. 예를 들면 다음과 같습니다.  

    ```python
    mssparkutils.notebook.exit("hello world") 
    ```
    
    Notebook 콘텐츠를 저장하고 파이프라인을 다시 트리거하면, 2단계에서 다음 활동에 사용할 수 있는 exitValue가 Notebook 활동 출력에 포함됩니다. 

2.  Notebook 활동 출력에서 exitValue 속성을 읽습니다. 다음은 Notebook 활동 출력에서 가져온 exitValue가 “hello world”와 같은지 확인하기 위해 사용하는 샘플 식입니다. 

    [![screenshot-showing-read-exit-value](./media/synapse-notebook-activity/synapse-read-exit-value.png)](./media/synapse-notebook-activity/synapse-read-exit-value.png#lightbox)


## <a name="run-another-synapse-notebook"></a>다른 Synapse Notebook 실행 

[%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) 또는 [mssparkutils Notebook 유틸리티](./spark/microsoft-spark-utilities.md#notebook-utilities)를 호출하여 Synapse Notebook 활동에서 다른 Notebook을 참조할 수 있습니다. 모두 중첩 함수 호출을 지원합니다. 시나리오에 따라 다음과 같이 이 두 방법 사이의 주요 차이점을 고려해야 합니다.

- [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference)은 참조된 Notebook의 모든 셀을 %run 셀에 복사하고 변수 컨텍스트를 공유합니다. notebook1이 `%run notebook2`를 통해 notebook2를 참조하고 notebook2는 [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) 함수를 호출할 경우 notebook1의 셀 실행이 중지됩니다. Notebook 파일을 "포함"하려면 %run magic을 사용하는 것이 좋습니다.
- [mssparkutils Notebook 유틸리티](./spark/microsoft-spark-utilities.md#notebook-utilities)는 참조된 Notebook을 메서드나 함수 형태로 호출합니다. 변수 컨텍스트는 공유되지 않습니다. notebook1이 `mssparkutils.notebook.run("notebook2")`를 통해 notebook2를 참조하고 notebook2는 [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) 함수를 호출할 경우 notebook1의 셀 실행이 진행됩니다. Notebook을 "가져오려는” 경우 mssparkutils Notebook 유틸리티를 사용하는 것이 좋습니다.

>[!Note]
> Synapse 파이프라인에서 다른 Synapse Notebook 실행은 미리 보기를 사용하는 Notebook에 대해서만 작동합니다.


## <a name="see-notebook-activity-run-history"></a>Notebook 활동 실행 기록 참조

**모니터** 탭의 **파이프라인 실행** 으로 이동하면 트리거한 파이프라인이 표시됩니다. Notebook 활동을 포함하는 파이프라인을 열어 실행 기록을 확인합니다. 

**Notebook 열기** 단추를 선택하여 셀 입력 및 출력을 모두 포함하는 최신 Notebook 실행 스냅샷을 볼 수 있습니다. 
![see-notebook-activity-history](./media/synapse-notebook-activity/input-output-open-notebook.png)


**입력** 또는 **출력** 단추를 선택하여 Notebook 활동 입력 또는 출력을 확인할 수 있습니다. 파이프라인이 실패하여 사용자 오류가 발생한 경우 **출력** 을 선택하여 **결과** 필드에서 상세 사용자 오류 역추적을 확인합니다.

![screenshot-showing-see-output-user-error](./media/synapse-notebook-activity/notebook-output-user-error.png)


## <a name="synapse-notebook-activity-definition"></a>Synapse Notebook 활동 정의

Synapse Notebook 활동의 샘플 JSON 정의는 다음과 같습니다.

```json
{
    "name": "parameter_test",
    "type": "SynapseNotebook",
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
        "notebook": {
            "referenceName": "parameter_test",
            "type": "NotebookReference"
        },
        "parameters": {
            "input": {
                "value": {
                    "value": "@pipeline().parameters.input",
                    "type": "Expression"
                }
            }
        }
    }
}

```


## <a name="synapse-notebook-activity-output"></a>Synapse Notebook 활동 출력

Synapse Notebook 활동 출력의 샘플 JSON 정의는 다음과 같습니다.

```json

{
    "status": {
        "Status": 1,
        "Output": {
            "status": <livySessionInfo>
            },
            "result": {
                "runId": "<GUID>",
                "runStatus": "Succeed",
                "message": "Notebook execution is in Succeeded state",
                "lastCheckedOn": "2021-03-23T00:40:10.6033333Z",
                "errors": {
                    "ename": "",
                    "evalue": ""
                },
                "sessionId": 4,
                "sparkpool": "sparkpool",
                "snapshotUrl": "https://myworkspace.dev.azuresynapse.net/notebooksnapshot/{guid}",
                "exitCode": "abc" // return value from user notebook via mssparkutils.notebook.exit("abc")
            }
        },
        "Error": null,
        "ExecutionDetails": {}
    },

    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US 2)",
    "executionDuration": 234,
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    },
    "billingReference": {
        "activityType": "ExternalActivity",
        "billableDuration": [
            {
                "meterType": "AzureIR",
                "duration": 0.06666666666666667,
                "unit": "Hours"
            }
        ]
    }
}

```
