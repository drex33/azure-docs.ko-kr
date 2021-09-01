---
author: yulin-li
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: yulili
ms.openlocfilehash: 7c64db9a503caff0bb2cda3e7dc576bb3e72884d
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423293"
---
이 빠른 시작에서는 Speech SDK를 사용하여 텍스트 음성 변환을 수행하기 위한 일반적인 디자인 패턴에 대해 알아봅니다.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [Go 빠른 시작 샘플](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/synthesizer)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기


작업을 수행하려면 먼저 [Go용 Speech SDK](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser)를 설치해야 합니다.

## <a name="text-to-speech-to-speaker"></a>화자로 텍스트 음성 변환

다음 코드 샘플을 사용하여 기본 오디오 출력 디바이스에 음성 합성을 실행합니다.
`subscription` 및 `region` 변수를 음성 키와 위치/지역으로 바꿉니다.
스크립트를 실행하면 입력 텍스트가 기본 화자로 표시됩니다.

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/common"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func synthesizeStartedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Synthesis started.")
}

func synthesizingHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesizing, audio chunk size %d.\n", len(event.Result.AudioData))
}

func synthesizedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesized, audio length %d.\n", len(event.Result.AudioData))
}

func cancelledHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation.")
}

func main() {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    audioConfig, err := audio.NewAudioConfigFromDefaultSpeakerOutput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        task := speechSynthesizer.SpeakTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        if outcome.Result.Reason == common.SynthesizingAudioCompleted {
            fmt.Printf("Speech synthesized to speaker for text [%s].\n", text)
        } else {
            cancellation, _ := speech.NewCancellationDetailsFromSpeechSynthesisResult(outcome.Result)
            fmt.Printf("CANCELED: Reason=%d.\n", cancellation.Reason)

            if cancellation.Reason == common.Error {
                fmt.Printf("CANCELED: ErrorCode=%d\nCANCELED: ErrorDetails=[%s]\nCANCELED: Did you update the subscription info?\n",
                    cancellation.ErrorCode,
                    cancellation.ErrorDetails)
            }
        }
    }
}
```

다음 명령을 실행하여 GitHub에서 호스팅되는 구성 요소에 연결되는 `go.mod` 파일을 만듭니다.

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

이제 코드를 빌드하고 실행합니다.

```shell
go build
go run quickstart
```

[`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) 및 [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer) 클래스에 대한 자세한 내용은 참조 문서를 확인하세요.

## <a name="text-to-speech-to-in-memory-stream"></a>메모리 내 스트림으로 텍스트 음성 변환

음성 애플리케이션을 개발하는 많은 시나리오에서는 파일에 직접 쓰지 않고 결과 오디오 데이터가 메모리 내 스트림으로 필요할 수 있습니다.
이렇게 하면 다음과 같은 사용자 지정 동작을 빌드할 수 있습니다.

* 결과 바이트 배열을 사용자 지정 다운스트림 서비스에 대한 검색 가능한 스트림으로 추상화합니다.
* 결과를 다른 API 또는 서비스와 통합합니다.
* 오디오 데이터를 수정하고, 사용자 지정 `.wav` 헤더 등을 작성합니다.

이전 예제에서 이 변경을 수행하는 것이 간단합니다. 먼저 제어를 향상하기 위해 이 시점부터 출력 동작을 수동으로 관리하므로 `AudioConfig`를 제거합니다. 그런 다음, `SpeechSynthesizer` 생성자의 `AudioConfig`에 대해 `nil`을 전달합니다.

> [!NOTE]
> 위의 스피커 출력 예제와 같이 생략하는 대신 `AudioConfig`에 대해 `nil`을 전달하면 현재 활성 출력 디바이스에서 기본적으로 오디오가 재생되지 않습니다.

이번에는 결과를 [`SpeechSynthesisResult`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesisResult) 변수에 저장합니다.
`AudioData` 속성은 출력 데이터의 `[]byte`를 반환합니다. 이 `[]byte`를 수동으로 사용하거나 [`AudioDataStream`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#AudioDataStream) 클래스를 사용하여 메모리 내 스트림을 관리할 수 있습니다.
다음 예제에서는 `NewAudioDataStreamFromSpeechSynthesisResult()` 정적 함수를 사용하여 결과에서 스트림을 가져옵니다.

`subscription` 및 `region` 변수를 음성 키와 위치/지역으로 바꿉니다.

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main(subscription string, region string) {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, nil)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        // StartSpeakingTextAsync sends the result to channel when the synthesis starts.
        task := speechSynthesizer.StartSpeakingTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        // in most case we want to streaming receive the audio to lower the latency,
        // we can use AudioDataStream to do so.
        stream, err := speech.NewAudioDataStreamFromSpeechSynthesisResult(outcome.Result)
        defer stream.Close()
        if err != nil {
            fmt.Println("Got an error: ", err)
            return
        }

        var all_audio []byte
        audio_chunk := make([]byte, 2048)
        for {
            n, err := stream.Read(audio_chunk)

            if err == io.EOF {
                break
            }

            all_audio = append(all_audio, audio_chunk[:n]...)
        }

        fmt.Printf("Read [%d] bytes from audio data stream.\n", len(all_audio))
    }
}
```

다음 명령을 실행하여 GitHub에서 호스팅되는 구성 요소에 연결되는 `go.mod` 파일을 만듭니다.

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

이제 코드를 빌드하고 실행합니다.

```shell
go build
go run quickstart
```

[`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) 및 [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer) 클래스에 대한 자세한 내용은 참조 문서를 확인하세요.

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

다음으로, XML 파일을 참조하도록 음성 합성 요청을 변경해야 합니다.
요청은 거의 같지만 `SpeakTextAsync()` 함수를 사용하는 대신 `SpeakSsmlAsync()`를 사용합니다. 이 함수에는 XML 문자열이 필요하므로 먼저 SSML 구성을 문자열로 로드합니다. 여기에서 결과 개체는 이전 예제와 정확히 동일합니다.

> [!NOTE]
> SSML을 사용하지 않고 음성을 변경하려면 `speechConfig.SetSpeechSynthesisVoiceName("en-US-ChristopherNeural")`을 사용하여 `SpeechConfig`에서 속성을 설정하면 됩니다.

## <a name="get-facial-pose-events"></a>얼굴 포즈 이벤트 가져오기

음성은 얼굴 식의 애니메이션을 구동하는 좋은 방법일 수 있습니다.
[visemes](../../../how-to-speech-synthesis-viseme.md)는 특정 음소를 생성할 때 입술, 턱 및 혀의 위치와 같은 관찰된 음성의 주요 포즈를 나타내는 데 사용되는 경우가 많습니다.
음성 SDK에서 viseme 이벤트를 구독할 수 있습니다.
그런 다음, 음성 오디오가 재생될 때 viseme 이벤트를 적용하여 캐릭터의 얼굴에 애니메이션을 적용할 수 있습니다.
[viseme 이벤트를 가져오는 방법](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)에 대해 알아봅니다.
