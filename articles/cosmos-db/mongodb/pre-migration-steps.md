---
title: Azure Cosmos DB의 API for MongoDB로 데이터 마이그레이션을 위한 마이그레이션 전 단계
description: 이 문서에서는 MongoDB에서 Cosmos DB로 데이터 마이그레이션을 위한 필수 구성 요소에 대한 개요를 제공합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 9bf6403f57ed7b49fb6b60d1f674a10616c017f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593879"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에서 Azure Cosmos DB의 API for MongoDB로 데이터 마이그레이션을 위한 마이그레이션 전 단계
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 마이그레이션 전 단계를 수행하기 전에 이 전체 가이드를 읽어보세요.
>

이 MongoDB 사전 마이그레이션 가이드는 MongoDB 마이그레이션 시리즈의 일부입니다. 중요한 MongoDB 마이그레이션 단계는 아래와 같이 마이그레이션 사전 작업, 마이그레이션 및 [마이그레이션 사후 작업](post-migration-optimization.md)입니다.

![마이그레이션 단계 다이어그램.](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-pre-migration"></a>사전 마이그레이션 개요

실제로 데이터를 이동하기 전에 마이그레이션에 대한 특정 계획 및 의사 결정을 미리 수행하는 것이 중요합니다. 이 초기 의사 결정 프로세스는 "사전 마이그레이션"입니다. 사전 마이그레이션의 목표는 (1) 애플리케이션의 마이그레이션 후 요구 사항을 충족하도록 Azure Cosmos DB를 설정하고 (2) 마이그레이션을 실행하는 방법을 계획하는 것입니다.

철저한 사전 마이그레이션을 수행하려면 다음 단계를 수행합니다.
* [기존 MongoDB 리소스를 검색하고 이를 추적할 아티팩트 만들기](#pre-migration-discovery)
* [데이터 마이그레이션을 위한 기존 MongoDB 리소스의 준비 상태 평가](#pre-migration-assessment)
* [기존 MongoDB 리소스를 새 Azure Cosmos DB 리소스에 매핑](#pre-migration-mapping)
* [전체 규모의 데이터 마이그레이션을 시작하기 전에 엔드투엔드 마이그레이션 프로세스의 실행 계획](#execution-logistics)

그런 다음, 사전 마이그레이션 계획에 따라 마이그레이션을 실행합니다.

마지막으로, [중단 및 최적화의 중요한 마이그레이션 후 단계를 수행합니다.](post-migration-optimization.md)

위의 모든 단계는 성공적인 마이그레이션을 위해 중요합니다.

마이그레이션을 계획할 때는 가능하면 리소스별 수준에서 계획하는 것이 좋습니다.

## <a name="pre-migration-discovery"></a>마이그레이션 전 검색

첫 번째 마이그레이션 전 단계는 리소스 검색입니다. 이 단계에서는 MongoDB 데이터 자산에 있는 기존 리소스의 포괄적인 목록을 만들려고 합니다.

### <a name="create-a-data-estate-migration-spreadsheet"></a>데이터 공간 마이그레이션 스프레드시트 만들기

기본 설정된 생산성 소프트웨어를 사용하여 마이그레이션에 대한 추적 문서로 **데이터 공간 마이그레이션 스프레드시트** 를 만듭니다. 
   * 이 스프레드시트의 목적은 생산성을 개선하고 엔드투엔드 마이그레이션을 계획하는 데 도움을 주는 것입니다.
   * 스프레드시트의 구조는 사용자에게 달려 있습니다. 다음 중요 항목은 몇 가지 권장 사항을 제공합니다.
   * 이 스프레드시트는 목록 형식으로 데이터 공간 리소스의 레코드로 구성되어야 합니다.
   * 각 행은 리소스(데이터베이스 또는 컬렉션)에 해당합니다.
   * 각 열은 리소스의 속성에 해당합니다. 지금은 적어도 *이름* 및 *데이터 크기(GB)* 를 열로 지정해야 하지만, 이상적으로는 각 리소스에 대한 MongoDB 버전에 대한 정보를 수집할 수도 있습니다. 이 경우에는 *Mongo 버전* 열도 추가합니다. 
   * 처음에는 MongoDB 데이터 공간에서 기존 리소스 목록으로 이 스프레드시트를 채웁니다. 이 가이드를 진행하면서 필요에 따라 열을 추가하는 엔드투엔드 마이그레이션 계획에 대한 추적 문서로 이 스프레드시트를 작성합니다.

### <a name="discover-existing-mongodb-data-estate-resources"></a>기존 MongoDB 데이터 공간 리소스 검색

적절한 검색 도구를 사용하여 기존 MongoDB 데이터 공간에서 가능한 한 포괄적으로 리소스(데이터베이스, 컬렉션)를 식별합니다. 

리소스를 검색하는 데 사용할 수 있는 몇 가지 도구는 다음과 같습니다.
   * [MongoDB Shell](https://www.mongodb.com/try/download/shell)
   * [MongoDB Compass](https://www.mongodb.com/try/download/compass)

## <a name="pre-migration-assessment"></a>마이그레이션 전 평가

둘째로, 마이그레이션을 계획할 때 prelude 데이터 공간에서 마이그레이션을 위해 각 리소스의 준비 상태를 평가합니다. 

준비에 영향을 주는 주 요소는 MongoDB 버전입니다. Azure Cosmos DB는 현재 MongoDB 이진 프로토콜 버전 3.2, 3.6 및 4.0을 지원합니다. 마이그레이션 계획 스프레드시트에 *MongoDB 버전* 에 대한 열이 있는 것이 좋습니다. 스프레드시트를 단계별로 안내하고 Azure Cosmos DB에 대한 호환되지 않는 MongoDB 버전을 사용하는 리소스를 강조 표시합니다.

## <a name="pre-migration-mapping"></a>마이그레이션 전 매핑

검색 및 평가 단계가 완료되면 수식의 MongoDB 쪽에서 작업을 완료합니다. 이제 수식의 Azure Cosmos DB 쪽을 계획할 차례입니다. 프로덕션 Azure Cosmos DB 리소스를 어떻게 설정하고 구성하나요? *리소스별* 수준에서 계획을 수행합니다. 즉, 계획 스프레드시트에 다음 열을 추가해야 합니다. 
* Azure Cosmos DB 매핑 
* 분할 키 
* 데이터 모델
* 전용 및 공유 처리량

자세한 내용은 다음 섹션에 제공됩니다.

### <a name="capacity-planning"></a>용량 계획

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요?
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 빈도를 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)을 읽어보세요.

### <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB 사용 시 고려 사항

Azure Cosmos DB 데이터 자산에 대한 계획을 수립하기 전에 다음 Azure Cosmos DB 개념을 이해해야 합니다.

- **용량 모델**: Azure Cosmos DB의 데이터베이스 용량은 처리량 기반 모델을 기반으로 합니다. 이 모델은 초당 컬렉션에 대해 실행할 수 있는 데이터베이스 작업의 수를 나타내는 단위인 [초당 요청 단위](../request-units.md)를 기반으로 합니다. 이 용량은 [데이터베이스 또는 컬렉션 수준](../set-throughput.md)에서 할당될 수 있으며, 할당 모델에서 프로비저닝하거나 [자동 크기 조정 프로비저닝된 처리량](../provision-throughput-autoscale.md)을 사용하여 프로비저닝할 수 있습니다.

- **요청 단위**: 모든 데이터베이스 작업에는 Azure Cosmos DB에 관련된 RU(요청 단위) 비용이 포함됩니다. 이를 실행하면 지정된 초에 사용 가능한 요청 단위 수준에서 뺍니다. 요청에 현재 할당된 RU/초보다 많은 RU가 필요한 경우 문제를 해결하는 두 가지 옵션이 있습니다. RU의 용량을 늘리거나 다음 초가 시작될 때까지 기다린 후 작업을 다시 시도하세요.

- **탄력적 용량**: 지정된 컬렉션 또는 데이터베이스에 대한 용량은 언제든지 변경할 수 있습니다. 이를 통해 데이터베이스는 워크로드의 처리량 요구 사항에 맞게 탄력적으로 적응할 수 있습니다.

- **자동 분할**: Azure Cosmos DB는 분할(또는 파티션 키)만 필요한 자동 분할 시스템을 제공합니다. [자동 분할 메커니즘](../partitioning-overview.md)은 모든 Azure Cosmos DB API에서 공유되고, 원활한 데이터를 허용하고 수평 분산을 통해 전체를 확장할 수 있습니다.

### <a name="plan-the-azure-cosmos-db-data-estate"></a>Azure Cosmos DB 데이터 자산 계획

만들려는 Azure Cosmos DB 리소스를 파악합니다. 즉, 데이터 자산 마이그레이션 스프레드시트를 단계별로 진행하여 기존 MongoDB 리소스를 새 Azure Cosmos DB 리소스에 매핑합니다. 
* 각 MongoDB 데이터베이스가 Azure Cosmos DB 데이터베이스가 될 것으로 예상합니다
* 각 MongoDB 컬렉션이 Azure Cosmos DB 컬렉션이 될 것으로 예상합니다
* Azure Cosmos DB 리소스에 대한 명명 규칙을 선택합니다. 데이터베이스 및 컬렉션의 구조 변경을 제외하고 동일한 리소스 이름을 유지하는 것이 일반적으로 좋은 선택입니다.
* MongoDB에서 분할 컬렉션은 선택 사항입니다. Azure Cosmos DB에서 모든 컬렉션은 분할됩니다.
* *MongoDB 컬렉션 분할 키가 Azure Cosmos DB 컬렉션 분할 키가 된다고 가정하지 않습니다. 기존 MongoDB 데이터 모델/문서 구조가 Azure Cosmos DB에서 사용하는 구조라고 가정하지 않습니다.* 
   * 분할 키는 Azure Cosmos DB의 확장성과 성능을 최적화하기 위한 가장 중요한 설정이며, 데이터 모델링은 두 번째로 중요한 설정입니다. 이러한 설정은 모두 변경할 수 없으며, 설정된 후에는 변경할 수 없습니다. 따라서 계획 단계에서 최적화하는 것이 매우 중요합니다. 자세한 내용은 [변경할 수 없는 결정 사항](#immutable-decisions) 섹션의 지침을 따르세요.
* Azure Cosmos DB는 제한된 컬렉션과 같은 특정 MongoDB 컬렉션 형식을 인식하지 못합니다. 이러한 리소스에 대해 일반적인 Azure Cosmos DB 컬렉션을 만듭니다.
* Azure Cosmos DB에는 두 가지 컬렉션 형식(공유 및 전용 처리량)이 있습니다. 공유 및 전용 처리량은 계획 단계에서 수행해야 하는 중요한 변경할 수 없는 또 다른 결정입니다. 자세한 내용은 [변경할 수 없는 결정 사항](#immutable-decisions) 섹션의 지침을 따르세요.

### <a name="immutable-decisions"></a>변경할 수 없는 결정

Azure Cosmos DB 리소스를 만든 후에는 다음 Azure Cosmos DB 구성 선택 항목을 수정하거나 취소할 수 없습니다. 따라서 마이그레이션을 시작하기 전에 마이그레이션 전 계획 중에 이러한 권한을 제공하는 것이 중요합니다.
* [이 가이드](../partitioning-overview.md)에 따라 가장 적합한 분할 키를 선택합니다. 분할(Sharding이라고도 함)은 데이터를 마이그레이션하기 전에 고려해야 할 주요 요소입니다. Azure Cosmos DB는 완전 관리형 분할을 사용하여 스토리지 및 처리량 요구 사항에 맞게 데이터베이스의 용량을 늘립니다. 이 기능에는 라우팅 서버의 호스팅 또는 구성이 필요 없습니다.   
   * 이와 비슷한 방식으로 분할 기능이 자동으로 용량을 추가하고 그에 따라 데이터의 균형을 다시 조정합니다. 데이터의 올바른 파티션 키를 선택하는 방법에 대한 자세한 내용과 권장 사항은 [파티션 키 선택 문서](../partitioning-overview.md#choose-partitionkey)를 참조하세요. 
* [이 가이드](../modeling-data.md)에 따라 데이터 모델을 선택합니다
* [이 가이드](../optimize-cost-throughput.md#optimize-by-provisioning-throughput-at-different-levels)에 따라 마이그레이션하려는 각 리소스에 대한 전용 및 공유 처리량 중에서 선택할 수 있습니다
* 의사 결정 프로세스에 도움이 되는 분할 및 데이터 모델링의 실제 예는 [여기](../how-to-model-partition-example.md)를 참조하세요

### <a name="cost-of-ownership"></a>소유 비용

* [Azure Cosmos DB 용량 계산기](https://cosmos.azure.com/capacitycalculator/)를 사용하여 새 Azure Cosmos DB 리소스의 소유 비용을 예측합니다.

### <a name="estimating-throughput"></a>처리량 예측

* Azure Cosmos DB에서 처리량은 사전에 프로비저닝되며 초당 RU(요청 단위)로 측정됩니다. VM 또는 온-프레미스 서버와 달리 RU는 언제든지 쉽게 확장 및 축소할 수 있습니다. 프로비저닝된 RU의 수를 즉시 변경할 수 있습니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](../request-units.md)를 참조하세요.

* [Azure Cosmos DB 용량 계산기](https://cosmos.azure.com/capacitycalculator/)를 사용하여 데이터베이스 계정 구성, 데이터 양, 문서 크기 및 초당 필요한 읽기/쓰기를 기반으로 하는 요청 단위 크기를 결정할 수 있습니다.

* 다음은 필수 RU의 수에 영향을 주는 주요 요소입니다.
   * **문서 크기**: 항목/문서의 크기가 증가함에 따라 항목/문서를 읽거나 쓰는 데 사용되는 RU 수도 증가합니다.

   * **문서 속성 수**: 문서를 만들거나 업데이트하는 데 사용된 RU 수는 해당 속성의 수, 복잡성 및 길이와 관련이 있습니다. [인덱싱되는 속성 수를 제한](mongodb-indexing.md)하면 쓰기 작업에 대한 요청 단위 사용을 줄일 수 있습니다.

   * **쿼리 패턴**: 쿼리의 복잡성은 쿼리에서 사용되는 요청 단위의 양에 영향을 줍니다. 

* 쿼리 비용을 이해하는 가장 좋은 방법은 Azure Cosmos DB에서 샘플 데이터를 사용하고 `getLastRequestStastistics` 명령을 사용하여 [MongoDB Shell에서 샘플 쿼리를 실행](connect-mongodb-account.md)하여 요청 요금을 가져오는 것입니다. 그러면 사용된 RU 수가 출력됩니다.

    `db.runCommand({getLastRequestStatistics: 1})`

    이 명령은 다음과 유사한 JSON 문서를 출력합니다.

    `{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}`

* [진단 설정](../cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB에 대해 실행되는 쿼리의 빈도와 패턴을 파악할 수도 있습니다. 진단 로그의 결과를 스토리지 계정, EventHub 인스턴스 또는 [Azure Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)로 보낼 수 있습니다.  

## <a name="pre-migration-logistics-planning"></a>마이그레이션 전 실행 계획

마지막으로, 기존 데이터 공간에 대한 보기와 새로운 Azure Cosmos DB 데이터 공간에 대한 디자인이 준비되었으므로 마이그레이션 프로세스를 엔드투엔드로 실행하는 방법을 계획할 수 있습니다. *리소스별* 수준으로 계획을 수행하고, 아래 로지스틱 차원을 캡처하기 위해 스프레드시트에 열을 추가합니다.

### <a name="execution-logistics"></a>실행 계획
* MongoDB에서 Azure Cosmos DB에 각 기존 리소스를 마이그레이션하는 책임을 할당합니다. 마이그레이션을 완료하기 위해 팀 리소스를 활용하는 방법은 사용자에게 달려 있습니다. 소규모 마이그레이션의 경우 한 팀이 전체 마이그레이션을 시작하고 진행 상황을 모니터링할 수 있습니다. 대규모 마이그레이션의 경우 해당 리소스를 마이그레이션하고 모니터링하기 위해 리소스별로 팀 멤버에게 책임을 할당할 수 있습니다.
* 리소스 마이그레이션에 대한 책임을 할당했으면 이제 마이그레이션에 적합한 마이그레이션 도구를 선택해야 합니다. 소규모 마이그레이션의 경우 MongoDB 네이티브 도구 또는 Azure DMS와 같은 하나의 마이그레이션 도구를 사용하여 모든 리소스를 한 번의 마이그레이션으로 마이그레이션할 수 있습니다. 대규모 마이그레이션 또는 특별한 요구 사항이 있는 마이그레이션의 경우 리소스별 세분 단위로 마이그레이션 도구를 선택할 수 있습니다.
   * 사용할 마이그레이션 도구를 계획하기 전에 사용 가능한 옵션을 직접 아는 것이 좋습니다. [Azure Cosmos DB의 API for MongoDB에 대한 Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db.md)는 완전 관리형 호스팅 플랫폼, 마이그레이션 모니터링 옵션 및 자동 제한 처리를 제공하여 데이터 마이그레이션을 간소화하는 메커니즘을 제공합니다. 옵션의 전체 목록은 다음과 같습니다.

   |**마이그레이션 유형**|**해결 방법**|**고려 사항**|
   |---------|---------|---------|
   |온라인|[Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB 대량 실행기 라이브러리 사용 <br/>&bull; 대량 데이터 세트에 적합하고 라이브 변경 내용 복제를 처리 <br/>&bull; 다른 MongoDB 소스에서만 작동|
   |오프라인|[Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB 대량 실행기 라이브러리 사용 <br/>&bull; 대량 데이터 세트에 적합하고 라이브 변경 내용 복제를 처리 <br/>&bull; 다른 MongoDB 소스에서만 작동|
   |오프라인|[Azure Data Factory](../../data-factory/connector-azure-cosmos-db.md)|&bull; 간편한 설정 및 여러 소스 지원 <br/>&bull; Azure Cosmos DB 대량 실행기 라이브러리 사용 <br/>&bull; 대량 데이터 세트에 적합 <br/>&bull; 검사점이 없으면 마이그레이션 과정에서 발생하는 모든 문제에 대해 전체 마이그레이션 프로세스를 다시 시작해야 함을 의미함<br/>&bull; 배달 못한 편지 큐가 없으면 일부 잘못된 파일이 전체 마이그레이션 프로세스를 중지할 수 있음을 의미함 <br/>&bull; 특정 데이터 원본에 대한 읽기 처리량을 늘리려면 사용자 지정 코드가 필요함|
   |오프라인|[기존 Mongo 도구(mongodump, mongorestore, Studio3T)](tutorial-mongotools-cosmos-db.md)|&bull; 간편한 설정 및 통합 <br/>&bull; 제한에 대한 사용자 지정 처리 필요|
   |오프라인/온라인|[Azure Databricks 및 Spark](migrate-databricks.md)|&bull; 마이그레이션 속도 및 데이터 변환에 대한 모든 권한 <br/>&bull; 사용자 지정 코드가 필요합니다|
    
   * 리소스에서 오프라인 마이그레이션을 허용하려면 아래 다이어그램을 사용하여 적절한 마이그레이션 도구를 선택합니다.

   ![오프라인 마이그레이션 도구.](./media/pre-migration-steps/offline-tools.png)

   * 리소스에 온라인 마이그레이션이 필요한 경우, 아래 다이어그램을 사용하여 적절한 마이그레이션 도구를 선택합니다.

   ![온라인 마이그레이션 도구.](./media/pre-migration-steps/online-tools.png)
   
   위에서 언급한 [마이그레이션 솔루션의 개요 및 데모](https://www.youtube.com/watch?v=WN9h80P4QJM)는 이 비디오를 시청하세요.

* 각 리소스에 대한 마이그레이션 도구를 선택한 후 다음 단계는 마이그레이션할 리소스의 우선 순위를 지정하는 것입니다. 우선 순위를 지정하면 일정에 따라 마이그레이션을 유지할 수 있습니다. 이동 시간이 가장 많은 리소스 마이그레이션의 우선 순위를 지정하는 것이 좋습니다. 이러한 리소스를 먼저 마이그레이션하면 완료될 때까지 가장 큰 절차가 진행됩니다. 또한 이러한 시간이 많이 걸리는 마이그레이션에는 일반적으로 더 많은 데이터가 포함되므로, 일반적으로 마이그레이션 도구에 대해 리소스 집약적이며, 따라서 마이그레이션 파이프라인의 문제를 초기에 노출할 가능성이 더 높습니다. 이렇게 하면 마이그레이션 파이프라인의 어려움으로 인해 일정이 지연될 가능성이 최소화됩니다.
* 마이그레이션이 시작된 후 진행 상황을 모니터링하는 방법을 계획합니다. 팀 간에 데이터 마이그레이션 작업을 조정하는 경우 우선 순위가 높은 마이그레이션이 진행되는 방식을 포괄적으로 볼 수 있도록 팀 동기화의 정기적인 흐름을 계획합니다.
  

### <a name="supported-migration-scenarios"></a>지원되는 마이그레이션 시나리오

가장 좋은 MongoDB 마이그레이션 도구를 선택하는 것은 마이그레이션 시나리오에 따라 달라집니다. 

#### <a name="types-of-migrations"></a>마이그레이션 유형

각 마이그레이션 시나리오에 대해 호환되는 도구는 다음과 같습니다.

![지원되는 마이그레이션 시나리오.](./media/pre-migration-steps/migration-tools-use-case-table.png)

#### <a name="tooling-support-for-mongodb-versions"></a>MongoDB 버전에 대한 도구 지원

특정 MongoDB 버전에서 마이그레이션하는 경우 지원되는 도구가 다음과 같이 표시됩니다.

![MongoDB 버전은 마이그레이션 도구에서 지원됩니다.](./media/pre-migration-steps/migration-tool-compatibility.png)

### <a name="post-migration"></a>마이그레이션 후 작업

마이그레이션 전 단계에서 앱 마이그레이션 및 최적화 후 마이그레이션을 수행할 단계를 계획하는 데 약간의 시간을 할애합니다.
* 마이그레이션 후 단계에서는 기존 MongoDB 데이터 공간 대신 Azure Cosmos DB를 사용하기 위해 애플리케이션의 중단을 실행합니다. 
* 리소스 수준별로 인덱싱, 전역 배포, 일관성 및 기타 *변경할 수 있는* Azure Cosmos DB 속성을 계획하는 것이 가장 좋습니다. 그러나 이러한 Azure Cosmos DB 구성 설정은 나중에 수정할 수 있으므로 이러한 설정에 대한 조정 작업을 추후에 수행할 수 *있습니다*. 이러한 측면이 분석 마비의 원인이 되지 않도록 하세요. 이러한 변경 가능한 구성을 마이그레이션 후에 적용합니다.
* 마이그레이션 후에 대한 최상의 가이드는 [여기](post-migration-optimization.md)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 빈도를 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)을 읽어보세요.
* Azure Cosmos DB API for MongoDB로 마이그레이션
   * [MongoDB 네이티브 도구를 사용한 오프라인 마이그레이션](tutorial-mongotools-cosmos-db.md)
   * [Azure DMS(데이터베이스 마이그레이션 서비스)를 사용한 오프라인 마이그레이션](../../dms/tutorial-mongodb-cosmos-db.md)
   * [Azure DMS(데이터베이스 마이그레이션 서비스)를 사용한 온라인 마이그레이션](../../dms/tutorial-mongodb-cosmos-db-online.md)
   * [Azure Databricks 및 Spark를 사용한 오프라인/온라인 마이그레이션](migrate-databricks.md)
* [마이그레이션 후 가이드](post-migration-optimization.md) - Azure Cosmos DB API for MongoDB로 마이그레이션한 후 단계 최적화
* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량 프로비저닝](../set-throughput.md)
* [Azure Cosmos DB에서 분할](../partitioning-overview.md)
* [Azure Cosmos DB의 글로벌 배포](../distribute-data-globally.md)
* [Azure Cosmos DB의 인덱싱](../index-overview.md)
* [Azure Cosmos DB의 요청 단위](../request-units.md)
