---
title: V3으로 마이그레이션 - Translator
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Translator의 V2에서 V3으로 마이그레이션하는 데 도움이 되는 단계를 제공합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 2e64e0699be0c53a7f515574be63deb613d04bfe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750757"
---
# <a name="translator-v2-to-v3-migration"></a>Translator V2에서 V3으로 마이그레이션

> [!NOTE]
> V2는 2018년 4월 30일부터 지원이 중단되었습니다. V3에서만 사용할 수 있는 새로운 기능을 활용하기 위해 애플리케이션을 V3으로 마이그레이션합니다. V2는 2021 년 5 월 24 일에 사용이 중지 되었습니다. 

Microsoft Translator 팀은 Translator의 버전 3(V3)을 릴리스했습니다. 이 릴리스에는 새로운 기능, 사용되지 않는 메서드 및 Microsoft Translator Service 간에 데이터를 보내고 받는 새 형식이 포함되어 있습니다. 이 문서에서는 V3을 사용할 애플리케이션을 변경하는 정보를 제공합니다. 

이 문서의 끝에는 자세히 알아볼 유용한 링크가 포함됩니다.

## <a name="summary-of-features"></a>기능 요약

* No Trace - V3에서 No Trace는 Azure Portal에서 모든 가격 책정 계층에 적용됩니다. 이 기능은 V3 API에 제출된 텍스트가 Microsoft에 저장되지 않습니다.
* JSON - XML은 JSON으로 바뀝니다. 서비스에 보내고 서비스로부터 받은 모든 데이터는 JSON 형식입니다.
* 단일 요청의 여러 대상 언어 - 변환 메서드는 단일 요청의 변환에 여러 'to' 언어를 허용합니다. 예를 들어 단일 요청은 'from' 영어 및 'to' 독일어, 스페인어 및 일본어 또는 다른 그룹의 언어일 수 있습니다.
* 이중언어 사전 - 이중언어 사전 메서드가 API에 추가되었습니다. 이 메서드에는 'lookup' 및 'examples'가 포함됩니다.
* 음역 - 음역 메서드가 API에 추가되었습니다. 이 메서드는 하나의 스크립트에서 단어 및 문장(예: 아랍어)을 다른 스크립트(예: 라틴어)로 변환합니다.
* 언어 - 새로운 'languages' 메서드는 'translate', 'dictionary' 및 'transliterate' 메서드와 함께 사용하기 위해 JSON 형식으로 언어 정보를 제공합니다.
* 번역에 대한 새로운 기능 - 새 기능이 'translate' 메서드에 추가되어 별도 메서드로 V2 API에 있던 기능 중 일부를 지원합니다. 예제는 TranslateArray입니다.
* 음성 메서드 - 텍스트 음성 변환 기능이 더 이상 Microsoft Translator에서 지원되지 않습니다. [Microsoft Speech Service](../speech-service/text-to-speech.md)에서 텍스트 음성 변환 기능을 사용할 수 있습니다.

다음과 같은 V2 및 V3 메서드 목록은 V2와 함께 포함된 기능을 제공하는 V3 메서드 및 API를 식별합니다.

