---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: b02c91690672d214017c554043e8ed1ec9a2df62
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129440017"
---
이 빠른 시작에서는 Windows용 Azure Communication Services Calling SDK를 사용하여 통화를 시작하는 방법에 대해 알아봅니다.

## <a name="sample-code"></a>샘플 코드

샘플 앱은 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/VoiceCalling)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 유니버설 Windows 플랫폼 개발 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다. 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- [사용자 액세스 토큰](../../../access-tokens.md)(Azure Communication Service용)

## <a name="setting-up"></a>설치

### <a name="creating-the-project"></a>프로젝트 만들기

Visual Studio에서 **비어 있는 앱(유니버설 Windows)** 템플릿을 사용하여 단일 페이지 UWP(유니버설 Windows 플랫폼) 앱을 설정하는 새 프로젝트를 만듭니다.

:::image type="content" source="../../media/windows/create-a-new-project.png" alt-text="Visual Studio 내 새 프로젝트 창을 보여 주는 스크린샷":::

### <a name="install-the-package"></a>패키지 설치

프로젝트를 마우스 오른쪽 단추로 클릭하고`Manage Nuget Packages`로 이동하여 `Azure.Communication.Calling`을 설치합니다. 

### <a name="request-access"></a>액세스 요청

`Package.appxmanifest`로 이동하여 `Capabilities`를 클릭합니다.
인터넷에 대한 인바운드 및 아웃바운드 액세스 권한을 얻으려면 `Internet (Client & Server)`을 선택합니다. 마이크의 오디오 피드에 액세스하려면 `Microphone`을 선택합니다. 

:::image type="content" source="../../media/windows/request-access.png" alt-text="Visual Studio에서 인터넷 및 마이크에 대한 액세스 요청을 보여 주는 스크린샷":::

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

논리를 연결하려면 기본 레이아웃을 구성해야 합니다. 아웃바운드 호출을 수행하려면 호출 수신자의 사용자 ID를 입력할 `TextBox`가 필요합니다. 또한 `Start Call` 단추와 `Hang Up` 단추도 필요합니다. 프로젝트의 `MainPage.xaml`을 열고 `StackPanel` 노드를 `Page`에 추가합니다. 

```C#
<Page
    x:Class="CallingQuickstart.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:CallingQuickstart"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
        <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
        <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
    </StackPanel>
</Page>
```

`MainPage.xaml.cs`를 열고 콘텐츠를 다음 구현으로 바꿉니다. 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.InitCallAgent();
        }
        
        private async void InitCallAgent()
        {
            // Create Call Client and initialize Call Agent
        }
        
        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Start call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient call_client_;
        CallAgent call_agent_;
        Call call_;
    }
}
```

## <a name="object-model"></a>개체 모델

Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient는 Calling SDK의 주 진입점입니다.|
| CallAgent | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다. |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier는 사용자의 ID를 나타내는 데 사용되며 다음 중 하나일 수 있습니다. CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>클라이언트 인증

사용자 액세스 토큰으로 `CallAgent` 인스턴스를 초기화하면 전화를 걸고 받을 수 있습니다. 

다음 코드에서 `<USER_ACCESS_TOKEN>`을 사용자 액세스 토큰으로 바꿉니다. 사용할 수 있는 토큰이 아직 없는 경우 [사용자 액세스 토큰](../../../access-tokens.md) 설명서를 참조하세요.

`InitCallAgent` 함수에 다음 코드를 추가합니다. 

```C#
CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
call_client_ = new CallClient();

CallAgentOptions callAgentOptions = new CallAgentOptions()
{
    DisplayName = "<YOUR_DISPLAY_NAME>"
};
call_agent_ = await call_client_.CreateCallAgent(token_credential, callAgentOptions);
```

## <a name="start-a-call"></a>통화 시작

생성된 `call_agent`를 사용하여 호출을 시작하는 구현을 `CallButton_ClickAsync`에 추가합니다. 

```C#
StartCallOptions startCallOptions = new StartCallOptions();
ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
{
    new CommunicationUserIdentifier(CalleeTextBox.Text)
};
call_ = await call_agent_.StartCallAsync(callees, startCallOptions);
```

## <a name="end-a-call"></a>통화 종료

`Hang Up` 단추를 클릭하면 현재 통화가 종료됩니다. 

```C#
await call_.HangUpAsync(new HangUpOptions());
```

## <a name="run-the-code"></a>코드 실행

Visual Studio에서 코드를 빌드하고 실행할 수 있습니다. 솔루션 플랫폼의 경우 `ARM64`, `x64` 및 `x86`이 지원됩니다. 

텍스트 필드에 사용자 ID를 지정하고 `Start Call` 단추를 클릭하여 아웃바운드 호출을 수행할 수 있습니다. `8:echo123`에 전화를 걸면 에코 봇과 연결됩니다. 이렇게 하면 통화를 시작하고 오디오 디바이스가 작동하는지 확인하는 데 유용합니다.

:::image type="content" source="../../media/windows/run-the-app.png" alt-text="빠른 시작 앱 실행을 보여 주는 스크린샷":::
