---
title: 자습서 - Azure Web PubSub 서비스를 사용하여 채팅 앱 만들기
description: Azure Web PubSub 서비스를 사용하여 채팅 앱을 만드는 방법을 안내하는 자습서
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: aa7180756dc5708b2747d7d7fe104138b48b058c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423999"
---
# <a name="tutorial-create-a-chat-app-with-azure-web-pubsub-service"></a>자습서: Azure Web PubSub 서비스를 사용하여 채팅 앱 만들기

[메시지 게시 및 구독 자습서](./tutorial-pub-sub-messages.md)에서는 Azure Web PubSub를 통해 메시지를 게시하고 구독하는 기본 사항을 배웠습니다. 이 자습서에서는 실시간 통신 기능을 제공하는 완전한 웹 애플리케이션을 빌드하는 데 사용할 수 있도록 Azure Web PubSub의 이벤트 시스템에 대해 알아보겠습니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Web PubSub 서비스 인스턴스 만들기
> * Azure Web PubSub에 대한 이벤트 처리기 설정 구성
> * 실시간 채팅 앱 빌드

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이렇게 설정하려면 버전 2.22.0 이상의 Azure CLI가 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-an-azure-web-pubsub-instance"></a>Azure Web PubSub 인스턴스 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>Web PubSub 인스턴스 만들기

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>나중에 사용할 수 있도록 ConnectionString 가져오기

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

가져온 **ConnectionString** 을 복사합니다. 이 자습서의 뒷부분에서 `<connection_string>` 값으로 사용됩니다.

## <a name="set-up-the-project"></a>프로젝트 설정

### <a name="prerequisites"></a>사전 요구 사항

# <a name="c"></a>[C#](#tab/csharp)

* [ASP.NET Core 3.1 이상](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 이상](https://nodejs.org)

# <a name="java"></a>[Java](#tab/java)

- [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="create-the-application"></a>애플리케이션 만들기

Azure Web PubSub에는 서버 역할과 클라이언트 역할이 있습니다. 이 개념은 웹 애플리케이션의 서버 역할 및 클라이언트 역할과 비슷합니다. 서버는 클라이언트를 관리하고, 클라이언트 메시지를 수신 대기하고, 클라이언트 메시지에 응답하는 역할을 담당하는 반면, 클라이언트의 역할은 사용자의 메시지를 서버로 보내고, 서버에서 메시지를 받아서 최종 사용자에게 시각화하는 것입니다.

이 자습서에서는 실시간 채팅 웹 애플리케이션을 빌드합니다. 실제 웹 애플리케이션에서는 클라이언트를 인증하고 애플리케이션 UI에 대한 정적 웹 페이지를 제공하는 것도 서버의 역할에 포함됩니다. 

# <a name="c"></a>[C#](#tab/csharp)

[ASP.NET Core](/aspnet/core)를 사용하여 웹 페이지를 호스트하고 들어오는 요청을 처리할 것입니다.

먼저 빈 ASP.NET Core 앱을 만들겠습니다.

1.  웹앱 만들기

    ```bash
    dotnet new web
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```

2.  그런 다음, `Startup.cs`에서 `app.UseRouting();` 앞에 `app.UseStaticFiles();`를 추가하여 정적 파일을 지원합니다. `app.UseEndpoints` 안에서 기본 `endpoints.MapGet`을 제거합니다.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseStaticFiles();

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
        });
    }
    ```

3.  또한 HTML 파일을 만들고 `wwwroot/index.html`로 저장합니다. 나중에 채팅 앱의 UI에 사용할 것입니다.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

브라우저에서 `dotnet run --urls http://localhost:8080`를 실행하고 http://localhost:8080/index.html 에 액세스하여 서버를 테스트할 수 있습니다.

