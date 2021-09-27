---
title: Azure Cosmos DB에서 API 선택
description: 워크로드 요구 사항에 따라 Azure Cosmos DB에서 SQL/Core, MongoDB, Cassandra, Gremlin 및 table API 중에서 선택하는 방법을 알아봅니다.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.openlocfilehash: 6d13f5f79da2906d1b994d4ab83e06d0593eaefe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586646"
---
# <a name="choose-an-api-in-azure-cosmos-db"></a>Azure Cosmos DB에서 API 선택
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 최신 앱 개발을 위한 완전 관리형 NoSQL 데이터베이스입니다. Azure Cosmos DB는 자동 관리, 업데이트 및 패치를 통해 데이터베이스 관리를 직접 수행할 수 있습니다. 또한 용량과 비용을 일치시키기 위해 애플리케이션 요구 사항에 대응하는 비용 효율적인 서버리스 및 자동 확장 옵션으로 용량 관리를 처리합니다.

## <a name="apis-in-azure-cosmos-db"></a>Azure Cosmos DB의 API

Azure Cosmos DB는 Core(SQL) API, API for MongoDB, Cassandra API, Gremlin API 및 Table API를 비롯한 여러 데이터베이스 API를 제공합니다. 이러한 API에서 문서, 키-값, 그래프 및 열 패밀리 데이터 모델을 사용하여 실제 데이터를 모델링할 수 있습니다. 이러한 API를 통해 애플리케이션은 관리 오버헤드 및 스케일링 접근 방식을 사용하지 않고도 여러 다른 데이터베이스 기술처럼 Azure Cosmos DB를 처리할 수 있습니다. 이러한 API를 사용하면 Azure Cosmos DB에서 데이터 모델링 및 쿼리를 위해 이미 보유하고 있는 에코시스템, 도구 및 기술을 사용할 수 있습니다.

모든 API는 스토리지 및 처리량의 자동 스케일링, 유연성을 제공하고 성능을 보장합니다. 최선의 API가 하나만 있는 것이 아니며 애플리케이션을 빌드하기 위해 API 중 하나를 선택할 수 있습니다. 이 문서는 워크로드 및 팀 요구 사항을 기준으로 API를 선택하는 데 도움이 됩니다.

## <a name="considerations-when-choosing-an-api"></a>API 선택 시 고려 사항

Core(SQL) API는 Azure Cosmos DB에 기본적으로 제공됩니다.

MongoDB, Cassandra, Gremlin 및 Table용 API는 오픈 소스 데이터베이스 엔진의 유선 프로토콜을 구현합니다. 이러한 API는 다음 조건에 해당하는 경우에 가장 적합합니다.

* 기존 MongoDB, Cassandra 또는 Gremlin 애플리케이션이 있는 경우
* 전체 데이터 액세스 계층을 다시 작성하지 않으려는 경우
* 데이터베이스에 대한 오픈 소스 개발자 에코시스템, 클라이언트 드라이버, 전문 지식 및 리소스를 사용하려는 경우
* 전역 배포, 스토리지 및 처리량의 탄력적 스케일링, 성능, 짧은 대기 시간, 트랜잭션 및 분석 워크로드를 실행하는 기능, 완전 관리형 플랫폼을 사용하려는 경우
* 다중 클라우드 환경에서 현대화된 앱을 개발하려는 경우

이러한 API를 사용하여 새 애플리케이션을 빌드하거나 기존 데이터를 마이그레이션할 수 있습니다. 마이그레이션된 앱을 실행하려면 애플리케이션의 연결 문자열을 변경하고 이전처럼 계속 실행합니다. 기존 앱을 마이그레이션할 때는 이러한 API의 기능 지원을 평가해야 합니다.

워크로드에 따라 요구 사항에 맞는 API를 선택해야 합니다. 다음 이미지는 새 앱을 빌드하거나 기존 앱을 Azure Cosmos DB로 마이그레이션할 때 적절한 API를 선택하는 방법에 대한 순서도를 보여 줍니다.

:::image type="content" source="./media/choose-api/choose-api-decision-tree.png" alt-text="Azure Cosmos DB에서 API를 선택하는 의사 결정 트리" lightbox="./media/choose-api/choose-api-decision-tree.png":::

## <a name="coresql-api"></a>Core(SQL) API

