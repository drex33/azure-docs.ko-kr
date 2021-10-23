---
title: Azure Cosmos DB에 대한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용하여 Azure Cosmos DB에 대한 비용을 계획하고 관리하는 방법을 알아봅니다.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 3dd9c80150c07d844072ccb2229e9828b0137353
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255771"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 비용 계획 및 관리
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 Azure Cosmos DB의 비용을 계획하고 관리하는 방법을 설명합니다. 먼저 Azure Cosmos DB 용량 계산기를 사용하여 리소스를 만들기 전에 워크로드 비용을 예측합니다. 나중에 예상 비용을 검토하고 리소스 만들기를 시작할 수 있습니다.

Azure Cosmos DB 리소스 사용을 시작한 후에는 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 예상 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure Cosmos DB 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 Azure Cosmos DB에 대한 비용을 계획하고 관리하는 방법을 설명하지만, 사용자에게는 타사 서비스를 비롯한 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구됩니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="provisioned-throughput-or-serverless"></a>프로비전된 처리량 또는 서버리스

Azure Cosmos DB는 [프로비전된 처리량](set-throughput.md)과 [서버리스](serverless.md)라는 두 가지 유형의 용량을 지원합니다. Azure Cosmos DB 사용량에 대해 요금이 청구되는 방식은 이러한 두 모드에 따라 매우 다르므로 워크로드에 가장 적합한 것을 선택하는 것이 중요합니다. 이 선택 방법에 관한 지침과 권장 사항은 [프로비전된 처리량과 서버리스 사이에서 선택하는 방법](throughput-serverless.md) 문서를 참조하세요.

### <a name="cost-analysis"></a>비용 분석

Cost Management의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-azure-cosmos-db"></a>Azure Cosmos DB를 사용하기 전에 비용 추정

Azure Cosmos DB는 프로비전된 처리량 및 서버리스라는 두 가지 다른 용량 모드로 제공됩니다. 두 모드 모두에서 정확히 동일한 데이터베이스 작업을 수행할 수 있지만 이러한 작업에 대해 비용이 청구되는 방식은 다릅니다.

### <a name="capacity-planning"></a>용량 계획

비용을 예측하는 데 있어 보조적 역할을 하며 Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하는 데 도움이 될 수 있습니다. 기존 데이터베이스 클러스터에서 Azure Cosmos DB로 마이그레이션을 계획하는 경우 용량 계획에 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 

![4코어 SKU의 복제본 3개가 있는 복제본 세트를 Azure Cosmos DB로 마이그레이션](media/convert-vcore-to-request-unit/one-replica-set.png)

* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

### <a name="estimate-provisioned-throughput-costs"></a>프로비전된 처리량 비용 추정

