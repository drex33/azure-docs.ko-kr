---
title: Azure Data Factory를 사용하여 Amazon Redshift에서 데이터 이동
description: Azure Data Factory 복사 작업을 사용하여 Amazon Redshift에서 데이터를 이동하는 방법을 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9a854b75dd9ad6cf84b17d8ed86327629f49ef17
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226568"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Amazon Redshift에서 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-amazon-redshift-connector.md)
> * [버전 2(현재 버전)](../connector-amazon-redshift.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 Amazon Redshift 커넥터](../connector-amazon-redshift.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Amazon Redshift에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

Data Factory는 현재 Amazon Redshift에서 [지원되는 싱크 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)로 데이터를 이동하는 작업만 지원합니다. 다른 데이터 저장소에서 Amazon Redshift로 데이터를 이동하는 작업은 지원되지 않습니다.

> [!TIP]
> Amazon Redshift에서 많은 양의 데이터를 복사할 때 최상의 성능을 위해 Amazon S3(Amazon Simple Storage Service)를 통해 기본 제공 Redshift **UNLOAD** 를 사용하는 것이 좋습니다. 자세한 내용은 [UNLOAD를 사용하여 Amazon Redshift에서 데이터 복사](#use-unload-to-copy-data-from-amazon-redshift)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
* 온-프레미스 데이터 저장소로 데이터를 이동하는 경우 온-프레미스 컴퓨터에 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 설치합니다. 온-프레미스 컴퓨터 IP 주소를 사용하여 게이트웨이에 대한 액세스 권한을 Amazon Redshift에 부여합니다. 자세한 지침은 [클러스터에 대한 액세스 권한 부여](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)를 참조하세요.
* Azure 데이터 저장소로 데이터를 이동하려면 [Microsoft Azure 데이터 센터에서 사용되는 컴퓨팅 IP 주소 및 SQL 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.

## <a name="getting-started"></a>시작
다른 도구 및 API를 사용하여 Amazon Redshift 원본의 데이터를 이동하는 복사 활동으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 Azure Data Factory 복사 마법사를 사용하는 것입니다. 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

Visual Studio, Azure PowerShell 또는 기타 도구를 사용하여 파이프라인을 만들 수도 있습니다. Azure Resource Manager 템플릿, .NET API 또는 REST API도 파이프라인을 만드는 데 사용할 수 있습니다. 복사 활동이 포함된 파이프라인을 만드는 단계별 지침은 [복사 활동 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 연결된 서비스를 만듭니다.
2. 복사 활동의 입력 및 출력 데이터를 나타내는 데이터 세트를 만듭니다.
3. 입력과 출력으로 각각의 데이터 세트를 사용하는 복사 활동이 포함된 파이프라인을 만듭니다.

복사 마법사를 사용하는 경우 이러한 Data Factory 엔터티에 대한 JSON 정의가 자동으로 만들어집니다. 도구 또는 API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 Data Factory 엔터티를 정의합니다. JSON 예제: Amazon Redshift에서 Azure Blob Storage로 데이터 복사에서는 Amazon Redshift 데이터 저장소에서 데이터를 복사하는 데 사용되는 Data Factory 엔터티에 대한 JSON 정의를 보여 줍니다.

다음 섹션에서는 Amazon Redshift에 대한 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음 표에 Amazon Redshift 연결된 서비스와 관련된 JSON 요소에 대한 설명이 나와 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| **type** |이 속성은 **AmazonRedshift** 로 설정되어야 합니다. |예 |
| **server** |Amazon Redshift 서버의 IP 주소 또는 호스트 이름입니다. |예 |
| **port** |Amazon Redshift 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트 수입니다. |아니요(기본값: 5439) |
| **database** |Amazon Redshift 데이터베이스의 이름입니다. |예 |
| **username** |데이터베이스에 대한 액세스 권한이 있는 사용자의 이름입니다. |예 |
| **password** |사용자 계정의 암호입니다. |예 |

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트를 정의하는 데 사용할 수 있는 섹션 및 속성 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. **structure**, **availability** 및 **policy** 섹션은 모든 데이터 세트 형식에 대해 유사합니다. 데이터 세트 형식의 예로 Azure SQL, Azure Blob Storage 및 Azure Table Storage가 있습니다.

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 형식(Amazon Redshift 데이터 세트를 포함)의 데이터 세트에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| **tableName** |연결된 서비스가 참조하는 Amazon Redshift 데이터베이스에서 테이블의 이름입니다. |아니요(**RelationalSource** 형식 복사 활동의 **query** 속성이 지정된 경우) |

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. **name**, **description**, **inputs** 테이블, **outputs** 테이블 및 **policy** 속성은 모든 유형의 작업에 사용할 수 있습니다. **typeProperties** 섹션에서 사용할 수 있는 속성은 각 작업 유형에 대해 다릅니다. 복사 활동의 경우 속성은 데이터 원본 및 싱크의 형식에 따라 달라집니다.

복사 활동의 경우 원본이 **AmazonRedshiftSource** 형식인 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| **쿼리** | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |아니요(데이터 세트의 **tableName** 속성이 지정된 경우) |
| **redshiftUnloadSettings** | Redshift **UNLOAD** 명령을 사용하는 경우 속성 그룹을 포함합니다. | 예 |
| **s3LinkedServiceName** | 중간 저장소로 사용할 Amazon S3입니다. 연결된 서비스는 **AwsAccessKey** 형식의 Azure Data Factory 이름을 사용하여 지정됩니다. | **redshiftUnloadSettings** 속성을 사용할 때 필요합니다. |
| **bucketName** | 중간 데이터를 저장하는 데 사용할 Amazon S3 버킷을 나타냅니다. 이 속성을 제공하지 않으면 복사 작업에서 자동으로 버킷을 생성합니다. | **redshiftUnloadSettings** 속성을 사용할 때 필요합니다. |

또는 **typeProperties** 섹션의 다음 속성과 함께 **RelationalSource**(Amazon Redshift 포함) 형식도 사용할 수 있습니다. 이 원본 유형은 Redshift **UNLOAD** 명령을 지원하지 않습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| **쿼리** |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | 아니요(데이터 세트의 **tableName** 속성이 지정된 경우) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>UNLOAD를 사용하여 Amazon Redshift에서 데이터 복사

Amazon Redshift [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) 명령은 Amazon S3에서 하나 이상의 파일에 대한 쿼리 결과를 언로드합니다. 이 명령은 Redshift에서 큰 데이터 세트를 복사하기 위해 Amazon에서 권장하는 방법입니다.

**예제: Amazon Redshift에서 Azure Synapse Analytics로 데이터 복사**

이 예제에서는 Amazon Redshift에서 Azure Synapse Analytics로 데이터를 복사합니다. 이 예제에서는 Redshift **UNLOAD** 명령, 준비된 복사 데이터 및 Microsoft PolyBase를 사용합니다.

이 샘플 사용 사례의 경우 먼저 복사 작업이 **redshiftUnloadSettings** 옵션에 구성된 대로 Amazon Redshift에서 Amazon S3로 데이터를 언로드합니다. 다음으로, **stagingSettings** 옵션에 지정된 대로 데이터가 Amazon S3에서 Azure Blob Storage로 복사됩니다. 마지막으로 PolyBase는 데이터를 Azure Synapse Analytics에 로드합니다. 모든 중간 형식은 복사 작업에서 처리됩니다.

:::image type="content" source="media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png" alt-text="Amazon Redshift에서 Azure Synapse Analytics로 워크플로 복사":::

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON 예제: Amazon Redshift에서 Azure Blob Storage로 데이터 복사
이 샘플은 Amazon Redshift 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 복사 작업을 사용하여 모든 [지원되는 싱크](data-factory-data-movement-activities.md#supported-data-stores-and-formats)로 직접 데이터를 복사할 수 있습니다.

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

* [AmazonRedshift](#linked-service-properties) 형식의 연결된 서비스
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [RelationalTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [RelationalSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 속성을 사용하는 복사 활동이 있는 [파이프라인](data-factory-create-pipelines.md)

샘플에서는 Amazon Redshift의 쿼리 결과에서 Azure Blob으로 매시간 데이터를 복사합니다. 샘플에 사용된 JSON 속성은 엔터티 정의 뒤에 나오는 섹션에서 설명됩니다.

**Amazon Redshift 연결된 서비스**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**비디오: Linux에서 Azure File Storage 사용**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon Redshift 입력 데이터 세트**

**external** 속성을 "true"로 설정하면 데이터 세트가 데이터 팩터리의 외부에 있다는 사실이 Data Factory 서비스에 전달됩니다. 이 속성 설정은 데이터 세트가 데이터 팩터리의 작업에 의해 생성되지 않음을 나타냅니다. 파이프라인에서의 활동에 의해 생성되지 않은 입력 데이터 세트에서 속성을 true로 설정합니다.

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

**Azure Blob 출력 데이터 세트**

**frequency** 속성을 "Hour"로 설정하고 **interval** 속성을 1로 설정하여 매시간 새 Blob에 데이터를 씁니다. Blob에 대한 **folderPath** 속성은 동적으로 평가됩니다. 속성 값은 처리 중인 조각의 시작 시간을 기반으로 합니다. 폴더 경로는 시작 시간의 년, 월, 일 및 시 부분을 사용합니다.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Azure Redshift 원본(RelationalSource 형식) 및 Azure Blob 싱크를 사용하는 파이프라인의 복사 작업**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 활동을 포함합니다. 파이프라인은 매시간 실행하도록 예약됩니다. 파이프라인에 대한 JSON 정의에서 **source** 형식은 **RelationalSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Amazon Redshift에 대한 형식 매핑
[데이터 이동 작업](data-factory-data-movement-activities.md) 문서에서 설명한 대로 복사 작업에서는 source 형식에서 sink 형식으로의 자동 형식 변환을 수행합니다. 형식은 2단계 접근 방식을 사용하여 변환됩니다.

1. 네이티브 소스 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

복사 활동에서 Amazon Redshift 형식에서 .NET 형식으로 데이터를 변환할 때 다음 매핑이 사용됩니다.

| Amazon Redshift 형식 | .NET 형식 |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| bigint |Int64 |
| DECIMAL |Decimal |
| real |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| timestamp |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑
원본 데이터 세트의 열을 싱크 데이터 세트의 열에 매핑하는 방법을 알아보려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-reads-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기
관계형 데이터 저장소에서 데이터를 복사할 때 의도하지 않은 결과를 방지하기 위해 반복성을 명심해야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 다시 시도 **policy** 속성을 구성할 수 있습니다. 조각의 재실행 횟수에 관계없이 동일한 데이터를 읽어야 합니다. 또한 조각을 다시 실행하는 방법에 관계 없이 동일한 데이터를 읽어야 합니다. 자세한 내용은 [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
[복사 활동 성능 및 조정 가이드](data-factory-copy-activity-performance.md)에서 복사 활동의 성능에 영향을 주는 주요 요소와 성능을 최적화하는 방법에 대해 알아보세요.

## <a name="next-steps"></a>다음 단계
복사 활동이 포함된 파이프라인을 만드는 단계별 지침은 [복사 활동 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