[메시지 게시 및 구독 자습서](./tutorial-pub-sub-messages.md)에서는 구독자가 Web PubSub SDK의 API를 사용하여 연결 문자열에서 액세스 토큰을 생성하고, 이 토큰을 사용하여 서비스에 연결했습니다. 실제 애플리케이션에서는 연결 문자열이 서비스에 대한 모든 작업을 수행할 수 있는 높은 권한을 갖기 때문에 일반적으로 안전하지 않으므로 클라이언트와 공유하면 안 됩니다. 클라이언트가 연결 문자열을 보관할 필요 없이 연결이 필요할 때마다 이 API를 호출하여 액세스 토큰을 요청할 수 있도록 이 액세스 토큰 생성 프로세스를 서버 쪽의 REST API로 변경해 보겠습니다.

1.  Microsoft.Extensions.Azure를 설치합니다.

    ```bash
    dotnet add package Microsoft.Extensions.Azure
    ```
2. `ConfigureServices` 내부에 서비스 클라이언트를 DI하고 `<connection_string>`을 서비스 중 하나로 바꿉니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAzureClients(builder =>
        {
            builder.AddWebPubSubServiceClient("<connection_string>", "chat");
        });
    }
    ```
2.  `/negotiate` 내부의 서버에 `app.UseEndpoints` API를 추가하여 토큰을 생성합니다.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/negotiate", async context =>
        {
            var id = context.Request.Query["id"];
            if (id.Count != 1)
            {
                context.Response.StatusCode = 400;
                await context.Response.WriteAsync("missing user id");
                return;
            }
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            await context.Response.WriteAsync(serviceClient.GenerateClientAccessUri(userId: id).AbsoluteUri);
        });
    });
    ```

    이 토큰 생성 코드는 토큰을 생성할 때 인수(`userId`)를 하나 더 전달하는 것을 제외하고 [메시지 게시 및 구독 자습서](./tutorial-pub-sub-messages.md)에서 사용한 코드와 비슷합니다. 사용자 ID를 사용하여 클라이언트 ID를 식별할 수 있으므로 메시지를 받을 때 어디서 오는 메시지인지 알 수 있습니다.

    `dotnet run --urls http://localhost:8080`을 실행하고 `http://localhost:8080/negotiate?id=<user-id>`에 액세스하여 이 API를 테스트할 수 있으며, 액세스 토큰이 포함된 Azure Web PubSub의 전체 URL이 제공됩니다.

3.  그런 다음, 서버에서 토큰을 가져와서 서비스에 연결하는 다음 스크립트를 포함하도록 `index.html`을 업데이트합니다.
 
    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Chrome을 사용하는 경우 홈페이지를 열고 사용자 이름을 입력하여 테스트할 수 있습니다. F12 키를 눌러 개발자 도구 창을 열고 **콘솔** 테이블로 전환하면 브라우저 콘솔에 `connected`가 인쇄되는 것을 볼 수 있습니다.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

