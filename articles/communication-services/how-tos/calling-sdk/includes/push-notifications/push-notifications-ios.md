---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c1d03c13ac4d2f4911baf0315358003dd43e0b3b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838736"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="set-up-push-notifications"></a>푸시 알림 설정

모바일 푸시 알림은 모바일 디바이스에 표시되는 팝업 알림입니다. 통화에서는 VoIP(Voice over Internet Protocol) 푸시 알림을 집중적으로 다루겠습니다. 

다음 섹션에서는 푸시 알림을 등록, 처리 및 등록 취소하는 방법을 설명합니다. 이러한 작업을 시작하기 전에 다음 필수 조건을 완료해야 합니다.

1. Xcode에서 **서명 및 기능** 으로 이동합니다. **+ 기능** 을 선택하여 기능을 추가한 다음, **푸시 알림** 을 선택합니다.
2. **+ 기능** 을 선택하여 다른 기능을 추가한 다음, **백그라운드 모드** 를 선택합니다.
3. **백그라운드 모드** 에서 **Voice over IP** 및 **원격 알림** 확인란을 선택합니다.

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png" alt-text="Xcode에서 기능을 추가하는 방법을 보여주는 스크린샷." lightbox="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>푸시 알림 등록

푸시 알림을 등록하려면 디바이스 등록 토큰을 사용하여 `CallAgent` 인스턴스에서 `registerPushNotification()`을 호출해야 합니다.

성공적으로 초기화한 이후 푸시 알림에 대한 등록이 이루어져야 합니다. `callAgent` 개체가 destroy되면 푸시 알림을 자동으로 등록 취소하는 `logout`이 호출됩니다.

```swift
let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}
```

## <a name="handle-push-notifications"></a>푸시 알림 처리
수신 전화에 대한 푸시 알림을 받으려면 사전 페이로드를 사용하여 `CallAgent` 인스턴스에서 `handlePushNotification()`을 호출합니다.

```swift
let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}
```
## <a name="unregister-push-notifications"></a>푸시 알림 등록 취소

애플리케이션에서 언제든지 푸시 알림을 등록 취소할 수 있습니다. `CallAgent`에서 `unregisterPushNotification` 메서드를 호출하기만 하면 됩니다.

> [!NOTE]
> 로그아웃 시 애플리케이션이 푸시 알림에서 자동으로 등록 취소되지 않습니다.

```swift
callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}
```