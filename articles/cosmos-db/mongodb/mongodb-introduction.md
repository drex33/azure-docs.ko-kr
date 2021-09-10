---
title: Azure Cosmos DB API for MongoDB 소개
description: Azure Cosmos DB의 API for MongoDB를 사용하여 Azure Cosmos DB로 대량의 데이터를 저장하고 쿼리하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: bce5b5874b12638d1f2d628e23812676053006a4
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035210"
---
# <a name="azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB API for MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API for MongoDB를 사용하면 Cosmos DB를 MongoDB 데이터베이스인 것처럼 간편하게 사용할 수 있습니다. 애플리케이션으로 API for MongoDB 계정의 연결 문자열을 가리켜서 MongoDB 환경을 활용하고 즐겨 사용하는 MongoDB 드라이버, SDK 및 도구를 계속 사용할 수 있습니다.

## <a name="why-choose-the-api-for-mongodb"></a>API for MongoDB를 선택하는 이유

API for MongoDB는 [Azure Cosmos DB](../introduction.md)를 기반으로 하기 때문에 MongoDB Atlas와 같은 서비스 제품보다 다양한 추가 이점이 있습니다.

* **즉각적인 확장성**: [자동 크기 조정](../provision-throughput-autoscale.md) 기능을 사용하도록 설정하면 데이터베이스를 준비 시간 없이 확장/축소할 수 있습니다.
* **자동 및 투명 분할**: API for MongoDB는 모든 인프라를 자동으로 관리합니다. 여기에는 MongoDB Atlas 같은 다른 MongoDB 제품과는 다른 분할 및 분할 수가 포함되며, 수평 확장을 위해서는 분할을 지정하고 관리해야 합니다. 따라서 개발자가 사용자를 위한 애플리케이션 개발에 집중할 시간을 더 많이 확보할 수 있습니다.
* **99.999%의 가용성**: [99.999%](../high-availability.md)의 가용성을 간편하게 구성할 수 있어 언제든지 필요할 때 데이터를 사용할 수 있습니다.  
* **비용 효율적이고, 세부적이며, 무제한의 확장성**: 다른 MongoDB 서비스 제품과 달리 분할된 컬렉션이 무제한 크기로 확장될 수 있습니다. API for MongoDB 사용자는 현재 600TB가 넘는 스토리지를 사용하여 데이터베이스를 실행하고 있습니다. 다른 MongoDB 서비스 제품과 달리 크기 조정이 비용 효율적으로 수행되므로 경제적인 크기 조정 및 리소스 거버넌스 때문에 VM의 1/100 수준의 작은 단위로 Cosmos DB 플랫폼의 크기를 조정할 수 있습니다.
* **서버리스 배포**: MongoDB Atlas와 달리 API for MongoDB는 [서버리스 용량 모드](../serverless.md)를 제공하는 클라우드 네이티브 데이터베이스입니다. [서버리스](../serverless.md)의 경우 작업당 요금만 청구되며, 사용하지 않을 때는 데이터베이스에 대한 요금을 지불하지 않습니다.
* **무료 계층**: Microsoft Azure Cosmos DB 무료 계층을 사용하는 경우 처음에는 1000RU/s 및 25GB의 스토리지가 계정 수준에서 계속 무료로 제공됩니다.
* **몇 초만에 업그레이드**: 모든 API 버전이 하나의 코드베이스에 포함되어 있기 때문에 [스위치만 누르면](upgrade-mongodb-version.md) 가동 중지 시간 없이 간단하게 버전을 변경할 수 있습니다.
* **모든 규모의 실시간 분석(HTAP)** : API for MongoDB는 데이터베이스에 영향을 주지 않고 실시간으로 데이터베이스 데이터에 대한 비즈니스 인텔리전스 같은 사용 사례에서 복잡한 분석 쿼리를 실행하는 기능을 제공합니다. 이는 ETL 파이프라인 없이 활용할 수 있는 클라우드 네이티브 분석 열 형식 저장소로 인해 빠르고 저렴합니다. [Azure Synapse Link](../synapse-link.md)에 대해 자세히 알아보세요.

> [!NOTE]
> [무료 계층에서 무료로 Azure Cosmos DB API for MongoDB를 사용할 수 있습니다](../free-tier.md). Microsoft Azure Cosmos DB 무료 계층을 사용하는 경우 처음에는 1000RU/s 및 25GB의 스토리지가 계정 수준에서 무료로 제공됩니다.


## <a name="how-the-api-works"></a>API의 작동 원리