인기 있는 node.js용 웹 프레임워크인 [express.js](https://expressjs.com/)를 사용하여 이 작업을 수행할 것입니다.

먼저 빈 express 앱을 만듭니다.

1.  express.js를 설치합니다.

    ```bash
    npm init -y
    npm install --save express
    ```

2.  그런 다음, express 서버를 만들고 `server.js`로 저장합니다.

    ```javascript
    const express = require('express');

    const app = express();
    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

3.  또한 HTML 파일을 만들고 `public/index.html`로 저장합니다. 나중에 채팅 앱의 UI에 사용할 것입니다.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

브라우저에서 `node server`를 실행하고 http://localhost:8080 에 액세스하여 서버를 테스트할 수 있습니다.


[메시지 게시 및 구독 자습서](./tutorial-pub-sub-messages.md)에서는 구독자가 Web PubSub SDK의 API를 사용하여 연결 문자열에서 액세스 토큰을 생성하고, 이 토큰을 사용하여 서비스에 연결했습니다. 실제 애플리케이션에서는 연결 문자열이 서비스에 대한 모든 작업을 수행할 수 있는 높은 권한을 갖기 때문에 일반적으로 안전하지 않으므로 클라이언트와 공유하면 안 됩니다. 클라이언트가 연결 문자열을 보관할 필요 없이 연결이 필요할 때마다 이 API를 호출하여 액세스 토큰을 요청할 수 있도록 이 액세스 토큰 생성 프로세스를 서버 쪽의 REST API로 변경해 보겠습니다.

1.  Azure Web PubSub SDK를 설치합니다.

    ```bash
    npm install --save @azure/web-pubsub
    ```

2.  서버에 `/negotiate` API를 추가하여 토큰을 생성합니다.

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const app = express();
    const hubName = 'chat';

    let serviceClient = new WebPubSubServiceClient(process.argv[2], hubName);

    app.get('/negotiate', async (req, res) => {
      let id = req.query.id;
      if (!id) {
        res.status(400).send('missing user id');
        return;
      }
      let token = await serviceClient.getAuthenticationToken({ userId: id });
      res.json({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    이 토큰 생성 코드는 토큰을 생성할 때 인수(`userId`)를 하나 더 전달하는 것을 제외하고 [메시지 게시 및 구독 자습서](./tutorial-pub-sub-messages.md)에서 사용한 코드와 비슷합니다. 사용자 ID를 사용하여 클라이언트 ID를 식별할 수 있으므로 메시지를 받을 때 어디서 오는 메시지인지 알 수 있습니다.

    `node server "<connection-string>"`을 실행하고 `http://localhost:8080/negotiate?id=<user-id>`에 액세스하여 이 API를 테스트할 수 있으며, 액세스 토큰이 포함된 Azure Web PubSub의 전체 URL이 제공됩니다.

3.  그런 다음, 서버에서 토큰을 가져와서 서비스에 연결하는 다음 스크립트를 사용하여 `index.html`을 업데이트합니다.
 
    ```html

    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let data = await res.json();
          let ws = new WebSocket(data.url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Chrome을 사용하는 경우 홈페이지를 열고 사용자 이름을 입력하여 테스트할 수 있습니다. F12 키를 눌러 개발자 도구 창을 열고 **콘솔** 테이블로 전환하면 브라우저 콘솔에 `connected`가 인쇄되는 것을 볼 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

[Javalin](https://javalin.io/) 웹 프레임워크를 사용하여 웹 페이지를 호스트하고 들어오는 요청을 처리할 것입니다.

1. 먼저 Maven을 사용하여 새 `webpubsub-tutorial-chat` 앱을 만들고 *webpubsub-tutorial-chat* 폴더로 전환합니다.

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=webpubsub-tutorial-chat --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-tutorial-chat
    ```

2. 다음과 같이 `javalin` 웹 프레임워크 종속성을 `pom.xml`의 `dependencies` 노드에 추가합니다.

    * `javalin`: 간단한 Java용 간단한 웹 프레임워크
    * `slf4j-simple`: Java용 로거

    ```xml
    <!-- https://mvnrepository.com/artifact/io.javalin/javalin -->
    <dependency>
        <groupId>io.javalin</groupId>
        <artifactId>javalin</artifactId>
        <version>3.13.6</version>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.30</version>
    </dependency>
    ```

3. 다음과 같이 편집기에서 */src/main/java/com/webpubsub/tutorial* 디렉터리를 찾아서 *App.java* 파일을 열고, `Javalin.create`를 사용하여 고정 파일을 제공합니다.

    ```java
    package com.webpubsub.tutorial;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
        }
    }
    ```

    설정에 따라 언어 수준을 Java 8로 명시적으로 설정해야 할 수도 있습니다. 이 작업은 pom.xml에서 수행할 수 있습니다. 다음 코드 조각을 추가합니다.
    ```xml
    <build>
        <plugins>
            <plugin>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.0</version>
              <configuration>
                <source>1.8</source>
                <target>1.8</target>
              </configuration>
            </plugin>
        </plugins>
    </build>
    ```

4. HTML 파일을 만들고 */src/main/resources/public/index.html* 에 저장하겠습니다. 나중에 채팅 앱의 UI에 사용됩니다.

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

*pom.xml* 파일이 들어 있는 디렉터리에서 다음 명령을 실행하고, 브라우저에서 http://localhost:8080 에 액세스하여 서버를 테스트할 수 있습니다.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false
```

[메시지 게시 및 구독 자습서](./tutorial-pub-sub-messages.md)에서는 구독자가 Web PubSub SDK의 API를 사용하여 연결 문자열에서 액세스 토큰을 생성하고, 이 토큰을 사용하여 서비스에 연결했습니다. 실제 애플리케이션에서는 연결 문자열이 서비스에 대한 모든 작업을 수행할 수 있는 높은 권한을 갖기 때문에 일반적으로 안전하지 않으므로 클라이언트와 공유하면 안 됩니다. 클라이언트가 연결 문자열을 보관할 필요 없이 연결이 필요할 때마다 이 API를 호출하여 액세스 토큰을 요청할 수 있도록 이 액세스 토큰 생성 프로세스를 서버 쪽의 REST API로 변경해 보겠습니다.

1. 다음과 같이 `pom.xml`의 `dependencies` 노드에 Azure Web PubSub SDK 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

2. 다음과 같이 `/negotiate` API를 `App.java` 파일에 추가하여 토큰을 생성합니다.

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubClientBuilder;
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.models.GetAuthenticationTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubAuthenticationToken;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient client = new WebPubSubClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                String id = ctx.queryParam("id");
                if (id == null) {
                    ctx.status(400);
                    ctx.result("missing user id");
                    return;
                }
                GetAuthenticationTokenOptions option = new GetAuthenticationTokenOptions();
                option.setUserId(id);
                WebPubSubAuthenticationToken token = client.getAuthenticationToken(option);
                ctx.result(token.getUrl());
                return;
            });
        }
    }
    ```

    이 토큰 생성 코드는 토큰을 생성할 때 `setUserId` 메서드를 호출하여 사용자 ID를 설정하는 것을 제외하고 [메시지 게시 및 구독 자습서](./tutorial-pub-sub-messages.md)에서 사용한 코드와 비슷합니다. 사용자 ID를 사용하여 클라이언트 ID를 식별할 수 있으므로 메시지를 받을 때 어디서 오는 메시지인지 알 수 있습니다.

    다음 명령을 실행하고, `<connection_string>`을 [이전 단계](#get-the-connectionstring-for-future-use)에서 가져온 **ConnectionString** 으로 바꾸고, `http://localhost:8080/negotiate?id=<user-id>`에 액세스하여 이 API를 테스트할 수 있으며, 액세스 토큰이 포함된 Azure Web PubSub의 전체 URL이 제공됩니다.

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```

3. 그런 다음, 서버에서 토큰을 가져와서 서비스에 연결하는 다음 스크립트를 사용하여 `index.html`을 업데이트합니다.

    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Chrome을 사용하는 경우 홈페이지를 열고 사용자 이름을 입력하여 테스트할 수 있습니다. F12 키를 눌러 개발자 도구 창을 열고 **콘솔** 테이블로 전환하면 브라우저 콘솔에 `connected`가 인쇄되는 것을 볼 수 있습니다.

---

## <a name="handle-events"></a>이벤트 처리

Azure Web PubSub는 클라이언트 쪽에서 특정 활동이 발생하는 경우(예: 클라이언트가 연결되거나 연결이 끊어지는 경우) 서버가 이러한 이벤트에 대응할 수 있도록 서버에 알림을 보냅니다.

이벤트는 Webhook 형식으로 서버에 전달됩니다. Webhook는 애플리케이션 서버를 통해 제공 및 노출되며 Azure Web PubSub 서비스 쪽에서 등록됩니다. 이 서비스는 이벤트가 발생할 때마다 webhook를 호출합니다.

Azure Web PubSub는 [CloudEvents](./reference-cloud-events.md)에 따라 이벤트 데이터를 설명합니다. 

# <a name="c"></a>[C#](#tab/csharp)
지금은 C#에서 직접 이벤트 처리기를 구현해야 합니다. 구현 단계는 [프로토콜 사양](./reference-cloud-events.md)에 따라 간단하게 진행되며 아래에 설명되어 있습니다.

1. `UseEndpoints` 내에 이벤트 처리기를 추가합니다. 이벤트에 대한 엔드포인트 경로를 지정합니다. 여기서는 `/eventhandler`로 지정하겠습니다. 

2. 먼저 남용 방지 OPTIONS 요청을 처리하고, 헤더에 `WebHook-Request-Origin` 헤더가 포함되어 있는지 확인하고, `WebHook-Allowed-Origin` 헤더를 반환하겠습니다. 간단한 데모 수행을 위해 여기서는 `*`를 반환하여 모든 원본을 허용하겠습니다.
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

3. 그런 다음, 들어오는 요청이 우리가 예상하는 이벤트인지 확인하겠습니다. 이 데모에서는 `connected` 이벤트가 중요하다고 가정하겠습니다. 이 이벤트에는 `ce-type` 헤더가 `azure.webpubsub.sys.connected`로 포함됩니다. 남용 방지 뒤에 논리를 추가합니다.
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

위의 코드에서는 클라이언트가 연결될 때 간단하게 콘솔에 메시지를 출력합니다. 연결된 클라이언트의 ID를 볼 수 있도록 `context.Request.Headers["ce-userId"]`를 사용하고 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Web PubSub SDK를 사용하는 경우 CloudEvents 스키마를 구문 분석하고 처리하는 구현이 이미 있으므로 이러한 세부 정보를 처리할 필요가 없습니다.

클라이언트 연결 이벤트를 처리할 수 있도록 `/eventhandler`에서 REST API를 노출하는(Web PubSub SDK에서 제공하는 express 미들웨어에 의해 수행됨) 다음 코드를 추가합니다.

```bash
npm install --save @azure/web-pubsub-express
```

```javascript
const { WebPubSubEventHandler } = require('@azure/web-pubsub-express');

let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  onConnected: async req => {
    console.log(`${req.context.userId} connected`);
  }
});

