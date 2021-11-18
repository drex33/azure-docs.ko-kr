---
title: 감정 분석 및 오피니언 마이닝 수행 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 감정을 탐지하고 텍스트에서 의견을 찾는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: e94918baac4946194e2382c3a336bdd52c510f50
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713576"
---
# <a name="how-to-use-sentiment-analysis-and-opinion-mining"></a>방법: 감정 분석 및 오피니언 마이닝 사용 

감정 분석과 오피니언 마이닝은 긍정적인 감정과 부정적인 감정을 탐지하는 두 가지 방법입니다. 감정 분석을 사용하면 문장 및 문서 수준에서 감정 레이블(예: "부정", "중립"및 "긍정")과 신뢰도 점수를 얻을 수 있습니다. Opinion Mining은 텍스트의 단어(예: 제품 또는 서비스의 특성)와 관련된 의견에 대한 세부 정보를 제공합니다.

> [!TIP]
> 이 기능을 사용하려면 [빠른 시작 도움말](../quickstart.md)에 따라 시작합니다. 코드를 작성할 필요 없이 [Language Studio](../../language-studio.md)를 사용하여 예시 요청을 만들 수도 있습니다.


## <a name="sentiment-analysis"></a>감정 분석

감정 분석은 텍스트에 감정 레이블을 적용합니다. 이는 문장과 문서 각각에 대한 신뢰도 점수와 함께 문장과 문서 수준에서 반환됩니다. 

레이블은 *긍정*, *부정* 및 *중립* 입니다. 문서 수준에서 *혼합된* 감정 레이블을 반환할 수도 있습니다. 문서의 감정은 다음과 같이 결정됩니다.

| 문장 감정                                                                            | 반환된 문서 레이블 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 문서에 `positive` 문장이 하나 이상 있습니다. 나머지 문장은 `neutral`입니다. | `positive`              |
| 문서에 `negative` 문장이 하나 이상 있습니다. 나머지 문장은 `neutral`입니다. | `negative`              |
| 문서에 `negative` 문장과 `positive` 문장이 각각 하나 이상 있습니다.    | `mixed`                 |
| 문서의 모든 문장이 `neutral`입니다.                                                  | `neutral`               |

신뢰도 점수의 범위는 1에서 0까지입니다. 점수가 1에 가까울수록 레이블 분류에 대한 높은 신뢰도를 의미하며, 점수가 낮을수록 낮은 신뢰도를 의미합니다. 각 문서 또는 각 문장에 대해 레이블(긍정, 부정 및 중립)과 관련된 예측 점수는 최대 1까지 추가됩니다. 자세한 내용은 [책임 있는 AI 투명도 참고](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context)를 참조하세요. 

## <a name="opinion-mining"></a>오피니언 마이닝

오피니언 마이닝은 감정 분석의 기능입니다. NLP(자연어 처리)의 양상 기반 감정 분석이라고도 하는 이 기능은 텍스트의 제품 또는 서비스 특성과 관련된 의견에 대한 더 세부적인 정보를 제공합니다. API는 의견을 대상(명사 또는 동사) 및 평가(형용사)로 표시합니다.

예를 들어 고객이 호텔에 대한 피드백을 "객실은 훌륭했어요. 그런데 직원이 불친절했어요"라고 남기면 오피니언 마이닝은 텍스트의 대상(양상)과 관련 평가(의견) 및 감정을 찾습니다. 감정 분석은 부정적인 감정만 보고할 수 있습니다.

:::image type="content" source="../media/opinion-mining.png" alt-text="오피니언 마이닝 다이어그램 예" lightbox="../media/opinion-mining.png":::

REST API를 사용하는 경우 결과에 Opinion Mining을 가져오려면 감정 분석 요청에 `opinionMining=true` 플래그를 포함해야 합니다. 오피니언 마이닝 결과는 감정 분석 응답에 포함됩니다. 의견 마이닝은 감정 분석의 확장이며 현재 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)에 포함됩니다.

## <a name="determine-how-to-process-the-data-optional"></a>데이터 처리 방법 결정(선택 사항)

### <a name="specify-the-sentiment-analysis-model"></a>감정 분석 모델 지정

기본적으로 감정 분석은 텍스트에서 사용 가능한 최신 AI 모델을 사용합니다. 특정 모델 버전을 사용하도록 API 요청을 구성할 수도 있습니다. 지정한 모델은 감정 분석 작업을 수행하는 데 사용됩니다.

| 지원되는 버전 | 최신 일반 공급 버전 | 최신 미리 보기 버전 |
|--|--|--|
| `2019-10-01`, `2020-04-01`, `2021-10-01-preview` | `2020-04-01`   | `2021-10-01-preview`   |

### <a name="using-a-preview-model-version"></a>미리 보기 모델 버전 사용

API 호출에서 미리 보기 모델 버전을 사용하려면 모델 버전 매개 변수를 사용하여 모델 버전을 지정해야 합니다. 예를 들어 Python을 사용하여 요청을 보내는 경우:

