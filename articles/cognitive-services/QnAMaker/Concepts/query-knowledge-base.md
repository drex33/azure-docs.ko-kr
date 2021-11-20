---
title: 기술 자료 쿼리 - QnA Maker
description: 기술 자료가 게시되어야 합니다. 게시된 후에는 generateAnswer API를 사용하여 런타임 예측 엔드포인트에서 기술 자료가 쿼리됩니다.
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ac59e5ac3fad38f3c556772dacc9ca31bd9e498d
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132865443"
---
# <a name="query-the-knowledge-base-for-answers"></a>기술 자료에서 답변 쿼리

기술 자료가 게시되어야 합니다. 게시된 후에는 generateAnswer API를 사용하여 런타임 예측 엔드포인트에서 기술 자료가 쿼리됩니다. 쿼리에는 질문 텍스트 및 기타 설정이 포함되어 QnA Maker가 답변에 가장 적합한 일치 항목을 선택할 수 있습니다.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker에서 사용자 쿼리를 처리하여 가장 적합한 답변을 선택하는 방법

학습되고 [게시된](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker 기술 자료는 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)에서 봇 또는 다른 클라이언트 애플리케이션의 사용자 쿼리를 수신합니다. 다음 다이어그램에서는 사용자 쿼리를 수신하는 프로세스를 보여줍니다.

![사용자 쿼리에 대한 순위 지정 모델 프로세스](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Ranker 프로세스

프로세스는 다음 표에 설명되어 있습니다.

|단계|목적|
|--|--|
|1|클라이언트 애플리케이션이 사용자 쿼리를 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)로 보냅니다.|
|2|QnA Maker가 언어 감지, 맞춤법 검사기 및 단어 분리기를 사용하여 사용자 쿼리를 전처리합니다.|
|3|이러한 전처리는 최상의 검색 결과를 위해 사용자 쿼리를 변경하는 데 사용됩니다.|
|4|이 변경된 쿼리는 `top` 수의 결과를 수신하는 Azure Cognitive Search Index에 전송됩니다. 이러한 결과에 올바른 답변이 없는 경우 `top`의 값을 약간 늘립니다. 일반적으로 `top`에 대한 값이 10이면 쿼리의 90%에서 작동합니다. 이 단계에서 Azure Search는 [중지 단어](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)를 필터링합니다.|
|5|QnA Maker는 구문 및 의미 체계 기반 기능화를 사용하여 사용자 쿼리와 가져온 QnA 결과 간의 유사성을 결정합니다.|
|6|머신에서 학습한 Ranker 모델은 5단계에서 제공하는 다양한 기능을 사용하여 신뢰도 점수와 새 순위를 결정합니다.|
|7|새 결과는 순위대로 클라이언트 애플리케이션에 반환됩니다.|
|||

사용되는 기능에는 단어 수준 의미 체계, 모음의 용어 수준 중요도, 두 텍스트 문자열 간의 유사성과 관련성을 확인하기 위한 딥 러닝 의미 체계 모델이 포함되지만 이에 국한되지 않습니다.

## <a name="http-request-and-response-with-endpoint"></a>엔드포인트를 사용하여 HTTP 요청 및 응답

지식 기반을 게시할 때 서비스는 애플리케이션, 일반적으로 챗봇에 통합할 수 있는 REST 기반 HTTP 엔드포인트를 만듭니다.

### <a name="the-user-query-request-to-generate-an-answer"></a>답변을 생성하는 사용자 쿼리 요청

사용자 쿼리는 기술 자료에 대한 최종 사용자의 질문입니다(예: `How do I add a collaborator to my app?`). 쿼리가 자연어 형식이거나 질문을 나타내는 몇 개의 키워드인 경우도 있습니다(예: `help with collaborators`). 클라이언트 애플리케이션의 HTTP 요청에서 기술 자료로 쿼리를 보냅니다.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

[scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers), [strictFilters](../how-to/query-knowledge-base-with-metadata.md) 등의 속성을 설정하여 응답을 제어합니다.

[대화 컨텍스트](../how-to/query-knowledge-base-with-metadata.md)를 [멀티 턴 기능](../how-to/multi-turn.md)과 함께 사용하여 질문 및 답변을 구체화하는 대화를 유지하고 정확한 최종 답변을 찾을 수 있습니다.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>답변을 생성하는 호출의 응답

HTTP 응답은 지정된 사용자 쿼리에 가장 잘 일치하는 항목을 기반으로 기술 자료에서 검색된 답변입니다. 응답에는 답변 및 예측 점수가 포함됩니다. `top` 속성을 사용하여 둘 이상의 최고 답변을 요청하는 경우 각각 점수가 있는 두 개 이상의 최고 답변을 얻을 수 있습니다.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [신뢰도 점수](./confidence-score.md)
