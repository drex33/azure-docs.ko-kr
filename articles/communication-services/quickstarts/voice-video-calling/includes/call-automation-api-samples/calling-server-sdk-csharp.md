---
title: 포함 파일
description: C#
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 264c59f53e05d79e9bc4118d4546d8e9bb07e2d5
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133405901"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/)를 참조하세요.
- [Visual Studio(2019 이상)](https://visualstudio.microsoft.com/vs/)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472)(Visual Studio 인스턴스에 따라 32비트 또는 64비트 버전을 설치해야 함).
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../../create-communication-resource.md)를 참조하세요. 이 샘플에 대한 **연결 문자열** 리소스를 기록해야 합니다.
- 새 Azure Communication Services 리소스의 전화 번호를 받습니다. 자세한 내용은 [전화 번호 받기](../../../telephony/get-phone-number.md?pivots=platform-azp)를 참조하세요.
- [ngrok](https://www.ngrok.com/download)를 다운로드하여 설치합니다. 샘플이 로컬로 실행되면 ngrok는 모든 이벤트를 수신할 수 있도록 설정합니다.
- (선택 사항) 애플리케이션에서 재생할 사용자 지정 메시지를 생성하기 위한 Azure Speech 리소스를 만듭니다. [이 참고 자료](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free)에 따라 리소스를 만듭니다.

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/OutboundCallReminder)에서 이 빠른 시작에 대한 최종 코드를 찾을 수 있습니다. 이 샘플에서는 Microsoft Cognitive Services Speech SDK를 사용합니다. 이 페이지에서 Microsoft Cognitive Services Speech SDK를 다운로드하면 해당 [라이선스](https://aka.ms/csspeech/license201809)를 승인하는 것입니다.

## <a name="object-model"></a>개체 모델

다음 클래스는 C#용 Azure Communication Calling Server SDK의 주요 기능 중 일부를 처리합니다.

| 속성                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | 이 클래스는 통화 기능에 필요합니다. Communication Services 리소스 연결 문자열을 사용하여 CallingServerClient 인스턴스를 만들고, 호출을 시작/종료하며, 오디오를 재생/취소하고, 참가자를 추가/제거합니다. |
| CommunicationIdentityClient | 이 클래스는 통신 사용자 ID를 만들거나 삭제하는 데 필요합니다. |

## <a name="create-a-call-client"></a>호출 클라이언트 만들기

호출 클라이언트를 만들려면 Communication Services 연결 문자열을 사용하여 호출 클라이언트 개체에 전달합니다.

```csharp
this.CallClient = new CallingServerClient("<Connection_String>");
```

## <a name="create-user-identity"></a>사용자 ID 만들기

Communication Services 리소스 연결 문자열을 사용하여 `CommunicationIdentityClient` 개체를 만든 다음, `CreateUserAsync` 메서드를 사용하여 원본 사용자 ID를 만듭니다.

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
var user = await client.CreateUserAsync().ConfigureAwait(false);
```

## <a name="delete-user-identity"></a>사용자 ID 삭제

`CommunicationIdentityClient` 개체의 `DeleteUserAsync` 메서드를 사용하여 사용자 ID 삭제:

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
await client.DeleteUserAsync(new CommunicationUserIdentifier(source)).ConfigureAwait(false);
```

## <a name="create-a-call"></a>호출 만들기

`CallingServerClient` 개체의 `CreateCallConnectionAsync` 메서드를 사용하여 호출을 시작합니다. 다음은 `CreateCallConnectionAsync` 메서드에 대한 매개 변수입니다.
- `source`는 `CommunicationIdentifier` 형식에 속한 호출자의 소스 사용자 ID입니다.
- `targets`는 `IEnumerable<CommunicationIdentifier>` 형식의 대상 ID 목록입니다.
- `options`는 `CreateCallOptions` 형식의 호출에 대한 옵션입니다. 다음은 `CreateCallOptions` 메서드에 대한 매개 변수입니다.

    - `callbackUri`는 애플리케이션의 콜백 URI입니다.
    - `requestedModalities`는 요청 호출 형식입니다. 유형은 `IEnumerable<MediaType>`입니다. 값은 오디오 또는 비디오일 수 있습니다.
    - `requestedCallEvents`는 요청된 콜백 이벤트입니다. 이것은 `IEnumerable<EventSubscriptionType>`의 형식입니다. 이 매개 변수를 사용하여 `EventSubscriptionType.ParticipantsUpdated` 및 `EventSubscriptionType.DtmfReceived` 이벤트를 구독합니다.

- `cancellationToken`은 취소 토큰입니다. 유형은 `CancellationToken`입니다.

```csharp
var source = new CommunicationUserIdentifier(callConfiguration.SourceIdentity);
var target = new PhoneNumberIdentifier(targetPhoneNumber);
var createCallOption = new CreateCallOptions(
    new Uri(callConfiguration.AppCallbackUrl),
    new List<MediaType> { MediaType.Audio },
    new List<EventSubscriptionType> { EventSubscriptionType.ParticipantsUpdated, EventSubscriptionType.DtmfReceived }
    );
createCallOption.AlternateCallerId = new PhoneNumberIdentifier(callConfiguration.SourcePhoneNumber);

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing CreateCall operation");
var call = await callClient.CreateCallConnectionAsync(source,
    new List<CommunicationIdentifier>() { target },
    createCallOption, reportCancellationToken)
    .ConfigureAwait(false);
```

`CreateCallConnectionAsync` 메서드는 오디오 재생, 오디오 취소 및 중단과 같은 그 밖의 호출 작업에 사용할 수 있는 `CallConnection` 개체를 반환합니다.

## <a name="play-audio"></a>오디오 재생

통화 연결이 생성되면 `CallConnection` 개체의 `PlayAudioAsync` 메서드를 사용하여 수신자에게 오디오 메시지를 재생할 수 있습니다. `PlayAudioAsync` 메서드가 지원하는 매개 변수는 다음과 같습니다.

- `options`(필수)는 오디오 재생 옵션입니다. 유형은 `PlayAudioOptions`입니다. `PlayAudioOptions` 개체가 지원하는 매개 변수는 다음과 같습니다.
    - `AudioFileUri`(필수)는 재생할 메시지가 있는 오디오 파일의 URI입니다.
    - `OperationContext`(필수)는 요청 컨텍스트의 고유 ID입니다.
    - `Loop`(선택 사항): 오디오 메시지를 반복하려면 true로 설정됩니다.
- `cancellationToken`은 취소 토큰입니다. 유형은 `CancellationToken`입니다.

```csharp
// Preparing data for request
var playAudioRequest = new PlayAudioOptions()
{
    AudioFileUri = new Uri(callConfiguration.AudioFileUrl),
    OperationContext = Guid.NewGuid().ToString(),
    Loop = true,
};

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
var response = await callConnection.PlayAudioAsync(playAudioRequest, reportCancellationToken).ConfigureAwait(false);
```

이 `PlayAudioAsync` 메서드는 `OperationStatus`를 가져오는 데 사용할 수 있는 `PlayAudioResponse`를 반환합니다. `OperationStatus`는 `NotStarted`, `Running`, `Completed`, 또는 `Failed` 같은 값을 가질 수 있습니다.

```csharp
Logger.LogMessage(Logger.MessageType.INFORMATION, $"Play Audio state: {response.Value.Status}");
```

## <a name="add-a-participant-to-the-call"></a>호출에 참가자 추가

`CallConnection` 개체의 `AddParticipantAsync` 메서드를 사용하여 호출에 참가자를 추가합니다. `AddParticipantAsync` 메서드가 지원하는 매개 변수는 다음과 같습니다.

- `participant`는 `CommunicationUserIdentifier` 또는 `PhoneNumberIdentifier` 형식의 참가자 식별자입니다.
- `alternateCallerId`는 소스 호출자 ID입니다.
- `operationContext`는 요청 컨텍스트의 고유 ID입니다.
- `cancellationToken`(선택 사항)은 취소 토큰입니다.


```csharp
// Preparing data for request
var operationContext = Guid.NewGuid().ToString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity)
{
    var response = await callConnection.AddParticipantAsync(new CommunicationUserIdentifier(addedParticipant), null, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity)
{
    var response = await callConnection.AddParticipantAsync(new PhoneNumberIdentifier(addedParticipant), callConfiguration.SourcePhoneNumber, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
```

## <a name="cancel-media-processing"></a>미디어 처리 취소

`CallConnection` 개체의 `CancelAllMediaOperationsAsync` 메서드를 사용하여 오디오 재생 작업을 취소합니다. `CancelAllMediaOperationsAsync` 메서드에 지원되는 매개 변수는 다음과 같습니다.

- `operationContext`는 요청 컨텍스트의 고유 ID입니다.
- `reportCancellationToken`은 취소 토큰입니다. 유형은 `CancellationToken`입니다.

```csharp
var operationContext = Guid.NewGuid().ToString();
var response = await callConnection.CancelAllMediaOperationsAsync(operationContext, reportCancellationToken).ConfigureAwait(false);
```

## <a name="hang-up-the-call"></a>호출 중단

`CallConnection` 개체의 `HangupAsync` 메서드를 사용하여 호출을 중단합니다. `HangupAsync` 메서드에 지원되는 매개 변수는 다음과 같습니다.

- `reportCancellationToken`은 취소 토큰입니다. 유형은 `CancellationToken`입니다.

```csharp
var hangupResponse = await callConnection.HangupAsync(reportCancellationToken).ConfigureAwait(false);
```