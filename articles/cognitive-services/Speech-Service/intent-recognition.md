---
title: 의도 인식 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: 의도 인식을 사용하여 미리 정의된 사용자 목표를 인식할 수 있습니다. 이 문서는 의도 인식 서비스의 이점과 기능을 개략적으로 설명합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
keywords: 의도 인식
ms.openlocfilehash: 221f5f0f022cba85df72979ed47b4033b2c3fdd1
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988486"
---
# <a name="what-is-intent-recognition"></a>의도 인식이란?

이 개요에서는 의도 인식의 이점과 기능에 대해 알아봅니다. Cognitive Services Speech SDK는 아래에 설명된 두 가지 의도 인식 방법을 제공합니다. 의도란 항공권 예약, 날씨 확인, 호출 등 사용자가 수행하려는 것을 말합니다. 의도 인식을 사용하면 애플리케이션, 도구, 디바이스가 의도 인식기나 LUIS에서 정의된 옵션에 따라 사용자가 시작하거나 수행할 작업을 확인할 수 있습니다.

## <a name="pattern-matching"></a>패턴 일치
SDK는 매우 엄격한 방식으로 의도를 인식하는 데 사용할 수 있는 포함된 패턴 검사기를 제공합니다. 이 패턴 검사기는 빠른 오프라인 솔루션이 필요한 경우에 유용합니다. 사용자가 어떤 방식으로든 학습되거나 특정 구를 사용하여 의도를 트리거할 수 있는 경우에 특히 효과적입니다. 예를 들어 “7층으로 이동” 또는 “전등 켜기” 등이 있습니다. 이 방법으로 시작하고, 더 이상 요구 사항을 충족하지 않는 경우 LUIS나 두 방법의 조합으로 전환하는 것이 좋습니다. 

## <a name="luis-language-understanding-intent-service"></a>LUIS(Language Understanding 의도 서비스)
Microsoft LUIS 서비스는 가능한 의도의 도메인이 크고 사용자가 말하는 내용이 확실하지 않은 경우에 효과적인 완전한 AI 의도 서비스로 제공됩니다. 많은 복잡한 시나리오, 의도, 엔터티를 지원합니다.

### <a name="luis-key-required"></a>LUIS 키 필요

* LUIS는 음성 서비스와 통합되어 음성에서 의도를 인식합니다. 음성 서비스 구독은 필요 없고 LUIS만 있으면 됩니다.
* 음성 의도 인식은 Speech SDK와 통합됩니다. Speech Service에서 LUIS 키를 사용할 수 있습니다.
* Speech SDK를 통한 의도 인식은 [LUIS에서 지원하는 지역의 하위 집합](./regions.md#intent-recognition)에서 제공됩니다.

## <a name="get-started"></a>시작
패턴 일치를 시작하려면 [방법](how-to-use-simple-language-pattern-matching.md)을 참조하세요.

LUIS 의도 인식을 시작하려면 [빠른 시작](get-started-intent-recognition.md)을 참조하세요.

## <a name="sample-code"></a>예제 코드

의도 인식용 샘플 코드:

* [빠른 시작: 미리 빌드된 홈 자동화 앱 사용](../luis/luis-get-started-create-app.md)
* [C#용 Speech SDK를 사용하여 음성에서 의도 인식](./how-to-recognize-intents-from-speech-csharp.md)
* [C#의 Unity를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Python용 Speech SDK를 사용하여 의도 인식](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Windows에서 C++용 Speech SDK를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Windows 또는 Linux에서 Java용 Speech SDK를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [웹 브라우저에서 JavaScript용 Speech SDK를 사용하는 의도 인식 및 기타 Speech Service](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>다음 단계

* 의도 인식 [빠른 시작](get-started-intent-recognition.md) 완료
* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [Speech SDK 가져오기](speech-sdk.md)
