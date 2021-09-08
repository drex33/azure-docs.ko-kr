---
title: 실제 예를 사용하여 Azure Cosmos DB에서 데이터 모델링 및 분할
description: Azure Cosmos DB Core API를 사용하여 실제 예제를 모델링하고 분할하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: c975d49ca3790798e9014345defb81cb691ef094
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117117"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>실제 예제를 사용하여 Azure Cosmos DB에서 데이터를 모델링하고 분할하는 방법
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서는 실제 데이터 설계 연습을 처리하는 방법을 보여 주기 위해 [데이터 모델링](../modeling-data.md), [분할](../partitioning-overview.md) 및 [프로비저닝된 처리량](../request-units.md)과 같은 여러 Azure Cosmos DB 개념을 기반으로 하여 작성되었습니다.

일반적으로 관계형 데이터베이스를 사용하는 경우 데이터 모델을 설계하는 방법에 대한 습관과 직관을 구축했을 수 있습니다. 특정 제약 조건뿐만 아니라 Azure Cosmos DB의 고유한 장점으로 인해 이러한 모범 사례의 대부분은 제대로 변환되지 않고 최적이 아닌 솔루션으로 끌어들일 수 있습니다. 이 문서의 목표는 항목 모델링에서 엔터티 공동 배치 및 컨테이너 분할에 이르기까지 Azure Cosmos DB의 실제 사용 사례를 모델링하는 전체 프로세스를 안내하는 것입니다.

