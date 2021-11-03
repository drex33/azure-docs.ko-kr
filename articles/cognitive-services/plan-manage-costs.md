---
title: Azure Cognitive Services에 대한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용하여 Azure Cognitive Services에 대한 비용을 계획하고 관리하는 방법을 알아봅니다.
author: PatrickFarley
ms.author: pafarley
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: df7d5264fd4620f50bb4848a4c52bfdc5f5b9100
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073342"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Azure Cognitive Services에 대한 비용 계획 및 관리

이 문서에서는 Azure Cognitive Services에 대한 비용을 계획하고 관리하는 방법을 설명합니다. 먼저, 비용을 예측하기 위해 서비스에 대한 리소스를 추가하기 전에 Azure 가격 계산기를 사용하여 Cognitive Services 비용을 계획할 수 있습니다. 그런 다음, Azure 리소스를 추가할 때 예상 비용을 검토합니다. Cognitive Services 리소스(예: Speech, Computer Vision, LUIS, Text Analytics, Translator 등)를 사용하기 시작한 후에는 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링할 수 있습니다. 예상 비용을 검토하고 지출 추세를 파악하여 조치를 취하고자 하는 영역을 식별할 수도 있습니다. Cognitive Services 비용은 Azure 청구서의 월간 비용 중 일부에 불과합니다. 이 문서에서는 Cognitive Services에 대한 비용을 계획하고 관리하는 방법을 설명하지만 사용자에게는 타사 서비스를 비롯한 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Cost Management에서의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Cognitive Services를 사용하기 전에 비용 추정

Cognitive Services를 추가하기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 비용을 예측합니다.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Cognitive Services에 대한 Azure 가격 계산기" border="true":::

작업 영역에 새 리소스를 추가할 때 이 계산기로 돌아가서 동일한 리소스를 추가하여 비용 추정치를 업데이트합니다.

자세한 내용은 [Azure Cognitive Services 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)을 참조하세요.

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Cognitive Services에 대한 전체 청구 모델 이해

