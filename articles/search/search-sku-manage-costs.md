---
title: 비용 예측
titleSuffix: Azure Cognitive Search
description: 청구 가능 이벤트, 가격 책정 모델 및 Cognitive Search 서비스 실행 비용 관리를 위한 팁을 알아보세요.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 1e46601858ece67efa2bca9543083da8a0e2cc79
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567476"
---
# <a name="estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스의 비용 예측 및 관리

이 문서에서는 Azure Cognitive Search 서비스를 실행하는 비용 관리를 위한 가격 책정 모델, 청구 가능 이벤트 및 팁에 대해 알아봅니다.

## <a name="pricing-model"></a>가격 책정 모델

Azure Cognitive Search의 확장성 아키텍처는 추가 쿼리 또는 인덱싱 파워의 필요 여부에 따라 용량을 변경하고 필요한 만큼 지불할 수 있도록 복제본과 파티션의 유연한 조합을 기반으로 합니다.

검색 서비스에서 사용하는 리소스 양과 서비스 계층에서 설정한 청구 비율을 곱하면 서비스 실행 비용이 결정됩니다. 비용과 용량은 긴밀하게 바인딩되어 있습니다. 비용을 예측할 때 인덱싱 및 쿼리 워크로드를 실행하는 데 필요한 용량을 이해하면 예상되는 비용을 잘 파악할 수 있습니다.

비용 청구를 위해 다음 두 가지 간단한 공식을 알아야 합니다.

| 수식 | 설명 |
|---------|-------------|
| **R x P = SU** | 사용된 복제본과 사용된 파티션을 곱한 수는 서비스에서 사용하는 *검색 단위*(SU)의 수와 같습니다. SU는 리소스 단위이며 파티션 또는 복제본이 될 수 있습니다. |
| **SU * 청구 비율 = 월간 지출** | 서비스를 프로비전한 계층의 청구 비율에 SU 수를 곱한 것이 전체 월간 청구의 주요 결정 요인입니다. 일부 기능 또는 워크로드는 다른 Azure 서비스에 대한 종속성을 가지며 구독 수준에서 솔루션 비용을 높일 수 있습니다. 아래의 청구 가능 이벤트 섹션은 청구에 추가할 수 있는 기능을 식별합니다. |

