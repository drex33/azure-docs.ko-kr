---
title: 비용 관리 계획
titleSuffix: Azure Machine Learning
description: Azure Portal에서 비용 분석을 사용하여 Azure Machine Learning에 대한 비용을 계획하고 관리합니다. ML 모델을 빌드할 때 비용을 낮출 수 있도록 추가 비용 절감 팁을 알아보세요.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 2dab204d2d4767c9edef4f65eadde8eea035c7b0
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429208"
---
# <a name="plan-to-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning에 대한 비용 관리 계획

이 문서에서는 Azure Machine Learning에 대한 비용을 계획하고 관리하는 방법을 설명합니다. 먼저 리소스를 추가하기 전에 Azure 가격 책정 계산기를 사용하여 비용을 계획할 수 있습니다. 다음으로 Azure 리소스를 추가할 때 예상 비용을 검토합니다. 

Azure Machine Learning 리소스 사용을 시작한 후에는 비용 관리 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 또한 예측 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수 있습니다.

Azure Machine Learning에 대한 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 다른 Azure 서비스를 사용하는 경우 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다. 이 문서에서는 Azure Machine Learning에 대한 비용을 계획하고 관리하는 방법을 설명합니다. Azure Machine Learning에 대한 비용 관리에 익숙해지면 비슷한 방법을 적용하여 구독에 사용되는 모든 Azure 서비스에 대한 비용을 관리할 수 있습니다.

