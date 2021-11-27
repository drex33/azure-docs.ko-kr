---
title: Azure Monitor 사용량 및 예상 비용 모니터링
description: Azure Monitor 사용량 및 예상 비용에 페이지를 사용하는 프로세스의 개요를 참조하세요.
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lagayhar
ms.reviewer: Dale.Koetke
ms.openlocfilehash: 672237480448c0cbd4de7ae2eba8a6f5bc44cfdf
ms.sourcegitcommit: 625ffc4a23b7b71680aaf572fede507053d4ef03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2021
ms.locfileid: "133176989"
---
# <a name="monitor-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor 사용량 및 예상 비용 모니터링

이 문서에서는 여러 Azure 모니터링 기능의 사용량 및 예상 비용을 보는 방법을 설명합니다. 

## <a name="azure-monitor-pricing-model"></a>Azure Monitor 가격 책정 모델

기본 Azure Monitor 청구 모델은 클라우드 친화적인 소비 기반 가격 책정(종량제)입니다. 사용한 것에 대해서만 요금을 지불합니다. 가격 책정 세부 정보는 [경고, 메트릭 및 알림에](https://azure.microsoft.com/pricing/details/monitor/)사용할 수 있습니다. [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/); 및 [애플리케이션 Insights.](https://azure.microsoft.com/pricing/details/application-insights/) 

로그 데이터에 대한 종량제 모델 외에도 Azure Monitor Log Analytics에는 약정 계층이 있습니다. 종량제 가격 책정에 비해 30%까지 절감할 수 있습니다. 약정 계층은 하루에 100GB에서 시작합니다. 약정 계층을 초과하는 모든 사용량은 약정 계층과 동일한 기가바이트당 가격으로 청구됩니다. [약정 계층 가격 책정에 대해 자세히 알아보세요.](https://azure.microsoft.com/pricing/details/monitor/)

일부 고객은 [레거시 Log Analytics 가격 책정 계층](logs/manage-cost-storage.md#legacy-pricing-tiers) 및 레거시 Enterprise Application Insights 가격 책정 계층 에 액세스할 수 [있습니다.](app/pricing.md#legacy-enterprise-per-node-pricing-tier) 

## <a name="azure-monitor-costs"></a>Azure Monitor 비용

비용을 이해하기 위한 두 가지 단계가 있습니다. 모니터링 솔루션으로 Azure Monitor 고려할 때 비용을 예측한 다음 배포 후 실제 비용을 추적합니다. 

### <a name="estimate-the-costs-to-manage-your-environment"></a>환경을 관리하는 데 드는 비용 예측

Azure Monitor 로그를 아직 사용하고 있지 않다면 [Azure Monitor 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=monitor)를 사용하여 Azure Monitor 사용 비용을 예상할 수 있습니다. 검색 **상자에** **Azure Monitor** 입력한 **다음, Azure Monitor** 타일을 선택하여 시작합니다. 페이지를 Scroll down **Azure Monitor** **형식** 드롭다운 목록에서 옵션 중 하나를 선택합니다.

- **메트릭 쿼리 및 경고**  
- **Log Analytics**
- **Application Insights**

이러한 각 유형에서 가격 계산기는 예상 사용률에 따라 예상 비용을 예측하는 데 도움이 됩니다.

예를 들어 Log Analytics를 사용하면 각 VM에서 수집할 것으로 예상되는 VM(가상 머신) 수와 기가바이트의 데이터를 입력할 수 있습니다. 일반적으로 매월 1GB~3GB의 데이터가 Azure VM에서 수집됩니다. 이미 Azure Monitor 로그를 평가하고 있는 경우 사용자 환경의 데이터 통계를 사용할 수 있습니다. [모니터링되는 VM 수와](logs/manage-cost-storage.md#understanding-nodes-sending-data) 작업 영역에서 [수집 중인 데이터 볼륨을](logs/manage-cost-storage.md#understanding-ingested-data-volume)확인할 수 있습니다.

애플리케이션 Insights 경우 애플리케이션 작업 기능에 **따라 데이터 볼륨 추정을** 사용하도록 설정하면 애플리케이션에 대한 입력을 제공할 수 있습니다(클라이언트 쪽 원격 분석을 수집하는 경우 월별 요청 및 월별 페이지 보기). 그런 다음 계산기는 유사한 애플리케이션에서 수집하는 데이터의 중앙값 및 90번째 백분위수 양을 알려줍니다. 

이러한 애플리케이션은 애플리케이션 Insights 구성의 범위에 걸쳐 있습니다. 예를 들어, 일부는 기본 샘플링이 있고, 일부는 샘플링이 없으며, 일부는 사용자 지정 샘플링을 갖습니다. 따라서 여전히 샘플링을 사용하여 수집한 데이터의 양을 중앙값 수준보다 훨씬 낮게 줄일 수 있습니다. 그러나 이는 비슷한 고객에게 표시되는 내용을 이해하기 위한 시작점입니다. [Application Insights 비용 예측에 대해 자세히 알아봅니다.](app/pricing.md#estimating-the-costs-to-manage-your-application)

### <a name="track-usage-and-costs"></a>사용량 및 비용 추적

Azure Monitor 사용을 시작한 후 사용량을 이해하고 추적하는 것이 중요합니다. 다양한 도구 집합이 이 추적을 용이하게 하는 데 도움이 될 수 있습니다. 

#### <a name="azure-cost-management--billing"></a>Azure Cost Management + 청구

Azure는 [Azure Cost Management + Billing](../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) 허브에서 유용한 기능을 제공합니다. 허브를 연 후 **Cost Management** 선택하고 [범위(조사할](../cost-management-billing/costs/understand-work-scopes.md) 리소스 집합)를 선택합니다. Cost Management 데이터에 대한 추가 액세스 권한이 필요할 수[있습니다(자세한 정보).](../cost-management-billing/costs/assign-access-acm-data.md)

지난 30일 동안의 Azure Monitor 비용을 보려면 일일 **비용** 타일을 선택하고 **상대 날짜** 에서 **지난 30일을** 선택한 다음, 서비스 이름을 선택하는 필터를 추가합니다.

- **Azure Monitor**
- **Application Insights**
- **Log Analytics**
- **Insight and Analytics**

결과는 다음 예제와 같은 뷰입니다.

![비용 정보가 있는 Azure Cost Management 보여 주는 스크린샷](./media/usage-estimated-costs/010.png)

이 누적 비용 요약에서 드릴인하여 **리소스별 비용** 보기에서 세부 정보를 확인할 수 있습니다. 현재 가격 책정 계층에서 Azure 로그 데이터는 Log Analytics 또는 애플리케이션 Insights 발생 여부에 관계없이 동일한 미터 집합에 대해 요금이 청구됩니다. 

Log Analytics 또는 Application Insights 사용량 비용을 구분하려면 **리소스 유형** 에 대해 필터를 추가하면 됩니다. 모든 애플리케이션 Insights 비용을 보려면 **리소스 종류를** **microsoft.insights/components로 필터링합니다.** Log Analytics 비용의 경우 **리소스 종류를** **microsoft.operationalinsights/workspaces로 필터링합니다.** 

사용량에 대한 자세한 내용은 [Azure Portal 에서 사용량을 다운로드하는](../cost-management-billing/understand/download-azure-daily-usage.md)경우 사용할 수 있습니다. 다운로드한 Excel 스프레드시트에서 일일 Azure 리소스당 사용량을 확인할 수 있습니다. **미터 범주** 열을 필터링하여 애플리케이션 Insights 및 Log Analytics를 표시하여 **애플리케이션 Insights** 리소스에서 사용량을 찾을 수 **있습니다.** 그런 다음 **인스턴스 ID** 열에 **포함된 microsoft.insights/components** 필터를 추가합니다. 

모든 Azure Monitor 구성 요소에 대한 단일 로그 백 엔드가 있기 때문에 대부분의 애플리케이션 Insights 사용량은 미터 범주에 대한 **Log Analytics를** 사용하여 **미터에** 보고됩니다. 레거시 가격 책정 계층 및 다중 단계 웹 테스트의 애플리케이션 Insights 리소스만 **미터 범주용** **애플리케이션 Insights** 보고됩니다. 사용량은 **사용량** 열에 표시되고 각 항목의 단위는 **측정 단위** 열에 표시됩니다. [Microsoft Azure 청구 방식을 이해](../cost-management-billing/understand/review-individual-bill.md)하는 데 도움이 되는 자세한 정보도 준비되어 있습니다. 

#### <a name="usage-and-estimated-costs"></a>사용량 및 예상 비용

Azure Monitor 사용량 보기에 대한 또 다른 옵션은 모니터 허브의 **사용량 및 예상 비용** 페이지입니다. 이 페이지에서는 [경고, 메트릭 및 알림과](https://azure.microsoft.com/pricing/details/monitor/)같은 핵심 모니터링 기능의 사용 현황을 보여 줍니다. [Azure Log Analytics;](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Azure 애플리케이션 Insights.](https://azure.microsoft.com/pricing/details/application-insights/) 2018년 4월 이전에 사용할 수 있는 가격 책정 플랜을 사용하는 고객의 경우 이 페이지에는 Insights 및 Analytics 제안을 통해 구매한 Log Analytics 사용량도 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 볼 수 있습니다. 드릴인은 31일 동안의 사용량 추세를 보여 줍니다. 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.

이 예에서는 모니터링 사용량과 그에 따른 비용의 추정치를 보여 줍니다.

![사용량 및 예상 비용을 보여 주는 Azure Portal 스크린샷.](./media/usage-estimated-costs/001.png)

**월별 사용량** 열의 링크를 선택하여 지난 31일 동안의 사용 추세를 보여 주는 차트를 엽니다. 

![노드당 포함된 데이터 볼륨에 대한 막대형 차트를 보여 주는 스크린샷](./media/usage-estimated-costs/002.png)

> [!NOTE]
> 모니터링 비용을 전반적으로 이해하기 위해서는 **Azure Cost Management + Billing** 허브에서 **Cost Management** 를 사용하는 것이 좋습니다.  [Log Analytics](logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs) 및 애플리케이션에 대한 **사용량 및 예상 비용** [환경은 Azure Monitor](app/pricing.md#understand-your-usage-and-estimate-costs) 각 부분에 대해 심층적인 인사이트를 제공할 Insights 있습니다.

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite 구독 자격

Microsoft Operations Management Suite E1 및 E2를 구매한 고객은 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 및 [Application Insights](app/pricing.md)에 대한 노드별 데이터 주입 자격이 있습니다. 고객이 구독의 Log Analytics 작업 영역 또는 애플리케이션 Insights 리소스에 대해 이러한 자격을 받을 수 있습니다. 

- Log Analytics 작업 영역은 OMS(Per-Node) 가격 책정 계층을 사용해야 합니다.
- 애플리케이션 Insights 리소스는 Enterprise 가격 책정 계층을 사용해야 합니다.

조직에서 구매한 제품군의 노드 수에 따라 일부 구독을 GB당(종량제) 가격 책정 계층으로 이동하는 것이 유리할 수 있지만 신중하게 고려해야 합니다.

> [!TIP]
> 조직에서 E1 또는 E2를 Microsoft Operations Management Suite 경우 일반적으로 Log Analytics 작업 영역을 OMS(Per-Node) 가격 책정 계층에 유지하고 애플리케이션 Insights 리소스를 Enterprise 가격 책정 계층에 유지하는 것이 가장 좋습니다. 
>

## <a name="next-steps"></a>다음 단계

Azure Monitor 특정 구성 요소에 대한 비용 정보를 얻습니다.

- [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](logs/manage-cost-storage.md)는 데이터 보존 기간을 변경하여 비용을 제어하는 방법 및 데이터 사용량에 대한 분석 및 경고 방법을 설명합니다.
- [Application Insights의 사용량 및 비용 관리](app/pricing.md)는 Application Insights의 데이터 사용량 분석 방법을 설명합니다.
