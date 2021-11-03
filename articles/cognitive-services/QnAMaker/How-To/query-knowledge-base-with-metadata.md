---
title: 메타데이터를 사용하여 컨텍스트 필터링
titleSuffix: Azure Cognitive Services
description: QnA Maker는 QnA 쌍을 메타데이터별로 필터링합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: e38baa47d22ebb1fbdd5127cbd415afc3027d8a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016854"
---
# <a name="filter-responses-with-metadata"></a>메타데이터로 응답 필터링

QnA Maker를 사용하면 키 및 값 쌍의 형식으로 질문과 응답 쌍에 메타데이터를 추가할 수 있습니다. 그런 다음, 이 정보를 사용하여 사용자 쿼리에 대한 결과를 필터링하고 후속 대화에 사용할 수 있는 추가 정보를 저장할 수 있습니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>QnA 엔터티를 사용하여 질문 및 답변 저장

QnA Maker가 질문 및 답변 데이터를 저장하는 방법을 이해하는 것이 중요합니다. 다음 일러스트레이션은 QnA 엔터티를 보여줍니다.

![QnA 엔터티 그림](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA 엔터티마다 고유한 영구 ID가 있습니다. ID를 사용하여 특정 QnA 엔터티를 업데이트할 수 있습니다.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>메타데이터를 사용하여 사용자 지정 메타데이터 태그별로 답변 필터링

메타데이터를 추가하면 이러한 메타데이터 태그별로 답변을 필터링할 수 있습니다. **보기 옵션** 메뉴에서 메타데이터 열을 추가합니다. 메타데이터 쌍을 추가할 메타데이터 **+** 아이콘을 선택하여 기술 자료에 메타데이터를 추가합니다. 이 쌍은 하나의 키와 하나의 값으로 구성됩니다.

![메타데이터 추가 스크린샷](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>메타데이터 태그에 대한 strictFilters로 결과 필터링

"이 호텔은 언제 닫나요?"라는 사용자 질문을 고려해 보세요. 여기에는 레스토랑 "Paradise"에 대한 의도가 암시되어 있습니다.

결과는 "Paradise" 레스토랑에만 필요하므로 "Restaurant Name" 메타데이터에 대한 GenerateAnswer 호출의 필터를 설정할 수 있습니다. 다음 예제에서는 이러한 방법을 보여줍니다.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

## <a name="filter-by-source"></a>원본으로 필터링

기술 자료에 콘텐츠 원본이 여러 개 있고 결과를 특정 원본 세트로 제한하려는 경우 아래와 같이 예약된 키워드 `source_name_metadata`를 사용하여 이 작업을 수행할 수 있습니다.

```json
"strictFilters": [
    {
        "name": "category",
        "value": "api"
    },
   {
        "name": "source_name_metadata",
        "value": "boby_brown_docx"
    },
   {
        "name": "source_name_metadata",
        "value": "chitchat.tsv"
   }
]
```

---

### <a name="logical-and-by-default"></a>기본적으로 논리 AND

쿼리에서 여러 메타데이터 필터를 결합하려면 `strictFilters` 속성의 배열에 메타데이터 필터를 추가합니다. 기본적으로 값은 논리적으로 결합되어 있습니다(AND). 논리 조합은 모든 필터가 QnA 쌍과 일치해야 해당 쌍이 답변에 반환됩니다.

이는 `strictFiltersCompoundOperationType` 속성을 `AND` 값으로 사용하는 것과 같습니다.

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>strictFiltersCompoundOperationType 속성을 사용하는 논리 OR

여러 메타데이터 필터를 결합할 때 일치하는 필터 중 하나 또는 일부에만 관심이 있는 경우 `strictFiltersCompoundOperationType`의 값으로 `OR` 속성을 사용합니다.

이렇게 하면 모든 필터가 일치할 때 기술 자료에서 답변을 반환할 수 있지만 메타데이터가 없는 답변은 반환하지 않습니다.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>빠른 시작의 메타데이터 예제

메타데이터에 대한 QnA Maker 포털 빠른 시작에서 메타데이터에 대해 자세히 알아보세요.
* [제작 - QnA 쌍에 메타데이터 추가](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [쿼리 예측 - 메타데이터를 통한 응답 필터링](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>질문 및 답변 결과를 사용하여 대화 컨텍스트 유지

GenerateAnswer에 대한 응답에는 일치하는 질문 및 답변 쌍의 해당 메타데이터 정보가 포함됩니다. 이 정보를 클라이언트 애플리케이션에서 사용하여 이후 대화에서 사용할 이전 대화의 컨텍스트를 저장할 수 있습니다.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 분석](../How-to/get-analytics-knowledge-base.md)
