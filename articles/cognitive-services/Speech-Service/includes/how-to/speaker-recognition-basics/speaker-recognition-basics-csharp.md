---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 44867e190c203bdb03b7e92ae1499a57d2dcdd57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069156"
---
이 빠른 시작에서는 다음을 포함하여 Speech SDK를 사용하는 Speaker Recognition에 대한 기본적인 디자인 패턴을 알아봅니다.

* 텍스트 종속 및 텍스트 독립 확인
* 음성 그룹 간의 음성 샘플을 식별하는 화자 식별
* 음성 프로필 삭제

Speaker Recognition 개념에 대한 간략한 설명은 [개요](../../../speaker-recognition-overview.md) 문서를 참조하세요. 지원되는 플랫폼 목록은 왼쪽 탐색의 참조 노드를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Microsoft에서는 Speaker Recognition에 대한 액세스를 제한합니다. [Azure Cognitive Services Speaker Recognition 제한된 액세스 검토](https://aka.ms/azure-speaker-recognition)를 통해 사용을 신청합니다. 승인 후에는 Speaker Recognition API에 액세스할 수 있습니다. 

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>종속성 가져오기

이 문서의 예제를 실행하려면 다음 `using` 문을 스크립트 위쪽에 포함시킵니다.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)를 만들어야 합니다. 이 예제에서는 구독 키와 지역을 사용하여 [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig)를 만듭니다. 또한 이 문서의 나머지 부분에 사용할 몇 가지 기본 상용구 코드를 만들 수 있습니다. 이 문서의 나머지 부분에서는 사용자 지정을 위해 수정합니다.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        // replace with your own subscription region 
        string region = "YourSubscriptionRegion";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>텍스트-종속 확인

Speaker Verification는 화자가 알려진 또는 **등록된** 음성으로 일치하는지 확인하는 행위입니다. 첫 번째 단계는 음성 프로필을 **등록** 하는 것입니다. 따라서 서비스에 향후 음성 샘플과 비교할 내용이 있습니다. 이 예제에서는 **텍스트-종속** 전략을 사용 하여 프로필을 등록합니다. 이 전략을 사용 하려면 등록 및 확인에 대한 지정 암호 구문 목록을 사용해야 합니다. 지원되는 암호 구문 목록은 [참조 문서](/rest/api/speakerrecognition/)를 확인하세요.

