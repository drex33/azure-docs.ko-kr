---
title: Synapse SQL 아키텍처
description: Azure Synapse SQL이 분산 쿼리 처리 기능을 Azure Storage와 결합하여 고성능 및 확장성을 달성하는 방법을 알아봅니다.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: e279aea4bdf0ae3bc18c2ece1556d7ad8483a2de
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217110"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL 아키텍처 

이 문서에서는 Synapse SQL의 아키텍처 구성 요소에 대해 설명합니다.

## <a name="synapse-sql-architecture-components"></a>Synapse SQL 아키텍처 구성 요소

Synapse SQL은 규모 확장 아키텍처를 활용하여 여러 노드에 걸쳐 데이터의 계산 처리를 분산합니다. 시스템 데이터와 독립적으로 컴퓨팅을 확장할 수 있도록 컴퓨팅이 스토리지에서 분리됩니다. 

전용 SQL 풀의 경우 규모 단위는 [데이터 웨어하우스 단위](resource-consumption-models.md)로 알려진 컴퓨팅 능력의 추상화입니다. 

서버리스 SQL 풀의 경우 서버리스이므로 쿼리 리소스 요구 사항을 수용하기 위해 자동으로 크기 조정이 수행됩니다. 토폴로지는 시간이 지남에 따라 노드 또는 장애 조치(failover)를 추가, 제거하면서 변화하므로 이러한 변화에 맞게 조정되며 쿼리가 충분한 리소스를 확보하여 성공적으로 완료되도록 합니다. 예를 들어 아래 이미지에서는 4개의 컴퓨팅 노드를 활용하여 쿼리를 실행하는 서버리스 SQL 풀을 보여 줍니다.

