---
title: 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용하여 Azure SQL Database 비용을 계획하고 관리하는 방법을 알아봅니다.
author: LitKnd
ms.author: kendralittle
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.subservice: service-overview
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 8e4a2a950a11c84b47ee94d838159cb83627b225
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133362465"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Azure SQL Database 비용 계획 및 관리

이 문서에서는 Azure SQL Database 비용을 계획하고 관리하는 방법을 설명합니다. 

먼저 Azure 가격 계산기를 사용하여 Azure 리소스를 추가하고 예상 비용을 검토합니다. Azure SQL Database 리소스 사용을 시작한 후에는 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 예상 비용을 검토하고 지출 추세를 기반으로 작업할 만한 영역을 파악할 수도 있습니다. Azure SQL Database 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 Azure SQL Database 비용을 계획하고 관리하는 방법을 설명하지만, 모든 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구됩니다.

> [!div class="nextstepaction"]
> [Azure SQL 개선을 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="prerequisites"></a>사전 요구 사항

비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. 

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.


## <a name="sql-database-initial-cost-considerations"></a>SQL Database 초기 비용 고려 사항

Azure SQL Database로 작업할 때 다음과 같은 몇 가지 비용 절감 기능을 고려해야 합니다.

### <a name="vcore-or-dtu-purchasing-models"></a>vCore 또는 DTU 구매 모델 

Azure SQL Database는 두 가지 구매 모델인 vCore와 DTU를 지원합니다. 요금이 청구되는 방법은 구매 모델에 따라 다르므로 비용을 계획하고 고려할 때 워크로드에 가장 적합한 모델을 파악하는 것이 중요합니다. vCore 및 DTU 구매 모델에 대한 자세한 내용은 [vCore 및 DTU 구매 모델 중에서 선택](purchasing-models.md)을 참조하세요.

### <a name="provisioned-or-serverless"></a>프로비저닝 또는 서버리스

vCore 구매 모델에서 Azure SQL Database는 두 종류의 컴퓨팅 계층인 프로비저닝된 처리량과 서버리스도 지원합니다. 요금이 청구되는 방법은 컴퓨팅 계층에 따라 다르므로 비용을 계획하고 고려할 때 워크로드에 가장 적합한 계층을 파악하는 것이 중요합니다. 자세한 내용은 [vCore 모델 개요 - 컴퓨팅 계층](service-tiers-sql-database-vcore.md#compute-tiers)을 참조하세요.

vCore 기반 구매 모델의 프로비저닝된 컴퓨팅 계층에서 할인된 요금으로 기존 라이선스를 교환할 수 있습니다. 자세한 내용은 [AHB(Azure 하이브리드 혜택)](../azure-hybrid-benefit.md)를 참조하세요.

### <a name="elastic-pools"></a>탄력적 풀

다양하고 예측할 수 없는 사용량 요구가 있는 여러 데이터베이스가 포함된 환경에서 탄력적 풀을 사용하면 동일한 수의 단일 데이터베이스를 프로비저닝하는 것보다 비용을 절감할 수 있습니다. 자세한 내용은 [탄력적 풀](elastic-pool-overview.md)을 참조하세요.

## <a name="estimate-azure-sql-database-costs"></a>Azure SQL Database 비용 예상

[Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 다양한 Azure SQL Database 구성에 따른 비용을 예상할 수 있습니다. 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/azure-sql-database/)을 참조하세요. 

다음 이미지의 정보 및 가격은 예제용으로만 사용됩니다. 

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Azure SQL Database 가격 계산기 예제":::

또한 다양한 보존 정책 옵션이 비용에 미치는 영향을 예상할 수 있습니다. 다음 이미지의 정보 및 가격은 예제용으로만 사용됩니다.

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="스토리지에 대한 Azure SQL Database 가격 계산기 예제":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Azure SQL Database에 대한 전체 청구 모델 이해

Azure SQL Database는 새 리소스를 배포할 때 Azure SQL Database와 함께 비용이 발생하는 Azure 인프라에서 실행됩니다. 추가 인프라로 비용이 누적될 수 있다는 것을 이해하는 것이 중요합니다.  

Azure SQL Database(서버리스 제외)는 예측 가능한 시간당 요금으로 청구됩니다. SQL 데이터베이스가 1시간 미만 동안 활성 상태인 경우, 데이터베이스가 1시간 미만 동안 활성 상태였는지 여부나 사용량과 관계없이 선택한 가장 높은 서비스 계층, 프로비저닝된 스토리지, 해당 시간 동안 적용된 IO에 대한 요금이 청구됩니다.

청구는 제품의 SKU, SKU의 생성 하드웨어 및 미터 범주에 따라 다릅니다. Azure SQL Database에는 다음과 같은 가능한 SKU가 있습니다.

- 기본(B)
- 표준(S)
- 프리미엄(P)
- 범용(GP)
- 중요 비즈니스용(BC)
- 스토리지: GRS(지역 중복 스토리지), LRS(로컬 중복 스토리지) 및 ZRS(영역 중복 스토리지)
- 사용되지 않는 리소스 제공에서 사용되지 않는 SKU를 가질 수도 있습니다.

자세한 내용은 [서비스 등급](service-tiers-general-purpose-business-critical.md)을 참조하세요. 

다음 표는 **단일 데이터베이스** 에 대한 가장 일반적인 청구 미터 및 가능한 SKU를 보여 줍니다. 

| 측정| 가능한 SKU | 설명 | 
| :----|:----|:----|
| Backup\* | GP/BC/HS | 백업에 사용된 스토리지 사용량을 측정하고 월별 사용된 스토리지 양(GB)으로 청구됩니다. | 
| 백업(LTR) | GRS/LRS/ZRS/GF | 장기 보존을 통해 구성된 장기 백업에서 사용하는 스토리지 사용량을 측정하고 사용된 스토리지 양으로 청구됩니다. | 
| Compute  | B/S/P/GP/BC  | 시간당 컴퓨팅 리소스 소비를 측정합니다. | 
| 컴퓨팅(기본/명명된 복제본) | HS | 기본 HS 복제본의 시간당 컴퓨팅 리소스 소비를 측정합니다. 
| 컴퓨팅(HA 복제본)             | HS | 보조 HS 복제본의 시간당 컴퓨팅 리소스 사용을 측정합니다. | 
| 컴퓨팅(ZR 추가 기능)              | GP | 영역 중복 추가 복제본의 분당 컴퓨팅 리소스 사용을 측정합니다. | 
| 컴퓨팅(서버리스)             | GP | 분당 서버리스 컴퓨팅 리소스의 사용을 측정합니다.  | 
| 라이선스 | GP/BC/HS | 매월 발생한 SQL Server 라이선스에 대한 청구입니다. | 
| Storage | B/S\*/P\*/G/BC/HS | 시간당 저장된 데이터 양에 따라 매월 청구됩니다. |

\* DTU 구매 모델에서는 데이터 및 백업을 위한 초기 스토리지 집합이 추가 비용 없이 제공됩니다. 스토리지 크기는 선택한 서비스 계층에 따라 다릅니다. 추가 데이터 스토리지는 표준 및 프리미엄 계층에서 구입할 수 있습니다. 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/azure-sql-database/)을 참조하세요. 

다음 표는 **탄력적 풀** 에 대해 가장 일반적인 청구 단위와 가능한 SKU를 보여 줍니다. 

| 측정| 가능한 SKU | 설명 | 
|:----|:----|:----|
| Backup\* | GP/BC  | 월 단위로 시간당 GB 단위로 청구되는 백업에 사용된 스토리지 사용량을 측정합니다. | 
| Compute  | B/S/P/GP/BC | vCore 및 메모리 또는 DTU와 같은 시간당 컴퓨팅 리소스 사용을 측정합니다. | 
| 라이선스 | GP/BC | 매월 발생한 SQL Server 라이선스에 대한 청구입니다. | 
| Storage | B/S\*/P\*/GP/HS | 시간당 스토리지 공간을 사용하여 드라이브에 저장된 데이터 양과 MBPS(초당 메가바이트) 처리량을 기준으로 매월 청구됩니다. |

\* DTU 구매 모델에서는 데이터 및 백업을 위한 초기 스토리지 집합이 추가 비용 없이 제공됩니다. 스토리지 크기는 선택한 서비스 계층에 따라 다릅니다. 추가 데이터 스토리지는 표준 및 프리미엄 계층에서 구입할 수 있습니다. 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/azure-sql-database/)을 참조하세요. 

