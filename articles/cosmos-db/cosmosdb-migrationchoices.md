---
title: Cosmos DB 마이그레이션 옵션
description: 이 문서에서는 온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 다양한 옵션을 설명합니다.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: d441d12a0c61bfcf6c8e89b51dad0e95e9906e4d
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501726"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 옵션
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

다양한 데이터 원본에서 Azure Cosmos DB로 데이터를 로드할 수 있습니다. Azure Cosmos DB는 여러 API를 지원하기 때문에 대상은 기존 API 중 하나일 수 있습니다. 데이터를 Azure Cosmos DB로 마이그레이션하는 몇 가지 시나리오는 다음과 같습니다.

* 한 Azure Cosmos 컨테이너에서 동일한 데이터베이스 또는 다른 데이터베이스의 또 다른 컨테이너로 데이터를 이동합니다.
* 전용 컨테이너 간 데이터를 공유 데이터베이스 컨테이너로 이동합니다.
* region1에 있는 Azure Cosmos 계정에서 동일한 또는 다른 지역의 또 다른 Azure Cosmos 계정으로 데이터를 이동합니다.
* Azure Blob Storage, JSON 파일, Oracle Database, Couchbase, DynamoDB와 같은 원본에서 Azure Cosmos DB로 데이터를 이동합니다.

다양한 원본에서 다양한 Azure Cosmos DB API에 대한 마이그레이션 경로를 지원하기 위해 각 마이그레이션 경로에 대한 특수화 처리 기능을 제공하는 여러 솔루션이 있습니다. 이 문서에서는 사용 가능한 솔루션을 나열하고 이점 및 제한 사항을 설명합니다.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>마이그레이션 도구 선택에 영향을 주는 요소

다음 요소는 어떤 마이그레이션 도구를 선택할지 결정합니다.

* **온라인 및 오프라인 마이그레이션**: 많은 마이그레이션 도구는 일회성 마이그레이션을 수행하는 경로를 제공합니다. 즉, 데이터베이스에 액세스하는 애플리케이션에 가동 중지 시간이 발생할 수 있습니다. 일부 마이그레이션 솔루션은 원본과 대상 사이에 복제 파이프라인이 설정된 실시간 마이그레이션을 수행하는 방법을 제공합니다.

* **데이터 원본**: 기존 데이터는 Oracle DB2, DataStax Cassandra, Azure SQL Database, PostgreSQL 등과 같은 다양한 데이터 원본에 있을 수 있습니다. 데이터는 기존 Azure Cosmos DB 계정에 있을 수 있으며, 데이터 모델을 변경하거나 다른 파티션 키를 사용하여 컨테이너의 데이터를 다시 분할하기 위해 마이그레이션할 수 있습니다.

* **Azure Cosmos DB API**: Azure Cosmos DB의 SQL API에는 여러 마이그레이션 시나리오를 지원하는 Azure Cosmos DB 팀에서 개발한 여러 도구가 있습니다. 다른 모든 API에는 커뮤니티에서 개발하고 유지 관리하는 고유한 전문 도구 세트가 있습니다. Azure Cosmos DB가 유선 프로토콜 수준에서 이러한 API를 지원하므로 이러한 도구는 데이터를 Azure Cosmos DB로 마이그레이션하는 동안 그대로 작동해야 합니다. 그러나 이 개념은 Azure Cosmos DB에만 해당하므로 제한에 대한 사용자 지정 처리가 필요할 수 있습니다.

* **데이터의 크기**: 대부분의 마이그레이션 도구는 작은 데이터 집합에 대해 매우 효율적으로 작동합니다. 데이터 집합이 수백 기가바이트를 초과하는 경우 마이그레이션 도구의 선택이 제한됩니다. 

* **예상되는 마이그레이션 기간**: 적은 처리량의 마이그레이션을 느린 증분 속도로 수행하도록 구성하거나 대상 Azure Cosmos DB 컨테이너에 프로비저닝된 전체 처리량의 마이그레이션을 더 짧은 시간에 완료할 수 있도록 구성할 수 있습니다.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

