---
title: Azure Cosmos DB의 API for MongoDB를 사용하여 데이터 쿼리
description: MongoDB 셸 명령을 사용하여 Azure Cosmos DB의 API for MongoDB에서 데이터를 쿼리하는 방법 알아보기
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 2e3604447a5909e7812f5a6acd64468cb0846d12
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354235"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB를 사용하여 데이터 쿼리
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB의 API for MongoDB](mongodb-introduction.md)는 [MongoDB 쿼리](https://docs.mongodb.com/manual/tutorial/query-documents/)를 지원합니다. 

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * MongoDB 셸을 사용하여 Cosmos 데이터베이스에 저장된 데이터 쿼리

이 문서의 예제를 사용하여 시작하고 [MongoDB 셸을 사용하여 Azure Cosmos DB 쿼리](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) 비디오를 시청할 수 있습니다.

## <a name="sample-document"></a>샘플 문서

이 문서의 쿼리에서는 다음 샘플 문서를 사용합니다.

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
        "gender": "female", "grade": 1,
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
## <a name="example-query-1"></a><a id="examplequery1"></a>예제 쿼리 1 

위의 샘플 가족 문서를 고려해 볼 때 다음 쿼리에서는 ID 필드가 `WakefieldFamily`와 일치하는 문서를 반환합니다.

**쿼리**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**결과**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
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
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>예제 쿼리 2 

다음 쿼리에서는 가족의 모든 자식을 반환합니다. 

**쿼리**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**결과**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
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
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a>예제 쿼리 3 

다음 쿼리는 등록된 모든 가족을 반환합니다. 

**쿼리**

```bash
db.families.find( { "isRegistered" : true })
``` 

**결과**

문서가 반환되지 않습니다. 

## <a name="example-query-4"></a><a id="examplequery4"></a>예제 쿼리 4

다음 쿼리는 등록되지 않은 모든 가족을 반환합니다. 

**쿼리**

```bash
db.families.find( { "isRegistered" : false })
``` 

**결과**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a>예제 쿼리 5

다음 쿼리는 등록되지 않았고 거주 지역(주)이 NY인 가족을 모두 반환합니다. 

**쿼리**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**결과**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a>예제 쿼리 6

다음 쿼리에서는 자식(children)의 학년(grade)이 8인 모든 가족을 반환합니다.

**쿼리**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**결과**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a>예제 쿼리 7

다음 쿼리에서는 children 배열의 크기가 3인 모든 가족을 반환합니다.

**쿼리**

```bash
db.Family.find( {children: { $size:3} } )
```

**결과**

자식이 셋 이상인 가족이 없으므로 결과가 반환되지 않습니다. 매개 변수가 2일 때만 이 쿼리가 성공하여 전체 문서를 반환합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Cosmos DB의 API for MongoDB를 사용하여 쿼리하는 방법을 알아보았습니다.

이제 전 세계로 데이터를 배포하는 방법을 알아보려면 다음 자습서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [전 세계로 데이터 배포](tutorial-global-distribution-sql-api.md)

