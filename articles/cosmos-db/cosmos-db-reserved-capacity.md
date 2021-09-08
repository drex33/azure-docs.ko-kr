---
title: 비용 최적화를 위한 Azure Cosmos DB에서 예약된 용량
description: Azure Cosmos DB 예약 용량을 구매하여 컴퓨팅 비용을 절약하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 7726960ac4fc5a4a3a64fe8b51e23e77e157ba6d
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037863"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Azure Cosmos DB에서 예약된 용량을 활용해 비용 최적화
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 예약된 용량을 통해 1년 또는 3년 동안 Azure Cosmos DB 리소스 예약을 약정하여 비용을 절약할 수 있습니다. Azure Cosmos DB 예약 용량을 사용하여 Cosmos DB 리소스에 대 해 프로비전된 처리량에 할인을 받을 수 있습니다. 리소스의 예로 데이터베이스 및 컨테이너(테이블, 컬렉션 및 그래프)를 들 수 있습니다.

Azure Cosmos DB 예약 용량은 1년 또는 3년 선불 약정을 통해 Cosmos DB 비용을 정상 가격의 &mdash;최대 65%까지 크게 낮출 수 있습니다. 예약 용량은 청구 할인을 제공하며, Azure Cosmos DB 리소스의 런타임 상태에는 영향을 주지 않습니다.

Azure Cosmos DB 예약 용량은 리소스에 대해 프로비전된 처리량을 포함합니다. 스토리지 및 네트워킹 요금은 포함하지 않습니다. 예약 용량을 구매하는 즉시 예약 특성과 일치하는 처리량 요금은 더이상 종량제 요금으로 부과되지 않습니다. 예약에 대한 자세한 내용은 [Azure 예약](../cost-management-billing/reservations/save-compute-costs-reservations.md) 문서를 참조하세요.

Azure Cosmos DB 예약 용량은 [Azure Portal](https://portal.azure.com)에서 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](../cost-management-billing/reservations/prepare-buy-reservation.md)로 처리할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

* 종량제 요금이 적용되는 하나 이상의 Enterprise 구독 또는 개별 구독에 대한 소유자 역할을 해야 합니다.  
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가** 를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리 담당자 또는 영업 담당자만 Azure Cosmos DB 예약 용량을 구입할 수 있습니다.

## <a name="determine-the-required-throughput-before-purchase"></a>구매 전 필요한 처리량 결정

예약된 용량의 크기는 기존 또는 곧 배포될 Azure Cosmos DB 리소스에서 매 시간 사용할 총 처리량을 기반으로 해야 합니다. 예를 들어: 일관된 시간별 사용 패턴이 있는 경우 30,000 RU/s의 예약된 용량을 구매합니다. 이 예에서 프로비전된 처리량 30,000 RU/s는 종량제 요금을 사용하여 청구됩니다. 프로비전된 처리량이 1시간 동안 30,000 RU/s 미만이면 해당 시간에 대한 추가로 예약된 용량이 낭비됩니다.

시간별 사용량 패턴에 따라 구매 추천 사항을 계산합니다. 최근 7일, 30일 및 60일에 대한 사용량을 분석하고, 절감액을 최대화하는 예약된 용량 구매가 권장됩니다. 다음 단계를 사용하여 Azure Portal에서 권장되는 예약 크기를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  

2. **모든 서비스** > **예약** > **추가** 를 차례로 선택합니다.

3. **구매 예약** 창에서 **Azure Cosmos DB** 를 선택합니다.

4. 권장 예약을 보려면 **권장** 탭을 선택합니다.

다음 특성을 기준으로 추천 사항을 필터링할 수 있습니다.

- **기간** (1년 또는 3년)
- **청구 빈도** (월별 또는 선불)
- **처리량 유형** (RU/s와 다중 지역 쓰기 RU/s)

또한 추천 구성의 범위를 단일 리소스 그룹, 단일 구독 또는 전체 Azure 등록 내에 지정할 수 있습니다. 

다음은 권장 사항 예제입니다.

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="예약된 용량 추천 사항":::

30,000 RU/s 예약을 구매하는 이 추천 사항은 3년 간의 예약에서 30,000 RU/s의 예약 크기를 절감하는 것을 의미합니다. 이 경우 추천 사항은 최근 30일의 Azure Cosmos DB 사용량을 기준으로 계산됩니다. 이 고객의 최근 30일의 Azure Cosmos DB 사용이 향후 사용을 나타내는 것으로 예상되는 경우 30,000 RU/s 예약을 구매하여 절감액을 극대화합니다.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 예약 용량 구매

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  

2. **모든 서비스** > **예약** > **추가** 를 차례로 선택합니다.  

3. **구매 예약** 창에서 **Azure Cosmos DB** 를 선택하여 새 예약을 구매합니다.  

4. 아래 표에서 설명한 대로 필요한 필드를 채웁니다.

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="예약 용량 양식 채우기":::

   |필드  |설명  |
   |---------|---------|
   |범위   |   얼마나 많은 구독이 예약과 연결된 청구 혜택을 사용할 수 있는지 제어하는 옵션입니다. 또한 예약이 특정 구독에 적용되는 방식을 제어합니다. <br/><br/>  **공유** 를 선택하면 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행되는 Azure Cosmos DB 인스턴스에 적용됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 경우 공유 범위에는 계정 관리자가 만든 종량제 요금을 사용하는 모든 개별 구독이 포함됩니다.  <br/><br/>  **단일 구독** 을 선택하면 예약 할인이 선택한 구독의 Azure Cosmos DB 인스턴스에 적용됩니다. <br/><br/> **단일 리소스 그룹** 을 선택하면 선택한 구독의 Azure Cosmos DB 인스턴스와 해당 구독 내에서 선택한 리소스 그룹에 예약 할인이 적용됩니다. <br/><br/> 예약 용량을 구입한 후 예약 범위를 변경할 수 있습니다.  |
   |Subscription  |   Azure Cosmos DB 예약 용량에 대한 요금을 지불하는 데 사용되는 구독입니다. 선택한 구독에 대한 지불 방법은 비용을 청구하는 데 사용됩니다. 구독은 다음 유형 중 하나여야 합니다. <br/><br/>  기업계약(제품 번호: MS-AZR-0017P 또는 MS-AZR-0148P): Enterprise 구독에 대한 요금은 등록의 Azure Prepayment(이전에는 금액 약정이라고 함) 잔액에서 차감되거나 초과분에 대한 요금으로 청구됩니다. <br/><br/> 종량제 요금을 사용하는 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P): 종량제 요금을 사용하는 개별 구독의 경우 요금은 구독에 대한 신용 카드 또는 청구서 지불 방법으로 청구됩니다.    |
   | 리소스 그룹 | 예약된 용량 할인이 적용되는 리소스 그룹입니다. |
   |용어  |   1년 또는 3년입니다.   |
   |처리량 유형   |  처리량은 요청 단위로 프로비전됩니다. 두 가지 설정(단일 지역 쓰기 및 여러 지역 쓰기) 모두에 대해 프로비전된 처리량에 대한 예약을 구입할 수 있습니다. 처리량 유형에는 두 개의 값이 있습니다. 시간당 100 RU/s 및 시간당 100 다중 지역 쓰기 RU/s.|
   | 예약된 용량 단위| 예약하려는 처리량이 크기입니다. 하위 지역별로 모든 Cosmos DB 리소스(예: 데이터베이스 또는 컨테이너)에 필요한 처리량을 확인하여 이 값을 계산할 수 있습니다. 그런 다음, 이 값을 Cosmos 데이터베이스에 연결할 하위 지역 수로 곱합니다. 예를 들어 지역마다 100만 RU/초 용량이 필요한 5개의 하위 지역이 있는 경우 구매할 예약 용량으로 500만 RU/초를 선택합니다. |


