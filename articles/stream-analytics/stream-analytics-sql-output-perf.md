---
title: Azure Stream Analytics에서 Azure SQL Database에 대한 처리량 성능 향상
description: Azure Stream Analytics에서 SQL Azure로 데이터를 출력하여 쓰기 처리량 속도를 높이는 방법을 알아봅니다.
author: chetanmsft
ms.author: chetang
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 8baa33c8d9622ff76db04345f5c6c465f026e261
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020233"
---
# <a name="increase-throughput-performance-to-azure-sql-database-from-azure-stream-analytics"></a>Azure Stream Analytics에서 Azure SQL Database에 대한 처리량 성능 향상

이 문서에서는 Azure Stream Analytics를 사용하여 Azure SQL Database에 데이터를 로드할 때 쓰기 처리량 성능을 향상하기 위한 팁에 대해 설명합니다.

Azure Stream Analytics의 SQL 출력에서는 병렬 쓰기를 옵션으로 지원합니다. 이 옵션을 사용하면 여러 출력 파티션이 대상 테이블에 병렬로 쓰는 [완전한 병렬](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) 작업 토폴로지가 가능합니다. 그러나 처리량은 데이터베이스 구성 및 테이블 스키마에 따라 크게 달라지므로 Azure Stream Analytics에서 이 옵션을 사용하도록 설정하는 것은 처리량을 높이는 데 충분하지 않을 수 있습니다. 선택하는 인덱스, 클러스터링 키, 인덱스 채우기 계수 및 압축은 테이블 로드 시간에 영향을 줍니다. 데이터베이스를 최적화하여 내부 벤치마크를 기준으로 쿼리 및 부하 성능을 개선하는 방법에 대한 자세한 내용은 [SQL Database 성능 지침](../azure-sql/database/performance-guidance.md)을 참조하세요. SQL Database에 병렬로 쓸 때는 쓰기 순서가 보장되지 않습니다.

다음은 솔루션의 전체 처리량을 개선하는 데 도움이 되는 각 서비스의 구성입니다.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **분할 상속** – 이 SQL 출력 구성 옵션은 이전 쿼리 단계 또는 입력의 파티션 구성표를 상속할 수 있습니다. 이 옵션을 사용하면 디스크 기반 테이블에 데이터를 쓰고 작업에 [완전한 병렬](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) 토폴로지를 사용할 때 처리량이 향상될 것으로 예상할 수 있습니다. 이 분할은 이미 여러 다른 [출력](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs)에서 자동으로 발생합니다. 이 옵션을 사용한 대량 삽입에도 테이블 잠금(TABLOCK)이 비활성화됩니다.

