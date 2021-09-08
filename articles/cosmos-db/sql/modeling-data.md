---
title: Azure Cosmos DB에서 데이터 모델링
titleSuffix: Azure Cosmos DB
description: NoSQL 데이터베이스의 데이터 모델링과 관계형 데이터베이스 및 문서 데이터베이스의 데이터 모델링 간 차이점에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 7a056ce90ca046acbc134d2e16d7ffb8779afbdb
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116424"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Azure Cosmos DB에서의 데이터 모델링
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB와 같은 스키마 없는 데이터베이스에서는 비정형 및 반정형 데이터를 쉽게 저장하고 쿼리할 수 있지만, 성능, 스케일링 수준, 최저 비용 측면에서 서비스를 최대한 활용하려면 시간을 할애하여 데이터 모델을 신중하게 결정해야 합니다.

데이터를 어떻게 저장할 것인가? 애플리케이션에서 데이터를 검색 및 쿼리하는 방법은 무엇인가? 애플리케이션 부하가 읽기 또는 쓰기 중 어디에 집중되어 있는가?

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

* 데이터 모델링은 무엇이고 어떻게 사용해야 하는가?
* 관계형 데이터베이스와 다른 Azure Cosmos DB에서 데이터는 어떻게 모델링되는가?
* 비관계형 데이터베이스에서 데이터 관계는 어떻게 표현하는가?
* 데이터를 포함해야 하는 경우 및 데이터에 링크하는 경우는 언제인가?

## <a name="embedding-data"></a>데이터 포함

Azure Cosmos DB에서 데이터 모델링을 시작할 때 엔터티를 JSON 문서로 표시되는 **자체 포함 항목** 으로 처리해 보세요.

비교를 위해 먼저 관계형 데이터베이스에서 데이터를 모델링하는 방법을 살펴보겠습니다. 다음 예제에서는 관계형 데이터베이스에서 사용자가 저장되는 방법을 보여 줍니다.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="관계형 데이터베이스 모델" border="false":::

관계형 데이터베이스로 작업할 때 전략은 모든 데이터를 정규화하는 것입니다. 데이터를 정규화하려면 일반적으로 사용자 등의 엔터티를 가져와 개별 구성 요소로 분석해야 합니다. 위 예제에서 사용자는 여러 개의 주소 레코드뿐 아니라 여러 개의 연락처 세부 레코드를 포함할 수 있습니다. 형식 등의 공통 필드를 추가로 추출하면 연락처 세부 정보를 더 세부적으로 분석할 수 있습니다. 주소의 경우도 마찬가지로, 각 레코드는 ‘집’ 또는 ‘회사’ 형식일 수 있습니다. 

데이터를 정규화할 때의 원칙은 각 레코드에서 **중복된 데이터 저장을 피하고** 데이터를 참조하는 것입니다. 이 예제에서 모든 연락처 세부 정보와 주소를 포함하여 사용자를 읽으려면 JOINS를 사용하여 데이터를 런타임에 효율적으로 다시 작성(또는 비정규화)해야 합니다.

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

연락처 세부 정보 및 주소를 사용해서 단일 사용자를 업데이트하려면 여러 개별 테이블 간의 쓰기 작업이 필요합니다.

이제 Azure Cosmos DB에서 자체 포함 엔터티와 동일한 데이터를 모델링하는 방법을 살펴보겠습니다.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

위의 방법을 사용하여 이 사용자와 관련된 모든 정보(예: 연락처 세부 정보, 주소)를 ‘단일 JSON’ 문서에 **포함** 함으로써 사용자 레코드를 **비정규화** 했습니다.
또한 고정된 스키마로 제한되지 않기 때문에 다른 도형의 연락처 세부 정보를 완전히 포함하는 것과 같은 유연성이 있습니다.

이제 데이터베이스에서 전체 사용자 레코드를 검색하는 작업은 단일 컨테이너와 단일 항목에 대해 **단일 읽기 작업** 을 수행하는 것과 같습니다. 연락처 세부 정보와 주소로 사용자 레코드를 업데이트하는 작업도 단일 항목에 대해 **단일 쓰기 작업** 을 수행하는 것과 같습니다.

데이터를 비정규화하면 애플리케이션이 일반 작업 수행을 위해 실행해야 하는 쿼리 및 업데이트 수가 줄어듭니다.

### <a name="when-to-embed"></a>포함해야 하는 경우

일반적으로 다음과 같은 경우에 포함된 데이터 모델을 사용합니다.

* 엔터티 간에 **포함** 관계가 있습니다.
* 엔터티 사이에 **일 대 몇(one-to-few)** 의 관계가 있습니다.
* 포함된 데이터가 **자주 변경** 되지 않습니다.
* **제한 없이** 증가하지 않는 포함 데이터가 있습니다.
* **자주 함께 쿼리되는** 포함 데이터가 있습니다.

