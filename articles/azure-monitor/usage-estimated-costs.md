---
title: Azure Monitor의 모니터링 사용량 및 예상 비용
description: Azure Monitor의 모니터링 사용량 및 예상 비용 프로세스 개요 페이지
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lagayhar
ms.reviewer: Dale.Koetke
ms.openlocfilehash: 540bed61d26b2387399230324db4373172a233a1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536037"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor의 모니터링 사용량 및 예상 비용

> [!NOTE]
> 이 문서에서는 여러 Azure 모니터링 기능 간에 사용량 및 예상 비용을 확인하는 방법을 설명합니다. Azure Monitor의 특정 구성 요소와 관련된 문서에는 다음이 포함됩니다.
> - [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](logs/manage-cost-storage.md)는 데이터 보존 기간을 변경하여 비용을 제어하는 방법 및 데이터 사용량에 대한 분석 및 경고 방법을 설명합니다.
> - [Application Insights의 사용량 및 비용 관리](app/pricing.md)는 Application Insights의 데이터 사용량 분석 방법을 설명합니다.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor 가격 책정 모델

기본 Azure Monitor 청구 모델은 클라우드에 친숙한 소비 기반 가격 책정입니다(“종량제”). 사용한 양만큼만 요금을 지불합니다. 가격 책정 세부 정보는 [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)에 대해 제공됩니다. 

로그 데이터에 대한 종량제 모델 외에도 Azure Monitor Log Analytics에는 약정 계층이 있습니다. 이를 사용하면 종량제에 비해 최대 30%까지 절약할 수 있습니다. 약정 계층은 100GB/일부터 시작합니다. 약정 계층을 초과하는 사용량은 약정 계층과 동일한 GB당 가격으로 청구됩니다. 약정 계층 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/monitor/).

