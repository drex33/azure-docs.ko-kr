---
title: 미리 작성 한 API-질문과 대답
titleSuffix: Azure Cognitive Services
description: 미리 빌드된 질문 대답 API를 사용 하 여 프로젝트/기술 자료를 만들 필요 없이 질문에 대 한 답변을 확인 하 고 질문에 대 한 답변을 받으세요.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: 9fe4b80220ccb94e2b89bcdeaacf8cacac624ed0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132161080"
---
# <a name="prebuilt-api"></a>미리 작성 한 API

**미리** 작성 된 질문에 대답 하면 프로젝트/기술 자료를 만들거나, 질문 및 답변 쌍을 유지 관리 하거나, 미달 사용 인프라의 비용을 발생 시 키 지 않고도 텍스트의 흐름을 기반으로 질문에 대답할 수 있는 기능을 제공 합니다. 이 기능은 API로 제공 되며 질문 답변에 대 한 세부 정보를 몰라도 질문과 대답 요구를 충족 하는 데 사용할 수 있습니다.

사용자 쿼리 및 텍스트/구절 블록을 지정하면 API가 답변 및 정확한 답변(있는 경우)을 반환합니다.

## <a name="example-api-usage"></a>API 사용 예제

지정된 질문에 대한 답변을 얻고자 하는 텍스트 블록이 하나 이상 있다고 가정하겠습니다. 일반적으로 많은 수의 원본을 텍스트 블록의 수로 만들어야 했습니다. 그러나 이제 미리 작성 된 API를 사용 하는 경우 프로젝트/기술 자료에서 콘텐츠 소스를 정의 하지 않고도 텍스트 블록을 쿼리할 수 있습니다.

이 API를 사용할 수 있는 몇 가지 다른 시나리오는 다음과 같습니다.

* 최종 사용자를 위해 전자책 reader 앱을 개발 하 고 있으며,이를 통해 텍스트를 강조 표시 하 고, 질문을 입력 하 고, 강조 표시 된 텍스트에 대 한 답변을 찾을 수 있습니다.
* 사용자가 브라우저 페이지에 현재 표시 되는 콘텐츠에 대해 질문 하는 데 사용할 수 있는 브라우저 확장입니다.
* 사용자의 쿼리를 받고 봇이 사용자 쿼리와 가장 관련이 있는 것으로 식별 하는 의료 콘텐츠에 기반 하 여 답변을 제공 하는 health 봇입니다.

샘플 요청의 예는 다음과 같습니다.

## <a name="sample-request"></a>샘플 요청

```
POST https://{Unique-to-your-endpoint}.api.cognitive.microsoft.com/language/:query-text
```

### <a name="sample-query-over-a-single-block-of-text"></a>단일 텍스트 블록에 대 한 샘플 쿼리

요청 본문

```json
{
  "parameters": {
    "Endpoint": "{Endpoint}",
    "Ocp-Apim-Subscription-Key": "{API key}",
    "Content-Type": "application/json",
    "api-version": "2021-10-01",
    "stringIndexType": "TextElements_v8",
    "textQueryOptions": {
      "question": "how long it takes to charge surface?",
      "records": [
        {
          "id": "1",
          "text": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it."
        },
        {
          "id": "2",
          "text": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."
        }
      ],
      "language": "en"
    }
  }
}
```

## <a name="sample-response"></a>샘플 응답

위의 요청 본문에서는 단일 텍스트 블록에 대해 쿼리합니다. 위의 쿼리에 대해 수신되는 샘플 응답은 다음과 같습니다.

```json
{
"responses": {
    "200": {
      "headers": {},
      "body": {
        "answers": [
          {
            "answer": "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.93,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 28,
              "length": 45
            },
            "offset": 0,
            "length": 224
          },
          {
            "answer": "It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.92,
            "id": "1",
            "answerSpan": {
              "text": "two to four hours",
              "confidenceScore": 0,
              "offset": 8,
              "length": 25
            },
            "offset": 20,
            "length": 224
          },
          {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it.",
            "confidenceScore": 0.05,
            "id": "1",
            "answerSpan": null,
            "offset": 110,
            "length": 244
          }
        ]
      }
    }
  }
```

API 응답의 일부로 여러 답변이 수신되는 것을 볼 수 있습니다. 각 답변에는 답변의 전반적인 관련성을 이해하는 데 도움이 되는 특정 신뢰도 점수가 있습니다. 응답 범위는 잠재적 짧은 답변이 검색 되었는지 여부를 나타냅니다. 사용자는이 신뢰 점수를 사용 하 여 쿼리에 대 한 응답으로 제공할 대답을 결정할 수 있습니다.

## <a name="prebuilt-api-limits"></a>미리 빌드된 API 제한

허용 한도보다 크기가 큰 문서를 사용해야 하는 경우 텍스트를 API로 보내기 전에 큰 텍스트를 작은 텍스트 청크로 분할할 수 있습니다. 이 컨텍스트에서 문서는 텍스트 문자를 정의 하는 단일 문자열입니다.

이러한 숫자는 **개별 API 호출 제한** 에 따라 다음과 같이 표시 됩니다.

* 문서 수: 5.
* 단일 문서의 최대 크기: 5120 문자
* 문서당 최대 3 개의 응답입니다.

## <a name="prebuilt-api-reference"></a>미리 작성된 API 참조

API를 호출 하는 데 필요한 입력 및 출력 매개 변수를 이해 하려면 [미리 빌드된 전체 api 샘플](https://github.com/Azure/azure-rest-api-specs/blob/main/specification/cognitiveservices/data-plane/Language/stable/2021-10-01/examples/questionanswering/SuccessfulQueryText.json) 설명서를 참조 하세요.