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
ms.custom: ignite-fall-2021
ms.openlocfilehash: 396cac23548eb951e47c4ec3a602aadcdfa304f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052941"
---
# <a name="migrate-from-version-2-of-the-text-analytics-api"></a>Text Analytics API 버전 2에서 마이그레이션

애플리케이션에서 Text Analytics API 버전 2.1을 사용하는 경우 이 문서는 최신 버전의 기능을 사용하도록 애플리케이션을 업그레이드하는 데 도움이 되며, 현재 [언어용 Azure Cognitive Service의](../overview.md)일부입니다.

## <a name="features"></a>기능

아래 기능 중 하나를 선택하여 애플리케이션을 업데이트하는 데 사용할 수 있는 정보를 확인합니다.

## <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment-analysis)

> [!TIP]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [감정 분석](../sentiment-opinion-mining/how-to/call-api.md) 방법 도움말 및 [빠른 시작](../sentiment-opinion-mining/quickstart.md)을 참조하세요. 

## <a name="feature-changes"></a>기능 변경 

버전 2.1의 감정 분석은 API에 전송된 각 문서에 대해 0~1 사이의 감정 점수를 반환하며, 점수가 1에 가까울수록 더 긍정적인 감정을 나타냅니다. 이 기능의 현재 버전은 문장과 문서 전체 및 관련 신뢰도 점수 모두에 대한 감정 레이블(예: "긍정" 또는 "부정")을 반환합니다. 

## <a name="steps-to-migrate"></a>마이그레이션 단계

### <a name="rest-api"></a>REST API

애플리케이션에서 REST API 사용하는 경우 감정 분석에 현재 엔드포인트를 사용하도록 요청 [엔드포인트를](../sentiment-opinion-mining/quickstart.md?pivots=rest-api) 업데이트합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`. 또한 [API의 응답](../sentiment-opinion-mining/how-to/call-api.md)에서 반환된 감정 레이블을 사용하려면 애플리케이션을 업데이트해야 합니다. 

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

### <a name="client-libraries"></a>클라이언트 라이브러리

감정 분석 클라이언트 라이브러리의 최신 버전을 사용하려면 네임스페이스에서 최신 소프트웨어 패키지를 다운로드해야 `Azure.AI.TextAnalytics` 합니다. [빠른 시작 문서에는](../sentiment-opinion-mining/quickstart.md) 기본 설정 언어에 사용할 수 있는 명령과 예제 코드가 나열됩니다.

## <a name="ner-and-entity-linking"></a>[NER 및 엔터티 링크 설정](#tab/named-entity-recognition)

> [!TIP]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 자세한 내용은 다음 문서를 참조하세요. NER:
> * [빠른 시작](../named-entity-recognition/quickstart.md)
> * [API를 호출하는 방법](../named-entity-recognition/how-to-call.md) 엔터티 연결
> * [빠른 시작](../entity-linking/quickstart.md)
> * [API를 호출하는 방법](../entity-linking/how-to/call-api.md)

## <a name="feature-changes"></a>기능 변경

버전 2.1에서 Text Analytics API는 NER(명명된 엔터티 인식) 및 엔터티 링크 설정에 대해 하나의 엔드포인트를 사용합니다. 이 기능의 현재 버전은 확장된 명명된 엔터티 검색을 제공하고 NER 및 엔터티 연결 요청에 별도의 엔드포인트를 사용합니다. 또한 언어 서비스에서 제공되는 다른 기능을 사용하여 [개인(pii) 및 상태(phi) 정보를 검색할](../personally-identifiable-information/overview.md)수 있습니다. 

## <a name="steps-to-migrate"></a>마이그레이션 단계

### <a name="rest-api"></a>REST API

애플리케이션에서 REST API 사용하는 경우 해당 요청 엔드포인트를 NER 및/또는 엔터티 링크에 대한 [현재 엔드포인트로](../named-entity-recognition/quickstart.md?pivots=rest-api) 업데이트합니다. 예:

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/general`

