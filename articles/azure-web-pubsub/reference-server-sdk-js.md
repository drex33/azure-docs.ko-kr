---
title: 참조 - Azure Web PubSub용 JavaScript SDK
description: 이 참조는 Azure Web PubSub 서비스에 대한 JavaScript SDK에 대해 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 4571bb8581892525785c3f08e0c627bf20f511aa
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579530"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>Azure Web PubSub용 JavaScript SDK

JavaScript용으로 제공되는 두 가지 라이브러리는 서비스 클라이언트 라이브러리와 Express 미들웨어입니다. 다음 섹션에는 이러한 라이브러리에 대한 자세한 정보가 포함되어 있습니다.

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>JavaScript용 Azure Web PubSub 서비스 클라이언트 라이브러리

이 라이브러리를 사용하여 다음을 수행할 수 있습니다.
- 허브 및 그룹에 메시지를 보냅니다. 
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결을 닫습니다.
- 기존 연결에 대한 사용 권한을 부여, 해지 및 확인합니다.

[소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[패키지(NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API 참조 설명서](/javascript/api/@azure/web-pubsub/) |
[제품 설명서](./index.yml) |
[샘플][samples_ref]

### <a name="get-started"></a>시작

[Node.js](https://nodejs.org/) 버전 8.x.x 이상 버전을 사용합니다. 또한 다음과 같은 필수 구성 조건이 있는지 확인합니다.

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 서비스 인스턴스.

#### <a name="install-the-azureweb-pubsub-package"></a>`@azure/web-pubsub` 패키지를 설치합니다.

```bash
npm install @azure/web-pubsub
```

#### <a name="create-and-authenticate-webpubsubserviceclient"></a>만들기 및 인증 `WebPubSubServiceClient`

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

엔드포인트 및 를 사용하여 인증할 수도 `WebPubSubServiceClient` 있습니다. `AzureKeyCredential`

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

이 라이브러리를 사용하는 경우 다음 환경 변수를 설정하여 디버그 로그를 얻을 수 있습니다.

- SignalR 클라이언트 라이브러리에서 디버그 로그 가져오기

```bash
export AZURE_LOG_LEVEL=verbose
```

로그를 사용하는 방법에 대한 자세한 내용은 [@azure/logger 패키지 문서](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)를 참조하세요.

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Express용 Azure Web PubSub CloudEvents 처리기

Express 라이브러리를 사용하여 다음을 수행할 수 있습니다.
- Web PubSub CloudEvents 미들웨어를 추가하여 들어오는 클라이언트 이벤트를 처리합니다.
  - 남용 유효성 검사 요청을 처리합니다.
  - 클라이언트 이벤트 요청을 처리합니다.

[소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[패키지(NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API 참조 설명서](/javascript/api/@azure/web-pubsub-express/) |
[제품 설명서](./index.yml) |
[샘플][samples_ref]

### <a name="get-started"></a>시작

[Node.js](https://nodejs.org/) 버전 8.x.x 이상 또는 [Express](https://expressjs.com/) 버전 4.x.x 이상 버전을 사용합니다. 또한 다음과 같은 필수 구성 조건이 있는지 확인합니다.

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 엔드포인트.

#### <a name="install-the-azureweb-pubsub-express-package"></a>`@azure/web-pubsub-express` 패키지를 설치합니다.

```bash
npm install @azure/web-pubsub-express
```

#### <a name="create-webpubsubeventhandler"></a>`WebPubSubEventHandler`을 만듭니다.

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

- **클라이언트 이벤트:** 클라이언트는 연결의 수명 주기 동안 이벤트를 만듭니다. 예를 들어 간단한 WebSocket 클라이언트 연결은 다음 이벤트를 만듭니다.
  - `connect`서비스에 연결하려고 할 때의 이벤트입니다.
  - `connected`서비스에 성공적으로 연결할 때의 이벤트입니다.
  - `message`서비스에 메시지를 보낼 때의 이벤트입니다.
  - `disconnected`서비스에서 연결을 끊을 때의 이벤트입니다.

- **이벤트 처리기:** 이벤트 처리기에는 클라이언트 이벤트를 처리하는 논리가 포함되어 있습니다. 이벤트 처리기는 Azure Portal 또는 Azure CLI 통해 서비스에 미리 등록 및 구성되어야 합니다. 서버는 일반적으로 이벤트 처리기 논리를 호스팅합니다.

### <a name="troubleshooting"></a>문제 해결

#### <a name="dump-request"></a>덤프 요청

수신 요청을 보려면 `dumpRequest`를 `true`로 설정합니다.

#### <a name="live-trace"></a>라이브 추적

Web PubSub 서비스 포털에서 **라이브 추적** 을 사용하여 실시간 트래픽을 확인합니다.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]