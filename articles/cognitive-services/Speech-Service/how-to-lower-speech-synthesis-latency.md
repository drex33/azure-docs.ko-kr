---
title: Speech SDK를 사용하여 음성 합성 대기 시간을 낮추는 방법
titleSuffix: Azure Cognitive Services
description: 스트리밍, 사전 연결 등을 비롯한 음성 SDK를 사용하여 음성 합성 대기 시간을 낮추는 방법입니다.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 38af8d1ffd422b151bebc2fea42c575f181cc72e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536378"
---
# <a name="lower-speech-synthesis-latency-using-speech-sdk"></a>Speech SDK를 사용하여 음성 합성 대기 시간 낮추기

> [!NOTE]
> 이 문서에는 Speech SDK 1.17.0 이상이 필요합니다.

합성 대기 시간은 애플리케이션에 매우 중요합니다.
이 문서에서는 대기 시간을 낮추고 최종 사용자에게 최상의 성능을 제공하기 위한 모범 사례를 소개합니다.

일반적으로 다음과 같이 `first byte latency` 및 `finish latency`에 의한 대기 시간을 측정합니다.

::: zone pivot="programming-language-csharp"

| 대기 시간 | Description | [SpeechSynthesisResult](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) 속성 키 |
|-----------|-------------|------------|
| 첫 번째 바이트 대기 시간 | 합성 작업 시작과 오디오 데이터의 첫 번째 청크 수신 사이의 지연 시간을 나타냅니다. | SpeechServiceResponse_SynthesisFirstByteLatencyMs |
| 완료 대기 시간 | 합성 작업 시작과 전체 합성 오디오 데이터의 수신 사이의 지연 시간을 나타냅니다. | SpeechServiceResponse_SynthesisFinishLatencyMs |

Speech SDK는 [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult)의 Properties 컬렉션에 대기 시간 기간을 배치합니다. 다음 샘플 코드에서는 이러한 값을 보여 줍니다.

```csharp
var result = await synthesizer.SpeakTextAsync(text);
Console.WriteLine($"first byte latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs)} ms");
Console.WriteLine($"finish latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs)} ms");
// you can also get the result id, and send to us when you need help for diagnosis
var resultId = result.ResultId;
```

::: zone-end

::: zone pivot="programming-language-cpp"

| 대기 시간 | Description | [SpeechSynthesisResult](/cpp/cognitive-services/speech/speechsynthesisresult) 속성 키 |
|-----------|-------------|------------|
| `first byte latency` | 합성이 시작되고 첫 번째 오디오 청크가 수신되는 사이의 시간 지연을 나타냅니다. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 합성이 시작되고 전체 합성 오디오가 수신되는 사이의 시간 지연을 나타냅니다. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Speech SDK는 대기 시간을 측정하여 [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult)의 속성 모음에 배치합니다. 이러한 항목을 가져오려면 다음 코드를 참조하세요.

```cpp
auto result = synthesizer->SpeakTextAsync(text).get();
auto firstByteLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFirstByteLatencyMs));
auto finishedLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFinishLatencyMs));
// you can also get the result id, and send to us when you need help for diagnosis
auto resultId = result->ResultId;
```

::: zone-end

::: zone pivot="programming-language-java"

| 대기 시간 | Description | [SpeechSynthesisResult](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) 속성 키 |
|-----------|-------------|------------|
| `first byte latency` | 합성이 시작되고 첫 번째 오디오 청크가 수신되는 사이의 시간 지연을 나타냅니다. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 합성이 시작되고 전체 합성 오디오가 수신되는 사이의 시간 지연을 나타냅니다. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Speech SDK는 대기 시간을 측정하여 [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult)의 속성 모음에 배치합니다. 이러한 항목을 가져오려면 다음 코드를 참조하세요.

```java
SpeechSynthesisResult result = synthesizer.SpeakTextAsync(text).get();
System.out.println("first byte latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs) + " ms.");
System.out.println("finish latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs) + " ms.");
// you can also get the result id, and send to us when you need help for diagnosis
String resultId = result.getResultId();
```

::: zone-end


::: zone pivot="programming-language-python"

| 대기 시간 | Description | [SpeechSynthesisResult](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) 속성 키 |
|-----------|-------------|------------|
| `first byte latency` | 합성이 시작되고 첫 번째 오디오 청크가 수신되는 사이의 시간 지연을 나타냅니다. | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 합성이 시작되고 전체 합성 오디오가 수신되는 사이의 시간 지연을 나타냅니다. | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Speech SDK는 대기 시간을 측정하여 [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult)의 속성 모음에 배치합니다. 이러한 항목을 가져오려면 다음 코드를 참조하세요.

