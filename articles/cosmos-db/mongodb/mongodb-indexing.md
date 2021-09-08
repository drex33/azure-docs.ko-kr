---
title: Azure Cosmos DB의 API for MongoDB에서 인덱싱 관리
description: 이 문서에서는 Azure Cosmos DB의 API for MongoDB를 사용하여 Azure Cosmos DB 인덱싱 기능의 개요를 설명합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js
ms.openlocfilehash: 27b051a54fc17b0d7d65fff4d7f02e806baa3fd0
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033317"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB에서 인덱싱 관리
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB의 API for MongoDB는 Azure Cosmos DB의 핵심 인덱스 관리 기능을 활용합니다. 이 문서에서는 Azure Cosmos DB의 API for MongoDB를 사용하여 인덱스를 추가하는 방법을 중점적으로 설명합니다. 모든 API에서 관련된 [Azure Cosmos DB의 인덱싱 개요](../index-overview.md)를 읽을 수도 있습니다.

## <a name="indexing-for-mongodb-server-version-36-and-higher"></a>MongoDB 서버 버전 3.6 이상에 대한 인덱싱

Azure Cosmos DB의 API for MongoDB 서버 버전 3.6 이상은 `_id` 필드를 자동으로 인덱싱하며, 이 필드는 삭제할 수 없습니다. 분할 키별 `_id` 필드의 고유성을 자동으로 적용합니다. Azure Cosmos DB의 API for MongoDB에서는 분할 및 인덱싱이 별도의 개념입니다. 분할 키를 인덱싱할 필요는 없습니다. 그러나 문서의 다른 속성과 마찬가지로 이 속성이 쿼리에서 일반 필터인 경우 분할 키를 인덱싱하는 것이 좋습니다.

추가 필드를 인덱싱하려면 MongoDB 인덱스 관리 명령을 적용합니다. MongoDB에서와 마찬가지로, Azure Cosmos DB의 API for MongoDB는 `_id` 필드만 자동으로 인덱싱합니다. 이 기본 인덱싱 정책은 모든 필드를 기본적으로 인덱싱하는 Azure Cosmos DB SQL API와 다릅니다.

쿼리에 정렬을 적용하려면 정렬 작업에 사용되는 필드에 대한 인덱스를 만들어야 합니다.

### <a name="editing-indexing-policy"></a>인덱싱 정책 편집

Azure Portal 내 데이터 탐색기에서 인덱싱 정책을 편집하는 것이 좋습니다.
. 데이터 탐색기의 인덱싱 정책 편집기에서 단일 필드 및 와일드카드 인덱스를 추가할 수 있습니다.

:::image type="content" source="./media/mongodb-indexing/indexing-policy-editor.png" alt-text="인덱싱 정책 편집기":::

> [!NOTE]
> 데이터 탐색기에서 인덱싱 정책 편집기를 사용하여 복합 인덱스를 만들 수는 없습니다.

## <a name="index-types"></a>인덱스 형식

### <a name="single-field"></a>단일 필드

단일 필드에 인덱스를 만들 수 있습니다. 단일 필드 인덱스의 정렬 순서는 중요하지 않습니다. 다음 명령은 `name` 필드에 인덱스를 만듭니다.

`db.coll.createIndex({name:1})`

Azure Portal에서 `name`에 동일한 단일 필드 인덱스를 만들 수 있습니다.

:::image type="content" source="./media/mongodb-indexing/add-index.png" alt-text="인덱싱 정책 편집기에서 이름 인덱스 추가":::

사용 가능한 경우 하나의 쿼리가 여러 개의 단일 필드 인덱스를 사용합니다. 컨테이너당 최대 500개의 단일 필드 인덱스를 만들 수 있습니다.

### <a name="compound-indexes-mongodb-server-version-36"></a>복합 인덱스(MongoDB 서버 버전 3.6 이상)

Azure Cosmos DB의 API for MongoDB는 버전 3.6 및 4.0 유선 프로토콜을 사용하는 계정에 대한 복합 인덱스를 지원합니다. 하나의 복합 인덱스에 최대 8개의 필드를 포함할 수 있습니다. MongoDB와 달리 쿼리가 한 번에 여러 필드에서 효율적으로 정렬되어야 하는 경우에만 복합 인덱스를 만들어야 합니다. 정렬할 필요가 없는 여러 필터가 포함된 쿼리의 경우 단일 복합 인덱스 대신 단일 필드 인덱스를 여러 개 만듭니다. 

