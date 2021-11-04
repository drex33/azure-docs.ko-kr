---
title: Azure Data Factory의 비용 관리 계획
description: Azure Portal에서 비용 분석을 사용하여 Azure Data Factory에 대한 비용 플랜 및 관리하는 방법을 알아봅니다.
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.subservice: pricing
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 11/01/2021
ms.openlocfilehash: 51f71bcf39f0b54bf0f7fb389fbbb9797fbb2151
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469467"
---
# <a name="plan-to-manage-costs-for-azure-data-factory"></a>Azure Data Factory의 비용 관리 계획

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에 대한 계획 및 비용 관리 방법을 설명합니다. 

먼저 ETL 프로젝트의 시작 부분에서 Azure 가격 책정 및 파이프라인 단위당 사용량과 가격 계산기의 조합을 사용하여 비용을 예측하는 서비스에 대한 리소스를 추가하기 전에 Azure Data Factory 비용을 계획할 수 있습니다. 그런 다음, Azure 리소스를 추가할 때 예상 비용을 검토합니다. Azure Data Factory 리소스 사용을 시작한 후 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 예측 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure Data Factory 비용은 Azure 청구서의 월간 비용 중에 일부에 불과합니다. 이 문서에서는 Data Factory에 대한 비용을 계획하고 관리하는 방법을 설명하지만, 사용자에게는 타사 서비스를 비롯한 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Cost Management에서의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-azure-data-factory"></a>Azure Data Factory를 사용하기 전에 비용 예측
 
[ADF 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=data-factory)를 사용하여 Azure Data Factory에서 ETL 워크로드를 실행하는 데 드는 비용을 예측합니다.  계산기를 사용하려면 활동 실행 횟수, 데이터 통합 단위 수, Data Flow에 사용되는 컴퓨팅 유형, 코어 수, 인스턴스 수, 실행 기간 등의 세부 정보를 입력해야 합니다.

가격 계산기에 대해 자주 묻는 질문 중 하나는 입력으로 사용해야 하는 값입니다.  개념 증명 단계에서 샘플 데이터 세트를 통해 시운전을 수행하여 다양한 ADF 미터의 사용량을 이해할 수 있습니다.  그런 다음, 샘플 데이터 세트의 사용량을 기반으로 전체 데이터 세트의 사용량 및 운영화 일정을 예상할 수 있습니다.

> [!NOTE]
> 아래 예제에 사용된 가격은 가상이며 실제 가격 책정을 의미하지는 않습니다.

예를 들어 AWS S3에서 Azure Data Lake Gen2로 매일 1TB의 데이터를 이동해야 한다고 가정해 보겠습니다.  100GB 데이터 이동의 POC를 수행하여 데이터 수집 처리량을 측정하고 해당 청구 사용량을 이해할 수 있습니다.

다음은 복사 작업 실행 세부 정보 샘플입니다. 실제 진행 속도는 특정 데이터 세트의 모양, 네트워크 속도, S3 계정의 송신 제한, ADLS Gen2 수신 제한 및 기타 요인에 따라 달라질 수 있습니다.

:::image type="content" source="media/plan-manage-costs/s3-copy-run-details.png" alt-text="S3 복사 실행":::

