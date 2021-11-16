---
title: '빠른 시작: Azure Web PubSub 인스턴스용 서비스 SDK를 사용하여 메시지 게시'
description: 서비스 SDK 사용 방법을 보여주는 빠른 시작
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/01/2021
ms.openlocfilehash: 72805453fb9929c4f598cca8d930eac3b447f6c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287907"
---
# <a name="quickstart-publish-messages-using-the-service-sdk-for-the-azure-web-pubsub-instance"></a>빠른 시작: Azure Web PubSub 인스턴스용 서비스 SDK를 사용하여 메시지 게시

이 빠른 시작에서는 서비스 SDK를 사용하여 클라이언트에 메시지를 게시하는 방법을 보여줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.22.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Web PubSub 인스턴스 만들기

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="get-the-connectionstring-for-future-use"></a>나중에 사용할 수 있도록 ConnectionString 가져오기

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

가져온 **ConnectionString** 을 복사합니다. 나중에 서비스 SDK를 사용할 때 `<connection_string>` 값으로 사용됩니다.

## <a name="connect-to-the-instance"></a>인스턴스에 연결

[!INCLUDE [az webpubsub client](includes/cli-awps-client-connect.md)]

## <a name="publish-messages-using-service-sdk"></a>서비스 SDK를 사용하여 메시지 게시

이번에는 Azure Web PubSub SDK를 사용하여 연결된 클라이언트에 메시지를 게시해 보겠습니다.

### <a name="prerequisites"></a>사전 요구 사항

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 이상](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 이상](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).

---

### <a name="set-up-the-project-to-publish-messages"></a>메시지를 게시하도록 프로젝트 설정

# <a name="c"></a>[C#](#tab/csharp)

1. 새 프로젝트 `publisher`를 추가하고 SDK 패키지 `package Azure.Messaging.WebPubSub`를 추가합니다.

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0-beta.3
    ```

2. `WebPubSubServiceClient` 클래스를 사용하여 클라이언트에 메시지를 보내도록 `Program.cs` 파일을 업데이트합니다.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;

    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];

                var service = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await service.SendToAllAsync(message);
            }
        }
    }
    ```

    `service.SendToAllAsync()`를 호출하면 허브에 있는 모든 연결된 클라이언트에 메시지가 전송됩니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. 이전 CLI 클라이언트가 메시지를 수신한 것을 볼 수 있습니다.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 먼저 이 프로젝트에 대한 새 폴더 `publisher`를 만들고 필요한 종속성을 설치하겠습니다.

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub@1.0.0-alpha.20211102.4
    ```

2. 이번에는 Azure Web PubSub SDK를 사용하여 서비스에 메시지를 게시해 보겠습니다. 아래 코드를 사용하여 `publish.js` 파일을 만듭니다.

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    if (process.argv.length !== 3) {
      console.log('Usage: node publish <message>');
      return 1;
    }
    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    `sendToAll()`를 호출하면 허브에 있는 모든 연결된 클라이언트에 메시지가 전송됩니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. 이전 CLI 클라이언트가 메시지를 수신한 것을 볼 수 있습니다.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="python"></a>[Python](#tab/python)

1. 먼저 이 프로젝트에 대한 새 폴더 `publisher`를 만들고 필요한 종속성을 설치하겠습니다.
    ```bash
    mkdir publisher
    cd publisher
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice

    ```
2. 이번에는 Azure Web PubSub SDK를 사용하여 서비스에 메시지를 게시해 보겠습니다. 아래 코드를 사용하여 `publish.py` 파일을 만듭니다.

    ```python
    import sys
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 4:
            print('Usage: python publish.py <connection-string> <hub-name> <message>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
        message = sys.argv[3]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        res = service.send_to_all(message, content_type='text/plain')
        print(res)
    ```

    `service.send_to_all()` 메서드는 메시지를 허브에서 연결된 모든 클라이언트에 보냅니다.

3. 아래 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. 이전 CLI 클라이언트가 메시지를 수신한 것을 볼 수 있습니다.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

# <a name="java"></a>[Java](#tab/java)

1. 먼저 Maven을 사용하여 새 콘솔 앱 `webpubsub-quickstart-publisher`를 만들고 *webpubsub-quickstart-publisher* 폴더로 전환합니다.
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. 다음과 같이 `pom.xml`의 `dependencies` 노드에 Azure Web PubSub SDK 종속성을 추가하겠습니다.

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

3. 이번에는 Azure Web PubSub SDK를 사용하여 서비스에 메시지를 게시해 보겠습니다. */src/main/java/com/webpubsub/quickstart* 디렉터리로 이동하고, 편집기에서 *App.java* 파일을 열고, 코드를 아래 코드로 바꿉니다.

    ```java
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;

    /**
    * Quickstart - Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }

            WebPubSubServiceClient service = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            service.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    `service.sendToAll()`를 호출하면 허브에 있는 모든 연결된 클라이언트에 메시지가 전송됩니다.

4. *pom.xml* 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

    ```console
    mvn compile
    ```
5. 그런 다음, 패키지를 빌드합니다.

    ```console
    mvn package
    ```
6. 다음 `mvn` 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꿉니다.

    ```console
    mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

7. 이전 CLI 클라이언트가 메시지를 수신한 것을 볼 수 있습니다.
   
    ```json
    {"type":"message","from":"server","dataType":"text","data":"Hello World"}
    ```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Web PubSub 서비스에 연결하는 방법과 연결된 클라이언트에 메시지를 게시하는 방법에 대한 기본 개념을 알아보았습니다.

실제 애플리케이션에서는 다양한 언어의 SDK를 사용하여 고유한 애플리케이션을 빌드할 수 있습니다. 또한 서버리스 애플리케이션을 쉽게 빌드할 수 있도록 함수 확장도 제공합니다.

[!INCLUDE [next step](includes/include-next-step.md)]