용량 계획에 도움이 필요한 경우 [Azure Cosmos DB 용량 플래너를 사용하여 RU/s를 추정하는 가이드](estimate-ru-with-capacity-planner.md)를 참조하세요. 
* vCore 또는 서버 기반 플랫폼에서 마이그레이션하고 요청 단위 예측에 대한 지침이 필요한 경우 [vCore 기반 RU/s를 예측하는 가이드](estimate-ru-with-capacity-planner.md)를 참조하세요.

|마이그레이션 유형|해결 방법|지원되는 원본|지원되는 대상|고려 사항|
|---------|---------|---------|---------|---------|
|오프라인|[데이터 마이그레이션 도구](import-data.md)| &bull;JSON/CSV 파일<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob Storage|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB 테이블 API<br/>&bull;JSON 파일 |&bull; 손쉽게 설정하고 여러 원본을 지원합니다. <br/>&bull; 대량 데이터 세트에 적합하지 않습니다.|
|오프라인|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV 파일<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/> <br/>지원되는 다른 원본에 관해서는 [Azure Data Factory](../data-factory/connector-overview.md) 문서를 참조하세요.|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB<br/>&bull;JSON 파일 <br/><br/> 지원되는 다른 대상에 관해서는 [Azure Data Factory](../data-factory/connector-overview.md) 문서를 참조하세요. |&bull; 손쉽게 설정하고 여러 원본을 지원합니다.<br/>&bull; Azure Cosmos DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull; 대량 데이터 세트에 적합합니다. <br/>&bull; 검사점 없음 - 마이그레이션 과정에서 문제가 발생하는 경우 전체 마이그레이션 프로세스를 다시 시작해야 함을 의미합니다.<br/>&bull; 배달 못한 편지 큐 없음 - 일부 잘못된 파일이 전체 마이그레이션 프로세스를 중지할 수 있음을 의미합니다.|
|오프라인|[Azure Cosmos DB Spark 커넥터](./create-sql-api-spark.md)|Azure Cosmos DB SQL API. <br/><br/>Spark 에코시스템의 추가 커넥터에서 다른 원본을 사용할 수 있습니다.| Azure Cosmos DB SQL API. <br/><br/>Spark 에코시스템의 추가 커넥터에서 다른 대상을 사용할 수 있습니다.| &bull; Azure Cosmos DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull; 대량 데이터 세트에 적합합니다. <br/>&bull; 사용자 지정 Spark 설정이 필요합니다. <br/>&bull; Spark에서는 스키마 불일치가 중요하며 이는 마이그레이션 중에 문제가 될 수 있습니다. |
|오프라인|[Cosmos DB 대량 실행기 라이브러리가 있는 사용자 지정 도구](migrate-cosmosdb-data.md)| 원본은 사용자 지정 코드에 따라 달라집니다. | Azure Cosmos DB SQL API| &bull; 마이그레이션 복원력을 높이는 검사점, 배달하지 못한 편지 기능을 제공합니다. <br/>&bull; 매우 큰 데이터 세트(10TB 이상)에 적합합니다.  <br/>&bull; App Service로 실행되는 이 도구의 사용자 지정 설정이 필요합니다. |
|온라인|[Cosmos DB 함수 + 변경 피드 API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; 손쉽게 설정합니다. <br/>&bull; 원본이 Azure Cosmos DB 컨테이너인 경우에만 작동합니다. <br/>&bull; 대량 데이터 세트에 적합하지 않습니다. <br/>&bull; 원본 컨테이너에서 삭제를 캡처하지 않습니다. |
|온라인|[변경 피드를 사용하는 사용자 지정 마이그레이션 서비스](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; 진행률 추적을 제공합니다. <br/>&bull; 원본이 Azure Cosmos DB 컨테이너인 경우에만 작동합니다. <br/>&bull; 더 큰 데이터 세트에서도 작동합니다.<br/>&bull; 사용자가 변경 피드 프로세서를 호스트하도록 App Service를 설정해야 합니다. <br/>&bull; 원본 컨테이너에서 삭제를 캡처하지 않습니다.|
|온라인|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> 지원되는 다른 원본은 [Striim 웹 사이트](https://www.striim.com/sources-and-targets/)를 참조하세요. |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> 지원되는 다른 대상은 [Striim 웹 사이트](https://www.striim.com/sources-and-targets/)를 참조하세요. | &bull; Oracle, DB2, SQL Server 같은 훨씬 다양한 원본에서 작동합니다.<br/>&bull; ETL 파이프라인을 쉽게 빌드하고 모니터링을 위한 대시보드를 제공합니다. <br/>&bull; 더 큰 데이터 세트를 지원합니다. <br/>&bull; 이는 타사 도구이므로 마켓플레이스에서 구매하고 사용자 환경에 설치해야 합니다.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

[마이그레이션 전 가이드](mongodb/pre-migration-steps.md)에 따라 마이그레이션을 계획합니다. 
* 용량 계획에 도움이 필요한 경우 [Azure Cosmos DB 용량 플래너를 사용하여 RU/s를 추정하는 가이드](estimate-ru-with-capacity-planner.md)를 참조하세요. 
* vCore 또는 서버 기반 플랫폼에서 마이그레이션하고 요청 단위 예측에 대한 지침이 필요한 경우 [vCore 기반 RU/s를 예측하는 가이드](convert-vcore-to-request-unit.md)를 참조하세요.

마이그레이션할 준비가 되면 아래에서 마이그레이션 도구에 대한 자세한 지침을 찾을 수 있습니다.
* [MongoDB 네이티브 도구를 사용한 오프라인 마이그레이션](mongodb/tutorial-mongotools-cosmos-db.md)
* [Azure DMS(데이터베이스 마이그레이션 서비스)를 사용한 오프라인 마이그레이션](../dms/tutorial-mongodb-cosmos-db.md)
* [Azure DMS(데이터베이스 마이그레이션 서비스)를 사용한 온라인 마이그레이션](../dms/tutorial-mongodb-cosmos-db-online.md)
* [Azure Databricks 및 Spark를 사용한 오프라인/온라인 마이그레이션](mongodb/migrate-databricks.md)

그런 다음, [마이그레이션 후 가이드](mongodb/post-migration-optimization.md)에 따라 마이그레이션 후 Azure Cosmos DB 데이터 자산을 최적화합니다.

현재 솔루션에서 Azure Cosmos DB API for MongoDB로의 마이그레이션 경로에 대한 요약은 다음과 같습니다.

|마이그레이션 유형|해결 방법|지원되는 원본|지원되는 대상|고려 사항|
|---------|---------|---------|---------|---------|
|온라인|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|Azure Cosmos DB API for MongoDB |&bull; Azure Cosmos DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull; 대량 데이터 세트에 적합하고 라이브 변경 내용 복제를 처리합니다. <br/>&bull; 다른 MongoDB 원본에서만 작동합니다.|
|오프라인|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| Azure Cosmos DB API for MongoDB| &bull; Azure Cosmos DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull; 대량 데이터 세트에 적합하고 라이브 변경 내용 복제를 처리합니다. <br/>&bull; 다른 MongoDB 원본에서만 작동합니다.|
|오프라인|[Azure Data Factory](../data-factory/connector-azure-cosmos-db-mongodb-api.md)| &bull;JSON/CSV 파일<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Table Storage<br/>&bull;Azure Blob Storage <br/><br/> 지원되는 다른 원본에 관해서는 [Azure Data Factory](../data-factory/connector-overview.md) 문서를 참조하세요. | &bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB API for MongoDB <br/>&bull; JSON 파일 <br/><br/> 지원되는 다른 대상에 관해서는 [Azure Data Factory](../data-factory/connector-overview.md) 문서를 참조하세요.| &bull; 손쉽게 설정하고 여러 원본을 지원합니다. <br/>&bull; Azure Cosmos DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull; 대량 데이터 세트에 적합합니다. <br/>&bull; 검사점이 없으면 마이그레이션 과정에서 발생하는 모든 문제에 대해 전체 마이그레이션 프로세스를 다시 시작해야 함을 의미합니다.<br/>&bull; 배달 못한 편지 큐가 없으면 일부 잘못된 파일이 전체 마이그레이션 프로세스를 중지할 수 있음을 의미합니다. <br/>&bull; 특정 데이터 원본에 대한 읽기 처리량을 늘리려면 사용자 지정 코드가 필요합니다.|
|오프라인|기존 Mongo 도구([mongodump](mongodb/tutorial-mongotools-cosmos-db.md#mongodumpmongorestore), [mongorestore](mongodb/tutorial-mongotools-cosmos-db.md#mongodumpmongorestore), [Studio3T](mongodb/connect-using-mongochef.md))]|MongoDB | Azure Cosmos DB API for MongoDB| &bull; 손쉽게 설정하고 통합합니다. <br/>&bull; 제한에 대한 사용자 지정 처리가 필요합니다.|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API

용량 계획에 도움이 필요한 경우 [Azure Cosmos DB 용량 플래너를 사용하여 RU/s를 추정하는 가이드](estimate-ru-with-capacity-planner.md)를 참조하세요. 

|마이그레이션 유형|해결 방법|지원되는 원본|지원되는 대상|고려 사항|
|---------|---------|---------|---------|---------|
|오프라인|[cqlsh COPY 명령](cassandra/migrate-data.md#migrate-data-by-using-the-cqlsh-copy-command)|CSV 파일 | Azure Cosmos DB Cassandra API| &bull; 손쉽게 설정합니다. <br/>&bull; 대량 데이터 세트에 적합하지 않습니다. <br/>&bull; 원본이 Cassandra 테이블인 경우에만 작동합니다.|
|오프라인|[Spark를 사용하여 테이블 복사](cassandra/migrate-data.md#migrate-data-by-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API| Azure Cosmos DB Cassandra API | &bull; Spark 기능을 사용하여 변환 및 수집을 병렬 처리할 수 있습니다. <br/>&bull; 제한을 처리하기 위해 사용자 지정 재시도 정책을 사용하는 구성이 필요합니다.|
|온라인|[Striim(Oracle DB/Apache Cassandra에서)](cassandra/migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> 지원되는 다른 원본은 [Striim 웹 사이트](https://www.striim.com/sources-and-targets/)를 참조하세요.|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API <br/><br/> 지원되는 다른 대상은 [Striim 웹 사이트](https://www.striim.com/sources-and-targets/)를 참조하세요.| &bull; Oracle, DB2, SQL Server 같은 훨씬 다양한 원본에서 작동합니다. <br/>&bull; ETL 파이프라인을 쉽게 빌드하고 모니터링을 위한 대시보드를 제공합니다. <br/>&bull; 더 큰 데이터 세트를 지원합니다. <br/>&bull; 이는 타사 도구이므로 마켓플레이스에서 구매하고 사용자 환경에 설치해야 합니다.|
|온라인|[Blitzz(Oracle DB/Apache Cassandra에서)](cassandra/oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>지원되는 다른 원본은 [Blitzz 웹 사이트](https://www.blitzz.io/)를 참조하세요. |Azure Cosmos DB Cassandra API. <br/><br/>지원되는 다른 대상은 [Blitzz 웹 사이트](https://www.blitzz.io/)를 참조하세요. | &bull; 더 큰 데이터 세트를 지원합니다. <br/>&bull; 이는 타사 도구이므로 마켓플레이스에서 구매하고 사용자 환경에 설치해야 합니다.|

## <a name="other-apis"></a>기타 API

SQL API, Mongo API 및 Cassandra API 이외의 API에는 각 API의 기존 에코시스템에서 지원하는 다양한 도구가 있습니다. 

**Table API** 

* [데이터 마이그레이션 도구](table/table-import.md#data-migration-tool)
* [AZCopy](table/table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [그래프 대량 실행기 라이브러리](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어 보세요.
* [.NET](bulk-executor-dot-net.md) 및 [Java](bulk-executor-java.md)에서 대량 실행기 라이브러리를 사용하는 샘플 애플리케이션을 사용해 보며 자세히 알아봅니다. 
* 대량 실행기 라이브러리는 Cosmos DB Spark 커넥터에 통합됩니다. 자세한 내용은 [Azure Cosmos DB Spark 커넥터](./create-sql-api-spark.md) 문서를 참조하세요.  
* 대규모 마이그레이션에 관한 추가적인 도움이 필요하면 “일반 권고” 문제 유형 및 “대규모(TB 이상) 마이그레이션” 문제 하위 유형에서 지원 티켓을 열어 Azure Cosmos DB 제품 팀에 문의하세요.