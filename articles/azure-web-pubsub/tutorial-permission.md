---
title: 자습서 - Azure Web PubSub를 사용하여 애플리케이션에 인증 및 권한 추가
description: Azure Web PubSub를 사용하여 애플리케이션에 인증 및 권한을 추가하는 방법을 안내합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/26/2021
ms.openlocfilehash: e2f2b7ec00250287b71b3882b7078b249262db70
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769867"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub"></a>자습서: Azure Web PubSub를 사용하여 애플리케이션에 인증 및 권한 추가

[채팅 앱 빌드](./tutorial-build-chat.md)에서는 WebSocket API를 사용하여 Azure Web PubSub와 데이터를 주고 받는 방법을 알아보았습니다. 아시는지 모르겠지만, 간단한 설명을 위해 인증을 요구하지 않았습니다. Azure Web PubSub는 액세스 토큰이 연결되어야 하지만, 이 자습서에서 액세스 토큰을 생성하기 위해 사용한 `negotiate` API는 인증을 요구하지 않습니다. 따라서 누구나 이 API를 호출하여 액세스 토큰을 얻을 수 있습니다.

실제 애플리케이션에서는 사용자가 애플리케이션을 사용하려면 먼저 로그인하도록 하는 것이 일반적입니다. 이 자습서에서는 Web PubSub를 애플리케이션의 인증 및 권한 부여 시스템과 통합하여 보안을 강화하는 방법을 알아봅니다.

이 자습서의 전체 코드 샘플은 [GitHub][code]에서 찾을 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * GitHub 인증 사용
> * 애플리케이션에 인증 미들웨어 추가
> * 클라이언트에 권한 추가

## <a name="add-authentication-to-the-chat-room-app"></a>채팅방 앱에 인증 추가

이 자습서에서는 [채팅 앱 빌드](./tutorial-build-chat.md)에서 만든 채팅 애플리케이션을 재사용합니다. [GitHub][chat-js]서 채팅 앱의 전체 코드 샘플을 복제할 수도 있습니다. 

이 자습서에서는 채팅 애플리케이션에 인증을 추가하고 Web PubSub와 통합합니다.

먼저, 사용자가 GitHub 계정을 사용하여 로그인할 수 있도록 채팅방에 GitHub 인증을 추가합니다.

1.  종속성을 설치합니다.

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