이 문서의 개념을 설명하는 [커뮤니티 생성 소스 코드를 다운로드하거나 봅니다](https://github.com/jwidmer/AzureCosmosDbBlogExample). 이 코드 샘플은 커뮤니티 기여자가 제공했으며 Azure Cosmos DB 팀은 유지 관리를 지원하지 않습니다.

## <a name="the-scenario"></a>시나리오

이 연습에서는 *사용자* 가 *게시물* 을 만들 수 있는 블로깅 플랫폼의 도메인을 고려합니다. 사용자는 이러한 게시물에 대해 *좋아요* 를 표시하고 *댓글* 을 추가할 수도 있습니다.

> [!TIP]
> *기울임꼴* 의 몇 가지 단어를 강조했습니다. 이러한 단어는 모델에서 조작해야 하는 "상황(things)"의 종류를 식별합니다.

사양에 추가해야 하는 요구 사항은 다음과 같습니다.

- 첫 페이지에는 최근에 만든 게시물의 피드가 표시됩니다.
- 사용자에 대한 모든 게시물, 게시물에 대한 모든 댓글, 게시물에 대한 모든 좋아요를 가져올 수 있습니다.
- 게시물은 작성자의 사용자 이름과 획득한 댓글 및 좋아요의 수와 함께 반환됩니다.
- 댓글 및 좋아요도 작성한 사용자의 사용자 이름과 함께 반환됩니다.
- 목록으로 표시되는 경우 게시물은 해당 콘텐츠의 잘린 요약만 제공해야 합니다.

## <a name="identify-the-main-access-patterns"></a>기본 액세스 패턴 식별

먼저, 솔루션의 액세스 패턴을 식별하여 초기 사양에 몇 가지 구조를 제공합니다. Azure Cosmos DB용 데이터 모델을 설계하는 경우 모델에서 해당 요청을 효율적으로 처리할 수 있도록 처리해야 하는 요청을 이해하는 것이 중요합니다.

전체 프로세스를 더 쉽게 수행하기 위해[CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation)에서 일부 어휘를 빌려 서로 다른 이러한 요청을 명령 또는 쿼리로 분류합니다. 여기서 명령은 쓰기 요청(즉 시스템 업데이트에 대한 의도)이고, 쿼리는 읽기 전용 요청입니다.

플랫폼에서 공개해야 하는 요청 목록은 다음과 같습니다.

- **[C1]** 사용자 만들기/편집
- **[Q1]** 사용자 검색
- **[C2]** 게시물 만들기/편집
- **[Q2]** 게시물 검색
- **[Q3]** 약식으로 사용자의 게시물 나열
- **[C3]** 댓글 만들기
- **[Q4]** 게시물의 댓글 나열
- **[C4]** 게시물에 대한 좋아요 표시
- **[Q5]** 게시물에 대한 좋아요 나열
- **[Q6]** 최근에 만든 *x* 개 게시물을 약식으로 나열(피드)

이 단계에서는 각 엔터티(사용자, 게시물 등)에 포함될 세부 정보에 대해 자세히 생각하지 않았습니다. 이 단계는 일반적으로 관계형 저장소에 대해 설계할 때 가장 먼저 처리해야 하는 단계 중 하나입니다. 왜냐하면 이러한 엔터티가 테이블, 열, 외래 키 등의 측면에서 어떻게 변환되는지 파악해야 하기 때문입니다. 스키마를 적용하지 않고 쓰는 문서 데이터베이스에 대한 우려는 훨씬 적습니다.

액세스 패턴을 처음부터 파악하는 것이 중요한 주된 이유는 이 요청 목록이 테스트 도구 모음이 되기 때문입니다. 데이터 모델을 반복할 때마다 각 요청을 검토하고 성능과 확장성을 확인합니다. 각 모델에서 사용되는 요청 단위를 계산하고 최적화합니다. 이러한 모든 모델은 기본 인덱싱 정책을 사용하며 특정 속성을 인덱싱하여 이를 재정의할 수 있으므로 RU 사용 및 대기 시간을 더욱 개선할 수 있습니다.

## <a name="v1-a-first-version"></a>V1: 첫 번째 버전

두 개의 컨테이너(`users` 및 `posts`)로 시작합니다.

### <a name="users-container"></a>users 컨테이너

다음 컨테이너는 사용자 항목만 저장합니다.

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

이 컨테이너를 `id`로 분할합니다. 즉, 해당 컨테이너 내의 각 논리 파티션에는 하나의 항목만 포함됩니다.

### <a name="posts-container"></a>posts 컨테이너

다음 컨테이너는 게시물, 댓글 및 좋아요를 호스팅합니다.

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

이 컨테이너를 `postId`로 분할합니다. 즉, goekd 컨테이너 내의 각 논리 파티션에는 하나의 게시물, 해당 게시물에 대한 모든 댓글 및 해당 게시물에 대한 모든 좋아요가 포함됩니다.

이 컨테이너에서 호스팅하는 세 가지 유형의 엔터티를 구분하기 위해 이 컨테이너에 저장된 항목에 `type` 속성을 도입했습니다.

또한 다음과 같은 이유로 관련 데이터를 포함하는 대신 참조하도록 선택했습니다(이러한 개념에 대한 자세한 내용은 [이 섹션](modeling-data.md) 참조).

- 사용자가 만들 수 있는 게시물의 수에 대한 상한이 없습니다.
- 게시물은 임의로 길 수 있습니다.
- 게시물에서 획득한 댓글과 좋아요에 대한 상한이 없습니다.
- 게시물 자체를 업데이트하지 않고도 게시물에 댓글 또는 좋아요를 추가할 수 있기를 원합니다.

## <a name="how-well-does-our-model-perform"></a>모델의 성능은 어떻나요?

이제 첫 번째 버전의 성능과 확장성을 평가하는 시간입니다. 이전에 식별된 각 요청에 대해 지연 시간과 소비하는 요청 단위의 크기를 측정합니다. 이 측정은 사용자당 5~50개의 게시물 및 게시물당 최대 25개의 댓글 및 100개의 좋아요가 있는 100,000명의 사용자가 포함된 더미 데이터 세트에 대해 수행되었습니다.

### <a name="c1-createedit-a-user"></a>[C1] 사용자 만들기/편집

이 요청은 `users` 컨테이너에서 항목을 만들거나 업데이트할 때 쉽게 구현할 수 있습니다. 요청은 `id` 파티션 키를 통해 모든 파티션에 잘 전달됩니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="users 컨테이너에 단일 항목 쓰기" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 7ms | 5.71RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] 사용자 검색

`users` 컨테이너에서 해당 항목을 읽어 사용자를 검색합니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="users 컨테이너에서 단일 항목 검색" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 2ms | 1RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] 게시물 만들기/편집

**[C1]** 과 마찬가지로 `posts` 컨테이너에 쓰기만 하면 됩니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="posts 컨테이너에 단일 항목 쓰기" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 9ms | 8.76RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] 게시물 검색

`posts` 컨테이너에서 해당 문서를 검색하는 것으로 시작합니다. 그러나 이것만으로는 충분하지 않습니다. 사양에 따라 게시물 작성자의 사용자 이름과 이 게시물에 대한 댓글 및 좋아요의 수를 집계해야 합니다. 이를 위해 실행할 3개의 SQL 쿼리가 추가로 필요합니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="게시물 검색 및 추가 데이터 집계" border="false":::

