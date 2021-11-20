---
title: 비용 계획 및 관리
titleSuffix: Azure Cognitive Search
description: Cognitive Search 서비스를 실행할 때 청구 가능한 이벤트, 청구 모델 및 비용 제어를 위한 팁에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/19/2021
ms.openlocfilehash: b9aea69ac9dbf2673ff2037e56de582f094ff7cc
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132864105"
---
# <a name="plan-and-manage-costs-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스의 비용 계획 및 관리

이 문서에서는 Azure Cognitive Search의 청구 모델 및 청구 가능 이벤트에 대해 설명 하 고 비용을 관리 하는 방향을 제공 합니다.

첫 번째 단계로 Azure 가격 계산기를 사용 하 여 기준 비용을 예측 합니다. 또는 서비스를 만들 때 [가격 책정 계층 선택](search-create-service-portal.md#choose-a-pricing-tier) 페이지에서 예상 비용 및 계층 비교를 찾을 수도 있습니다.

Azure는 서비스 경계를 넘어 포괄 비용 모니터링 및 예산 설정 및 경고 정의 기능을 제공 하는 기본 제공 비용 관리 기능을 제공 합니다. 검색 서비스를 실행 하는 비용은 용량 및 사용 하는 기능에 따라 달라 집니다. 검색 서비스를 만든 후에는 필요한 만큼만 요금을 지불할 수 있도록 용량을 최적화 합니다. 

<a name="billable-events"></a>

## <a name="understand-the-billing-model"></a>청구 모델 이해

Azure Cognitive Search는 Azure 인프라에서 실행 되며 새 리소스를 배포할 때 비용이 발생 합니다. 다른 추가 인프라 비용이 발생할 수도 있다는 점을 이해하는 것이 중요합니다.

### <a name="how-youre-charged-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 요금 청구 방법

검색 리소스를 만들거나 사용 하는 경우 다음 미터에 대 한 요금이 청구 됩니다.

+ 검색 서비스의 [가격 책정 계층](search-sku-tier.md) 을 기준으로 시간에 비례하여 청구 됩니다.

+ 요금 청구는 서비스에 할당 된 SU (검색 단위)의 수에 따라 적용 됩니다. 검색 단위는 [용량 단위](search-capacity-planning.md)입니다. Total SU는 서비스에서 사용 하는 복제본 및 파티션 (R x P = SU)의 곱입니다.

요금은 [AI (보강](cognitive-search-concept-intro.md)), [의미 체계 검색](semantic-search-overview.md), [개인 끝점](service-create-private-endpoint.md)등의 프리미엄 기능을 실행 하는 비용 (용량)을 기반으로 합니다. 프리미엄 기능과 관련 된 미터는 다음 표에 나와 있습니다.

| 미터 | 단위 |
|-------|------|
| 인덱서 사용 | 1000 API 호출 당 |
| 이미지 추출 (AI 보강) <sup>1, 2</sup> | 1000 이미지를 기준으로 합니다. [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/#pricing)를 참조 하세요. |
| 사용자 지정 엔터티 추출 (AI 보강) <sup>1</sup> | 1000 텍스트 레코드를 기준으로 합니다. [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/#pricing) |
| 사용자 지정 엔터티 조회 기술 (AI 보강) <sup>1</sup> | 1000 텍스트 레코드를 기준으로 합니다. [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/#pricing) |
| 기본 제공 기술 (AI 보강) <sup>1</sup> | Cognitive Services를 직접 호출 하 여 작업을 수행한 것과 동일한 속도로 청구 되는 트랜잭션 수입니다. 인덱서당 하루에 20개의 문서를 무료로 처리할 수 있습니다. 더 크거나 더 자주 수행 되는 작업에는 다중 리소스 Cognitive Services 키가 필요 합니다. |
| 의미 체계 검색 <sup>1</sup> | 점진적 속도로 청구 되는 "queryType = 의미 체계"의 쿼리 수입니다. [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/#pricing)를 참조 하세요. |
| 전용 끝점 <sup>1</sup> | 끝점이 존재 하는 한 요금이 청구 되 고 대역폭 요금이 청구 됩니다. |

<sup>1</sup> 은 기능을 사용 하거나 사용 하도록 설정 하는 경우에만 적용 됩니다.

<sup>2</sup> [인덱서 구성](/rest/api/searchservice/create-indexer#indexer-parameters)에서 "imageaction"은 이미지 추출을 트리거하는 매개 변수입니다. "ImageAction"이 "none" (기본값)으로 설정 된 경우 이미지 추출에 대 한 요금이 청구 되지 않습니다. 

[서비스 제한은](search-limits-quotas-capacity.md) 각 계층에서 적용 되기는 하지만 쿼리 수, 쿼리 응답 또는 문서 수집에 대 한 미터는 없습니다.

데이터 트래픽으로 인해 네트워킹 비용이 발생할 수도 있습니다. [대역폭 가격 책정](https://azure.microsoft.com/pricing/details/bandwidth/)을 참조하세요.

여러 프리미엄 기능 ([기술 자료 저장소](knowledge-store-concept-intro.md), [디버그 세션](cognitive-search-debug-session.md), [보강 cache (미리 보기)](cognitive-search-incremental-indexing-conceptual.md))은 Azure Storage에 종속 되어 있습니다. 이 경우에는 Azure Storage에 대 한 단위가 적용 되며 이러한 기능을 사용 하는 데 관련 된 저장소 비용은 Azure Storage 청구서에 포함 됩니다.

[고객 관리 키는](search-security-manage-encryption-keys.md) 중요 한 콘텐츠의 이중 암호화를 제공 합니다. 이 기능에는 청구 가능 [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))가 필요 합니다.

기술력과에는 [청구 가능한 기본 제공 기술](cognitive-search-predefined-skills.md), 청구 불가능 기본 제공 유틸리티 기술 및 사용자 지정 기술이 포함 될 수 있습니다. 청구할 수 없는 유틸리티 기술에는 조건부, 쉐이퍼, 텍스트 병합, 텍스트 분할 등이 있습니다. 이를 사용 하는 경우 요금 청구에 영향을 주지 않으며 Cognitive Services 키 요구 사항이 없으며 20 개의 문서 제한도 없습니다. 

사용자 지정 기술은 사용자가 제공하는 기능입니다. 사용자 지정 기술 사용 비용은 사용자 지정 코드가 다른 미터링 서비스 호출 여부에 따라 전적으로 달라집니다.  Cognitive Services 키 요구 사항은 없으며 사용자 지정 기술에 대한 20가지 문서 제한이 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

비용 관리는 Azure 인프라에 기본 제공되어 있습니다. 비용, 도구 및 API 추적에 대한 자세한 내용은 [청구 및 비용 관리](../cost-management-billing/cost-management-billing-overview.md)를 참조하세요.

## <a name="minimize-costs"></a>비용 최소화

Azure Cognitive Search 솔루션의 비용을 최소화 하려면 다음 지침을 따르세요.

1. 가능 하면 대역폭 요금을 최소화 하거나 제거 하기 위해 동일한 지역에 있거나 가능한 적은 수의 지역에 모든 리소스를 만듭니다.

1. 인덱싱 등의 리소스를 많이 사용 하는 작업을 [확장](search-capacity-planning.md) 한 다음 일반 쿼리 워크 로드에 대해 다시 조정 합니다. 워크 로드에 대 한 예측 가능한 패턴이 있는 경우 예상 된 볼륨과 일치 하도록 크기를 동기화 할 수 있습니다 .이를 자동화 하는 코드를 작성 해야 합니다.

   검색 솔루션의 비용을 예측 하는 경우 가격 책정 및 용량이 선형이 아닌 것을 염두에 두어야 합니다 (두 배의 용량은 동일한 계층의 비용을 두 배로 큼). 또한 어느 시점에서 더 높은 계층으로 전환하면 거의 동일한 가격대에서 신속하고 더 나은 성능을 제공할 수 있습니다. 자세한 내용 및 예제는 [표준 S2 계층으로 업그레이드](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier)를 참조하세요.

1. 요청 및 응답이 데이터 센터 경계 내에 유지 되도록 프런트 엔드 응용 프로그램에 대해 [Azure 웹 앱](../app-service/overview.md) 을 고려 합니다.

1. [AI 보강](cognitive-search-concept-intro.md)를 사용 하는 경우 blob 저장소에 대 한 추가 요금이 있지만 [보강 캐싱을](cognitive-search-incremental-indexing-conceptual.md)사용 하도록 설정 하면 누적 비용이 감소 합니다.

## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대한 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터는 추가 비용이 드는 새 리소스를 실수로 만들지 않도록 도움을 줍니다. 예산을 만들 때 사용할 수 있는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 비용에 대한 데이터 분석이 필요한 경우나 다른 사용자가 비용에 대한 더 많은 데이터 분석을 수행해야 하는 경우에 유용합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 세트를 검색하려면 비용 데이터를 내보내는 것이 좋습니다.

## <a name="faq"></a>FAQ

**검색 서비스를 일시적으로 종료 하 여 비용을 절감할 수 있나요?**

검색은 연속 서비스로 실행 됩니다. 전용 리소스는 항시 작동하며, 서비스 수명 동안 독점적으로 사용할 수 있도록 할당됩니다. 요금 청구를 완전히 중지 하려면 서비스를 삭제 해야 합니다. 서비스 삭제는 영구적이며 연결된 데이터도 삭제됩니다.

**기존 검색 서비스의 청구 요금 (계층)을 변경할 수 있나요?**

현재 위치의 업그레이드 또는 다운 그레이드는 지원 되지 않습니다. 서비스 계층을 변경 하려면 원하는 계층에서 새 서비스를 프로 비전 해야 합니다.

## <a name="next-steps"></a>다음 단계

+ Azure Cognitive Search에 대 한 가격 책정 방식에 대해 자세히 알아보세요. [Azure Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)를 참조 하세요.
+ [복제본 및 파티션에](search-sku-tier.md)대해 자세히 알아보세요.
+ [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
+ [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
+ [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
+ [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.
