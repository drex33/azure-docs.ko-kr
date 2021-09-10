---
title: 텍스트 분석 API 호출
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Text Analytics REST API 및 Postman을 호출하는 방법을 설명합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: aahi
ms.openlocfilehash: b9e18bb9bf313ce2bbf1441b319b841a3153f38b
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122538910"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>텍스트 분석 REST API를 호출하는 방법

이 문서에서는 Text Analytics REST API 및 [Postman](https://www.postman.com/downloads/)을 사용하여 주요 개념을 보여 줍니다. API는 서비스 기능을 사용하기 위한 여러 동기 및 비동기 엔드포인트를 제공합니다. 

## <a name="create-a-text-analytics-resource"></a>Text Analytics 리소스 만들기

> [!NOTE]
> * `/analyze` 또는 `/health` 엔드포인트를 사용하려는 경우 표준(S) [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)을 사용하는 Text Analytics 리소스가 필요합니다. `/analyze` 엔드포인트는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)에 포함되어 있습니다.

Text Analytics API를 사용하기 전에 애플리케이션에 대한 키 및 엔드포인트를 사용하여 Azure 리소스를 만들어야 합니다. 

1.  먼저 새 Text Analytics 리소스가 없는 경우 [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동하여 새로 만듭니다. [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)을 선택합니다.

2.  엔드포인트에 사용할 지역을 선택합니다.

3.  Text Analytics 리소스를 만들고 페이지 왼쪽의 리소스 관리 아래에 있는 "키 및 엔드포인트" 섹션으로 이동합니다. 나중에 API를 호출할 때 사용할 키를 복사합니다. 나중에 `Ocp-Apim-Subscription-Key` 헤더의 값으로 추가합니다.

4. Text Analytics 리소스를 사용하여 전송된 텍스트 레코드 수를 확인하려면 다음을 수행합니다.

    1. Azure Portal에서 Text Analytics 리소스로 이동합니다. 
    2. 왼쪽 탐색 메뉴의 **모니터링** 아래에 있는 **메트릭** 을 클릭합니다. 
    3. **메트릭** 에 대한 드롭다운 상자에서 *처리된 텍스트 레코드* 를 선택합니다.
    
텍스트 레코드는 최대 1000자의 입력 텍스트 단위입니다.  예를 들어 입력 텍스트로 제출된 1500자는 2개 텍스트 레코드로 계산됩니다.

## <a name="change-your-pricing-tier"></a>가격 책정 계층 변경 

S0~S4 가격 책정 계층을 사용하는 기존 Text Analytics 리소스가 있는 경우 표준(S) [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)을 사용하도록 업데이트해야 합니다. S0~S4 가격 책정 계층은 사용이 중지됩니다. 리소스의 가격을 업데이트하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Text Analytics 리소스로 이동합니다.
2. 왼쪽 탐색 메뉴에서 **가격 책정 계층** 을 선택합니다. **리소스 관리** 아래에 있습니다. 
3. 표준(S) 가격 책정 계층을 선택합니다. 그런 다음 **선택** 을 클릭합니다.

표준(S) 가격 책정 계층으로 새 Text Analytics 리소스를 만들고 새 리소스에 대한 자격 증명을 사용하도록 애플리케이션을 마이그레이션할 수도 있습니다. 

## <a name="using-the-api-synchronously"></a>동기식으로 API 사용

대기 시간이 짧은 시나리오의 경우 Text Analytics를 동기식으로 호출할 수 있습니다. 동기식 API를 사용할 때는 각 API(기능)를 별도로 호출해야 합니다. 여러 기능을 호출해야 하는 경우 아래 섹션에서 Text Analytics를 비동기적으로 호출하는 방법을 참조하세요. 

## <a name="using-the-api-asynchronously"></a>비동기식으로 API 사용

Text Analytics v3.1 API는 두 개의 비동기 엔드포인트를 제공합니다. 

* Text Analytics에 대한 `/analyze` 엔드포인트를 사용하면 하나의 API 호출에서 여러 텍스트 분석 기능이 있는 동일한 텍스트 문서 집합을 분석할 수 있습니다. 이전에는 여러 기능을 사용하려면 각 작업에 대해 별도의 API 호출을 수행해야 했습니다. 둘 이상의 Text Analytics 기능을 사용하여 많은 문서를 분석해야 하는 경우 이 기능을 사용해보세요.

* 의료 분야 Text Analytics의 `/health` 엔드포인트는 임상 문서에서 관련 의료 정보를 추출하고 레이블을 지정할 수 있습니다.  

비동기식으로 사용할 수 있는 기능을 보려면 아래 표를 참조하세요. `/analyze` 엔드포인트에서는 몇 가지 기능만 호출할 수 있습니다. 

| 기능 | 동기 | 비동기 |
|--|--|--|
| 언어 검색 | ✔ |  |
| 정서 분석 | ✔ | ✔* |
| 의견 마이닝 | ✔ | ✔* |
| 핵심 문구 추출 | ✔ | ✔* |
| 명명된 엔터티 인식(PII 및 PHI 포함) | ✔ | ✔* |
| 엔터티 연결 | ✔ | ✔* |
| 의료 분야 Text Analytics(컨테이너) | ✔ |  |
| 의료 분야 Text Analytics(API) |  | ✔  |
| 텍스트 요약 |  | ✔  |

`*` - `/analyze` 엔드포인트를 통해 비동기식으로 호출됩니다.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [text-analytics-character-limits](../includes/character-limits.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>API 요청 형식

Text Analytics API에 동기 및 비동기 호출을 모두 보낼 수 있습니다.

#### <a name="synchronous"></a>[동기](#tab/synchronous)

### <a name="synchronous-requests"></a>동기 요청

API 요청의 형식은 모든 동기 작업에서 동일합니다. 문서는 비구조적 원시 텍스트로 JSON 개체에서 제출됩니다. XML은 지원되지 않습니다. JSON 스키마는 아래에 설명된 요소로 구성됩니다.

| 요소 | 유효한 값 | 필수 여부 | 사용량 |
|---------|--------------|-----------|-------|
|`id` |데이터 형식은 문자열이지만 실제로 문서 ID는 정수인 경우가 많습니다. | 필수 | 시스템은 사용자가 제공하는 ID를 사용하여 출력을 구성합니다. 언어 코드, 핵심 구 및 감정 점수가 요청의 각 ID에 대해 생성됩니다.|
|`text` | 구조화되지 않은 원시 텍스트(최대 5,120자)입니다. | 필수 | 언어 감지의 경우 텍스트를 어떤 언어로도 나타낼 수 있습니다. 감정 분석, 핵심 구 추출 및 엔터티 식별의 경우 텍스트는 [지원되는 언어](../language-support.md)로 작성되어야 합니다. |
|`language` | [지원되는 언어](../language-support.md)의 2자리 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 코드 | 상황에 따라 다름 | 감정 분석, 핵심 구 추출 및 엔터티 연결의 경우 필수. 언어 감지의 경우 옵션 제외해도 오류는 발생하지 않지만 분석 효과가 약해집니다. 언어 코드는 사용자가 제공한 `text`와 일치해야 합니다. |

다음은 동기식 Text Analytics 엔드포인트에 대한 API 요청의 예입니다. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[비동기](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>`/analyze` 엔드포인트에 대한 비동기 요청

> [!NOTE]
> Text Analytics 클라이언트 라이브러리의 최신 시험판을 사용하면 클라이언트 개체를 통해 비동기 분석 작업을 호출할 수 있습니다. GitHub에서 예를 찾을 수 있습니다.
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze` 엔드포인트를 사용하면 단일 API 호출에서 사용할 지원되는 Text Analytics 기능을 선택할 수 있습니다. 이 엔드포인트는 현재 다음을 지원합니다.

* 핵심 문구 추출 
* 명명된 엔터티 인식(PII 및 PHI 포함)
* 엔터티 연결
* 감정 분석
* 오피니언 마이닝
* 텍스트 요약

| 요소 | 유효한 값 | 필수 여부 | 사용량 |
|---------|--------------|-----------|-------|
|`displayName` | String | Optional | 작업에 대한 고유 식별자의 표시 이름으로 사용됩니다.|
|`analysisInput` | 아래의 `documents` 필드 포함 | 필수 | 보내려는 문서에 대한 정보를 포함합니다. |
|`documents` | 아래의 `id` 및 `text` 필드 포함 | 필수 | 전송되는 각 문서에 대한 정보와 문서의 원시 텍스트를 포함합니다. |
|`id` | String | 필수 | 제공하는 ID는 출력을 구성하는 데 사용됩니다. |
|`text` | 구조화되지 않은 원시 텍스트(최대 125,000자)입니다. | 필수 | 현재 지원되는 유일한 언어인 영어로 되어 있어야 합니다. |
|`tasks` | `entityRecognitionTasks`,`entityLinkingTasks`,`keyPhraseExtractionTasks`,`entityRecognitionPiiTasks` `extractiveSummarizationTasks` 또는 `sentimentAnalysisTasks` 등의 Text Analytics 기능이 포함됩니다. | 필수 | 사용하려는 하나 이상의 Text Analytics 기능입니다. `entityRecognitionPiiTasks`에는 `pii` 또는 `phi` 및 선택된 엔터티 형식 검색을 위해 `piiCategories`로 설정할 수 있는 선택적 `domain` 매개 변수가 있습니다. `domain` 매개 변수가 지정되지 않은 경우 시스템 기본값은 `pii`입니다. 마찬가지로 `sentimentAnalysisTasks`에는 감정 분석에 대한 출력에 오피니언 마이닝 결과를 포함하는 `opinionMining` 부울 매개 변수가 있습니다. |
|`parameters` | 아래의 `model-version` 및 `stringIndexType` 필드 포함 | 필수 | 이 필드는 선택한 위의 기능 작업에 포함되어 있습니다. 여기에는 사용하려는 모델 버전 및 색인 유형에 대한 정보가 포함됩니다. |
|`model-version` | String | 필수 | 호출 중인 모델의 사용하려는 버전을 지정합니다.  |
|`stringIndexType` | String | 필수 | 프로그래밍 환경과 일치하는 텍스트 디코더를 지정합니다.  지원되는 유형은 `textElement_v8`(기본값), `unicodeCodePoint`, `utf16CodeUnit`입니다. 자세한 내용은 [텍스트 오프셋 문서](../concepts/text-offsets.md#offsets-in-api-version-31)를 참조하세요.  |
|`domain` | String | Optional | `entityRecognitionPiiTasks` 작업에 대한 매개 변수로만 적용되며 `pii` 또는 `phi`로 설정할 수 있습니다. 지정되지 않은 경우 기본값은 `pii`입니다.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction. The shops have very good food."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false
                }
            }
        ],
        "entityRecognitionPiiTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": true,
                    "domain": "phi",
                    "piiCategories":["default"]
                }
            }
        ],
        "entityLinkingTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false
                }
            }
        ],
        "keyPhraseExtractionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false
                }
            }
        ],
        "sentimentAnalysisTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "loggingOptOut": false,
                    "opinionMining": true
                }
            }
        ]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>`/health` 엔드포인트에 대한 비동기 요청

의료 분야 Text Analytics 호스팅 API에 대한 API 요청 형식은 해당 컨테이너와 동일합니다. 문서는 비구조적 원시 텍스트로 JSON 개체에서 제출됩니다. XML은 지원되지 않습니다. JSON 스키마는 아래에 설명된 요소로 구성됩니다.  의료 분야 Text Analytics에 대한 액세스를 요청하려면 [Cognitive Services 요청 양식](https://aka.ms/csgate)을 작성하고 제출하세요.

| 요소 | 유효한 값 | 필수 여부 | 사용량 |
|---------|--------------|-----------|-------|
|`id` |데이터 형식은 문자열이지만 실제로 문서 ID는 정수인 경우가 많습니다. | 필수 | 시스템은 사용자가 제공하는 ID를 사용하여 출력을 구성합니다. |
|`text` | 구조화되지 않은 원시 텍스트(최대 5,120자)입니다. | 필수 | 현재 영어 텍스트만 지원됩니다. |
|`language` | [지원되는 언어](../language-support.md)의 2자리 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 코드 | 필수 | 현재 `en`만 지원됩니다. |

다음은 의료 분야 Text Analytics 엔드포인트에 대한 API 요청의 예입니다. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Text Analytics API로 데이터를 전송하기 위한 속도 및 크기 제한에 대한 정보는 [데이터 및 속도 제한](../concepts/data-limits.md) 문서를 참조하세요.


## <a name="set-up-a-request"></a>요청 설정 

Postman(또는 다른 웹 API 테스트 도구)에서 사용하려는 기능에 대한 엔드포인트를 추가합니다. 아래 표를 사용하여 적절한 엔드포인트 형식을 찾고 `<your-text-analytics-resource>`를 리소스 엔드포인트로 바꿉니다. 예를 들면 다음과 같습니다.

> [!TIP]
> `/v3.1`을 `/v3.0/`으로 바꿔서 아래 동기 엔드포인트의 v 3.0을 호출할 수 있습니다.

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1/languages`

#### <a name="synchronous"></a>[동기](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>동기 요청을 보내기 위한 엔드포인트

| 기능 | 요청 유형 | 리소스 엔드포인트 |
|--|--|--|
| 언어 검색 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/languages` |
| 감정 분석 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/sentiment` |
| 오피니언 마이닝 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/sentiment?opinionMining=true` |
| 핵심 문구 추출 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/keyPhrases` |
| 명명된 엔터티 인식 - 일반 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/entities/recognition/general` |
| 명명된 엔터티 인식 - PII | POST | `<your-text-analytics-resource>/text/analytics/v3.1/entities/recognition/pii` |
| 명명된 엔터티 인식 - PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.1/entities/recognition/pii?domain=phi` |
| 엔터티 연결 | POST | `<your-text-analytics-resource>/text/analytics/v3.1/entities/linking` |

#### <a name="asynchronous"></a>[비동기](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>`/analyze` 엔드포인트에 비동기 요청을 보내기 위한 엔드포인트

| 기능 | 요청 유형 | 리소스 엔드포인트 |
|--|--|--|
| 분석 제출 작업 | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1/analyze` |
| 분석 상태 및 결과 가져오기 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>`/health` 엔드포인트에 비동기 요청을 보내기 위한 엔드포인트

| 기능 | 요청 유형 | 리소스 엔드포인트 |
|--|--|--|
| 의료 분야 Text Analytics 제출 작업  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1/entities/health/jobs` |
| 작업 상태 및 결과 가져오기 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1/entities/health/jobs/<Operation-Location>` |
| 작업 취소 | Delete | `https://<your-text-analytics-resource>/text/analytics/v3.1/entities/health/jobs/<Operation-Location>` |

--- 

엔드포인트를 만든 후 Postman(또는 다른 웹 API 테스트 도구)에서 다음을 수행합니다.

1. 사용하려는 기능에 대한 요청 유형을 선택합니다.
2. 위 표에서 원하는 작업의 엔드포인트에 붙여넣습니다.
3. 다음 세 개의 요청 헤더를 설정합니다.

   + `Ocp-Apim-Subscription-Key`: Azure Portal에서 가져온 액세스 키
   + `Content-Type`: application/json
   + `Accept`: application/json

    Postman을 사용하는 경우 `/keyPhrases` 엔드포인트를 가정하면 요청이 다음 스크린샷과 유사합니다.
    
    ![엔드포인트 및 헤더를 포함하는 요청 스크린샷](../media/postman-request-keyphrase-1.png)
    
4. **본문** 의 형식으로 **원시** 를 선택합니다.
    
    ![본문 설정을 포함하는 요청 스크린샷](../media/postman-request-body-raw.png)

5. 일부 JSON 문서를 유효한 형식으로 붙여넣습니다. 위의 **API 요청 형식** 섹션에 있는 예를 사용하고 자세한 정보와 예는 아래 항목을 참조하세요.

      + [언어 감지](text-analytics-how-to-language-detection.md)
      + [핵심 구 추출](text-analytics-how-to-keyword-extraction.md)
      + [감정 분석](text-analytics-how-to-sentiment-analysis.md)
      + [엔터티 인식](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>요청 보내기

API 요청을 제출합니다. 동기식 엔드포인트를 호출한 경우 응답은 요청에 제공된 각 문서 ID에 대한 항목과 함께 단일 JSON 문서로 즉시 표시됩니다.

비동기 `/analyze` 또는 `/health` 엔드포인트를 호출한 경우 202 응답 코드를 수신했는지 확인합니다. 결과를 보려면 응답을 수신해야 합니다.

1. API 응답의 헤더에서 API로 보낸 작업을 식별하는 `Operation-Location`을 찾습니다. 
2. 사용한 엔드포인트에 대한 GET 요청을 만듭니다. 엔드포인트 형식은 [위의 표](#set-up-a-request)를 참조하고 [API 참조 문서](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/AnalyzeStatus)를 검토합니다. 예를 들면 다음과 같습니다.

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1/analyze/jobs/<Operation-Location>`

3. 요청에 `Operation-Location`을 추가합니다.

4. 응답은 요청에 제공된 각 문서 ID에 대한 항목이 있는 단일 JSON 문서입니다.

비동기 `/analyze` 또는 `/health` 작업의 경우 위 2단계의 GET 요청 결과는 작업이 생성된 후 24시간 동안 사용할 수 있습니다.  이 시간은 GET 응답의 `expirationDateTime` 값으로 표시됩니다.  이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.    

## <a name="example-api-responses"></a>API 응답 예
 
# <a name="synchronous"></a>[동기](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>동기 작업에 대한 응답 예

동기식 엔드포인트 응답은 사용하는 엔드포인트에 따라 달라집니다. 응답 예는 다음 문서를 참조하세요.

+ [언어 감지](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [핵심 구 추출](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [감정 분석](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [엔터티 인식](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[비동기](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>비동기 작업에 대한 응답 예

성공하면 `/analyze` 엔드포인트에 대한 GET 요청에서 지정된 작업을 포함하는 개체를 반환합니다. 예: `keyPhraseExtractionTasks`. 이러한 작업에는 적절한 Text Analytics 기능의 응답 개체가 포함됩니다. 자세한 내용은 다음 문서를 참조하세요.

+ [핵심 구 추출](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [감정 분석](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [엔터티 인식](text-analytics-how-to-entity-linking.md#view-results)
+ [의료 분야 Text Analytics](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [모델 버전](../concepts/model-versioning.md)
* [FAQ(질문과 대답)](../text-analytics-resource-faq.yml)</br>
* [Text Analytics 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/client-libraries-rest-api.md)
* [새로운 기능](../whats-new.md)
