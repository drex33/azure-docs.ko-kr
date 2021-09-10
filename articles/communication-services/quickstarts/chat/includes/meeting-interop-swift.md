---
title: 빠른 시작 - Teams 모임 참가
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 06d7b776b49411fece14356f24a91f1a5df2b364
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722556"
---
이 빠른 시작에서는 iOS용 Azure Communication Services 채팅 SDK를 사용하여 Teams 모임에서 채팅하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항 

-  [Teams 배포](/deployoffice/teams-install). 
- 작동하는 [통화 앱](../../voice-video-calling/get-started-teams-interop.md) 

## <a name="joining-the-meeting-chat"></a>모임 채팅 참가 

Communication Services 사용자는 호출 SDK를 사용하여 익명 사용자로 Teams 모임에 참가할 수 있습니다. 모임에 참가하면 모임 채팅에도 참가자로 추가됩니다. 그러면 모임의 다른 사용자와 메시지를 보내고 받을 수 있습니다. 사용자는 모임에 참가하기 전에 전송된 채팅 메시지에 액세스할 수 없으며 모임이 끝난 후에는 메시지를 보내거나 받을 수 없습니다. 모임에 참가하고 채팅을 시작하려면 다음 단계를 수행하면 됩니다.

## <a name="add-chat-to-the-teams-calling-app"></a>Teams 통화 앱에 채팅 추가

`Podfile.lock`을 삭제하고 Podfile의 내용을 다음으로 바꿉니다.

```
platform :ios, '13.0'
use_frameworks!

target 'AzureCommunicationCallingSample' do
  pod 'AzureCommunicationCalling', '~> 1.0.0-beta.12'
  pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
end
```

## <a name="install-the-chat-packages"></a>채팅 패키지 설치

`pod install`을 실행하여 AzureCommunicationChat 패키지를 설치합니다.
설치한 후 `.xcworkspace` 파일을 엽니다.

## <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

다음 조각을 추가하여 `ContentView.swift`에서 AzureCommunicationChat 패키지를 가져옵니다.

```
import AzureCommunicationChat
```

## <a name="add-the-teams-ui-controls"></a>Teams UI 컨트롤 추가

`ContentView.swift`에서 기존 상태 변수 아래에 다음 코드 조각을 추가합니다. 

```
    // Chat
    @State var chatClient: ChatClient?
    @State var chatThreadClient: ChatThreadClient?
    @State var chatMessage: String = ""
    @State var meetingMessages: [MeetingMessage] = []

    let displayName: String = "<YOUR_DISPLAY_NAME_HERE>"
```

`<YOUR_DISPLAY_NAME_HERE>`를 채팅에서 사용할 표시 이름으로 바꿉니다.

다음으로 `Section` 양식을 수정하여 채팅 메시지를 표시하고 채팅을 위한 UI 컨트롤을 추가합니다.

다음 조각을 기존 양식에 추가합니다. 레코딩 상태에 대한 텍스트 보기 `Text(recordingStatus)` 바로 뒤입니다.

```
VStack(alignment: .leading) {
    ForEach(meetingMessages, id: \.id) { message in
        let currentUser: Bool = (message.displayName == self.displayName)
        let foregroundColor = currentUser ? Color.white : Color.black
        let background = currentUser ? Color.blue : Color(.systemGray6)
        let alignment = currentUser ? HorizontalAlignment.trailing : HorizontalAlignment.leading
        VStack {
            Text(message.displayName).font(Font.system(size: 10))
            Text(message.content)
        }
        .alignmentGuide(.leading) { d in d[alignment] }
        .padding(10)
        .foregroundColor(foregroundColor)
        .background(background)
        .cornerRadius(10)
    }
}.frame(minWidth: 0, maxWidth: .infinity)
TextField("Enter your message...", text: $chatMessage)
Button(action: sendMessage) {
    Text("Send Message")
}.disabled(chatThreadClient == nil)
```

