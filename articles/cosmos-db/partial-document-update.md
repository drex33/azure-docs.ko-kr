---
title: Azure Cosmos DB의 부분 문서 업데이트
description: Azure Cosmos DB의 부분 문서 업데이트에 대해 알아봅니다.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2fd4b082d5c09cb9329451e9f4f165a1699c85a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103222"
---
# <a name="partial-document-update-in-azure-cosmos-db"></a>Azure Cosmos DB의 부분 문서 업데이트
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 부분 문서 업데이트 기능(패치 API라고도 함)은 컨테이너에서 문서를 수정하는 편리한 방법을 제공합니다. 현재 클라이언트에서 읽어야 하는 문서를 업데이트하려면 낙관적 동시성 제어 검사를 실행하고(필요한 경우) 문서를 로컬로 업데이트한 다음 전체 문서 Replace API 호출로 유선으로 보냅니다. 

부분 문서 업데이트 기능은 이 환경을 크게 향상시킵니다. 클라이언트는 전체 문서 바꾸기 작업을 수행하지 않고 문서의 수정된 속성/필드만 보낼 수 있습니다. 이 기능의 주요 이점은 다음과 같습니다.

- **개발자 생산성 향상:** 사용 편의성과 조건부로 문서를 업데이트하는 기능을 위한 편리한 API를 제공합니다. 
- **성능 향상:** 클라이언트 쪽에서 추가 CPU 주기를 방지하고 엔드투엔드 대기 시간 및 네트워크 대역폭을 줄입니다.
- **다중 지역 쓰기:** 동일한 문서 내의 개별 경로에 부분 업데이트를 통해 자동 및 투명한 충돌 해결을 지원합니다.
 
## <a name="supported-operations"></a>지원되는 작업

아래 표에는 이 기능에서 지원하는 작업이 요약됩니다.

> [!NOTE]
> *대상 경로는* JSON 문서 내의 위치를 나타냅니다.

| **작업 유형** | **설명** |
| ------------ | -------- |
| **추가**      | `Add` 는 대상 경로에 따라 다음 중 하나를 수행합니다. <br/><ul><li>대상 경로가 존재하지 않는 요소를 지정하면 추가됩니다.</li><li>대상 경로가 이미 존재하는 요소를 지정하면 해당 값이 대체됩니다.</li><li>대상 경로가 유효한 배열 인덱스인 경우 새 요소가 지정된 인덱스에서 배열에 삽입됩니다. 기존 요소를 오른쪽으로 이동합니다.</li><li>지정된 인덱스의 길이가 배열의 길이와 같으면 배열에 요소가 추가됩니다. 인덱스 대신 문자를 사용할 수도 `-` 있습니다. 또한 요소가 배열에 추가됩니다.</li></ul> <br/> **참고:** 배열 길이보다 큰 인덱스 를 지정하면 오류가 발생합니다.|
| **설정**      | `Set` 작업은 Array `Add` 데이터 형식의 경우를 제외하고와 비슷합니다. 대상 경로가 유효한 배열 인덱스이면 해당 인덱스의 기존 요소가 업데이트됩니다.| 
| **바꾸기**      | `Replace` 연산은 `Set` _엄격한_ 의미 체계만 바꾸기를 따른다는 점을 제외하면 유사합니다. 대상 경로가 존재하지 않는 요소 또는 배열을 지정하는 경우 오류가 발생합니다.  | 
| **제거**     | `Remove` 는 대상 경로에 따라 다음 중 하나를 수행합니다. <br/><ul><li>대상 경로가 존재하지 않는 요소를 지정하면 오류가 발생합니다. </li><li> 대상 경로가 이미 존재하는 요소를 지정하면 제거됩니다. </li><li> 대상 경로가 배열 인덱스인 경우 삭제되고 지정된 인덱스 위의 모든 요소가 한 위치 왼쪽으로 이동됩니다.</li></ul> <br/> **참고:** 배열 길이보다 크거나 같은 인덱스 를 지정하면 오류가 발생합니다.  |
| **ID 증가값**     | 이 연산자는 필드를 지정된 값으로 증가합니다. 양수 값과 음수 값을 모두 사용할 수 있습니다. 필드가 없으면 필드를 만들고 지정된 값으로 설정합니다. |

## <a name="supported-modes"></a>지원되는 모드

부분 문서 업데이트 기능은 다음과 같은 작업 모드를 지원합니다. 코드 예제는 [시작](partial-document-update-getting-started.md) 문서를 참조하세요.

* **단일 문서 패치:** ID 및 파티션 키에 따라 단일 문서를 패치할 수 있습니다. 단일 문서에서 여러 패치 작업을 실행할 수 있습니다. [최대 제한은 10개의 작업 입니다.](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-)

