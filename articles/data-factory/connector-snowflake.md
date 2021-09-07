---
title: Snowflake에서 데이터 복사 및 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Data Factory 또는 Azure Synapse Analytics를 사용하여 Snowflake에서 데이터를 복사하고 변환하는 방법을 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 03/16/2021
ms.openlocfilehash: f17c8c4144dc1536804f5b7b0f6a237bc0aede30
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642899"
---
# <a name="copy-and-transform-data-in-snowflake-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 Snowflake에서 데이터 복사 및 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse 파이프라인의 복사 작업을 사용하여 Snowflake 간에 데이터를 복사하고 Data Flow를 사용하여 Snowflake에서 데이터를 변환하는 방법을 설명합니다. 자세한 내용은 [Data Factory](introduction.md) 또는 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)의 소개 문서를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Snowflake 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md) 테이블을 사용하는 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

복사 작업의 경우 이 Snowflake 커넥터는 다음과 같은 기능을 지원합니다.

- [COPY into [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 명령을 활용하여 최상의 성능을 얻는 Snowflake의 데이터 복사.
- [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 명령을 활용하여 최상의 성능을 얻는 Snowflake의 데이터 복사. Azure에서 Snowflake를 지원합니다.
- 자체 호스팅 Integration Runtime에서 Snowflake에 연결하는 데 프록시가 필요한 경우 Integration Runtime 호스트에서 HTTP_PROXY 및 HTTPS_PROXY에 대한 환경 변수를 구성해야 합니다. 

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Snowflake 커넥터에 국한된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Snowflake 연결된 서비스에 다음 속성이 지원됩니다.

| 속성         | 설명                                                  | 필수 |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | type 속성은 **Snowflake** 로 설정해야 합니다.              | 예      |
| connectionString | Snowflake 인스턴스에 연결하는 데 필요한 정보를 지정합니다. Azure Key Vault에 암호나 전체 연결 문자열을 배치하도록 선택할 수 있습니다. 자세한 내용은 테이블 아래의 예제와 함께 [Azure Key Vault의 저장소 자격 증명](store-credentials-in-key-vault.md) 문서를 참조하세요.<br><br>일반적인 설정은 다음과 같습니다.<br>- **계정 이름:** Snowflake 계정(지역 및 클라우드 플랫폼을 식별하는 추가 세그먼트 포함)의 [전체 계정 이름](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name)(예: xy12345.east-us-2.azure)입니다.<br/>- **사용자 이름:** 연결에 사용할 사용자의 로그인 이름입니다.<br>- **암호:** 사용자의 암호입니다.<br>- **데이터베이스:** 연결 시 사용할 기본 데이터베이스입니다. 지정된 역할에 권한이 있는 기존 데이터베이스여야 합니다.<br>- **웨어하우스:** 연결 시 사용할 가상 웨어하우스입니다. 지정된 역할에 권한이 있는 기존 웨어하우스여야 합니다.<br>- **역할:** Snowflake 세션에서 사용할 기본 액세스 제어 역할입니다. 지정된 역할은 지정된 사용자에게 이미 할당된 기존 역할이어야 합니다. 기본 역할은 PUBLIC입니다. | 예      |
| connectVia       | 데이터 저장소에 연결하는 데 사용되는 [통합 런타임](concepts-integration-runtime.md)입니다. Azure 통합 런타임 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임을 사용합니다. | 예       |

**예:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault의 암호:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

Snowflake 데이터 세트에 대해 다음 속성이 지원됩니다.

| 속성  | 설명                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 세트의 type 속성을 **SnowflakeTable** 로 설정해야 합니다. | 예                         |
| 스키마 | 스키마의 이름입니다. 스키마 이름은 대/소문자를 구분합니다. |원본의 경우 아니요이며, 싱크의 경우 예입니다.  |
| 테이블 | 테이블/뷰의 이름입니다. 테이블 이름은 대/소문자를 구분합니다. |원본의 경우 아니요이며, 싱크의 경우 예입니다.  |

**예:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Snowflake 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="snowflake-as-the-source"></a>원본으로서의 Snowflake

Snowflake의 [COPY into [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 명령을 활용하여 최상의 성능을 얻는 Snowflake 커넥터.

싱크 데이터 저장소 및 형식이 Snowflake COPY 명령에 의해 기본적으로 지원되는 경우 복사 작업을 사용하여 Snowflake에서 싱크로 직접 복사할 수 있습니다. 자세한 내용은 [Snowflake에서 직접 복사](#direct-copy-from-snowflake)를 참조하세요. 그렇지 않으면 기본 제공되는 [Snowflake에서 준비된 복사](#staged-copy-from-snowflake)를 사용합니다.

Snowflake에서 데이터를 복사하기 위해 복사 작업 **원본** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성                     | 설명                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 작업 원본의 type 속성을 **SnowflakeSource** 로 설정해야 합니다. | 예      |
| Query          | Snowflake에서 데이터를 읽을 SQL 쿼리를 지정합니다. 스키마 이름, 테이블 및 열에 소문자가 포함된 경우 쿼리의 개체 식별자(예: `select * from "schema"."myTable"`)를 인용합니다.<br>저장 프로시저 실행은 지원되지 않습니다. | 아니요       |
| exportSettings | Snowflake에서 데이터를 검색하는 데 사용되는 고급 설정입니다. 명령문이 호출될 때 서비스에서 전달하는 COPY into 명령에서 지원되는 항목을 구성할 수 있습니다. | 아니요       |
| ***`exportSettings` 아래에서:*** |  |  |
| 형식 | **SnowflakeExportCopyCommand** 로 설정된 내보내기 명령의 유형입니다. | 예 |
| additionalCopyOptions | 키-값 쌍의 사전으로 제공되는 추가 복사 옵션입니다. 예: MAX_FILE_SIZE, 덮어쓰기. 자세한 내용은 [Snowflake 복사 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)을 참조하세요. | 아니요 |
| additionalFormatOptions | 키-값 쌍의 사전으로 COPY 명령에 제공되는 추가 파일 형식 옵션입니다. 예: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. 자세한 내용은 [Snowflake 형식 유형 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)을 참조하세요. | 아니요 |

#### <a name="direct-copy-from-snowflake"></a>Snowflake에서 직접 복사

싱크 데이터 저장소와 형식이 이 섹션에 설명된 조건을 충족하는 경우 복사 작업을 사용하여 Snowflake에서 싱크로 직접 복사할 수 있습니다. 이 서비스는 설정을 확인하고 다음 조건이 충족되지 않으면 복사 작업 실행에 실패합니다.

- **싱크 연결된 서비스** 는 **공유 액세스 서명** 인증을 사용하는 [**Azure Blob Storage**](connector-azure-blob-storage.md)입니다. 다음의 지원되는 형식으로 Azure Data Lake Storage Gen2에 직접 데이터를 복사하려면 [Snowflake에서 준비된 복사](#staged-copy-from-snowflake)를 사용하지 않도록 ADLS Gen2 계정에 대해 SAS 인증을 사용하여 Azure Blob 연결된 서비스를 만들 수 있습니다.

- **싱크 데이터 형식** 은 다음 구성과 함께 **Parquet**, **구분된 텍스트** 또는 **JSON** 입니다.

    - **Parquet** 형식의 경우 압축 코덱은 **None**, **Snappy** 또는 **Lzo** 입니다.
    - **구분된 텍스트** 형식:
        - `rowDelimiter`는 **\r\n** 또는 모든 단일 문자입니다.
        - `compression`은 **압축 안 함**, **gzip**, **bzip2** 또는 **deflate** 일 수 있습니다.
        - `encodingName`는 기본값으로 남아 있거나 **utf-8** 로 설정됩니다.
        - `quoteChar`은 **큰따옴표**, **작은따옴표** 또는 **빈 문자열**(따옴표 문자 없음)입니다.
    - **JSON** 형식의 경우 직접 복사는 원본 Snowflake 테이블 또는 쿼리 결과에 단일 열만 있고 이 열의 데이터 유형이 **VARIANT**, **OBJECT** 또는 **ARRAY** 인 경우에만 지원합니다.
        - `compression`은 **압축 안 함**, **gzip**, **bzip2** 또는 **deflate** 일 수 있습니다.
        - `encodingName`는 기본값으로 남아 있거나 **utf-8** 로 설정됩니다.
        - 복사 작업 싱크의 `filePattern`은 기본값으로 남아 있거나, **setOfObjects** 로 설정됩니다.
- 복사 작업 원본에서 `additionalColumns`는 지정되지 않았습니다.
- 열 매핑은 지정되지 않았습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Snowflake에서 준비된 복사

싱크 데이터 저장소 또는 형식이 Snowflake COPY 명령과 기본적으로 호환되지 않는 경우 마지막 섹션에 설명된 대로 중간 Azure Blob Storage 인스턴스를 사용하여 기본 제공된 준비된 복사를 사용하도록 설정합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 이 서비스는 Snowflake에서 데이터를 스테이징 스토리지로 내보낸 다음, 데이터를 싱크에 복사하고 마지막으로 준비 스토리지에서 임시 데이터를 정리합니다. 스테이징을 사용하는 데이터 복사에 관한 자세한 내용은 [스테이징된 복사본](copy-activity-performance-features.md#staged-copy)을 참조하세요.

이 기능을 사용하려면 중간 스테이징으로 Azure Storage 계정을 참조하는 [Azure Blob Storage 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties)를 만듭니다. 그런 다음 복사 작업에서 `enableStaging`과 `stagingSettings` 속성을 지정합니다.

> [!NOTE]
> 스테이징 Azure Blob Storage 연결된 서비스는 Snowflake COPY 명령에 필요한 공유 액세스 서명 인증을 사용해야 합니다. 

**예:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>Snowflake를 싱크로

Snowflake의 [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 명령을 활용하여 최상의 성능을 얻는 Snowflake 커넥터. Azure의 Snowflake에 데이터를 쓰는 것을 지원합니다.

원본 데이터 저장소 및 형식이 Snowflake COPY 명령에 의해 기본적으로 지원되는 경우 복사 작업을 사용하여 원본에서 Snowflake로 직접 복사할 수 있습니다. 자세한 내용은 [Snowflake로 직접 복사](#direct-copy-to-snowflake)를 참조하세요. 그렇지 않은 경우 기본 제공되는 [Snowflake로 준비된 복사](#staged-copy-to-snowflake)를 사용합니다.

Snowflake로 데이터를 복사하기 위해 복사 작업 **싱크** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성          | 설명                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 복사 작업 싱크의 type 속성은 **SnowflakeSink** 로 설정해야 합니다. | 예                                           |
| preCopyScript     | 각 실행 시 Snowflake에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 예                                            |
| importSettings | Snowflake에 데이터를 쓰는 데 사용되는 고급 설정입니다. 명령문이 호출될 때 서비스에서 전달하는 COPY into 명령에서 지원되는 항목을 구성할 수 있습니다. | 아니요 |
| ***`importSettings` 아래에서:*** |                                                              |  |
| 형식 | **SnowflakeImportCopyCommand** 로 설정된 내보내기 명령의 유형입니다. | 예 |
| additionalCopyOptions | 키-값 쌍의 사전으로 제공되는 추가 복사 옵션입니다. 예: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. 자세한 내용은 [Snowflake 복사 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)을 참조하세요. | 아니요 |
| additionalFormatOptions | 키-값 쌍의 사전으로 COPY 명령에 제공되는 추가 파일 형식 옵션입니다. 예: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. 자세한 내용은 [Snowflake 형식 유형 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)을 참조하세요. | 아니요 |

#### <a name="direct-copy-to-snowflake"></a>Snowflake로 직접 복사

원본 데이터 저장소와 형식이 이 섹션에 설명된 조건을 충족하는 경우 복사 작업을 사용하여 원본에서 Snowflake로 직접 복사할 수 있습니다. 이 서비스는 설정을 확인하고 다음 조건이 충족되지 않으면 복사 작업 실행에 실패합니다.

- **원본 연결된 서비스** 는 **공유 액세스 서명** 인증을 사용하는 [**Azure Blob Storage**](connector-azure-blob-storage.md)입니다. 다음의 지원되는 형식으로 Azure Data Lake Storage Gen2에서 직접 데이터를 복사하려면 [Snowflake로 준비된 복사](#staged-copy-to-snowflake)를 사용하지 않도록 ADLS Gen2 계정에 대해 SAS 인증을 사용하여 Azure Blob 연결된 서비스를 만들 수 있습니다.

- **원본 데이터 형식** 은 다음 구성과 함께 **Parquet**, **구분된 텍스트** 또는 **JSON** 입니다.

    - **Parquet** 형식의 경우 압축 코덱은 **None** 또는 **Snappy** 입니다.

    - **구분된 텍스트** 형식:
        - `rowDelimiter`는 **\r\n** 또는 모든 단일 문자입니다. 행 구분 기호가 “\r\n”이 아닌 경우 `firstRowAsHeader`는 **false** 여야 하고 `skipLineCount`는 지정되지 않습니다.
        - `compression`은 **압축 안 함**, **gzip**, **bzip2** 또는 **deflate** 일 수 있습니다.
        - `encodingName`은 기본값으로 남아 있거나 “UTF-8”, “UTF-16”, “UTF-16BE”, “UTF-32”, “UTF-32BE”, “BIG5”, “EUC-JP”, “EUC-KR”, “GB18030”, “ISO-2022-JP”, “ISO-2022-KR”, “ISO-8859-1”, “ISO-8859-2”, “ISO-8859-5”, “ISO-8859-6”, “ISO-8859-7”, “ISO-8859-8”, “ISO-8859-9”, “WINDOWS-1250”, “WINDOWS-1251”, “WINDOWS-1252”, “WINDOWS-1253”, “WINDOWS-1254”, “WINDOWS-1255”로 설정됩니다.
        - `quoteChar`은 **큰따옴표**, **작은따옴표** 또는 **빈 문자열**(따옴표 문자 없음)입니다.
    - **JSON** 형식의 경우 직접 복사는 싱크 Snowflake 테이블에 단일 열만 있고 이 열의 데이터 유형이 **VARIANT**, **OBJECT** 또는 **ARRAY** 인 경우에만 지원합니다.
        - `compression`은 **압축 안 함**, **gzip**, **bzip2** 또는 **deflate** 일 수 있습니다.
        - `encodingName`는 기본값으로 남아 있거나 **utf-8** 로 설정됩니다.
        - 열 매핑은 지정되지 않았습니다.

- 복사 작업 원본에서는 다음이 해당됩니다. 

   -  `additionalColumns`을(를) 지정하지 않았습니다.
   - 원본이 폴더인 경우 `recursive`는 true로 설정됩니다.
   - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` 및 `enablePartitionDiscovery`는 지정되지 않습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Snowflake로 준비된 복사

원본 데이터 저장소 또는 형식이 Snowflake COPY 명령과 기본적으로 호환되지 않는 경우 마지막 섹션에 설명된 대로 중간 Azure Blob Storage 인스턴스를 사용하여 기본 제공된 준비된 복사를 사용하도록 설정합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 이 서비스는 Snowflake의 데이터 형식 요구 사항을 충족하도록 데이터를 자동으로 변환합니다. 그런 다음, COPY 명령을 호출하여 데이터를 Snowflake로 로드합니다. 마지막으로, Blob Storage에서 임시 데이터를 정리합니다. 스테이징을 사용하는 데이터 복사에 관한 자세한 내용은 [스테이징된 복사본](copy-activity-performance-features.md#staged-copy)을 참조하세요.

이 기능을 사용하려면 중간 스테이징으로 Azure Storage 계정을 참조하는 [Azure Blob Storage 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties)를 만듭니다. 그런 다음 복사 작업에서 `enableStaging`과 `stagingSettings` 속성을 지정합니다.

> [!NOTE]
> 스테이징 Azure Blob Storage 연결된 서비스는 Snowflake COPY 명령에 필요한 공유 액세스 서명 인증을 사용해야 합니다.

**예:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환하는 경우 Snowflake에서 테이블에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요. Snowflake 데이터 세트 또는 [인라인 데이터 세트](data-flow-source.md#inline-datasets)를 원본 및 싱크 유형으로 사용하도록 선택할 수 있습니다.

### <a name="source-transformation"></a>원본 변환

다음 표에서는 Snowflake 원본에서 지원하는 속성을 나열합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다. 커넥터는 Snowflake [내부 데이터 전송](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)을 활용합니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 테이블 | 테이블을 입력으로 선택하는 경우 데이터 흐름은 인라인 데이터 세트를 사용할 때 Snowflake 데이터 세트 또는 원본 옵션에 지정된 테이블에서 모든 데이터를 페치합니다. | 예 | String | *(인라인 데이터 세트에만 해당)*<br>tableName<br>schemaName |
| 쿼리 | 쿼리를 입력으로 선택하는 경우 Snowflake에서 데이터를 페치하는 쿼리를 입력합니다. 이렇게 설정하면 데이터 세트에서 선택한 모든 테이블이 재정의됩니다.<br>스키마 이름, 테이블 및 열에 소문자가 포함된 경우 쿼리의 개체 식별자(예: `select * from "schema"."myTable"`)를 인용합니다. | 예 | String | Query |

#### <a name="snowflake-source-script-examples"></a>Snowflake 원본 스크립트 예제

Snowflake 데이터 세트를 원본 유형으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

인라인 데이터 세트를 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>싱크 변환

다음 표에서는 Snowflake 싱크에서 지원하는 속성을 나열합니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다. 인라인 데이터 세트를 사용하는 경우 [데이터 세트 속성](#dataset-properties) 섹션에 설명된 속성과 동일한 추가 설정이 표시됩니다. 커넥터는 Snowflake [내부 데이터 전송](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)을 활용합니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 메서드 | Snowflake 대상에서 허용되는 작업을 지정합니다.<br>행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 [행 변경 변환](data-flow-alter-row.md)이 필요합니다. | 예 | `true` 또는 `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| 키 열 | 업데이트, upsert 및 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다. | 아니요 | Array | 키 |
| 테이블 작업 | 쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지 여부를 결정합니다.<br>- **None**: 테이블에 대한 작업이 수행되지 않습니다.<br>- **Recreate**: 테이블이 삭제되고 다시 생성됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.<br>- **Truncate**: 대상 테이블의 모든 행이 제거됩니다. | 아니요 | `true` 또는 `false` | recreate<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>Snowflake 싱크 스크립트 예제

Snowflake 데이터 세트를 싱크 유형으로 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

인라인 데이터 세트를 사용하는 경우 연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 관한 자세한 내용은 [조회 작업](control-flow-lookup-activity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
