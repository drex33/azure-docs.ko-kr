---
title: Azure SignalR Service 사용 방법을 배우기 위한 빠른 시작
description: Azure SignalR Service를 사용하여 ASP.NET Core MVC 앱으로 대화방을 만들기 위한 빠른 시작입니다.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mode-other
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 550ddd8eb6be505a00b08acea81d7e8ef76f930f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133044891"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>빠른 시작: SignalR Service를 사용하여 대화방 만들기

Azure SignalR Service는 개발자가 실시간 기능으로 손쉽게 웹 애플리케이션을 빌드할 수 있게 하는 Azure 서비스입니다. 이 서비스는 원래 [SignalR for ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)을 기반으로 하지만 이제는 이후 버전을 지원합니다.

이 문서에서는 Azure SignalR Service를 시작하는 방법을 보여줍니다. 이 빠른 시작에서는 ASP.NET Core MVC 웹앱을 사용하여 채팅 애플리케이션을 만듭니다. 이 앱은 Azure SignalR Service 리소스와 연결하여 실시간 콘텐츠 업데이트를 사용하도록 설정합니다. 웹 애플리케이션을 로컬로 호스팅하고 여러 브라우저 클라이언트에 연결합니다. 각 클라이언트는 다른 모든 클라이언트에 콘텐츠 업데이트 푸시할 수 있습니다. 

