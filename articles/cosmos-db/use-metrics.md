---
title: Azure Cosmos DB에서 인사이트를 사용하여 모니터링 및 디버그
description: Azure Cosmos DB에서 메트릭을 사용하여 일반적인 문제를 디버그하고 데이터베이스를 모니터링합니다.
ms.author: esarroyo
author: StefArroyo
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/14/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e85fa72288ab31fd8e61fcda731debb3517057f
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393390"
---
# <a name="monitor-and-debug-with-insights-in-azure-cosmos-db"></a>Azure Cosmos DB에서 인사이트를 사용하여 모니터링 및 디버그
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 처리량, 스토리지, 일관성, 가용성 및 대기 시간에 대한 인사이트를 제공합니다. Azure Portal은 이러한 메트릭의 집계 보기를 제공합니다. Azure Monitor API에서 Azure Cosmos DB 메트릭을 볼 수도 있습니다. 컨테이너 이름과 같은 메트릭의 차원 값은 대/소문자를 구분하지 않습니다. 따라서 해당 차원 값의 문자열 비교를 수행할 때는 대/소문자를 구분하지 않는 비교를 사용해야 합니다. Azure Monitor에서 메트릭을 보는 방법에 대해 알아보려면 [Azure Monitor에서 메트릭 가져오기](./monitor-cosmos-db.md) 문서를 참조하세요.

이 문서에서는 일반적인 사용 사례와 Azure Cosmos DB 인사이트를 사용하여 이러한 문제를 분석 및 디버그할 수 있는 방법을 안내합니다. 기본적으로 메트릭 인사이트는 5분마다 수집되고 7일 동안 유지됩니다.

## <a name="view-insights-from-azure-portal"></a>Azure Portal에서 인사이트 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Cosmos DB 계정으로 이동합니다.

1. **메트릭** 창 또는 **인사이트(미리 보기)** 창에서 계정 메트릭을 볼 수 있습니다.

   * **메트릭**. 이 창에서는 정기적으로 수집되며 특정 시간에 시스템의 일부 측면을 설명하는 수치 메트릭을 제공합니다. 예를 들어 [서버 측 대기 시간 메트릭](monitor-server-side-latency.md), [정규화된 요청 단위 사용량 메트릭](monitor-normalized-request-units.md) 등을 보고 모니터링할 수 있습니다.

   * **인사이트(미리 보기)** : 이 창에서는 Azure Cosmos DB에 대한 사용자 지정 모니터링 환경을 제공합니다. Azure Monitor에서 수집되는 것과 동일한 메트릭과 로그를 사용하여 계정에 대한 집계 보기를 표시합니다.

1. **인사이트(미리 보기)** 창을 엽니다. 기본적으로 인사이트 창에는 계정의 컨테이너에 대한 처리량, 요청, 스토리지, 가용성, 대기 시간, 시스템 및 계정 관리 메트릭이 표시됩니다. 인사이트를 보려는 **시간 범위**, **데이터베이스** 및 **컨테이너** 를 선택할 수 있습니다. **개요** 탭에는 선택한 데이터베이스 및 컨테이너에 대한 RU/s 사용량, 데이터 사용량, 인덱스 사용량, 제한된 요청 및 정규화된 RU/s 사용량이 표시됩니다.

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Azure Portal의 Cosmos DB 성능 메트릭" lightbox="./media/use-metrics/performance-metrics.png" :::

1. **인사이트** 창에서 다음 메트릭을 사용할 수 있습니다.

   * **처리량** - 이 탭에는 컨테이너에 대해 프로비저닝된 처리량 또는 스토리지 용량이 초과되어 사용 또는 실패한(429 응답 코드) 총 요청 단위 수가 표시됩니다.

   * **요청** - 이 탭에는 상태 코드, 작업 유형, 실패한 요청 수(429 응답 코드)별로 처리된 총 요청 수가 표시됩니다. 컨테이너에 대해 프로비저닝된 처리량 또는 스토리지 용량이 초과하면 요청이 실패합니다.

   * **스토리지** -이 탭에는 선택한 기간 동안의 데이터 크기 및 인덱스 사용량이 표시됩니다.

   * **가용성** - 이 탭에는 총 요청 수 중에서 시간당 성공한 요청의 비율이 표시됩니다. 성공률은 Azure Cosmos DB SLA에 의해 정의됩니다.

   * **대기 시간** - 이 탭에는 계정이 운영되는 지역에서 Azure Cosmos DB가 관찰한 읽기 및 쓰기 대기 시간이 표시됩니다. 지역 복제된 계정에 대한 지역 간 대기 시간을 시각화할 수 있습니다. 또한 다른 작업으로 서버 쪽 대기 시간을 지정할 수 있습니다. 이 메트릭은 엔드투엔드 요청의 대기 시간을 나타내지 않습니다.

   * **시스템** - 이 탭에는 주 파티션에서 처리된 메타데이터 요청 수가 표시됩니다. 또한 제한된 요청 식별을 지원합니다.

   * **계정 관리** - 이 탭에는 계정 만들기, 삭제, 키 업데이트, 네트워크, 복제 설정 등의 계정 관리 활동에 대한 메트릭이 표시됩니다.

