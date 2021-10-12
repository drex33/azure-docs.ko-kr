---
title: 빠른 시작 - Teams 모임 참가
author: agiurg
ms.author: agiurg
ms.date: 07/20/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: bb5ccb022b4bfeb90f7c50f27ce2a76d43386571
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378119"
---
이 빠른 시작에서는 Android용 Azure Communication Services 채팅 SDK를 사용하여 Teams 모임에서 채팅하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항 

-  [Teams 배포](/deployoffice/teams-install). 
- 작동하는 [통화 앱](../../voice-video-calling/get-started-teams-interop.md) 

## <a name="enable-teams-interoperability"></a>Teams 상호 운용성 사용 

게스트 사용자로 Teams 미팅에 조인하는 Communication Services 사용자는 Teams 미팅 호출에 조인한 경우에만 미팅 채팅에 액세스할 수 있습니다. Teams 미팅 호출에 Communication Services 사용자를 추가하는 방법을 알아보려면 [Teams interop](../../voice-video-calling/get-started-teams-interop.md) 설명서를 참조하세요.

이 기능을 사용하려면 두 엔터티를 소유하는 조직의 구성원이어야 합니다.

## <a name="joining-the-meeting-chat"></a>모임 채팅 참가 

Teams 상호 운용성을 사용하도록 설정하면 Communication Services 사용자가 Calling SDK를 사용하여 외부 사용자로 Teams 통화에 조인할 수 있습니다. 통화에 조인하면 채팅 통화에 참가자로 추가됩니다. 그러면 통화 중인 다른 사용자와 메시지를 주고받을 수 있습니다. 사용자는 통화에 조인하기 전에 전송된 채팅 메시지에 액세스할 수 없습니다. 모임에 참가하고 채팅을 시작하려면 다음 단계를 수행하면 됩니다.

## <a name="add-chat-to-the-teams-calling-app"></a>Teams 통화 앱에 채팅 추가

모듈 수준 build.gradle에서 채팅 SDK에 대한 종속성을 추가합니다.

> [!IMPORTANT]
> 알려진 문제: Android 채팅 및 통화 SDK를 동일한 애플리케이션에서 함께 사용하는 경우 채팅 SDK의 실시간 알림 기능이 작동하지 않습니다. 종속성 해결 문제가 발생합니다. 솔루션에 대해 작업하는 동안 앱의 `build.gradle` 파일에 있는 채팅 SDK 종속성에 다음 제외 항목을 추가하여 실시간 알림 기능을 끌 수 있습니다.
> 
> ```groovy
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> ```


## <a name="add-the-teams-ui-layout"></a>Teams UI 레이아웃 추가

activity_main.xml의 코드를 다음 코드 조각으로 바꿉니다. 스레드 ID 및 메시지 전송을 위한 입력, 입력된 메시지 전송 버튼 및 기본 채팅 레이아웃을 추가합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_thread_id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="128dp"
        android:ems="10"
        android:hint="Meeting Thread Id"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="64dp"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:id="@+id/button_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:gravity="center"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/teams_meeting_thread_id">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <ScrollView
        android:id="@+id/chat_box"
        android:layout_width="374dp"
        android:layout_height="294dp"
        android:layout_marginTop="40dp"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toTopOf="@+id/send_message_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button_layout"
        android:orientation="vertical"
        android:gravity="bottom"
        android:layout_gravity="bottom"
        android:fillViewport="true">

        <LinearLayout
            android:id="@+id/chat_box_layout"
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:gravity="bottom"
            android:layout_gravity="top"
            android:layout_alignParentBottom="true"/>
    </ScrollView>

    <EditText
        android:id="@+id/message_body"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="588dp"
        android:ems="10"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="Type your message here..."
        tools:visibility="invisible" />

    <Button
        android:id="@+id/send_message_button"
        android:layout_width="138dp"
        android:layout_height="45dp"
        android:layout_marginStart="133dp"
        android:layout_marginTop="48dp"
        android:layout_marginEnd="133dp"
        android:text="Send Message"
        android:visibility="invisible"
        app:layout_constraintBottom_toTopOf="@+id/recording_status_bar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.428"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/chat_box" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>Teams UI 컨트롤 사용

### <a name="import-packages-and-define-state-variables"></a>패키지 가져오기 및 상태 변수 정의

`MainActivity.java`의 콘텐츠에 다음 추가 가져오기를 추가합니다.

```
import android.graphics.Typeface;
import android.graphics.Color;
import android.text.Html;
import android.os.Handler;
import android.view.Gravity;
import android.view.View;
import android.widget.LinearLayout;
import java.util.Collections;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import com.azure.android.communication.chat.ChatThreadAsyncClient;
import com.azure.android.communication.chat.ChatThreadClientBuilder;
import com.azure.android.communication.chat.models.ChatMessage;
import com.azure.android.communication.chat.models.ChatMessageType;
import com.azure.android.communication.chat.models.ChatParticipant;
import com.azure.android.communication.chat.models.ListChatMessagesOptions;
import com.azure.android.communication.chat.models.SendChatMessageOptions;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.AsyncStreamHandler;
```