app.use(handler.getMiddleware());
```

위의 코드에서는 클라이언트가 연결될 때 간단하게 콘솔에 메시지를 출력합니다. 연결된 클라이언트의 ID를 볼 수 있도록 `req.context.userId`를 사용하고 있습니다.

# <a name="java"></a>[Java](#tab/java)
지금은 Java에서 직접 이벤트 처리기를 구현해야 합니다. 구현 단계는 [프로토콜 사양](./reference-cloud-events.md)에 따라 간단하게 진행되며 아래에 설명되어 있습니다.

1. 이벤트 처리기 경로에 대한 HTTP 처리기를 추가합니다. 여기서는 `/eventhandler`라고 하겠습니다. 

2. 먼저 남용 방지 OPTIONS 요청을 처리하고, 헤더에 `WebHook-Request-Origin` 헤더가 포함되어 있는지 확인하고, `WebHook-Allowed-Origin` 헤더를 반환하겠습니다. 간단한 데모 수행을 위해 여기서는 `*`를 반환하여 모든 원본을 허용하겠습니다.
    ```java
    
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });
    ```

3. 그런 다음, 들어오는 요청이 우리가 예상하는 이벤트인지 확인하겠습니다. 이 데모에서는 `connected` 이벤트가 중요하다고 가정하겠습니다. 이 이벤트에는 `ce-type` 헤더가 `azure.webpubsub.sys.connected`로 포함됩니다. 남용 방지 뒤에 논리를 추가합니다.
    ```java
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        }
        ctx.status(200);
    });

    ```

위의 코드에서는 클라이언트가 연결될 때 간단하게 콘솔에 메시지를 출력합니다. 연결된 클라이언트의 ID를 볼 수 있도록 `ctx.header("ce-userId")`를 사용하고 있습니다.

---

## <a name="set-up-the-event-handler"></a>이벤트 처리기 설정

### <a name="expose-localhost"></a>localhost 노출

그런 다음, 새 이벤트가 있을 때 호출할 위치를 알 수 있도록 서비스에서 Webhook URL을 설정해야 합니다. 그러나 서버가 localhost에서 실행되고 있으므로 인터넷에 액세스할 수 있는 엔드포인트가 없다는 문제가 있습니다. 여기서는 [ngrok](https://ngrok.com/)를 사용하여 localhost를 인터넷에 노출합니다.

1.  먼저 https://ngrok.com/download 에서 ngrok를 다운로드하고, 실행 파일을 로컬 폴더 또는 시스템 bin 폴더에 추출합니다.
2.  ngrok 시작
    
    ```bash
    ngrok http 8080
    ```

ngrok는 인터넷에서 액세스할 수 있는 URL(`https://<domain-name>.ngrok.io`)을 인쇄합니다.

