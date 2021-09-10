---
title: 복사 활동의 스키마 및 데이터 형식 매핑
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인의 복사 작업이 어떻게 원본 데이터의 스키마와 데이터 형식을 싱크 데이터에 매핑하는지 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: jianleishen
ms.openlocfilehash: 046b25164df92c609196a701d35f989aa397253b
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825094"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>복사 활동의 스키마 및 데이터 형식 매핑
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 복사 작업에서 원본 데이터의 스키마와 데이터 형식을 싱크 데이터에 매핑하는 방법을 설명합니다.

## <a name="schema-mapping"></a>스키마 매핑

### <a name="default-mapping"></a>기본 매핑

기본적으로 복사 작업은 원본 데이터를 대/소문자 구분 방식으로 **열 이름에 따라** 싱크에 매핑합니다. 예를 들어 파일에 쓰는 경우와 같이 싱크가 없으면 원본 필드 이름이 싱크 이름으로 유지됩니다. 싱크가 이미 있는 경우 원본에서 복사되는 모든 열이 포함되어야 합니다. 이러한 기본 매핑은 원본에서 싱크로, 실행에서 실행으로의 유연한 스키마 및 스키마 드리프트를 지원합니다. 원본 데이터 저장소에서 반환된 모든 데이터는 싱크로 복사할 수 있습니다.

