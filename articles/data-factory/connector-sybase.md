---
title: Azure Data Factory를 사용하여 Sybase에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Sybase에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jianleishen
ms.openlocfilehash: 2f3c672e136152fbd5abec20a81bd04dc5fb2e2a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642886"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Sybase에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-onprem-sybase-connector.md)
> * [현재 버전](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Sybase 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Sybase 커넥터는 다음과 같은 작업을 지원합니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Sybase 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Sybase 커넥터는 다음을 지원합니다.

- SAP Sybase SQL Anywhere(ASA) **버전 16 이상**
- **Basic** 또는 **Windows** 인증을 사용한 데이터 복사

Sybase IQ와 ASE는 지원하지 않습니다. 대신 일반 ODBC 커넥터를 Sybase 드라이버와 함께 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 Sybase 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- Integration Runtime 컴퓨터에 [Sybase iAnywhere.Data.SQLAnywhere 데이터 공급자](https://go.microsoft.com/fwlink/?linkid=324846) 16 이상을 설치합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Sybase 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Sybase 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Sybase** 로 설정되어야 합니다. | 예 |
| 서버 | Sybase 서버의 이름입니다. |예 |
| 데이터베이스 | Sybase 데이터베이스의 이름입니다. |예 |
| authenticationType | Sybase 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 **Basic** 및 **Windows** 입니다. |예 |
| 사용자 이름 | Sybase 데이터베이스에 연결할 사용자 이름을 지정합니다. |예 |
| password | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 Sybase 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Sybase에서 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형식 속성을 **SybaseTable** 로 설정해야 합니다. | 예 |
| tableName | Sybase 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 형식의 데이터 세트를 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에는 Sybase 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sybase-as-source"></a>Sybase를 원본으로

Sybase에서 데이터를 복사하기 위해 복사 작업 **원본** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성은 **SybaseSource** 으로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 형식의 원본을 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="data-type-mapping-for-sybase"></a>Sybase에 대한 데이터 형식 매핑

Sybase에서 데이터를 복사할 경우 Sybase 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

Sybase는 T-SQL 형식을 지원합니다. SQL 형식의 매핑 테이블을 Azure Data Factory 중간 데이터 형식에 매핑하려면 [Azure SQL Database 커넥터 - 데이터 형식 매핑](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) 섹션을 참조하세요.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.



## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
