---
title: 최신 버전의 Azure Cognitive Service for Language로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 최신 버전의 언어 서비스를 사용하도록 Text Analytics 애플리케이션을 이동하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/03/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: e2320a8b3deaafc9d1c470fc25fb23b72efbb04e
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133546473"
---
# <a name="migrate-to-the-latest-version-of-azure-cognitive-service-for-language"></a>최신 버전의 Azure Cognitive Service for Language로 마이그레이션

> [!TIP]
> 언어용 Azure Cognitive Service를 방금 시작하세요? 서비스, 사용 가능한 기능 및 첫 번째 API 요청을 보내기 위한 빠른 시작 링크에 대한 자세한 내용은 [개요 문서를](../overview.md) 참조하세요. 

애플리케이션이 이전 버전의 Text Analytics API(v3.1 이전) 또는 클라이언트 라이브러리(안정적인 v5.1.0 이전)를 사용하는 경우 이 문서는 최신 버전의 [Azure Cognitive Service를 언어](../overview.md) 기능에 사용하도록 애플리케이션을 업그레이드하는 데 도움이 됩니다.

## <a name="features"></a>기능

아래 기능 중 하나를 선택하여 애플리케이션을 업데이트하는 데 사용할 수 있는 정보를 확인합니다.

## <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment-analysis)

> [!NOTE]
> * 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [감정 분석](../sentiment-opinion-mining/how-to/call-api.md) 방법 도움말 및 [빠른 시작](../sentiment-opinion-mining/quickstart.md)을 참조하세요. 
> * 버전 `3.1-preview.x` REST API 엔드포인트 및 `5.1.0-beta.x` 클라이언트 라이브러리는 더 이상 사용되지 않습니다.

## <a name="feature-changes-from-version-21"></a>버전 2.1에서 기능 변경

버전 2.1의 감정 분석은 API에 전송된 각 문서에 대해 0~1 사이의 감정 점수를 반환하며, 점수가 1에 가까울수록 더 긍정적인 감정을 나타냅니다. 이 기능의 현재 버전은 문장과 문서 전체 및 관련 신뢰도 점수 모두에 대한 감정 레이블(예: "긍정" 또는 "부정")을 반환합니다.

## <a name="migrate-to-the-current-version"></a>현재 버전으로 마이그레이션

### <a name="rest-api"></a>REST API

애플리케이션에서 REST API 사용하는 경우 감정 분석에 현재 엔드포인트를 사용하도록 요청 [엔드포인트를](../sentiment-opinion-mining/quickstart.md?pivots=rest-api) 업데이트합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment`. 또한 [API의 응답](../sentiment-opinion-mining/how-to/call-api.md)에서 반환된 감정 레이블을 사용하려면 애플리케이션을 업데이트해야 합니다. 

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

### <a name="client-libraries"></a>클라이언트 라이브러리

감정 분석 클라이언트 라이브러리의 최신 버전을 사용하려면 네임스페이스에서 최신 소프트웨어 패키지를 다운로드해야 `Azure.AI.TextAnalytics` 합니다. [빠른 시작 문서에는](../sentiment-opinion-mining/quickstart.md) 기본 설정 언어에 사용할 수 있는 명령과 예제 코드가 나와 있습니다.

[!INCLUDE [SDK target versions](../includes/sdk-target-versions.md)]

## <a name="ner-pii-and-entity-linking"></a>[NER, PII 및 엔터티 링크](#tab/named-entity-recognition)

> [!NOTE]
> 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 자세한 내용은 다음 문서를 참조하세요.
>
> * [NER 빠른 시작](../named-entity-recognition/quickstart.md)
> * [엔터티 연결 빠른 시작](../entity-linking/quickstart.md)
> * [PII(개인 식별 정보) 검색 빠른 시작](../personally-identifiable-information/quickstart.md)
>
> 버전 `3.1-preview.x` REST API 엔드포인트 및 `5.1.0-beta.x` 클라이언트 라이브러리는 더 이상 사용되지 않습니다.

## <a name="feature-changes-from-version-21"></a>버전 2.1에서 기능 변경

버전 2.1에서 Text Analytics API는 NER(명명된 엔터티 인식) 및 엔터티 링크 설정에 대해 하나의 엔드포인트를 사용합니다. 이 기능의 현재 버전은 확장된 명명된 엔터티 검색을 제공하고 NER 및 엔터티 연결 요청에 별도의 엔드포인트를 사용합니다. 또한 언어 서비스에서 제공되는 다른 기능을 사용하여 [개인(pii) 및 상태(phi) 정보를 검색할](../personally-identifiable-information/overview.md)수 있습니다.

## <a name="migrate-to-the-current-version"></a>현재 버전으로 마이그레이션

### <a name="rest-api"></a>REST API

애플리케이션에서 REST API 사용하는 경우 해당 요청 엔드포인트를 NER 및/또는 엔터티 링크에 대한 [현재 엔드포인트로](../named-entity-recognition/quickstart.md?pivots=rest-api) 업데이트합니다. 예를 들면 다음과 같습니다.

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

[!INCLUDE [SDK target versions](../includes/sdk-target-versions.md)]

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

> [!NOTE]
> * 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [언어 감지](../language-detection/how-to/call-api.md) 방법 도움말 및 [빠른 시작](../language-detection/quickstart.md)을 참조하세요. 
> * 버전 `3.1-preview.x` REST API 엔드포인트 및 `5.1.0-beta.x` 클라이언트 라이브러리는 더 이상 사용되지 않습니다.

## <a name="feature-changes-from-version-21"></a>버전 2.1에서 기능 변경

언어 감지 기능 출력이 현재 버전에서 변경되었습니다. JSON 응답에는 `score` 대신 `ConfidenceScore`가 포함됩니다. 또한 현재 버전은 각 문서에 대한 특성에서 하나의 언어만  `detectedLanguage` 반환합니다.

## <a name="migrate-to-the-current-version"></a>현재 버전으로 마이그레이션

### <a name="rest-api"></a>REST API

애플리케이션에서 REST API 사용하는 경우 언어 감지를 위해 요청 [엔드포인트를 현재 엔드포인트로](../language-detection/quickstart.md?pivots=rest-api) 업데이트합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages`. 또한 [API의 응답](../language-detection/how-to/call-api.md)에서 `score` 대신 `ConfidenceScore`를 사용하도록 애플리케이션을 업데이트해야 합니다. 

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>클라이언트 라이브러리