제목을 `Chat Teams Quickstart`로 변경합니다. 다음 줄을 이 제목으로 수정합니다.

```
.navigationBarTitle("Chat Teams Quickstart")
```

## <a name="enable-the-teams-ui-controls"></a>Teams UI 컨트롤 사용

### <a name="initialize-the-chatclient"></a>ChatClient 초기화

`ChatClient`를 인스턴스화하고 실시간 알림을 사용하도록 설정합니다. 실시간 알림은 채팅 메시지를 수신하는 데 사용합니다.

`NavigationView` `.onAppear` 내에서 `CallAgent`를 초기화하는 기존 코드 뒤에 아래 코드 조각을 추가합니다.

```
// Initialize the ChatClient
do {
    let endpoint = "COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>"
    let credential = try CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN_HERE>")

    self.chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: AzureCommunicationChatClientOptions()
    )

    self.message = "ChatClient successfully created"

    // Start real-time notifications
    self.chatClient?.startRealTimeNotifications() { result in
        switch result {
        case .success:
            print("Real-time notifications started")
            // Receive chat messages
            self.chatClient?.register(event: ChatEventId.chatMessageReceived, handler: receiveMessage)
        case .failure:
            print("Failed to start real-time notifications")
            self.message = "Failed to enable chat notifications"
        }
    }
} catch {
    print("Unable to create ChatClient")
    self.message = "Please enter a valid endpoint and Chat token in source code"
    return
}
```

`<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>`를 Communication Services 리소스의 엔드포인트로 바꿉니다.
`<USER_ACCESS_TOKEN_HERE>`를 채팅 범위가 있는 액세스 토큰으로 바꿉니다. 

사용자 액세스 토큰에 대해 자세히 알아보기: [사용자 액세스 토큰](../../access-tokens.md)

### <a name="initialize-the-chatthreadclient"></a>ChatThreadClient 초기화

기존 `joinTeamsMeeting()` 함수 내에서 사용자가 모임에 참가한 후 `ChatThreadClient`를 초기화합니다.

`self.callAgent?.join()` 호출에 대한 완료 처리기 내에서 주석 `// Initialize the ChatThreadClient` 아래에 코드를 추가합니다. 전체 코드는 다음과 같습니다.

```
self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) { (call, error) in
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.message = "Teams meeting joined successfully"

        // Initialize the ChatThreadClient
        do {
            guard let threadId = getThreadId(from: self.meetingLink) else {
                self.message = "Failed to join meeting chat"
                return
            }
            self.chatThreadClient = try chatClient?.createClient(forThread: threadId)
            self.message = "Joined meeting chat successfully"
        } catch {
            print("Failed to create ChatThreadClient")
            self.message = "Failed to join meeting chat"
            return
        }
    } else {
        print("Failed to join Teams meeting")
    }
}
```

`ContentView`에 다음 도우미 함수를 추가합니다. 이 함수는 팀의 모임 링크에서 채팅 스레드 ID를 검색하는 데 사용됩니다.

```
func getThreadId(from meetingLink: String) -> String? {
    if let range = self.meetingLink.range(of: "meetup-join/") {
        let thread = self.meetingLink[range.upperBound...]
        if let endRange = thread.range(of: "/")?.lowerBound {
            return String(thread.prefix(upTo: endRange))
        }
    }
    return nil
}
```

### <a name="enable-sending-messages"></a>메시지 보내기 사용

`sendMessage()` 함수를 `ContentView`에 추가합니다. 이 함수는 `ChatThreadClient`를 사용하여 사용자의 메시지를 보냅니다.

```
func sendMessage() {
    let message = SendChatMessageRequest(
        content: self.chatMessage,
        senderDisplayName: self.displayName
    )
    
    self.chatThreadClient?.send(message: message) { result, _ in
        switch result {
        case .success:
            print("Chat message sent")
        case .failure:
            print("Failed to send chat message")
        }

        self.chatMessage = ""
    }
}
```