* **다중 문서 패치:** 동일한 파티션 키 내의 여러 문서를 [트랜잭션 의 일부로](transactional-batch.md)패치할 수 있습니다. 이 트랜잭션은 모든 작업이 설명된 순서대로 성공한 경우에만 커밋됩니다. 작업이 실패하면 전체 트랜잭션이 롤백됩니다.

* **조건부 업데이트** 앞서 언급한 모드의 경우 조건자에서 지정된 조건자가 충족되지 않으면 작업이 실패하도록 SQL 같은 필터 조건자(예: *c에서 c.taskNum = 3*)를 추가할 수도 있습니다. 

* 지원되는 SDK의 대량 API를 사용하여 여러 문서에서 하나 이상의 패치 작업을 실행할 수도 있습니다.


## <a name="similarities-and-differences"></a>유사성 및 차이점

### <a name="add-vs-set"></a>추가 및 설정

`Set` 작업은 를 제외한 모든 데이터 형식의 경우와 `Add` `Array` 유사합니다. `Add`임의의 (유효한) 인덱스에서 작업을 수행하면 지정된 인덱스에서 요소가 추가되고 배열의 기존 요소가 오른쪽으로 이동됩니다. `Set`이는 지정된 인덱스에서 기존 요소를 업데이트하는 작업과는 대조적입니다. 

### <a name="add-vs-replace"></a>추가 및 바꾸기

`Add` 작업이 없는 경우(데이터 형식 포함) 속성을 `Array` 추가합니다. `Replace` 속성이 없으면 작업이 실패합니다(데이터 `Array` 형식에도 적용).

### <a name="set-vs-replace"></a>설정 및 바꾸기

`Set` 작업이 아직 없는 경우 속성을 추가합니다(가 있는 경우 `Array` 제외). `Replace` 속성이 없으면 작업이 실패합니다(데이터 `Array` 형식에도 적용).

> [!NOTE]
> `Replace` 는 사용자가 일부 속성이 항상 존재할 것으로 예상하며 이를 어설션/적용할 수 있는 좋은 후보입니다.

## <a name="partial-document-update-specification"></a>부분 문서 업데이트 사양

부분 문서 업데이트 기능의 클라이언트 관련 구성 요소는 최상위 REST API 구현됩니다. Azure Cosmos DB에서 작업을 모델링하는 방법의 예는 다음과 같습니다. `Conditional Add`

```bash
PATCH /dbs/{db}/colls/{coll}/documents/{doc}
HTTP/1.1
Content-Type:application/json-patch+json

{
   "condition":"from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))", 
   "operations":[ 
      { 
         "op":"add", 
         "path":"amount", 
         "value":80000 
      }
   ]
} 
```

> [!NOTE]
> 이 경우 의 값이 `amount` `80000` 0이거나 존재하지 않는 *경우에만* path의 `TotalDue` 값이 (작업)로 설정됩니다(조건).

## <a name="document-level-vs-path-level-conflict-resolution"></a>문서 수준과 경로 수준 충돌 해결  

Azure Cosmos DB 계정이 여러 쓰기 지역으로 구성된 경우 [충돌 및 충돌 해결 정책이](conflict-resolution-policies.md) 문서 수준에서 적용되며, 마지막 쓰기 우선()은 기본 충돌 해결 `LWW` 정책입니다. 부분 문서 업데이트의 경우 여러 지역의 패치 작업은 보다 세부적인 경로 수준에서 충돌을 감지하고 해결합니다.

이 내용은 예제를 통해 더 잘 이해할 수 있습니다.

Azure Cosmos DB에 다음 문서가 있다고 가정합니다.

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345",
      "67890"
   ], 
   "level":"gold",
} 
```

아래 패치 작업은 서로 다른 지역의 다른 클라이언트에 의해 동시에 발급됩니다.

- `Set` attribute `/level` toum  
- `Remove` 67890 `/phone`

:::image type="content" source="./media/partial-document-update/patch-multi-region-conflict-resolution.png" alt-text="동시 다중 지역 부분 업데이트 작업의 충돌 해결을 보여 주는 이미지입니다." border="false" lightbox="./media/partial-document-update/patch-multi-region-conflict-resolution.png":::

패치 요청은 문서 내의 충돌하지 않는 경로에 대해 만들어졌기 때문에 이러한 충돌은 문서 수준에서 마지막 작성자 승이 아니라 자동으로 투명하게 해결됩니다.    

충돌 해결 후 클라이언트에 다음 문서가 표시됩니다.

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345"
   ], 
   "level":"platinum",
} 
```

> [!NOTE]
> 문서의 동일한 속성이 여러 지역에 동시에 패치되는 경우 정기적인 충돌 해결 [정책이](conflict-resolution-policies.md) 적용됩니다.

## <a name="next-steps"></a>다음 단계

- .NET, Java 및 [Node를](partial-document-update-getting-started.md) 사용하여 부분 문서 업데이트를 시작하는 방법을 알아봅니다.
- 부분 문서 업데이트에 대한 [질문과 대답](partial-document-update-faq.yml)
