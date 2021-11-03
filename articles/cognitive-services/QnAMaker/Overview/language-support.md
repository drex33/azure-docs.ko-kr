---
title: 언어 지원 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기술 자료에 대한 QnA Maker에서 지원되는 자연 언어인 문화권의 목록입니다. 동일한 기술 자료에서 언어를 혼합하지 마세요.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.custom: ignite-fall-2021
ms.openlocfilehash: 36f7c08cec7375d8b8a78519752c572357e6525a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073532"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker 리소스 및 기술 자료에 대한 언어 지원

이 문서에서는 QnA Maker 리소스 및 기술 자료에 대한 언어 지원 옵션을 설명합니다. 

[!INCLUDE [Custom question answering](../includes/new-version.md)]

리소스의 첫 번째 기술 자료를 만들 때 서비스의 언어가 선택됩니다. 리소스의 모든 추가 기술 자료는 동일한 언어로 되어 있어야 합니다. 

언어는 QnA Maker가 사용자 쿼리에 대한 응답으로 제공하는 결과의 관련성을 결정합니다. QnA Maker 리소스 및 해당 리소스 내의 모든 기술 자료는 단일 언어를 지원합니다. 쿼리에 대한 최상의 응답 결과를 제공하려면 단일 언어가 필요합니다.

## <a name="single-language-per-resource"></a>리소스당 단일 언어

다음을 살펴보세요.

* QnA Maker 서비스 및 모든 기술 자료는 한 가지 언어만 지원합니다.
* 언어는 서비스의 첫 번째 기술 자료 작성 시 명시적으로 설정됩니다.
* 언어는 기술 자료 작성 시 추가된 파일 및 URL에서 결정됩니다.
* 서비스의 다른 기술 자료에 대해서는 언어를 변경할 수 없습니다.
* 언어는 Cognitive Search 서비스(순위 #1)와 QnA Maker 서비스(순위 #2)에서 쿼리에 대한 최상의 응답을 생성하기 위해 사용됩니다.

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>하나의 QnA Maker 리소스에서 여러 언어 지원

이 기능은 현재 GA(일반 공급) 안정적인 릴리스에서는 지원되지 않습니다. 이 기능을 테스트하려면 관리 QnA Maker를 체크 아웃하세요. 

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>한 기술 자료에서 여러 언어 지원

여러 언어가 포함된 기술 자료 시스템을 지원해야 하는 경우 다음을 수행할 수 있습니다.

* 질문을 기술 자료로 보내기 전에 [Translator 서비스](../../translator/translator-info-overview.md)를 사용하여 질문을 단일 언어로 번역합니다. 이를 통해 단일 언어의 품질과 대체 질문 및 대답의 품질에 집중할 수 있습니다.
* 모든 언어에 대해 QnA Maker 리소스와 해당 리소스 내부에 기술 자료를 만듭니다. 이를 통해 각 언어에 대해 미묘하게 다른 대체 질문과 응답 텍스트를 관리할 수 있습니다. 이렇게 하면 훨씬 더 많은 유연성을 제공하지만 모든 언어에 대해 질문이나 대답이 변경될 경우 유지 관리 비용이 훨씬 더 많이 필요합니다.


## <a name="languages-supported"></a>지원되는 언어

다음 목록에는 QnA Maker 리소스에 대해 지원되는 언어가 나와 있습니다. 

| 언어 |
|--|
| 아랍어 |
| 아르메니아어 |
| 벵골어 |
| 바스크어 |
| 불가리아어 |
| 카탈로니아어 |
| 중국어 간체 |
| 중국어 번체 |
| 크로아티아어 |
| 체코어 |
| 덴마크어 |
| 네덜란드어 |
| 영어 |
| 에스토니아어 |
| 핀란드어 |
| 프랑스어 |
| 갈리시아어 |
| 독일어 |
| 그리스어 |
| 구자라트어 |
| 히브리어 |
| 힌디어 |
| 헝가리어 |
| 아이슬란드어 |
| 인도네시아어 |
| 아일랜드어 |
| 이탈리아어 |
| 일본어 |
| 칸나다어 |
| 한국어 |
| 라트비아어 |
| 리투아니아어 |
| 말라얄람어 |
| 말레이어 |
| 노르웨이어 |
| 폴란드어 |
| 포르투갈어 |
| 펀잡어 |
| 루마니아어 |
| 러시아어 |
| 세르비아어 키릴 자모 |
| 세르비아어 라틴어 |
| 슬로바키아어 |
| 슬로베니아어 |
| 스페인어 |
| 스웨덴어 |
| 타밀어 |
| 텔루구어 |
| 태국어 |
| 터키어 |
| 우크라이나어 |
| 우르두어 |
| 베트남어 |

## <a name="query-matching-and-relevance"></a>쿼리 일치 및 관련성
QnA Maker는 [Azure Cognitive Search 언어 분석기](/rest/api/searchservice/language-support)에 따라 다른 결과를 제공합니다.

Azure Cognitive Search 기능은 지원되는 언어에 동일하며 QnA Maker에는 Azure Search 결과의 상위에 있는 추가 랭커(ranker)가 포함되어 있습니다. 이 순위매기기 모델에서는 다음 언어로 된 몇 가지 특별한 의미 체계 및 단어 기반 기능을 사용합니다.

|추가 순위매기기 언어|
|--|
|중국어|
|체코어|
|네덜란드어|
|영어|
|프랑스어|
|독일어|
|헝가리어|
|이탈리아어|
|일본어|
|한국어|
|폴란드어|
|포르투갈어|
|스페인어|
|스웨덴어|

이 추가 순위 지정은 QnA Maker 순위매기기의 내부 작업입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [언어 선택](../index.yml)
