---
title: Azure Cosmos DB에 대 한 Azure Synapse의 사용자 지정 분할 링크 (미리 보기)
description: 사용자 지정 분할을 사용 하면 분석 쿼리에서 일반적으로 사용 되는 필드의 분석 저장소 데이터를 분할 하 여 쿼리 성능을 향상 시킬 수 있습니다.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72bd4c8a2001cc8e6f314f12862d7ed563ac770b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102732"
---
# <a name="custom-partitioning-in-azure-synapse-link-for-azure-cosmos-db-preview"></a>Azure Cosmos DB에 대 한 Azure Synapse의 사용자 지정 분할 링크 (미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

사용자 지정 분할을 사용 하면 분석 쿼리에서 일반적으로 사용 되는 필드의 분석 저장소 데이터를 분할 하 여 쿼리 성능을 향상 시킬 수 있습니다.

이 문서에서는 분석 워크 로드에 중요 한 키를 사용 하 여 Azure Cosmos DB 분석 저장소에서 데이터를 분할 하는 방법에 대해 설명 합니다. 또한 파티션 정리를 사용 하 여 향상 된 쿼리 성능을 활용 하는 방법을 설명 합니다. 또한 작업의 업데이트 또는 삭제가 많은 경우 분할 된 저장소를 사용 하 여 쿼리 성능을 개선 하는 방법을 배웁니다.

> [!IMPORTANT]
> 사용자 지정 분할 기능은 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!NOTE]
> Azure Cosmos DB 계정은 사용자 지정 분할을 활용 하기 위해 Azure Synapse Link를 사용 하도록 설정 해야 합니다. 사용자 지정 분할은 현재 Azure Synapse Spark 2.0에만 지원 됩니다.

## <a name="how-does-it-work"></a>작동 원리

사용자 지정 분할을 사용 하면 데이터 집합의 단일 필드 또는 필드 조합을 분석 저장소 파티션 키로 선택할 수 있습니다.

분석 저장소 분할은 트랜잭션 저장소의 분할에 독립적입니다. 기본적으로 분석 저장소는 분할 되지 않습니다. 날짜, 시간, 범주 등의 필드를 기준으로 분석 저장소를 자주 쿼리하려면 이러한 키에 따라 분할 된 저장소를 만드는 것이 좋습니다.

분할을 트리거하기 위해 Azure Synapse 링크를 사용 하 여 Azure Synapse Spark 노트북에서 정기적으로 분할 작업을 실행할 수 있습니다. 편리한 일정으로 백그라운드 작업으로 실행 되도록 예약할 수 있습니다.

> [!NOTE]
> 분할 된 저장소는 Azure Synapse 작업 영역에 연결 된 ADLS Gen2 기본 저장소 계정을 가리킵니다.

:::image type="content" source="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" alt-text="Azure Cosmos DB에 대 한 Azure Synapse 링크에서 분할 된 저장소의 아키텍처" lightbox="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" border="false":::

분할 된 저장소는 분할 작업을 실행 한 마지막 타임 스탬프까지 Azure Cosmos DB 분석 데이터를 포함 합니다. Synapse Spark의 파티션 키 필터를 사용 하 여 분석 데이터를 쿼리하면 Synapse Link가 분석 저장소의 최근 데이터를 분할 된 저장소의 데이터와 자동으로 병합 합니다. 이러한 방식으로 최신 결과를 얻을 수 있습니다. 쿼리 하기 전에 데이터를 병합 하지만 델타는 분할 된 저장소에 다시 기록 되지 않습니다. 분석 저장소와 분할 된 저장소의 데이터 간 델타가 확장 되 면 분할 된 데이터에 대 한 쿼리 시간이 달라질 수 있습니다. 분할 작업을 더 자주 트리거하는 경우이 델타를 줄일 수 있습니다. 파티션 작업을 실행할 때마다 전체 데이터 집합 대신 분석 저장소의 증분 변경 내용만 처리 됩니다.

## <a name="when-to-use"></a>사용하는 경우

Azure Cosmos DB에서 분석 데이터를 쿼리할 때 분할 된 저장소 사용은 선택 사항입니다. 기존 분석 저장소와 Synapse Link를 사용 하 여 동일한 데이터를 직접 쿼리할 수 있습니다. 다음 요구 사항을 충족 하는 경우 분할 된 저장소를 켤 수 있습니다.

* 일부 필드에서 필터링 된 분석 데이터를 자주 쿼리 하려고 합니다.

* 대량 업데이트/삭제 작업을 수행 하거나 데이터가 수집. 분할 된 저장소는 파티션 키를 사용 하 여 쿼리 하는지 여부에 관계 없이 이러한 경우에 더 나은 쿼리 성능을 제공 합니다.