> [!NOTE] 
> 입력 파티션이 8개보다 많은 경우 입력 파티션 구성표를 상속하는 것이 적합하지 않을 수 있습니다. 이 상한값은 ID 열이 하나이고 클러스터형 인덱스가 있는 테이블에서 관찰되었습니다. 이 경우 쿼리에서 [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8을 사용하여 출력 작성기 수를 명시적으로 지정하는 것이 좋습니다. 스키마 및 선택하는 인덱스에 따라 관찰 결과가 달라질 수 있습니다.

- **일괄 처리 크기** - SQL 출력 구성을 사용하면 대상 테이블/워크로드의 특성에 따라 Azure Stream Analytics SQL 출력의 최대 일괄 처리 크기를 지정할 수 있습니다. 일괄 처리 크기는 모든 대량 삽입 트랜잭션에서 전송된 최대 레코드 수입니다. 클러스터형 columnstore 인덱스에서 약 [100K](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)의 일괄 처리 크기를 사용하면 더 많은 병렬 처리, 최소 로깅 및 잠금 최적화가 가능합니다. 디스크 기반 테이블에서는 일괄 처리 크기가 크면 대량 삽입하는 동안 잠금 에스컬레이션이 트리거될 수 있으므로 솔루션에 적합한 크기는 10K(기본값) 이하입니다.

- **입력 메시지 튜닝** - 분할 상속 및 일괄 처리 크기를 사용하여 최적화한 경우 메시지/파티션당 입력 이벤트 수를 늘리면 쓰기 처리량을 추가로 높일 수 있습니다. 입력 메시지 튜닝을 사용하면 Azure Stream Analytics의 일괄 처리 크기를 지정된 일괄 처리 크기까지 늘릴 수 있으며, 따라서 처리량을 높일 수 있습니다. [압축](stream-analytics-define-inputs.md)을 사용하거나 EventHub 또는 Blob에서 입력 메시지 크기를 늘리면 됩니다.

## <a name="sql-azure"></a>SQL Azure

- **분할된 테이블 및 인덱스** – 파티션 키와 동일한 열(예: PartitionId)이 있는 테이블에서 [분할된](/sql/relational-databases/partitions/partitioned-tables-and-indexes) SQL 테이블 및 분할된 인덱스를 사용하면 데이터를 쓰는 동안 파티션 간 경합을 대폭 줄일 수 있습니다. 분할된 테이블의 경우 주 파일 그룹에 [파티션 함수](/sql/t-sql/statements/create-partition-function-transact-sql) 및 [파티션 구성표](/sql/t-sql/statements/create-partition-scheme-transact-sql)를 만들어야 합니다. 이렇게 하면 새 데이터가 로드되는 동안 기존 데이터의 가용성도 향상됩니다. 파티션 수에 따라 로그 IO 제한에 도달할 수 있으며, 파티션 수는 SKU를 업그레이드하여 늘릴 수 있습니다.

- **고유 키 위반 방지** – Azure Stream Analytics 활동 로그에서 [다중 키 위반 경고 메시지](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)를 받은 경우 작업이 복구 과정에서 발생하기 쉬운 고유한 제약 조건 위반의 영향을 받지 않게 해야 합니다. 이렇게 하려면 인덱스에서 [IGNORE\_DUP\_KEY](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) 옵션을 설정하면 됩니다.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory 및 메모리 내 테이블

- **임시 테이블인 메모리 내 테이블** – [메모리 내 테이블](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)을 사용하면 데이터를 매우 빠르게 로드할 수 있지만 데이터가 메모리에 맞아야 합니다. 벤치마크 결과, 메모리 내 테이블에서 디스크 기반 테이블로 대량 로드하는 방법이 단일 작성기를 사용하여 ID 열과 클러스터형 인덱스가 있는 디스크 기반 테이블에 직접 대량 삽입하는 방법보다 약 10배 빠른 것으로 나타났습니다. 이 대량 삽입 성능을 활용하려면 [Azure Data Factory를 사용하여 메모리 내 테이블에서 디스크 기반 테이블로 데이터를 복사하는 복사 작업](../data-factory/connector-azure-sql-database.md)을 설정하세요.

## <a name="avoiding-performance-pitfalls"></a>성능 문제 방지
데이터 대량 삽입은 데이터를 전송하고, insert 문을 구문 분석하고, 문을 실행하고, 트랜잭션 레코드를 발급하는 반복적인 오버헤드를 방지하므로 단일 삽입으로 데이터를 로드하는 것보다 훨씬 빠릅니다. 대신 더 효율적인 경로를 스토리지 엔진에 사용하여 데이터를 스트리밍합니다. 그러나 이 경로의 설정 비용은 디스크 기반 테이블의 단일 insert 문에 비해 훨씬 높습니다. 일반적으로 균형 지점은 약 100개 행이며, 이보다 크면 대량 로드가 거의 항상 더 효율적입니다. 

들어오는 이벤트 속도가 낮으면 100개 행보다 낮은 일괄 처리 크기를 쉽게 만들 수 있습니다. 이렇게 하면 일괄 처리 삽입이 비효율적으로 수행되고 디스크 공간을 너무 많이 사용합니다. 이 제한 사항을 해결하기 위해 다음 작업 중 하나를 수행할 수 있습니다.
* 모든 행에 단순 삽입을 사용하려면 INSTEAD OF [트리거](/sql/t-sql/statements/create-trigger-transact-sql)를 만듭니다.
* 이전 섹션에서 설명한 대로 메모리 내 임시 테이블을 사용합니다.

이와 같은 또 다른 시나리오는 NCCI(비클러스터형 columnstore 인덱스)에 쓸 때 발생합니다. 여기서 더 작은 대량 삽입으로 인해 너무 많은 세그먼트를 생성하여 인덱스에 크래시가 발생할 수 있습니다. 이 경우 클러스터형 columnstore 인덱스를 대신 사용하는 것이 좋습니다.

## <a name="summary"></a>요약

요약하자면, SQL 출력을 위한 Azure Stream Analytics의 분할된 출력 기능을 사용하면 SQL Azure의 분할된 테이블로 작업을 병렬 처리하여 처리량을 대폭 향상할 수 있습니다. Azure Data Factory를 활용하여 메모리 내 테이블에서 디스크 기반 테이블로 데이터 이동을 오케스트레이션하면 처리량을 엄청나게 높일 수 있습니다. 가능한 경우 메시지 밀도를 높이는 것도 전체 처리량을 높이는 중요한 요소가 될 수 있습니다.
