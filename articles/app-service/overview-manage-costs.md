---
title: App Service에 대한 비용 관리 플랜
description: Azure Portal에서 비용 분석을 사용하여 Azure App Service에 대한 비용 플랜 및 관리하는 방법을 알아봅니다.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 06/23/2021
ms.openlocfilehash: 3f2ae25c3f2e1076cf714aa56f7ecdf867d6dd8f
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112554192"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Azure App Service에 대한 비용 계획 및 관리

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

이 문서에서는 Azure App Service의 비용을 플랜 및 관리하는 방법을 설명합니다. 비용을 예측하는 서비스에 대한 리소스를 추가하기 전에 Azure 요금 계산기를 사용하여 App Service 비용을 계획할 수 있습니다. 그런 다음 Azure 리소스를 추가할 때 예상 비용을 검토합니다. App Service 리소스 사용을 시작한 후에는 [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 기능을 통해 예산을 설정하고 비용을 모니터링합니다. 예상 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure App Service 비용은 Azure 청구서의 월간 비용 중에 일부에 불과합니다. 이 문서에서는 App Service에 대한 비용을 계획하고 관리하는 방법을 설명하지만, 사용자에게는 타사 서비스를 비롯한 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구됩니다.

## <a name="understand-the-full-billing-model-for-azure-app-service"></a>Azure App Service에 대한 전체 청구 모델 이해

Azure App Service는 새 리소스를 배포할 때 비용이 발생하는 Azure 인프라에서 실행됩니다. 다른 추가 인프라 비용이 발생할 수도 있다는 점을 이해하는 것이 중요합니다.

### <a name="how-youre-charged-for-azure-app-service"></a>Azure App Service 요금 청구 방식

App Service 리소스를 만들거나 사용할 때 다음 측정 단위에 대해 요금이 청구됩니다.

- App Service 계획의 가격 책정 계층에 따라 시간당 요금이 청구되며, 초 단위로 비례 배분하여 계산됩니다.
- 요금은 VM 인스턴스가 할당된 시간에 따라 계획의 각 확장 인스턴스에 적용됩니다. 

App Service에 대한 기타 비용 리소스(세부 정보는 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/) 참조):

