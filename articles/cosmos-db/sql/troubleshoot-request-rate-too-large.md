---
title: Azure Cosmos DB 요청 빈도 너무 높음 예외 문제 해결
description: 요청 빈도 너무 높음 예외를 진단하고 수정하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/25/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 44ecb59508b93347ba57fb40a88c274adfedd320
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434055"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-429-exceptions"></a>Azure Cosmos DB 요청 빈도 너무 높음(429) 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에는 SQL API에 대한 다양한 429 상태 코드 오류의 알려진 원인 및 해결 방법이 포함됩니다. MongoDB용 API를 사용하는 경우 상태 코드 16500을 디버그하는 방법은 [MongoDB API의 일반적인 문제 해결](../mongodb/error-codes-solutions.md) 문서를 참조하세요.

오류 코드 429라고도 하는 “요청 빈도 너무 높음” 예외는 Azure Cosmos DB에 대한 요청의 빈도가 제한되고 있음을 나타냅니다.

프로비저닝된 처리량을 사용하는 경우 워크로드에 필요한 초당 요청 단위(RU/초)로 측정된 처리량을 설정합니다. 읽기, 쓰기, 쿼리와 같은 서비스에 대한 데이터베이스 작업은 일정 크기의 RU(요청 단위)를 사용합니다. [요청 단위](../request-units.md)에 대해 자세히 알아보세요.

특정 1초 내에 작업이 프로비저닝된 요청 단위보다 더 많이 사용하는 경우 Azure Cosmos DB는 429 예외를 반환합니다. 1초마다 사용할 수 있는 요청 단위 수가 다시 설정됩니다.

RU/초를 변경하는 작업을 수행하기 전에 빈도 제한의 근본 원인을 파악하고 기본 문제를 해결하는 것이 중요합니다.  