이 API는 데이터를 문서 형식으로 저장합니다. 인터페이스, 서비스 및 SDK 클라이언트 라이브러리를 완전히 제어할 수 있으므로 최상의 종단 간 환경을 제공합니다. Azure Cosmos DB에 롤아웃되는 새로운 기능은 SQL API 계정에서 처음 사용할 수 있습니다. Azure Cosmos DB SQL API 계정은 JSON 개체를 쿼리할 수 있는 가장 친숙하고 인기 있는 쿼리 언어 중 하나인 SQL(구조적 쿼리 언어) 구문을 사용하여 항목을 쿼리할 수 있도록 지원합니다. 자세한 내용은 [Azure Cosmos DB SQL API](/learn/modules/intro-to-azure-cosmos-db-core-api/) 학습 모듈 및 [SQL 쿼리 시작하기](sql-query-getting-started.md) 문서를 참조하세요.

Oracle, DynamoDB, HBase 등의 다른 데이터베이스에서 마이그레이션하려고 하며 최신 기술을 사용하여 앱을 빌드하려면 SQL API가 권장되는 옵션입니다. SQL API는 분석을 지원하고 운영 및 분석 워크로드 간에 성능을 격리할 수 있도록 합니다.

### <a name="capacity-planning-for-migration-to-api-for-mongodb"></a>MongoDB용 API로 마이그레이션하기 위한 용량 계획

기존 데이터베이스 클러스터에서 Azure Cosmos DB SQL API로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 분할 된 및 복제 된 데이터베이스 클러스터의 vcore 및 서버 수를 알고 있는 경우 [vcore 또는 vcore를 사용 하 여 요청 단위를 추정 하](convert-vcore-to-request-unit.md)는 방법을 참조 하세요.
* 현재 데이터베이스 작업에 대 한 일반적인 요청 요금을 알고 있는 경우 [Azure Cosmos DB capacity planner를 사용 하 여 요청 단위](estimate-ru-with-capacity-planner.md)를 계산 하는 방법을 참조 하세요.

## <a name="api-for-mongodb"></a>MongoDB용 API

이 API는 BSON 형식을 통해 문서 구조에 데이터를 저장합니다. MongoDB 유선 프로토콜과 호환되지만 네이티브 MongoDB 관련 코드를 사용하지는 않습니다. 이 API는 스케일링, 고가용성, 지역에서 복제, 여러 쓰기 위치, 자동 및 투명한 분할된 데이터베이스 관리, 운영 및 분석 저장소 간의 투명한 복제 등과 같은 Azure Cosmos DB의 기능을 계속 사용하면서 광범위한 MongoDB 에코시스템 및 기술을 사용하려는 경우에 적합합니다.

연결 문자열을 변경하여 MongoDB용 API에서 기존 MongoDB 앱을 사용할 수 있습니다. Mongodump 및 mongorestore와 같은 네이티브 MongoDB 도구를 사용하거나 Azure Database Migration 도구를 사용하여 기존 데이터를 이동할 수 있습니다. MongoDB 셸, [MongoDB Compass](mongodb/connect-using-compass.md) 및 [Robo3T](mongodb/connect-using-robomongo.md)와 같은 도구는 쿼리를 실행하고 네이티브 MongoDB에서와 같이 데이터 작업을 수행할 수 있습니다.

MongoDB용 API는 4.0, 3.6 및 3.2 MongoDB 서버 버전과 호환됩니다. 서버 버전 4.0은 최상의 성능 및 전체 기능 지원을 제공하므로 권장됩니다. 자세한 내용은 [MongoDB용 API](mongodb/mongodb-introduction.md) 문서를 참조하세요.

### <a name="capacity-planning-for-migration-to-api-for-mongodb"></a>MongoDB용 API로 마이그레이션하기 위한 용량 계획

기존 데이터베이스 클러스터에서 Azure Cosmos DB API for MongoDB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vcore 및 서버 수를 알고 있는 경우 [vcore 또는 vcore를 사용 하 여 요청 단위를 추정 하](convert-vcore-to-request-unit.md)는 방법을 참조 하세요.
* 현재 데이터베이스 작업에 대 한 일반적인 요청 요금을 알고 있는 경우 [Azure Cosmos DB capacity planner를 사용 하 여 요청 단위](./mongodb/estimate-ru-capacity-planner.md)를 계산 하는 방법을 참조 하세요.

## <a name="cassandra-api"></a>Cassandra API

