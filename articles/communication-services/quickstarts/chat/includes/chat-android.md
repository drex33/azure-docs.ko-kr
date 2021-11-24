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
ms.openlocfilehash: 0f025972835ec66aa1487c821152cd48feb0d801
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132949613"
---
## <a name="sample-code"></a>샘플 코드
[GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add-chat)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Android Studio](https://developer.android.com/studio)를 설치합니다. Android Studio를 사용하여 종속성을 설치하기 위한 빠른 시작용 Android 애플리케이션을 만듭니다.
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에서는 **리소스 엔드포인트를 기록** 해야 합니다.
- **2명** 의 Communication Services 사용자를 만들고 사용자 액세스 토큰 [사용자 액세스 토큰](../../access-tokens.md)을 발급합니다. 범위를 **채팅** 으로 설정하고 **토큰 문자열과 userId 문자열을 기록** 해 둡니다. 이 빠른 시작에서는 초기 참가자가 있는 스레드를 만든 다음, 두 번째 참가자를 스레드에 추가합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-android-application"></a>새 Android 애플리케이션 만들기

1. Android Studio를 열고 `Create a new project`를 선택합니다. 
2. 다음 창에서 프로젝트 템플릿으로 `Empty Activity`를 선택합니다.
3. 옵션을 선택할 때 프로젝트 이름에 `ChatQuickstart`를 입력합니다.
4. 다음을 클릭하고 프로젝트를 만들려는 디렉터리를 선택합니다.

### <a name="install-the-libraries"></a>라이브러리 설치

Gradle을 사용하여 필요한 Communication Services 종속성을 설치합니다. 명령줄에서 `ChatQuickstart` 프로젝트의 루트 디렉터리 안으로 이동합니다. 앱의 build.gradle 파일을 열고 `ChatQuickstart` 대상에 다음 종속성을 추가합니다.

```groovy
implementation 'com.azure.android:azure-communication-common:' + $azureCommunicationCommonVersion
implementation 'com.azure.android:azure-communication-chat:' + $azureCommunicationChatVersion
implementation 'org.slf4j:slf4j-log4j12:1.7.29'
```

최신 버전 번호는 https://search.maven.org/artifact/com.azure.android/azure-communication-common 및 https://search.maven.org/artifact/com.azure.android/azure-communication-chat 를 참조하세요.

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>루트 build.gradle의 패키징 옵션에서 메타 파일을 제외합니다.
```groovy
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>(대안) Maven을 통해 라이브러리를 설치하려면
[Maven](https://maven.apache.org/) 빌드 시스템을 사용하여 프로젝트로 라이브러리를 가져오려면 아티팩트 ID와 사용할 버전을 지정하여 앱의 `pom.xml` 파일에 있는 `dependencies` 섹션에 라이브러리를 추가합니다.

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version><!-- Please refer to https://search.maven.org/artifact/com.azure.android/azure-communication-chat for the latest version --></version>
</dependency>
```


### <a name="set-up-the-placeholders"></a>자리 표시자 설정

`MainActivity.java` 파일을 열고 편집합니다. 이 빠른 시작에서는 `MainActivity`에 코드를 추가하고 콘솔에서 출력을 확인합니다. 이 빠른 시작은 UI 빌드를 다루지 않습니다. 파일 맨 위에서 `Communication common`, `Communication chat` 및 기타 시스템 라이브러리를 가져옵니다.

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.chat.models.*;
import com.azure.android.communication.common.*;

import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import com.jakewharton.threetenabp.AndroidThreeTen;

import java.util.ArrayList;
import java.util.List;
```

다음 코드를 `MainActivity.java` 파일의 `MainActivity` 클래스에 복사합니다.

```java
    private String endpoint = "https://<resource>.communication.azure.com";
    private String firstUserId = "<first_user_id>";
    private String secondUserId = "<second_user_id>";
    private String firstUserAccessToken = "<first_user_access_token>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "<chat_sdk_version>";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "Chat Quickstart App";
    private ChatAsyncClient chatAsyncClient;

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            AndroidThreeTen.init(this);

            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>
            
            // <RECEIVE CHAT MESSAGES>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

1. `<resource>`을 Communication Services 리소스로 바꿉니다.
2. `<first_user_id>` 및 `<second_user_id>`를 사전 요구 사항 단계의 일부로 생성된 유효한 Communication Services 사용자 ID로 바꿉니다.
3. `<first_user_access_token>`을 사전 요구 사항 단계의 일부로 생성된 `<first_user_id>`의 Communication Services 액세스 토큰으로 바꿉니다.
4. `<chat_sdk_version>`을 Azure Communication Chat SDK 버전으로 바꿉니다.

다음 단계에서는 Azure Communication Services 채팅 라이브러리를 사용하여 자리 표시자를 샘플 코드로 바꿉니다.


### <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

`<CREATE A CHAT CLIENT>` 주석을 다음 코드로 바꿉니다(파일 맨 위에 import 문 배치).

```java
import com.azure.android.core.http.policy.UserAgentPolicy;

chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .buildAsyncClient();

```

## <a name="object-model"></a>개체 모델
다음 클래스 및 인터페이스는 JavaScript용 Azure Communication Services 채팅 SDK의 주요 기능 중 일부를 처리합니다.

| Name                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화한 다음 이를 사용해 스레드를 만들고, 가져오고, 삭제하고, 채팅 이벤트를 구독합니다. |
| ChatThreadClient/ChatThreadAsyncClient | 이 클래스는 채팅 스레드 기능에 필요합니다. 사용자는 ChatClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 사용자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`ChatAsyncClient`를 사용하여 초기 사용자로 새 스레드를 만듭니다.

`<CREATE A CHAT THREAD>` 주석을 다음 코드로 바꿉니다.

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(firstUserId))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

