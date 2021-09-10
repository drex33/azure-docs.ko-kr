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
ms.openlocfilehash: b5ea5a90a6ad39404208bb9c353bb33e86201299
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967951"
---
## <a name="sample-code"></a>샘플 코드
[GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/chat-quickstart-java)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-install), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- 배포된 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스 만들기](../../create-communication-resource.md)
- [사용자 액세스 토큰](../../access-tokens.md). 범위를 "채팅"으로 설정하고 토큰 문자열과 userId 문자열을 기록해 둡니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 열고 Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 maven-archetype-quickstart 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 목표에서 artifactId와 동일한 이름으로 디렉터리를 만들었습니다. 이 디렉터리 아래의 `src/main/java directory` 디렉터리는 프로젝트 소스 코드를 포함하고, `src/test/java` 디렉터리는 테스트 원본을 포함하고, pom.xml 파일은 프로젝트의 [POM(프로젝트 개체 모델)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)입니다.

Java 8 이상을 사용하도록 애플리케이션의 POM 파일을 업데이트합니다.

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>채팅 SDK에 대한 패키지 참조 추가

POM 파일에서 채팅 API를 사용하여 `azure-communication-chat` 패키지를 참조합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version><!-- Please refer to https://search.maven.org/artifact/com.azure/azure-communication-chat for the latest version --></version>
</dependency>
```

인증을 위해 클라이언트가 `azure-communication-common` 패키지를 참조해야 합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version><!-- Please refer to https://search.maven.org/artifact/com.azure/azure-communication-common for the latest version --></version>
</dependency>
```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Java용 Azure Communication Services 채팅 SDK의 주요 기능 중 일부를 처리합니다.

| Name                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatAsyncClient | 이 클래스는 비동기 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient | 이 클래스는 채팅 스레드 기능에 필요합니다. 사용자는 ChatClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 사용자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |
| ChatThreadAsyncClient | 이 클래스는 비동기 채팅 스레드 기능에 필요합니다. 사용자는 ChatAsyncClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 사용자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |

## <a name="create-a-chat-client"></a>채팅 클라이언트 만들기
채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 액세스 토큰을 사용합니다. 사용자 액세스 토큰을 사용하면 Azure Communication Service에 직접 인증되는 클라이언트 애플리케이션을 빌드할 수 있습니다. 서버에서 이러한 토큰을 생성한 후 클라이언트 디바이스에 다시 전달합니다. 공통 SDK의 CommunicationTokenCredential 클래스를 사용하여 토큰을 채팅 클라이언트에 전달해야 합니다.

[채팅 아키텍처](../../../concepts/chat/concepts.md)에 대한 자세한 정보

Import 문을 추가할 때 com.azure.communication.chat 및 com.azure.communication.chat.models namespaces의 import 문만 추가하고 com.azure.communication.chat.implementation 네임스페이스의 import 문은 추가하지 마세요. Maven을 통해 생성된 App.java 파일에서 다음 코드를 사용하여 다음 작업을 시작할 수 있습니다.

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`createChatThread` 메서드를 사용하여 채팅 스레드를 만듭니다.
`createChatThreadOptions`는 스레드 요청을 설명하는 데 사용됩니다.

- 구문의 `topic` 매개 변수를 사용하여 채팅에 주제를 제공합니다. 주제는 채팅 스레드를 만든 후 `UpdateThread` 함수를 사용하여 업데이트할 수 있습니다.
- `participants`를 사용하여 스레드에 추가할 스레드 참가자를 나열합니다. `ChatParticipant`는 [사용자 액세스 토큰](../../access-tokens.md) 빠른 시작에서 만든 사용자를 가져옵니다.

`CreateChatThreadResult`는 채팅 스레드를 만들 때 반환되는 응답입니다.
여기에는 `ChatThread` 개체를 반환하는 `getChatThread()` 메서드가 포함됩니다. 이는 참여자 추가, 메시지 보내기 등 생성된 스레드에서 작업을 수행하기 위해 `ChatThreadClient`를 가져올 수 있는 스레드 클라이언트를 가져오는 데 사용할 수 있습니다. 또한 `ChatThread` 개체에는 스레드의 고유 ID를 검색하는 `getId()` 메서드도 포함되어 있습니다.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>채팅 스레드 나열

`listChatThreads` 메서드를 사용하여 기존 채팅 스레드 목록을 검색합니다.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

