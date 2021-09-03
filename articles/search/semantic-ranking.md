---
title: 의미 체계 순위 지정
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 의미 체계 순위 지정 알고리즘이 어떻게 작동하는지 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 61fb6de73567b7a83e2c4db2010c717a160aebe0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473991"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 순위 지정

> [!IMPORTANT]
> 의미 체계 검색은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 퍼블릭 미리 보기로 제공됩니다. Azure Portal, 미리 보기 REST API, 베타 SDK를 통해 사용할 수 있습니다. 이러한 기능에는 비용이 청구될 수 있습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요.

의미 체계 순위 지정은 초기 결과 집합과 가장 일치하는 항목의 순위를 다시 지정하여 정밀도를 개선하는 쿼리 실행 파이프라인의 확장입니다. 의미 체계 순위 지정은 키워드와 일치하는 언어적 일치가 아닌 쿼리 용어의 의미 체계 의미를 포착하기 위해 학습된 대규모 변환기 기반 네트워크를 통해 지원됩니다. [기본적인 유사성 순위 알고리즘](index-ranking-similarity.md)과 다르게 의미 체계 순위매기기는 단어의 컨텍스트와 의미를 사용하여 관련성을 정합니다.

의미 체계 순위 지정에는 리소스와 시간이 모두 많이 듭니다. 쿼리 작업의 예상되는 대기 시간 내에 처리를 완료하기 위해 의미 체계 순위매기기에 대한 입력이 통합 및 감소되어 기본 요약 및 작성된 단계가 최대한 빠르게 완료될 수 있습니다.

## <a name="pre-processing"></a>전처리

관련성을 평가하기 전에 콘텐츠를 의미 체계 순위매기기를 통해 효율적으로 처리할 수 있는 관리 가능한 입력 수로 줄여야 합니다.

1. 첫째, 콘텐츠 감소는 키워드 검색에 사용되는 기본 [유사성 순위 알고리즘](index-ranking-similarity.md)에서 반환하는 초기 결과 집합으로 시작합니다. 모든 지정된 쿼리에 대해 결과는 최대 제한인 1,000개까지 여러 개의 문서로 구성될 수 있습니다. 많은 수의 일치 항목을 처리하는 데는 시간이 너무 오래 걸리기 때문에 상위 50개만 의미 체계 순위 지정을 진행합니다.

   문서 개수가 1개든 50개든 초기 결과 집합은 의미 체계 순위 지정에 대해 문서 모음의 첫 번째 반복을 구성합니다.

1. 다음으로 모음 전체에서 "searchFields"에 있는 모든 필드의 콘텐츠가 추출되어 하나의 긴 문자열로 결합됩니다.

1. 문자열을 통합한 후에는 너무 긴 문자열은 전체 길이가 요약 단계의 입력 요구 사항을 충족하도록 잘립니다.

   이와 같은 자르기가 적용되기 때문에 "searchFields"에 간결한 필드를 우선적으로 위치하도록 하여 문자열에 포함되도록 해야 합니다. 텍스트가 많은 필드가 있는 큰 문서를 사용하는 경우 최대 제한 이후의 모든 항목은 무시됩니다.

이제 각 문서는 하나의 긴 문자열로 표시됩니다.

문자열은 문자나 단어가 아니라 토큰으로 구성됩니다. 최대 토큰 수는 128개의 고유 토큰입니다. 추정을 위해 128개의 토큰이 128개 단어에 해당하는 문자열과 거의 동일하다고 가정할 수 있습니다. 

> [!NOTE]
>토큰화는 검색 가능한 필드에 대한 분석기 할당에 의해 부분적으로 결정됩니다. nGram이나 EdgeNGram과 같은 특수 분석기를 사용하는 경우 searchFields에서 해당 필드를 제외하는 것이 좋습니다. 문자열을 토큰화하는 방법에 대한 자세한 내용은 [Test Analyzer REST API](/rest/api/searchservice/test-analyzer)를 사용하여 분석기의 토큰 출력을 검토하면 됩니다.

## <a name="extraction"></a>추출

문자열을 줄인 후에는 컴퓨터 읽기 이해 및 언어 표현 모델을 통해 축소된 입력을 전달함으로써 문서를 가장 잘 요약하여 쿼리를 기준으로 하는 문장 및 구를 결정할 수 있습니다. 이 단계에서는 의미 체계 순위매기기로 이동할 문자열에서 콘텐츠를 추출합니다.

요약에 대한 입력은 준비 단계에서 각 문서에 대해 가져온 긴 문자열입니다. 각 문자열에서 요약 모델은 가장 대표적인 구절을 찾습니다. 또한 이 구절은 문서에 대한 [의미 체계 캡션](semantic-how-to-query-request.md)을 구성합니다. 각 캡션은 일반 텍스트 버전 및 강조 표시 버전으로 사용할 수 있으며 문서 당 200단어 미만으로 자주 사용됩니다.

"답변" 매개 변수를 지정하고, 쿼리가 질문으로 발생한 경우 및 질문에 대한 답을 제공하는 긴 문자열에서 구절을 찾을 수 있는 경우에도 [의미 체계 답변](semantic-answers.md)이 반환됩니다.

## <a name="semantic-ranking"></a>의미 체계 순위 지정

1. 캡션은 제공된 쿼리를 기준으로 개념 및 의미 체계 관련성에 대해 평가됩니다.

   다음 다이어그램에서는 "의미 체계 관련성"의 의미에 대한 설명을 제공합니다. "capital"이라는 용어는 재무, 법률, 지리, 문법의 컨텍스트에서 사용될 수 있습니다. 쿼리에 동일한 벡터 공간의 용어(예: "capital"과 "investment")가 포함되어 있는 경우 동일한 클러스터에 토큰도 포함하는 문서는 그렇지 않은 문서보다 점수가 높습니다.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="컨텍스트에 대한 벡터 표현" border="true":::

1. @search.rerankerScore는 캡션의 의미 체계 관련성에 따라 각 문서에 할당됩니다.

1. 모든 문서에 점수를 매긴 후에는 점수를 기준으로 내림차순으로 나열되고 쿼리 응답 페이로드에 포함됩니다. 페이로드에는 답변, 일반 텍스트 및 강조 표시된 캡션과 select 절에서 검색할 수 있거나 지정된 것으로 표시된 모든 필드가 포함됩니다.

## <a name="next-steps"></a>다음 단계

의미 체계 순위 지정은 특정 지역에서는 표준 계층에서 제공됩니다. 가용성과 가입에 대한 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요. 새 쿼리 형식을 사용하도록 설정하면 의미 체계 검색의 순위 및 응답 구조를 사용할 수 있습니다. 시작하려면 [의미 체계 쿼리를 만듭니다](semantic-how-to-query-request.md).

또는 기본 순위에 대한 다음 문서를 검토합니다. 의미 체계 순위 지정은 첫 번째 결과를 반환하기 위한 유사성 순위매기기에 따라 달라집니다. 쿼리 실행과 순위에 대해 알고 있으면 전체 프로세스가 어떻게 작동하는지 광범위하게 이해하게 됩니다.

+ [Azure Cognitive Search의 전체 텍스트 검색](search-lucene-query-architecture.md)
+ [Azure Cognitive Search의 유사성 및 점수 매기기](index-similarity-and-scoring.md)
+ [Azure Cognitive Search의 텍스트 처리용 분석기](search-analyzers.md)