---
title: 상업용 마켓플레이스 분석의 Marketplace Insights 대시보드
description: Microsoft AppSource 및 Azure Marketplace에서 고객 참여를 측정할 수 있게 해 주는 파트너 센터 Marketplace 웹 분석의 요약에 액세스합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 03/23/2021
ms.openlocfilehash: 18ff7d0faa7ace0dc70c7f90603167084a4b8135
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740449"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>상업용 마켓플레이스 분석의 Marketplace Insights 대시보드

이 문서에서는 파트너 센터의 Marketplace 인사이트 대시보드에 대한 정보를 제공합니다. 이 대시보드는 상업용 마켓플레이스 웹 분석의 요약을 나타냅니다. 이를 통해 게시자는 Microsoft AppSource 및 Azure Marketplace와 같은 상업용 마켓플레이스 온라인 스토어에 표시되는 제품 세부 정보 페이지에 대한 고객 참여도를 측정할 수 있습니다.

분석 용어에 대한 자세한 정의는 [상업용 마켓플레이스 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.

## <a name="marketplace-insights-dashboard"></a>Marketplace 인사이트 대시보드

[Marketplace 인사이트 대시보드](https://go.microsoft.com/fwlink/?linkid=2165936)에는 Azure Marketplace에 대한 개요와 AppSource의 제품의 비즈니스 성과가 표시됩니다. 대시보드는 다음의 광범위한 개요를 제공합니다.

- 페이지 방문 추세
- 작업에 대한 호출 추세
- 페이지 방문 및 작업에 대한 호출, 조회 도메인 및 캠페인 ID에 대한 작업 호출
- 지리별 Marketplace 정보
- Marketplace 인사이트 세부 정보 테이블

Marketplace 인사이트 대시보드는 클릭스트림 데이터를 제공하며,이는 리드 대상 엔드포인트에서 생성된 잠재 고객과 상관 관계가 있어서는 안 됩니다.

> [!NOTE]
> Azure Marketplace 또는 AppSource에서 제품을 방문한 사용자와 파트너 센터의 보고 간 최대 대기 시간은 48시간입니다.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Marketplace 인사이트 대시보드의 요소

Marketplace 인사이트 대시보드는 Azure Marketplace 및 AppSource에 대한 웹 원격 분석 세부 정보를 별도의 두 탭에 표시합니다. 다음 섹션에서는 요약 대시보드를 사용하는 방법과 데이터를 읽는 방법을 설명합니다.

파트너 센터에서 **Marketplace 인사이트** 대시보드에 액세스하려면 상업용 마켓플레이스에서 **[분석](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **Marketplace 인사이트** 를 선택합니다.

### <a name="month-range"></a>월 범위

각 페이지의 오른쪽 위 모서리에서 월 범위 선택 항목을 찾을 수 있습니다. 지난 6개월 또는 12개월을 기준으로 월 범위를 선택하거나 최대 기간이 12개월인 사용자 지정 월 범위를 선택하여 **Marketplace 인사이트** 페이지 그래프의 출력을 사용자 지정합니다. 기본 월 범위(계산 기간)는 6개월입니다.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Marketplace 인사이트 대시보드의 월 필터를 보여 줍니다.":::

> [!NOTE]
> 시각화 위젯 및 내보내기 보고서의 모든 메트릭은 사용자가 선택한 계산 기간을 사용합니다.

### <a name="page-visits-trends"></a>페이지 방문 추세

Marketplace 인사이트 **방문자** 차트에는 선택한 계산 기간에 대한 _페이지 방문_ 횟수와 _고유 방문자_ 수가 표시됩니다.

**페이지 방문**: 이 숫자는 선택된 계산 기간에 대한 제품 목록 페이지(제품 세부 정보 페이지)의 고유한 사용자 세션 수를 나타냅니다. 빨강 및 녹색 백분율 지표는 페이지 방문의 증가율을 나타냅니다. 추세 차트는 월간 페이지 방문 횟수를 나타냅니다.

**고유 방문자**:이 숫자는 Azure Marketplace와 AppSource의 제안에 대해 선택한 계산 기간 동안의 고유한 방문자 수를 나타냅니다. 하나 이상의 제품 세부 정보 페이지를 방문한 방문자는 하나의 고유 방문자로 계산됩니다.

[![Marketplace 인사이트 대시보드의 방문자 차트를 보여 줍니다.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>작업에 대한 호출 추세

이 숫자는 제품 목록 페이지(제품 정보 페이지)에서 **작업에 대한 호출** 단추 클릭 완료 횟수를 나타냅니다. _작업에 대한 호출_ 은 **지금 받기**, **평가판**, **담당자** 및 **시험 사용** 단추를 선택할 때 계산됩니다. *지정 된 동의* 는 Microsoft 또는 파트너에 대 한 고객 제공 동의의 총 클릭 수를 나타내며 제품에 대해 획득 한 고객의 수와 동일 합니다. 다음 두 예제는 *지정 된* 클릭이 표시 되는 위치를 보여 줍니다.

:::image type="content" source="./media/insights-dashboard/consent-screen.png" alt-text="승인 단추가 선택 된 위치를 보여 줍니다.":::

다음 그래프에서는 *Cta* 및 *지정 된 동의* 메트릭을 보여 줍니다.

:::image type="content" source="./media/insights-dashboard/consent-given-graph.png" alt-text="지정 된 동작 및 동의에 대 한 호출의 샘플 그래프를 보여 줍니다.":::

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>페이지 방문 및 작업에 대한 호출, 조회 도메인 및 캠페인 ID에 대한 작업 호출

#### <a name="offers"></a>제품

특정 제품을 선택 하 여 페이지 방문에 대 한 월별 추세, 작업 호출 및 차트에서 승인 된 클릭을 확인할 수 있습니다.

:::image type="content" source="./media/insights-dashboard/offers-funnel-graph.png" alt-text="제안에 대 한 샘플 깔때기형 그래프를 표시 합니다.":::

#### <a name="referral-domains"></a>조회 도메인

특정 조회 도메인을 선택 하면 페이지 방문의 월별 추세, 작업 호출 및 오른쪽의 차트에 대 한 승인 클릭이 표시 됩니다. 또한 Platform – 웹 사이트 및 클라이언트에 대 한 열도 AppSource 제품에 대해서만 표시 됩니다. 깔때기형 보기는 차트에서 페이지 보기, 작업 호출 및 동의 시 클릭 간의 변환 비율을 보여 줍니다.

:::image type="content" source="./media/insights-dashboard/referral-domains-funnel-graph.png" alt-text="조회 도메인의 샘플 깔때기 그래프를 표시 합니다.":::

#### <a name="campaign-ids"></a>캠페인 Id

특정 캠페인 ID를 선택 하면 캠페인의 성공 여부를 이해할 수 있습니다. 각 캠페인에 대해 페이지 방문의 월별 추세, 작업 호출 및 차트에서 승인 된 클릭을 확인할 수 있어야 합니다.

:::image type="content" source="./media/insights-dashboard/campaign-id-funnel-graph.png" alt-text="Marketplace 인사이트 대시보드의 캠페인 차트를 보여 줍니다.":::

### <a name="marketplace-insights-by-geography"></a>지리별 Marketplace 정보

선택한 계산 기간 동안 열 지도는 페이지 방문 횟수, 고유 방문자 수 및 CTA(작업에 대한 호출)를 표시합니다. 지도의 밝은 색에서 짙은 색은 고유 방문자 수의 낮은 값에서 높은 값을 나타냅니다. 국가/지역을 확대하려면 테이블의 레코드를 선택합니다.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Marketplace 인사이트 대시보드의 지리적 스프레드 차트를 보여 줍니다.":::

다음 사항에 유의하세요.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수 및 정규화된 사용량 시간의 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택하여 원래 보기로 되돌아갑니다.

### <a name="marketplace-insights-details-table"></a>Marketplace 인사이트 세부 정보 테이블

이 표에서는 선택한 제품 페이지의 페이지 방문 횟수 및 작업에 대한 호출의 목록 보기를 날짜별로 정렬하여 보여 줍니다.

- 레코드 수가 1,000보다 작은 경우 데이터를 .TSV 또는 .CSV 파일로 추출할 수 있습니다.
- 레코드 수가 1,000을 초과하는 경우 내보낸 데이터는 다음 30일 동안 비동기적으로 다운로드페이지에 배치됩니다.
- 원하는 데이터를 표시하기 위해 제품 이름 및 캠페인 이름을 기준으로 데이터를 필터링합니다.

> [!TIP]
> 위젯의 오른쪽 위 모서리에 있는 다운로드 아이콘을 사용하여 데이터를 다운로드할 수 있습니다. “좋아요” 또는 “싫어요” 아이콘을 클릭하여 각 위젯에 대한 피드백을 제공할 수 있습니다.

| 열 이름<br>사용자 인터페이스 | 특성 이름 | 정의 | 프로그래밍 방식의 열 이름<br>Access 보고서 |
| ------------ | ------------- | ------------- | ------------- |
| 날짜 | 방문한 날짜 | Azure Marketplace 및/또는 AppSource의 제품 페이지에서 페이지 방문 및/또는 CTA 클릭 이벤트 생성의 날짜입니다. | 날짜 |
| 제품 이름 | 제품 이름 | 상업용 Marketplace 제품의 이름입니다. | OfferName |
| 조회 도메인 | 조회 도메인 | 페이지를 방문한 조회 도메인의 이름입니다. 페이지 방문에 대해 캡처된 조회 도메인이 없는 경우 해당 항목은 "조회 도메인 없음"입니다. |  ReferralDomain |
| 국가 이름 | 국가 이름 | 페이지 방문이 발생한 국가 이름입니다. | CountryName |
| 페이지 방문 횟수 | 페이지 방문 횟수 | 특정 날짜에 대한 제품 이름과 연결된 페이지 방문 횟수입니다. | PageVisits |
| 바로 구매 | 바로 구매 | 특정 날짜에 대한 제품 페이지에서 "바로 구매" CTA의 클릭 수입니다. | GetItNow |
| 연락처 | 연락처 | 특정 날짜에 대한 제품 페이지에서 "연락처" CTA의 클릭 수입니다. | ContactMe |
| 시험 사용 | 시험 사용 | 특정 날짜에 대한 제품 페이지에서 "시험 사용" CTA의 클릭 수입니다. | TestDrive |
| 무료 평가판 | 무료 평가판 | 특정 날짜에 대한 제품 페이지의 "무료 평가판" CTA의 클릭 수입니다. | FreeTrial |
| 캠페인 | 캠페인의 이름 | 캠페인 이름에 대한 웹 원격 분석(페이지 방문 및 CTA 클릭)을 이해할 수 있는지 여부입니다. | 캠페인 |
| 지정 된 동의 | 지정 된 동의 | Microsoft 또는 파트너에 대 한 고객이 제공한 동의의 총 클릭 수입니다. | consentGivenCount |
| 플랫폼 | 플랫폼 | 웹 사이트 또는 클라이언트 (제품 내 저장소)를 페이지 보기, CTA 또는 동의 클릭의 원본으로 나타냅니다. | 플랫폼 |
| 해당 없음 | 사이트 | 페이지 방문 또는 CTA 클릭이 발생한 상점 이름입니다. 가능한 값은 다음과 같습니다.<br><ul><li>AZUREMARKETPLACE<li>APPSOURCE</ul> | 사이트 |
|

## <a name="next-steps"></a>다음 단계

- 상업용 마켓플레이스에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터의 상업용 마켓플레이스에 대한 분석 보고서 액세스](analytics.md)를 참조하세요.
- 그래픽 및 다운로드가능 형식으로 주문 정보를 보려면 [상업용 마켓플레이스 분석의 주문 대시보드](orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금제 청구 메트릭은 [상업용 마켓플레이스 분석의 사용량 대시보드](usage-dashboard.md)를 참조하세요.
- 증가 추세를 비롯한 고객에 대한 자세한 내용은 [상업용 Marketplace 분석의 고객 대시보드](customer-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 AppSource의 제품에 대한 고객 피드백을 모두 확인하려면 [파트너 센터의 평가 및 검토 분석 대시보드](ratings-reviews.md)를 참조하세요.
- 상업용 Marketplace 분석 및 데이터 용어의 종합 사전에 대한 자주 묻는 질문은 [상업용 Marketplace 분석 용어 및 일반적인 질문](analytics-faq.yml)을 참조하세요.