각 추가 쿼리는 정확히 성능과 확장성을 최대화하기 위해 해당 컨테이너의 파티션 키를 필터링합니다. 하지만 결국에는 4개의 작업을 수행하여 하나의 게시물을 반환해야 하므로 이 부분은 다음 반복에서 개선됩니다.

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 9ms | 19.54RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] 약식으로 사용자의 게시물 나열

먼저 특정 사용자에 해당하는 게시물을 가져오는 SQL 쿼리를 사용하여 원하는 게시물을 검색해야 합니다. 하지만 작성자의 사용자 이름과 댓글 및 좋아요의 수를 집계하는 추가 쿼리를 실행해야 합니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="사용자에 대한 모든 게시물 검색 및 추가 데이터 집계" border="false":::

이 구현에는 다음과 같이 많은 단점이 있습니다.

- 댓글 및 좋아요의 수를 집계하는 쿼리는 첫 번째 쿼리에서 반환된 각 게시물에 대해 실행되어야 합니다.
- 주 쿼리는 `posts` 컨테이너의 파티션 키를 필터링하지 않으므로 컨테이너 전체에서 팬아웃 및 파티션 검사가 수행됩니다.

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 130ms | 619.41RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] 댓글 만들기

댓글은 해당 항목을 `posts` 컨테이너에 기록함으로써 만들어집니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="posts 컨테이너에 단일 항목 쓰기" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 7ms | 8.57RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] 게시물의 댓글 나열

먼저 해당 게시물에 대한 모든 댓글을 가져오는 쿼리로 시작하고, 각 댓글에 대한 사용자 이름도 개별적으로 집계해야 합니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="게시물에 대한 모든 댓글 검색 및 추가 데이터 집계" border="false":::

주 쿼리는 컨테이너의 파티션 키를 필터링하지만, 사용자 이름을 개별적으로 집계하면 전체 성능이 저하됩니다. 이 부분은 나중에 개선됩니다.

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 23ms | 27.72RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] 게시물에 대한 좋아요 표시

**[C3]** 과 마찬가지로 해당 항목을 `posts` 컨테이너에 만듭니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="posts 컨테이너에 단일 항목 쓰기" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 6ms | 7.05RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] 게시물에 대한 좋아요 나열

**[Q4]** 와 마찬가지로 해당 게시물에 대한 좋아요를 쿼리한 다음, 해당 사용자 이름을 집계합니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="게시물에 대한 모든 좋아요 검색 및 추가 데이터 수집" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 59ms | 58.92RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] 최근에 만든 x개 게시물을 약식으로 나열(피드)

`posts` 컨테이너를 쿼리하여 만든 날짜를 내림차순으로 정렬한 최근 게시물을 가져온 다음, 각 게시물에 대한 사용자 이름과 댓글 및 좋아요의 수를 집계합니다.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="최근 게시물 검색 및 추가 데이터 집계" border="false":::

다시 한 번, 초기 쿼리는 비용이 많이 드는 팬아웃을 트리거하는 `posts` 컨테이너의 파티션 키를 필터링하지 않습니다. 이것은 훨씬 더 큰 결과 집합을 대상으로 하고 결과를 `ORDER BY` 절을 사용하여 정렬하므로 요청 단위 측면에서 비용이 더 많이 듭니다.

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 306ms | 2063.54RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>V1의 성능 반영

이전 섹션에서 직면한 성능 문제를 살펴보면 다음과 같은 두 가지 유형의 주요 문제를 식별할 수 있습니다.

- 일부 요청에서는 반환해야 하는 모든 데이터를 수집하기 위해 여러 개의 쿼리를 실행해야 합니다.
- 일부 쿼리는 대상 컨테이너의 파티션 키를 필터링하지 않으므로 확장성을 방해하는 팬아웃이 수행됩니다.

첫 번째 문제부터 시작하여 각 문제를 해결해 보겠습니다.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: 읽기 쿼리 최적화를 위한 비정규화 도입

초기 요청의 결과에 반환해야 하는 데이터가 모두 포함되어 있지 않으므로 경우에 따라 추가 요청을 발급해야 합니다. Azure Cosmos DB와 같은 비관계형 데이터 저장소를 사용하는 경우 이러한 유형의 문제는 일반적으로 데이터 세트에서 데이터를 정규화하여 해결됩니다.

다음 예제에서는 게시물 작성자의 사용자 이름, 댓글 수 및 좋아요 수를 추가하도록 게시물 항목을 수정합니다.

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

