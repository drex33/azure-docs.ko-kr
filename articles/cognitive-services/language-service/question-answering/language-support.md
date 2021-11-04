---
title: 언어 지원 - 사용자 지정 질문 답변
titleSuffix: Azure Cognitive Services
description: 기술 자료의 사용자 지정 질문 답변에서 지원하는 문화권, 자연어 목록입니다. 동일한 기술 자료에서 언어를 혼합하지 마세요.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: reference
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: dbef13bdb39085c650a1fc5cedceb6c143eea4ad
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450118"
---
# <a name="language-support-for-custom-question-answering-and-knowledge-bases"></a>사용자 지정 질문 답변 및 기술 자료의 언어 지원

이 문서에서는 사용자 지정 질문에 답변할 수 있는 리소스 및 기술 자료에 대한 언어 지원 옵션을 설명합니다.

사용자 지정 질문 답변에서 여러 언어 지원을 허용하는 리소스에 새 프로젝트를 추가할 때마다 언어를 선택하거나 리소스에 대한 모든 향후 프로젝트에 적용되는 언어를 선택할 수 있습니다.

## <a name="supporting-multiple-languages-in-one-custom-question-answering-enabled-resource"></a>하나의 사용자 지정 질문에 답변할 수 있는 리소스에서 여러 언어 지원

> [!div class="mx-imgBorder"]
> ![다국어 기술 자료 선택](./media/language-support/choose-language.png)

* 서비스에서 첫 번째 프로젝트를 만들 때 새 프로젝트를 만들 때마다 언어를 선택할 수 있습니다. 한 서비스 내에서 다른 언어에 속하는 기술 자료 를 만들려면 이 옵션을 선택합니다.
* 첫 번째 기술 자료를 작성한 후에는 서비스에 대한 언어 설정 옵션을 수정할 수 없습니다.
* 기술 자료에 대해 여러 언어를 사용하는 경우 서비스에 대해 하나의 테스트 인덱스를 갖는 대신 기술 자료당 하나의 테스트 인덱스를 갖게 됩니다.

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>한 기술 자료에서 여러 언어 지원

여러 언어가 포함된 기술 자료 시스템을 지원해야 하는 경우 다음을 수행할 수 있습니다.

* 질문을 기술 자료로 보내기 전에 [Translator 서비스](../../translator/translator-info-overview.md)를 사용하여 질문을 단일 언어로 번역합니다. 이를 통해 단일 언어의 품질과 대체 질문 및 대답의 품질에 집중할 수 있습니다.
* 모든 언어에 대해 사용자 지정 질문 답변 사용 언어 리소스 및 해당 리소스 내에 기술 자료를 만듭니다. 이를 통해 각 언어에 대해 미묘하게 다른 대체 질문과 응답 텍스트를 관리할 수 있습니다. 이렇게 하면 훨씬 더 많은 유연성을 제공하지만 모든 언어에 대해 질문이나 대답이 변경될 경우 유지 관리 비용이 훨씬 더 많이 필요합니다.

## <a name="single-language-per-resource"></a>리소스당 단일 언어

**리소스와 연결된 모든 프로젝트에서 사용하는 언어를 설정하는 옵션을 선택하는** 경우 다음을 고려하세요. 
* 언어 리소스 및 모든 프로젝트/기술 자료는 하나의 언어만 지원합니다.
* 서비스의 첫 번째 프로젝트를 만들 때 언어가 명시적으로 설정됩니다.
* 리소스와 연결된 다른 프로젝트에는 언어를 변경할 수 없습니다.
* 언어는 Cognitive Search 서비스(ranker #1) 및 사용자 지정 질문 답변(순위 #2)에서 쿼리에 가장 적합한 대답을 생성하는 데 사용됩니다.

## <a name="languages-supported"></a>지원되는 언어

다음 목록에는 질문 응답 리소스에 대해 지원되는 언어가 포함되어 있습니다.

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
사용자 지정 질문 답변은 결과를 제공하기 위한 [Azure Cognitive Search 언어 분석기를](/rest/api/searchservice/language-support) 사용합니다.

Azure Cognitive Search 기능은 지원되는 언어와 동등하지만 질문에 대한 답변에는 Azure 검색 결과 위에 있는 추가 순위가 있습니다. 이 순위매기기 모델에서는 다음 언어로 된 몇 가지 특별한 의미 체계 및 단어 기반 기능을 사용합니다.

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

이 추가 순위는 사용자 지정 질문 응답의 ranker에 대 한 내부 작업입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [언어 선택](../index.yml)
