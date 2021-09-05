---
title: Azure Logic Apps에 대한 비용 관리 계획
description: Azure Portal에서 비용 분석을 사용하여 Azure Logic Apps에 대한 비용을 계획하고 관리하는 방법을 알아봅니다
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 05/25/2021
ms.openlocfilehash: b6e23d008a0171e8b71c032349943b809b4bfc92
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667098"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Azure Logic Apps에 대한 비용 계획 및 관리

이 문서는 Azure Logic Apps에 대한 비용을 계획하고 관리하는 데 도움이 됩니다. 이 서비스를 사용하여 리소스를 만들거나 추가하기 전에 먼저 Azure 가격 계산기를 사용하여 비용을 예측합니다. Logic Apps 리소스 사용을 시작하면 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 사용하여 예산을 설정하고 비용을 모니터링할 수 있습니다. 작업을 수행할 수 있는 식별하기 위해 예측 비용을 검토하고 지출 추세를 모니터링할 수도 있습니다.

Logic Apps에 대한 비용은 Azure 청구서 월별 비용의 일부일 뿐입니다. 이 문서에서는 Logic Apps에 대한 비용을 예측하고 관리하는 방법에 대해 설명하지만, 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다. Logic Apps에 대한 비용 관리에 익숙해지면 비슷한 방법을 적용하여 구독에 사용되는 모든 Azure 서비스에 대한 비용을 관리할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)는 대부분의 Azure 계정 유형을 지원합니다. 지원되는 모든 계정 유형을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다.

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>청구 모델 이해

