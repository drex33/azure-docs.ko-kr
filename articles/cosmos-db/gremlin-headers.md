---
title: Azure Cosmos DB Gremlin 응답 헤더
description: 추가 문제 해결을 가능하게 하는 서버 응답 메타데이터에 대한 참조 설명서
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 0f0a1dd1dec64aae502de9e42d36e838d6fa996d
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113362362"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin 서버 응답 헤더
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

이 문서에서는 Cosmos DB Gremlin 서버가 요청 실행 시 호출자에게 반환하는 헤더에 대해 설명합니다. 이러한 헤더는 Cosmos DB 서비스에 기본적으로 통합되고 고객 지원을 간소화하는 애플리케이션을 구축하는 요청 성능 문제 해결에 유용합니다.

이러한 헤더에 대한 종속성을 유지하는 것은 애플리케이션의 이식성을 다른 Gremlin 구현으로 제한한다는 점에 유의해야 합니다. 반환 시 Cosmos DB Gremlin과 긴밀하게 통합됩니다. 이러한 헤더는 TinkerPop 표준이 아닙니다.

## <a name="headers"></a>헤더

| 헤더 | 유형 | 샘플 값 | 포함되는 경우 | 설명 |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Success and Failure | 부분 응답 메시지에 대한 [요청 단위(RU/s 또는 RU)](request-units.md)에서 사용되는 컬렉션 또는 데이터베이스 처리량입니다. 이 헤더는 여러 청크를 포함하는 요청에 대한 모든 연속에 존재합니다. 특정 응답 청크의 요금을 반영합니다. 단일 응답 청크로 구성된 요청에 대해서만 이 헤더는 총 순회 비용과 일치합니다. 하지만 대부분의 복합 순회에서 이 값은 부분 비용을 나타냅니다. |
| **x-ms-total-request-charge** | double | 423.987 | Success and Failure | 전체 요청에 대한 [요청 단위(RU/s 또는 RU)](request-units.md)에서 사용되는 컬렉션 또는 데이터베이스 처리량입니다. 이 헤더는 여러 청크를 포함하는 요청에 대한 모든 연속에 존재합니다. 요청 시작 이후 누적 요금을 나타냅니다. 마지막 청크의 이 헤더의 값은 전체 요청 요금을 나타냅니다. |
| **x-ms-server-time-ms** | double | 13.75 | Success and Failure | 이 헤더는 대기 시간 문제 해결을 위해 포함됩니다. Cosmos DB Gremlin 서버가 부분 응답 메시지를 실행하 고 생성하는 데 걸린 시간(밀리초)을 나타냅니다. 이 헤더의 값을 사용하여 전체 요청 대기 시간 애플리케이션과 비교하면 네트워크 대기 시간 오버헤드를 계산할 수 있습니다. |
| **x-ms-total-server-time-ms** | double | 130.512 | Success and Failure | Cosmos DB Gremlin 서버가 전체 순회를 실행하는 데 걸린 총 시간(밀리초)입니다. 이 헤더는 모든 부분 응답에 포함됩니다. 요청이 시작된 이후의 누적 실행 시간을 나타냅니다. 마지막 응답은 총 실행 시간을 나타냅니다. 이 헤더는 클라이언트와 서버를 대기 시간 원본으로 구분하는 데 유용합니다. 클라이언트의 순회 실행 시간을 이 헤더의 값과 비교할 수 있습니다. |
| **x-ms-status-code** | long | 200 | Success and Failure | 헤더는 요청 완료 또는 종결의 내부 원인을 나타냅니다. 애플리케이션은 이 헤더의 값을 확인하고 정정 작업을 수행하는 것이 좋습니다. |
| **x-ms-substatus-code** | long | 1003 | 실패만 | Cosmos DB는 통합 스토리지 레이어를 기반으로 하는 다중 모델 데이터베이스입니다. 이 헤더는 고가용성 스택의 하위 레이어에서 실패가 발생할 때 실패 원인에 대한 추가적인 인사이트를 포함합니다. 애플리케이션은 이 헤더를 저장하고 Cosmos DB 고객 지원에 연락할 때 사용하는 것이 좋습니다. 이 헤더의 값은 빠른 문제 해결을 위해 Cosmos DB 엔지니어에게 유용합니다. |
| **x-ms-retry-after-ms** | 문자열(TimeSpan) | "00:00:03.9500000" | 실패만 | 이 헤더는 .NET [TimeSpan](/dotnet/api/system.timespan) 형식의 문자열 표현입니다. 이 값은 프로비전된 처리량 소모로 인해 실패한 요청에만 포함됩니다. 애플리케이션은 지시 시간 후에 순회를 다시 전송해야 합니다. |
| **x-ms-activity-id** | 문자열(Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success and Failure | 헤더에는 요청의 고유한 서버 쪽 식별자가 포함되어 있습니다. 각 요청에는 추적을 위해 서버에서 고유 식별자가 할당됩니다. 애플리케이션은 고객이 고객 지원 담당자에게 문의할 수 있는 요청에 대해 서버에서 반환하는 작업 식별자를 로그해야 합니다. Cosmos DB 지원 담당자는 Cosmos DB 서비스 원격 분석에서 이러한 식별자로 특정 요청을 찾을 수 있습니다. |

## <a name="status-codes"></a>상태 코드

서버에서 `x-ms-status-code` 상태 특성에 대해 반환하는 가장 일반적인 코드는 다음과 같습니다.

| 상태 | 설명 |
| --- | --- |
| **401** | 오류 메시지 `"Unauthorized: Invalid credentials provided"`은 인증 암호가 Cosmos DB 계정 키와 일치하지 않을 때 반환됩니다. Azure Portal에서 Cosmos DB Gremlin 계정으로 이동하여 키가 올바른지 확인합니다.|
| **404** | 동일한 에지 또는 꼭짓점을 동시에 삭제하고 업데이트하려고 시도하는 동시 실행 작업입니다. `"Owner resource does not exist"` 오류 메시지는 지정된 데이터베이스 또는 컬렉션이 `/dbs/<database name>/colls/<collection or graph name>` 형식의 연결 매개 변수에서 올바르지 않다는 것을 의미합니다.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` 이 문제는 식별자가 있는 꼭짓점 또는 에지가 이미 그래프에 있는 경우에 발생합니다.| 
| **412** | 상태 코드는 오류 메시지 `"PreconditionFailedException": One of the specified pre-condition is not met`로 보완됩니다. 이 오류는 에지 또는 꼭짓점 읽기와 수정 후 저장소에 다시 쓰기 간의 낙관적 동시성 제어 위반을 나타냅니다. 이러한 오류가 발생하는 가장 일반적인 상황은 속성 수정입니다(예: `g.V('identifier').property('name','value')`). Gremlin 엔진은 꼭짓점을 읽고, 수정하고, 다시 씁니다. 동시에 동일한 꼭짓점 또는 에지를 쓰려고 하는 다른 순회를 실행하는 경우 그 중 하나에 이 오류가 표시됩니다. 애플리케이션은 서버에 다시 순회를 제출해야 합니다.| 
| **429** | 요청이 제한되었으며 **x-ms-retry-after-ms** 값 이후에 다시 시도해야 합니다.| 
| **500** | `"NotFoundException: Entity with the specified id does not exist in the system."`이 포함된 오류 메시지는 데이터베이스 및/또는 컬렉션이 같은 이름으로 다시 생성되었음을 나타냅니다. 변경 내용이 전파되면 5분 내에 이 오류가 사라지고 다른 Cosmos DB 구성 요소에서 캐시가 무효화됩니다. 이 문제를 방지하려면 매번 고유한 데이터베이스 및 컬렉션 이름을 사용합니다.| 
| **1000** | 이 상태 코드는 서버가 메시지를 성공적으로 구문 분석했지만 실행하지 못했을 때 반환됩니다. 일반적으로 쿼리에 문제가 있음을 나타냅니다.| 
| **1001** | 이 코드는 서버에서 순회 실행을 완료했지만 클라이언트에 응답을 다시 직렬화하지 못할 때 반환됩니다. 순회가 너무 크거나 TinkerPop 프로토콜 사양을 따르지 않는 복잡한 결과를 생성하는 경우 이 오류가 발생할 수 있습니다. 애플리케이션은 이 오류가 발생할 때 순회를 단순화해야 합니다. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`는 순회가 허용된 메모리 제한을 초과하면 반환됩니다. 메모리 제한은 순회당 **2GB** 입니다.| 
| **1004** | 이 상태 코드는 잘못된 그래프 요청을 나타냅니다. 요청이 역직렬화에 실패하거나, 값이 아닌 형식이 값 형식으로 역직렬화되거나, 지원되지 않는 gremlin 작업이 요청되는 경우 요청의 형식이 잘못될 수 있습니다. 애플리케이션은 성공하지 않으므로 요청을 다시 시도해서는 안 됩니다. | 
| **1007** | 일반적으로 이 상태 코드는 오류 메시지 `"Could not process request. Underlying connection has been closed."`와 함께 반환됩니다. 이 상황은 클라이언트 드라이버가 서버에서 닫히는 연결을 사용하려고 시도하는 경우에 발생할 수 있습니다. 애플리케이션은 다른 연결에서 순회를 다시 시도해야 합니다.
| **1008** | Cosmos DB Gremlin 서버는 클러스터에서 트래픽 균형을 다시 조정하는 연결을 종료할 수 있습니다. 클라이언트 드라이버는 이러한 상황을 처리하고 라이브 연결만을 사용하여 서버에 요청을 보냅니다. 클라이언트 드라이버에서 연결이 닫힌 것을 감지하지 못하는 경우도 있습니다. 애플리케이션에서 `"Connection is too busy. Please retry after sometime or open more connections."` 오류가 발생하면 다른 연결에서 순회를 다시 시도해야 합니다.
| **1009** | 작업이 할당된 시간 내에 완료되지 않았고 서버에 의해 취소되었습니다. 순회의 모든 홉에 대한 꼭짓점 또는 에지를 필터링하여 검색 범위를 좁힐 수 있도록 순회를 빠르게 실행할 수 있도록 최적화합니다. 기본 요청 시간 제한은 **60초** 입니다. |

## <a name="samples"></a>샘플

하나의 상태 특성을 읽는 Gremlin.Net 기반 샘플 클라이언트 애플리케이션:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Gremlin java 클라이언트에서 상태 특성을 읽는 방법을 보여주는 예제:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>다음 단계
* [Azure Cosmos DB에 대한 HTTP 상태 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [일반 Azure Cosmos DB REST 응답 헤더](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop Graph 드라이버 공급자 요구 사항]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