이 빠른 시작의 단계를 완료하려면 아무 코드 편집기나 사용할 수 있습니다. 한 가지 옵션은 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 [Visual Studio Code](https://code.visualstudio.com/)입니다.

이 자습서에 대한 코드는 [AzureSignalR-samples GitHub 리포지토리](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom)에서 다운로드할 수 있습니다. 또한 [SignalR Service 만들기 스크립트](scripts/signalr-cli-create-service.md)를 수행하여 이 빠른 시작에 사용되는 Azure 리소스를 만들 수도 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>사전 요구 사항

* [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.
* [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub 리포지토리를 다운로드하거나 복제합니다. 

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR 리소스 만들기

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

이 섹션에서는 [.NET Core CLI(명령줄 인터페이스)](/dotnet/core/tools/)를 사용하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다. Visual Studio 대신 .NET Core CLI를 사용하면 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있다는 이점이 있습니다. 

1. 프로젝트 폴더를 만듭니다. 이 빠른 시작에서는 *E:\Testing\chattest* 폴더를 사용합니다.

2. 새 폴더에서 다음 명령을 실행하여 프로젝트를 만듭니다.

    ```dotnetcli
    dotnet new mvc
    ```

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>프로젝트에 암호 관리자 추가

이 섹션에서는 [비밀 관리자 도구](/aspnet/core/security/app-secrets)를 프로젝트에 추가합니다. 비밀 관리자 도구는 개발 작업용 중요 데이터를 프로젝트 트리 외부에 저장합니다. 이 방법을 사용하면 소스 코드에서 앱 비밀을 실수로 공유하지 못하도록 방지할 수 있습니다.

1. *.csproj* 파일을 엽니다. *Microsoft.Extensions.SecretManager.Tools* 를 포함할 `DotNetCliToolReference` 요소를 추가합니다. 또한 *chattest.csproj* 에 대한 다음 코드와 같이 `UserSecretsId` 요소를 추가하고 파일을 저장합니다.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>웹앱에 Azure SignalR 추가

1. 다음 명령을 실행하여 `Microsoft.Azure.SignalR` NuGet 패키지에 대한 참조를 추가합니다.

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. 다음 명령을 실행하여 프로젝트에 대한 패키지를 복원합니다.

    ```dotnetcli
    dotnet restore
    ```

3. *Azure:SignalR:ConnectionString* 이라는 암호를 암호 관리자에 추가합니다. 

    이 암호는 SignalR Service 리소스에 액세스하기 위한 연결 문자열을 포함합니다. *Azure:SignalR:ConnectionString* 은 SignalR에서 연결을 설정하기 위해 찾는 기본 구성 키입니다. 다음 명령의 값을 SignalR Service 리소스에 대한 연결 문자열로 바꿉니다.

    이 명령은 *.csproj* 파일과 동일한 디렉터리에서 실행해야 합니다.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    비밀 관리자는 로컬로 호스팅되는 동안 웹앱을 테스트하는 데만 사용됩니다. 자습서의 뒷부분에서는 채팅 웹앱을 Azure에 배포합니다. 웹앱이 Azure에 배포되면 비밀 관리자를 통해 연결 문자열을 저장하는 대신 애플리케이션 설정을 사용합니다.

    이 비밀은 구성 API를 사용하여 액세스됩니다. 콜론(:)은 지원되는 모든 플랫폼에서 구성 API를 통해 구성 이름에서 작동합니다. [환경별 구성](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider)을 참조하세요.


4. *Startup.cs* 를 열고, `AddSignalR()` 및 `AddAzureSignalR()` 메서드를 호출하여 Azure SignalR Service를 사용하도록 `ConfigureServices` 메서드를 업데이트합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    매개 변수를 `AddAzureSignalR()`에 전달하지 않으면 이 코드에서 SignalR Service 리소스 연결 문자열에 대한 기본 구성 키를 사용합니다. 기본 구성 키는 *Azure:SignalR:ConnectionString* 입니다

5. *Startup.cs* 에서 `Configure` 메서드를 다음 코드로 바꿔서 업데이트합니다.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>허브 클래스 추가

SignalR에서 허브는 클라이언트에서 호출할 수 있는 메서드 세트를 공개하는 핵심 구성 요소입니다. 이 섹션에서는 다음 두 가지 방법으로 허브 클래스를 정의합니다.

* `Broadcast`: 이 메서드는 모든 클라이언트에 메시지를 브로드캐스트합니다.
* `Echo`: 이 메서드는 호출자에게 메시지를 보냅니다.

두 메서드는 모두 ASP.NET Core SignalR SDK에서 제공하는 `Clients` 인터페이스를 사용합니다. 이 인터페이스를 통해 연결된 모든 클라이언트에 액세스할 수 있으므로 콘텐츠를 클라이언트로 푸시할 수 있습니다.

1. 프로젝트 디렉터리에서 *Hub* 라는 새 폴더를 추가합니다. *ChatHub.cs* 라는 새 허브 코드 파일을 새 폴더에 추가합니다.

2. 다음 코드를 *ChatHub.cs* 에 추가하여 허브 클래스를 정의하고 파일을 저장합니다.

    *SignalR.Mvc* 와는 다른 프로젝트 이름을 사용한 경우 이 클래스에 대한 네임스페이스를 업데이트합니다.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>웹앱용 클라이언트 인터페이스 추가

이 대화방 앱용 클라이언트 사용자 인터페이스는 *wwwroot* 디렉터리의 *index.html* 파일에 HTML 및 JavaScript로 구성됩니다.

[샘플 리포지토리](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot)의 *wwwroot* 폴더에서 *css/site.css* 파일을 복사합니다. 프로젝트의 *css/site.css* 를 복사한 것으로 바꿉니다.

*index.html* 의 기본 코드는 다음과 같습니다.

*index.html* 이라는 *wwwroot* 디렉터리에 새 파일을 만들고, 다음 HTML을 새로 만든 파일에 복사하여 붙여넣습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
                document.getElementById('message').addEventListener('keypress', function (event) {
                    if (event.keyCode === 13) {
                        event.preventDefault();
                        document.getElementById('sendmessage').click();
                        return false;
                    }
                });
                document.getElementById('echo').addEventListener('click', function (event) {
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

*index.html* 의 코드는 `HubConnectionBuilder.build()`를 호출하여 Azure SignalR 리소스에 대한 HTTP 연결을 수행합니다.

연결이 성공적이면 해당 연결이 `bindConnectionMessage`로 전달되고, 들어오는 콘텐츠의 클라이언트 푸시를 위한 이벤트 처리기가 추가됩니다. 

`HubConnection.start()`는 허브와의 통신을 시작합니다. 그런 다음, `onConnected()`에서 단추 이벤트 처리기를 추가합니다. 이러한 처리기는 연결을 사용하여 이 클라이언트가 연결된 모든 클라이언트에 콘텐츠 업데이트를 푸시할 수 있도록 합니다.

## <a name="add-a-development-runtime-profile"></a>개발 런타임 프로필 추가

이 섹션에서는 ASP.NET Core용 개발 런타임 환경을 추가합니다. 자세한 내용은 [ASP.NET Core에서 여러 환경 사용](/aspnet/core/fundamentals/environments)을 참조하세요.

1. *Properties* 라는 폴더를 프로젝트에 만듭니다.

2. 다음 내용이 포함된 *launchSettings.json* 이라는 새 파일을 폴더에 추가하고 파일을 저장합니다.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

    ```dotnetcli
    dotnet build
    ```

1. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

    ```dotnetcli
    dotnet run
    ```

    앱은 개발 런타임 프로필에 구성된 대로 5000 포트에서 로컬로 호스팅됩니다.

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. 두 개의 브라우저 창을 엽니다. 각 브라우저에서 `http://localhost:5000`으로 이동합니다. 이름을 입력하라는 메시지가 표시됩니다. 두 클라이언트에 대한 클라이언트 이름을 입력하고, **보내기** 단추를 사용하여 두 클라이언트 간의 메시지 콘텐츠 푸시를 테스트합니다.

    ![Azure SignalR 그룹 채팅 예](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 계속 진행하려면 이 빠른 시작에서 만든 리소스를 유지하여 다시 사용할 수 있습니다.

빠른 시작 샘플 애플리케이션의 사용이 완료되면 요금이 청구되지 않도록 이 빠른 시작에서 만든 Azure 리소스를 삭제할 수 있습니다. 

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 되돌릴 수 없으며 해당 그룹의 모든 리소스가 포함됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹에 이 샘플을 호스팅하는 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.

[Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.

**이름으로 필터링** 텍스트 상자에서 리소스 그룹의 이름을 입력합니다. 이 빠른 시작의 지침에서는 *SignalRTestResources* 라는 리소스 그룹을 사용합니다. 결과 목록의 리소스 그룹에서 줄임표( **...** ) > **리소스 그룹 삭제** 를 차례로 선택합니다.

![리소스 그룹을 삭제하기 위한 선택 항목](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

리소스 그룹 삭제를 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인하고 **삭제** 를 선택합니다.

잠시 후, 리소스 그룹 및 모든 해당 리소스가 삭제됩니다.

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 Azure SignalR Service 리소스를 만들었습니다. 그런 다음, ASP.NET Core 웹앱에서 이를 사용하여 콘텐츠 업데이트를 연결된 여러 클라이언트에 실시간으로 푸시했습니다. Azure SignalR Service를 사용하는 방법을 자세히 알아보려면 인증을 시연하는 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure SignalR Service 인증](./signalr-concept-authenticate-oauth.md)
