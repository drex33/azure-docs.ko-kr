---
ms.openlocfilehash: 91ed308ecbeeb839dfddb78c0151e2c8aafd0d3b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123122512"
---
이 빠른 시작에서는 Windows용 Azure Communication Services Calling SDK를 사용하여 1:1 화상 통화를 시작하는 방법에 대해 알아봅니다.

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

프로젝트를 마우스 오른쪽 단추로 클릭하고`Manage Nuget Packages`로 이동하여 `[Azure.Communication.Calling](https://www.nuget.org/packages/Azure.Communication.Calling)`을 설치합니다. 시험판 포함이 선택되어 있고 패키지 원본이 https://www.nuget.org/api/v2/ 에서 온 것인지 확인합니다. 

### <a name="request-access"></a>액세스 요청

`Package.appxmanifest`로 이동하여 `Capabilities`를 클릭합니다.
인터넷에 대한 인바운드 및 아웃바운드 액세스 권한을 얻으려면 `Internet (Client & Server)`을 선택합니다. 마이크의 오디오 피드에 액세스하려면 `Microphone`을 선택합니다. 디바이스의 카메라에 액세스하려면 `WebCam`을 선택합니다. 

마우스 오른쪽 단추를 클릭하고 코드 보기를 선택하여 `Package.appxmanifest`에 다음 코드를 추가합니다. 
```XML
<Extensions>
<Extension Category="windows.activatableClass.inProcessServer">
<InProcessServer>
<Path>RtmMvrUap.dll</Path>
<ActivatableClass ActivatableClassId="VideoN.VideoSchemeHandler" ThreadingModel="both" />
</InProcessServer>
</Extension>
</Extensions>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

논리를 연결하려면 기본 레이아웃을 구성해야 합니다. 아웃바운드 호출을 수행하려면 호출 수신자의 사용자 ID를 입력할 `TextBox`가 필요합니다. 또한 `Start Call` 단추와 `Hang Up` 단추도 필요합니다. 로컬 비디오를 미리 보고 다른 참가자의 원격 비디오도 렌더링해야 합니다. 따라서 비디오 스트림을 표시하려면 두 개의 요소가 필요합니다.

프로젝트의 `MainPage.xaml`을 열고 콘텐츠를 다음 구현으로 바꿉니다. 

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
        <StackPanel>
            <TextBox Text="Who would you like to call?" TextWrapping="Wrap" x:Name="CalleeTextBox" Margin="10,10,10,10"></TextBox>
            <Button Content="Start Call" Click="CallButton_ClickAsync" x:Name="CallButton" Margin="10,10,10,10"></Button>
            <Button Content="Hang Up" Click="HangupButton_Click" x:Name="HangupButton" Margin="10,10,10,10"></Button>
        </StackPanel>
        <StackPanel Orientation="Vertical" HorizontalAlignment="Center">
            <MediaElement x:Name="RemoteVideo" AutoPlay="True" Stretch="UniformToFill"/>
            <MediaElement x:Name="LocalVideo" AutoPlay="True"  Stretch="UniformToFill" HorizontalAlignment="Right"  VerticalAlignment="Bottom"/>
        </StackPanel>
    </StackPanel>
</Page>
```

`App.xaml.cs`를 열고(마우스 오른쪽 단추를 클릭하고 코드 보기 선택) 이 줄을 맨 위에 추가합니다.
```C#
using CallingQuickstart;
```

