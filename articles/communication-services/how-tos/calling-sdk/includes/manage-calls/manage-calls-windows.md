---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c2d11835a455cb0a8a9456aad95d5be4da423259
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705619"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-windows.md)]

### <a name="request-access-to-the-microphone"></a>마이크에 대한 액세스 요청

앱이 제대로 실행되려면 마이크에 대한 액세스가 필요합니다. UWP 앱의 앱 매니페스트 파일에서 마이크 기능을 선언해야 합니다. 다음 단계는 이를 수행하는 방법을 안내합니다.

1. `Solution Explorer` 패널에서 확장명이 `.appxmanifest`인 파일을 두 번 클릭합니다.
2. `Capabilities` 탭을 클릭합니다.
3. 기능 목록에서 `Microphone` 확인란을 선택합니다.

### <a name="create-ui-buttons-to-place-and-hang-up-the-call"></a>통화를 걸고 끊는 UI 단추 만들기

이 간단한 샘플 앱에는 두 개의 단추가 포함됩니다. 하나는 통화를 걸고 다른 하나는 통화를 끊습니다.
다음 단계에서는 앱에 이러한 단추를 추가하는 방법을 안내합니다.

1. `Solution Explorer` 패널에서 `MainPage.xaml` 파일을 두 번 클릭합니다.
2. 중앙 패널의 UI 미리 보기에서 XAML 코드를 찾습니다.
3. `<Grid>` - `</Grid>` XAML 코드를 다음 발췌로 바꿉니다.
```xml
<StackPanel Orientation="Horizontal" VerticalAlignment="Center" HorizontalAlignment="Center">
    <Button x:Name="callButton" Click="CallHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Call</Button>
    <Button x:Name="hangupButton" Click="HangupHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Hang up</Button>
</StackPanel>
```

### <a name="setting-up-the-app-with-calling-sdk-apis"></a>Calling SDK API를 사용하여 앱 설정

Calling SDK API는 두 개의 다른 네임스페이스에 있습니다.
다음 단계에서는 Visual Studio의 IntelliSense에서 코드 개발을 지원할 수 있도록 하는 이러한 네임스페이스에 대해 C# 컴파일러에 알립니다.

1. `Solution Explorer` 패널에서 `MainPage.xaml` 파일의 왼쪽에 있는 화살표를 클릭합니다.
2. 나타난 `MainPage.xaml.cs` 파일을 두 번 클릭합니다.
3. 현재의 `using`문 아래에 다음 명령을 추가합니다.

```csharp
using Azure.Communication;
using Azure.Communication.Calling;
```

`MainPage.xaml.cs`를 연 상태로 유지합니다. 다음 단계에서는 더 많은 코드를 추가합니다.

## <a name="allow-app-interactions"></a>앱 상호 작용 허용

수행된 `Call`에 대해 이전에 추가한 UI 단추가 작동해야 합니다. 즉, `Call` 데이터 멤버가 `MainPage` 클래스에 추가되어야 합니다.
또한 `CallAgent`를 생성하는 비동기 작업이 성공하려면 동일한 클래스에 `CallAgent` 데이터 멤버도 추가되어야 합니다.

`MainPage` 클래스에 다음 데이터 멤버를 추가합니다.
```csharp
CallAgent agent_;
Call call_;
```

## <a name="create-button-handlers"></a>단추 처리기 만들기

이전에는 XAML 코드에 두 개의 UI 단추가 추가되었습니다. 다음 코드에서는 사용자가 단추를 클릭하면 실행될 처리기를 추가합니다.
이전 섹션에서 데이터 멤버 뒤에 다음 코드를 추가해야 합니다.

```csharp
private void CallHandler(object sender, RoutedEventArgs e)
{
}

private void HangupHandler(object sender, RoutedEventArgs e)
{
}
```

## <a name="object-model"></a>개체 모델