> [!NOTE]
> 일반적으로 비정규화된 데이터 모델은 **읽기** 성능이 더 뛰어납니다.

### <a name="when-not-to-embed"></a>포함하지 않는 경우

경험상 Azure Cosmos DB에서는 모든 것을 비정규화하고 모든 데이터를 단일 항목에 포함하는 것이 좋지만, 이 경우 방지해야 하는 몇 가지 상황이 발생할 수 있습니다.

다음 JSON 코드 조각을 예로 들어 보겠습니다.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

이 코드 조각은 일반적인 블로그 또는 CMS 시스템을 모델링할 때 나타날 수 있는 포함된 주석이 있는 포스트 엔터티를 나타낼 수 있습니다. 이 예제에서의 문제는 주석 배열이 **바인딩되지 않음** 으로써, 단일 포스트가 가질 수 있는 주석 수에 대한 제한(실질적인)이 없다는 것입니다. 이로 인해 항목 크기가 무한히 커질 수 있으므로 문제가 될 수 있습니다.

항목 크기가 증가함에 따라 유선을 통한 데이터 전송과 대규모 항목 읽기 및 업데이트 성능이 영향을 받습니다.

이 경우에는 다음과 같은 데이터 모델을 고려하는 것이 더 효율적입니다.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

이 모델은 고정된 특성 세트를 사용하는 배열인 게시물 컨테이너에 최근 3개 주석이 포함되어 있습니다. 다른 주석은 100개 주석의 배치로 그룹화되어 개별 항목으로 저장됩니다. 일괄 처리의 크기를 100으로 선택한 이유는 이 예제의 가상 애플리케이션에서 사용자가 한 번에 로드할 수 있는 주석의 수가 100개로 제한되기 때문입니다.  

데이터 포함이 이상적인 대안이 될 수 없는 또 다른 경우는 포함된 데이터가 종종 항목 간에 사용되고 자주 변경되는 경우입니다.

다음 JSON 코드 조각을 예로 들어 보겠습니다.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

이 예제는 한 사용자의 주식 포트폴리오를 나타낼 수 있습니다. 여기서는 각 포트폴리오 문서에 주식 정보를 포함하도록 선택했습니다. 주식 거래 애플리케이션과 같이 관련 데이터가 자주 변경되는 환경에서, 자주 변경되는 데이터를 포함시키면 주식이 거래될 때마다 포트폴리오 문서를 계속해서 업데이트해야 합니다.

*zaza* 주식은 하루에도 수백 번 거래될 수 있으며 수천 명의 사용자 포트폴리오에 *zaza* 주식이 포함되어 있을 수 있습니다. 위와 같은 데이터 모델에서는 매일 여러 번 수천 개의 포트폴리오 문서를 업데이트해야 하므로, 시스템의 확장성이 낮아질 수 있습니다.

## <a name="referencing-data"></a>데이터 참조

데이터 포함 방식은 많은 경우에 효과적일 수 있지만, 데이터 비정규화로 인해 얻는 이득보다 잃게 되는 손실이 큰 경우도 있습니다. 그러면 이제 무엇을 해야 할까요?

관계형 데이터베이스는 엔터티 간의 관계를 생성할 수 있는 유일한 대안이 아닙니다. 문서 데이터베이스에서는 다른 문서의 데이터와 관련된 정보를 하나의 문서에 저장할 수 있습니다. 여기서는 Azure Cosmos DB의 관계형 데이터베이스나 다른 문서 데이터베이스에 더 적합한 시스템을 빌드하는 것보다 단순한 관계로도 충분하고 매우 유용할 수 있다는 것을 보여드리고자 합니다.

아래의 JSON에서도 이전 단락의 주식 포트폴리오 예제를 사용하지만, 이번에는 포함시키는 대신 포트폴리오의 주식 항목에 대해 설명하고자 합니다. 이렇게 하면 주식 항목이 하루 중 자주 변경되더라도 업데이트해야 하는 문서는 단일 주식 문서뿐입니다.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

하지만 이 방식에서 나타날 수 있는 분명한 단점은 애플리케이션이 사용자 포트폴리오를 표시할 때 저장된 각 주식에 대한 정보를 표시해야 할 경우에 나타납니다. 이 경우에는 각 주식 문서에 대한 정보를 로드하기 위해 데이터베이스를 여러 번에 걸쳐서 경유해야 합니다. 여기에서는 하루 중 자주 발생하는 쓰기 작업의 효율을 향상시키지만, 그에 따라 이 특정 시스템의 성능에 대한 영향이 덜할 수 있는 읽기 작업 성능은 희생하기로 결정했습니다.