### <a name="enable-receiving-messages"></a>메시지 받기 사용

메시지를 받기 위해 `ChatMessageReceived` 이벤트에 대한 처리기를 구현합니다. 새 메시지가 스레드로 전송되면 이 처리기는 해당 메시지를 `meetingMessages` 변수에 추가하여 UI에 표시될 수 있도록 합니다.

먼저 다음 구조체를 `ContentView.swift`에 추가합니다. UI는 구조체의 데이터를 사용하여 채팅 메시지를 표시합니다.

```
struct MeetingMessage {
    let id: String
    let content: String
    let displayName: String
}
```

다음으로 `receiveMessage()` 함수를 `ContentView`에 추가합니다. `ChatMessageReceived` 이벤트가 발생할 때마다 호출되는 처리기입니다.

```
func receiveMessage(response: Any, eventId: ChatEventId) {
    let chatEvent: ChatMessageReceivedEvent = response as! ChatMessageReceivedEvent

    let displayName: String = chatEvent.senderDisplayName ?? "Unknown User"
    let content: String = chatEvent.message.replacingOccurrences(of: "<[^>]+>", with: "", options: String.CompareOptions.regularExpression)

    self.meetingMessages.append(
        MeetingMessage(
            id: chatEvent.id,
            content: content,
            displayName: displayName
        )
    )
}
```

### <a name="leave-the-chat"></a>채팅 나가기

사용자가 팀의 모임에서 나가면 UI에서 채팅이 지워집니다. 전체 코드는 다음과 같습니다.

```
func leaveMeeting() {
    if let call = call {
        call.hangUp(options: nil, completionHandler: { (error) in
            if error == nil {
                self.message = "Leaving Teams meeting was successful"
                // Clear the chat
                self.meetingMessages.removeAll()
            } else {
                self.message = "Leaving Teams meeting failed"
            }
        })
    } else {
        self.message = "No active call to hanup"
    }
}
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services 사용자를 위한 Teams 미팅 채팅 스레드 가져오기

Teams 모임 링크 및 채팅은 [그래프 문서](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)에 자세히 설명된 Graph API를 사용하여 검색할 수 있습니다. Communication Services Calling SDK는 전체 Teams 미팅 링크를 수락합니다. 이 링크는 [`joinWebUrl` 속성](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)에서 액세스할 수 있는 `onlineMeeting` 리소스의 일부로 반환됩니다. [Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)를 사용하면 `threadId`를 얻을 수도 있습니다. 응답에는 `threadID`를 포함하는 `chatInfo` 개체가 포함됩니다. 

또한 Teams 모임 초대 자체의 **모임 참가** URL에서 필요한 모임 정보 및 스레드 ID를 가져올 수 있습니다.
Teams 미팅 링크는 `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`와 같습니다. `threadId`는 링크의 `meeting_chat_thread_id` 위치에 있습니다. 사용하기 전에 `meeting_chat_thread_id`가 이스케이프되지 않았는지 확인합니다. `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2` 형식이어야 합니다.


## <a name="run-the-code"></a>코드 실행

애플리케이션을 실행합니다. 

Teams 모임에 참가하려면 Teams의 모임 링크를 UI에 입력합니다.

팀의 모임에 참가한 후에는 팀의 클라이언트에서 사용자의 모임 참가를 수락해야 합니다. 사용자가 수락되고 채팅에 참가하면 메시지를 보내고 받을 수 있습니다.

:::image type="content" source="../join-teams-meeting-chat-quickstart-ios.png" alt-text="완료된 iOS 애플리케이션 스크린샷":::

> [!NOTE] 
> 현재는 Teams와의 상호 운용성 시나리오에 대해서만 메시지를 보내고 받고 편집할 수 있습니다. 입력 표시기 및 Communication Services 사용자와 같은 다른 기능에서는 아직 Teams 미팅에서 다른 사용자를 추가하거나 제거할 수 없습니다.
