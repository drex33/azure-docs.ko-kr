---
title: SAP BW에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인의 복사 작업을 사용 하 여 SAP Business Warehouse에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 2cbdd3dc237c3f2e7b3cf23bb844a06fdd40d605
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325850"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory 또는 Synapse Analytics를 사용 하 여 SAP Business Warehouse에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [현재 버전](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse Analytics 파이프라인의 복사 작업을 사용 하 여 SAP Business Warehouse (BW)에서 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>SAP 데이터 통합 시나리오에 대 한 서비스의 전반적인 지원에 대 한 자세한 내용은 [Azure Data Factory 백서를 사용 하 여 sap 데이터 통합](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) 및 각 sap 커넥터에 대 한 자세한 소개, 비교 및 지침을 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

SAP Business Warehouse 커넥터는 다음 작업을 지원합니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SAP Business Warehouse에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SAP Business Warehouse 커넥터는 다음을 지원합니다.

- SAP Business Warehouse **버전 7.x**
- MDX 쿼리를 사용하여 **InfoCubes 및 QueryCubes**(BEx 쿼리 포함)에서 데이터를 복사합니다.
- 기본 인증을 사용하여 데이터를 복사합니다.

>[!NOTE]
>SAP Business Warehouse 커넥터는 현재 MDX를 사용 하는 매개 변수를 지원 하지 않습니다.  MDX 매개 변수를 사용 하 여 필터링 해야 하는 경우 대신 대체 [SAP Open Hub 커넥터](connector-sap-business-warehouse-open-hub.md) 를 사용 하는 것을 고려할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 SAP Business Warehouse 커넥터를 사용 하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- 통합 런타임 컴퓨터에 **SAP NetWeaver 라이브러리** 를 설치합니다. SAP Netweaver 라이브러리는 SAP 관리자를 통해 구하거나 [SAP 소프트웨어 다운로드 센터](https://support.sap.com/swdc)에서 바로 구할 수 있습니다. 최신 버전에 대한 다운로드 위치를 찾으려면 **SAP Note #1025361** 을 검색합니다. Integration Runtime 설치와 일치하는 **64-비트** SAP NetWeaver 라이브러리를 선택해야 합니다. 그런 다음 SAP Note에 따라 SAP NetWeaver RFC SDK에 포함된 모든 파일을 설치합니다. SAP NetWeaver 라이브러리는 SAP Client Tools 설치에도 포함됩니다.

>[!TIP]
>SAP BW에 대한 연결 문제를 해결하려면 다음을 확인합니다.
>- NetWeaver RFC SDK에서 추출된 모든 종속성 라이브러리는 %windir%\system32 폴더에 적용됩니다. 일반적으로 icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll이 포함 됩니다.
>- SAP 서버에 연결하는 데 사용되는 필요한 포트는 자체 호스팅 IR 머신에서 활성화되며, 일반적으로 3300 및 3201 포트입니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sap-bw-using-ui"></a>UI를 사용하여 SAP BW에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 SAP BW에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만듭니다.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 새 연결된 서비스를 만듭니다.":::

2. SAP를 검색하고 MDX 커넥터를 통해 SAP BW를 선택합니다.

    :::image type="content" source="media/connector-sap-business-warehouse/sap-business-warehouse-connector.png" alt-text="MDX 커넥터를 통한 SAP BW를 선택합니다.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-sap-business-warehouse/configure-sap-business-warehouse-linked-service.png" alt-text="SAP BW에 연결된 서비스를 구성합니다.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 SAP Business Warehouse 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP BW(Business Warehouse) 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **SapBw** 로 설정해야 합니다. | 예 |
| 서버 | SAP BW 인스턴스가 상주하는 서버의 이름. | 예 |
| systemNumber | SAP BW 시스템의 시스템 번호.<br/>허용되는 값: 문자열로 표현되는 두 자리 10진수 | 예 |
| clientId | SAP W 시스템에 있는 클라이언트의 클라이언트 ID.<br/>허용되는 값: 문자열로 표현되는 세 자리 10진수 | 예 |
| userName | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | 예 |
| password | 사용자에 대한 암호입니다. 이 필드를 SecureString으로 표시하여 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 SAP BW 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP BW에서 데이터를 복사하려면 데이터 세트의 형식 속성을 **SapBwCube** 로 설정합니다. 반면에 RelationalTable 형식의 SAP BW 데이터 세트에 대해 지원되는 type별 속성은 없습니다.

**예:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 형식의 데이터 세트를 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SAP BW 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-bw-as-source"></a>SAP BW를 원본으로

SAP BW에서 데이터를 복사하기 위해 복사 작업 **원본** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 원본의 형식 속성을 **SapBwSource** 로 설정해야 합니다. | 예 |
| Query | SAP BW 인스턴스에서 데이터를 읽을 MDX 쿼리를 지정합니다. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource` 형식의 원본을 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW에 대한 데이터 형식 매핑

SAP BW에서 데이터를 복사 하는 경우 SAP BW 데이터 형식에서 서비스 내부에 사용 되는 중간 데이터 형식으로 다음 매핑이 사용 됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP BW 데이터 형식 | 중간 서비스 데이터 형식 |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| 단위 | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계
복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
