---
title: 포함 파일
description: Java 호출 자동화
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: d603f27d198b929ede7da1731ebfb9c9a33d0835
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133406092"
---
## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.
- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/)를 참조하세요.
- [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-install), 버전 11 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../../create-communication-resource.md)를 참조하세요. 이 샘플에 대한 **연결 문자열** 리소스를 기록해야 합니다.
- 새 Azure Communication Services 리소스의 전화 번호를 받습니다. 자세한 내용은 [전화 번호 받기](../../../telephony/get-phone-number.md?pivots=platform-azp)를 참조하세요.
- [ngrok](https://www.ngrok.com/download)를 다운로드하여 설치합니다. 샘플이 로컬로 실행되면 ngrok는 모든 이벤트를 수신할 수 있도록 설정합니다.
- (선택 사항) 애플리케이션에서 재생할 사용자 지정 메시지를 생성하기 위한 Azure Speech 리소스를 만듭니다. [이 참고 자료](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free)에 따라 리소스를 만듭니다.

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/OutboundCallReminder)에서 이 빠른 시작에 대한 최종 코드를 찾을 수 있습니다. 이 샘플에서는 Microsoft Cognitive Services Speech SDK를 사용합니다. 이 페이지에서 Microsoft Cognitive Services Speech SDK를 다운로드하면 해당 [라이선스](https://aka.ms/csspeech/license201809)를 승인하는 것입니다.

## <a name="add-the-package-references-for-the-calling-server-sdk"></a>호출 서버 SDK에 대한 패키지 참조 추가

POM 파일에서 호출 API를 사용하여 `azure-communication-callingserver` 패키지를 참조합니다.

```java
<dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-callingserver</artifactId>
      <version>1.0.0-beta.2</version>
</dependency>
```

사용자 ID를 만들려면 애플리케이션에서`azure-communication-identity` 패키지를 참조해야 합니다.

```java
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.1.1</version>
      <exclusions>
        <exclusion>
          <groupId>com.azure</groupId>
          <artifactId>azure-communication-common</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
```

## <a name="object-model"></a>개체 모델

다음 클래스는 Java용 Azure Communication Calling Server SDK의 주요 기능 중 일부를 처리합니다.

| 속성                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | 이 클래스는 CallingServerClient의 인스턴스를 만드는 데 사용됩니다.|
| CallingServerClient | 이 클래스는 통화 기능에 필요합니다. CallingServerClientBuilder를 통해 인스턴스를 얻고 이를 사용하여 통화 시작/끊기, 오디오 재생/취소 및 참가자 추가/제거를 수행할 수 있습니다. |
| CommunicationIdentityClient | 이 클래스는 통신 사용자 ID를 만들거나 삭제하는 데 필요합니다. |

## <a name="create-a-call-client"></a>호출 클라이언트 만들기

Communication Services 연결 문자열과 httpClient 개체는 각각 `connectionString()` 및 `httpClient()` 함수를 통해 `CallingServerClientBuilder`에 전달되어야 합니다. 호출 클라이언트를 만들려면 `CallingServerClientBuilder` 개체의 `buildClient()` 함수를 사용합니다.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder callClientBuilder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
        .connectionString(this.callConfiguration.ConnectionString);

this.callingServerClient = callClientBuilder.buildClient();
```

## <a name="create-user-identity"></a>사용자 ID 만들기

`CommunicationIdentityClientBuilder` 개체를 사용하고 `connectionString()` 함수를 통해 Communication Services 리소스 연결 문자열을 설정하여 `CommunicationIdentityClient` 개체를 만든 다음, `createUser` 함수를 사용하여 원본 사용자 ID를 만듭니다.

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
CommunicationUserIdentifier user = client.createUser();
```

## <a name="delete-user-identity"></a>사용자 ID 삭제

`CommunicationIdentityClient` 개체의 `deleteUser` 함수를 사용하여 사용자 ID를 삭제합니다.

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
client.deleteUser(new CommunicationUserIdentifier(source));    
```

## <a name="create-a-call"></a>호출 만들기

`CallingServerClient` 개체의 `createCallConnectionWithResponse` 메서드를 사용하여 호출을 시작합니다. `createCallConnectionWithResponse` 메서드가 지원하는 매개 변수는 다음과 같습니다.
- `source`는 `CommunicationUserIdentifier` 형식에 속한 호출자의 소스 사용자 ID입니다.
- `targets`는 `Iterable<CommunicationIdentifier>` 형식의 대상 ID 목록입니다.
- `options`는 `CreateCallOptions` 형식의 호출에 대한 옵션입니다. 다음은 `CreateCallOptions` 메서드에 대한 매개 변수입니다.

    - `callbackUri`는 애플리케이션의 콜백 URI입니다.
    - `requestedModalities`는 요청 호출 형식입니다. 유형은 `Iterable<MediaType>`입니다. 값은 오디오 또는 비디오일 수 있습니다.
    - `requestedCallEvents`는 `Iterable<EventSubscriptionType>` 형식의 요청된 콜백 이벤트입니다. 이 매개 변수를 사용하여 `EventSubscriptionType.PARTICIPANTS_UPDATED` 및 `EventSubscriptionType.DTMF_RECEIVED` 이벤트를 구독합니다.

- `CreateCallOptions` 개체의 `setAlternateCallerId()` 함수를 사용하여 원본 호출자 ID를 대체 호출자 ID로 설정합니다.

```java
CommunicationUserIdentifier source = new CommunicationUserIdentifier(this.callConfiguration.SourceIdentity);
PhoneNumberIdentifier target = new PhoneNumberIdentifier(targetPhoneNumber);
List<MediaType> callModality = new ArrayList<>() { {add(MediaType.AUDIO);} };
List<EventSubscriptionType> eventSubscriptionType = new ArrayList<>() {
        {add(EventSubscriptionType.PARTICIPANTS_UPDATED); add(EventSubscriptionType.DTMF_RECEIVED);}};

CreateCallOptions createCallOption = new CreateCallOptions(this.callConfiguration.appCallbackUrl,
        callModality, eventSubscriptionType);
createCallOption.setAlternateCallerId(new PhoneNumberIdentifier(this.callConfiguration.sourcePhoneNumber));

Logger.logMessage(Logger.MessageType.INFORMATION,"Performing CreateCall operation");

List<CommunicationIdentifier> targets = new ArrayList<>() { {add(target);} };

Response<CallConnection> response = this.callingServerClient.createCallConnectionWithResponse(source, targets, createCallOption, null);
callConnection = response.getValue();
```

`createCallConnectionWithResponse` 메서드는 오디오 재생, 오디오 취소 및 중단과 같은 기타 호출 작업에 사용되는 `Response<CallConnection>` 개체를 반환합니다.

## <a name="play-audio"></a>오디오 재생

호출이 생성되면 `CallConnection` 개체의 `playAudioWithResponse` 메서드를 사용하여 수신자에게 오디오 메시지를 재생할 수 있습니다. `playAudioWithResponse` 메서드가 지원하는 매개 변수는 다음과 같습니다.

- `audioFileUri`는 재생할 메시지가 있는 오디오 파일의 URI입니다.
- `playAudioOptions`는 오디오 재생 옵션입니다. 유형은 `PlayAudioOptions`입니다. `PlayAudioOptions` 개체에 지원되는 매개 변수는 다음과 같습니다.
    - `loop` - 오디오 메시지를 반복하려면 true로 설정합니다.
    - `audioFileId` - 미디어를 캐시하는 데 사용하는 AudioFileUri의 미디어 ID입니다.
    - `operationContext`는 요청 컨텍스트의 고유 ID입니다.

```java
// Preparing data for request
String audioFileUri = callConfiguration.audioFileUrl;
Boolean loop = true;
String operationContext = UUID.randomUUID().toString();
String audioFileId = UUID.randomUUID().toString();
PlayAudioOptions playAudioOptions = new PlayAudioOptions();
playAudioOptions.setLoop(loop);
playAudioOptions.setAudioFileId(audioFileId);
playAudioOptions.setOperationContext(operationContext);

Logger.logMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
Response<PlayAudioResult> playAudioResponse = this.callConnection.playAudioWithResponse(audioFileUri, playAudioOptions, null);

PlayAudioResult response = playAudioResponse.getValue();
```

이 `playAudio` 메서드는 `getStatus()` 메서드를 사용하여 `OperationStatus`를 가져오는 데 사용할 수 있는 `Response<PlayAudioResult>`를 반환합니다. `OperationStatus`는 `NotStarted`, `Running`, `Completed`, 또는 `Failed` 같은 값을 가질 수 있습니다.

```java
Logger.logMessage(Logger.MessageType.INFORMATION, "playAudioWithResponse -- > " + GetResponse(playAudioResponse) +
", Id: " + response.getOperationId() + ", OperationContext: " + response.getOperationContext() + ", OperationStatus: " +
response.getStatus().toString());
```

## <a name="add-a-participant-to-the-call"></a>호출에 참가자 추가

`CallConnection` 개체의 `addParticipantWithResponse` 메서드를 사용하여 호출에 참가자를 추가합니다. `addParticipantWithResponse` 메서드가 지원하는 매개 변수는 다음과 같습니다.

- `participant`는 `CommunicationUserIdentifier` 또는 `PhoneNumberIdentifier` 형식의 참가자 식별자입니다.
- `alternateCallerId`는 원본 호출자 ID입니다.
- `operationContext`는 요청 컨텍스트의 고유 ID입니다.

```java
// Preparing data for request
CommunicationIdentifier participant = null;
String operationContext = UUID.randomUUID().toString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity) {
    participant = new CommunicationUserIdentifier(addedParticipant);

} else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity) {
    participant = new PhoneNumberIdentifier(addedParticipant);
}

Response<AddParticipantResult> response = callConnection.addParticipantWithResponse(participant, this.callConfiguration.sourcePhoneNumber, operationContext, null);
```

## <a name="cancel-media-processing"></a>미디어 처리 취소

`CallConnection` 개체의 `cancelAllMediaOperationsWithResponse` 메서드를 사용하여 오디오 재생 작업을 취소합니다. `cancelAllMediaOperationsWithResponse` 메서드가 지원하는 매개 변수는 다음과 같습니다.

- `operationContext`는 요청 컨텍스트의 고유 ID입니다.

```java
String operationContext = UUID.randomUUID().toString();
Response<CancelAllMediaOperationsResult> cancelmediaresponse = this.callConnection.cancelAllMediaOperationsWithResponse(operationContext, null);
```

## <a name="hang-up-the-call"></a>호출 중단

`CallConnection` 개체의 `hangupWithResponse` 메서드를 사용하여 호출을 중단합니다.

```java
Response<Void> response = this.callConnection.hangupWithResponse(null);
```