[파이프라인 실행 수준의 사용량 모니터링](#monitor-consumption-at-pipeline-run-level)을 활용하여 해당 데이터 이동 미터 사용량을 확인할 수 있습니다.

:::image type="content" source="media/plan-manage-costs/s3-copy-pipeline-consumption.png" alt-text="S3 복사 파이프라인 사용량":::

따라서 한 달 내내 매일 1TB를 이동하는 데 소요되는 총 DIU 시간은 다음과 같습니다.

1.2667(DIU 시간) * (1TB/100GB) * 30(한 달의 일수) = 380 DIU 시간

이제 30회의 활동 실행 및 380 DIU 시간을 ADF 가격 계산기에 연결하여 월간 예상 청구 금액을 구할 수 있습니다.

:::image type="content" source="media/plan-manage-costs/s3-copy-pricing-calculator.png" alt-text="S3 복사 가격 계산기":::

## <a name="understand-the-full-billing-model-for-azure-data-factory"></a>Azure Data Factory에 대한 전체 청구 모델 이해

Azure Data Factory는 새 리소스를 배포할 때 비용이 발생하는 Azure 인프라에서 실행됩니다. 다른 추가 인프라 비용이 발생할 수도 있다는 점을 이해하는 것이 중요합니다.

### <a name="how-youre-charged-for-azure-data-factory"></a>Azure Data Factory 요금 청구 방법

Azure Data Factory는 클라우드 스케일링을 위한 탄력적 서버리스 데이터 통합 서비스입니다.  즉, 최대 부하에 대해 계획해야 하는 고정 크기 컴퓨팅이 없습니다. 대신, 요청 시 작업별로 할당할 리소스의 양을 지정하여 확장성이 훨씬 더 큰 ETL 프로세스를 설계할 수 있습니다. 또한 ADF는 사용량 기반 요금제로 청구됩니다. 즉, 사용자가 사용한 만큼만 요금을 지불합니다.

Azure Data Factory 리소스를 만들거나 사용하는 경우 다음 측정 단위에 대해 요금이 청구될 수 있습니다.

- 오케스트레이션 활동 실행 - 작업이 실행하는 오케스트레이션 수에 따라 요금이 청구됩니다.
- DIU(데이터 통합 단위) 시간 – 복사 작업은 Azure Integration Runtime에서 실행되며 사용된 DIU 수 및 실행 기간에 따라 요금이 청구됩니다.
- vCore 시간 – 데이터 흐름 실행 및 디버깅의 경우 계산 형식, vCore 수 및 실행 기간을 기준으로 요금이 청구됩니다.


청구 주기가 끝날 때 각 측정 단위의 요금이 합산됩니다. 청구서 또는 청구서에 Azure Data Factory 관련 모든 비용에 대한 섹션이 표시됩니다. 각 측정 단위에 대한 별도의 품목이 있습니다.

### <a name="other-costs-that-might-accrue-with-azure-data-factory"></a>Azure Data Factory로 인해 발생할 수 있는 기타 비용

ADF (Azure Data Factory)에 대 한 리소스를 만들 때 다른 Azure 서비스에 대 한 리소스도 생성 됩니다. 해당 기능은 아래와 같습니다.

- 파이프라인 활동 실행
- 외부 파이프라인 활동 실행
- 데이터 팩터리 아티팩트의 생성/편집/검색/모니터링
- 인스턴스 유형 및 기간을 기반으로 하는 IR (SSIS Integration Runtime) 기간

> [!NOTE]
> ADF 및 기타 Azure 리소스에 동일한 태그를 할당 하 여 동일한 범주에 배치 하 여 통합 된 청구를 볼 수 있습니다. ADF 태그는 모든 SSIS IRs 상속 됩니다. ADF 태그를 변경 하는 경우 새 태그를 상속 하려면 모든 SSIS IRs을 중지 했다가 다시 시작 해야 합니다. [SSIS IR 다시 구성](manage-azure-ssis-integration-runtime.md#to-reconfigure-an-azure-ssis-ir) 섹션을 참조 하세요.

### <a name="using-azure-prepayment-with-azure-data-factory"></a>Azure Data Factory와 함께 Azure 선불 사용

Azure 선불 크레딧을 사용하여 Azure Data Factory 요금을 지불할 수 있습니다. 단, Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Data Factory 비용은 공장, 파이프라인 실행 및 활동 실행 수준에서 모니터링할 수 있습니다.

### <a name="monitor-costs-at-factory-level"></a>센터 수준에서 비용 모니터링

Data Factory를 사용하여 Azure 리소스를 사용하는 경우 비용이 발생합니다. Azure 리소스 사용량 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 단위 사용량(바이트, 메가바이트 등)에 따라 다릅니다. Data Factory 사용이 시작되는 즉시 비용이 발생하고 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 확인할 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격에 대한 그래프 및 테이블의 Data Factory 비용을 볼 수 있습니다. 몇 가지 예로 일, 현재 달과 이전 달 및 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.

비용 분석에서 Data Factory 관련 비용을 확인하려면:

1. Azure Portal에 로그인합니다.
2. Azure Portal에서 범위를 열고, 메뉴에서 **비용 분석** 을 선택합니다. 예를 들어, **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. **범위** 를 선택하여 비용 분석에서 다른 범위로 전환합니다.
3. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시됩니다. Azure Data Factory v2라는 레이블이 지정된 차트에서 영역을 선택합니다.

처음으로 비용 분석을 열 경우 실제 월별 비용이 표시됩니다. 다음은 월별 사용의 모든 비용을 보여 주는 예입니다.

:::image type="content" source="media/all-costs.png" alt-text="구독에 대한 누적 비용을 보여 주는 예":::

- Data Factory와 같은 단일 서비스로 비용 범위를 좁히려면 **필터 추가** 를 선택한 다음, **서비스 이름** 을 선택합니다. 그런 다음 **Azure Data Factory v2** 를 선택합니다.

Azure Data Factory에 대한 비용을 보여 주는 예는 다음과 같습니다.

:::image type="content" source="media/service-specific-cost.png" alt-text="ServiceName에 대한 누적 비용을 보여 주는 예":::

앞의 예제에서 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치) 및 리소스 그룹별 Data Factory 비용 또한 표시됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

### <a name="monitor-consumption-at-pipeline-run-level"></a>파이프라인 실행 수준에서 사용량 모니터링

파이프라인을 실행할 때는 파이프라인에 있는 활동의 유형, 이동하고 변환하는 데이터의 양 및 변환의 복잡도에 따라, Azure Data Factory에서 서로 다른 청구 미터가 작동합니다.

Azure Data Factory 사용자 환경에서 개별 파이프라인 실행에 대한 서로 다른 미터의 사용량을 볼 수 있습니다. 모니터링 환경을 열려면 [Azure Portal](https://portal.azure.com/) 데이터 팩터리 블레이드에서 **모니터링 및 관리** 타일을 선택합니다. ADF UX에 이미 있는 경우 왼쪽 사이드바에서 **모니터** 아이콘을 클릭합니다. 기본 모니터링 보기는 파이프라인 실행 목록입니다.

파이프라인 이름 옆에 있는 **사용량** 단추를 클릭하면 파이프라인 내의 모든 활동에서 집계된 파이프라인 실행의 사용량을 보여 주는 팝업 창이 표시됩니다.

:::image type="content" source="media/plan-manage-costs/pipeline-run-consumption.png" alt-text="파이프라인 실행 사용량":::

:::image type="content" source="media/plan-manage-costs/pipeline-consumption-details.png" alt-text="파이프라인 사용량 세부 정보":::

파이프라인 실행 사용량 보기에서는 특정 파이프라인 실행의 각 ADF 미터별 사용량을 보여 주지만 사용자에게 청구되는 금액은 보유하고 있는 Azure 계정의 유형과 사용하는 통화의 유형에 따라 달라지므로 실제 청구 금액은 표시되지 않습니다.  지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md)를 참조하세요.

### <a name="monitor-consumption-at-activity-run-level"></a>활동 실행 수준에서 사용량 모니터링
파이프라인 실행 수준에서 집계된 사용량을 이해한 후에는 필요에 따라 더 깊이 들어가서 파이프라인 내에서 가장 비용이 많이 드는 활동을 식별해야 합니다.

활동 실행 수준에서 사용량을 확인하려면 데이터 팩터리 **작성자 및 모니터** UI로 이동합니다. 파이프라인 실행 목록이 표시되는 **모니터** 탭에서 **파이프라인 이름** 링크를 클릭하여 파이프라인 실행의 활동 실행 목록에 액세스합니다.  활동 이름 옆의 출력 **단추** 를 클릭하고 JSON 출력에서 **billableDuration** 속성을 찾습니다.

다음은 복사 작업 실행의 샘플입니다.

:::image type="content" source="media/plan-manage-costs/copy-output.png" alt-text="출력 복사":::

다음은 매핑 데이터 흐름 활동 실행의 샘플입니다.

:::image type="content" source="media/plan-manage-costs/dataflow-output.png" alt-text="데이터 흐름 출력":::

## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 발생시키는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산을 만들 때 사용할 수 있는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 직접 수행해야 하거나 다른 사용자가 수행하는 경우에 유용합니다. 예를 들어 재무 팀이 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 세트를 검색하려면 비용 데이터를 내보내는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.
- [Azure Data Factory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [예제를 통한 Azure Data Factory 이해](./pricing-concepts.md)
- [Azure Data Factory 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