```python
result = synthesizer.speak_text_async(text).get()
first_byte_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs))
finished_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs))
# you can also get the result id, and send to us when you need help for diagnosis
result_id = result.result_id
```

::: zone-end

::: zone pivot="programming-language-objectivec"

| 대기 시간 | Description | [SPXSpeechSynthesisResult](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) 속성 키 |
|-----------|-------------|------------|
| `first byte latency` | 합성이 시작되고 첫 번째 오디오 청크가 수신되는 사이의 시간 지연을 나타냅니다. | `SPXSpeechServiceResponseSynthesisFirstByteLatencyMs` |
| `finish latency` | 합성이 시작되고 전체 합성 오디오가 수신되는 사이의 시간 지연을 나타냅니다. | `SPXSpeechServiceResponseSynthesisFinishLatencyMs` |

Speech SDK는 대기 시간을 측정하여 [`SPXSpeechSynthesisResult`](/objectivec/cognitive-services/speech/spxspeechsynthesisresult)의 속성 모음에 배치합니다. 이러한 항목을 가져오려면 다음 코드를 참조하세요.

```Objective-C
SPXSpeechSynthesisResult *speechResult = [speechSynthesizer speakText:text];
int firstByteLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFirstByteLatencyMs]];
int finishedLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFinishLatencyMs]];
// you can also get the result id, and send to us when you need help for diagnosis
NSString *resultId = result.resultId;
```

::: zone-end

대부분의 경우 첫 번째 바이트 대기 시간은 완료 대기 시간보다 훨씬 낮습니다.
첫 번째 바이트 대기 시간은 텍스트 길이와는 독립적이지만 완료 대기 시간은 텍스트 길이에 따라 증가합니다.

이상적으로 사용자가 경험하는 대기 시간(사용자가 소리를 듣기 전의 대기 시간)을 하나의 네트워크 경로 트립 시간과 음성 합성 서비스의 첫 번째 오디오 청크 대기 시간으로 최소화하는 것이 좋습니다.

## <a name="streaming"></a>스트리밍

스트리밍은 대기 시간을 줄이는 데 중요합니다.
클라이언트 코드는 첫 번째 오디오 청크가 수신될 때 재생을 시작할 수 있습니다.
서비스 시나리오에서 전체 오디오를 대기하지 않고 클라이언트에 직접 오디오 청크를 전달할 수 있습니다.

::: zone pivot="programming-language-csharp"