다음 섹션에서는 Azure Cosmos DB 메트릭을 사용할 수 있는 일반적인 시나리오에 대해 설명합니다.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>성공한 요청 수 또는 오류가 발생하는 요청 수 이해

시작하려면 [Azure Portal](https://portal.azure.com)에서 **인사이트** 블레이드로 이동합니다. 이 블레이드에서 **요청** 탭을 열면 총 요청 수가 상태 코드 및 작업 유형별로 분할되어 나와 있는 차트가 표시됩니다. HTTP 상태 코드에 대한 자세한 내용은 [Azure Cosmos DB에 대한 HTTP 상태 코드](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)를 참조하세요.

가장 일반적인 오류 상태 코드는 429입니다(속도 제한/제한). 이 오류는 Azure Cosmos DB에 대한 요청이 프로비전된 처리량보다 더 많은 것을 의미합니다. 이 문제에 대한 가장 일반적인 솔루션은 지정된 컬렉션에 대한 [RU를 확장](./set-throughput.md)하는 것입니다.

:::image type="content" source="media/use-metrics/request-count.png" alt-text="분당 요청 수" lightbox= "media/use-metrics/request-count.png":::

## <a name="determine-the-throughput-consumption-by-a-partition-key-range"></a>파티션 키 범위별로 처리량 사용량 확인

파티션 키의 좋은 카디널리티 사용은 확장 가능한 모든 애플리케이션에 필요합니다. 모든 분할된 컨테이너의 처리량 배포를 파티션 키 범위 ID로 분석하여 확인하려면 **인사이트(미리 보기)** 창으로 이동합니다. **처리량** 탭을 열면 서로 다른 파티션 키 범위에서 정규화된 RU/s 사용량이 차트에 표시됩니다.

:::image type="content" source="media/use-metrics/throughput-consumption-partition-key-range.png" alt-text="파티션 키 범위 ID별로 정규화된 처리량 사용량" lightbox="media/use-metrics/throughput-consumption-partition-key-range.png":::

이 차트를 통해 핫 파티션이 있는지 확인할 수 있습니다. 불균등 처리량 배포 시 *핫* 파티션이 발생할 수 있고, 이로 인해 제한된 요청이 초래되고 다시 분할해야 할 수 있습니다. 배포 불균등을 초래하는 파티션 키를 식별한 후 더 많이 배포된 파티션 키로 컨테이너를 다시 분할해야 할 수도 있습니다. Azure Cosmos DB에서 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](./partitioning-overview.md)을 참조하세요.

## <a name="determine-the-data-and-index-usage"></a>데이터 및 인덱스 사용량 확인

데이터 사용량, 인덱스 사용량 및 문서 사용량에 따라 분할된 컨테이너의 스토리지 배포를 결정하는 것이 중요합니다. 인덱스 사용량을 최소화하고, 데이터 사용량을 최대화하고, 쿼리를 최적화할 수 있습니다. 이 데이터를 가져오려면 **인사이트(미리 보기)** 창으로 이동하여 **스토리지** 탭을 엽니다.

:::image type="content" source="media/use-metrics/data-index-consumption.png" alt-text="데이터, 인덱스 및 문서 사용량" lightbox="media/use-metrics/data-index-consumption.png" :::

## <a name="compare-data-size-against-index-size"></a>데이터 크기 및 인덱스 크기 비교

Azure Cosmos DB에서 사용된 총 스토리지는 데이터 크기와 인덱스 크기의 조합입니다. 일반적으로 인덱스 크기는 데이터 크기의 비율입니다. 자세한 내용은 [인덱스 크기](index-policy.md#index-size) 문서를 참조하세요. [Azure Portal](https://portal.azure.com)의 메트릭 블레이드에서, 스토리지 탭에는 스토리지 사용량이 데이터 및 인덱스에 따라 분석되어 표시됩니다.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

인덱스 공간을 절약하려는 경우 [인덱싱 정책](index-policy.md)을 조정할 수 있습니다.

## <a name="debug-why-queries-are-running-slow"></a>쿼리가 느리게 실행되는 이유 디버그

SQL API SDK에서 Azure Cosmos DB는 쿼리 실행 통계를 제공합니다.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* 는 쿼리의 각 구성 요소를 실행하는 데 걸린 기간을 자세히 보여 줍니다. 장기 실행 쿼리에 대한 가장 일반적인 근본 원인은 검색이며 쿼리에서 인덱스를 활용할 수 없음을 의미합니다. 더 나은 필터 조건을 사용하여 이 문제를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Portal에서 제공된 메트릭을 사용하여 문제를 모니터링 및 디버그하는 방법을 알아보았습니다. 다음 문서를 참조하여 데이터베이스 성능 개선에 대해 자세히 알아볼 수 있습니다.

* Azure Monitor에서 메트릭을 보는 방법에 대해 알아보려면 [Azure Monitor에서 메트릭 가져오기](./monitor-cosmos-db.md) 문서를 참조하세요. 
* [Azure Cosmos DB를 사용한 성능 및 규모 테스트](performance-testing.md)
* [Azure Cosmos DB에 대한 성능 팁](performance-tips.md)