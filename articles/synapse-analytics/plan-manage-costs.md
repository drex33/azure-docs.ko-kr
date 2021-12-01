---
title: Azure Synapse Analytics 비용 관리 계획
description: Azure Portal에서 비용 분석을 사용하여 Azure Synapse Analytics에 대한 비용을 계획하고 관리하는 방법을 알아봅니다
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 834a402e76673a3f6078e3fd8b90b915649255cc
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133355168"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Azure Synapse Analytics 계획 및 비용 관리

이 문서에서는 Azure Synapse Analytics를 계획하고 비용을 관리하는 방법을 설명합니다. 비용 예측을 위해 서비스에 리소스를 추가하기 전에 먼저 Azure 요금 계산기를 사용하여 Azure Synapse Analytics 비용을 계획합니다. 그런 다음, Azure 리소스를 추가할 때 예상 비용을 검토합니다. Azure Synapse Analytics 리소스 사용을 시작한 후 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 예측 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure Synapse Analytics 비용은 Azure 청구서의 월간 비용 중에 일부에 불과합니다. 이 문서에서는 Azure Synapse Analytics에 대한 비용을 계획하고 관리하는 방법을 설명하지만, 사용자에게는 타사 서비스를 비롯한 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Cost Management에서의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Azure Synapse Analytics를 사용하기 전 비용 예측

