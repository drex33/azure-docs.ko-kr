---
title: Azure Table에서 데이터 이동
description: Azure Data Factory를 사용하여 Azure Table Storage 간 데이터를 이동하는 방법에 대해 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4443a84088bc5800a1ee94abe4dc10f7590b3747
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592353"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure 테이블 간 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-azure-table-connector.md)
> * [버전 2(현재 버전)](../connector-azure-table-storage.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 Azure Table Storage 커넥터](../connector-azure-table-storage.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Table Storage 간에 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다. 

모든 지원되는 원본 데이터 저장소에서 Azure Table Storage로 또는 Azure Blob Storage에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블을 참조하세요. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 Azure Table Storage 간에 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사** 를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API**, **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. 

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다. 

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다. 
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다. 

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. 다른 곳에서 Azure Table Storage로 또는 그 반대로 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 예](#json-examples) 섹션을 참조하세요.

다음 섹션에서는 Azure Table Storage에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다. 

## <a name="linked-service-properties"></a>연결된 서비스 속성
Azure Blob Storage를 Azure Data Factory에 연결하는 데 사용할 수 있는 두 가지 유형의 연결된 서비스가 있습니다. **AzureStorage** 연결된 서비스 및 **AzureStorageSas** 연결된 서비스입니다. Azure Storage 연결된 서비스는 Azure Storage에 대한 전역 액세스로 Data Factory를 제공합니다. 반면 Azure Storage SAS(공유 액세스 서명) 연결된 서비스는 Azure Storage에 대한 제한/시간 제한 액세스로 Data Factory를 제공합니다. 이 두 연결된 서비스에는 다른 차이가 없습니다. 필요에 맞는 연결된 서비스를 선택합니다. 다음 섹션에서는 이러한 두 연결된 서비스에 대한 자세한 정보를 제공합니다.

[!INCLUDE [data-factory-azure-storage-linked-services](includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **AzureTable** 데이터 세트 형식에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Azure 테이블 데이터베이스 인스턴스에서 테이블의 이름입니다. |예. azureTableSourceQuery 없이 tableName을 지정하면 테이블의 모든 레코드를 대상에 복사합니다. 또한 azureTableSourceQuery를 지정하면 쿼리를 만족 하는 테이블의 레코드를 대상에 복사합니다. |

### <a name="schema-by-data-factory"></a>Data Factory에서의 스키마
Azure 테이블과 같은 스키마 없는 데이터 저장소의 경우 Data Factory 서비스는 다음 방법 중 하나로 스키마를 유추합니다.

1. 데이터 세트 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하는 경우 Data Factory 서비스는 이 구조를 스키마로 인식합니다. 이 경우 행에 열의 값이 포함되어 있지 않으면 null 값이 제공됩니다.
2. 데이터 세트 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하지 않는 경우 Data Factory는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 이 경우 첫 번째 행에 전체 스키마가 포함되어 있지 않으면 일부 열이 복사 작업의 결과에서 누락됩니다.

따라서 스키마 없는 데이터 원본에 대한 모범 사례는 **구조** 속성을 사용하여 데이터의 구조를 지정하는 것입니다.

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 데이터 세트, 정책 등의 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

**AzureTableSource** 는 typeProperties 섹션에서 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| AzureTableSourceQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |Azure 테이블 쿼리 문자열. 다음 섹션의 예제를 참조하세요. |아니요. azureTableSourceQuery 없이 tableName을 지정하면 테이블의 모든 레코드를 대상에 복사합니다. 또한 azureTableSourceQuery를 지정하면 쿼리를 만족 하는 테이블의 레코드를 대상에 복사합니다. |
| azureTableSourceIgnoreTableNotFound |존재하지 않는 테이블의 예외를 받아들이는지를 나타냅니다. |TRUE<br/>FALSE |예 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 예제
Azure 테이블 열이 문자열 형식인 경우:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Azure 테이블 열이 날짜/시간 형식인 경우:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** 는 typeProperties 섹션에서 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |싱크에서 사용할 수 있는 기본 파티션 키 값입니다. |문자열 값입니다. |예 |
| azureTablePartitionKeyName |해당 값이 파티션 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 AzureTableDefaultPartitionKeyValue가 파티션 키로 사용됩니다. |열 이름 |예 |
| azureTableRowKeyName |해당 열 값이 행 키로 사용되는 열의 이름을 지정합니다. 지정하지 않으면 각 행에 GUID를 사용합니다. |열 이름 |예 |
| azureTableInsertType |Azure 테이블에 데이터를 삽입하는 모드입니다.<br/><br/>이 속성은 출력 테이블에서 파티션 및 행 키가 일치하는 기존 행의 값을 바꿀지 또는 병합할지 제어합니다. <br/><br/>이러한 설정(병합 및 바꾸기)이 작동하는 방법을 알아보려면 [엔터티 삽입 또는 병합](/rest/api/storageservices/Insert-Or-Merge-Entity) 및 [엔터티 삽입 또는 바꾸기](/rest/api/storageservices/Insert-Or-Replace-Entity)를 참조하세요. <br/><br>  이 설정은 테이블 수준이 아니라 행 수준에서 적용되며, 두 옵션 모두 출력 테이블에서 입력에 존재하지 않는 행을 삭제하지 않습니다. |병합(기본값)<br/>replace |예 |
| writeBatchSize |WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다. |정수(행 수) |아니요(기본값: 10000) |
| writeBatchTimeout |WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다. |timespan<br/><br/>예: "00:20:00"(20분) |No (기본적으로 스토리지 클라이언트 기본 시간 제한 값인 90초로 설정) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
대상 열을 azureTablePartitionKeyName으로 사용할 수 있기 전에 JSON 속성 변환기를 사용하여 원본 열을 대상 열에 매핑합니다.

다음 예제에서 원본 열 DivisionID은 대상 열 DivisionID에 매핑됩니다.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID는 파티션 키로 지정됩니다.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON 예
다음 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure Table Storage 및 Azure Blob 데이터베이스 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 임의의 원본에서 지원되는 싱크로 **직접** 데이터를 복사할 수 있습니다. 자세한 내용은 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md)에서 "지원되는 데이터 저장소 및 형식" 섹션을 참조하세요.

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>예제: Azure 테이블에서 Azure Blob으로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스(테이블 및 blob에 모두 사용됨)
2. [AzureTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
4. AzureTableSource 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 매시간 Azure 테이블의 기본 파티션에 속하는 데이터를 blob로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure Storage 연결된 서비스:**

```JSON
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
Azure Data Factory는 두 가지 유형의 Azure Storage 연결된 서비스: **AzureStorage** 및 **AzureStorageSas** 를 제공합니다. 첫 번째 것의 경우 계정 키를 포함하는 연결 문자열을 지정하고 이후 것의 경우 SAS(공유 액세스 서명) Uri를 지정합니다. 자세한 내용은 [연결된 서비스](#linked-service-properties) 섹션을 참조하세요.  

**Azure 테이블 입력 데이터 세트:**

샘플은 Azure Table에 'MyTable' 테이블을 만들었다고 가정합니다.

"external": "true" 설정은 데이터 세트가 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실을 Data Factory 서비스에 전달합니다.

```JSON
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

**Azure Blob 출력 데이터 세트:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**AzureTableSource 및 BlobSink를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **AzureTableSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다. **AzureTableSourceQuery** 속성으로 지정된 SQL 쿼리는 매시간 기본 파티션에서 복사할 데이터를 선택합니다.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
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
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>예제: Azure Blob에서 Azure 테이블로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스(테이블 및 blob에 모두 사용됨)
2. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
3. [AzureTable](#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 및 [AzureTableSink](#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure Blob에서 Azure 테이블로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure Storage 연결된 서비스(Azure 테이블 및 Blob용):**

```JSON
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

Azure Data Factory는 두 가지 유형의 Azure Storage 연결된 서비스: **AzureStorage** 및 **AzureStorageSas** 를 제공합니다. 첫 번째 것의 경우 계정 키를 포함하는 연결 문자열을 지정하고 이후 것의 경우 SAS(공유 액세스 서명) Uri를 지정합니다. 자세한 내용은 [연결된 서비스](#linked-service-properties) 섹션을 참조하세요.

**Azure Blob 입력 데이터 세트:**

데이터는 매시간 새 blob에 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 세트가 데이터 팩터리의 외부에 있으며 데이터 팩터리의 활동에 의해 생성되지 않음을 Data Factory 서비스에 알립니다.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
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

**Azure 테이블 출력 데이터 세트:**

샘플은 Azure Table의 'MyTable'이라는 테이블에 데이터를 복사합니다. Blob CSV 파일을 포함하려 하면 같은 수의 열을 사용하여 Azure 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**BlobSource 및 AzureTableSink를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **BlobSource** 로 설정되고 **sink** 형식은 **AzureTableSink** 로 설정됩니다.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
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
      }
    ]
  }
}
```
## <a name="type-mapping-for-azure-table"></a>Azure 테이블에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Azure 테이블에서 데이터를 이동하는 경우 다음 [Azure Table service에서 정의된 매핑](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)은 Azure 테이블 OData 형식에서 .NET 유형에 또는 그 반대로 사용됩니다.

| OData 데이터 형식 | .NET 형식 | 세부 정보 |
| --- | --- | --- |
| Edm.Binary |byte[] |바이트 배열은 최대 64KB입니다. |
| Edm.Boolean |bool |부울 값입니다. |
| Edm.DateTime |DateTime |UTC(협정 세계시)로 표현되는 64비트 값입니다. 지원되는 DateTime 범위는 서기 1601년 1월 1일 자정 12시부터 시작합니다. (서기), UTC입니다. 범위는 9999년 12월 31일에 끝납니다. |
| Edm.Double |double |64비트 부동 소수점 값입니다. |
| Edm.Guid |Guid |전역적으로 고유한 128 비트 식별자입니다. |
| Edm.Int32 |Int32 |32비트 정수입니다. |
| Edm.Int64 |Int64 |64비트 정수입니다. |
| Edm.String |String |UTF-16으로 인코딩된 값입니다. 문자열 값은 최대 64KB입니다. |

### <a name="type-conversion-sample"></a>형식 변환 샘플
다음 샘플은 Azure Blob에서 형식 변환이 있는 Azure 테이블에 데이터를 복사하는 데 사용합니다.

Blob 데이터 세트가 CSV 형식이며 3개의 열을 포함하고 있다고 가정합니다. 그 중 하나는 요일에 축약된 프랑스 이름을 사용하여 사용자 지정 datetime 형식을 사용하는 datetime 열입니다.

열에 대한 형식 정의가 다음과 같으면 Blob 원본 데이터 세트를 정의합니다.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Azure 테이블 OData 형식에서 .NET 형식에 형식 매핑을 지정하면 다음 스키마로 Azure 테이블에서 테이블을 정의합니다.

**Azure 테이블 스키마:**

| 열 이름 | Type |
| --- | --- |
| userId |Edm.Int64 |
| name |Edm.String |
| lastlogindate |Edm.DateTime |

다음으로, Azure 테이블 데이터 세트를 다음과 같이 정의합니다. 형식 정보가 기본 데이터 저장소에 이미 지정되어 있으므로 형식 정보로 'structure' 섹션을 지정할 필요가 없습니다.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

이 경우 .Blob에서 Azure 테이블에 데이터를 이동하면 Data Factory는fr-fr 문화권을 사용하여 사용자 지정 datetime 서식으로 Datetime 필드를 포함하는 형식 변환을 자동으로 수행합니다

> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.