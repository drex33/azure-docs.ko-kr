---
title: 비용 계획 및 관리
titleSuffix: Azure Cognitive Search
description: Cognitive Search 서비스를 실행할 때 청구 가능 이벤트, 청구 모델 및 비용 제어 팁에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: b668bedd03c6e9ef2a2c1fa8350013c5435078da
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808682"
---
# <a name="plan-and-manage-costs-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스의 비용 계획 및 관리

이 문서에서는 Azure Cognitive Search 청구 모델 및 청구 가능 이벤트를 설명하고 비용을 관리하는 지침을 제공합니다.

첫 번째 단계로, Azure 가격 계산기를 사용하여 기준 비용을 예측합니다. 또는 서비스를 만들 때 [가격 책정 계층 선택](search-create-service-portal.md#choose-a-pricing-tier) 페이지에서 예상 비용 및 계층 비교를 찾을 수도 있습니다.

Azure는 포괄적인 비용 모니터링과 예산을 설정하고 경고를 정의하는 기능을 제공하기 위해 서비스 경계를 넘지 않는 기본 제공 비용 관리를 제공합니다. 검색 서비스를 실행하는 비용은 용량 및 사용하는 기능에 따라 달라집니다. 검색 서비스를 만든 후 필요한 만큼만 지불되도록 용량을 최적화합니다. 

## <a name="understand-the-billing-model"></a>청구 모델 이해

용량은 청구의 주요 결정자입니다. Azure Cognitive Search 고정된 구성 요소가 있지만 확장성 아키텍처는 복제본과 파티션의 유연한 조합을 기반으로 하므로 더 많은 쿼리 또는 인덱싱 성능이 필요한지 여부에 따라 용량을 변경할 수 있습니다.

검색 서비스에서 사용하는 리소스 양에 서비스 계층에 설정된 청구 요율에 곱하면 서비스 실행의 기준 비용이 결정됩니다. 비용 청구를 위해 다음 두 가지 간단한 공식을 알아야 합니다.

| 수식 | 설명 |
|---------|-------------|
| **R x P = SU** | 사용된 복제본 수로 정의된 시스템 용량에 사용된 파티션 수를 곱한 값은 서비스에서 사용하는 *SU(검색 단위)* 수량과 같습니다. SU는 리소스 단위이며 파티션 또는 복제본이 될 수 있습니다. |
| **SU * 청구 비율 = 월간 지출** | 서비스를 프로비전한 계층의 청구 비율에 SU 수를 곱한 것이 전체 월간 청구의 주요 결정 요인입니다. 일부 기능 또는 워크로드는 다른 Azure 서비스에 대한 종속성을 가지며 구독 수준에서 솔루션 비용을 높일 수 있습니다. 아래의 청구 가능 이벤트 섹션은 청구에 추가할 수 있는 기능을 식별합니다. |

### <a name="su-minimum-and-maximum-values"></a>SU 최소값 및 최대값

모든 서비스는 최소로 하나의 SU(하나의 복제본에 하나의 파티션을 곱함)로 시작하며, 이는 서비스를 실행하는 고정 비용입니다.

모든 서비스의 최대 용량은 36US입니다. 이 최대값은 여러 가지 방법으로 도달할 수 있습니다(예: 파티션 6개 x 복제본 6개 또는 파티션 3개 x 복제본 12개). 일반적으로 총 용량(예: 9개의 SU로 청구된 3-복제본, 3-파티션 서비스)보다 적은 용량을 사용합니다. 유효한 조합은 [파티션 및 복제본 조합](search-capacity-planning.md#chart) 차트를 참조하세요.

### <a name="billing-rate"></a>청구 요금

청구 비율은 SU당 시간별로 계산됩니다. 각 계층의 요금은 점차적으로 높아집니다. 계층이 높을수록 파티션이 더 크고 빠르며, 이로써 해당 계층에 대한 전체적인 시간당 요금이 높아집니다. [가격 세부 정보](https://azure.microsoft.com/pricing/details/search/) 페이지에서 각 계층에 대한 요금을 확인할 수 있습니다.

대부분의 고객은 총 용량의 일부만 온라인 상태로 유지하고 나머지는 보류 상태로 둡니다. 청구의 경우 SU 수식으로 계산된 온라인으로 가져오는 파티션 및 복제본의 수가 시간별 속도를 결정합니다. 

## <a name="billable-events"></a>청구 가능 이벤트

다음 표에서는 Azure Cognitive Search 솔루션의 청구 가능 이벤트를 나열합니다.

| 이벤트 | Description |
|-------|-------------|
| 서비스 만들기 | 기본 요금으로 최소 구성(하나의 파티션 및 복제본)에서 24x7을 실행하는 서비스 자체의 고정 비용입니다. |
| [대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)  | 원격 지역의 리소스에서 콘텐츠를 추출하는 [인덱서](search-indexer-overview.md) 및 기술에 적용할 수 있습니다. 동일한 지역 데이터 액세스 또는 검색 원본으로 데이터 푸시에 대한 데이터 송신 요금은 없습니다. Cognitive Search 아웃바운드 쿼리 응답에 대한 미터가 없습니다. |
| 용량 추가 | 복제본 또는 파티션을 추가하는 것은 청구 가능 속도로 증분 증가합니다. 고가용성이 비즈니스 요구 사항인 경우 Azure Cognitive Search 대한 SLA(Service Level Agreement(서비스 수준 약정)) 요구 사항을 충족하기 위해 3개의 복제본이 필요합니다.|
| 프리미엄 기능 추가 | 일부 기능은 다른 청구 가능한 Azure 리소스에 대한 의존성이 있거나 더 큰 워크로드를 지원하기 위해 추가 인프라가 필요합니다. 다음 목록에서는 프리미엄 기능을 식별합니다. |

### <a name="add-on-services-required-for-premium-features"></a>프리미엄 기능에 필요한 추가 기능 서비스

| 기능 | Description |
|---------|-------------|
| [AI 보강](cognitive-search-concept-intro.md) | 청구 가능한 기술 [사용(Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)필요) 이미지 추출도 청구 가능합니다. 자세한 내용은 다음 섹션에 있습니다. |
| [지식 저장소](knowledge-store-concept-intro.md) | AI 보강의 출력을 저장합니다. 청구 가능한 [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)) 계정이 필요합니다. |
| [보강 캐시(미리 보기)](cognitive-search-incremental-indexing-conceptual.md) | AI 보강에 적용됩니다. 청구 가능한 [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)) 계정이 필요합니다. </br></br>캐싱을 사용하면 기술 기술의 변경 내용에 영향을 받지 않는 보강을 캐싱하고 다시 사용하여 기술셋 처리 비용을 크게 낮출 수 있습니다. 캐싱에는 청구 가능한 Azure Storage 필요하지만 기존 보강을 다시 사용할 수 있는 경우 일반적으로 기술 항목 실행의 누적 비용이 더 낮습니다.|
| [고객 관리형 키](search-security-manage-encryption-keys.md)| 중요한 콘텐츠의 이중 암호화를 제공합니다. 청구 가능 [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)필요합니다. |
| [프라이빗 엔드포인트](service-create-private-endpoint.md) | 인터넷이 없는 데이터 액세스 모델에 사용됩니다. [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/)가 필요합니다. |
| [의미 체계 검색](semantic-search-overview.md) |이는 표준 계층의 요금제 프리미엄 기능입니다(비용은 [Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/) 참조). 실수로 사용되는 것을 방지하기 위해 [의미 체계 검색을 사용하지 않도록 설정](semantic-search-overview.md#disable-semantic-search)할 수 있습니다.|

### <a name="drill-down-into-ai-enrichment-billing"></a>AI 보강 청구로 드릴다운

청구 가능한 기술을 사용하는 [AI 보강](cognitive-search-concept-intro.md)의 경우, Azure Cognitive Search와 동일한 지역에서 종량제 프로세스에 대해 S0 가격 계층으로 [청구 가능 Azure Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)하도록 계획해야 합니다. Cognitive Services 연결과 관련된 고정 비용은 없습니다. 필요한 처리량에 따른 비용만 지불합니다.

| 작업 | 청구 영향 |
|-----------|----------------|
| 문서 크래킹, 문자 추출 | 무료 |
| 문서 크래킹, 이미지 추출 | 문서에서 추출한 이미지 수에 따라 요금이 청구됩니다. [인덱서 구성](/rest/api/searchservice/create-indexer#indexer-parameters)에서 **imageAction** 은 이미지 추출을 트리거하는 매개 변수입니다. **imageAction** 이 "없음"(기본값)으로 설정된 경우 이미지 추출에 대한 요금이 청구되지 않습니다. 이미지 추출 요금은 [가격 책정](https://azure.microsoft.com/pricing/details/search/) 페이지를 참조하세요. |
| Cognitive Services를 기반으로 하는 [기본 제공 기술](cognitive-search-predefined-skills.md) | Cognitive Services를 직접 사용하여 작업을 수행한 것과 동일한 비율로 요금이 청구됩니다. 인덱서당 하루에 20개의 문서를 무료로 처리할 수 있습니다. 더 크거나 빈번한 워크로드에는 키가 필요합니다. |
| 강화를 추가하지 않는 [기본 제공 기술](cognitive-search-predefined-skills.md) | 없음 청구할 수 없는 유틸리티 기술에는 조건부, 쉐이퍼, 텍스트 병합, 텍스트 분할 등이 있습니다. 청구 영향, Cognitive Services 키 요구 사항 및 20개 문서 제한이 없습니다. |
| 사용자 지정 기술 | 사용자 지정 기술은 사용자가 제공하는 기능입니다. 사용자 지정 기술 사용 비용은 사용자 지정 코드가 다른 미터링 서비스 호출 여부에 따라 전적으로 달라집니다.  Cognitive Services 키 요구 사항은 없으며 사용자 지정 기술에 대한 20가지 문서 제한이 없습니다.|
| [사용자 지정 엔터티 조회](cognitive-search-skill-custom-entity-lookup.md) | Azure Cognitive Search에서 측정합니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/search/#pricing) 페이지를 참조하세요. |

## <a name="manage-costs"></a>비용 관리

비용 관리는 Azure 인프라에 기본 제공되어 있습니다. 비용, 도구 및 API 추적에 대한 자세한 내용은 [청구 및 비용 관리](../cost-management-billing/cost-management-billing-overview.md)를 참조하세요.

1. 대역폭 요금을 최소화하거나 제거하려면 동일한 지역 또는 가능한 적은 지역에 모든 리소스를 생성하세요.

1. 인덱싱과 같은 리소스 집약적인 작업에 맞게 스케일업한 다음 일반 쿼리 워크로드에 맞게 재조정하세요. 워크로드에 예측 가능한 패턴이 있는 경우 확장과 작업을 동기화할 수 있습니다(이를 자동화하려면 코드를 작성해야 합니다).

   검색 솔루션의 비용을 예상할 때 가격 책정 및 용량은 선형이 아닙니다(용량이 동일한 계층에서 두 배 이상 증가함). 또한 어느 시점에서 더 높은 계층으로 전환하면 거의 동일한 가격대에서 신속하고 더 나은 성능을 제공할 수 있습니다. 자세한 내용 및 예제는 [표준 S2 계층으로 업그레이드](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier)를 참조하세요.

1. 요청과 응답이 데이터 센터 경계 내에 유지되도록 프런트 엔드 애플리케이션의 Azure Web App을 고려하세요.

## <a name="faq"></a>FAQ

**비용을 절감하기 위해 검색 서비스를 일시적으로 종료할 수 있나요?**

검색은 연속 서비스로 실행됩니다. 전용 리소스는 항시 작동하며, 서비스 수명 동안 독점적으로 사용할 수 있도록 할당됩니다. 청구를 완전히 중지하려면 서비스를 삭제해야 합니다. 서비스 삭제는 영구적이며 연결된 데이터도 삭제됩니다.

**기존 검색 서비스의 청구 요금(계층)을 변경할 수 있나요?**

현재 위치 업그레이드 또는 다운그레이드는 지원되지 않습니다. 서비스 계층을 변경하려면 원하는 계층에서 새 서비스를 프로비전해야 합니다.

## <a name="next-steps"></a>다음 단계

+ 가격 책정이 Azure Cognitive Search 작동하는 방식에 대해 자세히 알아보세요. [Azure Cognitive Search 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/search/)참조하세요.
+ [복제본 및 파티션에](search-sku-tier.md)대해 자세히 알아봅니다.
+ [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
+ [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
+ [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
+ [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.
