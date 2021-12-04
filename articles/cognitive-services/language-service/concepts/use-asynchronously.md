---
title: '방법: 언어 서비스 기능을 비동기적으로 사용'
titleSuffix: Azure Cognitive Services
description: 언어 서비스 API 요청을 비동기적으로 보내는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 12/03/2021
ms.author: aahi
ms.openlocfilehash: 8b41e5ddee43757509b130971c2e8e8fbb905ea0
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133546465"
---
# <a name="how-to-use-language-service-features-asynchronously"></a>언어 서비스 기능을 비동기적으로 사용하는 방법

언어 서비스를 사용하면 REST API 또는 클라이언트 라이브러리를 사용하여 API 요청을 비동기적으로 보낼 수 있습니다. 데이터에 대해 동시에 수행할 여러 언어 서비스 기능을 요청에 포함할 수도 있습니다. 

현재 다음 기능을 비동기적으로 사용할 수 있습니다.
* 엔터티 연결
* 추출 요약
* 핵심 문구 추출
* 언어 감지
* NER(명명된 엔터티 인식)
* PII(개인 식별 정보) 검색
* 감정 분석 및 오피니언 마이닝
* Text Analytics for health

## <a name="send-asynchronous-api-requests-using-the-rest-api"></a>REST API 사용하여 비동기 API 요청 보내기

비동기 API 요청을 만들려면 요청에 보낼 JSON 본문에 대한 [참조 설명서를](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Analyze) 검토합니다.
1. 개체에 문서를 `analysisInput` 추가합니다.  
1. `tasks`개체에 데이터에 대해 수행하려는 작업을 포함합니다. 예를 들어 감정 분석을 수행하려는 경우 `SentimentAnalysisTasks` 개체를 포함합니다.
1. 필요에 따라 다음을 수행할 수 있습니다.
    1. 값이 있는 데이터에 사용되는 모델의 특정 버전을 `model-version` 선택합니다.
    1. 데이터에 대해 동시에 수행할 추가 언어 서비스 기능을 `tasks` 개체에 포함합니다.   

요청에 대한 JSON 본문을 만든 후 헤더에 키를 `Ocp-Apim-Subscription-Key` 추가합니다. 그런 다음, 엔드포인트에 API 요청을 보냅니다. `/analyze`

```http
https://your-endpoint/text/analytics/v3.1/analyze
```

호출이 성공하면 202 응답 코드가 반환됩니다. 응답 헤더의 는 `operation-location` API 결과를 검색하는 데 사용할 URL입니다. 이 값은 다음 URL과 유사합니다.

```http
https://your-endpoint.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/12345678-1234-1234-1234-12345678
```

요청 [결과를 검색하려면](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/AnalyzeStatus) GET 요청을 `operation-location` 이전 API 응답의 헤더에서 받은 URL로 보냅니다. 에 키를 포함해야 `Ocp-Apim-Subscription-Key` 합니다. 응답에는 API 호출의 결과가 포함됩니다.

## <a name="send-asynchronous-api-requests-using-the-client-library"></a>클라이언트 라이브러리를 사용하여 비동기 API 요청 보내기

먼저 선택한 언어에 맞게 클라이언트 라이브러리가 설치되어 있는지 확인합니다. 클라이언트 라이브러리를 설치하는 단계는 사용하려는 기능에 대한 빠른 시작 문서를 참조하세요.

그런 다음 클라이언트 개체를 사용하여 API에 비동기 호출을 보냅니다. 사용할 메서드 호출은 언어에 따라 달라집니다. 사용 가능한 샘플 및 참조 설명서를 사용하여 시작하는 데 도움을 주세요.

* [C#](/dotnet/api/overview/azure/ai.textanalytics-readme?preserve-view=true&view=azure-dotnet#async-examples)
* [Java](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview#analyze-multiple-actions)
* [JavaScript](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview#analyze-actions)
* [Python](/python/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-python-preview#multiple-analysis)

## <a name="result-availability"></a>결과 가용성 

이 기능을 비동기적으로 사용하는 경우 요청이 수집된 시간부터 24시간 동안 API 결과를 사용할 수 있으며 응답에 표시됩니다. 이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

[StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정된 대로 비동기 요청에 포함된 모든 문서에서 최대 125,000자를 보낼 수 있습니다. 이 문자 제한은 더 높은 처리량을 사용할 수 있도록 동기 요청에 대한 제한보다 높습니다. 

문서가 문자 제한을 초과하면 API는 전체 요청을 거부하고 그 `400 bad request` 안의 문서가 최대 크기를 초과하는 경우 오류를 반환합니다.

## <a name="see-also"></a>참조

* [Azure Cognitive Service for Language 개요](../overview.md)
* [다국어 및 이모지 지원](../concepts/multilingual-emoji-support.md)
* [새로운 기능](../whats-new.md)