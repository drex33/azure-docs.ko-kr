---
title: 예기치 않은 Azure 요금 분석
description: Azure 구독에 대한 예기치 않은 요금을 분석하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: de7787ab9b36bee5bce2f29f79260682289419c7
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705797"
---
# <a name="analyze-unexpected-charges"></a>예기치 않은 요금 분석

조직용으로 구축된 클라우드 리소스 인프라는 복잡할 가능성이 높습니다. 많은 Azure 리소스 종류마다 요금 유형이 다를 수 있습니다. Azure 리소스를 조직의 여러 팀에서 소유할 수 있으며, 다양한 리소스에 적용되는 여러 청구 모델 유형이 있을 수 있습니다. 요금을 보다 잘 이해하려면 다음 섹션에서 하나 이상의 전략을 사용하여 분석을 시작하세요.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>요금 청구를 담당하는 리소스에 대한 청구서 검토

Azure 서비스 구매 방법에 따라 요금과 관련된 리소스를 파악할 때 사용할 수 있는 방법 및 도구가 달라집니다. 적용되는 방법을 확인하려면 먼저 [Azure 제품 유형을 확인합니다](../costs/understand-cost-mgt-data.md#determine-your-offer-type). 그런 다음, [지원되는 Azure 제품](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers) 목록에서 고객 범주를 확인합니다.

다음 문서에서는 고객 유형에 따라 청구서를 검토하는 방법을 설명하는 자세한 단계를 제공합니다. 각 문서에는 지정된 청구 기간의 사용량 및 비용 세부 정보가 포함된 CSV 파일을 다운로드하는 방법에 대한 지침이 있습니다.

- [종량제 청구서 검토 프로세스](review-individual-bill.md#charges)
- [기업계약 청구서 검토 프로세스](review-enterprise-agreement-bill.md)
- [Microsoft 고객 계약 검토 프로세스](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft 파트너 계약 검토 프로세스](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Azure 청구서는 _미터_ 를 기준으로 해당 월의 요금을 집계합니다. 미터는 시간에 따른 리소스 사용량을 추적하는 데 사용되며 청구서를 계산하는 데 사용됩니다. 가상 머신과 같은 단일 Azure 리소스를 만들면 해당 리소스에 대한 하나 이상의 미터 인스턴스가 생성됩니다.

미터에 적용되는 모든 품목을 보려면 분석할 청구서에 표시된 대로 _MeterName_ 을 기준으로 사용량 CSV 파일을 필터링하세요. 품목의 _InstanceID_ 는 요금이 발생한 실제 Azure 리소스에 해당합니다.

문제의 리소스를 식별 한 경우 Cost Management에서 비용 분석을 사용 하 여 리소스와 관련 된 비용을 추가로 분석할 수 있습니다. 비용 분석을 사용하는 방법에 대해 자세히 알아보려면 [비용 분석 시작](../costs/quick-acm-cost-analysis.md)을 참조하세요.

## <a name="review-invoiced-charges-in-cost-analysis"></a>비용 분석에서 송장 발부 요금 검토

Azure Portal에서 청구서 세부 정보를 보려면 분석 중인 청구서와 관련된 범위에 대한 비용 분석으로 이동합니다. **청구서 세부 정보** 보기를 선택합니다. 청구서 세부 정보에는 청구서에 표시된 요금이 표시됩니다.

[![청구서 세부 정보를 보여주는 예제](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

청구서 세부 정보를 보면 예기치 않은 비용이 드는 서비스를 식별하고 비용 분석에서 리소스와 직접 연관된 리소스를 확인할 수 있습니다. 예를 들어 Virtual Machines 서비스에 대한 요금을 분석하려면 **누적 비용** 보기로 이동합니다. 그런 다음, 세분성을 **일별** 로 설정하고 요금 **서비스 이름: 가상 머신** 및 그룹 요금을 **리소스** 로 필터링합니다.

[![가상 머신의 누적 비용을 보여주는 예제](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>시간에 따른 비용 급증 식별

청구 요금 변동에 영향을 준 최근 비용을 모르는 경우가 있습니다. 변경 내용을 파악하려면 비용 분석을 사용하여 [시간에 따른 일별 또는 월별 비용 분석을 확인](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month)할 수 있습니다. 보기를 만든 후에 **서비스** 또는 **리소스** 를 기준으로 요금을 그룹화하여 변경 내용을 식별합니다. 또한 데이터를 보다 잘 시각화할 수 있도록 보기를 **꺾은선형** 차트로 변경할 수도 있습니다.

![비용 분석의 시간별 비용을 보여주는 예제](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>리소스 가격 책정 및 청구 모델 확인

여러 Azure 제품 및 서비스에서 단일 리소스에 대한 요금이 발생할 수 있습니다. 각 Azure 서비스의 가격 책정에 대해 자세히 알아보려면 [제품별 Azure 가격 책정](https://azure.microsoft.com/pricing/#product-pricing) 페이지를 확인하세요. 예를 들어 Azure에서 만든 단일 VM(가상 머신)의 경우 사용량을 추적하기 위해 다음과 같은 미터가 생성될 수 있습니다. 각 미터의 가격 책정 방식은 서로 다를 수 있습니다.

- 컴퓨팅 시간
- IP 주소 시간
- 데이터 수신
- 데이터 발신
- 표준 관리형 디스크
- 표준 관리형 디스크 작업
- 표준 IO-디스크
- 표준 IO-블록 Blob 읽기
- 표준 IO-블록 Blob 쓰기
- 표준 IO-블록 Blob 삭제

VM이 생성되면 각 미터가 사용량 레코드를 내보내기 시작합니다. 사용량과 미터의 요금은 Azure 계량 시스템에서 추적됩니다. 사용 현황 CSV 파일에서 청구서를 계산하는 데 사용된 미터를 확인할 수 있습니다.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>리소스를 담당하는 사용자를 찾아 문의

특정 리소스를 담당하는 팀이 해당 리소스에 대한 변경 내용을 알고 있는 경우가 종종 있습니다. 요금이 발생한 이유를 식별할 때 이러한 팀에 문의하면 유용합니다. 예를 들어 소유 팀이 최근에 리소스를 만들었거나, SKU를 업데이트하여 리소스 요율이 변경되었거나, 코드 변경으로 인해 리소스에 대한 부하가 증가했을 수 있습니다. 리소스 소유자를 확인하는 더 많은 방법을 보려면 다음 섹션을 계속 읽으세요.

### <a name="analyze-the-audit-logs-for-the-resource"></a>리소스의 감사 로그 분석

리소스를 볼 수 있는 권한이 있는 경우 해당 감사 로그에 액세스할 수 있어야 합니다. 로그를 검토하여 리소스에 대한 가장 최근의 변경 내용을 담당했던 사용자를 찾습니다. 자세한 정보는 [Azure 활동 로그 이벤트 보기 및 검색](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)을 참조하세요.

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>리소스의 부모 범위에 대한 사용자 권한 분석

구독 또는 리소스 그룹에 대한 쓰기 권한이 있는 사용자는 일반적으로 생성된 리소스에 대한 정보를 가지고 있습니다. 리소스의 용도를 직접 설명하거나 이를 알고 있는 사람을 알려줄 수 있을 것입니다. 구독 범위에 대한 권한이 있는 사용자를 식별하려면 [Azure 리소스에 대한 사용자 액세스 확인](../../role-based-access-control/check-access.md)을 참조하세요. 리소스 그룹에도 유사한 프로세스를 사용할 수 있습니다.

## <a name="get-help-to-identify-charges"></a>요금 식별에 대한 지원 받기

위의 전략을 사용했지만 특정 요금이 부과된 이유를 여전히 모르겠거나 청구 문제와 관련하여 다른 도움이 필요한 경우 [지원 요청을 작성](https://go.microsoft.com/fwlink/?linkid=2083458)하세요.

## <a name="next-steps"></a>다음 단계

- [Cost Management를 사용 하 여 클라우드 투자를 최적화](../costs/cost-mgt-best-practices.md)하는 방법에 대해 알아봅니다.