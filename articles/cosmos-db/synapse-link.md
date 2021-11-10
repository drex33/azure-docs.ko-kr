---
title: Microsoft Azure Cosmos DB용 Synapse Link 소개, 이점 및 사용 시기
description: Microsoft Azure Cosmos DB용 Synapse Link에 대해 알아봅니다. Synapse Link를 사용하면 Microsoft Azure Cosmos DB의 작동 데이터에 Azure Synapse Analytics를 사용하여 근 실시간 분석(HTAP)을 실행할 수 있습니다.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 459aedbda8ea42fb0ee0990fb1074373efc38acc
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133927"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Microsoft Azure Cosmos DB용 Synapse Link란?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Microsoft Azure Cosmos DB용 Synapse Link는 클라우드 네이티브 HTAP(하이브리드 트랜잭션 및 분석 처리) 기능으로, 이를 통해 Microsoft Azure Cosmos DB의 작동 데이터에 대해 근 실시간 분석을 실행할 수 있습니다. Azure Synapse Link를 통해 Microsoft Azure Cosmos DB와 Azure Synapse Analytics가 긴밀하게 통합됩니다.

Azure Synapse Link는 완전 격리된 열 저장소인 [ Microsoft Azure Cosmos DB 분석 저장소](analytical-store-introduction.md)를 사용하여 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)에서 대규모 작동 데이터에 대해 ETL(추출, 변환 및 로드) 분석을 수행할 수 없습니다. 비즈니스 분석가, 데이터 엔지니어 및 데이터 과학자는 이제 Synapse Spark나 Synapse SQL을 혼용하여 근 실시간 비즈니스 인텔리전스, 분석 및 기계 학습 파이프라인을 실행할 수 있습니다. Microsoft Azure Cosmos DB의 트랜잭션 워크로드 성능에 영향을 주지 않고 이를 달성할 수 있습니다.

다음 이미지에서는 Microsoft Azure Cosmos DB 및 Azure Synapse Analytics와의 Azure Synapse Link 통합을 보여줍니다. 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Microsoft Azure Cosmos DB와 Azure Synapse Analytics 통합을 위한 아키텍처 다이어그램" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> 이점

중요 업무용 트랜잭션 워크로드의 성능에 미치는 영향을 최소화하면서 대규모 작동 데이터 세트를 분석하기 위해 일반적으로 ETL 파이프라인에서 Microsoft Azure Cosmos DB의 작동 데이터를 추출하고 처리합니다. ETL 파이프라인에는 많은 레이어의 데이터 이동이 필요합니다. 이로 인해 작업이 많이 복잡해지고 트랜잭션 워크로드 성능에 영향이 있습니다. 또한 원본 시간에서 작동 데이터 분석을 위한 대기 시간이 늘어납니다.

기존 ETL 기반 솔루션과 비교하여 Microsoft Azure Cosmos DB용 Azure Synapse Link는 다음과 같은 몇 가지 이점을 제공합니다.  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>관리할 ETL 작업이 없어 복잡성 감소

Azure Synapse Link를 사용하면 복잡한 데이터 이동 없이 Azure Synapse Analytics를 사용하여 Microsoft Azure Cosmos DB 분석 저장소에 직접 액세스할 수 있습니다. 작동 데이터의 모든 업데이트는 ETL 또는 변경 피드 작업 없이 거의 실시간으로 분석 저장소에 표시됩니다. 추가 데이터 변환 없이 Azure Synapse Analytics에서 분석 저장소에 대해 대규모 분석을 실행할 수 있습니다.

### <a name="near-real-time-insights-into-your-operational-data"></a>작동 데이터에 대한 근 실시간 인사이트

이제 Azure Synapse Link를 사용하여 거의 실시간으로 작동 데이터에 대한 풍부한 인사이트를 얻을 수 있습니다. ETL 기반 시스템은 작동 데이터 추출, 변환 및 로드에 필요한 많은 레이어로 인해 작동 데이터 분석을 위한 대기 시간이 더 긴 경향이 있습니다. Azure Synapse Analytics와 Microsoft Azure Cosmos DB 분석 저장소의 네이티브 통합을 통해 거의 실시간으로 작동 데이터를 분석하여 새로운 비즈니스 시나리오를 지원할 수 있습니다. 

