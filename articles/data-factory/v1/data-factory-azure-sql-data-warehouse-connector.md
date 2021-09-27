---
title: Azure Synapse Analytics에(서) 데이터 복사
description: Azure Data Factory를 사용하여 Azure Synapse Analytics에(서) 데이터를 복사하는 방법을 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 489c4ee4c48c0d38d46d4ce3b73d820261f65616
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653416"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Synapse Analytics에(서) 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [버전 2(현재 버전)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Azure Synapse Analytics 커넥터(V2)](../connector-azure-sql-data-warehouse.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Synapse Analytics에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

> [!TIP]
> 최상의 성능을 얻으려면 PolyBase를 사용하여 Azure Synapse Analytics에 데이터를 로드합니다. 자세한 내용은 [PolyBase를 사용하여 Azure Synapse Analytics에 데이터 로드](#use-polybase-to-load-data-into-azure-synapse-analytics) 섹션을 참조하세요. 사용 사례가 있는 연습은 [Azure Data Factory를 통해 Azure Synapse Analytics에 15분 이내 1TB 로드](data-factory-load-sql-data-warehouse.md)를 참조하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오
**Azure Synapse Analytics** 에서 다음 데이터 저장소로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sinks](includes/data-factory-supported-sinks.md)]

다음 데이터 저장소에서 **Azure Synapse Analytics** 로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sources](includes/data-factory-supported-sources.md)]

