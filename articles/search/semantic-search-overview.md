---
title: 의미 체계 검색
titleSuffix: Azure Cognitive Search
description: Cognitive Search에서 Bing의 딥 러닝 의미 체계 검색 모델을 사용하여 검색 결과를 보다 직관적으로 만드는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.custom: references_regions
ms.openlocfilehash: 18ed79768f1a803c4565426de67f6cde35a0a1fe
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577995"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 검색

> [!IMPORTANT]
> 의미 체계 검색은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 퍼블릭 미리 보기로 제공됩니다. Azure Portal, 미리 보기 REST API, 베타 SDK를 통해 사용할 수 있습니다. 이러한 기능에는 비용이 청구될 수 있습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요.

의미 체계 검색은 검색 결과에 의미 체계 관련성과 언어 이해를 제공하는 쿼리 관련 기능 모음입니다. 이 문서는 의미 체계 검색을 위한 개략적인 소개이며 각 기능에 대한 설명과 이러한 기능이 집합적으로 작동하는 방식을 설명합니다. 포함된 비디오는 기술에 대해 설명하고, 최종 섹션에서는 가용성 및 가격 책정에 대해 설명합니다.

의미 체계 검색은 프리미엄 기능입니다. 이 문서를 백그라운드에서 수행하는 것이 좋지만 시작하려면 다음 단계를 수행합니다.

