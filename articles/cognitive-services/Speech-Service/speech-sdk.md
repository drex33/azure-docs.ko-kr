---
title: Speech SDK 정보 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK(소프트웨어 개발 키트)는 많은 Speech Service 기능을 제공하므로 음성 지원 애플리케이션을 보다 쉽게 개발할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: pafarley
ms.openlocfilehash: 6078fdd823160b6ea5e76efd5940342d333e84f1
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540074"
---
# <a name="about-the-speech-sdk"></a>Speech SDK 정보

Speech SDK(소프트웨어 개발 키트)는 음성 지원 애플리케이션을 개발할 수 있도록 많은 Speech Service 기능을 제공합니다. Speech SDK는 많은 프로그래밍 언어 및 모든 플랫폼에서 사용할 수 있습니다.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>시나리오 기능

Speech SDK는 Speech Service의 많은 기능을 노출하지만 일부는 제공하지 않습니다. Speech SDK의 기능은 시나리오와 연결되는 경우가 많습니다. Speech SDK는 로컬 디바이스, 파일, Azure Blob 스토리지 및 입력 및 출력 스트림을 사용하여 실시간 및 비 실시간 시나리오 모두에 적합합니다. Speech SDK를 사용하여 시나리오를 달성할 수 없는 경우 REST API 대안을 찾습니다.

### <a name="speech-to-text"></a>음성 텍스트 변환

[음성 텍스트 변환](speech-to-text.md)(*음성 인식* 이라고도 함)은 오디오 스트림을 애플리케이션, 도구 또는 디바이스가 사용하거나 표시할 수 있는 텍스트로 기록합니다. [LUIS(Language Understanding)](../luis/index.yml)에서 음성 텍스트 변환을 사용하여 기록된 음성에서 사용자 의도를 파생시키고 음성 명령 작업에 따라 행동합니다. [음성 번역](speech-translation.md)을 사용하여 한 번의 호출로 음성 입력을 다른 언어로 번역합니다. 자세한 내용은 [음성 텍스트 변환 기본 사항](./get-started-speech-to-text.md)을 참조하세요.

