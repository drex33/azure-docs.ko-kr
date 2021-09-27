---
title: 텍스트 음성 변환 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service의 텍스트 음성 변환 기능을 통해 애플리케이션, 도구 또는 디바이스에서 텍스트를 자연스러운 사람과 같은 합성된 음성으로 변환할 수 있습니다. 이 문서에서는 텍스트 음성 변환 서비스의 이점과 기능을 개략적으로 설명합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 텍스트 음성 변환
ms.openlocfilehash: 7be06937cf9f3117d426fd9c19cbb6059826e6ec
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736908"
---
# <a name="what-is-neural-text-to-speech"></a>인공신경망 텍스트 음성 변환이란 무엇인가요?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Microsoft 인공신경망 텍스트 음성 변환은 심층적인 인공신경망을 사용하여 컴퓨터의 음성을 녹음된 사람의 목소리와 거의 구별할 수 없도록 만듭니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 AI 시스템과 상호 작용할 때 인공신경망 텍스트 음성 변환은 수신 피로도를 현저히 줄일 수 있습니다.

구어의 강세와 억양 패턴을 _운율_ 이라고 합니다. 기존의 TTS(텍스트 음성 변환) 시스템은 운율을 독립적인 모델에 의해 관리되는 별도의 언어 분석 및 음향 예측 단계로 나눕니다. 그 결과 희미하고 윙윙거리는 음성 합성이 발생할 수 있습니다. Microsoft 인공신경망 텍스트 음성 변환 기능은 운율 예측과 음성 합성을 동시에 수행하고 심층적인 인공신경망을 사용하여 구어의 강세 및 억양 패턴을 일치시키는 데 있어 기존 문자 음성 변환 시스템의 한계를 극복하고 음성 단위를 컴퓨터 음성으로 합성합니다. 그 결과 더 유연하고 자연스러운 음성이 나옵니다.

