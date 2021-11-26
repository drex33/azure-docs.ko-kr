---
title: QnA Maker 미리 작성된 API
titleSuffix: Azure Cognitive Services
description: 미리 작성된 API를 사용하여 텍스트에 대한 답변 얻기
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/05/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9b5ef28e48ed2bca1988e305a2a894179af4b07c
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133129180"
---
# <a name="prebuilt-question-answering"></a>미리 작성된 질문 답변

미리 작성된 질문 답변은 기술 자료를 만들거나, 질문 및 답변 쌍을 유지 관리하거나, 사용률이 낮은 인프라의 비용을 유발하지 않고 텍스트 구절에 대한 질문에 답변할 수 있는 기능을 사용자에게 제공합니다. 이 기능은 API로 제공되며, QnA Maker 또는 추가 스토리지에 대한 세부 정보를 몰라도 질문과 답변 요구 사항을 충족하는 데 사용할 수 있습니다.

> [!NOTE]
> 이 설명서는 최신 릴리스에는 적용되지 않습니다. 최신 릴리스에서 미리 작성한 API를 사용하는 방법에 대한 자세한 내용은 [미리 작성한 API 문서에 답변하는 질문을 참조하세요.](../../language-service/question-answering/how-to/prebuilt.md)

사용자 쿼리 및 텍스트/구절 블록을 지정하면 API가 답변 및 정확한 답변(있는 경우)을 반환합니다.

<a name="qna-entity"></a>


## <a name="example-usage-of-prebuilt-question-answering"></a>미리 작성된 질문 답변의 사용 예

지정된 질문에 대한 답변을 얻고자 하는 텍스트 블록이 하나 이상 있다고 가정하겠습니다. 기존에는 텍스트 블록 수만큼의 원본을 만들어야 했습니다. 그러나 이제 미리 작성된 질문 답변을 통해 기술 자료에서 콘텐츠 원본을 정의하지 않고도 텍스트 블록을 쿼리할 수 있습니다. 

미리 작성된 API를 사용할 수 있는 몇 가지 다른 시나리오는 다음과 같습니다.

* 텍스트를 강조 표시하고, 질문을 입력하고, 강조 표시된 텍스트에 대한 답변을 찾을 수 있게 해주는 최종 사용자용 전자책 리더 앱을 개발하는 경우 
* 사용자가 브라우저 페이지에 현재 표시되고 있는 콘텐츠에 대한 질문을 할 수 있게 해주는 브라우저 확장
* 사용자의 쿼리를 받고 사용자 쿼리와 가장 관련성이 높은 것으로 식별된 의료 콘텐츠에 따라 답변을 제공하는 의료용 봇 

샘플 요청의 예는 다음과 같습니다.

## <a name="sample-request"></a>샘플 요청
```
POST https://{Endpoint}/qnamaker/v5.0-preview.2/generateanswer
```

### <a name="sample-query-over-a-single-block-of-text"></a>단일 텍스트 블록에 대한 샘플 쿼리

요청 본문

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "documents": [
        {
            "text": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "id": "doc1"
        }
    ],
    "Language": "en"
}
```
## <a name="sample-response"></a>샘플 응답

위의 요청 본문에서는 단일 텍스트 블록에 대해 쿼리합니다. 위의 쿼리에 대해 수신되는 샘플 응답은 다음과 같습니다.

```json
{
    "answers": [
        {
            "answer": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "answerSpan": {
                "text": "two to four hours",
                "score": 0.0,
                "startIndex": 47,
                "endIndex": 64
            },
            "score": 0.9599020481109619,
            "id": "doc1",
            "answerStartIndex": 0,
            "answerEndIndex": 390
        },
        {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.06749606877565384,
            "id": "doc1",
            "answerStartIndex": 129,
            "answerEndIndex": 390
        },
        {
            "answer": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.011389964260160923,
            "id": "doc1",
            "answerStartIndex": 265,
            "answerEndIndex": 390
        }
    ]
}
```
API 응답의 일부로 여러 답변이 수신되는 것을 볼 수 있습니다. 각 답변에는 답변의 전반적인 관련성을 이해하는 데 도움이 되는 특정 신뢰도 점수가 있습니다. 사용자는 이 신뢰도 점수를 사용하여 쿼리에 대한 답변을 표시할 수 있습니다.

## <a name="prebuilt-api-limits"></a>미리 작성된 API 제한 사항 

미리 작성된 [API 제한 사항](../limits.md#prebuilt-question-answering-limits) 설명서 참조 

## <a name="prebuilt-api-reference"></a>미리 작성된 API 참조
API를 호출하는 데 필요한 입력 및 출력 매개 변수를 이해하려면 [미리 작성된 API 참조](/rest/api/cognitiveservices-qnamaker/qnamaker5.0preview2/prebuilt/generateanswer) 설명서를 확인하세요.