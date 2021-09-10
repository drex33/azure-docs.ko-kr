---
title: Azure Cosmos DB에서 변경 피드 디자인 패턴
description: 일반적인 변경 피드 디자인 패턴 개요
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: df2836bf201ffcceed4c2b6b1bf9a767bc046f4d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116480"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Azure Cosmos DB에서 변경 피드 디자인 패턴
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB 변경 피드를 통해 대량의 쓰기가 있는 큰 데이터 세트를 효율적으로 처리할 수 있습니다. 변경 피드는 또한 변경된 내용을 식별하기 위해 전체 데이터 세트를 쿼리하는 대안을 제공합니다. 이 문서에서는 일반적인 변경 피드 디자인 패턴, 디자인 장단점 및 변경 피드 제한 사항에 중점을 두고 있습니다.

Azure Cosmos DB는 IoT, 게임, 소매 및 운영 로깅 애플리케이션에 적합합니다. 이러한 애플리케이션에서 일반적인 설계 패턴은 데이터 변경 사항을 사용하여 추가 작업을 트리거하는 것입니다. 추가 작업의 예제는 다음을 포함합니다.

* 항목이 삽입 또는 업데이트되는 경우 알림 또는 API에 대한 호출 트리거
* IoT에 대한 실시간 스트림 처리 또는 운영 데이터에 대한 실시간 분석 처리
* 데이터 이동(예: 캐시, 검색 엔진, 데이터 웨어하우스 또는 콜드 스토리지와 동기화)

Azure Cosmos DB의 변경 피드를 사용하면 다음 그림과 같이 이러한 패턴 각각에 대해 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

:::image type="content" source="../media/change-feed/changefeedoverview.png" alt-text="Azure Cosmos DB 변경 피드를 사용하여 실시간 분석 및 이벤트 기반 컴퓨팅 시나리오 작동" border="false":::

## <a name="event-computing-and-notifications"></a>이벤트 컴퓨팅 및 알림

Azure Cosmos DB 변경 피드는 알림을 트리거하거나 특정 이벤트를 기반으로 API에 대한 호출을 전송해야 하는 시나리오를 간소화할 수 있습니다. [변경 피드 프로세스 라이브러리](change-feed-processor.md)를 사용하여 자동으로 컨테이너에 변경 내용을 폴링하고 쓰기 또는 업데이트가 있을 때마다 외부 API를 호출할 수 있습니다.

특정 조건에 따라 알림을 선택적으로 트리거하거나 API에 대한 호출을 보낼 수도 있습니다. 예를 들어 [Azure Functions](change-feed-functions.md)를 사용하여 변경 피드에서 읽는 경우 특정 조건을 충족할 때에만 알림을 보내도록 논리를 함수에 삽입할 수 있습니다. Azure Function 코드는 각 쓰기 및 업데이트 중에 실행되지만 특정 조건이 충족된 경우에만 알림이 전송됩니다.

## <a name="real-time-stream-processing"></a>실시간 스트림 처리

Azure Cosmos DB 변경 피드는 IoT에 대한 실시간 스트림 처리 또는 운영 데이터에 대한 실시간 분석 처리에 사용할 수 있습니다.
예를 들어 디바이스, 센서, 인프라 및 애플리케이션에서 이벤트 데이터를 수신하고 저장하며, [Spark](../../hdinsight/spark/apache-spark-overview.md)를 사용하여 이러한 이벤트를 실시간으로 처리합니다. 다름 이미지에서는 변경 피드를 통해 Azure Cosmos DB를 사용하여 람다 아키텍처를 구현하는 방법을 보여줍니다.

:::image type="content" source="../media/change-feed/lambda.png" alt-text="수집 및 쿼리를 위한 Azure Cosmos DB 기반 lambda 파이프라인" border="false":::

스트림 처리 구현에서 먼저 많은 양의 들어오는 데이터를 Azure Event Hub 또는 Apache Kafka와 같은 임시 메시지 큐로 받는 경우가 많습니다. 변경 피드는 Azure Cosmos DB의 기능으로 인해 짧은 읽기 및 쓰기 대기 시간을 보장하여 지속적인 높은 비율의 데이터 수집을 지원하는 좋은 대안입니다. 메시지 큐에 대한 Azure Cosmos DB 변경 피드의 이점은 다음과 같습니다.

### <a name="data-persistence"></a>데이터 지속성

