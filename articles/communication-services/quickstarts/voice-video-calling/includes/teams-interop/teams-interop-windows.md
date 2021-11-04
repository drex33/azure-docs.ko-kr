---
title: 빠른 시작 - Windows 앱에서 Teams 모임에 조인
description: 이 자습서에서는 Windows용 Azure Communication Services 통화 SDK를 사용하여 Teams 모임에 조인하는 방법을 알아봅니다.
author: aurighet
ms.author: aurighet
ms.date: 05/13/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 62a13298e62a9c7f1cfe7a0643f9db2a1dfc9d15
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520155"
---
이 빠른 시작에서는 Windows용 Azure Communication Services 통화 SDK를 사용하여 Teams 모임에 조인하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 작동하는 [Communication Services 호출 Windows 앱](../../getting-started-with-calling.md).
- [Teams 배포](/deployoffice/teams-install)

## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Teams UI 컨트롤을 추가하고 Teams UI 컨트롤을 사용하도록 설정

MainPage.xaml의 코드를 다음 코드 조각으로 바꿉니다. 텍스트 상자는 Teams 모임 컨텍스트를 입력하는 데 사용되며, 단추는 지정된 모임에 참가하는 데 사용됩니다.

```xml
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
        <TextBox PlaceholderText="Please enter the Teams meeting link." TextWrapping="Wrap" x:Name="TeamsLinkTextBox" Margin="10,10,10,10" />
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
            <Button Content="Join Teams Meeting" Click="JoinButton_ClickAsync" x:Name="JoinButton" Margin="10,10,10,10" />
            <Button Content="Leave Meeting" Click="LeaveButton_ClickAsync" x:Name="LeaveButton" Margin="10,10,10,10" />
        </StackPanel>
        <TextBlock TextWrapping="Wrap" x:Name="CallStatusTextBlock" Margin="10,10,10,10" />
        <TextBlock TextWrapping="Wrap" x:Name="RecordingStatusTextBlock" Margin="10,10,10,10" />
    </StackPanel>
</Page>
```

## <a name="enable-the-teams-ui-controls"></a>Teams UI 컨트롤 사용

`MainPage.xaml.cs`의 콘텐츠를 다음 코드 조각으로 바꿉니다.

```csharp
using System;
using System.Threading.Tasks;

using Windows.UI.Core;
using Windows.UI.Popups;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

using Azure.Communication;
using Azure.Communication.Calling;

namespace CallingQuickstart
{
    public sealed partial class MainPage : Page
    {
        string user_token_ = "<User_Access_Token>";

        Call call_;
        CallClient call_client;
        CallAgent call_agent;        

        public MainPage()
        {
            this.InitializeComponent();
            call_client = new CallClient();
        }

        private async void JoinButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            if (!await ValidateInput())
            {
                return;
            }

            //
            //  Create CallAgent
            //
            CommunicationTokenCredential token_credential;
            try
            {
                token_credential = new CommunicationTokenCredential(user_token_);
                CallAgentOptions call_agent_options = new CallAgentOptions();
                call_agent = await call_client.CreateCallAgent(token_credential, call_agent_options);
            }
            catch
            {
                await new MessageDialog("It was not possible to create call agent. Please check if token is valid.").ShowAsync();
                return;
            }

            //
            //  Join a Teams meeting
            //
            try
            {
                JoinCallOptions joinCallOptions = new JoinCallOptions();
                TeamsMeetingLinkLocator teamsMeetingLinkLocator = new TeamsMeetingLinkLocator(TeamsLinkTextBox.Text);
                call_ = await call_agent.JoinAsync(teamsMeetingLinkLocator, joinCallOptions);
            }
            catch
            {
                await new MessageDialog("It was not possible to join the Teams meeting. Please check if Teams Link is valid.").ShowAsync();
                return;
            }

            //
            //  Set up call callbacks
            //
            call_.OnStateChanged += Call_OnStateChangedAsync;
            call_.OnIsRecordingActiveChanged += Call_OnIsRecordingActiveChangedAsync;
        }

        private async void Call_OnStateChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                CallStatusTextBlock.Text = call_.State.ToString();
            });
        }
        
        private async void Call_OnIsRecordingActiveChangedAsync(object sender, PropertyChangedEventArgs args)
        {
            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                RecordingStatusTextBlock.Text = call_.IsRecordingActive ? "Recording is active." : "Recording is inactive.";
            });
        }
        
        private async void LeaveButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            try
            {
                await call_.HangUp(new HangUpOptions());
            }
            catch
            {
                await new MessageDialog("It was not possible to leave the Teams meeting.").ShowAsync();
            }
        }
        
        private async Task<bool> ValidateInput()
        {
            if (user_token_.StartsWith("<"))
            {
                await new MessageDialog("Please enter token in source code.").ShowAsync();
                return false;
            }
        
            if (TeamsLinkTextBox.Text.Trim().Length == 0 || !TeamsLinkTextBox.Text.StartsWith("http"))
            {
                await new MessageDialog("Please enter Teams meeting link.").ShowAsync();
                return false;
            }
        
            return true;
        }
    }
}
```

## <a name="get-the-teams-meeting-link"></a>Teams 미팅 링크 가져오기

Teams 미팅 링크는 Graph API를 사용하여 검색할 수 있습니다. 자세한 내용은 [Graph 설명서](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)에서 설명하고 있습니다.
Communication Services Calling SDK는 전체 Teams 미팅 링크를 수락합니다. 이 링크는 [`joinWebUrl` 속성](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)에서 액세스할 수 있는 `onlineMeeting` 리소스의 일부로 반환됩니다. 또한 Teams 모임 초대 자체의 **모임 조인** URL에서 필요한 모임 정보를 가져올 수 있습니다.

## <a name="launch-the-app-and-join-teams-meeting"></a>앱을 시작하고 Teams 모임에 조인

**디버그** > **디버깅 시작** 을 선택하거나 (F5) 바로 가기 키를 사용하여 Visual Studio에서 앱을 빌드하고 실행할 수 있습니다.

Teams 컨텍스트를 텍스트 상자에 삽입하고, *Teams 모임 참가* 를 눌러 Communication Services 애플리케이션 내에서 Teams 모임에 참가합니다.
