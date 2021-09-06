---
title: Azure Cosmos DB에서 데이터 인덱싱
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 Azure Cosmos DB에 저장된 데이터를 인덱싱하는 검색 인덱서를 설정합니다. 이 문서에서는 SQL 및 MongoDB API 프로토콜을 사용하여 데이터를 인덱싱하는 방법을 설명합니다.
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/14/2021
ms.openlocfilehash: 29a6041206496d7970e3ea58deed1754c062b663
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529167"
---
# <a name="index-data-from-azure-cosmos-db-using-sql-or-mongodb-apis"></a>SQL 또는 MongoDB API를 사용하여 Azure Cosmos DB에서 데이터 인덱싱

> [!IMPORTANT] 
> SQL API는 일반적으로 사용할 수 있습니다.
> MongoDB API 지원은 현재 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. [액세스를 요청](https://aka.ms/azure-cognitive-search/indexer-preview)하고 액세스가 활성화된 후 [미리 보기 REST API(2020-06-30-preview 이상)](search-api-preview.md)를 사용하여 데이터에 액세스합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

이 문서에서는 콘텐츠를 추출하고 Azure Cognitive Search에서 검색할 수 있도록 Azure Cosmos DB [인덱서](search-indexer-overview.md)를 구성하는 방법을 보여 줍니다. 이 워크플로는 Azure Cognitive Search 인덱스를 만들고 Azure Cosmos DB에서 추출한 기존 텍스트를 사용하여 로드합니다.

용어가 혼동될 수 있기 때문에 [Azure Cosmos DB 인덱싱](../cosmos-db/index-overview.md) 및 [Azure Cognitive Search 인덱싱](search-what-is-an-index.md)은 각 서비스에 고유한 별개의 작업이라는 점에 유의해야 합니다. Azure Cognitive Search 인덱싱을 시작하기 전에 Azure Cosmos DB 데이터베이스가 이미 존재하고 데이터를 포함해야 합니다.

Azure Cognitive Search의 Cosmos DB 인덱서는 다음 프로토콜을 통해 액세스되는 [Azure Cosmos DB 항목](../cosmos-db/account-databases-containers-items.md#azure-cosmos-items)을 크롤링할 수 있습니다.

+ 일반적으로 사용할 수 있는 [SQL API](../cosmos-db/sql-query-getting-started.md)의 경우 [포털](#cosmos-indexer-portal), [REST API](/rest/api/searchservice/indexer-operations), [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) 또는 다른 Azure SDK를 사용하여 데이터 원본 및 인덱서를 만들 수 있습니다.

+ [MONGODB API(미리 보기)](../cosmos-db/mongodb-introduction.md)의 경우 [포털](#cosmos-indexer-portal) 또는 [REST API 버전 2020-06-30-미리 보기](search-api-preview.md) 중 하나를 사용하여 데이터 원본 및 인덱서를 만들 수 있습니다.

> [!Note]
> Azure Cognitive Search에서 지원되는지 확인하려면 [Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)에 대한 사용자 의견에 투표를 캐스트할 수 있습니다.
>

## <a name="prerequisites"></a>필수 구성 요소

[인덱싱 정책](../cosmos-db/index-policy.md)이 [일치](../cosmos-db/index-policy.md#indexing-mode)로 설정된 Cosmos DB 컬렉션만 Azure Cognitive Search에서 지원됩니다. 지연 인덱싱 정책으로 컬렉션을 인덱싱하는 것은 권장되지 않으며 데이터가 누락될 수 있습니다. 인덱싱이 사용하지 않도록 설정된 컬렉션은 지원되지 않습니다.

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>포털 사용

> [!Note]
> 포털은 현재 SQL API 및 MongoDB API(미리 보기)를 지원합니다.

Azure Cosmos DB 항목을 인덱싱하는 가장 쉬운 방법은 [Azure Portal](https://portal.azure.com/)에서 마법사를 사용하는 것입니다. Azure Cognitive Search의 [**데이터 가져오기**](search-import-data-portal.md) 마법사는 데이터를 샘플링하고 컨테이너에서 메타데이터를 읽음으로써 기본 인덱스를 만들고, 원본 필드를 대상 인덱스 필드에 매핑하고, 인덱스를 로드하는 과정을 단일 작업으로 수행할 수 있습니다. 원본 데이터의 크기 및 복잡성에 따라 몇 분 안에 운영 작업에 대한 전체 텍스트 검색 인덱스를 사용할 수 있습니다.

지연 시간을 줄이고 대역폭 요금이 부과되지 않도록 Azure Cognitive Search와 Azure Cosmos DB 모두에 동일한 지역 또는 위치를 사용하는 것이 좋습니다.

### <a name="step-1---prepare-source-data"></a>1단계 - 원본 데이터 준비

Cosmos DB 계정, SQL API 또는 MongoDB API(미리 보기)에 매핑된 Azure Cosmos DB 데이터베이스와 데이터베이스의 콘텐츠가 있어야 합니다.

Cosmos DB 데이터베이스에 데이터가 포함되어 있는지 확인합니다. [데이터 가져오기 마법사](search-import-data-portal.md)는 메타데이터를 읽고 데이터 샘플링을 수행하여 인덱스 스키마를 유추하지만 Cosmos DB에서도 데이터를 로드합니다. 데이터가 누락된 경우 "데이터 원본에서 인덱스 스키마를 검색하는 동안 오류 발생: 데이터 원본 'emptycollection'에서 데이터를 반환하지 않았으므로 프로토타입 인덱스를 빌드할 수 없습니다."라는 오류와 함께 마법사가 중지됩니다.

### <a name="step-2---start-import-data-wizard"></a>2단계 - 데이터 가져오기 마법사 시작

Azure Cognitive Search 서비스 페이지의 명령 모음에서 [마법사를 시작](search-import-data-portal.md)하거나 Cosmos DB SQL API에 연결된 경우 Cosmos DB 계정의 왼쪽 탐색 창에 있는 **설정** 섹션에서 **Azure Cognitive Search 추가** 를 클릭할 수 있습니다.

   ![포털에서 데이터 가져오기 명령](./media/search-import-data-portal/import-data-cmd2.png "데이터 가져오기 마법사 시작")

### <a name="step-3---set-the-data-source"></a>3단계 - 데이터 원본 설정

**데이터 원본** 페이지에서 원본은 다음 사양을 가진 **Cosmos DB** 여야 합니다.

+ **이름** 은 데이터 원본 개체의 이름입니다. 만든 후에는 다른 워크로드에 대해 선택할 수 있습니다.

+ **Cosmos DB 계정** 은 다음 형식 중 하나여야 합니다.
    1. 다음 형식을 사용하는 Cosmos DB의 기본 또는 보조 연결 문자열: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;`
        + 버전 3.2 및 버전 3.6 **MongoDB 컬렉션** 의 경우 Azure Portal의 Cosmos DB 계정에 다음 형식을 사용합니다. `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
    1.  계정 키를 포함하지 않는 다음 형식의 관리 ID 연결 문자열: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)`. 이 연결 문자열 형식을 사용하려면 [관리 ID를 사용하여 Cosmos DB 데이터베이스에 대한 인덱서 연결을 설정](search-howto-managed-identities-cosmos-db.md)하는 지침을 따르세요.

+ **데이터베이스** 는 계정의 기존 데이터베이스입니다. 

+ **컬렉션** 은 문서의 컨테이너입니다. 가져오기가 성공하려면 문서가 있어야 합니다. 

+ 모든 문서를 원하면 **쿼리** 를 비워둘 수 있습니다. 그렇지 않으면 문서 하위 집합을 선택하는 쿼리를 입력할 수 있습니다. **쿼리** 는 SQL API에서만 사용할 수 있습니다.

   ![Cosmos DB 데이터 원본 정의](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB 데이터 원본 정의")

### <a name="step-4---skip-the-enrich-content-page-in-the-wizard"></a>4단계 - 마법사에서 "콘텐츠 보강" 페이지 건너뛰기

인식 기술(또는 보강)을 추가하는 것은 가져오기 요구 사항이 아닙니다. 인덱싱 파이프라인에 [AI 보강을 추가](cognitive-search-concept-intro.md)해야 하는 특정한 이유가 없다면 이 단계를 건너뛸 수 있습니다.

단계를 건너뛰려면 페이지 맨 아래에 있는 "다음" 및 "건너뛰기"의 파란색 단추를 클릭합니다.

### <a name="step-5---set-index-attributes"></a>5단계 - 인덱스 특성 설정

**인덱스** 페이지에는 데이터 형식을 포함하는 필드 목록과 인덱스 특성을 설정하기 위한 여러 확인란이 표시됩니다. 마법사는 메타데이터에 따라, 원본 데이터를 샘플링하여 필드 목록을 생성할 수 있습니다. 

특성 열의 맨 위에 있는 확인란을 클릭하여 특성을 일괄 선택할 수 있습니다. 클라이언트 앱에 반환되어야 하고 전체 텍스트 검색 처리를 적용받아야 하는 모든 필드에 대해 **조회 가능** 및 **검색 가능** 을 선택합니다. 정수는 전체 텍스트 또는 유사 항목 검색이 불가능하다는 것을 알 수 있습니다(숫자는 그대로 평가되며 필터에 유용한 경우가 많습니다).

자세한 내용은 [인덱스 특성](/rest/api/searchservice/create-index#bkmk_indexAttrib) 및 [언어 분석기](/rest/api/searchservice/language-support)에 대한 설명을 검토하세요. 

시간을 내서 선택 항목을 검토합니다. 마법사를 실행하면 실제 데이터 구조가 만들어지며, 모든 개체를 삭제했다가 다시 만들지 않으면 이러한 필드를 편집할 수 없습니다.

   ![Cosmos DB 인덱스 정의](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB 인덱스 정의")

### <a name="step-6---create-indexer"></a>6단계 - 인덱서 만들기

완전히 지정된 마법사는 검색 서비스에서 세 개의 고유한 개체를 만듭니다. 데이터 원본 개체와 인덱스 개체는 Azure Cognitive Search 서비스에 명명된 리소스로 저장됩니다. 마지막 단계는 인덱서 개체를 만듭니다. 인덱서 이름을 지정하면 인덱서가 동일한 마법사 시퀀스에서 만든 인덱스 및 데이터 원본 개체와는 별도로 예약하고 관리할 수 있는 독립 실행형 리소스가 될 수 있습니다.

인덱서에 익숙하지 않은 경우 *인덱서* 를 검색 가능 콘텐츠의 외부 데이터 원본을 크롤링하는 Azure Cognitive Search의 리소스로 간주할 수 있습니다. **데이터 가져오기** 마법사를 실행하면 Cosmos DB 데이터 원본을 크롤링하고, 검색 가능한 콘텐츠를 추출하고, Azure Cognitive Search의 인덱스로 가져오는 인덱서가 제공됩니다.

다음 스크린샷에서는 기본 인덱서 구성을 보여 줍니다. 인덱서를 한 번 실행하려는 경우 **한 번** 으로 전환할 수 있습니다. **제출** 을 클릭하여 마법사를 실행하고 모든 개체를 만듭니다. 인덱싱이 즉시 시작됩니다.

   ![Cosmos DB 인덱서 정의](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB 인덱서 정의")

포털 페이지에서 데이터 가져오기를 모니터링할 수 있습니다. 진행률 알림은 인덱싱 상태 및 업로드된 문서 수를 나타냅니다. 

인덱싱이 완료되면 [검색 탐색기](search-explorer.md)를 사용하여 인덱스를 쿼리할 수 있습니다.

> [!NOTE]
> 원하는 데이터가 표시되지 않는 경우 더 많은 필드에서 더 많은 특성을 설정해야 할 수 있습니다. 방금 만든 인덱스 및 인덱서를 삭제하고 마법사를 다시 단계별로 실행하여 5단계에서 인덱스 특성에 대한 선택 항목을 수정합니다. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API 사용

REST API를 사용하여 Azure Cognitive Search의 모든 인덱서에 공통적인 세 부분으로 구성된 워크플로(데이터 원본 만들기, 인덱스 만들기, 인덱서 만들기)를 수행하여 Azure Cosmos DB 데이터를 인덱싱할 수 있습니다. 아래 프로세스에서는 인덱서 만들기 요청을 제출할 때 Cosmos DB에서 데이터 추출을 시작합니다.

이 문서의 앞 부분에서 [Azure Cosmos DB 인덱싱](../cosmos-db/index-overview.md) 및 [Azure Cognitive Search 인덱싱](search-what-is-an-index.md)이 고유한 작업이라고 언급했습니다. Cosmos DB 인덱싱의 경우 기본적으로 모든 문서가 자동으로 인덱싱됩니다. 자동 인덱싱을 해제하면 자체 링크를 통해서나 문서 ID를 사용한 쿼리로만 문서에 액세스할 수 있습니다. Azure Cognitive Search 인덱싱을 사용하려면 Azure Cognitive Search로 인덱싱할 컬렉션에서 Cosmos DB 자동 인덱싱이 설정되어 있어야 합니다.

> [!WARNING]
> Azure Cosmos DB는 DocumentDB의 다음 세대입니다. 이전에는 API 버전 **2017-11-11** 을 사용하여 `documentdb` 구문을 사용할 수 있었습니다. 즉, 데이터 원본 유형을 `cosmosdb` 또는 `documentdb`로 지정할 수 있었습니다. API 버전 **2019-05-06** 부터 Azure Cognitive Search API 및 포털은 모두 이 문서에 설명된 대로 `cosmosdb` 구문만 지원합니다. 이는 Cosmos DB 엔드포인트에 연결하려는 경우 데이터 원본 유형이 `cosmosdb`여야 함을 의미합니다.

### <a name="step-1---assemble-inputs-for-the-request"></a>1단계 - 요청에 대한 입력 어셈블

각 요청에 대해 Azure Cognitive Search(POST 헤더에서)에 대한 서비스 이름 및 관리자 키와 Blob Storage에 대한 스토리지 계정 이름 및 키를 제공해야 합니다. [Postman](search-get-started-rest.md) 또는 [Visual Studio Code](search-get-started-vs-code.md)를 사용하여 Azure Cognitive Search에 HTTP 요청을 보낼 수 있습니다.

요청에 사용할 다음 세 가지 값을 복사합니다.

+ Azure Cognitive Search 서비스 이름
+ Azure Cognitive Search 관리자 키
+ Cosmos DB 연결 문자열

포털에서 이러한 값을 찾을 수 있습니다.

1. Azure Cognitive Search에 대한 포털 페이지에서 개요 페이지의 검색 서비스 URL을 복사합니다.

2. 왼쪽 탐색 창에서 **키** 를 클릭한 다음 기본 키 또는 보조 키를 복사합니다.

3. Cosmos 스토리지 계정에 대한 포털 페이지로 전환합니다. 왼쪽 탐색 창의 **설정** 에서 **키** 를 클릭합니다. 이 페이지에서는 URI, 두 개의 연결 문자열 집합 및 두 개의 키 집합을 제공합니다. 연결 문자열 중 하나를 메모장에 복사합니다.

### <a name="step-2---create-a-data-source"></a>2단계 - 데이터 원본 만들기

**데이터 원본** 은 인덱싱할 데이터, 자격 증명 및 데이터의 변경 사항 식별 정책(예: 컬렉션 내 수정 또는 삭제된 문서)을 지정합니다. 데이터 소스는 독립 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

데이터 원본을 만들려면 POST 요청을 작성합니다.

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

| 필드   | 설명 |
|---------|-------------|
| **name** | 필수 사항입니다. 데이터 원본 개체를 나타낼 이름을 선택합니다. |
|**type**| 필수 사항입니다. `cosmosdb`이어야 합니다. |
|**credentials** | 필수 사항입니다. Cosmos DB 연결 문자열이 있어야 합니다.<br/><br/>**SQL 컬렉션** 의 경우 연결 문자열은 다음과 같은 형식입니다.`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>버전 3.2 및 버전 3.6 **MongoDB 컬렉션** 의 경우 연결 문자열에 다음 형식 중 하나를 사용합니다. `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>엔드포인트 URL에는 포트 번호를 사용하지 않습니다. 포트 번호를 포함하는 경우 Azure Cognitive Search가 Azure Cosmos DB 데이터베이스를 인덱싱할 수 없게 됩니다.|
| **container** | 다음과 같은 요소가 있습니다. <br/>**name**: 필수 사항입니다. 인덱싱할 데이터베이스 컬렉션의 ID를 지정합니다.<br/>**query**: (선택 사항) 추상 JSON 문서를 Azure Cognitive Search가 인덱싱할 수 있는 평면 스키마로 평면화하는 쿼리를 지정할 수 있습니다.<br/>MongoDB API의 경우 쿼리가 지원되지 않습니다. |
| **dataChangeDetectionPolicy** | 권장됩니다. [변경된 문서 인덱싱](#DataChangeDetectionPolicy) 섹션을 참조하세요.|
|**dataDeletionDetectionPolicy** | 선택 사항입니다. [삭제된 문서 인덱싱](#DataDeletionDetectionPolicy) 섹션을 참조하세요.|

### <a name="using-queries-to-shape-indexed-data"></a>쿼리를 사용하여 인덱싱된 데이터 형성
중첩된 속성 또는 배열을 평면화하고, JSON 속성을 프로젝션하고, 인덱싱할 데이터를 필터링하는 SQL 쿼리를 지정할 수 있습니다. 

> [!WARNING]
> **MongoDB API** 에는 사용자 지정 쿼리가 지원되지 않습니다. `container.query` 매개 변수를 Null로 설정하거나 생략해야 합니다. 사용자 지정 쿼리를 사용해야 하는 경우 [사용자 의견](https://feedback.azure.com/forums/263029-azure-search)에서 알려주세요.

예제 문서:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

필터 쿼리:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

평면화 쿼리:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

프로젝션 쿼리:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

배열 평면화 쿼리:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

<a name="SelectDistinctQuery"></a>

#### <a name="distinct-and-group-by"></a>DISTINCT 및 GROUP BY

[DISTINCT 키워드](../cosmos-db/sql-query-keywords.md#distinct) 또는 [GROUP BY 절](../cosmos-db/sql-query-group-by.md)을 사용하는 쿼리는 지원되지 않습니다. Azure Cognitive Search는 [SQL 쿼리 페이지 매김](../cosmos-db/sql-query-pagination.md)을 사용하여 쿼리 결과를 완전히 열거합니다. DISTINCT 키워드 또는 GROUP BY 절은 결과를 매길 때 사용되는 [연속 토큰](../cosmos-db/sql-query-pagination.md#continuation-tokens)과 호환되지 않습니다.

지원되지 않는 쿼리 예제:
```sql
SELECT DISTINCT c.id, c.userId, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts

SELECT DISTINCT VALUE c.name FROM c ORDER BY c.name

SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup FROM Food f GROUP BY f.foodGroup
```

Cosmos DB에 [ORDER BY 절을 사용하여 DISTINCT 키워드로 SQL 쿼리 페이지 매김](../cosmos-db/sql-query-pagination.md#continuation-tokens)을 지원하기 위한 해결 방법이 있지만 Azure Cognitive Search와 호환되지 않습니다. Azure Cognitive Search에는 JSON 개체가 필요하지만 쿼리는 단일 JSON 값을 반환합니다.

```sql
-- The following query returns a single JSON value and isn't supported by Azure Cognitive Search
SELECT DISTINCT VALUE c.name FROM c ORDER BY c.name
```

### <a name="step-3---create-a-target-search-index"></a>3단계 - 대상 검색 인덱스 만들기 

[대상 Azure Cognitive Search 인덱스](/rest/api/searchservice/create-index)가 아직 없으면 만듭니다. 다음 예에서는 ID 및 설명 필드를 사용하여 인덱스를 만듭니다.

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "searchable": true,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

대상 인덱스의 스키마가 소스 JSON 문서의 스키마 또는 사용자 지정 쿼리 프로젝션의 출력과 호환되는지 확인합니다.

> [!NOTE]
> 분할된 컬렉션의 경우, 기본 문서 키는 Azure Cosmos DB의 `_rid` 속성입니다. 필드 이름은 밑줄 문자로 시작할 수 없으므로 Azure Cognitive Search에서 이름이 `rid`로 자동으로 바뀝니다. 또한 Azure Cosmos DB의 `_rid` 값은 Azure Cognitive Search 키에 잘못된 문자를 포함합니다. 따라서 `_rid` 값은 Base64로 인코딩됩니다.
> 
> MongoDB 컬렉션의 경우 Azure Cognitive Search에서 `_id` 속성의 이름이 `id`로 자동으로 바뀝니다.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON 데이터 형식과 Azure Cognitive Search 데이터 형식 사이의 매핑

| JSON 데이터 형식 | 호환되는 대상 인덱스 필드 형식 |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| 정수와 같이 보이는 숫자 |Edm.Int32, Edm.Int64, Edm.String |
| 부동소수점처럼 보이는 숫자 |Edm.Double, Edm.String |
| String |Edm.String |
| 기본 형식의 배열, 예: ["a", "b", "c"] |Collection(Edm.String) |
| 날짜처럼 보이는 문자열 |Edm.DateTimeOffset, Edm.String |
| GeoJSON 개체, 예: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 기타 JSON 개체 |해당 없음 |

### <a name="step-4---configure-and-run-the-indexer"></a>4단계 - 인덱서 구성 및 실행

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](/rest/api/searchservice/create-indexer)를 확인하세요.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서를 예약하는 방법](search-howto-schedule-indexers.md)을 참조하세요.

## <a name="use-net"></a>.NET 사용

일반 공급 .NET SDK에는 일반 공급 REST API의 전체 패리티가 있습니다. 개념, 워크플로 및 요구 사항을 알아보려면 이전 REST API 섹션을 검토하는 것이 좋습니다. 그런 후, 다음 .NET API 참조 설명서를 참조하여 관리되는 코드에서 JSON 인덱서를 구현할 수 있습니다.

+ [azure.search.documents.indexes.models.searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents.indexes.models.searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents.indexes.models.searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents.indexes.models.searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>변경된 문서 인덱싱

데이터 변경 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재 지원되는 정책은 Azure Cosmos DB에서 제공하는 `_ts`(타임스탬프) 속성을 사용하는 [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy)뿐이며, 다음과 같이 지정됩니다.

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

적절한 인덱서 성능을 보장하기 위해서는 반드시 이 정책을 사용하는 것이 좋습니다. 

사용자 지정 쿼리를 사용하는 경우 `_ts` 속성이 쿼리에 의해 프로젝션되어야 합니다.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>증분 진행률 및 사용자 지정 쿼리

인덱싱하는 동안 증분 진행률은 인덱서 실행이 일시적 오류 또는 실행 시간 제한에 의해 중단되었는지를 확인합니다. 인덱서는 처음부터 전체 컬렉션을 다시 인덱스하는 대신 다음으로 실행할 위치를 선택할 수 있습니다. 대규모 컬렉션을 인덱싱할 때 특히 유용합니다. 

사용자 지정 쿼리를 사용하는 경우 증분 진행률을 사용하려면 쿼리가 `_ts` 열을 기준으로 결과를 정렬해야 합니다. 그러면 Azure Cognitive Search에서 사용하는 정기적 검사점이 오류가 있는 경우 증분 진행률을 제공할 수 있습니다.   

어떤 경우에는 쿼리에 `ORDER BY [collection alias]._ts` 절이 포함되더라도 Azure Cognitive Search는 쿼리가 `_ts`의 의해 정렬된 것으로 유추하지 못할 수 있습니다. `assumeOrderByHighWaterMarkColumn` 구성 속성을 사용하여 결과가 정렬되도록 Azure Cognitive Search에 알릴 수 있습니다. 이 힌트를 지정하려면 다음과 같이 인덱서를 만들거나 업데이트합니다. 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>삭제된 문서 인덱싱

컬렉션에서 행이 삭제된 경우 일반적으로 검색 인덱스에서도 해당 행을 삭제하려고 할 것입니다. 데이터 삭제 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재까지 지원되는 유일한 정책은 다음과 같이 지정되는 `Soft Delete` 정책입니다(삭제 시 일부 유형의 플래그로 표시됨).

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

사용자 지정 쿼리를 사용하는 경우 `softDeleteColumnName`에서 참조되는 속성이 쿼리에 의해 프로젝션됩니다.

다음 예제에서는 일시 삭제 정책을 사용하여 데이터 원본을 만듭니다.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>다음 단계

축하합니다! 인덱서를 사용하여 Azure Cosmos DB를 Azure Cognitive Search와 통합하는 방법을 알아보았습니다.

* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 서비스 페이지](https://azure.microsoft.com/services/cosmos-db/)를 참조하세요.
* Azure Cognitive Search에 대해 자세히 알아보려면 [Search 서비스 페이지](https://azure.microsoft.com/services/search/)를 참조하세요.