### <a name="no-impact-on-operational-workloads"></a>운영 워크로드에 영향이 없음

Azure Synapse Link를 사용하면 Microsoft Azure Cosmos DB 분석 저장소 (별도의 열 저장소)에 대해 분석 쿼리를 실행할 수 있지만 트랜잭션 작업은 트랜잭션 워크로드(행 기반 트랜잭션 저장소)에 대한 프로비저닝된 처리량을 사용하여 처리됩니다.  분석 워크로드는 작동 데이터에 대해 프로비저닝된 처리량을 소비하지 않고 트랜잭션 워크로드 트래픽과 독립적으로 처리됩니다.

### <a name="optimized-for-large-scale-analytics-workloads"></a>대규모 분석 워크로드에 최적화됨

Microsoft Azure Cosmos DB 분석 저장소는 컴퓨팅 실행 시간에 종속되지 않고 분석 워크로드에 확장성, 탄력성 및 성능을 제공하도록 최적화되었습니다. 스토리지 기술은 분석 워크로드를 최적화하기 위해 자체 관리됩니다. Azure Synapse Analytics에 대한 기본 제공 지원을 통해 이 스토리지 레이어에 액세스하면 간편하고 성능이 향상됩니다.

### <a name="cost-effective"></a>비용 효율성

Azure Synapse Link를 통해 운영 분석을 위한 비용에 최적화된 완전 관리형 솔루션을 사용할 수 있습니다. 이 솔루션은 작동 데이터를 분석하기 위해 기존 ETL 파이프라인에 필요한 추가 스토리지 및 컴퓨팅 추가 레이어를 없앱니다. 

Microsoft Azure Cosmos DB 분석 저장소는 실행된 데이터 스토리지 및 분석 읽기/쓰기 작업과 쿼리를 기반으로 하는 소비 기반 가격 책정 모델을 따릅니다. 오늘날의 트랜잭션 워크로드와 마찬가지로 처리량을 프로비저닝할 필요가 없습니다. Azure Synapse Analytics의 매우 탄력적인 컴퓨팅 엔진으로 데이터에 액세스하면 스토리지와 컴퓨팅을 실행하는 데 드는 전체 비용을 매우 효율적으로 관리할 수 있습니다.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>로컬로 사용할 수 있고, 전역적으로 분산된 다중 지역 쓰기에 대한 분석

Microsoft Azure Cosmos DB에서 데이터의 가장 가까운 지역 복사본을 대상으로 분석 쿼리를 효과적으로 실행할 수 있습니다. Microsoft Azure Cosmos DB는 활성-활성 방식으로 트랜잭션 워크로드와 함께 전역적으로 분산된 분석 워크 로드를 실행하는 최신 기능을 제공합니다.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>작동 데이터에 HTAP 시나리오 사용

Synapse Link는 Azure Synapse 분석 런타임 지원과 Microsoft Azure Cosmos DB 분석 저장소를 통합합니다. 이러한 통합을 통해 대규모 데이터 세트에서 작동 데이터에 대한 실시간 업데이트를 기반으로 인사이트를 생성하는 클라우드 네이티브 HTAP(하이브리드 트랜잭션/분석 처리) 솔루션을 구축할 수 있습니다. 이는 실시간 추세를 기반으로 경고를 발생시키고, 근 실시간 대시보드를 구축하고, 사용자 동작을 기반으로 한 비즈니스 환경을 제공하는 새로운 비즈니스 시나리오를 지원합니다.

### <a name="azure-cosmos-db-analytical-store"></a>Microsoft Azure Cosmos DB 분석 저장소

