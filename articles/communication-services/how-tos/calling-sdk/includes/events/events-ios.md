---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0ba2baf8d6517a4719f63bf4cec37541e223540a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705622"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

iOS SDK를 사용하면 값이 변경될 때 알림을 받을 수 있도록 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

## <a name="properties"></a>속성
`property changed` 이벤트를 구독하려면 다음 코드를 사용합니다.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

// to unsubscribe
self.call.delegate = nil
```

## <a name="collections"></a>컬렉션
`collection updated` 이벤트를 구독하려면 다음 코드를 사용합니다.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func call(_ call: Call, didUpdateLocalVideoStreams args: LocalVideoStreamsUpdatedEventArgs) {
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```