### <a name="using-monetary-credit-with-azure-sql-database"></a>Azure SQL Database에서 금액 크레딧 사용

Azure 선불(이전에는 현금 약정 금액이라고 함) 크레딧을 사용하여 Azure SQL Database 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용하여 Azure Marketplace에 있는 항목을 포함한 타사 제품 및 서비스에 대한 요금을 지불할 수는 없습니다.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

Azure SQL Database를 만드는 과정에서 컴퓨팅 계층을 구성하는 동안 예상 비용을 확인할 수 있습니다. 

이 화면에 액세스하려면 **SQL 데이터베이스 만들기** 페이지의 **기본** 탭에서 **데이터베이스 구성** 을 선택합니다. 다음 이미지의 정보 및 가격은 예제용으로만 사용됩니다.

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Azure Portal의 예상 비용을 보여 주는 예제":::

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과하여 지출하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용됩니다. 신용 한도에 도달하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용하지 않도록 설정됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대한 자세한 내용은 [Azure 지출 한도](../../cost-management-billing/manage/spending-limit.md)를 참조하세요.

## <a name="monitor-costs"></a>비용 모니터링

Azure SQL Database 사용을 시작하면 포털에서 예상 비용을 확인할 수 있습니다. 다음 단계를 사용하여 예상 비용을 검토합니다.

