---
title: Azure SQL의 데이터 인덱싱
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 콘텐츠와 메타데이터의 인덱싱을 자동화하도록 Azure SQL 인덱서를 설정합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 57c380116f77d5f9750b727b44841fc35c1fd010
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133545562"
---
# <a name="index-data-from-azure-sql"></a>Azure SQL의 데이터 인덱싱

이 문서에서는 콘텐츠를 추출하고 Azure Cognitive Search에서 검색할 수 있도록 Azure SQL 인덱서를 구성하는 방법을 보여 줍니다. 이 워크플로는 Azure Cognitive Search에 대한 검색 인덱스를 만들고 Azure SQL Database와 Azure SQL Managed Instance에서 추출한 기존 콘텐츠와 함께 로드합니다.

이 문서에서는 [인덱서](search-indexer-overview.md) 사용 원리를 다루지만 Azure SQL Database 또는 SQL Managed Instance에서만 사용할 수 있는 기능(예: 통합 변경 내용 추적)에 대해서도 설명합니다.

다음 클라이언트를 사용하여 Azure SQL 인덱서를 설정할 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

이 문서에서는 REST API를 사용합니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 데이터는 단일 테이블이나 뷰에서 시작됩니다. 데이터가 여러 테이블에 분산된 경우 데이터에 대한 단일 뷰를 만들 수 있습니다. 뷰 사용의 단점은 증분 변경 내용으로 인덱스를 새로 고치는 데 SQL Server 통합 변경 검색을 사용할 수 없다는 것입니다. 자세한 내용은 아래의 [변경 및 삭제된 행 캡처](#CaptureChangedRows)를 참조하세요.

* 데이터 형식이 호환되어야 합니다. 검색 인덱스에서는 전부는 아니지만 대부분의 SQL 형식이 지원됩니다. 목록은 [데이터 형식 매핑](#TypeMapping)을 참조하세요.

* SQL Managed Instance에는 퍼블릭 엔드포인트를 통해 연결되어야 합니다. 자세한 내용은 [퍼블릭 엔드포인트를 통한 인덱서 연결](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)을 참조하세요.

* Azure 가상 머신에서 SQL Server에 연결하려면 보안 인증서를 수동으로 설정해야 합니다. 자세한 내용은 [Azure VM의 SQL Server에 대한 인덱서 연결](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)을 참조하세요.

실시간 데이터 동기화는 애플리케이션 요구 사항이 아니어야 합니다. 인덱서는 최대 5분마다 테이블을 다시 인덱싱할 수 있습니다. 데이터가 자주 변경되고 해당 변경 내용을 몇 초 또는 몇 분 이내에 인덱스에 반영해야 하는 경우에는 [REST API](/rest/api/searchservice/AddUpdate-or-Delete-Documents) 또는 [.NET SDK](search-get-started-dotnet.md)를 사용하여 업데이트된 행을 직접 푸시하는 것이 좋습니다.

증분 인덱싱이 가능합니다. 데이터 집합이 크고 일정에 따라 인덱서를 실행하려는 경우 Azure Cognitive Search에서 새 행, 변경된 행 및 삭제된 행을 효율적으로 식별할 수 있어야 합니다. 비-증분 인덱싱은 주문 시(일정을 따르지 않고) 인덱싱하거나 100,000 미만의 행을 인덱싱하는 경우에만 허용됩니다. 자세한 내용은 아래의 [변경 및 삭제된 행 캡처](#CaptureChangedRows)를 참조하세요.

Azure Cognitive Search는 연결 문자열에 사용자 이름과 암호가 제공되는 SQL Server 인증을 지원합니다. 또는 관리 ID를 설정하고 Azure 역할을 사용하여 연결 시 자격 증명을 생략할 수 있습니다. 자세한 내용은 [관리 ID를 사용하여 인덱서 연결 설정](search-howto-managed-identities-sql.md)을 참조하세요.

## <a name="create-an-azure-sql-indexer"></a>Azure SQL 인덱서 만들기

1. 데이터 원본을 만듭니다.

   ```http
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   연결 문자열은 다음 형식 중 하나를 따를 수 있습니다.
    1. `ADO.NET connection string` 옵션을 사용하여 [Azure Portal](https://portal.azure.com)에서 연결 문자열을 가져올 수 있습니다.
    1. 다음 형식의 계정 키를 포함하지 않는 관리 ID 연결 문자열: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;`. 이 연결 문자열을 사용하려면 [관리 ID를 사용하여 Azure SQL Database에 대한 인덱서 연결 설정](search-howto-managed-identities-sql.md) 지침을 따르세요.

2. 대상 Azure Cognitive Search 인덱스가 아직 없으면 만듭니다. [포털](https://portal.azure.com) 또는 [인덱스 만들기 API](/rest/api/searchservice/Create-Index)를 사용하여 인덱스를 만들 수 있습니다. 대상 인덱스의 스키마가 원본 테이블의 스키마와 호환되는지 확인합니다. [SQL 데이터 형식과 Azure Cognitive Search 데이터 형식 사이의 매핑](#TypeMapping)을 참조하세요.

3. 이름을 지정하고 데이터 원본 및 대상 인덱스를 참조하여 인덱서는 만듭니다.

   ```http
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

이 방법으로 만든 인덱서에는 일정이 없습니다. 만들어지면 자동으로 한 번 실행됩니다. 언제든지 **run indexer** 요청을 사용하여 다시 실행할 수 있습니다.

```http
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

인덱서 동작의 몇 가지 측면(예: 배치 크기, 인덱서 실행에 실패하기 전에 건너뛸 수 있는 문서 수)을 사용자 지정할 수 있습니다. 자세한 내용은 [인덱서 API 만들기](/rest/api/searchservice/Create-Indexer)를 참조하세요.

Azure 서비스에서 데이터베이스에 연결하도록 허용해야 할 수 있습니다. 이 작업을 수행하는 방법에 대한 지침은 [Azure에서 연결](../azure-sql/database/firewall-configure.md) 을 참조하세요.

인덱서 상태 및 실행 기록(인덱싱된 항목 수, 오류 등)을 모니터링하려면 **indexer status** 요청을 사용합니다.

```http
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

응답은 다음과 같아야 합니다.

```json
    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

실행 기록은 50개의 최근에 완료한 실행까지 포함할 수 있으며, 시간 순서의 반대로 정렬됩니다(최신 항목이 응답에서 먼저 표시됨)
응답에 대한 추가 정보는 [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)에서 확인할 수 있습니다.

## <a name="run-indexers-on-a-schedule"></a>일정에 따라 인덱서 실행

일정에 따라 주기적으로 실행되도록 인덱서를 정렬할 수도 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **schedule** 속성을 추가합니다. 아래 예제에서는 인덱서를 업데이트하는 PUT 요청을 보여 줍니다.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 일정 지정 방법](search-howto-schedule-indexers.md)을 참조하세요.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>새 행, 변경된 행 및 삭제된 행 캡처

Azure Cognitive Search는 **증분 인덱싱** 을 사용하여 전체 테이블을 다시 인덱싱하거나 인덱서가 실행될 때마다 표시될 필요가 없도록 합니다. Azure Cognitive Search는 증분 인덱싱을 지원하는 두 가지 변경 검색 정책을 제공합니다. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL 통합 변경 내용 추적 정책

SQL 데이터베이스에서 [변경 내용 추적](/sql/relational-databases/track-changes/about-change-tracking-sql-server)을 지원하는 경우 **SQL 통합 변경 내용 추적 정책** 을 사용하는 것이 좋습니다. 가장 효율적인 정책입니다. 또한 테이블에 "일시 삭제" 열을 명시적으로 추가하지 않고도 Azure Cognitive Search에서 삭제된 행을 식별할 수 있습니다.

#### <a name="requirements"></a>요구 사항 

+ 데이터베이스 버전 요구 사항:
  * SQL Server 2012 SP3 이상(Azure VM에서 SQL Server를 사용하는 경우)
  * Azure SQL Database 또는 SQL Managed Instance.
+ 테이블만(뷰 제외). 
+ 데이터베이스에서 테이블에 대해 [변경 내용 추적을 설정](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)합니다. 
+ 테이블에서 복합 기본 키(두 개 이상의 열을 포함하는 기본 키)가 없습니다.  

#### <a name="usage"></a>사용량

이 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

SQL 통합 변경 내용 추적 정책을 사용할 때는 별도의 데이터 삭제 검색 정책을 지정하지 마세요. 이 정책은 삭제된 행 식별을 기본적으로 지원합니다. 그러나 삭제가 "자동"으로 탐지되려면 검색 인덱스의 문서 키가 SQL 테이블의 기본 키와 동일해야 합니다. 

> [!NOTE]  
> SQL 테이블에서 많은 수의 행을 제거하기 위해 [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql)을 사용할 때 인덱서를 [다시 설정](/rest/api/searchservice/reset-indexer)하여 행 삭제를 선택하도록 변경 추적 상태를 다시 설정해야 합니다.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>상위 워터마크 변경 검색 정책

이 변경 검색 정책은 행이 마지막으로 업데이트된 버전 또는 시간을 캡처하는 “상위 워터 마크” 열을 사용합니다. 뷰를 사용하는 경우 상위 워터 마크 정책을 사용해야 합니다. 상위 워터 마크 열은 다음 요구 사항을 충족해야 합니다.

#### <a name="requirements"></a>요구 사항 

* 모든 삽입 시 열의 값을 지정합니다.
* 항목에 대한 모든 업데이트는 열의 값도 변경합니다.
* 삽입 또는 업데이트할 때마다 이 열의 값이 증가합니다.
* 다음 WHERE 및 ORDER BY 절이 포함된 쿼리를 효율적으로 실행할 수 있습니다. `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> 상위 워터 마크 열에는 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 데이터 형식을 사용하는 것이 좋습니다. 다른 데이터 형식을 사용하는 경우 변경 추적이 인덱서 쿼리와 동시에 실행되는 트랜잭션의 모든 변경 내용을 캡처하지는 않습니다. 읽기 전용 복제본이 있는 구성에 **rowversion** 을 사용하는 경우 주 복제본에서 인덱서를 가리켜야 합니다. 데이터 동기화 시나리오에는 주 복제본만 사용할 수 있습니다.

#### <a name="usage"></a>사용량

높은 워터 마크 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> 원본 테이블의 상위 워터 마크 열에 인덱스가 없는 경우 SQL 인덱서에서 사용하는 쿼리는 시간 초과될 수 있습니다. 특히 테이블에 행이 많은 경우 `ORDER BY [High Water Mark Column]` 절에 따라 인덱스가 효율적으로 실행돼야 합니다.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

상위 워터 마크 열에 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 데이터 형식을 사용하는 경우 `convertHighWaterMarkToRowVersion` 인덱서 구성 설정을 사용하는 것이 좋습니다. `convertHighWaterMarkToRowVersion`는 다음 두 가지 작업을 수행합니다.

* 인덱서 sql 쿼리의 상위 워터 마크 열에는 rowversion 데이터 형식을 사용합니다. 올바른 데이터 형식을 사용하면 인덱서 쿼리 성능이 향상됩니다.
* 인덱서 쿼리를 실행하기 전에 rowversion 값에서 1을 뺍니다. 1개에서 다수의 조인이 있는 뷰에 중복된 rowversion 값이 있는 행이 있을 수 있습니다. 1을 빼면 인덱서 쿼리에서 이러한 행을 누락하지 않습니다.

이 기능을 사용하도록 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

시간 초과 오류를 발생하는 경우 `queryTimeout` 인덱서 구성 설정을 사용하여 쿼리 제한 시간을 기본값(5분)보다 높은 값으로 설정할 수 있습니다. 예를 들어 제한 시간을 10분으로 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

`ORDER BY [High Water Mark Column]` 절을 사용하지 않도록 설정할 수도 있습니다. 그러나 인덱서 실행이 오류로 인해 중단된 경우 그때까지 거의 모든 행을 처리했더라도 나중에 실행될 때 모든 행을 다시 처리해야 하므로 이는 권장되지 않습니다. `ORDER BY` 절을 사용하지 않도록 설정하려면 인덱서 정의에서 `disableOrderByHighWaterMarkColumn` 설정을 사용합니다.  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Soft Delete 열 삭제 검색 정책
원본 테이블에서 행이 삭제된 경우 검색 인덱스에서도 해당 행을 삭제할 수 있습니다. SQL 통합 변경 내용 추적 정책을 사용하는 경우 이 작업이 자동으로 수행됩니다. 그러나 상위 워터 마크 변경 내용 추적 정책은 삭제된 행을 지원하지 않습니다. 그렇다면 어떻게 해야 할까요?

행이 테이블에서 실제로 제거된 경우 Azure Cognitive Search가 더 이상 존재하지 않는 레코드의 현재 상태를 유추할 방법이 없습니다.  그러나 "일시 삭제" 기술을 사용하여 테이블에서 제거하지 않고 논리적으로 행을 삭제할 수 있습니다. 테이블 또는 뷰에 열을 추가하고 이 열을 사용하여 행을 삭제된 것으로 표시합니다.

일시 삭제 기술을 사용하는 경우 데이터 원본을 만들거나 업데이트할 때 다음과 같이 일시 삭제 정책을 지정할 수 있습니다.

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**SoftDeleteMarkerValue** 는 데이터 원본의 JSON 표현에 있는 문자열 이어야 합니다. 실제 값의 문자열 표현을 사용 합니다. 예를 들어 삭제된 행이 값 1로 표시된 정수 열이 있는 경우 `"1"`을 사용합니다. 삭제된 행이 부울 true 값으로 표시된 BIT 열이 있는 경우 문자열 리터럴 `"True"` 또는 `"true"`를 사용합니다. 대소문자는 상관 없습니다.

Azure Portal에서 일시 삭제 정책을 설정 하는 경우 일시 삭제 표식 값 앞뒤에 따옴표를 추가 하지 마세요. 필드 콘텐츠는 이미 문자열로 인식 되며 자동으로 JSON 문자열로 변환 됩니다. 위의 예제에서 `1` 를 입력 `True` 하거나 `true` 포털의 필드에 입력 하면 됩니다.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>SQL과 Azure Cognitive Search 데이터 형식 사이의 매핑
| SQL 데이터 형식 | 허용되는 대상 인덱스 필드 유형 | 참고 |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric |Edm.String |Azure Cognitive Search에서는 10진 형식을 Edm.Double로 변환하면 정밀도가 떨어지기 때문에 이를 지원하지 않습니다. |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |SQL 문자열은 문자열이 JSON 배열을 나타내는 경우 Collection(Edm.String) 필드를 채우는 데 사용할 수 있습니다. `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |SRID가 4326(기본값)인 POINT 형식의 지리 인스턴스만 지원됩니다. |
| rowversion |해당 없음 |행 버전 열은 변경 내용 추적에 사용할 수 있지만 검색 인덱스에 저장할 수는 없습니다. |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types |해당 없음 |지원되지 않음 |

## <a name="configuration-settings"></a>구성 설정
SQL 인덱서는 여러 구성 설정을 노출합니다.

| 설정 | 데이터 형식 | 목적 | 기본값 |
| --- | --- | --- | --- |
| queryTimeout |문자열 |SQL 쿼리 실행의 제한 시간 설정 |5분("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |상위 워터 마크 정책에서 사용하는 SQL 쿼리에서 ORDER BY 절이 생략되도록 합니다. [상위 워터 마크 정책](#HighWaterMarkPolicy)을 참조하세요. |false |

이러한 설정은 인덱서 정의의 `parameters.configuration` 개체에 사용됩니다. 예를 들어 쿼리 제한 시간을 10분으로 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>FAQ

**Q:** Azure의 IaaS VM에서 실행되는 SQL 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

예. 그러나 검색 서비스에서 데이터베이스에 연결할 수 있도록 허용해야 합니다. 자세한 내용은 [Azure VM에서 Azure Cognitive Search 인덱서로부터 SQL Server로의 연결 구성](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)을 참조하세요.

**Q:** 온-프레미스에서 실행되는 SQL 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

직접 끌 수는 없습니다. 직접 연결은 권장되거나 지원되지 않습니다. 이렇게 하려면 데이터베이스를 인터넷 트래픽에 개방해야 하기 때문입니다. 고객은 Azure Data Factory와 같은 브리지 기술을 사용하여 이 시나리오를 성공적으로 수행했습니다. 자세한 내용은 [Azure Data Factory를 사용하여 Azure Cognitive Search 인덱스에 데이터 푸시](../data-factory/v1/data-factory-azure-search-connector.md)를 참조하세요.

**Q:** Azure의 IaaS에서 실행되는 SQL Server가 아닌 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

아니요. SQL Server가 아닌 데이터베이스에서는 인덱서를 테스트하지 않았기 때문에 이 시나리오는 지원되지 않습니다.  

**Q:** 일정에 따라 실행되는 여러 인덱서를 만들 수 있습니까?

예. 그러나 한 번에 하나의 인덱서만 실행할 수 있습니다. 여러 인덱서를 동시에 실행하려면 둘 이상의 검색 단위로 검색 서비스를 확장하는 것이 좋습니다.

**Q:** 인덱서를 실행하면 쿼리 작업이 영향을 받습니까?

예. 인덱서는 검색 서비스의 노드 중 하나에서 실행되므로 해당 노드의 리소스가 인덱싱 및 쿼리 지원 트래픽과 다른 API 요청 간에 공유됩니다. 많은 인덱싱 및 쿼리 작업을 실행하는 경우 503 오류가 자주 발생하거나 응답 시간이 증가하면 [검색 서비스를 확장](search-capacity-planning.md)하는 것이 좋습니다.

**Q: [장애 조치(failover) 클러스터](../azure-sql/database/auto-failover-group-overview.md)에서 데이터 원본으로 보조 복제본을 사용할 수 있습니까?**

경우에 따라 다릅니다. 테이블 또는 뷰의 전체 인덱싱에 대해 보조 복제본을 사용할 수 있습니다. 

증분 인덱싱의 경우 Azure Cognitive Search는 SQL 통합 변경 내용 추적 및 상위 워터 마크라는 두 가지 변경 검색 정책을 지원합니다.

읽기 전용 복제본에서 SQL Database는 통합된 변경 내용 추적을 지원하지 않습니다. 따라서 상위 워터 마크 정책을 사용해야 합니다. 

상위 워터 마크 열에는 rowversion 데이터 형식을 사용하는 것이 일반적으로 권장됩니다. 그러나 rowversion 사용 시, 읽기 전용 복제본에서는 지원되지 않는 `MIN_ACTIVE_ROWVERSION` 함수에 의존합니다. 따라서 rowversion를 사용하는 경우 인덱서가 주 복제본을 가리키도록 해야 합니다.

읽기 전용 복제본에서 rowversion을 사용하려고 하면 다음 오류가 표시됩니다. 

"변경 내용 추적에 rowversion 열을 사용하는 것은 보조(읽기 전용) 가용성 복제본에서 지원되지 않습니다. 데이터 원본을 업데이트하고 주 가용성 복제본에 대한 연결을 지정하십시오. 현재 데이터베이스 ' 업데이트 가능 여부' 속성은 'READ_ONLY’입니다.”

**Q: 상위 워터 마크 변경 내용 추적에 대체의 rowversion이 아닌 열을 사용할 수 있습니까?**

권장되지 않습니다. 신뢰할 수 있는 데이터 동기화를 위해서는 **rowversion** 만 허용됩니다. 그러나 애플리케이션 논리에 따라 다음과 같은 경우 안전할 수 있습니다.

+ 인덱서가 실행될 때 인덱싱되는 테이블에 미해결 트랜잭션이 있는지 확인할 수 있습니다(예를 들어 모든 테이블 업데이트가 일정에 따라 일괄 처리되고 Azure Cognitive Search 인덱서 일정이 테이블 업데이트 일정과 겹치지 않도록 설정됨).  

+ 모든 누락된 행을 선택하기 위해 전체 다시 인덱싱을 정기적으로 수행합니다.