```python
result = text_analytics_client.analyze_sentiment(documents, show_opinion_mining=True, model_version="2021-10-01-preview")
```

또는 REST API를 사용하는 경우:

```rest
https://your-resource-name.cognitiveservices.azure.com/text/analytics/v3.1/sentiment?opinionMining=true&model-version=2021-10-01-preview
```

자세한 내용은 참조 설명서를 참조하세요.
* [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)
* [.NET](/dotnet/api/azure.ai.textanalytics.analyzesentimentaction?view=azure-dotnet#properties)
* [Python](/python/api/azure-ai-textanalytics/azure.ai.textanalytics.textanalyticsclient?view=azure-python#analyze-sentiment-documents----kwargs-)
* [Java](/java/api/com.azure.ai.textanalytics.models.analyzesentimentoptions.setmodelversion?view=azure-java-stable#com_azure_ai_textanalytics_models_AnalyzeSentimentOptions_setModelVersion_java_lang_String_)
* [JavaScript](/javascript/api/@azure/ai-text-analytics/analyzesentimentoptions?view=azure-node-latest)

### <a name="input-languages"></a>입력 언어

감정 분석으로 처리할 문서를 제출할 때 문서가 작성되는 [지원되는 언어](../language-support.md)를 지정할 수 있습니다. 언어를 지정하지 않으면 감정 분석은 기본적으로 영어로 설정됩니다. API는 다양한 [다국어 및 그림 이모티콘 인코딩](../../concepts/multilingual-emoji-support.md)을 지원하기 위해 응답에 오프셋을 반환할 수 있습니다. 

## <a name="submitting-data"></a>데이터 제출

감정 분석 및 오피니언 마이닝은 작업할 텍스트의 양을 줄이면 더 높은 품질의 결과를 생성합니다. 이는 더 큰 텍스트 블록에서 더 잘 수행되는 핵심 구 추출과 같은 일부 기능과 반대입니다. 

API 요청을 보내려면 언어 리소스 엔드포인트와 키가 필요합니다.

> [!NOTE]
> Azure Portal에서 언어 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다. 

요청을 받으면 분석이 수행됩니다. 분 및 초당 보낼 수 있는 요청의 크기와 수에 대한 정보는 아래의 데이터 제한 섹션을 참조하세요.

감정 분석 및 오피니언 마이닝 기능을 동기적으로 사용하는 것은 상태 비저장입니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

기능을 비동기식으로 사용하는 경우 API 결과는 요청이 수집된 시점부터 24시간 동안 사용할 수 있으며 응답에 표시됩니다. 이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.

## <a name="getting-sentiment-analysis-and-opinion-mining-results"></a>감정 분석 및 오피니언 마이닝 결과 가져오기

API에서 결과를 수신하면 반환되는 핵심 구의 순서는 모델에 의해 내부적으로 결정됩니다. 결과를 애플리케이션으로 스트리밍하거나 출력을 로컬 시스템의 파일에 저장할 수 있습니다.

감정 분석은 전체 문서와 그 안에 포함된 각 문장의 감정 레이블과 신뢰도 점수를 반환합니다. 점수가 1에 가까울수록 레이블 분류에 대한 높은 신뢰도를 의미하며, 점수가 낮을수록 낮은 신뢰도를 의미합니다. 문서에는 여러 문장이 있을 수 있으며, 각 문서 또는 문장 내 신뢰도 점수의 합계는 1입니다.

오피니언 마이닝은 텍스트에서 대상(명사 또는 동사)과 관련 평가(형용사)를 찾습니다. 예를 들어, "*식당의 음식은 훌륭했고 웨이터도 친절했어요*"라는 문장에는 *음식* 과 *웨이터* 라는 두 개의 대상이 있습니다. 각 대상에는 평가가 있습니다. 예를 들어 *음식* 에 대한 평가는 *훌륭한* 것이고 *서버* 에 대한 평가는 *친절한* 것입니다.

API는 의견을 대상(명사 또는 동사) 및 평가(형용사)로 반환합니다.


## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기(동기) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 5,120자.  |
| 요청당 최대 문자 수(비동기) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정되는 모든 제출된 문서에서 125K 문자를 계산합니다.  |
| 전체 요청의 최대 크기 | 1MB |
| 요청당 최대 문서 수 | 10 |

문서가 글자수 제한을 초과하는 경우 API는 사용 중인 기능에 따라 다르게 작동합니다.

* 비동기:
  * API는 전체 요청을 거부하고 그 안의 문서가 최대 크기를 초과하는 경우 `400 bad request` 오류를 반환합니다.
* 동기:  
  * API는 최대 크기를 초과하는 문서를 처리하지 않으며 잘못된 문서 오류를 반환합니다. API 요청에 여러 문서가 있는 경우 문자 제한 내에 있으면 API가 문서를 계속 처리합니다.

단일 요청으로 보낼 수 있는 최대 문서 수를 초과하면 HTTP 400 오류 코드가 생성됩니다.

### <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 달라집니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>참고 항목

* [감정 분석 및 오피니언 마이닝 개요](../overview.md)