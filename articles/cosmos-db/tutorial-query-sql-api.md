---
title: '자습서: Azure Cosmos DB에서 SQL을 사용하여 쿼리하는 방법'
description: '자습서: 쿼리 플레이그라운드를 통해 Azure Cosmos DB에서 SQL 쿼리를 사용하여 쿼리하는 방법 알아보기'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: 33825ba4d340e8422ed8a61e6dbb2e219aaa5c39
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123034871"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>자습서: SQL API를 사용하여 Azure Cosmos DB 쿼리
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB [SQL API](./introduction.md)는 SQL을 사용하여 문서를 쿼리할 수 있도록 지원합니다. 이 문서에서는 샘플 문서 및 두 가지 샘플 SQL 쿼리와 결과를 제공합니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * SQL을 사용한 데이터 쿼리

## <a name="sample-document"></a>샘플 문서

이 문서의 SQL 쿼리에서는 다음과 같은 샘플 문서를 사용합니다.

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

## <a name="where-can-i-run-sql-queries"></a>SQL 쿼리를 실행할 수 있는 위치

Azure Portal의 Data Explorer와 [REST API 및 SDK](sql-api-sdk-dotnet.md)를 통해 쿼리를 실행할 수 있습니다.

SQL 쿼리에 대한 자세한 내용은 다음을 참조하세요.
* [SQL 쿼리 및 SQL 구문](sql-query-getting-started.md)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 Azure Cosmos DB 계정과 컬렉션이 있다고 가정합니다. 이러한 리소스가 없나요? [5분 빠른 시작](create-cosmosdb-resources-portal.md)을 완료하세요.

## <a name="example-query-1"></a>예제 쿼리 1

위의 샘플 가족 문서를 고려해 볼 때 다음 SQL 쿼리에서는 ID 필드가 `WakefieldFamily`와 일치하는 문서를 반환합니다. `SELECT *` 문이므로 쿼리 결과는 완전한 JSON 문서입니다.

**쿼리**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**결과**

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

## <a name="example-query-2"></a>예제 쿼리 2

다음 쿼리에서는 ID가 `WakefieldFamily`와 일치하는 가족의 지정된 모든 자식 이름을 반환합니다.

**쿼리**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**결과**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * SQL을 사용하여 쿼리하는 방법  

이제 전 세계로 데이터를 배포하는 방법을 알아보려면 다음 자습서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [전 세계로 데이터 배포](tutorial-global-distribution-sql-api.md)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vcore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.