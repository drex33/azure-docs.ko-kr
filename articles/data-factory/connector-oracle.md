---
title: Oracle 간 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Data Factory 또는 Azure Synapse Analytics 파이프라인을 사용함으로써, 지원되는 원본 저장소에서 Oracle 데이터베이스로 데이터를 복사하거나 Oracle에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: bf21a264e64fd43ba98f73f96afc6fe2f1bfd069
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123317453"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 Oracle 간에 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-onprem-oracle-connector.md)
> * [현재 버전](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Oracle 데이터베이스 간에 데이터를 복사하는 방법을 간략하게 설명합니다. [복사 작업 개요](copy-activity-overview.md)를 기반으로 빌드됩니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Oracle 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Oracle 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Oracle에 복사할 수도 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Oracle 커넥터는 다음을 지원합니다.

- Oracle 데이터베이스의 다음 버전:
    - Oracle 19c R1 (19.1) 이상
    - Oracle 18c R1 (18.1) 이상
    - Oracle 12c R1 (12.1) 이상
    - Oracle 11c R1 (11.1) 이상
    - Oracle 10c R1 (10.1) 이상
    - Oracle 9i R2 (9.2) 이상
    - Oracle 8i R3 (8.1.7) 이상
    - Oracle Database Cloud Exadata Service
- Oracle 원본에서 병렬 복사. 자세한 내용은 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조하세요.

> [!Note]
> Oracle 프록시 서버는 지원되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

통합 런타임은 기본 제공 Oracle 드라이버를 제공합니다. 따라서 Oracle 데이터 복사 작업에는 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-oracle-using-ui"></a>UI를 사용하여 Oracle에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 Oracle에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택한 후 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

2. Oracle을 검색하고 Oracle 커넥터를 선택합니다.

    :::image type="content" source="media/connector-oracle/oracle-connector.png" alt-text="Oracle 커넥터 스크린샷":::    

1. 서비스 세부 정보를 구성하고, 연결을 테스트하고, 새로운 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-oracle/configure-oracle-linked-service.png" alt-text="Oracle의 연결된 서비스 구성 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Oracle 커넥터에 한정된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Oracle 연결된 서비스는 다음 속성을 지원합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Oracle** 로 설정해야 합니다. | 예 |
| connectionString | Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. <br/>Azure Key Vault에 암호를 입력하고 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. <br><br>**지원되는 연결 유형**: 데이터베이스를 식별하기 위해 **Oracle SID** 또는 **Oracle 서비스 이름** 을 사용할 수 있습니다.<br>- SID를 사용하는 경우: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 서비스 이름을 사용하는 경우: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>고급 Oracle 기본 연결 옵션의 경우 Oracle 서버에 있는 [TNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora) 파일에 항목을 추가하도록 선택할 수 있습니다. Oracle 연결된 서비스에서는 Oracle 서비스 이름 연결 형식을 사용하고 해당하는 서비스 이름을 구성하도록 선택할 수 있습니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |예 |

>[!TIP]
>"ORA-01025: UPI 매개 변수 범위를 벗어남" 오류가 발생하고 Oracle 버전이 8i인 경우, 연결 문자열에 `WireProtocolMode=1`을 추가합니다. 그런 다음, 다시 시도하세요.

장애 조치(failover) 시나리오에 대해 여러 Oracle 인스턴스가 있는 경우, Oracle 연결된 서비스를 만들고 기본 호스트, 포트, 사용자 이름, 암호 등을 입력한 다음 속성 이름을 `AlternateServers`로 하고 값을 `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)`으로 하여 새 "**추가 연결 속성**"을 추가합니다. 이 때 괄호를 누락하지 않고 콜론(`:`)을 구분 기호로 사용하도록 합니다. 예를 들어 대체 서버 값 `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`는 연결 장애 조치(failover)를 위해 두 개의 대체 데이터베이스 서버를 정의합니다.

경우에 따라 연결 문자열에서 설정할 수 있는 추가 연결 속성이 있습니다.

| 속성 | Description | 허용되는 값 |
|:--- |:--- |:--- |
| ArraySize |단일 네트워크 왕복에서 커넥터가 페치할 수 있는 바이트 수입니다. 예: `ArraySize=‭10485760‬`.<br/><br/>값이 클수록 네트워크를 통해 데이터를 페치하는 횟수가 줄어들어 처리량이 증가합니다. 값이 작을수록 서버에서 데이터 전송을 기다리는 지연 시간이 줄어들기 때문에 응답 시간이 증가합니다. | 1에서 4294967296(4GB)사이의 정수입니다. 기본값은 `60000`여야 합니다. 값 1은 바이트 수를 정의하지 않지만 정확히 하나의 데이터 행에 대한 공간 할당을 나타냅니다. |

Oracle 연결에서 암호화를 사용하도록 설정하려면 다음 두 가지 옵션이 있습니다.

-   **3DES(Triple-DES Encryption) 및 AES(Advanced Encryption Standard)** 를 사용하려면 Oracle 서버 쪽에서 OAS(Oracle Advanced Security)로 이동하여 암호화 설정을 구성합니다. 자세한 내용은 이 [Oracle 설명서](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)를 참조하세요. Oracle ADF(Application Development Framework) 커넥터는 Oracle에 대한 연결을 설정할 때 OAS에서 구성한 암호화 메서드를 사용하도록 자동으로 협상합니다.

-   **TLS** 를 사용하려면 다음을 수행합니다.

    1.  TLS/SSL 인증서를 가져옵니다. TLS/SSL 인증서의 DER(Distinguished Encoding Rules)로 인코딩된 인증서 정보를 가져오고 출력(----- Begin Certificate … End Certificate -----)을 텍스트 파일로 저장합니다.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **예:** DERcert.cer에서 인증서 정보를 추출한 다음, 출력을 cert.txt로 저장합니다.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  `keystore` 또는 `truststore`를 빌드합니다. 다음 명령은 PKCS-12 형식의 암호를 사용하거나 사용하지 않는 `truststore` 파일을 만듭니다.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **예:** 암호를 사용하는 MyTrustStoreFile이라는 PKCS12 `truststore` 파일을 만듭니다.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  자체 호스팅 IR 컴퓨터에 `truststore` 파일을 저장합니다. 예를 들어 파일을 C:\MyTrustStoreFile에 저장합니다.
    4.  서비스에서 `EncryptionMethod=1` 및 해당하는 `TrustStore`/`TrustStorePassword` 값을 사용하여 Oracle 연결 문자열을 구성합니다. 예: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**예:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 암호 저장**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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

이 섹션에는 Oracle 데이터 세트에서 지원하는 속성의 목록을 제공합니다. 데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요. 

Oracle 간에 데이터를 복사하려면 데이터 세트의 type 속성을 `OracleTable`로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 `OracleTable`로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

이 섹션에서는 Oracle 원본 및 싱크에서 지원하는 속성 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="oracle-as-source"></a>Oracle을 원본으로

>[!TIP]
>데이터 분할을 사용하여 Oracle에서 데이터를 효율적으로 로드하려면 [Oracle에서 병렬 복사](#parallel-copy-from-oracle)에서 자세히 알아보세요.

Oracle에서 데이터를 복사하려면 복사 작업의 원본 형식을 `OracleSource`로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 `OracleSource`로 설정해야 합니다. | 예 |
| oracleReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `"SELECT * FROM MyTable"`입니다.<br>분할된 로드를 사용하도록 설정하는 경우 쿼리에 해당하는 기본 제공 파티션 매개 변수를 후크해야 합니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조하세요. | 예 |
| partitionOptions | Oracle에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **PhysicalPartitionsOfTable** 및 **DynamicRange** 입니다.<br>파티션 옵션을 사용하도록 설정하는 경우 (즉, `None`이 아님), Oracle 데이터베이스에서 데이터를 동시에 로드하는 병렬 처리 수준이 복사 작업에서 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정으로 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용됩니다. | 예 |
| partitionNames | 복사해야 하는 물리적 파티션 목록입니다. <br>파티션 옵션이 `PhysicalPartitionsOfTable`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfTabularPartitionName`를 후크합니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조하세요. | 예 |
| partitionColumnName | 병렬 복사를 위해 범위 분할에서 사용되는 **정수 형식으로** 원본 열의 이름을 지정합니다. 지정하지 않으면 테이블의 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionColumnName`를 후크합니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조하세요. | 예 |
| partitionUpperBound | 데이터를 복사할 파티션 열의 최댓값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionUpbound`를 후크합니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조하세요. | 예 |
| partitionLowerBound | 데이터를 복사할 파티션 열의 최솟값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionLowbound`를 후크합니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조하세요. | 예 |

**예: 파티션이 없는 기본 쿼리를 사용하여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle을 싱크로

Oracle에 데이터를 복사하려면 복사 작업의 싱크 형식을 `OracleSink`로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 `OracleSink`로 설정해야 합니다. | 예 |
| writeBatchSize | 버퍼 크기가 `writeBatchSize`에 도달하면 SQL 테이블에 데이터를 삽입합니다.<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)입니다. | 예 |
| preCopyScript | 각 실행 시 Oracle에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 예 |
| maxConcurrentConnections |작업을 실행하는 동안 데이터 저장소에 설정된 동시 연결의 상한입니다. 동시 연결을 제한하려는 경우에만 값을 지정합니다.| 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Oracle에서 병렬 복사

Oracle 커넥터는 Oracle에서 병렬로 데이터를 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-oracle/connector-oracle-partition-options.png)

