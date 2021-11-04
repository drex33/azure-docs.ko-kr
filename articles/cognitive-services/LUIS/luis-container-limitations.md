---
title: 컨테이너 제한 사항 - LUIS
titleSuffix: Azure Cognitive Services
description: 지원되는 LUIS 컨테이너 언어입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: aahi
ms.openlocfilehash: cd29ef48f2d1042091a1379570e8ea710c718ff9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426225"
---
# <a name="language-understanding-luis-container-limitations"></a>LUIS(Language Understanding) 컨테이너 제한 사항

LUIS 컨테이너에는 몇 가지 주목할 만한 제한 사항이 있습니다. 지원되지 않는 종속성부터 지원되는 언어의 하위 집합까지 이 문서에서는 이러한 제한 사항에 대해 자세히 설명합니다.

## <a name="supported-dependencies-for-latest-container"></a>`latest` 컨테이너에 대해 지원되는 종속성

최신 LUIS 컨테이너는 다음을 지원합니다.

* [미리 빌드된 새 도메인](luis-reference-prebuilt-domains.md): 이러한 엔터프라이즈 중심 도메인에는 엔터티, 예제 발화 및 패턴이 포함됩니다. 사용자 용도에 맞게 이러한 도메인을 확장합니다.

## <a name="unsupported-dependencies-for-latest-container"></a>`latest` 컨테이너에 대해 지원되지 않는 종속성

[컨테이너를 내보내려면](luis-container-howto.md#export-packaged-app-from-luis) LUIS 앱에서 지원되지 않는 종속성을 제거해야 합니다. 컨테이너에 대해 내보내기를 시도하면 LUIS 포털에서 제거해야 하는 지원되지 않는 이러한 기능을 보고합니다.

다음 종속성 중 하나도 **포함하지 않는** 경우 LUIS 애플리케이션을 사용할 수 있습니다.

지원되지 않는 앱 구성|세부 정보|
|--|--|
|지원되지 않는 컨테이너 문화권| 네덜란드어(`nl-NL`), 일본어(`ja-JP`) 및 독일어(`de-DE`)는 [1.0.2 토크나이저](luis-language-support.md#custom-tokenizer-versions)에서만 지원됩니다.|
|모든 문화권에 지원되지 않는 엔터티|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) 모든 문화권에 미리 빌드된 엔터티|
|영어(`en-US`) 문화권에 지원되지 않는 엔터티|[GeographyV2](luis-reference-prebuilt-geographyV2.md) 미리 빌드된 엔터티|
|음성 초기화|컨테이너에서 외부 종속성은 지원되지 않습니다.|
|정서 분석|컨테이너에서 외부 종속성은 지원되지 않습니다.|
|Bing 맞춤법 검사|컨테이너에서 외부 종속성은 지원되지 않습니다.|

## <a name="languages-supported"></a>지원되는 언어

LUIS 컨테이너는 LUIS에서 [지원되는 언어](luis-language-support.md#languages-supported)의 하위 집합을 지원합니다. LUIS 컨테이너는 다음 언어로 된 발화를 이해할 수 있습니다.

| 언어 | Locale | 미리 빌드된 도메인 | 미리 빌드된 엔터티 | 문구 목록 권장 사항 | **[감정 분석](../language-service/sentiment-opinion-mining/language-support.md) 및 [핵심 구 추출](../language-service/key-phrase-extraction/language-support.md)|
|--|--|:--:|:--:|:--:|:--:|
| 영어(미국) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| 아랍어(미리 보기 - 최신 표준 아랍어) |`ar-AR`|❌|❌|❌|❌|
| *[중국어](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| 프랑스어(프랑스) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| 프랑스어(캐나다) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| 독일어 |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| 힌디어 | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| 이탈리아어 |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| 한국어 |`ko-KR` | ✔️ | ❌ | ❌ | *주요 구문* 만 |
| 마라티어 | `mr-IN`|❌|❌|❌|❌|
| 포르투갈어(브라질) |`pt-BR` | ✔️ | ✔️ | ✔️ | 일부 하위 문화권은 아님 |
| 스페인어(스페인) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| 스페인어(멕시코)|`es-MX` | ❌ | ❌ |✔️|✔️|
| 타밀어 | `ta-IN`|❌|❌|❌|❌|
| 텔루구어 | `te-IN`|❌|❌|❌|❌|
| 터키어 | `tr-TR` |✔️| ❌ | ❌ | *감정* 만 |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Language service support notes](includes/text-analytics-support-notes.md)]