| V2 API 메서드   | V3 API 호환성 |
|:----------- |:-------------|
| `Translate`     | [번역](reference/v3-0-translate.md)          |
| `TranslateArray`      | [번역](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [언어](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [언어](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](../speech-service/text-to-speech.md)          |
| `Detect`     | [검색](reference/v3-0-detect.md)         |
| `DetectArray`     | [검색](reference/v3-0-detect.md)         |
| `AddTranslation`     | 기능은 더 이상 지원되지 않습니다.       |
| `AddTranslationArray`    | 기능은 더 이상 지원되지 않습니다.          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | 기능은 더 이상 지원되지 않습니다.         |
| `GetTranslationsArray`      | 기능은 더 이상 지원되지 않습니다.         |

## <a name="move-to-json-format"></a>JSON 형식으로 이동

Microsoft Translator 번역 V2가 XML 형식으로 데이터를 허용하고 반환했습니다. V3에서 API를 사용하여 보내고 받은 모든 데이터는 JSON 형식입니다. XML은 V3에서 더 이상 허용되거나 반환되지 않습니다.

이 변경 내용은 V2 텍스트 번역 API에 대해 작성된 애플리케이션의 여러 측면에 영향을 줍니다. 예를 들어: 언어 API는 텍스트 번역, 음역 및 두 개의 사전 메서드에 대한 언어 정보를 반환합니다. 한 번의 호출에서 모든 메서드에 대한 모든 언어 정보를 요청하거나 개별적으로 요청할 수 있습니다.

언어 메서드에는 인증이 필요하지 않습니다. 다음 링크를 클릭하여 JSON에서 V3에 대한 모든 언어 정보를 확인할 수 있습니다.

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation 사전, 음역](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>인증 키

V2에 사용하는 인증 키는 V3에 허용됩니다. 새 구독을 가져올 필요가 없습니다. 장기 마이그레이션 기간 동안 앱에서 V2 및 V3를 혼합할 수 있으며 V2-XML에서 V3-JSON으로 마이그레이션하는 동안 더욱 쉽게 새 버전을 릴리스할 수 있습니다.

## <a name="pricing-model"></a>가격 책정 모델

Microsoft Translator V3는 V2와 동일한 방식으로 공백을 포함한 문자별로 가격 책정됩니다. V3의 새 기능은 청구에 사용하는 일부 문자를 변경했습니다.

| V3 메서드   | 청구에 계산되는 문자 |
|:----------- |:-------------|
| `Languages`     | 문자가 제출되지 않았습니다. 계산되지 않았습니다. 비용이 청구되지 않습니다.          |
| `Translate`     | 수는 번역에 제출되는 문자 수 및 번역되는 언어 및 문자 수에 따라 다릅니다. 제출된 50개 문자 및 요청된 5개 언어는 50x5입니다.           |
| `Transliterate`     | 음역에 제출된 문자 수가 계산됩니다.         |
| `Dictionary lookup & example`     | 사전 조회 및 예제에 제출된 문자 수가 계산됩니다.         |
| `BreakSentence`     | 무료입니다.       |
| `Detect`     | 무료입니다.      |

## <a name="v3-end-points"></a>V3 엔드포인트

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API 텍스트 번역 메서드

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>호환성 및 사용자 지정

> [!NOTE]
> 
> Microsoft Translator Hub는 2019년 5월 17일부터 사용 중지됩니다. [중요한 마이그레이션 정보 및 날짜 보기](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3는 기본적으로 신경망 기계 번역을 사용합니다. 따라서 Microsoft Translator Hub로 사용할 수 없습니다. Translator Hub만 기존의 통계 기계 번역을 지원합니다. 이제 신경망 번역에 대한 사용자 지정은 사용자 지정 변환기를 사용하여 사용할 수 있습니다. [신경망 기계 번역을 사용자 지정하는 방법에 대한 자세한 정보](custom-translator/overview.md)

V3 텍스트 API를 포함한 신경망 번역은 표준 범주(SMT, 음성, 텍스트, generalnn)의 사용을 지원하지 않습니다.

| 버전 | 엔드포인트 | GDPR 프로세서 규정 준수 | Translator Hub 사용 | Custom Translator(미리 보기) 사용 |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Translator 버전 2|    api.microsofttranslator.com|    예    |예    |예|
|Translator 버전 3|    api.cognitive.microsofttranslator.com|    예|    예|    예|

**Translator 버전 3**
* 일반적으로 사용할 수 있으며 완전히 지원됩니다.
* 프로세서로 GDPR을 규정 준수하며 SOC 3 인증 요구 사항뿐 아니라 모든 ISO 20001 및 20018을 충족합니다. 
* 새로운 Translator NMT 사용자 지정 기능인 Custom Translator(미리 보기)를 사용하여 사용자 지정한 인공신경망 변환 시스템을 호출할 수 있습니다. 
* Microsoft Translator Hub를 사용하여 작성된 사용자 지정 번역 시스템에 대한 액세스를 제공하지 않습니다.

api.cognitive.microsofttranslator.com 엔드포인트를 사용하는 경우 Translator 버전 3을 사용하고 있습니다.

**Translator 버전 2**
* 모든 ISO 20001, 20018 및 SOC 3 인증 요구 사항을 충족하지 않습니다. 
* Translator 사용자 지정 기능으로 사용자 지정한 인공신경망 번역 시스템을 호출할 수 없습니다.
* Microsoft Translator Hub를 사용하여 작성된 사용자 지정 번역 시스템에 대한 액세스를 제공합니다.
* api.microsofttranslator.com 엔드포인트를 사용하는 경우 Translator 버전 2를 사용하고 있습니다.

Translator 버전은 번역 레코드를 만들지 않습니다. 번역은 누구와도 공유되지 않습니다. 추가 정보는 [Translator 비추적](https://www.aka.ms/NoTrace) 웹 페이지에 있습니다.

## <a name="links"></a>링크

* [Microsoft 개인 정보 취급 방침](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure 법률 정보](https://azure.microsoft.com/support/legal)
* [온라인 서비스 약관.](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [V3.0 설명서 보기](reference/v3-0-reference.md)