먼저 `Program` 클래스에서 다음 함수를 만들어 음성 프로필을 등록합니다.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    using (var phraseResult = await client.GetActivationPhrasesAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine($"Speak the passphrase, \"${phraseResult.Phrases[0]}\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

이 함수에서 `await client.CreateProfileAsync()`는 실제로 새 음성 프로필을 만드는 것입니다. 만든 후에는 이 예제에서 `AudioConfig.FromDefaultMicrophoneInput()`을 사용하여 기본 입력 장치에서 오디오를 캡처하기 위해 오디오 샘플 입력 방법을 지정합니다. 다음으로 등록을 위해 남아 있는 샘플 수 및 필수 항목을 추적하는 `while` 루프에서 오디오 샘플을 등록합니다. 각 반복에서 `client.EnrollProfileAsync(profile, audioInput)`는 사용자의 마이크에 암호 구문을 말하도록 메시지를 표시하고, 샘플을 음성 프로필에 추가합니다.

등록이 완료된 후 `await SpeakerVerify(config, profile, profileMapping)`를 호출하여 방금 만든 프로필에 대해 확인합니다. 다른 함수를 추가하여 `SpeakerVerify`를 정의합니다.

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

이 함수에서는 확인을 위해 모델을 초기화 하기 위해 방금 만든 `VoiceProfile` 개체를 전달합니다. 그런 다음 `await speakerRecognizer.RecognizeOnceAsync(model)`은 암호 구문을 다시 입력하라는 메시지를 표시하지만 이번에는 음성 프로필에 대해 유효성을 검사하고 0.0-1.0 범위의 유사성 점수를 반환합니다. 또한 `result` 개체는 암호 구문의 일치 여부에 따라 `Accept` 또는 `Reject`을 반환합니다.

다음으로 `Main()` 함수를 수정하여 만든 새 함수를 호출합니다. 또한 함수 호출을 통해 참조로 전달되는 `Dictionary<string, string>`을 만듭니다. 이는 서비스에서 사용자가 읽을 수 있는 이름을 만든 `VoiceProfile`에 저장할 수 없고 개인 정보 취급 방침에 대해서만 ID 번호를 저장하기 때문입니다. `VerificationEnroll` 함수에서 텍스트 이름과 함께 새로 만든 ID를 사용하여 이 사전에 항목을 추가합니다. 사람이 읽을 수 있는 이름을 표시해야 하는 애플리케이션 개발 시나리오에서 **이 매핑을 저장해야 하고 서비스는 저장할 수 없습니다**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

스크립트를 실행하고, *내 음성이 패스포트라는 구절을 말하고, 등록을 위해 사용자를* 세 번 확인하고, 확인을 위해 한 번 더 추가적으로 확인합니다. 반환되는 결과는 확인에 대한 사용자 지정 임계값을 만드는 데 사용할 수 있는 유사성 점수입니다.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>텍스트 독립 확인

**텍스트-종속** 확인을 하는 것과 달리 **텍스트-독립** 을 확인합니다:

* 3개의 오디오 샘플이 필요하지 않지만 *는 총 20초의 오디오를 필요* 로 합니다.

`VerificationEnroll`함수에 대한 몇 가지를 간단히 변경하여 **텍스트 독립** 확인으로 전환합니다. 먼저 확인 유형을 `VoiceProfileType.TextIndependentVerification`로 변경합니다. 다음으로 `while`루프를 변경하여 `result.RemainingEnrollmentsSpeechLength`를 추적합니다. 그러면 오디오 20초가 캡처될 때까지 계속해서 사용자가 말을 하도록 요구합니다.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    using (var phraseResult = await client.GetActivationPhrasesAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine($"Speak the activation phrase, \"${phraseResult.Phrases[0]}\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

프로그램을 다시 실행합니다. 마찬가지로 유사성 점수가 반환됩니다.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Speak the activation phrase, "<FIRST ACTIVATION PHRASE>"
Remaining enrollment audio time needed: 00:00:15.3200000

Speak the activation phrase, "<FIRST ACTIVATION PHRASE>"
Remaining enrollment audio time needed: 00:00:09.8100008

Speak the activation phrase, "<FIRST ACTIVATION PHRASE>"
Remaining enrollment audio time needed: 00:00:05.1900000

Speak the activation phrase, "<FIRST ACTIVATION PHRASE>"
Remaining enrollment audio time needed: 00:00:00.8700000

Speak the activation phrase, "<FIRST ACTIVATION PHRASE>"
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>화자 식별

화자 식별은 지정 등록된 음성 그룹에서 **누가 말하고 있는지** 확인하는 데 사용됩니다. 이 프로세스는 **텍스트 독립 확인** 하는 것과 매우 유사합니다. 주요 차이점은 단일 프로필을 확인하는 것이 아니라 한 번에 여러 음성 프로필에 대해 확인할 수 있다는 것입니다.

여러 음성 프로필을 등록하는 함수 `IdentificationEnroll`를 만듭니다. 각 프로필에 대한 등록 프로세스는 **텍스트 독립 확인** 등록 프로세스와 동일하며, 각 프로필에 대해 20초 정도의 오디오가 필요합니다. 이 함수는 `profileNames`문자열 목록을 받아들이고 목록의 각 이름에 대해 새 음성 프로필을 만듭니다. 함수는 다음 함수에서 화자를 식별하는 데 사용하는 `VoiceProfile` 개체의 목록을 반환합니다.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    using (var phraseResult = await client.GetActivationPhrasesAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Speak the activation phrase, \"${phraseResult.Phrases[0]}\" to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

ID 요청을 제출하기 위해 다음 함수 `SpeakerIdentification`를 만듭니다. **화자 검증** 요청과 이 함수의 주요 차이점은 `VoiceProfile` 개체 목록을 수락하는 `SpeakerIdentificationModel.FromProfiles()`를 사용하는 것입니다. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

`Main()` 함수를 다음과 같이 변경합니다. `IdentificationEnroll()` 함수에 전달하는 `profileNames`문자열 목록을 만듭니다. 그러면 이 목록에 있는 각 이름에 대해 새 음성 프로필을 만들라는 메시지가 표시 되므로 친구나 동료를 위한 추가 프로필을 만들기 위해 더 많은 이름을 추가할 수 있습니다.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    // replace with your own subscription region 
    string region = "YourSubscriptionRegion";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

스크립트를 실행하면 첫 번째 프로필에 대한 음성 샘플을 등록하라는 메시지가 표시 됩니다. 등록이 완료되면 목록 `profileNames`의 각 이름에 대해 이 프로세스를 반복하라는 메시지가 표시됩니다. 각 등록을 완료한 후 사용자에게 **누구** 하고든 대화하라는 메시지가 표시되고 서비스는 등록된 음성 프로필 중에서 이 사용자를 식별하려고 합니다.

이 예제에서는 가장 근접한 일치 항목만 반환하고 이는 유사성 점수이지만 `SpeakerIdentification`함수에 `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)`을 추가하여 상위 5개 유사성 점수를 포함하는 전체 응답을 받을 수 있습니다.

## <a name="changing-audio-input-type"></a>오디오 입력 형식 변경

이 문서의 예제에서는 기본 장치 마이크를 오디오 샘플에 대한 입력으로 사용합니다. 그러나 마이크 입력 대신 오디오 파일을 사용해야 하는 경우에는 `AudioConfig.FromDefaultMicrophoneInput()`의 인스턴스를 `AudioConfig.FromWavFileInput(path/to/your/file.wav)`로 변경하여 파일 입력으로 전환 하면 됩니다. 예를 들어 등록용 마이크와 황인용 파일을 사용하여 입력을 혼합할 수 있습니다.

## <a name="deleting-voice-profile-enrollments"></a>음성 프로필 등록 삭제

등록된 프로필을 삭제하려면 `VoiceProfileClient` 개체에서 `DeleteProfileAsync()` 함수를 사용합니다. 다음 예제 함수는 알려진 음성 프로필 ID에서 음성 프로필을 삭제하는 방법을 보여줍니다.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```