감정 분석 클라이언트 라이브러리의 최신 버전을 사용하려면 네임스페이스에서 최신 소프트웨어 패키지를 다운로드해야 `Azure.AI.TextAnalytics` 합니다. [빠른 시작 문서에는](../language-detection/quickstart.md) 기본 설정 언어에 사용할 수 있는 명령과 예제 코드가 나와 있습니다.

[!INCLUDE [SDK target versions](../includes/sdk-target-versions.md)]

## <a name="key-phrase-extraction"></a>[핵심 구 추출](#tab/key-phrase-extraction)

> [!NOTE]
> * 애플리케이션에서 최신 버전의 API를 사용하려고 하나요? API의 현재 버전에 대한 정보는 [핵심 구 추출](../key-phrase-extraction/how-to/call-api.md) 방법 도움말 및 [빠른 시작](../key-phrase-extraction/quickstart.md)을 참조하세요. 
> * 버전 `3.1-preview.x` REST API 엔드포인트 및 `5.1.0-beta.x` 클라이언트 라이브러리는 더 이상 사용되지 않습니다.

## <a name="feature-changes-from-version-21"></a>버전 2.1에서 기능 변경

핵심 구 추출 기능은 현재 엔드포인트 버전 외부에서 변경되지 않았습니다.

## <a name="migrate-to-the-current-version"></a>현재 버전으로 마이그레이션

### <a name="rest-api"></a>REST API

애플리케이션에서 REST API 사용하는 경우 핵심 구 추출을 위해 해당 요청 [엔드포인트를 현재 엔드포인트로](../key-phrase-extraction/quickstart.md?pivots=rest-api) 업데이트합니다. 예: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.1/keyPhrases`

JSON 응답의 예제는 참조 설명서를 참조하세요.
* [버전 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [버전 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

### <a name="client-libraries"></a>클라이언트 라이브러리

감정 분석 클라이언트 라이브러리의 최신 버전을 사용하려면 네임스페이스에서 최신 소프트웨어 패키지를 다운로드해야 `Azure.AI.TextAnalytics` 합니다. [빠른 시작 문서에는](../key-phrase-extraction/quickstart.md) 기본 설정 언어에 사용할 수 있는 명령과 예제 코드가 나와 있습니다.

[!INCLUDE [SDK target versions](../includes/sdk-target-versions.md)]

---

## <a name="see-also"></a>참조

* [언어용 Azure Cognitive Service란?](../overview.md)