> [!NOTE]
> 중첩된 속성 또는 배열에는 복합 인덱스를 만들 수 없습니다.

다음 명령은 `name` 및 `age` 필드에 복합 인덱스를 만듭니다.

`db.coll.createIndex({name:1,age:1})`

다음 예제와 같이 복합 인덱스를 사용하여 여러 필드에서 한 번에 효율적으로 정렬할 수 있습니다.

`db.coll.find().sort({name:1,age:1})`

또한 앞의 복합 인덱스를 사용하여 모든 필드에서 반대의 정렬 순서로 쿼리를 효율적으로 정렬할 수도 있습니다. 예를 들면 다음과 같습니다.

`db.coll.find().sort({name:-1,age:-1})`

그러나 복합 인덱스의 경로 시퀀스는 쿼리와 정확히 일치해야 합니다. 다음은 추가 복합 인덱스가 필요한 쿼리의 예입니다.

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> 복합 인덱스는 결과를 정렬하는 쿼리에만 사용됩니다. 정렬할 필요가 없는 여러 필터가 있는 쿼리의 경우 여러 개의 단일 필드 인덱스를 만듭니다.

### <a name="multikey-indexes"></a>Multikey 인덱스

Azure Cosmos DB는 배열에 저장된 콘텐츠를 인덱싱하기 위해 Multikey 인덱스를 만듭니다. 배열 값을 사용하여 필드를 인덱싱하는 경우 Azure Cosmos DB는 배열의 모든 요소를 자동으로 인덱싱합니다.

### <a name="geospatial-indexes"></a>지리 공간적 인덱스

많은 지리 공간적 연산자가 지리 공간적 인덱스를 활용합니다. 현재 Azure Cosmos DB의 API for MongoDB는 `2dsphere` 인덱스를 지원합니다. 이 API는 아직 `2d` 인덱스를 지원하지 않습니다.

`location` 필드에 지리 공간적 인덱스를 만드는 예는 다음과 같습니다.

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>텍스트 인덱스

현재 Azure Cosmos DB의 API for MongoDB는 텍스트 인덱스를 지원하지 않습니다. 문자열에 대한 텍스트 검색 쿼리의 경우 Azure Cosmos DB와 통합된 [Azure Cognitive Search](../../search/search-howto-index-cosmosdb.md)를 사용해야 합니다. 

## <a name="wildcard-indexes"></a>와일드카드 인덱스

와일드카드 인덱스를 사용하면 알 수 없는 필드에 대해 쿼리를 지원할 수 있습니다. 패밀리에 대한 데이터를 보유하는 컬렉션이 있다고 가정해 보겠습니다.

다음은 해당 컬렉션의 예제 문서의 일부입니다.

```json
"children": [
   {
     "firstName": "Henriette Thaulow",
     "grade": "5"
   }
]
```

아래는 또 다른 예이며 이번에는 `children`에서 약간 다른 속성 집합을 사용합니다.

```json
"children": [
    {
     "familyName": "Merriam",
     "givenName": "Jesse",
     "pets": [
         { "givenName": "Goofy" },
         { "givenName": "Shadow" }
         ]
   },
   {
     "familyName": "Merriam",
     "givenName": "John",
   }
]
```

이 컬렉션에서 문서에는 여러 가지 사용 가능한 속성이 포함될 수 있습니다. `children` 배열의 모든 데이터를 인덱싱하려는 경우 두 가지 옵션이 있습니다. 각 개별 속성에 대해 별도의 인덱스를 만드는 옵션과 전체 `children` 배열에 대해 하나의 와일드카드 인덱스를 만드는 옵션입니다.

### <a name="create-a-wildcard-index"></a>와일드카드 인덱스 만들기

다음 명령은 `children` 내의 모든 속성에 와일드카드 인덱스를 만듭니다.

`db.coll.createIndex({"children.$**" : 1})`

**MongoDB와 달리 와일드카드 인덱스는 쿼리 조건자의 여러 필드를 지원할 수 있습니다**. 각 속성에 대한 별도의 인덱스를 만드는 대신 하나의 와일드카드 인덱스를 사용해도 쿼리 성능에 차이가 없습니다.

