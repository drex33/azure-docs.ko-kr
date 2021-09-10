---
title: 상업용 Marketplace의 파트너 센터 분석에 대한 요약 대시보드
description: 파트너 센터의 요약 대시보드에서 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값에 액세스하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 08/06/2021
ms.openlocfilehash: b9b3a1e651e6207cc037ab8e4c70f81a176c3d82
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529199"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>상업용 마켓플레이스 분석의 요약 대시보드

이 문서에서는 파트너 센터의 요약 대시보드에 대한 정보를 제공합니다. 이 대시보드는 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값을 표시합니다.

>[!NOTE]
> 분석 용어에 대한 자세한 정의는 [상업용 마켓플레이스 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.

## <a name="summary-dashboard"></a>요약 대시보드

[요약 대시보드](https://go.microsoft.com/fwlink/?linkid=2165765)에는 Azure Marketplace에 대한 개요와 Microsoft AppSource의 제품의 비즈니스 성과가 표시됩니다. 대시보드는 다음의 광범위한 개요를 제공합니다.

- 고객의 주문
- 고객
- 고객의 제품 사용량
- Azure Marketplace 및 AppSource에서 고객의 페이지 방문 횟수

## <a name="elements-of-the-summary-dashboard"></a>요약 대시보드의 요소

다음 섹션에서는 요약 대시보드를 사용하는 방법과 데이터를 읽는 방법을 설명합니다.

파트너 센터에서 요약 대시보드에 액세스하려면 **상업용 마켓플레이스** 에서 **[분석](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **요약** 을 선택합니다.

### <a name="month-range"></a>월 범위

각 페이지의 오른쪽 위 모서리에서 월 범위 선택 항목을 찾을 수 있습니다. 지난 3개월, 6개월 또는 12개월을 기준으로 월 범위를 선택하거나 최대 기간이 12개월인 사용자 지정 월 범위를 선택하여 **요약** 페이지 그래프의 출력을 사용자 지정합니다. 기본 월 범위(계산 기간)는 6개월입니다.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="요약 대시보드의 월간 범위 옵션을 보여 줍니다.":::

> [!NOTE]
> 시각화 위젯 및 내보내기 보고서의 모든 메트릭은 사용자가 선택한 계산 기간을 사용합니다.

### <a name="orders-widget"></a>주문 위젯

**요약** 대시보드의 주문 위젯에는 모든 트랜잭션 기반 제품에 대한 현재 주문이 표시됩니다. 주문 위젯은 선택한 계산 기간에 대해 구매한 주문(취소한 주문 제외)의 수와 추세를 표시합니다. 백분율 값 **주문** 은 선택한 계산 기간 동안의 증가량을 나타냅니다.

[![요약 대시보드의 주문 위젯을 보여 줍니다.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


위젯의 왼쪽 아래 모서리에 있는 **주문 대시보드** 링크를 선택하여 주문 보고서로 이동할 수도 있습니다.

### <a name="customers-widget"></a>고객 위젯

**요약** 대시보드의 **고객** 위젯에는 선택한 계산 기간 동안 제품을 수령한 총 고객 수가 표시됩니다. 고객 위젯에는 선택한 계산 기간 동안의 총 활성(신규 및 기존) 고객(변동 고객 제외) 수와 추세가 표시됩니다. **고객** 아래의 백분율 값은 선택한 계산 기간 동안의 증가량을 나타냅니다.

[![요약 대시보드의 고객 위젯을 보여 줍니다.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

위젯의 왼쪽 아래 모서리에 있는 **고객 대시보드** 링크를 선택하여 고객 보고서로 이동할 수도 있습니다.

### <a name="usage-widget"></a>사용량 위젯

**요약** 대시보드의 **사용량** 위젯은 모든 Azure VM(가상 머신) 제품에 대한 총 정규화된 사용 시간 및 가공되지 않은 사용 시간을 나타냅니다. 사용량 위젯에는 선택한 계산 기간 동안의 총 사용 시간과 추세가 표시됩니다.

사용량 요약 테이블은 구매한 모든 제품에 대한 고객 사용 시간을 표시합니다.

- 정규화된 사용 시간은 VM 코어 수를 고려하도록 정규화된 사용 시간으로 정의됩니다([VM 코어 수] x [가공되지 않은 사용량 시간])으로 계산됩니다. "SHAREDCORE"로 지정된 VM은 [VM 코어 수] 승수로 1/6(0.1666)을 사용합니다.
- 가공되지 않은 사용 시간은 VM이 실행된 기간을 시간 단위로 정의합니다.

총 사용 시간 아래의 백분율 값은 선택한 계산 기간 동안의 사용 시간 증가량을 나타냅니다.

[![요약 대시보드의 사용량 위젯을 보여 줍니다.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

위젯의 왼쪽 아래 모서리에 있는 **사용량 대시보드** 링크를 선택하여 사용량 보고서로 이동할 수도 있습니다.

### <a name="marketplace-insights"></a>Marketplace 인사이트

Marketplace 인사이트에는 Azure Marketplace 및 AppSource에서 제품의 페이지를 방문한 사용자 수가 표시됩니다. **페이지 방문 횟수** 대시보드는 상업용 마켓플레이스 웹 분석의 요약을 표시합니다. 이를 통해 게시자는 Microsoft AppSource 및 Azure Marketplace와 같은 상업용 마켓플레이스 온라인 스토어에 표시되는 제품 세부 정보 페이지에 대한 고객 참여도를 측정할 수 있습니다. 이 위젯은 선택한 계산 기간 동안의 총 페이지 방문 횟수와 추세를 표시합니다.

[![요약 대시보드의 페이지 방문 횟수 위젯을 보여 줍니다.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

위젯의 왼쪽 아래 모서리에 있는 **Marketplace 인사이트 대시보드** 링크를 선택하여 Marketplace 인사이트 보고서로 이동할 수도 있습니다.

### <a name="geographical-spread"></a>지리적 분산

열 지도는 선택한 계산 기간 동안 지리 차원에 대한 총 고객 수, 주문 수 및 정규화된 사용 시간이 표시됩니다.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="요약 대시보드의 국가 분산 위젯을 보여 줍니다.":::

다음 사항에 유의하세요.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수, 정규화된 사용량 시간의 세부 정보를 볼 수 있는 보조 눈금이 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택하여 원래 보기로 되돌아갑니다.

> [!TIP]
> 위젯의 오른쪽 위 모서리에 있는 다운로드 아이콘을 사용하여 데이터를 다운로드할 수 있습니다. “좋아요” 또는 “싫어요” 아이콘을 선택하여 각 위젯에 대한 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 상업용 마켓플레이스에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터의 상업용 마켓플레이스에 대한 분석 보고서 액세스](analytics.md)를 참조하세요.
- 그래픽 및 다운로드 가능 형식으로 주문 정보를 보려면 [상업용 마켓플레이스 분석의 주문 대시보드](orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금제 청구 메트릭은 [상업용 마켓플레이스 분석의 사용량 대시보드](usage-dashboard.md)를 참조하세요.
- 증가 추세를 비롯한 고객에 대한 자세한 내용은 [상업용 마켓플레이스 분석의 고객 대시보드](customer-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 마켓플레이스 분석의 다운로드 대시보드](downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 AppSource의 제품에 대한 고객 피드백을 모두 확인하려면 [파트너 센터의 평가 및 검토 분석 대시보드](ratings-reviews.md)를 참조하세요.
- 상업용 Marketplace 분석 및 데이터 용어의 종합 사전에 대한 자주 묻는 질문은 [상업용 Marketplace 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.