비용 최적화에 대한 자세한 내용은 [Azure Machine Learning에서 비용을 관리하고 최적화하는 방법](how-to-manage-optimize-cost.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Cost Management에서의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 

비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Azure Machine Learning을 사용하기 전에 비용 추정

- Azure Machine Learning 작업 영역에서 리소스를 만들기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 비용을 추정합니다.
왼쪽에서 **AI + 기계 학습** 을 선택한 다음, **Azure Machine Learning** 을 선택하여 시작합니다.  

다음 스크린샷은 계산기를 사용한 비용 추정을 보여 줍니다.

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 요금 계산기로 예측 비용을 보여 주는 예":::

작업 영역에 새 리소스를 추가할 때 이 계산기로 돌아가서 동일한 리소스를 추가하여 비용 추정치를 업데이트합니다.

자세한 내용은 [Azure Machine Learning 가격 책정](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Azure Machine Learning에 대한 전체 청구 모델 이해

Azure Machine Learning은 새 리소스를 배포할 때 Azure Machine Learning과 함께 비용이 누적되는 Azure 인프라에서 실행됩니다. 추가 인프라로 비용이 누적될 수 있다는 것을 이해하는 것이 중요합니다. 배포된 리소스를 변경할 때 비용을 관리해야 합니다. 


### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>일반적으로 Azure Machine Learning으로 누적되는 비용

Azure Machine Learning 작업 영역에 대한 리소스를 만들 때 다른 Azure 서비스에 대한 리소스도 생성됩니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 기본 계정
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)(범용 v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

[컴퓨팅 인스턴스](concept-compute-instance.md)를 만들 때 VM은 작업에 사용할 수 있도록 유지됩니다.  컴퓨팅 인스턴스(미리 보기)를 자동으로 시작 및 중지하도록 [일정을 설정](how-to-create-manage-compute-instance.md#schedule)하여 사용하지 않을 때 비용을 절감합니다.
 
### <a name="costs-might-accrue-before-resource-deletion"></a>리소스를 삭제하기 전 비용이 누적될 수 있음

Azure Portal에서 또는 Azure CLI를 사용하여 Azure Machine Learning 작업 영역을 삭제하기 전에 다음 하위 리소스는 작업 영역에서 적극적으로 작업하지 않는 경우에도 누적되는 일반적인 비용입니다. 나중에 Azure Machine Learning 작업 영역으로 돌아가려는 경우 이러한 리소스는 비용이 계속 발생할 수 있습니다.

* VM
* Load Balancer
* Virtual Network
* 대역폭

각 VM은 실행 중인 시간당 요금이 청구됩니다. 비용은 VM 사양에 따라 달라집니다. 실행 중이지만 데이터 세트에서 활발하게 작업하지 않는 VM은 부하 분산 장치를 통해 요금이 청구됩니다. 각 컴퓨팅 인스턴스의 경우 하루에 하나의 부하 분산 장치에 대한 요금이 청구됩니다. 컴퓨팅 클러스터의 노드 50개마다 하나의 표준 부하 분산 장치에 대한 요금이 청구됩니다. 각 부하 분산 장치는 하루에 약 0.33달러의 요금이 청구됩니다. 중지된 컴퓨팅 인스턴스 및 컴퓨팅 클러스터에서 부하 분산 장치 비용을 방지하려면 컴퓨팅 리소스를 삭제합니다. 구독당 및 지역당 하나의 가상 네트워크가 청구됩니다. 가상 네트워크는 지역 또는 구독을 포괄할 수 없습니다. vNet 설정에서 프라이빗 엔드포인트를 설정하면 요금이 발생할 수도 있습니다. 대역폭은 사용량에 따라 청구됩니다. 전송되는 데이터가 많을수록 더 많은 요금이 청구됩니다.

### <a name="costs-might-accrue-after-resource-deletion"></a>리소스를 삭제한 후 비용이 누적될 수 있음

Azure Portal에서 또는 Azure CLI를 사용하여 Azure Machine Learning 작업 영역을 삭제하면 다음과 같은 리소스가 계속 남아 있습니다. 이를 삭제할 때까지는 계속 비용이 누적됩니다.

* Azure Container Registry
* Azure Block Blob Storage
* Key Vault
* Application Insights

이러한 종속 리소스와 함께 작업 영역을 삭제하려면 SDK를 사용합니다.

```python
ws.delete(delete_dependent_resources=True)
```

작업 영역에 AKS(Azure Kubernetes Service)를 만들거나 작업 영역에 컴퓨팅 리소스를 연결하는 경우 [Azure Portal](https://portal.azure.com)에서 별도로 삭제해야 합니다.

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure Machine Learning에 Azure 선불 크레딧 사용

Azure 선불 크레딧을 사용하여 Azure Machine Learning 요금을 지불할 수 있습니다. 단, Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

<!-- Note for Azure service writer: If your service shows estimated costs when a user is creating resources in the Azure portal, at a minimum, insert this section as a brief walkthrough that steps through creating a Azure Machine Learning resource where the estimated cost is shown to the user, updated for your service. Add a screenshot where the estimated costs or subscription credits are shown.

If your service doesn't show costs as they create a resource or if estimated costs aren't shown to users before they use your service, then omit this section.

For example, you might start with the following (modify for your service):
-->

Azure Machine Learning에 대한 컴퓨팅 리소스를 만들 때 예상 비용이 표시됩니다.

*컴퓨팅 인스턴스*를 만들고 예상 가격을 보려면 다음을 수행합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)에 로그인합니다.
1. 왼쪽에서 **컴퓨팅** 을 선택합니다.
1. 상단 도구 모음에서 **+새로 만들기** 를 선택합니다.
1. 사용 가능한 각 가상 머신 크기에 대해 표시된 예상 가격을 검토합니다.
1. 리소스 만들기를 완료합니다.


:::image type="content" source="media/concept-plan-manage-cost/create-resource.png" alt-text="컴퓨팅 인스턴스를 만드는 동안 예상 비용을 보여 주는 예" lightbox="media/concept-plan-manage-cost/create-resource.png" :::

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과하여 지출하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용됩니다. 신용 한도에 도달하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용하지 않도록 설정됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대한 자세한 내용은 [Azure 지출 한도](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요.

## <a name="monitor-costs"></a>비용 모니터링

Azure Machine Learning에서 Azure 리소스를 사용하면 비용이 발생합니다. Azure 리소스 사용량 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 단위 사용량(바이트, 메가바이트 등)에 따라 다릅니다. Azure Machine Learning 사용이 시작되자마자 비용이 발생하며 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 확인할 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격에 대한 그래프 및 테이블로 Azure Machine Learning 비용을 조회할 수 있습니다. 몇 가지 예로 일, 현재 달과 이전 달 및 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.

비용 분석에서 Azure Machine Learning 비용을 보려면:

1. Azure Portal에 로그인합니다.
2. Azure Portal에서 범위를 열고, 메뉴에서 **비용 분석** 을 선택합니다. 예를 들어, **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. **범위** 를 선택하여 비용 분석에서 다른 범위로 전환합니다.
3. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시됩니다. Azure Machine Learning이라는 레이블이 지정된 차트에서 영역을 선택합니다.

처음으로 비용 분석을 열 경우 실제 월별 비용이 표시됩니다. 다음은 월별 사용의 모든 비용을 보여 주는 예입니다.

:::image type="content" source="media/concept-plan-manage-cost/all-costs.png" alt-text="구독에 대한 누적 비용을 보여 주는 예" lightbox="media/concept-plan-manage-cost/all-costs.png" :::


Azure Machine Learning과 같은 단일 서비스로 비용 범위를 좁히려면 **필터 추가** 를 선택한 다음, **서비스 이름** 을 선택합니다. 그런 다음 **Azure Machine Learning** 을 선택합니다.

Azure Machine Learning에 대한 비용을 보여 주는 예제는 다음과 같습니다.

:::image type="content" source="media/concept-plan-manage-cost/vm-specific-cost.png" alt-text="ServiceName에 대한 누적 비용을 보여 주는 예" lightbox="media/concept-plan-manage-cost/vm-specific-cost.png" :::

<!-- Note to Azure service writer: The image shows an example for Azure Storage. Replace the example image with one that shows costs for your service. -->

앞의 예제에서 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치) 및 리소스 그룹별 Azure Machine Learning 비용 또한 표시됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.
## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터를 사용하면 추가 비용을 발생시키는 새 리소스를 실수로 만드는 일을 방지할 수 있습니다. 예산 생성 시 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 직접 수행해야 하거나 다른 사용자가 수행하는 경우에 유용합니다. 예를 들어 재무 팀이 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 세트를 검색하는 데 권장되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Azure Machine Learning에 대한 비용을 관리하고 절감하는 기타 방법

다음 팁을 사용하여 컴퓨팅 리소스 비용을 관리하고 최적화할 수 있습니다.

- 자동 크기 조정을 위한 학습 클러스터 구성
- 구독 및 작업 영역에 대한 할당량 설정
- 학습 실행에 대한 종료 정책 설정
- 우선 순위가 낮은 VM(가상 머신) 사용
- 컴퓨팅 인스턴스가 자동으로 종료 및 시작하도록 예약
- Azure Reserved VM Instance 사용
- 로컬에서 학습
- 학습 병렬화
- 데이터 보존 및 삭제 정책 설정
- 동일한 지역에 리소스 배포
- 가까운 미래에 사용할 계획이 없는 경우 인스턴스 및 클러스터를 삭제합니다.

자세한 내용은 [Azure Machine Learning에서 비용 관리 및 최적화](how-to-manage-optimize-cost.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Machine Learning에서 비용 관리 및 최적화](how-to-manage-optimize-cost.md).
- [조직 규모에서 Azure Machine Learning에 대한 예산, 비용 및 할당량 관리](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)
- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.
