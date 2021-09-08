---
title: SAP 테이블에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인의 복사 작업을 사용하여 SAP 테이블에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: b5e60883f4af77eabcd3a59334bbf31b63d5cc49
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123316574"
---
# <a name="copy-data-from-an-sap-table-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory 또는 Azure Synapse Analytics를 사용하여 SAP 테이블에서 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 복사 작업을 사용하여 SAP 테이블에서 데이터를 복사하는 방법을 간략하게 설명합니다. 자세한 내용은 [작업 복사 개요](copy-activity-overview.md)를 참조하세요.

>[!TIP]
>SAP 데이터 통합 시나리오에서 전반적인 지원에 대한 자세한 내용은 [Azure Data Factory를 사용한 SAP 데이터 통합 백서](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)에 설명된 각 SAP 커넥터의 자세한 소개, 비교 및 지침을 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP 테이블 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SAP 테이블에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SAP 테이블 커넥터는 다음을 지원합니다.

- 다음의 SAP 테이블에서 데이터 복사:

  - SAP ECC(SAP ERP Central Component) 버전 7.01 이상(2015년 이후에 릴리스된 최근 SAP 지원 패키지 스택).
  - SAP BW(SAP Business Warehouse) 버전 7.01 이상(2015년 이후에 릴리스된 최근 SAP 지원 패키지 스택).
  - SAP S/4HANA.
  - SAP Business Suite 버전 7.01 이상(2015년 이후에 릴리스된 최근 SAP 지원 패키지 스택)의 기타 제품.

- SAP 투명 테이블, 풀링된 테이블, 클러스터된 테이블 및 보기 모두에서 데이터 복사.
- SNC(Secure Network Communications)가 구성된 경우 기본 인증 또는 SNC를 사용하여 데이터 복사.
- SAP 애플리케이션 서버 또는 SAP 메시지 서버에 연결.
- 기본 또는 사용자 지정 RFC를 통해 데이터 검색.

버전 7.01 이상에서는 SAP ECC 버전 대신 SAP NetWeaver 버전을 참조합니다. 예를 들어 SAP ECC 6.0 EHP 7은 일반적으로 NetWeaver 버전 >=7.4입니다. 현재 환경에 대해 잘 모르는 경우 SAP 시스템에서 다음 단계를 따라 버전을 확인합니다.

1. SAP GUI를 사용하여 SAP 시스템에 연결합니다. 
2. **시스템** -> **상태** 로 이동합니다. 
3. SAP_BASIS의 릴리스를 확인하여 701보다 크거나 같은지 확인합니다.  
      ![SAP_BASIS 확인](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>필수 구성 요소

이 SAP 테이블 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 통합 런타임을 설정합니다(버전 3.17 이상). 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](create-self-hosted-integration-runtime.md)을 참조하세요.

