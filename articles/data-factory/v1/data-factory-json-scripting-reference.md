---
title: Azure Data Factory - JSON 스크립팅 참조
description: Data Factory 엔터티에 JSON 스키마를 제공합니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 84e4d5449ba2ac80e45abc2fe9f870f03097981a
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132863160"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - JSON 스크립팅 참조
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다.

[!INCLUDE[ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)] 

이 문서에서는 Azure Data Factory 엔터티(파이프라인, 활동, 데이터 세트 및 연결된 서비스)를 정의하기 위한 JSON 스키마와 예제를 제공합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>파이프라인
파이프라인 정의에 대한 간략한 구조는 다음과 같습니다.

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

다음 표에서는 파이프라인 JSON 정의의 속성을 설명합니다.

| 속성 | 설명 | 필수
-------- | ----------- | --------
| name | 파이프라인의 이름입니다. 작업 또는 파이프라인이 수행되도록 구성된 작업을 나타내는 이름을 지정합니다.<br/><ul><li>최대 문자 수: 260</li><li>문자, 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |예 |
| description |작업 또는 파이프라인이 무엇에 사용되는지 설명하는 텍스트입니다. | No |
| 작업 | 활동의 목록을 포함합니다. | 예 |
| start |파이프라인에 대한 시작 날짜-시간입니다. [ISO 형식](https://en.wikipedia.org/wiki/ISO_8601)에 있어야 합니다. 예: 2014-10-14T16:32:41 <br/><br/>예를 들어 EST 시간처럼 현지 시간을 지정할 수 있습니다. 예: `2016-02-27T06:00:00**-05:00`(오전 6시 동부 표준시)<br/><br/>start 및 end 속성은 함께 파이프라인의 활성 기간을 지정합니다. 출력 조각은 이 활성 기간에만 생성됩니다. |예<br/><br/>end 속성에 대한 값을 지정하는 경우 반드시 start 속성에 대한 값도 지정해야 합니다.<br/><br/>파이프라인을 만들 때에는 시작 및 종료 시간을 비워 둘 수 있습니다. 파이프라인을 실행할 활성 기간을 설정하려면 두 값 모두를 지정해야 합니다. 파이프라인을 만들 때 시작 시간과 종료 시간을 지정하지 않으면 나중에 Set-AzDataFactoryPipelineActivePeriod cmdlet을 사용하여 설정할 수 있습니다. |
| end |파이프라인에 대한 종료 날짜-시간입니다. 지정된 경우 ISO 형식에 있어야 합니다. 예: 2014-10-14T17:32:41 <br/><br/>예를 들어 EST 시간처럼 현지 시간을 지정할 수 있습니다. 예: `2016-02-27T06:00:00**-05:00`(오전 6시 동부 표준시)<br/><br/>파이프라인을 무기한 실행하려면 end 속성 값으로 9999-09-09를 지정합니다. |예 <br/><br/>시작 속성에 대한 값을 지정하는 경우 반드시 끝 속성에 대한 값도 지정해야 합니다.<br/><br/>**start** 속성에 대한 참조를 확인합니다. |
| isPaused |파이프라인을 true로 설정하면 실행되지 않습니다. 기본값 = false입니다. 이 속성을 사용하여 활성화 또는 비활성화할 수 있습니다. |예 |
| pipelineMode |파이프라인에 대한 실행을 예약하는 메서드입니다. 허용되는 값은 scheduled(기본), onetime입니다.<br/><br/>‘Scheduled’는 파이프라인이 활성 기간(시작 및 종료 시간)에 따라 지정된 시간 간격으로 실행된다는 것을 나타냅니다. ‘Onetime’은 파이프라인이 한 번만 실행된다는 것을 나타냅니다. 현재는, Onetime 파이프라인이 생성된 후에 수정/업데이트가 불가능합니다. 일회성 설정에 대한 세부 정보는 [일회성 파이프라인](data-factory-create-pipelines.md#onetime-pipeline)을 참조하세요. |예 |
| expirationTime |생성 후에 파이프라인이 유효하고 프로비전된 상태로 유지해야 하는 시간입니다. 활성 작업, 실패한 작업 또는 보류 중인 작업이 없는 경우 만료 시간이 되면 파이프라인은 자동으로 삭제됩니다. |예 |


## <a name="activity"></a>활동
파이프라인 정의(activities 요소) 내의 활동(activity)에 대한 간략한 구조는 다음과 같습니다.

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

다음 표에서는 activity JSON 정의 내의 속성을 설명합니다.

| 태그 | 설명 | 필수 |
| --- | --- | --- |
| name |활동의 이름입니다. 활동을 수행하도록 구성된 작업을 나타내는 이름을 지정합니다.<br/><ul><li>최대 문자 수: 260</li><li>문자, 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |예 |
| description |활동의 용도를 설명하는 텍스트입니다. |예 |
| type |작업의 유형을 지정합니다. 다른 유형의 작업에 대해서는 [데이터 저장소](#data-stores) 및 [데이터 변환 작업](#data-transformation-activities) 섹션을 참조하세요. |예 |
| 입력 |작업에서 사용하는 입력 테이블<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |HDInsightStreaming 및 SqlServerStoredProcedure 작업의 경우 아니요 <br/> <br/> 다른 모든 사용자의 경우 예 |
| outputs |활동에서 사용하는 출력 테이블입니다.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |예 |
| linkedServiceName |작업에서 사용하는 연결된 서비스의 이름입니다. <br/><br/>작업은 필요한 컴퓨팅 환경에 연결하는 연결된 서비스를 지정해야 할 수 있습니다. |예(HDInsight 작업, ML Studio(클래식) 작업, 저장 프로시저 작업의 경우) <br/><br/>다른 모든 사용자의 경우 아니요 |
| typeProperties |typeProperties 섹션의 속성은 작업의 종류에 따라 달라 집니다. |No |
| policy |작업의 런타임 동작에 영향을 주는 정책입니다. 지정하지 않으면 기본 정책이 사용됩니다. |예 |
| scheduler |"scheduler" 속성은 작업에 원하는 일정을 정의하는 데 사용됩니다. 하위 속성은 [데이터 세트에서 가용성 속성](data-factory-create-datasets.md#dataset-availability)에 있는 속성과 같습니다. |예 |

### <a name="policies"></a>정책
정책은 특히 테이블의 조각을 처리할 때 활동의 런타임 동작에 영향을 줍니다. 다음 테이블에서는 자세한 내용을 제공합니다.

| 속성 | 허용된 값 | 기본값 | 설명 |
| --- | --- | --- | --- |
| 동시성 |정수 <br/><br/>최대값: 10 |1 |작업의 동시 실행 수입니다.<br/><br/>다른 조각에 발생할 수 있는 병렬 작업 실행 횟수를 결정합니다. 예를 들어 활동이 사용 가능한 많은 데이터 집합을 거쳐야 하는 경우 동시성 값을 높이면 데이터 처리가 빨라집니다. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |처리 중인 데이터 조각의 순서를 결정합니다.<br/><br/>예를 들어 2개의 조각이 있으며(각각 오후 4시 및 오후 5시에 발생) 둘 다 실행 보류 상태입니다. executionPriorityOrder를 설정하여 NewestFirst가 되도록 하면 오후 5시에 조각이 먼저 처리됩니다. 마찬가지로 executionPriorityORder를 OldestFIrst로 설정하면 오후 4시의 조각이 처리됩니다. |
| retry |정수<br/><br/>최대값이 10이 될 수 있음 |0 |조각에 대한 데이터 처리 전에 다시 시도 횟수가 실패로 표시됩니다. 데이터 조각에 대한 활동 실행은 지정된 재시도 횟수까지 다시 시도됩니다. 재시도는 실패 후 가능한 한 빨리 수행합니다. |
| 시간 제한 |TimeSpan |00:00:00 |활동에 대한 시간 제한입니다. 예: 00:10:00(시간 제한 10분을 의미함)<br/><br/>값이 지정되지 않거나 0인 경우 시간 제한은 무한입니다.<br/><br/>조각의 데이터 처리 시간이 시간 제한 값을 초과하면 취소되고 시스템이 처리를 다시 시도합니다. 다시 시도 횟수는 다시 시도 속성에 따라 달라집니다. 시간 제한이 발생할 때 상태는 TimedOut으로 설정됩니다. |
| delay |TimeSpan |00:00:00 |조각의 데이터 처리 작업이 시작되기 전에 지연을 지정합니다.<br/><br/>데이터 조각에 대한 활동의 실행은 지연이 예상된 실행 시간을 지난 후에 시작됩니다.<br/><br/>예: 00:10:00(10분의 지연을 의미함) |
| longRetry |정수<br/><br/>최대값: 10 |1 |조각 실행이 실패하기 전까지의 긴 재시도 횟수입니다.<br/><br/>longRetry 시도는 longRetryInterval에 따라 간격이 조정됩니다. 따라서 재시도 간의 시간을 지정해야 하는 경우 longRetry를 사용합니다. Retry 및 longRetry를 둘 다 지정하면 각 longRetry 시도에는 Retry 시도가 포함되고 최대 시도 횟수는 Retry * longRetry가 됩니다.<br/><br/>예를 들어 활동 정책에 다음 설정이 있는 경우:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>실행할 조각이 하나뿐이며(Waiting 상태) 작업 실행이 매번 실패한다고 가정합니다. 우선 3번 연속 실행 시도를 합니다. 시도한 후 각 조각 상태는 다시 시도입니다. 처음 3번 시도 후에 조각 상태는 LongRetry입니다.<br/><br/>한 시간(즉, longRetryInteval의 값) 후에 3번 연속 실행이 다시 시도됩니다. 그 후에 조각 상태가 실패이면 다시 시도는 더 이상 시도하지 않습니다. 즉, 전체적으로 6번의 시도가 일어납니다.<br/><br/>모든 실행에 성공하면 조각 상태는 준비 상태가 되며 더 이상 다시 시도하지 않습니다.<br/><br/>longRetry는 종속 데이터가 명확하지 않은 시간에 도착하거나 데이터 처리가 발생하는 전체적인 환경을 신뢰할 수 없는 상황에서 사용될 수 있습니다. 이러한 경우 하나씩 다시 시도를 수행해도 도움이 되지 않을 수 있으며 특정 시간 간격 후에 시도할 경우 출력이 나타납니다.<br/><br/>주의: longRetry 또는 longRetryInterval에 높은 값을 설정하지 마세요. 일반적으로 더 높은 값을 지정하면 다른 시스템 문제가 발생합니다. |
| longRetryInterval |TimeSpan |00:00:00 |긴 다시 시도 간의 지연 |

### <a name="typeproperties-section"></a>typeProperties 섹션
typeProperties 섹션은 각 활동마다 다릅니다. 변환 활동에는 type 속성만 있습니다. 파이프라인에서 변환 활동을 정의하는 JSON 샘플은 이 문서의 [데이터 변환 활동](#data-transformation-activities) 섹션을 참조하세요.

**복사 활동**(Copy activity)의 typeProperties 섹션에는 두 하위 섹션, 즉 **source** 및 **sink** 가 있습니다. 데이터 저장소를 소스 및/또는 싱크로 사용하는 방법을 보여 주는 JSON 샘플은 이 문서의 [데이터 저장소](#data-stores) 섹션을 참조하세요.

### <a name="sample-copy-pipeline"></a>샘플 복사 파이프라인
다음 샘플 파이프라인에는 **Copy** in the **활동** 유형의 하나의 활동이 있습니다. 샘플에서 [복사 작업](data-factory-data-movement-activities.md)은 Azure Blob Storage의 데이터를 Azure SQL Database에 복사합니다.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

다음 사항에 유의하세요.

* 작업 섹션에는 **형식** 이 **복사** 로 설정된 작업만 있습니다.
* 작업에 대한 입력을 **InputDataset** 으로 설정하고 작업에 대한 출력을 **OutputDataset** 으로 설정합니다.
* **typeProperties** 섹션에서 **BlobSource** 를 원본 유형으로 지정하고 **SqlSink** 를 싱크 유형으로 지정합니다.

데이터 저장소를 소스 및/또는 싱크로 사용하는 방법을 보여 주는 JSON 샘플은 이 문서의 [데이터 저장소](#data-stores) 섹션을 참조하세요.

이 파이프라인을 만드는 전체 연습은 [자습서: Blob Storage의 데이터를 SQL Database에 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

### <a name="sample-transformation-pipeline"></a>샘플 변환 파이프라인
다음 샘플 파이프라인에는 **HDInsightHive** in the **활동** 유형의 하나의 활동이 있습니다. 이 샘플에서 [HDInsight Hive 활동](data-factory-hive-activity.md) 은 Azure HDInsight Hadoop 클러스터에서 Hive 스크립트 파일을 실행하여 Azure Blob Storage에서 데이터를 변환합니다.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

다음 사항에 유의하세요.

* activities 섹션에는 **type** 이 **HDInsightHive** 로 설정된 작업만 있습니다.
* Hive 스크립트 파일 **partitionweblogs.hql** 은 Azure Storage 계정(**AzureStorageLinkedService** 라고 하는 scriptLinkedService에 의해 지정됨)과 **adfgetstarted** 컨테이너에 있는 **스크립트** 폴더에 저장됩니다.
* **defines** 섹션은 Hive 스크립트에 Hive 구성 값(예: `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`)으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

파이프라인에서 변환 활동을 정의하는 JSON 샘플은 이 문서의 [데이터 변환 활동](#data-transformation-activities) 섹션을 참조하세요.

이 파이프라인을 만드는 전체 연습은 [자습서: Hadoop 클러스터를 사용하여 데이터를 처리하는 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="linked-service"></a>연결된 서비스
연결된 서비스 정의의 간략한 구조는 다음과 같습니다.

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

다음 표에서는 activity JSON 정의 내의 속성을 설명합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| name | 연결된 서비스의 이름입니다. | 예 |
| properties - type | 연결된 서비스의 형식입니다. 예: Azure Storage, Azure SQL Database. |
| typeProperties | typeProperties 섹션에는 각 데이터 저장소 또는 컴퓨팅 환경마다 다른 요소가 있습니다. 모든 데이터 저장소 연결 서비스는 데이터 저장소 섹션을, 모든 컴퓨팅 연결 서비스는 [컴퓨팅 환경](#compute-environments)을 참조하세요. |

## <a name="dataset"></a>데이터 세트
Azure Data Factory의 데이터 세트는 다음과 같이 정의됩니다.

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

다음 표에서는 위의 JSON에서 속성을 설명합니다.

| 속성 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| name | 데이터 세트의 이름입니다. 명명 규칙은 [Azure Data Factory - 명명 규칙](data-factory-naming-rules.md) 을 참조하세요. |예 |해당 없음 |
| type | 데이터 세트의 형식입니다. Azure Data Factory에서 지원되는 형식(예: AzureBlob, AzureSqlTable) 중 하나를 지정합니다. Data Factory에서 지원하는 모든 데이터 저장소 및 데이터 세트 유형에 대해서는 [데이터 저장소](#data-stores) 섹션을 참조하세요. |
| structure | 데이터 세트의 스키마입니다. 열, 해당 형식 등을 포함합니다. | 예 |해당 없음 |
| typeProperties | 선택한 형식에 해당하는 속성입니다. 지원되는 형식 및 해당 속성은 [데이터 저장소](#data-stores) 섹션을 참조하세요. |예 |해당 없음 |
| external | 데이터 세트가 데이터 팩터리 파이프라인에 의해 명시적으로 생성되는지를 지정하는 부울 플래그입니다. |예 |false |
| availability | 데이터 세트 생성에 대한 처리 창 또는 조각화 모델을 정의합니다. 데이터 세트 조각화 모델에 대한 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요. |예 |해당 없음 |
| policy |데이터 세트 조각이 충족해야 하는 기준 또는 조건을 정의합니다. <br/><br/>세부 정보는 데이터 세트 정책을 참조하세요. |예 |해당 없음 |

**structure** 섹션의 각 열에는 다음과 같은 속성이 포함됩니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| name |열의 이름입니다. |예 |
| type |열의 데이터 형식입니다.  |예 |
| culture |지정된 형식이 .NET 형식 `Datetime` 또는 `Datetimeoffset`일 때 사용할 .NET 기반 culture입니다. 기본값은 `en-us`입니다. |예 |
| format |지정된 형식이 .NET 형식 `Datetime` 또는 `Datetimeoffset`일 때 사용할 형식 문자열입니다. |예 |

다음 예제에서는 데이터 세트에 3개의 열 `slicetimestamp`, `projectname` 및 `pageviews`가 있으며 형식은 각각 문자열, 문자열 및 10진수입니다.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

다음 표에서는 **availability** 섹션에서 사용할 수 있는 속성을 설명합니다.

| 속성 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| frequency |데이터 세트 조각 생성을 위한 시간 단위를 지정합니다.<br/><br/><b>지원되는 빈도</b>: 분, 시, 일, 주, 월 |예 |해당 없음 |
| interval |빈도 승수를 지정합니다.<br/><br/>"빈도 x 간격"은 조각을 생성하는 빈도를 결정합니다.<br/><br/>데이터 세트를 시간 단위로 조각화해야 하는 경우 <b>frequency</b>를 <b>Hour</b>로, <b>interval</b>을 <b>1</b>로 설정합니다.<br/><br/><b>참고:</b> 빈도를 Minute(분)으로 지정하면 15 이상으로 간격을 설정하는 것이 좋습니다. |예 |해당 없음 |
| 스타일 |간격의 시작/끝에 조각을 생성해야 하는지를 지정합니다.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Frequency를 Month로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 월의 마지막 날에 생성합니다. style을 StartOfInterval로 설정하는 경우 조각을 달의 첫 번째 날에 생성합니다.<br/><br/>frequency를 Day로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 일의 마지막 시간에 생성합니다.<br/><br/>Frequency를 Hour로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 시간의 끝에 생성합니다. 예를 들어 오후 1~2시 기간에 대한 조각은 오후 2시에 생성됩니다. |예 |EndOfInterval |
| anchorDateTime |스케줄러에서 사용하는 시간에 절대 위치를 정의하여 데이터 세트 조각 경계를 컴퓨팅합니다. <br/><br/><b>참고:</b> AnchorDateTime에 빈도보다 더 세분화된 날짜 부분이 있는 경우 더 세분화된 부분을 무시합니다. <br/><br/>예를 들어 <b>간격</b>이 <b>매시간</b>(frequency: Hour 및 interval: 1)이고 <b>AnchorDateTime</b>에서 <b>분 및 초</b>를 포함하는 경우 AnchorDateTime의 <b>분 및 초</b> 부분은 무시됩니다. |예 |01/01/0001 |
| offset |모든 데이터 세트 조각의 시작과 끝이 이동에 의한 Timespan입니다. <br/><br/><b>참고:</b> anchorDateTime 및 offset이 모두 지정되면 결과적으로 이동이 결합됩니다. |예 |해당 없음 |

다음 availability 섹션에서는 출력 데이터 세트를 시간마다 생성하거나 (또는) 입력 데이터 세트를 시간마다 사용할 수 있도록 지정합니다.

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

데이터 세트 정의의 **정책** 섹션에서 데이터 세트 조각이 충족해야 하는 기준 또는 조건을 정의합니다.

| 정책 이름 | Description | 에 적용 | 필수 | 기본값 |
| --- | --- | --- | --- | --- |
| minimumSizeMB |**Azure Blob** 에서 데이터가 최소 크기 요구 사항(메가바이트)을 충족하는지 확인합니다. |Azure Blob |예 |해당 없음 |
| minimumRows |**Azure SQL Database** 또는 **Azure 테이블** 의 데이터에 최소 행 수가 포함되어 있는지 확인합니다. |<ul><li>Azure SQL Database</li><li>Azure 테이블</li></ul> |예 |해당 없음 |

**예:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

데이터 세트가 Azure Data Factory에서 생성되지 않는 한 **external** 로 표시되어야 합니다. 이 설정은 작업 또는 파이프라인 연결을 활용하고 있지 않는 한 파이프라인에서 첫 번째 작업의 입력에 일반적으로 적용됩니다.

| 이름 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| dataDelay |지정된 조각에 대한 외부 데이터의 가용성 확인을 지연하는 시간입니다. 예를 들어 데이터를 매시간 사용할 수 있는 경우 외부 데이터를 볼 수 있고 해당 조각이 준비(Ready) 상태인지 확인하기 위한 검사는 dataDelay를 사용하여 지연시킬 수 있습니다.<br/><br/>를 사용하여 지연할 수 있습니다.  예를 들어 현재 오후 1시이고 이 값이 10 분이라면 유효성 검사는 오후 1시 10분에 시작합니다.<br/><br/>이 설정은 과거의 조각(조각 종료 시간 + dataDelay < Now를 사용한 조각)에는 영향을 주지 않아 아무런 지연 없이 처리됩니다.<br/><br/>23:59보다 큰 시간은 `day.hours:minutes:seconds` 형식을 사용하여 지정해야 합니다. 예를 들어 24시간을 지정하려면 24:00:00을 사용하는 대신 1.00:00:00을 사용합니다. 24:00:00을 사용하면 24일(24.00:00:00)로 처리됩니다. 1일 4시간의 경우 1:04:00:00을 지정합니다. |예 |0 |
| retryInterval |오류 발생과 다음 다시 시도 사이의 대기 시간입니다. 시도가 실패하는 경우 다음 시도는 retryInterval 이후입니다. <br/><br/>오후 1시가 되면 첫 번째 시도를 시작합니다. 첫 번째 유효성 검사를 완료하는 데 걸리는 기간이 1분이고 작업이 실패하는 경우 다음 다시 시도는 1시 + 1분(기간) + 1분(다시 시도 간격) = 오후 1시 2분입니다. <br/><br/>과거 조각의 경우 지연이 없습니다. 재시도는 곧바로 이뤄집니다. |예 |00:01:00 (1분) |
| retryTimeout |각 재시도에 대한 제한 시간입니다.<br/><br/>이 속성이 10분으로 설정한 경우 유효성 검사를 10분 내에 완료해야 합니다. 유효성 검사를 수행하는 데 10분 보다 오래 걸리는 경우 재시도는 제한 시간을 초과합니다.<br/><br/>유효성 검사에 대한 모든 시도의 시간이 초과되면 조각에 TimedOut이 표시됩니다. |예 |00:10:00 (10분) |
| maximumRetry |외부 데이터의 가용성을 검사한 횟수입니다. 허용되는 최대값은 10입니다. |예 |3 |


## <a name="data-stores"></a>데이터 저장소
[연결된 서비스](#linked-service) 섹션에서 모든 유형의 연결된 서비스에 공통적인 JSON 요소에 대해 설명했습니다. 이 섹션에서는 각 데이터 저장소와 관련된 JSON 요소에 대해 자세히 설명합니다.

[데이터 세트](#dataset) 섹션에서 모든 유형의 데이터 세트에 공통적인 JSON 요소에 대해 설명했습니다. 이 섹션에서는 각 데이터 저장소와 관련된 JSON 요소에 대해 자세히 설명합니다.

[활동](#activity) 섹션에서 모든 유형의 활동에 공통적인 JSON 요소에 대해 설명했습니다. 이 섹션에서는 복사 활동의 소스/싱크로 사용될 때 각 데이터 저장소와 관련된 JSON 요소에 대해 자세히 설명합니다.

관심 있는 저장소 링크를 클릭하여 복사 활동의 연결된 서비스, 데이터 세트 및 소스/싱크에 대한 JSON 스키마를 확인하세요.

| 범주 | 데이터 저장소
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table Storage](#azure-table-storage) |
| **데이터베이스** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **최근에 사용한 파일** |[Amazon S3](#amazon-s3) |
| &nbsp; |[파일 시스템](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **기타** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |웹 테이블 |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>연결된 서비스
연결된 서비스에는 두 가지 유형, 즉 Azure Storage 연결된 서비스와 Azure Storage SAS 연결된 서비스가 있습니다.

#### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
**계정 키** 를 사용하여 Azure 스토리지 계정을 데이터 팩터리에 연결하려면 Azure Storage 연결된 서비스를 만듭니다. Azure Storage 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AzureStorage** 로 설정합니다. 그런 다음 **typeProperties** 섹션에서 다음 속성을 지정하면 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| connectionString |connectionString 속성에 대한 Azure Storage에 연결하는 데 필요한 정보를 지정합니다. |예 |

##### <a name="example"></a>예제

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS 연결된 서비스
Azure Storage SAS 연결된 서비스에서 SAS(공유 액세스 서명)을 사용하여 Azure Storage 계정을 Azure Data Factory에 연결할 수 있습니다. 이 서비스는 스토리지의 모든/특정 리소스(Blob/컨테이너)에 대해 제한된/시간 제한 액세스를 데이터 팩터리에 제공합니다. 공유 액세스 서명을 사용하여 Azure 스토리지 계정을 데이터 팩터리에 연결하려면 Azure Storage SAS 연결된 서비스를 만듭니다. Azure Storage SAS 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AzureStorageSas** 로 설정합니다. 그런 다음 **typeProperties** 섹션에서 다음 속성을 지정하면 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| sasUri |BLOB, 컨테이너, 테이블 등의 Azure Storage 리소스에 공유 액세스 서명 URI를 지정합니다. |예 |

##### <a name="example"></a>예제

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

이러한 연결된 서비스에 대한 자세한 내용은 [Azure Blob Storage 커넥터](data-factory-azure-blob-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Azure Blob 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **AzureBlob** 으로 설정합니다. 그런 다음 **typeProperties** 섹션에서 다음 Azure Blob 특정 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |Blob Storage에서 컨테이너 및 폴더에 대한 경로입니다. 예제: myblobcontainer\myblobfolder\ |예 |
| fileName |Blob의 이름입니다. fileName은 선택 사항이며 대/소문자를 구분합니다.<br/><br/>filename을 지정하는 경우 활동(복사 포함)은 특정 Blob에서 작동합니다.<br/><br/>fileName을 지정하지 않으면 복사는 입력 데이터 세트에 대한 folderPath에 모든 Blob을 포함합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않으면 생성된 파일의 이름은 다음 형식을 사용합니다.`Data.<Guid>.txt`(예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |예 |
| partitionedBy |partitionedBy는 선택적 속성입니다. 동적 folderPath 및 시계열 데이터에 대한 filename을 지정하는 데 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대한 매개 변수화됩니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


자세한 내용은 [Azure Blob 커넥터](data-factory-azure-blob-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="blobsource-in-copy-activity"></a>복사 활동의 BlobSource
Azure Blob Storage에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **BlobSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True(기본값), False |예 |

#### <a name="example-blobsource"></a>예제: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>복사 활동의 BlobSink
Azure Blob Storage에 데이터를 복사하는 경우 복사 활동의 **sink type** 을 **BlobSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| copyBehavior |원본이 BlobSource 또는 FileSystem인 경우 복사 동작을 정의합니다. |<b>PreserveHierarchy</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/><b>FlattenHierarchy</b>: 원본 폴더의 모든 파일은 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><br/><b>MergeFiles(기본값):</b> 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다. |예 |

#### <a name="example-blobsink"></a>예제: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Azure Blob 커넥터](data-factory-azure-blob-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>연결된 서비스
Azure Data Lake Store 연결된 서비스를 정의하려면 연결된 서비스의 type을 **AzureDataLakeStore** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureDataLakeStore** | 예 |
| dataLakeStoreUri | Azure 데이터 레이크 저장소 계정에 대한 정보를 지정합니다. 형식 예: `https://[accountname].azuredatalakestore.net/webhdfs/v1` 또는 `adl://[accountname].azuredatalakestore.net/`. | 예 |
| subscriptionId | Data Lake Store가 속하는 Azure 구독 ID입니다. | 싱크에 필요 |
| resourceGroupName | Data Lake Store가 속하는 Azure 리소스 그룹 이름입니다. | 싱크에 필요 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예(서비스 주체 인증의 경우) |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. | 예(서비스 주체 인증의 경우) |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 예(서비스 주체 인증의 경우) |
| 권한 부여 | **Data Factory 편집기** 에서 **권한 부여** 단추를 클릭하고 자격 증명을 입력합니다. 그러면 자동 생성된 authorization URL이 이 속성에 할당됩니다. | 예(사용자 자격 증명 인증의 경우)|
| sessionID | OAuth 권한 부여 세션에서 가져온 OAuth 세션 ID입니다. 각 세션 ID는 고유하고 한 번만 사용될 수 있으며 이 설정은 Data Factory 편집기를 사용하는 경우 자동으로 생성됩니다. | 예(사용자 자격 증명 인증의 경우) |

#### <a name="example-using-service-principal-authentication"></a>예제: 서비스 주체 인증 사용
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>예제: 사용자 자격 증명 인증 사용
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

자세한 내용은 [Azure Data Lake Store 커넥터](data-factory-azure-datalake-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Azure Data Lake Store 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **AzureDataLakeStore** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| folderPath |Azure 데이터 레이크 저장소의 컨테이너 및 폴더에 대한 경로입니다. |예 |
| fileName |Azure 데이터 레이크 저장소에 있는 파일의 이름입니다. fileName은 선택 사항이며 대/소문자를 구분합니다. <br/><br/>filename을 지정하는 경우 활동(복사 포함)은 특정 파일에서 작동합니다.<br/><br/>fileName을 지정하지 않으면 복사는 입력 데이터 세트에 대한 folderPath에 모든 파일을 포함합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않으면 생성된 파일의 이름은 다음 형식을 사용합니다.`Data.<Guid>.txt`(예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |예 |
| partitionedBy |partitionedBy는 선택적 속성입니다. 동적 folderPath 및 시계열 데이터에 대한 filename을 지정하는 데 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대한 매개 변수화됩니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

#### <a name="example"></a>예제
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [Azure Data Lake Store 커넥터](data-factory-azure-datalake-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="azure-data-lake-store-source-in-copy-activity"></a>복사 활동의 Azure Data Lake Store 소스
Azure Data Lake Store에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **AzureDataLakeStoreSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

**AzureDataLakeStoreSource** 는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True(기본값), False |예 |

#### <a name="example-azuredatalakestoresource"></a>예제: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Azure Data Lake Store 커넥터](data-factory-azure-datalake-connector.md#copy-activity-properties) 문서를 참조하세요.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>복사 활동의 Azure Data Lake Store 싱크
Azure Data Lake Store에 데이터를 복사하는 경우 복사 활동의 **sink type** 을 **AzureDataLakeStoreSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| copyBehavior |복사 동작을 지정합니다. |<b>PreserveHierarchy</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/><b>FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 만들어집니다. 대상 파일은 자동 생성된 이름으로 만들어집니다.<br/><br/><b>MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다. |예 |

#### <a name="example-azuredatalakestoresink"></a>예제: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Azure Data Lake Store 커넥터](data-factory-azure-datalake-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>연결된 서비스
Azure Cosmos DB 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **DocumentDb** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| connectionString |Azure Cosmos DB 데이터베이스에 연결하는 데 필요한 정보를 지정합니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
자세한 내용은 [Azure Cosmos DB 커넥터](data-factory-azure-documentdb-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Azure Cosmos DB 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **DocumentDbCollection** 으로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| collectionName |Azure Cosmos DB 컬렉션의 이름입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
자세한 내용은 [Azure Cosmos DB 커넥터](data-factory-azure-documentdb-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>복사 작업에서 Azure Cosmos DB 컬렉션 원본
Azure Cosmos DB에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **DocumentDbCollectionSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| **속성** | **설명** | **허용된 값** | **필수** |
| --- | --- | --- | --- |
| Query |데이터를 읽는 쿼리를 지정합니다. |Azure Cosmos DB에서 지원하는 쿼리 문자열입니다. <br/><br/>예: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |예 <br/><br/>지정하지 않는 경우 실행되는 SQL 문: `select <columns defined in structure> from mycollection` |
| nestingSeparator |문서가 중첩됨을 나타내는 특수 문자 |임의의 문자. <br/><br/>Azure Cosmos DB는 중첩된 구조를 허용하는 JSON 문서용 NoSQL 저장소입니다. Azure 데이터 팩터리를 사용하면 nestingSeparator, 즉 위 예에서 "."를 통해 계층 구조를 표시할 수 있습니다. 테이블 정의에서 "Name.First", "Name.Middle" 및 "Name.Last"에 따르면 구분 기호를 사용하여 복사 작업이 3개의 자식 요소(처음, 중간 및 마지막)가 있는 "Name" 개체를 생성합니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>복사 작업의 Azure Cosmos DB 컬렉션 싱크
Azure Cosmos DB에 데이터를 복사하는 경우 복사 작업의 **sink type** 을 **DocumentDbCollectionSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| **속성** | **설명** | **허용된 값** | **필수** |
| --- | --- | --- | --- |
| nestingSeparator |중첩된 해당 문서를 나타내는 원본 열 이름에 특수 문자가 필요합니다. <br/><br/>위의 예에서 출력 테이블의 `Name.First`는 Cosmos DB 문서에서 다음 JSON 구조를 생성합니다.<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |중첩 수준을 구분하는데 사용되는 문자입니다.<br/><br/>기본값은 `.`.(점)입니다. |중첩 수준을 구분하는데 사용되는 문자입니다. <br/><br/>기본값은 `.`.(점)입니다. |
| writeBatchSize |문서를 작성하는 Azure Cosmos DB 서비스에 대한 병렬 요청 수입니다.<br/><br/>이 속성을 사용하여 Azure Cosmos DB 간 데이터를 복사하는 경우 성능을 미세 조정할 수 있습니다. Azure Cosmos DB에 더 많은 병렬 요청이 전송되기 때문에 writeBatchSize 증가하는 경우 더 나은 성능을 기대할 수 있습니다. 하지만 오류 메시지: “요청 빈도가 높습니다."를 발생 시킬 수 있는 제한을 방지해야 합니다.<br/><br/>제한은 문서 크기, 문서의 용어 수, 대상 컬렉션의 인덱싱 정책 등 여러 요소에 의해 결정됩니다. 복사 작업의 경우 더 나은 컬렉션(예: S3)을 사용하여 사용 가능한 처리량을 최대한 늘릴 수 있습니다.(2,500 요청 단위/초). |정수 |아니요(기본값: 5) |
| writeBatchTimeout |시간이 초과 되기 전에 완료하려는 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예: “00:30:00”(30분). |예 |

#### <a name="example"></a>예제

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

자세한 내용은 [Azure Cosmos DB 커넥터](data-factory-azure-documentdb-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>연결된 서비스
Azure SQL Database 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AzureSqlDatabase** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| connectionString |Azure SQL Database 인스턴스에 연결하는 데 필요한 정보를 connectionString 속성에 대해 지정합니다. |예 |

#### <a name="example"></a>예제
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

자세한 내용은 [Azure SQL 커넥터](data-factory-azure-sql-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Azure SQL Database 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **AzureSqlTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Azure SQL Database 인스턴스에서 테이블 또는 보기의 이름입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
자세한 내용은 [Azure SQL 커넥터](data-factory-azure-sql-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="sql-source-in-copy-activity"></a>복사 활동의 SQL 소스
Azure SQL Database에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **SqlSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| SqlReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable`. |예 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. |저장 프로시저의 이름입니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
자세한 내용은 [Azure SQL 커넥터](data-factory-azure-sql-connector.md#copy-activity-properties) 문서를 참조하세요.

### <a name="sql-sink-in-copy-activity"></a>복사 활동의 SQL 싱크
Azure SQL Database에 데이터를 복사하는 경우 복사 활동의 **sink type** 을 **SqlSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예: “00:30:00”(30분). |예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 |정수(행 수) |아니요(기본값: 10000) |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. |쿼리 문입니다. |예 |
| sliceIdentifierColumnName |자동 생성된 조각 식별자를 입력할 복사 활동의 열 이름을 지정합니다. 이 식별자는 복사 활동을 다시 실행할 때 특정 조각의 데이터를 정리하는 데 사용됩니다. |이진(32) 데이터 형식이 있는 열의 열 이름입니다. |예 |
| sqlWriterStoredProcedureName |대상 테이블에 대한 데이터 Upsert(업데이트/삽입)를 수행하는 저장 프로시저의 이름입니다. |저장 프로시저의 이름입니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. |테이블 유형 이름 |예 |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Azure SQL 커넥터](data-factory-azure-sql-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>연결된 서비스
Azure Synapse Analytics에 연결된 서비스를 정의하려면 연결된 서비스의 **유형** 을 **AzureSqlDW** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| connectionString |connectionString 속성에 대해 Azure Synapse Analytics 인스턴스에 연결하는 데 필요한 정보를 지정합니다. |예 |



#### <a name="example"></a>예제

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

자세한 내용은 [Azure Synapse Analytics 커넥터](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Azure Synapse Analytics 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **AzureSqlDWTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스에서 참조하는 Azure Synapse Analytics 데이터베이스에 있는 테이블 또는 뷰의 이름입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [Azure Synapse Analytics 커넥터](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>복사 작업의 Azure Synapse Analytics 원본
Azure Synapse Analytics에서 데이터를 복사하는 경우 복사 작업의 **source type** 을 **SqlDWSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| SqlReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` |예 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. |저장 프로시저의 이름입니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Azure Synapse Analytics 커넥터](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) 문서를 참조하세요.

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>복사 작업의 Azure Synapse Analytics 싱크
Azure Synapse Analytics에 데이터를 복사하는 경우 복사 작업의 **sink type** 을 **SqlDWSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. |쿼리 문입니다. |예 |
| allowPolyBase |BULKINSERT 메커니즘 대신 PolyBase(있는 경우)를 사용할지 여부를 나타냅니다. <br/><br/> **Azure Synapse Analytics에 데이터를 로드하는 데 권장되는 방법은 PolyBase를 사용하는 것입니다.** |True <br/>False(기본값) |예 |
| polyBaseSettings |**allowPolybase** 속성이 **true** 로 설정된 경우 지정될 수 있는 속성의 그룹입니다. |&nbsp; |예 |
| rejectValue |쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다. <br/><br/>**외부 테이블 만들기(Transact-SQL)** 토픽의 [인수](/sql/t-sql/statements/create-external-table-transact-sql) 섹션에 있는 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. |0(기본값), 1, 2, … |예 |
| rejectType |rejectValue 옵션을 리터럴 값 또는 백분율로 지정할지 여부를 지정합니다. |값(기본값), 백분율 |예 |
| rejectSampleValue |PolyBase가 거부된 행의 비율을 다시 계산하기 전에 검색할 행 수를 결정합니다. |1, 2, … |예. **rejectType** 이 **백분율** 인 경우 |
| useTypeDefault |PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)를 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. |True, False(기본값) |예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 |정수(행 수) |아니요(기본값: 10000) |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예: “00:30:00”(30분). |예 |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Azure Synapse Analytics 커넥터](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>연결된 서비스
Azure Cognitive Search 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AzureSearch** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| url | 검색 서비스에 대한 URL입니다. | 예 |
| key | 검색 서비스에 대한 관리 키입니다. | 예 |

#### <a name="example"></a>예제

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

자세한 내용은 [Azure Cognitive Search 커넥터](data-factory-azure-search-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Azure Cognitive Search 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **AzureSearchIndex** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| type | 형식 속성은 **AzureSearchIndex** 로 설정되어야 합니다.| 예 |
| indexName | 검색 인덱스의 이름입니다. Data Factory는 인덱스를 만들지 않습니다. 인덱스는 Azure Cognitive Search에 있어야 합니다. | 예 |

#### <a name="example"></a>예제

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

자세한 내용은 [Azure Cognitive Search 커넥터](data-factory-azure-search-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>복사 작업의 Azure Cognitive Search 인덱스 싱크
검색 인덱스에 데이터를 복사하는 경우 복사 작업의 **sink type** 을 **AzureSearchIndexSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | 문서가 인덱스에 이미 있는 경우 병합할지 또는 바꿀지를 지정합니다. | 병합(기본값)<br/>업로드| 예 |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달한 경우 검색 인덱스에 데이터를 업로드합니다. | 1~1,000입니다. 기본값은 1,000입니다. | 예 |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Azure Cognitive Search 커넥터](data-factory-azure-search-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>연결된 서비스
연결된 서비스에는 두 가지 유형, 즉 Azure Storage 연결된 서비스와 Azure Storage SAS 연결된 서비스가 있습니다.

#### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
**계정 키** 를 사용하여 Azure 스토리지 계정을 데이터 팩터리에 연결하려면 Azure Storage 연결된 서비스를 만듭니다. Azure Storage 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AzureStorage** 로 설정합니다. 그런 다음 **typeProperties** 섹션에서 다음 속성을 지정하면 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **AzureStorage** |예 |
| connectionString |connectionString 속성에 대한 Azure Storage에 연결하는 데 필요한 정보를 지정합니다. |예 |

**예:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS 연결된 서비스
Azure Storage SAS 연결된 서비스에서 SAS(공유 액세스 서명)을 사용하여 Azure Storage 계정을 Azure Data Factory에 연결할 수 있습니다. 이 서비스는 스토리지의 모든/특정 리소스(Blob/컨테이너)에 대해 제한된/시간 제한 액세스를 데이터 팩터리에 제공합니다. 공유 액세스 서명을 사용하여 Azure 스토리지 계정을 데이터 팩터리에 연결하려면 Azure Storage SAS 연결된 서비스를 만듭니다. Azure Storage SAS 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AzureStorageSas** 로 설정합니다. 그런 다음 **typeProperties** 섹션에서 다음 속성을 지정하면 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **AzureStorageSas** |예 |
| sasUri |BLOB, 컨테이너, 테이블 등의 Azure Storage 리소스에 공유 액세스 서명 URI를 지정합니다. |예 |

**예:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

이러한 연결된 서비스에 대한 자세한 내용은 [Azure Table Storage 커넥터](data-factory-azure-table-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Azure Table 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **AzureTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Azure 테이블 데이터베이스 인스턴스에서 테이블의 이름입니다. |예. azureTableSourceQuery 없이 tableName을 지정하면 테이블의 모든 레코드를 대상에 복사합니다. 또한 azureTableSourceQuery를 지정하면 쿼리를 만족 하는 테이블의 레코드를 대상에 복사합니다. |

#### <a name="example"></a>예제

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

이러한 연결된 서비스에 대한 자세한 내용은 [Azure Table Storage 커넥터](data-factory-azure-table-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="azure-table-source-in-copy-activity"></a>복사 활동의 Azure Table 소스
Azure Table Storage에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **AzureTableSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| AzureTableSourceQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |Azure 테이블 쿼리 문자열. 다음 섹션의 예제를 참조하세요. |아니요. azureTableSourceQuery 없이 tableName을 지정하면 테이블의 모든 레코드를 대상에 복사합니다. 또한 azureTableSourceQuery를 지정하면 쿼리를 만족 하는 테이블의 레코드를 대상에 복사합니다. |
| azureTableSourceIgnoreTableNotFound |존재하지 않는 테이블의 예외를 받아들이는지를 나타냅니다. |TRUE<br/>FALSE |예 |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

이러한 연결된 서비스에 대한 자세한 내용은 [Azure Table Storage 커넥터](data-factory-azure-table-connector.md#copy-activity-properties) 문서를 참조하세요.

### <a name="azure-table-sink-in-copy-activity"></a>복사 활동의 Azure Table 싱크
Azure Table Storage에 데이터를 복사하는 경우 복사 활동의 **sink type** 을 **AzureTableSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |싱크에서 사용할 수 있는 기본 파티션 키 값입니다. |문자열 값입니다. |예 |
| azureTablePartitionKeyName |해당 값이 파티션 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 AzureTableDefaultPartitionKeyValue가 파티션 키로 사용됩니다. |열 이름 |예 |
| azureTableRowKeyName |해당 열 값이 행 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 각 행에 GUID를 사용합니다. |열 이름 |예 |
| azureTableInsertType |Azure 테이블에 데이터를 삽입하는 모드입니다.<br/><br/>이 속성은 출력 테이블에서 파티션 및 행 키가 일치하는 기존 행의 값을 바꿀지 또는 병합할지 제어합니다. <br/><br/>이러한 설정(병합 및 바꾸기)이 작동하는 방법을 알아보려면 [엔터티 삽입 또는 병합](/rest/api/storageservices/Insert-Or-Merge-Entity) 및 [엔터티 삽입 또는 바꾸기](/rest/api/storageservices/Insert-Or-Replace-Entity)를 참조하세요. <br/><br>  이 설정은 테이블 수준이 아니라 행 수준에서 적용되며, 두 옵션 모두 출력 테이블에서 입력에 존재하지 않는 행을 삭제하지 않습니다. |병합(기본값)<br/>replace |예 |
| writeBatchSize |WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다. |정수(행 수) |아니요(기본값: 10000) |
| writeBatchTimeout |WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다. |timespan<br/><br/>예: "00:20:00"(20분) |No (기본적으로 스토리지 클라이언트 기본 시간 제한 값인 90초로 설정) |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
이러한 연결된 서비스에 대한 자세한 내용은 [Azure Table Storage 커넥터](data-factory-azure-table-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>연결된 서비스
Amazon Redshift 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AmazonRedshift** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 서버 |Amazon Redshift 서버의 IP 주소 또는 호스트 이름입니다. |예 |
| 포트 |Amazon Redshift 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트 수입니다. |기본값이 없음: 5439 |
| 데이터베이스 |Amazon Redshift 데이터베이스의 이름입니다. |예 |
| 사용자 이름 |데이터베이스에 대한 액세스 권한이 있는 사용자의 이름입니다. |예 |
| password |사용자 계정의 password입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

자세한 내용은 Amazon Redshift 커넥터 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Amazon Redshift 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Amazon Redshift 데이터베이스에서 테이블의 이름입니다. |아니요(**RelationalSource** 의 **쿼리** 가 지정된 경우) |


#### <a name="example"></a>예제

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
자세한 내용은 Amazon Redshift 커넥터 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
Amazon Redshift에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
자세한 내용은 Amazon Redshift 커넥터 문서를 참조하세요.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>연결된 서비스
IBM DB2 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesDB2** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 서버 |DB2 서버의 이름입니다. |예 |
| 데이터베이스 |DB2 데이터베이스의 이름입니다. |예 |
| 스키마 |데이터베이스에서 스키마의 이름입니다. schema 이름은 대/소문자를 구분합니다. |예 |
| authenticationType |DB2 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명, 기본 및 Windows입니다. |예 |
| 사용자 이름 |기본 또는 Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예 |
| gatewayName |데이터 팩터리 서비스가 온-프레미스 DB2 데이터 베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름 |예 |

#### <a name="example"></a>예제
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
자세한 내용은 IBM DB2 커넥터 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
DB2 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 DB2 데이터베이스 인스턴스에서 테이블의 이름입니다. tableName은 대/소문자를 구분합니다. |아니요(**RelationalSource** 의 **쿼리** 가 지정된 경우)

#### <a name="example"></a>예제
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 IBM DB2 커넥터 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
IBM DB2에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `"query": "select * from "MySchema"."MyTable""` |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |

#### <a name="example"></a>예제
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
자세한 내용은 IBM DB2 커넥터 문서를 참조하세요.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>연결된 서비스
MySQL 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesMySql** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 서버 |MySQL 서버의 이름입니다. |예 |
| 데이터베이스 |MySQL 데이터베이스의 이름입니다. |예 |
| 스키마 |데이터베이스에서 스키마의 이름입니다. |예 |
| authenticationType |MySQL 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 `Basic`입니다. |예 |
| userName |MySQL 데이터베이스에 연결할 사용자 이름을 지정합니다. |예 |
| password |지정한 사용자 계정의 암호를 지정합니다. |예 |
| gatewayName |데이터 팩터리 서비스가 온-프레미스 MySQL 데이터 베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

자세한 내용은 [MySQL 커넥터](data-factory-onprem-mysql-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
MySQL 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 MySQL 데이터베이스 인스턴스에서 테이블의 이름입니다. |아니요(**RelationalSource** 의 **쿼리** 가 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
자세한 내용은 [MySQL 커넥터](data-factory-onprem-mysql-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
MySQL 데이터베이스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |


#### <a name="example"></a>예제
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

자세한 내용은 [MySQL 커넥터](data-factory-onprem-mysql-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>연결된 서비스
Oracle 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesOracle** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| driverType | Oracle Database로 데이터를 복사하거나 Oracle Database에서 데이터를 복사하는 데 사용할 드라이버를 지정합니다. 허용되는 값은 **Microsoft** 또는 **ODP**(기본값)입니다. 드라이버 세부 정보에 대해서는 지원되는 버전 및 설치 섹션을 참조하세요. | 예 |
| connectionString | connectionString 속성에 대한 Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. | 예 |
| gatewayName | 온-프레미스 Oracle 서버에 연결하는 데 사용되는 게이트웨이 이름입니다. |예 |

#### <a name="example"></a>예제
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

자세한 내용은 [Oracle 커넥터](data-factory-onprem-oracle-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Oracle 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **OracleTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Oracle 데이터베이스에 있는 테이블의 이름입니다. |아니요(**OracleSource** 의 **oracleReaderQuery** 가 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
자세한 내용은 [Oracle 커넥터](data-factory-onprem-oracle-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="oracle-source-in-copy-activity"></a>복사 활동의 Oracle 소스
Oracle 데이터베이스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **OracleSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| oracleReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` <br/><br/>지정하지 않는 경우 실행되는 SQL 문: `select * from MyTable` |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Oracle 커넥터](data-factory-onprem-oracle-connector.md#copy-activity-properties) 문서를 참조하세요.

### <a name="oracle-sink-in-copy-activity"></a>복사 활동의 Oracle 싱크
Oracle 데이터베이스에 데이터를 복사하는 경우 복사 활동의 **sink type** 을 **OracleSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |timespan<br/><br/>  예: “00:30:00”(30분). |예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 |정수(행 수) |아니요(기본값: 100) |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. |쿼리 문입니다. |예 |
| sliceIdentifierColumnName |자동 생성된 조각 식별자를 입력할 복사 활동의 열 이름을 지정합니다. 이 식별자는 복사 활동을 다시 실행할 때 특정 조각의 데이터를 정리하는 데 사용됩니다. |이진(32) 데이터 형식이 있는 열의 열 이름입니다. |예 |

#### <a name="example"></a>예제
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
자세한 내용은 [Oracle 커넥터](data-factory-onprem-oracle-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>연결된 서비스
PostgreSQL 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesPostgreSql** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 서버 |PostgreSQL 서버의 이름입니다. |예 |
| 데이터베이스 |PostgreSQL 데이터베이스의 이름입니다. |예 |
| 스키마 |데이터베이스에서 스키마의 이름입니다. schema 이름은 대/소문자를 구분합니다. |예 |
| authenticationType |PostgreSQL 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명, 기본 및 Windows입니다. |예 |
| 사용자 이름 |기본 또는 Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예 |
| gatewayName |데이터 팩터리 서비스가 온-프레미스 PostgreSQL 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
자세한 내용은 [PostgreSQL 커넥터](data-factory-onprem-postgresql-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
PostgreSQL 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 PostgreSQL 데이터베이스 인스턴스에서 테이블의 이름입니다. tableName은 대/소문자를 구분합니다. |아니요(**RelationalSource** 의 **쿼리** 가 지정된 경우) |

#### <a name="example"></a>예제
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
자세한 내용은 [PostgreSQL 커넥터](data-factory-onprem-postgresql-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
PostgreSQL 데이터베이스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: "query": "select * from \"MySchema\".\"MyTable\"". |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

자세한 내용은 [PostgreSQL 커넥터](data-factory-onprem-postgresql-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>연결된 서비스
SAP BW(Business Warehouse) 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **SapBw** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
서버 | SAP BW 인스턴스가 상주하는 서버의 이름. | 문자열 | 예
systemNumber | SAP BW 시스템의 시스템 번호. | 문자열로 표현되는 두 자리 10진수. | 예
clientId | SAP W 시스템에 있는 클라이언트의 클라이언트 ID. | 문자열로 표현되는 세 자리 10진수. | 예
사용자 이름 | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름 | 문자열 | 예
password | 사용자에 대한 암호입니다. | 문자열 | 예
gatewayName | Data Factory 서비스가 온-프레미스 SAP BW 인스턴스에 연결하는 데 사용해야 하는 게이트웨이의 이름. | 문자열 | 예
encryptedCredential | 암호화된 자격 증명 문자열. | 문자열 | 예

#### <a name="example"></a>예제

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

자세한 내용은 [SAP Business Warehouse 커넥터](data-factory-sap-business-warehouse-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
SAP BW 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정합니다. **RelationalTable** 형식의 SAP BW 데이터 세트에 대해 지원되는 type별 속성은 없습니다.

#### <a name="example"></a>예제

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
자세한 내용은 [SAP Business Warehouse 커넥터](data-factory-sap-business-warehouse-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
SAP Business Warehouse에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query | SAP BW 인스턴스에서 데이터를 읽을 MDX 쿼리를 지정합니다. | MDX 쿼리. | 예 |

#### <a name="example"></a>예제

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

자세한 내용은 [SAP Business Warehouse 커넥터](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>연결된 서비스
SAP HANA 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **SapHana** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
서버 | SAP HANA 인스턴스가 상주하는 서버의 이름. 서버에서 사용자 지정된 포트를 사용하는 경우 `server:port`를 지정합니다. | 문자열 | 예
authenticationType | 인증 유형입니다. | 문자열을 생성할 수 있습니다. "Basic" 또는 "Windows" | 예
사용자 이름 | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름 | 문자열 | 예
password | 사용자에 대한 암호입니다. | 문자열 | 예
gatewayName | Data Factory 서비스가 온-프레미스 SAP HANA 인스턴스에 연결하는 데 사용해야 하는 게이트웨이의 이름. | 문자열 | 예
encryptedCredential | 암호화된 자격 증명 문자열. | 문자열 | 예

#### <a name="example"></a>예제

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
자세한 내용은 [SAP HANA 커넥터](data-factory-sap-hana-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
SAP HANA 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정합니다. **RelationalTable** 형식의 SAP HANA 데이터 세트에 대해 지원되는 type별 속성은 없습니다.

#### <a name="example"></a>예제

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
자세한 내용은 [SAP HANA 커넥터](data-factory-sap-hana-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
SAP HANA 데이터 저장소에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query | SAP HANA 인스턴스에서 데이터를 읽을 SQL 쿼리를 지정합니다. | SQL 쿼리. | 예 |


#### <a name="example"></a>예제


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

자세한 내용은 [SAP HANA 커넥터](data-factory-sap-hana-connector.md#copy-activity-properties) 문서를 참조하세요.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>연결된 서비스
SQL Server 데이터베이스를 Data Factory에 연결하기 위해 **OnPremisesSqlServer** 유형의 연결된 서비스를 만듭니다. 다음 표에서는 SQL Server 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

다음 표에서는 SQL Server 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |type 속성은 **OnPremisesSqlServer** 로 설정해야 합니다. |예 |
| connectionString |SQL 인증 또는 Windows 인증을 사용하여 SQL Server 데이터베이스에 연결하는 데 필요한 connectionString 정보를 지정합니다. |예 |
| gatewayName |Data Factory 서비스가 SQL Server 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |
| 사용자 이름 |Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. 예: **domainname\\username**. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예 |

**New-AzDataFactoryEncryptValue** cmdlet을 사용하여 자격 증명을 암호화하고 다음 예제와 같이 연결 문자열에 해당 자격 증명을 사용할 수 있습니다(**EncryptedCredential** 속성).

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>예제: SQL 인증을 사용하는 JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>예제: Windows 인증을 사용하는 JSON

사용자 이름 및 암호가 지정된 경우 게이트웨이는 이러한 정보를 사용해 지정된 사용자 계정을 가장하여 SQL Server Database에 연결합니다. 그렇지 않은 경우 게이트웨이는 게이트웨이의 보안 컨텍스트(시작 계정)를 사용하여 SQL Server에 직접 연결합니다.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

자세한 내용은 [SQL Server 커넥터](data-factory-sqlserver-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
SQL Server 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **SqlServerTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 SQL Server 데이터베이스 인스턴스에서 테이블 또는 보기의 이름입니다. |예 |

#### <a name="example"></a>예제
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [SQL Server 커넥터](data-factory-sqlserver-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="sql-source-in-copy-activity"></a>복사 활동의 SQL 소스
SQL Server 데이터베이스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **SqlSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| SqlReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` 입력 데이터 세트에 의해 참조되는 데이터베이스의 여러 테이블을 참조할 수 있습니다. 지정하지 않는 경우 실행되는 SQL 문: select from MyTable. |예 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. |저장 프로시저의 이름입니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |

**sqlReaderQuery** 가 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 SQL Server 데이터베이스 원본에 대해 이 쿼리를 실행합니다.

또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters** 를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).

sqlReaderQuery 또는 sqlReaderStoredProcedureName을 지정하지 않으면 structure 섹션에 정의된 열을 사용하여 SQL Server Database에 대해 실행할 선택 쿼리를 작성합니다. 데이터 세트 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

> [!NOTE]
> **sqlReaderStoredProcedureName** 을 사용하는 경우에도 데이터 세트 JSON에서 **tableName** 속성 값을 지정해야 합니다. 그러나 이 테이블에 대해 수행되는 유효성 검사는 없습니다.


#### <a name="example"></a>예제
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

위의 예에서는 SqlSource에 대해 **sqlReaderQuery** 가 지정됩니다. 복사 작업은 데이터를 가져오는 SQL Server 데이터베이스 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters** 를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우). sqlReaderQuery는 입력 데이터 세트에서 참조하는 데이터베이스 내의 여러 테이블을 참조할 수 있습니다. 즉, 데이터 세트의 tableName typeProperty로 설정된 테이블만 참조하도록 제한되지 않습니다.

sqlReaderQuery 또는 sqlReaderStoredProcedureName을 지정하지 않으면 structure 섹션에 정의된 열을 사용하여 SQL Server Database에 대해 실행할 선택 쿼리를 작성합니다. 데이터 세트 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

자세한 내용은 [SQL Server 커넥터](data-factory-sqlserver-connector.md#copy-activity-properties) 문서를 참조하세요.

### <a name="sql-sink-in-copy-activity"></a>복사 활동의 SQL 싱크
SQL Server 데이터베이스에 데이터를 복사하는 경우 복사 활동의 **sink type** 을 **SqlSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예: “00:30:00”(30분). |예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 |정수(행 수) |아니요(기본값: 10000) |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. 자세한 내용은 반복성 섹션을 참조하세요. |쿼리 문입니다. |예 |
| sliceIdentifierColumnName |자동 생성된 조각 식별자를 입력할 복사 활동의 열 이름을 지정합니다. 이 식별자는 복사 활동을 다시 실행할 때 특정 조각의 데이터를 정리하는 데 사용됩니다. 자세한 내용은 반복성 섹션을 참조하세요. |이진(32) 데이터 형식이 있는 열의 열 이름입니다. |예 |
| sqlWriterStoredProcedureName |대상 테이블에 대한 데이터 Upsert(업데이트/삽입)를 수행하는 저장 프로시저의 이름입니다. |저장 프로시저의 이름입니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. |테이블 유형 이름 |예 |

#### <a name="example"></a>예제
파이프라인은 이러한 입력 및 출력 데이터 세트를 사용하도록 구성되며 매시간 실행되도록 예약되는 복사 활동을 포함합니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource** 로 설정되고 **싱크** 형식은 **SqlSink** 로 설정됩니다.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [SQL Server 커넥터](data-factory-sqlserver-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>연결된 서비스
Sybase 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesSybase** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 서버 |Sybase 서버의 이름입니다. |예 |
| 데이터베이스 |Sybase 데이터베이스의 이름입니다. |예 |
| 스키마 |데이터베이스에서 스키마의 이름입니다. |예 |
| authenticationType |Sybase 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명, 기본 및 Windows입니다. |예 |
| 사용자 이름 |기본 또는 Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예 |
| gatewayName |데이터 팩터리 서비스가 온-프레미스 Sybase 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |

#### <a name="example"></a>예제
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

자세한 내용은 [Sybase 커넥터](data-factory-onprem-sybase-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Sybase 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Sybase 데이터베이스 인스턴스에서 테이블의 이름입니다. |아니요(**RelationalSource** 의 **쿼리** 가 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [Sybase 커넥터](data-factory-onprem-sybase-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
Sybase 데이터베이스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

자세한 내용은 [Sybase 커넥터](data-factory-onprem-sybase-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>연결된 서비스
Teradata 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesTeradata** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 서버 |Teradata 서버의 이름입니다. |예 |
| authenticationType |Teradata 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명, 기본 및 Windows입니다. |예 |
| 사용자 이름 |기본 또는 Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예 |
| gatewayName |데이터 팩터리 서비스가 온-프레미스 Teradata 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |

#### <a name="example"></a>예제
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

자세한 내용은 [Teradata 커넥터](data-factory-onprem-teradata-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Teradata Blob 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정합니다. 현재 Teradata 데이터 세트에 대해 지원되는 형식 속성은 없습니다.

#### <a name="example"></a>예제
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [Teradata 커넥터](data-factory-onprem-teradata-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
Teradata 데이터베이스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` |예 |

#### <a name="example"></a>예제

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

자세한 내용은 [Teradata 커넥터](data-factory-onprem-teradata-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>연결된 서비스
Cassandra 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesCassandra** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 호스트 |Cassandra 서버에 대한 하나 이상의 IP 주소 또는 호스트 이름.<br/><br/>모든 서버에 동시에 연결하려면 쉼표로 구분된 IP 주소 또는 호스트 이름 목록을 지정합니다. |예 |
| 포트 |Cassandra 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. |아니요. 기본값: 9042 |
| authenticationType |Basic 또는 Anonymous |예 |
| 사용자 이름 |사용자 계정의 사용자 이름을 지정합니다. |예. authenticationType은 Basic으로 설정됩니다. |
| password |사용자 계정으로 password를 지정합니다. |예. authenticationType은 Basic으로 설정됩니다. |
| gatewayName |온-프레미스 Cassandra 데이터베이스에 연결하는 데 사용되는 게이트웨이 이름입니다. |예 |
| encryptedCredential |게이트웨이에 의해 암호화된 자격 증명입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

자세한 내용은 [Cassandra 커넥터](data-factory-onprem-cassandra-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Cassandra 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **CassandraTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| keyspace |Cassandra 데이터베이스의 키스페이스 또는 스키마의 이름입니다. |예(**CassandraSource** 의 **query** 가 정의되지 않은 경우) |
| tableName |Cassandra 데이터베이스에 있는 테이블의 이름입니다. |예(**CassandraSource** 의 **query** 가 정의되지 않은 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [Cassandra 커넥터](data-factory-onprem-cassandra-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="cassandra-source-in-copy-activity"></a>복사 활동의 Cassandra 소스
Cassandra에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **CassandraSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL-92 쿼리 또는 CQL 쿼리입니다. [CQL 참조](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)를 참조하세요. <br/><br/>SQL 쿼리를 사용할 경우 **keyspace name.table name** 을 지정하여 쿼리하려는 테이블을 나타냅니다. |아니요(데이터 세트의 tableName 및 keyspace가 정의된 경우) |
| consistencyLevel |일관성 수준은 클라이언트 애플리케이션에 데이터를 반환하기 전에 읽기 요청에 응답해야 하는 복제본 수를 지정합니다. Cassandra는 데이터의 지정된 수의 복제본이 읽기 요청을 충족하는지 확인합니다. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. 자세한 내용은 [데이터 일관성 구성](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) 을 참조하세요. |아니요. 기본값은 ONE입니다. |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [Cassandra 커넥터](data-factory-onprem-cassandra-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>연결된 서비스
MongoDB 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesMongoDB** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 서버 |MongoDB 서버의 IP 주소 또는 호스트 이름입니다. |예 |
| 포트 |MongoDB 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. |선택 사항, 기본값: 27017 |
| authenticationType |Basic 또는 Anonymous입니다. |예 |
| 사용자 이름 |MongoDB에 액세스하는 사용자 계정입니다. |예(기본 인증을 사용하는 경우) |
| password |사용자에 대한 암호입니다. |예(기본 인증을 사용하는 경우) |
| authSource |인증에 대한 자격 증명을 확인하는 데 사용하려는 MongoDB 데이터베이스의 이름입니다. |선택 사항(기본 인증을 사용하는 경우). 기본값: 관리자 계정 및 databaseName 속성을 사용하는 지정된 데이터베이스를 사용합니다. |
| databaseName |액세스하려는 MongoDB 데이터베이스의 이름입니다. |예 |
| gatewayName |데이터 저장소에 액세스하는 게이트웨이의 이름입니다. |예 |
| encryptedCredential |게이트웨이에 의해 암호화된 자격 증명입니다. |Optional |

#### <a name="example"></a>예제

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

자세한 내용은 [MongoDB 커넥터](data-factory-on-premises-mongodb-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
MongoDB 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **MongoDbCollection** 으로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| collectionName |MongoDB 데이터베이스에 있는 컬렉션의 이름입니다. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

자세한 내용은 [MongoDB 커넥터](data-factory-on-premises-mongodb-connector.md#dataset-properties) 문서를 참조하세요.

#### <a name="mongodb-source-in-copy-activity"></a>복사 활동의 MongoDB 소스
MongoDB에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **MongoDbSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL-92 쿼리 문자열입니다. 예: `select * from MyTable` |아니요(**데이터 세트** 의 **collectionName** 이 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

자세한 내용은 [MongoDB 커넥터](data-factory-on-premises-mongodb-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>연결된 서비스
Amazon S3 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **AwsAccessKey** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| accessKeyID |비밀 액세스 키의 ID입니다. |문자열 |예 |
| secretAccessKey |비밀 액세스 키 자체입니다. |암호화된 비밀 문자열 |예 |

#### <a name="example"></a>예제
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

자세한 내용은 [Amazon S3 커넥터](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties) 문서를 참조 하세요.

### <a name="dataset"></a>데이터 세트
Amazon S3 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **AmazonS3** 으로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| bucketName |S3 버킷 이름입니다. |String |예 |
| key |S3 개체 키입니다. |String |예 |
| 접두사 |S3 개체 키에 대한 접두사입니다. 이 접두사로 시작하는 키를 가진 개체가 선택됩니다. 키가 비어 있을 때에만 적용됩니다. |String |예 |
| 버전 |S3 버전 관리를 사용하도록 설정되면 S3 개체의 버전입니다. |String |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 | |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 | |


> [!NOTE]
> bucketName + 키는 S3 개체의 위치를 지정합니다. 여기서 버킷은 S3 개체에 대한 루트 컨테이너이며 키는 S3 개체의 전체 경로입니다.

#### <a name="example-sample-dataset-with-prefix"></a>예제: 샘플 데이터 세트(prefix 포함)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>예제: 샘플 데이터 집합(version 포함)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>예제: S3에 대한 동적 경로
이 샘플에서는 Amazon S3 데이터 세트의 키 및 bucketName 속성에 대해 고정 값을 사용합니다.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

SliceStart와 같은 시스템 변수를 사용하여 데이터 팩터리가 런타임에 동적으로 키와 bucketName 계산하게 할 수 있습니다.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Amazon S3 데이터 세트의 접두사 속성에 대해서도 동일하게 수행할 수 있습니다. 지원되는 함수 및 변수 목록은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md) 를 참조하세요.

자세한 내용은 [Amazon S3 커넥터](data-factory-amazon-simple-storage-service-connector.md#dataset-properties) 문서를 참조 하세요.

### <a name="file-system-source-in-copy-activity"></a>복사 활동의 파일 시스템 소스
Amazon S3에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **FileSystemSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.


| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |S3 개체를 디렉터리 아래에 재귀적으로 나열할 것인지를 지정합니다. |true/false |예 |


#### <a name="example"></a>예제


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

자세한 내용은 [Amazon S3 커넥터](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties) 문서를 참조 하세요.

## <a name="file-system"></a>파일 시스템


### <a name="linked-service"></a>연결된 서비스
**온-프레미스 파일 서버** 연결 서비스를 사용하면 Azure Data Factory에 온-프레미스 파일 시스템을 연결할 수 있습니다. 다음 표에서는 온-프레미스 파일 서버 연결 서비스에 지정된 JSON 요소에 대해 설명합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |type 속성은 **OnPremisesFileServer** 로 설정되어야 합니다. |예 |
| host |복사할 폴더의 루트 경로를 지정하고 있습니다. 문자열에서 특수 문자로 이스케이프 문자 '\'를 사용합니다. 예제를 살펴보려면 연결된 서비스 및 데이터 세트 정의 샘플을 참조하세요. |예 |
| userId |서버에 대한 액세스 권한이 있는 사용자의 ID를 지정합니다. |아니요(encryptedCredential을 선택하는 경우) |
| password |사용자(userid)의 암호를 지정합니다. |아니요(encryptedcredential을 선택하는 경우) |
| encryptedCredential |New-AzDataFactoryEncryptValue cmdlet을 실행하여 얻을 수 있는 암호화된 자격 증명을 지정합니다. |아니요(일반 텍스트에 userid 및 암호를 지정하는 경우) |
| gatewayName |Data Factory에서 온-프레미스 파일 서버에 연결하는 데 사용해야 하는 게이트웨이의 이름을 지정하고 있습니다. |예 |

#### <a name="sample-folder-path-definitions"></a>샘플 폴더 경로 정의

| 시나리오 | 연결된 서비스 정의의 호스트 | 데이터 세트 정의의 folderPath |
| --- | --- | --- |
| 데이터 관리 게이트웨이 컴퓨터의 로컬 폴더: <br/><br/>예제: D:\\\* 또는 D:\folder\subfolder\\* |D:\\\\(데이터 관리 게이트웨이 버전 2.0 이상) <br/><br/> localhost(데이터 관리 게이트웨이 버전 2.0 미만) |.\\\\ 또는 folder\\\\subfolder(데이터 관리 게이트웨이 버전 2.0 이상) <br/><br/>D:\\\\ 또는 D:\\\\folder\\\\subfolder(게이트웨이 버전 2.0 미만) |
| 원격 공유 폴더: <br/><br/>예: \\\\myserver\\share\\\* 또는 \\\\myserver\\share\\folder\\subfolder\\\* |\\\\\\\\myserver\\\\share |.\\\\ 또는 folder\\\\subfolder |


#### <a name="example-using-username-and-password-in-plain-text"></a>예제: 일반 텍스트에 사용자 이름 및 암호 사용

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>예제: encryptedcredential 사용

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

자세한 내용은 [파일 시스템 커넥터](data-factory-onprem-file-system-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
파일 시스템 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **FileShare** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |폴더의 하위 경로를 지정하고 있습니다. 문자열에서 특수 문자로 이스케이프 문자 '\'를 사용합니다. 예제를 살펴보려면 연결된 서비스 및 데이터 세트 정의 샘플을 참조하세요.<br/><br/>이 속성을 **partitionBy** 와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |테이블이 폴더의 특정 파일을 참조하도록 하려는 경우 **folderPath** 에 파일 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 세트에 대한 fileName이 지정되는 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>`Data.<Guid>.txt`(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |예 |
| fileFilter |모든 파일이 아닌 folderPath의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다. <br/><br/>허용 되는 값은 `*`(여러 문자) 및 `?`(하나의 문자)입니다.<br/><br/>예 1: "fileFilter": "*.log"<br/>예 2: "fileFilter": 2016-1-?.txt"<br/><br/>fileFilter는 FileShare 입력 데이터 세트에 적용할 수 있습니다. |예 |
| partitionedBy |partitionedBy를 사용하면 시계열 데이터의 동적 folderPath/fileName을 지정할 수 있습니다. 예를 들어 매시간 데이터에 대한 매개 변수를 포함하는 folderPath가 있습니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 이고 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

> [!NOTE]
> fileName 및 fileFilter는 동시에 사용할 수 없습니다.

#### <a name="example"></a>예제

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [파일 시스템 커넥터](data-factory-onprem-file-system-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="file-system-source-in-copy-activity"></a>복사 활동의 파일 시스템 소스
파일 시스템에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **FileSystemSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True, False(기본값) |예 |

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
자세한 내용은 [파일 시스템 커넥터](data-factory-onprem-file-system-connector.md#copy-activity-properties) 문서를 참조하세요.

### <a name="file-system-sink-in-copy-activity"></a>복사 활동의 파일 시스템 싱크
파일 시스템에 데이터를 복사하는 경우 복사 활동의 **sink type** 을 **FileSystemSink** 로 설정하고 **sink** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| copyBehavior |원본이 BlobSource 또는 FileSystem인 경우 복사 동작을 정의합니다. |**PreserveHierarchy:** 대상 폴더에서 파일 계층 구조를 유지합니다. 즉, 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/>**FlattenHierarchy:** 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 만들어집니다. 대상 파일은 자동 생성된 이름으로 만들어집니다.<br/><br/>**MergeFiles:** 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 이름/Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. |예 |

auto-

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [파일 시스템 커넥터](data-factory-onprem-file-system-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>연결된 서비스
FTP 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **FtpServer** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| 호스트 |FTP 서버의 이름 또는 IP 주소 |예 |&nbsp; |
| authenticationType |인증 유형 지정 |예 |기본, 익명 |
| 사용자 이름 |FTP 서버에 액세스하는 사용자 |예 |&nbsp; |
| password |사용자(사용자 이름) 암호 |예 |&nbsp; |
| encryptedCredential |FTP 서버 액세스를 위한 암호화된 자격 증명 |예 |&nbsp; |
| gatewayName |온-프레미스 FTP 서버에 연결하기 위한 데이터 관리 게이트웨이의 이름입니다. |예 |&nbsp; |
| 포트 |FTP 서버가 수신 대기하는 포트 |예 |21 |
| enableSsl |SSL/TLS 채널을 통해 FTP를 사용할지 여부 |예 |true |
| enableServerCertificateValidation |SSL/TLS 채널을 통해 FTP를 사용할 때 서버 TLS/SSL 인증서 유효성 검사를 사용할지 여부를 지정합니다. |예 |true |

#### <a name="example-using-anonymous-authentication"></a>예제: 익명 인증 사용

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>예제: 기본 인증에 일반 텍스트 형식의 사용자 이름 및 암호 사용

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>예제: port, enableSsl, enableServerCertificateValidation 사용

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>예제: 인증 및 게이트웨이에 encryptedCredential 사용

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

자세한 내용은 [FTP 커넥터](data-factory-ftp-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
FTP 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **FileShare** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |폴더에 대한 하위 경로. 문자열의 특수 문자에 이스케이프 문자 '\'를 사용합니다. 예제를 살펴보려면 연결된 서비스 및 데이터 세트 정의 샘플을 참조하세요.<br/><br/>이 속성을 **partitionBy** 와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예
| fileName |테이블이 폴더의 특정 파일을 참조하도록 하려는 경우 **folderPath** 에 파일 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 세트에 대한 fileName이 지정되는 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>`Data.<Guid>.txt`(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |예 |
| fileFilter |모든 파일이 아닌 folderPath의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다.<br/><br/>허용 되는 값은 `*`(여러 문자) 및 `?`(하나의 문자)입니다.<br/><br/>예 1: `"fileFilter": "*.log"`<br/>예 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter는 FileShare 입력 데이터 세트에 적용할 수 있습니다. 이 속성은 HDFS에는 지원되지 않습니다. |예 |
| partitionedBy |동적 folderPath, 시계열 데이터에 대한 filename을 지정하는 데 partitionedBy를 사용할 수 있습니다. 예를 들어 매시간 데이터에 대한 매개 변수가 있는 folderPath입니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 이고 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |
| useBinaryTransfer |이전 전송 모드를 사용할지 여부를 지정합니다. 이진 모드인 경우 true이고 ASCII인 경우 false입니다. 기본값은 True입니다. 이 속성은 연결된 서비스 유형이 FtpServer인 경우에만 사용할 수 있습니다. |예 |

> [!NOTE]
> filename 및 fileFilter는 동시에 사용할 수 없습니다.

#### <a name="example"></a>예제

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

자세한 내용은 [FTP 커넥터](data-factory-ftp-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="file-system-source-in-copy-activity"></a>복사 활동의 파일 시스템 소스
FTP 서버에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **FileSystemSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True, False(기본값) |예 |

#### <a name="example"></a>예제

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

자세한 내용은 [FTP 커넥터](data-factory-ftp-connector.md#copy-activity-properties) 문서를 참조하세요.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>연결된 서비스
HDFS 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **Hdfs** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |형식 속성은 다음으로 설정해야 함: **Hdfs** |예 |
| Url |HDFS에 대한 URL |예 |
| authenticationType |익명 또는 Windows입니다. <br><br> HDFS 커넥터에 **Kerberos 인증** 을 사용하려면 이 섹션을 참조하여 온-프레미스 환경을 적절히 설정합니다. |예 |
| userName |Windows 인증에 대한 사용자 이름. |예(Windows 인증에 대한) |
| password |Windows 인증에 대한 암호. |예(Windows 인증에 대한) |
| gatewayName |데이터 팩터리 서비스가 HDFS에 연결하는 데 사용해야 하는 게이트웨이의 이름. |예 |
| encryptedCredential |액세스 자격 증명의 [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) 출력. |예 |

#### <a name="example-using-anonymous-authentication"></a>예제: 익명 인증 사용

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>예제: Windows 인증 사용

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

자세한 내용은 HDFS 커넥터 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
HDFS 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **FileShare** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |파일의 경로입니다. 예: `myfolder`<br/><br/>문자열의 특수 문자에 이스케이프 문자 '\'를 사용합니다. 예: 폴더\하위 폴더의 경우 폴더\\\\하위 폴더를 지정하고 d:\samplefolder의 경우 d:\\\\samplefolder를 지정합니다.<br/><br/>이 속성을 **partitionBy** 와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |테이블이 폴더의 특정 파일을 참조하도록 하려는 경우 **folderPath** 에 파일 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 세트에 대한 fileName이 지정되는 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>`Data.<Guid>.txt`(예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |예 |
| partitionedBy |동적 folderPath, 시계열 데이터에 대한 filename을 지정하는 데 partitionedBy를 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대해 매개 변수화됩니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

> [!NOTE]
> filename 및 fileFilter는 동시에 사용할 수 없습니다.

#### <a name="example"></a>예제

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

자세한 내용은 HDFS 커넥터 문서를 참조하세요.

### <a name="file-system-source-in-copy-activity"></a>복사 활동의 파일 시스템 소스
HDFS에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **FileSystemSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

**FileSystemSource** 는 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True, False(기본값) |예 |

#### <a name="example"></a>예제

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

자세한 내용은 HDFS 커넥터 문서를 참조하세요.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>연결된 서비스
SFTP 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **Sftp** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| host | SFTP 서버의 이름 또는 IP 주소입니다. |예 |
| 포트 |SFTP 서버가 수신하는 포트입니다. 기본값은 21입니다. |예 |
| authenticationType |인증 유형을 지정합니다. 허용되는 값은 **기본** 및 **SshPublicKey** 입니다. <br><br> 더 많은 속성 및 각 속성의 JSON 샘플은 기본 인증 사용 및 [SSH 공개 키 인증 사용](#using-ssh-public-key-authentication) 섹션을 참조하세요. |예 |
| skipHostKeyValidation | 호스트 키 유효성 검사를 건너뛸지 여부를 지정합니다. | 아니요. 기본값: false |
| hostKeyFingerprint | 호스트 키의 지문을 지정합니다. | `skipHostKeyValidation`이 false로 지정되면 필수입니다.  |
| gatewayName |온-프레미스 SFTP 서버에 연결하기 위한 데이터 관리 게이트웨이의 이름입니다. | 온-프레미스 SFTP 서버에서 데이터를 복사하는 경우에는 필수입니다. |
| encryptedCredential | SFTP 서버 액세스를 위한 암호화된 자격 증명입니다. 복사 마법사 또는 ClickOnce 팝업 대화 상자에서 기본 인증(사용자 이름 + 암호) 또는 SshPublicKey 인증(사용자 이름 + 프라이빗 키 경로 또는 콘텐츠)를 지정할 때 자정으로 생성됩니다. | 아니요. 온-프레미스 SFTP 서버에서 데이터를 복사하는 경우에만 적용됩니다. |

#### <a name="example-using-basic-authentication"></a>예제: 기본 인증 사용

기본 인증을 사용하려면 `authenticationType`을 `Basic`으로 설정하고, 마지막 섹션에서 소개한 SFTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 사용자 이름 | SFTP 서버에 액세스하는 사용자. |예 |
| password | 사용자(사용자 이름) 암호. | 예 |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>예제: **암호화된 자격 증명으로 기본 인증**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**SSH 공개 키 인증 사용**

기본 인증을 사용하려면 `authenticationType`을 `SshPublicKey`으로 설정하고, 마지막 섹션에서 소개한 SFTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 사용자 이름 |SFTP 서버에 액세스하는 사용자 |예 |
| privateKeyPath | 게이트웨이에서 액세스할 수 있는 프라이빗 키 파일의 절대 경로를 지정합니다. | `privateKeyPath` 또는 `privateKeyContent`를 지정합니다. <br><br> 온-프레미스 SFTP 서버에서 데이터를 복사하는 경우에만 적용됩니다. |
| privateKeyContent | 프라이빗 키 콘텐츠의 직렬화된 문자열입니다. 복사 마법사는 프라이빗 키 파일을 읽고 프라이빗 키 콘텐츠를 자동으로 추출할 수 있습니다. 다른 도구/SDK를 사용하는 경우 privateKeyPath 속성을 대신 사용합니다. | `privateKeyPath` 또는 `privateKeyContent`를 지정합니다. |
| passPhrase | 키 파일이 암호문으로 보호되는 경우 프라이빗 키를 해독하는 암호문/암호를 지정합니다. | 프라이빗 키 파일이 암호문으로 보호되는 경우에는 필수입니다. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>예: **프라이빗 키 콘텐츠를 사용하여 SshPublicKey 인증**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

자세한 내용은 [SFTP 커넥터](data-factory-sftp-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
SFTP 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **FileShare** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |폴더에 대한 하위 경로. 문자열의 특수 문자에 이스케이프 문자 '\'를 사용합니다. 예제를 살펴보려면 연결된 서비스 및 데이터 세트 정의 샘플을 참조하세요.<br/><br/>이 속성을 **partitionBy** 와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |테이블이 폴더의 특정 파일을 참조하도록 하려는 경우 **folderPath** 에 파일 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 세트에 대한 fileName이 지정되는 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>`Data.<Guid>.txt`(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |예 |
| fileFilter |모든 파일이 아닌 folderPath의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다.<br/><br/>허용 되는 값은 `*`(여러 문자) 및 `?`(하나의 문자)입니다.<br/><br/>예 1: `"fileFilter": "*.log"`<br/>예 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter는 FileShare 입력 데이터 세트에 적용할 수 있습니다. 이 속성은 HDFS에는 지원되지 않습니다. |예 |
| partitionedBy |동적 folderPath, 시계열 데이터에 대한 filename을 지정하는 데 partitionedBy를 사용할 수 있습니다. 예를 들어 매시간 데이터에 대한 매개 변수가 있는 folderPath입니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사** 하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |
| useBinaryTransfer |이전 전송 모드를 사용할지 여부를 지정합니다. 이진 모드인 경우 true이고 ASCII인 경우 false입니다. 기본값은 True입니다. 이 속성은 연결된 서비스 유형이 FtpServer인 경우에만 사용할 수 있습니다. |예 |

> [!NOTE]
> filename 및 fileFilter는 동시에 사용할 수 없습니다.

#### <a name="example"></a>예제

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

자세한 내용은 [SFTP 커넥터](data-factory-sftp-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="file-system-source-in-copy-activity"></a>복사 활동의 파일 시스템 소스
SFTP 소스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **FileSystemSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True, False(기본값) |예 |



#### <a name="example"></a>예제

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

자세한 내용은 [SFTP 커넥터](data-factory-sftp-connector.md#copy-activity-properties) 문서를 참조하세요.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>연결된 서비스
HTTP 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **Http** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| url | 웹 서버에 대한 기본 URL입니다. | 예 |
| authenticationType | 인증 유형을 지정합니다. 허용되는 값: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> 각 인증 형식에 대한 더 많은 속성 및 JSON 샘플은 표 아래 섹션을 참조하세요. | 예 |
| enableServerCertificateValidation | 원본이 HTTPS 웹 서버인 경우 서버 TLS/SSL 인증서 유효성 검사를 사용할지 여부를 지정합니다. | 아니요. 기본값은 True입니다. |
| gatewayName | 온-프레미스 HTTP 소스에 연결하기 위한 데이터 관리 게이트웨이의 이름입니다. | 온-프레미스 HTTP 소스에서 데이터를 복사하는 경우에는 필수입니다. |
| encryptedCredential | HTTP 엔드포인트 액세스를 위한 암호화된 자격 증명입니다. 복사 마법사 또는 ClickOnce 팝업 대화 상자에서 인증 정보를 구성할 때 자동 생성됩니다. | 아니요. 온-프레미스 HTTP 서버에서 데이터를 복사하는 경우에만 적용됩니다. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>예제: Basic, Digest 또는 Windows 인증 사용
`authenticationType`을 `Basic`, `Digest` 또는 `Windows`로 설정하고, 위에서 소개한 HTTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 사용자 이름 | HTTP 엔드포인트 액세스를 위한 사용자 이름입니다. | 예 |
| password | 사용자(사용자 이름) 암호. | 예 |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>예제: ClientCertificate 인증 사용

기본 인증을 사용하려면 `authenticationType`을 `ClientCertificate`로 설정하고, 위에서 소개한 HTTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| embeddedCertData | PFX(개인 정보 교환) 파일의 이진 데이터에 대해 Base64로 인코딩된 콘텐츠 | `embeddedCertData` 또는 `certThumbprint`를 지정합니다. |
| certThumbprint | 게이트웨이 컴퓨터의 인증서 저장소에 설치된 인증서의 지문입니다. 온-프레미스 HTTP 소스에서 데이터를 복사하는 경우에만 적용됩니다. | `embeddedCertData` 또는 `certThumbprint`를 지정합니다. |
| password | 인증서와 연결된 암호입니다. | 예 |

인증에 `certThumbprint`를 사용하고 인증서가 로컬 컴퓨터의 개인 저장소에 설치된 경우 게이트웨이 서비스에 읽기 권한을 부여해야 합니다.

1. MMC(Microsoft Management Console)를 시작합니다. **로컬 컴퓨터** 를 대상으로 하는 **인증서** 스냅인을 추가합니다.
2. **인증서**, **개인** 을 확장하고 **인증서** 를 클릭합니다.
3. 개별 저장소에서 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**->**프라이빗 키 관리...** 를 선택합니다.
3. **보안** 탭에서 인증서에 대한 읽기 권한으로 데이터 관리 게이트웨이 호스트 서비스를 실행 중인 사용자 계정을 추가합니다.

**예제: 클라이언트 인증서 사용:** 이 연결된 서비스는 데이터 팩터리를 온-프레미스 HTTP 웹 서버에 연결합니다. 데이터 관리 게이트웨이가 설치된 컴퓨터에 설치된 클라이언트 인증서를 사용합니다.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>예제: 파일로 클라이언트 인증서 사용
이 연결된 서비스는 데이터 팩터리를 온-프레미스 HTTP 웹 서버에 연결합니다. 데이터 관리 게이트웨이가 설치된 컴퓨터에서 클라이언트 인증서 파일을 사용합니다.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

자세한 내용은 [HTTP 커넥터](data-factory-http-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
HTTP 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **Http** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 경로를 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. <br><br> 동적 URL을 구성하려면 [데이터 팩터리 함수 및 시스템 변수](data-factory-functions-variables.md)를 사용할 수 있습니다. 예제: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` | 예 |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **GET** 또는 **POST** 입니다. | 아니요. 기본값은 `GET`입니다. |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 예 |
| requestBody | HTTP 요청의 본문입니다. | 예 |
| format | 데이터를 구문 분석하지 않고 **HTTP 엔드포인트에서 데이터를 그대로 검색** 하려면 이 서식 설정을 건너뜁니다. <br><br> 복사 중에 HTTP 응답 내용을 구문 분석하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

#### <a name="example-using-the-get-default-method"></a>예제: GET(기본) 메서드 사용

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>예제: POST 메서드 사용

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
자세한 내용은 [HTTP 커넥터](data-factory-http-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="http-source-in-copy-activity"></a>복사 활동의 HTTP 소스
HTTP 소스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **HttpSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(TimeSpan)입니다. 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. | 아니요. 기본값: 00:01:40 |


#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [HTTP 커넥터](data-factory-http-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="odata"></a>OData

### <a name="linked-service"></a>연결된 서비스
OData 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OData** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| url |OData 서비스의 URL입니다. |예 |
| authenticationType |OData 소스에 연결하는 데 사용되는 인증 형식입니다. <br/><br/> 클라우드 OData의 경우 가능한 값은 익명, 기본 및 OAuth입니다(Azure Data Factory는 현재 Azure Active Directory 기반 OAuth만 지원). <br/><br/> 온-프레미스 OData의 경우 가능한 값은 익명, 기본 및 Windows입니다. |예 |
| 사용자 이름 |기본 인증을 사용하는 경우 사용자 이름을 지정합니다. |예(기본 인증을 사용하는 경우에만) |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예(기본 인증을 사용하는 경우에만) |
| authorizedCredential |OAuth를 사용하는 경우 Data Factory Copy Wizard 또는 Editor에서 **권한 부여** 단추를 클릭하고 자격 증명을 입력합니다. 그러면 이 속성의 값이 자동으로 생성됩니다. |예(OAuth 인증을 사용하는 경우에만) |
| gatewayName |데이터 팩터리 서비스가 온-프레미스 OData 서비스에 연결하는 데 사용해야 하는 게이트웨이의 이름 온-프레미스 OData 소스의 데이터를 복사하는 경우에만 지정합니다. |예 |

#### <a name="example---using-basic-authentication"></a>예제: 기본 인증 사용
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>예제: 익명 인증 사용

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>예제: 온-프레미스 OData 소스에 액세스하는 Windows 인증 사용

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>예제: 클라우드 OData 소스에 액세스하는 OAuth 인증 사용
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

자세한 내용은 [OData 커넥터](data-factory-odata-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
OData 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **ODataResource** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| path |OData 리소스에 대한 경로 |예 |

#### <a name="example"></a>예제

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

자세한 내용은 [OData 커넥터](data-factory-odata-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
OData 소스에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 예 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |"?$select=Name, Description&$top=5" |예 |

#### <a name="example"></a>예제

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

자세한 내용은 [OData 커넥터](data-factory-odata-connector.md#copy-activity-properties) 문서를 참조하세요.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>연결된 서비스
ODBC 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **OnPremisesOdbc** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| connectionString |선택적 암호화된 자격 증명 및 연결 문자열의 비 액세스 자격 증명 부분입니다. 다음 섹션의 예제를 참조하십시오. |예 |
| 자격 증명(credential) |드라이버 관련 속성 값 형식에 지정된 연결 문자열의 액세스 자격 증명 부분입니다. 예: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |예 |
| authenticationType |ODBC 데이터 저장소에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명 및 기본입니다. |예 |
| 사용자 이름 |기본 인증을 사용하는 경우 사용자 이름을 지정합니다. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예 |
| gatewayName |데이터 팩터리 서비스가 ODBC 데이터 저장소에 연결하는 데 사용해야 하는 게이트웨이의 이름. |예 |

#### <a name="example---using-basic-authentication"></a>예제: 기본 인증 사용

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>예제: 암호화된 자격 증명으로 기본 인증 사용
[New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) cmdlet을 사용하여 자격 증명을 암호화할 수 있습니다.

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>예제: 익명 인증 사용

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

자세한 내용은 [ODBC 커넥터](data-factory-odbc-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
ODBC 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |ODBC 데이터 저장소에 있는 테이블의 이름입니다. |예 |


#### <a name="example"></a>예제

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [ODBC 커넥터](data-factory-odbc-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
ODBC 데이터 저장소에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable` |예 |

#### <a name="example"></a>예제

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

자세한 내용은 [ODBC 커넥터](data-factory-odbc-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>연결된 서비스
Salesforce 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **Salesforce** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| environmentUrl | Salesforce 인스턴스의 URL을 지정합니다. <br><br> -기본값은 "https:\//login.salesforce.com"입니다. <br> - 샌드박스에서 데이터를 복사하려면 "[https://test.salesforce.com](https://test.salesforce.com)"을 지정합니다. <br> -사용자 지정 도메인에서 데이터를 복사하려면 예를 들어 "https://[domain].my.salesforce.com"을 지정합니다. |예 |
| 사용자 이름 |사용자 계정의 사용자 이름을 지정합니다. |예 |
| password |사용자 계정으로 password를 지정합니다. |예 |
| securityToken |사용자 계정에 대한 보안 토큰을 지정합니다. 보안 토큰을 재설정하거나 가져오는 방법에 대한 자세한 내용은 [보안 토큰 가져오기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) 를 참조하세요. 일반적인 보안 토큰에 대해 자세히 알아보려면 [보안 및 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)를 참조하세요. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

자세한 내용은 [Salesforce 커넥터](data-factory-salesforce-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
Salesforce 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **RelationalTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |Salesforce에 있는 테이블의 이름입니다. |아니요(**RelationalSource** 의 **query** 가 지정된 경우) |

#### <a name="example"></a>예제

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

자세한 내용은 [Salesforce 커넥터](data-factory-salesforce-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="relational-source-in-copy-activity"></a>복사 활동의 Relational 소스
Salesforce에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **RelationalSource** 로 설정하고 **source** 섹션에서 다음 속성을 지정합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL-92 쿼리 또는 [SOQL(Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 쿼리입니다. 예제: `select * from MyTable__c` |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |

#### <a name="example"></a>예제



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 API 이름에 "__c" 부분이 필요합니다.

자세한 내용은 [Salesforce 커넥터](data-factory-salesforce-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="web-data"></a>웹 데이터

### <a name="linked-service"></a>연결된 서비스
웹 연결된 서비스를 정의하려면 연결된 서비스의 **type** 을 **Web** 으로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| Url |웹 원본에 대한 URL입니다. |예 |
| authenticationType |익명 |예 |


#### <a name="example"></a>예제


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

자세한 내용은 [웹 테이블 커넥터](data-factory-web-table-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="dataset"></a>데이터 세트
웹 데이터 세트를 정의하려면 데이터 세트의 **type** 을 **WebTable** 로 설정하고 **typeProperties** 섹션에서 다음 속성을 지정합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type |데이터 세트의 형식입니다. **데이터 집합** |예 |
| 경로 |테이블을 포함하는 리소스에 대한 상대 URL입니다. |아니요. 경로를 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. |
| 인덱스 |리소스에 있는 테이블의 인덱스입니다. HTML 페이지에서 테이블의 인덱스를 가져오는 단계는 HTML 페이지에서 테이블의 인덱스 가져오기 섹션을 참조하세요. |예 |

#### <a name="example"></a>예제

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

자세한 내용은 [웹 테이블 커넥터](data-factory-web-table-connector.md#dataset-properties) 문서를 참조하세요.

### <a name="web-source-in-copy-activity"></a>복사 활동의 웹 소스
웹 테이블에서 데이터를 복사하는 경우 복사 활동의 **source type** 을 **WebSource** 로 설정합니다. 현재 복사 작업의 원본이 **WebSource** 형식인 경우 추가 속성이 지원되지 않습니다.

#### <a name="example"></a>예제

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

자세한 내용은 [웹 테이블 커넥터](data-factory-web-table-connector.md#copy-activity-properties) 문서를 참조하세요.

## <a name="compute-environments"></a>컴퓨팅 환경
다음 표에서는 Data Factory에서 지원하는 컴퓨팅 환경과 이러한 환경에서 실행할 수 있는 변환 활동을 나열합니다. 관심 있는 컴퓨팅 링크를 클릭하여 연결된 서비스에서 데이터 팩터리에 연결하기 위한 JSON 스키마를 확인하세요.

| 컴퓨팅 환경 | 활동 |
| --- | --- |
| [주문형 HDInsight 클러스터](#on-demand-azure-hdinsight-cluster) 또는 [사용자 고유의 HDInsight 클러스터](#existing-azure-hdinsight-cluster) |[.NET 사용자 지정 활동](#net-custom-activity), [Hive 활동](#hdinsight-hive-activity), [Pig 활동](#hdinsight-pig-activity), [MapReduce 활동](#hdinsight-mapreduce-activity), Hadoop 스트리밍 활동, [Spark 활동](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET 사용자 지정 작업](#net-custom-activity) |
| [Machine Learning Studio(클래식)](#ml-studio-classic) | [ML Studio(클래식) Batch Execution 작업](#ml-studio-classic-batch-execution-activity), [ML Studio(클래식) 업데이트 리소스 작업](#ml-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[데이터 레이크 분석 U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure Synapse Analytics](#azure-synapse-analytics), [SQL Server](#sql-server-stored-procedure) |[저장 프로시저](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>주문형 Azure HDInsight 클러스터
Azure 데이터 팩터리 서비스는 데이터를 처리하는 Windows/Linux 기반 주문형 HDInsight 클러스터를 자동으로 만들 수 있습니다. 클러스터는 클러스터와 연결된 스토리지 계정(JSON에서 linkedServiceName 속성)과 동일한 하위 지역에 만들어집니다. 이 연결된 서비스에서는 변환 활동, 즉 [.NET 사용자 지정 활동](#net-custom-activity), [Hive 활동](#hdinsight-hive-activity), [Pig 활동](#hdinsight-pig-activity), [MapReduce 활동](#hdinsight-mapreduce-activity), Hadoop 스트리밍 활동, [Spark 활동](#hdinsight-spark-activity)을 실행할 수 있습니다.

### <a name="linked-service"></a>연결된 서비스
다음 표에서는 주문형 HDInsight 연결된 서비스의 Azure JSON 정의에 사용된 속성에 대해 설명합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |형식 속성은 **HDInsightOnDemand** 로 설정해야 합니다. |예 |
| clusterSize |클러스터의 작업자/데이터 노드 수 HDInsight 클러스터는 속성에 지정한 작업자 노드의 수와 함께 2개의 헤드 노드로 생성됩니다. 노드의 크기는 4개 코어를 포함한 Standard_D3이므로, 4개 작업자 노드 클러스터에서 24개 코어(작업자 노드용 4\*4 = 16코어 및 헤드 노드용 2\*4 = 8코어)를 사용합니다. Standard_D3 계층에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. |예 |
| timetolive |주문형 HDInsight 클러스터에 대한 허용된 유휴 시간입니다. 클러스터에 다른 활성 작업이 없으면 작업이 완료된 후에 주문형 HDInsight 클러스터가 유지될 기간을 지정합니다.<br/><br/>예를 들어 활동 실행에 6분이 걸리고 timetolive이 5분으로 설정된 경우 클러스터는 활동을 처리하는 6분 동안 실행된 후에 5분 동안 유지됩니다. 다른 활동 실행이 6분 창을 실행하는 경우 동일한 클러스터에 의해 처리됩니다.<br/><br/>주문형 HDInsight 클러스터를 만드는 데는 많은 시간이 걸려 값 비싼 작업이 되므로 필요할 때마다 이 설정을 사용하여 주문형 HDInsight 클러스터를 다시 사용함으로써 데이터 팩터리의 성능을 향상시킵니다.<br/><br/>timetolive 값을 0으로 설정한 경우 클러스터는 활동이 처리되는 즉시 삭제됩니다. 반면 높은 값을 설정하는 경우 클러스터는 불필요하게 많은 비용이 발생하는 유휴 상태에 머무를 수 있습니다. 따라서 필요에 따라 적절한 값을 설정하는 것이 중요합니다.<br/><br/>timetolive 속성 값이 적절하게 설정되는 경우 여러 파이프라인은 주문형 HDInsight 클러스터의 동일한 인스턴스를 공유할 수 있습니다. |예 |
| 버전 |HDInsight 클러스터의 버전입니다. 자세한 내용은 [Azure Data Factory에서 지원되는 HDInsight 버전](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)을 참조하세요. |예 |
| linkedServiceName |데이터를 저장 및 처리하기 위해 주문형 클러스터에서 사용하는 Azure Storage 연결 서비스입니다. <p>현재 Azure Data Lake Store를 스토리지로 사용하는 주문형 HDInsight 클러스터를 만들 수 없습니다. HDInsight 처리의 결과 데이터를 Azure Data Lake Store에 저장하려면 복사 작업을 사용하여 Azure Blob Storage의 데이터를 Azure Data Lake Store로 복사합니다.</p>  | 예 |
| additionalLinkedServiceNames |HDInsight 연결된 서비스에 대한 추가 스토리지 계정을 지정하므로 데이터 팩터리 서비스가 사용자를 대신해 계정을 등록할 수 있습니다. |예 |
| osType |운영 체제 유형입니다. 허용되는 값은 Windows(기본값) 및 Linux입니다. |예 |
| hcatalogLinkedServiceName |HCatalog 데이터베이스를 가리키는 Azure SQL 연결된 서비스 이름입니다. 주문형 HDInsight 클러스터는 Azure SQL Database를 메타스토어로 사용하여 만듭니다. |예 |

### <a name="json-example"></a>JSON 예제
다음 JSON는 Linux 기반 주문형 HDInsight 연결된 서비스를 정의합니다. Data Factory 서비스는 데이터 조각을 처리하는 경우 **Linux 기반** HDInsight 클러스터를 자동으로 만듭니다.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

자세한 내용은 [컴퓨팅 연결 서비스](data-factory-compute-linked-services.md)을 참조하세요.

## <a name="existing-azure-hdinsight-cluster"></a>기존 Azure HDInsight 클러스터
Azure HDInsight 연결된 서비스를 만들어서 데이터 팩터리를 사용하는 사용자 고유의 HDInsight 클러스터를 등록할 수 있습니다. 이 연결된 서비스에서는 데이터 변환 활동, 즉 [.NET 사용자 지정 활동](#net-custom-activity), [Hive 활동](#hdinsight-hive-activity), [Pig 활동](#hdinsight-pig-activity), [MapReduce 활동](#hdinsight-mapreduce-activity), Hadoop 스트리밍 활동, [Spark 활동](#hdinsight-spark-activity)을 실행할 수 있습니다.

### <a name="linked-service"></a>연결된 서비스
다음 표에서는 Azure HDInsight 연결된 서비스의 Azure JSON 정의에 사용된 속성에 대해 설명합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |형식 속성은 **HDInsight** 로 설정해야 합니다. |예 |
| clusterUri |HDInsight 클러스터의 URI입니다. |예 |
| 사용자 이름 |기존 HDInsight 클러스터에 연결하는데 사용할 사용자의 이름을 지정합니다. |예 |
| password |사용자 계정으로 password를 지정합니다. |예 |
| linkedServiceName | HDInsight 클러스터에서 사용하는 Azure Blob Storage를 참조하는 Azure Storage 연결된 서비스의 이름입니다. <p>현재 이 속성에 대한 Azure Data Lake Store 연결된 서비스를 지정할 수 없습니다. HDInsight 클러스터가 Data Lake Store에 액세스할 경우 Hive/Pig 스크립트의 Azure Data Lake Store에 있는 데이터에 액세스할 수 있습니다. </p>  |예 |

지원되는 HDInsight 클러스터 버전은 [지원되는 HDInsight 버전](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)을 참조하세요.

#### <a name="json-example"></a>JSON 예제

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Azure Batch 연결된 서비스를 만들어 데이터 팩터리에 가상 머신(VM)의 Batch 풀을 등록할 수 있습니다. Azure Batch 또는 Azure HDInsight를 사용하여 .NET 사용자 지정 활동을 실행할 수 있습니다. 이 연결된 서비스에서 [.NET 사용자 지정 활동](#net-custom-activity)을 실행할 수 있습니다.

### <a name="linked-service"></a>연결된 서비스
다음 표에서는 Azure Batch 연결된 서비스의 Azure JSON 정의에 사용된 속성에 대해 설명합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |형식 속성은 **AzureBatch** 로 설정해야 합니다. |예 |
| accountName |Azure Batch 계정의 이름 |예 |
| accessKey |Azure Batch 계정에 대한 선택키 |예 |
| poolName |가상 머신의 풀 이름입니다. |예 |
| linkedServiceName |Azure Batch 연결된 서비스와 관련된 Azure Storage 연결된 서비스의 이름입니다. 이 연결된 서비스는 활동을 실행하는 데 필요한 파일을 스테이징하고 활동 실행 로그를 저장하는 데 사용됩니다. |예 |


#### <a name="json-example"></a>JSON 예제

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="ml-studio-classic"></a>ML Studio(클래식)
ML Studio(클래식)에 연결된 서비스를 만들어 Studio(클래식) 일괄 채점 엔드포인트를 데이터 팩터리에 등록할 수 있습니다. 이 연결된 서비스에서는 두 가지 데이터 변환 작업인 [ML Studio(클래식) Batch Execution 작업](#ml-studio-classic-batch-execution-activity)과 [ML Studio(클래식) 업데이트 리소스 작업](#ml-studio-classic-update-resource-activity)을 실행할 수 있습니다.

### <a name="linked-service"></a>연결된 서비스
다음 표에서는 Studio(클래식)에 연결된 서비스의 Azure JSON 정의에 사용되는 속성에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| Type |형식 속성은 **AzureML** 로 설정해야 합니다. |예 |
| mlEndpoint |일괄 처리 점수 매기기 URL입니다. |예 |
| apiKey |게시된 작업 영역 모델의 API입니다. |예 |

#### <a name="json-example"></a>JSON 예제

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure 데이터 레이크 분석
파이프 라인에서 **데이터 레이크 분석 U-SQL 작업** 을 사용하기 전에 Azure 데이터 레이크 분석 컴퓨팅 서비스와 Azure Data Factory에 연결하는 [Azure 데이터 레이크 분석](data-factory-usql-activity.md) 연결 서비스를 만듭니다.

### <a name="linked-service"></a>연결된 서비스

다음 표에서는 Azure Data Lake Analytics 연결된 서비스의 JSON 정의에 사용된 속성에 대해 설명합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| Type |형식 속성은 **AzureDataLakeAnalytics** 로 설정해야 합니다. |예 |
| accountName |Azure 데이터 레이크 분석 계정 이름입니다. |예 |
| dataLakeAnalyticsUri |Azure 데이터 레이크 분석 URI입니다. |예 |
| 권한 부여 |Data Factory 편집기에서 **권한 부여** 단추를 클릭하고 OAuth 로그인을 완료하면 인증 코드가 자동으로 검색됩니다. |예 |
| subscriptionId |Azure 구독 ID입니다. |아니요(지정하지 않으면 Data Factory의 구독이 사용됨). |
| resourceGroupName |Azure 리소스 그룹 이름 |아니요(지정하지 않으면 Data Factory의 리소스 그룹이 사용됨). |
| sessionID |OAuth 권한 부여 세션의 세션 ID입니다. 각 세션 ID는 고유하고 한 번만 사용될 수 있으며 Data Factory 편집기를 사용하면 이 ID가 자동으로 생성됩니다. |예 |


#### <a name="json-example"></a>JSON 예제
다음 예제에서는 Azure 데이터 레이크 분석 연결된 서비스에 JSON 정의를 제공합니다.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server 저장 프로시저

SQL Server 연결된 서비스를 만들고 [저장 프로시저 활동](data-factory-stored-proc-activity.md) 에서 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출합니다.

### <a name="linked-service"></a>연결된 서비스
SQL Server 데이터베이스를 Data Factory에 연결하기 위해 **OnPremisesSqlServer** 유형의 연결된 서비스를 만듭니다. 다음 표에서는 SQL Server 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

다음 표에서는 SQL Server 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |type 속성은 **OnPremisesSqlServer** 로 설정해야 합니다. |예 |
| connectionString |SQL 인증 또는 Windows 인증을 사용하여 SQL Server 데이터베이스에 연결하는 데 필요한 connectionString 정보를 지정합니다. |예 |
| gatewayName |Data Factory 서비스가 SQL Server 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |
| 사용자 이름 |Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. 예: **domainname\\username**. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. |예 |

**New-AzDataFactoryEncryptValue** cmdlet을 사용하여 자격 증명을 암호화하고 다음 예제와 같이 연결 문자열에 해당 자격 증명을 사용할 수 있습니다(**EncryptedCredential** 속성).

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>예제: SQL 인증을 사용하는 JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>예제: Windows 인증을 사용하는 JSON

사용자 이름 및 암호가 지정된 경우 게이트웨이는 이러한 정보를 사용해 지정된 사용자 계정을 가장하여 SQL Server Database에 연결합니다. 그렇지 않은 경우 게이트웨이는 게이트웨이의 보안 컨텍스트(시작 계정)를 사용하여 SQL Server에 직접 연결합니다.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

자세한 내용은 [SQL Server 커넥터](data-factory-sqlserver-connector.md#linked-service-properties) 문서를 참조하세요.

## <a name="data-transformation-activities"></a>데이터 변환 작업

작업 | Description
-------- | -----------
[HDInsight Hive 활동](#hdinsight-hive-activity) | Data Factory 파이프라인에서 HDInsight Hive 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Hive 쿼리를 실행합니다.
[HDInsight Pig 활동](#hdinsight-pig-activity) | Data Factory 파이프라인에서 HDInsight Pig 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Pig 쿼리를 실행합니다.
[HDInsight MapReduce 작업](#hdinsight-mapreduce-activity) | Data Factory 파이프라인의 HDInsight MapReduce 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 MapReduce 프로그램을 실행합니다.
[HDInsight 스트리밍 작업](#hdinsight-streaming-activity) | Data Factory 파이프라인의 HDInsight 스트리밍 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다.
[HDInsight Spark 작업](#hdinsight-spark-activity) | Data Factory 파이프라인에서 HDInsight Spark 작업은 사용자 고유 HDInsight 클러스터에서 Spark 프로그램을 실행합니다.
[ML Studio(클래식) Batch Execution 작업](#ml-studio-classic-batch-execution-activity) | Azure Data Factory를 사용하면 예측 분석을 위해 게시된 Studio(클래식) 웹 서비스를 사용하는 파이프라인을 쉽게 만들 수 있습니다. Azure Data Factory 파이프라인에서 일괄 실행 작업을 사용하면 Studio(클래식) 웹 서비스를 호출하여 데이터를 일괄적으로 예측할 수 있습니다.
[ML Studio(클래식) 업데이트 리소스 작업](#ml-studio-classic-update-resource-activity) | 시간이 지남에 따라 ML Studio(클래식) 점수 매기기 실험의 예측 모델은 새 입력 데이터 세트를 사용하여 다시 학습되어야 합니다. 재학습으로 완료한 후에는 재학습한 Machine Learning 모델로 점수 매기기 웹 서비스를 업데이트하려고 합니다. 업데이트 리소스 활동을 사용하여 새로 학습된 모델로 웹 서비스를 업데이트할 수 있습니다.
[저장 프로시저 작업](#stored-procedure-activity) | Data Factory 파이프라인에서 저장 프로시저 작업을 사용하여 엔터프라이즈 또는 Azure VM의 Azure SQL Database,Azure Synapse Analytics, SQL Server Database의 데이터 저장소 중 하나에서 저장 프로시저를 호출할 수 있습니다.
[Data Lake Analytics U-SQL 활동](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL 작업은 Azure Data Lake Analytics 클러스터에 대해 U-SQL 스크립트를 실행합니다.
[.NET 사용자 지정 작업](#net-custom-activity) | Data Factory에서 지원되지 않는 방식으로 데이터를 변환해야 하는 경우 고유의 데이터 이동 논리가 포함된 사용자 지정 작업을 만들어서 파이프라인에 해당 작업을 사용할 수 있습니다. Azure Batch 서비스 또는 Azure HDInsight 클러스터를 사용하여 실행되도록 사용자 지정 .NET 작업을 구성할 수 있습니다.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive 작업
Hive 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **HDInsightHive** 여야 합니다. 먼저 HDInsight 연결된 서비스를 만들고 해당 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다. 활동의 type을 HDInsightHive로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 스크립트 |Hive 스크립트 인라인 지정 |예 |
| script path |.NET용 File Storage 시작 'script' 또는 'scriptPath' 속성을 사용합니다. 둘 모두를 사용할 수는 없습니다. 파일 이름은 대/소문자를 구분합니다. |예 |
| defines |'hiveconf'를 사용하는 Hive 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정 |예 |

이러한 type 속성은 Hive 활동에만 적용됩니다. typeProperties 섹션 외부의 다른 속성은 모든 활동에서 지원됩니다.

### <a name="json-example"></a>JSON 예제
다음 JSON은 파이프라인에서 HDInsight Hive 활동을 정의합니다.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

자세한 내용은 [Hive 활동](data-factory-hive-activity.md)을 참조하세요.

## <a name="hdinsight-pig-activity"></a>HDInsight Pig 작업
Pig 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **HDInsightPig** 여야 합니다. 먼저 HDInsight 연결된 서비스를 만들고 해당 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다. 활동의 type을 HDInsightPig로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| 스크립트 |Pig 스크립트 인라인 지정 |예 |
| script path |Azure File Storage는 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 클라우드에서 파일 공유를 제공하는 서비스입니다. 'script' 또는 'scriptPath' 속성을 사용합니다. 둘 모두를 사용할 수는 없습니다. 파일 이름은 대/소문자를 구분합니다. |예 |
| defines |Pig 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정 |예 |

이러한 type 속성은 Pig 활동에만 적용됩니다. typeProperties 섹션 외부의 다른 속성은 모든 활동에서 지원됩니다.

### <a name="json-example"></a>JSON 예제

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

자세한 내용은 Pig 활동 문서를 참조하세요.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 작업
MapReduce 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **HDInsightMapReduce** 여야 합니다. 먼저 HDInsight 연결된 서비스를 만들고 해당 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다. 활동의 type을 HDInsightMapReduce로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| jarLinkedService | JAR 파일이 포함된 Azure Storage에 대한 연결된 서비스의 이름입니다. | 예 |
| jarFilePath | Azure Storage의 JAR 파일에 대한 경로입니다. | 예 |
| className | JAR 파일의 주 클래스 이름입니다. | 예 |
| 인수 | MapReduce 프로그램에 대해 쉼표로 구분된 인수 목록입니다. 런타임에 MapReduce 프레임워크의 몇 개 인수(예: mapreduce.job.tags)가 추가로 표시됩니다. MapReduce 인수로 사용자 인수를 구분하려면 다음 예제와 같이 인수로 옵션과 값을 모두 사용하는 것이 좋습니다(-s, --input, --output 등은 바로 뒤에 해당 값이 있는 옵션임). | 예 |

### <a name="json-example"></a>JSON 예제

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

자세한 내용은 [MapReduce 활동](data-factory-map-reduce.md)을 참조하세요.

## <a name="hdinsight-streaming-activity"></a>HDInsight 스트리밍 작업
Hadoop 스트리밍 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **HDInsightStreaming** 이어야 합니다. 먼저 HDInsight 연결된 서비스를 만들고 해당 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다. 활동의 type을 HDInsightStreaming으로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description |
| --- | --- |
| mapper | 매퍼 실행 파일의 이름입니다. 예제에서는 cat.exe가 매퍼 실행 파일입니다.|
| reducer | 리듀서 실행 파일의 이름입니다. 예제에서는 wc.exe가 리듀서 실행 파일입니다. |
| input | 매퍼의 입력 파일(위치 포함)입니다. `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` 예제에서 adfsample은 Blob 컨테이너이고, example/data/Gutenberg는 폴더이며, davinci.txt는 Blob입니다. |
| output | 리듀서의 출력 파일(위치 포함)입니다. Hadoop 스트리밍 작업의 출력이 이 속성에 지정된 위치에 기록됩니다. |
| filePaths | 매퍼 및 리듀서 실행 파일의 경로입니다. "adfsample/example/apps/wc.exe" 예에서 adfsample은 Blob 컨테이너, example/apps는 폴더, wc.exe는 실행 파일입니다. |
| fileLinkedService | filePaths 섹션에 지정된 파일이 포함된 Azure 스토리지를 나타내는 Azure Storage 연결된 서비스입니다. |
| 인수 | MapReduce 프로그램에 대해 쉼표로 구분된 인수 목록입니다. 런타임에 MapReduce 프레임워크의 몇 개 인수(예: mapreduce.job.tags)가 추가로 표시됩니다. MapReduce 인수로 사용자 인수를 구분하려면 다음 예제와 같이 인수로 옵션과 값을 모두 사용하는 것이 좋습니다(-s, --input, --output 등은 바로 뒤에 해당 값이 있는 옵션임). |
| getDebugInfo | 선택적 요소입니다. Failure로 설정되면 실패한 경우에만 로그가 다운로드됩니다. All로 설정되면 실행 상태에 관계 없이 로그가 항상 다운로드됩니다. |

> [!NOTE]
> **outputs** 속성의 Hadoop 스트리밍 활동에 대한 출력 데이터 세트를 지정해야 합니다. 이 데이터 세트는 파이프라인 일정(매시간, 매일 등)을 진행하는 데 필요한 더미 데이터 세트일 수 있습니다. 활동에서 입력(input)을 사용하지 않는 경우 **inputs** 속성의 활동에 대한 입력 데이터 세트를 지정하지 않은 채 건너뛸 수 있습니다.

## <a name="json-example"></a>JSON 예제

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

자세한 내용은 [Hadoop 스트리밍 활동](data-factory-hadoop-streaming-activity.md) 문서를 참조하세요.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark 작업
Spark 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **HDInsightSpark** 여야 합니다. 먼저 HDInsight 연결된 서비스를 만들고 해당 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다. 활동의 type을 HDInsightSpark로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| rootPath | Spark 파일이 포함된 Azure Blob 컨테이너 및 폴더입니다. 파일 이름은 대/소문자를 구분합니다. | 예 |
| entryFilePath | Spark 코드/패키지의 루트 폴더에 대한 상대 경로입니다. | 예 |
| className | 애플리케이션의 Java/Spark main 클래스 | 예 |
| 인수 | Spark 프로그램에 대한 명령줄 인수 목록입니다. | 예 |
| proxyUser | Spark 프로그램 실행을 가장하는 사용자 계정 | 예 |
| sparkConfig | Spark 구성 속성입니다. | 예 |
| getDebugInfo | sparkJobLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 Spark 로그 파일을 언제 복사할지 지정합니다. 허용되는 값은 다음과 같습니다. 없음, 항상 또는 실패. 기본값: 없음 | 예 |
| sparkJobLinkedService | Spark 작업 파일, 종속성 및 로그를 보유하는 Azure Storage 연결된 서비스입니다.  이 속성에 대한 값을 지정하지 않으면 HDInsight 클러스터와 연결된 스토리지가 사용됩니다. | 예 |

### <a name="json-example"></a>JSON 예제

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
다음 사항에 유의하세요.

- **type** 속성은 **HDInsightSpark** 로 설정됩니다.
- **rootPath** 는 **adfspark\\pyFiles** 로 설정되며, 여기서 adfspark는 Azure Blob 컨테이너이고, pyFiles는 해당 컨테이너의 파일 폴더입니다. 이 예에서 Azure Blob Storage는 Spark 클러스터와 연결되어 있습니다. 파일을 다른 Azure Storage에 업로드할 수 있습니다. 이렇게 하는 경우 해당 스토리지 계정을 데이터 팩터리에 연결하는 Azure Storage 연결된 서비스를 만들어야 합니다. 그런 다음 연결된 서비스의 이름을 **sparkJobLinkedService** 속성의 값으로 지정합니다. 이 속성과 Spark 작업에서 지원하는 기타 속성에 대한 자세한 내용은 Spark 작업 속성을 참조하세요.
- **entryFilePath** 는 python 파일인 **test.py** 로 설정됩니다.
- **getDebugInfo** 속성은 **Always** 로 설정되며, 이는 로그 파일이 항상 생성(성공 또는 실패)된다는 것을 의미합니다.

    > [!IMPORTANT]
    > 문제를 해결하는 경우가 아니라면 프로덕션 환경에서 이 속성을 Always로 설정하지 않는 것이 좋습니다.
- **outputs** 섹션에는 하나의 출력 데이터 세트만 있습니다. Spark 프로그램이 출력을 생성하지 않더라도 출력 데이터 세트를 지정해야 합니다. 출력 데이터 세트는 파이프라인의 일정(매시간, 매일 등)을 구동합니다.

활동에 대한 자세한 내용은 [Spark 활동](data-factory-spark.md) 문서를 참조하세요.

## <a name="ml-studio-classic-batch-execution-activity"></a>ML Studio(클래식) Batch Execution 작업
ML Studio(클래식) Batch Execution 작업 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **AzureMLBatchExecution** 이어야 합니다. 먼저 Studio(클래식)에 연결된 서비스를 만들고 **linkedServiceName** 속성 값으로 이름을 지정해야 합니다. 활동의 type을 AzureMLBatchExecution로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
webServiceInput | Studio(클래식) 웹 서비스에 대한 입력으로 전달할 데이터 세트입니다. 이 데이터 세트는 활동의 입력에도 포함되어야 합니다. |webServiceInput 또는 webServiceInputs를 사용합니다. |
webServiceInputs | Studio(클래식) 웹 서비스에 대한 입력으로 전달할 데이터 세트를 지정합니다. 웹 서비스에서 여러 입력을 사용하는 경우 webServiceInput 속성 대신 webServiceInputs 속성을 사용합니다. **webServiceInputs** 에서 참조하는 데이터 세트는 또한 **입력** 작업에 포함되어야 합니다. | webServiceInput 또는 webServiceInputs를 사용합니다. |
webServiceOutputs | Studio(클래식) 웹 서비스에 대한 출력으로 할당된 데이터 세트입니다. 웹 서비스는 이 데이터 세트의 출력 데이터를 반환합니다. | 예 |
globalParameters | 이 섹션에서 웹 서비스 매개 변수의 값을 지정합니다. | 예 |

### <a name="json-example"></a>JSON 예제
이 예제의 활동에는 입력으로 **MLSqlInput** 데이터 세트가, 출력으로 **MLSqlOutput** 이 있습니다. **MLSqlInput** 은 **webServiceInput** JSON 속성을 사용하여 입력으로 웹 서비스에 전달됩니다. **MLSqlOutput** 은 **webServiceOutputs** JSON 속성을 사용하여 출력으로 웹 서비스에 전달됩니다.

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

JSON 예제에서 배포된 Studio(클래식) 웹 서비스는 판독기와 기록기 모듈을 사용하여 Azure SQL Database에서 데이터를 읽고 씁니다. 이 웹 서비스는 네 개의 매개 변수, 즉 데이터베이스 서버 이름, 데이터베이스 이름, 서버 사용자 계정 이름 및 서버 사용자 계정 암호를 공개합니다.

> [!NOTE]
> AzureMLBatchExecution 작업의 입력 및 출력만 웹 서비스에 매개 변수로 전달될 수 있습니다. 예를 들어 위의 JSON 조각에서 MLSqlInput은 AzureMLBatchExecution 활동에 대한 입력이며, webServiceInput 매개 변수를 통해 입력으로 웹 서비스에 전달됩니다.

## <a name="ml-studio-classic-update-resource-activity"></a>ML Studio(클래식) 업데이트 리소스 작업
ML Studio(클래식) 업데이트 리소스 작업 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **AzureMLUpdateResource** 이어야 합니다. 먼저 Studio(클래식)에 연결된 서비스를 만들고 **linkedServiceName** 속성 값으로 이름을 지정해야 합니다. 활동의 type을 AzureMLUpdateResource로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
trainedModelName | 다시 학습된 모델의 이름입니다. | 예 |
trainedModelDatasetName | 재학습 작업으로 반환된 iLearner 파일을 가리키는 데이터 세트입니다. | 예 |

### <a name="json-example"></a>JSON 예제
파이프라인에는 **AzureMLBatchExecution** 및 **AzureMLUpdateResource** 라는 두 활동이 있습니다. Studio(클래식) 일괄 실행 작업은 학습 데이터를 입력으로 사용하고 iLearner 파일을 출력으로 생성합니다. 이 작업은 입력 교육 데이터와 함께 학습 웹 서비스(웹 서비스로 노출된 학습 실험)를 호출하고 웹 서비스로부터 ilearner 파일을 수신합니다. placeholderBlob는 Azure 데이터 팩터리 서비스가 파이프라인을 실행하기 위해 필요로 하는 더미 출력 데이터 세트입니다.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 작업
U-SQL 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **DataLakeAnalyticsU-SQL** 이어야 합니다. Azure Data Lake Analytics 연결된 서비스를 만들고 해당 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다. 활동의 type을 DataLakeAnalyticsU-SQL로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| scriptPath |U-SQL 스크립트가 포함된 폴더 경로입니다. 파일 이름은 대/소문자를 구분합니다. |아니요(스크립트를 사용하는 경우) |
| scriptLinkedService |스크립트가 포함된 스토리지를 Data Factory에 연결하는 연결된 서비스입니다. |아니요(스크립트를 사용하는 경우) |
| 스크립트 |scriptPath 및 scriptLinkedService를 지정하는 대신 인라인 스크립트를 지정합니다. 예: "script" : "CREATE DATABASE test" |아니요(scriptPath 및 scriptLinkedService를 사용하는 경우) |
| degreeOfParallelism |작업을 실행하는 데 동시에 사용되는 최대 노드 수입니다. |예 |
| priority |대기열에 있는 모든 작업 중에서 먼저 실행해야 하는 작업을 결정합니다. 번호가 낮을수록 우선 순위가 높습니다. |예 |
| 매개 변수 |U-SQL 스크립트의 매개 변수 |예 |

### <a name="json-example"></a>JSON 예제

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

자세한 내용은 [Data Lake Analytics U-SQL 활동](data-factory-usql-activity.md)을 참조하세요.

## <a name="stored-procedure-activity"></a>저장 프로시저 작업
저장 프로시저 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **SqlServerStoredProcedure** 여야 합니다. 다음 연결된 서비스 중 하나를 만들고 해당 연결된 서비스의 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다.

- SQL Server
- Azure SQL Database
- Azure Synapse Analytics

활동의 type을 SqlServerStoredProcedure로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| storedProcedureName |출력 테이블이 사용하는 연결된 서비스로 표시되는 Azure SQL Database 또는 Azure Synapse Analytics의 저장 프로시저 이름을 지정합니다. |예 |
| storedProcedureParameters |저장 프로시저 매개 변수의 값을 지정합니다. 매개 변수에 대해 null을 전달해야 하는 경우 구문: "param1": null(모두 소문자)을 사용합니다. 이 속성을 사용하는 방법에 대한 자세한 내용은 다음 샘플을 참조하세요. |예 |

입력 데이터 세트를 지정하는 경우 실행할 저장 프로시저 작업에 사용할 수 있어야 합니다('Ready' 상태). 저장 프로시저에서 입력 데이터 세트를 매개 변수로 사용할 수 없습니다. 저장 프로시저 작업을 시작하기 전에 종속성을 확인하는 데만 사용됩니다. 저장 프로시저 작업에 대한 출력 데이터 세트를 지정해야 합니다.

출력 데이터 세트는 저장 프로시저 작업에 대한 **일정** (매시간, 매주, 매월 등)을 지정합니다. 출력 데이터 세트는 저장 프로시저를 실행하려는 Azure SQL Database 또는 Azure Synapse Analytics 또는 SQL Server 데이터베이스를 참조하는 **연결된 서비스** 를 사용해야 합니다. 출력 데이터 세트는 파이프라인에서 다른 작업에 의한 후속 처리([활동 체이닝](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline))를 위해 저장 프로시저의 결과를 전달하는 방법으로 사용할 수 있습니다. 그러나 Data Factory는 저장 프로시저의 출력을 이 데이터 세트에 자동으로 쓰지 않습니다. 출력 데이터 세트가 가리키는 SQL 테이블에 기록하는 저장 프로시저입니다. 경우에 따라 출력 데이터 세트는 저장 프로시저 작업을 실행하는 일정을 지정하기 위해서만 사용되는 **더미 데이터 세트** 일 수 있습니다.

### <a name="json-example"></a>JSON 예제

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
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

자세한 내용은 [저장 프로시저 활동](data-factory-stored-proc-activity.md)을 참조하세요.

## <a name="net-custom-activity"></a>.NET 사용자 지정 작업
.NET 사용자 지정 활동 JSON 정의에서 다음 속성을 지정할 수 있습니다. 활동의 type 속성은 **DotNetActivity** 여야 합니다. Azure HDInsight 연결된 서비스 또는 Azure Batch 연결된 서비스를 만들고 해당 연결된 서비스의 이름을 **linkedServiceName** 속성의 값으로 지정해야 합니다. 활동의 type을 DotNetActivity로 설정하는 경우 **typeProperties** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| AssemblyName | 어셈블리의 이름입니다. 예제에서는 **MyDotnetActivity.dll** 입니다. | 예 |
| EntryPoint |IDotNetActivity 인터페이스를 구현하는 클래스의 이름입니다. 예제에서는 **MyDotNetActivityNS.MyDotNetActivity** 이며, 여기서 MyDotNetActivityNS는 네임스페이스이고, MyDotNetActivity는 클래스입니다.  | 예 |
| PackageLinkedService | 사용자 지정 활동 zip 파일이 포함된 Blob 스토리지를 가리키는 Azure Storage 연결된 서비스의 이름입니다. 예제에서는 **AzureStorageLinkedService** 입니다.| 예 |
| PackageFile | zip 파일의 이름입니다. 예제에서는 **customactivitycontainer/MyDotNetActivity.zip** 입니다. | 예 |
| extendedProperties | 정의하고 .NET 코드에 전달할 수 있는 확장된 속성입니다. 예제에서 **SliceStart** 변수는 SliceStart 시스템 변수에 기반한 값으로 설정됩니다. | 예 |

### <a name="json-example"></a>JSON 예제

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

자세한 내용은 [Data Factory에서 사용자 지정 활동 사용](data-factory-use-custom-activities.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 자습서를 참조하세요.

- [자습서: 복사 활동이 있는 파이프라인 만들기](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [자습서: Hive 활동이 있는 파이프라인 만들기](data-factory-build-your-first-pipeline.md)
