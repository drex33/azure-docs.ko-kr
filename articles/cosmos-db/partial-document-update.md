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
ms.openlocfilehash: 85bb6f2a509b2e58f364f2393a56722e11231b89
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132939142"
---
# <a name="partial-document-update-in-azure-cosmos-db"></a>Azure Cosmos DB의 부분 문서 업데이트
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 부분 문서 업데이트 기능 (Patch API 라고도 함)은 컨테이너에서 문서를 수정 하는 편리한 방법을 제공 합니다. 현재, 클라이언트에서 문서를 읽고, 해당 문서를 읽고, 필요한 경우 낙관적 동시성 제어 검사를 실행 하 고, 문서를 로컬로 업데이트 한 후 전체 문서로 전송 하 여 API 호출을 대체 하는 문서를 업데이트 해야 합니다. 

부분 문서 업데이트 기능은 이러한 환경을 크게 향상 시킵니다. 클라이언트는 전체 문서 바꾸기 작업을 수행 하지 않고도 문서에서 수정 된 속성/필드를 보낼 수 있습니다. 이 기능의 주요 이점은 다음과 같습니다.

- **향상 된 개발자 생산성**: 간편 하 게 사용할 수 있는 편리한 API와 문서를 조건부로 업데이트 하는 기능을 제공 합니다. 
- **성능 향상**: 클라이언트 쪽에서 추가 CPU 순환을 방지 하 고 종단 간 대기 시간 및 네트워크 대역폭을 줄입니다.
- **다중 지역 쓰기**: 동일한 문서 내에서 불연속 경로에 대 한 부분 업데이트를 사용 하 여 자동 및 투명 충돌 해결을 지원 합니다.
 
## <a name="supported-operations"></a>지원되는 작업

아래 표에는이 기능에서 지원 되는 작업이 요약 되어 있습니다.

> [!NOTE]
> *대상 경로* 는 JSON 문서 내의 위치를 참조 합니다.

| **작업 유형** | **설명** |
| ------------ | -------- |
| **추가**      | `Add` 대상 경로에 따라 다음 중 하나를 수행 합니다. <br/><ul><li>대상 경로가 존재 하지 않는 요소를 지정 하는 경우 해당 요소는 추가 됩니다.</li><li>대상 경로가 이미 있는 요소를 지정 하는 경우 해당 값이 대체 됩니다.</li><li>대상 경로가 유효한 배열 인덱스 이면 새 요소가 배열에서 지정 된 인덱스에 삽입 됩니다. 기존 요소를 오른쪽으로 이동 합니다.</li><li>지정 된 인덱스가 배열의 길이와 같으면 배열에 요소를 추가 합니다. 인덱스를 지정 하는 대신 문자를 사용할 수도 있습니다 `-` . 또한 요소가 배열에 추가 됩니다.</li></ul> <br/> **참고**: 배열 길이 보다 큰 인덱스를 지정 하면 오류가 발생 합니다.|
| **설정**      | `Set` 작업은 배열 데이터 형식의 경우를 제외 하 고는와 유사 합니다 `Add` . 대상 경로가 유효한 배열 인덱스 이면 해당 인덱스에 있는 기존 요소가 업데이트 됩니다.| 
| **바꾸기**      | `Replace` 작업은 `Set` _엄격한_ 바꾸기 의미 체계를 따르는 경우를 제외 하 고와 비슷합니다. 대상 경로가 존재 하지 않는 요소나 배열을 지정 하는 경우 오류가 발생 합니다.  | 
| **제거**     | `Remove` 대상 경로에 따라 다음 중 하나를 수행 합니다. <br/><ul><li>대상 경로가 존재 하지 않는 요소를 지정 하는 경우 오류가 발생 합니다. </li><li> 이미 존재 하는 요소를 지정 하는 경우 대상 경로는 제거 됩니다. </li><li> 대상 경로가 배열 인덱스 이면 삭제 되 고 지정 된 인덱스 위에 있는 모든 요소가 왼쪽으로 이동 됩니다.</li></ul> <br/> **참고**: 배열 길이 보다 크거나 같은 인덱스를 지정 하면 오류가 발생 합니다.  |
| **ID 증가값**     | 이 연산자는 지정 된 값으로 필드를 증가 시킵니다. 양수 및 음수 값을 모두 사용할 수 있습니다. 필드가 없으면 필드를 만들고 지정 된 값으로 설정 합니다. |

## <a name="supported-modes"></a>지원되는 모드

부분 문서 업데이트 기능은 다음과 같은 작업 모드를 지원 합니다. 코드 예제는 [시작](partial-document-update-getting-started.md) 문서를 참조 하세요.

* **단일 문서 패치**: ID 및 파티션 키를 기준으로 단일 문서를 패치할 수 있습니다. 단일 문서에서 여러 패치 작업을 실행할 수 있습니다. [최대 한도는 10 개의 작업](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-)입니다.

* **다중 문서 패치**: 동일한 파티션 키 내의 여러 문서를 [트랜잭션의 일부로](transactional-batch.md)패치할 수 있습니다. 이 트랜잭션은 설명 된 순서 대로 모든 작업이 성공 하는 경우에만 커밋됩니다. 작업이 실패 하면 전체 트랜잭션이 롤백됩니다.

* **조건부 업데이트** 앞서 언급 한 모드의 경우 조건자에 지정 된 사전 조건이 충족 되지 않을 경우 작업이 실패 하도록 하는 것과 같은 SQL 필터 조건자 (예: *c where c. tasknum = 3*)를 추가할 수도 있습니다. 