또한 댓글 및 좋아요 항목을 만든 사용자의 사용자 이름을 추가하도록 해당 항목을 수정합니다.

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>댓글 및 좋아요 수 비정규화

달성하고자 하는 것은 댓글이나 좋아요를 추가할 때마다 해당 게시물에서 `commentCount` 또는 `likeCount`도 증분시키는 것입니다. `posts` 컨테이너가 `postId`로 분할되므로 새 항목(댓글 또는 좋아요)과 해당 게시물은 동일한 논리 파티션에 배치됩니다. 따라서 [저장 프로시저](stored-procedures-triggers-udfs.md)를 사용하여 해당 작업을 수행할 수 있습니다.

이제 댓글(**[C3]**)을 만들 때 `posts` 컨테이너에 새 항목을 추가하는 대신 해당 컨테이너에 대해 다음과 같은 저장 프로시저를 호출합니다.

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

이 저장 프로시저는 게시물의 ID와 새 댓글의 본문을 매개 변수로 사용하여 다음을 수행합니다.

- 게시물 검색
- `commentCount` 증분
- 게시물 대체
- 새 댓글 추가

저장 프로시저가 원자성 트랜잭션으로 실행되기 때문에 `commentCount` 값과 실제 주석 수는 항상 동기화된 상태로 유지됩니다.

새 좋아요를 추가하여 `likeCount`를 증분시킬 때도 분명히 이와 비슷한 저장 프로시저를 호출합니다.

### <a name="denormalizing-usernames"></a>사용자 이름 비정규화

사용자는 다른 파티션뿐만 아니라 다른 컨테이너에도 배치되므로 사용자 이름에는 다른 접근 방식이 필요합니다. 파티션과 컨테이너 간에 데이터를 비정규화해야 하는 경우 원본 컨테이너의 [변경 피드](../change-feed.md)를 사용할 수 있습니다.

다음 예제에서는 사용자가 자신의 사용자 이름을 업데이트할 때마다 `users` 컨테이너의 변경 피드를 사용하여 반응합니다. 이 경우 `posts` 컨테이너에 대해 다른 저장 프로시저를 호출하여 변경 내용을 전파합니다.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="posts 컨테이너에 사용자 이름 비정규화" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

이 저장 프로시저는 사용자의 ID와 사용자의 새 사용자 이름을 매개 변수로 사용하여 다음을 수행합니다.

- `userId`와 일치하는 모든 항목(게시물, 댓글 또는 좋아요 항목일 수 있음) 가져오기
- 각 항목의 경우
  - `userUsername` 대체
  - 항목 대체

> [!IMPORTANT]
> 이 작업은 `posts` 컨테이너의 모든 파티션에서 이 저장 프로시저를 실행해야 하므로 비용이 많이 듭니다. 대부분의 사용자는 등록 중에 적절한 사용자 이름을 선택하고 변경하지 않으므로 이 업데이트는 거의 실행되지 않습니다.

## <a name="what-are-the-performance-gains-of-v2"></a>V2의 성능 향상 효과는?

### <a name="q2-retrieve-a-post"></a>[Q2] 게시물 검색

이제 비정규화가 완료되었으므로 해당 요청을 처리하기 위해 하나의 항목만 가져오면 됩니다.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="posts 컨테이너에서 단일 항목 검색" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 2ms | 1RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] 게시물의 댓글 나열

여기서 다시, 사용자 이름을 가져온 추가 요청을 줄이고 파티션 키를 필터링하는 단일 쿼리로 끝낼 수 있습니다.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="게시물에 대한 모든 댓글 검색" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 4ms | 7.72RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] 게시물에 대한 좋아요 나열

좋아요를 나열하는 경우와 똑같은 상황입니다.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="게시물에 대한 모든 좋아요 검색" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 4ms | 8.92RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: 모든 요청이 확장 가능한지 확인

전반적인 성능 향상을 살펴보면 아직 완전히 최적화되지 않은 **[Q3]** 와 **[Q6]** 의 두 가지 요청이 있습니다. 이는 대상 컨테이너의 파티션 키를 필터링하지 않는 쿼리와 관련된 요청입니다.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] 약식으로 사용자의 게시물 나열

이 요청은 이미 V2에서 도입된 향상된 기능의 이점으로 인해 추가 쿼리를 줄일 수 있습니다.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="짧은 형식으로 사용자의 게시물을 나열하는 쿼리를 보여 주는 다이어그램" border="false":::

