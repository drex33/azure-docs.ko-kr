---
title: Azure Cosmos DB에서 사용자 요청 비용 최적화
description: 이 문서에서는 Azure Cosmos DB에 대한 요청을 실행하는 경우 비용을 최적화하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: db6973d19fd8bd4fc4d36903b3205e6c8e01314d
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029150"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 요청 비용 최적화
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 읽기 및 쓰기 요청을 [요청 단위](request-units.md)로 변환하는 방법 및 관련 요청 비용을 최적화하는 방법을 설명합니다. 읽기 작업에는 지점 읽기 및 쿼리가 포함됩니다. 쓰기 작업에는 항목 삽입, 바꾸기, 삭제, upsert가 포함됩니다.

Azure Cosmos DB는 컨테이너 내 항목에서 작동하는 다양한 데이터베이스 작업 집합을 제공합니다. 이러한 작업 각각과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신, 요청을 처리하기 위한 다양한 데이터베이스 작업을 수행하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다.

## <a name="measuring-the-ru-charge-of-a-request"></a>요청의 RU 요금 측정

실제 비용을 이해하고 최적화 효과를 평가하기 위해 요청에 대한 RU 요금을 측정하는 것이 중요합니다. Azure Portal을 사용하거나 SDK 중 하나를 통해 Azure Cosmos DB에서 다시 보낸 응답을 검사하여 이 비용을 가져올 수 있습니다. 이를 수행하는 방법에 대한 자세한 지침은 [Azure Cosmos DB에서 요청 단위 요금 찾기](find-request-unit-charge.md)를 참조하세요.

## <a name="reading-data-point-reads-and-queries"></a>데이터 읽기: 지점 읽기 및 쿼리

Azure Cosmos DB의 읽기 작업은 다음과 같이 일반적으로 RU 사용 측면에서 가장 빠르고 효율이 높은 순서부터 가장 느리고 효율이 덜한 순서로 정렬됩니다.  

* 지점 읽기(단일 항목 ID 및 파티션 키에 대한 키/값 조회).
* 단일 파티션 키 내에 필터 절이 있는 쿼리
* 속성에 대해 같음 또는 범위 필터 절이 없는 쿼리
* 필터가 없는 쿼리

### <a name="role-of-the-consistency-level"></a>일관성 수준의 역할

**강력** 또는 **제한된 부실** [일관성 수준](consistency-levels.md) 중 하나를 사용하는 경우, 읽기 작업(지점 읽기 또는 쿼리)의 RU 비용은 두 배가 됩니다.

### <a name="point-reads"></a>지점 읽기

지점 읽기의 RU 요금에 영향을 주는 유일한 요인은(사용된 일관성 수준 외) 검색된 항목 크기입니다. 다음 표에서는 1KB 및 100KB 크기의 항목에 대한 RU 비용을 표시합니다.

| **항목 크기** | **지점 한 개의 읽기 비용** |
| --- | --- |
| 1KB | 1RU |
| 100KB | 10RU |

지점 읽기(항목 ID의 키/값 조회)는 가장 효율적인 읽기이므로, 가능한 경우 항목 ID에 의미 있는 값이 있는지 확인하여(쿼리 대신) 지점 읽기가 포함된 항목을 가져올 수 있도록 합니다.

### <a name="queries"></a>쿼리

쿼리의 요청 단위는 여러 가지 요인에 따라 달라집니다. 예를 들어 로드/반환된 Azure Cosmos 항목 수, 인덱스에 대한 조회 수, 쿼리 컴파일 시간 등의 세부 정보가 여기에 해당합니다. Azure Cosmos DB는 동일한 데이터에 대해 실행될 경우 동일한 쿼리가 항상 반복 실행에서 동일한 요청 단위 수를 사용하도록 보장합니다. 쿼리 실행 메트릭을 사용하는 쿼리 프로필은 요청 단위가 소비되는 적절한 방식을 제시합니다.  

일부 경우에 페이징된 쿼리 실행에서 200개 및 429개의 응답 시퀀스와 가변 요청 단위를 볼 수 있습니다. 쿼리가 사용 가능한 RU에 따라 가능한 한 빠르게 실행되기 때문입니다. 쿼리 실행이 서버와 클라이언트 간에 여러 페이지/왕복으로 구분되는 것을 볼 수도 있습니다. 예를 들어, 10,000개 항목이 여러 페이지로 반환될 수 있으며, 해당 페이지에 대해 계산 기준으로 요금이 부과됩니다. 이러한 페이지에서 합계를 계산하는 경우 전체 쿼리의 경우와 동일한 수의 RU를 가져와야 합니다.

#### <a name="metrics-for-troubleshooting-queries"></a>쿼리 문제 해결을 위한 메트릭

쿼리별 사용 성능 및 사용 처리량(사용자 정의 함수 포함)은 주로 함수 본문에 따라 달라집니다. UDF에서 쿼리 실행에 사용되는 시간 및 소비되는 RU 수를 확인하는 가장 쉬운 방법은 쿼리 메트릭을 사용하도록 설정하는 것입니다. .NET SDK를 사용하는 경우 SDK에서 반환되는 샘플 쿼리 메트릭은 다음과 같습니다.

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>쿼리 최적화 비용에 대한 모범 사례 

비용에 대한 쿼리를 최적화하는 경우 다음 모범 사례를 고려합니다.