Azure Cosmos DB API for MongoDB는 MongoDB용 유선 프로토콜을 구현합니다. 이 구현을 통해 네이티브 MongoDB 클라이언트 SDK, 드라이버 및 도구와 투명하게 호환됩니다. Azure Cosmos DB는 MongoDB 데이터베이스 엔진을 호스트하지 않습니다. 사용 중인 API 버전과 호환되는 MongoDB 클라이언트 드라이버에서 특별한 구성 없이 연결할 수 있습니다.

MongoDB 기능 호환성:

Azure Cosmos DB API for MongoDB와 호환 가능한 MongoDB 서버 버전은 다음과 같습니다.
- [버전 4.0](feature-support-40.md)
- [버전 3.6](feature-support-36.md)
- [버전 3.2](feature-support-32.md)

API for MongoDB의 모든 버전이 동일한 코드베이스에서 실행되므로 업그레이드를 가동 중지 시간 없이 몇 초만에 간단하게 수행할 수 있습니다. Azure Cosmos DB는 몇 가지 기능 플래그의 설정/해제만으로 한 버전에서 다른 버전으로 전환됩니다.  또한 이러한 기능 플래그를 통해 3.2 및 3.6 같은 이전 API 버전도 계속해서 지원 가능합니다. 따라서 내게 가장 맞는 서버 버전을 선택할 수 있습니다.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Azure Cosmos DB의 MongoDB API" border="false":::

## <a name="what-you-need-to-know-to-get-started"></a>시작하기 전에 알아야 할 사항

* 클러스터의 가상 머신에 대해서는 비용이 청구되지 않습니다. [가격](../how-pricing-works.md)은 데이터베이스당 또는 컬렉션별로 구성된 RU(요청 단위)의 처리량을 기준으로 책정됩니다. 첫 1000RU/s는 [무료 계층](../free-tier.md)을 통해 무료로 제공됩니다.

* Azure Cosmos DB API for MongoDB는 다음 세 가지 방법 중 하나로 배포할 수 있습니다.
     * [프로비전된 처리량](../set-throughput.md): RU/초 수를 설정하고 수동으로 변경합니다. 이 모델은 일관된 워크로드에 적합합니다.
     * [Autoscale]../(provision-throughput-autoscale.md): 필요한 처리량의 상한선을 설정합니다. 처리량이 요구에 맞게 즉시 확장됩니다. 이 모델은 자주 바뀌는 워크로드에 적합하며 비용을 최적화해줍니다.
     * [서버리스](../serverless.md): 사용한 처리량에 대해서만 요금을 지불합니다. 이 모델은 개발/테스트 워크로드에 적합합니다. 

* 분할된 클러스터의 성능은 컬렉션을 만들 때 선택한 분할 키에 따라 달라집니다. 따라서 데이터가 각 분할 간에 균등하게 분산되도록 분할 키를 신중하게 선택해야 합니다.

### <a name="capacity-planning"></a>용량 계획

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](../estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

## <a name="quickstart"></a>빠른 시작

* [기존 MongoDB Node.js 웹앱 마이그레이션](create-mongodb-nodejs.md)
* [Azure Cosmos DB의 API for MongoDB 및 .NET SDK를 사용하여 웹앱 빌드](create-mongodb-dotnet.md)
* [Azure Cosmos DB의 API for MongoDB 및 Java SDK를 사용하여 콘솔 앱 빌드](create-mongodb-java.md)
* [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md) 
* [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](../estimate-ru-with-capacity-planner.md)

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.
* [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md) 자습서에 따라 계정 연결 문자열 정보를 가져오는 방법을 알아봅니다.
* [Azure Cosmos DB에 Studio 3T 사용](connect-using-mongochef.md) 자습서에 따라 Studio 3T에서 Cosmos 데이터베이스와 MongoDB 앱을 연결하는 방법을 알아봅니다.
* [Azure Cosmos DB로 MongoDB 데이터 가져오기](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) 자습서에 따라 Cosmos 데이터베이스로 데이터를 가져옵니다.
* [Robo 3T](connect-using-robomongo.md)를 사용하여 Cosmos 계정에 연결
* [전역적으로 분산되는 앱의 읽기 기본 설정 구성](tutorial-global-distribution-mongodb.md) 방법을 알아봅니다.
* [문제 해결 가이드](error-codes-solutions.md)에서 일반적으로 발견되는 오류에 대한 솔루션 찾기


<sup>참고: 이 문서에서는 MongoDB 데이터베이스와 유선 프로토콜 호환성을 제공하는 Azure Cosmos DB 기능을 설명합니다. Microsoft는 이 서비스를 제공하기 위해 MongoDB 데이터베이스를 실행하지 않습니다. Azure Cosmos DB는 MongoDB, Inc.와 관련이 없습니다.</sup>