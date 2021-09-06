---
title: 매핑 데이터 흐름의 원본 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인의 매핑 데이터 흐름에서 원본 변환을 설정하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/27/2021
ms.openlocfilehash: ef2db7e11666c104215b29f298882cfe77631310
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529585"
---
# <a name="source-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 원본 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

원본 변환에서는 데이터 흐름의 데이터 원본을 구성합니다. 데이터 흐름을 설계할 때 첫 번째 단계는 항상 원본 변환을 구성하는 것입니다. 원본을 추가하려면 데이터 흐름 캔버스에서 **원본 추가** 상자를 선택합니다.

모든 데이터 흐름에는 하나 이상의 원본 변환이 필요하지만 데이터 흐름을 완료하는 데 필요한 수만큼의 원본을 추가할 수 있습니다. 조인, 조회 또는 공용 구조체 변환을 사용하여 이러한 원본을 함께 조인할 수 있습니다.

각 원본 변환은 정확히 하나의 데이터 세트 또는 연결된 서비스와 연결됩니다. 데이터 세트는 작성하거나 읽어 들이려는 데이터의 형식과 위치를 결정합니다. 파일 기반 데이터 세트를 사용하는 경우, 원본의 와일드카드 및 파일 목록을 사용하여 한 번에 두 개 이상의 파일을 처리할 수 있습니다.

## <a name="inline-datasets"></a>인라인 데이터 세트

원본 변환을 만들 때 처음으로 결정해야 할 사항은 원본 정보를 데이터 세트 개체 내에 정의할지, 아니면 원본 변환 내에 정의할지입니다. 대부분의 형식은 한 가지만 선택할 수 있습니다. 특정 커넥터를 사용하는 방법을 알아보려면 해당 커넥터 문서를 참조하세요.

특정 형식이 인라인 및 데이터 세트 개체 모두에 지원되는 경우 두 가지 다 이점이 있습니다. 데이터 세트 개체는 다른 데이터 흐름 및 복사와 같은 작업에서 사용될 수 있는 재사용 가능한 엔터티입니다. 이러한 재사용 가능한 엔터티는 강화된 스키마를 사용할 때 특히 유용합니다. 데이터 세트는 Spark를 기반으로 하지 않습니다. 경우에 따라 원본 변환에서 특정 설정 또는 스키마 프로젝션을 재정의해야 할 수도 있습니다.

유연한 스키마, 일회용 원본 인스턴스 또는 매개 변수가 있는 원본을 사용하는 경우 인라인 데이터 세트를 사용하는 것이 좋습니다. 원본에 매개 변수가 많은 경우 인라인 데이터 세트를 사용하여 "더미" 개체를 만들 수 없습니다. 인라인 데이터 세트는 Spark를 기반으로 하며 해당 속성은 데이터 흐름의 기본입니다.

인라인 데이터 세트를 사용하려면 **원본 형식** 선택기에서 원하는 형식을 선택합니다. 원본 데이터 세트를 선택하는 대신, 연결하려는 연결된 서비스를 선택합니다.

![선택된 인라인을 보여 주는 스크린샷.](media/data-flow/inline-selector.png "선택된 인라인을 보여 주는 스크린샷.")

## <a name="workspace-db-synapse-workspaces-only"></a>작업 영역 DB(Synapse 작업 영역에만 해당)

Azure Synapse 작업 영역에서 ```Workspace DB```라는 데이터 흐름 원본 변환에 추가 옵션이 있습니다. 이렇게 하면 추가 연결된 서비스 또는 데이터 세트 없이도 사용 가능한 모든 유형의 작업 영역 데이터베이스를 원본 데이터로 직접 선택할 수 있습니다.

> [!NOTE]
> Azure Synapse 작업 영역 DB 커넥터는 현재 공개 미리 보기로 제공되며 지금은 Spark Lake 데이터베이스와만 사용할 수 있습니다.

![선택한 workspacedb를 보여 주는 스크린샷](media/data-flow/syms-source.png "선택한 작업 영역 DB를 보여 주는 스크린샷")

