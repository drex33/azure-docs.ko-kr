---
title: Azure API Management에 대한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용하여 Azure API Management에 대한 비용을 계획 및 관리하는 방법을 알아봅니다.
author: dlepow
ms.author: danlep
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 8baf9e9e7ff1ae12fc5c53af9aa2a479e1ce8342
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670313"
---
# <a name="plan-and-manage-costs-for-api-management"></a>API Management에 대한 비용 계획 및 관리

이 문서에서는 Azure API Management의 비용을 계획 및 관리하는 방법을 설명합니다. 비용을 예측하는 서비스에 대한 리소스를 추가하기 전에 Azure 가격 책정 계산기를 사용하여 API Management 비용을 계획할 수 있습니다. API Management 리소스 사용을 시작한 후에는 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 예측 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수도 있습니다. 

API Management 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 API Management에 대한 비용을 계획하고 관리하는 방법을 설명하지만, 사용자에게는 타사 서비스를 비롯한 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Cost Management에서의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-api-management"></a>API Management를 사용하기 전에 비용 예측

API Management를 추가하기 전에 [Azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 비용을 예측합니다. 

1. *API Management* 를 검색하거나 **통합** > **API Management** 를 선택합니다.
1. API Management 서비스 인스턴스에 대한 기본 예상 비용을 추가하려면 **보기** 를 선택합니다.

> [!NOTE]
> 이 예제에 표시된 비용은 데모용으로만 사용됩니다. 최신 가격 책정 정보는 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="개발자 계층의 비용 예측":::

* 기본 예상 비용은 **개발자** [서비스 계층](api-management-features.md)에서 [용량 단위](api-management-capacity.md)가 1인 API Management 서비스 인스턴스를 기반으로 합니다. 개발자 계층은 비프로덕션 사용 사례 및 평가를 위한 것입니다. 서비스 수준 계약에서 지원되지 않습니다.

* 추가 용량 단위 또는 다른 서비스 계층에 대한 비용을 예측하려면 **단위** 및 **계층** 드롭다운에서 기타 옵션을 선택합니다.

* 기능 가용성 및 서비스 계층에 따라 [자체 호스팅 게이트웨이](self-hosted-gateway-overview.md)의 사용에 대한 추가 요금이 적용될 수 있습니다.

추가 가격 책정 및 기능에 대한 자세한 내용은 다음을 참조하세요.

* [API Management 가격](https://azure.microsoft.com/pricing/details/api-management/)
* [Azure API Management 계층의 기능 기반 비교](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>API Management에서 통화 크레딧 사용

Azure 선불(이전에는 현금 약정 금액이라고 함)을 사용하여 API Management 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="understand-the-full-billing-model"></a>전체 청구 모델 이해

API Management를 통해 Azure 리소스를 사용하는 경우 비용 또는 청구 가능 미터가 발생합니다. Azure 리소스 사용 단위 비용은 다음에 따라 달라집니다.
* 시간 간격(초, 분, 시간 및 일)
* 단위 사용량(바이트, 메가바이트 등)
* 트랜잭션 수

### <a name="how-youre-charged-for-api-management"></a>API Management 요금 청구 방법

API Management를 사용하여 Azure 리소스를 만들거나 사용하는 경우 작업 중인 계층에 따라 요금이 청구됩니다. [가장 적합한 계층을 선택](./api-management-features.md)하는 방법에 대해 자세히 알아보세요.

| 계층 | Description |
| ----- | ----------- |
| Consumption | 고정 비용이 발생하지 않습니다. 특정 임계값을 초과하는 서비스에 대한 API 호출 수를 기준으로 요금이 청구됩니다. |
| 개발자, 기본, 표준 및 프리미엄 | [단위](./api-management-capacity.md) 및 [자체 호스팅 게이트웨이](./self-hosted-gateway-overview.md) 수에 따라 월별 비용이 발생합니다. 자체 호스팅 게이트웨이는 개발자 계층에 무료로 제공됩니다. 언제든지 다른 서비스 계층으로 [업그레이드](./upgrade-and-scale.md)합니다. |

가상 네트워크, 가용성 영역 및 다중 지역 쓰기와 같은 API Management가 포함된 다른 Azure 리소스를 사용하는 경우 추가 요금이 발생할 수도 있습니다. 청구 주기가 끝날 때 각 측정 단위의 요금이 합산됩니다. 청구서 또는 송장에는 모든 API Management 비용에 대한 섹션이 표시됩니다. 각 측정 단위에 대한 별도의 줄 항목이 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

API Management 사용이 시작되는 즉시 비용이 발생합니다. [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 또는 Azure 가격 책정 계산기를 통해 비용을 확인할 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격(일, 현재, 전 월, 연도)에 대한 그래프 및 테이블에서 API Management 비용을 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.

> [!NOTE]
> 이 예제에 표시된 비용은 데모용으로만 사용됩니다. 비용은 리소스 사용량과 현재 가격 책정에 따라 달라집니다.

비용 분석에서 API Management 비용을 보려면 다음을 수행합니다.

1. [Azure Portal](https://azure.microsoft.com)에 로그인합니다.
1. **Cost Management + Billing** 창을 열고 메뉴에서 **비용 관리** 를 선택한 다음, **청구 범위** 를 선택합니다. 예를 들어 목록에서 구독을 선택합니다.
1. 메뉴에서 **Cost Management** 를 선택한 다음, **비용 분석** 을 선택합니다.
1. 기본적으로 모든 서비스에 대한 월별 비용은 첫 번째 도넛형 차트에 표시됩니다. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="구독에 대한 월별 비용":::

1. API Management와 같은 단일 서비스에 대한 비용으로 범위를 좁히려면 **필터 추가** 를 선택한 다음, **서비스 이름** 을 선택합니다. 그런 다음, **API Management** 를 선택합니다.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="API Management에 대한 누적 비용을 보여 주는 예제":::

앞의 예제에서 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치)별 비용 및 리소스 그룹별 API Management 비용 또한 표시됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터를 사용하면 추가 비용을 발생시키는 새 리소스를 실수로 만드는 일을 방지할 수 있습니다. 예산 생성 시 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 다른 사용자가 비용에 대한 추가 데이터 분석을 수행하는 경우에 유용합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 세트를 검색하려면 비용 데이터를 내보내는 것이 좋습니다.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>API Management의 비용을 관리하고 줄이기 위한 기타 방법

### <a name="scale-using-capacity-units"></a>용량 단위를 사용하여 크기 조정

소비 서비스 계층을 제외하고 API Management는 [*용량 단위*](api-management-capacity.md)를 추가하거나 제거하여 크기 조정을 지원합니다. API Management 인스턴스에서 부하가 증가하는 경우 더 높은 서비스 계층으로 업그레이드하는 것보다 용량 단위를 추가하는 것이 더 경제적일 수 있습니다. 단위의 최대 수는 서비스 계층에 따라 달라집니다.

각 용량 단위에는 서비스 계층에 따라 달라지는 특정 요청 처리 기능이 있습니다. 예를 들어 기본 계층의 단위는 초당 약 1,000개의 요청에 대한 예상 최대 처리량을 갖습니다. 

단위를 추가하거나 제거할 때 용량 및 비용을 비례적으로 조정합니다. 예를 들어 표준 계층의 두 단위는 초당 약 2,000개의 요청에 대한 예상 처리량을 제공합니다. 실제 처리량은 요청 또는 응답의 크기, 연결 패턴, 요청하는 클라이언트 수 및 기타 요인으로 인해 달라질 수 있습니다.

API Management 인스턴스의 용량 메트릭을 [모니터링](api-management-howto-use-azure-monitor.md)하여 더 많은 부하를 수용할 수 있도록 API Management 인스턴스를 크기 조정하거나 업그레이드할지 여부를 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.
- API Management [용량](api-management-capacity.md)에 대해 알아봅니다.
- [Azure Portal](upgrade-and-scale.md)을 사용하여 API Management 크기 조정하고 업그레이드하는 단계 및 [자동 크기 조정](api-management-howto-autoscale.md)에 대해 알아봅니다.
