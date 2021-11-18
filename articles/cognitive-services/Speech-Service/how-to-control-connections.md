---
title: 서비스 연결 방법 - Speech SDK
titleSuffix: Azure Cognitive Services
description: 연결 상태를 모니터링하고 Speech Service에서 수동으로 미리 연결하거나 연결을 끊는 방법을 알아봅니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: travisw
zone_pivot_groups: programming-languages-set-thirteen
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3898892e73df804dd65175654cd43f9308ae92f3
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132765167"
---
# <a name="how-to-monitor-and-control-service-connections-with-the-speech-sdk"></a>방법: Speech SDK를 사용하여 서비스 연결 모니터링 및 제어

`SpeechRecognizer` Speech SDK의 및 기타 개체는 적절한 경우 Speech Service에 자동으로 연결됩니다. 경우에 따라 연결이 시작 및 종료될 때를 추가로 제어하거나 Speech SDK가 연결을 설정하거나 끊을 때에 대한 자세한 정보를 원할 수 있습니다. 지원 `Connection` 클래스는 이 기능을 제공합니다.

## <a name="retrieve-a-connection-object"></a>Connection 개체 검색

정적 `Connection` 팩터리 메서드(예: 의 경우)를 통해 대부분의 최상위 Speech SDK 개체에서 를 가져올 수 `From...` `Connection::FromRecognizer(recognizer)` `SpeechRecognizer` 있습니다.

::: zone pivot="programming-language-csharp"

```csharp
var connection = Connection.FromRecognizer(recognizer);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto connection = Connection::FromRecognizer(recognizer);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
Connection connection = Connection.fromRecognizer(recognizer);
```

::: zone-end

## <a name="monitor-for-connections-and-disconnections"></a>연결 및 연결 끊김 모니터링

는 `Connection` `Connected` Speech `Disconnected` SDK의 Speech Service 연결에서 해당 상태 변경이 발생할 때 및 이벤트를 발생합니다. 이러한 이벤트를 수신 대기하여 최신 연결 상태를 알 수 있습니다.

::: zone pivot="programming-language-csharp"

```csharp
connection.Connected += (sender, connectedEventArgs) =>
{
    Console.WriteLine($"Successfully connected, sessionId: {connectedEventArgs.SessionId}");
};
connection.Disconnected += (sender, disconnectedEventArgs) =>
{
    Console.WriteLine($"Disconnected, sessionId: {disconnectedEventArgs.SessionId}");
};
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
connection->Connected += [&](ConnectionEventArgs& args)
{
    std::cout << "Successfully connected, sessionId: " << args.SessionId << std::endl;
};
connection->Disconnected += [&](ConnectionEventArgs& args)
{
    std::cout << "Disconnected, sessionId: " << args.SessionId << std::endl;
};
```

::: zone-end

::: zone pivot="programming-language-java"

```java
connection.connected.addEventListener((s, connectionEventArgs) -> {
    System.out.println("Successfully connected, sessionId: " + connectionEventArgs.getSessionId());
});
connection.disconnected.addEventListener((s, connectionEventArgs) -> {
    System.out.println("Disconnected, sessionId: " + connectionEventArgs.getSessionId());
});
```

::: zone-end

## <a name="connect-and-disconnect"></a>연결 및 연결 끊기

`Connection` 에는 Speech Service에 대한 연결을 시작하거나 종료하는 명시적 메서드가 있습니다. 이러한 사용이 필요할 수 있는 이유는 다음과 같습니다.

- Speech Service에 "미리 연결"하여 첫 번째 상호 작용이 최대한 빨리 시작될 수 있도록 합니다.
- 초기 연결 실패를 정상적으로 예측 가능하게 처리하기 위해 애플리케이션 논리에서 특정 시간에 연결 설정
- 즉시 다시 연결이 필요하지 않지만 개체를 삭제하지 않으려는 경우 유휴 연결을 지우기 위해 연결 끊기

연결 상태를 수동으로 수정할 때 동작에 대한 몇 가지 중요한 참고 사항:

- 이미 연결된 경우 연결하려고 하면 아무 것도 수행하지 않습니다. 오류가 생성되지 않습니다. `Connected`연결의 현재 상태를 알고 싶은 경우 및 `Disconnected` 이벤트를 모니터링합니다.
- 잘못된 상태에서 수행하려는 시도와 같이 Speech Service와 관련이 없는 문제에서 발생하는 연결 실패는 프로그래밍 언어에 적절하게 오류를 throw하거나 반환합니다. 인증 실패와 같이 네트워크 확인이 필요한 오류는 오류를 throw하거나 반환하지 않고 가 `Canceled` 만들어진 최상위 개체에서 이벤트를 생성합니다. `Connection`
- 진행 중인 상호 작용 중에 Speech Service와의 연결을 수동으로 끊는 경우 연결 오류가 발생하여 해당 상호 작용에 대한 데이터가 손실됩니다. 연결 오류는 적절한 최상위 개체의 이벤트에 `Canceled` 표시됩니다.

::: zone pivot="programming-language-csharp"

```csharp
try
{
    connection.Open(forContinuousRecognition: false);
}
catch (ApplicationException ex)
{
    Console.WriteLine($"Couldn't pre-connect. Details: {ex.Message}");
}
// ... Use the SpeechRecognizer
connection.Close();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
try
{
    connection->Open(false);
}
catch (std::runtime_error&)
{
    std::cout << "Unable to pre-connect." << std::endl;
}
// ... Use the SpeechRecognizer
connection->Close();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
try {
    connection.openConnection(false);
} catch {
   System.out.println("Unable to pre-connect.");
}
// ... Use the SpeechRecognizer
connection.closeConnection();
```

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)