이제 채팅 스레드를 만들었으므로 스레드 내에서 작업을 수행하기 위해 `ChatThreadAsyncClient`를 가져옵니다. `<CREATE A CHAT THREAD CLIENT>` 주석을 다음 코드로 바꿉니다.

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

이제 해당 스레드에 메시지를 보냅니다.

`<SEND A MESSAGE>` 주석을 다음 코드로 바꿉니다.

```java
// The chat message content, required.
final String content = "Please take a look at the attachment";

// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";

// Use metadata optionally to include any additional data you want to send along with the message.
// This field provides a mechanism for developers to extend chat message functionality and add
// custom information for your use case. For example, when sharing a file link in the message, you
// might want to add 'hasAttachment:true' in metadata so that recipient's application can parse
// that and display accordingly.
final Map<String, String> metadata = new HashMap<String, String>();
metadata.put("hasAttachment", "true");
metadata.put("attachmentUrl", "https://contoso.com/files/attachment.docx");

SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName)
    .setMetadata(metadata);

// A string is the response returned from sending a message, it is an id, which is the unique ID
// of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

### <a name="real-time-notifications"></a>실시간 알림
실시간 신호를 통해, 새로 들어오는 메시지를 구독하고 메모리의 현재 메시지를 적절하게 업데이트할 수 있습니다. Azure Communication Services는 [구독할 수 있는 이벤트 목록](../../../concepts/chat/concepts.md#real-time-notifications)을 지원합니다.

`<RECEIVE CHAT MESSAGES>` 주석을 다음 코드로 바꿉니다(파일 맨 위에 import 문 배치).

```java

// Start real time notification
chatAsyncClient.startRealtimeNotifications(firstUserAccessToken, getApplicationContext());

// Register a listener for chatMessageReceived event
chatAsyncClient.addEventHandler(ChatEventType.CHAT_MESSAGE_RECEIVED, (ChatEvent payload) -> {
    ChatMessageReceivedEvent chatMessageReceivedEvent = (ChatMessageReceivedEvent) payload;
    // You code to handle chatMessageReceived event
    
});