와일드카드 구문을 사용하여 다음과 같은 인덱스 유형을 만들 수 있습니다.

* 단일 필드
* 지리 공간적

### <a name="indexing-all-properties"></a>모든 속성 인덱싱

모든 필드에 와일드카드 인덱스를 만드는 방법은 다음과 같습니다.

`db.coll.createIndex( { "$**" : 1 } )`

Azure Portal에서 데이터 탐색기를 사용하여 와일드카드 인덱스를 만들 수도 있습니다.

:::image type="content" source="./media/mongodb-indexing/add-wildcard-index.png" alt-text="인덱싱 정책 편집기에서 와일드카드 인덱스 추가":::

> [!NOTE]
> 개발을 이제 막 시작하는 경우 모든 필드에서 와일드카드 인덱스를 사용하여 시작하는 것이 **가장 좋습니다**. 이를 통해 개발을 간소화하고 쿼리를 보다 쉽게 최적화할 수 있습니다.

많은 필드가 있는 문서는 쓰기 및 업데이트에 대한 높은 RU(요청 단위) 요금이 부과될 수 있습니다. 따라서 쓰기 작업이 많은 경우에는 와일드카드 인덱스를 사용하는 대신 개별적으로 경로를 인덱싱해야 합니다.

### <a name="limitations"></a>제한 사항

와일드카드 인덱스는 다음 인덱스 유형 또는 속성을 지원하지 않습니다.

* 복합
* TTL
* 고유한

**MongoDB와 달리** Azure Cosmos DB의 API for MongoDB에는 다음에 대한 와일드카드 인덱스를 사용할 수 **없습니다**.

* 여러 특정 필드를 포함하는 와일드카드 인덱스 만들기

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection " :
          {
             "children.givenName" : 1,
             "children.grade" : 1
          }
      }
  )
  ```

* 여러 특정 필드를 제외하는 와일드카드 인덱스 만들기

  ```json
  db.coll.createIndex(
      { "$**" : 1 },
      { "wildcardProjection" :
          {
             "children.givenName" : 0,
             "children.grade" : 0
          }
      }
  )
  ```

또는 여러 와일드카드 인덱스를 만들 수 있습니다.

## <a name="index-properties"></a>인덱스 속성

다음 작업은 유선 프로토콜 버전 4.0을 지원하는 계정 및 그 이전 버전을 지원하는 계정에서 일반적입니다. [지원되는 인덱스 및 인덱싱된 속성](feature-support-40.md#indexes-and-index-properties)에 대해 자세히 알아볼 수 있습니다.

### <a name="unique-indexes"></a>고유 인덱스

[고유 인덱스](../unique-keys.md)는 두 개 이상의 문서에서 인덱싱된 필드에 대해 동일한 값을 포함하지 않도록 하는 데 유용합니다.

> [!IMPORTANT]
> 현재 컬렉션이 비어 있는 경우(문서를 포함하지 않음)에만 고유 인덱스를 만들 수 있습니다.

다음 명령은 `student_id` 필드에 고유 인덱스를 만듭니다.

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 4
}
```

분할된 컬렉션의 경우 고유 인덱스를 만들려면 분할(파티션) 키를 제공해야 합니다. 즉, 분할된 컬렉션의 모든 고유 인덱스는 필드 중 하나가 파티션 키인 복합 인덱스입니다.

