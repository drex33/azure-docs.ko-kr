---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: eur
ms.openlocfilehash: 4f396aa1a3ac1b5b02039f5ae525c4a8581780bc
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508938"
---
## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-voice-signatures"></a>음성 서명 만들기

(사전 등록된 사용자 프로필을 사용하여 특정 참가자를 식별하지 않으려면 이 단계를 건너뛸 수 있습니다.)

사용자 프로필을 등록하는 첫 번째 단계는 고유 스피커로 식별할 수 있도록 대화 참가자의 음성 서명을 만드는 것입니다. 음성 서명을 만들기 위한 입력 `.wav` 오디오 파일은 16비트, 16kHz 샘플 속도이고 단일 채널(모노) 형식이어야 합니다. 각 오디오 샘플의 권장 길이는 30초~2분 사이입니다. 오디오 샘플이 너무 짧으면 화자를 인식할 때 정확도가 떨어집니다. `.wav` 파일은 고유한 음성 프로필이 생성되도록 **한 사람의** 음성 샘플이어야 합니다.

다음 예제에서는 C#에서 [REST API를 사용](https://aka.ms/cts/signaturegenservice)하여 음성 서명을 만드는 방법을 보여 줍니다. `subscriptionKey`, `region` 및 샘플 `.wav` 파일 경로의 실제 정보를 대체해야 합니다.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Runtime.Serialization;
using System.Threading.Tasks;
using Newtonsoft.Json;

[DataContract]
internal class VoiceSignature
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public VoiceSignatureData Signature { get; private set; }

    [DataMember]
    public string Transcription { get; private set; }
}

[DataContract]
internal class VoiceSignatureData
{
    internal VoiceSignatureData()
    { }

    internal VoiceSignatureData(int version, string tag, string data)
    {
        this.Version = version;
        this.Tag = tag;
        this.Data = data;
    }

    [DataMember]
    public int Version { get; private set; }

    [DataMember]
    public string Tag { get; private set; }

    [DataMember]
    public string Data { get; private set; }
}

private static async Task<string> GetVoiceSignatureString()
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";

    byte[] fileBytes = File.ReadAllBytes("path-to-voice-sample.wav");
    var content = new ByteArrayContent(fileBytes);
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
    
    var jsonData = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<VoiceSignature>(jsonData);
    return JsonConvert.SerializeObject(result.Signature);
}
```

`GetVoiceSignatureString()` 함수를 실행하면 음성 서명 문자열이 올바른 형식으로 반환됩니다. 함수를 두 번 실행하면 아래 `voiceSignatureStringUser1` 및 `voiceSignatureStringUser2` 변수에 대한 입력으로 두 개의 문자열을 사용할 수 있습니다.

> [!NOTE]
> 음성 서명을 만들려면 REST API를 사용 **해야 합니다**.

## <a name="transcribe-conversations"></a>대화 기록

다음 샘플 코드에서는 두 개의 스피커에 대해 실시간으로 대화를 기록하는 방법을 보여 줍니다. 위에 표시된 대로 각 스피커에 대한 음성 서명 문자열을 이미 만들었다고 가정합니다. 기록할 오디오에 관한 `subscriptionKey`, `region` 및 경로 `filepath`의 실제 정보를 대체합니다.

미리 등록된 사용자 프로필을 사용하지 않으면 알 수 없는 사용자를 스피커1, 스피커2 등으로 처음 인식하는 데 몇 초가 더 걸립니다.

> [!NOTE]
> 서명 만들기를 위해 애플리케이션 전체에서 동일한 `subscriptionKey`가 사용되는지 확인합니다. 그렇지 않으면 오류가 발생합니다. 

이 샘플 코드에서는 다음을 수행합니다.

* 기록할 샘플 `.wav` 파일에서 `AudioConfig`를 만듭니다.
* `CreateConversationAsync()`를 사용하여 `Conversation`을 만듭니다.
* 생성자를 사용하여 `ConversationTranscriber`를 만들고 필요한 이벤트를 구독합니다.
* 대화에 참가자를 추가합니다. 문자열 `voiceSignatureStringUser1` 및 `voiceSignatureStringUser2`는 위의 단계에서 `GetVoiceSignatureString()` 함수의 출력으로 제공됩니다.
* 대화에 참가하고 대화 내용 기록을 시작합니다.
* 음성 샘플을 제공 하지 않고 스피커를 차별화 하려면 `DifferentiateGuestSpeakers` [대화 내용 개요](../../../conversation-transcription.md)에서와 같이 기능을 사용 하도록 설정 하세요. 

> [!NOTE]
> `AudioStreamReader`는 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/csharp/dotnet/conversation-transcription/helloworld/AudioStreamReader.cs)에서 가져올 수 있는 도우미 클래스입니다.

`TranscribeConversationsAsync()` 함수를 호출하여 대화 내용 기록을 시작합니다.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

class transcribe_conversation
{
// all your other code

public static async Task TranscribeConversationsAsync(string voiceSignatureStringUser1, string voiceSignatureStringUser2)
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav";

    var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // en-us by default. Adding this code to specify other languages, like zh-cn.
    // config.SpeechRecognitionLanguage = "zh-cn";
    var stopRecognition = new TaskCompletionSource<int>();

    using (var audioInput = AudioConfig.FromWavFileInput(filepath))
    {
        var meetingID = Guid.NewGuid().ToString();
        using (var conversation = await Conversation.CreateConversationAsync(config, meetingID))
        {
            // create a conversation transcriber using audio stream input
            using (var conversationTranscriber = new ConversationTranscriber(audioInput))
            {
                conversationTranscriber.Transcribing += (s, e) =>
                {
                    Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                };

                conversationTranscriber.Transcribed += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                conversationTranscriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopRecognition.TrySetResult(0);
                    }
                };

                conversationTranscriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine($"\nSession started event. SessionId={e.SessionId}");
                };

                conversationTranscriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine($"\nSession stopped event. SessionId={e.SessionId}");
                    Console.WriteLine("\nStop recognition.");
                    stopRecognition.TrySetResult(0);
                };

                // Add participants to the conversation.
                var speaker1 = Participant.From("User1", "en-US", voiceSignatureStringUser1);
                var speaker2 = Participant.From("User2", "en-US", voiceSignatureStringUser2);
                await conversation.AddParticipantAsync(speaker1);
                await conversation.AddParticipantAsync(speaker2);

                // Join to the conversation and start transcribing
                await conversationTranscriber.JoinConversationAsync(conversation);
                await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                // waits for completion, then stop transcription
                Task.WaitAny(new[] { stopRecognition.Task });
                await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
            }
         }
      }
   }
}
```