> [!NOTE]
> 정규화된 데이터 모델은 서버에 대해 **더 많은 라운드 트립이 요구될 수 있습니다.**

### <a name="what-about-foreign-keys"></a>외래 키의 경우는 어떻게 될까요?

현재는 제약 조건이라는 개념이 없으므로, 외래 키 등 문서에 포함될 수 있는 모든 문서 간 관계는 실질적으로 "약한 링크"이며 데이터베이스 자체에서 확인되지 않습니다. 문서가 참조하는 데이터가 실제로 존재하는지 확인하기 위해서는 애플리케이션에서 또는 서버측 트리거 또는 Azure Cosmos DB의 저장 프로시저를 사용해서 이를 수행해야 합니다.

### <a name="when-to-reference"></a>참조하는 경우

일반적으로 정규화된 데이터 모델은 다음과 같은 경우에 사용합니다.

* **일대다** 관계를 나타내는 경우
* **다대다** 관계를 나타내는 경우
* 관련 데이터가 **자주 변경** 되는 경우
* 참조 데이터가 **바인딩되지 않는** 경우

> [!NOTE]
> 일반적으로 정규화에서는 **쓰기** 성능이 더 뛰어납니다.

### <a name="where-do-i-put-the-relationship"></a>관계는 어디에 배치해야 할까요?

관계의 성장은 참조를 저장할 문서를 결정하는 데 도움이 됩니다.

아래의 JSON에서는 발행자와 책을 모델링합니다.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

발행자당 책 수가 작고 제한적으로 증가할 경우, 책 참조를 해당 발행자 문서에 저장해도 좋을 수 있습니다. 하지만 발행자당 책 수가 제한적이지 않으면, 이 데이터 모델의 경우 위의 발행자 문서 예제와 같이 배열이 변하기 쉽고 계속 증가할 수 있습니다.

이를 조금만 변경하면 동일한 데이터를 제공하지만, 쉽게 변경되는 대규모 컬렉션을 피할 수 있는 모델을 만들 수 있습니다.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

위 예제에서는 발행자 문서에 바인딩되지 않은 컬렉션을 배치했습니다. 그리고 각 책 문서에서 발행자에 대한 참조만 두었습니다.

### <a name="how-do-i-model-manymany-relationships"></a>다대다 관계는 어떻게 모델링할 수 있을까요?

관계형 데이터베이스에서 *다대다* 관계는 다른 테이블의 레코드를 단순히 하나로 조인하는 조인 테이블을 사용해서 모델링되는 경우가 많습니다.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="테이블 조인" border="false":::

여기에서도 문서를 사용해서 동일한 방식을 따르고 다음과 비슷하게 보이는 데이터 모델을 만들고 싶을 수도 있습니다.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

이러한 모델도 작동은 가능합니다. 하지만 특정 저자와 해당 저자의 책을 로드하거나 특정 책과 해당 책의 저자를 로드하려면 항상 데이터베이스에 대해 2개 이상의 추가 쿼리를 실행해야 할 것입니다. 조인 문서에 대해 쿼리를 한 번 수행하고 조인되는 실제 문서를 인출하기 위해 또 다른 쿼리를 수행해야 합니다.

이 조인 테이블이 수행하는 작업이 두 가지 데이터를 하나로 묶는 작업뿐이라면 이를 완전히 삭제할 수도 있을 것입니다.
다음을 고려해보세요.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

특정 저자가 있으면 그가 작성한 책이 무엇인지 즉시 알 수 있고, 반대로 로드된 책 문서가 있으면 해당 저자의 ID를 알 수 있습니다. 이렇게 하면 조인 테이블에 대한 중간 쿼리를 절약해서 애플리케이션에서 수행해야 하는 서버 라운드 트립 수를 줄일 수 있습니다.

## <a name="hybrid-data-models"></a>하이브리드 데이터 모델

지금까지 데이터 포함(또는 비정규화)과 참조(또는 정규화)에 대해 살펴봤습니다. 이러한 데이터 포함과 참조는 위에서 살펴본 것처럼 각각 장점과 단점을 갖고 있습니다.

하지만 어느 쪽이든 장단점이 있으므로 두 방식을 혼합해서 사용해도 좋습니다.

애플리케이션의 특정 사용 패턴 및 워크로드에 따라 포함 및 참조 데이터를 혼합하는 것이 적합할 수 있으며, 애플리케이션 논리를 단순화하면서 서버 라운드 트립도 줄이고, 적절한 수준으로 성능을 유지할 수 있습니다.

다음과 같은 JSON을 고려해보세요.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

여기에서는 주로 포함된 모델을 따랐으며, 다른 엔터티의 데이터가 최상위 문서에 포함되지만 다른 데이터는 참조됩니다.

