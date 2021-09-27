---
title: 상업용 Marketplace 분석의 파트너 센터 주문 대시보드 | Microsoft AppSource 및 Azure Marketplace
description: 그래픽 및 다운로드 가능한 형식으로 상업용 Marketplace 제품 주문에 대한 분석 보고서에 액세스하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: 95d0ced24ee4f7426cac96ff2e550f2be88d8d02
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083368"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>상업용 마켓플레이스 분석의 주문 대시보드

이 문서에서는 파트너 센터의 주문 대시보드에 대한 정보를 제공합니다. 이 대시보드에서 그래픽 및 다운로드 가능한 형식으로 제공되는 증가 추세를 비롯한 주문 정보를 볼 수 있습니다.

>[!NOTE]
> 분석 용어에 대한 자세한 정의는 [상업용 Marketplace 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.

## <a name="orders-dashboard"></a>주문 대시보드

[주문 대시보드](https://go.microsoft.com/fwlink/?linkid=2165914)에는 모든 SaaS(software as a Service) 제품에 대한 현재 주문이 표시됩니다. 다음 항목에 대한 그래픽 표현을 볼 수 있습니다.

- 주문 추세
- 사용자 및 사이트 추세별 주문
- 제품 및 SKU별 주문
- 지리별 주문
- 자세한 주문 테이블
- 주문 페이지 필터

> [!NOTE]
> 파트너 센터에서 고객 획득과 보고 사이의 최대 대기 시간은 48시간입니다.

## <a name="access-the-orders-dashboard"></a>주문 대시보드에 액세스

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 홈 페이지에서 **Insights** 타일을 선택합니다.

    [![파트너 센터 홈 페이지의 Insights 타일을 보여 줍니다. ](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. 왼쪽 메뉴에서 **주문을** 선택합니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색에서 상업용 **Marketplace** 주문  >    >  **분석을** 선택합니다.

---

## <a name="elements-of-the-orders-dashboard"></a>주문 대시보드의 요소

다음 섹션에서는 주문 대시보드를 사용하는 방법과 데이터를 읽는 방법을 설명합니다.

### <a name="month-range"></a>월 범위

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

각 페이지의 오른쪽 위 모서리에서 월 범위 선택 항목을 찾을 수 있습니다. 지난 6 또는 12개월을 기준으로 월 범위를 선택하거나 최대 기간이 12개월인 사용자 지정 월 범위를 선택하여 **주문** 페이지 그래프의 출력을 사용자 지정합니다. 기본 월 범위(계산 기간)는 6개월입니다.

[![주문 대시보드의 월 필터를 보여 줍니다. ](./media/orders-dashboard/order-workspace-filters.png) ](./media/orders-dashboard/order-workspace-filters.png#lightbox)

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

각 페이지의 오른쪽 위 모서리에서 월 범위 선택 항목을 찾을 수 있습니다. 지난 6 또는 12개월을 기준으로 월 범위를 선택하거나 최대 기간이 12개월인 사용자 지정 월 범위를 선택하여 **주문** 페이지 그래프의 출력을 사용자 지정합니다. 기본 월 범위(계산 기간)는 6개월입니다.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="주문 대시보드의 월 필터를 보여 줍니다.":::

---

> [!NOTE]
> 시각화 위젯 및 내보내기 보고서의 모든 메트릭은 사용자가 선택한 계산 기간을 사용합니다.

### <a name="orders-trend"></a>주문 추세

이 섹션에서는 선택한 계산 기간의 활성 및 취소된 주문의 추세를 보여 주는 **주문** 차트를 찾을 수 있습니다. 메트릭 및 증가 추세는 꺾은선형 차트로 표시되며 차트의 선 위로 마우스를 가져가면 각 월의 값이 표시됩니다. 위젯의 주문 메트릭 아래 백분율 값은 선택된 계산 기간 동안 증가 또는 거부되는 정도를 나타냅니다.

_활성_ 및 _취소됨_ 이라는 두 개의 주문 카운터가 있습니다.

- **활성** 은 선택한 날짜 범위 동안 고객이 현재 사용 중인 주문 수와 같습니다.
- **취소됨** 은 이전에 구매한 다음, 선택한 날짜 범위 동안 취소된 주문 수와 같습니다.

[![주문 대시보드의 주문 위젯을 보여 주며, 이는 활성 및 취소된 주문의 추세를 보여 줍니다.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>사용자 및 사이트 추세별 주문

**사용자 및 사이트 기반 주문** 꺾은선형 차트는 고객이 구매한 사용자 SaaS 주문 및 사이트별 SaaS의 메트릭과 추세를 나타냅니다(이 차트는 취소된 주문을 포함함).

[![사용자 및 사이트 추세별 주문을 보여 주는 주문 대시보드의 주문 위젯을 보여 줍니다.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

SaaS 제품은 각 계획에 대한 두 가지 가격 책정 모델 중 하나를 사용할 수 있습니다. 즉, 플랫 요율(사이트 기반) 또는 사용자 단위(사용자 기반) 중 하나를 사용할 수 있습니다.

- **정액제**: 단일 월간 또는 연간 정액제로 제품에 대한 액세스를 허용합니다. 이를 사이트 기반 가격 책정이라고도 합니다.
- **사용자당**: 제품에 액세스하는 사용자 수 또는 상시 사용자 수를 기준으로 한 가격으로 제품에 대한 액세스를 허용합니다. 이 사용 빈도 기반 모델을 사용하면 계획에서 지원되는 사용자의 최소 및 최대 수를 설정할 수 있습니다. 여러 요금제를 만들어 사용자 수에 따라 다양한 가격을 구성할 수 있습니다. 이러한 필드는 선택 사항입니다. 선택하지 않을 경우 사용자 수에 제한이 없는 것으로 해석됩니다(최소는 1, 최대는 서비스에서 지원할 수 있는 최대한). 이러한 필드는 플랜 업데이트의 일부로 편집할 수 있습니다.
- **요금 청구**: 정액 요금에 청구됩니다. 이 가격 책정 모델을 사용하는 경우 필요에 따라 정액 요금에 포함되지 않는 사용량에 대해 고객에게 요금을 청구하기 위해 마켓플레이스 계량 서비스 API를 사용하는 계량된 플랜을 정의할 수 있습니다.

사용자, 사이트 및 요금제 기반 청구에 대한 자세한 내용은 [상업용 Marketplace에 대한 SaaS 제품을 계획하는 방법](plan-saas-offer.md)을 참조하세요.

### <a name="orders-by-offers-and-skus"></a>제품 및 SKU별 주문

제품 및 SKU별 주문 차트는 모든 제품의 측정값과 추세를 보여 줍니다.

- 최상위 제품이 그래프에 표시되고 나머지 제품은 **Rest All** 로 그룹화됩니다.
- 범례에서 특정 제품을 선택하여 해당 제품 및 관련 SKU만 그래프에 표시할 수 있습니다.
- 그래프에서 조각을 마우스로 가리키면 모든 제품의 총 주문 수와 비교한 주문의 수와 해당 제품의 백분율이 표시됩니다.
- **제품별 주문 추세** 는 월별 증가 추세를 표시합니다. 월 열은 제품 이름별 주문 수를 나타냅니다. 꺾은선형 차트는 z축에 그려진 증가율 추세를 표시합니다.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="주문 대시보드에 제품별 주문 차트를 보여 줍니다.":::

제품 및 해당 제품의 최대 3개 SKU를 선택하여 제품, SKU 및 사용자에 대한 월 단위 추세를 볼 수 있습니다.

### <a name="orders-by-geography"></a>지리별 주문

선택한 계산 기간에 대해 열 지도는 전체 주문 수와 지리에 대해 새로 추가된 주문의 증가율을 표시합니다.  지도의 밝은 색에서 짙은 색은 고객 수의 낮은 값에서 높은 값을 나타냅니다. 테이블에서 특정 국가 또는 지역으로 확대할 레코드를 선택합니다.

[![주문 대시보드에 지리적 스프레드 차트를 보여 줍니다.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

다음 사항에 유의하세요.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수, 정규화된 사용량 시간의 세부 정보를 볼 수 있는 보조 눈금이 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택하여 원래 보기로 돌아갑니다.

### <a name="orders-details-table"></a>주문 세부 정보 표

주문 세부 정보 표에는 획득 날짜별로 정렬된 1000개의 상위 주문의 번호가 매겨진 목록이 표시됩니다.

- 그리드의 각 열을 정렬할 수 있습니다.
- 레코드 수가 1,000개보다 작은 경우 데이터를 .CSV 또는 .TSV 파일로 추출할 수 있습니다.
- 레코드 수가 1,000개를 초과하는 경우 내보낸 데이터는 다음 30일 동안 비동기적으로 다운로드 페이지에 배치됩니다.
- 필터를 **주문 세부 정보** 표에 적용하여 관심 있는 데이터만 표시할 수 있습니다. 국가/지역, Azure 라이선스 유형, 상업용 Marketplace 라이선스 유형, 제품 유형, 주문 상태, 무료 내역, 상업용 Marketplace 구독 ID, 고객 ID 및 회사 이름으로 필터링합니다.
- 보호된 고객이 제품을 구매하면 **주문 상세 데이터** 정보가 마스킹됩니다(************).

***표 1: 데이터 용어 사전***

| 열 이름<br>사용자 인터페이스 | 특성 이름 | 정의 | 프로그래밍 방식의 열 이름<br>액세스 보고서 |
| ------------ | ------------- | ------------- | ------------- |
| Marketplace 구독 ID | Marketplace 구독 ID | 고객이 상업용 Marketplace 제품을 구매하는 데 사용한 Azure 구독과 연결된 고유 식별자입니다. 인프라 제품의 경우 고객의 Azure 구독 GUID입니다. SaaS 제품의 경우 SaaS 구매에는 Azure 구독이 필요하지 않으므로 0으로 표시됩니다. | Marketplace 구독 ID |
| MonthStartDate | 월 시작 날짜 | 월 시작 날짜는 구매 월을 나타냅니다. yyyy-mm-dd 형식입니다. | MonthStartDate |
| 제품 유형 | 제품 유형 | 상업용 Marketplace 제품의 유형입니다. | OfferType |
| Azure 라이선스 유형 | Azure 라이선스 유형 | Azure 구매 고객이 체결하는 라이선싱 계약 유형입니다. 채널이라고도 합니다. 가능한 값은 다음과 같습니다.<ul><li>[클라우드 솔루션 공급자](cloud-solution-providers.md)</li><li>Enterprise</li><li>재판매인을 통한 Enterprise</li><li>종량제</li><li>GTM</li></ul> | AzureLicenseType |
| Marketplace 라이선스 유형 | Marketplace 라이선스 유형 | 상업용 Marketplace 제품의 청구 방법입니다. 다른 값은 다음과 같습니다.<ul><li>Azure를 통해 청구</li><li>사용자 라이선스 필요</li><li>무료</li><li>Microsoft 대리점</li></ul> | MarketplaceLicenseType |
| SKU | SKU | 제품에 연결된 플랜 | SKU |
| 고객 국가 | 고객 국가/지역 | 고객이 입력한 국가/지역 이름. 국가/지역은 고객의 Azure 구독에서 국가/지역과 다를 수 있습니다. | CustomerCountry |
| 미리 보기 SKU | 미리 보기 SKU | 이 값은 SKU를 "미리 보기"로 태그 지정한 경우를 알려 줍니다. 이에 따라 SKU가 태그 지정된 경우 값은 "예"이며, 사용자가 승인한 Azure 구독만 이 이미지를 배포하고 사용할 수 있습니다. SKU가 "미리 보기"로 식별되지 않은 경우 값은 "아니요"입니다. | IsPreviewSKU |
| 주문 ID | 주문 ID | 상업용 Marketplace 서비스의 고객 주문에 대한 고유 식별자입니다. Virtual Machine 사용량 기준 제품은 주문에 연결되지 않습니다. | OrderID |
| Order Quantity | Order Quantity | 활성 주문의 주문 ID와 연결된 자산 수 | OrderQuantity |
| 클라우드 인스턴스 이름 | 클라우드 인스턴스 이름 | VM이 배포된 Microsoft Cloud입니다. | CloudInstanceName |
| 새 고객 | 새 고객 | 이 값은 하나 이상의 제품을 처음으로 획득한 새 고객을 식별합니다. "취득일"에 대해 동일한 월 내인 경우 값이 "예"로 나타납니다. 고객이 보고한 월 이전에 제품을 구매한 경우 값은 "아니요"입니다. | IsNewCustomer |
| 주문 상태 | 주문 상태 | 데이터를 마지막으로 새로 고칠 때의 상업용 Marketplace 주문 상태입니다. | OrderStatus |
| 주문 취소 날짜 | 주문 취소 날짜 | 상업용 Marketplace 주문이 취소된 날짜입니다. | OrderCancelDate |
| 고객 회사 이름 | 고객 회사 이름 | 고객이 입력한 회사 이름입니다. 이름은 고객의 Azure 구독에서 구/군/시와 다를 수 있습니다. | CustomerCompanyName |
| 주문 구매 날짜 | 주문 구매 날짜 | 상업용 Marketplace 주문이 생성된 날짜입니다. yyyy-mm-dd 형식입니다. | OrderPurchaseDate |
| 제품 이름 | 제품 이름 | 상업용 Marketplace 제품의 이름입니다. | OfferName |
| 평가판 종료 날짜 | 평가판 종료 날짜 | 이 주문의 평가 기간이 종료되었거나 종료될 예정인 날짜입니다. | TrialEndDate |
| 고객 ID | 고객 ID | 고객에게 할당된 고유 식별자입니다. 고객은 Azure Marketplace 구독이 0개 이상 있을 수 있습니다. | CustomerId |
| 청구 계정 ID | 청구 계정 ID | 청구를 생성하는 계정의 식별자입니다. **청구 계정 ID** 를 **customerID** 에 매핑하여 고객, 주문 및 사용 현황 보고서에 지급 트랜잭션 보고서를 연결합니다. | BillingAccountId |
| AssetCount | 자산 수 | 주문 ID와 연결된 자산 수입니다. | 사용되지 않음 |
| 사용할 수 없음 * | TermStartDate | 주문 기간의 시작 날짜를 나타냅니다. | TermStartDate |
| 사용할 수 없음 * | TermEndDate | 주문 기간의 종료 날짜를 나타냅니다. | TermEndDate |
| 사용할 수 없음 * | purchaseRecordId | 주문과 관련된 구매 레코드의 식별자입니다. | purchaseRecordId |
| 사용할 수 없음 * | purchaseRecordLineItemId | 이 주문과 관련된 구매 레코드 품목의 식별자입니다. | purchaseRecordLineItemId |
| 사용할 수 없음 * | EstimatedCharges | 모든 주문 단위에 대해 고객에게 청구되는 세전 가격입니다. 세금 포함 국가에서는 이 가격에 세금이 포함되고, 세금 제외 국가에서는 세금이 포함되지 않습니다. | EstimatedCharges |
| 사용할 수 없음 * | 통화 | 제안의 BillingCurrency입니다. | 통화 |
| 사용할 수 없음 * | HasTrial | 제안에 평가판 기간이 사용하도록 설정되었는지 여부를 나타냅니다. | HasTrial |
|||||

`*` 새 필드는 현재 ISVOrderV2 데이터 세트에 제공되며, 프로그래매틱 방식으로 액세스할 수 있습니다.

### <a name="orders-page-filters"></a>주문 페이지 필터

**주문** 페이지 필터는 주문 페이지 수준에서 적용됩니다. 하나 또는 여러 필터를 선택하여 보려고 선택한 기준과 '자세한 주문 데이터' 그리드/내보내기에서 보려는 데이터에 대한 차트를 렌더링할 수 있습니다. 필터는 주문 페이지의 오른쪽 위 모서리에서 선택한 월 범위에 대해 추출된 데이터에 적용됩니다.

> [!TIP]
> 위젯의 오른쪽 위 모서리에 있는 다운로드 아이콘을 사용하여 데이터를 다운로드할 수 있습니다. "엄지 손가락 위로" 또는 "엄지 손가락 아래로" 아이콘을 클릭하여 각 위젯에 대한 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 상업용 Marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 Marketplace에 대한 분석 보고서 액세스](analytics.md)를 참조하세요.
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 Marketplace 분석의 요약 대시보드](./summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능 형식으로 주문 정보를 보려면 [상업용 마켓플레이스 분석의 주문 대시보드](orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금제 청구 메트릭은 [상업용 Marketplace 분석의 사용량 대시보드](./usage-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 AppSource의 제품에 대한 고객 피드백을 모두 확인하려면 [파트너 센터의 평가 및 검토 분석 대시보드](ratings-reviews.md)를 참조하세요.
- 상업용 Marketplace 분석 및 데이터 용어의 종합 사전에 대한 자주 묻는 질문은 [상업용 Marketplace 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.
