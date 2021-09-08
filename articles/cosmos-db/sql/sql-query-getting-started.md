---
title: Azure Cosmos DB에서 SQL 쿼리 시작하기
description: SQL 쿼리를 사용하여 Azure Cosmos DB에서 데이터를 쿼리하는 방법에 대해 알아봅니다. Azure Cosmos DB의 컨테이너에 샘플 데이터를 업로드하 고 쿼리할 수 있습니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: tisande
ms.openlocfilehash: af71ff52ead86c0cdeea307e736c36ac27536bfa
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029943"
---
# <a name="getting-started-with-sql-queries"></a>SQL 쿼리 시작
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL API 계정에서는 두 가지 방법으로 데이터를 읽을 수 있습니다.

**Point reads** - 단일 ‘항목 ID’ 및 파티션 키에 대한 키/값 조회를 수행할 수 있습니다. ‘항목 ID’ 와 파티션 키 조합이 키이며 항목 자체는 값입니다. 1KB 문서의 경우 point reads 비용은 일반적으로 10밀리 미만의 대기 시간으로 1개의 [요청 단위](../request-units.md)입니다. Point reads는 단일 항목을 반환합니다.

다음은 각 SDK를 사용하여 **Point reads** 를 수행하는 방법에 대한 몇 가지 예입니다.

- [.NET SDK](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**SQL 쿼리** - JSON 쿼리 언어로 SQL(구조적 쿼리 언어)를 사용하는 쿼리를 작성하여 데이터를 쿼리할 수 있습니다. 쿼리는 항상 최소 2.3개의 요청 단위로 요금이 청구되며 일반적으로 Point reads보다 대기 시간이 더 길고 가변적입니다. 쿼리는 많은 항목을 반환할 수 있습니다.

Azure Cosmos DB에 대한 대부분의 대량 읽기 워크로드는 point reads와 SQL 쿼리의 조합을 사용합니다. 단일 항목을 읽어야 하는 경우 Point reads가 쿼리보다 더 저렴하고 빠릅니다. Point reads는 데이터에 액세스하기 위해 쿼리 엔진을 사용할 필요가 없으며, 데이터를 직접 읽을 수 있습니다. 물론, 모든 워크로드에서 Point reads를 사용하여 데이터를 독점적으로 읽을 수 있는 것은 아니므로, SQL을 쿼리 언어로 지원하고 [스키마와 관계없는 인덱싱](../index-overview.md)으로 데이터에 더욱 유연하게 액세스할 수 있습니다.

다음은 각 SDK를 사용하여 **SQL 쿼리** 를 수행하는 방법에 대한 몇 가지 예입니다.

- [.NET SDK](../sql-api-dotnet-v3sdk-samples.md#query-examples)
- [Java SDK](../sql-api-java-sdk-samples.md#query-examples)
- [Node.js SDK](../sql-api-nodejs-samples.md#item-examples)
- [Python SDK](../sql-api-python-samples.md#item-examples)

이 문서의 나머지 부분에서는 Azure Cosmos DB에서 SQL 쿼리 작성을 시작하는 방법을 알아봅니다. SQL 쿼리는 SDK 또는 Azure Portal을 통해 실행할 수 있습니다.

## <a name="upload-sample-data"></a>샘플 데이터 업로드

SQL API Cosmos DB 계정에서 [데이터 탐색기](../data-explorer.md)를 열어 `Families` 컨테이너를 만듭니다. 컨테이너를 만든 후에는 데이터 구조 브라우저를 사용하여 컨테이너를 찾아 엽니다. `Families` 컨테이너에서, 컨테이너 이름 바로 아래에 `Items` 옵션이 표시됩니다. 이 옵션을 열면 화면 가운데의 메뉴 모음에서 ‘새 항목’을 만들 수 있는 단추가 표시됩니다. 이 기능을 사용하여 아래 JSON 항목을 만듭니다.

### <a name="create-json-items"></a>JSON 항목 만들기

다음 2개의 JSON 항목은 Andersen family 및 Wakefield family에 대한 문서입니다. 부모, 자식, 애완 동물, 주소, 등록 정보를 포함합니다. 

첫 번째 항목에는 문자열, 숫자, 부울, 배열, 중첩 속성이 있습니다.

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

두 번째 항목은 `firstName` 및 `lastName` 대신 `givenName` 및 `familyName`을 사용합니다.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>JSON 항목 쿼리

JSON 데이터에 대한 몇 가지 쿼리를 수행하여 Azure Cosmos DB의 SQL 쿼리 언어의 몇 가지 주요 측면을 이해합니다.

다음 쿼리는 `id` 필드가 `AndersenFamily`와 일치하는 항목을 반환합니다. 쿼리가 `SELECT *`이므로 쿼리의 출력은 전체 JSON 항목입니다. SELECT 구문에 대한 자세한 내용은 [SELECT 문](sql-query-select.md)을 참조하세요.

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

쿼리 결과는 다음과 같습니다.

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

다음 쿼리는 JSON 출력을 다른 셰이프로 다시 포맷합니다. 쿼리는 주소 도시가 상태와 동일한 경우 두 개의 선택된 필드인 `Name` 및 `City`가 있는 새 JSON `Family` 개체를 생성합니다. “NY, NY”가 여기에 해당합니다.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

쿼리 결과는 다음과 같습니다.

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

다음 쿼리는 `id`가 `WakefieldFamily`와 일치하는 가족의 지정된 모든 이름을 도시별로 정렬하여 반환합니다.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

결과는 다음과 같습니다.

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>설명

위의 예에서는 Cosmos DB 쿼리 언어의 몇 가지 측면을 보여 줍니다.  

* SQL API는 JSON 값에 대해 작동하므로 행과 열 대신 트리 모양의 엔터티를 다룹니다. ANSI SQL에서 `<table>.<column>`의 두 부분으로 구성된 참조와 유사한 `Node1.Node2.Node3…..Nodem`과 같은 임의의 수준에서 트리 노드를 참조할 수 있습니다.

* 쿼리 언어는 스키마가 없는 데이터와 함께 작동하므로 형식 시스템을 동적으로 바인딩해야 합니다. 항목에 따라 동일한 식이 다른 형식을 생성할 수 있습니다. 쿼리 결과는 유효한 JSON 값이지만 고정 스키마가 아닐 수도 있습니다.  

* Azure Cosmos DB는 엄격한 JSON 항목만 지원합니다. 형식 시스템과 식이 JSON 형식만 처리하도록 제한됩니다. 자세한 내용은 [JSON 사양](https://www.json.org/)을 참조하세요.  

* Cosmos 컨테이너는 JSON 항목의 스키마 없는 컬렉션입니다. 컨테이너의 항목 내 및 항목 간 데이터 엔터티의 관계는 기본 키 및 외래 키 관계가 아니라 포함을 통해 암시적으로 캡처됩니다. 이 기능은 [Azure Cosmos DB의 조인](sql-query-join.md)에서 설명하는 항목 간 조인에 중요합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](../introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 절](sql-query-select.md)
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](../estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
