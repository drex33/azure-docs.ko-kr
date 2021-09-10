---
title: Open Hub를 통해 SAP Business Warehouse에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Open Hub를 통해 SAP BW(Business Warehouse)에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 07/30/2021
ms.openlocfilehash: 2155e39da58650fb5ce14c56d7a3564f211a2cdd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642575"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Open Hub를 통해 SAP Business Warehouse에서 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP BW(Business Warehouse)에서 Open Hub를 통해 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>SAP 데이터 통합 시나리오에서 ADF의 전반적인 지원에 대한 자세한 내용은 [Azure Data Factory 백서를 사용한 SAP 데이터 통합](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)에 설명된 각 SAP 커넥터의 자세한 소개, 비교 및 지침을 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Open Hub 커넥터를 통해 이 SAP Business Warehouse에서 지원하는 작업은 다음과 같습니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Open Hub를 통해 SAP Business Warehouse에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

이 SAP Business Warehouse 커넥터는 구체적으로 다음 작업을 지원합니다.

- SAP Business Warehouse **버전 7.01이상(2015년 이후 출시된 최신 SAP Support Package Stack에 포함되어 있음)** . SAP BW/4HANA는 이 커넥터에서 지원되지 않습니다.
- Open Hub 대상 로컬 테이블(아래에 DSO, InfoCube, MultiProvider, DataSource 등이 있을 수 있음)을 통해 데이터를 복사합니다.
- 기본 인증을 사용하여 데이터를 복사합니다.
- SAP 애플리케이션 서버 또는 SAP 메시지 서버에 연결.
- RFC를 통해 데이터를 검색합니다.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub 통합 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service)를 사용하면 SAP BW에서 데이터를 효율적으로 추출할 수 있습니다. 다음 다이어그램은 고객 SAP 시스템의 일반적인 흐름 중 하나를 보여줍니다. 여기서 데이터는 SAP ECC -> PSA -> DSO -> Cube로 이동됩니다.

SAP BW OHD(Open Hub 대상)에 따라 SAP 데이터가 릴레이되는 대상이 정의됩니다. SAP DTP(데이터 전송 프로세스)에서 지원하는 모든 개체는 Open Hub 데이터 원본(예: DSO, InfoCube, DataSource 등)으로 사용할 수 있습니다. 릴레이된 데이터가 저장되는 Open Hub 대상 유형은 데이터베이스 테이블(로컬 또는 원격) 및 플랫 파일일 수 있습니다. 이 SAP BW Open Hub 커넥터를 사용하면 BW에서 OHD 로컬 테이블의 데이터를 복사할 수 있습니다. 다른 형식을 사용하는 경우에는 다른 커넥터를 통해 데이터베이스 또는 파일 시스템에 직접 연결할 수 있습니다.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>델타 추출 흐름

ADF SAP BW Open Hub 커넥터는 `excludeLastRequest` 및 `baseRequestId`의 두 가지 선택적 속성을 제공하여 Open Hub의 델타 로드를 처리하는 데 사용할 수 있습니다. 

- **excludeLastRequestId**: 마지막 요청의 레코드를 제외할지 여부입니다. 기본값은 true입니다. 
- **baseRequestId**: 델타 로드 요청의 ID입니다. 설정하는 경우 requestId가 이 속성의 값보다 큰 데이터만 검색됩니다. 

SAP InfoProvider에서 ADF(Azure Data Factory)로의 추출은 대체로 다음 두 단계로 구성됩니다. 

1. **SAP BW DTP(데이터 전송 프로세스)** 이 단계에서는 데이터를 SAP BW InfoProvider에서 SAP BW Open Hub 테이블로 복사합니다. 

1. **ADF 데이터 복사** 이 단계에서는 ADF 커넥터에서 Open Hub 테이블을 읽습니다. 