Cognitive Services는 새 리소스를 배포할 때 [비용을 누적](https://azure.microsoft.com/pricing/details/cognitive-services/)하는 Azure 인프라에서 실행됩니다. 추가 인프라로 인해 비용이 누적될 수 있다는 것을 이해하는 것이 중요합니다. 배포된 리소스를 변경할 때 비용을 관리해야 합니다. 

### <a name="how-youre-charged-for-cognitive-services"></a>Cognitive Services 청구 방법

Cognitive Services 리소스를 만들거나 사용하는 경우 사용하는 서비스에 따라 다음 미터에 대한 요금이 청구될 수 있습니다.

| 서비스 | 미터 | 대금 청구 정보 | 
|---------|-------|---------------------|
| **Vision** | | |
| [Computer Vision](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) | 무료, 표준(S1) | 트랜잭션 수를 기준으로 청구됩니다. 트랜잭션당 가격은 기능(읽기, OCR, 공간 분석)에 따라 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)을 참조하세요. |
| [Custom Vision](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) | 무료, 표준 | <li>예측은 트랜잭션 수에 따라 청구됩니다.</li><li>학습 요금은 컴퓨팅 시간별로 청구됩니다.</li><li>이미지 스토리지는 이미지(이미지당 최대 6MB) 수에 따라 청구됩니다.</li>|
| [Face](https://azure.microsoft.com/pricing/details/cognitive-services/face-api/) | 무료, 표준 | 트랜잭션 수를 기준으로 청구됩니다. |
| **Speech** | | |
| [Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) | 무료, 표준 | 청구는 기능(음성 텍스트 변환, 텍스트 음성 변환, 음성 번역, 화자 인식)에 따라 다릅니다. 주로 트랜잭션 수 또는 문자 수에 따라 청구됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요. |
| **언어** | | |
| [언어 이해(LUIS)](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) | 무료 작성, 무료 예측, 표준 | 트랜잭션 수에 따라 청구됩니다. 트랜잭션당 가격은 기능(음성 요청, 텍스트 요청)에 따라 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)을 참조하세요. |
| [QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) | 무료, 표준 | 매월 청구되는 구독 요금입니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)을 참조하세요. | 
| [텍스트 분석](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) | 무료, 표준 | 텍스트 레코드 수에 따라 청구됩니다. | 
| [Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator/) | 무료, 종량제(S1), 볼륨 할인(S2, S3, S4, C2, C3, C4, D3) | 가격 책정은 미터 및 기능에 따라 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/translator/)을 참조하세요. <li>텍스트 번역은 번역된 문자 수에 따라 청구됩니다.</li><li>문서 번역은 번역된 문자에 따라 청구됩니다.</li><li>사용자 지정 번역은 원본 및 대상 학습 데이터의 문자에 따라 청구됩니다.</li> |  
| **의사 결정** | | |
| [Anomaly Detector](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/) | 무료, 표준 | 트랜잭션 수를 기준으로 청구됩니다. | 
| [Content Moderator](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) | 무료, 표준 | 트랜잭션 수를 기준으로 청구됩니다. |
| [Personalizer](https://azure.microsoft.com/pricing/details/cognitive-services/personalizer/) | 무료, 표준(S0) | 월별 트랜잭션에 따라 청구됩니다. 스토리지 및 트랜잭션 할당량이 있습니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/personalizer/)을 참조하세요. | 


### <a name="costs-that-typically-accrue-with-cognitive-services"></a>일반적으로 Cognitive Services로 누적되는 비용

일반적으로 Azure 리소스를 배포한 후에는 가격 책정 계층 및 엔드포인트에 대한 API 호출에 따라 비용이 결정됩니다. 사용 중인 서비스에 약정 계층이 있는 경우 계층에서 할당된 호출을 넘을 때 초과분 요금이 청구될 수 있습니다.

다음 서비스를 사용하면 추가 비용이 누적될 수 있습니다.

#### <a name="qna-maker"></a>QnA Maker

QnA Maker에 대한 리소스를 만들 때 다른 Azure 서비스에 대한 리소스를 만들 수도 있습니다. 다음과 같은 변경 내용이 해당됩니다.

- [Azure App Service(런타임용)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search(데이터용)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>리소스 삭제 후에도 누적될 수 있는 비용

#### <a name="qna-maker"></a>QnA Maker

QnA Maker 리소스를 삭제한 후에도 다음 리소스는 계속 존재할 수 있습니다. 이를 삭제할 때까지는 계속 비용이 누적됩니다.

- [Azure App Service(런타임용)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search(데이터용)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Cognitive Services에 Azure 선불 크레딧 사용

Azure 선불(이전의 현금 약정 금액) 크레딧을 사용하여 Cognitive Services 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Cognitive Services에서 Azure 리소스를 사용하는 경우 비용이 발생합니다. 리소스 사용량 단위 비용은 시간 간격(초, 분, 시간, 일) 또는 단위 사용량(바이트, 메가바이트 등)에 따라 달라집니다. Cognitive Service(또는 Cognitive Services)를 사용하는 즉시 비용이 발생하며 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 확인할 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격에 대한 그래프 및 테이블의 Cognitive Services 비용을 볼 수 있습니다. 몇 가지 예로 일, 현재 달과 이전 달, 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.

비용 분석에서 Cognitive Services 비용을 보려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.
2. Azure Portal에서 범위를 열고, 메뉴에서 **비용 분석** 을 선택합니다. 예를 들어, **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. **범위** 를 선택하여 비용 분석에서 다른 범위로 전환합니다.
3. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시됩니다. Cognitive Services라는 차트에서 영역을 선택합니다.

처음으로 비용 분석을 열 경우 실제 월별 비용이 표시됩니다. 다음은 월별 사용의 모든 비용을 보여 주는 예입니다.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="구독에 대한 누적 비용을 보여 주는 예제":::

- Cognitive Services와 같은 단일 서비스로 비용 범위를 좁히려면 **필터 추가** 를 선택한 다음, **서비스 이름** 을 선택합니다. 그런 다음, **Cognitive Services** 를 선택합니다.

Cognitive Services에 대한 비용만 보여 주는 예제는 다음과 같습니다.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Cognitive Services에 대한 누적 비용을 보여 주는 예제":::

앞의 예제에서 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치) 및 리소스 그룹별 Cognitive Services 비용도 표시됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터는 추가 비용이 드는 새 리소스를 실수로 만들지 않도록 도움을 줍니다. 예산을 만들 때 사용하는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 귀하나 다른 사용자가 비용에 대한 추가 데이터 분석을 수행해야 하는 경우에 유용합니다. 예를 들어 재무 팀이 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 세트를 검색하려면 비용 데이터를 내보내는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.