`getChatThreadClient` 메서드는 이미 존재하는 스레드에 대한 스레드 클라이언트를 반환합니다. 생성된 스레드에서 참가자 추가, 메시지 보내기 등의 작업을 수행하는 데 사용할 수 있습니다. `chatThreadId`는 기존 채팅 스레드의 고유 ID입니다.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`sendMessage` 메서드를 사용하여 chatThreadId로 식별되는 방금 만든 스레드에 채팅 메시지를 보냅니다.
`sendChatMessageOptions`는 채팅 메시지 요청을 설명하는 데 사용됩니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다.
- `type`을 사용하여 채팅 메시지 콘텐츠 유형, TEXT 또는 HTML을 지정합니다.
- `senderDisplayName`을 사용하여 보낸 사람의 표시 이름을 지정합니다.
- 선택적으로 `metadata`를 사용하여 메시지와 함께 보내려는 추가 데이터를 포함합니다. 이 필드는 개발자가 채팅 메시지 기능을 확장하고 사용 사례에 대한 사용자 지정 정보를 추가할 수 있는 메커니즘을 제공합니다. 예를 들어 메시지에서 파일 링크를 공유할 때 메타데이터에 'hasAttachment:true'를 추가하여 수신자의 애플리케이션이 이를 구문 분석하고 그에 따라 표시할 수 있습니다.

응답 `sendChatMessageResult`에는 메시지의 고유 ID인 `id`가 포함됩니다.

```Java
Map<String, String> metadata = new HashMap<String, String>();
metadata.put("hasAttachment", "true");
metadata.put("attachmentUrl", "https://contoso.com/files/attachment.docx");

SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Please take a look at the attachment")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name")
    .setMetadata(metadata);

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

지정된 간격으로 채팅 스레드 클라이언트에서 `listMessages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages`는 .editMessage() 및 .deleteMessage()를 사용하여 메시지에 발생한 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다. 삭제된 메시지의 경우 `chatMessage.getDeletedOn()`은 메시지가 삭제된 시간을 나타내는 datetime 값을 반환합니다. 편집된 메시지의 경우 `chatMessage.getEditedOn()`은 메시지가 편집된 시간을 나타내는 datetime을 반환합니다. 원래 메시지 생성 시간은 `chatMessage.getCreatedOn()`을 사용하여 액세스할 수 있으며 메시지를 정렬하는 데 사용할 수 있습니다.

[메시지 유형](../../../concepts/chat/concepts.md#message-types)에서 메시지 유형에 대해 더 알아보세요.

## <a name="send-read-receipt"></a>읽음 확인 보내기

`sendReadReceipt` 메서드를 사용하여 사용자 대신 채팅 스레드에서 읽음 확인 이벤트를 게시합니다.
`chatMessageId`는 읽은 채팅 메시지의 고유 ID입니다.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>채팅 참가자 나열

`listParticipants`를 사용하여 chatThreadId로 식별된 채팅 스레드의 참여자가 포함된 페이징된 컬렉션을 검색합니다.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

채팅 스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 채팅 스레드에 메시지를 보내고 다른 참가자를 추가/제거할 수 있는 액세스 권한이 사용자에게 부여됩니다. 해당 사용자의 새 액세스 토큰과 ID를 가져오는 것부터 시작해야 합니다. addParticipants 메서드를 호출하기 전에 해당 사용자의 새 액세스 토큰과 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`addParticipants` 메서드를 사용하여 참가자를 스레드에 추가합니다.

- 필수 사항인 `communicationIdentifier`는 [사용자 액세스 토큰](../../access-tokens.md) 빠른 시작에서 CommunicationIdentityClient를 통해 만든 CommunicationIdentifier입니다.
- 선택 사항인 `displayName`은 스레드 참가자의 표시 이름입니다.
- 선택 사항인 `shareHistoryTime`은 채팅 기록이 참가자와 공유된 시간입니다. 채팅 스레드가 시작된 이후의 기록을 공유하려면 이 속성을 스레드 생성 날짜와 동일한 날짜 또는 그 이전의 날짜로 설정합니다. 참가자가 추가되기 전의 기록을 공유하지 않으려면 현재 날짜로 설정합니다. 일부 기록을 공유하려면 필요한 날짜로 설정합니다.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>코드 실행

*pom.xml* 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

```console
mvn compile
```

그런 다음, 패키지를 빌드합니다.

```console
mvn package
```

다음 `mvn` 명령을 실행하여 앱을 실행합니다.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```