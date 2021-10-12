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
ms.openlocfilehash: d61120d3f4e1256b9da50b8128b23fd73b36a5ef
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129406841"
---
## <a name="sample-code"></a>샘플 코드
[GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/add-chat)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요. 
- [Xcode](https://developer.apple.com/xcode/) 및 [CocoaPods](https://cocoapods.org/)를 설치합니다. Xcode를 사용하여 빠른 시작에 사용할 iOS 애플리케이션을 만들고, CocoaPods를 사용하여 종속성을 설치합니다.
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [빠른 시작: Communication Services 리소스 만들기 및 관리](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에서는 리소스 엔드포인트를 기록해야 합니다.
- 2명의 Azure Communication Services 사용자를 만들고 [사용자 액세스 토큰](../../access-tokens.md)을 발급합니다. 범위를 `chat`으로 설정하고 `token` 문자열과 `userId` 문자열을 기록해 둡니다. 이 빠른 시작에서는 초기 참가자가 있는 스레드를 만든 다음, 두 번째 참가자를 스레드에 추가합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-ios-application"></a>새 iOS 애플리케이션 만들기

Xcode를 열고 **Create a new Xcode project** 를 선택합니다. 그런 다음, 플랫폼에 **iOS** 를, 템플릿에 **앱** 을 선택합니다.

프로젝트 이름으로 **ChatQuickstart** 를 입력합니다. 그런 다음, 인터페이스로 **Storyboard** 를, 수명 주기로 **UIKit App Delegate** 를, 언어로 **Swift** 를 선택합니다.

**다음** 을 클릭하고 프로젝트를 만들려는 디렉터리를 선택합니다.

### <a name="install-the-libraries"></a>라이브러리 설치

CocoaPods를 사용하여 필요한 Communication Services 종속성을 설치합니다.

명령줄에서 `ChatQuickstart` iOS 프로젝트의 루트 디렉터리 안으로 이동합니다. `pod init` 명령을 사용하여 Podfile을 만듭니다.

Podfile를 열고 `ChatQuickstart` 대상에 다음 종속성을 추가합니다.

```
pod 'AzureCommunicationCommon', '~> 1.0.2'
pod 'AzureCommunicationChat', '~> 1.1.0-beta.2'
```

`pod install` 명령을 사용하여 종속성을 설치합니다. 이는 Xcode 작업 영역도 만듭니다.

`pod install`을 설치한 후 새로 만든 `.xcworkspace`를 선택하여 Xcode에서 프로젝트를 다시 엽니다.

### <a name="set-up-the-placeholders"></a>자리 표시자 설정

Xcode에서 작업 영역 `ChatQuickstart.xcworkspace`를 연 후 `ViewController.swift`를 엽니다.

이 빠른 시작에서는 `viewController`에 코드를 추가하고 Xcode 콘솔에서 출력을 확인합니다. iOS에서 사용자 인터페이스를 구축하는 방법은 이 빠른 시작에서 다루지 않습니다. 

`viewController.swift` 상단에서 `AzureCommunication` 및 `AzureCommunicatonChat` 라이브러리를 가져옵니다.

```
import AzureCommunicationCommon
import AzureCommunicationChat
```

`ViewController`의 `viewDidLoad()` 메서드에 다음 코드를 복사합니다.

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

데모를 위해 세마포를 사용하여 코드를 동기화합니다. 다음 단계에서는 Azure Communication Services 채팅 라이브러리를 사용하여 자리 표시자를 샘플 코드로 바꿉니다.


### <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 액세스 토큰을 사용합니다.

[사용자 액세스 토큰](../../access-tokens.md)에 대해 자세히 알아보세요.

이 빠른 시작에서는 권장되는 사항이기는 하지만 채팅 애플리케이션에 대한 토큰을 관리하는 서비스 계층을 만드는 방법을 다루지 않습니다. [채팅 아키텍처](../../../concepts/chat/concepts.md)에 대한 자세한 정보

`<CREATE A CHAT CLIENT>` 주석을 다음 코드 조각으로 바꿉니다.

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

`<ACS_RESOURCE_ENDPOINT>`를 실제 Azure Communication Services 리소스의 엔드포인트로 바꿉니다. `<ACCESS_TOKEN>`을 올바른 Communication Services 액세스 토큰으로 바꿉니다.

## <a name="object-model"></a>개체 모델 

다음 클래스 및 인터페이스는 iOS용 Azure Communication Services 채팅 SDK의 주요 기능 중 일부를 처리합니다.

| Name                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하고, 채팅 이벤트를 구독합니다. |
| `ChatThreadClient` | 이 클래스는 채팅 대화목록 기능에 필요합니다. `ChatClient`를 통해 인스턴스를 가져오고 이를 사용하여 메시지를 전송, 수신, 업데이트 및 삭제할 수 있습니다. 또한 이를 통해 사용자를 추가, 제거 및 가져오고, 입력 알림과 읽음 확인을 보낼 수 있습니다. |

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`CreateChatThreadResult`는 채팅 스레드를 만들 때 반환되는 응답입니다.
`ChatThreadProperties` 개체인 `chatThread` 속성을 포함합니다. 이 개체에는 생성된 스레드에서 참가자 추가, 메시지 보내기 등 작업을 수행하기 위해 `ChatThreadClient`를 가져오는 데 사용할 수 있는 threadId가 포함됩니다.

`<CREATE A CHAT THREAD>` 주석을 다음 코드 조각으로 바꿉니다.

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

`<USER_ID>`를 올바른 Communication Services 사용자 ID로 바꿉니다.

계속하기 전에 여기서 세마포를 사용하여 완료 처리기를 기다립니다. 이후 단계에서는 완료 처리기에 반환된 응답의 `threadId`를 사용합니다.

## <a name="list-all-chat-threads"></a>모든 채팅 스레드 나열

채팅 스레드를 만든 후에는 `ChatClient`에서 `listChatThreads` 메서드를 호출하여 모든 채팅 대화목록을 나열할 수 있습니다. `<LIST ALL CHAT THREADS>` 주석을 다음 코드로 바꿉니다.

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(threads):
        guard let chatThreadItems = threads.pageItems else {
            print("No threads returned.")
            return
        }

        for chatThreadItem in chatThreadItems {
            print("Thread id: \(chatThreadItem.id)")
        }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

`createClient` 메서드는 이미 존재하는 스레드의 `ChatThreadClient`를 반환합니다. 생성된 스레드에서 참가자 추가, 메시지 보내기 등의 작업을 수행하는 데 사용할 수 있습니다. threadId는 기존 채팅 스레드의 고유 ID입니다.

`<GET A CHAT THREAD CLIENT>` 주석을 다음 코드로 바꿉니다.

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`send` 메서드를 사용하여 threadId로 식별되는 스레드에 메시지를 보냅니다.

`SendChatMessageRequest`는 메시지 요청을 설명하는 데 사용됩니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다.
- `senderDisplayName`을 사용하여 보낸 사람의 표시 이름을 지정합니다.
- `type`을 사용하여 메시지 유형(예: ‘text’ 또는 ‘html’)을 지정합니다.
- 선택적으로 `metadata`를 사용하여 메시지와 함께 보내려는 추가 데이터를 포함합니다. 이 필드는 개발자가 채팅 메시지 기능을 확장하고 사용 사례에 대한 사용자 지정 정보를 추가할 수 있는 메커니즘을 제공합니다. 예를 들어 메시지에서 파일 링크를 공유할 때 메타데이터에 'hasAttachment:true'를 추가하여 수신자의 애플리케이션이 이를 구문 분석하고 그에 따라 표시할 수 있습니다.

`SendChatMessageResult`는 메시지 전송 후 반환된 응답이며, 메시지의 고유 ID인 ID를 포함합니다.

`<SEND A MESSAGE>` 주석을 다음 코드 조각으로 바꿉니다.

```
let message = SendChatMessageRequest(
                        content: "Hello!",
                        senderDisplayName: "Jack",
                        type: .text,
                        metadata: [
                            "hasAttachment": "true",
                            "attachmentUrl": "https://contoso.com/files/attachment.docx"
                        ]
                    )

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="send-a-read-receipt"></a>읽음 확인 보내기

`sendReadReceipt` 메서드를 사용하여 사용자 대신 채팅 스레드에서 읽음 확인 이벤트를 게시합니다.
`messageId`는 읽은 채팅 메시지의 고유 ID입니다.

`<SEND A READ RECEIPT>` 주석을 다음 코드로 바꿉니다.

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

실시간 신호를 통해, 새로 들어오는 메시지를 수신 대기하도록 구독하고 메모리의 현재 메시지를 적절하게 업데이트할 수 있습니다. Azure Communication Services는 [구독할 수 있는 이벤트 목록](../../../concepts/chat/concepts.md#real-time-notifications)을 지원합니다.

`<RECEIVE MESSAGES>` 주석을 다음 코드로 바꿉니다. 알림을 사용하도록 설정한 후 새 메시지를 보내 ChatMessageReceivedEvents를 확인해 봅니다.

```
chatClient.startRealTimeNotifications { result in
    switch result {
    case .success:
        print("Real-time notifications started.")
    case .failure:
        print("Failed to start real-time notifications.")
    }
    semaphore.signal()
}
semaphore.wait()

chatClient.register(event: .chatMessageReceived, handler: { response in
    switch response {
    case let .chatMessageReceivedEvent(event):
        print("Received a message: \(event.message)")
    default:
        return
    }
})
```

또는 지정된 간격으로 `listMessages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다. `listMessages`에 관해서는 다음 코드 조각을 참조하세요.

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messagesResult):
        guard let messages = messagesResult.pageItems else {
            print("No messages returned.")
            return
        }

        for message in messages {
            print("Received message with id: \(message.id)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 스레드에 메시지를 보내고 다른 참가자를 추가/제거할 수 있는 액세스 권한이 해당 사용자에게 부여됩니다. `add`를 호출하기 전에 해당 사용자에 대한 새 액세스 토큰 및 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`ChatThreadClient`의 `add` 메서드를 사용하여 채팅 스레드에 참가자를 하나 이상 추가합니다. 다음은 각 스레드 참가자에 대해 지원되는 특성입니다.
- 필수 사항인 `id`는 스레드 참가자의 ID입니다.
- 선택 사항인 `displayName`은 스레드 참가자의 표시 이름입니다.
- 선택 사항인 `shareHistoryTime`은 채팅 기록이 참가자와 공유된 시간입니다.

`<ADD A USER>` 주석을 다음 코드로 바꿉니다.

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

`<USER_ID>`를 추가할 사용자의 Communication Services 사용자 ID로 바꿉니다.

## <a name="list-users-in-a-thread"></a>스레드에 사용자 나열

`listParticipants` 메서드를 사용하여 특정 채팅 스레드의 모든 참가자를 가져옵니다.

`<LIST USERS>` 주석을 다음 코드로 바꿉니다.

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participantsResult):
        guard let participants = participantsResult.pageItems else {
            print("No participants returned.")
            return
        }

        for participant in participants {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>코드 실행

Xcode에서 실행 단추를 눌러 프로젝트를 빌드하고 실행합니다. 콘솔에서 코드의 출력과 ChatClient의 로거 출력을 볼 수 있습니다.

**참고:** `Build Settings > Build Options > Enable Bitcode`를 `No`로 설정합니다. 현재 iOS용 AzureCommunicationChat SDK는 비트코드 사용을 지원하지 않습니다. 다음 [GitHub 이슈](https://github.com/Azure/azure-sdk-for-ios/issues/787)는 이를 추적합니다.