5. 양식을 채운 후에는 예약된 용량을 구매하는 데 필요한 가격이 계산됩니다. 출력에는 선택한 옵션으로 얻을 수 있는 할인의 백분율이 표시됩니다. 다음 클릭 **선택**

6. **구매 예약** 창에서 예약의 할인 및 가격을 검토합니다. 이 예약 가격은 모든 하위 지역에서 처리량이 프로비전되는 Azure Cosmos DB 리소스에 적용됩니다.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="예약된 용량 요약":::

7. **검토 + 구매** 를 선택한 다음 **지금 구입** 을 선택합니다. 구매가 성공적으로 완료되면 다음과 같은 페이지가 표시됩니다.

예약이 구입되면 예약 조건과 일치하는 기존 Azure Cosmos DB 리소스에 즉시 적용됩니다. 기존 Azure Cosmos DB 리소스가 없으면 배포 시 예약 조건과 일치하는 새 Cosmos DB 인스턴스에 예약이 적용됩니다. 예약 기간은 두 경우 모두 구매하는 즉시 시작됩니다.

예약이 만료되는 경우 Azure Cosmos DB 인스턴스는 계속 실행되지만 일반 종량제 요금으로 청구됩니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

예약 할인은 예약 범위 및 특성과 일치하는 Azure Cosmos DB 리소스에 자동으로 적용됩니다. 예약 범위는 Azure Portal, PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

*  예약 용량 할인이 Azure Cosmos DB에 적용되는 방법을 알아보려면 [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)를 참조하세요.

* Azure 예약에 대해 자세히 알아보려면 다음 문서를 참조하세요.

   * [Azure 예약은 무엇인가요?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Azure 예약 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [종량제 구독의 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [파트너 센터 CSP 프로그램에서 Azure 예약](/partner-center/azure-reservations)

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).