이 API는 열 기반 스키마에 데이터를 저장합니다. Apache Cassandra는 열 기반 스키마에 대한 유연한 접근 방식을 제공하면서 대량의 데이터를 저장하는 고도로 분산된 수평 스케일링 방법을 제공합니다. Azure Cosmos DB의 Cassandra API는 분산 NoSQL 데이터베이스와 관련해서 이 원칙을 따릅니다. Cassandra API는 Apache Cassandra와 호환되는 유선 프로토콜입니다. Azure Cosmos DB의 탄력성 및 완전히 관리되는 특성과 대부분의 기본 Apache Cassandra 기능, 도구 및 에코시스템을 사용하려는 경우 Cassandra API를 고려하는 것이 좋습니다. 즉, Cassandra API에서는 OS, Java VM, 가비지 수집기, 읽기/쓰기 성능, 노드, 클러스터 등을 관리할 필요가 없음을 의미합니다.

Apache Cassandra 클라이언트 드라이버를 사용하여 Cassandra API에 연결할 수 있습니다. Cassandra API를 사용하면 이미 익숙한 CQL(Cassandra 쿼리 언어), CQL 셸과 같은 도구, Cassandra 클라이언트 드라이버를 사용하여 데이터와 상호 작용할 수 있습니다. Cassandra API는 현재 OLTP 시나리오만 지원합니다. Cassandra API를 사용하여 변경 피드와 같은 Azure Cosmos DB의 고유한 기능을 사용할 수도 있습니다. 자세히 알아보려면 [Cassandra API](cassandra-introduction.md) 문서를 참조하세요.

## <a name="gremlin-api"></a>Gremlin API

이 API에서는 그래프 쿼리를 만들 수 있으며 데이터를 모서리와 꼭짓점으로 저장합니다. 이 API는 동적 데이터, 복잡한 관계가 있는 데이터, 너무 복잡하여 관계형 데이터베이스로 모델링할 수 없는 데이터, 기존 Gremlin 에코시스템 및 기술을 사용하고자 하는 경우에 사용합니다. Azure Cosmos DB의 Gremlin API는 그래프 데이터베이스 알고리즘의 기능을 고도로 스케일링이 가능한 관리형 인프라와 결합합니다. 또한 유연성과 관계형 접근 방식이 결여되어 발생하는 대부분의 일반적인 데이터 문제에 대한 고유하고 유연한 솔루션을 제공합니다. Gremlin API는 현재 OLTP 시나리오만 지원합니다.

Azure Cosmos DB의 Gremlin API는 [Apache TinkerPop](https://tinkerpop.apache.org/) 그래프 컴퓨팅 프레임워크를 기준으로 합니다. Gremlin API는 동일한 Graph 쿼리 언어를 사용하여 데이터를 수집하고 쿼리합니다. Azure Cosmos DB의 파티션 전략을 사용하여 그래프 데이터베이스 엔진에서 읽기/쓰기 작업을 수행합니다. Gremlin API는 오픈 소스 Gremlin은 사용하여 유선 프로토콜을 지원하므로 오픈 소스 Gremlin SDK를 사용하여 애플리케이션을 빌드할 수 있습니다. Azure Cosmos DB Gremlin API는 복잡한 분석 그래프 시나리오의 경우 Apache Spark 및 [GraphFrames](https://github.com/graphframes/graphframes)에서도 작동합니다. 자세히 알아보려면 [Gremlin API](graph-introduction.md) 문서를 참조하세요.

## <a name="table-api"></a>테이블 API

이 API는 키/값 형식으로 데이터를 저장합니다. 현재 Azure Table Storage를 사용하고 있는 경우 대기 시간, 스케일링, 처리량, 글로벌 배포, 인덱스 관리, 낮은 쿼리 성능에 대해 몇 가지 제한 사항이 있을 수 있습니다. Table API는 이러한 제한을 해결하며, Azure Cosmos DB의 이점을 사용하기 위해서는 앱을 마이그레이션하는 것이 좋습니다. Table API는 OLTP 시나리오만 지원합니다.

Azure Table Storage에 대해 작성된 애플리케이션은 코드를 거의 변경하지 않고 Table API로 마이그레이션할 수 있으며 프리미엄 기능을 활용할 수 있습니다. 자세히 알아보려면 [Table API](table/introduction.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB SQL API 시작](create-sql-api-dotnet.md)
* [Azure Cosmos DB의 MongoDB API 시작](mongodb/create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 시작](cassandra/manage-data-dotnet.md)
* [Azure Cosmos DB Gremlin API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB Table API 시작](create-table-dotnet.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
