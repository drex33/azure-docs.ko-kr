---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: pafarley
ms.openlocfilehash: b887683361f13bb2ae670571093c967159d4d4c2
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539393"
---
이 빠른 시작에서는 Speech SDK를 사용하여 텍스트 음성 변환을 수행하기 위한 일반적인 디자인 패턴에 대해 알아봅니다. 먼저 기본 구성 및 합성을 수행하고 다음과 같은 사용자 지정 애플리케이션 개발을 위한 고급 예제로 이동합니다.

* 메모리 내 스트림으로 응답 가져오기
* 출력 샘플 속도 및 비트 전송률 사용자 지정
* SSML(음성 합성 마크업 언어)을 사용하여 합성 요청 제출
* 인공신경망 음성 사용

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [C++ 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/text-to-speech)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>종속성 가져오기

이 문서의 예제를 실행하려면 스크립트 맨 위에 다음 import 문 및 `using` 문을 포함합니다.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 만들어야 합니다. 이 클래스에는 음성 키 및 관련 위치/지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

[`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 초기화하는 다음과 같은 방법이 있습니다.

* 구독 사용: 키 및 관련 위치/지역을 전달합니다.
* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 관련 위치/지역을 전달합니다.

이 예제에서는 구독 키와 지역을 사용하여 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig)를 만듭니다. [음성 서비스 무료로 사용해 보기](../../../overview.md#try-the-speech-service-for-free)의 단계를 따라 이러한 자격 증명을 가져오세요. 또한 이 문서의 나머지 부분에 사용할 몇 가지 기본 상용구 코드를 만들 수 있습니다. 이 문서의 나머지 부분에서는 사용자 지정을 위해 수정합니다.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}

void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
}
```

## <a name="synthesize-speech-to-a-file"></a>음성을 파일로 합성

다음으로, 텍스트 음성 변환을 실행하고 스피커, 파일 또는 기타 출력 스트림으로 출력하는 [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) 개체를 만듭니다. [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer)는 이전 단계에서 만든 [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) 개체와 출력 결과를 처리하는 방법을 지정하는 [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) 개체를 매개 변수로 수락합니다.

시작하려면 `AudioConfig`를 만들어 `FromWavFileOutput()` 함수를 사용하여 출력을 `.wav` 파일에 자동으로 작성합니다.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

다음으로, `config` 개체와 `audioConfig` 개체를 매개 변수로 전달하는 `SpeechSynthesizer`를 인스턴스화합니다. 그러면 음성 합성 및 파일 쓰기를 실행하는 것은 텍스트 문자열을 사용하여 `SpeakTextAsync()`를 실행하는 것만큼 간단합니다.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

프로그램이 실행되면 합성된 `.wav` 파일을 지정한 위치에 씁니다. 이는 가장 기본적인 사용법의 좋은 예이지만, 다음으로 출력을 사용자 지정하고 출력 응답을 메모리 내 스트림으로 처리하여 사용자 지정 시나리오를 사용하는 방법을 살펴봅니다.

## <a name="synthesize-to-speaker-output"></a>스피커 출력으로 합성

경우에 따라 합성된 음성을 스피커로 직접 출력할 수도 있습니다. 이렇게 하려면 위의 예제에서 `SpeechSynthesizer`를 만들 때 `AudioConfig` 매개 변수를 생략합니다. 이 경우 현재 활성 출력 디바이스로 합성합니다.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>결과를 메모리 내 스트림으로 가져오기

음성 애플리케이션을 개발하는 많은 시나리오에서는 파일에 직접 쓰지 않고 결과 오디오 데이터가 메모리 내 스트림으로 필요할 수 있습니다. 이렇게 하면 다음과 같은 사용자 지정 동작을 빌드할 수 있습니다.

* 결과 바이트 배열을 사용자 지정 다운스트림 서비스에 대한 검색 가능한 스트림으로 추상화합니다.
* 결과를 다른 API 또는 서비스와 통합합니다.
* 오디오 데이터를 수정하고, 사용자 지정 `.wav` 헤더 등을 작성합니다.

이전 예제에서 이 변경을 수행하는 것이 간단합니다. 먼저 제어를 향상하기 위해 이 시점부터 출력 동작을 수동으로 관리하므로 `AudioConfig`를 제거합니다. 그런 다음, `SpeechSynthesizer` 생성자의 `AudioConfig`에 대해 `NULL`을 전달합니다.

> [!NOTE]
> 위의 스피커 출력 예제와 같이 생략하는 대신 `AudioConfig`에 대해 `NULL`을 전달하면 현재 활성 출력 디바이스에서 기본적으로 오디오가 재생되지 않습니다.

이번에는 결과를 [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult) 변수에 저장합니다. `GetAudioData` getter는 출력 데이터의 `byte []`를 반환합니다. 이 `byte []`를 수동으로 사용하거나 [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) 클래스를 사용하여 메모리 내 스트림을 관리할 수 있습니다. 다음 예제에서는 `AudioDataStream.FromResult()` 정적 함수를 사용하여 결과에서 스트림을 가져옵니다.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

여기에서 결과 `stream` 개체를 사용하여 사용자 지정 동작을 구현할 수 있습니다.

## <a name="customize-audio-format"></a>오디오 형식 사용자 지정

다음 섹션에서는 아래와 같은 오디오 출력 특성을 사용자 지정하는 방법을 보여 줍니다.

* 오디오 파일 형식
* 샘플 속도
* 비트 수준

오디오 형식을 변경하려면 `SpeechConfig` 개체에서 `SetSpeechSynthesisOutputFormat()` 함수를 사용합니다. 이 함수에는 출력 형식을 선택하는 데 사용하는 [`SpeechSynthesisOutputFormat`](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) 형식의 `enum`이 필요합니다. 사용 가능한 [오디오 형식 목록](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)은 참조 문서를 참조하세요.

요구 사항에 따라 다양한 파일 형식에 대한 다양한 옵션이 있습니다. 정의에 따라 `Raw24Khz16BitMonoPcm`과 같은 원시 형식에는 오디오 헤더가 포함되지 않습니다. 다운스트림 구현에서 원시 비트 스트림을 디코딩할 수 있음을 것을 알고 있거나 비트 수준, 샘플 속도, 채널 수 등에 따라 헤더를 수동으로 작성하려는 경우에만 원시 형식을 사용합니다.

다음 예제에서는 `SpeechConfig` 개체에서 `SpeechSynthesisOutputFormat`을 설정하여 고화질 RIFF 형식인 `Riff24Khz16BitMonoPcm`을 지정합니다. 이전 섹션의 예제와 마찬가지로 [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream)을 사용하여 결과의 메모리 내 스트림을 가져온 다음, 파일에 씁니다.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

프로그램이 다시 실행되면 `.wav` 파일을 지정한 경로에 씁니다.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML을 사용하여 음성 특성 사용자 지정

SSML(Speech Synthesis Markup Language)을 사용하면 XML 스키마에서 요청을 제출하여 피치, 발음, 말하기 속도, 볼륨 및 더 많은 텍스트 음성 변환 출력을 미세 조정할 수 있습니다. 이 섹션에서는 음성을 변경하는 예를 보여주지만, 보다 자세한 가이드는 [SSML 방법 문서](../../../speech-synthesis-markup.md)를 참조하세요.

SSML을 사용자 지정에 사용하려면 음성을 전환하는 간단한 변경 작업을 수행합니다.
먼저, SSML 구성에 대한 새 XML 파일(이 예제에서는 `ssml.xml`)을 루트 프로젝트 디렉터리에 만듭니다. 루트 요소는 항상 `<speak>`이며, 텍스트를 `<voice>` 요소에 래핑하면 `name` 매개 변수를 사용하여 음성을 변경할 수 있습니다. 지원되는 **인공신경망** 음성의 [전체 목록](../../../language-support.md#neural-voices)을 참조하세요.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-ChristopherNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

다음으로, XML 파일을 참조하도록 음성 합성 요청을 변경해야 합니다. 요청은 거의 같지만 `SpeakTextAsync()` 함수를 사용하는 대신 `SpeakSsmlAsync()`를 사용합니다. 이 함수에는 XML 문자열이 필요하므로 먼저 SSML 구성을 문자열로 로드합니다. 여기에서 결과 개체는 이전 예제와 정확히 동일합니다.

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("<paste-your-speech-key-here>", "<paste-your-speech-location/region-here>");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

> [!NOTE]
> SSML을 사용하지 않고 음성을 변경하려면 `SpeechConfig.SetSpeechSynthesisVoiceName("en-US-ChristopherNeural")`을 사용하여 `SpeechConfig`에서 속성을 설정하면 됩니다.

## <a name="get-facial-pose-events"></a>얼굴 포즈 이벤트 가져오기

음성은 얼굴 식의 애니메이션을 구동하는 좋은 방법일 수 있습니다.
[visemes](../../../how-to-speech-synthesis-viseme.md)는 특정 음소를 생성할 때 입술, 턱 및 혀의 위치와 같은 관찰된 음성의 주요 포즈를 나타내는 데 사용되는 경우가 많습니다.
음성 SDK에서 viseme 이벤트를 구독할 수 있습니다.
그런 다음, 음성 오디오가 재생될 때 viseme 이벤트를 적용하여 캐릭터의 얼굴에 애니메이션을 적용할 수 있습니다.
[viseme 이벤트를 가져오는 방법](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)에 대해 알아봅니다.