소스가 헤더 줄이 없는 텍스트 파일인 경우 원본에 열 이름이 포함되지 않으므로 [명시적 매핑](#explicit-mapping)이 필요합니다.

### <a name="explicit-mapping"></a>명시적 매핑

명시적 매핑을 지정하여 필요에 따라 원본에서 싱크로 열/필드 매핑을 사용자 지정할 수도 있습니다. 명시적 매핑을 사용하면 부분 원본 데이터만 싱크로 복사하거나, 다른 이름을 가진 싱크에 원본 데이터를 매핑하거나, 테이블 형식/계층 구조 데이터의 모양을 변경할 수 있습니다. 복사 작업:

1. 원본에서 데이터를 읽고 원본 스키마를 결정합니다.
2. 정의된 매핑을 적용합니다.
3. 싱크에 데이터를 씁니다.

다음에 대해 자세히 알아보세요.

- [테이블 형식 원본에서 테이블 형식 싱크로](#tabular-source-to-tabular-sink)
- [계층 구조 원본에서 테이블 형식 싱크로](#hierarchical-source-to-tabular-sink)
- [테이블 형식/계층 구조 원본에서 계층 구조 싱크로](#tabularhierarchical-source-to-hierarchical-sink)

작성 UI -> 복사 작업 -> 매핑 탭 또는 복사 작업에서 프로그래밍 방식으로 매핑 지정 -> `translator` 속성에서 매핑을 구성할 수 있습니다. 다음 속성은 `translator` -> `mappings` 배열 -> 개체 -> 맵 데이터에 특정 열/필드를 가리키는 `source` 및 `sink`에서 지원됩니다.

| 속성 | Description                                                  | 필수 |
| -------- | ------------------------------------------------------------ | -------- |
| name     | 원본 또는 싱크 열/필드의 이름입니다. 테이블 형식 소스 및 싱크에 적용됩니다. | 예      |
| ordinal  | 열 인덱스입니다. 1부터 시작합니다. <br>헤더 줄이 없는 분리된 텍스트를 사용하는 경우 적용되며 필요합니다. | 예       |
| 경로     | 추출하거나 매핑할 각 필드의 JSON 경로 식입니다. 계층 구조 원본 및 싱크에 적용합니다(예: Cosmos DB, MongoDB 또는 REST 커넥터).<br>루트 개체 아래에 있는 필드의 경우 JSON 경로는 루트 `$`로 시작하고, `collectionReference` 속성으로 선택한 배열 내에 있는 필드의 경우 JSON 경로는 `$` 없이 배열 요소에서 시작합니다. | 예       |
| type     | 원본 또는 싱크 열의 중간 데이터 형식입니다. 일반적으로 이 속성을 지정하거나 변경할 필요가 없습니다. [데이터 형식 매핑](#data-type-mapping)에 대한 자세한 정보를 알아봅니다. | 예       |
| culture  | 원본 또는 싱크 열의 문화권입니다. 형식이 `Datetime` 또는 `Datetimeoffset`인 경우에 적용됩니다. 기본값은 `en-us`입니다.<br>일반적으로 이 속성을 지정하거나 변경할 필요가 없습니다. [데이터 형식 매핑](#data-type-mapping)에 대한 자세한 정보를 알아봅니다. | 예       |
| format   | 형식이 `Datetime` 또는 `Datetimeoffset`일 때 사용할 형식 문자열입니다. 날짜/시간 형식을 지정하는 방법은 [사용자 지정 날짜 및 시간 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. 일반적으로 이 속성을 지정하거나 변경할 필요가 없습니다. [데이터 형식 매핑](#data-type-mapping)에 대한 자세한 정보를 알아봅니다. | 예       |

다음은 `mappings` 외에도 `translator`에서 지원되는 속성입니다.

| 속성            | Description                                                  | 필수 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 계층 구조 원본에서 데이터를 복사하는 경우(예: Cosmos DB, MongoDB 또는 REST 커넥터)에 적용됩니다.<br>동일한 패턴으로 **배열 필드 내부** 의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. | 예       |

#### <a name="tabular-source-to-tabular-sink"></a>테이블 형식 원본에서 테이블 형식 싱크로

예를 들어 Salesforce에서 Azure SQL Database로 데이터를 복사하고 세 개의 열을 명시적으로 매핑하려면 다음을 수행합니다.

1. 복사 작업 -> 매핑 탭에서 **스키마 가져오기** 단추를 클릭하여 원본 및 싱크 스키마를 모두 가져옵니다.

2. 필요한 필드를 매핑하고 나머지를 제외/삭제합니다.

![테이블 형식에 테이블 형식 매핑](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

다음과 같이 복사 작업 페이로드에서 동일한 매핑을 구성할 수 있습니다(`translator` 참조).

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

헤더 줄이 없는 분리된 텍스트 파일에서 데이터를 복사하려면 이름 대신 열이 서수로 표시됩니다. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>계층 구조 원본에서 테이블 형식 싱크로

계층 구조 원본에서 테이블 형식 싱크로 데이터를 복사하는 경우, 복사 작업은 다음과 같은 기능을 지원합니다.

- 개체 및 배열에서 데이터를 추출합니다.
- 배열에서 동일한 패턴을 가진 여러 개체를 교차 적용합니다. 이 경우 테이블 형식 결과에서 하나의 JSON 개체를 여러 레코드로 변환하기 위한 작업입니다.

고급 계층 구조에서-테이블 형식으로 변환의 경우 [Data Flow](concepts-data-flow-overview.md)를 사용할 수 있습니다. 

예를 들면 MongoDB 문서에 다음과 같은 콘텐츠가 들어 있습니다.

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

또한 배열 *(order_pd 및 order_price)* 내 데이터를 평면화함으로써 헤더 줄을 사용하여 다음 형식으로 텍스트 파일에 복사하고 공통 루트 정보 *(숫자, 날짜, 도시)* 를 사용하여 크로스 조인하길 원합니다.

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | 시애틀 |
| 01          | 20170122  | P2       | 13          | 시애틀 |
| 01          | 20170122  | P3       | 231         | 시애틀 |

Data Factory 제작 UI에서 다음과 같은 매핑을 정의할 수 있습니다.

1. 복사 작업 -> 매핑 탭에서 **스키마 가져오기** 단추를 클릭하여 원본 및 싱크 스키마를 모두 가져옵니다. 스키마를 가져오면서 서비스가 최상위 개체를 샘플링할 때 필드가 표시되지 않는 경우, 기존 필드 이름을 마우스로 가리킨 다음 노드, 개체 또는 배열을 추가하도록 선택하여 이를 계층 구조 내에서 올바른 레이어에 추가할 수 있습니다.

2. 데이터를 반복하고 추출하려는 배열을 선택합니다. 이는 자동으로 **컬렉션 참조** 로 채워집니다. 이러한 작업에는 단일 배열만 지원된다는 점에 유의하세요.

3. 필요한 필드를 싱크에 매핑합니다. 이 서비스는 계층 구조 쪽에 해당하는 JSON 경로를 자동으로 결정합니다.

> [!NOTE]
> 컬렉션 참조로 표시된 배열이 비어 있고 확인란이 선택된 레코드의 경우 전체 레코드를 건너뜁니다.

![UI를 사용하여 테이블 형식에 계층 구조 매핑](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

**고급 편집기** 로 전환할 수도 있습니다. 이 경우 필드의 JSON 경로를 직접 보고 편집할 수 있습니다. 이 보기에서 새 매핑을 추가하도록 선택한 경우 JSON 경로를 지정합니다.

![고급 편집기를 사용하여 테이블 형식에 계층 구조 매핑](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

다음과 같이 복사 작업 페이로드에서 동일한 매핑을 구성할 수 있습니다(`translator` 참조).


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>테이블 형식/계층 구조 원본에서 계층 구조 싱크로

사용자 환경 흐름은 [계층 구조 원본에서 테이블 형식 싱크로](#hierarchical-source-to-tabular-sink)와 유사합니다. 

테이블 형식 원본에서 계층 구조 싱크로 데이터를 복사하는 경우, 개체 안의 배열에 쓰는 것은 지원되지 않습니다.

계층 구조 원본에서 계층 구조 싱크로 데이터를 복사하는 경우, 개체/배열을 선택하고 내부 필드를 건드리지 않고 싱크로 매핑하여 전체 레이어의 계층 구조를 추가로 유지할 수 있습니다.

고급 데이터 변경 변환을 위해 [Data Flow](concepts-data-flow-overview.md)를 사용할 수 있습니다. 

### <a name="parameterize-mapping"></a>매핑 매개 변수화

템플릿화된 파이프라인을 만들어 많은 수의 개체를 동적으로 복사하려면 [기본 매핑](#default-mapping)을 활용할 수 있는지 또는 각 개체에 대한 [명시적 매핑](#explicit-mapping)을 정의해야 하는지 여부를 결정합니다.

명시적 매핑이 필요한 경우 다음을 수행할 수 있습니다.

1. 파이프라인 수준에서 개체 형식을 사용하여 매개 변수를 정의합니다. 예를 들어, `mapping`.

2. 매핑 매개 변수화: 복사 작업 -> 매핑 탭에서 동적 콘텐츠 추가를 선택하고 위의 매개 변수를 선택합니다. 활동 페이로드는 다음과 같습니다.

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. 매핑 매개 변수에 전달할 값을 생성합니다. 이 클래스는 `translator` 정의의 전체 개체여야 합니다. [명시적 매핑](#explicit-mapping) 섹션의 샘플을 참조하세요. 예를 들어 테이블 형식 원본에서 테이블 형식 싱크로 복사하는 경우, 값은 `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}`이어야 합니다.

## <a name="data-type-mapping"></a>데이터 형식 매핑

복사 작업은 다음 흐름에 따라 원본 형식에서 싱크 형식으로 매핑합니다. 

1. 원본 원시 데이터 형식에서 Azure Data Factory 및 Synapse 파이프라인에서 사용하는 중간 데이터 형식으로 변환합니다.
2. [기본 매핑](#default-mapping)과 [명시적 매핑](#explicit-mapping)에 적용되는 해당 싱크 형식을 일치시키는 데 필요한 대로 중간 데이터 형식을 자동으로 변환합니다.
3. 중간 데이터 형식을 싱크 원시 데이터 형식으로 변환합니다.

복사 작업은 현재 부울, 바이트, 바이트 배열, 날짜/시간, DatetimeOffset, Decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, 문자열, Timespan, UInt16, UInt32, UInt64와 같은 중간 데이터 형식을 지원합니다.

원본에서 싱크로의 중간 형식 간에는 다음과 같은 데이터 형식 변환이 지원됩니다.

| 원본\싱크 | 부울 | 바이트 배열 | Decimal | 날짜/시간 <small>(1)</small> | 부동 소수점 <small>(2)</small> | GUID | 정수 <small>(3)</small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| 부울     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 바이트 배열  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| 날짜/시간   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 부동 소수점 | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| 정수     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) 날짜/시간에는 날짜/시간 및 DateTimeOffset이 포함됩니다.

(2) 부동 소수점에는 Single 및 Double이 포함됩니다.

(3) 정수에는 SByte, Byte, Int16, UInt16, Int32, UInt32, Int64, UInt64가 포함됩니다.

> [!NOTE]
> - 현재 이러한 데이터 형식 변환은 표 형식 데이터 간에 복사할 때 지원됩니다. 계층 구조 원본/싱크는 지원되지 않습니다. 즉, 원본 및 싱크 중간 형식 간에 시스템에서 정의된 데이터 형식 변환이 없습니다.
> - 이 기능은 최신 데이터 세트 모델에서 작동합니다. UI에서 이 옵션이 표시되지 않으면 새 데이터 세트를 만들어 보세요.

다음은 데이터 형식 변환에 대한 복사 작업에서 지원되는 속성입니다(프로그램 방식으로 제작의 `translator` 섹션 아래).

| 속성                         | Description                                                  | 필수 |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | 새 데이터 형식 변환 환경을 사용하도록 설정합니다. <br>이전 버전과의 호환성으로 인해 기본값은 false입니다.<br><br>2020년 6월 하순 이후 Data Factory 제작 UI를 통해 만들어진 새 복사 작업의 경우, 해당 데이터 형식 변환은 기본적으로 최상의 환경에서 사용하도록 설정되며, 해당 시나리오에 대한 복사 작업 -> 매핑 탭에서 다음과 같은 형식 변환 설정을 확인할 수 있습니다. <br>파이프라인을 프로그래밍 방식으로 만들려면 `typeConversion` 속성을 true로 명시적으로 설정하여 사용하도록 설정해야 합니다.<br>이 기능을 릴리스하기 전에 만든 기존 복사 작업의 경우 이전 버전과의 호환성을 위해 작성 UI에 대한 형식 변환 옵션이 표시되지 않습니다. | 예       |
| typeConversionSettings           | 형식 변환 설정 그룹입니다. `typeConversion`이 `true`로 설정된 경우에 적용됩니다. 다음 속성은 모두 이 그룹 아래에 있습니다. | 예       |
| *`typeConversionSettings` 아래* |                                                              |          |
| allowDataTruncation              | 복사 중에 원본 데이터를 다른 유형의 싱크로 변환할 때 데이터 잘림을 허용합니다(예: decimal에서 정수로, DatetimeOffset에서 날짜/시간으로). <br>기본값은 true입니다. | 예       |
| treatBooleanAsNumber             | 부울을 숫자로 처리합니다(예: true를 1로).<br>기본값은 false입니다. | 예       |
| dateTimeFormat                   | 표준 시간대 오프셋과 문자열이 없는 날짜 사이를 변환할 때의 형식 문자열입니다(예: `yyyy-MM-dd HH:mm:ss.fff`).  자세한 내용은 [사용자 지정 날짜 및 시간 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. | 예       |
| dateTimeOffsetFormat             | 표준 시간대 오프셋과 문자열이 있는 날짜 사이를 변환할 때의 형식 문자열입니다(예: `yyyy-MM-dd HH:mm:ss.fff zzz`).  자세한 내용은 [사용자 지정 날짜 및 시간 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. | 예       |
| timeSpanFormat                   | 기간과 문자열 사이를 변환할 때의 형식 문자열입니다(예: `dd\.hh\:mm`). 자세한 내용은 [사용자 지정 TimeSpan 형식 문자열](/dotnet/standard/base-types/custom-timespan-format-strings)을 참조하세요. | 예       |
| culture                          | 형식을 변환할 때 사용되는 문화권 정보입니다(예: `en-us` 또는 `fr-fr`). | 예       |

**예:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>레거시 모델

> [!NOTE]
> 원본 열/필드를 싱크로 매핑하기 위한 다음 모델은 이전 버전과의 호환성을 위해 계속 지원됩니다. [스키마 매핑](#schema-mapping)에서 언급된 새 모델을 사용하는 것이 좋습니다. 새 모델을 생성하도록 작성 UI가 전환되었습니다.

### <a name="alternative-column-mapping-legacy-model"></a>대체 열 매핑(레거시 모델)

테이블 형식 모양의 데이터 사이에 매핑하기 위해 복사 작업 -> `translator` -> `columnMappings`을 지정할 수 있습니다. 이 경우 입력 및 출력 데이터 세트 모두에 “구조체” 섹션이 필요합니다. 열 매핑은 **원본 데이터 세트 "structure"의 전체 열 또는 하위 집합을 싱크 데이터 세트 "structure"** 의 모든 열에 매핑하는 것을 지원합니다. 다음은 예외가 발생하는 오류 조건입니다.

- 원본 데이터 저장소 쿼리 결과에 입력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
- 싱크 데이터 저장소(미리 정의된 스키마가 있는 경우)에 출력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
- 매핑에서 지정한 것보다 싱크 데이터 세트의 "structure"에 열이 더 많거나 적습니다.
- 중복 매핑

이 예제에서 입력 데이터 세트에는 구조체가 있고 그 구조체는 온-프레미스 Oracle 데이터베이스에 있는 테이블을 가리킵니다.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

이 샘플에서 출력 데이터 세트에는 구조체가 있고 그 구조체는 Salesfoce에 있는 테이블을 가리킵니다.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

다음 JSON은 파이프라인으로 복사 작업을 정의합니다. 매핑된 원본의 열에서 **translator** -> **columnMappings** 속성을 사용한 싱크 내 열까지입니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

`"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 구문을 사용하여 열 매핑을 지정하면 있는 그대로 지원됩니다.

### <a name="alternative-schema-mapping-legacy-model"></a>대체 스키마 매핑(레거시 모델)

계층 구조 모양의 데이터 및 테이블 형식 모양의 데이터 사이에 매핑을 하기 위해 복사 작업 -> `translator` -> `schemaMapping`을 지정할 수 있습니다(예: MongoDB/REST에서 텍스트 파일로 복사 및 Oracle에서 MongoDB에 대한 Azure Cosmos DB의 API로 복사) 다음 속성은 복사 작업 `translator` 섹션에서 지원됩니다.

| 속성            | Description                                                  | 필수 |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | 복사 작업 번역기의 형식 속성은 **TabularTranslator** 로 설정되어 있어야 합니다. | 예      |
| schemaMapping       | **원본 쪽에서 싱크 쪽으로** 의 매핑 관계를 보여 주는 키-값 쌍의 컬렉션입니다.<br/>- **키:** 원본을 나타냅니다. **테이블 형식 원본** 의 경우, 데이터 세트 구조에 정의된 열 이름을 지정합니다. **계층 구조 원본** 의 경우, 추출 및 매핑할 각 필드에 대한 JSON 경로 식을 지정합니다.<br>- **값:** 싱크를 나타냅니다. **테이블 형식 싱크** 의 경우, 데이터 집합 구조에 정의된 열 이름을 지정합니다. **계층 구조 싱크** 의 경우, 추출 및 매핑할 각 필드에 대한 JSON 경로 식을 지정합니다. <br>계층 구조 데이터의 경우 루트 개체 아래의 필드에 대한 JSON 경로는 root $로 시작합니다. `collectionReference` 속성에 의해 선택된 배열 내에 있는 필드의 경우 JSON 경로는 배열 요소에서 시작합니다. | 예      |
| collectionReference | 동일한 패턴으로 **배열 필드 내부** 의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. 이 속성은 계층적 데이터가 원본인 경우에만 지원됩니다. | 예       |

**예: MongoDB에서 Oracle로 복사합니다.**

예를 들어 MongoDB 문서에 다음과 같은 콘텐츠가 들어 있는 경우:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

그리고 *(order_pd 및 order_price)* 배열 내부의 데이터를 평면화하고 일반적인 루트 정보 *(number, date 및 city)* 로 크로스 조인하여 Azure SQL 테이블에 데이터를 다음 형식으로 복사하려는 경우:

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | 시애틀 |
| 01          | 20170122  | P2       | 13          | 시애틀 |
| 01          | 20170122  | P3       | 231         | 시애틀 |

다음 복사 작업 JSON 샘플처럼 스키마-매핑 규칙을 구성합니다.

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