모든 서비스는 최소값으로 1SU(복제본 1개 x 파티션 1개)에서 시작합니다. 모든 서비스의 최대값은 36 SU입니다. 이 최대값은 여러 가지 방법으로 도달할 수 있습니다(예: 파티션 6개 x 복제본 6개 또는 파티션 3개 x 복제본 12개). 일반적으로 총 용량(예: 9개의 SU로 청구된 3-복제본, 3-파티션 서비스)보다 적은 용량을 사용합니다. 유효한 조합은 [파티션 및 복제본 조합](search-capacity-planning.md#chart) 차트를 참조하세요.

청구 비율은 SU당 시간별로 계산됩니다. 각 계층의 요금은 점차적으로 높아집니다. 계층이 높을수록 더 크고 더 빠른 파티션을 제공하므로, 해당 계층의 시간당 요금이 전반적으로 높아집니다. [가격 세부 정보](https://azure.microsoft.com/pricing/details/search/) 페이지에서 각 계층에 대한 요금을 확인할 수 있습니다.

대부분의 고객은 총 용량의 일부만 온라인 상태로 유지하고 나머지는 보류 상태로 둡니다. 청구의 경우 SU 공식으로 계산하여 온라인으로 가져오는 파티션 및 복제본 수에 따라 시간당 지불 비용이 결정됩니다. 

## <a name="billable-events"></a>청구 가능 이벤트

Azure Cognitional Search를 기반으로 구축된 솔루션으로 다음과 같은 방법으로 비용이 발생될 수 있습니다.

+ 최소 구성(한 개의 파티션 및 복제본)에서 기본 요금으로 24x7을 실행하는 [서비스 자체 비용](#service-costs). 이는 서비스 실행에 드는 고정 비용이라고 간주될 수 있습니다.

+ 용량(복제본 또는 파티션)을 추가하면, 비용이 청구 가능 비율 증분에 따라 증가합니다. 고가용성이 비즈니스 요구 사항인 경우 3개의 복제본이 필요합니다.

+ 대역폭 요금(아웃바운드 데이터 전송)

+ 특정 기능 또는 프리미엄 기능에 필요한 애드온 서비스:

  + 청구 가능한 기술을 사용하는 AI 강화([Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) 필요). 이미지 추출도 청구 가능합니다.
  + 지식 저장소([Azure Storage](https://azure.microsoft.com/pricing/details/storage/) 필요)
  + 증분 보강([Azure Storage](https://azure.microsoft.com/pricing/details/storage/) 필요, AI 보강에 적용)
  + 고객 관리 키 및 이중 암호화([Azure Key Vault ](https://azure.microsoft.com/pricing/details/key-vault/) 필요)
  + 비 인터넷 액세스 모델의 프라이빗 엔드포인트([Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/) 필요)
  + 의미 체계 검색은 표준 계층의 프리미엄 기능입니다(비용은 [Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/) 참조). 실수로 사용되는 것을 방지하기 위해 [의미 체계 검색을 사용하지 않도록 설정](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch)할 수 있습니다.

### <a name="service-costs"></a>서비스 비용

가상 머신 또는 요금이 부과되지 않도록 "일시 중지"할 수 있는 다른 리소스와 달리, Azure Cognitive Search 서비스는 항상 사용자의 전용 하드웨어에서 사용할 수 있습니다. 따라서 서비스 생성은 서비스 생성 시 시작되고 서비스 삭제 시 종료되는 청구 가능 이벤트입니다. 

최소 요금은 청구 가능한 비율의 첫 번째 검색 단위(복제본 1개 x 파티션 1개)입니다. 이 최소값은 이 구성보다 작은 구성에서는 서비스가 실행될 수 없기 때문에 서비스 수명 동안 고정됩니다. 

최소한으로, 복제본 및 파티션을 서로 독립적으로 추가할 수 있습니다. 복제본 및 파티션을 통해 용량이 증가하면 **(복제본 x 파티션 x 청구 비율)** 공식에 따라 청구 요금이 증가합니다. 여기서 청구 비율은 선택한 가격 책정 계층에 따라 달라집니다.

검색 솔루션의 비용을 예측하는 경우 가격과 용량은 선형적이지 않다는 점을 염두에 두세요(용량을 두 배로 늘리면 동일한 계층에서 비용이 두 배 이상 증가함). 또한 어느 시점에서 더 높은 계층으로 전환하면 거의 동일한 가격대에서 신속하고 더 나은 성능을 제공할 수 있습니다. 자세한 내용 및 예제는 [표준 S2 계층으로 업그레이드](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier)를 참조하세요.

### <a name="bandwidth-charges"></a>대역폭 요금

Azure 데이터 원본이 Azure Cognitive Search과 다른 지역에 있는 경우 [인덱서](search-indexer-overview.md)를 사용하면 청구에 영향을 미칠 수 있습니다. 이 시나리오에서는 Azure 데이터 원본에서 Azure Cognitive Search으로 아웃바운드 데이터를 이동하는 비용이 발생할 수 있습니다. 자세한 내용은 해당 Azure 데이터 플랫폼의 가격 책정 페이지를 참조하세요.

데이터와 동일한 지역에 Azure Cognitive Search 서비스를 생성하는 경우 데이터 송신 요금을 완전히 없앨 수 있습니다. [대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)의 몇 가지 정보는 다음과 같습니다.

+ 인바운드 데이터: Microsoft는 Azure의 모든 서비스에 대해 인바운드 데이터를 청구하지 않습니다. 

+ 아웃바운드 데이터: 아웃바운드 데이터는 쿼리 결과를 나타냅니다. Cognitive Search은 아웃바운드 데이터에 대해 요금을 부과하지 않지만, 서비스가 다른 지역에 있는 경우 Azure의 아웃바운드 요금은 가능합니다.

  이 요금은 실제로 해당 Azure Cognitive Search 청구에 포함되지 않습니다. 다른 지역 또는 비Azure 앱에 결과를 전송하는 경우 전체 청구에 반영되는 비용을 확인할 수 있기 때문에 여기에 설명되어 있습니다.

### <a name="ai-enrichment-with-cognitive-services"></a>Cognitive Search를 통한 AI 보강

청구 가능한 기술을 사용하는 [AI 보강](cognitive-search-concept-intro.md)의 경우, Azure Cognitive Search와 동일한 지역에서 종량제 프로세스에 대해 S0 가격 계층으로 [청구 가능 Azure Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)하도록 계획해야 합니다. Cognitive Services 연결과 관련된 고정 비용은 없습니다. 필요한 처리량에 따른 비용만 지불합니다.

| 작업 | 청구 영향 |
|-----------|----------------|
| 문서 크래킹, 문자 추출 | 무료 |
| 문서 크래킹, 이미지 추출 | 문서에서 추출한 이미지 수에 따라 요금이 청구됩니다. [인덱서 구성](/rest/api/searchservice/create-indexer#indexer-parameters)에서 **imageAction** 은 이미지 추출을 트리거하는 매개 변수입니다. **imageAction** 이 "없음"(기본값)으로 설정된 경우 이미지 추출에 대한 요금이 청구되지 않습니다. 이미지 추출 요금은 [가격 책정](https://azure.microsoft.com/pricing/details/search/) 페이지를 참조하세요. |
| Cognitive Services를 기반으로 하는 [기본 제공 기술](cognitive-search-predefined-skills.md) | Cognitive Services를 직접 사용하여 작업을 수행한 것과 동일한 비율로 요금이 청구됩니다. 인덱서당 하루에 20개의 문서를 무료로 처리할 수 있습니다. 더 크거나 빈번한 워크로드에는 키가 필요합니다. |
| 강화를 추가하지 않는 [기본 제공 기술](cognitive-search-predefined-skills.md) | 없음 청구할 수 없는 유틸리티 기술에는 조건부, 쉐이퍼, 텍스트 병합, 텍스트 분할 등이 있습니다. 청구 영향, Cognitive Services 키 요구 사항 및 20개 문서 제한이 없습니다. |
| 사용자 지정 기술 | 사용자 지정 기술은 사용자가 제공하는 기능입니다. 사용자 지정 기술 사용 비용은 사용자 지정 코드가 다른 미터링 서비스 호출 여부에 따라 전적으로 달라집니다.  Cognitive Services 키 요구 사항은 없으며 사용자 지정 기술에 대한 20가지 문서 제한이 없습니다.|
| [사용자 지정 엔터티 조회](cognitive-search-skill-custom-entity-lookup.md) | Azure Cognitive Search에서 측정합니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/search/#pricing) 페이지를 참조하세요. |

> [!TIP]
> [증분 보강(미리 보기)](cognitive-search-incremental-indexing-conceptual.md)은 기술 세트의 변경 내용에 영향을 받지 않는 보강을 캐싱하고 다시 사용하여 기술 세트 처리 비용을 낮춥니다. 캐싱에는 Azure Storage([가격 책정](/pricing/details/storage/blobs/) 참조)가 필요합니다. 그러나 기존 보강을 다시 사용할 수 있는 경우 기술 세트 실행의 누적 비용이 더 낮습니다.

## <a name="tips-for-managing-costs"></a>비용 관리를 위한 팁

다음 지침은 비용을 절감하거나 비용을 보다 효과적으로 관리하는 데 도움이 됩니다.

+ 대역폭 요금을 최소화하거나 제거하려면 동일한 지역 또는 가능한 적은 지역에 모든 리소스를 생성하세요.

+ 모든 서비스를 Azure Cognitive Search, Cognitive Search 및 솔루션에 사용되는 다른 Azure 서비스와 같은 하나의 리소스 그룹으로 통합합니다. Azure Portal에서 리소스 그룹을 찾고 **Cost Management** 명령을 사용하여 실제 지출과 예상 지출을 파악하세요.

+ 요청과 응답이 데이터 센터 경계 내에 유지되도록 프런트 엔드 애플리케이션의 Azure Web App을 고려하세요.

+ 인덱싱과 같은 리소스 집약적인 작업에 맞게 스케일업한 다음 일반 쿼리 워크로드에 맞게 재조정하세요. Azure Cognitional Search(파티션 1개와 복제본 1개로 구성된 1개의 SU)에 대한 최소 구성으로 시작한 다음 사용자 활동을 모니터링하여 더 많은 용량에 대한 필요성을 나타내는 사용 패턴을 식별하세요. 예측 가능한 패턴이 있는 경우 스케일과 작업을 동기화할 수 있습니다. 이러한 패턴을 자동화하려면 코드를 작성해야 합니다.

+ 비용 관리는 Azure 인프라에 기본 제공되어 있습니다. 비용, 도구 및 API 추적에 대한 자세한 내용은 [청구 및 비용 관리](../cost-management-billing/cost-management-billing-overview.md)를 참조하세요.

검색 서비스를 일시적으로 종료할 수 없습니다. 전용 리소스는 항시 작동하며, 서비스 수명 동안 독점적으로 사용할 수 있도록 할당됩니다. 서비스 삭제는 영구적이며 연결된 데이터도 삭제됩니다.

서비스 자체 측면에서 청구 요금을 절감할 수 있는 유일한 방법은 복제본 및 파티션을 허용 가능한 성능 및 [SLA 규정 준수](https://azure.microsoft.com/support/legal/sla/search/v1_0/)를 제공하는 수준으로 줄이거나 더 낮은 계층에서 서비스를 생성하는 것입니다(S1 시간당 요금이 S2 또는 S3 요금보다 낮음). 로드 프로젝션 하단에서 서비스를 프로비전한다고 가정하면 서비스를 초과하는 경우 두 번째 계층 서비스를 생성하고 두 번째 서비스에 인덱스를 재구성한 다음 첫 번째 서비스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

> [!div class="nextstepaction"]
> [Cost Management를 통한 비용 분석 시작](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)