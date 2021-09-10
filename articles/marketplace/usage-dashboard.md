---
title: 상업용 Marketplace 분석의 사용량 대시보드 | Azure Marketplace
description: Azure Marketplace에 게시된 제품에 대한 모든 사용량 및 요금제 청구 메트릭에 액세스하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 07/15/2021
ms.openlocfilehash: c68216675a41a71b21216ce558b03171781dbc43
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567309"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>상업용 마켓플레이스 분석의 사용량 대시보드

이 문서에서는 파트너 센터의 사용량 대시보드에 대한 정보를 제공합니다. 이 대시보드는 모든 VM(가상 머신)에서 표준화된 사용량, 가공되지 않은 사용량 및 요금제 청구 메트릭을 3개의 별도 탭(VM 정규화된 사용량, VM 원시 사용량 및 요금제 청구 사용량)으로 표시합니다.

>[!NOTE]
> 분석 용어에 대한 자세한 정의는 [상업용 Marketplace 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.

## <a name="usage-dashboard"></a>사용량 대시보드

[사용량 대시보드](https://go.microsoft.com/fwlink/?linkid=2166106)에는 모든 SaaS(software as a Service) 제품에 대한 현재 주문이 표시됩니다. 다음 항목에 대한 그래픽 표현을 볼 수 있습니다.

- 사용량 추세
- 제품별 정규화된 사용량
- 다른 차원별 정규화된 사용량: VM 크기, 판매 채널 및 제품 유형
- 지리별 사용량
- 자세한 사용량 표
- 주문 페이지 필터

> [!NOTE]
> 파트너 센터에서 사용량 이벤트 생성과 보고 사이의 최대 대기 시간은 48시간입니다.

## <a name="elements-of-the-usage-dashboard"></a>사용량 대시보드의 요소

다음 섹션에서는 사용량 대시보드를 사용하는 방법과 데이터를 읽는 방법을 설명합니다.

파트너 센터에서 주문 대시보드에 액세스하려면 **상업용 Marketplace** 에서 **[분석](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **사용량** 을 선택합니다.

### <a name="month-range"></a>월 범위

각 페이지의 오른쪽 위 모서리에서 월 범위 선택 항목을 찾을 수 있습니다. 지난 6개월 또는 12개월을 기준으로 월 범위를 선택하거나 최대 기간이 12개월인 사용자 지정 월 범위를 선택하여 **사용량** 페이지 그래프의 출력을 사용자 지정합니다. 기본 월 범위(계산 기간)는 6개월입니다.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="사용량 대시보드의 월 필터를 보여 줍니다.":::

### <a name="usage-trend"></a>사용량 추세

이 섹션에서는 선택된 계산 기간 동안 고객이 소비하는 모든 제품에 대한 총 사용 시간 및 추세를 확인할 수 있습니다. 메트릭 및 증가 추세는 꺾은선형 차트로 표시됩니다. 차트의 선 위로 마우스를 가져가면 각 월의 값이 표시됩니다. 위젯의 사용 현황 메트릭 아래 백분율 값은 선택된 계산 기간 동안 증가 또는 감소되는 양을 나타냅니다.

사용 시간에는 VM 정규화된 사용량과 VM 가공되지 않은 사용량이라는 두 가지 표현이 있습니다.

- 정규화된 사용 시간은 VM 코어 수를 고려하도록 정규화된 사용 시간으로 정의됩니다([VM 코어 수] x [가공되지 않은 사용량 시간])으로 계산됩니다. "SHAREDCORE"로 지정된 VM은 [VM 코어 수] 승수로 1/6(0.1666)을 사용합니다.
- 가공되지 않은 사용 시간은 VM이 실행된 기간을 시간 단위로 정의합니다.

[![사용량 대시보드의 정규화된 사용량과 가공되지 않은 사용량 데이터를 보여 줍니다.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>제품별 정규화된 사용량

이 섹션에서는 Azure Marketplace의 사용량 기반 제품에 대한 총 사용량 시간 및 추세를 제공합니다. 제품별 정규화된 사용량 차트는 아래에 설명되어 있습니다.

- **제품별 정규화된 사용량** 누적 세로 막대형 차트는 선택된 계산 기간에 따라 상위 5개 제품에 대한 정규화된 사용량 시간에 대한 분석 결과를 표시합니다. 상위 5개 제품은 그래프에 표시되지만 나머지 제품은 **나머지 전체** 범주로 그룹화됩니다.
- 누적 세로 막대형 차트는 선택한 날짜 범위의 월별 증가 추세를 나타냅니다. 월 열은 해당 월의 사용 시간이 가장 많은 제품의 사용 시간을 나타냅니다. 꺾은선형 차트는 보조 Y축에 그려진 증가율 추세를 표시합니다.
- 범례에서 특정 제품을 선택하여 그래프에 해당 제품만 표시할 수 있습니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="사용량 대시보드의 정규화된 사용량 제품 데이터를 보여 줍니다.":::

제품 및 해당 제품의 최대 3개 SKU를 선택하여 제품 및 선택한 SKU에 대한 월별 사용량 추세를 볼 수 있습니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="사용량 대시보드의 정규화된 사용량 제품 및 SKU 데이터를 보여 줍니다.":::

### <a name="orders-by-offers-and-skus"></a>제품 및 SKU별 주문

**제품 및 SKU별 주문** 차트는 모든 제품의 측정값과 추세를 보여 줍니다. 다음 사항에 유의하세요.

- 최상위 제품이 그래프에 표시되고 나머지 제품은 **Rest All** 로 그룹화됩니다.
- 범례에서 특정 제품을 선택하여 그래프에 해당 제품만 표시할 수 있습니다.
- 그래프에서 조각을 마우스로 가리키면 모든 제품의 총 주문 수와 비교한 주문의 수와 해당 제품의 백분율이 표시됩니다.
- **제품별 주문 추세** 는 월별 증가 추세를 표시합니다. 월 열은 제품 이름별 주문 수를 나타냅니다. 꺾은선형 차트는 z축에 그려진 증가율 추세를 표시합니다.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="사용량 대시보드의 주문 및 SKU별 주문 차트를 보여 줍니다.":::

제품 및 해당 제품의 최대 3개 SKU를 선택하여 제품, SKU 및 사용자에 대한 월 단위 추세를 볼 수 있습니다.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="사용량 대시보드의 주문 및 SKU별 주문 차트를 보여 줍니다. 제품 추세, SKU 추세 및 사용자 추세가 표시됩니다.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>다른 차원별 정규화된 사용량: VM 크기, 판매 채널 및 제품 유형

다른 차원별로는 VM 크기, 판매 채널 및 제품 유형이라는 세 가지 탭이 있습니다. 이러한 각 차원에 대한 사용 현황 메트릭 및 월별 추세를 볼 수 있습니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="사용량 대시보드의 정규화된 사용량 기타 차원 차트를 보여 줍니다.":::

### <a name="usage-by-geography"></a>지리별 사용량

선택한 계산 기간에 대해 열 지도는 지리 차원에 대한 총 사용량을 표시합니다. 지도의 밝은 색에서 짙은 색은 고객 수의 낮은 값에서 높은 값을 나타냅니다. 국가/지역을 확대하려면 테이블의 레코드를 선택합니다.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="사용량 대시보드의 정규화된 사용량 국가 차트를 보여 줍니다.":::

다음 사항에 유의하세요.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수 및 정규화된 사용량 시간의 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택하여 원래 보기로 돌아갑니다.

### <a name="usage-details-table"></a>사용량 세부 정보 표

**사용량 세부 정보** 표는 상위 1,000개의 사용량 레코드를 사용량에 따라 정렬하고 번호를 매겨서 표시합니다. 다음 사항에 유의하세요.

- 그리드의 각 열을 정렬할 수 있습니다.
- 레코드 수가 1,000보다 작은 경우 데이터를 .TSV 또는 .CSV 파일로 추출할 수 있습니다.
- 레코드 수가 1,000개를 초과하는 경우 내보낸 데이터는 비동기적으로 다운로드 페이지에 배치되며 다음 30일 동안 사용할 수 있습니다.
- **자세한 사용량 데이터** 에 필터를 적용하여 관심 있는 데이터만 표시할 수 있습니다. 국가/지역, 판매 채널, Marketplace 라이선스 유형, 사용 유형, 제품 이름, 제품 유형, 평가판, Marketplace 구독 ID, 고객 ID 및 회사 이름을 기준으로 데이터를 필터링할 수 있습니다.

_**표 1: 데이터 용어 사전**_

| 열 이름<br>사용자 인터페이스 | 특성 이름 | 정의 | 프로그래밍 방식의 열 이름<br>액세스 보고서 |
| ------------ | ------------- | ------------- | ------------- |
| Marketplace 구독 ID | Marketplace 구독 ID | 고객이 상업용 Marketplace 제품을 구매하는 데 사용한 Azure 구독과 연결된 고유 식별자입니다. ID는 이전에 Azure 구독 GUID였습니다. | MarketplaceSubscriptionId |
| MonthStartDate | 월 시작 날짜 | 월 시작 날짜는 구매 월을 나타냅니다. | MonthStartDate |
| 제품 유형 | 제품 유형 | 상업용 Marketplace 제품의 유형입니다. | OfferType |
| Azure 라이선스 유형 | Azure 라이선스 유형 | Azure 구매 고객이 체결하는 라이선싱 계약 유형입니다. 채널이라고도 합니다. 가능한 값은 다음과 같습니다.<ul><li>클라우드 솔루션 공급자</li><li>Enterprise</li><li>재판매인을 통한 Enterprise</li><li>종량제</li></ul> | AzureLicenseType |
| Marketplace 라이선스 유형 | Marketplace 라이선스 유형 | 상업용 Marketplace 제품의 청구 방법입니다. 가능한 값은 다음과 같습니다.<ul><li>Azure를 통해 청구</li><li>사용자 라이선스 필요</li><li>무료</li><li>Microsoft 대리점</li></ul> | MarketplaceLicenseType |
| SKU | SKU | 제품에 연결된 플랜입니다. | SKU |
| 고객 국가 | 고객 국가/지역 | 고객이 입력한 국가/지역 이름. 국가/지역은 고객의 Azure 구독에서 국가/지역과 다를 수 있습니다. | CustomerCountry |
| 미리 보기 SKU | 미리 보기 SKU | 값은 SKU를 “미리 보기”로 태그 지정한 경우를 알려 줍니다. 이에 따라 SKU가 태그 지정된 경우 값은 "예"이며, 사용자가 승인한 Azure 구독만 이 이미지를 배포하고 사용할 수 있습니다. SKU가 "미리 보기"로 식별되지 않은 경우 값은 "아니요"입니다. | IsPreviewSKU |
| SKU 청구 유형 | SKU 청구 유형 | 제품의 각 SKU와 연결된 청구 유형입니다. 가능한 값은 다음과 같습니다.<ul><li>무료</li><li>유료</li></ul> | SKUBillingType |
| IsInternal | 사용되지 않음 | 사용되지 않음 | 사용되지 않음 |
| VM 크기 | Virtual Machine 크기 | VM 기반 제품 유형의 경우 이 엔터티는 제품의 SKU와 연결된 VM의 크기를 나타냅니다. | VMSize |
| 클라우드 인스턴스 이름 | 클라우드 인스턴스 이름 | VM이 배포된 Microsoft Cloud입니다. | CloudInstanceName |
| ServicePlanName | 사용되지 않음 | 사용되지 않음(SKU와 동일한 정의) | ServicePlanName |
| 제품 이름 | 제품 이름 | 상업용 Marketplace 제품의 이름입니다. | OfferName |
| DeploymentMethod | 사용되지 않음 | 사용되지 않음(제품 유형과 동일한 정의) | DeploymentMethod |
| 고객 회사 이름 | 고객 회사 이름 | 고객이 입력한 회사 이름입니다. 이름은 고객의 Azure 구독에서 제공된 이름과 다를 수 있습니다. | CustomerCompanyName |
| 사용 날짜 | 사용 날짜 | 사용량 기반 자산에 대한 사용량 이벤트 생성 날짜입니다. | UsageDate |
| IsMultisolution | Is Multisolution | 제품이 Multisolution 제품 유형인지 여부를 나타냅니다. | IsMultisolution |
| 새 고객 | 사용되지 않음 | 사용되지 않음 | IsNewCustomer |
| 코어 크기 | 코어 크기 | VM 기반 제품에 연결된 코어 수입니다. | CoreSize |
| 사용 유형 | 사용 유형 | 제품에 연결된 사용량 이벤트가 다음 중 하나인지 여부를 나타냅니다.<ul><li>정규화된 사용량</li><li>가공되지 않은 사용량</li><li>요금제 사용</li></ul> | UsageType |
| 평가판 종료 날짜 | 평가판 종료 날짜 | 이 주문의 평가 기간이 종료되었거나 종료될 예정인 날짜입니다. | TrialEndDate |
| 고객 통화(CC) | 고객 통화 | 상업용 Marketplace 거래에 대해 고객이 사용하는 통화입니다. | CustomerCurrencyCC |
| 가격(CC) | 가격 | 고객 통화로 표시되는 SKU의 단가입니다. | PriceCC |
| PC(지급 통화) | 지급 통화 | 게시자는 게시자에 의해 구성된 통화에서 자산과 연결된 사용량 이벤트에 대해 지불됩니다. | PayoutCurrencyPC |
| 예상 가격(PC) | 예상 가격 | 게시자에 의해 구성된 통화의 SKU 단가입니다. | EstimatedPricePC |
| 사용량 기준 | 사용량 기준 | 지급 트랜잭션 보고서와 함께 사용량 보고서(상업용 Marketplace 분석)를 연결하는 데 사용되는 연결된 GUID입니다. 사용량 참조는 지급 트랜잭션 보고서에서 OrderId 및 LineItemId와 연결됩니다. | UsageReference |
| 사용량 단위 | 사용량 단위 | SKU와 연결된 소비 단위입니다. | UsageUnit |
| 고객 ID | 고객 ID | 고객에게 할당된 고유 식별자입니다. 고객은 Azure Marketplace 구독이 0개 이상 있을 수 있습니다. | CustomerId |
| 청구 계정 ID | 청구 계정 ID | 청구를 생성하는 계정의 식별자입니다. **청구 계정 ID** 를 **customerID** 에 매핑하여 고객, 주문 및 사용 현황 보고서에 지급 트랜잭션 보고서를 연결합니다. | BillingAccountId |
| 사용 수량 | 사용 수량 | 고객이 배포한 자산에서 사용하는 총 사용량 단위입니다.<br>사용량 유형 항목을 기준으로 합니다. 예를 들어 사용량 유형이 정규화된 사용량인 경우 사용 수량은 정규화된 사용량에 대한 것입니다. | UsageQuantity |
| NormalizedUsage | 정규화된 사용량 | 고객이 배포한 자산에서 사용하는 총 정규화된 사용량 단위입니다.<br>정규화된 사용 시간은 VM 코어 수를 고려하도록 정규화된 사용 시간으로 정의됩니다([VM 코어 수] x [가공되지 않은 사용량 시간])으로 계산됩니다. "SHAREDCORE"로 지정된 VM은 [VM 코어 수] 승수로 1/6(0.1666)을 사용합니다. | NormalizedUsage |
| MeteredUsage | 요금제 사용량 | 고객이 배포한 제품을 사용하여 구성된 미터에서 사용하는 총 사용량 단위입니다. | MeteredUsage |
| RawUsage | 원시 사용량 | 고객이 배포한 자산에서 사용하는 총 가공되지 않은 사용량 단위입니다.<br>가공되지 않은 사용량 시간은 VM이 실행된 기간을 사용량 단위로 정의합니다. | RawUsage |
| 예상 요금 소계(CC) | 예상 연장 요금(고객 통화) | 사용량과 관련된 요금을 나타냅니다. 열은 가격(CC)과 사용 수량의 곱입니다. | EstimatedExtendedChargeCC |
| 예상 요금 소계(PC) | 예상 연장 요금(지급 통화) | 사용량과 관련된 요금을 나타냅니다. 열은 예상 가격(PC)과 사용 수량의 곱입니다. | EstimatedExtended ChargePC |
| 측정기 ID | 측정기 ID | 제품의 측정기 ID를 의미합니다. | MeterId |
| 파트너 센터에서 탐지된 변칙 | 파트너 센터에서 탐지된 변칙 | **사용자 지정 측정기 차원을 사용하는 제품에 적용할 수 있습니다**.<br>게시자가 파트너 센터에 의한 변칙으로 플래그가 지정된 제품의 사용자 지정 측정기 차원에 대해 초과 사용량을 보고했는지 여부를 의미합니다. 가능한 값은 다음과 같습니다. <ul><li>0(변칙 아님)</li><li>1(변칙)</li></ul>_게시자에게 사용자 지정 측정기 차원을 사용하는 제품이 없고 프로그래밍 방식 액세스를 통해 이 열을 내보내는 경우 값은 null이 됩니다._ | PartnerCenterDetectedAnomaly |
| 게시자가 표시한 변칙 | 게시자가 표시한 변칙 | **사용자 지정 측정기 차원을 사용하는 제품에 적용할 수 있습니다**.<br>게시자가 제품의 사용자 지정 측정기 차원에 대한 고객의 초과 사용량을 있는 그대로 또는 거짓으로 승인했는지 여부를 의미합니다. 가능한 값은 다음과 같습니다.<ul><li>0(게시자가 변칙이 아닌 것으로 표시함)</li><li>1(게시자가 변칙으로 표시함)</li></ul>_게시자에게 사용자 지정 측정기 차원을 사용하는 제품이 없고 프로그래밍 방식 액세스를 통해 이 열을 내보내는 경우 값은 null이 됩니다._ | PublisherMarkedAnomaly |
| 새롭게 보고된 사용량 | 새롭게 보고된 사용량 | **사용자 지정 측정기 차원을 사용하는 제품에 적용할 수 있습니다**.<br>게시자가 비정상으로 식별한 제품의 사용자 지정 측정기 차원에 대한 고객의 초과 사용량입니다. 이 필드는 게시자가 새롭게 보고한 초과 사용량을 지정합니다.<br>_게시자에게 사용자 지정 측정기 차원을 사용하는 제품이 없고 프로그래밍 방식 액세스를 통해 이 열을 내보내는 경우 값은 null이 됩니다._ | NewReportedUsage |
| 작업 수행 시간 | 작업 수행 시간 | **사용자 지정 측정기 차원을 사용하는 제품에 적용할 수 있습니다**.<br>게시자가 제안의 사용자 지정 측정기 차원에 대한 고객의 초과 사용량을 있는 그대로 또는 거짓으로 승인한 시간을 지정합니다.<br>_게시자에게 사용자 지정 측정기 차원을 사용하는 제품이 없고 프로그래밍 방식 액세스를 통해 이 열을 내보내는 경우 값은 null이 됩니다._ | ActionTakenAt |
| 작업 수행자 | 작업 수행자 | **사용자 지정 측정기 차원을 사용하는 제품에 적용할 수 있습니다**.<br>제품의 사용자 지정 측정기 차원에 대한 고객의 초과 사용량을 있는 그대로 또는 거짓으로 승인한 사람을 지정합니다.<br>_게시자에게 사용자 지정 측정기 차원을 사용하는 제품이 없고 프로그래밍 방식 액세스를 통해 이 열을 내보내는 경우 값은 null이 됩니다._ | ActionTakenBy |
| 예상 재무 영향(USD) | 예상 재무 영향(USD) | **사용자 지정 측정기 차원을 사용하는 제품에 적용할 수 있습니다**.<br>파트너 센터에서 제품의 사용자 지정 측정기 차원에 대한 고객의 초과 사용량을 비정상으로 표시할 경우, 이 필드에서는 비정상적인 초과 사용에 대한 예상 재무 영향(USD)을 지정합니다.<br>_게시자에게 사용자 지정 측정기 차원을 사용하는 제품이 없고 프로그래밍 방식의 수단을 통해 이 열을 내보내는 경우 값은 null이 됩니다._ | EstimatedFinancialImpactUSD |
| 해당 없음 | 리소스 ID | 리소스 이름과 리소스 종류를 포함하여 리소스의 정규화된 ID입니다. 이 필드는 보고서 다운로드에서만 사용할 수 있는 데이터 필드입니다.<br>다음 형식을 사용합니다.<br> /subscriptions/{guid}/resourceGroups/{resource-group-name}/{resource-provider-namespace}/{resource-type}/{resource-name} | 해당 없음 |
|||||

### <a name="usage-page-filters"></a>사용량 페이지 필터

**사용량** 페이지 필터는 주문 페이지 수준에서 적용됩니다. 보려고 선택한 기준에 대한 차트와 사용량 주문 데이터의 그리드/내보내기에서 보려는 데이터에 대한 차트를 하나 또는 여러 필터를 선택하여 렌더링할 수 있습니다. 필터는 사용량 페이지의 오른쪽 위 모서리에서 선택한 월 범위에 대해 추출된 데이터에 적용됩니다.

VM 가공되지 않은 사용량에 대한 위젯 및 내보내기 보고서는 다음과 같은 차이가 있는 VM 정규화된 사용량과 유사합니다.

- 정규화된 사용 시간은 VM 코어 수를 고려하도록 정규화된 사용 시간으로 정의됩니다([VM 코어 수] x [가공되지 않은 사용량 시간])으로 계산됩니다. "SHAREDCORE"로 지정된 VM은 [VM 코어 수] 승수로 1/6(0.1666)을 사용합니다.
- 가공되지 않은 사용량 시간은 VM이 실행된 기간을 사용량 단위로 정의합니다.

### <a name="metered-billing-usage"></a>요금제 청구 사용량

**요금제 사용량** 탭은 미터 크기별로 사용량을 측정하는 제품 유형에 대한 사용량 정보를 제공합니다. 현재는 SaaS 제품 유형 초과분이 표시됩니다. 이 탭은 SaaS 요금 청구 사용량에 대한 초과 추세를 그래픽으로 나타냅니다.

- **미터 크기별 초과 추세**: 제품의 선택한 미터 크기에 대한 월별 초과 추세를 표시합니다. X축은 월을 나타내고 Y축은 초과 사용량을 나타냅니다. 사용자 지정 미터의 측정 단위도 Y축에 표시됩니다.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="사용량 대시보드의 VM 정규화된 사용량 차트를 보여 줍니다.":::

- **SKU별 초과 추세**: SKU/플랜별로 선택한 미터 크기의 사용량 추세를 나타냅니다. 선택한 제품에 대한 사용량이 가장 많은 상위 5개 플랜이 표시됩니다.

- **고객별 초과 추세**: 고객 리더 보드는 사용량 시간이 가장 많은 고객의 누적 목록을 나타내며, 사용자 지정 미터의 최고 사용량에 따라 순위가 지정되어 _리더 보드_ 에 표시됩니다. 선택한 미터 크기의 초과 사용량 추세를 보려면 리더 보드에서 고객을 선택합니다.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="사용량 대시보드에서 고객 차트의 요금제 사용량을 보여 줍니다.":::

사용자 지정 미터를 사용하는 제품이 여러 개 있는 경우 요금제 청구 사용량 보고서에는 사용자 지정 미터 크기에 따라 모든 제품에 대한 사용 정보가 표시됩니다.

> [!TIP]
> 위젯의 오른쪽 위 모서리에 있는 다운로드 아이콘을 사용하여 데이터를 다운로드할 수 있습니다. "엄지 손가락 위로" 또는 "엄지 손가락 아래로" 아이콘을 클릭하여 각 위젯에 대한 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 상업용 Marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 Marketplace에 대한 분석 보고서 액세스](analytics.md)를 참조하세요.
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 마켓플레이스 분석의 요약 대시보드](./summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능 형식의 주문에 대한 정보는 [상업용 Marketplace 분석의 주문 대시보드](./orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금 청구 메트릭에 관한 내용은 [상업용 Marketplace 분석의 사용량 대시보드](usage-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 Microsoft AppSource에서 제품에 대한 고객 피드백 통합 보기를 확인하려면 [파트너 센터의 평점 및 리뷰 분석 대시보드](ratings-reviews.md)를 참조하세요.
- 상업용 Marketplace 분석에 관해 자주 묻는 질문 및 데이터 용어의 종합 사전에 관한 내용은 [상업용 Marketplace 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.
