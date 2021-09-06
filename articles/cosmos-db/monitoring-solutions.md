---
title: 타사 모니터링 도구를 사용한 Azure Cosmos DB 모니터링
description: 이 문서에서는 타사 도구 모니터링을 통한 Cosmos DB 모니터링에 대해 설명합니다.
author: manishmsfte
ms.author: mansha
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: b78d6a4c92ad96c15d55332aac320bad09c58c1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566252"
---
# <a name="monitoring-azure-cosmos-db-using-third-party-solutions"></a>타사 솔루션을 사용한 Azure Cosmos DB 모니터링
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor 외에도 타사 모니터링 솔루션을 사용하여 Cosmos DB 인스턴스를 모니터링할 수 있습니다.

> [!IMPORTANT] 
> 이 문서에서 언급한 솔루션은 정보 목적으로만 사용되며 소유권은 개별 솔루션 소유자에게 있습니다. 철저한 평가를 통해 자신에게 가장 적합한 것을 선택하는 것이 좋습니다.

## <a name="datadog"></a>Datadog
{Supports: SQL API, Azure Cosmos DB API for MongoDB, Gremlin API, Cassandra API & Table API}

[Datadog](https://www.datadoghq.com/)은 인프라 모니터링, 애플리케이션 성능 모니터링, 로그 관리, 사용자 환경 모니터링 등을 망라하는 완전 통합형 플랫폼입니다. Datadog는 회사 스택의 모든 도구와 서비스 데이터를 통합하여 문제 해결, 성능 최적화, 팀 간 협업을 위한 진정한 단일 소스를 제공합니다.
Datadog의 모든 항목은 동일한 태그 집합으로 구성되므로 특정 문제와 관련한 모든 데이터는 자동으로 상관 관계가 지정됩니다. Datadog은 블라인드 스폿을 제거함으로써 오류 간과의 위험을 줄이고, 진행 중인 서비스 유지 관리의 부담을 완화하며, 디지털 변환을 가속화합니다.

Datadog은 지역별 전체 가용 스토리지, 만든 SQL 데이터베이스 수 등과 같이, 40여 가지의 계기와 메트릭을 CosmosDB에서 수집합니다. 이러한 메트릭은 Datadog Azure 통합을 통해 수집되며 다른 동종 제품보다 40% 빠르게 플랫폼에 표시됩니다. Datadog은 바로 사용할 수 있는 CosmosDB용 대시보드도 제공하므로 CosmosDB 인스턴스의 성능에 대한 즉각적인 인사이트를 확보할 수 있습니다. 사용된 총 요청 단위와 같은 플랫폼 수준의 메트릭과, 만든 Cassandra 키스페이스 수와 같은 API 수준 메트릭을 모두 시각화하여 CosmosDB 사용에 대한 이해 수준을 높일 수 있습니다.

다음을 비롯한 다양한 Cosmos DB 고객이 Datadog을 사용하고 있습니다.
- Maersk
- PWC 
- PayScale 
- AllScripts 
- Hearst



:::image type="content" source="./media/monitor-solutions/datadog-demo.gif" alt-text="Datadog 데모" border="false":::
**그림:** 작동 중인 Datadog

유용한 링크:
- [가격 정보](https://www.datadoghq.com/pricing/)
- [14일 평가판 시작](https://www.datadoghq.com/free-datadog-trial/)


## <a name="dynatrace"></a>Dynatrace
{지원: SQL API & Azure Cosmos DB API for MongoDB}

[Dynatrace](https://www.dynatrace.com/platform/)는 클라우드 복잡성을 넘어 디지털 변환을 가속화할 수 있는 클라우드를 위한 소프트웨어 인텔리전스를 제공합니다. Dynatrace는 대규모의 자동 및 지능형 관찰을 구현하는 종합 소프트웨어 인텔리전스 플랫폼으로, 애플리케이션의 성능 및 보안, 기본 인프라, 모든 사용자 환경에 대한 정확한 답을 제시하므로 팀이 클라우드 작업을 자동화하고, 더 나은 소프트웨어를 더 신속하게 출시하며, 최강의 디지털 환경을 제공할 수 있습니다.  
Dynatrace는 Mongo API를 사용하여 CosmosDB 메트릭을 수집하여 전달합니다. 여기에는 호출 수 및 응답 시간이 포함되며 모두 집계, 명령, 읽기 및 쓰기 작업에 따라 시각화됩니다.  사용자 환경에서 실행되는 정확한 데이터베이스 문을 알려 주기도 합니다.  마지막으로, 성능 저하의 근본 원인이 되는 데이터베이스 문이 무엇인지 정확히 검색하고 근본 원인으로 확인된 데이터베이스를 볼 수 있는 [Davis AI Engine](https://www.dynatrace.com/davis)이 있습니다.

:::image type="content" source="./media/monitor-solutions/dynatrace-demo.gif" alt-text="Cosmos DB 모니터링 정보를 제공하는 Dynatrace의 다양한 화면" border="false":::
**그림:** 작동 중인 Dynatrace

### <a name="useful-links"></a>유용한 링크

- [Dynatrace 15일 평가판 사용해 보기](https://www.dynatrace.com/trial)
- [Azure Marketplace에서 시작](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dynatrace.dynatrace-managed)
- [Cosmos DB를 Azure Monitor에 사용하는 방법과 관련한 설명서](https://www.dynatrace.com/support/help/technology-support/cloud-platforms/microsoft-azure-services/set-up-integration-with-azure-monitor/?_ga=2.184080354.559899881.1623174355-748416177.1603817475)
- [Cosmos DB - Dynatrace 통합 세부 정보](https://www.dynatrace.com/news/blog/azure-services-explained-part-4-azure-cosmos-db/?_ga=2.185016301.559899881.1623174355-748416177.1603817475)
- [Azure 데이터베이스용 Dynatrace 모니터링](https://www.dynatrace.com/technologies/azure-monitoring/azure-database-performance/)
- [Azure 솔루션용 Dynatrace 개요](https://www.dynatrace.com/technologies/azure-monitoring/)
- [솔루션 파트너](https://www.dynatrace.com/partners/solution-partners/)

## <a name="next-steps"></a>다음 단계
- [Cosmos DB 모니터링 데이터 참조](./monitor-cosmos-db-reference.md)