Azure Synapse Analytics를 추가하기 전 [Azure 요금 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 비용을 예측합니다.

아래 비용 예측에 표시된 것처럼 Azure Synapse에는 서로 다른 요금의 다양한 리소스가 포함됩니다. 

![Azure 요금 계산기로 예측 비용을 보여 주는 예제](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Azure Synapse Analytics에 대한 전체 청구 모델 이해

Azure Synapse는 새 리소스를 배포할 때 Azure Synapse와 함께 비용이 증가하는 Azure 인프라에서 실행됩니다. 추가 인프라로 비용이 증가할 수 있다는 것을 이해하는 것이 중요합니다. 

### <a name="how-youre-charged-for-azure-synapse-analytics"></a>Azure Synapse Analytics의 요금 청구 방식

Azure Synapse Analytics 리소스를 만들거나 사용하는 경우 다음 측정 단위에 대해 요금이 청구될 수 있습니다.

- 데이터 탐색 및 데이터 웨어하우징 
    - 전용 SQL 풀 - DWU 블록 수와 실행 시간에 따라 요금이 청구됩니다.
    - 저장소 - 저장된 TB 수에 따라 요금이 청구됩니다.
    - 서버리스 SQL 풀 - 처리된 데이터의 TB로 요금이 청구됩니다.
- Apache Spark 풀 - 인스턴스 수 및 실행 시간에 대한 요금이 청구됩니다.
- 데이터 통합 
    - 오케스트레이션 활동 실행 - 작업이 실행 수에 따라 요금이 청구됩니다.
    - 데이터 이동 – 복사 작업은 Azure Integration Runtime에서 실행되며 사용된 DIU 수 및 실행 기간에 따라 요금이 청구됩니다.
    - 데이터 흐름 vCore 시간 – 데이터 흐름 실행 및 디버깅의 경우 컴퓨팅 형식, vCore 수 및 실행 기간을 기준으로 요금이 청구됩니다.

청구 주기가 끝날 때 각 측정 단위의 요금이 합산됩니다. 청구서 또는 송장에 모든 Synapse Analytics 비용에 대한 섹션이 표시됩니다. 각 측정 단위에 대한 별도의 줄 항목이 있습니다.

### <a name="other-costs-that-might-accrue-with-azure-synapse-analytics"></a>Azure Synapse Analytics 사용 시 발생할 수 있는 기타 비용

Azure Synapse에 대해 리소스를 만들면 다른 Azure 서비스에 대한 리소스도 생성됩니다. 다음과 같은 변경 내용이 해당됩니다.

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>리소스를 삭제한 후에도 비용이 증가할 수 있습니다.

Azure Synapse 리소스를 삭제한 후에도 다음 리소스는 계속 존재할 수 있습니다. 이를 삭제할 때까지는 계속 비용이 증가합니다.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Azure Synapse에 Azure 선불 크레딧 사용 

Azure 선불 크레딧을 사용하여 Azure Synapse 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

### <a name="pre-purchase-plan-for-azure-synapse"></a>Azure Synapse 대한 사전 구매 계획

1년 동안 SCU(Azure Synapse 커밋 단위)를 미리 구매할 때 Azure Synapse Analytics 비용을 절감할 수 있습니다. 구매 기간 동안 언제든지 사전 구매한 SCU를 사용할 수 있습니다. 자세한 내용은 사전 구매 계획을 통해 [Azure Synapse Analytics 비용 최적화를 참조하세요.](../cost-management-billing/reservations/synapse-analytics-pre-purchase-plan.md)


## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

Azure Synapse Analytics에 대한 리소스를 만들 때 예상 비용이 표시됩니다. 작업 영역에는 작업 영역에 생성된 서버리스 SQL 풀이 포함됩니다. 서버리스 SQL 풀은 쿼리를 실행할 때까지 요금이 부과되지 않습니다. 전용 SQL 풀 및 서버리스 Apache Spark 풀과 같은 다른 리소스는 작업 영역 내에 만들어야 합니다.

Azure Synapse Analytics 작업 영역을 만들고 예상 가격을 보려면 다음을 수행합니다.

1. Azure Portal에서 서비스로 이동합니다.
2. 리소스를 만듭니다.
3. 요약에 표시된 예측 비용을 검토합니다.
4. 리소스 만들기를 완료합니다.

![리소스를 만드는 동안 예측 비용을 보여주는 예제입니다.](./media/plan-manage-costs/create-workspace-cost.png)


Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과하여 지출하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용됩니다. 신용 한도에 도달하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용하지 않도록 설정됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대한 자세한 내용은 [Azure 지출 한도](../cost-management-billing/manage/spending-limit.md)를 참조하세요.

## <a name="monitor-costs"></a>비용 모니터링

Azure Synapse 리소스를 사용하면 비용이 발생합니다. Azure 리소스 사용량 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 단위 사용량(바이트, 메가바이트 등)에 따라 다릅니다. Azure Synapse에서 리소스 사용을 시작하는 즉시 비용이 발생하고 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 확인할 수 있습니다.

비용 분석을 사용하면 여러 시간 간격에 대해 Azure Synapse Analytics 비용을 그래프 및 표로 확인할 수 있습니다. 몇 가지 예로 일, 현재 달과 이전 달 및 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.

비용 분석에서 Azure Synapse 비용을 보려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.
2. Azure Portal에서 구독 또는 리소스 그룹의 범위를 열고 메뉴에서 **비용 분석** 을 선택합니다. 예를 들어, **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. **범위** 를 선택하여 비용 분석에서 다른 범위로 전환합니다.
3. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시됩니다. Azure Synapse라는 레이블이 지정된 차트에서 영역을 선택합니다.

처음으로 비용 분석을 열 경우 실제 월별 비용이 표시됩니다. 다음은 월별 사용의 모든 비용을 보여 주는 예입니다.

![구독에 대한 누적 비용을 보여 주는 예제](./media/plan-manage-costs/actual-monthly-costs.png)

- Azure Synapse와 같은 단일 서비스로 비용 범위를 좁히려면 **필터 추가** 를 선택한 다음, **서비스 이름** 을 선택합니다. 그런 후 **Azure Synapse Analytics** 를 선택합니다.

Azure Synapse에 대한 비용을 보여 주는 예제는 다음과 같습니다.

![ServiceName에 대한 누적 비용을 보여 주는 예제](./media/plan-manage-costs/filtered-monthly-costs.png)

앞의 예제에서 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치) 및 리소스 그룹별 Azure Synapse 비용 또한 표시됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 발생시키는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산을 만들 때 사용할 수 있는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 직접 수행해야 하거나 다른 사용자가 수행하는 경우에 유용합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 세트를 검색하는 데 권장되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Azure Synapse의 비용을 관리하고 줄이기 위한 기타 방법 

### <a name="serverless-sql-pool"></a>서버리스 SQL 풀

서버리스 SQL 풀의 비용에 대해 알아보려면 [Azure Synapse Analytics에서 서버리스 SQL의 비용 관리](./sql/data-processed.md)를 참조하세요.

### <a name="dedicated-sql-pool"></a>전용 SQL 풀

사용 중이 아닐 때 리소스를 일시 중지하여 전용 SQL 풀의 비용을 관리할 수 있습니다. 예를 들어, 밤 시간과 주말에 데이터베이스를 사용하지 않으려면 해당 시간에 일시 중지했다가 주간에 다시 시작할 수 있습니다. 자세한 내용은 [Azure Portal을 통해 전용 SQL 풀에서 컴퓨팅 일시 중지 및 다시 시작](./sql-data-warehouse/pause-and-resume-compute-portal.md?context=/azure/synapse-analytics/context/context)을 참조하세요.

### <a name="serverless-apache-spark-pool"></a>서버리스 Apache Spark 풀

서버리스 Apache Spark 풀의 비용을 제어하려면 서버리스 Apache Spark 자동 일시 중지 기능을 사용하도록 설정하고 그에 따라 제한 시간 값을 설정합니다.  개발을 위해 Synapse Studio를 사용하는 경우 스튜디오는 구성 가능한 세션을 활성 상태로 유지하기 위해 연결 유지 메시지를 보내므로 자동 일시 중지에 대한 짧은 시간 제한 값을 설정합니다.  작업이 완료되어 세션을 닫으면 제한 시간 값에 도달했을 때 Apache Spark 풀이 자동으로 일시 중지됩니다.
 
개발 중 여러 크기에 대해 Apache Spark 풀 정의를 여러 개 만듭니다.  Apache Spark 풀 정의 만들기는 무료이고 사용량에 대해서만 요금이 청구됩니다.  Azure Synapse의 Apache Spark 사용량은 vCore 사용 시간을 기준으로 청구되고 사용 시간(분)에 따라 일할 계산됩니다.  예를 들어 성능 테스트를 위해서는 더 큰 풀을 사용하고 코드 개발 및 유효성 검사를 위해서는 작은 풀을 사용합니다.

### <a name="data-integration---pipelines-and-data-flows"></a>데이터 통합 - 파이프라인 및 데이터 흐름 

데이터 통합 비용에 대한 자세한 내용은 [Azure Data Factory 플랜 및 비용 관리](../data-factory/plan-manage-costs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [비용 관리](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행합니다.
- [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)의 계획 및 비용 관리에 대해 알아봅니다.