##  <a name="supported-source-types"></a><a name="supported-sources"></a>지원되는 원본 유형

매핑 데이터 흐름은 ELT(추출, 로드, 변환) 접근 방식을 따르며 모두 Azure에 있는 *준비* 데이터 세트와 함께 작동합니다. 현재 원본 변환에서 다음 데이터 세트를 사용할 수 있습니다.

| 커넥터 | 서식 | 데이터 세트/인라인 |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)<br>[델타](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓ |
| [Azure Cosmos DB(SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[공통 데이터 모델](format-common-data-model.md#source-properties)<br>[구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)<br>[델타](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br/>-/✓<br>✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/✓<br/>✓/✓<br>✓/✓ |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/✓ |
| [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/✓ |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |
| [SQL Server](connector-sql-server.md) | | ✓/✓ |

이러한 커넥터와 관련된 설정은 **원본 옵션** 탭에 있습니다. 이러한 설정에 대한 정보 및 데이터 흐름 스크립트 예는 커넥터 설명서에 있습니다.

Azure Data Factory 및 Synapse 파이프라인은 [90개 넘는 기본 커넥터](connector-overview.md)에 액세스할 수 있습니다. 다른 원본의 데이터를 데이터 흐름에 포함하려면 복사 작업을 사용하여 지원되는 준비 영역 중 하나에 해당 데이터를 로드합니다.

## <a name="source-settings"></a>원본 설정

원본을 추가한 후에는 **원본 설정** 탭을 통해 구성합니다. 여기서 원본이 가리키는 데이터 세트를 선택하거나 만들 수 있습니다. 또한 데이터의 스키마 및 샘플링 옵션을 선택할 수도 있습니다.

데이터 세트 매개 변수에 대한 개발 값은 [디버그 설정](concepts-data-flow-debug-mode.md)에서 구성할 수 있습니다. (디버그 모드가 켜져 있어야 합니다.)

![원본 설정 탭을 보여 주는 스크린샷.](media/data-flow/source1.png "원본 설정 탭을 보여 주는 스크린샷.")

**출력 스트림 이름**: 원본 변환의 이름입니다.

**원본 형식**: 인라인 데이터 세트를 사용할지, 기존 데이터 세트 개체를 사용할지 선택합니다.

**연결 테스트**: 데이터 흐름의 Spark 서비스가 원본 데이터 세트에 사용된 연결된 서비스에 성공적으로 연결할 수 있는지 테스트합니다. 이 기능을 사용하려면 디버그 모드가 켜져 있어야 합니다.

**스키마 드리프트**: [스키마 드리프트](concepts-data-flow-schema-drift.md)는 열 변경 내용을 명시적으로 정의할 필요 없이 데이터 흐름에서 유연한 스키마를 기본적으로 처리하는 서비스의 기능입니다.

* 원본 열이 자주 변경되는 경우 **스키마 드리프트 허용** 확인란을 선택합니다. 이 설정을 사용하면 들어오는 모든 원본 필드가 변환을 통해 싱크로 이동할 수 있습니다.

* **드리프트 열 형식 유추** 를 선택하면 서비스가 검색된 각 새 열의 데이터 형식을 검색하고 정의하라는 지시를 받습니다. 이 기능을 끄면 모든 드리프트 열이 문자열 형식이 됩니다.

**스키마 유효성 검사:** **스키마 유효성 검사** 를 선택하면 들어오는 원본 데이터가 데이터 세트의 정의된 스키마와 일치하지 않는 경우 데이터 흐름이 실행되지 않습니다.

**건너뛸 줄 수**: **건너뛸 줄 수** 필드는 데이터 세트의 시작 부분에서 무시할 줄 수를 지정합니다.

**샘플링**: 원본의 행 수를 제한하려면 **샘플링** 을 사용하도록 설정합니다. 디버깅을 위해 원본의 데이터를 테스트하거나 샘플링할 때 이 설정을 사용합니다. 이는 파이프라인에서 디버그 모드로 데이터 흐름을 실행할 때 매우 유용합니다.

원본이 올바르게 구성되었는지 확인하려면 디버그 모드를 설정하고 데이터 미리 보기를 페치합니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

> [!NOTE]
> 디버그 모드가 켜져 있으면 디버그 설정의 행 제한 구성이 데이터 미리 보기 중에 원본의 샘플링 설정을 덮어씁니다.

## <a name="source-options"></a>원본 옵션

**원본 옵션** 탭에는 선택한 커넥터 및 형식과 관련된 설정이 포함되어 있습니다. 자세한 내용과 예제는 관련 [커넥터 설명서](#supported-sources)를 참조하세요.

## <a name="projection"></a>프로젝션

데이터 세트의 스키마와 마찬가지로, 원본의 프로젝션은 원본 데이터의 데이터 열, 유형 및 형식을 정의합니다. SQL 및 Parquet와 같은 대부분의 데이터 세트 형식의 경우, 원본의 프로젝션이 데이터 세트에 정의된 스키마를 반영하도록 수정됩니다. 원본 파일이 강력한 형식이 아닌 경우(예: Parquet 파일이 아닌 플랫 .csv 파일), 원본 변환의 각 필드에 대한 데이터 형식을 정의할 수 있습니다.

![프로젝션 탭의 설정을 보여 주는 스크린샷.](media/data-flow/source3.png "프로젝션 탭의 설정을 보여 주는 스크린샷.")

텍스트 파일에 정의된 스키마가 없는 경우 **데이터 형식 검색** 을 선택하여 서비스가 데이터 형식을 샘플링하고 유추하도록 합니다. 기본 데이터 형식을 자동으로 검색하려면 **기본 형식 정의** 를 선택합니다.

**스키마 다시 설정** 은 프로젝션을 참조된 데이터 세트에 정의된 것으로 다시 설정합니다.

다운스트림 파생 열 변환에서 열 데이터 형식을 수정할 수 있습니다. 열 이름을 수정하려면 선택 변환을 사용합니다.

### <a name="import-schema"></a>스키마 가져오기

활성 디버그 클러스터를 사용하여 스키마 프로젝션을 만들려면 **프로젝션** 탭에서 **스키마 가져오기** 단추를 선택합니다. 모든 원본 형식으로 제공됩니다. 여기에서 스키마를 가져오면 데이터 세트에 정의된 프로젝션이 재정의됩니다. 데이터 세트 개체는 변경되지 않습니다.

스키마 가져오기는 데이터 세트에 스키마 정의가 존재하지 않아도 되는 복잡한 데이터 구조를 지원하는 Avro 및 Azure Cosmos DB 같은 데이터 세트에 유용합니다. 인라인 데이터 세트의 경우 스키마 드리프트 없이 열 메타데이터를 참조하는 유일한 방법은 스키마를 가져오는 것입니다.

## <a name="optimize-the-source-transformation"></a>원본 변환 최적화

**최적화** 탭에서는 각 변환 단계의 파티션 정보를 편집할 수 있습니다. 대부분의 경우 **현재 분할을 사용** 하면 원본의 이상적인 분할 구조에 맞게 최적화할 수 있습니다.

Azure SQL Database 원본에서 읽어 들이는 경우, 사용자 지정 **원본** 분할이 데이터를 가장 빠르게 읽을 가능성이 높습니다. 서비스는 데이터베이스에 대한 연결을 병렬로 만들어 대규모 쿼리를 읽습니다. 열에서 또는 쿼리를 사용하여 이 원본 분할을 수행할 수 있습니다.

![원본 파티션 설정을 보여 주는 스크린샷.](media/data-flow/sourcepart3.png "원본 파티션 설정을 보여 주는 스크린샷.")

매핑 데이터 흐름 내 최적화에 대한 자세한 내용은 [최적화 탭](concepts-data-flow-overview.md#optimize)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[파생 열 변환](data-flow-derived-column.md)과 [선택 변환](data-flow-select.md)을 사용하여 데이터 흐름 구축을 시작합니다.