- SAP 웹 사이트에서 64비트 [SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)을 다운로드하고 이를 자체 호스팅 통합 런타임 컴퓨터에 설치합니다. 설치 도중 **선택적 설치 단계** 창에서 **GAC에 어셈블리 설치** 를 선택해야 합니다.

  ![SAP Connector for .NET 설치](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP 테이블 커넥터에서 사용 중인 SAP 사용자는 다음 사용 권한이 있어야 합니다.

  - RFC(Remote Function Call) 대상을 사용하기 위한 권한 부여.
  - S_SDSAUTH 권한 부여 개체 실행 작업에 대한 사용 권한. 과반수 권한 부여 개체에서 SAP Note 460089를 참조하세요. 기본 NCo 커넥터에는 RFC_FUNCTION_SEARCH와 같은 특정 RFC가 필요합니다. 

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-an-sap-table-using-ui"></a>UI를 사용하여 SAP 테이블에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 SAP 테이블에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하여 연결된 서비스를 선택하고 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스 만들기 스크린샷":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스 만들기 스크린샷":::

2. SAP를 검색하고 SAP 테이블 커넥터를 선택합니다.

    :::image type="content" source="media/connector-sap-table/sap-table-connector.png" alt-text="SAP 테이블 커넥터 스크린샷":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-sap-table/configure-sap-table-linked-service.png" alt-text="SAP 테이블 연결된 서비스 구성 스크린샷":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 SAP 테이블 커넥터와 관련된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP BW Open Hub 연결된 서비스에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `type` | 이 옵션을 사용하는 경우 `type` 속성은 `SapTable`로 설정해야 합니다. | 예 |
| `server` | SAP 인스턴스가 있는 서버의 이름입니다.<br/>SAP 애플리케이션 서버에 연결하는 데 사용합니다. | 예 |
| `systemNumber` | SAP 시스템의 시스템 번호입니다.<br/>SAP 애플리케이션 서버에 연결하는 데 사용합니다.<br/>허용되는 값은 문자열 형식의 두 자리 10진수입니다. | 예 |
| `messageServer` | SAP 메시지 서버의 호스트 이름입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `messageServerService` | 메시지 서버의 서비스 이름 또는 포트 번호입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `systemId` | 테이블이 있는 SAP 시스템의 ID입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `logonGroup` | SAP 시스템의 로그온 그룹입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| `clientId` | SAP 시스템의 클라이언트 ID입니다.<br/>허용되는 값은 문자열 형식의 세 자리 10진수입니다. | 예 |
| `language` | SAP 시스템에서 사용하는 언어입니다.<br/>기본값은 `EN`여야 합니다.| 예 |
| `userName` | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | 예 |
| `password` | 사용자의 암호입니다. 이 필드를 `SecureString` 유형으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| `sncMode` | 테이블이 있는 SAP 서버에 액세스하기 위한 SNC 활성화 표시기입니다.<br/>SNC로 SAP 서버에 연결하려는 경우에 사용합니다.<br/>허용되는 값은 `0`(해제, 기본값) 또는 `1`(설정)입니다. | 예 |
| `sncMyName` | 테이블이 있는 SAP 서버에 액세스하기 위한 개시 장치의 SNC 이름입니다.<br/>`sncMode`의 값이 설정일 때 적용됩니다. | 예 |
| `sncPartnerName` | 테이블이 있는 SAP 서버에 액세스하기 위한 통신 파트너의 SNC 이름입니다.<br/>`sncMode`의 값이 설정일 때 적용됩니다. | 예 |
| `sncLibraryPath` | 테이블이 있는 SAP 서버에 액세스하기 위한 외부 보안 제품의 라이브러리입니다.<br/>`sncMode`의 값이 설정일 때 적용됩니다. | 예 |
| `sncQop` | 적용할 SNC 보호 품질 수준입니다.<br/>`sncMode`의 값이 설정일 때 적용됩니다. <br/>허용되는 값은 `1`(인증), `2`(무결성), `3`(개인 정보), `8`(기본값), `9`(최대값)입니다. | 예 |
| `connectVia` | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [사전 요구 사항](#prerequisites)에서 설명한 대로 자체 호스팅 통합 런타임이 필요합니다. |예 |

### <a name="example-1-connect-to-an-sap-application-server"></a>예 1: SAP 애플리케이션 서버에 연결

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>예 2: SAP 메시지 서버에 연결

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>예 3: SNC를 사용하여 연결

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 대한 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요. 이 섹션에서는 SAP 테이블 데이터 세트에서 지원되는 속성의 목록을 제공합니다.

SAP BW Open Hub 연결된 서비스 간에 데이터를 복사하려는 경우 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `type` | 이 옵션을 사용하는 경우 `type` 속성은 `SapTableResource`로 설정해야 합니다. | 예 |
| `tableName` | 데이터를 복사할 SAP 테이블의 이름입니다. | 예 |

### <a name="example"></a>예제

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 대한 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 다음 섹션에서는 SAP 테이블 데이터 원본에서 지원되는 속성의 목록을 제공합니다.

### <a name="sap-table-as-source"></a>SAP 테이블 원본

SAP 테이블의 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 속성                         | Description                                                  | 필수 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | 이 옵션을 사용하는 경우 `type` 속성은 `SapTableSource`로 설정해야 합니다.         | 예      |
| `rowCount`                         | 검색할 행의 수입니다.                              | 예       |
| `rfcTableFields`                 | SAP 테이블에서 복사할 필드(열)입니다. 예들 들어 `column0, column1`입니다. | 예       |
| `rfcTableOptions`                | SAP 테이블의 행을 필터링하는 옵션입니다. 예들 들어 `COLUMN0 EQ 'SOMEVALUE'`입니다. 이 문서 뒷부분에 나오는 SAP 쿼리 연산자 표도 참조하세요. | 예       |
| `customRfcReadTableFunctionModule` | SAP 테이블에서 데이터를 읽는 데 사용할 수 있는 사용자 지정 RFC 함수 모듈입니다.<br>사용자 지정 RFC 함수 모듈을 사용하여 SAP 시스템에서 데이터를 검색 후 서비스로 반환하는 방법을 정의할 수 있습니다. 사용자 지정 함수 모듈에는 서비스에서 사용하는 기본 인터페이스인 `/SAPDS/RFC_READ_TABLE2` 인터페이스와 비슷하게 구현된 인터페이스(가져오기, 내보내기, 테이블)가 있어야 합니다.| 예       |
| `partitionOption`                  | SAP 테이블에서 읽을 파티션 메커니즘입니다. 지원되는 옵션은 다음과 같습니다. <ul><li>`None`</li><li>`PartitionOnInt`(`0000012345`의 값과 같이 왼쪽 여백이 0인 일반 정수 또는 정수 값)</li><li>`PartitionOnCalendarYear`("YYYY" 형식의 4자리 숫자)</li><li>`PartitionOnCalendarMonth`("YYYYMM" 형식의 6자리 숫자)</li><li>`PartitionOnCalendarDate`("YYYYMMDD" 형식의 8자리 숫자)</li><li>`PartitionOntime`(`235959` 형식과 같은 "HHMMSS" 형식의 6자리 숫자)</li></ul> | 예       |
| `partitionColumnName`              | 데이터를 분할하는 데 사용되는 열의 이름입니다.                | 예       |
| `partitionUpperBound`              | 분할을 계속하는데 사용할 `partitionColumnName`에 지정된 열의 최대값입니다. | 예       |
| `partitionLowerBound`              | 분할을 계속하는데 사용할 `partitionColumnName`에 지정된 열의 최소값입니다. (참고: 파티션 옵션이 `PartitionOnInt`인 경우 `partitionLowerBound`의 값은 "0"일 수 없음) | 예       |
| `maxPartitionsNumber`              | 데이터를 분할할 최대 파티션 수입니다. 기본값은 1입니다.    | 예       |
| `sapDataColumnDelimiter` | 출력 데이터를 분할하기 위해 SAP RFC에 전달되어 구분 기호로 사용하는 단일 문자입니다. | 예 |

>[!TIP]
>SAP 테이블에 수십억 개의 행과 같은 방대한 양의 데이터가 있는 경우 `partitionOption` 및 `partitionSetting`을 사용하여 데이터를 더 작은 파티션으로 분할합니다. 이 경우 데이터는 파티션당 읽히고, 각 데이터 파티션은 단일 RFC 호출을 통해 SAP 서버에서 검색됩니다.<br/>
<br/>
>예를 들어, `partitionOption`을 `partitionOnInt`로 선택하면 각 파티션에 있는 행의 수는 (`partitionUpperBound`에서 `partitionLowerBound` 사이에 있는 총 행 수)/`maxPartitionsNumber`의 수식으로 계산됩니다.<br/>
<br/>
>복사 속도를 높이기 위해 데이터 파티션을 병렬로 로드하려면 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정이 병렬 처리 수준을 제어합니다. 예를 들어 `parallelCopies`의 값을 4로 설정하면 서비스는 지정된 파티션 옵션과 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며, 각 쿼리는 SAP 테이블에서 데이터의 일부를 검색합니다. `maxPartitionsNumber`의 값은 `parallelCopies` 속성 값의 배수로 설정하는 것이 좋습니다. 데이터를 파일 기반 데이터 저장소로 복사하는 경우 폴더에 여러 개의 파일(폴더 이름만 지정)로 쓰는 것이 좋습니다. 이 경우에는 단일 파일에 쓰는 것보다 성능이 더 좋습니다.


>[!TIP]
> `BASXML`은 서비스 내의 이 SAP 테이블 커넥터를 기본적으로 사용합니다.

`rfcTableOptions`에서 다음의 일반적인 SAP 쿼리 연산자를 사용하여 행을 필터링할 수 있습니다.

| 연산자 | Description |
| :------- | :------- |
| `EQ` | 같음 |
| `NE` | 같지 않음 |
| `LT` | 보다 작음 |
| `LE` | 작거나 같음 |
| `GT` | 초과 |
| `GE` | 크거나 같음 |
| `IN` | `TABCLASS IN ('TRANSP', 'INTTAB')`의 값과 같음 |
| `LIKE` | `LIKE 'Emma%'`의 값과 같음 |

### <a name="example"></a>예제

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="join-sap-tables"></a>SAP 테이블 조인

현재 SAP 테이블 커넥터는 기본 함수 모듈을 사용하는 하나의 테이블만 지원합니다. 여러 테이블이 조인된 데이터를 가져오려면 다음 단계를 수행하여 SAP 테이블 커넥터에서 [customRfcReadTableFunctionModule](#copy-activity-properties) 속성을 활용할 수 있습니다.

- 쿼리를 “옵션”으로 사용하고 데이터를 검색하는 고유한 논리를 적용할 수 있는 [사용자 지정 함수 모듈을 작성](#create-custom-function-module)합니다.
- "사용자 지정 함수 모듈"의 경우 사용자 지정 함수 모듈의 이름을 입력합니다.
- "RFC 테이블 옵션"의 경우 함수 모듈에 제공할 테이블 조인 문을 "COLUMN0의 `<TABLE1>` INNER JOIN `<TABLE2>`"과 같은 옵션으로 지정합니다.

예제는 아래와 같습니다.

![SAP 테이블 조인](./media/connector-sap-table/sap-table-join.png) 

>[!TIP]
>SAP 테이블 커넥터에서 지원하는 뷰에서 조인된 데이터를 집계하는 것도 고려할 수 있습니다.
>또한 관련 테이블을 추출하여 Azure(예: Azure Storage, Azure SQL Database)에 온보딩한 다음 Data Flow를 사용하여 추가 조인 또는 필터링을 진행해 볼 수 있습니다.

## <a name="create-custom-function-module"></a>사용자 지정 함수 모듈 만들기

SAP 테이블의 경우 현재 복사 원본에서 [customRfcReadTableFunctionModule](#copy-activity-properties) 속성을 지원하므로 사용자 고유의 논리를 활용하고 데이터를 처리할 수 있습니다.

다음은 "사용자 지정 함수 모듈"을 시작하기 위한 몇 가지 요구 사항에 관한 간단한 지침입니다.

- 정의:

    ![정의](./media/connector-sap-table/custom-function-module-definition.png) 

- 데이터를 아래 테이블 중 하나로 내보냅니다.

    ![내보내기 테이블 1](./media/connector-sap-table/export-table-1.png) 

    ![내보내기 테이블 2](./media/connector-sap-table/export-table-2.png)
 
다음은 SAP 테이블 커넥터가 사용자 지정 함수 모듈에서 작동하는 방식을 보여 주는 그림입니다.

1. SAP NCO를 통해 SAP 서버와의 연결을 빌드합니다.

1. 아래와 같이 설정된 매개 변수를 사용하여 "사용자 지정 함수 모듈"을 호출합니다.

    - QUERY_TABLE: SAP 테이블 데이터 세트에 설정된 테이블 이름입니다. 
    - Delimiter: SAP 테이블 원본에서 설정한 구분 기호입니다. 
    - ROWCOUNT/Option/Fields: 테이블 원본에서 설정한 행 수/집계 옵션/필드입니다.

1. 결과를 가져오고 아래의 방법으로 데이터를 구문 분석합니다.

    1. 필드 테이블의 값을 구문분석하여 스키마를 가져옵니다.

        ![필드 값 구문 분석](./media/connector-sap-table/parse-values.png)

    1. 출력 테이블의 값을 가져와서 해당 값이 포함된 테이블을 확인합니다.

        ![출력 테이블 값 가져오기](./media/connector-sap-table/get-values.png)

    1. OUT_TABLE의 값을 가져와서 데이터를 구문 분석한 다음 싱크에 씁니다.

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 테이블의 데이터 형식 매핑

SAP 테이블의 데이터를 복사하는 경우 SAP 테이블 데이터 형식에서 서비스 내에서 사용되는 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP ABAP 형식 | 서비스 중간 데이터 형식 |
|:--- |:--- |
| `C`(문자열) | `String` |
| `I`(정수) | `Int32` |
| `F`(부동) | `Double` |
| `D`(날짜) | `String` |
| `T`(시간) | `String` |
| `P`(BCD 압축, 통화, 10진수, 수량) | `Decimal` |
| `N`(숫자) | `String` |
| `X`(이진 및 원시) | `String` |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계

복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