Microsoft Azure Cosmos DB 분석 저장소는 열을 기반으로 Microsoft Azure Cosmos DB의 작동 데이터를 표현한 것입니다. 이 분석 저장소는 데이터를 복사하지 않고 트랜잭션 워크로드의 성능에 영향을 주지 않으면서 대규모 작동 데이터 세트에 대한 빠르고 비용 효율적인 쿼리에 적합합니다.

분석 저장소는 Microsoft Azure Cosmos DB의 완전 관리형 기능("자동 동기화")으로 트랜잭션 워크로드에서 잦은 삽입, 업데이트 및 삭제를 거의 실시간으로 자동 선택합니다. 변경 피드 또는 ETL이 필요하지 않습니다. 

전역적으로 분산된 Microsoft Azure Cosmos DB 계정이 있는 경우 컨테이너에 분석 저장소를 사용하도록 설정하면 해당 계정의 모든 지역에서 이 계정을 사용할 수 있습니다. 분석 저장소에 대한 자세한 내용은 [Microsoft Azure Cosmos DB 분석 저장소 개요](analytical-store-introduction.md) 문서를 참조하세요.

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Azure Synapse Analytics와 통합

이제 Synapse Link를 사용하여 Azure Synapse Analytics에서 Microsoft Azure Cosmos DB 컨테이너에 직접 연결하고 별도의 커넥터 없이 분석 저장소에 액세스할 수 있습니다. Azure Synapse Analytics는 현재 [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) 및 [서버리스 SQL 풀](../synapse-analytics/sql/on-demand-workspace-overview.md)과 함께 Synapse Link를 지원합니다.

Azure Synapse Analytics에서 지원하는 여러 분석 런타임에서 interop를 사용하여 Microsoft Azure Cosmos DB 분석 저장소의 데이터를 동시에 쿼리할 수 ​​있습니다. 작동 데이터 분석을 위한 추가 데이터 변환이 필요하지 않습니다. 다음을 사용하여 분석 저장소 데이터를 쿼리하고 분석할 수 있습니다.

* Scala, Python, SparkSQL 및 C#을 완벽하게 지원하는 Synapse Apache Spark. Synapse Spark는 데이터 엔지니어링 및 데이터 과학 시나리오의 중심입니다.

* T-SQL 언어 및 익숙한 BI 도구(예: Power BI Premium 등) 지원이 포함된 사용하는 서버리스 SQL 풀

> [!NOTE]
> Azure Synapse Analytics에서 Microsoft Azure Cosmos DB 컨테이너의 분석 및 트랜잭션 저장소에 모두 액세스할 수 있습니다. 그러나 작동 데이터에 대한 대규모 분석이나 검사를 실행하려는 경우 분석 저장소를 사용하여 트랜잭션 워크로드에 대한 성능 영향을 방지하는 것이 좋습니다.

> [!NOTE]
> Microsoft Azure Cosmos DB 컨테이너를 해당 지역의 Synapse 런타임에 연결하여 Azure 지역에서 짧은 대기 시간으로 분석을 실행할 수 있습니다.

이러한 통합을 통해 여러 사용자에 대해 다음과 같은 HTAP 시나리오를 사용할 수 있습니다.

* Power BI 보고서를 모델링하고 게시하여 Synapse SQL을 통해 Azure Cosmos DB의 실시간 운영 데이터에 직접 액세스하려는 BI 엔지니어.

* Synapse SQL로 쿼리하여 Microsoft Azure Cosmos DB 컨테이너의 작동 데이터에서 인사이트를 얻고, 대규모로 데이터를 읽고, 이러한 결과를 다른 데이터 원본과 결합하려는 데이터 분석가

* Synapse Spark를 사용하여 모델을 개선하고 복잡한 데이터 엔지니어링을 수행하지 않고 해당 모델을 학습하는 기능을 원하는 데이터 과학자. Spark Synapse를 통해 데이터에 대한 실시간 채점을 위해 모델 사후 유추 결과를 Microsoft Azure Cosmos DB에 쓸 수도 있습니다.