위의 워크 로드를 제외 하 고 파티션 키와 다른 쿼리 필터를 사용 하 여 라이브 데이터를 쿼리 하는 경우, 특히 분할 작업이 자주 실행 되지 않는 경우 분석 저장소에서 직접 쿼리 하는 것이 좋습니다.

## <a name="benefits"></a>이점

### <a name="reduced-data-scanning-from-partition-pruning"></a>파티션 정리에서 데이터 검색 축소

분할 된 저장소에서 각 고유 파티션 키에 해당 하는 데이터가 공동 배치 되므로 파티션 키를 쿼리 필터로 사용 하면 쿼리 실행에서 기본 데이터를 정리 하 고 필요한 데이터만 검색할 수 있습니다. 파티션 정리는 제한 된 데이터를 검사 하 여 분석 쿼리 성능을 향상 시킵니다.

### <a name="flexibility-to-partition-your-analytical-data"></a>분석 데이터를 분할할 수 있는 유연성

별도의 파티션 키를 사용 하 여 분석 저장소 데이터를 분할할 수 있는 특정 분석 저장소 컨테이너에 대해 여러 분할 전략을 사용할 수 있습니다. 예를 들어 "store_sales" 컨테이너는 키로 "sold_date"를 사용 하 여 분할할 수 있으며 "item"을 키로 사용 하 여 분할할 수도 있습니다. 이 경우 두 개의 개별 분할 작업이 있어야 합니다 .이 경우 기본적으로 두 개의 분할 된 저장소로 데이터를 분할 합니다. 이 분할 전략은 일부 쿼리에서 쿼리 필터로 "sold_date"을 사용 하 고 일부 다른 쿼리가 "item"을 쿼리 필터로 사용 하는 경우에 유용 합니다.

서로 다른 파티션 키에 있는 데이터는 동일한 분할 된 저장소의 일부가 되며 파티션 키를 기준으로 쿼리하여 해당 데이터를 선택할 수 있습니다.

### <a name="query-performance-improvements"></a>쿼리 성능 향상

파티션 정리의 쿼리 향상 외에도 사용자 지정 분할은 다음과 같은 작업에 대 한 쿼리 성능 향상을 발생 시킵니다.

* **대량 워크 로드 업데이트/삭제** -분석 저장소에서 여러 버전의 레코드를 추적 하 고 각 쿼리 실행 중에 로드 하는 대신 분할 된 저장소에는 최신 버전의 데이터만 포함 됩니다. 이렇게 하면 작업량이 많은 작업을 업데이트/삭제 하는 경우 쿼리 성능이 크게 향상 됩니다.

* **데이터 수집 워크 로드 속도 저하** -분석 데이터를 분할 하는 작업의 경우 데이터 수집 속도가 느려지는 경우 이러한 압축으로 인해 쿼리 성능이 향상 될 수 있습니다.

### <a name="transactional-guarantee"></a>트랜잭션 보장

사용자 지정 분할은 트랜잭션 보장을 완벽 하 게 보장 한다는 점에 유의 해야 합니다. 분할 실행이 진행 중인 동안에는 쿼리 경로가 차단 되지 않습니다. 각 쿼리 실행은 마지막으로 성공한 분할에서 분할 된 데이터를 읽습니다. 분석 저장소에서 최신 데이터를 읽으므로 쿼리는 분할 된 저장소를 사용할 때 항상 최신 데이터를 반환 합니다.

## <a name="security"></a>보안

분석 저장소에 대 한 [관리 되는 개인 끝점](analytical-store-private-endpoints.md) 을 구성한 경우 분할 된 저장소에 대 한 네트워크 격리를 보장 하려면 분할 된 저장소에 대 한 관리 되는 개인 끝점도 추가 하는 것이 좋습니다. 분할 된 저장소는 Synapse 작업 영역에 연결 된 기본 저장소 계정입니다.