> [!div class="checklist"]
> * [지역 및 서비스 계층 요구 사항을 확인합니다](#availability-and-pricing).
> * [미리 보기 프로그램에 가입합니다](https://aka.ms/SemanticSearchPreviewSignup). 요청을 처리하는 데 영업일 기준 최대 2일까지 걸릴 수 있습니다.
> * 승인되면 [의미 체계 캡션과 강조 표시를 반환](semantic-how-to-query-request.md)하는 쿼리를 만들거나 수정합니다.
> * 몇 가지 추가 쿼리 속성을 추가하여 [의미 체계 대답](semantic-answers.md)도 반환합니다.
> * 필요에 따라 [맞춤법 검사](speller-how-to-add.md)를 호출하여 정밀도와 재현율을 최대화합니다.

## <a name="what-is-semantic-search"></a>의미 체계 검색이란?

의미 체계 검색은 검색 결과의 품질을 향상하는 기능 모음입니다. 검색 서비스에서 사용하도록 설정된 경우 의미 체계 검색은 쿼리 실행 파이프라인을 두 가지 방식으로 확장합니다. 첫째, 초기 결과 집합에 보조 순위를 추가하여 의미 체계 측면에서 가장 관련성이 높은 결과를 목록의 맨 위로 올립니다. 둘째, 사용자의 검색 환경을 개선하기 위해 검색 페이지에서 렌더링할 수 있는 캡션과 답변을 응답에서 추출하여 반환합니다.

| 기능 | Description |
|---------|-------------|
| [의미 체계 다시 순위 지정](semantic-ranking.md) | 는 컨텍스트 또는 의미 체계 의미를 사용 하 여 기존 결과에 대해 새로운 관련성 점수를 계산 합니다. |
| [의미 체계 캡션 및 강조 표시](semantic-how-to-query-request.md) | 콘텐츠를 가장 잘 요약 한 문서에서 문장을 추출 하 여 쉽게 검색할 수 있는 주요 통로를 강조 합니다. 결과 페이지에서 개별 콘텐츠 필드가 너무 조밀한 경우 결과를 요약하는 캡션이 유용합니다. 강조 표시된 텍스트는 사용자가 일치 항목으로 간주되는 이유를 빠르게 확인할 수 있도록 가장 관련성이 높은 용어와 구를 승격시킵니다. |
| [의미 체계 답변](semantic-answers.md) | 의미 체계 쿼리에서 반환되는 선택적 및 추가 하위 구조입니다. 질문처럼 보이는 쿼리에 직접 답변을 제공합니다. 문서에는 응답의 특징을 가진 텍스트가 있어야 합니다. |
| [맞춤법 검사](speller-how-to-add.md) | 쿼리 용어가 검색 엔진에 도달하기 전에 오타를 수정합니다. |

## <a name="how-semantic-ranking-works"></a>의미 체계 순위 지정의 작동 방식

*의미 체계 순위 지정* 은 용어 간의 컨텍스트 및 관련성을 검색하여 쿼리에 더 적합한 일치 항목을 승격합니다. 언어 이해는 콘텐츠 내에서 요약 또는 *캡션* 및 *답변* 을 찾고 응답에 포함합니다. 이를 검색 결과 페이지에서 렌더링하여 보다 생산적인 검색 환경을 제공할 수 있습니다.

요약 및 순위 지정에는 미리 학습된 최신 모델이 사용됩니다. 사용자가 검색에서 필요로 하는 빠른 성능을 유지하기 위해 의미 체계 요약 및 순위 지정은 [기본 유사성 점수 매기기 알고리즘](index-similarity-and-scoring.md#similarity-ranking-algorithms)에 따라 점수가 매겨진 상위 50개의 결과에만 적용됩니다. 이러한 결과를 문서 모음으로 사용하면 의미 체계 순위 지정이 일치의 의미 체계 강도에 따라 결과의 점수를 다시 지정합니다.

기본 기술은 Bing 및 Microsoft Research에서 추가 기능으로 Cognitive Search 인프라에 통합되어 있습니다. 의미 체계 검색을 지원하는 연구 및 AI 투자에 대한 자세한 내용은 [Bing의 AI가 Azure Cognitive Search를 지원하는 방법(Microsoft Research 블로그)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)을 참조하세요.

다음 동영상에서는 기능의 개요를 제공합니다.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

### <a name="order-of-operations"></a>작업 순서

의미 체계 검색의 구성 요소는 기존 쿼리 실행 파이프라인을 양방향으로 확장합니다. 맞춤법 수정 기능을 사용하는 경우 [맞춤법 검사기](speller-how-to-add.md)는 용어가 검색 엔진에 도달하기 전에 쿼리 시작에서 오타를 수정합니다.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="쿼리 실행의 의미 체계 구성 요소" border="true":::

쿼리 실행은 반전된 인덱스에 대한 용어 구문 분석, 분석 및 검색을 통해 일반적인 방식으로 진행됩니다. 엔진은 토큰 일치를 사용하여 문서를 검색하고 [기본 유사성 점수 매기기 알고리즘](index-similarity-and-scoring.md#similarity-ranking-algorithms)을 사용하여 결과를 채점합니다. 점수는 인덱스에서 쿼리 용어와 일치하는 용어 간의 언어적 유사성을 기준으로 계산됩니다. 정의한 경우 점수 매기기 프로필도 이 단계에서 적용됩니다. 그런 다음, 결과가 의미 체계 검색 하위 시스템에 전달됩니다.

준비 단계에서 초기 결과 집합에서 반환된 문서 모음은 각 문서를 요약하는 구절을 찾기 위해 문장과 단락 수준에서 분석됩니다. 키워드 검색과 달리 이 단계에서는 머신 읽기와 이해력을 사용하여 콘텐츠를 평가합니다. 이 콘텐츠 처리 단계를 통해 의미 체계 쿼리는 [캡션](semantic-how-to-query-request.md) 및 [답변](semantic-answers.md)을 반환합니다. 이를 공식화하기 위해 의미 체계 검색에서는 언어 표현을 사용하여 결과를 가장 잘 요약하는 주요 구절을 추출하고 강조 표시합니다. 검색 쿼리가 질문이고 답변이 요청된 경우 응답에는 검색 쿼리로 표현되는 질문에 가장 잘 대답하는 텍스트 구절이 포함됩니다. 

캡션과 답변 모두에 대해 기존 텍스트는 공식화에서 사용됩니다. 의미 체계 모델은 사용 가능한 콘텐츠에서 새로운 문장이나 구를 작성하지 않으며 새로운 결론에 도달할 수 있는 논리를 적용하지 않습니다. 즉, 시스템은 아직 존재하지 않는 콘텐츠는 반환하지 않습니다.

그런 다음, 쿼리 용어의 [개념적 유사성](semantic-ranking.md)을 기준으로 결과는 다시 채점됩니다.

쿼리에서 의미 체계 기능을 사용하려면 [검색 요청](semantic-how-to-query-request.md)을 약간만 수정해야 하지만 추가 구성이나 인덱스 다시 작성은 필요하지 않습니다.

## <a name="semantic-capabilities-and-limitations"></a>의미 체계 기능 및 제한 사항

의미 체계 검색은 최신 기술이므로 수행할 수 있는 작업과 수행할 수 없는 작업에 대한 기대치를 설정하는 것이 중요합니다. 다음과 같은 방법으로 검색의 품질을 향상 시킬 수 있습니다.

* 원래 쿼리의 의도에 대 한 의미 체계에 근접 한 일치 항목을 승격 합니다.

* 검색 결과 페이지에서 렌더링할 수 있는 캡션으로 사용 될 수 있는 각 결과의 문자열을 찾습니다.

모음 전체에 대해 쿼리를 다시 실행 하 여 의미상 관련 된 결과를 찾을 수 없습니다. 의미 체계 검색은 기본 순위 알고리즘에 따라 점수가 매겨진 상위 50 결과로 구성 된 *기존* 결과 집합의 순위를 다시 지정 합니다. 또한 의미 체계 검색은 새 정보나 문자열을 만들 수 없습니다. 캡션과 답변은 내용에서 약어로 추출 되므로 결과가 대답 텍스트를 포함 하지 않는 경우 언어 모델은 해당 텍스트를 생성 하지 않습니다.

의미 체계 검색은 모든 시나리오에서 유용 하지 않지만 특정 콘텐츠가 기능에서 크게 이점을 누릴 수 있습니다. 의미 체계 검색의 언어 모델은 정보가 풍부한 산문 구조의 검색 가능한 콘텐츠에서 가장 효과적입니다. 설명 콘텐츠를 포함 하는 기술 자료, 온라인 설명서 또는 문서에서는 의미 체계 검색 기능에서 가장 큰 이점을 볼 것입니다.

## <a name="availability-and-pricing"></a>가용성 및 가격 책정

의미 체계 검색은 [등록 가입](https://aka.ms/SemanticSearchPreviewSignup)을 통해 사용할 수 있습니다. 의미 체계 검색과 맞춤법 검사를 위해 한 번만 등록하면 됩니다.

| 기능 | 계층 | 지역 | 등록 | 가격 책정 |
|---------|------|--------|---------------------|-------------------|
| 의미 체계 검색 (순위, 캡션, 하이라이트, 답변) | 표준 계층(S1, S2, S3) | 미국 중북부, 미국 서부, 미국 서부 2, 미국 동부 2, 북유럽, 서유럽 | 필수 | [Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)  |
| 맞춤법 검사 | 기본<sup>1</sup> 이상  | 모두 | 없음 | 없음(무료) |

<sup>1</sup> 공유 (무료) 검색 서비스의 프로 비전 메커니즘과 수명으로 인해 적은 수의 서비스에서 무료 계층에 대 한 맞춤법 검사를 수행 하는 경우가 발생 합니다. 그러나 무료 계층 서비스에 대 한 맞춤법 검사 가용성이 보장 되지 않으며 예상 하지 않아야 합니다.

의미 체계 검색에 대 한 요금은 쿼리 요청에 "queryType = 의미 체계"가 포함 되 고 검색 문자열이 비어 있지 않은 경우 (예: "levied의 애완 동물 호텔 호텔") 검색 문자열이 비어 있으면 ("search = *") queryType가 "의미 체계"로 설정 된 경우에도 요금이 부과 되지 않습니다.

## <a name="disable-semantic-search"></a>의미 체계 검색 사용 안 함

실수로 발생하는 사용량 및 요금에 대한 전체 보호를 위해 검색 서비스에서 서비스 만들기 또는 업데이트 API를 사용하여 [의미 체계 검색을 사용하지 않도록 설정할](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch)  수 있습니다. 기능을 사용하지 않도록 설정하면 의미 체계 쿼리 유형을 포함하는 모든 요청이 거부됩니다.

* Management REST API 버전 2021-04-01-Preview에서 이 옵션을 제공합니다.

* 기능을 사용하지 않도록 설정하려면 소유자 또는 기여자 권한이 필요합니다.

```http
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "semanticSearch": "disabled"
      }
    }
```

의미 체계 검색을 다시 사용하도록 설정하려면 위의 요청을 다시 실행하여 "semanticSearch"를 "free"(기본값) 또는 "표준"으로 설정합니다.

> [!TIP]
> 관리 REST API 호출은 Azure Active Directory 통해 인증됩니다. 보안 원칙 및 요청 설정에 대한 지침은 [Postman을 통해 Azure REST API(2021)라는](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)블로그 게시물을 참조하세요. 이전 예제는 블로그 게시물에 제공된 지침 및 Postman 컬렉션을 사용하여 테스트되었습니다.

## <a name="next-steps"></a>다음 단계

이전 섹션에서 설명한 계층 및 지역 요구 사항을 충족하는 검색 서비스에 대한 미리 보기를 [가입](https://aka.ms/SemanticSearchPreviewSignup)합니다.

요청을 처리하는 데 영업일 기준 최대 2일까지 걸릴 수 있습니다. 서비스가 준비되면 [의미 체계 쿼리를 만들어](semantic-how-to-query-request.md) 콘텐츠에 대한 서비스 성능을 평가합니다.
