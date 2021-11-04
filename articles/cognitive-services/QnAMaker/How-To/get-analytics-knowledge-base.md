---
title: 기술 자료 분석 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker Service를 생성하는 동안 App Insights를 사용하도록 설정한 경우 QnA Maker는 모든 채팅 로그 및 기타 원격 분석을 저장합니다. App Insights에서 채팅 로그를 가져오려면 샘플 쿼리를 실행합니다.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d9b596ad4766848e460d534be42930f39c5b6043
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069200"
---
# <a name="get-analytics-on-your-knowledge-base"></a>기술 자료에 대한 분석 가져오기

[QnA Maker Service를 만드는](./set-up-qnamaker-service-azure.md) 동안 Application Insights를 사용하도록 설정한 경우 QnA Maker는 모든 채팅 로그 및 기타 원격 분석을 저장합니다. 샘플 쿼리를 실행하여 Application Insight에서 채팅 로그를 가져옵니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

1. Application Insights 리소스로 이동합니다.

    ![Application Insights 리소스 선택](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. **Log(Analytics)** 를 선택합니다. QnA Maker 원격 분석을 쿼리할 수 있는 새 창이 열립니다.

3. 다음 쿼리에 붙여넣고 실행합니다.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    **실행** 을 선택하여 쿼리를 실행합니다.

    [![쿼리를 실행하여 사용자의 질문, 대답 및 점수 확인](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>QnA Maker 기술 자료에 대한 다른 분석에 대해 쿼리 실행

### <a name="total-90-day-traffic"></a>총 90일 트래픽

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>지정된 기간 동안 총 질문 트래픽

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>사용자 트래픽

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>질문 배포 대기 시간

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>대답이 없는 질문

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 선택](./improve-knowledge-base.md)