그러나 나머지 쿼리에서는 아직 `posts` 컨테이너의 파티션 키를 필터링하지 않습니다.

이 상황에 대해 생각하는 방법은 다음과 같이 실제로 간단합니다.

1. 이 요청은 특정 사용자에 대한 모든 게시물을 가져오려고 하므로 `userId`를 *필터링해야 합니다*.
1. `userId`로 분할되지 않은 `posts` 컨테이너에 대해 실행되므로 효율적으로 수행되지 않습니다.
1. 분명히 말하면, `userId`로 *분할된* 컨테이너에 대해 이 요청을 실행하여 성능 문제를 해결할 수 있습니다.
1. 이미 `users` 컨테이너가 있습니다!

따라서 전체 게시물을 `users` 컨테이너에 복제하여 두 번째 수준의 비정규화를 도입합니다. 이렇게 하면 다른 차원을 따라서만 분할된 게시물 복사본을 효과적으로 얻을 수 있으므로 `userId`로 검색하는 것이 더 효율적입니다.

이제 `users` 컨테이너에는 다음 두 종류의 항목이 포함되어 있습니다.

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

다음 사항에 유의합니다.

- 사용자와 게시물을 구분하기 위해 `type` 필드를 사용자 항목에 도입했습니다.
- `userId` 필드도 사용자 항목에 추가되었습니다. 이 필드는 `id` 필드와 중복되지만, 이제 `users` 컨테이너가 `userId`(앞서와 같이 `id`가 아님)로 분할되므로 필요합니다.

이 비정규화를 달성하기 위해 변경 피드를 다시 한번 사용합니다. 이번에는 `posts` 컨테이너의 변경 피드에 반응하여 새 게시물이나 업데이트된 게시물을 `users` 컨테이너로 발송합니다. 그리고 게시물을 나열하면 전체 콘텐츠를 반환할 필요가 없으므로 이 프로세스에서 해당 콘텐츠를 자를 수 있습니다.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="users 컨테이너에 게시물 비정규화" border="false":::

이제 쿼리를 `users` 컨테이너로 라우팅하여 해당 컨테이너의 파티션 키를 필터링할 수 있습니다.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="사용자에 대한 모든 게시물 검색" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 4ms | 6.46RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] 최근에 만든 x개 게시물을 약식으로 나열(피드)

여기서 비슷한 상황을 처리해야 합니다. V2에 도입된 비정규화에서 남겨진 불필요한 추가 쿼리를 줄인 후에도 나머지 쿼리에서는 컨테이너의 파티션 키를 필터링하지 않습니다.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="짧은 형식으로 작성된 가장 최근 게시물 x개를 나열하는 쿼리를 보여 주는 다이어그램" border="false":::

동일한 접근 방식에 따라 이 요청의 성능과 확장성을 최대화하려면 하나의 파티션만 적중해야 합니다. 이는 제한된 수의 항목만 반환해야 하므로 고려할 수 있습니다. 블로깅 플랫폼의 홈페이지를 채우기 위해 전체 데이터 세트에 대해 페이지를 매길 필요 없이 100개의 최근 게시물을 가져오기만 하면 됩니다.

따라서 이 마지막 요청을 최적화하기 위해 전적으로 이 요청을 처리하는 데 전념하는 세 번째 컨테이너를 설계했습니다. 게시물을 새 `feed` 컨테이너에 비정규화합니다.

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

이 컨테이너는 항목에서 항상 `post`인 `type`으로 분할됩니다. 이렇게 하면 이 컨테이너의 모든 항목이 동일한 파티션에 배치됩니다.