이 개요에서는 애플리케이션, 도구 또는 디바이스에서 텍스트를 사람과 유사한 합성된 음성으로 변환할 수 있는 텍스트 음성 변환 서비스의 이점과 기능에 관해 알아봅니다. 인간과 유사한 신경망 음성을 사용하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만듭니다. 지원되는 음성, 언어, 로캘의 전체 목록은 [지원되는 언어](language-support.md#text-to-speech)를 참조하세요.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* **빠른 시작** 은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* **방법 가이드** 에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* **개념** 은 서비스 기능에 대한 심층적인 설명을 제공합니다.
* **자습서** 는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

> [!NOTE]
>
> Bing Speech는 2019년 10월 15일에 서비스가 중단되었습니다. 애플리케이션, 도구 또는 제품에서 Bing Speech API 또는 Custom Speech를 사용하는 경우 Speech Service로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.
>
> * [Bing Speech에서 Speech Service로 마이그레이션](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>핵심 기능

* 음성 합성 - [Speech SDK](./get-started-text-to-speech.md) 또는 [REST API](rest-text-to-speech.md)를 사용하여 [플랫폼 인공신경망 음성](language-support.md#text-to-speech) 또는 [사용자 지정 인공신경망 음성](custom-neural-voice.md)을 사용하여 텍스트를 음성으로 변환합니다.

* 긴 오디오의 비동기 합성 - 10분보다 긴 텍스트 음성 변환 파일(예: 오디오북 또는 오디오 강의)을 비동기적으로 합성하려면 [긴 오디오 API](long-audio-api.md)를 사용합니다. Speech SDK 또는 음성 텍스트 변환 REST API를 사용하여 수행한 합성과 달리 응답은 실시간으로 반환되지 않습니다. 요청이 비동기적으로 전송되고, 응답이 폴링되며, 서비스에서 사용할 수 있을 때 합성된 오디오가 다운로드될 것으로 예상됩니다.

* 플랫폼 인공신경망 음성 - 심층 인공신경망은 음성 언어의 강세 및 억양과 관련하여 기존 음성 합성의 한계를 극복하는 데 사용됩니다. 운율 예측 및 음성 합성은 동시에 수행되므로 더 부드럽고 자연스럽게 들리는 출력이 생성됩니다. 신경망 음성을 사용하여 챗봇 및 음성 도우미와의 상호 작용에 더 자연스럽게 참여하도록 만들고, eBook 같은 디지털 텍스트를 오디오북으로 변환하고, 자동차 내부 내비게이션 시스템을 향상시킬 수 있습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 AI 시스템과 상호 작용할 때 신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다. 플랫폼 인공신경망 음성의 전체 목록은 [지원되는 언어](language-support.md#text-to-speech)를 참조하세요.

* SSML을 사용하여 TTS 출력 미세 조정 - SSML(Speech Synthesis Markup Language)은 텍스트 음성 변환 출력을 사용자 지정하는 데 사용되는 XML 기반 태그 언어입니다. SSML을 사용하면 피치를 조정하고, 일시 중지를 추가하고, 발음을 개선하고, 말하기 속도를 변경하고, 볼륨을 조정하고, 여러 음성 특성을 단일 문서로 정의하고, 다른 말하기 스타일로 전환할 수 있습니다. [다국어 음성 기능](https://techcommunity.microsoft.com/t5/azure-ai/azure-text-to-speech-updates-at-build-2021/ba-p/2382981)을 사용하면 SSML을 통해 말하기 언어를 조정할 수도 있습니다. 시나리오의 음성 출력을 미세 조정하기 위해 [SSML을 사용하는 방법](speech-synthesis-markup.md)을 참조하세요.

* Viseme - [Viseme](how-to-speech-synthesis-viseme.md)는 특정 음소를 생성할 때 입술, 턱 및 혀의 위치를 포함하여 관찰된 음성의 주요 포즈입니다. Viseme는 음성 및 음소와 강력한 상관 관계가 있습니다. Speech SDK에서 viseme 이벤트를 사용하면 입술을 읽는 커뮤니케이션, 교육, 엔터테인먼트 및 고객 서비스에서 얼굴에 애니메이션 효과를 주는 데 사용할 수 있는 얼굴 애니메이션 데이터를 생성할 수 있습니다. Viseme은 현재 `en-US` 영어(미국) [신경망 음성](language-support.md#text-to-speech)에만 지원됩니다.

## <a name="get-started"></a>시작

텍스트 음성 변환 시작하려면 [빠른 시작](get-started-text-to-speech.md)을 참조하세요. 텍스트 음성 변환 서비스는 [Speech SDK](speech-sdk.md), [REST API](rest-text-to-speech.md) 및 [Speech CLI](spx-overview.md)를 통해 사용할 수 있습니다.

## <a name="sample-code"></a>예제 코드

텍스트 음성 변환 샘플 코드는 GitHub에서 사용할 수 있습니다. 해당 샘플은 가장 인기 있는 프로그래밍 언어의 텍스트 음성 변환을 다룹니다.

* [텍스트 음성 변환 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>사용자 지정

신경망 음성 외에도 제품 또는 브랜드에 고유한 사용자 지정 음성을 만들고 미세 조정할 수 있습니다. 몇 가지 오디오 파일과 연결된 대화 내용 기록만 있으면 시작할 수 있습니다. 자세한 내용은 [사용자 지정 신경망 음성 시작](how-to-custom-voice.md)을 참조하세요.

## <a name="pricing-note"></a>가격 책정 메모

텍스트 음성 변환 서비스를 사용하는 경우 문장 부호를 포함하여 음성으로 변환되는 각 문자에 대한 요금이 청구됩니다. SSML 문서 자체에는 요금이 청구되지 않지만 음소 및 음 높이 같이 텍스트가 음성으로 변환되는 방법을 조정하는 데 사용되는 선택적 요소는 청구 대상 문자로 계산됩니다. 청구 대상 항목 목록은 다음과 같습니다.

* 요청의 SSML 본문에서 텍스트 음성 변환 서비스에 전달되는 텍스트
* `<speak>` 및 `<voice>` 태그를 제외한 SSML 형식의 요청 본문 텍스트 필드에 있는 모든 태그
* 문자, 문장 부호, 공백, 탭, 태그 및 모든 공백 문자
* 유니코드에 정의된 모든 코드 포인트

자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

> [!IMPORTANT]
> 각 중국어, 일본어 및 한국어 문자는 청구를 위해 두 문자로 계산됩니다.

## <a name="migrate-to-neural-voice"></a>인공신경망 음성으로 마이그레이션

표준 음성은 **2024년 8월 31일** 에 중단되며 그 날짜 이후에는 더 이상 지원되지 않습니다.  **2021년 8월 31일** 이전에는 모든 기존 음성 구독으로 공지가 전송되었습니다. 사용 중지 기간 (**8 월 2021-31 2024 년 8 월 31 일까** 지) 동안 기존 표준 음성 사용자는 계속 해 서 표준 음성을 사용할 수 있습니다. 모든 새 사용자/새 음성 리소스는 신경망로 이동 해야 합니다.

**필요한 동작**

1. [가격](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 구조를 검토하고 페이지 하단의 인공신경망 목소리 [샘플](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#overview)을 듣고 비즈니스 요구 사항에 적합한 목소리를 결정합니다.
1. 변경하려면 2024년 8월 31일까지 [샘플 코드에 따라](speech-synthesis-markup.md#choose-a-voice-for-text-to-speech) 음성 합성 요청의 음성 이름을 선택한 언어로 지원되는 인공신경망 음성 이름으로 업데이트합니다. **2024년 9월 1일부터** 표준 음성은 더 이상 지원되지 않습니다. 클라우드 또는 온-프레미스에 음성 합성 요청에 인공신경망 음성을 사용하세요. 온-프레미스 컨테이너의 경우 [인공신경망 음성 컨테이너](../containers/container-image-tags.md)를 사용하고 [지침](speech-container-howto.md)을 따르세요.

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)

## <a name="next-steps"></a>다음 단계

* [체험 Speech Service 구독 받기](overview.md#try-the-speech-service-for-free)
* [Speech SDK 가져오기](speech-sdk.md)