UWP용 Azure Communication Services 통화 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 속성                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient는 통화 클라이언트 라이브러리의 주 진입점입니다. |
| CallAgent | CallAgent는 통화를 시작하고 통화에 참여하는 데 사용됩니다. |
| 호출 | Call은 수행한 통화 또는 참여한 통화를 관리하는 데 사용됩니다. |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.|
| CallAgentOptions | CallAgentOptions는 호출자를 식별하는 정보를 포함합니다. |
| HangupOptions | HangupOptions는 모든 참가자에게 통화를 종료해야 하는지 여부를 알립니다. |

## <a name="initialize-the-callagent"></a>CallAgent 초기화

`CallClient`에서 `CallAgent` 인스턴스를 만들려면 초기화된 후 비동기적으로 `CallAgent` 개체를 반환하는 `CallClient.CreateCallAgent` 메서드를 사용해야 합니다.

`CallAgent`를 만들려면 `CommunicationTokenCredential` 개체와 `CallAgentOptions` 개체를 전달해야 합니다. 잘못된 형식의 토큰이 전달되면 `CommunicationTokenCredential`이 throw됩니다.

다음 코드는 `CallHandler` 내에 추가되어야 합니다.

```csharp
CallClient client = new CallClient();
CommunicationTokenCredential credentials;

CallAgentOptions callAgentOptions = new CallAgentOptions
{
  DisplayName = "<CALLER NAME>"
};

try
{
    credentials = new CommunicationTokenCredential("<CREDENTIAL TOKEN>");
}
catch (Exception)
{
    throw new Exception("Invalid credential token");
}
```

`<USER ACCESS TOKEN>`을 리소스의 유효한 자격 증명 토큰으로 바꾸어야 합니다. 자격 증명 토큰을 원본으로 사용해야 하는 경우에는 [사용자 액세스 토큰](../../../../quickstarts/access-tokens.md) 설명서를 참조하세요.

## <a name="create-callagent-and-place-a-call"></a>CallAgent를 만들고 통화 걸기

`CallAgent`를 만드는 데 필요한 개체가 준비되었습니다. 이제 `CallAgent`를 비동기식으로 만들고 통화를 걸어야 합니다.

이전 단계의 예외를 처리한 후 다음 코드를 추가해야 합니다.

```csharp
client.CreateCallAgent(creds, callAgentOptions).Completed +=
async (IAsyncOperation<CallAgent> asyncInfo, AsyncStatus asyncStatus) =>
{
    agent_ = asyncInfo.GetResults();

    CommunicationUserIdentifier target = new CommunicationUserIdentifier("<CALLEE>");

    StartCallOptions startCallOptions = new StartCallOptions();
    call_ = await agent_.StartCallAsync(new List<ICommunicationIdentifier>() { target }, startCallOptions);
};
```

`<CALLEE>`를 테넌트의 다른 ID로 바꿉니다. 또는 `8:echo123`을 사용하여 ACS echo 봇과 통신할 수 있습니다.

## <a name="end-a-call"></a>통화 종료

통화가 걸린 후에는 `Call` 개체의 `Hangup` 메서드를 사용하여 통화를 중단해야 합니다.

또한 `HangupOptions`의 인스턴스를 사용하여 모든 참가자에게 통화를 종료해야 하는지 여부를 알려야 합니다.

다음 코드는 `HangupHandler` 내에 추가되어야 합니다.

```csharp
HangUpOptions hangupOptions = new HangUpOptions();
call_.HangUpAsync(hangupOptions).Completed += (IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{

};
```

## <a name="run-the-code"></a>코드 실행

Visual Studio에서 `x64`, `x86` 또는 `ARM64`를 위한 앱을 빌드할 수 있는지 확인한 다음, `F5`를 눌러 앱 실행을 시작합니다. 그런 다음, `Call` 단추를 클릭하여 정의된 통화 수신자에게 통화를 겁니다.

앱을 처음 실행하면 마이크에 대한 액세스 권한을 부여하라는 메시지가 표시됩니다.