```

> [!IMPORTANT]
> 알려진 문제: Android Chat 및 Calling SDK를 동일한 애플리케이션에서 함께 사용하는 경우 Chat SDK의 실시간 알림 기능이 작동하지 않습니다. 종속성 해결 문제가 발생할 수 있습니다.
> 솔루션에 대해 작업하는 동안 앱의 build.gradle 파일에 다음 종속성 정보를 추가하여 실시간 알림 기능을 해제하고 대신 GetMessages API를 폴링하여 들어오는 메시지를 사용자에게 표시할 수 있습니다. 
> 
> ```
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> implementation 'com.azure.android:azure-communication-calling:1.0.0'
> ```
> 
> 위의 업데이트에서 애플리케이션이 `chatAsyncClient.startRealtimeNotifications()` 또는 `chatAsyncClient.addEventHandler()`과 같은 알림 API를 사용하려고 하면 런타임 오류가 발생합니다.

### <a name="push-notifications"></a>푸시 알림

> [!NOTE]
> 현재 채팅 푸시 알림은 버전 1.1.0-beta.4의 Android SDK에 대해서만 지원됩니다.

푸시 알림을 통해 모바일 앱이 포그라운드로 실행되고 있지 않은 상황에서 채팅 스레드에서 발생하는 들어오는 메시지 및 기타 작업에 대한 알림을 클라이언트에 알릴 수 있습니다. Azure Communication Services는 [구독할 수 있는 이벤트 목록](../../../concepts/chat/concepts.md#push-notifications)을 지원합니다.

1. ChatQuickstart 프로젝트를 사용하여 Firebase Cloud Messaging을 설정합니다. [Firebase 문서](https://firebase.google.com/docs/cloud-messaging/android/client)의 `Create a Firebase project`, `Register your app with Firebase`, `Add a Firebase configuration file`, `Add Firebase SDKs to your app` 및 `Edit your app manifest` 단계를 완료합니다.

2. Communication Services 리소스와 동일한 구독 내에 Notification Hub를 만들고, 허브에 대한 Firebase Cloud Messaging 설정을 구성하고, Notification Hub를 Communication Services 리소스에 연결합니다. [Notification Hub 프로비저닝](../../../concepts/notifications.md#notification-hub-provisioning)을 참조하세요.
3. 파일 `MainActivity.java`의 동일한 경로에 새 파일 `MyFirebaseMessagingService.java`를 만듭니다. 다음 코드를 파일 `MyFirebaseMessagingService.java`에 복사합니다. `<your_package_name>`을 `MainActivity.java`에 사용된 패키지 이름으로 바꿔야 합니다. `<your_intent_name>`에 고유한 값을 사용할 수 있습니다. 이 값은 아래의 6단계에서 사용됩니다.

   ```java
      package <your_package_name>;

      import android.content.Intent;
      import android.util.Log;

      import androidx.localbroadcastmanager.content.LocalBroadcastManager;

      import com.azure.android.communication.chat.models.ChatPushNotification;
      import com.google.firebase.messaging.FirebaseMessagingService;
      import com.google.firebase.messaging.RemoteMessage;

      import java.util.concurrent.Semaphore;

      public class MyFirebaseMessagingService extends FirebaseMessagingService {
          private static final String TAG = "MyFirebaseMsgService";
          public static Semaphore initCompleted = new Semaphore(1);

          @Override
          public void onMessageReceived(RemoteMessage remoteMessage) {
              try {
                  Log.d(TAG, "Incoming push notification.");

                  initCompleted.acquire();

                  if (remoteMessage.getData().size() > 0) {
                      ChatPushNotification chatPushNotification =
                          new ChatPushNotification().setPayload(remoteMessage.getData());
                      sendPushNotificationToActivity(chatPushNotification);
                  }

                  initCompleted.release();
              } catch (InterruptedException e) {
                  Log.e(TAG, "Error receiving push notification.");
              }
          }

          private void sendPushNotificationToActivity(ChatPushNotification chatPushNotification) {
              Log.d(TAG, "Passing push notification to Activity: " + chatPushNotification.getPayload());
              Intent intent = new Intent("<your_intent_name>");
              intent.putExtra("PushNotificationPayload", chatPushNotification);
              LocalBroadcastManager.getInstance(this).sendBroadcast(intent);
          }
      }

   ```

4. 파일 `MainActivity.java` 맨 위에 다음 가져오기를 추가합니다.

   ```java
      import android.content.BroadcastReceiver;
      import android.content.Context;
      import android.content.Intent;
      import android.content.IntentFilter;

      import androidx.localbroadcastmanager.content.LocalBroadcastManager;
      import com.azure.android.communication.chat.models.ChatPushNotification;
      import com.google.android.gms.tasks.OnCompleteListener;
      import com.google.android.gms.tasks.Task;
      import com.google.firebase.messaging.FirebaseMessaging;
   ```

5. 다음 코드를 `MainActivity` 클래스에 추가합니다.

   ```java
      private BroadcastReceiver firebaseMessagingReceiver = new BroadcastReceiver() {
          @Override
          public void onReceive(Context context, Intent intent) {
              ChatPushNotification pushNotification =
                  (ChatPushNotification) intent.getParcelableExtra("PushNotificationPayload");

              Log.d(TAG, "Push Notification received in MainActivity: " + pushNotification.getPayload());

              boolean isHandled = chatAsyncClient.handlePushNotification(pushNotification);
              if (!isHandled) {
                  Log.d(TAG, "No listener registered for incoming push notification!");
              }
          }
      };


      private void startFcmPushNotification() {
          FirebaseMessaging.getInstance().getToken()
              .addOnCompleteListener(new OnCompleteListener<String>() {
                  @Override
                  public void onComplete(@NonNull Task<String> task) {
                      if (!task.isSuccessful()) {
                          Log.w(TAG, "Fetching FCM registration token failed", task.getException());
                          return;
                      }

                      // Get new FCM registration token
                      String token = task.getResult();

                      // Log and toast
                      Log.d(TAG, "Fcm push token generated:" + token);
                      Toast.makeText(MainActivity.this, token, Toast.LENGTH_SHORT).show();

                      chatAsyncClient.startPushNotifications(token, new Consumer<Throwable>() {
                          @Override
                          public void accept(Throwable throwable) {
                              Log.w(TAG, "Registration failed for push notifications!", throwable);
                          }
                      });
                  }
              });
      }

   ```

6. 클래스 `MainActivity`에서 함수 `onCreate`를 업데이트합니다.

   ```java
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.activity_main);
    
          LocalBroadcastManager
              .getInstance(this)
              .registerReceiver(
                  firebaseMessagingReceiver,
                  new IntentFilter("<your_intent_name>"));
      }
   ```

7. 주석 `<RECEIVE CHAT MESSAGES>` 아래에 다음 코드를 입력합니다.

```java
   startFcmPushNotification();

   chatAsyncClient.addPushNotificationHandler(CHAT_MESSAGE_RECEIVED, (ChatEvent payload) -> {
       Log.i(TAG, "Push Notification CHAT_MESSAGE_RECEIVED.");
       ChatMessageReceivedEvent event = (ChatMessageReceivedEvent) payload;
       // You code to handle ChatMessageReceived event
   });
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