`MainActivity` 클래스에 다음 변수를 추가합니다.

```
    // InitiatorId is used to differentiate incoming messages from outgoing messages
    private static final String InitiatorId = "<USER_ID>";
    private static final String ResourceUrl = "<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>";
    private String threadId;
    private ChatThreadAsyncClient chatThreadAsyncClient;
    
    // The list of ids corresponsding to messages which have already been processed
    ArrayList<String> chatMessages = new ArrayList<>();
```

`<USER_ID>`를 채팅을 시작한 사용자의 ID로 바꿉니다.
`<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>`를 Communication Services 리소스의 엔드포인트로 바꿉니다. 

### <a name="initialize-the-chatthreadclient"></a>ChatThreadClient 초기화

회의에 참가한 후 `ChatThreadClient`를 인스턴스화하고 채팅 구성 요소를 표시합니다.

아래 코드로 `MainActivity.joinTeamsMeeting()` 메서드의 끝을 업데이트합니다.

```
    private void joinTeamsMeeting() {
        ...
        EditText threadIdView = findViewById(R.id.teams_meeting_thread_id);
        threadId = threadIdView.getText().toString();
        // Initialize Chat Thread Client
        chatThreadAsyncClient = new ChatThreadClientBuilder()
                .endpoint(ResourceUrl)
                .credential(new CommunicationTokenCredential(UserToken))
                .chatThreadId(threadId)
                .buildAsyncClient();
        Button sendMessageButton = findViewById(R.id.send_message_button);
        EditText messageBody = findViewById(R.id.message_body);
        // Register the method for sending messages and toggle the visibility of chat components
        sendMessageButton.setOnClickListener(l -> sendMessage());
        sendMessageButton.setVisibility(View.VISIBLE);
        messageBody.setVisibility(View.VISIBLE);
        
        // Start the polling for chat messages immediately
        handler.post(runnable);
    }
```

### <a name="enable-sending-messages"></a>메시지 보내기 사용

`MainActivity`에 `sendMessage()` 메서드를 추가합니다. `ChatThreadClient`를 사용하여 사용자를 대신하여 메시지를 보냅니다.

```
    private void sendMessage() {
        // Retrieve the typed message content
        EditText messageBody = findViewById(R.id.message_body);
        // Set request options and send message
        SendChatMessageOptions options = new SendChatMessageOptions();
        options.setContent(messageBody.getText().toString());
        options.setSenderDisplayName("ACS User");
        chatThreadAsyncClient.sendMessage(options);
        // Clear the text box
        messageBody.setText("");
    }
```

### <a name="enable-polling-for-messages-and-rendering-them-in-the-application"></a>메시지에 대한 폴링을 사용하도록 설정하고 애플리케이션에서 메시지를 렌더링합니다.

> [!IMPORTANT]
> 알려진 문제: 채팅 SDK의 실시간 알림 기능은 통화 SDK와 함께 작동하지 않으므로 사전 정의된 간격으로 `GetMessages` API를 폴링해야 합니다. 이 샘플에서는 3초 간격을 사용합니다.

`GetMessages` API가 반환한 메시지 목록에서 다음 데이터를 얻을 수 있습니다. 
 - 조인 이후 스레드의 `text` 및 `html` 메시지
 - 스레드 명단에 대한 변경 사항
 - 스레드 토픽에 대한 업데이트


`MainActivity` 클래스에 처리기와 3초 간격으로 실행될 실행 가능한 작업을 추가합니다.

```
    private Handler handler = new Handler();
    private Runnable runnable = new Runnable() {
        @Override
        public void run() {
            try {
                retrieveMessages();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // Repeat every 3 seconds
            handler.postDelayed(runnable, 3000);
        }
    };
```

작업은 초기화 단계에서 업데이트된 `MainActivity.joinTeamsMeeting()` 메서드의 끝에서 이미 시작되었습니다.

마지막으로 스레드에서 액세스 가능한 모든 메시지를 쿼리하고, 메시지 유형별로 구문 분석하고, `html` 및 `text` 메시지를 표시하는 메서드를 추가합니다.

