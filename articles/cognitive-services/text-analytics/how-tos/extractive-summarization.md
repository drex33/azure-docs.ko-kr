---
title: Text Analytics 추출 요약 API를 사용하여 텍스트 요약
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Text Analytics 추출 요약 API를 사용하여 텍스트를 요약하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: aahi
ms.openlocfilehash: f8db386da9890be2a07f201243dbbb4beaf5c499
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539210"
---
# <a name="how-to-summarize-text-with-text-analytics-preview"></a>방법: Text Analytics를 사용하여 텍스트 요약(미리 보기)

> [!IMPORTANT] 
> Text Analytics 추출 요약은 “있는 그대로”, “모든 오류 포함”으로 제공되는 미리 보기 기능입니다. 따라서 Text Analytics 추출 요약(미리 보기)은 프로덕션 용도로 구현하거나 배포해서는 안 됩니다. Text Analytics 추출 요약 사용의 책임은 전적으로 고객에게 있습니다. 

일반적으로 자동 텍스트 요약에는 추출 및 추상의 두 가지 접근 방식이 있습니다. Text Analytics API는 `3.2-preview.1` 버전부터 추출 요약을 제공합니다.

추출 요약은 원본 콘텐츠 내에서 가장 중요하거나 관련된 정보를 집합적으로 나타내는 문장을 추출하여 요약을 생성하는 Azure Text Analytics의 기능입니다.

이 기능은 사용자가 너무 길어서 읽을 수 없다고 간주하는 콘텐츠를 줄이도록 디자인됩니다. 추출 요약은 기사, 보고서 또는 문서를 핵심 문장으로 압축합니다.

API에서 사용하는 AI 모델은 서비스를 통해 제공되며, 분석 용도로만 콘텐츠를 전송해야 합니다.

## <a name="extractive-summarization-and-features"></a>추출 요약 및 기능

Text Analytics의 추출 요약 기능은 자연어 처리 기술을 사용하여 구조화되지 않은 텍스트 문서에서 핵심 문장을 찾습니다. 이 문장은 문서의 기본 아이디어를 집합적으로 전달합니다.

추출 요약은 추출된 문장, 원본 문서 내 위치와 함께 순위 점수를 시스템 응답의 일부로 반환합니다. 순위 점수는 문장과 문서의 기본 아이디어 간 관련성을 나타내는 표시기입니다. 모델은 각 문장에 대해 0~1(포함) 사이의 점수를 제공하고 요청당 점수가 가장 높은 문장을 반환합니다. 예를 들어, 3개 문장 요약을 요청하는 경우 서비스는 점수가 가장 높은 3개 문장을 반환합니다.

핵심 정보를 추출할 수 있는 Text Analytics의 또 다른 기능인 [핵심 구 추출](./text-analytics-how-to-keyword-extraction.md)이 있습니다. 핵심 구 추출과 추출 요약 중에서 결정하는 경우 다음을 고려합니다.
* 핵심 구 추출은 구를 반환하는 반면, 추출 요약은 문장을 반환합니다.
* 추출 요약은 순위 점수와 함께 문장을 반환합니다. 요청당 순위가 가장 높은 문장이 반환됩니다.

## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기 

> [!TIP]
> 최신 미리 보기 버전의 클라이언트 라이브러리를 사용하여 추출 요약을 사용할 수도 있습니다. GitHub에서 다음 샘플을 참조하세요. 
> * [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample8_ExtractSummary.md)
> * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java)
> * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_extract_summary.py)

### <a name="preparation"></a>준비