* 수동 ETL 프로세스 없이 Microsoft Azure Cosmos DB 컨테이너를 통해 SQL 또는 Spark 테이블을 만들어 소비자가 데이터에 액세스 할 수 있도록 하려는 데이터 엔지니어

Microsoft Azure Cosmos DB에 대한 Azure Synapse Analytics 런타임 지원에 대한 자세한 내용 [Cosmos DB에 대한 Azure Synapse Analytics 지원](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)을 참조하세요.

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Microsoft Azure Cosmos DB용 Synapse Link 사용 시기

Synapse Link는 다음과 같은 경우에 권장됩니다.

* Microsoft Azure Cosmos DB 고객이며 작동 데이터에 대한 분석, BI 및 기계 학습을 실행하려는 경우. 이러한 경우 Synapse Link는 트랜잭션 저장소의 프로비저닝된 처리량에 영향을 주지 않고 보다 긴밀하게 통합된 분석 환경을 제공합니다. 다음은 그 예입니다.

  * 현재 별도의 커넥터를 사용하여 Microsoft Azure Cosmos DB 작동 데이터에 대해 직접 분석 또는 BI를 실행하는 경우 또는

  * ETL 프로세스를 실행하여 별도의 분석 시스템으로 작동 데이터를 추출하는 경우
 
이러한 경우 Synapse Link는 트랜잭션 저장소의 프로비저닝된 처리량에 영향을 주지 않고 보다 긴밀하게 통합된 분석 환경을 제공합니다.