### <a name="set-event-handler"></a>이벤트 처리기 설정

그런 다음, 서비스 이벤트 처리기를 업데이트하고 Webhook URL을 설정합니다.

Azure CLI [az webpubsub hub create](/cli/azure/webpubsub/hub#az_webpubsub_hub_update) 명령을 사용하여 채팅 허브에 대한 이벤트 처리기 설정을 만듭니다.

  > [!Important]
  > &lt;your-unique-resource-name&gt;을 이전 단계에서 만든 Web PubSub 리소스의 이름으로 바꿉니다.
  > &lt;domain-name&gt;을 출력된 ngrok 이름으로 바꿉니다.

```azurecli-interactive
az webpubsub hub create -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name chat --event-handler url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event="connected"
```

업데이트가 완료되면 홈페이지 http://localhost:8080/index.html 을 열고 사용자 이름을 입력합니다. 그러면 연결되었다는 메시지가 서버 콘솔에 출력됩니다.

## <a name="handle-message-events"></a>메시지 이벤트 처리

`connected` 또는 `disconnected`와 같은 시스템 이벤트 외에도, 클라이언트는 WebSocket 연결을 통해 메시지를 보낼 수 있으며 이러한 메시지는 `message` 이벤트라는 특수한 유형의 이벤트로 서버에 전달됩니다. 이 이벤트를 사용하여 한 클라이언트에서 메시지를 수신한 후 모든 클라이언트에 메시지를 브로드캐스트하면 클라이언트가 서로 통신할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

`message` 이벤트의 `ce-type`은 항상 `azure.webpubsub.user.message`입니다. 자세한 내용은 [이벤트 메시지](./reference-cloud-events.md#message)를 참조하세요.

1. 메시지 이벤트 처리

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
                else if (context.Request.Headers["ce-type"] == "azure.webpubsub.user.message")
                {
                    using var stream = new StreamReader(context.Request.Body);
                    await serviceClient.SendToAllAsync($"[{userId}] {await stream.ReadToEndAsync()}");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

    이 이벤트 처리기는 `WebPubSubServiceClient.SendToAllAsync()`를 사용하여 수신 메시지를 모든 클라이언트에 브로드캐스트합니다.

2.  사용자로부터 서버로 메시지를 보내고 받은 메시지를 페이지에 표시하는 논리를 추가하도록 `index.html`을 업데이트합니다.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    위의 코드에서는 `WebSocket.send()`를 사용하여 메시지를 보내고 `WebSocket.onmessage`를 사용하여 서비스의 메시지를 수신 대기합니다.

3.  마지막으로 채팅방에 참가한 사람을 볼 수 있도록, 연결된 이벤트를 모든 클라이언트에 브로드캐스트하도록 `onConnected` 처리기를 업데이트합니다.

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    await serviceClient.SendToAllAsync($"[SYSTEM] {userId} joined.");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

이제 `dotnet run --urls http://localhost:8080`을 사용하여 서버를 실행하고 여러 브라우저 인스턴스를 열어 http://localhost:8080/index.html 에 액세스합니다. 그러면 다른 사용자와 채팅할 수 있습니다.

이 자습서의 전체 코드 샘플은 [여기][code-csharp]서 찾을 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 새 `handleUserEvent` 처리기를 추가합니다.

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, ['*'], {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') 
            await serviceClient.sendToAll(`[${req.context.userId}] ${req.data}`, { contentType: 'text/plain' });
        res.success();
      }
    });
    ```

    이 이벤트 처리기는 `WebPubSubServiceClient.sendToAll()`을 사용하여 수신 메시지를 모든 클라이언트에 브로드캐스트합니다.

    `handleUserEvent`에도 이벤트 보낸 사람에게 메시지를 다시 보낼 수 있는 `res` 개체가 포함되어 있습니다. 여기서는 간단하게 `res.success()`를 호출하여 WebHook가 200을 반환하도록 하고 있습니다. 클라이언트에 아무것도 반환하지 않으려는 경우에도 이 호출이 필요합니다. 이 호출이 없으면 WebHook가 절대 반환되지 않고 클라이언트 연결이 닫힙니다.

2.  사용자로부터 서버로 메시지를 보내고 받은 메시지를 페이지에 표시하는 논리를 추가하도록 `index.html`을 업데이트합니다.

    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            ws.onmessage = event => {
              let m = document.createElement('p');
              m.innerText = event.data;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
      </html>
    ```

    위의 코드에서는 `WebSocket.send()`를 사용하여 메시지를 보내고 `WebSocket.onmessage`를 사용하여 서비스의 메시지를 수신 대기합니다.

3. `sendToAll`은 개체를 입력으로 허용하고 JSON 텍스트를 클라이언트에 보냅니다. 실제 시나리오에서는 메시지에 대한 자세한 정보를 전달하기 위해 복잡한 개체가 필요할 것입니다. 마지막으로 JSON 개체를 모든 클라이언트에 브로드캐스트하도록 다음과 같이 처리기를 업데이트합니다.

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, ['*'], {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
        await serviceClient.sendToAll({
          type: "system",
          message: `${req.context.userId} joined`
        });
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') {
          await serviceClient.sendToAll({
            from: req.context.userId,
            message: req.data
          });
        }
        res.success();
      }
    });
    ```

4. 그리고 JSON 데이터를 구문 분석하도록 다음과 같이 클라이언트를 업데이트합니다.
    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            
            ws.onmessage = event => {
              let m = document.createElement('p');
              let data = JSON.parse(event.data);
              m.innerText = `[${data.type || ''}${data.from || ''}] ${data.message}`;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
    </html>
    ```

