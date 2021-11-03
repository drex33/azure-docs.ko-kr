---
title: Java를 사용하여 Azure Functions와 SignalR Service로 대화방 만들기
description: Java를 사용하여 Azure SignalR Service와 Azure Functions로 GitHub 별모양 개수를 표시하는 앱을 만들기 위한 빠른 시작입니다.
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom: devx-track-java, mode-api
ms.openlocfilehash: 5cea13b3f6410df1d5a63efe4acdd080b21f2f5c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069581"
---
# <a name="quickstart-use-java-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>빠른 시작: Java를 사용하여 SignalR Service와 Azure Functions로 GitHub 별모양 개수를 표시하는 앱 만들기

Azure SignalR Service를 사용하면 애플리케이션에 실시간 기능을 쉽게 추가할 수 있으며 Azure Functions는 인프라를 관리하지 않고도 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 빠른 시작에서는 Java를 사용하여 SignalR Service와 Azure Functions로 서버리스 애플리케이션을 빌드하고 클라이언트에 메시지를 브로드캐스트하는 방법을 알아봅니다.

> [!NOTE]
> 문서에 언급된 모든 코드는 [GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/java)에서 얻을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기
- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Azure 함수 앱을 로컬로 실행하는 데 사용됩니다.

   > [!NOTE]
   > Java의 필수 SignalR Service 바인딩은 Azure Function Core Tools 버전 2.4.419(호스트 버전 2.0.12332) 이상에서만 지원됩니다.

   > [!NOTE]
   > 확장을 설치하려면 Azure Functions Core Tools에 [.NET Core SDK](https://dotnet.microsoft.com/download)가 설치되어 있어야 합니다. 그러나 JavaScript Azure 함수 앱을 빌드하는 데는 .NET에 대한 지식이 필요하지 않습니다.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), 버전 11
- [Apache Maven](https://maven.apache.org), 버전 3.0 이상

> [!NOTE]
> 이 빠른 시작은 macOS, Windows 또는 Linux에서 실행할 수 있습니다.

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 <https://portal.azure.com/>에서 Azure Portal에 로그인합니다.

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsjava).


## <a name="configure-and-run-the-azure-function-app"></a>Azure 함수 앱을 구성하고 실행합니다.

1. Azure Function Core Tools, java(샘플의 버전 11) 및 maven이 설치되어 있는지 확인합니다.
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=11
    ```

    Maven은 프로젝트 생성을 완료하는 데 필요한 값을 요청합니다. 다음 값을 제공할 수 있습니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | **groupId** | `com.signalr` | Java에 대한 [패키지 명명 규칙](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)에 따라 모든 프로젝트에서 프로젝트를 고유하게 식별하는 값입니다. |
    | **artifactId** | `java` | 버전 번호가 없는 jar의 이름인 값입니다. |
    | **version** | `1.0-SNAPSHOT` | 기본값을 선택합니다. |
    | **package** | `com.signalr` | 생성된 함수 코드에 대한 Java 패키지인 값입니다. 기본값을 사용하세요. |   

2. 프로젝트를 초기화한 후. `src/main/java/com/signalr` 폴더로 이동하여 다음 코드를 `Function.java`에 복사합니다.

    ```java
    package com.signalr;
  
    import com.google.gson.Gson;
    import com.microsoft.azure.functions.ExecutionContext;
    import com.microsoft.azure.functions.HttpMethod;
    import com.microsoft.azure.functions.HttpRequestMessage;
    import com.microsoft.azure.functions.HttpResponseMessage;
    import com.microsoft.azure.functions.HttpStatus;
    import com.microsoft.azure.functions.annotation.AuthorizationLevel;
    import com.microsoft.azure.functions.annotation.FunctionName;
    import com.microsoft.azure.functions.annotation.HttpTrigger;
    import com.microsoft.azure.functions.annotation.TimerTrigger;
    import com.microsoft.azure.functions.signalr.*;
    import com.microsoft.azure.functions.signalr.annotation.*;
    
    import org.apache.commons.io.IOUtils;
    
    
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.HttpResponse.BodyHandlers;
    import java.nio.charset.StandardCharsets;
    import java.util.Optional;
    
    public class Function {
        @FunctionName("index")
        public HttpResponseMessage run(
                @HttpTrigger(
                    name = "req",
                    methods = {HttpMethod.GET},
                    authLevel = AuthorizationLevel.ANONYMOUS)HttpRequestMessage<Optional<String>> request,
                final ExecutionContext context) throws IOException {
            
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("content/index.html");
            String text = IOUtils.toString(inputStream, StandardCharsets.UTF_8.name());
            return request.createResponseBuilder(HttpStatus.OK).header("Content-Type", "text/html").body(text).build();
        }
  
        @FunctionName("negotiate")
        public SignalRConnectionInfo negotiate(
                @HttpTrigger(
                    name = "req",
                    methods = { HttpMethod.POST },
                    authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
                @SignalRConnectionInfoInput(
                    name = "connectionInfo",
                    hubName = "serverless") SignalRConnectionInfo connectionInfo) {
                    
            return connectionInfo;
        }
    
        @FunctionName("broadcast")
        @SignalROutput(name = "$return", hubName = "serverless")
        public SignalRMessage broadcast(
            @TimerTrigger(name = "timeTrigger", schedule = "*/5 * * * * *") String timerInfo) throws IOException, InterruptedException {
            
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest req = HttpRequest.newBuilder().uri(URI.create("https://api.github.com/repos/azure/azure-signalr")).header("User-Agent", "serverless").build();
            HttpResponse<String> res = client.send(req, BodyHandlers.ofString());
            Gson gson = new Gson();
            GitResult result = gson.fromJson(res.body(), GitResult.class);
            return new SignalRMessage("newMessage", "Current star count of https://github.com/Azure/azure-signalr is:".concat(result.stargazers_count));
        }
    
        class GitResult {
            public String stargazers_count;
        }
    }
    ```

3. 일부 종속성을 추가해야 합니다. 따라서 `pom.xml`을 열고 코드에 사용된 일부 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.functions</groupId>
        <artifactId>azure-functions-java-library-signalr</artifactId>
        <version>1.0.0</version>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.4</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.7</version>
    </dependency>
    ```

4. 이 샘플의 클라이언트 인터페이스는 웹 페이지입니다. `index` 함수에서 `content/index.html`의 HTML 콘텐츠를 읽은 것으로 간주하여 `resources` 디렉터리에 새 파일 `content/index.html`을 만듭니다. 디렉터리 트리는 다음과 같아야 합니다.
    
    ```
    FunctionsProject
     | - src
     | | - main
     | | | - java
     | | | | - com
     | | | | | - signalr 
     | | | | | | - Function.java
     | | | - resources
     | | | | - content
     | | | | | - index.html
     | - pom.xml
     | - host.json
     | - local.settings.json
    ```

    `index.html`을 열고 다음 내용을 복사합니다.

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

5. 이제 거의 완료되었습니다. 마지막 단계는 Azure Function 설정에 SignalR Service의 연결 문자열을 설정하는 것입니다.

    1. Azure Portal이 열리는 브라우저에서, 포털의 맨 위에 있는 검색 상자에서 해당 이름을 검색하여 이전에 배포한 SignalR Service 인스턴스를 성공적으로 만들었는지 확인합니다. 인스턴스를 선택하여 엽니다.

        ![SignalR Service 인스턴스를 검색합니다.](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. SignalR Service 인스턴스의 연결 문자열을 보려면 **키** 를 선택합니다.
    
        ![기본 연결 문자열을 강조 표시하는 스크린샷.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. 기본 연결 문자열을 복사합니다. 그런 다음 아래의 명령을 실행합니다.
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        # Also we need to set AzureWebJobsStorage as Azure Function's requirement
        func settings add AzureWebJobsStorage "UseDevelopmentStorage=true"
        ```
    
6. 로컬에서 Azure Function 실행:

    ```bash
    mvn clean package
    mvn azure-functions:run
    ```

    로컬로 Azure Function을 실행한 후 브라우저에서 `http://localhost:7071/api/index`를 방문하면 현재 별 개수를 볼 수 있습니다. 또한 GitHub에서 별을 표시하거나 표시 해제하면 몇 초마다 별 개수가 바뀝니다.

    > [!NOTE]
    > SignalR 바인딩에는 Azure Storage가 필요하지만 함수가 로컬에서 실행 중일 때 로컬 스토리지 에뮬레이터를 사용할 수 있습니다.
    > `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.`과 같은 오류가 발생한 경우 [Storage Emulator](../storage/common/storage-use-emulator.md)를 다운로드하여 사용하도록 설정해야 합니다.
    
문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsjava).


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

문제가 있나요? [문제 해결 가이드](signalr-howto-troubleshoot-guide.md)를 사용해 보거나 [알려주세요](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 로컬에서 실시간 서버리스 애플리케이션을 빌드하고 실행했습니다. Azure Functions에 대해 SignalR Service 바인딩을 사용하는 방법을 자세히 알아봅니다.
그런 다음, SignalR Service를 사용하여 클라이언트와 Azure Function 간 양방향 통신을 수행하는 방법을 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Functions의 SignalR Service 바인딩](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [서버리스에서 양방향 통신](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Java 및 Maven을 사용하여 첫 번째 함수 만들기](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)