* 지원 되는 Sdk의 대량 Api를 사용 하 여 여러 문서에서 하나 이상의 패치 작업을 실행할 수도 있습니다.


## <a name="similarities-and-differences"></a>유사성 및 차이점

### <a name="add-vs-set"></a>Vs Set 추가

`Set` 작업은를 `Add` 제외한 모든 데이터 형식의 경우와 유사 `Array` 합니다. `Add`모든 (유효한) 인덱스에서의 작업으로 인해 지정 된 인덱스에 요소가 추가 되 고 배열 끝의 모든 기존 요소가 오른쪽으로 이동 합니다. 이는 `Set` 지정 된 인덱스에 있는 기존 요소를 업데이트 하는 작업과 대조 됩니다. 

### <a name="add-vs-replace"></a>Vs 바꾸기 추가

`Add` 작업은 속성 (데이터 형식 포함)이 아직 없는 경우 추가 `Array` 합니다. `Replace` 속성이 없으면 작업이 실패 `Array` 합니다 (데이터 형식에도 적용 됨).

### <a name="set-vs-replace"></a>Vs 바꾸기 설정

`Set` 작업은 아직 없는 경우 속성을 추가 합니다 (가 있는 경우 제외 `Array` ). `Replace` 속성이 없으면 작업이 실패 `Array` 합니다 (데이터 형식에도 적용 됨).

> [!NOTE]
> `Replace` 는 사용자가 일부 속성을 항상 표시 하 고이를 어설션/적용할 수 있는 좋은 후보입니다.

## <a name="rest-api-reference-for-partial-document-update"></a>부분 문서 업데이트에 대 한 REST API 참조

[Azure Cosmos DB REST API](/rest/api/cosmos-db/) 는 데이터베이스, 문서 컬렉션 및 문서를 생성, 쿼리 및 삭제 하는 Azure Cosmos DB 리소스에 대 한 프로그래밍 방식의 액세스를 제공 합니다. 컬렉션의 JSON 문서에서 삽입, 바꾸기, 삭제, 읽기, 열거 및 쿼리 작업을 실행 하는 것 외에도 `PATCH` 부분 문서 업데이트 작업에는 HTTP 메서드를 사용할 수 있습니다. 자세한 내용은 [Azure Cosmos DB REST API 참조](/rest/api/cosmos-db/patch-a-document) 를 참조 하세요.

예를 들어 `set` 부분 문서 업데이트를 사용 하 여 작업에 대 한 요청은 다음과 같습니다.

```json
PATCH https://querydemo.documents.azure.com/dbs/FamilyDatabase/colls/FamilyContainer/docs/Andersen.1 HTTP/1.1  
x-ms-documentdb-partitionkey: ["Andersen"]  
x-ms-date: Tue, 29 Mar 2016 02:28:29 GMT  
Authorization: type%3dmaster%26ver%3d1.0%26sig%3d92WMAkQv0Zu35zpKZD%2bcGSH%2b2SXd8HGxHIvJgxhO6%2fs%3d
Content-Type:application/json_patch+json
Cache-Control: no-cache  
User-Agent: Microsoft.Azure.DocumentDB/2.16.12  
x-ms-version: 2015-12-16  
Accept: application/json  
Host: querydemo.documents.azure.com  
Cookie: x-ms-session-token#0=602; x-ms-session-token=602  
Content-Length: calculated when request is sent  
Connection: keep-alive
  
{"operations":[{ "op" :"set", "path":"/Parents/0/FamilyName","value":"Bob" }]}
```

## <a name="document-level-vs-path-level-conflict-resolution"></a>문서 수준 vs 경로 수준 충돌 해결  

Azure Cosmos DB 계정이 여러 쓰기 영역으로 구성 된 경우 [충돌 및 충돌 해결 정책은](conflict-resolution-policies.md) 문서 수준에서 적용 가능 하며 마지막 쓰기 Wins ( `LWW` )가 기본 충돌 해결 정책입니다. 부분 문서 업데이트의 경우 여러 지역에서의 패치 작업은 더 세부적인 경로 수준에서 충돌을 감지 하 고 해결 합니다.

이는 예제를 사용 하 여 더 잘 이해할 수 있습니다.

Azure Cosmos DB에 다음 문서가 있다고 가정 합니다.

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345",
      "67890"
   ], 
   "level":"gold"
} 
```

아래 패치 작업은 서로 다른 지역의 여러 클라이언트에서 동시에 실행 됩니다.

- `Set` 특성 `/level` 을 platinum으로  
- `Remove` 67890 시작 `/phone`

:::image type="content" source="./media/partial-document-update/patch-multi-region-conflict-resolution.png" alt-text="동시 다중 지역 부분 업데이트 작업에서 충돌 해결을 보여 주는 이미지입니다." border="false" lightbox="./media/partial-document-update/patch-multi-region-conflict-resolution.png":::

문서 내에서 충돌 하지 않는 경로에 대 한 패치 요청이 수행 되었으므로 이러한 충돌은 문서 수준에서 마지막 기록기와는 달리 자동 및 투명 하 게 해결 됩니다.    

충돌 해결 후 클라이언트에 다음 문서가 표시 됩니다.

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
> 문서의 동일한 속성이 여러 지역에서 동시에 패치 되는 경우 일반 [충돌 해결 정책이](conflict-resolution-policies.md) 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- , .NET, Java 및 Node를 사용 하 여 부분 문서 업데이트를 [시작](partial-document-update-getting-started.md) 하는 방법을 알아봅니다.
- 부분 문서 업데이트에 대 한 [질문과 대답](partial-document-update-faq.yml)
