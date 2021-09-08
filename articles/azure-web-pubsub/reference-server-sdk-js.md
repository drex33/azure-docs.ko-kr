---
title: 참조 - Azure Web PubSub 서비스에 대한 JavaScript SDK
description: 참조는 Azure Web PubSub 서비스에 대한 JavaScript SDK를 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1d507f29b184403cb9ef31d84111af60f75c1584
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116919"
---
# <a name="javascript-sdk-for-the-azure-web-pubsub-service"></a>Azure Web PubSub 서비스에 대한 JavaScript SDK

JavaScript용으로 2개의 라이브러리가 제공됩니다.
- [서비스 클라이언트 라이브러리](#service-client-library):
    - 허브 및 그룹에 메시지를 보냅니다.
    - 특정 사용자 및 연결에 메시지를 보냅니다.
    - 사용자와 연결을 그룹으로 구성합니다.
    - 연결 종료
    - 기존 연결에 대한 권한 부여/철회/확인
- 수신 클라이언트 이벤트를 처리하기 위한 [Express 미들웨어](#express)
  - 남용 유효성 검사 요청 처리
  - 클라이언트 이벤트 요청 처리

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>JavaScript용 Azure Web PubSub 서비스 클라이언트 라이브러리
라이브러리를 사용하여 다음을 수행합니다.

- 허브 및 그룹에 메시지를 보냅니다.
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여/철회/확인

[소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[패키지(NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API 참조 설명서](/javascript/api/@azure/web-pubsub/) |
[제품 설명서](https://aka.ms/awps/doc) |
[샘플][samples_ref]

### <a name="getting-started"></a>시작

#### <a name="currently-supported-environments"></a>현재 지원되는 환경

- [Node.js](https://nodejs.org/) 버전 8.x.x 이상

#### <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 서비스 인스턴스.

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. `@azure/web-pubsub` 패키지 설치

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. WebPubSubServiceClient 만들기 및 인증

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

엔드포인트와 `AzureKeyCredential`을 사용하여 `WebPubSubServiceClient`를 인증할 수도 있습니다.

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>주요 개념

[!INCLUDE [Terms](includes/terms.md)]

### <a name="examples"></a>예제

#### <a name="broadcast-a-json-message-to-all-users"></a>모든 사용자에게 JSON 메시지 브로드캐스트

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" });
```

#### <a name="broadcast-a-plain-text-message-to-all-users"></a>모든 사용자에게 일반 텍스트 메시지 브로드캐스트

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });
```

#### <a name="broadcast-a-binary-message-to-all-users"></a>모든 사용자에게 이진 메시지 브로드캐스트

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

### <a name="troubleshooting"></a>문제 해결

#### <a name="enable-logs"></a>로그 사용 설정

다음 환경 변수를 설정하여 이 라이브러리를 사용할 때 디버그 로그를 볼 수 있습니다.

- SignalR 클라이언트 라이브러리에서 디버그 로그 가져오기

```bash
export AZURE_LOG_LEVEL=verbose
```

로그를 사용하는 방법에 대한 자세한 내용은 [@azure/logger 패키지 문서](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)를 참조하세요.

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Express용 Azure Web Pubsub CloudEvents 처리기

Express 라이브러리를 사용하여 다음을 수행합니다.
- 들어오는 클라이언트 이벤트를 처리하기 위해 Web PubSub CloudEvents 미들웨어 추가
  - 남용 유효성 검사 요청 처리
  - 클라이언트 이벤트 요청 처리

[소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[패키지(NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API 참조 설명서](/javascript/api/@azure/web-pubsub-express/) |
[제품 설명서](https://aka.ms/awps/doc) |
[샘플][samples_ref]

### <a name="getting-started"></a>시작

#### <a name="currently-supported-environments"></a>현재 지원되는 환경

- [Node.js](https://nodejs.org/) 버전 8.x.x 이상
- [Express](https://expressjs.com/) 버전 4.x.x 이상

#### <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 엔드포인트.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. `@azure/web-pubsub-express` 패키지 설치

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. WebPubSubEventHandler 만들기

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler(
  "chat",
  ["https://<yourAllowedService>.webpubsub.azure.com"],
  {
    handleConnect: (req, res) => {
      // auth the connection and set the userId of the connection
      res.success({
        userId: "<userId>"
      });
    }
  }
);

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>주요 개념

#### <a name="client-events"></a>클라이언트 이벤트

이벤트는 클라이언트 연결의 수명 주기 동안 생성됩니다. 예를 들어 단순 WebSocket 클라이언트 연결에서는 서비스에 연결하려고 하면 `connect` 이벤트, 서비스에 성공적으로 연결되면 `connected` 이벤트, 서비스에 메시지를 보낼 때 `message` 이벤트, 서비스에서 연결이 끊어지면 `disconnected` 이벤트가 생성됩니다.

#### <a name="event-handler"></a>이벤트 처리기

이벤트 처리기에는 클라이언트 이벤트를 처리하는 논리가 포함되어 있습니다. 이벤트 처리기는 미리 포털 또는 Azure CLI를 통해 서비스에 등록 및 구성해야 합니다. 이벤트 처리기 논리를 호스팅하는 위치는 일반적으로 서버 쪽으로 간주됩니다.

### <a name="troubleshooting"></a>문제 해결

#### <a name="dump-request"></a>덤프 요청

수신 요청을 보려면 `dumpRequest`를 `true`로 설정합니다.

#### <a name="live-trace"></a>라이브 추적

Web PubSub 서비스 포털에서 **라이브 추적** 을 사용하여 실시간 트래픽을 확인합니다.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