프로비전된 처리량 모드에서 Azure Cosmos DB 사용을 계획하려는 경우 Azure Cosmos 계정에 리소스를 만들기 전에 [Azure Cosmos DB 용량 계산기](https://cosmos.azure.com/capacitycalculator/)를 사용하여 비용을 추정합니다. 용량 계산기는 워크로드에 필요한 처리량 및 비용의 추정치를 얻는 데 사용됩니다. 용량 계산기는 현재 SQL API, Cassandra API 및 MongoDB용 API에서만 사용할 수 있습니다.

적절한 크기의 프로비전된 처리량을 사용하여 Azure Cosmos 데이터베이스 및 컨테이너를 구성하거나 워크로드에 대한 [요청 단위(RU/초)](request-units.md)를 구성하는 것은 비용과 성능을 최적화하는 데 필수적입니다. 예상 비용을 얻기 위해서는 API 형식, 지역 수, 항목 크기, 초당 읽기/쓰기 요청 수, 저장된 총 데이터와 같은 세부 정보를 입력해야 합니다. 용량 계산기에 대해 자세히 알아보려면 [예측](estimate-ru-with-capacity-planner.md) 문서를 참조하세요.

다음 스크린샷은 용량 계산기를 사용한 처리량 및 비용 예측을 보여 줍니다.

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode-sql-api.png" alt-text="용량 플래너 기본 모드" border="true":::

### <a name="estimate-serverless-costs"></a><a id="estimating-serverless-costs"></a> 서버리스 비용 예측

서버리스 모드로 Azure Cosmos DB를 사용하려고 계획하는 경우 월 기준으로 사용할 수 있는 [요청 단위](request-units.md) 수와 스토리지의 GB를 예측해야 합니다. 한 달에 발생하는 데이터베이스 작업의 수를 평가하여 필요한 요청 단위 양을 예측하고 해당하는 양을 해당하는 RU 비용에 곱할 수 있습니다. 다음 표에는 일반적인 데이터베이스 작업에 대해 예상된 RU 비용이 나열되어 있습니다.

| 작업 | 예상 비용 | 참고 |
| --- | --- | --- |
| 항목 만들기 | 5RU | 5개 미만의 속성을 인덱싱하는 1KB 항목에 대한 평균 비용 |
| 항목 업데이트 | 10RU | 5개 미만의 속성을 인덱싱하는 1KB 항목에 대한 평균 비용 |
| 해당 ID 및 파티션 키로 개별 항목 읽기(지점 읽기) | 1RU | 1KB 항목에 대한 평균 비용 |
| 항목 삭제 | 5RU | |
| 쿼리 실행 | 10RU | [인덱싱](index-overview.md)을 최대한 활용하고 100개 이하의 결과를 반환하는 쿼리의 평균 비용 |

> [!IMPORTANT] 
> 위의 표에서 메모를 확인하세요. 작업의 실제 비용을 더 정확하게 예측하려면 [Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용하여 [작업의 정확한 RU 비용을 측정](find-request-unit-charge.md)할 수 있습니다. Azure Cosmos DB 에뮬레이터는 서버리스를 지원하지 않지만 데이터베이스 작업에 대한 표준 RU 요금을 보고하고 이 예측에 사용할 수 있습니다.

한 달 동안 소비할 가능성이 높은 요청 단위의 총 수 및 스토리지의 GB를 계산한 후에는 **([요청 단위 수]/100만 * $0.25) + ([스토리지의 GB] * $0.25)** 와 같은 수식을 통해 예상 비용이 반환됩니다.

> [!NOTE]
> 앞의 예제에 표시된 비용은 데모용으로만 사용됩니다. 최신 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="understand-the-full-billing-model"></a>전체 청구 모델 이해

Azure Cosmos DB는 Azure 인프라에서 실행되며 새 리소스를 배포할 때 비용이 발생합니다. 다른 추가 인프라 비용이 발생할 수도 있다는 점을 이해하는 것이 중요합니다.

### <a name="how-youre-charged-for-azure-cosmos-db"></a>Azure Cosmos DB 요금이 청구되는 방식

Azure Cosmos DB 리소스를 만들거나 사용하는 경우 다음 측정 단위에 대해 요금이 청구될 수 있습니다.

* **데이터베이스 작업** - 프로비전되거나 사용된 요청 단위(RU/초)를 기준으로 요금이 청구됩니다.
  * 표준(수동) 프로비전된 처리량 - 컨테이너 또는 데이터베이스에서 프로비전된 RU/초에 대한 시간당 요금이 청구됩니다.
  * 자동 크기 조정 프로비전된 처리량 - 시스템이 각 시간에 스케일 업된 최대 RU/초의 최대 수치를 기준으로 요금이 청구됩니다.

* **사용된 스토리지** - 지정된 시간 동안 데이터 및 인덱스에서 사용된 총 스토리지 크기(GB)를 기준으로 요금이 청구됩니다.

백업 스토리지, 분석 스토리지, 가용성 영역, 다중 지역 쓰기와 같은 Azure Cosmos DB 기능을 사용하는 경우 추가 요금이 부과됩니다. 청구 주기가 끝날 때 각 측정 단위의 요금이 합산됩니다. 청구서 또는 송장에 모든 Azure Cosmos DB 비용에 대한 섹션이 표시됩니다. 각 측정 단위에 대한 별도의 줄 항목이 있습니다. 자세히 알아보려면 [가격 책정 모델](how-pricing-works.md) 문서를 참조하세요.

### <a name="using-azure-prepayment"></a>Azure 선불 사용

Azure 선불 크레딧을 사용하여 Azure Cosmos DB 요금을 지불할 수 있습니다. 단, Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

Azure Portal에서 Azure Cosmos DB 리소스를 사용하기 시작하면 예상 비용을 볼 수 있습니다. 다음 단계를 사용하여 예상 비용을 검토합니다.

1. Azure Portal에 로그인하고, Azure Cosmos 계정으로 이동합니다.
1. **개요** 섹션으로 이동합니다.
1. 아래쪽의 **비용** 차트를 확인합니다. 이 차트는 구성 가능한 기간 동안의 현재 비용을 예상하여 보여 줍니다.
1. 그래프 컨테이너와 같은 새 컨테이너를 만듭니다.
1. 400RU/초와 같은 워크로드에 필요한 처리량을 입력합니다. 처리량 값을 입력한 후에는 다음 스크린샷에 표시된 가격 책정 예상치를 확인할 수 있습니다.

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Azure Portal의 예상 비용":::

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과하여 지출하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용됩니다. 신용 한도에 도달하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용하지 않도록 설정됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대한 자세한 내용은 [Azure 지출 한도](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요.

Azure 선불(이전에는 현금 약정 금액이라고 함) 크레딧을 사용하여 Azure Cosmos DB 요금을 지불할 수 있습니다. 단, Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Cosmos DB에서 리소스를 사용하는 경우 비용이 발생합니다. 리소스 사용 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 요청 단위 사용에 따라 달라집니다. Azure Cosmos DB를 시작하는 즉시 비용이 발생하며 Azure Portal의 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 창에서 이를 볼 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격에 대한 그래프 및 테이블로 Azure Cosmos DB 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재, 이전 달 및 연도가 있습니다. 예산 및 예상 비용에 대한 비용도 볼 수 있습니다. 시간 경과에 따라 더 긴 보기로 전환하면 지출 추세를 파악하고 낭비가 발생될 수 있는 위치를 확인할 수 있습니다. 예산을 만든 경우 초과되는 지점을 쉽게 확인할 수도 있습니다.

비용 분석에서 Azure Cosmos DB 비용을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 범위를 열고, 메뉴에서 **비용 분석** 을 선택합니다. 예를 들어, **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. **범위** 를 선택하여 비용 분석에서 다른 범위로 전환합니다.

1. 기본적으로 모든 서비스에 대한 비용은 첫 번째 도넛형 차트에 표시됩니다. “Azure Cosmos DB”라는 레이블이 지정된 차트에서 영역을 선택합니다.

1. Azure Cosmos DB와 같은 단일 서비스에 대한 비용으로 범위를 좁히려면 **필터 추가** 를 선택한 다음, **서비스 이름** 을 선택합니다. 그런 다음, 목록에서 **Azure Cosmos DB** 를 선택합니다. Azure Cosmos DB에 대한 비용을 보여 주는 예제는 다음과 같습니다.

   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="비용 분석 창을 통한 비용 모니터링":::

위의 예제에서는 2월의 Azure Cosmos DB에 대한 현재 비용을 볼 수 있습니다. 차트에는 위치 및 리소스 그룹별 Azure Cosmos DB 비용도 포함됩니다.

## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터를 사용하면 추가 비용을 발생시키는 새 리소스를 실수로 만드는 일을 방지할 수 있습니다. 예산 생성 시 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 직접 수행해야 하거나 다른 사용자가 수행하는 경우에 유용합니다. 예를 들어 재무 팀이 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 세트를 검색하는 데 권장되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs"></a>비용을 관리하고 절감하기 위한 다른 방법

다음은 비용을 절감하는 데 사용할 수 있는 몇 가지 모범 사례입니다.

* [프로비전된 처리량 비용 최적화](optimize-cost-throughput.md) - 이 문서에서는 처리량 비용을 최적화하는 모범 사례에 대해 자세히 설명합니다. 워크로드 유형에 따라 컨테이너 수준 대 데이터베이스 수준에서 처리량을 프로비전하는 시기를 설명합니다.

* [요청 비용 최적화](optimize-cost-reads-writes.md) - 이 문서에서는 읽기 및 쓰기 요청을 요청 단위로 변환하는 방법 및 이러한 요청의 비용을 최적화하는 방법을 설명합니다.

* [스토리지 비용 최적화](optimize-cost-storage.md) - 스토리지 비용은 사용량에 따라 청구됩니다. 변경 피드 및 TTL(Time to Live)과 같은 기능을 사용하여 항목 크기, 인덱싱 정책으로 스토리지 비용을 최적화하는 방법에 대해 알아봅니다.

* [다중 지역 비용 최적화](optimize-cost-regions.md) - 미달 사용 읽기 지역이 하나 이상인 경우 읽기 지역에서 변경 피드를 사용하여 읽기 지역의 RU를 최대한 활용하거나, 과도한 사용 시 다른 보조로 이동하는 단계를 수행할 수 있습니다.

* [개발/테스트 비용 최적화](optimize-dev-test.md) - 로컬 에뮬레이터, Azure Cosmos DB 무료 계층, Azure 체험 계정 및 몇 가지 다른 옵션을 사용하여 개발 비용을 최적화하는 방법을 알아봅니다.

* [예약된 용량으로 비용 최적화](cosmos-db-reserved-capacity.md) - 1년 또는 3년 동안 Azure Cosmos DB 리소스에 대한 예약을 커밋하여 예약된 용량을 사용하여 비용을 절감하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 가격 책정이 작동하는 방식에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
* [Azure Cosmos DB의 가격 책정 모델](how-pricing-works.md)
* [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에 대해 알아봅니다.
* [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
* [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
* [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.
