---
title: Azure Cosmos DB Graph API를 사용하는 Infosys 공급망 추적 가능성 솔루션
description: Infosys에서 구현된 공급망 추적 가능성 그래프 솔루션은 Azure Cosmos DB Gremlin API 및 기타 Azure 서비스를 사용합니다. 완제품에 대한 글로벌 공급망 추적 및 추적 기능을 제공합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/07/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 4ec236a57e9c8f24625d22f4af3f39299d118804
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129717708"
---
# <a name="supply-chain-traceability-solution-using-azure-cosmos-db-graph-api"></a>Azure Cosmos DB Graph API를 사용하는 공급망 추적 가능성 솔루션

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

이 문서에서는 [Infosys 에서 구현하는 추적 가능성 그래프 솔루션에 대한 개요를](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)제공합니다. 이러한 솔루션은 Azure Cosmos DB Gremlin API 및 기타 Azure 기능을 사용하여 완제품에 대한 글로벌 공급망 추적 및 추적 기능을 제공합니다.

이 문서를 읽은 후 다음을 알아봅니다.

* 공급망의 컨텍스트에서 추적 가능성이란?
* Azure 기능을 사용하여 제공되는 글로벌 추적 가능성 솔루션의 아키텍처입니다.  
* Azure Cosmos DB 그래프 데이터베이스를 통해 글로벌 공급망에서 원재료와 완제품 간의 관계를 복잡하게 만들 수 있는 방법은 무엇인가요?
* API Management, Event Hub와 같은 Azure 통합 플랫폼 서비스는 다양한 공급망 애플리케이션 에코시스템을 통합하는 데 어떻게 도움이 됩니까?
* Infosys에서 추적 가능성 요구 사항으로 이 솔루션을 사용하기 위해 어떻게 도움을 받을 수 있나요?

## <a name="overview"></a>개요

식품 공급망에서 제품 추적 기능은 제품 수명 주기 전체에서 공급망에서 이를 '추적 및 추적'하는 기능입니다. 공급망에는 공급, 제조 및 분포가 포함됩니다. 추적 가능성은 음식 안전, 브랜드 및 규제 노출에 매우 중요합니다. 과거에는 일부 조직에서 공급망에서 제품을 효과적으로 추적하고 추적하지 못하여 고가의 회수, 통계 및 소비자 상태 문제가 발생했습니다.

애플리케이션 서비스, 통합 서비스 및 데이터베이스 서비스와 같은 Azure 기능을 사용하여 개발된 Infosys의 추적 가능성 솔루션은 다음을 위한 중요한 기능을 제공합니다.

* 공장, 웨어하우스/배포 센터에 커넥트.
* 병렬 재고 이동 이벤트를 처리/처리합니다.
* 지식 그래프 - 원재료, 일괄 처리, FG(마침 상품) 자식 집합, 다단계 부모/자식 자식 관계, 상품 이동 간의 연결을 보여줍니다.
* 검색을 사용하여 추적 및 추적 세트가 있는 포털.
* 영향을 받는 원시 자재 일괄 처리, 영향을 받는 팔레트, 팔레트의 위치와 같은 품질 인시던트의 영향을 식별합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

공급망 추적 가능성은 일반적으로 팔레트 이동 수집, 품질 인시던트 전달 및 저장소 데이터 추적/분석의 패턴을 공유합니다. 첫째, 이러한 시스템은 여러 지역에 분산된 공장/웨어하우스 관리 시스템 시스템에서 데이터의 버스트를 수집해야 합니다. 다음으로, 이러한 시스템은 스트리밍 데이터를 처리하고 분석하여 원재료, 프로덕션 일괄 처리, 완성된 양호한 토켓 및 복잡한 부모/자식 관계(공동 압축/재패키지) 간의 복잡한 관계를 파생합니다. 그런 다음 시스템에서는 추적에 필요한 원재료, 완제품, 팔레트 간의 복잡한 관계를 저장해야 합니다. 검색 기능이 있는 사용자 포털을 사용하면 사용자가 공급망 네트워크에서 제품을 추적하고 추적할 수 있습니다.

Microsoft Azure Azure Cosmos DB, Azure Event Hubs, Azure API Management, Azure App Service, Azure SignalR, Azure Synapse Analytics 및 Power BI 포함하여 추적 가능성 사용 사례에 적용할 수 있는 풍부한 서비스를 제공합니다.

Infosys의 추적 가능성 솔루션은 추적 및 추적 기능을 개선하는 데 사용할 수 있는 미리 베이킹된 솔루션을 제공합니다. 다음 이미지에서는 이 추적 가능성 솔루션에 사용되는 아키텍처를 설명합니다.

:::image type="content" source="./media/supply-chain-traceability-solution/solution-architecture.png" alt-text="공급망 추적 가능성 솔루션 아키텍처" lightbox="./media/supply-chain-traceability-solution/solution-architecture.png" border="false":::

이 아키텍처에 사용되는 다양한 Azure 서비스는 다음 작업에 도움이 됩니다.