> [!TIP]
> SQL Server 또는 Azure SQL Database에서 Azure Synapse Analytics로 데이터를 복사할 때 대상 저장소에 테이블이 없으면 Data Factory가 원본 데이터 저장소에 있는 테이블의 스키마를 사용하여 자동으로 Azure Synapse Analytics에 테이블을 만들 수 있습니다. 자세한 내용은 [자동 테이블 만들기](#auto-table-creation)를 참조하세요.

## <a name="supported-authentication-type"></a>지원되는 인증 유형
Azure Synapse Analytics 커넥터는 기본 인증을 지원합니다.

## <a name="getting-started"></a>시작
복사 작업을 사용하여 다양한 도구/API를 통해 Azure Synapse Analytics에서 데이터를 이동하는 파이프라인을 만들 수 있습니다.

Azure Synapse Analytics에(서) 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 작성하는 빠른 연습은 [자습서: Data Factory를 통해 Azure Synapse Analytics에 데이터 로드](../load-azure-sql-data-warehouse.md)를 참조하세요.

또한 **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API**, **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. **데이터 팩터리** 를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 
2. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다. 예를 들어 Azure Blob Storage에서 Azure Synapse Analytics로 데이터를 복사하는 경우 Azure Storage 계정 및 Azure Synapse Analytics를 데이터 팩터리에 연결하는 두 개의 연결된 서비스를 만듭니다. Azure Synapse Analytics와 관련된 연결된 서비스 속성은 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요. 
3. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다. 마지막 단계에서 설명한 예제에서는 입력 데이터가 포함된 BLOB 컨테이너 및 폴더를 지정하는 데이터 세트를 만듭니다. 그리고 Blob Storage에서 복사한 데이터를 포함하는 Azure Synapse Analytics의 테이블을 지정하는 또 다른 데이터 세트를 만듭니다. Azure Synapse Analytics와 관련된 데이터 세트 속성은 [데이터 세트 속성](#dataset-properties) 섹션을 참조하세요.
4. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다. 앞에서 언급한 예에서는 BlobSource를 원본으로, SqlDWSink를 복사 작업의 싱크로 사용합니다. 마찬가지로, Azure Synapse Analytics에서 Azure Blob Storage로 복사하는 경우 복사 작업에 SqlDWSource 및 BlobSink를 사용합니다. Azure Synapse Analytics와 관련된 복사 작업 속성은 [복사 작업 속성](#copy-activity-properties) 섹션을 참조하세요. 원본 또는 싱크로 데이터 저장소를 사용하는 방법에 대 한 자세한 내용을 보려면 데이터 저장소에 대한 이전 섹션의 링크를 클릭하세요.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. Azure Synapse Analytics에(서) 데이터를 복사하는 데 사용되는 데이터 팩터리 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 샘플](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) 섹션을 참조하세요.

다음 섹션에서는 Azure Synapse Analytics와 관련된 데이터 팩터리 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 표에서는 Azure Synapse Analytics 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| type |형식 속성은 **AzureSqlDW** |예 |
| connectionString |connectionString 속성에 대해 Azure Synapse Analytics 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 기본 인증만 지원됩니다. |예 |

> [!IMPORTANT]
> [Azure SQL Database 방화벽](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure)과 데이터베이스 서버를 구성하여 [Azure 서비스가 서버에 액세스할 수 있도록](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) 해야 합니다. 또한 데이터 팩터리 게이트웨이를 사용하여 온-프레미스 데이터 원본을 포함한 Azure 외부에서 Azure Synapse Analytics로 데이터를 복사하는 경우 데이터를 Azure Synapse Analytics로 보내는 컴퓨터에 대한 적절한 IP 주소 범위를 구성합니다.

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **AzureSqlDWTable** 형식의 데이터 세트에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스에서 참조하는 Azure Synapse Analytics 데이터베이스에 있는 테이블 또는 뷰의 이름입니다. |예 |

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

> [!NOTE]
> 복사 작업은 하나의 입력을 가지고 하나의 출력을 생성합니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

### <a name="sqldwsource"></a>SqlDWSource
원본이 **SqlDWSource** 형식인 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| SqlReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: select * from MyTable. |예 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. |저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |예 |

SqlDWSource에 **sqlReaderQuery** 가 지정되면 복사 작업은 데이터를 가져올 Azure Synapse Analytics 원본에 대해 이 쿼리를 실행합니다.

또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters** 를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).

sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 세트의 구조 섹션에 정의된 열은 Azure Synapse Analytics에 대해 실행할 쿼리를 빌드하는 데 사용됩니다. 예: `select column1, column2 from mytable`. 데이터 세트 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

#### <a name="sqldwsource-example"></a>SqlDWSource 예제

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**저장 프로시저 정의:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>파이프라인
**SqlDWSink** 는 다음 속성을 지원합니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. 자세한 내용은 [반복성 섹션](#repeatability-during-copy)을 참조하세요. |쿼리 문입니다. |예 |
| allowPolyBase |BULKINSERT 메커니즘 대신 PolyBase(있는 경우)를 사용할지 여부를 나타냅니다. <br/><br/> **Azure Synapse Analytics에 데이터를 로드하는 데 권장되는 방법은 PolyBase를 사용하는 것입니다.** 제약 조건 및 세부 정보는 [PolyBase를 사용하여 Azure Synapse Analytics로 데이터 로드](#use-polybase-to-load-data-into-azure-synapse-analytics) 섹션을 참조하세요. |True <br/>False(기본값) |예 |
| polyBaseSettings |**allowPolybase** 속성이 **true** 로 설정된 경우 지정될 수 있는 속성의 그룹입니다. |&nbsp; |예 |
| rejectValue |쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다. <br/><br/>**CREATE EXTERNAL TABLE(Transact-SQL)** 항목의 [인수](/sql/t-sql/statements/create-external-table-transact-sql) 섹션에 있는 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. |0(기본값), 1, 2, … |예 |
| rejectType |rejectValue 옵션을 리터럴 값 또는 백분율로 지정할지 여부를 지정합니다. |값(기본값), 백분율 |예 |
| rejectSampleValue |PolyBase가 거부된 행의 비율을 다시 계산하기 전에 검색할 행 수를 결정합니다. |1, 2, … |예. **rejectType** 이 **백분율** 인 경우 |
| useTypeDefault |PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)를 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. |True, False(기본값) |예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 |정수(행 수) |아니요(기본값: 10000) |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예제: "00:30:00"(30분). |예 |

#### <a name="sqldwsink-example"></a>SqlDWSink 예제

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>PolyBase를 사용하여 Azure Synapse Analytics에 데이터 로드
**[PolyBase](/sql/relational-databases/polybase/polybase-guide)** 를 사용하면 높은 처리량으로 대량 데이터를 Azure Synapse Analytics에 효율적으로 로드할 수 있습니다. 기본 BULKINSERT 메커니즘 대신 PolyBase를 사용하여 처리량의 증가를 확인할 수 있습니다. 자세히 비교하려면 [복사 성능 참조 번호](data-factory-copy-activity-performance.md#performance-reference)를 참조하세요. 사용 사례가 있는 연습은 [Azure Data Factory를 통해 Azure Synapse Analytics에 15분 이내 1TB 로드](data-factory-load-sql-data-warehouse.md)를 참조하세요.

* 원본 데이터가 **Azure Blob 또는 Azure Data Lake Storage** 에 있고 형식이 PolyBase와 호환될 경우 PolyBase를 사용하여 Azure Synapse Analytics로 직접 복사할 수 있습니다. 자세한 내용은 **[PolyBase를 사용하여 직접 복사](#direct-copy-using-polybase)** 를 참조하세요.
* 원본 데이터 저장소와 형식이 PolyBase에서 원래 지원되지 않는 경우 대신 **[PolyBase를 사용하여 준비한 복사본](#staged-copy-using-polybase)** 기능을 사용할 수 있습니다. 데이터를 PolyBase 호환 형식으로 자동으로 변환하고 Azure Blob Storage에 저장하여 향상된 처리량을 제공하기도 합니다. 또한 Azure Synapse Analytics에 데이터를 로드합니다.

Azure Data Factory에 대한 다음 예제와 같이 `allowPolyBase` 속성을 **true** 로 설정하여 Azure Synapse Analytics로 데이터를 복사하기 위해 PolyBase를 사용합니다. allowPolyBase를 true로 설정하면 `polyBaseSettings` 속성 그룹을 사용하여 PolyBase 특정 속성을 지정할 수 있습니다. polyBaseSettings에 사용할 수 있는 속성에 대한 세부 정보는 [SqlDWSink](#sqldwsink) 섹션을 참조하세요.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>PolyBase를 사용하여 직접 복사
Azure Synapse Analytics PolyBase는 Azure Blob 및 Azure Data Lake Storage(서비스 주체 사용)를 원본으로 직접 지원하며 특정 파일 형식 요구 사항이 적용됩니다. 원본 데이터가 이 섹션에 설명된 조건을 충족하는 경우 PolyBase를 사용하여 원본 데이터 저장소에서 Azure Synapse Analytics로 직접 복사할 수 있습니다. 그렇지 않을 경우, [PolyBase를 사용하여 준비한 복사본](#staged-copy-using-polybase)을 사용할 수 있습니다.

> [!TIP]
> Data Lake Storage에서 Azure Synapse Analytics로 데이터를 효율적으로 복사하려면 [Azure Synapse Analytics와 함께 Data Lake Storage를 사용할 경우 Azure Data Factory를 통해 데이터에서 인사이트를 더 쉽고 편리하게 얻을 수 있음](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse)에서 자세히 알아보세요.

조건을 충족하지 않는 경우 Azure 데이터 팩터리는 설정을 확인한 후 데이터 이동을 위한 BULKINSERT 메커니즘으로 자동으로 대체됩니다.

1. **원본에 연결된 서비스** 의 형식은 **AzureStorage** 또는 **서비스 주체 인증이 적용된 AzureDataLakeStore** 입니다.
2. **입력 데이터 세트** 는 **AzureBlob** 또는 **AzureDataLakeStore** 형식이고 `type` 속성의 서식 형식은 다음 구성이 포함된 **OrcFormat**, **ParquetFormat** 또는 **TextFormat** 입니다.

   1. `rowDelimiter`는 **\n** 이어야 합니다.
   2. `nullValue`가 **빈 문자열**("")으로 설정되어 있거나 `treatEmptyAsNull`이 **true** 로 설정되어 있습니다.
   3. `encodingName`이 **기본값** 인 **utf-8** 로 설정됩니다.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` 및 `skipLineCount`는 지정되지 않습니다.
   5. `compression`은 **no compression**, **GZip** 또는 **Deflate** 일 수 있습니다.

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. 파이프라인에서 복사 작업에 대한 **BlobSource** 또는 **AzureDataLakeStore** 에는 `skipHeaderLineCount` 설정이 없습니다.
4. 파이프라인에서 복사 작업에 대한 **SqlDWSink** 에는 `sliceIdentifierColumnName` 설정이 없습니다. (PolyBase는 한 번의 실행으로 모든 데이터를 업데이트하거나 아무 것도 업데이트하지 않도록 보장합니다. **반복성** 을 달성하려면 `sqlWriterCleanupScript`를 사용할 수 있습니다.
5. `columnMapping`은 연결된 복사 작업에서 사용되지 않습니다.

### <a name="staged-copy-using-polybase"></a>PolyBase를 사용하여 준비한 복사본
원본 데이터가 이전 섹션에서 도입된 조건을 충족하지 않는 경우 중간 스테이징 Azure Blob Storage를 통해 데이터를 복사하도록 설정할 수 있습니다(Premium Storage일 수 없음). 이 경우 Azure Data Factory는 PolyBase의 데이터 형식 요구 사항을 충족시키기 위해 데이터에 변환을 수행한 다음 PolyBase를 사용하여 Azure Synapse Analytics로 데이터를 로드하고 마지막으로 Blob Storage에서 임시 데이터를 삭제합니다. 스테이징 Azure Blob을 통해 데이터를 복사하는 방법에 대한 자세한 내용은 [준비된 복사](data-factory-copy-activity-performance.md#staged-copy)를 참조하세요.

> [!NOTE]
> PolyBase와 스테이징을 사용하여 온-프레미스 데이터 저장소에서 Azure Synapse Analytics로 데이터를 복사할 때 데이터 관리 게이트웨이 버전이 2.4 미만이면 원본 데이터를 적절한 형식으로 변환하는 데 사용되는 JRE(Java Runtime Environment)가 게이트웨이 컴퓨터에 필요합니다. 이러한 종속성을 방지하려면 게이트웨이를 최신으로 업그레이드하는 것이 좋습니다.
>

이 기능을 사용하려면 중간 Blob Storage가 있는 Azure Storage 계정을 나타내는 [Azure Storage 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service)를 만든 후 다음 코드에 표시된 복사 작업에 대해 `enableStaging` 및 `stagingSettings` 속성을 지정합니다.

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>PolyBase를 사용하는 경우 모범 사례
다음 섹션에서는 [Azure Synapse Analytics 모범 사례](../../synapse-analytics/sql/best-practices-dedicated-sql-pool.md)에 나와 있는 추가 모범 사례를 제공합니다.

### <a name="required-database-permission"></a>필수 데이터베이스 권한
PolyBase를 사용하려면 Azure Synapse Analytics에 데이터를 로드하는 데 사용되는 사용자에게 대상 데이터베이스에 대한 ['CONTROL' 권한](/sql/relational-databases/security/permissions-database-engine)이 필요합니다. 이를 달성하기 위한 한 가지 방법으로 해당 사용자를 "db_owner" 역할의 구성원으로 추가합니다. 이를 수행하는 방법에 대해서는 [이 섹션](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)을 참조하세요.

### <a name="row-size-and-data-type-limitation"></a>행 크기 및 데이터 형식 제한 사항
Polybase 부하는 **1MB** 보다 작고 VARCHR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)로 로드할 수 없는 행을 로드할 때만 사용됩니다. [여기](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)를 참조합니다.

1MB보다 큰 크기의 행을 포함한 원본 데이터가 있는 경우 각 항목의 최대 행 크기가 제한을 초과하지 않는 정도의 작은 테이블로 원본 테이블을 수직으로 분할하는 것이 좋습니다. 더 작은 테이블은 PolyBase를 사용하여 로드하고 Azure Synapse Analytics에 병합할 수 있습니다.

### <a name="azure-synapse-analytics-resource-class"></a>Azure Synapse Analytics 리소스 클래스
가능한 최상의 처리량을 달성하려면 PolyBase를 통해 Azure Synapse Analytics에 데이터를 로드하는 데 사용되는 사용자에게 더 큰 리소스 클래스를 할당하는 것이 좋습니다. [사용자 리소스 클래스 변경 예제](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)에 따라 이 작업을 하는 방법을 알아보세요.

### <a name="tablename-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 tableName
다음 테이블은 스키마와 테이블 이름의 다양한 조합에 대한 JSON 데이터 세트에서 **tableName** 속성을 지정하는 방법에 대한 예제를 제공합니다.

| DB 스키마 | 테이블 이름 | tableName JSON 속성 |
| --- | --- | --- |
| dbo |MyTable |MyTable 또는 dbo.MyTable 또는 [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable 또는 [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] 또는 [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

다음 오류가 표시하는 경우 tableName 속성에 지정한 값에 관한 문제일 수 있습니다. tableName JSON 속성에 대한 값을 지정하는 올바른 방법은 테이블을 참조하세요.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>기본값이 있는 열
현재 데이터 팩터리의 PolyBase 기능은 대상 테이블과 동일한 열 수를 허용합니다. 가령 네 개의 열이 있고 그 중 한 열이 기본 값으로 정의된 테이블이 있다고 합시다. 입력 데이터는 여전히 네 개의 열을 포함해야 합니다. 3열 입력 데이터 세트를 제공하면 다음 메시지와 비슷한 오류가 발생합니다.

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL 값은 특별한 형태의 기본값입니다. 열이 null을 허용하면 해당 열에 대한 입력 데이터(Blob)는 비어 있을 수 있습니다(입력 데이터 세트에서 누락될 수 없음). PolyBase는 Azure Synapse Analytics의 해당 항목에 NULL을 삽입합니다.

## <a name="auto-table-creation"></a>자동 테이블 만들기
복사 마법사를 사용하여 SQL Server 또는 Azure SQL Database에서 Azure Synapse Analytics로 데이터를 복사하는 경우 원본 테이블에 해당하는 테이블이 대상 저장소에 없으면 Data Factory는 원본 테이블 스키마를 사용하여 데이터 웨어하우스에 테이블을 자동으로 만들 수 있습니다.

Data Factory는 원본 데이터 저장소와 동일한 테이블 이름으로 대상 저장소에 테이블을 만듭니다. 열에 대한 데이터 형식은 다음 형식 매핑을 기반으로 선택됩니다. 필요한 경우 원본과 대상 저장소 사이의 비호환성을 해결하기 위해 형식 변환을 수행합니다. 라운드 로빈 테이블 배포를 사용하기도 합니다.

| 원본 SQL Database 열 형식 | 대상 Azure Synapse Analytics 열 유형(크기 제한) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bit | bit |
| Decimal | Decimal |
| 숫자 | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| 이진 | 이진 |
| Varbinary | Varbinary(최대 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| 시간 | 시간 |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| 텍스트 | Varchar(최대 8000) |
| NText | NVarChar(최대 4000) |
| 이미지 | VarBinary(최대 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar(최대 8000) |
| NVarChar | NVarChar(최대 4000) |
| Xml | Varchar(최대 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Azure Synapse Analytics에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Azure Synapse Analytics 간에 데이터를 이동할 때는 다음과 같은 매핑이 SQL 형식에서 .NET 형식으로 또는 그 반대로 사용됩니다.

매핑은 [ADO.NET에 대한 SQL Server 데이터 형식 매핑](/dotnet/framework/data/adonet/sql-server-data-type-mappings)과 같습니다.

| SQL Server 데이터베이스 엔진 형식 | .NET Framework 형식 |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |부울 |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 특성(varbinary(max)) |Byte[] |
| Float |Double |
| 이미지 |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |개체 * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |xml |

복사 작업 정의에서 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>Azure Synapse Analytics에(서) 데이터를 복사하는 JSON 예제
다음 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure Synapse Analytics 및 Azure Blob Storage에(서) 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 **여기** 에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>예제: Azure Synapse Analytics에서 Azure Blob으로 데이터 복사
샘플이 다음 데이터 팩터리 엔터티를 정의합니다.

1. [AzureSqlDW](#linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [AzureSqlDWTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
5. [SqlDWSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 샘플은 Azure Synapse Analytics의 테이블에서 Blob으로 시계열 데이터(매시간, 매일 등)를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure Synapse Analytics 연결된 서비스:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob Storage 연결된 서비스:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Synapse Analytics 입력 데이터 세트:**

이 샘플은 Azure Synapse Analytics에서 만든 테이블 'MyTable'에 시계열 데이터에 대한 'timestampcolumn'이라는 열이 포함되었다고 가정합니다.

"external": "true" 설정은 데이터 세트가 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실을 Data Factory 서비스에 전달합니다.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Blob 출력 데이터 세트:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**SqlDWSource 및 BlobSink를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **SqlDWSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> 예에서 **sqlReaderQuery** 는 SqlDWSource에 지정됩니다. 복사 작업은 데이터를 가져올 Azure Synapse Analytics 원본에 대해 이 쿼리를 실행합니다.
>
> 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters** 를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).
>
> sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 세트의 구조 섹션에 정의된 열은 Azure Synapse Analytics에 대해 실행할 쿼리를 작성하는 데 사용됩니다(mytable에서 column1, column2 선택). 데이터 세트 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>예제: Azure Blob에서 Azure Synapse Analytics로 데이터 복사
샘플이 다음 데이터 팩터리 엔터티를 정의합니다.

1. [AzureSqlDW](#linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [AzureSqlDWTable](#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)입니다.
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 및 [SqlDWSink](#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 샘플은 Azure Blob에서 Azure Synapse Analytics 데이터베이스의 테이블로 시계열 데이터(매시간, 매일 등)를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure Synapse Analytics 연결된 서비스:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob Storage 연결된 서비스:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Blob 입력 데이터 세트:**

데이터는 매시간 새 blob에 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 Data Factory 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Synapse Analytics 출력 데이터 세트:**

이 샘플은 Azure Synapse Analytics의 'MyTable'이라는 테이블에 데이터를 복사합니다. Blob CSV 파일을 포함하려고 하므로 같은 수의 열을 사용하여 Azure Synapse Analytics의 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**BlobSource 및 SqlDWSink를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **BlobSource** 로 설정되고 **sink** 형식은 **SqlDWSink** 로 설정됩니다.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
연습을 진행하려면 Azure Synapse Analytics 설명서에서 [Azure Data Factory를 사용하여 15분 내에 Azure Synapse Analytics에 1TB 로드](data-factory-load-sql-data-warehouse.md) 및 [Azure Data Factory를 사용하여 데이터 로드](../load-azure-sql-data-warehouse.md) 문서를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.