일부 고객은 [레거시 Log Analytics 가격 책정 계층](logs/manage-cost-storage.md#legacy-pricing-tiers) 및 [레거시 엔터프라이즈 Application Insights 가격 책정 계층](app/pricing.md#legacy-enterprise-per-node-pricing-tier)을 이용할 수 있습니다. 

## <a name="understanding-your-azure-monitor-costs"></a>Azure Monitor 비용 이해

비용을 이해하기 위해서는 두 가지 단계가 필요합니다. 첫 번째는 Azure Monitor를 모니터링 솔루션으로 고려하는 경우입니다. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>환경을 관리하는 데 드는 비용을 추정

Azure Monitor 로그를 아직 사용하고 있지 않다면 [Azure Monitor 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=monitor)를 사용하여 Azure Monitor 사용 비용을 예상할 수 있습니다. 먼저 검색 상자에 "Azure Monitor"를 입력한 후 화면에 표시되는 Azure Monitor 타일을 클릭합니다. 페이지를 Azure Monitor까지 아래로 스크롤하고 유형 드롭다운에서 옵션 중 하나를 선택합니다.

- 메트릭 쿼리 및 경고  
- Log Analytics
- Application Insights

각 항목에서 가격 계산기는 예상 사용량을 기준으로 가능한 비용을 예상할 수 있게 해줍니다.

예를 들어 Log Analytics를 사용하여 VM 수 및 각 VM에서 수집할 것으로 예상되는 데이터 양(GB)을 입력할 수 있습니다. 일반적인 Azure VM에서 일반적으로 매월 1~3GB 데이터가 수집됩니다. 이미 Azure Monitor 로그를 평가하고 있는 경우, 자신의 환경에서 데이터 통계를 사용할 수 있습니다. [모니터링되는 VM의 수](logs/manage-cost-storage.md#understanding-nodes-sending-data)와 [작업 영역에서 수집 중인 데이터의 볼륨](logs/manage-cost-storage.md#understanding-ingested-data-volume)을 확인하는 방법은 아래를 참조하세요.

Application Insights에서도 비슷하게 “애플리케이션 작업을 기반으로 데이터 볼륨 예상” 기능을 사용하도록 설정한 경우 애플리케이션에 대한 입력(클라이언트 쪽 원격 분석을 수집할 경우 월 요청 건수와 월 페이지 보기 수)을 제공하면 계산기에서 비슷한 애플리케이션에 의해 수집된 데이터 양의 중앙값과 90번째 백분위수를 알려 줍니다. 이러한 애플리케이션에는 여러 Application Insights 구성이 적용되어 있으므로(예: 기본 샘플링을 사용하는 경우와 샘플링을 사용하지 않는 경우 등) 샘플링을 사용하여 수집하는 데이터의 볼륨을 중앙값보다 훨씬 밑으로 제어할 수 있습니다. 그러나 이것은 다른 비슷한 고객의 경우를 이해하기 위한 출발점에 지나지 않습니다. Application Insights의 비용 예상에 대해 [자세히 알아보세요](app/pricing.md#estimating-the-costs-to-manage-your-application).

### <a name="understanding-your-usage-and-estimated-costs"></a>사용량 및 예상 비용 이해

Azure Monitor를 사용하여 사용량을 이해하고 추적하는 것이 중요합니다. 그리고 이를 위한 많은 도구가 있습니다. 

Azure는 [Azure Cost Management + 청구](../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) 허브에서 많은 유용한 기능을 제공합니다. **Azure Cost Management + 청구** 허브를 연 후 **Cost Management** 를 클릭하고 [범위](../cost-management-billing/costs/understand-work-scopes.md)(조사할 리소스 집합)를 선택합니다. 

그런 다음, 이전 30일 동안 Azure Monitor 비용을 보려면 **일별 비용** 타일을 클릭하고, 상대 날짜에서 “최근 30일”을 선택하고, 서비스 이름을 선택하는 필터를 추가합니다.

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insight and Analytics

그러면 다음과 같은 보기가 표시됩니다.

![Azure Cost Management 스크린샷](./media/usage-estimated-costs/010.png)

여기에서는 이 누적된 비용 요약에서 드릴인하여 “리소스별 비용” 보기의 세부 정보를 확인할 수 있습니다. 현재 가격 책정 계층에서 Azure Log 데이터는 Log Analytics 또는 Application Insights에서 시작되었는지 여부에 관계없이 동일한 측정 집합에 따라 청구됩니다. Log Analytics 또는 Application Insights 사용량 비용을 구분하려면 **리소스 유형** 에 대해 필터를 추가하면 됩니다. 모든 Application Insights 비용을 보려면 리소스 유형을 "microsoft.insights/components"로 필터링하고, Log Analytics 비용을 보려면 리소스 유형을 "microsoft.operationalinsights/workspaces"로 필터링합니다. 

사용량에 대한 추가 세부 정보는 [Azure Portal에서 사용량을 다운로드](../cost-management-billing/understand/download-azure-daily-usage.md)하여 확인할 수 있습니다. 다운로드한 스프레드시트에서 Azure 리소스별 일일 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서는 먼저 “미터 범주” 열에서 필터링을 진행하여 “Application Insights” 및 “Log Analytics”를 표시한 다음, “microsoft.insights/components 포함”에 해당되는 “인스턴스 ID” 열에서 하나의 필터를 추가하면 Application Insights 리소스의 사용량을 확인할 수 있습니다.  모든 Azure Monitor 구성 요소에 대한 로그 백엔드는 하나이므로 대부분의 Application Insights 사용량이 Log Analytics의 미터 범주를 사용하여 미터로 보고됩니다.  레거시 가격 책정 계층이 적용되는 Application Insights 리소스와 다중 단계 웹 테스트만 Application Insights의 미터 범주를 사용하여 보고됩니다.  사용량은 “사용한 수량” 열에 표시되며 각 항목에 대한 단위는 “측정 단위” 열에 표시됩니다.  [Microsoft Azure 청구 방식을 이해](../cost-management-billing/understand/review-individual-bill.md)하는 데 도움이 되는 자세한 정보를 제공합니다. 

> [!NOTE]
> 모니터링 비용을 전반적으로 이해하기 위해서는 **Azure Cost Management + Billing** 허브에서 **Cost Management** 를 사용하는 것이 좋습니다.  [Log Analytics](logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs) 및 [Application Insights](app/pricing.md#understand-your-usage-and-estimate-costs)에 대한 **사용량 및 예상 비용** 경험은 이러한 각 Azure Monitor 부분에 대해 더 자세한 인사이트를 제공합니다.

Azure Monitor 사용량 보기에 대한 또 다른 옵션은 모니터 허브의 **사용량 및 예상 비용** 페이지입니다. 여기에서는 [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/)과 같은 핵심 모니터링 기능, [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)의 사용량을 보여줍니다. 2018년 4월 이전 제공된 요금제 고객의 경우 Insights 및 Analytics 제안을 통해 구매한 Log Analytics 사용량도 여기에 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 볼 수 있습니다. `Drill-ins`는 31일 기간 동안 사용량 추세를 보여줍니다. 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.

이 예에서는 모니터링 사용량과 그에 따른 비용의 추정치를 보여 줍니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/usage-estimated-costs/001.png)

월별 사용량 열의 링크를 클릭하면 지난 31일 간의 사용량 추세를 보여 주는 차트가 열립니다. 

![노드당 포함 가로 막대형 차트 스크린 샷](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite 구독 자격

Microsoft Operations Management Suite E1 및 E2를 구매한 고객은 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 및 [Application Insights](app/pricing.md)에 대한 노드별 데이터 주입 자격이 있습니다. 지정된 구독에서 Log Analytics 작업 영역 또는 Application Insights 리소스에 대해 이러한 자격을 얻으려면: 

- Log Analytics 작업 영역에서 "노드당(OMS)" 가격 책정 계층을 사용해야 합니다.
- Application Insights 리소스는 “엔터프라이즈” 가격 책정 계층을 사용합니다.

조직에서 구매한 제품군의 노드 수에 따라 일부 구독을 종량제(GB당) 가격 책정 계층으로 이동하는 것이 이익일 수 있지만 세심한 주의가 필요합니다.

> [!WARNING]
> 조직에 현재 Microsoft Operations Management Suite E1 및 E2가 있으면 Log Analytics 작업 영역을 “노드별(OMS)” 가격 책정 계층에 두고, Application Insights 리소스를 “엔터프라이즈” 가격 책정 계층에 두는 것이 좋습니다. 
>

