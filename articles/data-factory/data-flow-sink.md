---
title: 매핑 데이터 흐름의 싱크 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: 매핑 데이터 흐름에서 싱크 변환을 구성하는 방법에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/14/2021
ms.openlocfilehash: 152445c18c6d69a9db52b4acd58b70dddcca6617
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063852"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 싱크 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

데이터 변환을 완료한 후에는 싱크 변환을 사용하여 대상 저장소에 데이터를 씁니다. 모든 데이터 흐름에는 하나 이상의 싱크 변환이 필요하지만 변환 흐름을 완료하는 데 필요한 수 만큼의 싱크에 쓸 수 있습니다. 추가 싱크에 쓰려면 새 분기 및 조건부 분할을 통해 새 스트림을 만듭니다.

각 싱크 변환은 정확히 하나의 데이터 세트 개체나 연결된 서비스와 연결됩니다. 싱크 변환은 작성하려는 데이터의 셰이프와 위치를 결정합니다.

## <a name="inline-datasets"></a>인라인 데이터 세트

싱크 변환을 만들 때 싱크 정보가 데이터 세트 개체 내부에 정의되어 있는지 또는 싱크 변환 내에 정의되어 있는지 여부를 선택합니다. 대부분의 형식은 한 가지만 선택할 수 있습니다. 특정 커넥터를 사용하는 방법을 알아보려면 해당 커넥터 문서를 참조하세요.

특정 형식이 인라인 및 데이터 세트 개체 모두에 지원되는 경우 두 가지 다 이점이 있습니다. 데이터 세트 개체는 다른 데이터 흐름 및 복사와 같은 작업에서 사용될 수 있는 재사용 가능한 엔터티입니다. 이러한 재사용 가능한 엔터티는 강화된 스키마를 사용할 때 특히 유용합니다. 데이터 세트는 Spark를 기반으로 하지 않습니다. 경우에 따라 싱크 변환에서 특정 설정 또는 스키마 프로젝션을 재정의해야 할 수도 있습니다.

유연한 스키마, 일회용 싱크 인스턴스 또는 매개 변수가 있는 싱크를 사용하는 경우 인라인 데이터 세트를 사용하는 것이 좋습니다. 싱크에 매개 변수가 많은 경우 인라인 데이터 세트를 사용하여 "더미" 개체를 만들 수 없습니다. 인라인 데이터 세트는 Spark를 기반으로 하며 해당 속성은 데이터 흐름의 기본입니다.

인라인 데이터 세트를 사용하려면 **싱크 형식** 선택기에서 원하는 형식을 선택합니다. 싱크 데이터 세트를 선택하는 대신 연결하려는 연결된 서비스를 선택합니다.

:::image type="content" source="media/data-flow/inline-selector.png" alt-text="선택된 인라인을 보여 주는 스크린샷.":::

## <a name="workspace-db-synapse-workspaces-only"></a>작업 영역 DB(Synapse 작업 영역에만 해당)

Azure Synapse 작업 영역에서 데이터 흐름을 사용하는 경우 Synapse 작업 영역 내에 있는 데이터베이스 형식으로 직접 데이터를 싱크하는 추가 옵션이 제공됩니다. 이 옵션을 사용하면 해당 데이터베이스에 대한 연결된 서비스 또는 데이터 세트를 추가할 필요성이 줄어듭니다.

> [!NOTE]
> Azure Synapse 작업 영역 DB 커넥터는 현재 퍼블릭 미리 보기로 제공되며 지금은 Spark Lake 데이터베이스에서만 사용할 수 있습니다.

:::image type="content" source="media/data-flow/syms-sink.png" alt-text="선택된 작업 영역 DB를 보여 주는 스크린샷":::

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> 지원되는 싱크 형식

매핑 데이터 흐름은 ELT(추출, 로드, 변환) 접근 방식을 따르며 Azure에 모두 있는 *준비* 데이터 세트와 작동합니다. 현재 원본 변환에서 다음 데이터 세트를 사용할 수 있습니다.

