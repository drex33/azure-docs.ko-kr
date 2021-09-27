---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9ecf53a84a804cd312f4d3a98661f4e688726577
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838734"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

### <a name="additional-prerequisites-for-push-notifications"></a>푸시 알림에 대 한 추가 필수 구성 요소

Cloud Messaging(FCM)을 사용하도록 설정되었으며 Firebase Cloud Messaging 서비스가 Azure Notification Hub 인스턴스에 연결된 Firebase 계정 자세한 내용은 [Communication Services 알림](../../../../concepts/notifications.md)을 참조하세요.
또한 이 자습서에서는 Android Studio 버전 3.6 이상을 사용하여 애플리케이션을 빌드하는 것으로 가정합니다.

Firebase Cloud Messaging 서비스에서 알림 메시지를 받으려면 Android 애플리케이션에 대한 권한 세트가 필요합니다. 파일에서 `AndroidManifest.xml` 태그 바로 뒤에 다음 권한 집합을 추가 합니다 `<manifest ...>` `</application>` .

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

## <a name="overview"></a>개요
모바일 푸시 알림은 모바일 디바이스에 표시되는 팝업 알림입니다. 통화에서는 VoIP(Voice over Internet Protocol) 푸시 알림을 집중적으로 다루겠습니다. 푸시 알림을 등록하고 푸시 알림을 처리한 다음, 푸시 알림을 등록 취소하겠습니다.

## <a name="register-for-push-notifications"></a>푸시 알림 등록

푸시 알림을 등록 하려면 응용 프로그램이 `registerPushNotification()` `CallAgent` 장치 등록 토큰을 사용 하 여 인스턴스에 대해를 호출 해야 합니다.

장치 등록 토큰을 가져오려면 `build.gradle` 섹션에 다음 줄을 추가 하 여 응용 프로그램 모듈의 파일에 FIREBASE SDK를 추가 합니다 ( `dependencies` 아직 없는 경우).

```groovy
// Add the SDK for Firebase Cloud Messaging
implementation 'com.google.firebase:firebase-core:16.0.8'
implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

프로젝트 수준의 *build.gradle* 파일에서 다음을 `dependencies` 섹션에 추가합니다(아직 없는 경우).

```groovy
classpath 'com.google.gms:google-services:4.3.3'
```

다음 플러그 인이 아직 없는 경우 파일 첫 부분에 추가합니다.

```groovy
apply plugin: 'com.google.gms.google-services'
```

도구 모음에서 *지금 동기화* 를 선택합니다. 다음 코드 조각을 추가하여 클라이언트 애플리케이션 인스턴스에 대한 Firebase Cloud Messaging SDK에서 생성한 디바이스 등록 토큰을 가져옵니다. 인스턴스에 대한 주 활동의 헤더에 아래 가져오기를 추가해야 합니다. 코드 조각이 토큰을 검색하는 데 필요합니다.

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

다음 코드 조각을 추가하여 토큰을 검색합니다.

```java
FirebaseInstanceId.getInstance().getInstanceId()
    .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
        @Override
        public void onComplete(@NonNull Task<InstanceIdResult> task) {
            if (!task.isSuccessful()) {
                Log.w("PushNotification", "getInstanceId failed", task.getException());
                return;
            }

            // Get new Instance ID token
            String deviceToken = task.getResult().getToken();
            // Log
            Log.d("PushNotification", "Device Registration token retrieved successfully");
        }
    });
```
수신 통화 푸시 알림에 대한 Calling Services SDK에 디바이스 등록 토큰을 등록합니다.

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

## <a name="push-notification-handling"></a>푸시 알림 처리

수신 통화 푸시 알림을 받으려면 페이로드가 있는 *CallAgent* 인스턴스에서 *handlePushNotification()* 을 호출합니다.

Firebase Cloud Messaging에서 페이로드를 가져오려면 *FirebaseMessagingService* Firebase SDK 클래스를 확장하는 새 서비스를 만들고(파일 > 새로 만들기 > 서비스 > 서비스) `onMessageReceived` 메서드를 재정의합니다. 이 메서드는 Firebase Cloud Messaging에서 애플리케이션에 푸시 알림을 전달할 때 호출되는 이벤트 처리기입니다.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
`AndroidManifest.xml` 파일의 `<application>` 태그 내부에 다음 서비스 정의를 추가합니다.

```xml
<service
    android:name=".MyFirebaseMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

- 페이로드를 검색한 후에는 *CallAgent* 인스턴스에서 *handlePushNotification* 메서드를 호출하여 처리되는 내부 *IncomingCallInformation* 개체로 구문 분석될 *Communication Services* SDK에 전달할 수 있습니다. `CallAgent` 인스턴스는 `CallClient` 클래스에서 `createCallAgent(...)` 메서드를 호출하여 만듭니다.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

푸시 알림 메시지가 성공적으로 처리되고 모든 이벤트 처리기가 제대로 등록되면 애플리케이션이 전화를 겁니다.

## <a name="unregister-push-notifications"></a>푸시 알림 등록 취소

애플리케이션에서 언제든지 푸시 알림을 등록 취소할 수 있습니다. 등록을 취소하려면 callAgent에서 `unregisterPushNotification()` 메서드를 호출합니다.

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```