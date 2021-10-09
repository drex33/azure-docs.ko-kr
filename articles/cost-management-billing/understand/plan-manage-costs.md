---
title: Azure 비용 관리 계획
description: Azure 비용 관리를 계획하고 Azure 계정에 대한 비용 추적 및 관리 기능을 사용하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: d1a76078db3acd79d1f59ce43852d538edac7964
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710890"
---
# <a name="plan-to-manage-azure-costs"></a>Azure 비용 관리 계획

이 문서는 Azure 비용 관리를 계획하는 방법을 시작하는 데 도움이 됩니다. Azure에 등록하면 몇 가지 방법을 통해 지출을 보다 명확히 파악할 수 있습니다.

- [가격 계산기](https://azure.microsoft.com/pricing/calculator/), Azure 가격표를 사용하여 서비스를 추가하기 전에 또는 Azure Portal에서 서비스를 추가하면서 예상 비용을 얻습니다.
- [예산](../costs/tutorial-acm-create-budgets.md), [경고](../costs/cost-mgt-alerts-monitor-usage-spending.md) 및 [비용 분석](../costs/quick-acm-cost-analysis.md)을 사용하여 비용을 모니터링합니다.
- 청구서의 요금을 [상세 사용 현황 파일](../manage/download-azure-invoice-daily-usage-date.md)과 비교하여 검토합니다.
- [청구](/rest/api/billing/) 및 [사용량](/rest/api/consumption/) API를 사용하여 청구 및 비용 데이터를 사용자 고유의 보고 시스템과 통합합니다.
- EA(기업계약), CSP(클라우드 솔루션 공급자) 및 Azure 스폰서쉽 고객을 위한 추가 리소스와 도구를 사용합니다.
- [Azure 체험 계정](https://azure.microsoft.com/free/)으로 [가장 인기 있는 Azure 서비스 중 일부를 12개월 동안 무료로](../manage/create-free-services.md) 사용합니다. 아래에 나열된 권장 사항과 함께 [체험 계정에서 요금 청구 방지](../manage/avoid-charges-free-account.md)를 참조하세요.

Azure 구독을 취소해야 하는 경우 [Azure 구독 취소](../manage/cancel-azure-subscription.md)를 참조하세요.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Azure 서비스를 추가하기 전에 예상 비용 구하기

다음 도구 중 하나를 사용하여 Azure 서비스 사용에 대한 비용을 예측합니다.
- Azure 요금 계산기
- Azure 가격표
- Azure portal

다음 섹션의 이미지에서는 미국 달러로 가격 책정 예를 보여 줍니다. 언급된 가격은 설명을 돕기 위한 예시일 뿐입니다. 실제 비용을 의미하는 것은 아닙니다. 

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>가격 계산기를 사용하여 온라인으로 비용 예측

[가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 확인하여 추가하려는 서비스의 월별 비용을 예측합니다. 통화를 변경하여 현지 통화로 추정할 수 있습니다.

![가격 계산기 메뉴의 스크린샷](./media/plan-manage-costs/pricing-calc.png)

모든 자사 Azure 서비스의 예상 비용을 확인할 수 있습니다. 예를 들어 아래 스크린샷에서 A1 Windows VM(Virtual Machine)은 전체 시간 동안 실행하는 경우 컴퓨팅 시간 기준으로 매월 66.96달러가 소요될 것으로 예측됩니다.

![A1 Windows VM의 예상 월별 비용을 보여 주는 가격 계산기 스크린샷](./media/plan-manage-costs/pricing-calc-vm.png)

언급된 가격은 설명을 돕기 위한 예시일 뿐입니다. 실제 비용을 의미하는 것은 아닙니다.

가격 책정에 대한 자세한 내용은 [가격 책정 FAQ](https://azure.microsoft.com/pricing/faq/)를 참조하세요. Azure 영업 직원에게 문의하려면 FAQ 페이지 맨 위에 표시된 전화 번호로 전화하세요.

### <a name="review-prices"></a>가격 검토

EA(기업계약) 또는 MCA(Microsoft 고객 계약)를 통해 Azure에 액세스할 수 있는 경우 Azure 계정에 대한 가격표를 보고 다운로드할 수 있습니다. 가격표는 모든 Azure 서비스에 대한 가격이 포함된 Excel 파일입니다. 자세한 내용은 [Azure 가격 책정 보기 및 다운로드](../manage/ea-pricing.md)를 참조하세요.

다른 구독 유형을 사용하는 경우 [Azure Retail Prices API](/rest/api/cost-management/retail-prices/azure-retail-prices)를 사용하여 표준 소매 가격을 가져올 수 있습니다.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

Azure Portal에서 서비스를 추가하면서 월별 예상 비용을 확인할 수 있습니다. 예를 들어 Windows VM의 크기를 선택하는 경우 컴퓨팅 시간에 대해 예상되는 월별 비용이 표시됩니다.

![예제: 월별 예상 비용을 보여 주는 A1 Windows VM](./media/plan-manage-costs/vm-size-cost.png)

언급된 가격은 설명을 돕기 위한 예시일 뿐입니다. 실제 비용을 의미하는 것은 아닙니다.

## <a name="monitor-costs-when-using-azure-services"></a>Azure 서비스를 사용할 때 비용 모니터링
다음 도구를 사용하여 비용을 모니터링할 수 있습니다.

- 예산 및 비용 경고
- 비용 분석

### <a name="track-costs-with-budgets-and-cost-alerts"></a>예산 및 비용 경고를 사용하여 비용 추적

[예산](../costs/tutorial-acm-create-budgets.md)을 만들어 비용을 관리하고, [경고](../costs/cost-mgt-alerts-monitor-usage-spending.md)를 만들어 관련자에게 비정상 지출 및 과다 지출을 자동으로 알립니다.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> 비용 분석을 사용하여 비용 검색 및 분석

Azure 서비스가 실행되면 비용을 정기적으로 확인하여 Azure 지출을 추적합니다. 비용 분석을 사용하여 Azure 사용량에 대한 비용이 발생한 위치를 파악할 수 있습니다.

[Azure Portal에서 Cost Management + 청구 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade)로 이동합니다.

화면의 왼쪽에서 **비용 분석** 을 클릭하여 서비스, 위치 및 구독과 같은 다양한 피벗을 통해 분석된 현재 비용을 확인합니다. 서비스를 추가하거나 구입한 후 데이터가 표시될 때까지 24시간 동안 기다립니다. 기본적으로 비용 분석에는 사용자가 있는 범위의 비용이 표시됩니다. 예를 들어 아래 스크린샷에는 Contoso 청구 계정에 대한 비용이 표시됩니다. [범위] 필을 사용하여 비용 분석에서 다른 범위로 전환합니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](../costs/understand-work-scopes.md#scopes)을 참조하세요.

![Azure Portal의 비용 분석 보기의 스크린 샷](./media/plan-manage-costs/cost-analysis.png)

태그, 리소스 종류 및 시간 범위와 같은 다양한 속성을 기준으로 필터링할 수 있습니다. 속성에 대한 필터를 추가하고 필터링할 값을 선택하려면 **필터 추가** 를 클릭합니다. 보기를 쉼표로 구분된 값(.csv) 파일로 내보내려면 **내보내기** 를 선택합니다.

또한 차트의 레이블을 눌러 해당 레이블의 일별 지출 기록을 확인할 수 있습니다. 예를 들어 아래 스크린샷에서 가상 머신을 클릭하면 일별 VM 실행 비용이 표시됩니다.

:::image type="content" source="./media/plan-manage-costs/cost-history.png" alt-text="Azure Portal의 사용 기록 보기 스크린 샷" lightbox="./media/plan-manage-costs/cost-history.png" :::

## <a name="optimize-and-reduce-costs"></a>비용 최적화 및 절감

비용 관리 원칙에 익숙하지 않은 경우 [Cost Management 사용하여 클라우드 투자를 최적화하는 방법을](../costs/cost-mgt-best-practices.md)읽어보세요.

Azure Portal에서 VM에 대한 자동 종료 및 Advisor 권장 지침을 사용하여 Azure 비용을 최적화하고 절감할 수도 있습니다.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM에 대한 자동 종료와 같은 비용 절감 기능 사용 고려

시나리오에 따라 Azure Portal에서 VM에 대한 자동 종료를 구성할 수 있습니다. 자세한 내용은 [Azure Resource Manager를 사용한 VM에 대한 자동 종료](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)를 참조하세요.

![포털의 자동 종료 옵션 스크린샷](./media/plan-manage-costs/auto-shutdown.png)

자동 종료는 전원 옵션을 사용하여 VM 내에서 종료할 때와는 다릅니다. 자동 종료는 VM을 중지한 후 할당 취소하여 추가 사용 요금 부과를 중지합니다. 자세한 내용은 VM 상태에 대한 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)의 가격 책정 FAQ를 참조하세요.

개발 및 테스트 환경에 대한 추가 비용 절감에 기능에 대해서는 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)를 확인하세요.

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor 권장 지침 설정 및 검토

[Azure Advisor](../../advisor/advisor-overview.md)는 사용량이 낮은 리소스를 식별하여 비용을 절감하는 데 도움을 줍니다. Azure Portal에서 **Advisor** 를 검색합니다.

![Azure Portal의 Azure Advisor 단추 스크린 샷](./media/plan-manage-costs/advisor-button.png)

왼쪽에서 **비용** 을 선택합니다. **비용** 탭에 실행 가능한 추천 사항이 표시됩니다.

![Advisor cost 권장 지침 예제 스크린 샷](./media/plan-manage-costs/advisor-action.png)

언급된 가격은 설명을 돕기 위한 예시일 뿐입니다. 실제 비용을 의미하는 것은 아닙니다.

비용 절감을 위한 Advisor 권장 지침에 대한 단계별 자습서는 [권장 사항에서 비용 최적화](../costs/tutorial-acm-opt-recommendations.md) 자습서를 검토합니다.

## <a name="integrate-with-billing-and-consumption-apis"></a>청구 및 사용량 API와 통합

Azure [청구](/rest/api/billing/) 및 [사용량](/rest/api/consumption/) API를 사용하여 청구 및 비용 데이터를 프로그래밍 방식으로 가져옵니다. RateCard API 및 사용 현황 API를 함께 사용하여 청구된 사용량을 확인합니다. 자세한 내용은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](../manage/consumption-api-overview.md#usage-details-api)를 참조하세요.

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> 추가 리소스 및 특수 사례

### <a name="ea-csp-and-sponsorship-customers"></a>EA, CSP 및 스폰서쉽 고객
계정 관리자 또는 Azure 파트너에 시작하도록 알립니다.

| 제안 | 리소스 |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA(기업 계약) | [EA 포털](https://ea.azure.com/), [도움말 문서](https://ea.azure.com/helpdocs) 및 [Power BI 보고서](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| CSP(클라우드 솔루션 공급자) | 공급자에게 알림 |
| Azure 스폰서쉽 | [스폰서쉽 포털](https://www.microsoftazuresponsorships.com/) |

대기업의 IT를 관리하는 경우 [Azure 엔터프라이즈 스 캐폴드](/azure/architecture/cloud-adoption-guide/subscription-governance) 및 [엔터프라이즈 IT 백서](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf)(.pdf 다운로드, 영어 버전만 제공)를 읽어보세요.

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Azure Portal에서 기업계약 비용 보기

엔터프라이즈 비용 보기는 현재 공개 미리 보기 상태입니다. 참고할 항목:

- 구독 비용은 사용을 기반으로 하며 사전에 지불한 금액, 초과분, 포함된 수량, 조정 및 세금을 포함하지 않습니다. 실제 요금은 등록 수준에서 계산됩니다.
- Azure Portal 내에 표시되는 금액은 엔터프라이즈 포털에 있는 금액과 다를 수 있습니다. 엔터프라이즈 포털의 업데이트는 Azure Portal에 변경 내용이 표시되기 전에 몇 분 정도 걸릴 수 있습니다.
- 비용이 표시되지 않는 경우 다음 이유 중 하나에 해당할 수 있습니다.
    - 구독 수준에서 권한이 없습니다. Enterprise 비용 보기를 표시하려면 구독 수준에서 청구 읽기 권한자, 읽기 권한자, 참가자 또는 소유자여야 합니다.
    - 사용자는 계정 소유자이며 등록 관리자가 “AO 보기 요금” 설정을 비활성화했습니다.  등록 관리자에게 비용에 대한 액세스 권한을 문의하세요.
    - 사용자는 부서 관리자이며 등록 관리자가 **DA 보기 요금** 설정을 비활성화했습니다.  등록 관리자에게 액세스 권한을 문의하세요.
    - 채널 파트너를 통해 Azure를 구매했고 파트너가 가격 정보를 릴리스하지 않았습니다.  
- 비용과 관련된 설정을 업데이트하는 경우 엔터프라이즈 포털에서 액세스하면 변경 내용이 Azure Portal에 표시될 때까지 몇 분 정도 지연됩니다.
- 지출 한도 및 송장 지침은 EA 구독에 적용되지 않습니다.

### <a name="check-your-subscription-and-access"></a>구독 및 액세스 권한 확인

비용을 확인하려면 비용 또는 청구 정보에 대한 계정 또는 구독 수준 액세스 권한이 필요합니다. 액세스 권한은 청구 계정 유형에 따라 달라집니다. 청구 계정에 대해 자세히 알아보고 청구 계정 유형을 확인하려면 [Azure Portal에서 청구 계정 보기](../manage/view-all-accounts.md)를 참조하세요.

MOSP(Microsoft 온라인 서비스 프로그램) 청구 계정을 통해 Azure에 액세스할 수 있는 경우 [Azure의 청구 정보에 대한 액세스 관리](../manage/manage-billing-access.md)를 참조하세요.

EA(기업계약) 청구 계정을 통해 Azure에 액세스할 수 있는 경우 [Azure의 Azure 기업계약 관리 역할 이해](../manage/understand-ea-roles.md)를 참조하세요.

MCA(Microsoft 고객 계약) 청구 계정을 통해 Azure에 액세스할 수 있는 경우 [Azure의 Microsoft 고객 계약 관리 역할 이해](../manage/understand-mca-roles.md)를 참조하세요.

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>서비스 인시던트에 대한 Service Level Agreement(서비스 수준 계약) 크레딧 요청

SLA(서비스 수준 계약)에서는 작동 시간 및 연결에 대한 Microsoft의 정책을 설명합니다. 서비스 인시던트는 Azure 서비스에서 가동 시간 또는 연결에 영향을 주는 문제가 발생하는 경우(*중단* 이라고도 함) 보고됩니다. SLA에 설명된 대로 각 서비스에 대한 서비스 수준이 달성 및 유지 관리되지 않는 경우 월별 서비스 요금 중 일부에 대한 크레딧을 받을 수 있습니다.

크레딧을 요청하려면

[Azure Portal](https://portal.azure.com/)에 로그인합니다. 계정이 여러 개인 경우 Azure 가동 중지 시간의 영향을 받는 계정을 사용해야 합니다. 그런 다음, 새 지원 요청을 만듭니다.

**문제 유형** 에서 **청구** 를 선택한 다음, **문제 유형** 에서 **환불 요청** 을 선택합니다.

세부 정보를 추가하여 SLA 크레딧을 요청하고 있음을 지정하고, 영향을 받는 서비스(VM, 웹 사이트 등)뿐만 아니라 날짜/시간/표준 시간대도 지정합니다.

마지막으로, 연락처 세부 정보를 확인하고 **만들기** 를 선택하여 요청을 제출합니다.

일부 서비스의 경우 SLA를 적용하기 위한 필수 구성 요소가 있습니다. 예를 들어 가상 머신에는 동일한 가용성 집합에 배포된 인스턴스가 둘 이상 있어야 합니다.

자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 및 [Azure 서비스에 대한 SLA 요약](https://azure.microsoft.com/support/legal/sla/summary/) 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [지출 한도](../manage/spending-limit.md)를 사용하여 과다 지출을 방지하는 방법을 알아봅니다.
- [Azure 비용 분석](../costs/quick-acm-cost-analysis.md)을 시작합니다.