Speech SDK의 [`PullAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pullaudiooutputstream), [`PushAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudiooutputstream), [`Synthesizing` 이벤트](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing) 및 [`AudioDateStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream)을 사용하여 스트리밍을 사용하도록 설정할 수 있습니다.

`AudioDateStream`을 예로 들겠습니다.

```csharp
using (var synthesizer = new SpeechSynthesizer(config, null as AudioConfig))
{
    using (var result = await synthesizer.StartSpeakingTextAsync(text))
    {
        using (var audioDataStream = AudioDataStream.FromResult(result))
        {
            byte[] buffer = new byte[16000];
            uint filledSize = 0;
            while ((filledSize = audioDataStream.ReadData(buffer)) > 0)
            {
                Console.WriteLine($"{filledSize} bytes received.");
            }
        }
    }
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

Speech SDK의 [`PullAudioOutputStream`](/cpp/cognitive-services/speech/audio-pullaudiooutputstream), [`PushAudioOutputStream`](/cpp/cognitive-services/speech/audio-pushaudiooutputstream), [`Synthesizing` 이벤트](/cpp/cognitive-services/speech/speechsynthesizer#synthesizing) 및 [`AudioDateStream`](/cpp/cognitive-services/speech/audiodatastream)을 사용하여 스트리밍을 사용하도록 설정할 수 있습니다.

`AudioDateStream`을 예로 들겠습니다.

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto result = synthesizer->SpeakTextAsync(text).get();
auto audioDataStream = AudioDataStream::FromResult(result);
uint8_t buffer[16000];
uint32_t filledSize = 0;
while ((filledSize = audioDataStream->ReadData(buffer, sizeof(buffer))) > 0)
{
    cout << filledSize << " bytes received." << endl;
}
```

::: zone-end

::: zone pivot="programming-language-java"

Speech SDK의 [`PullAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pullaudiooutputstream), [`PushAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pushaudiooutputstream), [`Synthesizing` 이벤트](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_Synthesizing) 및 [`AudioDateStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream)을 사용하여 스트리밍을 사용하도록 설정할 수 있습니다.

`AudioDateStream`을 예로 들겠습니다.

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(config, null);
SpeechSynthesisResult result = synthesizer.StartSpeakingTextAsync(text).get();
AudioDataStream audioDataStream = AudioDataStream.fromResult(result);
byte[] buffer = new byte[16000];
long filledSize = audioDataStream.readData(buffer);
while (filledSize > 0) {
    System.out.println(filledSize + " bytes received.");
    filledSize = audioDataStream.readData(buffer);
}
```

::: zone-end

::: zone pivot="programming-language-python"

Speech SDK의 [`PullAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pullaudiooutputstream), [`PushAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pushaudiooutputstream), [`Synthesizing` 이벤트](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#synthesizing) 및 [`AudioDateStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream)을 사용하여 스트리밍을 사용하도록 설정할 수 있습니다.

`AudioDateStream`을 예로 들겠습니다.

```python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = speech_synthesizer.start_speaking_text_async(text).get()
audio_data_stream = speechsdk.AudioDataStream(result)
audio_buffer = bytes(16000)
filled_size = audio_data_stream.read_data(audio_buffer)
while filled_size > 0:
    print("{} bytes received.".format(filled_size))
    filled_size = audio_data_stream.read_data(audio_buffer)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

Speech SDK의 [`SPXPullAudioOutputStream`](/objectivec/cognitive-services/speech/spxpullaudiooutputstream), [`SPXPushAudioOutputStream`](/objectivec/cognitive-services/speech/spxpushaudiooutputstream), [`Synthesizing` 이벤트](/objectivec/cognitive-services/speech/spxspeechsynthesizer#addsynthesizingeventhandler) 및 [`SPXAudioDataStream`](/objectivec/cognitive-services/speech/spxaudiodatastream)을 사용하여 스트리밍을 사용하도록 설정할 수 있습니다.

`AudioDateStream`을 예로 들겠습니다.

```Objective-C
SPXSpeechSynthesizer *synthesizer = [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig audioConfiguration:nil];
SPXSpeechSynthesisResult *speechResult = [synthesizer startSpeakingText:inputText];
SPXAudioDataStream *stream = [[SPXAudioDataStream alloc] initFromSynthesisResult:speechResult];
NSMutableData* data = [[NSMutableData alloc]initWithCapacity:16000];
while ([stream readData:data length:16000] > 0) {
    // Read data here
}
```

::: zone-end

## <a name="pre-connect-and-reuse-speechsynthesizer"></a>SpeechSynthesizer 사전 연결 및 다시 사용

Speech SDK는 websocket을 사용하여 서비스와 통신합니다.
이상적으로 네트워크 대기 시간은 하나의 RTT(경로 트립 시간)여야 합니다.
연결이 새로 설정된 경우 네트워크 대기 시간에 연결을 설정하기 위한 추가 시간이 포함됩니다.
websocket 연결을 설정하려면 TCP 핸드셰이크, SSL 핸드셰이크, HTTP 연결 및 프로토콜 업그레이드가 필요하며 이로 인해 시간 지연이 발생합니다.
연결 대기 시간을 방지하려면 `SpeechSynthesizer`를 미리 연결하고 다시 사용하는 것이 좋습니다.

### <a name="pre-connect"></a>연결 전

사전에 연결하려면 연결이 곧 필요할 것을 알 때 Speech Service에 대한 연결을 설정합니다. 예를 들어 클라이언트에서 음성 봇을 빌드하는 경우 사용자가 대화를 시작할 때 음성 합성 서비스에 미리 연결하고 봇 회신 텍스트가 준비되면 `SpeakTextAsync`를 호출할 수 있습니다.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(uspConfig, null as AudioConfig))
{
    using (var connection = Connection.FromSpeechSynthesizer(synthesizer))
    {
        connection.Open(true);
    }
    await synthesizer.SpeakTextAsync(text);
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto connection = Connection::FromSpeechSynthesizer(synthesizer);
connection->Open(true);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, (AudioConfig) null);
Connection connection = Connection.fromSpeechSynthesizer(synthesizer);
connection.openConnection(true);
```

::: zone-end

::: zone pivot="programming-language-python"

```python
synthesizer = speechsdk.SpeechSynthesizer(config, None)
connection = speechsdk.Connection.from_speech_synthesizer(synthesizer)
connection.open(True)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer* synthesizer = [[SPXSpeechSynthesizer alloc]initWithSpeechConfiguration:self.speechConfig audioConfiguration:nil];
SPXConnection* connection = [[SPXConnection alloc]initFromSpeechSynthesizer:synthesizer];
[connection open:true];
```

::: zone-end

> [!NOTE]
> 합성 텍스트를 사용할 수 있는 경우 `SpeakTextAsync`를 호출하여 오디오를 합성합니다. SDK는 연결을 처리합니다.

### <a name="reuse-speechsynthesizer"></a>SpeechSynthesizer 다시 사용

연결 대기 시간을 줄이는 또 다른 방법은 `SpeechSynthesizer`를 다시 사용하는 것입니다. 따라서 각 합성에 대해 새 `SpeechSynthesizer`를 만들 필요가 없습니다.
서비스 시나리오에서 개체 풀을 사용하는 것이 좋습니다. [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) 및 [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java)에 대한 샘플 코드를 참조하세요.


## <a name="transmit-compressed-audio-over-the-network"></a>네트워크를 통해 압축된 오디오 전송

네트워크가 불안정하거나 대역폭이 제한된 경우에도 페이로드 크기는 대기 시간에 영향을 줍니다.
한편, 압축된 오디오 형식은 사용자의 네트워크 대역폭을 절약하는 데 도움이 됩니다. 이는 모바일 사용자에게 특히 유용합니다.

`opus`, `webm`, `mp3`, `silk` 등을 비롯한 다양한 압축 형식을 지원합니다. [SpeechSynthesisOutputFormat](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)의 전체 목록을 참조하세요.
예를 들어 `Riff24Khz16BitMonoPcm` 형식의 비트 전송률은 384kbps이지만 `Audio24Khz48KBitRateMonoMp3`의 비용은 48kbps입니다.
Speech SDK는 `pcm` 출력 형식이 설정되어 있고 `GStreamer`가 제대로 설치된 경우 전송에 압축된 형식을 자동으로 사용합니다.
Speech SDK에 대해 `GStreamer`를 설치하고 구성하려면 [이 지침](how-to-use-codec-compressed-audio-input-streams.md)을 참조하세요.

## <a name="others-tips"></a>기타 팁

### <a name="cache-crl-files"></a>CRL 파일 캐시

Speech SDK는 CRL 파일을 사용하여 인증을 확인합니다.
CRL 파일을 만료될 때까지 캐시하면 매번 CRL 파일을 다운로드하지 않아도 됩니다.
자세한 내용은 [Linux용 OpenSSL을 구성하는 방법](how-to-configure-openssl-linux.md#certificate-revocation-checks)을 참조하세요.

### <a name="use-latest-speech-sdk"></a>최신 Speech SDK 사용

Speech SDK의 성능을 향상시킬 수 있으므로 애플리케이션에서 최신 Speech SDK를 사용해 보세요.
예를 들어 [1.16.0](releasenotes.md#speech-sdk-1160-2021-march-release)의 `TCP_NODELAY` 설정 문제를 해결하여 추가 경로 트립 시간을 줄였습니다.

## <a name="load-test-guideline"></a>부하 테스트 지침

부하 테스트를 사용하여 음성 합성 서비스 용량과 대기 시간을 테스트할 수 있습니다.
다음은 몇 가지 지침입니다.

 - 음성 합성 서비스는 자동 크기 조정 기능을 제공하지만 규모를 확장하는 데 시간이 걸립니다. 짧은 시간 내에 동시성이 증가하는 경우 클라이언트는 긴 대기 시간 또는 `429` 오류 코드(너무 많은 요청)를 받을 수 있습니다. 따라서 부하 테스트의 단계별 동시성 단계를 늘리는 것이 좋습니다. 자세한 내용과 특히 [워크로드 패턴 예제](speech-services-quotas-and-limits.md#example-of-a-workload-pattern-best-practice)를 보려면 [이 문서를 참조](speech-services-quotas-and-limits.md#general-best-practices-to-mitigate-throttling-during-autoscaling)하세요.
 - 개체 풀([C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) 및 [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java))을 부하 테스트에 사용하고 대기 시간 수치를 얻어 샘플을 활용할 수 있습니다. 대상 동시성에 맞게 샘플의 테스트 차례와 동시성을 수정할 수 있습니다.
 - 서비스에는 실제 트래픽에 따른 할당량 제한이 있으므로 실제 트래픽보다 큰 동시성을 사용하여 부하 테스트를 수행하려면 테스트 전에 연결합니다.

## <a name="next-steps"></a>다음 단계

* GitHub에서 [샘플 참조](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master)
