---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: df7a63c57674417bbee5bf04a154cf64bcdc474f
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910601"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

Android SDK 사용하면 값이 변경될 때 알림을 받을 수 있도록 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

## <a name="properties"></a>속성
`property changed` 이벤트를 구독하는 방법은 다음과 같습니다.

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```
동일한 클래스 내에 정의된 이벤트 수신기를 사용하는 경우 수신기를 변수에 바인딩합니다. 변수를 인수로 전달하여 수신기 메서드를 추가하고 제거합니다.

에서 수신기를 인수로 직접 전달하려고 하면 해당 수신기에 대한 참조가 손실됩니다. Java는 이러한 수신기의 새 인스턴스를 만들고 이전에 만든 인스턴스를 참조하지 않습니다. 여전히 제대로 시작되지만 더 이상 참조가 없으므로 제거할 수 없습니다.


## <a name="collections"></a>컬렉션
`collection updated` 이벤트를 구독하는 방법은 다음과 같습니다.

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
