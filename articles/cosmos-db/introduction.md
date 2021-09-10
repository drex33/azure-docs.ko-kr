---
title: Azure Cosmos DB 소개
description: Azure Cosmos DB에 대해 알아봅니다. 전 세계에 배포된 이 다중 모델 데이터베이스는 낮은 대기 시간, 탄력적 확장성 및 고가용성을 위해 구축되었으며 NoSQL 데이터를 기본적으로 지원합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 08/26/2021
ms.openlocfilehash: ffd59ebcac1779c43222ea9a006888edd634bafa
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035912"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB 시작
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

오늘날의 애플리케이션은 응답성이 뛰어나며 항상 온라인을 유지해야 합니다. 짧은 대기 시간 및 고가용성을 이루려면 이러한 애플리케이션의 인스턴스를 해당 사용자에서 가까운 데이터 센터에 배포해야 합니다. 애플리케이션은 최고 사용 시간의 큰 변화에 실시간으로 응답하고, 계속 증가하는 데이터 볼륨을 저장하며, 사용자가 이 데이터를 밀리초 단위로 사용할 수 있도록 해야 합니다.

Azure Cosmos DB는 최신 앱 개발을 위한 완전 관리형 NoSQL 데이터베이스입니다. 한 자릿수 밀리초 응답 시간과 자동 및 즉시 확장성은 모든 규모에서 속도를 보장합니다. 비즈니스 연속성은 [SLA 지원](https://azure.microsoft.com/support/legal/sla/cosmos-db) 가용성 및 엔터프라이즈급 보안을 통해 보장됩니다. 전 세계 어디서나 턴키 다중 지역 데이터 배포, 인기 있는 언어를 위한 오픈 소스 API 및 SDK를 통해 더 빠르고 효율적으로 웹 개발이 이루어집니다. 완전 관리형 서비스인 Azure Cosmos DB는 자동 관리, 업데이트 및 패치를 통해 데이터베이스 관리를 직접 수행할 수 있습니다. 또한 용량과 비용을 일치시키기 위해 애플리케이션 요구 사항에 대응하는 비용 효율적인 서버리스 및 자동 확장 옵션으로 용량 관리를 처리합니다.

Azure 구독, 비용 및 약정 없이 [Azure Cosmos DB 무료 체험](https://azure.microsoft.com/try/cosmosdb/)을 사용해 보거나, [Azure Cosmos DB 무료 계층](free-tier.md)을 사용하여 처음에 1000RU/s 및 25GB의 스토리지를 무료로 사용할 수 있는 계정을 얻을 수 있습니다.

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)

> [!TIP]
> Azure Cosmos DB에 대해 자세히 알아보려면 매주 목요일 오후 1시에 Azure Cosmos DB Live TV에 참여하세요. [예정된 세션 일정 및 지난 에피소드](https://gotcosmos.com/tv)를 참조하세요.

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB는 최신 앱 개발을 위한 완전 관리형 NoSQL 데이터베이스입니다." border="false":::

## <a name="key-benefits"></a>주요 이점

### <a name="guaranteed-speed-at-any-scale"></a>모든 규모에서 보장된 속도

최고의 [SLA 지원](https://azure.microsoft.com/support/legal/sla/cosmos-db) 속도와 처리량, 빠른 글로벌 액세스 및 즉각적인 탄력성을 활용하세요.

- 빠른 읽기 및 쓰기 대기 시간과 처리량 및 일관성을 통해 전역적으로 실시간 액세스가 가능하며 모두 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)에서 지원됩니다.
- 단추를 클릭하여 모든 Azure 지역에 다중 지역 쓰기 및 데이터 배포.
- 예측 불가능한 트래픽 버스트 중에도 모든 Azure 지역에서 스토리지 및 처리량을 독립적이고 탄력적으로 확장하여 전 세계적으로 무제한 확장할 수 있습니다.

### <a name="simplified-application-development"></a>간소화된 애플리케이션 개발

오픈 소스 API, 다중 SDK, 스키마 없는 데이터 및 운영 데이터에 대한 비 ETL 분석으로 빠르게 빌드하세요.

- Azure Functions, IoT Hub, AKS(Azure Kubernetes Service), App Service 등을 비롯한 최신(클라우드 네이티브) 앱 개발에 사용되는 주요 Azure 서비스와 긴밀하게 통합됩니다.
- 네이티브 Core(SQL) API, API for MongoDB, Cassandra API, Gremlin API 및 Table API를 비롯한 여러 데이터베이스 API 중에서 선택합니다.
- .NET, Java, Node.js 및 Python용 SDK에서 선택한 언어를 사용하여 Core(SQL) API에서 앱을 빌드하세요. 또는 다른 데이터베이스 API에 대한 드라이버를 선택합니다.
- Azure Synapse Analytics를 사용하여 Azure Cosmos DB에 저장된 거의 실시간으로 작동하는 데이터에 대해 비 ETL 분석을 실행합니다.
- 변경 피드를 사용하면 데이터베이스 컨테이너에 대한 변경 내용을 쉽게 추적 및 관리하고 Azure Functions를 사용하여 트리거된 이벤트를 만들 수 있습니다.
- Azure Cosmos DB의 스키마 없는 서비스는 데이터 모델에 관계없이 모든 데이터를 자동으로 인덱싱하여 매우 빠른 쿼리를 제공합니다.

### <a name="mission-critical-ready"></a>중요 업무에 적합

모든 애플리케이션에 대해 비즈니스 연속성, 99.999% 가용성 및 엔터프라이즈 수준 보안을 보장합니다.

- Azure Cosmos DB는 전 세계 업계 최고의 가용성을 포함하는 포괄적인 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 제품군을 제공합니다.
- 자동 데이터 복제를 통해 모든 Azure 지역에 데이터를 쉽게 배포할 수 있습니다. 강력한 일관성을 사용하는 경우 다중 지역 쓰기 또는 RPO 0으로 가동 중지 시간을 방지합니다.
- 자체 관리형 키를 사용하여 엔터프라이즈급 미사용 암호화 기능을 이용하세요.
- Azure 역할 기반 액세스 제어는 데이터를 안전하게 유지하고 세밀하게 조정되는 컨트롤을 제공합니다.

### <a name="fully-managed-and-cost-effective"></a>완전 관리형 및 비용 효율성

애플리케이션 및 TCO 요구 사항에 맞는 서버리스 및 자동 확장을 통해 엔드투엔드 데이터베이스 관리

- 완전 관리형 데이터베이스 서비스. 자동, 노 터치, 유지 관리, 패치 및 업데이트를 통해 개발자 시간과 비용을 절감합니다.
- 모든 크기 또는 규모를 예측할 수 없거나 간헐적인 워크로드를 위한 비용 효율적인 옵션을 통해 개발자는 용량을 계획하거나 관리할 필요 없이 쉽게 시작할 수 있습니다.
- 서버리스 모델은 급증하는 워크로드에 자동 및 응답성 서비스를 제공하여 주문형 트래픽 버스트를 관리합니다.
- 프로비저닝된 처리량을 자동으로 확장하여 예측할 수 없는 워크로드에 대해 용량을 즉시 확장하는 동시에 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)를 유지합니다.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Azure Cosmos DB를 활용하는 솔루션

다양한 데이터에 대한 거의 실시간에 가까운 응답 시간을 통해 [전 세계 규모](distribute-data-globally.md)에서 대량의 데이터, 읽기 및 쓰기를 처리해야 하는 모든 [웹, 모바일, 게임 및 IoT 애플리케이션](use-cases.md)은 Cosmos DB에서 [보장하는 고가용성](https://azure.microsoft.com/support/legal/sla/cosmos-db/), 높은 처리량, 짧은 대기 시간 및 튜닝 가능한 일관성의 이점을 누릴 수 있습니다. Azure Cosmos DB를 [IoT 및 전자 통신 정보](use-cases.md#iot-and-telematics), [소매 및 마케팅](use-cases.md#retail-and-marketing), [게임](use-cases.md#gaming), [웹 및 모바일 애플리케이션](use-cases.md#web-and-mobile-applications)에 적용하는 방법에 대해 알아보세요.

## <a name="next-steps"></a>다음 단계

다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

- Azure Cosmos DB에서 [API를 선택하는 방법](choose-api.md) 알아보기
- [Azure Cosmos DB SQL API 시작](create-sql-api-dotnet.md)
- [Azure Cosmos DB의 MongoDB API 시작](mongodb/create-mongodb-nodejs.md)
- [Azure Cosmos DB Cassandra API 시작](cassandra/manage-data-dotnet.md)
- [Azure Cosmos DB Gremlin API 시작](create-graph-dotnet.md)
- [Azure Cosmos DB Table API 시작](table/create-table-dotnet.md)
- [Azure Cosmos DB를 사용한 차세대 앱 개발에 대한 백서](https://azure.microsoft.com/resources/microsoft-azure-cosmos-db-flexible-reliable-cloud-nosql-at-any-scale/)
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요?
    - 기존 데이터베이스 클러스터의 vcore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)