높은 동시성, 워크로드 관리 및 여러 데이터 원본의 집계 지속성과 같은 기존 데이터 웨어하우스 요구 사항을 찾고 있다면 Synapse Link는 권장되지 않습니다. 자세한 내용은 [Microsoft Azure Cosmos DB용 Azure Synapse Link로 강화할 수 있는 일반적인 시나리오](synapse-link-use-cases.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

* Azure Cosmos DB용 Azure Synapse Link는 SQL API 및 Azure Cosmos DB API for MongoDB에 대해 지원됩니다. Gremlin API, Cassandra API 및 Table API에 대해서는 지원되지 않습니다.

* Synapse Link SQL API 및 MongoDB API 계정 모두에 대해 새 컨테이너에서 사용하도록 설정할 수 있지만 기존 컨테이너는 SQL API에 대해서만 지원됩니다.

* 분석 저장소에서 데이터의 백업 및 복원은 현재 지원되지 않습니다. 이 제한은 주기적 백업 모드와 연속 백업 모드 모두에 적용되며 Cosmos DB 트랜잭션 저장소 데이터에 영향을 미치지 않습니다.

* 동일한 데이터베이스 계정에서 Synapse Link 및 주기적 백업 모드가 공존할 수 있습니다. 분석 저장소에서 데이터의 백업 및 복원은 지원되지 않지만

* 동일한 데이터베이스 계정에서 Synapse Link 및 연속 백업 모드가 공존하는 것은 지원되지 않습니다.

* Azure Synapse Dedicated SQL 풀을 사용하여 Azure Cosmos DB 분석 저장소에 액세스하는 것은 현재 지원되지 않습니다.

* Azure Synapse 링크 및 주기적 백업 모드는 동일한 데이터베이스 계정에 공존할 수 있습니다. 그러나 분석 저장소 데이터는 백업 및 복원에 포함되지 않습니다. Synapse Link를 사용하도록 설정하면 Azure Cosmos DB는 예약된 백업 간격에 따라 컨테이너의 트랜잭션 저장소에 있는 데이터를 자동으로 계속 백업합니다.


## <a name="security"></a>보안

Synapse Link를 사용하면 Azure Cosmos DB에서 중요 업무용 데이터를 근 실시간으로 분석할 수 있습니다. 중요 비즈니스 데이터가 트랜잭션 및 분석 저장소에 모두 안전하게 저장되도록 하는 것이 중요합니다. Azure Cosmos DB에 대한 Azure Synapse Link는 다음 기능을 통해 해당 보안 요구 사항을 충족하는 데 도움이 되도록 설계되었습니다.

* **프라이빗 엔드포인트를 사용한 네트워크 격리** - 트랜잭션 및 분석 저장소에 있는 데이터에 대한 네트워크 액세스를 독립적으로 제어할 수 있습니다. 네트워크 격리는 Azure Synapse 작업 영역의 관리형 가상 네트워크 내에서 각 저장소마다 별도의 관리형 프라이빗 엔드포인트를 사용하여 수행됩니다. 자세히 알아보려면 [분석 저장소에 대한 프라이빗 엔드포인트 구성](analytical-store-private-endpoints.md) 방법에 대한 문서를 참조하세요.

* **고객 관리형 키를 통한 데이터 암호화** - 자동화되고 투명한 방식으로 동일한 고객 관리형 키를 사용하여 트랜잭션 및 분석 저장소에서 데이터를 원활하게 암호화할 수 있습니다. Azure Synapse Link는 Azure Cosmos DB 계정의 관리 ID를 사용하여 고객 관리형 키 구성만 지원합니다. 계정에서 Azure Synapse Link](configure-synapse-link.md#enable-synapse-link)를 활성화하기 전에 Azure Key Vault 액세스 정책에서 계정의 관리 ID를 구성해야 합니다. 자세한 내용은 [Azure Cosmos DB 계정의 관리 ID를 사용하여 고객 관리형 키 구성](how-to-setup-cmk.md#using-managed-identity) 문서를 참조하세요.

* **보안 키 관리** - Synapse Analytics 및 Synapse 서버리스 SQL 풀에서 분석 저장소에 있는 데이터에 액세스하려면 Synapse Analytics 작업 영역 내의 Azure Cosmos DB 키를 관리해야 합니다. Spark 작업 또는 SQL 스크립트에서 Azure Cosmos DB 계정 키를 인라인으로 사용하는 대신 Azure Synapse Link에서 더 안전한 기능을 제공합니다.

  * Synapse 서버리스 SQL 풀을 사용하는 경우 계정 키를 저장하고 `OPENROWSET` 함수에서 참조하는 SQL 자격 증명을 미리 만들어 Azure Cosmos DB 분석 저장소를 쿼리할 수 있습니다. 자세히 알아보려면 [Synapse Analytics Link에서 서버리스 SQL 풀로 쿼리](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) 문서를 참조하세요.

  * Synapse Spark를 사용하는 경우 Azure Cosmos DB 데이터베이스를 가리키는 연결된 서비스 개체에 계정 키를 저장하고 런타임에 Spark 구성에서 이를 참조할 수 있습니다. 자세히 알아보려면 [Apache Spark를 사용하여 전용 SQL 풀로 데이터 복사](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) 문서를 참조하세요.


## <a name="pricing"></a>가격 책정

Azure Synapse Link의 청구 모델은 Microsoft Azure Cosmos DB 분석 저장소와 Synapse 런타임을 사용하여 발생하는 비용을 포함합니다. 자세한 내용은 [Microsoft Azure Cosmos DB 분석 저장소 가격 책정](analytical-store-introduction.md#analytical-store-pricing)과 [Azure Synapse Analytics 가격 책정](https://azure.microsoft.com/pricing/details/synapse-analytics/) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요

* [Microsoft Azure Cosmos DB 분석 저장소 개요](analytical-store-introduction.md)

* [Azure Synapse Analytics를 사용하여 하이브리드 트랜잭션 및 분석 처리를 디자인](/learn/modules/design-hybrid-transactional-analytical-processing-using-azure-synapse-analytics/)하는 방법에 대한 학습 모듈을 확인하세요.

* [Microsoft Azure Cosmos DB용 Azure Synapse Link 시작하기](configure-synapse-link.md)
 
* [Azure Synapse Analytics 런타임에서 지원되는 기능](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.yml)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link 사용 사례](synapse-link-use-cases.md)
