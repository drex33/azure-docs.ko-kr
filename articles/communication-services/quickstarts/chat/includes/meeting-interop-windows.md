---
title: 빠른 시작 - Teams 모임 참가
author: juramir
ms.author: juramir
ms.date: 10/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: fd559b8f393f7dca88bfb64fa24e9ba17e6b3832
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253655"
---
이 빠른 시작에서는 C#용 Azure Communication Services 채팅 SDK를 사용하여 Teams 모임에서 채팅하는 방법을 알아봅니다.

## <a name="sample-code"></a>예제 코드
[GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ChatTeamsInteropQuickStart)에서 이 빠른 시작에 대한 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항 

*  [Teams 배포](/deployoffice/teams-install). 
* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).  
* 유니버설 Windows 플랫폼 개발 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.  
* 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md). 
* Teams 모임 링크.

## <a name="joining-the-meeting-chat"></a>모임 채팅 참가 

Communication Services 사용자는 호출 SDK를 사용하여 익명 사용자로 Teams 모임에 참가할 수 있습니다. 모임에 참가하면 모임 채팅에도 참가자로 추가됩니다. 그러면 모임의 다른 사용자와 메시지를 보내고 받을 수 있습니다. 사용자는 모임에 참가하기 전에 전송된 채팅 메시지에 액세스할 수 없으며 모임이 끝난 후에는 메시지를 보내거나 받을 수 없습니다. 모임에 참가하고 채팅을 시작하려면 다음 단계를 수행하면 됩니다.

## <a name="run-the-code"></a>코드 실행
Visual Studio에서 코드를 빌드하고 실행할 수 있습니다. 지원되는 솔루션 플랫폼은 `x64`, `x86` 및 `ARM64`입니다. 

1. PowerShell, Windows 터미널, 명령 프롬프트 또는 그에 상응하는 인스턴스를 열고 샘플을 복제할 디렉터리로 이동합니다.
2. `git clone https://github.com/Azure-Samples/Communication-Services-dotnet-quickstarts.git`
3. Visual Studio에서 ChatTeamsInteropQuickStart/ChatTeamsInteropQuickStart.csproj 프로젝트를 엽니다.
4. 다음 NuGet 패키지 버전(또는 그 이상)을 설치합니다.
``` csharp
Install-Package Azure.Communication.Calling -Version 1.0.0-beta.29
Install-Package Azure.Communication.Chat -Version 1.1.0
Install-Package Azure.Communication.Common -Version 1.0.1
Install-Package Azure.Communication.Identity -Version 1.0.1

```

5. 사전 요구 사항에서 확보한 Communication Services 리소스를 사용하여 **ChatTeamsInteropQuickStart/MainPage.xaml.cs** 파일에 connectionstring을 추가합니다. 

``` csharp
//ACS resource connection string i.e = "endpoint=https://your-resource.communication.azure.net/;accesskey=your-access-key";
private const string connectionString_ = "";
```

> [!IMPORTANT]
> * 코드를 실행하기 <b>전에</b> Visual Studio의 '솔루션 플랫폼' 드롭다운 목록에서 적절한 플랫폼을 선택합니다. 예: `x64`
> * Windows 10에서 '개발자 모드'를 사용하는지 확인합니다[(개발자 설정).](/windows/apps/get-started/enable-your-device-for-development)
>  
>  *올바르게 구성되지 않으면 다음 단계가 작동하지 않습니다.*


6. F5 키를 눌러 디버깅 모드에서 프로젝트를 시작합니다.
7. 'Teams 모임 링크' 상자에 유효한 팀 회의 링크를 붙여넣습니다(다음 섹션 참조).
8. 'Teams 모임 참가'를 눌러 채팅을 시작합니다.

> [!IMPORTANT]
> 호출 SDK가 [Windows 앱을 호출하는 Communication Services 참조](../../voice-video-calling/getting-started-with-calling.md) 팀 모임과 연결되면 채팅 작업을 처리하는 주요 기능은 StartPollingForChatMessages와 SendMessageButton_Click입니다. 두 코드 조각 모두 ChatTeamsInteropQuickStart\MainPage.xaml.cs에 있습니다. 

```csharp
        /// <summary>
        /// Backgroung task that keeps polling for chat messages while the call connection is stablished
        /// </summary>
        private async Task StartPollingForChatMessages()
        {
            CommunicationTokenCredential communicationTokenCredential = new(user_token_);
            chatClient_ = new ChatClient(EndPointFromConnectionString(), communicationTokenCredential);
            await Task.Run(async () =>
            {
                keepPolling_ = true;

                ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
                int previousTextMessages = 0;
                while (keepPolling_)
                {
                    try
                    {
                        CommunicationUserIdentifier currentUser = new(user_Id_);
                        AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
                        SortedDictionary<long, string> messageList = new();
                        int textMessages = 0;
                        string userPrefix;
                        await foreach (ChatMessage message in allMessages)
                        {
                            if (message.Type == ChatMessageType.Html || message.Type == ChatMessageType.Text)
                            {
                                textMessages++;
                                userPrefix = message.Sender.Equals(currentUser) ? "[you]:" : "";
                                messageList.Add(long.Parse(message.SequenceId), $"{userPrefix}{StripHtml(message.Content.Message)}");
                            }
                        }

                        //Update UI just when there are new messages
                        if (textMessages > previousTextMessages)
                        {
                            previousTextMessages = textMessages;
                            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                            {
                                TxtChat.Text = string.Join(Environment.NewLine, messageList.Values.ToList());
                            });

                        }
                        if (!keepPolling_)
                        {
                            return;
                        }

                        await SetInCallState(true);
                        Thread.Sleep(3000);
                    }
                    catch (Exception e)
                    {
                        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                        {
                            _ = new MessageDialog($"An error ocurred while fetching messages in PollingChatMessagesAsync(). The application will shutdown. Details : {e.Message}").ShowAsync();
                            throw e;
                        });
                        await SetInCallState(false);
                    }
                }
            });
        }
        private async void SendMessageButton_Click(object sender, RoutedEventArgs e)
        {
            SendMessageButton.IsEnabled = false;
            ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
            _ = await chatThreadClient.SendMessageAsync(TxtMessage.Text);
            
            TxtMessage.Text = "";
            SendMessageButton.IsEnabled = true;
        }
```



## <a name="get-a-teams-meeting-link"></a>Teams 모임 링크 가져오기

Teams 모임 링크는 [그래프 설명서](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)에 자세히 설명된 Graph API를 사용하여 검색할 수 있습니다. 이 링크는 [`joinWebUrl` 속성](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)에서 액세스할 수 있는 `onlineMeeting` 리소스의 일부로 반환됩니다. 

또한 Teams 모임 초대 자체의 **모임 참가** URL에서 필요한 모임 링크를 가져올 수 있습니다.
Teams 모임 링크는 `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`와 같습니다. 

:::image type="content" source="../join-teams-meeting-chat-quickstart-windows.png" alt-text="완료된 csharp 애플리케이션 스크린샷":::

> [!NOTE] 
> 현재는 Teams와의 상호 운용성 시나리오에 대해서만 메시지 전송, 수신, 편집 및 타이핑 알림 보내기가 지원됩니다. 읽음 확인 및 Communication Services 사용자와 같은 다른 기능에서는 아직 Teams 모임에서 다른 사용자를 추가하거나 제거할 수 없습니다.
