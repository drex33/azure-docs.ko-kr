---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: e1962ebc42c688adfecd18f7d46ce4957147ac7e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967909"
---
## <a name="sample-code"></a>샘플 코드
[GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/add-chat)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.
- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에 대한 **엔드포인트** 리소스를 기록해야 합니다.
- [사용자 액세스 토큰](../../access-tokens.md). 범위를 "채팅"으로 설정하고 토큰 문자열과 userId 문자열을 기록해 둡니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `ChatQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o ChatQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

.NET용 Azure Communication 채팅 SDK 설치

```PowerShell
dotnet add package Azure.Communication.Chat
```

## <a name="object-model"></a>개체 모델

다음 클래스는 C#용 Azure Communication Services 채팅 SDK의 주요 기능 중 일부를 처리합니다.

| Name                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient | 이 클래스는 채팅 스레드 기능에 필요합니다. 사용자는 ChatClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 참가자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |

## <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 액세스 토큰을 사용합니다. ID SDK의 `CommunicationIdentityClient` 클래스를 사용하여 사용자를 만들고 채팅 클라이언트에 전달할 토큰을 발급해야 합니다.

[사용자 액세스 토큰](../../access-tokens.md)에 대해 자세히 알아보세요.

이 빠른 시작에서는 권장되는 사항이기는 하지만 채팅 애플리케이션에 대한 토큰을 관리하는 서비스 계층을 만드는 방법을 다루지 않습니다. [채팅 아키텍처](../../../concepts/chat/concepts.md)에 대한 자세한 정보

**Program.cs** 코드 조각을 복사하여 원본 파일에 붙여넣습니다.
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

chatClient의 `createChatThread` 메서드를 사용하여 채팅 스레드를 만듭니다.
- `topic`을 사용하여 채팅에 주제를 제공합니다. 주제는 `UpdateTopic` 함수를 사용하여 채팅 스레드를 만든 후 업데이트할 수 있습니다.
- `participants` 속성을 사용하여 채팅 스레드에 추가할 `ChatParticipant` 개체 목록을 전달합니다. `ChatParticipant` 개체가 `CommunicationIdentifier` 개체로 초기화되었습니다. `CommunicationIdentifier`는 `CommunicationUserIdentifier`, `MicrosoftTeamsUserIdentifier` 또는 `PhoneNumberIdentifier` 형식일 수 있습니다. 예를 들어 `CommunicationIdentifier` 개체를 가져오려면 [사용자 생성](../../access-tokens.md#create-an-identity) 지침에 따라 만든 액세스 ID를 전달해야 합니다.

`createChatThread` 메서드의 응답 개체에는 `chatThread` 세부 정보가 포함됩니다. 참가자 추가, 메시지 보내기, 메시지 삭제 등과 같은 채팅 스레드 작업과 상호 작용하려면 `chatThreadClient` 클라이언트 인스턴스가 `ChatClient` 클라이언트에서 `GetChatThreadClient` 메서드를 사용하여 인스턴스화되어야 합니다.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기
`GetChatThreadClient` 메서드는 이미 존재하는 스레드에 대한 스레드 클라이언트를 반환합니다. 생성된 스레드에서 작업(예: 멤버 추가, 메시지 보내기 등)을 수행하는 데 사용할 수 있습니다. threadId는 기존 채팅 스레드의 고유 ID입니다.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>모든 채팅 스레드 나열
`GetChatThreads`를 사용하여 사용자가 속한 모든 채팅 스레드를 검색합니다.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`SendMessage`를 사용하여 스레드에 메시지를 보냅니다.

- `content`를 사용하여 메시지 콘텐츠를 제공합니다. 이는 필수입니다.
- 'Text' 또는 'Html'과 같은 메시지의 콘텐츠 형식에 `type`을 사용합니다. 지정되지 않는 경우 ‘Text’가 설정됩니다.
- `senderDisplayName`을 사용하여 보낸 사람의 표시 이름을 지정합니다. 지정되지 않는 경우 빈 문자열이 설정됩니다.
- 선택적으로 `metadata`를 사용하여 메시지와 함께 보내려는 추가 데이터를 포함합니다. 이 필드는 개발자가 채팅 메시지 기능을 확장하고 사용 사례에 대한 사용자 지정 정보를 추가할 수 있는 메커니즘을 제공합니다. 예를 들어 메시지에서 파일 링크를 공유할 때 메타데이터에 'hasAttachment:true'를 추가하여 수신자의 애플리케이션이 이를 구문 분석하고 그에 따라 표시할 수 있습니다.

```csharp
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
{
    Content = "Please take a look at the attachment",
    MessageType = ChatMessageType.Text
};
sendChatMessageOptions.Metadata["hasAttachment"] = "true";
sendChatMessageOptions.Metadata["attachmentUrl"] = "https://contoso.com/files/attachment.docx";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(sendChatMessageOptions);