다른 Text Analytics 기능과 달리, 추출 요약 기능은 비동기 전용 작업이며 /analyze 엔드포인트를 통해 액세스할 수 있습니다. JSON 요청 데이터는 [/analyze 엔드포인트에 대한 비동기 요청](./text-analytics-how-to-call-api.md?tabs=asynchronous#api-request-formats)에 설명된 형식을 따라야 합니다.

추출 요약은 문서 입력을 위한 광범위한 언어를 지원합니다. 자세한 내용은 [지원되는 언어](../language-support.md)를 참조하세요.

문서 크기는 문서당 125,000자 미만이어야 합니다. 컬렉션에 허용되는 최대 문서 수는 [데이터 제한](../concepts/data-limits.md?tabs=version-3) 문서를 참조하세요. 컬렉션은 요청 본문에 제출됩니다.

### <a name="structure-the-request"></a>요청 구성

POST 요청을 만듭니다. [Postman](text-analytics-how-to-call-api.md)을 사용하거나 다음 참조 링크의 **API 테스트 콘솔** 을 사용하여 신속하게 요청을 구성하고 보낼 수 있습니다. 

[텍스트 요약 참조](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-preview-1/operations/Analyze)

### <a name="request-endpoints"></a>요청 엔드포인트

Azure에서 Text Analytics 리소스를 사용하여 추출 요약의 HTTPS 엔드포인트를 설정합니다. 예를 들면 다음과 같습니다.

> [!NOTE]
> Azure Portal에서 Text Analytics 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 해당 항목은 리소스의 **키 및 엔드포인트** 페이지에 있습니다. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze`

Text Analytics API 키를 포함하도록 요청 헤더를 설정합니다. 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

### <a name="example-request"></a>요청 예 

다음은 요약을 위해 제출할 수 있는 콘텐츠의 예로, 데모용으로 [통합 AI에 대한 전체적인 표현](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/)을 사용하여 추출됩니다. 추출 요약 API는 훨씬 더 긴 입력 텍스트를 허용할 수 있습니다. Text Analytics API의 데이터 제한에 관한 자세한 내용은 [데이터 제한](../Concepts/data-limits.md)을 참조하세요.

하나의 요청에 여러 문서가 포함될 수 있습니다. 

각 문서에는 다음 매개 변수가 있습니다.
* `id` - 문서를 식별합니다. 
* `language` - 문서의 원본 언어를 나타내며 `en`이 기본값입니다.
* `text` - 문서 텍스트를 첨부합니다.

한 요청의 모든 문서는 다음 매개 변수를 공유합니다. 해당 매개 변수는 요청의 `tasks` 정의에서 지정할 수 있습니다.
* `model-version` - 사용할 모델 버전을 지정하며 `latest`가 기본값입니다. 자세한 내용은 [모델 버전](../concepts/model-versioning.md)을 참조하세요. 
* `sentenceCount` - 반환될 문장 수를 지정하며 `3`이 기본값입니다. 범위는 1~20 사이입니다.
* `sortBy` - 추출된 문장이 반환되는 순서를 지정합니다. `sortBy`의 허용되는 값은 `Offset` 및 `Rank`이며 `Offset`이 기본값입니다. `Offset` 값은 원본 문서에서 문장의 시작 위치입니다.    

```json
{
  "analysisInput": {
    "documents": [
      {
        "language": "en",
        "id": "1",
        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."
      }
    ]
  },
  "tasks": {
    "extractiveSummarizationTasks": [
      {
        "parameters": {
          "model-version": "latest",
          "sentenceCount": 3,
          "sortBy": "Offset"
        }
      }
    ]
  }
}
```

### <a name="post-the-request"></a>요청 게시

추출 요약 API는 요청이 수신될 때 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

Text Analytics 추출 요약 API는 비동기 API이므로 응답 개체에 텍스트가 없습니다. 그러나 작업과 출력의 상태를 확인하기 위해 GET 요청을 수행하려면 응답 헤더에 `operation-location` 키 값이 필요합니다.  다음은 POST 요청의 응답 헤더에 있는 작업-위치 KEY 값의 예입니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/<jobID>`

작업 상태를 확인하려면 POST 응답의 작업-위치 KEY 헤더 값에 있는 URL에 GET 요청을 수행합니다.  작업 상태를 반영하는 데는 `NotStarted`, `running`, `succeeded`, `failed` 또는 `rejected` 상태가 사용됩니다.  

작업이 성공하면 API의 출력이 GET 요청의 본문에서 반환됩니다. 


### <a name="view-the-results"></a>결과 보기

다음은 GET 요청 응답의 예입니다.  출력은 `expirationDateTime`(작업이 생성된 시간부터 24시간)이 경과되어 출력이 제거될 때까지 검색에 사용할 수 있습니다. 다국어 지원 및 emoji 지원으로 인해 응답에 텍스트 오프셋이 포함될 수 있습니다. 자세한 내용은 [오프셋 처리 방법](../concepts/text-offsets.md)을 참조하세요.

### <a name="example-response"></a>예제 응답
  
추출 요약 기능은 다음을 반환합니다. 

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-06-11T05:43:37Z",
    "createdDateTime": "2021-06-11T05:42:32Z",
    "expirationDateTime": "2021-06-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "sentences": [
                    {
                        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                        "rankScore": 0.7673416137695312,
                        "Offset": 0,
                        "length": 160
                    },
                    {
                        "text": "In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                        "rankScore": 0.7644073963165283,
                        "Offset": 324,
                        "length": 192
                    },
                    {
                        "text": "At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                        "rankScore": 0.7623870968818665,
                        "Offset": 517,
                        "length": 203
                    }    
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-08-01"
    }
}
```

## <a name="summary"></a>요약

이 문서에서는 Text Analytics 추출 요약 API를 사용하는 추출 요약의 개념과 워크플로를 알아보았습니다. 추출 요약을 사용하여 다음을 수행할 수 있습니다.

* 효율성을 높이기 위해 문서 처리를 지원합니다.
* 긴 문서, 보고서, 기타 텍스트 양식에서 중요한 정보를 추출합니다.
* 문서에서 핵심 문장을 강조 표시합니다.
* 라이브러리에서 문서를 빠르게 훑어봅니다.
* 뉴스 피드 콘텐츠를 생성합니다.

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [새로운 기능](../whats-new.md)
* [모델 버전](../concepts/model-versioning.md)
