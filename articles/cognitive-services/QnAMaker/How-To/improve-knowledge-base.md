---
title: 활성 학습 추천 질문 - QnA Maker
description: 활성 학습을 통해 기술 자료의 품질을 향상시킵니다. 기존 질문을 삭제하거나 변경하지 않고 검토, 수락, 거부 또는 추가합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: 775163041208de79a6c943630afe6b718f1849b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039167"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>기술 자료의 활성 학습 추천 질문 수락

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

활성 학습은 제안을 승인한 후 기술 자료 또는 Search Service를 변경한 다음, 저장하고 학습합니다. 추천을 승인하면 대안 질문으로 추가됩니다.

## <a name="turn-on-active-learning"></a>활성 학습 설정

추천 질문을 보려면 QnA Maker 리소스에 대해 [활성 학습을 켜야](../index.yml)합니다.

## <a name="view-suggested-questions"></a>추천 질문 보기

1. 추천 질문을 보려면 기술 자료 **편집** 페이지에서 **보기 옵션** 을 선택한 다음, **활성 학습 추천 표시** 를 선택합니다. 질문 및 답변 쌍에 대한 추천이 없는 경우에는 이 옵션을 사용할 수 없습니다.  

    [![포털의 편집 섹션에서 활성 학습의 새로운 질문 대안을 보려면 제안 사항 표시를 선택합니다.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. **추천으로 필터링** 을 선택하여 추천만 표시되도록 질문과 대답 쌍이 포함된 기술 자료를 필터링합니다.

    [![추천으로 필터팅 토글을 사용하여 활성 학습의 추천 질문 대안만 표시합니다.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 각 QnA 쌍은 질문을 수락할 수 있는 확인 표시(`✔`) 또는 추천을 거부할 수 있는 `x`가 포함된 새로운 질문 대안을 추천합니다. 질문을 추가하려면 확인 표시를 선택합니다.

    [![녹색 확인 표시 또는 빨간색 삭제 표시를 선택하여 활성 학습의 추천 질문 대안을 선택하거나 거부합니다.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    컨텍스트 도구 모음에서 **모두 추가** 또는 **모두 거부** 를 선택하여 _모든 추천_ 을 추가하거나 삭제할 수 있습니다.

1. **저장 및 학습** 을 선택하여 기술 자료에 변경 내용을 저장합니다.

1. **게시** 를 선택하여 [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)에서 변경 내용을 사용할 수 있도록 허용합니다.

    5개 이상의 유사한 쿼리가 클러스터링되면 30분마다 QnA Maker는 허용 또는 거부할 수 있는 대안 질문을 추천합니다.


<a name="#score-proximity-between-knowledge-base-questions"></a>

## <a name="active-learning-suggestions-are-saved-in-the-exported-knowledge-base"></a>활성 학습 추천은 내보낸 기술 자료에 저장됩니다.

앱에서 활성 학습을 사용하도록 설정하고 앱을 내보내는 경우 tsv 파일의 `SuggestedQuestions` 열은 활성 학습 데이터를 유지합니다.

`SuggestedQuestions` 열은 암시적인 `autosuggested` 및 명시적인 `usersuggested` 피드백에 대한 정보의 JSON 개체입니다. 사용자가 제출한 `help`에 대한 단일 질문의 이 JSON 개체 예제는 다음과 같습니다.

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

이 앱을 다시 가져오는 경우 활성 학습은 계속해서 정보를 수집하고 기술 자료에 대한 추천을 권장합니다.


### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>봇에서 GenerateAnswer 및 학습 API 사용을 위한 아키텍처 흐름

봇 또는 다른 클라이언트 애플리케이션은 다음 아키텍처 흐름을 사용하여 활성 학습을 사용해야 합니다.

1. 봇은 `top` 속성을 사용하여 GenerateAnswer API로 [기술 자료에서 답변을 가져와](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) 많은 답변을 가져옵니다.

2. 봇은 명시적인 피드백을 결정합니다.
    * 사용자 고유의 [사용자 지정 비즈니스 논리](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)를 사용하여 낮은 점수를 필터링합니다.
    * 봇 또는 클라이언트 애플리케이션에서 사용자에게 가능한 답변 목록을 표시하고 사용자가 선택한 대답을 가져옵니다.
3. 봇은 [학습 API](#train-api)를 사용하여 [선택한 답변을 QnA Maker로 다시 보냅니다](#bot-framework-sample-code).

### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>GenerateAnswer 요청에서 top 속성을 사용하여 여러 일치하는 답변을 가져옵니다.

답변을 얻기 위해 QnA Maker 질문을 제출할 때 JSON 본문의 `top` 속성이 반환할 답변 수를 설정합니다.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>비즈니스 논리와 함께 점수 속성을 사용하여 사용자에게 표시할 답변 목록 가져오기

챗봇 등의 클라이언트 애플리케이션이 응답을 수신하면 상위 3개 질문이 반환됩니다. `score` 속성을 사용하여 점수 간의 근접을 분석합니다. 이러한 근접 범위는 사용자 고유의 비즈니스 논리에 의해 결정됩니다.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>질문의 점수가 비슷할 때의 클라이언트 애플리케이션 추가 작업

클라이언트 애플리케이션에서 사용자가 자신의 의도를 가장 적절하게 반영하는 _단일 질문_ 을 선택할 수 있는 옵션과 함께 질문을 표시합니다.

사용자가 기존 질문 중 하나를 선택 하면 클라이언트 응용 프로그램은 QnA Maker 학습 API를 사용 하 여 사용자의 선택 항목을 사용자 의견으로 보냅니다. 이 피드백으로 활성 학습 피드백 루프가 완료됩니다.

## <a name="train-api"></a>학습 API

활성 학습 피드백은 학습 API POST 요청을 통해 QnA Maker로 전송됩니다. API 서명은 다음과 같습니다.

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 요청 속성|이름|Type|목적|
|--|--|--|--|
|URL 경로 매개 변수|기술 자료 ID|문자열|기술 자료를 위한 GUID입니다.|
|사용자 지정 하위 도메인|QnAMaker 리소스 이름|문자열|리소스 이름은 QnA Maker의 사용자 지정 하위 도메인으로 사용됩니다. 이 속성은 기술 자료를 게시한 후 설정 페이지에서 사용할 수 있습니다. 이는 `host`로 나열됩니다.|
|헤더|콘텐츠 형식|문자열|API로 전송되는 본문의 미디어 유형입니다. 기본값은 `application/json`입니다.|
|헤더|권한 부여|문자열|엔드포인트 키(EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)입니다.|
|게시물 본문|JSON 개체|JSON|학습 피드백|

JSON 본문에는 다음과 같은 몇 가지 설정이 있습니다.

|JSON 본문 속성|형식|목적|
|--|--|--|--|
|`feedbackRecords`|array|피드백 목록입니다.|
|`userId`|문자열|추천 질문을 수락하는 사람의 사용자 ID입니다. 사용자 ID 형식은 사용자가 결정합니다. 예를 들어, 이메일 주소가 아키텍처에서 유효한 사용자 ID가 될 수 있습니다. 선택 사항입니다.|
|`userQuestion`|문자열|사용자 쿼리의 정확한 텍스트입니다. 필수 요소.|
|`qnaID`|number|[GenerateAnswer 응답](metadata-generateanswer-usage.md#generateanswer-response-properties)에 있는 질문의 ID입니다. |

JSON 본문 예는 다음과 같습니다.

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

응답이 성공하면 204 상태가 반환되며 JSON 응답 본문은 반환되지 않습니다.

### <a name="batch-many-feedback-records-into-a-single-call"></a>여러 피드백 레코드를 단일 호출로 일괄 처리

봇과 같은 클라이언트 쪽 애플리케이션에서 데이터를 저장한 다음, `feedbackRecords` 배열의 단일 JSON 본문에 많은 레코드를 보낼 수 있습니다.

JSON 본문 예는 다음과 같습니다.

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot Framework 샘플 코드

사용자의 쿼리를 활성 학습에 사용해야 하는 경우 Bot Framework 코드에서 학습 API를 호출해야 합니다. 작성하는 코드로는 다음과 같이 두 가지가 있습니다.

* 쿼리가 활성 학습에 사용되어야 하는지 결정하는 코드
* 활성 학습을 위한 QnA Maker 학습 API로 쿼리를 다시 보냅니다.

[Azure Bot 샘플](https://github.com/microsoft/BotBuilder-Samples)에서 이러한 활동이 모두 프로그래밍되었습니다.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Bot Framework 4.x를 사용하는 학습 API에 대한 예제 C# 코드

다음 코드에서는 학습 API를 사용하여 QnA Maker에 정보를 다시 보내는 방법을 보여 줍니다.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Bot Framework 4.x를 사용하는 학습 API에 대한 예제 Node.js 코드

다음 코드에서는 학습 API를 사용하여 QnA Maker에 정보를 다시 보내는 방법을 보여 줍니다.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="best-practices"></a>모범 사례

활성 학습 사용 시의 모범 사례는 [모범 사례](../Concepts/best-practices.md#active-learning)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GenerateAnswer API로 메타데이터 사용](metadata-generateanswer-usage.md)