Azure Logic Apps는 새 리소스를 배포할 때 [비용이 발생](https://azure.microsoft.com/pricing/details/logic-apps/)하는 Azure 인프라에서 실행됩니다. [관련 Azure 리소스와 함께 Logic Apps 서비스에 대한 청구 모델](logic-apps-pricing.md)을 이해하고, 배포된 리소스를 변경할 때 이러한 종속성으로 인한 비용을 관리해야 합니다.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Azure Logic Apps에서 일반적으로 발생하는 비용

Azure Logic Apps 서비스는 사용자가 만들고 사용하는 리소스에 따라 다양한 가격 책정 모델을 적용합니다.

* 다중 테넌트 Azure Logic Apps에서 만들고 실행하는 논리 앱 리소스는 [사용량(유료) 가격 책정 모델](../logic-apps/logic-apps-pricing.md#consumption-pricing)을 사용합니다.

* 단일 테넌트 Azure Logic Apps 서비스에서 만들고 실행하는 논리 앱 리소스는 [호스팅 플랜 가격 책정 모델](../logic-apps/logic-apps-pricing.md#standard-pricing)을 사용합니다.

* [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 만들고 실행하는 논리 앱 리소스는 [ISE 가격 책정 모델](../logic-apps/logic-apps-pricing.md#ise-pricing)을 사용합니다.

논리 앱에서 사용하기 위해 만들 때 비용이 발생하는 다른 리소스는 다음과 같습니다.

* [통합 계정](../logic-apps/logic-apps-pricing.md#integration-accounts)은 B2B 통합을 구축하기 위해 사용자가 만들고 논리 앱에 연결하는 별도의 리소스입니다. 통합 계정은 사용하는 통합 계정 유형 또는 *계층* 을 기반으로 하여 요율이 책정되는 [고정 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts)을 사용합니다.

* [ISE](../logic-apps/logic-apps-pricing.md#ise-pricing)는 가상 네트워크의 리소스에 직접 액세스해야 하는 논리 앱의 배포 위치에 만드는 별도의 리소스입니다. ISE는 요율이 사용자가 만든 ISE SKU 및 기타 설정을 기반으로 하는 [ISE 가격 책정 모델](../logic-apps/logic-apps-pricing.md#ise-pricing)을 사용합니다. 그렇지만 데이터 보존 및 스토리지 사용량에는 비용이 발생하지 않습니다.

* [사용자 지정 커넥터](../logic-apps/logic-apps-pricing.md#consumption-pricing)는 논리 앱에서 사용할 수 있도록 미리 빌드된 커넥터가 없는 REST API에 대해 만드는 별도의 리소스입니다. 사용자 지정 커넥터 실행은 ISE에서 커넥터를 사용하는 경우를 제외하고 [사용량 가격 책정 모델](../logic-apps/logic-apps-pricing.md#consumption-pricing)을 사용합니다.

<a name="storage-operations-costs"></a>

#### <a name="storage-operations-and-costs"></a>스토리지 작업 및 비용

Azure Logic Apps는 모든 스토리지 작업에 [Azure Storage](../storage/index.yml)를 사용합니다. 다중 테넌트 Azure Logic Apps를 사용하여 스토리지 사용량 및 비용을 논리 앱에 연결합니다. [데이터 보존 및 스토리지 사용량](../logic-apps/logic-apps-pricing.md#storage-operations)은 [고정 가격 책정 모델](../logic-apps/logic-apps-pricing.md#storage-operations)을 사용하여 비용을 계산합니다. 예를 들어 실행 기록의 입력 및 출력은 논리 앱에서 독립적으로 만들고 관리하고 액세스하는 스토리지 리소스와는 다른 백그라운드 스토리지에 유지됩니다.

단일 테넌트 Azure Logic Apps를 사용하여 고유한 Azure [Storage 계정](../azure-functions/storage-considerations.md#storage-account-requirements)을 사용할 수 있습니다. 이 기능은 Logic Apps 데이터에 대해 더 많은 제어와 유연성을 제공합니다. *상태 저장* 워크플로가 작업을 실행할 때 Azure Logic Apps 런타임은 스토리지 트랜잭션을 수행합니다. 예를 들어 큐는 예약에 사용되고 테이블 및 Blob은 워크플로 상태를 저장하는 데 사용됩니다. 스토리지 비용은 워크플로 콘텐츠에 따라 변경됩니다. 트리거, 작업 및 페이로드가 다르면 스토리지 작업 및 요구 사항도 다릅니다. 스토리지 트랜잭션은 [Azure Storage 가격 책정 모델](https://azure.microsoft.com/pricing/details/storage/)을 따릅니다. 스토리지 비용은 Azure 청구서에 별도로 표시됩니다.

단일 테넌트 Azure Logic Apps의 경우, [Logic Apps 스토리지 계산기](https://logicapps.azure.com/calculator)를 사용하여 워크플로가 실행할 수 있는 스토리지 작업 수와 해당 비용을 파악할 수 있습니다. 샘플 워크플로를 선택하거나 기존 워크플로 정의를 사용할 수 있습니다. 첫 번째 계산은 워크플로의 스토리지 작업 수를 예측합니다. 그런 다음, 이러한 수치를 사용하여 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)에서 가능한 비용을 예상할 수 있습니다. 자세한 내용은 [단일 테넌트 Azure Logic Apps 워크플로에 대한 스토리지 요구 사항 및 비용 예측](estimate-storage-costs.md)을 검토하세요.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>리소스 삭제 후 비용이 발생할 수 있음

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

논리 앱이 삭제되면 Logic Apps 서비스에서 새 워크플로 인스턴스를 만들거나 실행하지 않습니다. 그러나 진행 중이거나 보류 중인 모든 실행은 완료될 때까지 계속됩니다. 이러한 실행의 수에 따라 이 프로세스에는 시간이 다소 걸릴 수 있습니다. 자세한 내용은 [논리 앱 관리](manage-logic-apps-with-azure-portal.md#delete-logic-apps)를 참조하세요.

논리 앱이 삭제된 후 다음과 같은 리소스가 있는 경우 해당 리소스는 계속 존재하며 삭제될 때까지 비용이 발생합니다.

* 해당 리소스에 연결하는 논리 앱과는 별도로 만들고 관리하는 Azure 리소스(예: Azure 함수 앱, 이벤트 허브, 이벤트 그리드 등)

* 통합 계정

* ISE(통합 서비스 환경)

  [ISE가 삭제](ise-manage-integration-service-environment.md#delete-ise)되더라도 연결된 Azure 가상 네트워크, 서브넷 및 기타 관련 리소스는 계속 존재합니다. ISE를 삭제한 후 가상 네트워크 또는 서브넷을 삭제하려면 특정 시간까지 기다려야 할 수 있습니다.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Azure Logic Apps에서 금액 크레딧 사용

Azure Logic Apps 요금은 EA 금액 약정 크레딧을 사용하여 지불할 수 있습니다. 그러나 Azure Marketplace의 제품과 서비스를 포함하여 타사 제품 및 서비스에 대한 요금은 EA 금액 약정 크레딧을 사용하여 지불할 수 없습니다.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>비용 예측

Azure Logic Apps를 사용하여 리소스를 만들기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 비용을 예측합니다. 자세한 내용은 [Azure Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)을 참조하세요.

1. [Azure 가격 계산기 페이지](https://azure.microsoft.com/pricing/calculator/)의 왼쪽 메뉴에서 **통합** > **Azure Logic Apps** 를 차례로 선택합니다.

   !["Azure Logic Apps"가 선택된 Azure 가격 계산기를 보여 주는 스크린샷](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Azure Logic Apps 가격 계산기를 볼 수 있을 때까지 페이지를 아래로 스크롤합니다. Azure Logic Apps와 직접 관련된 Azure 리소스에 대한 다양한 섹션에서 사용하려는 리소스의 수와 해당 리소스를 사용할 수 있는 간격의 수를 입력합니다.

   다음 스크린샷에서는 계산기를 사용하여 비용을 예측하는 예를 보여 줍니다.

   ![Azure 가격 계산기의 예상 비용을 보여 주는 예](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. 관련된 새 리소스를 만들고 사용할 때 예상되는 비용을 업데이트하려면 이 계산기로 돌아와서 해당 리소스를 업데이트합니다.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>예산 및 경고 만들기

Azure 계정 또는 구독에 대한 비용을 사전에 관리하려면 [Azure Cost Management 및 청구](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 서비스 및 기능을 사용하여 [예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 및 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다.  예산 및 경고는 Azure 구독 및 리소스 그룹에 대해 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다.

예산 및 비용 임계값과 비교한 지출을 기반으로 하여 경고는 자동으로 관련자에게 지출 이상과 과다 지출 위험에 대해 알립니다. 모니터링을 더 세분화하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하는 예산을 만들 수도 있습니다. 필터는 추가 비용이 드는 새 리소스를 실수로 만들지 않도록 도움을 줍니다. 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>비용 모니터링

리소스 사용량 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 단위 사용량(바이트, 메가바이트 등)에 따라 달라집니다. 몇 가지 예로 일, 현재 및 이전 월, 연도가 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 비용 분석 기능을 사용하면 비용을 다양한 시간 간격에 대한 그래프 및 표로 볼 수 있습니다. 예산 및 비용 예측을 만든 경우 예산이 초과되고 과다 지출이 발생한 위치를 쉽게 찾을 수도 있습니다.

Azure에서 만들거나 사용하는 리소스에 대한 비용이 발생하면 다음과 같은 방법으로 이러한 비용을 검토하고 모니터링할 수 있습니다.

* Azure Monitor를 사용하여 [논리 앱 실행 및 스토리지 사용량 모니터링](#monitor-billing-metrics)

* [Azure Cost Management 및 청구](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 사용하여 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 실행

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-usage"></a>논리 앱 실행 및 스토리지 사용량 모니터링

Azure Monitor를 사용하면 특정 논리 앱에 대한 다음 메트릭을 확인할 수 있습니다.

* 청구 가능한 작업 실행
* 청구 가능한 트리거 실행
* 기본 작업 실행에 대한 청구 사용량
* 표준 커넥터 실행에 대한 청구 사용량
* 스토리지 사용량에 대한 청구 사용량
* 총 청구 가능한 실행 수

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>실행 및 스토리지 사용량 메트릭 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다. 논리 앱 메뉴의 **모니터링** 아래에서 **메트릭** 을 선택합니다.

1. 오른쪽 창의 **차트 제목** 아래에 있는 메트릭 모음에서 **메트릭** 목록을 열고, 원하는 메트릭을 선택합니다.

   > [!NOTE]
   > 스토리지 사용량은 논리 앱에서 사용하고 청구되는 스토리지 단위(GB)의 수로 측정됩니다. 스토리지에서 500MB 미만을 사용하는 실행은 모니터링 보기에 표시되지 않을 수 있지만 여전히 청구됩니다.

   !["메트릭" 목록이 열린 메트릭 창을 보여 주는 스크린샷](./media/logic-apps-pricing/select-metric.png)

1. 창의 오른쪽 위 모서리에서 원하는 기간을 선택합니다.

1. 다른 스토리지 사용량 데이터, 특히 논리 앱의 실행 기록에서 작업 입력 및 출력 크기를 보려면 [다음 단계를 수행](#view-input-output-sizes)합니다.

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>실행 기록에서 작업 입력 및 출력 크기 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱의 메뉴에서 **개요** 를 선택합니다.

1. 오른쪽 창의 **실행 기록** 아래에서 보려는 입력 및 출력이 포함된 실행을 선택합니다.

1. **논리 앱 실행** 아래에서 **실행 정보** 를 선택합니다.

1. **논리 앱 실행 정보** 창의 작업 테이블에서 보려는 작업을 선택합니다. 여기에는 각 작업의 상태 및 기간이 나열되어 있습니다.

1. **논리 앱 작업** 창에서 해당 작업의 입력 및 출력 크기를 찾습니다. **입력 링크** 및 **출력 링크** 아래에서 해당 입력 및 출력에 대한 링크를 찾습니다.

   > [!NOTE]
   > 루프의 경우 최상위 작업만 입력 및 출력 크기를 표시합니다. 중첩된 루프 내 작업의 경우 입력 및 출력에서 0 크기를 표시하고 링크는 표시하지 않습니다.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Azure Cost Management 및 청구를 사용하여 비용 분석 실행

Azure 구독과 같은 특정 범위를 기반으로 하여 Logic Apps 서비스에 대한 비용을 검토하려면 [Azure Cost Management 및 청구](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 기능을 사용할 수 있습니다.

1. Azure Portal에서 원하는 범위(예: Azure 구독)를 엽니다. 왼쪽 메뉴의 **Cost Management** 아래에서 **비용 분석** 을 선택합니다.

   비용 분석 창을 처음 열면 위쪽 그래프에 구독에 있는 모든 서비스에 대한 현재 월의 실제 및 예측 사용량 비용이 표시됩니다.

   ![Azure Portal 및 구독의 실제 비용 및 예측 비용 예가 포함된 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > 범위를 변경하려면 **비용 분석** 창의 필터 모음에서 **범위** 필터를 선택합니다. **범위 선택** 창에서 원하는 범위로 전환합니다.

   아래의 도넛형 차트에는 Azure 서비스, Azure 지역(위치) 및 리소스 그룹별 현재 비용이 표시됩니다.

   ![Azure Portal 및 서비스, 지역 및 리소스 그룹에 대한 도넛형 차트 예가 포함된 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. 차트를 서비스 또는 리소스와 같은 특정 영역으로 필터링하려면 필터 모음에서 **필터 추가** 를 선택합니다.

1. 왼쪽 목록에서 필터 유형(예: **서비스 이름**)을 선택합니다. 오른쪽 목록에서 필터(예: **논리 앱**)를 선택합니다. 완료되면 녹색 확인 표시를 선택합니다.

   ![Azure Portal 및 필터 선택 항목이 포함된 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   예를 들어 Logic Apps 서비스에 대한 결과는 다음과 같습니다.

   ![Azure Portal 및 "논리 앱"으로 필터링된 결과가 포함된 비용 분석 창을 보여 주는 스크린샷](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>비용 데이터 내보내기

비용에 대해 더 많은 데이터 분석을 수행해야 하는 경우 [비용 데이터를 스토리지 계정으로 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수 있습니다. 예를 들어 재무 팀에서 Excel 또는 Power BI를 사용하여 이 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 따라 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 내보내기는 비용 데이터 세트를 검색하는 데 권장되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs"></a>비용을 관리하고 절감하는 다른 방법

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

논리 앱 및 관련 리소스에 대한 비용을 줄이려면 다음 옵션을 사용해 보세요.

* 가능하면 실행당 실행 비용이 [관리형 커넥터 트리거 및 작업](../connectors/managed.md)보다 더 저렴한 [기본 제공 트리거 및 작업](../connectors/built-in.md)을 사용합니다.

  예를 들어 [HTTP 작업](../connectors/connectors-native-http.md)을 사용하거나 [Azure Functions 서비스](../azure-functions/functions-overview.md)에서 [기본 제공 Azure Functions 작업](../logic-apps/logic-apps-azure-functions.md)을 사용하여 만든 함수를 호출하여 다른 리소스에 액세스할 때 발생하는 비용을 줄일 수 있습니다. 그러나 Azure Functions를 사용하면 비용이 발생하므로 옵션을 비교해야 합니다.

* 워크플로를 실행하기 위한 [정확한 트리거 조건을 지정](logic-apps-workflow-actions-triggers.md#trigger-conditions)합니다.

  예를 들어 대상 웹 사이트에서 내부 서버 오류를 반환하는 경우에만 트리거가 실행되도록 지정할 수 있습니다. 트리거의 JSON 정의에서 `conditions` 속성을 사용하여 트리거의 상태 코드를 참조하는 조건을 지정합니다.

* 폴링 버전 및 웹후크 버전이 트리거에 있는 경우 이벤트를 정기적으로 확인하는 대신 지정된 이벤트가 발생할 때까지 기다리는 웹후크 버전을 시도합니다.

* 다른 서비스를 통해 논리 앱을 호출하여 워크플로를 실행해야 하는 경우에만 트리거가 실행되도록 합니다.

  예를 들어 Azure Functions 서비스를 사용하여 만들고 실행하는 함수에서 논리 앱을 호출할 수 있습니다. 예를 들어 [Azure Functions 및 Azure Service Bus를 사용하여 논리 앱 호출 및 트리거](logic-apps-scenario-function-sb-trigger.md)를 참조하세요.

* 지속적으로 실행할 필요가 없는 [논리 앱을 사용하지 않도록 설정](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps)하거나 더 이상 필요하지 않은 [논리 앱을 삭제](manage-logic-apps-with-azure-portal.md#delete-logic-apps)합니다. 가능하면 지속적으로 활성화할 필요가 없는 다른 리소스를 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cost Management로 클라우드 투자를 최적화](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [비용 분석을 사용하여 비용 관리](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [예기치 않은 비용 방지](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정 수행