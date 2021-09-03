---
title: Text Analytics API의 v2에 대한 마이그레이션 가이드
titleSuffix: Azure Cognitive Services
description: Text Analytics API 버전 3을 사용하도록 애플리케이션을 이동하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/06/2021
ms.author: aahi
ms.openlocfilehash: e10c1f8622e1bbb60a3fbb4f5d6fd5bd3da46255
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550447"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Text Analytics API 버전 3.x로 마이그레이션

Text Analytics API 버전 2.1을 사용하는 경우 이 문서를 참조하여 버전 3.x를 사용하도록 애플리케이션을 업그레이드할 수 있습니다. 버전 3.1 및 3.0은 일반적으로 사용할 수 있으며 확장된 [NER(명명된 엔터티 인식)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) 및 [모델 버전 관리](concepts/model-versioning.md)와 같은 새로운 기능을 소개합니다. [오피니언 마이닝](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) 및 [프라이빗 식별 정보](how-tos/text-analytics-how-to-entity-linking.md?tabs=version-3-1#personally-identifiable-information-pii) 감지와 같은 기능이 추가된 v3.1 버전도 사용할 수 있습니다. v2 또는 3.1-preview.x에서 사용된 모델은 향후 업데이트를 받지 않습니다. 

## <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment-analysis)

> [!TIP]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md) 방법 도움말 및 [빠른 시작](quickstarts/client-libraries-rest-api.md)을 참조하세요. 

### <a name="feature-changes"></a>기능 변경 

버전 2.1의 감정 분석은 API에 전송된 각 문서에 대해 0~1 사이의 감정 점수를 반환하며, 점수가 1에 가까울수록 더 긍정적인 감정을 나타냅니다. 대신 버전 3에서는 문장과 문서 전체에 대한 감정 레이블(예: "긍정적" 또는 "부정적")과 관련된 신뢰도 점수를 반환합니다. 

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

애플리케이션에서 REST API를 사용하는 경우 감정 분석을 위해 해당 요청 엔드포인트를 v3 엔드포인트로 업데이트합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`. 또한 [API의 응답](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)에서 반환된 감정 레이블을 사용하려면 애플리케이션을 업데이트해야 합니다. 

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER 및 엔터티 링크 설정](#tab/named-entity-recognition)

> [!TIP]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [NER 및 엔터티 연결](how-tos/text-analytics-how-to-entity-linking.md) 방법 도움말 및 [빠른 시작](quickstarts/client-libraries-rest-api.md)을 참조하세요. 

### <a name="feature-changes"></a>기능 변경

버전 2.1에서 Text Analytics API는 NER(명명된 엔터티 인식) 및 엔터티 링크 설정에 대해 하나의 엔드포인트를 사용합니다. 버전 3은 확장된 명명된 엔터티 검색을 제공하고 NER 및 엔터티 링크 설정 요청에 대해 별도의 엔드포인트를 사용합니다. v3.1에서 NER은 개인 `pii` 및 상태 `phi` 정보를 추가로 검색할 수 있습니다. 

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

애플리케이션에서 REST API를 사용하는 경우 NER 및/또는 엔터티 링크 설정을 위해 해당 요청 엔드포인트를 v3 엔드포인트로 업데이트합니다.

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/general`

또한 [API의 응답](how-tos/text-analytics-how-to-entity-linking.md#view-results)에서 반환된 [엔터티 범주](named-entity-types.md)를 사용하려면 애플리케이션을 업데이트해야 합니다.

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="version-21-entity-categories"></a>버전 2.1 엔터티 범주

다음 표에는 NER v2.1에 대해 반환된 엔터티 범주가 나열되어 있습니다.

| 범주   | 설명                          |
|------------|--------------------------------------|
| 사람   |   사용자의 이름.  |
|Location    | 자연 및 인간이 만든 랜드마크, 구조, 지리적 기능 및 지정학적 엔터티입니다. |
|조직 | 회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관 및 공공 단체입니다. 국적 및 종교는 이 엔터티 형식에 포함되지 않습니다. |
| PhoneNumber | 전화 번호(미국과 유럽 전화 번호만 해당). |
| Email | 이메일 주소. |
| URL | 웹 사이트에 대한 URL입니다. |
| IP | 네트워크 IP 주소. |
| DateTime | 날짜 및 하루 중 시간.| 
| Date | 달력 날짜. |
| 시간 | 하루 중 시간 |
| DateRange | 날짜 범위. |
| TimeRange | 시간 범위. |
| Duration | 기간 |
| 설정 | 반복 횟수를 설정합니다. |
| 수량 | 숫자 및 숫자 수량. |
| 숫자 | 숫자 |
| 백분율 | 백분율.|
| 서수 | 서수 번호. |
| 나이 | 나이. |
| 통화 | 통화. |
| 차원 | 차원 및 측정. |
| 온도 | 온도. |

## <a name="language-detection"></a>[언어 감지](#tab/language-detection)

> [!TIP]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [언어 감지](how-tos/text-analytics-how-to-language-detection.md) 방법 도움말 및 [빠른 시작](quickstarts/client-libraries-rest-api.md)을 참조하세요. 

### <a name="feature-changes"></a>기능 변경 

언어 감지 기능 출력이 v3에서 변경되었습니다. JSON 응답에는 `score` 대신 `ConfidenceScore`가 포함됩니다. 또한 V3는 각 문서의 `detectedLanguage` 특성에서 하나의 언어만 반환합니다.

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

애플리케이션에서 REST API를 사용하는 경우 언어 감지를 위해 해당 요청 엔드포인트를 v3 엔드포인트로 업데이트합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages`. 또한 [API의 응답](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)에서 `score` 대신 `ConfidenceScore`를 사용하도록 애플리케이션을 업데이트해야 합니다. 

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[핵심 구 추출](#tab/key-phrase-extraction)

> [!TIP]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md) 방법 도움말 및 [빠른 시작](quickstarts/client-libraries-rest-api.md)을 참조하세요. 

### <a name="feature-changes"></a>기능 변경 

핵심 구 추출 기능이 엔드포인트 버전 외부의 v3에서 변경되지 않았습니다.

### <a name="steps-to-migrate"></a>마이그레이션 단계

#### <a name="rest-api"></a>REST API

애플리케이션에서 REST API를 사용하는 경우 핵심 구 추출을 위해 해당 요청 엔드포인트를 v3 엔드포인트로 업데이트합니다. 예: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>클라이언트 라이브러리

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>참고 항목

* [Text Analytics API란?](overview.md)
* [언어 지원](language-support.md)
* [모델 버전 관리](concepts/model-versioning.md)
