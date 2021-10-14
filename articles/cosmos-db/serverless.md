---
title: Azure Cosmos DB의 사용량 기반 서버리스 제품
description: Azure Cosmos DB의 사용량 기반 서버리스 제품에 대한 자세한 정보를 알아봅니다.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: de2ef5b76e67ae19eca6bbb24c9d65dfc7b602b6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987312"
---
# <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB 서버리스
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 서버리스를 통해 데이터베이스 작업에서 사용한 요청 단위 및 데이터에서 사용하는 스토리지에 대해서만 요금이 부과되는 사용량 기반 방식으로 Azure Cosmos 계정을 사용할 수 있습니다. 서버리스 컨테이너는 최소 요금과 용량 계획 없이도 초당 수천 개의 요청을 처리할 수 있습니다.

> [!IMPORTANT] 
> 서버리스에 대한 피드백이 있나요? 많은 의견 부탁드립니다. 다음 주소를 통해 Azure Cosmos DB 서버리스 팀에 메시지를 보내세요. [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com).

Azure Cosmos DB를 사용하는 경우 모든 데이터베이스 작업에는 [요청 단위](request-units.md)로 표현된 비용이 있습니다. 이 비용이 부과되는 방법은 사용하는 Azure Cosmos 계정 유형에 따라 다음과 같이 달라집니다.

- [프로비저닝된 처리량](set-throughput.md) 모드에서는 데이터베이스 및 컨테이너에 프로비저닝되는 특정 처리량(초당 요청 단위로 표현)으로 커밋해야 합니다. 그런 다음 매 초마다 사용 가능한 요청 단위 수에서 데이터베이스 작업 비용을 공제합니다. 청구 기간이 끝나면 프로비저닝한 처리량에 대한 요금이 청구됩니다.
- 서버리스 모드에서는 Azure Cosmos 계정에 컨테이너를 만들 때 처리량을 프로비저닝할 필요가 없습니다. 청구 기간이 종료되면 데이터베이스 작업에 사용된 요청 단위 수에 대한 요금이 청구됩니다.

## <a name="use-cases"></a>사용 사례

Azure Cosmos DB 서버리스의 경우 유휴 시간이 길며 **일시적이고 예측할 수 없는 트래픽** 이 예상되는 시나리오에 가장 적합합니다. 다음과 같은 사용 사례에서는 프로비저닝 용량이 필요하지 않은 데다 비용이 높을 수 있기 때문에 Azure Cosmos DB 서버리스를 사용할 지를 고려해야 합니다.

- Azure Cosmos DB 시작
- 버스티,
    - 예측하기 어려운 일시적인 트래픽 또는
    - 낮은(<10%) 평균 대 최고 트래픽 비율로 애플리케이션 실행
- 트래픽 패턴을 알 수 없는 새로운 애플리케이션 프로덕션에서 개발, 테스트, 프로토타입 작성 및 실행
- [Azure Functions](../azure-functions/functions-overview.md)와 같은 서버리스 컴퓨팅 서비스와 통합

사용 사례에 가장 적합한 제품을 선택하는 방법에 대한 자세한 참고 자료는 [프로비저닝된 처리량과 서버리스 중에서 선택하는 방법](throughput-serverless.md) 문서를 참조하세요.

## <a name="using-serverless-resources"></a>서버리스 리소스 사용

서버리스는 새 Azure Cosmos 계정 유형입니다. 즉, 새 계정을 만들 때 **프로비저닝된 처리량** 과 **서버리스** 중에서 선택해야 합니다. 서버리스 상태로 시작하려면 새 서버리스 계정을 만들어야 합니다. 서버리스 모드로의 또는 서버리스 모드에서의 기존 계정 마이그레이션은 현재 지원되지 않습니다.

서버리스 계정에서 만든 컨테이너는 서버리스 컨테이너입니다. 서버리스 컨테이너는 프로비저닝된 처리량 모드로 만든 컨테이너와 동일한 기능을 제공하므로 정확히 동일한 방법으로 데이터를 읽고 쓰고 쿼리합니다. 그러나 서버리스 계정 및 컨테이너에는 다음과 같은 특정 특성도 있습니다.

- 서버리스 계정은 단일 Azure 지역에서만 실행할 수 있습니다. 계정을 만든 후에는 서버리스 계정에 Azure 영역을 더 추가할 수 없습니다.
- 서버리스 컨테이너에는 프로비저닝 처리량이 필요하지 않으므로 다음 문이 적용됩니다.
    - 서버리스 컨테이너를 만들 때 처리량을 전달할 수 없으며 그렇게 하면 오류가 반환됩니다.
    - 서버리스 컨테이너에서는 처리량을 읽거나 업데이트할 수 없으며 그렇게 하면 오류가 반환됩니다.
    - 서버리스 계정에서 공유 처리량 데이터베이스를 만들 수 없으며 그렇게 하면 오류가 반환됩니다.
- 서버리스 컨테이너는 최대 50GB의 데이터와 인덱스를 저장할 수 있습니다.

## <a name="monitoring-your-consumption"></a>사용량 모니터링

이전에 프로비저닝된 처리량 모드에서 Azure Cosmos DB를 사용했다면, 트래픽이 프로비저닝된 용량을 양쪽으로 맞추지 않을 때는 서버리스를 사용하는 것이 더 비용 효율적입니다. 하지만 데이터베이스가 처리한 요청 수를 기준으로 요금이 청구되므로 비용을 예측하기가 더 어렵게 됩니다. 따라서 현재 소비를 파악하고 있는 것이 중요합니다.

계정에서 **메트릭** 창을 검색할 때 **개요** 탭에서 **사용한 요청 단위** 라는 차트를 찾을 수 있습니다. 다음 차트는 계정이 사용한 요청 단위 수를 보여 줍니다.

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="사용한 요청 단위를 보여 주는 차트" border="false":::

Azure Monitor를 사용하는 경우 [여기](monitor-request-unit-usage.md)에 설명된 대로 동일한 차트를 찾을 수 있습니다. Azure Monitor를 사용하면 요청 단위 사용량이 특정 임계값을 통과한 경우 사용자에게 알리는 데 사용할 수 있는 [경고](../azure-monitor/alerts/alerts-metric-overview.md)를 설정할 수 있습니다.

## <a name="performance"></a><a id="performance"></a>성능

서버리스 리소스는 프로비저닝된 처리량 리소스가 제공하는 것과는 다른 특정 성능 특성을 제공합니다. 서버리스 컨테이너의 대기 시간에는 지점 읽기의 경우 10밀리초 이하이고 쓰기는 30밀리초 이하인 SLO(서비스 수준 목표)가 적용됩니다. 지점 읽기 작업은 ID 및 파티션 키 값을 기준으로 단일 항목을 가져오는 것으로 구성됩니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 서버리스 상태로 시작하세요.

- [Azure Cosmos DB의 요청 단위](request-units.md)
- [프로비저닝된 처리량과 서버리스 중에서 선택](throughput-serverless.md)
- [Azure Cosmos DB의 가격 책정 모델](how-pricing-works.md)