Azure Cosmos DB에 기록된 데이터는 변경 피드에 표시되고 삭제될 때까지 보존됩니다. 일반적으로 메시지 큐에는 최대 보존 기간이 있습니다. 예를 들어 [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)는 최대 90일간 데이터를 보존합니다.

### <a name="querying-ability"></a>쿼리 기능

Cosmos 컨테이너의 변경 피드에서 읽는 것 외에 Azure Cosmos DB에 저장된 데이터에 대해 SQL 쿼리를 실행할 수도 있습니다. 변경 피드는 이미 컨테이너에 있는 데이터의 중복이 아닌 데이터를 읽는 다른 메커니즘입니다. 따라서 변경 피드에서 데이터를 읽으면 항상 동일한 Azure Cosmos DB 컨테이너의 쿼리와 일치하게 됩니다.

### <a name="high-availability"></a>고가용성

Azure Cosmos DB는 최대 99.999% 읽기 및 쓰기 가용성을 제공합니다. 많은 메시지 큐와 달리 Azure Cosmos DB 데이터는 [RTO(복구 시간 목표)](../consistency-levels.md#rto)를 0으로 하여 쉽게 전역적으로 배포 및 구성할 수 있습니다.

변경 피드의 항목을 처리한 후 구체화된 뷰를 작성하고 집계된 값을 Azure Cosmos DB에 다시 유지할 수 있습니다. 예를 들어, Azure Cosmos DB를 사용하여 게임을 빌드하는 경우 변경 피드를 사용하여 완료된 게임의 점수에 따라 실시간 순위표를 구현할 수 있습니다.

## <a name="data-movement"></a>데이터 이동

실시간 데이터 이동에 대한 변경 피드를 읽을 수도 있습니다.

예를 들어 변경 피드를 사용하여 다음 작업을 효율적으로 수행할 수 있습니다.

* Azure Cosmos DB에 저장된 데이터를 사용하여 캐시, 검색 인덱스 또는 데이터 웨어하우스를 업데이트합니다.

* 다른 논리 파티션 키를 사용하여 다른 Azure Cosmos 계정 또는 다른 Azure Cosmos 컨테이너에 대한 중단 시간이 없는 마이그레이션을 수행합니다.

* 애플리케이션 수준 데이터 계층화 및 보관을 구현합니다. 예를 들어 Azure Cosmos DB에 "핫 데이터"를 저장하고 만료된 "콜드 데이터"를 다른 스토리지 시스템(예: [Azure Blob Storage](../../storage/common/storage-introduction.md))으로 내보낼 수 있습니다.

[파티션 및 컨테이너 간에 데이터를 비정규화](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)해야 하는 경우 이 데이터 복제의 원본으로 컨테이너의 변경 피드를 읽을 수 있습니다. 변경 피드를 사용한 실시간 데이터 복제는 최종 일관성만 보장할 수 있습니다. Cosmos 컨테이너의 변경 내용을 처리할 때 [변경 피드 프로세서 지연 시간을 모니터링](how-to-use-change-feed-estimator.md)할 수 있습니다.

## <a name="event-sourcing"></a>이벤트 소싱

[이벤트 소싱 패턴](/azure/architecture/patterns/event-sourcing)에는 추가 전용 저장소를 사용하여 해당 데이터에 대한 전체 작업을 기록하는 작업이 포함됩니다. Azure Cosmos DB의 변경 피드는 모든 데이터 수집이 쓰기(업데이트 또는 삭제 없음)로 모델링되는 이벤트 소싱 아키텍처의 중앙 데이터 저장소로 선택하는 것이 좋습니다. 이 경우 Azure Cosmos DB에 대한 각 쓰기는 "이벤트"이며, 사용자는 변경 피드의 과거 이벤트에 대한 전체 레코드를 갖게 됩니다. 중앙 이벤트 저장소에서 게시한 이벤트는 일반적으로 구체화된 뷰를 유지 관리하고 외부 시스템과 통합하는 데 사용됩니다. 변경 피드의 보존에 대한 시간 제한이 없기 때문에 Cosmos 컨테이너의 변경 피드의 시작 부분에서 읽어 이전 이벤트를 모두 재생할 수 있습니다.

[여러 변경 피드 소비자가 동일한 컨테이너의 변경 피드를 구독](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)하도록 할 수 있습니다. [임대 컨테이너](change-feed-processor.md#components-of-the-change-feed-processor)의 프로비저닝된 처리량 외에도 변경 피드를 활용하는 비용이 없습니다. 변경 피드는 사용 여부에 관계없이 모든 컨테이너에서 사용할 수 있습니다.

Azure Cosmos DB는 가로 확장성 및 고가용성의 장점 때문에 이벤트 소싱 패턴에서 뛰어난 중앙의 추가 전용 영구 데이터 저장소입니다. 또한 변경 피드 프로세서 라이브러리는 ["한 번 이상"](change-feed-processor.md#error-handling) 보증을 제공하여 이벤트 처리를 놓치지 않도록 합니다.

## <a name="current-limitations"></a>현재 제한 사항

변경 피드에는 이해해야 하는 중요한 제한 사항이 있습니다. Cosmos 컨테이너의 항목은 항상 변경 피드에 남아 있지만 변경 피드는 전체 작업 로그가 아닙니다. 변경 피드를 활용하는 애플리케이션을 디자인할 때 고려해야 할 중요한 영역이 있습니다.

### <a name="intermediate-updates"></a>중간 업데이트

지정된 항목에 대한 가장 최근의 변경 내용만 변경 피드에 포함됩니다. 변경 내용을 처리할 때 사용 가능한 최신 항목 버전을 참조하세요. 짧은 시간 동안 동일한 항목에 대한 업데이트가 여러 개 있는 경우 중간 업데이트 처리를 놓칠 수 있습니다. 업데이트를 추적하고 항목에 대한 이전 업데이트를 재생할 수 있도록 하려면 대신 이러한 업데이트를 일련의 쓰기로 모델링하는 것이 좋습니다.

### <a name="deletes"></a>Deletes

변경 피드는 삭제를 캡처하지 않습니다. 컨테이너에서 항목을 삭제하면 변경 피드에서도 제거됩니다. 이를 처리하는 가장 일반적인 방법은 삭제할 항목에 소프트 마커를 추가하는 것입니다. "삭제됨"이라는 속성을 추가하고 삭제 시 "true"로 설정할 수 있습니다. 이 문서 업데이트는 변경 피드에 표시됩니다. 나중에 자동으로 삭제할 수 있도록 이 항목에 대한 TTL을 설정할 수 있습니다.

### <a name="guaranteed-order"></a>보장된 순서

변경 피드의 순서는 임의 파티션 키 값 내에서 보장됩니다(여러 파티션 키 값을 걸치면 안 됨). 의미 있는 순서를 보증하는 파티션 키를 선택해야 합니다.

예를 들어 이벤트 소싱 디자인 패턴을 사용하는 소매 애플리케이션이 있다고 가정합니다. 이 애플리케이션에서는 Azure Cosmos DB에 대한 쓰기로 모델링되는 각 "이벤트"가 서로 다른 사용자 동작입니다. 몇 가지 이벤트 예가 다음 순서로 발생했다고 생각해 보세요.

1. 고객이 쇼핑 카트에 항목 A를 추가합니다.
2. 고객이 쇼핑 카트에 항목 B를 추가합니다.
3. 고객이 쇼핑 카트에서 항목 A를 제거합니다.
4. 고객이 체크 아웃하고 쇼핑 카트 콘텐츠가 배송됩니다.

현재 쇼핑 카트 콘텐츠의 구체화된 뷰는 각 고객에 대해 유지 관리됩니다. 이 애플리케이션은 이러한 이벤트를 발생 순서대로 처리해야 합니다. 예를 들어 항목 A가 제거되기 전에 카트 체크 아웃을 처리하면 고객이 원하는 항목 B가 아니라 항목 A가 배송될 수 있습니다. 이러한 4개의 이벤트가 발생 순서대로 처리되도록 보장하기 위해 동일한 파티션 키 값 내에 포함해야 합니다. **사용자 이름**(각 고객은 고유한 사용자 이름을 가짐)을 파티션 키로 선택하면 이러한 이벤트가 Azure Cosmos DB에 기록된 순서와 동일하게 변경 피드에 표시되도록 보장할 수 있습니다.

## <a name="examples"></a>예

Microsoft docs에서 제공되는 샘플 범위 이상으로 확장되는 실제 변경 피드 코드의 예는 다음과 같습니다.

- [변경 피드 소개](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [변경 피드 중심의 IoT 사용 사례](https://github.com/AzureCosmosDB/scenario-based-labs)
- [변경 피드 중심의 소매 사용 사례](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>다음 단계

* [변경 피드 개요](../change-feed.md)
* [변경 피드를 읽는 옵션](read-change-feed.md)
* [Azure Functions로 변경 피드 사용](change-feed-functions.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB Capacity Planner를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.