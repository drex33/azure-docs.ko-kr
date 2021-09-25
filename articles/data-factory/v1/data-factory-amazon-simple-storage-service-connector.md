---
title: Data Factory를 사용하여 Amazon Simple Storage Service에서 데이터 이동
description: Azure Data Factory를 사용하여 Amazon 단순 Storage 서비스(S3)에서 데이터를 이동하는 방법에 대해 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9915f2bb83c8d2f1ca6491d3603ff78c8c3df4d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655698"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Amazon 단순 Storage 서비스에서 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-amazon-simple-storage-service-connector.md)
> * [버전 2(현재 버전)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 Amazon S3 커넥터](../connector-amazon-simple-storage-service.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Amazon Simple Storage Service(S3)에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

Amazon S3에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블을 참조하세요. 현재 Data Factory는 Amazon S3에서 다른 데이터 저장소로의 데이터 이동 만을 지원하지만, 다른 데이터 저장소에서 Amazon S3로 데이터 이동은 지원하지 않습니다.

## <a name="required-permissions"></a>필요한 사용 권한
Amazon S3에서 데이터를 복사하려면 다음과 같은 권한이 부여되어 있는지 확인합니다.

* Amazon S3 개체 작업에 대한 `s3:GetObject` 및 `s3:GetObjectVersion`.
* Amazon S3 버킷 작업에 대한 `s3:ListBucket`. Data Factory 복사 마법사를 사용하는 경우 `s3:ListAllMyBuckets`도 필요합니다.

Amazon S3 사용 권한의 전체 목록은 [정책에서 사용 권한 지정](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)을 참조하세요.

## <a name="getting-started"></a>시작
다른 도구 또는 API를 사용하여 Amazon S3 원본의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사** 를 사용하는 것입니다. 빠른 연습을 위해서는 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API**, **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구 또는 API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 데이터 팩터리 엔터티를 정의합니다. Amazon S3 데이터 저장소의 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의에 대한 샘플은 이 문서의 [JSON의 예: Amazon S3에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) 섹션을 참조하세요.

> [!NOTE]
> 복사 작업에 대해 지원되는 파일 및 압축 형식에 대한 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md)을 참조하세요.

다음 섹션에서는 Amazon S3에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
연결된 서비스는 데이터 저장소를 데이터 팩터리에 연결합니다. Amazon S3 데이터 저장소를 데이터 팩터리에 연결하는 **AwsAccessKey** 형식의 연결된 서비스를 만듭니다. 다음 표는 Amazon S3(AwsAccessKey) 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| accessKeyID |비밀 액세스 키의 ID입니다. |문자열 |예 |
| secretAccessKey |비밀 액세스 키 자체입니다. |암호화된 비밀 문자열 |예 |

>[!NOTE]
>이 커넥터를 사용하려면 Amazon S3에서 데이터를 복사하기 위해 IAM 계정에 대한 액세스 키가 필요합니다. [임시 보안 자격 증명](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)은 지원되지 않습니다.
>

다음은 예제입니다.

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

## <a name="dataset-properties"></a>데이터 세트 속성
Azure Blob Storage에서 입력 데이터를 표시할 데이터 세트를 지정하려면 데이터 세트의 형식 속성을 **AmazonS3** 으로 설정합니다. 데이터 세트의 **linkedServiceName** 속성을 Amazon S3 연결된 서비스의 이름으로 설정합니다. 데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md)를 참조하세요. 

구조, 가용성 및 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(예: SQL Database, Azure Blob, Azure 테이블). **typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **AmazonS3** 형식(Amazon S3 데이터 세트를 포함)의 데이터 세트에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| bucketName |S3 버킷 이름입니다. |String |예 |
| key |S3 개체 키입니다. |String |예 |
| 접두사 |S3 개체 키에 대한 접두사입니다. 이 접두사로 시작하는 키를 가진 개체가 선택됩니다. 키가 비어 있을 때에만 적용됩니다. |String |예 |
| 버전 |S3 버전 관리를 사용하도록 설정하면 S3 개체의 버전입니다. |String |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [JSON 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. | |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate** 입니다. 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. | |예 |


> [!NOTE]
> **bucketName + 키** 는 S3 개체의 위치를 지정합니다. 여기서 버킷은 S3 개체에 대한 루트 컨테이너이며 키는 S3 개체의 전체 경로입니다.

### <a name="sample-dataset-with-prefix"></a>접두사를 사용한 샘플 데이터 세트

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
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
### <a name="sample-dataset-with-version"></a>샘플 데이터 세트(버전 포함)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
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

### <a name="dynamic-paths-for-s3"></a>S3에 대한 동적 경로
이전 샘플에서는 Amazon S3 데이터 세트의 **키** 및 **bucketName** 속성에 대해 고정 값을 사용합니다.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

SliceStart와 같은 시스템 변수를 사용하여 Data Factory가 런타임에 동적으로 이러한 속성을 계산하게 할 수 있습니다.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Amazon S3 데이터 세트의 **접두사** 속성에 대해서도 동일하게 수행할 수 있습니다. 지원되는 함수 및 변수 목록은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md)를 참조하세요.

## <a name="copy-activity-properties"></a>복사 작업 속성
작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다. 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 달라집니다. 복사 작업의 원본이 **FileSystemSource** 형식인 경우(Amazon S3 포함) **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |S3 개체를 디렉터리 아래에 재귀적으로 나열할 것인지를 지정합니다. |true/false |예 |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON 예제: Amazon S3에서 Azure Blob Storage로 데이터 복사
이 샘플은 Amazon S3 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여줍니다. 그러나 Data Factory의 복사 작업을 사용하여 [지원되는 싱크](data-factory-data-movement-activities.md#supported-data-stores-and-formats)로 직접 데이터를 복사할 수 있습니다.

샘플은 다음 Data Factory 엔터티에 대한 JSON 정의를 제공합니다. 해당 정의에 따라 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 Amazon S3에서 Blob 스토리지로 데이터를 복사하는 파이프라인을 만들 수 있습니다.   

* [AwsAccessKey](#linked-service-properties)형식의 연결된 서비스.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [AmazonS3](#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [FileSystemSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 웹 테이블의 데이터를 Amazon S3으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

### <a name="amazon-s3-linked-service"></a>Amazon S3 연결된 서비스

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

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스

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

### <a name="amazon-s3-input-dataset"></a>Amazon S3 입력 데이터 세트

**"external": true** 로 설정하면 데이터 세트가 데이터 팩터리의 외부에 있다는 사실이 Data Factory 서비스에 전달됩니다. 파이프라인에서의 작업에 의해 생성되지 않은 입력 데이터 세트에서 이 속성을 true로 설정합니다.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
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


### <a name="azure-blob-output-dataset"></a>Azure Blob 출력 데이터 세트

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월, 일 및 시 부분을 사용합니다.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Amazon S3 원본 및 Blob 싱크를 사용하는 파이프라인의 복사 작업

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **FileSystemSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
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
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

* Data Factory에서 데이터 이동(복사 작업) 성능에 영향을 미치는 주요 요소 및 다양한 최적화 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

* 복사 작업이 포함된 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
