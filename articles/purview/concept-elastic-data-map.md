---
title: 탄력적 데이터 맵
description: 이 문서에서는 Azure Purview의 탄력적 데이터 맵 개념을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.custom: template-concept
ms.openlocfilehash: fd667abab0802d6d11fc855a620bf51f8419b978
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555767"
---
# <a name="elastic-data-map-in-azure-purview"></a>Azure Purview의 탄력적 데이터 맵

Azure Purview 데이터 맵은 데이터 검색과 데이터 거버넌스를 위한 기반을 제공합니다. 분석, SaaS(Software-as-a-Service)와 하이브리드, 온-프레미스, 다중 클라우드 환경의 운영 체제에 있는 엔터프라이즈 데이터에 관한 메타데이터를 캡처합니다. Purview 데이터 맵은 기본 제공 검사 및 분류 시스템을 통해 자동으로 최신 상태로 유지됩니다. UI를 통해 개발자는 오픈 소스 Apache Atlas API를 사용하여 프로그래밍 방식으로 데이터 맵과 추가로 상호 작용할 수 있습니다.

## <a name="elastic-data-map"></a>탄력적 데이터 맵

모든 Azure Purview 계정에는 1개 용량 단위에서 시작하여 탄력적으로 증가할 수 있는 데이터 맵이 있습니다. 탄력성 기간 내의 요청 부하에 따라 스케일 업 및 다운됩니다([현재 한도 확인](how-to-manage-quotas.md)). 이러한 제한은 대부분의 데이터 환경을 다루어야 합니다. 그러나 더 높은 용량이 필요한 경우 [지원 티켓을 만들 수 있습니다.](#request-capacity)

## <a name="data-map-capacity-unit"></a>데이터 맵 용량 단위

탄력적 데이터 맵은 CU(용량 단위)로 표시되는 작업 처리량 및 스토리지 구성 요소가 제공됩니다. 기본적으로 모든 Azure Purview 계정은 1개 용량 단위가 제공되며 사용량에 따라 탄력적으로 증가합니다. 각 데이터 맵 용량 단위에는 초당 25개 작업의 처리량과 2GB의 메타데이터 스토리지 한도가 포함됩니다.  

### <a name="operations"></a>작업

작업은 Purview 데이터 맵의 처리량 측정값입니다. 여기에는 데이터 맵에 저장된 메타데이터에 대한 만들기, 읽기, 쓰기, 업데이트, 삭제 작업이 포함됩니다. 작업의 몇 가지 예제는 다음과 같이 나열됩니다.

- 데이터 맵에 자산 만들기
- 소유자, 관리자, 부모, 계보 등의 관계를 자산에 추가합니다.
- 자산을 편집하여 설명, 용어집 용어 등의 비즈니스 메타데이터를 추가합니다.
- 검색 결과 페이지에 결과를 반환하는 키워드 검색입니다.

### <a name="storage"></a>Storage

스토리지는 데이터 맵의 두 번째 구성 요소이며 기술, 비즈니스, 운영, 의미 체계 메타데이터를 포함합니다.

기술 메타데이터에는 Purview [검사](concept-scans-and-ingestion.md)에서 검색되는 스키마, 데이터 형식, 열 등이 포함됩니다. 비즈니스 메타데이터에는 설명, 용어집 용어 등의 자동화된(예: Power BI 데이터 세트 또는 SQL 테이블의 설명에서 승격됨) 태그 지정과 수동 태그 지정이 포함됩니다. 의미 체계 메타데이터의 예로는 데이터 원본이나 분류에 대한 컬렉션 매핑이 있습니다. 운영 메타데이터에는 데이터 팩터리 복사 및 데이터 흐름 활동 실행 상태와 실행 시간이 포함됩니다.

## <a name="work-with-elastic-data-map"></a>탄력적 데이터 맵 작업

- **자동 스케일링을 사용하는 탄력적 데이터 맵** – 부하에 따라 자동 스케일링할 수 있는 1개 용량 단위만큼 낮은 데이터 맵으로 시작합니다. 대부분의 조직에서 이 기능을 사용하면 데이터 거버넌스 프로젝트 시작과 관련된 절감액이 증가하고 가격대가 낮아집니다. 이 기능은 가격 책정에 영향을 미칩니다.

- **향상된 검사 및 수집** – 데이터 자산의 모집단과 검사 및 수집 프로세스의 분류와 계보를 추적하고 제어할 수 있습니다. 이 기능은 가격 책정에 영향을 미칩니다.

## <a name="scenario"></a>시나리오

Claudia는 Contoso의 Azure 관리자로서, Azure Portal에서 새 Azure Purview 계정을 프로비저닝하려고 합니다. 프로비저닝하는 동안 플랫폼의 미래 상태를 지원하는 데 필요한 Purview 데이터 맵 크기를 알지 못합니다. 그러나 Purview 데이터 맵 요금이 스토리지 및 작업 처리량의 영향을 받는 용량 단위로 청구된다는 것을 알고 있습니다. 비용을 낮게 유지하고 사용량에 따라 탄력적으로 데이터 맵 크기를 늘리기 위해 가장 작은 데이터 맵을 프로비저닝하려고 합니다.  

Claudia는 자동으로 스케일 업 및 다운될 수 있는 1개 용량 단위의 기본 데이터 맵 크기로 Purview 계정을 만들 수 있습니다. 또한 자동 스케일링 기능을 사용하면 특정 기간 동안 일시적 또는 계획된 데이터 버스트에 따라 용량을 튜닝할 수 있습니다. Claudia는 프로비저닝 환경에서 다음 단계를 수행하여 네트워크 구성을 설정하고 프로비저닝을 완료합니다.  

Azure Monitor 메트릭 페이지에서 Claudia는 데이터 맵 스토리지 및 작업 처리량의 사용량을 확인할 수 있습니다. 스토리지 또는 작업 처리량이 새 Purview 계정의 사용량과 청구를 모니터링하기 위한 특정 한도에 도달할 때 경고를 추가로 설정할 수 있습니다.  

## <a name="data-map-billing"></a>데이터 맵 청구

고객은 1개 용량 단위(초당 25개 작업 및 2GB)에 대해 요금이 청구되며, 추가 청구는 해당 시간까지 롤업된 각 추가 용량 단위의 사용량을 기준으로 합니다. 데이터 맵 작업은 초당 25개 작업의 증분으로 스케일링되고 메타데이터 스토리지는 2GB 크기의 중분으로 스케일링됩니다. Purview 데이터 맵은 탄력성 기간 내에서 자동으로 스케일 업 및 다운될 수 있습니다([현재 한도 확인](how-to-manage-quotas.md)). 그러나 다음 수준의 탄력성 기간을 가져오려면 지원 티켓을 만들어야 합니다.

데이터 맵 용량 단위에는 작업 처리량과 스토리지에 대한 한도가 제공됩니다. 스토리지가 현재 용량 단위를 초과하면 작업 처리량이 사용되지 않더라도 고객에게 다음 용량 단위에 대한 요금이 청구됩니다. 아래 표에는 데이터 맵 용량 단위 범위가 표시됩니다. 데이터 맵 용량 단위가 100개 용량 단위를 초과하는 경우에는 고객 지원팀에 문의하세요.

|데이터 맵 용량 단위  |초당 작업 처리량   |스토리지 용량(GB)|
|----------|-----------|------------|
|1    |25      |2     |
|2    |50      |4     |
|3    |75      |6     |
|4    |100     |8     |
|5    |125     |10    |
|6    |150     |12   |
|7    |175      |14     |
|8    |200     |16    |
|9    |225      |18    |
|10    |250     |20    |
|..   |..      |..     |
|100    |2500     |200   |

### <a name="billing-examples"></a>청구 예제

- 지정된 시간 동안 Purview 데이터 맵의 작업 처리량은 초당 25개 작업보다 작거나 같으며 스토리지 크기는 1GB입니다. 고객에게는 1개 용량 단위에 대한 요금이 청구됩니다.

- 지정된 시간 동안 Purview 데이터 맵의 작업 처리량은 초당 25개 작업보다 작거나 같으며 스토리지 크기는 3GB입니다. 고객에게는 2개 용량 단위에 대한 요금이 청구됩니다.

- 지정된 시간 동안 Purview 데이터 맵의 작업 처리량은 초당 50개 작업이며 스토리지 크기는 1GB입니다. 고객에게는 2개 용량 단위에 대한 요금이 청구됩니다.

- 지정된 시간 동안 Purview 데이터 맵의 작업 처리량은 초당 50개 작업이며 스토리지 크기는 5GB입니다. 고객에게는 3개 용량 단위에 대한 요금이 청구됩니다.

- 지정된 시간 동안 Purview 데이터 맵의 작업 처리량은 초당 250개 작업이며 스토리지 크기는 5GB입니다. 고객에게는 10개 용량 단위에 대한 요금이 청구됩니다.

### <a name="detailed-billing-example"></a>자세한 청구 예제

아래 데이터 맵 청구 예제에서는 오후 12시부터 오후 6시까지 6시간 동안 메타데이터 스토리지 증가와 초당 작업 변동이 있는 데이터 맵을 보여 줍니다. 그래프의 빨간색 선은 초당 작업 사용량이며 파란색 점선은 이 6시간 기간 동안 메타데이터 스토리지 사용량입니다.

:::image type="content" source="./media/concept-elastic-data-map/operations-and-metadata.png" alt-text="시간 경과에 따른 작업 수와 메타데이터 증가를 보여 주는 차트":::

각 데이터 맵 용량 단위는 초당 25개 작업과 2GB의 메타데이터 스토리지를 지원합니다. 데이터 맵은 시간 단위로 요금이 청구됩니다. 시간 내에 필요한 최대 데이터 맵 용량 단위에 대한 요금이 청구됩니다. 경우에 따라 시간 내에 더 많은 초당 작업이 필요할 수 있으며 이 경우 해당 시간 내에 필요한 용량 단위 수가 증가합니다. 다른 경우에는 초당 작업 사용량이 낮을 수 있지만 여전히 대용량 메타데이터 스토리지가 필요할 수 있습니다. 메타데이터 스토리지에 따라 시간 내에 필요한 용량 단위 수가 결정됩니다.

아래 표에서는 이 청구 예제에 대해 시간당 사용된 최대 초당 작업 수와 메타데이터 스토리지 수를 보여 줍니다. 

:::image type="content" source="./media/concept-elastic-data-map/billing-table.png" alt-text="시간 경과에 따른 최대 작업 수와 메타데이터 증가를 보여 주는 표":::

이 기간의 데이터 맵 초당 작업과 메타데이터 스토리지 사용량에 따라 해당 데이터 맵에는 6시간 기간 동안 20개 용량 단위 시간에 대한 요금이 청구됩니다(1 + 3 + 4 + 3 + 6 + 3).

:::image type="content" source="./media/concept-elastic-data-map/billing-capacity-hours.png" alt-text="시간 경과에 따른 CU 시간 수를 보여 주는 표":::

>[!Important]
>Purview 데이터 맵은 탄력성 기간 내에서 자동으로 스케일 업 및 다운될 수 있습니다([현재 한도 확인](how-to-manage-quotas.md)). 다음 수준의 탄력성 기간을 가져오려면 지원 티켓을 만들어야 합니다.

## <a name="request-capacity"></a>용량 요청

매우 큰 데이터 집합 또는 대규모 환경에서 작업 하 고 탄력적 데이터 맵에 더 많은 용량이 필요한 경우 [지원 티켓을 만들어](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)탄력성 창의 용량을 더 많이 요청할 수 있습니다.

**서비스 및 구독 제한 (할당량)** 을 선택 하 고 더 많은 용량을 요청 하려는 부서의 범위 계정을 선택 하 여 화면에 표시 되는 지침을 완료 합니다.

:::image type="content" source="./media/concept-elastic-data-map/increase-limit.png" alt-text="제한 증가 옵션이 선택 된 지원 사례 생성을 보여 주는 화면":::

설명에는 사용자 환경에 대 한 정보와 요청 하려는 추가 용량에 대 한 적절 한 정보를 제공 합니다.

## <a name="monitoring-the-elastic-data-map"></a>탄력적 데이터 맵 모니터링

데이터 공간 크기와 요금 청구를 이해 하기 위해 메트릭 _데이터 맵 용량 단위_ 및 _데이터 맵 저장소 크기_ 를 모니터링할 수 있습니다.

1. [Azure Portal](https://portal.azure.com), **Purview 계정** 페이지로 차례로 이동하여 _Purview 계정_ 을 선택합니다.

2. **개요** 를 클릭 하 고 아래로 스크롤하여 _데이터 맵 용량 단위_ 및 _데이터 맵에_ 대 한 **모니터링** 섹션을 관찰 하 고 다른 기간에 대 한 크기 메트릭을 Storage 합니다.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-metrics.png" alt-text="탄력적 데이터 맵 메트릭 개요 페이지를 표시 하는 메뉴의 스크린샷":::

3. 추가 설정을 보려면 **모니터링--> 메트릭으로** 이동 하 여 **데이터 맵 용량 단위** 및 **데이터 맵 Storage 크기** 를 확인 합니다.

    :::image type="content" source="./media/concept-elastic-data-map/elastic-data-map-metrics.png" alt-text="메트릭을 보여 주는 메뉴의 스크린샷":::

4. 최근 24 시간 동안의 데이터 맵 용량 단위 사용량을 보려면 **데이터 맵 용량 단위** 를 클릭 합니다. 선 그래프 위로 마우스를 가져가면 특정 날짜에 해당 시간에 사용 된 데이터 맵 용량 단위가 표시 됩니다.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-default.png" alt-text="24 시간 동안 소비 된 데이터 맵 용량 단위를 보여 주는 메뉴의 스크린샷":::

5. 화면 오른쪽 위에 있는 **현지 시간: 최근 24 시간 (자동-1 시간)** 을 클릭 하 여 그래프에 표시 되는 시간 범위를 수정 합니다.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-custom.png" alt-text="사용자 지정 시간 범위 동안 소비 된 데이터 맵 용량 단위를 보여 주는 메뉴의 스크린샷":::

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-time-range.png" alt-text="3 일 시간 범위 동안 소비 된 데이터 맵 용량 단위를 보여 주는 메뉴의 스크린샷":::

6. 아래에 표시 된 대로 옵션을 클릭 하 여 그래프 유형을 사용자 지정 합니다.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-graph-type.png" alt-text="그래프 유형을 수정 하는 옵션을 보여 주는 메뉴의 스크린샷":::

7. **새 차트** 를 클릭 하 여 데이터 맵에 대 한 그래프 Storage 크기 차트를 추가 합니다.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-storage-size.png" alt-text="사용 된 데이터 맵 저장소 크기를 보여 주는 메뉴의 스크린샷":::

## <a name="summary"></a>요약

탄력적 데이터 맵을 사용하면 Purview는 고객이 데이터 거버넌스 경험을 시작할 수 있는 저렴한 비용의 장벽을 제공합니다.
Purview DataMap은 1개 용량 단위만큼 작게 시작하는 종량제 모델을 통해 탄력적으로 증가할 수 있습니다.
고객은 프로비저닝 시간에 데이터 자산의 올바른 데이터 맵 크기를 선택하는 것을 걱정하고 크기 한도로 인해 향후 플랫폼 마이그레이션을 처리할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 계정 만들기](create-catalog-portal.md)
- [Purview 가격 책정](https://azure.microsoft.com/pricing/details/azure-purview/)
