---
title: SAP ECC에서 데이터 복사
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 파이프라인의 복사 활동을 사용하여 데이터를 SAP ECC에서 지원되는 싱크 데이터 저장소로 복사하는 방법을 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/28/2020
ms.openlocfilehash: bf20bc4b3e796f118b829cc979af1ff4ac75a0c6
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123316829"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP ECC에서 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP ECC(Enterprise Central Component)에서 데이터를 복사하는 방법을 설명합니다. 자세한 내용은 [작업 복사 개요](copy-activity-overview.md)를 참조하세요.

>[!TIP]
>SAP 데이터 통합 시나리오에서 ADF의 전반적인 지원에 대한 자세한 내용은 [Azure Data Factory 백서를 사용한 SAP 데이터 통합](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)에 설명된 각 SAP 커넥터의 자세한 소개, 비교 및 지침을 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SFTP ECC 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SAP ECC에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SAP ECC 커넥터는 다음을 지원합니다.

- SAP NetWeaver 7.0 버전 이상의 SAP ECC에서 데이터 복사
- SAP ECC OData 서비스에서 노출하는 다음과 같은 개체에서 데이터 복사

  - SAP 테이블 또는 뷰
  - BAPI[비즈니스 애플리케이션 프로그래밍 인터페이스] 개체
  - 데이터 추출기
  - 상대 어댑터를 통해 OData로 수신할 수 있으며 SAP PI(프로세스 통합)로 전송되는 데이터 또는 중간 문서(IDOC)

- 기본 인증을 사용하여 데이터 복사

버전 7.0 이상에서는 SAP ECC 버전 대신 SAP NetWeaver 버전을 참조합니다. 예를 들어 SAP ECC 6.0 EHP 7은 일반적으로 NetWeaver 버전 >=7.4입니다. 현재 환경에 대해 잘 모르는 경우 SAP 시스템에서 다음 단계를 따라 버전을 확인합니다.

1. SAP GUI를 사용하여 SAP 시스템에 연결합니다. 
2. **시스템** -> **상태** 로 이동합니다. 
3. SAP_BASIS의 릴리스를 확인하여 701보다 크거나 같은지 확인합니다.  
      ![SAP_BASIS 확인](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>SAP 테이블 또는 뷰를 통해 SAP ECC에서 데이터를 복사하려면 빠르고 확장성이 뛰어난 [SAP 테이블](connector-sap-table.md) 커넥터를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 SAP ECC 커넥터를 사용하려면 OData 서비스를 사용하여 SAP 게이트웨이를 통해 SAP ECC 엔터티를 노출해야 합니다. 더 구체적으로 살펴보면 다음과 같습니다.

- **SAP 게이트웨이 설정**. SAP NetWeaver 버전이 7.4 이상인 서버에는 SAP 게이트웨이가 이미 설치되어 있습니다. 이전 버전의 경우 OData 서비스를 통해 SAP ECC 데이터를 노출하기 전에 포함된 SAP 게이트웨이 또는 SAP 게이트웨이 허브 시스템을 설치해야 합니다. SAP 게이트웨이를 설정하려면 [설치 가이드](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)를 참조하세요.

- **SAP OData 서비스 활성화 및 구성**. 수초 내에 TCODE SICF를 통해 OData 서비스를 활성화할 수 있습니다. 공개해야 하는 개체를 구성할 수도 있습니다. 자세한 내용은 [단계별 가이드](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)를 참조하세요.

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sap-ecc-using-ui"></a>UI를 사용하여 SAP ECC에 연결된 서비스 만들기

다음 단계를 사용하여 Azure Portal UI에서 SAP ECC에 연결된 서비스를 만듭니다.

1. Azure Data Factory 또는 Synapse 작업 영역에서 관리 탭으로 이동하고 연결된 서비스를 선택한 다음 새로 만들기를 클릭합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory UI를 사용하여 새 연결된 서비스를 만드는 스크린샷.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse UI를 사용하여 연결된 새 서비스를 만드는 스크린샷.":::

2. SAP를 검색하고 SAP ECC 커넥터를 선택합니다.

    :::image type="content" source="media/connector-sap-ecc/sap-ecc-connector.png" alt-text="SAP ECC 커넥터의 스크린샷.":::    

1. 서비스 세부 정보를 구성하고 연결을 테스트하고 새 연결된 서비스를 만듭니다.

    :::image type="content" source="media/connector-sap-ecc/configure-sap-ecc-linked-service.png" alt-text="SAP ECC의 연결된 서비스 구성 스크린샷.":::

## <a name="connector-configuration-details"></a>커넥터 구성 세부 정보

다음 섹션에서는 SAP ECC 커넥터와 관련된 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP ECC 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `type` | 이 옵션을 사용하는 경우 `type` 속성은 `SapEcc`로 설정해야 합니다. | 예 |
| `url` | SAP ECC OData 서비스의 URL입니다. | 예 |
| `username` | SAP ECC에 연결하는 데 사용되는 사용자 이름입니다. | 예 |
| `password` | SAP ECC에 연결하는 데 사용되는 일반 텍스트 암호입니다. | 예 |
| `connectVia` | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 런타임을 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. | 예 |

### <a name="example"></a>예제

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요. 이 섹션에서는 SAP ECC 데이터 세트에서 지원되는 속성의 목록을 제공합니다.

SAP ECC에서 데이터를 복사하려면 데이터 세트의 `type` 속성을 `SapEccResource`로 설정합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `path` | SAP ECC OData 엔터티의 경로입니다. | 예 |

### <a name="example"></a>예제

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 이 섹션에서는 SAP ECC 원본에서 지원되는 속성의 목록을 제공합니다.

### <a name="sap-ecc-as-a-source"></a>SAP ECC를 원본으로 설정

SAP ECC에서 데이터를 복사하려면 복사 작업의 `source` 섹션에서 `type` 속성을 `SapEccSource`로 설정합니다.

복사 작업의 `source` 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| `type` | 복사 작업 `source` 섹션의 `type` 속성을 `SapEccSource`로 설정해야 합니다. | 예 |
| `query` | 데이터를 필터링하는 OData 쿼리 옵션입니다. 예를 들면 다음과 같습니다.<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 커넥터가 결합된 URL에서 데이터를 복사합니다.<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>자세한 내용은 [OData URL 구성 요소](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)를 참조하세요. | 예 |
| `sapDataColumnDelimiter` | 출력 데이터를 분할하기 위해 SAP RFC에 전달되어 구분 기호로 사용하는 단일 문자입니다. | 예 |
| `httpRequestTimeout` | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 지정하지 않으면 기본값은 **00:30:00**(30분)입니다. | 예 |

### <a name="example"></a>예제

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC에 대한 데이터 형식 매핑

SAP ECC에서 데이터를 복사하는 경우 SAP ECC 데이터에 대한 OData 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로의 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| OData 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> 복합 데이터 형식은 현재 지원되지 않습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계

Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
