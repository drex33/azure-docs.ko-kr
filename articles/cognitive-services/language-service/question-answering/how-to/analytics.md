---
title: 기술 자료 분석 - 사용자 지정 질문 답변
titleSuffix: Azure Cognitive Services
description: 사용자 지정 질문 답변은 Azure 진단 로깅을 사용하여 원격 분석 데이터 및 채팅 로그를 저장합니다.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: fba1481ffe7aa45514e53813ccf7b14ea01bd6bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103040"
---
# <a name="get-analytics-for-your-knowledge-base"></a>기술 자료 분석 얻기

사용자 지정 질문 답변은 Azure 진단 로깅을 사용하여 원격 분석 데이터 및 채팅 로그를 저장합니다. 아래 단계에 따라 샘플 쿼리를 실행하여 사용자 지정 질문 응답 프로젝트의 사용에 대한 분석을 얻습니다.

1. 사용자 지정 질문 대답을 사용하도록 설정하여 언어 리소스에 대한 [진단 로깅을](../../../diagnostic-logging.md) 사용하도록 설정합니다.

2. 이전 단계에서 로깅을 위해 **Audit, RequestResponse 및 AllMetrics** 외에 **Trace** 를 선택합니다.

    ![사용자 지정 질문 답변에서 추적 로깅 사용](../media/analytics/qnamaker-v2-enable-trace-logging.png)

## <a name="kusto-queries"></a>Kusto 쿼리

### <a name="chat-log"></a>채팅 로그

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>특정 기간 동안의 기술 자료 및 사용자당 트래픽 수

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>GenerateAnswer API의 대기 시간

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>모든 작업의 평균 대기 시간

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>대답이 없는 질문

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [용량 선택](../../../qnamaker/how-to/improve-knowledge-base.md)
