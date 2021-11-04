---
title: Data Factory에서 파이프라인, 체인 작업 만들기/예약
description: Azure Data Factory에서 데이터 파이프라인을 만들어 데이터를 이동하고 변환하는 방법을 배웁니다. 데이터 기반 워크플로를 만들어 정보를 사용하도록 준비합니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2e507117f355e2cfea903a1aea7c2d2d52fea466
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "131454064"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure 데이터 팩터리의 파이프라인 및 활동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-create-pipelines.md)
> * [버전 2(현재 버전)](../concepts-pipelines-activities.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 파이프라인](../concepts-pipelines-activities.md)을 참조하세요.

이 문서는 Azure Data Factory의 파이프라인 및 활동을 이해하고 데이터 이동 및 데이터 처리 시나리오를 위한 엔드투엔드 데이터 기반 워크플로 사용하는 데 도움이 됩니다.

> [!NOTE]
> 이 문서는 [Azure Data Factory 소개](data-factory-introduction.md)를 마쳤다고 간주합니다. 데이터 팩터리를 직접 만든 경험이 없는 경우 [데이터 변환 자습서](data-factory-build-your-first-pipeline.md) 및/또는 [데이터 이동 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 진행하면 이 문서를 이해하는 데 도움이 됩니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 함께 작업을 수행하는 활동의 논리적 그룹화입니다. 파이프라인의 작업은 데이터에 대해 수행할 동작을 정의합니다. 예를 들어, 복사 작업을 사용하여 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터를 복사할 수 있습니다. 그런 다음 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 Hive 활동을 사용하여 Blob Storage의 데이터를 처리/변환함으로써 출력 데이터를 생성합니다. 마지막으로 두 번째 복사 작업을 사용하여 BI(비즈니스 인텔리전스) 보고 솔루션의 기반이 되는 Azure Synapse Analytics로 출력 데이터를 복사합니다.

활동은 0개 이상의 입력 [데이터 세트](data-factory-create-datasets.md)를 받고 하나 이상의 출력 [데이터 세트](data-factory-create-datasets.md)를 생성할 수 있습니다. 다음 다이어그램은 데이터 팩터리의 파이프라인, 활동 및 데이터 세트 간 관계를 보여 줍니다.

:::image type="content" source="media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png" alt-text="파이프라인, 활동, 데이터 세트 간 관계":::

파이프라인을 사용하면 개별 활동 단위가 아닌 하나의 집합으로서 활동을 관리할 수 있습니다. 예를 들어 파이프라인의 활동을 개별적으로 처리하지 않고 파이프라인을 배포하고, 예약하고, 일시 중단하고, 계속할 수 있습니다.

Data Factory는 데이터 이동 활동 및 데이터 변환 활동이라는 두 종류의 활동을 지원합니다. 각 활동은 0개 이상의 입력 [데이터 세트](data-factory-create-datasets.md)를 갖고 하나 이상의 출력 데이터 세트를 생성할 수 있습니다.

입력 데이터 세트는 파이프라인의 작업에 대한 입력을 나타내고 출력 데이터 세트는 작업에 대한 출력을 나타냅니다. 데이터 세트는 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 데이터 세트를 만든 후 파이프라인의 작업에 사용할 수 있습니다. 예를 들어 데이터 세트는 복사 작업 또는 HDInsightHive 작업의 입력/출력 데이터 세트일 수 있습니다. 데이터 세트에 대한 자세한 내용은 [Azure Data Factory의 데이터 세트](data-factory-create-datasets.md) 문서를 참조하세요.

### <a name="data-movement-activities"></a>데이터 이동 활동
데이터 팩터리의 복사 활동은 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사합니다. Data Factory는 다음과 같은 데이터 저장소를 지원합니다. 모든 소스의 데이터를 모든 싱크에 쓸 수 있습니다. 데이터 저장소를 클릭하면 해당 저장소에서/저장소로 데이터를 복사하는 방법을 확인할 수 있습니다.

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> *가 있는 데이터 저장소는 온-프레미스 또는 Azure IaaS에 있을 수 있으며 온-프레미스/Azure IaaS 컴퓨터에 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 를 설치해야 합니다.

자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 참조하세요.

### <a name="data-transformation-activities"></a>데이터 변환 활동
[!INCLUDE [data-factory-transformation-activities](includes/data-factory-transformation-activities.md)]

자세한 내용은 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서를 참조하세요.

### <a name="custom-net-activities"></a>사용자 지정 .NET 활동
복사 작업에서 지원하지 않는 데이터 저장소에서/로 데이터를 이동해야 하거나 사용자 고유한 논리를 사용하여 데이터를 변환해야 하는 경우 **사용자 지정 .NET 활동** 을 만듭니다. 사용자 지정 활동을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory 파이프라인에서 사용자 지정 활동 사용](data-factory-use-custom-activities.md)을 참조하세요.