1. Azure Portal에 로그인하여 Azure SQL 데이터베이스의 리소스 그룹으로 이동합니다. 데이터베이스로 이동하고 **개요** 섹션에서 **리소스 그룹** 을 선택하여 리소스 그룹을 찾을 수 있습니다.
1. 메뉴에서 **비용 분석** 을 선택합니다.
1. **누적 비용** 을 확인하고 하단 차트를 **서비스 이름** 으로 설정합니다. 이 차트에서는 현재 SQL Database의 예상 비용을 보여 줍니다. 전체 페이지의 비용을 Azure SQL Database로 좁히려면 **필터 추가** 를 선택한 다음, **Azure SQL Database** 를 선택합니다. 다음 이미지의 정보 및 가격은 예제용으로만 사용됩니다.

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Azure Portal의 누적 비용을 보여 주는 예제":::

여기에서 비용을 직접 살펴볼 수 있습니다. 다른 비용 분석 설정에 대한 자세한 내용은 [비용 분석 시작](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="create-budgets"></a>예산 만들기

[예산](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터는 실수로 새 리소스를 만들지 않도록 합니다. 예산을 만들 때 사용하는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 수행해야 할 때 유용합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 세트를 검색하는 데 권장되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Azure SQL Database 비용을 관리하고 절감하는 다른 방법

또한 Azure SQL Database를 사용하면 애플리케이션 요구에 따라 비용을 제어하기 위해 리소스를 스케일 업하거나 스케일 다운할 수 있습니다. 자세한 내용은 [데이터베이스 리소스의 동적 스케일링](scale-resources.md)을 참조하세요.

1~3년간 컴퓨팅 리소스에 대한 예약을 커밋하여 비용을 절감할 수 있습니다. 자세한 내용은 [예약된 용량으로 리소스 비용 절감](reserved-capacity-overview.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.