---
title: 파트너 센터의 서비스 품질 대시보드
description: 파트너 센터 (Azure Marketplace)에서 서비스 품질에 사용할 수 있는 여러 보고서를 표시 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: smannepalle
ms.date: 09/27/2021
ms.openlocfilehash: c7df2a30d3de65966d289c0caad7bc6afaca6afd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088030"
---
# <a name="quality-of-service-qos-dashboard"></a>QoS (서비스 품질) 대시보드

이 대시보드는 모든 제품에 대 한 배포 품질을 표시 합니다. 더 높은 제품 배포 성공은 고객에 게 제공 되는 더 높은 수준의 서비스를 나타냅니다.

다음 항목에 대 한 그래픽 표현을 봅니다.

- [제품 품질](#quality-by-offers)
- [배포 기간별 품질](#quality-by-deployment-duration)
- [배포 수](#deployment-count)
- [상태별 배포 수](#deployment-count-by-status)
- [배포 오류 코드 및 리소스](#deployment-errors-codes-and-resources)
- [제공 요금제 배포 오류](#deployment-errors-by-offer-plan)
- [위치별 배포 안정성](#deployment-reliability-by-location)

또한 [제품 배포 세부 정보](#offer-deployment-details) 를 테이블 형식으로 봅니다.

> [!IMPORTANT]
> 이 대시보드는 현재 모든 사용자가 사용할 수 있는 **Azure 응용 프로그램** 제공에만 사용할 수 있습니다 (비공개 제안 아님).

이 기능은 현재 ARM (Azure Resource Manager) 템플릿을 사용 하는 Azure 응용 프로그램 제품의 배포를 수행 하는 모든 파트너에 게 적용 됩니다 (개인 제안에는 적용 되지 않음). 이 보고서는 다른 marketplace 제품에 대 한 데이터를 표시 하지 않습니다.

## <a name="access-the-quality-of-service-dashboard"></a>서비스 품질 대시보드 액세스

[!INCLUDE [preview interface note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home) 에 로그인

1. 홈 페이지에서 **Insights** 타일을 선택 합니다.

    [![파트너 센터의 Insights 타일을 보여 줍니다. ](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 왼쪽 메뉴에서 **서비스 품질** 을 선택 합니다.

    [![Insights 대시보드의 왼쪽 탐색을 보여 줍니다. ](./media/quality-of-service/insights-overview-workspaces.png) ](./media/quality-of-service/insights-overview-workspaces.png#lightbox)

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home) 에 로그인

1. 왼쪽 탐색 창에서 **상업적 Marketplace**  >  [](https://partner.microsoft.com/dashboard/commercial-marketplace/quality-of-service/summary)  >  **서비스 품질** 분석을 선택 합니다.

---

## <a name="elements-of-the-quality-of-service-dashboard"></a>서비스 품질 대시보드의 요소

다음 섹션에서는 QoS (서비스 품질) 대시보드를 사용 하는 방법 및 데이터를 읽는 방법을 설명 합니다.

### <a name="month-range"></a>월 범위

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

각 페이지의 오른쪽 위 모서리에는 월 범위를 선택할 수 있습니다. 지난 6 개월 또는 12 개월을 기준으로 월 범위를 선택 하거나 최대 기간이 12 개월 인 사용자 지정 월 범위를 선택 하 여 **서비스 품질** 페이지 그래프의 출력을 사용자 지정 합니다. 기본 월 범위 (선택 기간)는 6 개월입니다.

[![서비스 품질 대시보드의 필터를 보여 줍니다. ](./media/quality-of-service/qos-filters-workspaces.png) ](./media/quality-of-service/qos-filters-workspaces.png#lightbox)

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

각 페이지의 오른쪽 위 모서리에는 월 범위를 선택할 수 있습니다. 지난 6 개월 또는 12 개월을 기준으로 월 범위를 선택 하거나 최대 기간이 12 개월 인 사용자 지정 월 범위를 선택 하 여 **서비스 품질** 페이지 그래프의 출력을 사용자 지정 합니다. 기본 월 범위 (선택 기간)는 6 개월입니다.

---

### <a name="quality-by-offers"></a>제품 품질

이 그래프는 제공 서비스 품질 및 해당 Sku를 보여 줍니다. 월별 **전체**, **성공** 및 **실패** 한 제품 배포에 대 한 메트릭 및 추세를 제공 합니다. 가로 막대형 차트는 배포 수를 나타냅니다.

꺾은선형 차트는 다음의 변경 비율을 나타냅니다.

- 총 배포 ( **모두** 탭)
- 성공 배포 ( **성공한** 탭)
- 실패 한 배포 ( **오류** 탭)

차트는 모든 제품의 메트릭 및 추세를 보여 줍니다. 최상위 제품이 그래프에 표시되고 나머지 제품은 **Rest All** 로 그룹화됩니다.

:::image type="content" source="media/quality-of-service/quality-by-offers-1.png" alt-text="제품 그래프의 품질 (버전 1)을 표시 합니다.":::

이 그래프 정보:

- 범례에서 특정 제품을 선택 하 여 해당 제품 및 관련 Sku만 그래프에 표시 합니다.
- 그래프의 조각을 마우스로 가리키면 모든 제품의 총 배포 수와 비교 하 여 해당 제품의 배포 수와 백분율이 표시 됩니다.
- 품질 별 제공 추세는 월 단위 증가 또는 감소 추세를 표시 합니다.

:::image type="content" source="media/quality-of-service/quality-by-offers-2.png" alt-text="제품 품질 그래프, 버전 2를 표시 합니다.":::

이 그래프 정보:

- 표시할 범례의 특정 제안 및 관련 Sku를 선택 합니다.
- 품질 별 제공 추세는 월 단위 메트릭을 표시 합니다.
- 제품에 대 한 월 단위 추세를 볼 때 해당 제품의 최대 3 개 Sku를 선택 합니다.
- 꺾은선형 차트는 이전 그래프에 대해 표시 된 것과 동일한 비율 변경 내용을 나타냅니다.

### <a name="quality-by-deployment-duration"></a>배포 기간별 품질

이 그래프는 성공 및 실패 한 배포에 대 한 평균 기간의 메트릭과 추세를 보여 줍니다. 드롭다운 메뉴에서 제품을 선택 하 여 메트릭을 봅니다. 테이블 형식 뷰에서 SKU를 선택 하거나 검색 창에 입력 합니다. 다음은 다양 한 평균 배포 기간 (분)입니다.

- **성공 기간** – 성공으로 표시 된 제품 배포 상태를 사용 하는 배포 기간의 평균 시간입니다. 이 집계 메트릭은 성공적인 상태로 표시 된 배포의 시작 타임 스탬프와 종료 타임 스탬프 사이에 있는 기간을 사용 하 여 계산 됩니다.
- **실패 기간** – 제공 배포 상태를 실패로 표시 한 배포 기간의 평균 시간입니다. 이 집계 메트릭은 실패 상태로 표시 된 배포의 시작 타임 스탬프와 종료 타임 스탬프 사이의 기간을 사용 하 여 계산 됩니다.
- **첫 번째 성공적인 배포 기간** – 성공으로 표시 된 제품 배포 상태를 사용 하는 배포 기간의 평균 시간입니다. 이 집계 메트릭은 첫 번째 배포의 시작 타임 스탬프와 성공적인 상태로 표시 된 최종 배포의 종료 타임 스탬프 사이의 기간을 사용 하 여 계산 됩니다. 특정 제품 SKU 및 고객에 대해 표시 된 각 배포에 대해 계산 됩니다.

:::image type="content" source="media/quality-of-service/deployment-duration-quality.png" alt-text="배포 기간 품질 그래프를 표시 합니다.":::

이 그래프 정보:

- 범례에서 표시 될 실패, 성공 또는 첫 번째 성공적인 배포 기간을 선택 합니다.
- 선형 그래프는 성공, 실패 및 성공한 배포 성공으로 표시 된 배포의 평균 기간을 실패 한 이전 시도로 표시 합니다.
- 첫 번째 배포에 대 한 평균 시간 장애 시도에 소요 된 시간을 배포 성공으로 표시 합니다.

### <a name="deployment-count"></a>배포 수

이 그래프는 제품의 총 배포를 보여 줍니다. 메트릭 및 증가 추세는 꺾은선형 차트로 표시됩니다. 꺾은선형 차트를 마우스로 가리키면 각 월에 대 한 값을 봅니다.

배포 메트릭 아래의 백분율 값은 선택한 월 범위 동안 증가 또는 거부 되는 정도를 나타냅니다.

:::image type="content" source="media/quality-of-service/deployment-count.png" alt-text="배포 수 그래프를 표시 합니다.":::

이 그래프 정보:

- 선택한 날짜 범위에 대 한 총 제품 배포 수입니다.
- 선택한 날짜 범위 동안 제품 배포의 백분율을 변경 합니다.
- 제품 배포의 총 개수에 대 한 월간 월 추세 추세입니다.

### <a name="deployment-count-by-status"></a>상태별 배포 수

이 그래프는 선택한 월 범위에 대 한 고객의 성공 및 실패 한 제품 배포 메트릭과 추세를 보여 줍니다. 제품 배포는 **성공** 또는 **실패** 의 두 가지 상태를 가질 수 있습니다.

:::image type="content" source="media/quality-of-service/deployment-count-by-status.png" alt-text="상태 그래프 별 배포 수를 표시 합니다.":::

이 그래프 정보:

- 선택한 날짜 범위에 대 한 제안 된 총 성공 및 실패 한 배포 수입니다.
- 선택한 날짜 범위에 대 한 성공 및 실패 한 제공 배포의 백분율을 변경 합니다.
- 성공 및 실패 한 제품 배포 수에 대 한 월별 추세입니다.

### <a name="deployment-errors-codes-and-resources"></a>배포 오류 코드 및 리소스

이 그래프는 제품 배포 기준 오류 코드 및 리소스의 메트릭과 추세를 보여 줍니다. 테이블 형식 섹션은 오류 코드와 리소스에서 피벗 될 수 있습니다. 첫 번째 하위 탭는 분석 기준 오류 코드, 설명 및 오류 개수를 제공 합니다. 두 번째는 배포 리소스에 대 한 분석 기반을 제공 합니다. 꺾은선형 차트는 전체 오류 수 기준 오류 코드 및 리소스를 제공 합니다.

오류 코드에 대 한 자세한 내용은 [azure 서비스의 Azure Resource Manager 및 리소스 공급자](../azure-resource-manager/management/azure-services-resource-providers.md) [를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/templates/common-deployment-errors.md) 을 참조 하세요.

:::image type="content" source="media/quality-of-service/deployment-error-codes-1.png" alt-text="배포 오류 코드 그래프, 샘플 1을 표시 합니다.":::
:::image type="content" source="media/quality-of-service/deployment-error-codes-2.png" alt-text="배포 오류 코드 그래프, 샘플 2를 표시 합니다.":::

이러한 그래프 정보:

- 범례에서 표시할 특정 오류 또는 리소스를 선택 합니다.
- 추세 위젯은 월 단위로 오류 수를 표시 합니다.
- 오류 코드나 리소스 별로 월 단위 추세를 볼 때 테이블에서 최대 3 개의 항목을 선택 합니다.
- 테이블에서 오류 수를 기준으로 배포 오류에 대 한 오류 코드 및 리소스를 정렬 합니다.

### <a name="deployment-errors-by-offer-plan"></a>제공 요금제 배포 오류

이 그래프에서 Y 축은 배포 오류 수를 나타내고 X 축은 상위 제안 계획의 백분위 수를 나타냅니다 (오류 수 기준).

:::image type="content" source="media/quality-of-service/deployment-error-by-offer-plan.png" alt-text="제안 계획 그래프 별로 배포 오류를 표시 합니다.":::

이 그래프 정보:

- 가로 막대형 차트는 선택한 월 범위의 배포 오류 횟수를 나타냅니다.
- 꺾은선형 차트의 값은 제품 요금제의 누적 오차 비율을 나타냅니다.

### <a name="deployment-reliability-by-location"></a>위치별 배포 안정성

이 그래프에는 선택한 월 범위의 성공한 배포 수에 대 한 열 지도가 표시 됩니다. 또한 각 지역에 대해 실패 비율을 보여 줍니다. 녹색에서 빨강 색 눈금은 낮은 실패율의 낮은 값을 나타냅니다. 테이블에서 배포 영역을 확대할 레코드를 선택 합니다.

:::image type="content" source="media/quality-of-service/deployment-reliability.png" alt-text="위치 그래프의 배포 안정성을 보여 줍니다.":::

이 그래프 정보:

- 정확한 위치를 보려면 지도를 이동 합니다.
- 특정 위치를 확대 합니다.
- 열 지도에는 특정 위치에서 성공한 개수, 실패 횟수 및 실패 비율의 배포 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 빨간색 영역은 더 높은 실패율을 나타내며 녹색은 더 낮습니다.
- 표에서 국가/지역을 검색 하 고 선택 하 여 지도의 위치를 확대/축소 합니다. **홈** 아이콘을 사용 하 여 원래 뷰로 되돌립니다.

## <a name="offer-deployment-details"></a>배포 세부 정보 제공

이 표에는 사용 가능한 모든 제품 배포 세부 정보가 표시 됩니다. 제품 배포에 대 한 원시 데이터를 보려면 보고서를 다운로드 합니다.

:::image type="content" source="media/quality-of-service/deployment-details.png" alt-text="배포 세부 정보 테이블을 표시 합니다.":::

이 테이블 정보:

- 배포 날짜별로 정렬 된 1000 상위 배포 목록을 표시 합니다.
- 그리드의 각 열을 정렬할 수 있습니다.
- 컨트롤을 확장 하 고 테이블을 내보냅니다.
- 세부 정보 보기의 페이지가 매겨집니다. 맨 아래에 있는 다른 페이지를 선택 합니다.

### <a name="dictionary-of-data-terms"></a>데이터 용어 사전

| 열 이름 | 특성 이름 | 정의 |
| --- | --- | --- |
| 제품 ID | 제품 ID | 배포 된 제품의 이름 |
| SKU | SKU | 배포 된 제안 계획/SKU의 이름 |
| 배포 상태 | 배포 상태 | **성공** 또는 **실패** 로 표시 된 제품 배포 상태 |
| 구독 ID | 구독 ID | 고객의 구독 ID |
| 고객 테넌트 ID | 고객 테넌트 ID | 고객의 테 넌 트 ID입니다. |
| 고객 이름 | 고객 이름 | 고객 이름 |
| 템플릿 유형 | 템플릿 유형 | 배포 된 Azure 앱의 유형입니다. 이는 관리 되는 앱 또는 솔루션 템플릿이 될 수 있으며, 전용 일 수 없습니다. |
| 배포 시작 시간 | 배포 시작 시간 | 배포의 시작 시간입니다. |
| 배포 종료 시간 | 배포 종료 시간 | 배포의 종료 시간 |
| 배포 기간: | 배포 기간: | 제품 배포의 총 기간 (밀리초)입니다. 그래프에서 분 단위로 표시 됩니다. |
| 배포 지역 | 배포 지역 | Azure 앱 배포의 위치 |
| 리소스 공급자 | 리소스 공급자 | 배포 된 특정 리소스의 리소스 공급자입니다. |
| 리소스 URI | 리소스 URI | 배포 된 리소스의 URI입니다. |
| 리소스 그룹 | 리소스 그룹 | 리소스가 배포 되는 리소스 그룹입니다. |
| 리소스 종류 | 리소스 종류 | 배포 된 리소스의 유형입니다. |
| 리소스 이름 | 리소스 이름 | 배포 된 리소스의 이름입니다. |
| 오류 코드 | 오류 코드 | 배포 실패에 대 한 오류 코드 |
| 오류 이름 | 오류 이름 | 배포 실패에 대 한 오류 이름 |
| 오류 메시지 | 오류 메시지 | 배포 실패에 대한 오류 메시지 |
| 심도 있는 오류 코드 | 심도 있는 오류 코드 | 있는 경우 오류 코드에 대한 추가 정보를 포함합니다. |
| 심도 있는 메시지 코드 | 심도 있는 메시지 코드 | 있는 경우 오류 메시지에 대한 추가 정보를 포함합니다. |
| 상관관계 ID | 상관관계 ID | 서로 다른 배포를 구분하는 데 사용되는 식별자입니다. 동일한 값은 배포되는 모든 리소스가 하나의 배포에 대한 리소스라는 것을 의미합니다. |
|

## <a name="next-steps"></a>다음 단계

- 배포 오류에 대한 자세한 내용은 [Azure Resource Manager 일반적인 Azure 배포 오류 해결을](../azure-resource-manager/templates/common-deployment-errors.md)참조하세요.
- 리소스 공급자에 대한 자세한 내용은 [Azure 서비스용 리소스 공급자를 참조하세요.](../azure-resource-manager/management/azure-services-resource-providers.md)
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 Marketplace 분석의 요약 대시보드](./summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능 형식으로 주문 정보를 보려면 [상업용 마켓플레이스 분석의 주문 대시보드](./orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금제 청구 메트릭은 [상업용 마켓플레이스 분석의 사용량 대시보드](./usage-dashboard.md)를 참조하세요.
- 증가 추세를 비롯한 고객에 대한 자세한 내용은 [상업용 Marketplace 분석의 고객 대시보드](./customer-dashboard.md)를 참조하세요.
- 라이선스에 대한 자세한 내용은 [상업용 Marketplace 분석의 라이선스 대시보드를 참조하세요.](./license-dashboard.md)
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](./downloads-dashboard.md)를 참조하세요.
- Microsoft AppSource 및 Azure Marketplace 대한 제안에 대한 고객 피드백의 통합 보기를 보려면 [상업용 Marketplace 분석의 등급 및 리뷰 대시보드를 참조하세요.](./ratings-reviews.md)
- 상업용 마켓플레이스 분석 및 포괄적인 데이터 용어 사전에 대한 FAQ는 [상업용 마켓플레이스 분석 일반적인 질문을](./analytics-faq.yml)참조하세요.