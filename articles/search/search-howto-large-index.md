---
title: 기본 제공 인덱서를 사용하여 큰 데이터 세트 인덱싱
titleSuffix: Azure Cognitive Search
description: 일괄 처리 모드, 리소싱 및 예약, 병렬 및 분산된 인덱싱의 기술을 통해 큰 데이터 인덱싱 또는 계산 집약적 인덱싱에 대한 전략입니다.
manager: nitinme
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 0c7dccdc484a99e751a7676729a760499b501672
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487980"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Azure Cognitive Search에서 대용량 데이터 세트를 인덱싱하는 방법

Azure Cognitive Search는 데이터를 검색 인덱스로 가져오기 위한 [두 가지 기본 방법](search-what-is-data-import.md)을 지원합니다. 즉, 프로그래밍 방식으로 데이터를 인덱스로 *푸시* 하거나 지원되는 데이터 원본에서 [Azure Cognitive Search 인덱서](search-indexer-overview.md)를 가리켜서 데이터를 *풀* 합니다.

데이터 볼륨이 커지거나 처리 요구 사항이 변경되면 단순하거나 기본 인덱싱 전략은 더 이상 유용하지 않을 수 있습니다. Azure Cognitive Search의 경우 예약 및 분산된 워크로드에 대한 소스 관련 인덱서 사용에 대해 큰 데이터 세트 수용, 데이터 업로드 요청을 구성하는 방법의 범위에 대한 여러 가지 방법이 있습니다.

