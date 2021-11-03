---
title: GenerateAnswer API와 메타데이터 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 메타데이터를 키/값 쌍의 형태로 질문/답변 쌍에 추가할 수 있습니다. 사용자 쿼리에 대한 결과를 필터링하고 후속 대화에 사용할 수 있는 추가 정보를 저장할 수 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: ba2b39a854dd7d70690aea943f7365bc50bc8804
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038787"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>GenerateAnswer API를 사용하여 답변 가져오기

사용자의 질문에 대한 예측 답변을 가져오려면 GenerateAnswer API를 사용합니다. 기술 자료를 게시할 때 **게시** 페이지에서 이 API를 사용하는 방법에 대한 정보를 볼 수 있습니다. 메타데이터 태그를 기준으로 답변을 필터링하도록 API를 구성하고 테스트 쿼리 문자열 매개 변수를 사용하여 엔드포인트에서 기술 자료를 테스트할 수도 있습니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API를 사용하여 답변 예측 가져오기

봇 또는 애플리케이션에서 [GenerateAnswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)를 사용하여 사용자 질문으로 기술 자료를 쿼리하면 질문 및 답변 쌍에서 최적의 일치 항목을 가져올 수 있습니다.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>게시하여 GenerateAnswer 엔드포인트 가져오기

기술 자료를 게시한 후에는 [QnA Maker 포털](https://www.qnamaker.ai)에서 또는 [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)를 사용하여 GenerateAnswer 엔드포인트 세부 정보를 가져올 수 있습니다.

엔드포인트 세부 정보를 가져오려면 다음을 수행합니다.
1. [https://www.qnamaker.ai](https://www.qnamaker.ai)에 로그인합니다.
1. **내 기술 자료** 에서 기술 자료에 대한 **코드 보기** 를 선택합니다.
    ![내 기술 자료 스크린샷](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer 엔드포인트 세부 정보를 가져옵니다.

    ![엔드포인트 세부 정보 스크린샷](../media/qnamaker-how-to-metadata-usage/view-code.png)

기술 자료의 **설정** 탭에서 엔드포인트 세부 정보를 가져올 수도 있습니다.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 요청 구성

HTTP POST 요청을 사용하여 GenerateAnswer를 호출합니다. GenerateAnswer를 호출하는 방법을 보여주는 샘플 코드는 [빠른 시작](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)을 참조하세요.

POST 요청은 다음을 사용합니다.

* 필수 [URI 매개 변수](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 보안을 위한 필수 헤더 속성 `Authorization`
* 필수 [본문 속성](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)

GenerateAnswer URL의 형식은 다음과 같습니다.

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

`Authorization`의 HTTP 헤더 속성은 후행 공백이 있는 `EndpointKey` 문자열 값으로 설정한 다음, **설정** 페이지에 있는 엔드포인트 키로 설정해야 합니다.

JSON 본문 예는 다음과 같습니다.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

[rankerType](../concepts/best-practices.md#choosing-ranker-type)에 대해 자세히 알아보세요.

이전 JSON은 임계값 점수보다 30% 이상인 답변만 요청했습니다.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 응답 속성

[응답](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)은 답변과 다음 차례의 대화(사용 가능한 경우)를 표시하는 데 필요한 모든 정보를 포함하는 JSON 개체입니다.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

이전 JSON은 점수가 38.5%인 답변으로 응답했습니다.

## <a name="match-questions-only-by-text"></a>텍스트를 기준으로 질문과 대답 찾기

기본적으로 QnA Maker는 질문과 답변을 검색합니다. 질문에 대해서만 검색하려는 경우 답변을 생성하려면 GenerateAnswer 요청의 POST 본문에 `RankerType=QuestionOnly`를 사용합니다.

`isTest=false`를 사용하는 게시된 kb를 통해 또는 `isTest=true`를 사용하는 테스트 kb에서 검색할 수 있습니다.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>C#의 봇과 함께 QnA Maker 사용

Bot Framework는 [getAnswer API](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)를 사용하여 QnA Maker의 속성에 대한 액세스 권한을 제공합니다.

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

이전 JSON은 임계값 점수보다 30% 이상인 답변만 요청했습니다.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js의 봇과 함께 QnA Maker 사용

Bot Framework는 [getAnswer API](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)를 사용하여 QnA Maker의 속성에 대한 액세스 권한을 제공합니다.

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

이전 JSON은 임계값 점수보다 30% 이상인 답변만 요청했습니다.

## <a name="get-precise-answers-with-generateanswer-api"></a>GenerateAnswer API를 사용하여 정확한 답변 받기

QnA Maker 관리형 버전에서만 정확한 답변 기능을 제공합니다.

## <a name="common-http-errors"></a>일반 HTTP 오류

|코드|설명|
|:--|--|
|2xx|Success|
|400|요청 매개 변수가 잘못되었습니다. 필수 매개 변수가 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|400|요청 본문이 잘못되었습니다. JSON이 누락되었거나, 형식이 잘못되었거나, 너무 큽니다.|
|401|잘못된 키|
|403|사용 권한 없음 - 올바른 사용 권한이 없음|
|404|기술 자료가 존재하지 않음|
|410|이 API는 더 이상 사용되지 않으며, 더 이상 사용할 수 없습니다.|

## <a name="next-steps"></a>다음 단계

**게시** 페이지에서는 Postman 또는 cURL과 함께 [답변을 생성](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)하기 위한 정보도 제공합니다.

> [!div class="nextstepaction"]
> [기술 자료에 대한 분석 가져오기](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [신뢰도 점수](../Concepts/confidence-score.md)