| 커넥터 | 서식 | 데이터 세트/인라인 |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties) <br>[델타](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB(SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[공통 데이터 모델](format-common-data-model.md#sink-properties)<br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties) <br>[델타](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure Data Explorer](connector-azure-data-explorer.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/✓ |
| [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |
| [SQL Server](connector-sql-server.md) | | ✓/✓ |

이러한 커넥터와 관련된 설정은 **설정** 탭에 있습니다. 이러한 설정에 대한 정보 및 데이터 흐름 스크립트 예는 커넥터 설명서에 있습니다.

이 서비스는 [90가지가 넘는 네이티브 커넥터](connector-overview.md)에 액세스할 수 있습니다. 데이터 흐름에서 다른 원본으로 데이터를 쓰려면 복사 작업을 사용하여 지원되는 싱크에서 해당 데이터를 로드합니다.

## <a name="sink-settings"></a>싱크 설정

싱크를 추가한 후에는 **싱크** 탭을 통해 구성합니다. 여기서 싱크가 쓰는 데이터 세트를 선택하거나 만들 수 있습니다. 데이터 세트 매개 변수에 대한 개발 값은 [디버그 설정](concepts-data-flow-debug-mode.md)에서 구성할 수 있습니다. (디버그 모드가 켜져 있어야 합니다.)

다음 비디오는 텍스트 구분 파일 형식에 대한 여러 싱크 옵션을 설명합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

:::image type="content" source="media/data-flow/sink-settings.png" alt-text="싱크 설정을 보여 주는 스크린샷.":::

**스키마 드리프트**: [스키마 드리프트](concepts-data-flow-schema-drift.md)는 열 변경 내용을 명시적으로 정의할 필요 없이 데이터 흐름에서 유연한 스키마를 기본적으로 처리하는 서비스의 기능입니다. **스키마 드리프트 허용** 을 사용하도록 설정하여 싱크 데이터 스키마에 정의된 내용 위에 추가 열을 씁니다.

**스키마 유효성 검사:** 스키마 유효성 검사를 선택하면 싱크 프로젝션의 열이 싱크 저장소에 없거나 데이터 형식이 일치하지 않으면 데이터 흐름이 실패합니다. 싱크 스키마가 정의된 프로젝션의 계약을 충족하도록 하려면 이 설정을 사용합니다. 데이터베이스 싱크 시나리오에서 열 이름 또는 형식이 변경되었음을 알리는 데 유용합니다.


## <a name="cache-sink"></a>캐시 싱크

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

*캐시 싱크* 는 데이터 흐름이 데이터 저장소 대신 Spark 캐시에 데이터를 쓰는 경우입니다. 매핑 데이터 흐름에서는 *캐시 조회* 를 사용하여 동일한 흐름 내에서 이 데이터를 여러 번 참조할 수 있습니다. 이 방법은 데이터를 식의 일부로 참조하지만 명시적으로 열에 조인하지 않으려는 경우에 유용합니다. 캐시 싱크가 데이터 저장소에서 최대값을 조회하고 오류 코드를 오류 메시지 데이터베이스와 일치시킬 수 있는 일반적인 예입니다. 

캐시 싱크에 쓰려면 싱크 변환을 추가하고 싱크 형식으로 **캐시** 를 선택합니다. 외부 저장소에 쓰지 않으므로 다른 싱크 형식과 달리 데이터 세트 또는 연결된 서비스를 선택할 필요가 없습니다. 

:::image type="content" source="media/data-flow/select-cache-sink.png" alt-text="캐시 싱크 선택":::

싱크 설정에서 필요에 따라 캐시 싱크의 키 열을 지정할 수 있습니다. 이는 캐시 조회에서 `lookup()` 함수를 사용할 때 일치 조건으로 사용됩니다. 키 열을 지정하는 경우 캐시 조회에 `outputs()` 함수를 사용할 수 없습니다. 캐시 조회 구문에 대해 자세히 알아보려면 [캐시된 조회](concepts-data-flow-expression-builder.md#cached-lookup)를 참조하세요.

:::image type="content" source="media/data-flow/cache-sink-key-columns.png" alt-text="캐시 싱크 키 열":::

예를 들어, `cacheExample`이라는 캐시 싱크에 `column1`의 단일 키 열을 지정하는 경우, `cacheExample#lookup()`를 호출하면 하나의 매개 변수가 캐시 싱크에서 일치시킬 행을 지정합니다. 함수는 매핑되는 각 열에 대해 하위 열이 있는 단일 복합 열을 출력합니다.

> [!NOTE]
> 캐시 싱크는 캐시 조회를 통해 참조하는 모든 변환에서 완전히 독립적인 데이터 스트림에 있어야 합니다. 또한 캐시 싱크는 작성된 첫 번째 싱크여야 합니다. 

**작업 출력에 쓰기** 캐시된 싱크는 필요에 따라 출력 데이터를 다음 파이프라인 작업의 입력에 쓸 수 있습니다. 이렇게 하면 데이터 저장소에 데이터를 유지할 필요 없이 데이터 흐름 작업에서 데이터를 빠르고 쉽게 전달할 수 있습니다.

## <a name="field-mapping"></a>필드 매핑

선택 변환과 마찬가지로 싱크의 **매핑** 탭에서 쓸 들어오는 열을 결정할 수 있습니다. 기본적으로 초안이 작성된 열을 포함한 모든 입력 열이 매핑됩니다. 이 동작을 *automapping* 이라고 합니다.

자동 매핑을 끄는 경우, 고정 열 기반 매핑 또는 규칙 기반 매핑을 추가할 수 있습니다. 규칙 기반 매핑을 추가하면 패턴 일치를 사용하여 식을 쓸 수 있습니다. 고정 매핑은 논리적 및 물리적 열 이름을 매핑합니다. 규칙 기반 매핑에 대한 자세한 내용은 [매핑 데이터 흐름의 열 패턴](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)을 참조하세요.

## <a name="custom-sink-ordering"></a>사용자 지정 싱크 순서

기본적으로 데이터는 비결정적 순서로 여러 싱크에 쓰여집니다. 변환 논리가 완료되면 실행 엔진이 데이터를 병렬로 쓰고 싱크 순서가 각 실행 마다 다를 수 있습니다. 정확한 싱크 순서를 지정하려면 데이터 흐름의 **일반** 탭에서 **사용자 지정 싱크** 를 사용하도록 설정합니다. 이렇게 설정하면 싱크가 오름차순으로 쓰여집니다.

:::image type="content" source="media/data-flow/custom-sink-ordering.png" alt-text="사용자 지정 싱크 순서를 보여 주는 스크린샷.":::

> [!NOTE]
> [캐시된 조회](./concepts-data-flow-expression-builder.md#cached-lookup)를 활용하는 경우 싱크 순서에서 캐시된 싱크가 가장 낮은 순서인 1 또는 first로 설정되어 있는지 확인합니다.

:::image type="content" source="media/data-flow/cache-2.png" alt-text="사용자 지정 싱크 순서":::

### <a name="sink-groups"></a>싱크 그룹

일련의 싱크에 동일한 순서 번호를 적용하여 싱크를 함께 그룹화할 수 있습니다. 이 서비스는 해당 싱크를 병렬로 실행할 수 있는 그룹으로 처리합니다. 병렬 실행 옵션은 파이프라인 데이터 흐름 작업에 노출됩니다.

## <a name="error-row-handling"></a>오류 행 처리

데이터베이스에 쓸 때 대상에서 설정된 제약 조건으로 인해 특정 데이터 행이 실패할 수 있습니다. 기본적으로 첫 번째 오류가 발생할 때 데이터 흐름 실행이 실패합니다. 특정 커넥터에서 **오류 발생 시 계속** 을 선택하여 개별 행에 오류가 있는 경우에도 데이터 흐름이 완료되도록 할 수 있습니다. 현재 이 기능은 Azure SQL Database와 Azure Synapse에서만 사용할 수 없습니다. 자세한 내용은 [Azure SQL DB의 오류 행 처리](connector-azure-sql-database.md#error-row-handling)를 참조하세요.

다음은 싱크 변환에서 자동으로 데이터베이스 오류 행 처리를 사용하는 방법에 대한 비디오 자습서입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>싱크의 데이터 미리 보기

디버그 모드에서 데이터 미리 보기를 페치하는 경우 싱크에 데이터가 쓰여지지 않습니다. 데이터가 어떻게 표시되는지 보여 주는 스냅샷은 반환되지만 대상에는 아무것도 쓰여지지 않습니다. 싱크에 대한 데이터 쓰기를 테스트하려면 파이프라인 캔버스에서 파이프라인 디버그를 실행합니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="example"></a>예제

다음은 싱크 변환 및 해당 데이터 흐름 스크립트의 예입니다.

```
sink(input(
        movie as integer,
        title as string,
        genres as string,
        year as integer,
        Rating as integer
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:false,
    updateable:true,
    upsertable:false,
    keys:['movie'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true,
    saveOrder: 1,
    errorHandlingOption: 'stopOnFirstError') ~> sink1
```

## <a name="next-steps"></a>다음 단계

데이터 흐름을 만들었으므로 [파이프라인에 데이터 흐름 작업](concepts-data-flow-overview.md)을 추가합니다.
