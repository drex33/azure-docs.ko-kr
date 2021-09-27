---
title: Azure Cosmos DB 그래프를 통해 시스템 문서 속성 액세스
description: Gremlin API를 통해 Cosmos DB 시스템 문서 속성을 읽고 쓰는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/16/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 8a0574d5622ae0ceceb52be72ccd8c5d99a18529
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614810"
---
# <a name="system-document-properties"></a>시스템 문서 속성
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Azure Cosmos DB의 모든 문서에는 ```_ts```, ```_self```, ```_attachments```, ```_rid```, ```_etag``` 등의 [시스템 속성](/rest/api/cosmos-db/databases)이 있습니다. 또한 Gremlin 엔진이 모서리에 ```inVPartition``` 및 ```outVPartition``` 속성을 추가합니다. 기본적으로 이러한 속성은 통과에 사용할 수 없습니다. 그러나 Gremlin 통과에 특정 속성이나 모든 속성을 포함할 수 있습니다.

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

이 속성은 낙관적 동시성 제어에 사용됩니다. 애플리케이션이 몇 개의 분리된 통과로 작업을 나누어야 하는 경우 eTag 속성을 사용하여 동시 쓰기에서 데이터 손실을 방지할 수 있습니다.

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL(Time to Live)

컬렉션에서 문서 만료가 사용되고 문서에 `ttl` 속성이 설정되어 있으면, 이 속성을 일반 꼭짓점 또는 모서리 속성으로 Gremlin 통과에 사용할 수 있습니다. `ProjectionStrategy`는 TTL(Time to Live) 속성 노출을 사용하도록 설정하는 데 필요하지 않습니다.

* 다음 명령을 사용하여 새 꼭짓점에서 Time-to-Live를 설정합니다.

  ```console
  g.addV(<ID>).property('ttl', <expirationTime>)
  ```

  예를 들어 다음 순회를 통해 만든 꼭짓점은 *123초* 후에 자동으로 삭제됩니다.

  ```console
  g.addV('vertex-one').property('ttl', 123)
  ```

* 다음 명령을 사용하여 기존 꼭짓점에서 Time-to-Live를 설정합니다.

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).property('ttl', <expirationTime>)
  ```

* 꼭짓점에서 Time-to-Live 속성을 적용해도 에지에 자동으로 적용되지는 않습니다. 에지는 데이터베이스 저장소의 독립적인 레코드이기 때문입니다. 다음 명령을 사용하여 꼭짓점의 모든 들어오고 나가는 가장자리와 꼭짓점에서 Time-to-Live를 설정합니다.

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).as('v').bothE().hasNot('ttl').property('ttl', <expirationTime>)
  ```

컨테이너의 TTL을 -1로 설정하거나 Azure Portal **On(기본값 없음)으로** 설정할 수 있습니다. 그러면 항목에 TTL 값이 명시적으로 설정되어 있지 않으면 모든 항목에 대해 TTL이 무한합니다.

## <a name="next-steps"></a>다음 단계
* [Cosmos DB 낙관적 동시성](../faq.yml#how-does-the-sql-api-provide-concurrency-)
* Azure Cosmos DB의 [TTL(Time to Live)](../time-to-live.md)
