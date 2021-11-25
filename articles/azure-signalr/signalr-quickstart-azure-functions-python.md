---
title: Azure SignalR Service Serverless 빠른 시작 - Python
description: Python을 사용하여 Azure SignalR Service와 Azure Functions로 GitHub 별모양 개수를 표시하는 앱을 만들기 위한 빠른 시작입니다.
author: anthonychu
ms.author: antchu
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom: devx-track-python, mode-other
ms.openlocfilehash: 7fed7990c76181899aaa2aff84d10a815127dc02
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133054371"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-python"></a>빠른 시작: Python을 사용하여 SignalR Service와 Azure Functions로 GitHub 별모양 개수를 표시하는 앱 만들기

Azure SignalR Service를 사용하면 애플리케이션에 실시간 기능을 쉽게 추가할 수 있습니다. Azure Functions는 인프라를 관리하지 않고 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 빠른 시작에서는 Python을 사용하여 SignalR Service와 Azure Functions로 서버리스 애플리케이션을 빌드하고 클라이언트에 메시지를 브로드캐스트하는 방법을 알아봅니다.

> [!NOTE]
> 문서에 언급된 모든 코드는 [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/python)에서 얻을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작은 macOS, Windows 또는 Linux에서 실행할 수 있습니다.

[Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기가 설치되어 있는지 확인합니다.

Python Azure 함수 앱을 로컬로 실행하려면 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)(버전 2.7.1505 이상)를 설치하세요.

Azure Functions에는 [Python 3.6 이상](https://www.python.org/downloads/)이 필요합니다. ([지원되는 Python 버전](../azure-functions/functions-reference-python.md#python-version) 참조)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 <https://portal.azure.com/>에서 Azure Portal에 로그인합니다.

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qspython).


## <a name="setup-and-run-the-azure-function-locally"></a>Azure Function을 로컬로 설정 및 실행

1. Azure Function Core Tools가 설치되어 있는지 확인합니다. 그리고 빈 디렉터리를 만들고 명령줄을 사용하여 디렉터리로 이동합니다.

    ```bash
    # Initialize a function project
    func init --worker-runtime python
    ```

2. 프로젝트를 초기화한 후에는 함수를 만들어야 합니다. 이 샘플에서는 3개의 함수를 만들어야 합니다.

    1. 다음 명령을 실행하여 클라이언트용 웹 페이지를 호스트할 `index` 함수를 만듭니다.

        ```bash
        func new -n index -t HttpTrigger
        ```
        `index/function.json`을 열고 다음 json 코드를 복사합니다.

        ```json
        {
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "name": "req",
              "methods": [
                "get",
                "post"
              ]
            },
            {
              "type": "http",
              "direction": "out",
              "name": "res"
            }
          ]
        }
        ```

        `index/__init__.py`를 열고 다음 코드를 복사합니다.

        ```javascript
        import os
    
        import azure.functions as func
        
        
        def main(req: func.HttpRequest) -> func.HttpResponse:
            f = open(os.path.dirname(os.path.realpath(__file__)) + '/../content/index.html')
            return func.HttpResponse(f.read(), mimetype='text/html')
        ```
    
    2. 클라이언트가 액세스 토큰을 가져오도록 `negotiate` 함수를 만듭니다.
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        `negotiate/function.json`을 열고 다음 json 코드를 복사합니다.
    
        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "name": "req",
              "methods": [
                "post"
              ]
            },
            {
              "type": "http",
              "direction": "out",
              "name": "$return"
            },
            {
              "type": "signalRConnectionInfo",
              "name": "connectionInfo",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "in"
            }
          ]
        }
        ```

        `negotiate/__init__.py`를 열고 다음 코드를 복사합니다.

        ```python
        import azure.functions as func
    
        
        def main(req: func.HttpRequest, connectionInfo) -> func.HttpResponse:
            return func.HttpResponse(connectionInfo)
        ```
    
    3. 모든 클라이언트에 메시지를 브로드캐스트하는 `broadcast` 함수를 만듭니다. 샘플에서는 시간 트리거를 사용하여 주기적으로 메시지를 브로드캐스트합니다.
    
        ```bash
        func new -n broadcast -t TimerTrigger
        # install requests
        pip install requests
        ```

        `broadcast/function.json`을 열고 다음 코드를 복사합니다.

        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "name": "myTimer",
              "type": "timerTrigger",
              "direction": "in",
              "schedule": "*/5 * * * * *"
            },
            {
              "type": "signalR",
              "name": "signalRMessages",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "out"
            }
          ]
        }
        ```
    
        `broadcast/__init__.py`를 열고 다음 코드를 복사합니다.
    
        ```python
        import requests
        import json
        
        import azure.functions as func
        
        
        def main(myTimer: func.TimerRequest, signalRMessages: func.Out[str]) -> None:
            headers = {'User-Agent': 'serverless'}
            res = requests.get('https://api.github.com/repos/azure/azure-signalr', headers=headers)
            jres = res.json()
        
            signalRMessages.set(json.dumps({
                'target': 'newMessage',
                'arguments': [ 'Current star count of https://github.com/Azure/azure-signalr is: ' + str(jres['stargazers_count']) ]
            }))
        ```