비정규화를 달성하기 위해 앞에서 도입한 변경 공급 파이프라인에 후크하여 게시물을 새 컨테이너로 발송하기만 하면 됩니다. 명심해야 할 한 가지 중요한 점은 100개의 최근 게시물만 저장해야 한다는 것입니다. 그렇지 않으면 컨테이너의 콘텐츠가 파티션의 최대 크기를 초과할 수 있습니다. 이 작업은 컨테이너에 문서가 추가될 때마다 [사후 트리거](stored-procedures-triggers-udfs.md#triggers)를 호출하여 수행됩니다.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="feed 컨테이너에 게시물 비정규화" border="false":::

컬렉션을 자르는 사후 트리거의 본문은 다음과 같습니다.

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

마지막 단계는 쿼리를 새 `feed` 컨테이너로 다시 라우팅하는 것입니다.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="최근 게시물 검색" border="false":::

| **대기 시간** | **RU 요금** | **성능** |
| --- | --- | --- |
| 9ms | 16.97RU | ✅ |

## <a name="conclusion"></a>결론

서로 다른 버전의 설계에 도입한 전반적인 성능과 확장성의 향상된 기능에 대해 살펴보겠습니다.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7ms/5.71RU | 7ms/5.71RU | 7ms/5.71RU |
| **[Q1]** | 2ms/1RU | 2ms/1RU | 2ms/1RU |
| **[C2]** | 9ms/8.76RU | 9ms/8.76RU | 9ms/8.76RU |
| **[Q2]** | 9ms/19.54RU | 2ms/1RU | 2ms/1RU |
| **[Q3]** | 130ms/619.41RU | 28ms/201.54RU | 4ms/6.46RU |
| **[C3]** | 7ms/8.57RU | 7ms/15.27RU | 7ms/15.27RU |
| **[Q4]** | 23ms/27.72RU | 4ms/7.72RU | 4ms/7.72RU |
| **[C4]** | 6ms/7.05RU | 7ms/14.67RU | 7ms/14.67RU |
| **[Q5]** | 59ms/58.92RU | 4ms/8.92RU | 4ms/8.92RU |
| **[Q6]** | 306ms/2063.54RU | 83ms/532.33RU | 9ms/16.97RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>읽기 집약적 시나리오를 최적화함

쓰기 요청(명령)을 희생하여 읽기 요청(쿼리)의 성능을 향상시키려고 노력했음을 알 수 있을 것입니다. 대부분의 경우 쓰기 작업은 변경 피드를 통해 후속 비정규화를 트리거하므로 이를 구현하는 데 있어 컴퓨팅 비용이 더 많이 들고 시간이 더 오래 걸립니다.

이는 대부분의 소셜 앱과 마찬가지로 블로깅 플랫폼의 작업이 읽기 집약적이라는 사실로 정당화됩니다. 즉 제공해야 하는 읽기 요청의 양은 일반적으로 쓰기 요청의 양보다 훨씬 더 많습니다. 따라서 읽기 요청이 더 저렴하고 더 효율적으로 수행될 수 있도록 쓰기 요청을 실행하는 데 더 많은 비용을 들이는 것이 좋습니다.

지금까지 수행한 가장 극단적인 최적화를 살펴보면 **[Q6]** 이 2,000 이상의 RU에서 단 17RU로 감소되었습니다. 이러한 목표는 항목당 약 10RU의 비용으로 게시물을 비정규화하여 달성했습니다. 게시물을 만들거나 업데이트하는 것보다 훨씬 더 많은 피드 요청을 처리하므로 전체 비용의 절감을 고려할 때 이 비정규화 비용은 무시할 수 있습니다.

### <a name="denormalization-can-be-applied-incrementally"></a>비정규화를 증분 방식으로 적용할 수 있음

이 문서에서 살펴본 향상된 확장성 기능은 데이터 세트 전체의 데이터 비정규화 및 복제와 관련이 있습니다. 이러한 최적화는 1일차에 시행할 필요가 없습니다. 파티션 키를 필터링하는 쿼리는 규모에 맞게 더 효율적으로 수행되지만, 파티션 간 쿼리는 거의 호출되지 않거나 제한된 데이터 세트에 대해 호출되는 경우에만 완전히 허용될 수 있습니다. 프로토타입을 작성하거나 제어되는 소규모 사용자 기반의 프로덕션 환경을 시작하는 경우 나중을 위해 이러한 향상된 기능을 보류할 수 있습니다. 중요한 것은 모델의 성능을 [모니터링](../use-metrics.md)하여 도입해야 하는 경우와 시기를 결정할 수 있다는 것입니다.

업데이트를 다른 컨테이너에 배포하는 데 사용하는 변경 피드는 모든 업데이트를 영구적으로 저장합니다. 이렇게 하면 시스템에 이미 많은 양의 데이터가 있는 경우에도 컨테이너 및 부트스트랩에 대한 비정규화된 보기를 만든 이후의 모든 업데이트를 일회성 가져오기(catch-up) 작업으로 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

실제적인 데이터 모델링 및 분할에 대한 소개를 살펴보았으면 다음 문서를 확인하여 지금까지 다룬 개념을 검토할 수 있습니다.

- [데이터베이스, 컨테이너 및 항목 작업](../account-databases-containers-items.md)
- [Azure Cosmos DB에서 분할](../partitioning-overview.md)
- [Azure Cosmos DB의 변경 피드](../change-feed.md)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
