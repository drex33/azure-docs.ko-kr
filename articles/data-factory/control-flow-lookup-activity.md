---
title: 조회 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 조회 작업을 사용하여 외부 원본에서 값을 조회하는 방법을 알아봅니다. 이 출력을 이후 작업에서 계속 참조할 수 있습니다.
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: bf2813381fe02a453c7a7c1ddec4c027117f0ce0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642283"
---
# <a name="lookup-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics의 조회 작업

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

조회 작업은 데이터 팩터리 및 Synapse 파이프라인에서 지원하는 데이터 원본에서 데이터 세트를 검색할 수 있습니다. 개체 이름을 하드 코딩하는 대신, 후속 작업에서 작동할 개체를 동적으로 결정하는 데 사용할 수 있습니다. 개체의 예로는 파일과 테이블이 있습니다.

조회 작업은 구성 파일 또는 테이블의 내용을 읽고 반환합니다. 쿼리 또는 저장 프로시저를 실행한 결과도 반환합니다. 출력은 싱글톤 값 또는 특성의 배열일 수 있으며 해당 출력은 후속 복사, 변환 또는 ForEach 작업과 같은 제어 흐름 작업에서 사용될 수 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

다음 사항에 유의하세요.

- 조회 작업은 최대 **5,000개의 행** 을 반환할 수 있습니다. 결과 집합에 더 많은 레코드가 있으면 처음 5000행이 반환됩니다.
- 조회 작업 출력은 최대 **4MB** 의 크기를 지원하며 크기가 제한을 초과하면 작업은 실패합니다. 
- 시간 제한 전까지 가능한 최대 조회 작업 기간은 **24시간** 입니다.
- 쿼리 또는 저장 프로시저를 사용하여 데이터를 조회하는 경우 정확히 하나의 결과 집합을 반환하는지 확인해야 합니다. 그렇지 않으면 조회 작업이 실패합니다.

다음은 조회 작업에 지원되는 데이터 원본입니다. 

