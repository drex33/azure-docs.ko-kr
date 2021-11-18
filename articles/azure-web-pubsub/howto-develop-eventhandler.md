---
title: 이벤트 처리기를 구성하는 방법
description: Azure Web PubSub 서비스를 통해 개발할 때 이벤트 처리기 개념 및 통합 소개에 대한 지침입니다.
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/31/2021
ms.openlocfilehash: 2fc3395ac5a58377900d74573c03667edf7b3ac1
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755738"
---
# <a name="event-handler-in-azure-web-pubsub-service"></a>Azure Web PubSub 서비스의 이벤트 처리기

이벤트 처리기는 수신 클라이언트 이벤트를 처리합니다. 이벤트 처리기는 포털 또는 Azure CLI를 통해 서비스에 미리 등록되고 구성되므로, 클라이언트 이벤트가 트리거되면 서비스에서 이벤트 처리가 예상되는지 여부를 식별할 수 있습니다. 이제 이벤트가 트리거될 때 호출할 서비스에 대한 공용 액세스 가능 엔드포인트를 노출하는 이벤트 처리기를 서버 쪽으로 지원합니다. 즉, **webhook** 역할을 합니다. 

서비스는 [CloudEvents HTTP 프로토콜](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)을 사용하여 업스트리밍 웹후크에 클라이언트 이벤트를 전달합니다.

모든 이벤트에 대해 등록된 업스트리밍에 대한 HTTP POST 요청을 공식화하고 HTTP 응답을 예상합니다. 

서비스에서 서버로 보내는 데이터는 항상 CloudEvents `binary` 형식입니다.

:::image type="content" source="media/howto-develop-eventhandler/event-trigger.png" alt-text="Web PubSub 서비스 이벤트 트리거의 스크린샷.":::

## <a name="upstream-and-validation"></a>업스트림 및 유효성 검사

웹후크 엔드포인트를 구성할 때 URL에서 `{event}` 매개 변수를 사용하여 URL 템플릿을 정의할 수 있습니다. 서비스는 클라이언트 요청이 들어올 때 웹후크 URL의 값을 동적으로 계산합니다. 예를 들어 허브 `chat`에 맞게 구성된 이벤트 처리기 URL 패턴 `http://host.com/api/{event}`와 함께 요청 `/client/hubs/chat`가 들어오면 클라이언트가 연결할 때 먼저 이 URL `http://host.com/api/connect`에 POST합니다. 이는 PubSub WebSocket 클라이언트가 사용자 지정 이벤트를 보낼 때 유용할 수 있습니다. 즉, 이벤트 처리기가 다른 이벤트를 다른 업스트림으로 전달하는 데 도움이 됩니다. URL 도메인 이름에는 `{event}` 매개 변수가 허용되지 않습니다.

Azure Portal 또는 CLI를 통해 이벤트 처리기 업스트림을 설정하는 경우 서비스는 [CloudEvents 남용 보호에](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) 따라 업스트림 웹후크의 유효성을 검사합니다. 등록된 모든 업스트림 웹후크 URL은 이 메커니즘에 의해 유효성이 검사됩니다. `WebHook-Request-Origin`요청 헤더는 서비스 도메인 이름 로 설정되며 이 도메인 `xxx.webpubsub.azure.com` 이름 또는 을 포함하는 헤더가 있는 응답을 예상합니다. `WebHook-Allowed-Origin` `*`

유효성 검사를 수행할 때 `{event}` 매개 변수는 `validate`로 확인됩니다. 예를 들어 URL을 `http://host.com/api/{event}`로 설정하려고 하면 서비스에서 `http://host.com/api/validate`에 대한 요청을 **OPTIONS** 하려고 하고, 응답이 유효한 경우에만 구성을 제대로 설정할 수 있습니다.

현재 [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) 및 [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback)은 지원되지 않습니다.

## <a name="authentication-between-service-and-webhook"></a>서비스와 웹후크 간 인증

- 익명 모드
- 를 통한 간단한 `?code=<code>` 인증은 구성된 웹후크 URL을 통해 쿼리 매개 변수로 제공됩니다.
- AAD 인증을 사용하세요. 자세한 내용은 [여기를 확인하세요.](howto-use-managed-identity.md)
   - 1단계: Web PubSub 서비스에 대한 ID 사용
   - 2단계: 웹후크 웹앱을 의미하는 기존 AAD 애플리케이션에서 선택

## <a name="configure-event-handler"></a>이벤트 처리기 구성

### <a name="configure-through-azure-portal"></a>Azure Portal 통해 구성

**Azure Portal** Azure Web PubSub 서비스를 찾습니다. **설정** 이동하여 허브 이름을 입력합니다. 그런 다음 **추가를** 클릭하여 서버 쪽 웹후크 URL을 구성합니다. 완료되면 **저장을** 클릭해야 합니다.

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="이벤트 처리기를 설정하는 스크린샷.":::

### <a name="configure-through-azure-cli"></a>Azure CLI 통해 구성

Azure CLI [**az webpubsub hub**](/cli/azure/webpubsub/hub) group 명령을 사용하여 이벤트 처리기 설정을 구성합니다.

명령 | Description
--|--
create | WebPubSub 서비스에 대한 허브 설정을 만듭니다.
delete | WebPubSub 서비스에 대한 허브 설정을 삭제합니다.
list   | WebPubSub 서비스에 대한 모든 허브 설정을 나열합니다.
show   | WebPubSub 서비스에 대한 허브 설정을 표시합니다.
update | WebPubSub 서비스에 대한 허브 설정을 업데이트합니다.

다음은 리소스 허브에 대한 2개 webhook URL을 만드는 `MyHub` `MyWebPubSub` 예제입니다.

```azurecli-interactive
az webpubsub hub create -n "MyWebPubSub" -g "MyResourceGroup" --hub-name "MyHub" --event-handler url-template="http://host.com" user-event-pattern="*" --event-handler url-template="http://host2.com" system-event="connected" system-event="disconnected" auth-type="ManagedIdentity" auth-resource="uri://myUri"
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]