string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

지정된 간격으로 채팅 스레드 클라이언트에서 `GetMessages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages`는 선택적 `DateTimeOffset` 매개 변수입니다. 해당 오프셋이 지정되면 오프셋 시간 이후에 수신, 업데이트 또는 삭제된 메시지를 받게 됩니다. 오프셋 시간 이전에 받았지만 그 후 편집 또는 제거된 메시지도 반환됩니다.

`GetMessages`는 `UpdateMessage` 및 `DeleteMessage`를 사용하여 메시지에 발생한 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다. 삭제된 메시지의 경우 `chatMessage.DeletedOn`은 메시지가 삭제된 시간을 나타내는 datetime 값을 반환합니다. 편집된 메시지의 경우 `chatMessage.EditedOn`은 메시지가 편집된 시간을 나타내는 datetime을 반환합니다. 원래 메시지 생성 시간은 `chatMessage.CreatedOn`을 사용하여 액세스할 수 있으며 메시지를 정렬하는 데 사용할 수 있습니다.

`GetMessages`는 `chatMessage.Type`으로 식별할 수 있는 다양한 유형의 메시지를 반환합니다. 그 유형은 다음과 같습니다.

- `Text`: 스레드 멤버가 보낸 일반 채팅 메시지

- `Html`: 형식이 지정된 문자 메시지 Communication Services 사용자는 현재 RichText 메시지를 보낼 수 없습니다. 이 메시지 유형은 Teams Interop 시나리오에서 Teams 사용자가 Communication Services 사용자에게 보내는 메시지에서 지원합니다.

- `TopicUpdated`: 주제가 업데이트되었음을 나타내는 시스템 메시지입니다. (읽기 전용)

- `ParticipantAdded`: 한 명 이상의 참가자가 채팅 스레드에 추가되었음을 나타내는 시스템 메시지입니다(읽기 전용).

- `ParticipantRemoved`: 참가자가 채팅 스레드에서 제거되었음을 나타내는 시스템 메시지입니다.

자세한 내용은 [메시지 유형](../../../concepts/chat/concepts.md#message-types)을 참조하세요.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 스레드에 메시지를 보내고 다른 참가자를 추가/제거할 수 있는 액세스 권한이 해당 사용자에게 부여됩니다. `AddParticipants`를 호출하기 전에 해당 사용자에 대한 새 액세스 토큰 및 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`AddParticipants`를 사용하여 채팅 스레드에 참가자를 하나 이상 추가합니다. 다음은 각 스레드 참가자에 대해 지원되는 특성입니다.
- 필수 사항인 `communicationUser`는 스레드 참가자의 ID입니다.
- 선택 사항인 `displayName`은 스레드 참가자의 표시 이름입니다.
- 선택 사항인 `shareHistoryTime`은 채팅 기록이 참가자와 공유된 시간입니다.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>스레드 참가자 가져오기

`GetParticipants`를 사용하여 채팅 스레드의 참가자를 검색합니다.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>읽음 확인 보내기

`SendReadReceipt`를 사용하여 다른 참가자에게 사용자가 메시지를 읽었음을 알립니다.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```