분할된 복사를 사용하도록 설정하면 서비스가 Oracle 원본에 대한 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어 `parallelCopies`를 4로 설정하는 경우, 서비스는 지정된 파티션 옵션과 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며, 각 쿼리는 Oracle 데이터베이스에서 데이터의 일부를 검색합니다.

특히 Oracle 데이터베이스에서 대량의 데이터를 로드하는 경우 특별히 데이터 분할로 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소로 데이터를 복사하는 경우 폴더에 여러 파일(폴더 이름만 지정)로 쓰는 것이 좋습니다. 이 경우에는 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션을 사용하여 초대형 테이블에서 전체 로드          | **파티션 옵션**: 테이블의 물리적 파티션. <br><br/>실행하는 동안 서비스에서 실제 파티션을 자동으로 검색하여 데이터를 파티션별로 복사합니다. |
| 데이터 분할을 위해 물리적 파티션을 사용하지 않지만 정수 열을 사용하여 대형 테이블에서 전체 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 기본 키 열이 사용됩니다. |
| 사용자 지정 쿼리를 사용하여 물리적 파티션과 함께 대량의 데이터를 로드합니다. | **파티션 옵션**: 테이블의 물리적 파티션.<br>**쿼리**:`SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**파티션 이름**: 데이터를 복사할 파티션 이름을 지정합니다. 지정하지 않으면 서비스는 Oracle 데이터 세트에서 지정한 테이블의 물리적 파티션을 자동으로 검색합니다.<br><br>실행하는 동안 서비스는 `?AdfTabularPartitionName`을 실제 파티션 이름으로 바꾸고 Oracle로 보냅니다. |
| 물리적 파티션이 없는 사용자 지정 쿼리를 사용하여 대량의 데이터를 로드하는 동시에 데이터 분할을 위한 정수 열을 사용합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한**: 파티션 열에 대해 필터링하려는 하한과 상한 범위 사이에서만 데이터를 검색하도록 지정합니다.<br><br>실행하는 동안 서비스에서 `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, `?AdfRangePartitionLowbound`를 각 파티션의 실제 열 이름과 값 범위로 바꾸고 Oracle에 보냅니다. <br>예를 들어 파티션 열 “ID”의 하한이 1로 설정되고 상한이 80으로 설정된 경우 병렬 복사를 4로 설정하면 서비스는 4개의 파티션으로 데이터를 검색합니다. 해당 ID는 [1, 20], [21, 40], [41, 60] 및 [61, 80] 사이에 각각 있습니다. |

> [!TIP]
> 분할되지 않은 테이블에서 데이터를 복사하는 경우 "동적 범위" 파티션 옵션을 사용하여 정수 열에 대해 분할할 수 있습니다. 원본 데이터에 이러한 유형의 열이 없는 경우 원본 쿼리에서 [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) 함수를 활용하여 열을 생성하고 이를 파티션 열로 사용할 수 있습니다.

**예: 물리적 파티션이 있는 쿼리**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Oracle에 대한 데이터 형식 매핑

Oracle에서 데이터를 복사할 때 서비스 내에서 다음과 같은 중간 데이터 형식 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Oracle 데이터 형식 | 중간 데이터 형식 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Oracle 10g 이상에서만 지원됨) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| INTEGER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER(p,s) |Decimal, String(전체 자릿수의 경우 > 28) |
| 전체 자릿수 및 소수 자릿수가 없는 NUMBER |Double |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |숫자 |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 데이터 형식 INTERVAL YEAR TO MONTH 및 INTERVAL DAY TO SECOND는 지원되지 않습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