책 문서를 보면 저자 배열을 볼 때 몇 가지 흥미로운 필드를 발견할 수 있습니다. 여기에는 정규화된 모델의 표준 방법인 저자 문서를 다시 참조하기 위해 사용하는 필드인 `id` 필드가 있지만, `name` 및 `thumbnailUrl`도 있습니다. `id`만 사용하고 애플리케이션이 “link”를 통해 필요한 추가 정보를 해당 저자 문서에서 가져오도록 할 수도 있지만, 이 애플리케이션에서는 표시된 모든 책과 함께 저자 이름과 썸네일 사진을 표시하므로 저자에서 **일부** 데이터를 비정규화하여 목록에 있는 책당 서버 왕복을 줄일 수 있습니다.

물론, 저자 이름이 변경되거나 사진을 업데이트하려는 경우 저자가 출판한 모든 책에 대해 업데이트를 수행해야 하지만, 저자가 자신의 이름을 자주 변경하지 않는다는 가정에 따라 이 애플리케이션에서 이러한 정도는 설계상으로 허용되는 결정입니다.  

위 예제에서는 읽기 작업의 고비용 처리를 줄이기 위해 **사전 계산된 집계** 값이 있습니다. 이 예제에서 저자의 문서에 포함된 일부 데이터는 런타임에 계산되는 데이터입니다. 새 책이 게시될 때마다, 책 문서가 생성됩니다. **그리고** countOfBooks 필드가 특정 저자에 대해 존재하는 책 문서 번호를 기준으로 계산된 값으로 설정됩니다. 이러한 최적화는 읽기를 최적화하기 위해 읽기 계산을 수행할 수 있는 읽기에 집중된 시스템에서 효과적일 수 있습니다.

모델에 사전 계산된 필드를 포함할 수 있는 기능은 Azure Cosmos DB에서 **다중 문서 트랜잭션** 이 지원되기 때문에 가능합니다. 많은 NoSQL 저장소에서는 문서 간 트랜잭션을 수행할 수 없기 때문에 이러한 제한으로 인해 "항상 모든 것으로 포함"하는 디자인을 선호합니다. Azure Cosmos DB에서는 서버 쪽 트리거 또는 저장 프로시저를 사용해서 ACID 트랜잭션 내에서 책을 삽입하고 저자를 업데이트할 수 있습니다. 이제 데이터를 일관되게 유지하기 위해 하나의 문서에 모든 것을 포함시킬 **필요가 없습니다**.

## <a name="distinguishing-between-different-document-types"></a>여러 문서 형식 구별

일부 시나리오에서는 동일한 컬렉션에 여러 문서 형식을 포함할 수 있습니다. 일반적으로 동일한 [파티션](../partitioning-overview.md)에 여러 관련 문서를 배치하려는 경우에 해당합니다. 예를 들어 동일한 컬렉션에 책과 책 리뷰를 모두 배치하고 `bookId`로 분할할 수 있습니다. 이 경우, 일반적으로 책과 책 리뷰를 구별하기 위해 유형을 식별하는 필드가 있는 문서에 추가하는 것이 좋습니다.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서 가장 중요한 사항은 스키마가 없는 환경에서의 데이터 모델링도 이전과 같이 중요하다는 것을 이해하는 것입니다.

화면에 데이터를 표시하는 방법이 하나만 있지 않은 것처럼 데이터를 모델링하는 방법도 하나만 있는 것이 아닙니다. 애플리케이션을 이해하고 데이터를 생산, 소비 및 처리하는 방법을 이해해야 합니다. 그런 다음 여기에 제공된 일부 지침을 적용해서 애플리케이션에 즉시 필요한 사항을 처리할 수 있는 모델을 제작 방법을 설정할 수 있습니다. 애플리케이션에 변경이 필요한 경우에는 스키마가 없는 데이터베이스의 유연성을 활용해서 변경 사항을 포용하고 데이터 모델을 쉽게 발전시킬 수 있습니다.

* Azure Cosmos DB에 대한 자세한 내용은 서비스의 [설명서](https://azure.microsoft.com/documentation/services/cosmos-db/) 페이지를 참조하세요.

* 여러 파티션에 데이터를 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 데이터 분할](../partitioning-overview.md)을 참조하세요.

* 실제 예제를 사용하여 Azure Cosmos DB에서 데이터를 모델링하고 분할하는 방법을 알아보려면 [데이터 모델링 및 분할 - 실제 예제](how-to-model-partition-example.md)를 참조하세요.

* [Azure Cosmos DB에서 데이터를 모델링하고 분할](/learn/modules/model-partition-data-azure-cosmos-db/)하는 방법에 관한 학습 모듈을 참조하세요.

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
