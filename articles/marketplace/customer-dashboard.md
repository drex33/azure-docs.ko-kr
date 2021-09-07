---
title: 파트너 센터, Azure Marketplace, Microsoft AppSource에 대한 Microsoft 상업용 Marketplace 분석의 고객 대시보드
description: 상업용 Marketplace 분석에서 고객 대시보드를 사용하여 증가 추세를 비롯한 고객 정보에 액세스하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 11/09/2020
ms.openlocfilehash: 43332c559c309bc99754467994a4718ece78d3cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528493"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>상업용 Marketplace 분석의 고객 대시보드

이 문서에서는 파트너 센터의 고객 대시보드에 대한 정보를 제공합니다. 이 대시보드에서 그래픽 및 다운로드 가능한 형식으로 제공되는 증가 추세를 비롯한 고객 정보를 볼 수 있습니다.

>[!NOTE]
> 분석 용어에 대한 자세한 정의는 [상업용 Marketplace 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.

## <a name="customers-dashboard"></a>고객 대시보드

[고객 대시보드](https://go.microsoft.com/fwlink/?linkid=2166011)에는 제품을 구입한 고객에 대한 데이터가 표시됩니다. 다음 항목에 대한 그래픽 표현을 볼 수 있습니다.

- 활성 및 변동 고객 추세
- 기존 고객, 신규 고객, 변동 고객을 포함한 고객 성장 추세
- 주문 및 사용량별 고객
- 고객 백분위수
- 고객 유형별 주문 및 사용량
- 지역별 고객
- 고객 세부 정보 표
- 고객 페이지 필터

> [!NOTE]
> 파트너 센터에서 고객 확보와 보고 사이의 최대 대기 시간은 48시간입니다.

## <a name="elements-of-the-customers-dashboard"></a>고객 대시보드 요소

다음 섹션에서는 고객 대시보드를 사용하는 방법과 데이터를 읽는 방법에 관해 설명합니다.

파트너 센터에서 고객 대시보드에 액세스하려면 **상업용 Marketplace** 아래에서 **[분석](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  > **고객** 을 선택합니다.

### <a name="month-range"></a>월 범위

각 페이지의 오른쪽 위 모서리에서 월 범위 선택 항목을 찾을 수 있습니다. 지난 6개월 또는 12개월을 기준으로 월 범위를 선택하거나 최대 기간이 12개월인 사용자 지정 월 범위를 선택하여 **고객** 페이지 그래프의 출력을 사용자 지정합니다. 기본 월 범위(계산 기간)는 6개월입니다.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="고객 페이지의 월 필터를 보여 줍니다.":::

> [!NOTE]
> 시각화 위젯 및 내보내기 보고서의 모든 메트릭은 사용자가 선택한 계산 기간을 사용합니다.

### <a name="active-and-churned-customers-trend"></a>활성 및 변동 고객 추세

이 섹션에서는 선택한 계산 기간의 고객 증가 추세를 확인할 수 있습니다. 메트릭 및 증가 추세는 꺾은선형 차트로 표시되며 차트의 선 위로 마우스를 가져가면 각 월의 값이 표시됩니다. 위젯의 **활성 고객** 아래의 백분율 값은 선택한 계산 기간 동안 증가한 양을 나타냅니다. 예를 들어 다음 스크린샷은 선택한 계산 기간 동안 0.92%의 증가율을 보여 줍니다.

[![고객 페이지의 고객 위젯을 보여 줍니다.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

_고객 유형_ 에는 신규, 기존 및 변동 세 가지가 있습니다.

- 새 고객이 선택한 월에 처음으로 하나 이상의 제품을 획득했습니다.
- 기존 고객이 선택한 월 이전에 하나 이상의 제품을 획득했습니다.
- 변동 고객이 이전에 구매한 모든 제품을 취소했습니다. 변동 고객은 추세 위젯의 음수 축에 표시됩니다.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>기존 고객, 신규 고객, 변동 고객을 포함한 고객 성장 추세

이 섹션에서는 신규 고객, 기존 고객, 변동 고객을 비롯한 모든 고객의 수와 추세를 매월 성장 추세로 확인할 수 있습니다.

- 꺾은선형 차트는 전체 고객 성장률을 나타냅니다.
- 월 열은 신규, 기존 및 변동 고객별로 누적된 고객의 수를 나타냅니다.
- 변동 고객 수는 X축의 음의 방향으로 표시됩니다.
- 특정 범례 항목을 선택하여 상세 보기를 표시할 수 있습니다. 예를 들어 범례에서 새 고객을 선택하면 새 고객만 표시됩니다.
- 차트에서 열 위로 마우스를 가져가면 해당 달의 세부 정보만 표시됩니다.

[![고객 페이지의 고객 추세 위젯을 보여 줍니다.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>주문/사용량별 고객

**주문/사용량별 고객** 차트에는 주문, 정규화된 사용량, 가공되지 않은 사용량의 세 개의 탭이 있습니다. 주문 세부 정보를 표시하려면 **주문** 탭을 선택합니다.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="고객 페이지의 주문 및 사용량 위젯별 고객의 주문 탭을 보여 줍니다.":::

다음 사항에 유의하세요.

- 리더 보드는 주문 수에 따라 순위가 매겨진 고객에 대한 세부 정보를 표시합니다. 고객을 선택한 후 고객 상세 정보는 인접한 “상세 정보”, “SKU별 주문”, “SKUs by Seats(실제 사용자 수별 SKU)” 섹션에 표시됩니다.
- 게시자가 소유자 역할로 로그인하면 고객 프로필 세부 정보가 이 공간에 표시됩니다. 게시자가 참가자 역할로 로그인하면 이 섹션의 세부 정보를 사용할 수 없습니다.
- **SKU별 주문** 도넛형 차트에는 플랜에 대해 구매한 주문의 분석 결과가 표시됩니다. 주문 수가 가장 많은 상위 5개의 플랜이 표시되고 나머지 주문은 **나머지 모두** 로 그룹화됩니다.
- **SKUs by Seats(실제 사용자 수별 SKU)** 도넛형 차트에는 플랜에 대해 주문한 실제 사용자 수 분석이 표시됩니다. 실제 사용자 수가 가장 많은 상위 5개 플랜이 표시되고 나머지 주문은 **나머지 모두** 로 그룹화됩니다.

**정규화된 사용량** 또는 **가공되지 않은 사용량** 탭을 선택하여 사용량 세부 정보를 볼 수도 있습니다.

- 리더 보드는 사용량(시간) 수에 따라 순위가 매겨진 고객에 대한 세부 정보를 표시합니다. 고객을 선택한 후에는 인접한 “상세 정보”, “제품별 정규화된 사용량”, “VM(가상 머신) 크기별 정규화된 사용량” 섹션에 고객의 세부 정보가 표시됩니다.
- 게시자가 소유자 역할로 로그인하면 고객 프로필 정보가 이 공간에 표시됩니다. 게시자가 참가자 역할을 사용하여 로그인한 경우에는 이 섹션의 세부 정보를 사용할 수 없습니다.
- **제품별 정규화된 사용량** 도넛형 차트에는 제품에서 사용하는 사용량에 대한 분석 결과가 표시됩니다. 사용량 수가 가장 많은 상위 5개의 플랜이 표시되고 나머지 제품은 **나머지 모두** 로 그룹화됩니다.
- **VM 크기별 정규화된 사용량** 도넛형 차트에는 다른 VM 크기에서 사용되는 사용량의 분석 결과가 표시됩니다. 정규화된 사용량이 가장 많은 상위 5개의 VM 크기가 표시되고 나머지 사용량은 **나머지 모두** 로 그룹화됩니다.

### <a name="top-customers-percentile"></a>상위 고객 백분위수

**상위 고객 백분위수** 차트에는 “주문”, “정규화된 사용량”, “가공되지 않은 사용량”의 세 개의 탭이 있습니다. _상위 고객 백분위수_ 는 주문 수에 따라 x축에 표시됩니다. y축에는 고객의 주문 수가 표시됩니다. 보조 y축(선 그래프)에는 총 주문 수의 누적 백분율이 표시됩니다. 꺾은선형 차트의 점 위로 마우스 커서를 이동하여 세부 정보를 표시할 수 있습니다.

[![고객 페이지의 상위 고객 백분위수 위젯의 주문 탭을 보여 줍니다.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>고객 유형별 주문 및 사용량

**고객 유형별 주문/사용량** 차트에는 새 고객과 기존 고객 간에 분할된 주문 수, 정규화된 사용량 및 원시 사용 시간이 차트에서 선택한 각각의 주문, 표준화 및 원시 사용을 기반으로 표시됩니다.

이 차트에는 다음 정보가 표시됩니다.

- 새 고객이 하나 이상의 제품을 획득했거나 동일한 월에 처음으로 사용량을 보고했습니다(y축).
- 기존 고객이 이전에 제품을 획득했거나 보고된 월간 사용량을 보고했습니다(y축).

[![고객 페이지의 고객 유형별 주문 위젯의 주문 탭을 보여 줍니다.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>지역별 고객

선택한 계산 기간에 대해 열 지도에는 총 고객 수와 지리 차원의 새로 추가된 고객 비율이 표시됩니다. 지도의 밝은 색에서 짙은 색은 고객 수의 낮은 값에서 높은 값을 나타냅니다. 국가 또는 지역을 확대하려면 표의 레코드를 선택합니다.

[![고객 페이지의 지역별 주문 위젯의 주문 탭을 보여 줍니다.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

다음 사항에 유의하세요.

- 지도를 이동하여 정확한 위치를 볼 수 있습니다.
- 특정 위치를 확대할 수 있습니다.
- 열 지도에는 특정 위치의 고객 수, 주문 수, 정규화된 사용량 시간의 세부 정보를 볼 수 있는 보조 표가 있습니다.
- 표에서 국가/지역을 검색하고 선택하여 지도에서 해당 위치를 확대할 수 있습니다. 지도에서 **홈** 단추를 선택하여 원래 보기로 되돌아갑니다.

### <a name="customer-details-table"></a>고객 세부 정보 표

**고객 세부 정보** 표에서는 제품 중 하나를 처음 획득한 날짜 기준으로 정렬된 상위 1,000개의 고객 번호가 지정된 목록을 표시합니다. 세부 정보 리본에서 확장 아이콘을 선택하여 섹션을 확장할 수 있습니다.

다음 사항에 유의하세요.

- 고객의 개인 정보는 고객이 동의한 경우에만 사용할 수 있습니다. 소유자 역할 수준의 사용 권한으로 로그인한 경우에만 이 정보를 볼 수 있습니다.
- 그리드의 각 열을 정렬할 수 있습니다.
- 레코드 수가 1,000개 미만인 경우 데이터를 .CSV 또는 .TSV 파일로 추출할 수 있습니다.
- 레코드 수가 1000개를 초과하는 경우 내보낸 데이터는 다음 30일 동안 비동기적으로 다운로드 페이지에 배치됩니다.
- 표에 필터를 적용하여 관심있는 데이터만 표시할 수 있습니다. 회사 이름, 고객 ID, Marketplace 구독 ID, Azure 라이선스 유형, 취득일, 취소일, 고객 메일, 고객 국가/지역/시/도/우편 번호, 고객 언어 등을 기준으로 데이터를 필터링할 수 있습니다.
- 보호된 고객이 제품을 구매하면 **고객 상세 데이터** 정보가 마스킹됩니다(************).
- 회사 이름, 고객 이름, 고객 메일 등의 고객 차원 세부 정보는 Azure Marketplace 또는 Microsoft AppSource 트랜잭션 수준이 아닌 조직 ID 수준에 있습니다.

_**표 1: 데이터 용어 사전**_

| 열 이름<br>사용자 인터페이스 | 특성 이름 | 정의 | 프로그래밍 방식의 열 이름<br>액세스 보고서 |
| ------------ | ------------- | ------------- | ------------- |
| Marketplace 구독 ID | Marketplace 구독 ID | 고객이 상업용 Marketplace 제품을 구매하는 데 사용한 Azure 구독과 연결된 고유 식별자입니다. 인프라 제품의 경우 고객의 Azure 구독 GUID입니다. SaaS 제품의 경우 SaaS 구매에는 Azure 구독이 필요하지 않으므로 0으로 표시됩니다. | MarketplaceSubscriptionId |
| DateAcquired | 취득일 | 게시된 제품을 고객이 최초 구매한 날짜입니다. | DateAcquired |
| DateLost | 취소일 | 고객이 이전에 구매한 모든 제품 중 마지막으로 취소한 날짜입니다. | DateLost |
| Provider Name | Provider Name | Microsoft와 고객 간의 관계와 관련된 공급자의 이름입니다. 고객이 재판매인을 통한 Enterprise인 경우 재판매인이 됩니다. CSP(클라우드 솔루션 공급자)가 관련된 경우 이는 CSP가 됩니다. | ProviderName |
| 공급자 이메일 | 공급자 이메일 | Microsoft와 고객 간의 관계와 관련된 공급자의 메일 주소입니다. 고객이 재판매인을 통한 Enterprise인 경우 재판매인이 됩니다. CSP(클라우드 솔루션 공급자)가 관련된 경우 이는 CSP가 됩니다. | ProviderEmail |
| FirstName | 고객 이름 | 고객이 입력한 이름입니다. 이름은 고객의 Azure 구독에 제공된 이름과 다를 수 있습니다. | FirstName |
| LastName | 고객 성 | 고객이 입력한 성입니다. 성은 고객의 Azure 구독에 제공된 성과 다를 수 있습니다. | LastName |
| Email | 고객 이메일 | 최종 고객이 입력한 이메일 주소입니다. 메일은 고객의 Azure 구독에 제공된 메일 주소와 다를 수 있습니다. | Email |
| 고객 회사 이름 | 고객 회사 이름 | 고객이 입력한 회사 이름입니다. 회사 이름은 고객의 Azure 구독에 제공된 구/군/시와 다를 수 있습니다. | CustomerCompany 이름 |
| CustomerCity | 고객 구/군/시 | 고객이 입력한 구/군/시 이름입니다. 구/군/시는 고객의 Azure 구독에 제공된 구/군/시와 다를 수 있습니다. | CustomerCity |
| 고객 우편 번호 | 고객 우편 번호 | 고객이 입력한 우편 번호입니다. 우편 번호는 고객의 Azure 구독에 제공된 우편 번호와 다를 수 있습니다. | CustomerPostal 번호 |
| CustomerCommunicationCulture | 고객 통신 언어 | 고객이 통신용으로 선택한 기본 설정 언어입니다. | CustomerCommunicationCulture |
| CustomerCountryRegion | 고객 국가/지역 | 고객이 입력한 국가/지역 이름. 국가/지역은 고객의 Azure 구독에 제공된 국가/지역과 다를 수 있습니다. | CustomerCountryRegion |
| AzureLicenseType | Azure 라이선스 유형 | Azure 구매 고객이 체결하는 라이선싱 계약 유형입니다. _채널_ 이라고도 합니다. 가능한 값은 다음과 같습니다.<ul><li>클라우드 솔루션 공급자</li><li>Enterprise</li><li>재판매인을 통한 Enterprise</li><li>종량제</li></ul> | AzureLicenseType |
| PromotionalCustomers | 홍보 연락 옵트인 | 이 값은 고객이 게시자의 홍보를 위한 연락을 받도록 사전에 옵트인했는지 여부를 알려 줍니다. 현재는 고객에게 해당 옵션이 제공되지 않으므로 보드 전체에 “아니요”가 표시됩니다. 이 기능이 배포되면 그에 따라 업데이트를 시작할 예정입니다. | PromotionalCustomers |
| CustomerState | 고객 시/도 | 고객이 제공한 거주중인 시/도입니다. 시/도는 고객의 Azure 구독에 제공된 시/도와 다를 수 있습니다. | CustomerState |
| CommerceRootCustomer | 상거래 루트 고객 | 하나의 청구 계정 ID를 여러 고객 ID와 연결할 수 있습니다.<br>청구 계정 ID와 고객 ID의 조합은 여러 상업용 Marketplace 구독과 연결될 수 있습니다.<br>상거래 루트 고객은 구독의 고객 이름을 나타냅니다. | CommerceRootCustomer |
| 고객 ID | 고객 ID | 고객에게 할당된 고유 식별자입니다. 고객은 Azure Marketplace 구독이 0개 이상 있을 수 있습니다. | CustomerId |
| 청구 계정 ID | 청구 계정 ID | 청구를 생성하는 계정의 식별자입니다. **청구 계정 ID** 를 **customerID** 에 매핑하여 고객, 주문 및 사용 현황 보고서에 지급 트랜잭션 보고서를 연결합니다. | BillingAccountId |
| 고객 유형 | 고객 유형 | 이 필드의 값은 고객의 유형을 나타냅니다. 가능한 값은 다음과 같습니다.<ul><li>개인</li> <li>organization</li></ul> | CustomerType |
|||||

### <a name="customers-page-filters"></a>고객 페이지 필터

고객 페이지 필터는 고객 페이지 수준에서 적용됩니다. 여러 필터를 선택하여 보려고 선택한 기준과 “자세한 주문 데이터” 그리드/내보내기에서 보려고 하는 데이터에 대한 차트를 렌더링할 수 있습니다. 필터는 고객 페이지의 오른쪽 위 모서리에서 선택한 월 범위에 대해 추출된 데이터에 적용됩니다.

> [!TIP]
> 위젯의 오른쪽 위 모서리에 있는 다운로드 아이콘을 사용하여 데이터를 다운로드할 수 있습니다. "엄지 손가락 위로" 또는 "엄지 손가락 아래로" 아이콘을 클릭하여 각 위젯에 대한 피드백을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 상업용 Marketplace에서 사용할 수 있는 분석 보고서의 개요는 [파트너 센터에서 상업용 Marketplace에 대한 분석 보고서 액세스](analytics.md)를 참조하세요.
- 제품에 대한 Marketplace 활동을 요약하는 집계 데이터의 그래프, 추세 및 값은 [상업용 Marketplace 분석의 요약 대시보드](./summary-dashboard.md)를 참조하세요.
- 그래픽 및 다운로드 가능한 형식의 주문에 관한 자세한 내용은 [상업용 Marketplace 분석의 주문 대시보드](./orders-dashboard.md)를 참조하세요.
- VM(가상 머신) 제품 사용량 및 요금 청구 메트릭에 관한 내용은 [상업용 Marketplace 분석의 사용량 대시보드](./usage-dashboard.md)를 참조하세요.
- 최근 30일 동안의 다운로드 요청 목록은 [상업용 Marketplace 분석의 다운로드 대시보드](downloads-dashboard.md)를 참조하세요.
- Azure Marketplace 및 Microsoft AppSource에서 제품에 대한 고객 피드백 통합 보기를 확인하려면 [파트너 센터의 평점 및 리뷰 분석 대시보드](ratings-reviews.md)를 참조하세요.
- 상업용 Marketplace 분석에 관해 자주 묻는 질문 및 데이터 용어의 종합 사전에 관한 내용은 [상업용 Marketplace 분석 용어 및 일반적인 질문](./analytics-faq.yml)을 참조하세요.
