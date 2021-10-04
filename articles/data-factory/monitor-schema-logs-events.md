---
title: 로그 및 이벤트 스키마
description: 모니터링을 위해 Azure Data Factory 로그 및 이벤트에서 사용 하는 스키마에 대해 알아봅니다.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4b64818b505863ad7f7e2878640eeb28224af2f1
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400195"
---
# <a name="schema-of-logs-and-events"></a>로그 및 이벤트 스키마

이 문서에서는 모니터링을 위해 Azure Data Factory 로그 및 이벤트에서 사용 하는 스키마에 대해 설명 합니다.

## <a name="monitor-schema"></a>모니터 스키마
모니터링에 사용 되는 특성의 목록은 다음과 같습니다.

### <a name="activity-run-log-attributes"></a>활동 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 속성 | 유형 | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 이벤트 시간(시간대 UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)입니다. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 활동 실행의 ID입니다. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `ActivityRuns`로 설정합니다. | `ActivityRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 활동의 이름과 그 상태입니다. 활동이 시작 하트비트인 경우 속성 값은 `MyActivity -`입니다. 활동이 끝 하트비트인 경우 속성 값은 `MyActivity - Succeeded`입니다. | `MyActivity - Succeeded` |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**activityName**| String | 작업의 이름입니다. | `MyActivity` |
|**start**| String | 활동 실행의 시작 시간(시간대 UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | 활동 실행의 종료 시간(시간대 UTC 형식)입니다. 진단 로그에 활동이 시작되었지만 아직 종료되지 않았다고 표시되는 경우 속성 값은 `1601-01-01T00:00:00Z`입니다. | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-log-attributes"></a>파이프라인 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 속성 | 유형 | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 이벤트 시간(시간대 UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)입니다. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 파이프라인의 이름과 상태입니다. 파이프라인 실행이 완료되면 속성 값은 `Pipeline - Succeeded`입니다. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**start**| String | 활동 실행의 시작 시간(시간대 UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | 활동 실행의 종료 시간(시간대 UTC 형식)입니다. 진단 로그에 활동이 시작되었지만 아직 종료되지 않은 것으로 표시되면 속성 값은 `1601-01-01T00:00:00Z`입니다.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | 파이프라인 실행의 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed`입니다. | `Succeeded`|

### <a name="trigger-run-log-attributes"></a>트리거 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| 속성 | 유형 | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | 이벤트 시간(시간대 UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)입니다. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| 트리거 실행의 ID입니다. | `08587023010602533858661257311` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 트리거의 이름과 트리거가 성공적으로 실행되었는지 여부를 나타내는 최종 상태입니다. 하트비트가 성공한 경우 속성 값은 `MyTrigger - Succeeded`입니다. | `MyTrigger - Succeeded` |
|**triggerName**| String | 트리거의 이름입니다. | `MyTrigger` |
|**triggerType**| String | 트리거의 유형입니다. 가능한 속성 값은 `Manual Trigger` 및 `Schedule Trigger`입니다. | `ScheduleTrigger` |
|**triggerEvent**| String | 트리거의 이벤트입니다. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | 트리거 실행 시작 시간(시간대 UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | 트리거가 성공적으로 실행되었는지 여부를 보여주는 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed`입니다. | `Succeeded`|

### <a name="ssis-integration-runtime-log-attributes"></a>SSIS 통합 런타임 로그 특성

다음은 SSIS (SQL Server Integration Services) IR (통합 런타임) 시작, 중지 및 유지 관리 작업의 로그 특성입니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| 속성                   | 유형   | Description                                                   | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`) | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | SSIS IR 작업의 이름                            | `Start/Stop/Maintenance/Heartbeat` |
| **category**               | String | 진단 로그의 범주                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | 데이터 팩터리의 이름입니다.                                          | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                      | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                  | `Informational` |
| **resultType**             | String | SSIS IR 작업의 결과                          | `Started/InProgress/Succeeded/Failed/Healthy/Unhealthy` |
| **message**                | String | SSIS IR 작업의 출력 메시지                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | 데이터 팩터리 리소스의 고유 ID입니다.                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-message-context-log-attributes"></a>SSIS 이벤트 메시지 컨텍스트 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 이벤트 메시지와 관련된 조건의 로그 특성은 다음과 같습니다. 이러한 정보는 여러 SSIS 패키지 속성의 런타임 값을 보여 주는 [SSISDB (ssis 카탈로그) 이벤트 메시지 컨텍스트 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-event-message-context) 유사한 정보를 전달 합니다. `Basic/Verbose`로깅 수준을 선택 하 고 디버깅 또는 준수 검사에 유용한 경우에 생성 됩니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| 속성                   | 유형   | Description                                                          | 예제                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | 진단 로그의 범주                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 데이터 팩터리 이름                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                             | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                         | `Informational` |
| **operationId**            | String | SSISDB에서 특정 작업을 추적하기 위한 고유 ID          | `1`(1은 T-SQL 통해 SSISDB에 *저장/호출되지 않은* 패키지와 관련된 작업을 의미합니다.) |
| **contextDepth**           | String | 이벤트 메시지 컨텍스트의 깊이                              | `0` (0은 패키지 실행이 시작되기 전에 컨텍스트를 의미하고, 1은 오류가 발생할 때 컨텍스트를 의미하며, 컨텍스트가 오류에서 더 커지면 증가합니다.) |
| **packagePath**            | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 경로      | `\Package` |
| **contextType**            | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 유형      | `60` (더 [많은 컨텍스트 형식을](/sql/integration-services/system-views/catalog-event-message-context#remarks)참조하세요.) |
| **contextSourceName**      | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 이름      | `MyPackage` |
| **contextSourceId**        | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 고유 ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | 이벤트 메시지 컨텍스트 원본의 패키지 속성 이름   | `DelayValidation` |
| **propertyValue**          | String | 이벤트 메시지 컨텍스트 원본의 패키지 속성 값  | `False` |
| **resourceId**             | String | 데이터 팩터리 리소스의 고유 ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-event-messages-log-attributes"></a>SSIS 이벤트 메시지 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 이벤트 메시지의 로그 특성은 다음과 같습니다. 이벤트 메시지의 자세한 텍스트 또는 메타데이터를 보여 주는 [SSISDB 이벤트 메시지 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-event-messages) 비슷한 정보를 전달합니다. `None`을 제외한 모든 로깅 수준에서 생성됩니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| 속성                   | 유형   | Description                                                        | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | 진단 로그의 범주                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 데이터 팩터리 이름                                               | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                           | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                       | `Informational` |
| **operationId**            | String | SSISDB에서 특정 작업을 추적하기 위한 고유 ID        | `1`(1은 T-SQL 통해 SSISDB에 *저장/호출되지 않은* 패키지와 관련된 작업을 의미합니다.) |
| **messageTime**            | String | 이벤트 메시지가 생성된 시간(UTC 형식)          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | 이벤트 메시지의 유형                                     | `70` (더 [많은 메시지 유형을](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)참조하세요.) |
| **messageSourceType**      | String | 이벤트 메시지 원본의 유형                              | `20` (더 [많은 메시지 원본 유형을](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)참조하세요.) |
| **message**                | String | 이벤트 메시지의 텍스트                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | 실행된 패키지 파일의 이름                             | `MyPackage.dtsx` |
| **eventName**              | String | 관련 런타임 이벤트의 이름                                 | `OnPreValidate` |
| **messageSourceName**      | String | 이벤트 메시지 원본인 패키지 구성 요소의 이름         | `Data Flow Task` |
| **messageSourceId**        | String | 이벤트 메시지 원본인 패키지 구성 요소의 고유 ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | 이벤트 메시지 원본인 데이터 흐름 구성 요소의 이름       | `SSIS.Pipeline` |
| **packagePath**            | String | 이벤트 메시지 원본인 패키지 개체의 경로            | `\Package\Data Flow Task` |
| **executionPath**          | String | 부모 패키지에서 실행된 구성 요소까지의 전체 경로            | `\Transformation\Data Flow Task` 이 경로는 구성 요소 반복도 캡처합니다. |
| **threadId**               | String | 이벤트 메시지가 로그될 때 실행되는 스레드의 고유 ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 실행 파일 통계 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 실행 파일 통계의 로그 특성은 다음과 같습니다. 여기서 실행 파일은 패키지 제어 흐름의 컨테이너 또는 태스크입니다. 해당 반복을 포함 하 여 실행 중인 각 실행 파일의 행을 보여 주는 [SSISDB 실행 통계 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-executable-statistics) 유사한 정보를 전달 합니다. 이러한 `None` 작업은 작업 수준 병목 현상 또는 실패를 식별 하는 데 유용 하며 모든 로깅 수준에서 생성 됩니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| 속성                   | 유형   | Description                                                      | 예제                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | 진단 로그의 범주                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 데이터 팩터리의 이름입니다.                                             | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                         | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                     | `Informational` |
| **executionId**            | String | SSISDB에서 특정 실행을 추적하기 위한 고유 ID      | `1`(1은 T-SQL를 통해 SSISDB/호출 *된 패키지와* 관련 된 실행을 나타냅니다.) |
| **executionPath**          | String | 부모 패키지에서 실행된 구성 요소까지의 전체 경로          | `\Transformation\Data Flow Task` 이 경로는 구성 요소 반복도 캡처합니다. |
| **startTime**              | String | 실행 파일이 실행 전 단계로 들어가는 시간(UTC 형식)  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 실행 파일이 실행 후 단계로 들어가는 시간(UTC 형식) | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | 실행 파일의 실행 시간(밀리초)                   | `1,125` |
| **executionResult**        | String | 실행 파일을 실행한 결과                                 | `0` 0은 성공, 1은 실패, 2는 완료를 나타내고, 3은 취소를 나타냅니다. |
| **executionValue**         | String | 실행 파일을 실행하여 반환된 사용자 정의 값            | `1` |
| **resourceId**             | String | 데이터 팩터리 리소스의 고유 ID입니다.                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 실행 구성 요소 단계 로그 특성

Ssis IR에서 SSIS 패키지 실행에 의해 생성 되는 데이터 흐름 구성 요소에 대 한 런타임 통계의 로그 특성은 다음과 같습니다. 모든 실행 단계에서 데이터 흐름 구성 요소에 소요 된 시간을 보여 주는 [SSISDB 실행 구성 요소 단계 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-execution-component-phases) 유사한 정보를 전달 합니다. `Performance/Verbose` 로깅 수준을 선택하면 생성되며 데이터 흐름 실행 통계를 캡처하는 데 유용합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 속성                   | 유형   | Description                                                         | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | 진단 로그의 범주                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | 특정 작업을 추적하기 위한 고유 ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | 데이터 팩터리의 이름입니다.                                                | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                            | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                        | `Informational` |
| **executionId**            | String | SSISDB에서 특정 실행을 추적하기 위한 고유 ID         | `1`(1은 T-SQL를 통해 SSISDB/호출 *된 패키지와* 관련 된 실행을 나타냅니다.) |
| **packageName**            | String | 실행된 패키지 파일의 이름                              | `MyPackage.dtsx` |
| **taskName**               | String | 실행된 데이터 흐름 태스크의 이름                                 | `Data Flow Task` |
| **subcomponentName**       | String | 데이터 흐름 구성 요소의 이름                                     | `Derived Column` |
| **phase**                  | String | 실행 단계의 이름                                         | `AcquireConnections` |
| **startTime**              | String | 실행 단계가 시작되는 시간(UTC 형식)                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 실행 단계가 끝나는 시간(UTC 형식)                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | 데이터 흐름 태스크의 실행 경로                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | 데이터 팩터리 리소스의 고유 ID입니다.                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 실행 데이터 통계 로그 특성

SSIS IR에서 SSIS 패키지 실행을 통해 생성되는 업스트림에서 다운스트림 구성 요소까지 데이터 흐름 파이프라인의 각 구간을 통과하는 데이터 이동의 로그 특성은 다음과 같습니다. 또한 데이터 흐름 태스크를 통해 이동 된 데이터의 행 수를 보여 주는 [SSISDB 실행 데이터 통계 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-execution-data-statistics) 유사한 정보를 전달 합니다. `Verbose` 로깅 수준을 선택하면 생성되며 데이터 흐름 처리량을 계산하는 데 유용합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 속성                     | 유형   | Description                                                        | 예제                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | 이벤트 시간(UTC 형식: `YYYY-MM-DDTHH:MM:SS.00000Z`)      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | 진단 로그의 범주                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | 특정 작업을 추적하기 위한 고유 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | 데이터 팩터리의 이름입니다.                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | SSIS IR의 이름                                           | `MySSISIR` |
| **level**                    | String | 진단 로그의 수준                                       | `Informational` |
| **executionId**              | String | SSISDB에서 특정 실행을 추적하기 위한 고유 ID        | `1`(1은 T-SQL를 통해 SSISDB/호출 *된 패키지와* 관련 된 실행을 나타냅니다.) |
| **packageName**              | String | 실행된 패키지 파일의 이름                             | `MyPackage.dtsx` |
| **taskName**                 | String | 실행된 데이터 흐름 태스크의 이름                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | 데이터 흐름 경로 추적을 위한 고유 ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | 데이터 흐름 경로의 이름                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | 데이터를 보내는 데이터 흐름 구성 요소의 이름                    | `SQLDB Table3` |
| **destinationComponentName** | String | 데이터를 받는 데이터 흐름 구성 요소의 이름                 | `Derived Column` |
| **rowsSent**                 | String | 원본 구성 요소가 보낸 행 수                        | `500` |
| **createdTime**              | String | 행 값을 얻은 시간(UTC 형식)                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | 데이터 흐름 태스크의 실행 경로                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | 데이터 팩터리 리소스의 고유 ID입니다.                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

## <a name="log-analytics-schema"></a>Log Analytics 스키마

Log Analytics는 다음과 같은 예외를 제외 하 고 Azure Monitor에서 스키마를 상속 합니다.

* 각 열 이름의 첫 글자는 대문자로 표시됩니다. 예를 들어 Monitor의 열 이름 "correlationId"는 Log Analytics에서 "CorrelationId"입니다.
* "Level" 열이 없습니다.
* 동적 "properties" 열은 다음과 같은 동적 JSON Blob 유형으로 유지됩니다.

    | Azure Monitor 열 | Log Analytics 열 | 형식 |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 동적 |
    | $.properties.Annotations | 주석 | 동적 |
    | $.properties.Input | 입력 | 동적 |
    | $.properties.Output | 출력 | 동적 |
    | $.properties.Error.errorCode | 오류 코드 | int |
    | $.properties.Error.message | ErrorMessage | 문자열 |
    | $.properties.Error | 오류 | 동적 |
    | $.properties.Predecessors | Predecessors | 동적 |
    | $.properties.Parameters | 매개 변수 | 동적 |
    | $.properties.SystemParameters | SystemParameters | 동적 |
    | $.properties.Tags | 태그 | 동적 |

## <a name="next-steps"></a>다음 단계

[Azure Marketplace에서 Azure Data Factory Analytics 솔루션 설치](monitor-install-analytics.md)