* Azure Cosmos DB를 사용하면 성능을 탄력적으로 확장하거나 축소할 수 있습니다. Gremlin API를 사용하면 원자재, 완제품 및 웨어하우스 간의 복잡한 관계를 만들고 쿼리할 수 있습니다.
* Azure API Management 3CL(thirdpParty Logistic Providers) 및 WMS(웨어하우스 관리 시스템)에 주식 이동 이벤트에 대한 API를 제공합니다.  
* Azure Event Hub는 추가 처리를 위해 WMS 및 3PL에서 많은 수의 동시 이벤트를 수집하는 기능을 제공합니다.
* Azure Function 앱은 그래프 API를 사용하여 이벤트를 처리하고 Azure Cosmos DB에 데이터를 수집합니다.
* Azure Search Service 사용하면 복잡한 찾기, 필터링 팔레트 정보를 수행할 수 있습니다.
* Azure Databricks 변경 피드를 읽고 Power BI 사용자를 위한 셀프 서비스 보고를 위해 Synapse Analytics 모델을 만듭니다.
* Azure Web App 및 App Service 계획을 사용하면 사용자 포털을 배포할 수 있습니다.
* Azure Storage 계정은 장기 규정 요구 사항을 위해 보관된 데이터를 저장합니다.

## <a name="graph-db-and-its-data-design"></a>Graph DB 및 해당 데이터 디자인

상품의 생산 및 배포에는 복잡하고 동적인 관계 집합을 유지 관리해야 합니다.  추적 가능성 그래프의 적응형 데이터 모델을 사용하면 원자재 수령부터 시작하여 이러한 관계를 저장하고, 공장의 완제품을 제조하고, 공급망 중에 다른 웨어하우스로 이전하고, 마지막으로 고객 웨어하우스로 이전할 수 있습니다. 프로세스의 고급 시각화는 다음 이미지와 같습니다.

:::image type="content" source="./media/supply-chain-traceability-solution/data-design-visualization.png" alt-text="공급망 데이터 디자인 시각화" lightbox="./media/supply-chain-traceability-solution/data-design-visualization.png" border="true":::

위의 다이어그램은 복잡한 공급망 프로세스에 대한 간략하고 간소화된 뷰를 보여줍니다. 그러나 공장 및 웨어하우스에서 중요한 주식 이동 정보를 실시간으로 파악하면 이러한 서로 다른 모든 정보를 연결하는 정교한 그래프를 만들 수 있습니다.

1. 추적 가능성 프로세스는 공급자가 공장에 원자재를 보내고 그래프 및 관계(에지)의 초기 노드(꼭짓점)가 만들어질 때 시작됩니다.

1. 완제품(항목)은 원재료에서 생산되어 세트로 포장됩니다.

1. 그러면 고객 요구/주문에 따라 공장 웨어하우스 또는 시장 웨어하우스로 이동됩니다.

1. 웨어하우스는 회사 소유 또는 3PL(타사 물류 공급자)일 수 있습니다. 그러면 고객 주문에 따라 다양한 다른 웨어하우스로 배송됩니다. 고객의 요구에 따라, 주문된 수량을 수용하기 위해 자식 팔레트 또는 자식 자식 팔레트가 만들어집니다. 경우에 따라 여러 항목을 혼합하여 완전히 새로운 항목을 만들 수 있습니다. 예를 들어 다양한 팩을 생성하는 copack 시나리오에서 동일한 항목이 고객 주문의 일부로 더 작거나 더 큰 수량으로 다른 팔레트로 다시 압축되는 경우가 있습니다.

   :::image type="content" source="./media/supply-chain-traceability-solution/pallet-relationship.png" alt-text="공급망 추적 가능성 솔루션의 팔레트 관계" lightbox="./media/supply-chain-traceability-solution/pallet-relationship.png" border="true":::

1. 그런 다음, 팔레트는 공급망 네트워크를 통해 이동하고 결국 고객 웨어하우스에 도달합니다. 이 프로세스 중에는 팔레트를 더 세분화하거나 다른 팔레트와 결합하여 고객 주문을 이행하기 위한 새 팔레트를 생성할 수 있습니다.

1. 결국 시스템은 품질 인시던트 관리를 위한 중요한 관계 정보를 포함하는 복잡한 그래프를 만듭니다. 이 내용은 곧 설명하겠습니다.

   :::image type="content" source="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" alt-text="공급망 개체 관계 전체 아키텍처" lightbox="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" border="true":::

1. 이러한 복잡한 관계는 시스템이 공급망 전체에서 팔레트를 추적하고 추적할 수 있는 품질 인시던트에서 매우 중요합니다. Graph 및 해당 순회는 이에 필요한 정보를 제공합니다. 예를 들어, 하나의 원재료에 문제가 있는 경우 그래프는 영향을 받은 팔레트, 현재 위치를 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Infosys 추적 가능성 그래프 솔루션](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)
* 그래프 데이터를 시각화하려면 [Gremlin API 시각화 솔루션 를 참조하세요.](graph-visualization-partners.md)
* 그래프 데이터를 모델링하려면 [Gremlin API 모델링 솔루션을 참조하세요.](graph-modeling-tools.md)