동일한 기술이 장기 실행 프로세스에도 적용됩니다. 특히 [병렬 인덱싱](#parallel-indexing)에서 설명된 단계는 [AI 보강 파이프라인](cognitive-search-concept-intro.md)에서의 이미지 분석 또는 자연어 처리와 같은 계산 집약적인 인덱싱에 유용합니다.

다음 섹션에서는 푸시 API와 인덱서 모두를 사용하여 대량의 데이터를 인덱싱하는 기술에 대해 설명합니다.

## <a name="use-the-push-api"></a>푸시 API 사용

[문서 추가 REST API](/rest/api/searchservice/addupdate-or-delete-documents) 또는 [IndexDocuments 메서드(.NET)를](/dotnet/api/azure.search.documents.searchclient.indexdocuments)사용하여 인덱스에 데이터를 푸시하는 경우 인덱싱 속도에 영향을 주는 몇 가지 주요 고려 사항이 있습니다. 이러한 요소는 아래 섹션에 설명되어 있으며 서비스 용량 설정에서 코드 최적화에 이르기까지 다양합니다.

푸시 모델 인덱싱을 설명하는 코드 샘플 및 자세한 내용은 [자습서: 인덱싱 속도 최적화](tutorial-optimize-indexing-push-api.md)를 참조하세요.

### <a name="capacity-of-your-service"></a>서비스의 용량

첫 번째 단계로 서비스를 프로비저닝한 계층의 특성과 [한도](search-limits-quotas-capacity.md)를 검토합니다. 가격 책정 계층 간의 주요 차별화 요소 중 하나는 인덱싱 속도에 직접적인 영향을 미치는 파티션의 크기와 속도입니다. 워크로드에 비해 충분하지 않은 계층에서 검색 서비스를 프로비저닝한 경우 새 계층으로 업그레이드하는 것이 인덱싱 처리량을 늘리는 가장 쉽고 효과적인 솔루션일 수 있습니다.

계층에 대해 만족한다면 다음 단계는 파티션 수를 늘리는 것일 수 있습니다. 서비스 실행의 전체 비용을 줄이기 위해 초기 인덱싱 실행 후 파티션 할당을 다시 하향 조정할 수 있습니다.

> [!NOTE]
> 또는 복제본을 추가하면 인덱싱 속도가 빨라질 수 있지만 보장되지는 않습니다. 반면에 추가 복제본은 검색 서비스가 처리할 수 있는 쿼리 볼륨을 증가시킵니다. 복제본은 [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)를 얻기 위한 핵심 구성 요소이기도 합니다.
>
> 파티션/복제본을 추가하거나 상위 계층으로 업그레이드하기 전에 금전적 비용과 할당 시간을 고려합니다. 파티션을 추가하면 인덱싱 속도가 크게 향상될 수 있지만 파티션을 추가/제거하는 데 15분에서 몇 시간이 걸릴 수 있습니다. 자세한 내용은 [용량 조정](search-capacity-planning.md)에 대한 설명서를 참조하세요.
>

### <a name="review-index-schema"></a>인덱스 스키마 검토

인덱스의 스키마는 데이터를 인덱싱하는 데 중요한 역할을 합니다. 더 많은 필드가 있고 더 많은 속성(예: *검색 가능*, *패싯 가능* 또는 *필터링 가능*)을 설정하면 인덱스 생성 시간이 늘어납니다. 일반적으로 검색 인덱스에서 실제로 필요한 필드만 생성하고 지정해야 합니다.

> [!NOTE]
> 문서 크기를 줄이려면 쿼리할 수 없는 데이터를 인덱스에 추가하지 마세요. 이미지 및 기타 이진 데이터는 직접 검색할 수 없으므로 인덱스에 저장하지 않아야 합니다. 쿼리할 수 없는 데이터를 검색 결과에 통합하려면 리소스에 대한 URL 참조를 저장하는 검색할 수 없는 필드를 정의해야 합니다.

### <a name="check-the-batch-size"></a>일괄 처리 크기 확인

대용량 데이터 세트를 인덱싱하기 위한 가장 간단한 메커니즘 중 하나는 단일 요청에서 여러 문서 또는 레코드를 제출하는 것입니다. 전체 페이로드가 16MB 미만인 한 요청은 대량 업로드 작업에서 최대 1000개의 문서를 처리할 수 있습니다. 이러한 한도는 .NET SDK에서 [문서 추가 REST API](/rest/api/searchservice/addupdate-or-delete-documents) 또는 [IndexDocuments 메서드](/dotnet/api/azure.search.documents.searchclient.indexdocuments)를 사용하는지 여부에 관계없이 적용됩니다. 두 API 모두 각 요청의 본문에 1000개의 문서를 패키징합니다.

일괄 처리를 사용하여 문서를 인덱싱하면 인덱싱 성능이 크게 향상됩니다. 가장 적합한 데이터 일괄 처리 크기를 결정하는 것은 인덱싱 속도를 최적화하는 핵심 구성 요소입니다. 최적의 일괄 처리 크기에 영향을 주는 두 가지 주요 요소는 다음과 같습니다.

+ 인덱스의 스키마
+ 데이터의 크기

최적의 일괄 처리 크기는 인덱스와 데이터에 따라 달라지므로 다양한 일괄 처리 크기를 테스트하여 시나리오에서 가장 빠른 인덱싱 속도를 달성할 수 있는 결과를 확인하는 것이 가장 좋습니다. 이 [자습서](tutorial-optimize-indexing-push-api.md)는 .NET SDK를 사용하여 일괄 처리 크기를 테스트하기 위한 샘플 코드를 제공합니다. 

### <a name="number-of-threadsworkers"></a>스레드/작업자 수

Azure Cognitive Search의 인덱싱 속도를 최대한 활용하려면 여러 스레드를 사용하여 일괄 처리 인덱싱 요청을 서비스에 동시에 보내야 할 수 있습니다.  

최적의 스레드 수는 다음에 의해 결정됩니다.

+ 검색 서비스 계층
+ 파티션 수
+ 일괄 처리의 크기
+ 인덱스의 스키마

이 샘플을 수정하고 다양한 스레드 수로 테스트하여 시나리오에 가장 적합한 스레드 수를 결정할 수 있습니다. 그러나 여러 스레드가 동시에 실행되는 동안에는 대부분의 효율성 이점을 활용할 수 있어야 합니다. 

> [!NOTE]
> 검색 서비스의 계층을 늘리거나 파티션을 늘리면 동시 스레드의 수도 늘려야 합니다.

검색 서비스에 대한 요청을 늘리면 요청이 완전히 성공하지 못했음을 나타내는 [HTTP 상태 코드](/rest/api/searchservice/http-status-codes)가 발생할 수 있습니다. 인덱싱 중에 발생하는 두 가지 일반적인 HTTP 상태 코드는 다음과 같습니다.

+ **503 서비스를 사용할 수 없음** - 이 오류는 시스템의 부하가 높고 요청을 현재 처리할 수 없음을 의미합니다.
+ **207 여러 상태** - 이 오류는 일부 문서가 성공했지만 하나 이상의 문서가 실패했음을 의미합니다.

### <a name="retry-strategy"></a>재시도 전략

오류가 발생하면 [지수 백오프 다시 시도 전략](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)을 사용하여 요청을 다시 시도해야 합니다.

Azure Cognitive Search의 .NET SDK에서 503 및 기타 실패한 요청을 자동으로 다시 시도하지만 207을 다시 시도하는 사용자 고유의 논리를 구현해야 합니다. [Polly](https://github.com/App-vNext/Polly)와 같은 오픈 소스 도구를 사용하여 다시 시도 전략을 구현할 수도 있습니다.

### <a name="network-data-transfer-speeds"></a>네트워크 데이터 전송 속도

네트워크 데이터 전송 속도는 데이터를 인덱싱할 때 제한 요소가 될 수 있습니다. Azure 환경 내에서 데이터를 인덱싱하면 인덱싱 속도를 쉽게 높일 수 있습니다.

## <a name="use-indexers-pull-api"></a>인덱서 사용(풀 API)

[인덱서](search-indexer-overview.md)는 검색 가능한 콘텐츠에 대해 지원되는 Azure 데이터 원본을 크롤링하는 데 사용됩니다. 대규모 인덱싱에 대해 명시적으로 의도하지 않으므로 여러 인덱서 기능은 더 큰 데이터 세트를 수용하는 데 특히 유용합니다.

+ 스케줄러를 통해 시간이 지남에 따라 분배할 수 있도록 정기적으로 인덱싱을 분배할 수 있습니다.
+ 예약된 인덱싱은 마지막으로 알려진 중지 지점에서 다시 시작할 수 있습니다. 데이터 원본이 24시간 기간 내에 완벽하게 크롤링되지 않는 경우 인덱서는 중단 위치에서 두 번째 날에 인덱싱을 다시 시작합니다.
+ 데이터를 작은 개별 데이터 원본으로 분할하면 병렬 처리가 가능합니다. 원본 데이터를 Azure Blob Storage의 여러 컨테이너와 같은 더 작은 구성 요소로 나눈 다음, Azure Cognitive Search에서 병렬로 인덱싱할 수 있는 해당하는 여러 [데이터 원본 개체](/rest/api/searchservice/create-data-source)를 만들 수 있습니다.

> [!NOTE]
> 인덱서는 데이터 원본에 따라 다르므로 인덱서 접근 방식을 사용하는 것은 Azure에서 [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md)의 선택된 데이터 원본에 대해서만 가능합니다.

### <a name="check-the-batchsize-argument-on-create-indexer"></a>인덱서 생성에서 batchSize 인수 확인

푸시 API와 마찬가지로 인덱서를 사용하여 일괄 처리당 항목 수를 구성할 수 있습니다. [만들기 인덱서 REST API](/rest/api/searchservice/Create-Indexer)에 따른 인덱서의 경우 `batchSize` 인수를 설정하여 데이터의 특징에 더 잘 일치하도록 이 설정을 사용자 지정할 수 있습니다. 

기본 일괄 처리 크기는 데이터 원본에 따라 다릅니다. Azure SQL Database 및 Azure Cosmos DB의 기본 일괄 처리 크기는 1000입니다. 반면 Azure Blob 인덱싱은 큰 평균 문서 크기를 고려하여 일괄 처리 크기를 문서 10개로 설정합니다. 

### <a name="scheduled-indexing"></a>예약된 인덱싱

인덱서 예약은 큰 데이터 세트를 처리하고 인식 검색 파이프라인에서 이미지 분석과 같이 느리게 처리되는 분석을 처리하는 중요한 메커니즘입니다. 인덱서 처리는 24시간 범위 내에 실행됩니다. 24시간 내에 처리가 완료되지 않으면 인덱서 일정 예약의 동작이 사용자에게 유리하게 작용할 수 있습니다. 

기본적으로 예약된 인덱싱은 특정 간격으로 시작되며, 대개 다음으로 예약된 간격에서 다시 시작되기 전에 작업이 완료됩니다. 하지만 간격 내에 처리가 완료되지 않으면 인덱서가 중지됩니다(시간이 초과되기 때문임). 다음 번 간격이 시작되면, 마지막으로 중단된 위치에서 처리가 다시 시작되며, 시스템은 해당 위치를 추적합니다. 

실제 여러 날에 걸쳐 수행되는 인덱스 로드의 경우 24시간 일정으로 인덱서를 배치할 수 있습니다. 다음 번 24시간 주기에서 인덱싱이 다시 시작되면 마지막으로 성공한 문서에서 다시 시작됩니다. 이러한 방식으로 인덱서는 처리되지 않은 모든 문서가 처리될 때까지 여러 날에 걸쳐 문서 백로그를 통해 작업을 수행할 수 있습니다. 일반적인 일정 설정에 대한 자세한 내용은 [인덱서 REST API 만들기](/rest/api/searchservice/Create-Indexer) 또는 [Azure Cognitive Search에 대한 인덱서를 예약하는 방법](search-howto-schedule-indexers.md)을 참조하세요.

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>병렬 인덱싱

병렬 인덱싱 전략은 여러 데이터 원본을 한 번에 인덱싱하는 데 기반하며 여기에서 각 데이터 원본 정의는 데이터의 하위 세트를 지정합니다. 

비 루틴, 계산 집약적 인덱싱 요구 사항의 경우(예제: 인식 검색 파이프라인에서 스캔된 문서의 OCR, 이미지 분석 또는 자연어 처리) 병렬 인덱싱 전략은 종종 짧은 시간에 장기 실행 프로세스를 완료하기 위한 적합한 방법입니다. 쿼리 요청을 제거하거나 줄일 수 있는 경우 쿼리를 동시에 처리하지 않는 서비스에 대한 병렬 인덱싱은 처리 속도가 느린 대량의 콘텐츠를 통한 작업에 최상의 전략 옵션입니다. 

병렬 처리에는 다음과 같은 요소가 있습니다.

+ 원본 데이터를 동일한 컨테이너 내의 여러 컨테이너 또는 여러 가상 폴더로 세분화합니다. 
+ 각각의 미니 데이터 세트를 자체 [데이터 원본](/rest/api/searchservice/create-data-source)에 매핑하고, 자체 [인덱서](/rest/api/searchservice/create-indexer)와 쌍을 이루도록 합니다.
+ Cognitive Search의 경우 각 인덱서 정의에 동일한 [기술 집합](/rest/api/searchservice/create-skillset)을 참조합니다.
+ 동일한 대상 검색 인덱스에 기록합니다. 
+ 모든 인덱서가 동시에 실행되도록 예약합니다.

> [!NOTE]
> Azure Cognitive Search에서는 개별 복제본 또는 파티션을 인덱싱 또는 쿼리 처리에 할당할 수 없습니다. 시스템은 리소스가 사용되는 방식을 결정합니다. 쿼리 성능에 미치는 영향을 이해하기 위해 프로덕션으로 롤링하기 전에 테스트 환경에서 병렬 인덱싱을 시도할 수 있습니다.  

### <a name="how-to-configure-parallel-indexing"></a>병렬 인덱싱을 구성하는 방법

인덱서의 경우 처리 용량은 검색 서비스에 사용되는 각 SU(서비스 단위)에 대해 하나의 인덱서 하위 시스템을 기반으로 합니다. 두 개 이상의 복제본이 있는 기본 또는 표준 계층에 프로비저닝된 Azure Cognitive Search 서비스에서는 여러 개의 동시 인덱서가 존재할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)의 검색 서비스 대시보드 **개요** 페이지에서 **가격 책정 계층** 을 확인하여 병렬 인덱싱을 수용할 수 있는지 확인합니다. 기본 및 표준 계층 모두 여러 복제본이 제공됩니다.

2. 서비스 내 검색 단위 수만큼의 인덱서를 병렬로 실행할 수 있습니다. **설정** > **규모** 에서 병렬 처리를 위해 인덱서 워크로드마다 복제본 또는 파티션을 하나씩 추가하여 [복제본 또는 파티션을 증가](search-capacity-planning.md)시킵니다. 기존 쿼리 볼륨에 대해 충분한 수를 유지합니다. 인덱싱을 위해 쿼리 워크로드를 희생하는 것은 바람직한 절충안이 아닙니다.

3. Azure Cognitive Search 인덱서가 도달할 수 있는 수준의 여러 컨테이너에 데이터를 배포합니다. 여기에는 Azure SQL Database의 여러 테이블, Azure Blob Storage의 여러 컨테이너 또는 여러 컬렉션이 해당될 수 있습니다. 각 테이블 또는 컨테이너에 대해 하나의 데이터 원본 개체를 정의합니다.

4. 병렬로 실행할 여러 인덱서 생성 및 예약:

   + 서비스에 복제본이 여섯 개 있다고 가정합니다. 전체 데이터 집합의 6방향 분할을 위해 데이터 집합의 1/6을 포함하는 데이터 원본에 각각 매핑된 인덱서 여섯 개를 구성합니다. 

   + 각 인덱서에서 동일한 인덱스를 가리킵니다. Cognitive Search 워크로드의 경우 각 인덱서가 동일한 기술 집합을 가리키도록 합니다.

   + 각 인덱서 정의 내에 동일한 런타임 실행 패턴을 예약합니다. 예를 들어, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }`는 2018-05-15에 모든 인덱서에 대해 8시간 간격으로 실행되는 일정을 만듭니다.

예약된 시간에 모든 인덱서는 실행, 데이터 로드, 보강(Cognitive Search 파이프라인을 구성한 경우) 적용, 인덱스에 쓰기 작업을 시작합니다. Azure Cognitive Search는 업데이트를 위해 인덱스를 잠그지 않습니다. 동시 쓰기가 관리되며 첫 번째 시도에서 특정 쓰기가 성공하지 못하면 다시 시도됩니다.

> [!Note]
> 복제본을 늘릴 때 인덱스 크기가 상당히 증가할 것으로 예상되면 파티션 수를 늘리는 것이 좋습니다. 파티션은 인덱싱된 콘텐츠 조각을 저장합니다. 파티션이 많을수록 각 파티션이 저장해야 하는 조각이 줄어듭니다.

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Cognitive Search의 보안](search-security-overview.md)