* **여러 엔터티 형식을 공동으로 배치**

   단일 컨테이너 또는 소수의 컨테이너 내에 여러 엔터티 형식을 공동으로 배치합니다. 이 방법은 가격 책정 관점에서 뿐만 아니라 쿼리 실행 및 트랜잭션 측면에서도 이점이 있습니다. 쿼리의 범위는 단일 컨테이너로 지정되고, 저장 프로시저/트리거를 통해 여러 레코드에서 수행되는 원자성 트랜잭션의 범위는 단일 컨테이너 내의 파티션 키로 지정됩니다. 같은 컨테이너 내에서 엔터티를 공동으로 배치하면 레코드 간 관계를 해석하는 데 필요한 네트워크 왕복 수를 줄일 수 있습니다. 이를 통해 엔드투엔드 성능이 향상되고, 더 큰 데이터 세트의 여러 레코드에 대해 원자성 트랜잭션이 활성화되며 결과적으로 비용이 절감됩니다. 일반적으로 기존 애플리케이션을 마이그레이션하며 코드를 변경하고 싶지 않기 때문에 단일 컨테이너 또는 더 작은 수의 컨테이너에 여러 엔터티 형식을 공동으로 배치하는 것이 어려우면 데이터베이스 수준에서 처리량을 프로비전하는 것이 좋습니다.  

* **낮은 요청 단위/초 사용량 측정 및 튜닝**

   쿼리의 복잡성은 작업에 사용되는 RU(요청 단위)의 수에 영향을 줍니다. 조건자의 수, 조건자의 특성, UDF 수 및 원본 데이터 세트의 크기 등의 모든 요소는 쿼리 작업의 비용에 영향을 줍니다. 

Azure Cosmos DB는 프로비저닝된 처리량 모델을 사용하여 처리량 및 대기 시간 측면에서 예측 가능한 성능을 제공합니다. 프로비전된 처리량은 초당 [요청 단위](request-units.md) 또는 RU/초로 표시됩니다. RU(요청 단위)는 CPU, 메모리, IO 등과 같이 요청을 수행하는 데 필요한 컴퓨팅 리소스에 대한 논리적 추상화입니다. 프로비전된 처리량(RU)은 따로 구분되며, 예측 가능한 처리량 및 대기 시간을 제공하기 위해 컨테이너 또는 데이터베이스에 전용으로 사용됩니다. 프로비전된 처리량을 사용하면 Azure Cosmos DB는 규모에 관계없이, 예측 가능하고 일관된 성능, 보장된 낮은 대기 시간 및 고가용성을 제공할 수 있습니다. 요청 단위는 애플리케이션에 필요한 리소스의 수를 보다 간편하게 파악할 수 있도록 하는 정규화된 통화를 나타냅니다.

요청 헤더에 반환된 요청 비용은 지정된 쿼리의 비용을 나타냅니다. 예를 들어, 쿼리가 1000개의 1KB 항목을 반환하는 경우 작업 비용은 1000입니다. 따라서 1초 이내에 서버는 후속 요청의 속도를 제한하기 전에 이러한 두 가지 요청만 인식합니다. 자세한 내용은 [요청 단위](request-units.md) 문서와 요청 단위 계산기를 참조하세요.

## <a name="writing-data"></a>데이터 쓰기

항목 작성에 드는 RU 비용은 다음에 따라 다릅니다.

- 항목 크기.
- [인덱싱 정책](index-policy.md)이 적용되며 인덱싱되는 데 필요한 속성 수입니다.

약 5.5 RU를 기준으로 인덱싱 비용 없이 1KB 항목을 삽입합니다. 항목 교체 비용은 동일한 항목을 삽입할 때 요금의 두 배에 달합니다.

### <a name="optimizing-writes"></a>쓰기 최적화

쓰기 작업의 RU 비용을 최적화하는 최고의 방법은 항목 크기와 인덱싱되는 속성 수를 오른쪽으로 조정하는 것입니다.

- 매우 큰 항목을 Azure Cosmos DB에 저장하면 높은 수준의 RU 비용이 발생하며 이를 방지 패턴으로 간주할 수 있습니다. 특히 쿼리하지 않아도 되는 이진 콘텐츠나 청크 텍스트를 저장하지 않도록 합니다. 모범 사례는 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 관련 종류의 데이터를 저장하고 Azure Cosmos DB에 쓰는 항목에 blob에 대한 참조(또는 링크)를 저장하는 것입니다.
- 쿼리를 통해 필터링되는 속성만 인덱싱하는 인덱싱 정책 최적화는 쓰기 작업에서 사용하는 RU에서 큰 차이를 만들 수 있습니다. 새 컨테이너를 만들 때 기본 인덱싱 정책은 항목에 있는 모든 속성 및 각 속성을 인덱싱합니다. 이는 개발 작업에서는 바람직한 기본값이지만, 프로덕션으로 전환하거나 워크로드에서 상당한 트래픽을 수신하게 될 경우, 재평가와 함께 [인덱싱 정책을 사용자 지정](how-to-manage-indexing-policy.md)하는 것을 권장합니다.

데이터 대량 수집을 수행하는 경우, 관련 작업의 RU 사용을 최적화하도록 설계된 [Azure Cosmos DB 대량 실행기 라이브러리](bulk-executor-overview.md)를 사용하는 것을 권장합니다. 필요에 따라, 동일한 라이브러리를 기반으로 하는 [Azure Data Factory](../data-factory/introduction.md)를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
* [Azure Cosmos DB 예약 용량](cosmos-db-reserved-capacity.md)에 대한 자세한 정보
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