![Synapse SQL 아키텍처](./media//overview-architecture/sql-architecture.png)

Synapse SQL은 노드 기반 아키텍처를 사용합니다. 애플리케이션은 Synapse SQL의 단일 입력 지점인 제어 노드에 연결하고 T-SQL 명령을 보냅니다. 

Azure Synapse SQL 제어 노드는 분산 쿼리 엔진을 활용하여 병렬 처리를 위해 쿼리를 최적화한 다음 연산을 컴퓨팅 노드에 전달하여 병렬로 처리되도록 합니다. 

서버리스 SQL 풀 제어 노드는 DQP(분산 쿼리 처리) 엔진을 활용하여 사용자 쿼리를 컴퓨팅 노드에서 실행되는 더 작은 쿼리로 분할한 다음 쿼리 분산 실행을 최적화하고 조정합니다. 작은 쿼리를 각각 태스크라고 하며 분산 실행 단위를 나타냅니다. 태스크는 스토리지에서 파일을 읽고, 다른 태스크의 결과를 조인하고, 다른 태스크에서 검색된 데이터를 그룹화 또는 정렬합니다. 

컴퓨팅 노드는 모든 사용자 데이터를 Azure Storage에 저장하고 병렬 쿼리를 실행합니다. DMS(Data Movement Service)는 쿼리를 병렬로 실행하고 정확한 결과를 반환하기 위해 필요할 때 노드에서 데이터를 이동시키는 시스템 수준의 내부 서비스입니다. 

스토리지와 컴퓨팅을 분리되면 Synapse SQL을 사용할 때 스토리지 요구 사항에 관계없이 컴퓨팅 능력을 독립적으로 크기 조정하는 이점을 활용할 수 있습니다. 서버리스 SQL 풀 확장의 경우 자동으로 수행되는 반면 전용 SQL 풀의 경우 다음을 수행할 수 있습니다.

* 데이터 이동 없이 전용 SQL 풀 내에서 컴퓨팅 성능을 늘리거나 줄입니다.
* 데이터를 그대로 둔 채 컴퓨팅 용량을 일시 중지하여 스토리지 비용만 지불합니다.
* 운영 시간 동안 컴퓨팅 용량을 다시 시작합니다.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL은 Azure Storage를 활용하여 사용자 데이터를 안전하게 유지합니다. 데이터가 Azure Storage에 의해 저장되고 관리되므로 스토리지 사용에 대한 별도 요금이 부과됩니다. 

서버리스 SQL 풀을 사용하면 데이터 레이크 파일을 쿼리할 수 있지만, 전용 SQL 풀을 사용하면 데이터 레이크 파일에서 데이터를 쿼리하고 수집할 수 있습니다. 전용 SQL 풀로 수집된 데이터는 시스템 성능을 최적화하기 위해 **분산** 으로 분할됩니다. 테이블을 정의할 때 데이터 분산에 사용할 분할 패턴을 선택할 수 있습니다. 다음과 같은 분할 패턴이 지원됩니다.

* Hash
* 라운드 로빈
* 복제

## <a name="control-node"></a>제어 노드

제어 노드는 아키텍처의 두뇌입니다. 모든 애플리케이션 및 연결과 상호 작용하는 프런트 엔드입니다. 

Synapse SQL에서 분산 쿼리 엔진은 제어 노드에서 실행되어 병렬 쿼리를 최적화하고 조정합니다. 전용 SQL 풀에 T-SQL 쿼리를 제출하면 제어 노드는 이것을 각 분산에 대해 병렬로 실행되는 쿼리로 변환합니다.

서버리스 SQL 풀에서 DQP 엔진은 제어 노드에서 실행되어 사용자 쿼리를 컴퓨팅 노드에서 실행되는 더 작은 쿼리로 분할한 다음 쿼리 분산 실행을 최적화하고 조정합니다. 또한 각 노드에서 처리할 파일 집합을 할당합니다.

## <a name="compute-nodes"></a>컴퓨팅 노드

컴퓨팅 노드는 컴퓨팅 성능을 제공합니다. 

전용 SQL 풀에서 분산은 처리를 위해 컴퓨팅 노드로 매핑됩니다. 비용을 지불하는 컴퓨팅 리소스가 많을수록 풀은 사용 가능한 컴퓨팅 노드에 분산을 다시 매핑합니다. 컴퓨팅 노드의 수는 1~60 사이이며 전용 SQL 풀의 서비스 수준에 따라 결정됩니다. 각 컴퓨팅 노드에는 시스템 뷰에 표시되는 노드 ID가 있습니다. 시스템 뷰에서 이름이 sys.pdw_nodes로 시작하는 node_id 열을 검색하여 Compute 노드 ID를 볼 수 있습니다. 이러한 시스템 뷰 목록은 [Synapse SQL 시스템 뷰](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

서버리스 SQL 풀에서는 각 컴퓨팅 노드에 태스크를 할당하고 태스크를 실행할 파일 집합을 할당합니다. 태스크는 분산 쿼리 실행 단위로서, 실제로는 사용자가 제출한 쿼리의 일부입니다. 자동 크기 조정은 사용자 쿼리를 실행하는 데 충분한 컴퓨팅 노드가 활용되도록 하기 위함입니다.

## <a name="data-movement-service"></a>데이터 이동 서비스

DMS(데이터 이동 서비스)는 컴퓨팅 노드 간의 데이터 이동을 조정하는 전용 SQL 풀의 데이터 전송 기술입니다. 일부 쿼리는 병렬 쿼리가 정확한 결과를 반환하도록 하려면 데이터 이동이 필요합니다. 데이터 이동이 필요할 때 DMS는 올바른 데이터가 올바른 위치에 도달하도록 보장합니다.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>배포

분산은 전용 SQL 풀의 분산 데이터에 실행되는 병렬 쿼리의 스토리지 및 처리의 기본 단위입니다. 전용 SQL 풀이 쿼리를 실행하면 병렬로 실행되는 60개의 작은 쿼리로 작업이 나뉩니다. 

60개의 작은 쿼리는 각각 데이터 분산 중 하나에서 실행됩니다. 각 컴퓨팅 노드는 60개 중 하나 이상의 분산을 관리합니다. 컴퓨팅 리소스가 최대인 전용 SQL 풀은 컴퓨팅 노드당 하나의 분산이 있습니다. 컴퓨팅 리소스가 최소인 전용 SQL 풀은 하나의 컴퓨팅 노드에 모든 분산이 포함됩니다. 

## <a name="hash-distributed-tables"></a>해시 분산 테이블
해시 분산 테이블을 이용하면 대형 테이블의 조인 및 집계 시 쿼리 성능 극대화할 수 있습니다. 

데이터를 해시 분산 테이블로 분할하기 위해 전용 SQL 풀은 해시 함수를 사용하여 각 행을 결정적으로 하나의 분산에 할당합니다. 테이블 정의에서 열 중 하나는 분산 열로 지정됩니다. 해시 함수는 분산 열의 값을 사용하여 각 행을 분산에 할당합니다.

다음은 전체(분산되지 않은 테이블)가 해시 분산 테이블로 저장되는 방식을 보여 주는 다이어그램입니다. 

![분산 테이블](media//overview-architecture/hash-distributed-table.png "분산 테이블") 

* 각 행은 하나의 분산에 속합니다. 
* 결정적 해시 알고리즘은 각 행을 하나의 분산에 할당합니다. 
* 분산당 테이블 행의 수는 테이블 크기에 따라 달라집니다.

분산 열을 선택할 때 고유성, 데이터 기울이기, 시스템에서 실행되는 쿼리 종류 등 성능에 대해 고려할 사항이 있습니다.

## <a name="round-robin-distributed-tables"></a>라운드 로빈 분산 테이블

라운드 로빈 테이블은 만들기 가장 간단한 테이블이며 로드를 위한 준비 테이블로 사용될 때 빠른 성능을 제공합니다.

라운드 로빈 분산 테이블은 데이터를 테이블 전체에 고르게 분산하지만 최적화는 하지 않습니다. 먼저 분산이 무작위로 선택되고 행 버퍼가 순차적으로 분산에 할당됩니다. 데이터는 로드는 라운드 로빈 테이블이 빠르지만 쿼리 성능은 해시 분산 테이블이 더 뛰어난 경우가 많습니다. 라운드 로빈 테이블의 조인에는 데이터 다시 섞기가 필요하며 여기에는 추가 시간이 소요됩니다.

## <a name="replicated-tables"></a>복제된 테이블
복제된 테이블은 작은 테이블에 가장 빠른 쿼리 성능을 제공합니다.

복제된 테이블은 각 컴퓨팅 노드에 테이블의 전체 복사본을 캐시합니다. 따라서 테이블을 복제하면 조인 또는 집계 전에 Compute 노드 간에 데이터를 전송하지 않아도 됩니다. 복제된 테이블은 작은 테이블에서 가장 잘 활용됩니다. 추가 스토리지가 필요하며, 데이터를 쓸 때 발생하는 추가 오버 헤드로 인해 대용량 테이블에는 비실용적입니다. 

아래 다이어그램은 각 컴퓨팅 노드의 첫 번째 분산에 캐시된 복제 테이블을 보여 줍니다. 

![복제 테이블](media/overview-architecture/replicated-table.png "복제 테이블") 

## <a name="next-steps"></a>다음 단계

이제 Synapse SQL에 대해 어느 정도 알게 되었으므로 빠르게 [전용 SQL 풀을 만들고](../quickstart-create-sql-pool-portal.md) [샘플 데이터를 로드](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)하는 방법을 알아보세요. 또는 [서버리스 SQL 풀 사용](../quickstart-sql-on-demand.md)을 시작합니다. Azure을 처음 접하는 경우 새 용어를 발견하면 [Azure 용어집](../../azure-glossary-cloud-terminology.md) 을 유용하게 사용할 수 있습니다. 