- [App Service 도메인](manage-custom-dns-buy-domain.md)  자동 갱신을 사용하도록 설정하는 경우 구독에는 매년 도메인 등록 요금이 청구됩니다.
- [App Service 인증서](configure-ssl-certificate.md#import-an-app-service-certificate)  구매 시점에 일회성으로 요금이 부과됩니다. 보호할 하위 도메인이 여러 개 있는 경우 여러 표준 인증서 대신 하나의 와일드카드 인증서를 구매하여 비용을 절감할 수 있습니다.
- [IP 기반 인증서 바인딩](configure-ssl-bindings.md#create-binding)  해당 바인딩은 앱 수준의 인증서에 대해 구성됩니다. 비용은 각 바인딩에 대해 발생합니다. **표준** 계층 이상의 경우 첫 번째 IP 기반 바인딩에는 요금이 부과되지 않습니다.

청구 주기가 끝날 때 각 VM 인스턴스의 요금이 청구됩니다. 청구서에는 모든 App Service 비용에 대한 섹션이 표시됩니다. 각 측정 단위에 대한 별도의 줄 항목이 있습니다.

### <a name="other-costs-that-might-accrue-with-azure-app-service"></a>Azure App Service로 인해 발생할 수 있는 기타 비용

App Service에서 사용하는 기능에 따라 다음과 같은 비용 발생 리소스가 만들어질 수 있습니다.

- **격리 계층** App Service Environment에는 [가상 네트워크](../virtual-network/index.yml)가 필요하며 별도의 요금이 청구됩니다.
- **백업** 백업하려면 [스토리지 계정](../storage/index.yml)이 필요하며 별도로 청구됩니다.
- **진단 로그**  [스토리지 계정](../storage/index.yml)을 로깅 옵션으로 선택하거나 [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)와 통합할 수 있습니다. 이러한 서비스는 별도로 청구됩니다.
- **App Service 인증서** Azure에서 구입한 인증서는 [Azure Key Vault](../key-vault/index.yml)에서 유지 관리해야 하며 요금은 별도로 청구됩니다.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>리소스 삭제 후 발생할 수 있는 비용

App Service 플랜의 모든 앱을 삭제하는 경우 플랜은 구성된 가격 책정 계층 및 인스턴스 수에 따라 요금을 계속 발생시킵니다. 원치 않는 요금을 방지하려면 플랜을 삭제하거나 **평가판** 계층으로 축소합니다.

Azure App Service 리소스를 삭제한 후에도 관련 Azure 서비스의 리소스가 계속 존재할 수 있습니다. 해당 리소스는 삭제할 때까지 계속해서 비용을 발생시킵니다. 예를 들면 다음과 같습니다.

- **격리** 계층 App Service 플랜에 대해 만든 Virtual Network
- 백업 또는 진단 로그를 저장하기 위해 만든 스토리지 계정
- App Service 인증서를 저장하기 위해 만든 Key Vault
- 진단 로그를 발송하기 위해 만든 Log Analytics 네임스페이스
- 아직 만료되지 않은 App Service에 대한 [인스턴스 또는 스탬프 예약](#azure-reservations)

### <a name="using-azure-prepayment-with-azure-app-service"></a>Azure App Service와 함께 Azure 선불 사용

Azure 선불 크레딧을 사용하여 Azure App Service 요금을 지불할 수 있습니다. 그러나 Azure Marketplace에서 구매한 서비스를 포함하여 타사 제품 및 서비스에 대한 요금은 Azure 선불 크레딧을 사용하여 지불할 수 없습니다.

## <a name="estimate-costs"></a>비용 예측

사전에 App Service 비용을 예측하고 최적화하는 가장 쉬운 방법은 [Azure 요금 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하는 것입니다.

요금 계산기를 사용하려면 **제품** 탭에서 **App Service** 를 클릭합니다. 그런 다음 아래로 스크롤하여 계산기로 작업합니다. 다음 스크린샷은 예제이며 현재 요금은 반영되지 않습니다.

![Azure 요금 계산기의 예상 비용을 보여 주는 예제](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

App Service 앱 또는 App Service 플랜을 만드는 경우 예상 비용을 볼 수 있습니다.

앱을 만들고 예상 비용을 보려면 다음을 수행합니다.

1. 만들기 페이지에서 **App Service 플랜** 으로 스크롤을 내려 **새로 만들기** 를 클릭합니다.
1. 이름을 지정하고 **확인** 을 클릭합니다.
1. **SKU 및 크기** 옆에 있는 **크기 변경** 을 클릭합니다.
1. 요약에 표시된 예상 비용을 검토합니다. 다음 스크린샷은 예제이며 현재 요금은 반영되지 않습니다.

    ![포털에서 각 가격 책정 계층에 대한 예상 비용 검토](media/overview-manage-costs/pricing-estimates.png)

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과하여 지출하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용됩니다. 신용 한도에 도달하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용하지 않도록 설정됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대한 자세한 내용은 [Azure 지출 한도](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요.

## <a name="optimize-costs"></a>비용 최적화

기본 수준에서 App Service 앱은 해당 앱을 호스트하는 App Service 플랜에 의해 부과됩니다. App Service 배포와 관련된 비용은 다음의 몇 가지 주요 요소에 따라 달라집니다.

- **가격 책정 계층**  혹은 App Service 플랜의 SKU라고도 합니다. 상위 계층은 보다 많은 CPU 코어, 메모리, 스토리지나 기능, 또는 그 모두의 결합을 제공합니다.
- **인스턴스 수**  전용 계층(기본 이상)을 확장할 수 있으며, 확장된 각 인스턴스는 비용을 발생시킬 수 있습니다.
- **인지세**  격리 계층에서는 호스트되는 앱 또는 작업자 인스턴스 수에 관계없이 App Service 환경에서 고정 요금이 발생합니다.

App Service 플랜은 둘 이상의 앱을 호스트할 수 있습니다. 배포에 따라 하나의 App Service 플랜에서 더 많은 앱을 호스트하여, 즉 여러 앱을 적은 수의 App Service 플랜으로 호스트하여 비용을 절감할 수 있습니다.

세부 정보는 [App Service 플랜 개요](overview-hosting-plans.md)를 참조하세요.

### <a name="non-production-workloads"></a>프로덕션 외 워크로드

저렴하거나 최소의 비용으로 App Service 또는 솔루션을 테스트하려면 공유 인스턴스에 호스트되는 두 개의 항목 수준 가격 책정 계층인 **체험** 및 **공유** 계층을 사용하여 시작할 수 있습니다. 더 나은 성능으로 전용 인스턴스에서 앱을 테스트하려면 Windows 및 Linux 앱을 모두 지원하는 **기본** 계층으로 업그레이드할 수 있습니다. 

> [!NOTE]
> **Azure 개발/테스트 요금**  더 높은 계층(**격리** 계층을 제외한 모든 계층)을 요구하는 프로덕션 이전 워크로드를 테스트하기 위해 Visual Studio 구독자는 상당한 할인을 제공하는 [Azure 개발/테스트 요금](https://azure.microsoft.com/pricing/dev-test/)을 활용할 수도 있습니다.
>
> Azure 개발/테스트 요금을 통한 가격 할인 뿐만 아니라 **체험** 및 **공유** 계층은 모두 재정적 지원을 받는 SLA를 제공하지 않습니다.

### <a name="production-workloads"></a>프로덕션 워크로드

프로덕션 워크로드는 전용 **표준** 가격 책정 계층의 권장 사항과 함께 제공됩니다. 상위 계층에서는 가격이 상승하지만, 더 많은 메모리와 스토리지 및 더 높은 성능의 하드웨어를 제공하여 컴퓨팅 인스턴스당 애플리케이션 밀도를 높입니다. 이는 동일한 수의 앱에 대한 인스턴스 수를 줄임으로써 비용을 절감하는 것으로 해석됩니다. 실제로 **프리미엄 V3**(비 **격리** 계층 중 가장 높은 계층)는 대규모 앱을 지원하는 가장 효율적인 방법입니다. [Premium V3 예약](#azure-reservations)에 대한 심층 할인을 받으면, 절감 효과는 더욱 커집니다.

> [!NOTE]
> **Premium V3** 계층은 Windows 컨테이너와 Linux 컨테이너를 모두 지원합니다. 

원하는 가격 책정 계층을 선택한 후에는 유휴 인스턴스를 최소화해야 합니다. 스케일 아웃 배포에서는 미달 사용 컴퓨팅 인스턴스에 대한 비용이 낭비됩니다. **표준** 계층 이상에서 사용할 수 있는 [자동 스케일링 구성](../azure-monitor/autoscale/autoscale-get-started.md) 작업을 수행해야 합니다. 메트릭 기반 스케일 아웃 규칙뿐만 아니라 스케일 아웃 일정을 만들어 언제든 정말 필요한 인스턴스에만 요금을 지불하면 됩니다.

### <a name="azure-reservations"></a>Azure Reservations

알려진 최소 수의 컴퓨팅 인스턴스를 1년 이상 활용할 플랜인 경우 **Premium V3** 계층을 이용하면 해당 인스턴스를 1년 또는 3년 단위로 예약하여 인스턴스 비용을 크게 줄일 수 있습니다. 인스턴스당 55%까지 월별 비용을 절감할 수 있습니다. 두 가지 유형의 예약이 가능합니다.

- **Windows(또는 플랫폼에 관계없음)**  구독에서 Windows 또는 Linux 인스턴스에 적용할 수 있습니다.
- **Linux 전용**  구독의 Linux 인스턴스에만 적용됩니다.

예약 인스턴스 가격 책정은 구독에서 예약한 인스턴스의 수까지 적용됩니다. 예약 인스턴스는 청구에 관련된 것이며 특정 컴퓨팅 인스턴스와는 연결되지 않습니다. 예약 기간 중 언제든지 예약된 것보다 적은 인스턴스를 실행하는 경우에도 예약 인스턴스에 대한 요금은 계속 청구됩니다. 예약 기간 중 임의의 시점에 예약된 것보다 더 많은 인스턴스를 실행하는 경우 추가 인스턴스에 대해 정상적으로 발생한 비용을 지불해야 합니다.

**격리** 계층(App Service 환경)은 저렴한 요금으로 1년 및 3년 단위 예약도 지원합니다. 자세한 내용은 [Azure App Service에 예약 할인이 적용되는 방식](../cost-management-billing/reservations/reservation-discount-app-service.md)을 참조하세요.

## <a name="monitor-costs"></a>비용 모니터링

App Service에서 Azure 리소스를 사용하는 경우 비용이 발생합니다. Azure 리소스 사용 단위 비용은 시간 간격(초, 분, 시간, 일)에 따라 달라집니다. App Service를 시작하는 즉시 비용이 발생하며 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 볼 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격에 대한 그래프 및 테이블의 App Service 비용을 볼 수 있습니다. 몇 가지 예로 일, 현재 달과 이전 달 및 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.
    
비용 분석에서 App Service 비용을 보려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.
2. Azure Portal에서 범위를 열고, 메뉴에서 **비용 분석** 을 선택합니다. 예를 들어, **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. **범위** 를 선택하여 비용 분석에서 다른 범위로 전환합니다.
3. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시됩니다. App Service라는 레이블이 붙은 차트에서 영역을 선택합니다.

처음으로 비용 분석을 열 경우 실제 월별 비용이 표시됩니다. 다음은 월별 사용의 모든 비용을 보여 주는 예입니다.

![구독에 대한 누적 비용을 보여 주는 예제](media/overview-manage-costs/all-costs.png)

App Service와 같은 단일 서비스로 비용 범위를 좁히려면 **필터 추가** 를 선택한 다음 **서비스 이름** 을 선택합니다. 그런 다음 **App Service** 를 선택합니다.

App Service에 대한 비용만을 보여 주는 예제는 다음과 같습니다.

![ServiceName에 대한 누적 비용을 보여 주는 예제](media/overview-manage-costs/service-specific-costs.png)

앞의 예제에서 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치) 및 리소스 그룹별 App Service 비용 또한 표시됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

## <a name="create-budgets"></a>예산 만들기

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. 예산 및 경고는 Azure 구독 및 리소스 그룹에 대해 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터는 추가 비용이 드는 새 리소스를 실수로 만들지 않도록 도움을 줍니다. 예산을 만들 때 사용할 수 있는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 비용에 대한 데이터 분석이 필요한 경우나 다른 사용자가 비용에 대한 더 많은 데이터 분석을 수행해야 하는 경우에 유용합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 세트를 검색하려면 비용 데이터를 내보내는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- Azure Storage에서 가격 책정을 사용하는 방법에 대한 자세한 정보. [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.
- [Azure Cost Management를 사용해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에 대해 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->