---
title: Amazon RDS for Oracle에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Data Factory 또는 Azure Synapse Analytics 파이프라인을 사용 하 여 Amazon RDS for Oracle에서 지원 되는 싱크 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: jianleishen
ms.openlocfilehash: c6f14b2bb5129907aa5efac042f23217791d84d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634148"
---
# <a name="copy-data-from-amazon-rds-for-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용 하 여 Amazon RDS for Oracle에서 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Amazon RDS for Oracle 데이터베이스에서 데이터를 복사 하는 방법을 설명 합니다. [복사 작업 개요](copy-activity-overview.md)를 기반으로 빌드됩니다.

## <a name="supported-capabilities"></a>지원되는 기능

Amazon RDS for Oracle 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Amazon RDS for Oracle 데이터베이스에서 지원 되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조 하세요.

특히이 Amazon RDS for Oracle 커넥터는 다음을 지원 합니다.

- 다음 버전의 Amazon RDS for Oracle 데이터베이스는 다음과 같습니다.
    - Amazon RDS for Oracle 19c R1 (19.1) 이상
    - Amazon RDS for Oracle 18c R1 (18.1) 이상
    - Amazon RDS for Oracle 12c R1 (12.1) 이상
    - Oracle 11g R1 (11.1) 이상 Amazon RDS