[!INCLUDE [data-factory-v2-supported-data-stores](includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>구문

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>형식 속성

Name | 설명 | Type | 필수 여부
---- | ----------- | ---- | --------
데이터 세트 | 조회를 위한 데이터 세트 참조를 제공합니다. 자세한 내용은 해당하는 각 커넥터 문서의 **데이터 세트 속성** 섹션에서 확인하세요. | 키/값 쌍 | 예
source | 복사 작업 원본과 동일한 데이터 세트 관련 원본 속성을 포함하고 있습니다. 자세한 내용은 해당하는 각 커넥터 문서의 **복사 작업 속성** 섹션에서 확인하세요. | 키/값 쌍 | 예
firstRowOnly | 첫 번째 행만 반환할 것인지 아니면 모든 행을 반환할 것인지 여부를 나타냅니다. | 부울 | 아니요. 기본값은 `true`입니다.

> [!NOTE]
> 
> * **ByteArray** 형식의 원본 열은 지원되지 않습니다.
> * **구조체** 는 데이터 세트 정의에서 지원되지 않습니다. 텍스트 서식 파일의 경우 헤더 행을 사용하여 열 이름을 입력합니다.
> * 조회 원본이 JSON 파일인 경우 JSON 개체의 모양을 변경하는 `jsonPathDefinition` 설정이 지원되지 않습니다. 전체 개체가 검색됩니다.

## <a name="use-the-lookup-activity-result"></a>조회 작업 결과 사용

조회 결과는 작업 실행 결과의 `output` 섹션에 반환됩니다.

* **`firstRowOnly`가 `true`(기본값)로 설정되면** 출력 형식은 다음 코드와 같습니다. 조회 결과는 고정된 `firstRow` 키 아래에 있습니다. 결과를 후속 작업에 사용하려면 `@{activity('LookupActivity').output.firstRow.table}` 패턴을 사용합니다.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **`firstRowOnly`가 `false`로 설정되면** 출력 형식은 다음 코드와 같습니다. `count` 필드는 반환되는 레코드 수를 나타냅니다. 자세한 값은 고정 `value` 배열 아래에 표시됩니다. 이 경우 조회 작업 후에 [Foreach 작업](control-flow-for-each-activity.md)이 이어집니다. `@activity('MyLookupActivity').output.value` 패턴을 사용하여 `value` 배열을 ForEach 작업 `items` 필드에 전달합니다. `value` 배열의 요소에 액세스하려면 `@{activity('lookupActivity').output.value[zero based index].propertyname}` 구문을 따릅니다. 예제는 `@{activity('lookupActivity').output.value[0].schema}`입니다.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>예제

이 예제에서 파이프라인은 **조회** 및 **복사** 작업을 포함하고 있습니다. 복사 작업은 Azure SQL Database 인스턴스의 SQL 테이블에서 Azure Blob 스토리지로 데이터를 복사합니다. SQL 테이블의 이름은 Blob Storage의 JSON 파일에 저장됩니다. 조회 작업은 런타임 시 테이블 이름을 조회합니다. JSON은 이 방법을 사용하여 동적으로 수정됩니다. 따라서 사용자가 파이프라인 또는 데이터 세트를 다시 배포할 필요가 없습니다. 

이 예제는 첫 번째 행만 조회합니다. 모든 행을 조회하고 결과를 ForEach 작업과 연결하려면 [여러 테이블 대량 복사](tutorial-bulk-copy.md)의 샘플을 참조하세요.


### <a name="pipeline"></a>파이프라인

- 조회 작업은 Azure Blob Storage의 위치를 참조하는 **LookupDataset** 를 사용하도록 구성됩니다. 조회 작업은 이 위치에 있는 JSON 파일에서 SQL 테이블의 이름을 읽습니다. 
- 복사 작업은 SQL 테이블의 이름인 조회 작업의 출력을 사용합니다. **SourceDataset** 의 **tableName** 속성은 조회 작업의 출력을 사용하도록 구성됩니다. 복사 작업은 SQL 테이블의 데이터를 Azure Blob Storage의 위치로 복사합니다. 위치는 **SinkDataset** 속성을 통해 지정합니다. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
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
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>조회 데이터 세트

**lookup** 데이터 세트는 **AzureBlobStorageLinkedService** 형식으로 지정된 Azure Storage 조회 폴더의 **sourcetable.json** 파일입니다. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>복사 작업의 **원본** 데이터 세트

**원본** 데이터 세트는 SQL 테이블의 이름인 조회 작업의 출력을 사용합니다. 복사 작업은 이 SQL 테이블의 데이터를 Azure Blob Storage의 위치로 복사합니다. 위치는 **sink** 속성을 통해 지정합니다. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>복사 작업의 **Sink** 데이터 세트

복사 작업은 SQL 테이블의 데이터를 Azure Storage에 있는 **csv** 폴더의 **filebylookup.csv** 파일에 복사합니다. 이 파일은 **AzureBlobStorageLinkedService** 속성을 통해 지정합니다. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

**sourcetable.json** 파일에 대해 다음 두 종류의 형식을 사용할 수 있습니다.

#### <a name="set-of-objects"></a>개체의 집합

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>개체의 배열

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

조회 활동 및 제안된 해결 방법의 몇 가지 제한 사항은 다음과 같습니다.

| 제한 사항 | 해결 방법 |
|---|---|
| 조회 작업에는 최대 5,000개의 행 및 최대 4MB의 크기가 있습니다. | 최대 행 수 또는 최대 크기를 초과하지 않는 데이터를 검색하는 내부 파이프라인을 통해 외부 파이프라인을 반복하는 두 수준의 파이프라인을 설계합니다. |
| | |

## <a name="next-steps"></a>다음 단계
Azure Data Factory 및 Synapse 파이프라인에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [웹 작업](control-flow-web-activity.md)