마찬가지로, [분석 저장소에서 고객 관리 키](how-to-setup-cmk.md#is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store)를 구성한 경우에는 분할 된 저장소 인 Synapse 작업 영역 기본 저장소 계정에서 직접 사용 하도록 설정 해야 합니다.

## <a name="limitations"></a>제한 사항

* 사용자 지정 분할은 Azure Synapse Spark에만 사용할 수 있습니다. 사용자 지정 분할은 현재 서버 리스 SQL 풀에 대해 지원 되지 않습니다.

* 현재 분할 된 저장소는 Synapse 작업 영역과 연결 된 기본 저장소 계정만 가리킬 수 있습니다. 이때 사용자 지정 저장소 계정 선택은 지원 되지 않습니다.

* MongoDB 용 API는 분석 저장소 및 Synapse 링크를 지원 하지만 현재 사용자 지정 분할을 지원 하지 않습니다.

## <a name="pricing"></a>가격 책정

[Azure Synapse 링크 가격](synapse-link.md#pricing)외에도 사용자 지정 분할을 사용 하는 경우 다음과 같은 요금이 발생 합니다.

* 분석 저장소에서 분할 작업을 실행할 때 Synapse Apache Spark 풀 사용에 대 한 [요금이 청구](https://azure.microsoft.com/pricing/details/synapse-analytics/#pricing) 됩니다.

* 분할 된 데이터는 Azure Synapse Analytics 작업 영역과 연결 된 기본 Azure Data Lake Storage Gen2 계정에 저장 됩니다. ADLS Gen2 저장소 및 트랜잭션을 사용 하는 것과 관련 된 비용이 발생 합니다. 이러한 비용은 분할 된 분석 데이터에 필요한 저장소 및 Synapse의 분석 쿼리에 대해 각각 처리 되는 데이터에 따라 결정 됩니다. 가격 책정에 대 한 자세한 내용은 [Azure Data Lake Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/data-lake/)를 참조 하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-often-should-i-run-the-custom-partitioning-job"></a>사용자 지정 분할 작업을 얼마나 자주 실행 해야 하나요?

증분 데이터 볼륨, 쿼리 대기 시간 요구 사항 등의 몇 가지 요소를 통해 사용자 지정 분할 작업을 실행할 수 있는 빈도를 결정 합니다. 하루에 한 번 또는 몇 시간 마다 한 번 실행 될 수 있습니다. 들어오는 데이터 볼륨이 높고 예상 쿼리 대기 시간이 낮은 경우 분할 작업을 더 자주 예약할 수 있습니다. 또한 파티션 정리를 효과적으로 적용 하기 위해 먼저 분석 저장소에 증분 데이터를 누적 해야 합니다.

### <a name="do-the-query-results-include-latest-data-while-the-partitioning-job-execution-is-in-progress"></a>분할 작업 실행이 진행 중인 동안에는 쿼리 결과에 최신 데이터가 포함 됩니까?

예, 사용자 지정 분할은 전체 트랜잭션 보장을 제공 합니다. 따라서 모든 시점에서 쿼리 결과는 기존 분할 된 데이터를 최신 분석 저장소 데이터 집합을 반환 하는 비상 데이터와 결합 합니다.

### <a name="can-the-custom-partitioning-make-use-of-linked-service-authentication-on-azure-synapse-analytics"></a>사용자 지정 분할을 통해 Azure Synapse Analytics에서 연결 된 서비스 인증을 사용할 수 있나요?

예, 연결 된 서비스 인증을 분석 저장소 분할에 사용할 수 있습니다.

### <a name="can-i-change-the-partition-key-for-a-given-container-at-a-later-point-in-time"></a>이후 시점에서 지정 된 컨테이너에 대 한 파티션 키를 변경할 수 있나요?

예, 지정 된 컨테이너에 대 한 파티션 키를 변경할 수 있으며 새 파티션 키 정의가 새 분할 된 저장소를 만듭니다.

> [!NOTE]
> 파티션 키 정의가 분할 된 저장소 경로의 일부입니다.

### <a name="can-different-partition-keys-point-to-the-same-basepath"></a>서로 다른 파티션 키가 동일한 BasePath를 가리킬 수 있나요?

예, 파티션 키 정의가 분할 된 저장소 경로의 일부 이므로 다른 파티션 키의 경로는 동일한 BasePath에서 분기 됩니다.

기본 경로 형식을 다음과 같이 지정할 수 있습니다./mnt/partitionedstorename/ \<Cosmos_DB_account_name\> / \<Cosmos_DB_database_rid\> / \<Cosmos_DB_container_rid\> /partition = partitionkey/

예:/mnt/CosmosDBPartitionedStore/store_sales/.../partition = sold_date/.../mnt/CosmosDBPartitionedStore/store_sales/.../partition = Date/...

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요

* [사용자 지정 분할을 구성](configure-custom-partitioning.md) 하 여 분석 저장소 데이터를 분할 합니다.
* [Microsoft Azure Cosmos DB용 Azure Synapse Link](synapse-link.md)
* [Microsoft Azure Cosmos DB 분석 저장소 개요](analytical-store-introduction.md)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link 시작하기](configure-synapse-link.md)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.yml)
* [Microsoft Azure Cosmos DB용 Azure Synapse Link 사용 사례](synapse-link-use-cases.md)