`<ADD A USER>` 주석을 다음 코드로 바꿉니다.

```java
// The display name for the thread participant.
String secondUserDisplayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```


## <a name="list-users-in-a-thread"></a>스레드에 사용자 나열

`<LIST USERS>` 주석을 다음 코드로 바꿉니다(파일 맨 위에 import 문 배치).

```java
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.RequestContext;

// The maximum number of participants to be returned per page, optional.
int maxPageSize = 10;

// Skips participants up to a specified position in response.
int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatParticipant> participantsPagedAsyncStream =
      chatThreadAsyncClient.listParticipants(listParticipantsOptions, RequestContext.NONE);

participantsPagedAsyncStream.forEach(chatParticipant -> {
    // You code to handle participant
});

```


## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

이제 스레드에서 두 번째 사용자를 제거합니다.

`<REMOVE A USER>` 주석을 다음 코드로 바꿉니다.

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>입력 알림 보내기

`<SEND A TYPING NOTIFICATION>` 주석을 다음 코드로 바꿉니다.

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>읽음 확인 보내기

위에서 보낸 메시지에 대한 읽음 확인을 보냅니다.

`<SEND A READ RECEIPT>` 주석을 다음 코드로 바꿉니다.

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>읽음 확인 나열

`<READ RECEIPTS>` 주석을 다음 코드로 바꿉니다.

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatMessageReadReceipt> readReceiptsPagedAsyncStream =
      chatThreadAsyncClient.listReadReceipts(listReadReceiptOptions, RequestContext.NONE);

readReceiptsPagedAsyncStream.forEach(readReceipt -> {
    // You code to handle readReceipt
});

```

## <a name="run-the-code"></a>코드 실행

Android Studio에서 실행 단추를 눌러 프로젝트를 빌드하고 실행합니다. 콘솔에서 코드의 출력과 ChatClient의 로거 출력을 볼 수 있습니다.
