---
title: Data Factory 함수 및 시스템 변수
description: Azure 데이터 팩터리 함수 및 시스템 변수 목록을 제공합니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 484ec6e84ea9dbb547ed0949913278854846a60a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259983"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure 데이터 팩터리 - 함수 및 시스템 변수
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Data Factory의 시스템 변수](../control-flow-system-variables.md)를 참조하세요.

이 문서에서는 Azure 데이터 팩터리에서 지원하는 함수와 변수에 대한 정보를 제공합니다.

## <a name="data-factory-system-variables"></a>데이터 팩터리 시스템 변수

| 변수 이름 | 설명 | 개체 범위 | JSON 범위 및 사용 사례 |
| --- | --- | --- | --- |
| WindowStart |현재 작업 실행 창에 대한 시간 간격의 시작 |활동 |<ol><li>데이터 선택 쿼리를 지정합니다. [데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 참조되는 커넥터 문서 참조)</li> |
| WindowEnd |현재 작업 실행 창에 대한 시간 간격의 끝 |활동 |WindowStart와 동일 |
| SliceStart |생성되는 데이터 조각에 대한 시간 간격 시작 |활동<br/>데이터 세트 |<ol><li>[Azure Blob](data-factory-azure-blob-connector.md) 및 [파일 시스템 데이터 세트](data-factory-onprem-file-system-connector.md)와 작업하는 동안 동적 폴더 경로 및 파일 이름을 지정합니다.</li><li>작업 입력 컬렉션에서 데이터 팩터리 함수에 입력 종속성을 지정합니다.</li></ol> |
| SliceEnd |현재 데이터 조각에 대한 시간 간격 끝 |활동<br/>데이터 세트 |SliceStart와 동일 |

> [!NOTE]
> 현재 데이터 팩터리에서는 작업에 지정된 일정이 출력 데이터 세트의 가용성에 지정된 일정과 정확히 일치해야 합니다. 따라서 WindowStart, WindowEnd, SliceStart 및 SliceEnd가 항상 같은 기간과 단일 출력 조각으로 매핑됩니다.
> 

### <a name="example-for-using-a-system-variable"></a>시스템 변수 사용 예제
다음 예제에서 **SliceStart** 의 연도, 월, 일 및 시간은 **folderPath** 및 **fileName** 속성에서 사용하는 별도 변수로 추출됩니다.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>데이터 팩터리 함수
데이터 팩터리의 함수를 시스템 변수와 함께 다음과 같은 용도로 사용할 수 있습니다.

1. 데이터 선택 쿼리 지정([데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 참조되는 커넥터 문서 참조)
   
   데이터 팩터리 함수를 호출하는 구문은 데이터 선택 쿼리와 작업 및 데이터 세트의 기타 속성에 대해 **$$\<function>** 입니다.  
2. 작업 입력 컬렉션에서 데이터 팩터리 함수에 입력 종속성 지정
   
    $$는 입력 종속성 식을 지정할 때는 필요하지 않습니다.     

다음 샘플에서는 JSON 파일에서 **sqlReaderQuery** 속성을 `Text.Format` 함수가 반환하는 값에 할당합니다. 또한 작업 실행 창의 시작 시간을 나타내는 **WindowStart** 라는 시스템 변수도 사용합니다.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

사용할 수 있는 다른 서식 옵션을 설명하는 [사용자 지정 날짜 및 시간 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)(예: ay 및 yyyy) 토픽을 참조하세요. 

### <a name="functions"></a>Functions
다음 표에서는 Azure Data Factory의 모든 함수를 보여 줍니다.

| 범주 | 함수 | 매개 변수 | Description |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |지정된 시간 X에 Y시간을 추가합니다. <br/><br/>예: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| 시간 |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |X에 Y분을 추가합니다.<br/><br/>예: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| 시간 |StartOfHour(X) |X: DateTime |X의 시간 구성 요소로 표현되는 시간에 대한 시작 시간을 가져옵니다. <br/><br/>예: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |X에 Y일을 추가합니다. <br/><br/>예제: 9/15/2013 12:00:00 PM + 2일 = 9/17/2013 12:00:00 PM<br/><br/>Y를 음수로 지정하여 일도 뺄 수 있습니다.<br/><br/>예: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |X에 Y개월을 추가합니다.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Y를 음수로 지정하여 월도 뺄 수 있습니다.<br/><br/>예: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |X에 Y * 3개월을 추가합니다.<br/><br/>예: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |X에 Y * 7일을 추가합니다.<br/><br/>예: 9/15/2013 12:00:00 PM + 1주 = 9/22/2013 12:00:00 PM<br/><br/>Y를 음수로 지정하여 주도 뺄 수 있습니다.<br/><br/>예: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |X에 Y년을 추가합니다.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Y를 음수로 지정하여 년도 뺄 수 있습니다.<br/><br/>예: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X: DateTime |X의 일 구성 요소를 가져옵니다.<br/><br/>예: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X: DateTime |X의 요일 구성 요소를 가져옵니다.<br/><br/>예: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: DateTime |X의 연도 구성 요소로 표현되는 연도의 일을 가져옵니다.<br/><br/>예:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X: DateTime |매개 변수 X의 월 구성 요소로 표현되는 월의 일을 가져옵니다.<br/><br/>예: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X: DateTime |X의 끝나는 날(일 구성 요소)을 나타내는 날짜-시간을 가져옵니다.<br/><br/>예: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X: DateTime |매개 변수 X의 월 구성 요소로 표현되는 월의 끝을 가져옵니다. <br/><br/>예: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM`(9월의 끝을 나타내는 날짜 시간) |
| Date |StartOfDay(X) |X: DateTime |매개 변수 X의 일 구성 요소로 표현되는 일의 시작을 가져옵니다.<br/><br/>예: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: String |문자열 X를 날짜 시간으로 구문 분석합니다. |
| DateTime |Ticks(X) |X: DateTime |매개 변수 X의 틱 속성을 가져옵니다. 1틱은 100나노초에 해당합니다. 이 속성 값은 0001년 1월 1일 자정 12:00:00 이후 경과된 틱 수를 나타냅니다. |
| 텍스트 |Format(X) |X: String 변수 |텍스트의 서식을 지정합니다(`\\'` 조합을 사용하여 `'` 문자 이스케이프).|

> [!IMPORTANT]
> 다른 함수 내에서 함수를 사용할 경우 내부 함수에 대한 접두사로 **$$** 를 사용해야 합니다. 예: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' 및 RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). 이 예에서는 **Time.AddHours** 함수에 대해 **$$** 접두사가 사용되지 않았습니다. 

#### <a name="example"></a>예제
다음 예제에서는 Hive 활동에 대한 입력 및 출력 매개 변수가 `Text.Format` 함수 및 SliceStart 시스템 변수를 사용하여 결정됩니다. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>예제 2

다음 예제에서는 저장 프로시저 작업에 대한 DataTime 매개 변수가 Text를 사용하여 결정됩니다. Format 함수 및 SliceStart 변수입니다. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>예제 3
SliceStart로 표현된 일 대신 이전 일의 데이터를 읽으려면 다음 예제와 같이 AddDays 함수를 사용합니다. 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

사용할 수 있는 다른 서식 옵션을 설명하는 [사용자 지정 날짜 및 시간 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings) (예: yy 대 yyyy) 토픽을 참조하세요.