다른 유형의 429 예외에 해당하는 다양한 오류 메시지가 있습니다.
- [요청 빈도가 높습니다. 추가 요청 단위가 필요할 수 있으므로 변경되지 않았습니다.](#request-rate-is-large)
- [메타데이터 요청 빈도가 높아서 요청이 완료되지 않았습니다.](#rate-limiting-on-metadata-requests)
- [일시적인 서비스 오류로 인해 요청이 완료되지 않았습니다.](#rate-limiting-due-to-transient-service-error)


## <a name="request-rate-is-large"></a>요청 빈도가 높습니다.
이는 가장 일반적인 시나리오입니다. 데이터에 대한 작업에서 사용하는 요청 단위가 프로비저닝된 RU/초 수를 초과할 때 발생합니다. 

### <a name="step-1-check-the-metrics-to-determine-the-percentage-of-requests-with-429-error"></a>1단계: 메트릭을 확인하여 429 오류가 있는 요청 비율 확인
429 오류 메시지가 표시되는 것이 반드시 데이터베이스 또는 컨테이너에 문제가 있음을 의미하지는 않습니다.

#### <a name="how-to-investigate"></a>조사 방법

요청의 전체 성공 횟수에 비해 429를 초래한 데이터베이스 또는 컨테이너에 대한 요청 비율을 확인합니다. Azure Cosmos DB 계정 블레이드에서 **인사이트** > **요청** > **상태 코드별 총 요청** 으로 이동합니다. 특정 데이터베이스 및 컨테이너로 필터링합니다. 

기본적으로 Azure Cosmos DB 클라이언트 SDK 및 데이터 가져오기 도구(예: Azure Data Factory, 대량 실행기 라이브러리)는 429에서 자동으로 요청을 다시 시도합니다. 일반적으로 9회까지 다시 시도합니다. 따라서 메트릭에 429가 표시되더라도 이 오류는 애플리케이션에 반환되지 않았을 수 있습니다. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/insights-429-requests.png" alt-text="429 및 2xx 요청 수를 보여 주는 상태 코드별 총 요청 차트":::


#### <a name="recommended-solution"></a>권장된 솔루션
일반적으로 프로덕션 워크로드의 경우 429가 있는 요청의 1~5% 사이가 표시되고 엔드투엔드 대기 시간이 허용되면 이는 RU/초가 완전히 활용되고 있다는 정상 신호입니다. 사용자가 조치할 필요는 없습니다. 그렇지 않으면 다음 문제 해결 단계로 이동합니다.

### <a name="step-2-determine-if-there-is-a-hot-partition"></a>2단계: 핫 파티션이 있는지 확인
핫 파티션은 더 높은 요청 볼륨으로 인해 하나 이상의 논리 파티션 키가 총 RU/초의 너무 작은 크기를 사용하는 경우에 발생합니다. 이는 요청을 균등하게 분산하지 않는 파티션 키 디자인으로 인해 발생할 수 있습니다. 이로 인해 많은 요청이 “핫”이 되는 논리(물리적을 의미함) 파티션의 작은 하위 집합으로 전달됩니다. 논리 파티션의 모든 데이터는 하나의 물리적 파티션에 있고 총 RU/초는 물리적 파티션 간에 균등하게 분산되므로 핫 파티션으로 인해 429가 발생하고 처리량이 비효율적으로 사용될 수 있습니다. 

핫 파티션을 발생시키는 분할 전략의 몇 가지 예제는 다음과 같습니다.
- 로 분할된 쓰기 작업이 많은 워크로드에 대한 IoT 디바이스 데이터를 저장하는 컨테이너가 `date` 있습니다. 단일 날짜의 모든 데이터는 동일한 논리 및 물리적 파티션에 있습니다. 매일 기록된 모든 데이터에는 동일한 날짜가 있기 때문에 매일 핫 파티션이 발생합니다. 
    - 대신, 이 시나리오의 경우 (GUID 또는 디바이스 ID)와 같은 파티션 키 또는 및 를 `id` 결합하는 [가상 파티션](./synthetic-partition-keys.md) `id` `date` 키는 값의 카디널리티를 높이고 요청 볼륨을 더 잘 배포합니다.
- 컨테이너가 로 분할된 다중 테넌트 시나리오가 `tenantId` 있습니다. 한 테넌트가 다른 테넌트보다 훨씬 더 활동적이면 핫 파티션이 발생합니다. 예를 들어 가장 큰 테넌트에서 100,000명의 사용자가 있지만 대부분의 테넌트에서 10명 미만의 사용자가 있는 경우 로 분할할 때 핫 파티션이 `tenantID` 있습니다. 
    - 이 이전 시나리오에서는 와 같은 보다 세분화된 속성으로 분할된 가장 큰 테넌트 전용 컨테이너를 사용하는 것이 `UserId` 좋습니다. 
    
#### <a name="how-to-identify-the-hot-partition"></a>핫 파티션을 식별하는 방법

핫 파티션이 있는지 확인하려면 **인사이트** > **처리량** > **PartitionKeyRangeID별 정규화된 RU 사용량(%)** 으로 이동합니다. 특정 데이터베이스 및 컨테이너로 필터링합니다. 

각 PartitionKeyRangeId는 하나의 물리적 파티션에 매핑됩니다. 한 PartitionKeyRangeId가 다른 항목보다 정규화된 RU 사용량이 훨씬 더 높은 경우(예: 한 항목은 일관되게 100%이지만 다른 항목은 30% 이하인 경우) 핫 파티션의 표시일 수 있습니다. [정규화된 RU 사용량 메트릭](../monitor-normalized-request-units.md)에 관해 자세히 알아봅니다.

:::image type="content" source="media/troubleshoot-request-rate-too-large/split-norm-utilization-by-pkrange-hot-partition.png" alt-text="핫 파티션이 있는 PartitionKeyRangeId별 정규화된 RU 사용량 차트":::

가장 많은 RU/초를 사용하는 논리 파티션 키를 확인하려면 [Azure 진단 로그](../cosmosdb-monitor-resource-logs.md)를 사용합니다. 이 샘플 쿼리는 각 논리 파티션 키에서 초당 사용되는 총 요청 단위의 합계를 계산합니다. 

> [!IMPORTANT]
> 진단 로그를 사용하면 수집된 데이터 볼륨에 따라 요금이 청구되는 Log Analytics 서비스에 대한 별도 요금이 발생합니다. 디버깅을 위해 제한된 시간 동안 진단 로그를 켜고 더 이상 필요하지 않은 경우 끄는 것이 좋습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24hour)
| where Category == "PartitionKeyRUConsumption"
| where collectionName_s == "CollectionName" 
| where isnotempty(partitionKey_s)
// Sum total request units consumed by logical partition key for each second
| summarize sum(todouble(requestCharge_s)) by partitionKey_s, operationType_s, bin(TimeGenerated, 1s)
| order by sum_requestCharge_s desc
```
이 샘플 출력에서는 특정 분에 값이 “Contoso”인 논리 파티션 키가 약 12,000 RU/초를 사용했지만 값이 “Fabrikam”인 논리 파티션 키가 600 RU/초 미만을 사용했음을 보여 줍니다. 빈도 제한이 발생한 기간 동안 이 패턴이 일관되었다면 핫 파티션을 나타냅니다. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/hot-logical-partition-key-results.png" alt-text="초당 요청 단위를 가장 많이 사용하는 논리 파티션 키":::

> [!TIP]
> 모든 워크로드에는 논리 파티션 간에 요청 볼륨의 자연 변형이 있습니다. 핫 파티션이 파티션 키 선택(키 변경이 필요할 수 있음)으로 인한 기초적 왜도 또는 워크로드 패턴의 자연 변형으로 인한 일시적인 급증에 의해 발생하는지 확인해야 합니다.

#### <a name="recommended-solution"></a>권장된 솔루션
[양호한 파티션 키를 선택하는 방법](../partitioning-overview.md#choose-partitionkey)에 관한 지침을 검토합니다.

빈도 제한 요청의 비율이 높고 핫 파티션이 없는 경우:
- 클라이언트 SDK, Azure Portal, PowerShell, CLI 또는 ARM 템플릿을 사용하여 데이터베이스 또는 컨테이너에서 [RU/초를 증가](../set-throughput.md)시킬 수 있습니다. [프로비전된 처리량(RU/s)의 크기를 조정하는 모범 사례에](../scaling-provisioned-throughput-best-practices.md) 따라 설정할 올바른 RU/s를 결정합니다.

빈도 제한 요청의 비율이 높고 기본 핫 파티션이 있는 경우:
-  장기적으로, 최상의 비용 및 성능을 위해서는 **파티션 키를 변경** 하는 것이 좋습니다. 파티션 키는 현재 위치에서 업데이트할 수 없으므로 다른 파티션 키를 사용하여 데이터를 새 컨테이너로 마이그레이션해야 합니다. Azure Cosmos DB는 이를 위해 [라이브 데이터 마이그레이션 도구](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)를 지원합니다.
- 단기적으로, RU/초를 일시적으로 늘려서 핫 파티션에 대한 더 많은 처리량을 허용할 수 있습니다. 이는 RU/초를 오버프로비저닝하고 비용이 더 많이 들기 때문에 장기 전략으로 권장되지 않습니다. 

> [!TIP]
>  처리량을 늘리면 스케일 업 작업이 즉시 완료되거나 스케일 업하려는 RU/초 수에 따라 완료하는 데 최대 5~6시간이 필요합니다. 비동기 스케일 업 작업을 트리거하지 않고 설정할 수 있는(Azure Cosmos DB가 더 많은 물리적 파티션을 프로비저닝하는 데 필요한) 가장 높은 RU/초 수를 알아보려면 고유한 PartitionKeyRangeId 수에 10,0000 RU/초를 곱합니다. 예를 들어, 30,000 RU/초가 프로비저닝되고 5개 물리적 파티션(물리적 파티션당 6000 RU/초가 할당됨)이 있는 경우 즉각적인 스케일 업 작업에서 50,000 RU/초(물리적 파티션당 10,000 RU/초)로 늘릴 수 있습니다. 50,000 RU/초보다 크게 늘리려면 비동기 스케일 업 작업이 필요합니다.

### <a name="step-3-determine-what-requests-are-returning-429s"></a>3단계: 429를 반환하는 요청 확인

#### <a name="how-to-investigate-requests-with-429s"></a>429가 있는 요청을 조사하는 방법
[Azure 진단 로그](../cosmosdb-monitor-resource-logs.md)를 사용하여 429를 반환하는 요청 및 사용한 CPU 수를 식별합니다. 이 샘플 쿼리는 분 수준에서 집계됩니다. 

> [!IMPORTANT]
> 진단 로그를 사용하면 수집된 데이터 볼륨에 따라 요금이 청구되는 Log Analytics 서비스에 대한 별도 요금이 발생합니다. 디버깅을 위해 제한된 시간 동안 진단 로그를 켜고 더 이상 필요하지 않은 경우 끄는 것이 좋습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24h)
| where Category == "DataPlaneRequests"
| summarize throttledOperations = dcountif(activityId_g, statusCode_s == 429), totalOperations = dcount(activityId_g), totalConsumedRUPerMinute = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, OperationName, requestResourceType_s, bin(TimeGenerated, 1min)
| extend averageRUPerOperation = 1.0 * totalConsumedRUPerMinute / totalOperations 
| extend fractionOf429s = 1.0 * throttledOperations / totalOperations
| order by fractionOf429s desc
```
예를 들어, 샘플 출력은 1분마다 문서 만들기 요청의 30%가 빈도 제한되었으며 각 요청은 평균 17 RU를 사용함을 보여 줍니다.
:::image type="content" source="media/troubleshoot-request-rate-too-large/throttled-requests-diagnostic-logs-results.png" alt-text="진단 로그의 429가 있는 요청":::

#### <a name="recommended-solution"></a>권장된 솔루션
##### <a name="429s-on-create-replace-or-upsert-document-requests"></a>문서 만들기, 바꾸기 또는 upsert 요청에 대한 429
- 기본적으로 SQL API에서 모든 속성은 인덱싱됩니다. 필요한 속성만 인덱싱하도록 [인덱싱 정책](../index-policy.md)을 조정합니다.
이렇게 하면 문서 만들기 작업당 필요한 요청 단위가 감소하여 429가 표시될 가능성이 감소하거나 프로비저닝된 RU/초의 동일한 양에 대해 초당 더 높은 작업을 수행할 수 있습니다. 

##### <a name="429s-on-query-document-requests"></a>문서 쿼리 요청에 대한 429
- [높은 RU 요금이 부과되는 쿼리 문제를 해결](troubleshoot-query-performance.md#querys-ru-charge-is-too-high)하기 위한 지침을 따릅니다.

##### <a name="429s-on-execute-stored-procedures"></a>저장 프로시저 실행에 대한 429
- [저장 프로시저](stored-procedures-triggers-udfs.md)는 파티션 키 값에서 쓰기 트랜잭션이 필요한 작업에 사용됩니다. 많은 읽기 또는 쿼리 작업에는 저장 프로시저를 사용하지 않는 것이 좋습니다. 최상의 성능을 위해 Cosmos SDK를 사용하여 클라이언트 쪽에서 관련 읽기 또는 쿼리 작업을 수행해야 합니다. 

## <a name="rate-limiting-on-metadata-requests"></a>메타데이터 요청에 대한 빈도 제한

메타데이터 빈도 제한은 데이터베이스 및/또는 컨테이너에서 대용량 메타데이터 작업을 수행할 때 발생할 수 있습니다. 메타데이터 작업은 다음을 포함합니다.
- 컨테이너 또는 데이터베이스 만들기, 읽기, 업데이트 또는 삭제
- Cosmos 계정의 데이터베이스 또는 컨테이너 나열
- 제안을 쿼리하여 현재 프로비저닝된 처리량 확인 

이 작업에 대한 시스템 예약 RU 제한이 있으므로 데이터베이스 또는 컨테이너의 프로비저닝된 RU/초를 늘리는 것은 영향을 주지 않으며 권장되지 않습니다. [메타데이터 작업에 대한 제한](../concepts-limits.md#metadata-request-limits)을 참조하세요.

#### <a name="how-to-investigate"></a>조사 방법
**인사이트** > **시스템** > **상태 코드별 메타데이터 요청** 으로 이동합니다. 원하는 경우 특정 데이터베이스 및 컨테이너로 필터링합니다. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/metadata-throttling-insights.png" alt-text="인사이트의 상태 코드별 메타데이터 요청 차트":::

#### <a name="recommended-solution"></a>권장된 솔루션
- 애플리케이션이 메타데이터 작업을 수행해야 하는 경우에는 요청을 더 낮은 빈도로 전송하는 백오프 정책을 구현하는 것이 좋습니다. 

- 정적 Cosmos DB 클라이언트 인스턴스 사용 DocumentClient 또는 CosmosClient가 초기화되면 Cosmos DB SDK는 일관성 수준, 데이터베이스, 컨테이너, 파티션, 제안에 관한 정보를 포함하여 계정에 관한 메타데이터를 가져옵니다. 이 초기화는 많은 수의 RU를 사용할 수 있으며 드물게 수행해야 합니다. 단일 DocumentClient 인스턴스를 사용하며 애플리케이션의 수명에 대해 사용합니다.

- 데이터베이스 및 컨테이너의 이름을 캐시합니다. 구성에서 데이터베이스 및 컨테이너의 이름을 검색하거나 시작할 때 이름을 캐시합니다. ReadDatabaseAsync/ReadDocumentCollectionAsync 또는 CreateDatabaseQuery/CreateDocumentCollectionQuery 같은 호출은 시스템 예약 RU 제한에서 사용되는 서비스에 대한 메타데이터 호출을 생성합니다. 이 작업은 자주 수행되지 않습니다.

## <a name="rate-limiting-due-to-transient-service-error"></a>일시적인 서비스 오류로 인한 빈도 제한

이 429 오류는 요청에 일시적인 서비스 오류가 발생할 때 반환됩니다. 데이터베이스 또는 컨테이너에 대한 RU/초를 늘리는 것은 영향을 주지 않으며 권장되지 않습니다.

#### <a name="recommended-solution"></a>권장된 솔루션
요청을 다시 시도하십시오. 오류가 몇 분 동안 지속되면 [Azure Portal](https://portal.azure.com/)에서 지원 티켓을 제출합니다.

## <a name="next-steps"></a>다음 단계
* 데이터베이스 또는 컨테이너의 [정규화 RU/초 사용량을 모니터링](../monitor-normalized-request-units.md)합니다.
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
* Azure Cosmos DB Java v4 SDK를 사용할 때 발생하는 문제를 [진단하고 해결](troubleshoot-java-sdk-v4-sql.md)합니다.
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.