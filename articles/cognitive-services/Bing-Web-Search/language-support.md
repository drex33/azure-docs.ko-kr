---
title: 언어 지원 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API에서 지원되는 자연 언어, 국가 및 지역 목록입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f1ffad8a0d5741ad25c4dd92ec9917d8b845bcd7
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913854"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Bing Web Search API에 대한 언어 및 지역 지원

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Web Search API는 수십 개의 국가 또는 지역을 지원하며, 이 중에는 둘 이상의 언어를 사용하는 국가/지역도 많습니다. 쿼리를 통한 국가 또는 지역 지정은 해당 국가 또는 지역의 관심 분야에 따라 검색 결과를 구체화하는 데 유용합니다. 결과는 Bing에 대한 링크를 포함할 수 있으며 이러한 링크는 지정된 국가/지역 또는 언어에 따라 Bing 사용자 경험을 지역화할 수 있습니다.

`cc` 쿼리 매개 변수를 사용하여 국가 또는 지역을 지정할 수 있습니다. 국가 또는 지역이 지정된 경우 [`Accept-Language` 헤더](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)를 사용하여 하나 이상의 언어 코드를 지정해야 합니다. 각 시장에서 지원되는 언어 목록에 대한 [시장 테이블](#markets)을 사용합니다.

또는 **시장** 테이블에서 `mkt` 쿼리 매개 변수 및 코드를 사용하여 시장을 지정할 수 있습니다. 시장을 지정하면 동시에 국가 또는 지역 및 기본 언어가 지정됩니다. `setLang` 쿼리 매개 변수로 언어를 명시적으로 설정할 수 있습니다.

## <a name="countriesregions"></a>국가/지역

|국가/지역|코드|
|-------|----|
|아르헨티나|AR|
|오스트레일리아|AU|
|오스트리아|AT|
|벨기에|BE|
|브라질|BR|
|Canada|CA|
|칠레|CL|
|덴마크|DK|
|핀란드|FI|
|프랑스|FR|
|독일|DE|
|홍콩 특별 행정구|HK|
|인도|IN|
|인도네시아|ID|
|이탈리아|IT|
|일본|JP|
|한국|KR|
|말레이시아|MY|
|멕시코|MX|
|네덜란드|NL|
|뉴질랜드|NZ|
|노르웨이|아니요|
|중국|CN|
|폴란드|PL|
|포르투갈|PT|
|필리핀|PH|
|러시아|RU|
|사우디아라비아|SA|
|남아프리카|ZA|
|스페인|ES|
|스웨덴|SE|
|스위스|CH|
|대만|TW|
|터키|TR|
|United Kingdom|GB|
|미국|US|

## <a name="markets"></a>시장

|국가/지역|언어|시장 코드|
|-------|--------|-----------|
|아르헨티나|스페인어|es-AR|
|오스트레일리아|영어|en-AU|
|오스트리아|독일어|de-AT|
|벨기에|네덜란드어|nl-BE|
|벨기에|프랑스어|fr-BE|
|브라질|포르투갈어|pt-BR|
|캐나다|영어|en-CA|
|캐나다|프랑스어|fr-CA|
|칠레|스페인어|es-CL|
|덴마크|덴마크어|da-DK|
|핀란드|핀란드어|fi-FI|
|프랑스|프랑스어|fr-FR|
|독일|독일어|de-DE|
|홍콩 특별행정구|중국어 번체|zh-HK|
|인도|영어|en-IN|
|인도네시아|영어|en-ID|
|이탈리아|이탈리아어|it-IT|
|일본|일본어|ja-JP|
|한국|한국어|ko-KR|
|말레이시아|영어|en-MY|
|멕시코|스페인어|es-MX|
|네덜란드|네덜란드어|nl-NL|
|뉴질랜드|영어|en-NZ|
|노르웨이|노르웨이어|no-NO|
|중국|중국어|zh-CN|
|폴란드|폴란드어|pl-PL|
|포르투갈|포르투갈어|pt-PT|
|필리핀|영어|en-PH|
|러시아|러시아어|ru-RU|
|사우디아라비아|아랍어|ar-SA|
|남아프리카 공화국|영어|en-ZA|
|스페인|스페인어|es-ES|
|스웨덴|스웨덴어|sv-SE|
|스위스|프랑스어|fr-CH|
|스위스|독일어|de-CH|
|대만|중국어 번체|zh-TW|
|터키|터키어|tr-TR|
|영국|영어|en-GB|
|미국|영어|en-US|
|미국|스페인어|es-US|

## <a name="next-steps"></a>다음 단계

* [Bing Image Search API 참조](/rest/api/cognitiveservices/bing-images-api-v7-reference)