1.  다음 코드를 `server.js`에 추가하여 GitHub 인증을 사용하도록 설정합니다.

    ```javascript
    const app = express();

    const users = {};
    passport.use(
      new GitHubStrategy({
        clientID: process.argv[3],
        clientSecret: process.argv[4]
      },
      (accessToken, refreshToken, profile, done) => {
        users[profile.id] = profile;
        return done(null, profile);
      }
    ));

    passport.serializeUser((user, done) => {
      done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
      if (users[id]) return done(null, users[id]);
      return done(`invalid user id: ${id}`);
    });

    app.use(cookieParser());
    app.use(session({
      resave: false,
      saveUninitialized: true,
      secret: 'keyboard cat'
    }));
    app.use(passport.initialize());
    app.use(passport.session());
    app.get('/auth/github', passport.authenticate('github', { scope: ['user:email'] }));
    app.get('/auth/github/callback', passport.authenticate('github', { successRedirect: '/' }));
    ```

    위의 코드는 [Passport.js](http://www.passportjs.org/)를 사용하여 GitHub 인증을 사용하도록 설정합니다. 다음은 인증 방식에 대한 간단한 설명입니다.

    1. 로그인을 위해 `/auth/github`가 github.com으로 리디렉션합니다.
    1. 사용자가 로그인하면 애플리케이션에서 인증을 완료하도록 GitHub가 코드를 사용하여 사용자를 `/auth/github/callback`으로 리디렉션합니다. (GitHub에서 반환된 프로필을 확인하고 서버에 보관하는 방법을 알아보려면 `passport.use()`의 확인 콜백을 참조하세요.)
    1. 인증이 완료되면 사용자는 사이트의 홈페이지(`/`)로 리디렉션됩니다.
 
    GitHub OAuth 및 Passport.js에 대한 자세한 내용은 다음 문서를 참조하세요.

    - [OAuth 앱 권한 부여](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Passport.js 문서](http://www.passportjs.org/docs/)

    이를 테스트하려면 먼저 다음과 같이 GitHub OAuth 앱을 만들어야 합니다.

    1. https://www.github.com 으로 이동하여 프로필을 열고, **설정** > **개발자 설정** 을 엽니다.
    1. OAuth 앱으로 이동하여 **새 OAuth 앱** 을 선택합니다.
    1. 애플리케이션 이름 및 홈페이지 URL(원하는 아무 URL)을 입력하고, **권한 부여 콜백 URL** 을 `http://localhost:8080/auth/github/callback`으로 설정합니다. 이 URL은 서버에서 노출한 콜백 API와 일치합니다.
    1. 애플리케이션이 등록되면 클라이언트 ID를 복사하고 **새 클라이언트 암호 생성** 을 선택합니다.

    그런 다음, `node server <connection-string> <client-id> <client-secret>` 명령을 실행하고 `http://localhost:8080/auth/github`를 엽니다. 사용자가 로그인하도록 GitHub로 리디렉션됩니다. 로그인하면 채팅 애플리케이션으로 리디렉션됩니다.

1.  사용자에게 사용자 이름을 묻는 대신 GitHub에서 가져온 ID를 사용하도록 대화방을 업데이트합니다.

    사용자 ID를 전달하지 않고 직접 `/negotiate`를 호출하도록 `public/index.html`을 업데이트합니다.

    ```javascript
    let messages = document.querySelector('#messages');
    let res = await fetch(`/negotiate`);
    if (res.status === 401) {
      let m = document.createElement('p');
      m.innerHTML = 'Not authorized, click <a href="/auth/github">here</a> to login';
      messages.append(m);
      return;
    }
    let data = await res.json();
    let ws = new WebSocket(data.url);
    ```

    사용자가 로그인하면 요청에서 쿠키를 통해 사용자 ID를 자동으로 전달합니다. 따라서 다음과 같이 사용자가 `req` 개체에 있는지 확인하고 사용자 이름을 Web PubSub 액세스 토큰에 추가하기만 하면 됩니다.

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getAuthenticationToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    이제 서버를 다시 실행하고 채팅방을 처음으로 열면 "권한 없음" 메시지가 표시됩니다. 로그인 링크를 클릭하여 로그인하면 채팅방이 이전처럼 작동합니다.

## <a name="work-with-permissions"></a>권한 작업

이전 자습서에서는 `WebSocket.send()`를 사용하여 하위 프로토콜을 통해 메시지를 다른 클라이언트에 직접 게시하는 방법을 배웠습니다. 실제 애플리케이션에서는 클라이언트가 권한 제어 없이 모든 그룹에 메시지를 게시 또는 구독하는 것을 허용하지 않을 것입니다. 이 섹션에서는 Web PubSub의 권한 시스템을 사용하여 클라이언트를 제어하는 방법을 알아봅니다.

Web PubSub에서 클라이언트는 하위 프로토콜을 사용하여 다음과 같은 유형의 작업을 수행할 수 있습니다.

- 서버에 이벤트 보내기
- 그룹에 메시지 게시
- 그룹 조인(구독)

서버에 이벤트 보내기는 클라이언트의 기본 작업입니다. 프로토콜이 사용되지 않으므로 항상 허용됩니다. 그룹을 게시하고 구독하려면 클라이언트에 권한이 있어야 합니다. 다음과 같은 두 가지 방법으로 서버에서 클라이언트에 권한을 부여할 수 있습니다.

- 클라이언트가 연결될 때 역할을 지정합니다(*역할* 은 클라이언트가 연결될 때 초기 권한을 나타내는 개념).
- API를 사용하여 클라이언트가 연결된 후 클라이언트에 권한을 부여합니다.

그룹 조인 권한의 경우 클라이언트는 권한을 가져온 후에도 여전히 "그룹 조인" 메시지를 사용하여 그룹에 조인해야 합니다. 또는 서버는 조인 권한이 없어도 API를 사용하여 그룹에 클라이언트를 추가할 수 있습니다.

이제 이 권한 시스템을 사용하여 채팅방에 새 기능을 추가해 보겠습니다. *관리자* 라는 새로운 유형의 사용자를 대화방에 추가하겠습니다. 관리자가 클라이언트에서 직접 시스템 메시지("[SYSTEM]"으로 시작하는 메시지)를 보낼 수 있도록 허용하겠습니다.

먼저, 시스템 메시지와 사용자 메시지를 서로 다른 두 그룹으로 구분하여 권한을 개별적으로 제어할 수 있도록 해야 합니다.

다른 그룹에 다른 메시지를 전송하도록 다음과 같이 `server.js`를 변경합니다.

```javascript
let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  handleConnect: (req, res) => {
    res.success({
      groups: ['system', 'message'],
    });
  },
  onConnected: req => {
    console.log(`${req.context.userId} connected`);
    serviceClient.group('system').sendToAll(`${req.context.userId} joined`, { contentType: 'text/plain' });
  },
  handleUserEvent: (req, res) => {
    if (req.context.eventName === 'message') {
      serviceClient.group('message').sendToAll({
        user: req.context.userId,
        message: req.data
      });
    }
    res.success();
  }
});
```

위의 코드에서는 `WebPubSubServiceClient.group().sendToAll()` 명령을 사용하여 허브 대신 그룹에 메시지를 보냅니다.

이제 메시지가 그룹에 전송되므로, 그룹에서 메시지를 계속 받을 수 있도록 클라이언트를 그룹에 추가해야 합니다. `handleConnect` 처리기를 사용하여 그룹에 클라이언트를 추가합니다.

> [!Note]
> `handleConnect`는 클라이언트가 Web PubSub에 연결하려고 할 때 트리거됩니다. 이 처리기에서 그룹과 역할을 반환할 수 있으므로, 연결이 설정되는 즉시 서비스에서 그룹에 대한 연결을 추가하거나 역할을 부여할 수 있습니다. 서비스에서 `res.fail()`을 사용하여 연결을 거부할 수도 있습니다.
>

`handleConnect`를 트리거하려면 Azure Portal에서 이벤트 처리기 설정으로 이동하고, 시스템 이벤트에서 **연결** 을 선택합니다.

이제 서버에서 일반 텍스트 대신 JSON 메시지를 보내기 때문에 클라이언트 HTML도 업데이트해야 합니다.

```javascript
let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
ws.onopen = () => console.log('connected');

ws.onmessage = event => {
  let m = document.createElement('p');
  let message = JSON.parse(event.data);
  switch (message.type) {
    case 'message':
      if (message.group === 'system') m.innerText = `[SYSTEM] ${message.data}`;
      else if (message.group === 'message') m.innerText = `[${message.data.user}] ${message.data.message}`;
      break;
  }
  messages.appendChild(m);
};

let message = document.querySelector('#message');
message.addEventListener('keypress', e => {
  if (e.charCode !== 13) return;
  ws.send(JSON.stringify({
    type: 'event',
    event: 'message',
    dataType: 'text',
    data: message.value
  }));
  message.value = '';
});
```

그런 다음, 사용자가 **시스템 메시지** 를 선택하면 시스템 그룹에 보낼 클라이언트 코드를 변경합니다.

```html
<button id="system">system message</button>
...
<script>
  (async function() {
    ...
    let system = document.querySelector('#system');
    system.addEventListener('click', e => {
      ws.send(JSON.stringify({
        type: 'sendToGroup',
        group: 'system',
        dataType: 'text',
        data: message.value
      }));
      message.value = '';
    });
  })();
</script>
```

기본적으로 클라이언트는 모든 그룹에 메시지를 보낼 수 있는 권한이 없습니다. 따라서 관리 사용자에 대한 권한을 부여하도록 서버 코드를 업데이트합니다(간단한 설명을 위해 관리자 ID는 명령줄 인수로 제공됨).

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[5]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getAuthenticationToken(options);
});
```

이제 `node server <connection-string> <client-id> <client-secret> <admin-id>`를 실행합니다. 이제 `<admin-id>`로 로그인하면 모든 클라이언트에 시스템 메시지를 보낼 수 있습니다.

그러나 다른 사용자로 로그인하면 **시스템 메시지** 를 선택해도 아무 작업도 수행되지 않습니다. 작업이 허용되지 않는 것을 알리기 위해 서비스에서 오류를 표시할 수도 있습니다. 이 피드백을 제공하려면 메시지를 게시할 때 `ackId`를 설정하면 됩니다. `ackId`가 지정될 때마다 Web PubSub는 일치하는 `ackId`와 함께 작업의 성공 여부를 나타내는 메시지를 반환합니다.

시스템 메시지를 보내는 코드를 다음 코드로 변경합니다.

```javascript
let ackId = 0;
system.addEventListener('click', e => {
  ws.send(JSON.stringify({
    type: 'sendToGroup',
    group: 'system',
    ackId: ++ackId,
    dataType: 'text',
    data: message.value
    }));
  message.value = '';
});
```

또한 `ack` 메시지를 처리하도록 다음과 같이 메시지 처리 코드를 변경합니다.

```javascript
ws.onmessage = event => {
  ...
  switch (message.type) {
    case 'ack':
      if (!message.success && message.error.name === 'Forbidden') m.innerText = 'No permission to send system message';
      break;
  }
};
```

이제 서버를 다시 실행하고, 다른 사용자로 로그인합니다. 시스템 메시지를 보내려고 하면 오류 메시지가 표시됩니다.

이 자습서의 전체 코드 샘플은 [GitHub][code]서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 하위 프로토콜을 사용하여 Web PubSub 서비스에 연결하는 방법과 연결된 클라이언트에 메시지를 게시하는 방법에 대한 기본 개념을 알아보았습니다.

Web PubSub 서비스 사용 방법에 대한 자세한 내용은 설명서에서 제공하는 다른 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