3. 이 샘플의 클라이언트 인터페이스는 웹 페이지입니다. `index` 함수에서 `content/index.html`의 HTML 콘텐츠를 읽은 것으로 간주하여 프로젝트 루트 폴더 아래의 `content` 디렉터리에 새 파일 `index.html`을 만듭니다. 그리고 다음 내용을 복사합니다.

    ```html
    <html>
    
    <body>
      <h1>Azure SignalR Serverless Sample</h1>
      <div id="messages"></div>
      <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.7/signalr.min.js"></script>
      <script>
        let messages = document.querySelector('#messages');
        const apiBaseUrl = window.location.origin;
        const connection = new signalR.HubConnectionBuilder()
            .withUrl(apiBaseUrl + '/api')
            .configureLogging(signalR.LogLevel.Information)
            .build();
          connection.on('newMessage', (message) => {
            document.getElementById("messages").innerHTML = message;
          });
    
          connection.start()
            .catch(console.error);
      </script>
    </body>
    
    </html>
    ```
    
4. 이제 거의 완료되었습니다. 마지막 단계는 Azure Function 설정에 SignalR Service의 연결 문자열을 설정하는 것입니다.

    1. Azure Portal이 열리는 브라우저에서, 포털의 맨 위에 있는 검색 상자에서 해당 이름을 검색하여 이전에 배포한 SignalR Service 인스턴스를 성공적으로 만들었는지 확인합니다. 인스턴스를 선택하여 엽니다.

        ![SignalR Service 인스턴스를 검색합니다.](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    2. SignalR Service 인스턴스의 연결 문자열을 보려면 **키** 를 선택합니다.
    
        ![기본 연결 문자열을 강조 표시하는 스크린샷.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. 기본 연결 문자열을 복사합니다. 그런 다음 아래의 명령을 실행합니다.
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
5. 로컬에서 Azure Function 실행:

    ```bash
    func start
    ```

    로컬로 Azure Function을 실행한 후 브라우저에서 `http://localhost:7071/api/index`를 방문하면 현재 별 개수를 볼 수 있습니다. 또한 GitHub에서 별을 표시하거나 표시 해제하면 몇 초마다 별 개수가 바뀝니다.

    > [!NOTE]
    > SignalR 바인딩에는 Azure Storage가 필요하지만 함수가 로컬에서 실행 중일 때 로컬 스토리지 에뮬레이터를 사용할 수 있습니다.
    > `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`과 같은 오류가 발생한 경우 [Storage Emulator](../storage/common/storage-use-emulator.md)를 다운로드하여 사용하도록 설정해야 합니다.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 로컬에서 실시간 서버리스 애플리케이션을 빌드하고 실행했습니다. Azure Functions에 대해 SignalR Service 바인딩을 사용하는 방법을 자세히 알아봅니다.
그런 다음, SignalR Service를 사용하여 클라이언트와 Azure Function 간 양방향 통신을 수행하는 방법을 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Functions의 SignalR Service 바인딩](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [서버리스의 양방향 통신](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [VS Code로 Azure Functions 배포](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