다음 명령은 `student_id` 및 `university` 필드에서 고유 인덱스를 사용하여 분할된 컬렉션 ```coll```(분할 키는 ```university```)을 만듭니다.

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "university" : 1, "student_id" : 1 }, {unique:true});
{
    "_t" : "CreateIndexesResponse",
    "ok" : 1,
    "createdCollectionAutomatically" : false,
    "numIndexesBefore" : 3,
    "numIndexesAfter" : 4
}
```

앞의 예제에서 ```"university":1``` 절을 생략하면 다음 메시지와 함께 오류가 반환됩니다.

*분할 키 패턴이 { university : 1.0 }인 {student_id : 1.0}에 대해 고유 인덱스를 만들 수 없습니다.*

### <a name="ttl-indexes"></a>TTL 인덱스

특정 컬렉션에서 문서 만료를 사용하려면 [TTL(Time to Live) 인덱스](../time-to-live.md)를 만들어야 합니다. TTL 인덱스는 `_ts` 필드에 `expireAfterSeconds` 값을 사용한 인덱스입니다.

예:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

위의 명령은 마지막 10초에서 수정되지 않은 ```db.coll``` 컬렉션의 문서를 삭제합니다.

> [!NOTE]
> **_ts** 필드는 Azure Cosmos DB 전용이며 MongoDB 클라이언트에서 액세스할 수 없습니다. 문서의 마지막 수정에 대한 타임스탬프가 포함된 예약된(시스템) 속성입니다.

## <a name="track-index-progress"></a>인덱스 진행률 추적

Azure Cosmos DB의 API for MongoDB 버전 3.6 이상에서는 데이터베이스 인스턴스의 인덱스 진행률을 추적하는 `currentOp()` 명령을 지원합니다. 이 명령은 데이터베이스 인스턴스에서 진행 중인 작업에 대한 정보가 포함된 문서를 반환합니다. `currentOp` 명령을 사용하여 원시 MongoDB의 모든 진행 중인 작업을 추적할 수 있습니다. Azure Cosmos DB의 API for MongoDB에서 이 명령은 인덱스 작업 추적만 지원합니다.

다음은 `currentOp` 명령을 사용하여 인덱스 진행률을 추적하는 방법을 보여주는 몇 가지 예입니다.

* 컬렉션에 대한 인덱스 진행률 가져오기:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* 데이터베이스의 모든 컬렉션에 대한 인덱스 진행률 가져오기:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Azure Cosmos 계정의 모든 데이터베이스 및 컬렉션에 대한 인덱스 진행률 가져오기:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>인덱스 진행률 출력의 예

인덱스 진행률 세부 정보에는 현재 인덱스 작업에 대한 진행률이 표시됩니다. 다음은 인덱스 진행률의 여러 단계에 대한 출력 문서 형식을 보여주는 예제입니다.

* "foo" 컬렉션과 "bar" 데이터베이스의 인덱스 작업(60% 완료)은 다음과 같은 출력 문서를 갖게 됩니다. `Inprog[0].progress.total` 필드는 목표 완료 백분율로 100을 표시합니다.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

* "foo" 컬렉션과 "bar" 데이터베이스에서 인덱스 작업을 막 시작한 경우에는 측정 가능한 수준에 도달할 때까지 출력 문서의 진행률이 0%로 표시될 수 있습니다.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

* 진행 중인 인덱스 작업이 완료되면 출력 문서에 빈 `inprog` 작업이 표시됩니다.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>백그라운드 인덱스 업데이트

**백그라운드** 인덱스 속성에 지정된 값에 관계 없이 인덱스 업데이트는 항상 백그라운드에서 수행됩니다. 인덱스 업데이트는 다른 데이터베이스 작업 보다 낮은 우선 순위로 RU(요청 단위)를 사용하므로 인덱스를 변경해도 쓰기, 업데이트 또는 삭제에 대한 가동 중지 시간이 발생하지 않습니다.

새 인덱스를 추가하는 경우 읽기 가용성에는 영향을 주지 않습니다. 인덱스 변환이 완료되면 쿼리에서 새 인덱스만 활용합니다. 인덱스를 변환하는 동안 쿼리 엔진은 기존 인덱스를 계속 사용하므로 인덱싱 변경을 시작하기 전에 관찰한 내용에 대한 인덱싱 변환 중에 유사한 읽기 성능을 확인할 수 있습니다. 새 인덱스를 추가하는 경우 쿼리 결과가 불완전하거나 일치하지 않을 위험도 없습니다.

인덱스를 제거하고 삭제된 인덱스에 대한 필터가 있는 쿼리를 바로 실행하는 경우 인덱스 변환이 완료될 때까지 결과가 일관적이고 완전하지 않을 수 있습니다. 인덱스를 제거하는 경우 새로 제거된 이러한 인덱스에서 쿼리가 필터링하면 쿼리 엔진에서 일관되거나 완전한 결과를 제공하지 않습니다. 대부분의 개발자는 인덱스를 삭제하지 않고 즉시 쿼리를 시도하므로 실제로는 이러한 상황이 발생할 가능성이 낮습니다.

> [!NOTE]
> [인덱스 진행률을 추적](#track-index-progress)할 수 있습니다.

## <a name="reindex-command"></a>ReIndex 명령

`reIndex` 명령은 컬렉션의 모든 인덱스를 다시 만듭니다. 대부분의 경우 이 명령은 필요하지 않습니다. 그러나 드문 경우이지만 `reIndex` 명령을 실행한 후 쿼리 성능이 향상될 수 있습니다.

다음 구문을 사용하여 `reIndex` 명령을 실행할 수 있습니다.

`db.runCommand({ reIndex: <collection> })`

아래 구문을 사용하여 `reIndex` 명령을 실행해야 하는지 여부를 확인할 수 있습니다.

`db.runCommand({"customAction":"GetCollection",collection:<collection>, showIndexes:true})`

샘플 출력:

```
{
        "database" : "myDB",
        "collection" : "myCollection",
        "provisionedThroughput" : 400,
        "indexes" : [
                {
                        "v" : 1,
                        "key" : {
                                "_id" : 1
                        },
                        "name" : "_id_",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                },
                {
                        "v" : 1,
                        "key" : {
                                "b.$**" : 1
                        },
                        "name" : "b.$**_1",
                        "ns" : "myDB.myCollection",
                        "requiresReIndex" : true
                }
        ],
        "ok" : 1
}
```

`reIndex`가 필요한 경우 **requiresReIndex** 는 true가 됩니다. `reIndex`가 필요하지 않은 경우 이 속성은 생략됩니다.

## <a name="migrate-collections-with-indexes"></a>인덱스를 사용하여 컬렉션 마이그레이션

현재는 컬렉션에 문서가 포함되지 않은 경우에만 고유 인덱스를 만들 수 있습니다. 많이 사용되는 MongoDB 마이그레이션 도구는 데이터를 가져온 후 고유 인덱스를 만들도록 시도합니다. 이 문제를 피하려면 마이그레이션 도구를 사용할 수 있도록 하는 대신 해당 컬렉션 및 고유 인덱스를 수동으로 만들 수 있습니다. 명령줄에서 `--noIndexRestore` 플래그를 사용하여 ```mongorestore```에 대해 이 동작을 수행할 수 있습니다.

## <a name="indexing-for-mongodb-version-32"></a>MongoDB 버전 3.2에 대한 인덱싱

사용 가능한 인덱싱 기능 및 기본값은 MongoDB 유선 프로토콜의 버전 3.2와 호환되는 Azure Cosmos 계정에서 다릅니다. [계정의 버전을 확인](feature-support-36.md#protocol-support)하고 [버전 3.6으로 업그레이드](upgrade-mongodb-version.md)할 수 있습니다.

버전 3.2를 사용하는 경우 이 섹션에서는 버전 3.6 이상과의 주요 차이점을 간략하게 설명합니다.

### <a name="dropping-default-indexes-version-32"></a>기본 인덱스 삭제(버전 3.2)

Azure Cosmos DB의 API for MongoDB 버전 3.6 이상과 달리 버전 3.2는 기본적으로 모든 속성을 인덱싱합니다. 다음 명령을 사용하여 컬렉션(```coll```)에 대한 이러한 기본 인덱스를 삭제할 수 있습니다.

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

기본 인덱스를 삭제한 후에는 버전 3.6 이상에서와 같이 더 많은 인덱스를 추가할 수 있습니다.

### <a name="compound-indexes-version-32"></a>복합 인덱스(버전 3.2)

복합 인덱스는 문서의 여러 필드에 대한 참조를 유지합니다. 복합 인덱스를 만들려는 경우 [버전 3.6 또는 4.0으로 업그레이드](upgrade-mongodb-version.md)합니다.

### <a name="wildcard-indexes-version-32"></a>와일드카드 인덱스(버전 3.2)

와일드카드 인덱스를 만들려는 경우 [버전 4.0 또는 3.6으로 업그레이드](upgrade-mongodb-version.md)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 인덱싱](../index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../time-to-live.md)
* 분할 및 인덱싱 간의 관계에 대해 알아보려면 [Azure Cosmos 컨테이너를 쿼리](../how-to-query-container.md)하는 방법 문서를 참조하세요.
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