```
    private void retrieveMessages() throws InterruptedException {
        // Initialize the list of messages not yet processed
        ArrayList<ChatMessage> newChatMessages = new ArrayList<>();
        
        // Retrieve all messages accessible to the user
        PagedAsyncStream<ChatMessage> messagePagedAsyncStream
                = this.chatThreadAsyncClient.listMessages(new ListChatMessagesOptions(), null);
        // Set up a lock to wait until all returned messages have been inspected
        CountDownLatch latch = new CountDownLatch(1);
        // Traverse the returned messages
        messagePagedAsyncStream.forEach(new AsyncStreamHandler<ChatMessage>() {
            @Override
            public void onNext(ChatMessage message) {
                // Messages that should be displayed in the chat
                if ((message.getType().equals(ChatMessageType.TEXT)
                    || message.getType().equals(ChatMessageType.HTML))
                    && !chatMessages.contains(message.getId())) {
                    newChatMessages.add(message);
                    chatMessages.add(message.getId());
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_ADDED)) {
                    // Handle participants added to chat operation
                    List<ChatParticipant> participantsAdded = message.getContent().getParticipants();
                    CommunicationIdentifier participantsAddedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_REMOVED)) {
                    // Handle participants removed from chat operation
                    List<ChatParticipant> participantsRemoved = message.getContent().getParticipants();
                    CommunicationIdentifier participantsRemovedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.TOPIC_UPDATED)) {
                    // Handle topic updated
                    String newTopic = message.getContent().getTopic();
                    CommunicationIdentifier topicUpdatedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
            }
            @Override
            public void onError(Throwable throwable) {
                latch.countDown();
            }
            @Override
            public void onComplete() {
                latch.countDown();
            }
        });
        // Wait until the operation completes
        latch.await(1, TimeUnit.MINUTES);
        // Returned messages should be ordered by the createdOn field to be guaranteed a proper chronological order
        // For the purpose of this demo we will just reverse the list of returned messages
        Collections.reverse(newChatMessages);
        for (ChatMessage chatMessage : newChatMessages)
        {
            LinearLayout chatBoxLayout = findViewById(R.id.chat_box_layout);
            // For the purpose of this demo UI, we don't need to use HTML formatting for displaying messages
            // The Teams client always sends html messages in meeting chats 
            String message = Html.fromHtml(chatMessage.getContent().getMessage(), Html.FROM_HTML_MODE_LEGACY).toString().trim();
            TextView messageView = new TextView(this);
            messageView.setText(message);
            // Compare with sender identifier and align LEFT/RIGHT accordingly
            // ACS users are of type CommunicationUserIdentifier
            CommunicationIdentifier senderId = chatMessage.getSenderCommunicationIdentifier();
            if (senderId instanceof CommunicationUserIdentifier
                && InitiatorId.equals(((CommunicationUserIdentifier) senderId).getId())) {
                messageView.setTextColor(Color.GREEN);
                messageView.setGravity(Gravity.RIGHT);
            } else {
                messageView.setTextColor(Color.BLUE);
                messageView.setGravity(Gravity.LEFT);
            }
            // Note: messages with the deletedOn property set to a timestamp, should be marked as deleted
            // Note: messages with the editedOn property set to a timestamp, should be marked as edited
            messageView.setTypeface(Typeface.SANS_SERIF, Typeface.BOLD);
            chatBoxLayout.addView(messageView);
        }
    }
```

채팅 스레드 참가자의 표시 이름은 Teams 클라이언트에서 설정하지 않습니다. 이름이 참가자 나열을 위한 API, `participantsAdded` 이벤트 및 `participantsRemoved` 이벤트에서 null로 반환됩니다. 채팅 참가자의 표시 이름은 `call` 개체의 `remoteParticipants` 필드에서 검색할 수 있습니다.

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services 사용자를 위한 Teams 미팅 채팅 스레드 가져오기

Teams 모임 링크 및 채팅은 [그래프 설명서](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)에 자세히 설명된 Graph API를 사용하여 검색할 수 있습니다. Communication Services Calling SDK는 전체 Teams 모임 링크를 수락합니다. 이 링크는 [`joinWebUrl` 속성](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)에서 액세스할 수 있는 `onlineMeeting` 리소스의 일부로 반환됩니다.

[Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)를 사용하면 `threadID`도 가져올 수 있습니다. 응답에는 `threadID`를 포함하는 `chatInfo` 개체가 포함됩니다.

또한 Teams 모임 초대 자체의 **모임 참가** URL에서 필요한 모임 정보 및 스레드 ID를 가져올 수 있습니다.
Teams 미팅 링크는 `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`와 같습니다. `threadId`는 링크의 `meeting_chat_thread_id` 위치에 있습니다. 사용하기 전에 `meeting_chat_thread_id`가 이스케이프되지 않았는지 확인합니다. `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2` 형식이어야 합니다.


## <a name="run-the-code"></a>코드 실행

이제 도구 모음의 "앱 실행" 단추(Shift+F10)를 사용하여 앱을 시작할 수 있습니다.

Teams 모임에 참여하고 채팅하려면 UI에 팀의 모임 링크와 스레드 ID를 입력합니다.

팀의 모임에 참가한 후에는 팀의 클라이언트에서 사용자의 모임 참가를 수락해야 합니다. 사용자가 수락되고 채팅에 참가하면 메시지를 보내고 받을 수 있습니다.

:::image type="content" source="../join-teams-meeting-chat-quickstart-android.png" alt-text="완성된 Android 애플리케이션의 스크린샷.":::

> [!NOTE] 
> 현재는 Teams와의 상호 운용성 시나리오에 대해서만 메시지를 보내고 받고 편집할 수 있습니다. 입력 표시기 및 Communication Services 사용자와 같은 다른 기능에서는 아직 Teams 미팅에서 다른 사용자를 추가하거나 제거할 수 없습니다.