또한 [API의 응답](../named-entity-recognition/how-to-call.md)에서 반환된 [엔터티 범주](../named-entity-recognition/concepts/named-entity-categories.md)를 사용하려면 애플리케이션을 업데이트해야 합니다.

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/EntitiesRecognitionGeneral)

### <a name="client-libraries"></a>클라이언트 라이브러리

최신 버전의 NER 및 엔터티 연결 클라이언트 라이브러리를 사용하려면 네임스페이스에서 최신 소프트웨어 패키지를 다운로드해야 `Azure.AI.TextAnalytics` 합니다. [명명된 엔터티 인식](../named-entity-recognition/quickstart.md) 및 [엔터티 연결에](../entity-linking/quickstart.md) 대한 빠른 시작 문서에는 기본 설정 언어에 사용할 수 있는 명령과 예제 코드가 나와 있습니다.

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
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [언어 감지](../language-detection/how-to/call-api.md) 방법 도움말 및 [빠른 시작](../language-detection/quickstart.md)을 참조하세요. 

## <a name="feature-changes"></a>기능 변경 

언어 검색 기능 출력이 현재 버전에서 변경 되었습니다. JSON 응답에는 `score` 대신 `ConfidenceScore`가 포함됩니다. 또한 현재 버전은  `detectedLanguage` 각 문서에 대 한 특성에서 하나의 언어만 반환 합니다.

## <a name="steps-to-migrate"></a>마이그레이션 단계

### <a name="rest-api"></a>REST API

응용 프로그램에서 REST API 사용 하는 경우 해당 요청 끝점을 언어 검색에 대 한 [현재 끝점](../language-detection/quickstart.md?pivots=rest-api) 으로 업데이트 합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages`. 또한 [API의 응답](../language-detection/how-to/call-api.md)에서 `score` 대신 `ConfidenceScore`를 사용하도록 애플리케이션을 업데이트해야 합니다. 

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>클라이언트 라이브러리

최신 버전의 감정 analysis client 라이브러리를 사용 하려면 네임 스페이스에서 최신 소프트웨어 패키지를 다운로드 해야 합니다 `Azure.AI.TextAnalytics` . [빠른 시작 문서](../language-detection/quickstart.md) 에는 기본 설정 언어에 사용할 수 있는 명령과 예제 코드가 나열 되어 있습니다.

## <a name="key-phrase-extraction"></a>[핵심 구 추출](#tab/key-phrase-extraction)

> [!TIP]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [핵심 구 추출](../key-phrase-extraction/how-to/call-api.md) 방법 도움말 및 [빠른 시작](../key-phrase-extraction/quickstart.md)을 참조하세요. 

## <a name="feature-changes"></a>기능 변경 

핵심 구 추출 기능은 현재 끝점 버전 외부에서 변경 되지 않았습니다.

## <a name="steps-to-migrate"></a>마이그레이션 단계

### <a name="rest-api"></a>REST API

응용 프로그램에서 REST API 사용 하는 경우 키 구 추출을 위해 요청 끝점을 [현재 끝점](../key-phrase-extraction/quickstart.md?pivots=rest-api) 으로 업데이트 합니다. 예: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.1/keyPhrases`

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

### <a name="client-libraries"></a>클라이언트 라이브러리

최신 버전의 감정 analysis client 라이브러리를 사용 하려면 네임 스페이스에서 최신 소프트웨어 패키지를 다운로드 해야 합니다 `Azure.AI.TextAnalytics` . [빠른 시작 문서](../key-phrase-extraction/quickstart.md) 에는 기본 설정 언어에 사용할 수 있는 명령과 예제 코드가 나열 되어 있습니다.

---

## <a name="see-also"></a>참고 항목

* [언어에 대 한 Azure 인지 서비스는 무엇 인가요?](../overview.md)