## <a name="schedule-pipelines"></a>파이프라인 예약
파이프라인은 **시작** 시간과 **종료** 시간 사이에서만 활성화됩니다. 시작 시간 이전 또는 종료 시간 이후에 실행되지 않습니다. 파이프라인이 일시 중지된 경우 시작 및 종료 시간에 관계없이 실행되지 않습니다. 실행될 파이프라인의 경우 일시 중지되지 않아야 합니다. Azure 데이터 팩터리에서 예약 및 실행의 작동 방식을 이해하려면 [예약 및 실행](data-factory-scheduling-and-execution.md) 을 참조하세요.

## <a name="pipeline-json"></a>파이프라인 JSON
JSON 형식으로 파이프라인을 정의하는 방법에 대해 자세히 살펴보겠습니다. 파이프라인에 대한 일반 구조는 다음과 같이 보입니다.

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| 태그 | 설명 | 필수 |
| --- | --- | --- |
| name |파이프라인의 이름입니다. 파이프라인이 수행하는 작업을 나타내는 이름을 지정합니다. <br/><ul><li>최대 문자 수: 260</li><li>문자, 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>사용할 수 없는 문자: “.”, “+”, “?”, “/”, “<”,”>”,”\*”,”%”,”&”,”:”,”\\”</li></ul> |예 |
| description | 파이프라인의 용도를 설명하는 텍스트를 지정합니다. |예 |
| 작업 | **활동** 섹션에는 내부에서 정의된 하나 이상의 활동이 있을 수 있습니다. 활동 JSON 요소에 대한 자세한 내용은 다음 섹션을 참조하세요. | 예 |
| start | 파이프라인에 대한 시작 날짜-시간입니다. [ISO 형식](https://en.wikipedia.org/wiki/ISO_8601)에 있어야 합니다. 예: `2016-10-14T16:32:41Z` <br/><br/>예를 들어 EST 시간처럼 현지 시간을 지정할 수 있습니다. 예제: `2016-02-27T06:00:00-05:00`”(오전 6시 동부 표준시)<br/><br/>start 및 end 속성은 함께 파이프라인의 활성 기간을 지정합니다. 출력 조각은 이 활성 기간에만 생성됩니다. |예<br/><br/>end 속성에 대한 값을 지정하는 경우 반드시 start 속성에 대한 값도 지정해야 합니다.<br/><br/>파이프라인을 만들 때에는 시작 및 종료 시간을 비워 둘 수 있습니다. 파이프라인을 실행할 활성 기간을 설정하려면 두 값 모두를 지정해야 합니다. 파이프라인을 만들 때 시작 시간과 종료 시간을 지정하지 않으면 나중에 Set-AzDataFactoryPipelineActivePeriod cmdlet을 사용하여 설정할 수 있습니다. |
| end | 파이프라인에 대한 종료 날짜-시간입니다. 지정된 경우 ISO 형식에 있어야 합니다. 예: `2016-10-14T17:32:41Z` <br/><br/>예를 들어 EST 시간처럼 현지 시간을 지정할 수 있습니다. 예: `2016-02-27T06:00:00-05:00`(오전 6시 동부 표준시)<br/><br/>파이프라인을 무기한 실행하려면 end 속성 값으로 9999-09-09를 지정합니다. <br/><br/> 파이프라인은 시작 시간과 종료 시간 사이에서만 활성화됩니다. 시작 시간 이전 또는 종료 시간 이후에 실행되지 않습니다. 파이프라인이 일시 중지된 경우 시작 및 종료 시간에 관계없이 실행되지 않습니다. 실행될 파이프라인의 경우 일시 중지되지 않아야 합니다. Azure 데이터 팩터리에서 예약 및 실행의 작동 방식을 이해하려면 [예약 및 실행](data-factory-scheduling-and-execution.md) 을 참조하세요. |예 <br/><br/>시작 속성에 대한 값을 지정하는 경우 반드시 끝 속성에 대한 값도 지정해야 합니다.<br/><br/>**start** 속성에 대한 참조를 확인합니다. |
| isPaused | true로 설정하면 파이프라인이 실행되지 않습니다. 일시 중지된 상태를 유지합니다. 기본값 = false입니다. 이 속성을 사용하여 파이프라인을 사용하거나 사용하지 않도록 설정할 수 있습니다. |예 |
| pipelineMode | 파이프라인에 대한 실행을 예약하는 메서드입니다. 허용되는 값은 scheduled(기본), onetime입니다.<br/><br/>‘Scheduled’는 파이프라인이 활성 기간(시작 및 종료 시간)에 따라 지정된 시간 간격으로 실행된다는 것을 나타냅니다. ‘Onetime’은 파이프라인이 한 번만 실행된다는 것을 나타냅니다. 현재는, Onetime 파이프라인이 생성된 후에 수정/업데이트가 불가능합니다. 일회성 설정에 대한 세부 정보는 [일회성 파이프라인](#onetime-pipeline)을 참조하세요. |예 |
| expirationTime | 생성 후에 [일회성 파이프라인](#onetime-pipeline)이 유효하고 프로비전된 상태로 유지해야 하는 시간입니다. 활성 실행, 실패한 실행 또는 보류 중인 실행이 없는 경우 만료 시간이 되면 파이프라인은 자동으로 삭제됩니다. 기본값: `"expirationTime": "3.00:00:00"`|예 |
| datasets |파이프라인에 정의된 작업에서 사용할 데이터 세트 목록입니다. 이 속성은 데이터 팩터리 내에 정의되지 않은 파이프라인에만 해당되는 데이터 세트를 정의하는 데 사용될 수 있습니다. 이 파이프라인 내에 정의된 데이터 세트는 이 파이프라인에서만 사용될 수 있고 공유될 수 없습니다. 자세한 내용은 [범위가 지정된 데이터 세트](data-factory-create-datasets.md#scoped-datasets)를 참조하세요. |예 |

## <a name="activity-json"></a>활동 JSON
**활동** 섹션에는 내부에서 정의된 하나 이상의 활동이 있을 수 있습니다. 각 활동에는 다음과 같은 최상위 수준 구조가 있습니다.

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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

다음 표에서는 활동 JSON 정의의 속성을 설명합니다.

| 태그 | 설명 | 필수 |
| --- | --- | --- |
| name | 활동의 이름입니다. 활동이 수행하는 작업을 나타내는 이름을 지정합니다. <br/><ul><li>최대 문자 수: 260</li><li>문자, 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |예 |
| description | 활동의 용도를 설명하는 텍스트입니다. |예 |
| type | 활동의 형식입니다. 다른 형식의 활동에 대해서는 [데이터 이동 활동](#data-movement-activities) 및 [데이터 변환 활동](#data-transformation-activities) 섹션을 참조하세요. |예 |
| 입력 |작업에서 사용하는 입력 테이블<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |예 |
| outputs |활동에서 사용하는 출력 테이블입니다.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |예 |
| linkedServiceName |작업에서 사용하는 연결된 서비스의 이름입니다. <br/><br/>작업은 필요한 컴퓨팅 환경에 연결하는 연결된 서비스를 지정해야 할 수 있습니다. |HDInsight Activity 및 ML Studio(클래식) Batch Scoring Activity의 경우 예 <br/><br/>다른 모든 사용자의 경우 아니요 |
| typeProperties |**typeProperties** 섹션의 속성은 활동의 형식에 따라 달라집니다. 활동의 형식 속성을 보려면 이전 섹션의 활동 링크를 클릭합니다. | No |
| policy |작업의 런타임 동작에 영향을 주는 정책입니다. 지정하지 않으면 기본 정책이 사용됩니다. |예 |
| scheduler | "scheduler" 속성은 작업에 원하는 일정을 정의하는 데 사용됩니다. 하위 속성은 [데이터 세트에서 가용성 속성](data-factory-create-datasets.md#dataset-availability)에 있는 속성과 같습니다. |예 |

### <a name="policies"></a>정책
정책은 특히 테이블의 조각을 처리할 때 활동의 런타임 동작에 영향을 줍니다. 다음 테이블에서는 자세한 내용을 제공합니다.

| 속성 | 허용된 값 | 기본값 | Description |
| --- | --- | --- | --- |
| 동시성 |정수 <br/><br/>최대값: 10 |1 |작업의 동시 실행 수입니다.<br/><br/>다른 조각에 발생할 수 있는 병렬 작업 실행 횟수를 결정합니다. 예를 들어 활동이 사용 가능한 많은 데이터 집합을 거쳐야 하는 경우 동시성 값을 높이면 데이터 처리가 빨라집니다. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |처리 중인 데이터 조각의 순서를 결정합니다.<br/><br/>예를 들어 2개의 조각이 있으며(각각 오후 4시 및 오후 5시에 발생) 둘 다 실행 보류 상태입니다. executionPriorityOrder를 설정하여 NewestFirst가 되도록 하면 오후 5시에 조각이 먼저 처리됩니다. 마찬가지로 executionPriorityORder를 OldestFIrst로 설정하면 오후 4시의 조각이 처리됩니다. |
| retry |정수<br/><br/>최대값이 10이 될 수 있음 |0 |조각에 대한 데이터 처리 전에 다시 시도 횟수가 실패로 표시됩니다. 데이터 조각에 대한 활동 실행은 지정된 재시도 횟수까지 다시 시도됩니다. 재시도는 실패 후 가능한 한 빨리 수행합니다. |
| 시간 제한 |TimeSpan |00:00:00 |활동에 대한 시간 제한입니다. 예: 00:10:00(시간 제한 10분을 의미함)<br/><br/>값이 지정되지 않거나 0인 경우 시간 제한은 무한입니다.<br/><br/>조각의 데이터 처리 시간이 시간 제한 값을 초과하면 취소되고 시스템이 처리를 다시 시도합니다. 다시 시도 횟수는 다시 시도 속성에 따라 달라집니다. 시간 제한이 발생할 때 상태는 TimedOut으로 설정됩니다. |
| delay |TimeSpan |00:00:00 |조각의 데이터 처리 작업이 시작되기 전에 지연을 지정합니다.<br/><br/>데이터 조각에 대한 활동의 실행은 지연이 예상된 실행 시간을 지난 후에 시작됩니다.<br/><br/>예: 00:10:00(10분의 지연을 의미함) |
| longRetry |정수<br/><br/>최대값: 10 |1 |조각 실행이 실패하기 전까지의 긴 재시도 횟수입니다.<br/><br/>longRetry 시도는 longRetryInterval에 따라 간격이 조정됩니다. 따라서 재시도 간의 시간을 지정해야 하는 경우 longRetry를 사용합니다. Retry 및 longRetry를 둘 다 지정하면 각 longRetry 시도에는 Retry 시도가 포함되고 최대 시도 횟수는 Retry * longRetry가 됩니다.<br/><br/>예를 들어 활동 정책에 다음 설정이 있는 경우:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>실행할 조각이 하나뿐이며(Waiting 상태) 작업 실행이 매번 실패한다고 가정합니다. 우선 3번 연속 실행 시도를 합니다. 시도한 후 각 조각 상태는 다시 시도입니다. 처음 3번 시도 후에 조각 상태는 LongRetry입니다.<br/><br/>한 시간(즉, longRetryInteval의 값) 후에 3번 연속 실행이 다시 시도됩니다. 그 후에 조각 상태가 실패이면 다시 시도는 더 이상 시도하지 않습니다. 즉, 전체적으로 6번의 시도가 일어납니다.<br/><br/>모든 실행에 성공하면 조각 상태는 준비 상태가 되며 더 이상 다시 시도하지 않습니다.<br/><br/>longRetry는 종속 데이터가 명확하지 않은 시간에 도착하거나 데이터 처리가 발생하는 전체적인 환경을 신뢰할 수 없는 상황에서 사용될 수 있습니다. 이러한 경우 하나씩 다시 시도를 수행해도 도움이 되지 않을 수 있으며 특정 시간 간격 후에 시도할 경우 출력이 나타납니다.<br/><br/>주의: longRetry 또는 longRetryInterval에 높은 값을 설정하지 마세요. 일반적으로 더 높은 값을 지정하면 다른 시스템 문제가 발생합니다. |
| longRetryInterval |TimeSpan |00:00:00 |긴 다시 시도 간의 지연 |

## <a name="sample-copy-pipeline"></a>샘플 복사 파이프라인
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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

다음 사항에 유의하세요.

* 작업 섹션에는 **형식** 이 **복사** 로 설정된 작업만 있습니다.
* 작업에 대한 입력을 **InputDataset** 으로 설정하고 작업에 대한 출력을 **OutputDataset** 으로 설정합니다. JSON의 데이터 세트 정의에 대해서는 [데이터 세트](data-factory-create-datasets.md) 문서를 참조하세요.
* **typeProperties** 섹션에서 **BlobSource** 를 원본 유형으로 지정하고 **SqlSink** 를 싱크 유형으로 지정합니다. [데이터 이동 활동](#data-movement-activities) 섹션에서 원본 또는 싱크로 사용할 데이터 저장소를 클릭하여 해당 데이터 저장소로/부터 데이터를 이동하는 방법을 알아봅니다.

이 파이프라인을 만드는 전체 연습은 [자습서: Blob Storage의 데이터를 SQL Database에 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

## <a name="sample-transformation-pipeline"></a>샘플 변환 파이프라인
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

다음 사항에 유의하세요.

* activities 섹션에는 **type** 이 **HDInsightHive** 로 설정된 작업만 있습니다.
* Hive 스크립트 파일 **partitionweblogs.hql** 은 Azure Storage 계정(**AzureStorageLinkedService** 라고 하는 scriptLinkedService에 의해 지정됨)과 **adfgetstarted** 컨테이너에 있는 **스크립트** 폴더에 저장됩니다.
* `defines` 섹션은 하이브 스크립트에 하이브 구성 값(예: `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`)으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

**typeProperties** 섹션은 각 변환 활동마다 다릅니다. 변환 활동에 대해 지원되는 형식 속성에 대해 알아보려면 [데이터 변환 활동](#data-transformation-activities) 표에서 변환 활동을 클릭합니다.

이 파이프라인을 만드는 전체 연습은 [자습서: Hadoop 클러스터를 사용하여 데이터를 처리하는 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="multiple-activities-in-a-pipeline"></a>파이프라인의 여러 활동
이전에 나온 두 개의 샘플 파이프라인에는 활동이 하나만 있습니다. 그렇지만 하나의 파이프라인에 여러 개의 활동이 있을 수 있습니다.

파이프라인에 여러 활동이 있고 활동의 출력이 다른 활동의 입력이 아닌 경우 해당 활동에 대한 입력 데이터 조각이 준비되면 활동을 병렬로 실행할 수 있습니다.

한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 지정하여 두 활동을 연결할 수 있습니다. 두 번째 활동은 첫 번째 활동이 완료된 경우에만 실행됩니다.

:::image type="content" source="./media/data-factory-create-pipelines/chaining-one-pipeline.png" alt-text="동일한 파이프라인에서 활동 연결":::

이 샘플에서 파이프라인에는 두 개의 Activity1과 Activity2가 있습니다. Activity1은 Dataset1을 입력으로 받고 출력으로 Dataset2를 생성합니다. Activity는 Dataset2를 입력으로 받고 출력으로 Dataset3를 생성합니다. Activity1의 출력(Dataset2)이 Activity2의 입력이므로 Activity2는 Activity가 성공적으로 완료되고 Dataset2 조각을 생성한 후에만 실행됩니다. Activity1이 어떤 이유로든 실패하여 Dataset2 조각을 생성하지 않는 경우 Activity2는 해당 조각에 대해 실행되지 않습니다(예: 오전 9시 ~ 오전 10시).

다른 파이프라인에 있는 활동을 연결할 수도 있습니다.

:::image type="content" source="./media/data-factory-create-pipelines/chaining-two-pipelines.png" alt-text="두 개의 파이프라인에서 활동 연결":::

이 샘플에서 Pipeline1은 Dataset1을 입력으로 받고 Dataset2를 출력으로 생성하는 하나의 활동만 포함합니다. Pipeline2는 Dataset2를 입력으로 받고 Dataset3를 출력으로 생성하는 하나의 활동만 포함합니다.

자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요.
## <a name="create-and-monitor-pipelines"></a>파이프라인 만들기 및 모니터링
다음 도구 또는 SDK 중 하나를 사용하여 파이프라인을 만들 수 있습니다.

- 복사 마법사
- Visual Studio
- Azure PowerShell
- Azure Resource Manager 템플릿
- REST API
- .NET API

이러한 도구 또는 SDK 중 하나를 사용하여 파이프라인을 만들기 위한 단계별 지침을 보려면 다음 자습서를 참조하세요.

- [데이터 변환 활동을 사용하여 파이프라인 빌드](data-factory-build-your-first-pipeline.md)
- [데이터 이동 활동을 사용하여 파이프라인 빌드](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

파이프라인이 생성/배포되면 Azure Portal 블레이드 또는 모니터 및 관리 앱을 사용하여 파이프라인을 관리하고 모니터링할 수 있습니다. 단계별 지침은 다음 항목을 참조하세요.

- [Azure Portal 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)
- [모니터 및 관리 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>일회성 파이프라인
파이프라인 정의 내에 지정한 시작 및 종료 시간 내에서 정기적(예: 매시간 또는 매일)으로 실행되도록 파이프라인을 만들고 일정을 예약할 수 있습니다. 자세한 내용은 활동 예약을 참조하세요. 한 번만 실행되는 파이프라인을 만들 수도 있습니다. 이렇게 하려면 다음 JSON 샘플에서 보여 주듯이 파이프라인 정의에서 **pipelineMode** 속성을 **onetime** 으로 설정합니다. 이 속성의 기본값은 **scheduled** 입니다.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

다음 사항에 유의하세요.

* 파이프라인의 **start** 및 **end** 시간은 지정되지 않습니다.
* Data Factory에서 값이 사용되지 않더라도 입력 및 출력 데이터 세트의 **availability**(**frequency** 및 **interval**)가 지정됩니다.
* 다이어그램 뷰는 일회성 파이프라인을 표시하지 않습니다. 이 동작은 의도된 것입니다.
* 일회성 파이프라인은 업데이트할 수 없습니다. 일회성 파이프라인을 복제하고, 이름을 변경하고, 속성을 업데이트하고, 배포하여 또 다른 파이프라인을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 데이터 세트에 대한 자세한 내용은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요.
- 파이프라인을 예약하고 실행하는 방법에 대한 자세한 내용은 [Azure Data Factory에서 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.