`MainPage.xaml.cs`를 열고(마우스 오른쪽 단추로 클릭하고 코드 보기 선택) 콘텐츠를 다음 구현으로 바꿉니다. 
```C#
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.WinRT.Communication;
using Azure.Communication.Calling;
using System.Diagnostics;
using System.Threading.Tasks;
using System.Collections.Generic;

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
            this.InitCallAgentAndDeviceManager();
        }
        
        private async void InitCallAgentAndDeviceManager()
        {
            // Initialize call agent and Device Manager
        }

        private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            // Authenticate the client and start call
        }
        
        private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
        {
            // Accept an incoming call
        }

        private async void HangupButton_Click(object sender, RoutedEventArgs e)
        {
            // End the current call
        }

        CallClient callClient;
        CallAgent callAgent;
        Call call;
        DeviceManager deviceManager;
        LocalVideoStream[] localVideoStream;
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

`CallAgent`를 초기화하려면 사용자 액세스 토큰이 필요합니다. 일반적으로 이 토큰은 애플리케이션과 관련된 인증을 사용하는 서비스에서 생성됩니다. 사용자 액세스 토큰에 대한 자세한 내용은 [사용자 액세스 토큰](../../../access-tokens.md) 가이드를 확인하세요. 

빠른 시작에서는 `<USER_ACCESS_TOKEN>`을 Azure Communication Services 리소스에 대해 생성된 사용자 액세스 토큰으로 바꿉니다.

토큰이 있으면 이 토큰으로 `CallAgent` 인스턴스를 초기화하여 전화를 걸고 받을 수 있습니다. 디바이스의 카메라에 액세스하려면 디바이스 관리자 인스턴스도 가져와야 합니다. 

```C#
private async void InitCallAgentAndDeviceManager()
{
    CallClient callClient = new CallClient();
    deviceManager = await callClient.GetDeviceManager();

    CommunicationTokenCredential token_credential = new CommunicationTokenCredential("<USER_ACCESS_TOKEN>");
    
    CallAgentOptions callAgentOptions = new CallAgentOptions()
    {
        DisplayName = "<DISPLAY_NAME>"
    };
    callAgent = await callClient.CreateCallAgent(token_credential, callAgentOptions);
    callAgent.OnCallsUpdated += Agent_OnCallsUpdated;
    callAgent.OnIncomingCall += Agent_OnIncomingCall;
}
```

## <a name="start-a-call-with-video"></a>비디오로 통화 시작

비디오로 통화를 시작하려면 디바이스 관리자 인스턴스를 사용하여 카메라를 열거하고 `LocalVideoStream`을 생성해야 합니다. `LocalVideoStream`을 사용하여 `VideoOptions`를 설정하고 `startCallOptions`를 전달하여 호출의 초기 옵션을 설정해야 합니다. `LocalVideoStream`을 `MediaElement`에 연결하면 로컬 비디오의 미리 보기를 볼 수 있습니다. 

```C#
private async void CallButton_ClickAsync(object sender, RoutedEventArgs e)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }

    StartCallOptions startCallOptions = new StartCallOptions();
    startCallOptions.VideoOptions = new VideoOptions(localVideoStream);
    ICommunicationIdentifier[] callees = new ICommunicationIdentifier[1]
    {
        new CommunicationUserIdentifier(CalleeTextBox.Text)
    };

    call = await callAgent.StartCallAsync(callees, startCallOptions);
}
```

## <a name="accept-an-incoming-call"></a>수신 전화 받기

비디오를 사용하여 수신 전화에 응답하려면 `LocalVideoStream`을 `acceptCallOptions`에 전달합니다. 

```C#
private async void Agent_OnIncomingCall(object sender, IncomingCall incomingcall)
{
    Debug.Assert(deviceManager.Microphones.Count > 0);
    Debug.Assert(deviceManager.Speakers.Count > 0);
    Debug.Assert(deviceManager.Cameras.Count > 0);

    if (deviceManager.Cameras.Count > 0)
    {
        VideoDeviceInfo videoDeviceInfo = deviceManager.Cameras[0];
        localVideoStream = new LocalVideoStream[1];
        localVideoStream[0] = new LocalVideoStream(videoDeviceInfo);

        Uri localUri = await localVideoStream[0].CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            LocalVideo.Source = localUri;
            LocalVideo.Play();
        });

    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    acceptCallOptions.VideoOptions = new VideoOptions(localVideoStream);

    call = await incomingcall.AcceptAsync(acceptCallOptions);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>원격 참가자 및 원격 비디오 스트림

모든 원격 참가자는 통화 인스턴스의 `RemoteParticipants` 컬렉션을 통해 사용할 수 있습니다. 전화가 연결되면 전화의 원격 참가자에게 액세스하고 원격 비디오 스트림을 처리할 수 있습니다. 

```C#
private async void Agent_OnCallsUpdated(object sender, CallsUpdatedEventArgs args)
{
    foreach (var call in args.AddedCalls)
    {
        foreach (var remoteParticipant in call.RemoteParticipants)
        {
            await AddVideoStreams(remoteParticipant.VideoStreams);
            remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
        }
        call.OnRemoteParticipantsUpdated += Call_OnRemoteParticipantsUpdated;
        call.OnStateChanged += Call_OnStateChanged;
    }
}

private async void Call_OnRemoteParticipantsUpdated(object sender, ParticipantsUpdatedEventArgs args)
{
    foreach (var remoteParticipant in args.AddedParticipants)
    {
        await AddVideoStreams(remoteParticipant.VideoStreams);
        remoteParticipant.OnVideoStreamsUpdated += async (s, a) => await AddVideoStreams(a.AddedRemoteVideoStreams);
    }
}
```

### <a name="render-remote-videos"></a>원격 비디오 렌더링

각 원격 비디오 스트림의 경우 `MediaElement`에 연결합니다. 

```C#
private async Task AddVideoStreams(IReadOnlyList<RemoteVideoStream> streams)
{

    foreach (var remoteVideoStream in streams)
    {
        var remoteUri = await remoteVideoStream.CreateBindingAsync();

        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            RemoteVideo.Source = remoteUri;
            RemoteVideo.Play();
        });
        remoteVideoStream.Start();
    }
}
```

## <a name="call-state-update"></a>통화 상태 업데이트
통화가 끊기면 비디오 렌더러를 정리해야 합니다. 

```C#
private async void Call_OnStateChanged(object sender, PropertyChangedEventArgs args)
{
    switch (((Call)sender).State)
    {
        case CallState.Disconnected:
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                LocalVideo.Source = null;
                RemoteVideo = null;
            });
            break;
        default:
            Debug.WriteLine(((Call)sender).State);
            break;
    }
}
```

## <a name="end-a-call"></a>통화 종료

`Hang Up` 단추를 클릭하면 현재 통화가 종료됩니다. 

```C#
private async void HangupButton_Click(object sender, RoutedEventArgs e)
{
    var hangUpOptions = new HangUpOptions();
    await call.HangUpAsync(hangUpOptions);
}
```

## <a name="run-the-code"></a>코드 실행

Visual Studio에서 코드를 빌드하고 실행할 수 있습니다. 솔루션 플랫폼의 경우 `ARM64`, `x64` 및 `x86`이 지원됩니다. 

텍스트 필드에 사용자 ID를 지정하고 `Start Call` 단추를 클릭하여 아웃바운드 비디오 호출을 수행할 수 있습니다. 

참고: 에코 봇은 동영상 스트리밍을 지원하지 않으므로 `8:echo123`을 호출하면 동영상 스트리밍이 중지됩니다. 

사용자 ID에 대한 자세한 내용은 [사용자 액세스 토큰](../../../access-tokens.md) 가이드를 확인하세요. 
