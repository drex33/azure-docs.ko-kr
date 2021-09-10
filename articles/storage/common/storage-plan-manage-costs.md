---
title: Azure Blob Storage에 대한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용하여 Azure Blob Storage에 대한 비용을 계획하고 관리하는 방법을 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 0497f35b1b0d1df05c1e64f092ff45ebb9678390
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113594574"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Azure Blob Storage에 대한 비용 계획 및 관리

이 문서는 Azure Blob Storage에 대한 비용을 계획하고 관리하는 데 도움이 됩니다. 첫째, Azure 가격 계산기를 사용하여 비용을 예측합니다. 스토리지 계정을 만든 후에는 필요한 부분에 대해서만 요금을 지불하도록 계정을 최적화합니다. 비용 관리 기능을 사용하여 예산 및 모니터링 비용을 설정합니다. 작업을 수행할 수 있는 영역을 식별하기 위해 예측 비용을 검토하고 지출 추세를 모니터링할 수도 있습니다.

Blob Storage 비용은 Azure 청구서의 월별 비용 중 일부일 뿐임에 유의하세요. 이 문서에서는 Blob Storage 비용을 예측하고 관리하는 방법을 설명하지만 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다. Blob Storage에 대한 비용 관리에 익숙해지면 비슷한 방법을 적용하여 구독에 사용되는 모든 Azure 서비스에 대한 비용을 관리할 수 있습니다.

## <a name="estimate-costs"></a>비용 예측

[Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 Azure Storage 계정으로 데이터 전송을 만들고 시작하기 전에 비용을 예측합니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 페이지에서 **Storage 계정** 타일을 선택합니다.

2. 페이지 아래로 스크롤하고 예상되는 **스토리지 계정** 섹션을 찾습니다.

3. 드롭다운 목록에서 옵션을 선택합니다. 

   이러한 드롭다운 목록의 값을 수정하면 예상 비용이 변경됩니다. 예상 값은 예상 비용의 아래쪽과 위쪽 모서리에 표시됩니다.

   ![예상 비용을 보여 주는 스크린샷](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   **유형** 드롭다운 목록의 값을 변경하면 이 워크시트에 표시되는 다른 옵션도 변경됩니다. **추가 정보** 섹션의 링크를 사용하여 각 옵션의 의미와 이러한 옵션이 스토리지 관련 작업의 가격에 미치는 영향에 대해 자세히 알아보세요. 

4. 나머지 옵션을 수정하여 예상 값에 대한 영향을 확인합니다.

## <a name="understand-the-full-billing-model-for-azure-blob-storage"></a>Azure Blob Storage에 대한 전체 청구 모델 이해

Azure Blob Storage는 새 리소스를 배포할 때 비용이 발생하는 Azure 인프라에서 실행됩니다. 다른 추가 인프라 비용이 발생할 수도 있다는 점을 이해하는 것이 중요합니다.

### <a name="how-youre-charged-for-azure-blob-storage"></a>Azure Blob Storage 요금 청구 방식

Azure Blob Storage 리소스를 만들거나 사용할 때 다음 측정 단위에 대해 요금이 청구됩니다. 

| 미터 | 단위 | 
|---|---|
| 데이터 스토리지 | GB당/매월|
| 작업 | 트랜잭션당 |
| 데이터 전송 | GB당 |
| 메타데이터 | GB당/매월<sup>1 |
| Blob 인덱스 태그 | 태그당<sup>2  |
| 변경 피드 | 기록된 변경당<sup>2 |
| 암호화 범위 | 매월<sup>2 |
| 쿼리 가속 | 검사한 GB당 및 반환된 GB당 |

<sup>1</sup> 계층 구조 네임스페이스가 있는 계정에만 적용됩니다.<br />
<sup>2</sup> 기능을 사용하도록 설정한 경우에만 적용됩니다<br />

데이터 트래픽으로 인해 네트워킹 비용이 발생할 수도 있습니다. [대역폭 가격 책정](https://azure.microsoft.com/pricing/details/data-transfers/)을 참조하세요.

청구 주기가 끝날 때 각 측정 단위의 요금이 합산됩니다. 청구서 또는 송장에 모든 Azure Blob Storage 비용에 대한 섹션이 표시됩니다. 각 측정 단위에 대한 별도의 줄 항목이 있습니다.

데이터 저장소 및 메타데이터는 매월 GB당 청구됩니다. 한 달 미만 동안 저장된 데이터 및 메타데이터의 경우, 하루에 각 GB의 비용을 계산하여 월별 청구서에 미치는 영향을 예상할 수 있습니다. 비슷한 접근 방식을 사용하여 한 달 미만으로 사용 중인 암호화 범위의 비용을 예측할 수 있습니다. 지정된 월의 일 수는 다릅니다. 따라서 지정된 월의 비용에 대한 최상의 근사값을 얻으려면 월별 비용을 해당 월에 발생하는 일 수를 기준으로 나누어야 합니다. 

### <a name="finding-the-unit-price-for-each-meter"></a>미터당 단가 찾기

단가를 찾으려면 올바른 가격 책정 페이지를 엽니다. 계정에서 계층 구조 네임스페이스 기능을 사용하도록 설정한 경우 [Azure Data Lake Storage Gen2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/) 페이지를 참조하세요. 이 기능을 사용하도록 설정하지 않은 경우에는 [블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 페이지를 참조하세요.

가격 책정 페이지에서 적절한 중복성, 지역 및 통화 필터를 적용합니다. 각 미터의 가격은 테이블에 표시됩니다. 가격은 데이터 중복 옵션, 액세스 계층, 성능 계층 등 계정의 다른 설정에 따라 다릅니다. 

### <a name="flat-namespace-accounts-and-transaction-pricing"></a>단일 구조 네임스페이스 계정 및 트랜잭션 가격 책정

클라이언트는 계정의 Blob Storage 엔드포인트 또는 Data Lake Storage 엔드포인트를 사용하여 요청할 수 있습니다. 스토리지 계정 엔드포인트를 자세히 알아보려면 [스토리지 계정 개요](storage-account-overview.md#storage-account-endpoints)를 참조하세요.

[블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 페이지에 표시되는 트랜잭션 가격은 Blob Storage 엔드포인트(예: `https://<storage-account>.blob.core.windows.net`)를 사용하는 요청에만 적용됩니다. 나열된 가격은 Data Lake Storage Gen2 엔드포인트(예: `https://<storage-account>.dfs.core.windows.net`)를 사용하는 요청에는 적용되지 않습니다. 이러한 요청의 트랜잭션 가격의 경우 [Azure Data Lake Storage Gen2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/) 페이지를 열고 **단일 구조 네임스페이스** 옵션을 선택합니다. 

> [!div class="mx-imgBorder"]
> ![단일 구조 네임스페이스 옵션](media/storage-plan-manage-costs/select-flat-namespace.png)

Data Lake Storage Gen2 엔드포인트에 대한 요청은 다음 원본 중 하나에서 시작될 수 있습니다.

- Azure Blob File System 드라이버 또는 [ABFS 드라이버](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)를 사용하는 워크로드

- [Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2)를 사용하는 REST 호출

- Azure Storage 클라이언트 라이브러리에서 Data Lake Storage Gen2 API를 사용하는 애플리케이션  


### <a name="using-azure-prepayment-with-azure-blob-storage"></a>Azure Blob Storage와 함께 Azure 선불 사용

Azure 선불(이전에는 현금 약정 금액이라고 함) 크레딧을 사용하여 Azure Blob Storage 요금을 지불할 수 있습니다. 단, Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="optimize-costs"></a>비용 최적화

이러한 옵션을 사용하여 비용을 줄이는 것이 좋습니다. 

- 스토리지 용량 예약

- 액세스 계층으로 데이터 구성

- 액세스 계층 간에 데이터를 자동으로 이동

이 섹션에서는 각 옵션에 대해 자세히 설명합니다. 

#### <a name="reserve-storage-capacity"></a>스토리지 용량 예약

Azure Storage 예약된 용량을 사용하여 Blob 데이터에 대한 스토리지 비용을 절감할 수 있습니다. Azure Storage 예약된 용량은 1년 또는 3년 예약을 약정하면 표준 스토리지 계정의 블록 Blob 및 Azure Data Lake Storage Gen2 데이터에 대한 용량 할인을 제공합니다. 예약은 예약 기간 동안 고정된 양의 스토리지 용량을 제공합니다. Azure Storage 예약된 용량은 블록 Blob 및 Azure Data Lake Storage Gen2 데이터에 대한 용량 비용을 크게 줄일 수 있습니다. 

자세한 내용은 [예약된 용량으로 Blob Storage에 대한 비용 최적화](../blobs/storage-blob-reserved-capacity.md)를 참조하세요.

#### <a name="organize-data-into-access-tiers"></a>액세스 계층으로 데이터 구성

Blob 데이터를 가장 비용 효율적인 액세스 계층에 배치하여 비용을 줄일 수 있습니다. 데이터 사용에 대한 비용을 최적화하도록 설계된 세 가지 계층 중에서 선택합니다. 예를 들어, *핫* 계층은 스토리지 비용은 더 높지만 액세스 비용은 더 낮습니다. 따라서 데이터에 자주 액세스할 계획인 경우 핫 계층이 가장 비용 효율적인 선택이 될 수 있습니다. 데이터 액세스 빈도를 줄이려는 경우 *콜드* 또는 *보관* 계층이 데이터 액세스 비용을 높이고 데이터 저장 비용을 절감하므로 가장 적합할 수 있습니다.    

자세한 내용은 [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal)을 참조하세요.

#### <a name="automatically-move-data-between-access-tiers"></a>액세스 계층 간에 데이터를 자동으로 이동

수명 주기 관리 정책을 사용하여 정기적으로 계층 간에 데이터를 이동하면 비용을 절감할 수 있습니다. 이러한 정책은 지정한 규칙을 사용하여 데이터를 이동할 수 있습니다. 예를 들어 Blob가 90일 이내에 수정되지 않은 경우 Blob를 보관 계층으로 이동하는 규칙을 만들 수 있습니다. 데이터의 액세스 계층을 조정하는 정책을 만들어 요구에 가장 저렴한 스토리지 옵션을 디자인할 수 있습니다.

자세한 내용은 [Azure Blob Storage 수명 주기 관리](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)를 참조하세요.

## <a name="create-budgets"></a>예산 만들기

[예산](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 그러나 더 높은 수준에서 비용을 추적하도록 디자인되었기 때문에 Azure Storage 비용과 같은 개별 Azure 서비스 비용을 관리하는 기능이 제한될 수 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Storage를 사용하여 Azure 리소스를 사용하는 경우 비용이 발생합니다. 리소스 사용 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 단위 사용(바이트, 메가바이트 등)에 따라 달라집니다. 비용은 Azure Storage 사용이 시작되는 즉시 발생합니다. Azure Portal의 [비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 창에서 비용을 조회할 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격에 대한 그래프 및 테이블로 Azure Storage 비용을 조회할 수 있습니다. 몇 가지 예로 일, 현재 달과 이전 달 및 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간 경과에 따라 더 긴 보기로 전환하면 지출 추세를 파악하고 낭비가 발생할 수 있는 위치를 확인할 수 있습니다. 예산을 만든 경우 초과되는 지점을 쉽게 확인할 수도 있습니다.

>[!NOTE]
> 비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

비용 분석에서 Azure Storage 비용을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + Billing** 창을 열고 메뉴에서 **비용 관리** 를 선택한 다음, **비용 분석 열기** 를 선택합니다. 그런 다음 **범위** 드롭다운에서 특정 구독에 대한 범위를 변경할 수 있습니다.

   ![범위를 보여 주는 스크린샷](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Azure Storage에 대한 비용만 보려면 **필터 추가** 를 선택한 다음 **서비스 이름** 을 선택합니다. 그런 다음 목록에서 **스토리지** 를 선택합니다. 

   Azure Storage에 대한 비용을 보여 주는 예제는 다음과 같습니다.

   ![스토리지로 필터링을 보여 주는 스크린샷](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

앞의 예제에서 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치) 및 리소스 그룹별 비용도 표시됩니다. 다른 필터(예: 특정 스토리지 계정에 대한 비용을 확인하는 필터)도 추가할 수 있습니다.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 직접 수행해야 하거나 다른 사용자가 수행하는 경우에 유용합니다. 예를 들어 재무 팀이 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 세트를 검색하려면 비용 데이터를 내보내는 것이 좋습니다.

## <a name="faq"></a>FAQ

**Azure Storage를 한 달에 며칠만 사용하는 경우 비용이 일별로 계산되나요?**

스토리지 용량은 한 달 동안 저장된 평균 일일 데이터 양(GB)을 단위로 요금이 청구됩니다. 예를 들어 매월 첫 15일은 10GB의 스토리지를 지속적으로 사용하고 나머지 15일은 사용하지 않은 경우 평균 5GB의 스토리지 사용량에 대해 요금이 청구됩니다.


## <a name="next-steps"></a>다음 단계

- Azure Storage를 사용한 가격 책정의 작동 방법에 대해 자세히 알아봅니다. [Azure Storage 개요 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.
- [예약된 용량으로 Blob Storage에 대한 비용을 최적화](../blobs/storage-blob-reserved-capacity.md)합니다.
- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.