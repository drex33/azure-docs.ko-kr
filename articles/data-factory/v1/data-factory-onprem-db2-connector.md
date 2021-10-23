---
title: Azure Data Factory를 사용하여 DB2에서 데이터 이동
description: Azure Data Factory 복사 활동을 사용하여 온-프레미스 DB2 데이터베이스에서 데이터를 이동하는 방법을 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f5ed929ecd81661dd2d50881dff8ed7ccaf6eef2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263803"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Azure Data Factory 복사 활동을 사용하여 DB2에서 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-onprem-db2-connector.md)
> * [버전 2(현재 버전)](../connector-db2.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 DB2 커넥터](../connector-db2.md)를 참조하세요.


이 문서에서는 Azure Data Factory에서 복사 활동을 사용하여 온-프레미스 DB2 데이터베이스에서 데이터 저장소로 데이터를 복사하는 방법에 대해 설명합니다. [Data Factory 데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 문서에서 지원되는 싱크로 나열되는 저장소로 데이터를 복사할 수 있습니다. 이 항목은 복사 활동을 사용한 데이터 이동에 대해 간략히 설명하고 지원되는 데이터 저장소 조합을 나열하는 Data Factory 문서를 기반으로 하여 작성되었습니다. 

Data Factory는 현재 DB2 데이터베이스에서 [지원되는 싱크 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)로 데이터를 이동하는 작업만 지원하고, 다른 데이터 저장소에서 DB2 데이터베이스로 데이터를 이동하는 작업은 지원하지 않습니다.

## <a name="prerequisites"></a>필수 조건
Data Factory는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 사용하여 온-프레미스 DB2 데이터베이스에 연결하는 작업을 지원합니다. 데이터를 이동하기 위해 게이트웨이 데이터 파이프라인을 설정하는 단계별 지침은 [온-프레미스에서 클라우드로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

DB2가 Azure IaaS VM에 호스팅되는 경우에도 게이트웨이가 필요합니다. 데이터 저장소와 동일한 IaaS VM에 게이트웨이를 설치할 수 있습니다. 게이트웨이에서 데이터베이스에 연결할 수 있으면 다른 VM에 게이트웨이를 설치할 수 있습니다.

데이터 관리 게이트웨이에서 기본 제공 DB2 드라이버를 제공하므로 DB2에서 데이터를 복사하기 위해 드라이버를 수동으로 설치할 필요가 없습니다.

> [!NOTE]
> 연결 및 게이트웨이 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 문서를 참조하세요.


## <a name="supported-versions"></a>지원되는 버전
Data Factory DB2 커넥터는 DRDA(Distributed Relational Database Architecture) SQL Access Manager 버전 9, 10 및 11과 함께 다음 IBM DB2 플랫폼 및 버전을 지원합니다.

* z/OS용 IBM DB2 버전 11.1
* z/OS용 IBM DB2 버전 10.1
* i(AS400)용 IBM DB2 버전 7.2
* i(AS400)용 IBM DB2 버전 7.1
* LUW(Linux, UNIX, and Windows)용 IBM DB2 버전 11
* LUW용 IBM DB2 버전 10.5
* LUW용 IBM DB2 버전 10.1

> [!TIP]
> "SQL 문 실행 요청에 해당하는 패키지가 없습니다. SQLSTATE=51002 SQLCODE=-805"라는 오류 메시지가 표시되면 OS에 일반 사용자에게 필요한 패키지가 만들어지지 않은 것입니다. 이 문제를 해결하려면 DB2 서버 유형에 대한 다음 지침을 따릅니다.
> - i(AS400)용 DB2: 복사 활동을 실행하기 전에 고급 사용자가 일반 사용자에 대한 컬렉션을 만들 수 있도록 합니다. 컬렉션을 만들려면 `create collection <username>` 명령을 사용합니다.
> - z/OS 또는 LUW용 DB2: 높은 권한 계정(패키지 권한 및 BIND, BINDADD, GRANT EXECUTE TO PUBLIC 권한이 있는 고급 사용자 또는 관리자)을 사용하여 복사를 한 번 실행합니다. 필요한 패키지는 복사 중에 자동으로 만들어집니다. 나중에 후속 복사 실행을 위해 일반 사용자로 다시 전환할 수 있습니다.

## <a name="getting-started"></a>시작
여러 도구/API를 사용하여 복사 활동이 포함된 파이프라인을 만들어 온-프레미스 DB2 데이터 저장소의 데이터를 이동할 수 있습니다. 

- 파이프라인을 만드는 가장 쉬운 방법은 Azure Data Factory 복사 마법사를 사용하는 것입니다. 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 
- 또한 Visual Studio, Azure PowerShell, Azure Resource Manager 템플릿, .NET API, REST API 등의 도구를 사용하여 파이프라인을 만들 수 있습니다. 복사 활동이 포함된 파이프라인을 만드는 단계별 지침은 [복사 활동 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. 

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 연결된 서비스를 만듭니다.
2. 복사 활동의 입력 및 출력 데이터를 나타내는 데이터 세트를 만듭니다. 
3. 입력과 출력으로 각각의 데이터 세트를 사용하는 복사 활동이 포함된 파이프라인을 만듭니다. 

복사 마법사를 사용하는 경우 Data Factory 연결된 서비스, 데이터 세트 및 파이프라인 엔터티에 대한 JSON 정의가 자동으로 만들어집니다. 도구 또는 API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 Data Factory 엔터티를 정의합니다. JSON 예제: DB2에서 Azure Blob Storage로 데이터 복사에서는 온-프레미스 DB2 데이터 스토리지에서 데이터를 복사하는 데 사용되는 Data Factory 엔터티에 대한 JSON 정의를 보여 줍니다.

다음 섹션에서는 DB2 데이터 저장소와 관련된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="db2-linked-service-properties"></a>DB2 연결된 서비스 속성
다음 표에는 DB2 연결된 서비스와 관련된 JSON 속성이 나열되어 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| **type** |이 속성을 **OnPremisesDb2** 로 설정해야 합니다. |예 |
| **server** |DB2 서버의 이름입니다. |예 |
| **database** |DB2 데이터베이스의 이름입니다. |예 |
| **schema** |DB2 데이터베이스의 스키마 이름입니다. 대/소문자를 구분합니다. |예 |
| **authenticationType** |DB2 데이터베이스에 연결하는 데 사용되는 인증 유형입니다. 가능한 값은 Anonymous, Basic 및 Windows입니다. |예 |
| **username** |Basic 또는 Windows 인증을 사용하는 경우 사용자 계정의 이름입니다. |예 |
| **password** |사용자 계정의 암호입니다. |예 |
| **gatewayName** |Data Factory 서비스에서 온-프레미스 DB2 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. |예 |

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트를 정의하는 데 사용할 수 있는 섹션 및 속성 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. JSON 데이터 세트에 대한 **structure**, **availability** 및 **policy** 과 같은 섹션은 모든 데이터 세트 유형(Azure SQL, Azure Blob Storage, Azure Table Storage 등)에서 비슷합니다.

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. DB2 데이터 세트를 포함하는 **RelationalTable** 형식의 데이터 세트에 대한 **typeProperties** 섹션에는 다음과 같은 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| **tableName** |연결된 서비스에서 참조하는 DB2 데이터베이스 인스턴스의 테이블 이름입니다. 대/소문자를 구분합니다. |아니요(**RelationalSource** 형식 복사 활동의 **query** 속성이 지정된 경우) |

## <a name="copy-activity-properties"></a>복사 활동 속성
복사 활동을 정의하는 데 사용할 수 있는 섹션 및 속성 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. **name**, **description**, **inputs** 테이블, **outputs** 테이블 및 **policy** 와 같은 복사 활동 속성은 모든 유형의 활동에 사용할 수 있습니다. 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 유형에 대해 다릅니다. 복사 활동의 경우 속성은 데이터 원본 및 싱크의 형식에 따라 달라집니다.

복사 활동의 경우 원본이 **RelationalSource** 형식인 경우(DB2 포함) **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| **쿼리** |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `"query": "select * from "MySchema"."MyTable""` |아니요(데이터 세트의 **tableName** 속성이 지정된 경우) |

> [!NOTE]
> 스키마 및 테이블 이름은 대/소문자를 구분합니다. 쿼리 문에서 ""(큰 따옴표)를 사용하여 속성 이름을 묶습니다.

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON 예제: DB2에서 Azure Blob Storage로 데이터 복사
이 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. DB2 데이터베이스에서 Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory 복사 활동을 사용하여 [지원되는 데이터 저장소 싱크 형식](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 데이터를 복사할 수 있습니다.

샘플에 포함된 Data Factory 엔터티는 다음과 같습니다.

- [OnPremisesDb2](data-factory-onprem-db2-connector.md) 형식의 DB2 연결된 서비스
- [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 Azure Blob 스토리지 연결된 서비스
- [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
- [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 속성을 사용하는 복사 활동이 있는 [파이프라인](data-factory-create-pipelines.md)

샘플은 DB2 데이터베이스의 쿼리 결과에서 Azure Blob에 매시간 데이터를 복사합니다. 샘플에 사용된 JSON 속성은 엔터티 정의 뒤에 나오는 섹션에서 설명됩니다.

첫 번째 단계로 데이터 관리 게이트웨이를 설치하고 구성합니다. 지침은 [온-프레미스 위치와 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

**DB2 연결된 서비스**

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

**비디오: Linux에서 Azure File Storage 사용**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**DB2 입력 데이터 세트**

샘플에서는 시계열 데이터에 대한 "timestamp"라는 열이 있는 "MyTable"이라는 DB2 테이블을 만들었다고 가정합니다.

**external** 속성이 "true"로 설정되었습니다. 이 설정을 사용하는 경우 이 데이터 세트는 Data Factory의 외부에 있고 Data Factory의 활동으로 생성되지 않는다고 Data Factory 서비스에 알려줍니다. **type** 속성은 **RelationalTable** 로 설정되어 있습니다.


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

**Azure Blob 출력 데이터 세트**

**frequency** 속성을 "Hour"로 설정하고 **interval** 속성을 1로 설정하여 매시간 새 Blob에 데이터를 씁니다. Blob에 대한 **folderPath** 속성은 처리 중인 조각의 시작 시간에 따라 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월, 일 및 시 부분을 사용합니다.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**복사 활동에 대한 파이프라인**

파이프라인에는 지정된 입력 및 출력 데이터 세트를 사용하도록 구성되고 매 시간마다 실행하도록 예약된 복사 활동이 포함됩니다. 파이프라인에 대한 JSON 정의에서 **source** 형식은 **RelationalSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 "Orders" 테이블에서 데이터를 선택합니다.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
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
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a> DB2에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 대로 복사 활동에서는 다음 2단계 접근 방법을 사용하여 source 형식에서 sink 형식으로의 자동 형식 변환을 수행합니다.

1. 네이티브 소스 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

복사 활동에서 DB2 형식에서 .NET 형식으로 데이터를 변환할 때 다음 매핑이 사용됩니다.

| DB2 데이터베이스 형식 | .NET Framework 형식 |
| --- | --- |
| SmallInt |Int16 |
| 정수 |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| 숫자 |Decimal |
| Date |DateTime |
| 시간 |TimeSpan |
| 타임스탬프 |DateTime |
| Xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| 이진 |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| 정수 |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| 숫자 |Decimal |
| Date |DateTime |
| 시간 |TimeSpan |
| 타임스탬프 |DateTime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑
원본 데이터 세트의 열을 싱크 데이터 세트의 열에 매핑하는 방법을 알아보려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-reads-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기
관계형 데이터 저장소에서 데이터를 복사할 때 의도하지 않은 결과를 방지하기 위해 반복성을 명심해야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 다시 시도 **policy** 속성을 구성할 수도 있습니다. 조각의 재실행 횟수 및 재실행 방법과 관계 없이 동일한 데이터를 읽어야 합니다. 자세한 내용은 [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
[복사 활동 성능 및 조정 가이드](data-factory-copy-activity-performance.md)에서 복사 활동의 성능에 영향을 주는 주요 요소와 성능을 최적화하는 방법에 대해 알아보세요.
