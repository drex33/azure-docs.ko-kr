---
title: MongoDB용 Azure Cosmos DB의 API를 사용한 마이그레이션 후 최적화 단계
description: 이 문서는 MongoDB에서 Mongo DB용 Azure Cosmos DB의 API로 마이그레이션 후 최적화 기술을 제공합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 6dbcb5e65c99c2009602d660e3cd0cc9e22efdbf
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031570"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB용 Azure Cosmos DB의 API 사용 시 마이그레이션 후 최적화 단계
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> 마이그레이션 사후 작업 단계를 수행하기 전에 전체 가이드를 읽어보세요.
>

이 MongoDB 마이그레이션 사후 작업 가이드는 MongoDB 마이그레이션에 대한 시리즈의 일부입니다. 중요한 MongoDB 마이그레이션 단계는 아래와 같이 [마이그레이션 사전 작업](pre-migration-steps.md), 마이그레이션, 마이그레이션 사후 작업입니다.

![마이그레이션 단계 다이어그램.](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-post-migration"></a>마이그레이션 사후 작업 개요

MongoDB 데이터베이스에 저장된 데이터를 MongoDB용 Azure Cosmos DB의 API로 마이그레이션한 후 Azure Cosmos DB에 연결하고 데이터를 관리할 수 있습니다. 이 가이드는 마이그레이션 후에 고려해야 하는 단계를 제공합니다. 마이그레이션 단계는 [MongoDB에서 MongoDB용 Azure Cosmos DB의 API로 마이그레이션 자습서](../../dms/tutorial-mongodb-cosmos-db.md)를 참조하세요.

마이그레이션 사후 작업을 수행하려면 다음 단계를 따르세요.

- [인덱싱 정책 최적화](#optimize-the-indexing-policy)
- [MongoDB용 Azure Cosmos DB의 API에 대한 글로벌 배포 구성](#globally-distribute-your-data)
- [일관성 수준 설정](#set-consistency-level)
- [애플리케이션 연결(중단)](#connect-or-cutover-your-application)
- [최적의 성능을 위한 조정](#tune-for-optimal-performance)

> [!NOTE]
> 마이그레이션 사후 작업의 유일한 필수 단계는 애플리케이션의 연결 문자열을 새 Azure Cosmos DB 계정을 향하도록 변경하는 것입니다. 다른 모든 마이그레이션 사후 작업 단계는 데이터 레이어 성능을 향상시키기 위해 추천되는 최적화 작업입니다. *그러나*, 다른 단계 없이 즉시 애플리케이션 중단을 하는 경우, 애플리케이션에는 최적화되지 않은 인덱싱 및 일관성 문제가 발생할 수 있습니다. 특히 인덱스를 구성하기 전에 중단을 하는 경우, 애플리케이션에 즉각적인 가격 및 성능의 하락이 발생할 수 있습니다. 이 문제는 해결될 수 있습니다. 즉, [인덱스가 최적화되면](#optimize-the-indexing-policy) Azure Cosmos DB는 가성비에 대한 현재 솔루션의 성능을 상회할 것입니다.
>

## <a name="pre-requisites"></a>필수 구성 요소

이 가이드에서는 스프레드시트와 같은 일종의 추적 아티팩트를 사용하여 마이그레이션 진행률에 대한 기록이 유지 관리된다고 가정합니다. 아직 이를 갖추지 못한 경우, [마이그레이션 사전 작업 가이드](pre-migration-steps.md)를 참조하여 *데이터 자산 마이그레이션 스프레드시트* 를 빌드하고 기존 MongoDB 리소스를 검색하여 마이그레이션을 계획하세요.

## <a name="optimize-the-indexing-policy"></a>인덱싱 정책 최적화

가격과 성능을 최적화하기 위해 데이터 자산 마이그레이션 스프레드시트를 단계별로 실행하고 각 리소스에 대한 인덱스 구성을 설계하는 것이 좋습니다. 
1. [마이그레이션 사전 작업 단계에서 인덱스를 계획](pre-migration-steps.md#post-migration)하는 것이 좋습니다. 인덱스 설정에 대해 데이터 자산 마이그레이션 스프레드시트에 열을 추가합니다. 
   * MongoDB 서버 버전 3.6 및 상위 버전용 Azure Cosmos DB API는 _id 필드만 자동으로 인덱싱합니다. 이 필드는 드롭할 수 없습니다. 분할 키별 _id 필드의 고유성을 자동으로 적용합니다. 추가 필드를 인덱싱하려면 MongoDB 인덱스 관리 명령을 적용합니다. 이 기본 인덱싱 정책은 모든 필드를 기본적으로 인덱싱하는 Azure Cosmos DB SQL API와 다릅니다.

   * MongoDB 서버 버전 3.2용 Azure Cosmos DB API의 경우, 모든 데이터 필드는 Azure Cosmos DB 데이터를 마이그레이션하는 동안 기본값으로 자동으로 인덱싱됩니다. 대부분의 경우 이 기본 인덱싱 정책을 사용할 수 있습니다. 일반적으로 인덱스를 제거하면 쓰기 요청이 최적화되고 기본 인덱싱 정책(즉, 자동 인덱싱)이 읽기 요청을 최적화합니다.

   * Azure Cosmos DB가 제공하는 인덱싱 기능에는 복합 인덱스, 고유 인덱스, TTL(Time to Live) 인덱스 추가가 포함됩니다. 인덱스 관리 인터페이스는 createIndex() 명령에 매핑됩니다. Azure Cosmos DB의 인덱싱 및 MongoDB용 Azure Cosmos DB API의 인덱싱에서 자세히 알아보세요.
2. 마이그레이션 사후 작업 중 이러한 인덱스 설정을 적용합니다.
   * [Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db.md)는 MongoDB 컬렉션을 고유 인덱스로 자동으로 마이그레이션합니다. 그러나 마이그레이션 전에 고유 인덱스를 만들어야 합니다. 컬렉션에 이미 데이터가 있는 경우 Azure Cosmos DB는 고유 인덱스 생성을 지원하지 않습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](../unique-keys.md)를 참조하세요.

## <a name="globally-distribute-your-data"></a>데이터 전역 배포

Azure Cosmos DB는 전 세계의 모든 [Azure 지역](https://azure.microsoft.com/regions/#services)에서 사용할 수 있습니다. 
1. 데이터를 전역적으로 배포하려면 [MongoDB용 Azure Cosmos DB API에서 데이터를 전역적으로 배포하기](tutorial-global-distribution-mongodb.md) 문서의 참고 자료를 따르세요. Azure Cosmos DB 계정의 기본 일관성 수준을 선택한 후 글로벌 배포 요구 사항에 따라 하나 이상의 Azure 영역을 연결할 수 있습니다. 고가용성 및 비즈니스 연속성을 위해 항상 둘 이상의 지역에서 실행하는 것이 좋습니다. [Azure Cosmos DB에서 다중 지역 배포의 비용을 최적화](../optimize-cost-regions.md)하기 위한 팁을 검토할 수 있습니다.

## <a name="set-consistency-level"></a>일관성 수준 설정

Azure Cosmos DB는 올바르게 정의된 5가지 [일관성 수준](../consistency-levels.md)을 제공합니다. MongoDB와 Azure Cosmos DB 일관성 수준 간의 매핑에 대한 자세한 내용은 [일관성 수준 및 Azure Cosmos DB API](../consistency-levels.md)를 참조하세요. 기본 일관성 수준은 세션 일관성 수준입니다. 일관성 수준 변경은 선택 사항이며 앱에 맞게 최적화할 수 있습니다. Azure Portal을 사용하여 일관성 수준을 변경하는 방법은 다음과 같습니다.

1. 설정에서 **기본 일관성** 블레이드로 이동합니다.
2. [일관성 수준](../consistency-levels.md)을 선택합니다.

대부분의 사용자는 기본 세션 일관성 설정에서 일관성 수준을 유지합니다. 하지만 [다양한 일관성 수준에 대한 가용성 및 성능 절충안](../consistency-levels.md)이 있습니다.

## <a name="connect-or-cutover-your-application"></a>애플리케이션 연결 또는 중단

애플리케이션을 중단하거나 연결 처리를 하면 마이그레이션이 완료된 후 애플리케이션이 Azure Cosmos DB를 사용하도록 애플리케이션을 전환할 수 있습니다. 아래 단계를 따릅니다.

1. 새 창에서 [Azure Portal](https://www.portal.azure.com/)에 로그인합니다.
2. [Azure Portal](https://www.portal.azure.com/)의 왼쪽 창에서 **모든 리소스** 메뉴를 열고 데이터를 마이그레이션한 Azure Cosmos DB 계정을 찾습니다.
3. **연결 문자열** 블레이드를 엽니다. 오른쪽 창에는 계정에 성공적으로 연결하는 데 필요한 모든 정보가 포함되어 있습니다.
4. 애플리케이션의 구성(또는 기타 관련 장소)에 있는 연결 정보를 사용하여 앱에 MongoDB용 Azure Cosmos DB의 API 연결을 반영합니다.
:::image type="content" source="./media/post-migration-optimization/connection-string.png" alt-text="연결 문자열에 대한 설정을 보여주는 스크린샷.":::

자세한 내용은 [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md) 페이지를 참조하세요.

## <a name="tune-for-optimal-performance"></a>최적의 성능을 위한 조정

[인덱싱](#optimize-the-indexing-policy), [전역 배포](#globally-distribute-your-data), [일관성](#set-consistency-level)에 대한 편리한 한 가지 사실 - 모든 해당 항목들이 적합한 방향으로 쉽게 구성 및 재구성될 수 있습니다. 따라서 애플리케이션 중단이 완료되면 애플리케이션의 성능을 모니터링하고 애플리케이션의 요구 사항을 충족하기 위해 필요에 따라 이러한 설정을 조정하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요?
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
* [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md)
* [Studio 3T를 사용하여 Azure Cosmos DB 계정에 연결](connect-using-mongochef.md)
* [Azure Cosmos DB의 MongoDB용 API를 사용하여 읽기를 전역 배포하는 방법](readpreference-global-distribution.md)
* [Azure Cosmos DB의 API for MongoDB를 사용하여 데이터 만료](mongodb-time-to-live.md)
* [Azure Cosmos DB의 일관성 수준](../consistency-levels.md)
* [Azure Cosmos DB의 인덱싱](../index-overview.md)
* [Azure Cosmos DB의 요청 단위](../request-units.md)