---
title: Speech SDK를 통해 오디오 처리 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK(소프트웨어 개발 키트)를 사용한 오디오 처리의 기능, 기능 및 제한에 대한 개요입니다.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: bbf774ac1f0b91848df48366436144d36b193033
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102825"
---
# <a name="audio-processing-with-speech-sdk"></a>Speech SDK를 통해 오디오 처리

Speech SDK는 MAS(Microsoft Audio Stack)를 통합하여 모든 애플리케이션 또는 제품이 입력 오디오에서 오디오 처리 기능을 사용할 수 있도록 합니다. [Microsoft Audio Stack을 사용하기 위한 최소 요구 사항의](audio-processing-overview.md#minimum-requirements-to-use-microsoft-audio-stack) 최소 요구 사항이 적용됩니다.

Speech SDK API를 통해 사용할 수 있는 주요 기능은 다음과 같습니다.
* **실시간 마이크 입력 & 파일 입력** - Microsoft Audio Stack 처리를 실시간 마이크 입력, 스트림 및 파일 기반 입력에 적용할 수 있습니다. 
* **향상된 기능 선택** - 시나리오를 완전히 제어할 수 있도록 SDK를 사용하면 역행, 노이즈 억제, 자동 게인 제어 및 음향 에코 취소와 같은 개별 향상 기능을 사용하지 않도록 설정할 수 있습니다. 예를 들어 시나리오에 입력 오디오에서 표시하지 않는 렌더링 출력 오디오가 포함되지 않은 경우 음향 에코 취소를 사용하지 않도록 설정하는 옵션이 있습니다.
* **사용자 지정 마이크 기하 도형** - SDK를 사용하면 선형 2-mic, 선형 4-mic 및 원형 7-마이크 배열과 같은 미리 설정된 기하 도형을 지원할 뿐만 아니라 사용자 고유의 사용자 지정 마이크 기하 도형 정보를 제공할 수 [있습니다(마이크 배열 권장 사항에서](speech-sdk-microphone.md#microphone-geometry)지원되는 미리 설정 기하 도형에 대한 자세한 정보 참조).
* **힌포밍 각도** - 마이크를 기준으로 미리 정해진 위치에서 시작되는 오디오 입력을 최적화하기 위해 특정 괄공형 각도를 제공할 수 있습니다.

처리는 Speech SDK가 사용되는 곳에서 완전히 로컬로 수행됩니다. 오디오 데이터는 Microsoft 오디오 스택에서 처리하기 위해 Microsoft의 클라우드 서비스로 스트리밍되지 않습니다. 이에 대한 유일한 예외는 처리를 위해 원시 오디오가 Microsoft의 클라우드 서비스로 전송되는 대화 전사 서비스에 대한 것입니다. 

> [!NOTE]
> Speech Devices SDK는 이제 사용되지 않습니다. Speech Devices SDK의 보관된 버전은 [여기에서](speech-devices-sdk.md)사용할 수 있으며, 해당 샘플 코드는 [GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)사용할 수 있습니다. Speech Devices SDK의 모든 사용자는 Speech SDK v1.19 이상으로 마이그레이션하는 것이 좋습니다.

## <a name="reference-channel-for-echo-cancellation"></a>에코 취소에 대한 참조 채널

Microsoft 오디오 스택은 에코 취소를 수행하기 위해 참조 채널(루프백 채널이라고도 함)이 필요합니다. 참조 채널의 원본은 플랫폼에 따라 다릅니다.
* **Windows** - 를 만들 때 옵션이 제공되면 참조 채널이 Speech SDK에서 자동으로 `SpeakerReferenceChannel::LastChannel` `AudioProcessingOptions` 수집됩니다.
* **Linux** - ALSA(고급 Linux 사운드 아키텍처)는 사용할 오디오 입력 디바이스의 마지막 채널로 참조 오디오 스트림을 제공하도록 구성되어야 합니다. 이는 를 만들 때 옵션을 제공하는 것 외에도 `SpeakerReferenceChannel::LastChannel` `AudioProcessingOptions` 가능합니다.

## <a name="language-and-platform-support"></a>언어 및 플랫폼 지원

| 언어   | 플랫폼    | 참조 문서 |
|------------|----------------|----------------|
| C++        | Windows, Linux | [C++ 문서](/cpp/cognitive-services/speech/) |
| C#         | Windows, Linux | [C# 문서](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java       | Windows, Linux | [Java 문서](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>예제 코드

### <a name="using-microsoft-audio-stack-with-all-default-options"></a>모든 기본 옵션과 함께 Microsoft 오디오 스택 사용

이 샘플에서는 디바이스의 기본 마이크의 입력에 대한 모든 기본 개선 옵션과 함께 MAS를 사용하는 방법을 보여 줍니다.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-a-preset-microphone-geometry"></a>미리 설정된 마이크 기하 도형과 함께 Microsoft 오디오 스택 사용

이 샘플에서는 지정된 오디오 입력 디바이스에서 미리 정의된 마이크 기하 도형으로 MAS를 사용하는 방법을 보여줍니다. 이 예제에 대한 설명:
* **향상된 옵션** - 기본 향상된 기능이 입력 오디오 스트림에 적용됩니다.
* **미리 설정된 기하 도형** - 미리 설정된 기하 도형은 선형 2 마이크 배열을 나타냅니다.
* **오디오 입력 디바이스** - 오디오 입력 디바이스 ID는 입니다. `hw:0,1` 오디오 입력 디바이스를 선택하는 방법에 대한 자세한 내용은 [방법: Speech SDK를 사용하여 오디오 입력 디바이스 선택을](how-to-select-audio-input-devices.md)참조하세요.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
var audioInput = AudioConfig.FromMicrophoneInput("hw:0,1", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry::Linear2);
auto audioInput = AudioConfig::FromMicrophoneInput("hw:0,1", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
AudioConfig audioInput = AudioConfig.fromMicrophoneInput("hw:0,1", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-custom-microphone-geometry"></a>사용자 지정 마이크 기하 도형과 함께 Microsoft 오디오 스택 사용

이 샘플에서는 지정된 오디오 입력 디바이스에서 사용자 지정 마이크 기하 도형으로 MAS를 사용하는 방법을 보여줍니다. 이 예제에 대한 설명:
* **향상된 옵션** - 기본 향상된 기능이 입력 오디오 스트림에 적용됩니다.
* **사용자 지정 기하 도형** - 마이크 좌표를 지정하여 7 마이크 배열에 대한 사용자 지정 마이크 기하 도형을 제공합니다. 좌표 단위는 밀리미터입니다.
* **오디오 입력** - 오디오 입력은 파일에서 가져오며, 파일 내의 오디오는 지정된 사용자 지정 기하 도형에 해당하는 오디오 입력 디바이스에서 캡처되어야 합니다. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7]
{
    new MicrophoneCoordinates(0, 0, 0),
    new MicrophoneCoordinates(40, 0, 0),
    new MicrophoneCoordinates(20, -35, 0),
    new MicrophoneCoordinates(-20, -35, 0),
    new MicrophoneCoordinates(-40, 0, 0),
    new MicrophoneCoordinates(-20, 35, 0),
    new MicrophoneCoordinates(20, 35, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var audioInput = AudioConfig.FromWavFileInput("katiesteve.wav", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Planar,
    { { 0, 0, 0 }, { 40, 0, 0 }, { 20, -35, 0 }, { -20, -35, 0 }, { -40, 0, 0 }, { -20, 35, 0 }, { 20, 35, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto audioInput = AudioConfig::FromWavFileInput("katiesteve.wav", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7];
microphoneCoordinates[0] = new MicrophoneCoordinates(0, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(40, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, -35, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(-20, -35, 0);
microphoneCoordinates[4] = new MicrophoneCoordinates(-40, 0, 0);
microphoneCoordinates[5] = new MicrophoneCoordinates(-20, 35, 0);
microphoneCoordinates[6] = new MicrophoneCoordinates(20, 35, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
AudioConfig audioInput = AudioConfig.fromWavFileInput("katiesteve.wav", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-select-enhancements"></a>향상된 기능 선택과 함께 Microsoft 오디오 스택 사용

이 샘플에서는 입력 오디오의 향상된 사용자 지정 집합과 함께 MAS를 사용하는 방법을 보여 줍니다. 기본적으로 모든 향상된 기능을 사용할 수 있지만 을 사용하여 취소, 노이즈 비표시, 자동 게인 제어 및 에코 취소를 개별적으로 사용하지 않도록 설정하는 옵션이 `AudioProcessingOptions` 있습니다.

이 예제에 대한 설명:
* **향상된 옵션** - 에코 취소 및 노이즈 제거는 사용하지 않도록 설정되지만 다른 모든 향상된 기능을 계속 사용할 수 있습니다.
* **오디오 입력 디바이스** - 오디오 입력 디바이스는 디바이스의 기본 마이크입니다.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-to-specify-beamforming-angles"></a>Microsoft 오디오 스택을 사용하여 괄공형 각도 지정

이 샘플에서는 지정된 오디오 입력 디바이스에서 사용자 지정 마이크 기하 도형 및 괄공형 각도와 함께 MAS를 사용하는 방법을 보여줍니다. 이 예제에 대한 설명:
* **향상된 옵션** - 기본 향상된 기능이 입력 오디오 스트림에 적용됩니다.
* **사용자 지정 기하 도형** - 마이크 좌표를 지정하여 4-마이크 배열에 대한 사용자 지정 마이크 기하 도형을 제공합니다. 좌표 단위는 밀리미터입니다.
* **힌포밍 각도** - 방형 각도는 해당 범위에서 시작되는 오디오에 최적화하도록 지정됩니다. 각도 단위는 도입니다. 아래 샘플 코드에서 시작 각도는 70도로 설정되고 끝 각도는 110도로 설정됩니다.
* **오디오 입력** - 오디오 입력은 푸시 스트림에서 가져오며, 스트림 내의 오디오는 지정된 사용자 지정 기하 도형에 해당하는 오디오 입력 디바이스에서 캡처되어야 합니다. 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4]
{
    new MicrophoneCoordinates(-60, 0, 0),
    new MicrophoneCoordinates(-20, 0, 0),
    new MicrophoneCoordinates(20, 0, 0),
    new MicrophoneCoordinates(60, 0, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Linear, 70, 110, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var pushStream = AudioInputStream.CreatePushStream();
var audioInput = AudioConfig.FromStreamInput(pushStream, audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Linear,
    70,
    110,
    { { -60, 0, 0 }, { -20, 0, 0 }, { 20, 0, 0 }, { 60, 0, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto pushStream = AudioInputStream::CreatePushStream();
auto audioInput = AudioConfig::FromStreamInput(pushStream, audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4];
microphoneCoordinates[0] = new MicrophoneCoordinates(-60, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(-20, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, 0, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(60, 0, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, 70, 110, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
PushAudioInputStream pushStream = AudioInputStream.createPushStream();
AudioConfig audioInput = AudioConfig.fromStreamInput(pushStream, audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---