이제 서버를 실행하고 여러 브라우저 인스턴스를 엽니다. 그러면 다른 사용자와 채팅할 수 있습니다.

이 자습서의 전체 코드 샘플은 [여기][code-js]서 찾을 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

`message` 이벤트의 `ce-type`은 항상 `azure.webpubsub.user.message`입니다. 자세한 내용은 [이벤트 메시지](./reference-cloud-events.md#message)를 참조하세요.

1. 메시지 이벤트 처리

    ```java
    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });
    ```

    이 이벤트 처리기는 `client.sendToAll()`를 사용하여 수신 메시지를 모든 클라이언트에 브로드캐스트합니다.

2.  사용자로부터 서버로 메시지를 보내고 받은 메시지를 페이지에 표시하는 논리를 추가하도록 `index.html`을 업데이트합니다.

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
          ws.onmessage = event => {
            let m = document.createElement('p');
            m.innerText = event.data;
            messages.appendChild(m);
          };

          let message = document.querySelector('#message');
          message.addEventListener('keypress', e => {
            if (e.charCode !== 13) return;
            ws.send(message.value);
            message.value = '';
          });
        })();
      </script>
    </body>

    </html>
    ```

    위의 코드에서는 `WebSocket.send()`를 사용하여 메시지를 보내고 `WebSocket.onmessage`를 사용하여 서비스의 메시지를 수신 대기합니다.

3.  마지막으로 채팅방에 참가한 사람을 볼 수 있도록, 연결된 이벤트를 모든 클라이언트에 브로드캐스트하도록 `connected` 이벤트 처리기를 업데이트합니다.

    ```java

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            client.sendToAll(String.format("[SYSTEM] %s joined", id), WebPubSubContentType.TEXT_PLAIN);
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            client.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });

    ```

이제 아래 명령을 사용하여 서버를 실행하고 여러 브라우저 인스턴스를 엽니다. 그러면 다른 사용자와 채팅할 수 있습니다.

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
```

이 자습서의 전체 코드 샘플은 [여기][code-java]서 찾을 수 있습니다.

---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Web PubSub 서비스에서 이벤트 시스템이 작동하는 방식에 대한 기본 개념을 제공합니다.

다른 자습서를 확인하여 서비스 사용 방법을 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://aka.ms/awps/samples)


[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp/
[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp/
[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp/