- Amazon RDS for Oracle 원본에서의 병렬 복사 자세한 내용은 [AMAZON RDS For Oracle에서 병렬 복사](#parallel-copy-from-amazon-rds-for-oracle) 섹션을 참조 하세요.

> [!Note]
> Amazon RDS for Oracle 프록시 서버는 지원 되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

Integration runtime은 기본 제공 Amazon RDS for Oracle 드라이버를 제공 합니다. 따라서 Amazon RDS for Oracle에서 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-rds-for-oracle-using-ui"></a>UI를 사용 하 여 Amazon RDS for Oracle에 연결 된 서비스 만들기

다음 단계를 사용 하 여 Azure Portal UI에서 Amazon RDS for Oracle에 연결 된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택한 후 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새로운 연결된 서비스를 만드는 스크린샷":::

1. Amazon RDS for Oracle을 검색 하 고 Amazon RDS for Oracle 커넥터를 선택 합니다.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/amazon-rds-for-oracle-connector.png" alt-text="Amazon RDS for Oracle 커넥터의 스크린샷":::    

1. 서비스 세부 정보를 구성하고, 연결을 테스트하고, 새로운 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/configure-amazon-rds-for-oracle-linked-service.png" alt-text="Amazon RDS for Oracle에 대 한 연결 된 서비스 구성의 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 Amazon RDS for Oracle 커넥터에 한정 된 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Amazon RDS for Oracle 연결 된 서비스는 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 **AmazonRdsForOracle** 로 설정 해야 합니다. | 예 |
| connectionString | Amazon RDS for Oracle Database 인스턴스에 연결 하는 데 필요한 정보를 지정 합니다. <br/>Azure Key Vault에 암호를 입력하고 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요. <br><br>**지원 되는 연결 형식**: **Amazon RDS for Oracle SID** 또는 **Amazon rds for oracle Service Name** 을 사용 하 여 데이터베이스를 식별할 수 있습니다.<br>- SID를 사용하는 경우: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 서비스 이름을 사용하는 경우: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>고급 Amazon RDS for Oracle 기본 연결 옵션의 경우 Tnsnames에 항목을 추가 하도록 선택할 수 있습니다 [. ORA](http://www.orafaq.com/wiki/Tnsnames.ora) 파일을 사용 하는 경우에는 amazon rds for oracle 연결 된 서비스의 경우 amazon rds for Oracle Service name 연결 형식을 사용 하 여 해당 서비스 이름을 구성 하도록 선택 합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |예 |

장애 조치 (failover) 시나리오에 대 한 Amazon RDS for Oracle 인스턴스가 여러 개 있는 경우, 기본 호스트, 포트, 사용자 이름, 암호 등을 사용 하 여 기본 호스트, 포트, 사용자 이름, 암호 등을 입력 하 고 속성 이름을로 사용 하 여 새 "**추가 연결 속성**"을 추가 하 고, `AlternateServers` 괄호를 `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` 놓치지 않고 콜론 ( `:` )을 구분 기호로 사용 합니다. 예를 들어 대체 서버 값 `(HostName=AccountingAmazonRdsForOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`는 연결 장애 조치(failover)를 위해 두 개의 대체 데이터베이스 서버를 정의합니다.

경우에 따라 연결 문자열에서 설정할 수 있는 추가 연결 속성이 있습니다.

| 속성 | Description | 허용되는 값 |
|:--- |:--- |:--- |
| ArraySize |단일 네트워크 왕복에서 커넥터가 페치할 수 있는 바이트 수입니다. 예: `ArraySize=‭10485760‬`.<br/><br/>값이 클수록 네트워크를 통해 데이터를 페치하는 횟수가 줄어들어 처리량이 증가합니다. 값이 작을수록 서버에서 데이터 전송을 기다리는 지연 시간이 줄어들기 때문에 응답 시간이 증가합니다. | 1에서 4294967296(4GB)사이의 정수입니다. 기본값은 `60000`여야 합니다. 값 1은 바이트 수를 정의하지 않지만 정확히 하나의 데이터 행에 대한 공간 할당을 나타냅니다. |

Amazon RDS for Oracle 연결에서 암호화를 사용 하도록 설정 하려면 다음 두 가지 옵션을 사용할 수 있습니다.

-   **3des (TRIPLE DES) 및 AES(Advanced Encryption Standard) (AES)** 를 사용 하려면 Amazon RDS for oracle 서버 쪽에서 Oracle Advanced SECURITY (OAS)로 이동 하 여 암호화 설정을 구성 합니다. 자세한 내용은 이 [Oracle 설명서](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)를 참조하세요. Amazon RDS for Oracle ADF (응용 프로그램 개발 프레임 워크) 커넥터는 Amazon RDS for Oracle에 대 한 연결을 설정할 때 자동으로 암호화 방법을 사용 하 여 OAS에서 구성한 암호화 방법을 자동으로 협상 합니다.

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
    4.  서비스에서 및 해당 값을 사용 하 여 Amazon RDS for Oracle 연결 문자열을 구성 합니다 `EncryptionMethod=1` `TrustStore` / `TrustStorePassword` . 예들 들어 `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`입니다.

**예:**

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
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
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
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

이 섹션에서는 Amazon RDS for Oracle 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다. 데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요. 

Amazon RDS for Oracle에서 데이터를 복사 하려면 데이터 집합의 type 속성을로 설정 `AmazonRdsForOracleTable` 합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 `AmazonRdsForOracleTable`로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. | 예 |
| 테이블 | 테이블/뷰의 이름입니다. | No |
| tableName | 스키마가 포함된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 예 |

**예:**

```json
{
    "name": "AmazonRdsForOracleDataset",
    "properties":
    {
        "type": "AmazonRdsForOracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

이 섹션에서는 Amazon RDS for Oracle 원본에서 지원하는 속성 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="amazon-rds-for-oracle-as-source"></a>원본으로 Oracle용 Amazon RDS

>[!TIP]
>데이터 분할을 사용하여 Amazon RDS for Oracle에서 데이터를 효율적으로 로드하려면 [Oracle용 Amazon RDS의 병렬 복사에서](#parallel-copy-from-amazon-rds-for-oracle)자세히 알아보세요.

Oracle용 Amazon RDS에서 데이터를 복사하려면 복사 활동의 원본 형식을 로 `AmazonRdsForOracleSource` 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 `AmazonRdsForOracleSource`로 설정해야 합니다. | 예 |
| oracleReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `"SELECT * FROM MyTable"`입니다.<br>분할된 로드를 사용하도록 설정하는 경우 쿼리에 해당하는 기본 제공 파티션 매개 변수를 후크해야 합니다. 예제는 [Amazon RDS for Oracle의 병렬 복사본 섹션을](#parallel-copy-from-amazon-rds-for-oracle) 참조하세요. | 예 |
| partitionOptions | Amazon RDS for Oracle에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용되는 값은 **None**(기본값), **PhysicalPartitionsOfTable** 및 **DynamicRange** 입니다.<br>파티션 옵션을 사용하는 경우(즉, 이 아닌 `None` 경우) Oracle 데이터베이스용 Amazon RDS에서 데이터를 동시에 로드하는 병렬의 정도는 복사 작업의 설정에 의해 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 제어됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 `None`이 아닌 경우에 적용됩니다. | 예 |
| partitionNames | 복사해야 하는 물리적 파티션 목록입니다. <br>파티션 옵션이 `PhysicalPartitionsOfTable`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfTabularPartitionName`를 후크합니다. 예제를 보려면 [Amazon RDS for Oracle의 병렬 복사본 섹션을](#parallel-copy-from-amazon-rds-for-oracle) 참조하세요. | 예 |
| partitionColumnName | 병렬 복사를 위해 범위 분할에서 사용되는 **정수 형식으로** 원본 열의 이름을 지정합니다. 지정하지 않으면 테이블의 기본 키가 자동으로 검색되어 파티션 열로 사용됩니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionColumnName`를 후크합니다. 예제를 보려면 [Amazon RDS for Oracle의 병렬 복사본 섹션을](#parallel-copy-from-amazon-rds-for-oracle) 참조하세요. | 예 |
| partitionUpperBound | 데이터를 복사할 파티션 열의 최댓값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionUpbound`를 후크합니다. 예제를 보려면 [Amazon RDS for Oracle의 병렬 복사본 섹션을](#parallel-copy-from-amazon-rds-for-oracle) 참조하세요. | 예 |
| partitionLowerBound | 데이터를 복사할 파티션 열의 최솟값입니다. <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절에서 `?AdfRangePartitionLowbound`를 후크합니다. 예제를 보려면 [Amazon RDS for Oracle의 병렬 복사본 섹션을](#parallel-copy-from-amazon-rds-for-oracle) 참조하세요. | 예 |

**예: 파티션이 없는 기본 쿼리를 사용하여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for Oracle input dataset name>",
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
                "type": "AmazonRdsForOracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-amazon-rds-for-oracle"></a>Oracle용 Amazon RDS의 병렬 복사

Amazon RDS for Oracle 커넥터는 Amazon RDS for Oracle에서 병렬로 데이터를 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

:::image type="content" source="./media/connector-amazon-rds-for-oracle/connector-amazon-rds-for-oracle-partition-options.png" alt-text="파티션 옵션의 스크린샷.":::

분할된 복사를 사용하도록 설정하면 서비스는 Amazon RDS for Oracle 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어 를 `parallelCopies` 4로 설정하면 서비스는 지정된 파티션 옵션 및 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며, 각 쿼리는 Amazon RDS for Oracle 데이터베이스에서 데이터의 일부를 검색합니다.

특히 Amazon RDS for Oracle 데이터베이스에서 대량의 데이터를 로드할 때 데이터 분할을 통해 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소로 데이터를 복사하는 경우 폴더에 여러 파일(폴더 이름만 지정)로 쓰는 것이 좋습니다. 이 경우에는 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션을 사용하여 초대형 테이블에서 전체 로드          | **파티션 옵션**: 테이블의 물리적 파티션. <br><br/>실행하는 동안 서비스에서 실제 파티션을 자동으로 검색하여 데이터를 파티션별로 복사합니다. |
| 데이터 분할을 위해 물리적 파티션을 사용하지 않지만 정수 열을 사용하여 대형 테이블에서 전체 로드합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 지정하지 않으면 기본 키 열이 사용됩니다. |
| 사용자 지정 쿼리를 사용하여 물리적 파티션과 함께 대량의 데이터를 로드합니다. | **파티션 옵션**: 테이블의 물리적 파티션.<br>**쿼리**:`SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**파티션 이름**: 데이터를 복사할 파티션 이름을 지정합니다. 지정하지 않으면 서비스는 Amazon RDS for Oracle 데이터 세트에서 지정한 테이블의 실제 파티션을 자동으로 검색합니다.<br><br>실행 중에 서비스는 `?AdfTabularPartitionName` 를 실제 파티션 이름으로 바꾸고 Oracle용 Amazon RDS로 보냅니다. |
| 물리적 파티션이 없는 사용자 지정 쿼리를 사용하여 대량의 데이터를 로드하는 동시에 데이터 분할을 위한 정수 열을 사용합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한**: 파티션 열에 대해 필터링하려는 하한과 상한 범위 사이에서만 데이터를 검색하도록 지정합니다.<br><br>실행 중에 서비스는 , 및 를 `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` 각 `?AdfRangePartitionLowbound` 파티션에 대한 실제 열 이름 및 값 범위로 바꾸고 Oracle용 Amazon RDS로 보냅니다. <br>예를 들어 파티션 열 “ID”의 하한이 1로 설정되고 상한이 80으로 설정된 경우 병렬 복사를 4로 설정하면 서비스는 4개의 파티션으로 데이터를 검색합니다. 해당 ID는 [1, 20], [21, 40], [41, 60] 및 [61, 80] 사이에 각각 있습니다. |

> [!TIP]
> 분할되지 않은 테이블에서 데이터를 복사하는 경우 "동적 범위" 파티션 옵션을 사용하여 정수 열에 대해 분할할 수 있습니다. 원본 데이터에 이러한 유형의 열이 없는 경우 원본 쿼리에서 [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) 함수를 활용하여 열을 생성하고 이를 파티션 열로 사용할 수 있습니다.

**예: 물리적 파티션이 있는 쿼리**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
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
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