![델타 추출 흐름](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

첫 번째 단계에서는 DTP가 실행됩니다. 각 실행에서 새 SAP 요청 ID를 만듭니다. 요청 ID는 Open Hub 테이블에 저장된 다음, ADF 커넥터에서 델타를 식별하는 데 사용됩니다. 두 단계는 비동기적으로 실행됩니다. 즉, DTP는 SAP에서 트리거되고, ADF 데이터 복사는 ADF를 통해 트리거됩니다. 

기본적으로 ADF는 Open Hub 테이블에서 최신 델타를 읽지 않습니다("마지막 요청 제외" 옵션이 true임). 따라서 ADF의 데이터는 Open Hub 테이블의 데이터가 모두 포함된 완전한 최신 상태가 아닙니다(마지막 델타가 누락됨). 대신 이 절차는 비동기 추출로 인해 행이 손실되지 않도록 합니다. DTP에서 여전히 동일한 테이블에 쓰는 동안 ADF에서 Open Hub 테이블을 읽는 경우에도 정상적으로 작동합니다. 

일반적으로 ADF에서 마지막으로 실행한 최대 복사된 요청 ID를 준비 데이터 저장소(예: 위 다이어그램의 Azure Blob)에 저장합니다. 따라서 후속 실행에서 ADF는 동일한 요청을 두 번째로 읽지 않습니다. 한편 데이터는 Open Hub 테이블에서 자동으로 삭제되지 않습니다.

적절한 델타 처리를 위해 동일한 Open Hub 테이블에는 다른 DTP의 요청 ID가 있을 수 없습니다. 따라서 각 OHD(Open Hub 대상)에 대해 둘 이상의 DTP를 만들지 않아야 합니다. 동일한 InfoProvider로부터의 전체 및 델타 추출이 필요한 경우 동일한 InfoProvider에 대해 두 개의 OHD를 만들어야 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 SAP Business Warehouse 커넥터를 사용하려면 다음 작업을 수행해야 합니다.

- 버전 3.13 이상의 자체 호스팅 통합 런타임을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.

- SAP 웹 사이트에서 **64비트 [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** 을 다운로드하여 자체 호스팅 IR 컴퓨터에 설치합니다. 설치 시 선택적 설정 단계 창에서 다음 이미지에 나와 있는 것처럼 **GAC에 어셈블리 설치** 옵션을 선택하세요. 

    ![SAP .NET Connector 설치](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory BW 커넥터에서 사용 중인 SAP 사용자에게는 다음 권한이 있어야 합니다. 

    - RFC 및 SAP BW에 대해 부여된 권한 
    - 권한 부여 개체 "S_SDSAUTH"의 "실행" 작업에 대한 권한

- "기술 키" 옵션을 선택하여 SAP Open Hub 대상 유형을 **데이터베이스 테이블** 로 만듭니다.  테이블에서 데이터 삭제는 선택을 취소한 상태로 유지하는 것이 좋습니다(반드시 선택 취소해야 하는 것은 아님). DTP(직접 실행 또는 기존 프로세스 체인에 통합)를 사용하여 선택한 원본 개체(예: 큐브)의 데이터를 Open Hub 대상 테이블로 가져옵니다.

## <a name="getting-started"></a>시작

> [!TIP]
>
> SAP BW Open Hub 커넥터를 사용하는 방법에 대한 연습은 [Azure Data Factory를 사용하여 SAP BW(Business Warehouse)에서 데이터 로드](load-sap-bw-data.md)를 참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP Business Warehouse Open Hub 커넥터와 관련된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP Business Warehouse Open Hub 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **SapOpenHub** 로 설정해야 합니다. | 예 |
| 서버 | SAP BW 인스턴스가 상주하는 서버의 이름. | 예 |
| systemNumber | SAP BW 시스템의 시스템 번호.<br/>허용되는 값: 문자열로 표현되는 두 자리 10진수 | 예 |
| messageServer | SAP 메시지 서버의 호스트 이름입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| messageServerService | 메시지 서버의 서비스 이름 또는 포트 번호입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| systemId | 테이블이 있는 SAP 시스템의 ID입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| logonGroup | SAP 시스템의 로그온 그룹입니다.<br/>SAP 메시지 서버에 연결하는 데 사용합니다. | 예 |
| clientId | SAP W 시스템에 있는 클라이언트의 클라이언트 ID.<br/>허용되는 값: 문자열로 표현되는 세 자리 10진수 | 예 |
| 언어 | SAP 시스템에서 사용하는 언어입니다. | No(기본값: **EN**)|
| userName | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | 예 |
| password | 사용자에 대한 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 SAP BW Open Hub 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP BW Open Hub 간에 데이터를 복사하려면 데이터 세트의 형식 속성을 **SapOpenHubTable** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 이 속성은 **SapOpenHubTable** 로 설정해야 합니다.  | 예 |
| openHubDestinationName | 복사할 데이터가 있는 Open Hub 대상의 이름입니다. | 예 |

데이터 세트에서 `excludeLastRequest` 및 `baseRequestId`를 설정한 경우 여전히 있는 그대로 지원되지만, 앞으로는 작업 원본에 새 모델을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SAP BW Open Hub 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub(원본)

SAP BW Open Hub에서 데이터를 복사하기 위해 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 source의 **type** 속성은 **SapOpenHubSource** 로 설정해야 합니다. | 예 |
| excludeLastRequest | 마지막 요청의 레코드를 제외할지 여부입니다. | No(기본값: **true**) |
| baseRequestId | 델타 로드의 요청 ID입니다. 설정하는 경우 requestId가 이 속성의 값 **보다 큰** 데이터만 검색됩니다.  | 예 |
| customRfcReadTableFunctionModule | SAP 테이블에서 데이터를 읽는 데 사용할 수 있는 사용자 지정 RFC 함수 모듈입니다. <br/> 사용자 지정 RFC 함수 모듈을 사용하여 SAP 시스템에서 데이터를 검색 후 Data Factory로 반환하는 방법을 정의할 수 있습니다. 사용자 지정 함수 모듈에는 Data Factory에서 사용하는 기본 인터페이스인 `/SAPDS/RFC_READ_TABLE2` 인터페이스와 비슷하게 구현된 인터페이스 (가져오기, 내보내기, 테이블)가 있어야 합니다. | 예 |
| sapDataColumnDelimiter | 출력 데이터를 분할하기 위해 SAP RFC에 전달되어 구분 기호로 사용하는 단일 문자입니다. | 예 |

>[!TIP]
>항상 테이블의 기존 데이터를 모두 로드하고 덮어쓰거나, 테스트를 위해 DTP를 한 번만 실행하는 등 Open Hub 테이블에 단일 요청 ID를 통해 생성된 데이터만 포함되는 경우에는 "excludeLastRequest" 옵션 선택을 취소하여 데이터를 외부로 복사해야 합니다.

데이터 로드 속도를 높이려면 SAP BW Open Hub에서 데이터를 병렬로 로드하는 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy)를 복사 작업에 설정할 수 있습니다. 예를 들어`parallelCopies`를 4로 설정하면 Data Factory에서 4개의 RFC 호출을 동시에 실행하고, 각 RFC 호출에서 DTP 요청 ID 및 패키지 ID로 분할된 SAP BW Open Hub 테이블로부터 데이터의 일부를 검색합니다. 이는 고유 DTP 요청 ID + 패키지 ID의 수가 `parallelCopies`의 값보다 클 때 적용됩니다. 데이터를 파일 기반 데이터 저장소로 복사하는 경우 폴더에 여러 개의 파일(폴더 이름만 지정)로 쓰는 것이 좋습니다. 이 경우에는 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW Open Hub에 대한 데이터 형식 매핑

SAP BW Open Hub에서 데이터를 복사할 때는 SAP BW 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로의 다음과 같은 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP ABAP 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| C(문자열) | String |
| I(정수) | Int32 |
| F(부동) | Double |
| D(날짜) | String |
| T(시간) | String |
| P(BCD 압축, 통화, 10진수, 수량) | Decimal |
| N(숫자) | String |
| X(이진 및 원시) | String |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="troubleshooting-tips"></a>문제 해결 팁

**증상:** HANA에서 SAP BW를 실행하고 ADF 복사 작업(100만 개 행)을 사용하여 데이터의 하위 세트만 복사한다고 관찰되는 경우 가능한 원인은 DTP에서 "SAP HANA 실행" 옵션을 사용하도록 설정하기 때문입니다. 이 경우 ADF는 첫 번째 데이터 일괄 처리만 검색할 수 있습니다.

**해결 방법:** DTP에서 "SAP HANA 실행" 옵션을 사용하지 않도록 설정하고, 데이터를 다시 처리한 다음, 복사 작업을 다시 실행합니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