**SR(음성 인식), 구 목록, 의도, 번역 및 온-프레미스 컨테이너** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C++/Windows & Linux & macOS
  - C#(Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java(Jre 및 Android)
  - JavaScript(브라우저 및 NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go(SR 전용)

### <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성 변환](text-to-speech.md)(*음성 합성* 이라고도 함)은 텍스트를 사람과 유사한 합성 음성으로 변환합니다. 입력 텍스트는 문자열 리터럴 또는 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)을 사용하는 것입니다. 표준 또는 인공신경망 음성에 대한 자세한 내용은 [텍스트 음성 변환 언어 및 음성 지원](language-support.md#text-to-speech)을 참조하세요.

**TTS(텍스트 음성 변환)** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C++/Windows & Linux & macOS
  - C#(Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java(Jre 및 Android)
  - JavaScript(브라우저 및 NodeJS)
  - Python
  - Swift
  - Objective-C
  - Go
  - TTS REST API는 다른 모든 상황에서 사용할 수 있습니다.

### <a name="voice-assistants"></a>음성 도우미

Speech SDK를 사용하는 [음성 도우미](voice-assistants.md)를 사용하면 애플리케이션과 환경에 맞게 자연스럽고 인간적인 대화형 인터페이스를 만들 수 있습니다. Speech SDK는 단일 연결에서 음성 텍스트 변환, 텍스트 음성 변환 및 대화형 데이터를 포함하는 빠르고 안정적인 상호 작용을 제공합니다. 구현에서는 작업을 완료하는 데 Bot Framework의 Direct Line Speech 채널 또는 통합된 사용자 지정 명령 서비스를 사용할 수 있습니다. 또한 음성 도우미는 [사용자 지정 음성 포털](https://aka.ms/customvoice)에서 만든 사용자 지정 음성을 사용하여 고유한 음성 출력 환경을 추가할 수 있습니다.

**음성 도우미** 지원은 다음 플랫폼에서 사용할 수 있습니다.

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android(Speech Devices SDK)
  - Go

#### <a name="keyword-recognition"></a>키워드 인식

[키워드 인식](custom-keyword-basics.md)의 개념은 Speech SDK에서 지원됩니다. 키워드 인식은 음성에서 키워드를 식별한 후 키워드를 들으면 작업을 수행하는 동작입니다. 예를 들어 "Hey Cortana"는 Cortana 도우미를 활성화합니다.

**키워드 인식** 은 다음 플랫폼에서 사용할 수 있습니다.

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android

### <a name="meeting-scenarios"></a>모임 시나리오

Speech SDK는 단일 디바이스 또는 다중 디바이스 대화에서든 상관 없이 모임 시나리오를 기록하는 데 적합합니다.

#### <a name="conversation-transcription"></a>대화 전사

[대화 기록](conversation-transcription.md)을 통해 각 스피커(*diarization* 이라고도 함)에 대한 실시간(및 비동기) 음성 인식, 화자 식별 및 문장 특성을 사용할 수 있습니다. 화자를 구별하는 기능을 사용하여 직접 만남을 전사하는 데 이상적입니다.

**대화 기록** 은 다음 플랫폼에서 사용할 수 있습니다.

  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android(Speech Devices SDK)

#### <a name="multi-device-conversation"></a>다중 디바이스 대화

[다중 디바이스 대화](multi-device-conversation.md)를 사용하면 대화의 다중 디바이스 또는 클라이언트를 연결하여 음성 기반 또는 텍스트 기반 메시지를 전송하고 기록 및 번역을 간편하게 지원합니다.

**다중 디바이스 대화** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C++/Windows
  - C#(Framework & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>사용자 지정/에이전트 시나리오

Speech SDK는 전화 통신 데이터가 생성되는 콜 센터 시나리오 기록에 사용할 수 있습니다.

#### <a name="call-center-transcription"></a>호출 센터 전사

[콜 센터 기록](call-center-transcription.md)은 IVR(대화형 음성 응답)과 같은 다양한 시스템에서 발생할 수 있는 대량의 전화 통신 데이터 기록을 위한 음성 텍스트 변환의 일반적인 시나리오입니다. Speech Service의 최신 음성 인식 모델은 데이터가 사람이 이해하기 어려운 경우에도 이 전화 통신 데이터를 기록할 때 처리 속도를 높여줍니다.

**콜 센터 기록** 은 REST API를 통해 Batch Speech Service를 통해 사용할 수 있으며 모든 상황에서 사용할 수 있습니다.

### <a name="codec-compressed-audio-input"></a>코덱 압축 오디오 입력

몇 가지 Speech SDK 프로그래밍 언어는 코덱 압축 오디오 입력 스트림을 지원합니다. 자세한 내용은 <a href="/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">압축 오디오 입력 형식 사용</a>을 참조하세요.

**코덱 압축 오디오 입력** 은 다음 플랫폼에서 사용할 수 있습니다.

  - C++/Linux
  - C#/Linux
  - Java/Linux, Android 및 iOS

## <a name="rest-api"></a>REST API

Speech SDK는 Speech Service의 많은 기능을 다루지만 일부 시나리오에서는 REST API를 사용하는 것이 좋습니다.

### <a name="batch-transcription"></a>일괄 처리 기록

[일괄 처리 기록](batch-transcription.md)을 사용하면 대량의 비동기 음성 텍스트 변환 기록을 수행할 수 있습니다. 일괄 처리 기록은 REST API에서만 가능합니다. 음성 오디오를 텍스트로 변환하는 것 외에도 일괄 처리 음성 텍스트 변환에서는 다이어리화 및 감정 분석도 가능합니다.

## <a name="customization"></a>사용자 지정

Speech Service는 음성 텍스트 변환, 텍스트 음성 변환 및 음성 변환에서 기본 모델과 함께 뛰어난 기능을 제공합니다. 경우에 따라 고유한 사용 사례에서 더 잘 작동하도록 기본 성능을 높일 수 있습니다. Speech Service에는 쉽게 만들 수 있는 다양한 코드 없는 사용자 지정 도구가 있으며 사용자 고유의 데이터를 기반으로 사용자 지정 모델을 사용하여 경쟁 우위를 만들 수 있습니다. 이러한 모델은 사용자와 사용자의 조직에서만 사용할 수 있습니다.

### <a name="custom-speech-to-text"></a>사용자 지정 음성 텍스트 변환

고유한 환경에서 인식 및 기록을 위해 음성 텍스트 변환을 사용하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습하여 주변 소음 또는 산업용 어휘를 처리할 수 있습니다. 코드 없는 Custom Speech 모델의 생성 및 관리는 [Custom Speech 포털](./custom-speech-overview.md)을 통해 사용할 수 있습니다. Custom Speech 모델을 게시한 후에는 Speech SDK에서 사용할 수 있습니다.

### <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

Custom Voice라고도 하는 사용자 지정 텍스트 음성 변환은 사용자의 브랜드에 대해 인식할 수 있는 일종의 음성을 만들 수 있는 온라인 도구 집합입니다. 코드 없는 Custom Voice 모델의 생성 및 관리는 [Custom Voice 포털](https://aka.ms/customvoice)을 통해 사용할 수 있습니다. Custom Voice 모델을 게시한 후에는 Speech SDK에서 사용할 수 있습니다.

## <a name="get-the-speech-sdk"></a>Speech SDK 가져오기

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[브라우저](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

* [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)
