---
title: Azure Cosmos DB 그래프 데이터를 위한 타사 데이터 모델링 도구
description: 이 문서에서는 Graph 데이터 모델을 디자인하는 다양한 도구에 대해 설명합니다.
author: mansha
ms.author: mansha
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.reviewer: sngun
ms.openlocfilehash: 224465fe282b086c2158f4d81ee67e5bf7cedc09
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771786"
---
# <a name="third-party-data-modeling-tools-for-azure-cosmos-db-graph-data"></a>Azure Cosmos DB 그래프 데이터를 위한 타사 데이터 모델링 도구

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

데이터 모델은 디자인하여 유지 관리하는 것이 중요합니다. 다음은 그래프 데이터 모델을 디자인하고 유지 관리하는 데 도움이 되는 타사의 시각적 디자인 도구 모음입니다.

> [!IMPORTANT] 
> 이 문서에서 언급한 솔루션은 정보 목적으로만 사용되며 소유권은 개별 솔루션 소유자에게 있습니다. 철저한 평가를 통해 자신에게 가장 적합한 것을 선택하는 것이 좋습니다.

## <a name="hackolade"></a>Hackolade

Hackolade는 NoSQL 데이터베이스를 위한 데이터 모델링 및 스키마 디자인 도구입니다. 이 도구에는 미사용 데이터 및 이동 중인 데이터에 대한 스키마 관리에 유용한 데이터 모델링 스튜디오가 있습니다.

### <a name="how-it-works"></a>작동 방식
이 도구는 꼭짓점/모서리와 해당 속성의 데이터 모델링을 제공합니다.  몇 가지 사용 사례를 지원하며, 그중 일부는 다음과 같습니다.
-   빈 페이지에서 시작하여 Cosmos DB Gremlin 모델을 그래픽으로 빌드하는 다양한 옵션을 생각해 보세요.  그런 다음, 모델을 Azure 인스턴스로 포워드 엔지니어링하여 결과를 평가하면서 계속 발전시킵니다.  한 줄의 코드를 작성하지 않고도 이러한 모든 기능을 활용할 수 있습니다.
-   Azure에서 기존 그래프를 리버스 엔지니어링하여 구조를 명확하게 이해할 수 있으므로 그래프를 효과적으로 쿼리할 수도 있습니다.  그런 다음, 설명, 메타데이터 및 제약 조건으로 데이터 모델을 보강하면서 문서를 생성합니다. 이 기능은 HTML, Markdown 또는 PDF 형식을 지원하고 회사 데이터 거버넌스 또는 사전 시스템에 피드합니다.
-   데이터 구조의 비정규화를 통해 관계형 데이터베이스에서 NoSQL로 마이그레이션합니다.
-   명령줄 인터페이스를 통해 CI/CD 파이프라인과 통합
-   Git을 사용한 협업 및 버전 관리
-   기타 기능...

### <a name="sample"></a>샘플

그림 2의 애니메이션은 RDBMS에서 엔터티를 추출하는 리버스 엔지니어링 데모를 제공합니다. 여기서 Hackolade는 외래 키 관계에서 관계를 검색한 다음, 수정합니다.

[여기](https://github.com/Azure-Samples/northwind-ddl-sample/blob/main/nw.sql)에서 SQL Server 원본으로 사용할 수 있는 샘플 DDL 참조   


:::image type="content" source="./media/graph-modeling-tools/hackolade-screenshot.jpg" alt-text="Graph 다이어그램":::
**그림-1:** Graph 다이어그램(그래프 데이터 모델에서 추출)

데이터 모델을 수정한 후 이 도구는 최적의 인덱스가 생성되도록 사용자 지정 Cosmos DB 인덱스 스크립트를 포함할 수 있는 gremlin 스크립트를 생성할 수 있습니다. 전체 흐름은 그림-2를 참조하세요.

다음 이미지는 실행 중인 RDBMS 및 Hackolade의 리버스 엔지니어링을 보여 줍니다. :::image type="content" source="./media/graph-modeling-tools/cosmos-db-gremlin-hackolade.gif" alt-text="실행 중인 Hackolade":::

**그림-2:** 실행 중인 Hackolade(SQL에서 Gremlin로의 데이터 모델 변환 예시)
### <a name="useful-links"></a>유용한 링크 
-   [14일 평가판 다운로드](https://hackolade.com/download.html)
-   [데모 예약](https://c.x.ai/pdesmarets)
-  [추가 데이터 모델 가져오기](https://hackolade.com/samplemodels.html#cosmosdb)
-  [Hackolade 설명서](https://hackolade.com/help/CosmosDBGremlin.html)

## <a name="next-steps"></a>다음 단계
- [데이터 시각화](./graph-visualization-partners.md)