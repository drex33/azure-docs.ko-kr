---
title: 참조 - Azure Web PubSub용 Java SDK
description: 이 참조에서는 Azure Web PubSub 서비스에 대한 Java SDK를 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: f0e1017cacf2d556ed9b61a90a9881653a7ca336
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714792"
---
# <a name="azure-web-pubsub-service-client-library-for-java"></a>Java용 Azure Web PubSub 서비스 클라이언트 라이브러리

[Azure Web PubSub 서비스는](./index.yml) 개발자가 실시간 기능 및 게시-구독 패턴으로 웹 애플리케이션을 쉽게 빌드할 수 있도록 도와주는 Azure 관리형 서비스입니다. 서버와 클라이언트 간 또는 클라이언트 간에 실시간 게시-구독 메시징이 필요한 시나리오는 Azure Web PubSub 서비스를 사용할 수 있습니다. 서버에서 폴링하거나 HTTP 요청을 제출해야 하는 기존의 실시간 기능에서도 Azure Web PubSub 서비스를 사용할 수 있습니다.

아래 다이어그램과 같이 앱 서버 쪽에서 이 라이브러리를 사용하여 WebSocket 클라이언트 연결을 관리할 수 있습니다.

![오버플로 다이어그램은 서비스 클라이언트 라이브러리를 사용하는 의 오버플로를 보여줍니다.](media/sdk-reference/service-client-overflow.png)

이 라이브러리를 사용하여 다음을 수행합니다.
- 허브 및 그룹에 메시지를 보냅니다. 
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여, 철회 및 확인

여기에 사용된 용어에 대한 자세한 내용은 [주요 개념](#key-concepts) 섹션에 설명되어 있습니다.

[소스 코드][source_code] | [API 참조 설명서] [api] | [제품 설명서][product_documentation]  |  [샘플][samples_readme]

## <a name="getting-started"></a>시작

### <a name="prerequisites"></a>필수 조건

- [JDK(Java Development Kit)][jdk_link], 버전 8 이상
- [Azure 구독][azure_subscription]

### <a name="include-the-package"></a>패키지 포함

[//]: # ({x-version-update-start;com.azure:azure-messaging-webpubsub;current})

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-webpubsub</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-webpubsubserviceclient-using-connection-string"></a>연결 `WebPubSubServiceClient` 문자열을 사용하여 만들기

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L21-L24 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-webpubsubserviceclient-using-access-key"></a>액세스 `WebPubSubServiceClient` 키를 사용하여 만들기

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L31-L35 -->
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubServiceClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

## <a name="key-concepts"></a>주요 개념

### <a name="connection"></a>연결

클라이언트 또는 클라이언트 연결이라고도 하는 연결은 Web PubSub 서비스에 연결된 개별 WebSocket 연결을 나타냅니다. 성공적으로 연결되면 Web PubSub 서비스에서 고유한 연결 ID를 이 연결에 할당합니다.

### <a name="hub"></a>허브

허브는 클라이언트 연결 집합에 대한 논리적 개념입니다. 일반적으로 하나의 목적에 대해 하나의 허브를 사용합니다(예: 채팅 허브 또는 알림 허브). 클라이언트 연결이 만들어지면 허브에 연결되고 수명 동안 해당 허브에 속합니다. 애플리케이션마다 서로 다른 허브 이름을 사용하여 하나의 Azure Web PubSub 서비스를 공유할 수 있습니다.

### <a name="group"></a>그룹

그룹은 허브에 대한 연결의 하위 집합입니다. 그룹에 클라이언트 연결을 추가하거나 원하는 경우 그룹에서 클라이언트 연결을 제거할 수 있습니다. 예를 들어 클라이언트가 채팅방에 참가하거나 채팅방에서 나가면 이 채팅방이 그룹일 수 있습니다. 클라이언트는 여러 그룹에 참가할 수 있으며, 한 그룹에 여러 클라이언트가 포함될 수 있습니다.

### <a name="user"></a>사용자

Web PubSub에 대한 연결은 한 명의 사용자에게 속할 수 있습니다. 단일 사용자가 여러 디바이스 또는 여러 브라우저 탭에서 연결된 경우와 같이 사용자에게 여러 연결이 있을 수 있습니다.

### <a name="message"></a>메시지

클라이언트가 연결되면 업스트림 애플리케이션으로 메시지를 보내거나 WebSocket 연결을 통해 업스트림 애플리케이션에서 메시지를 받을 수 있습니다.

## <a name="examples"></a>예제

* [전체 허브에 메시지 브로드캐스트](#broadcast-all "전체 허브에 메시지 브로드캐스트")
* [그룹에 메시지 브로드캐스트](#broadcast-group "그룹에 메시지 브로드캐스트")
* [연결로 메시지 보내기](#send-to-connection "연결로 메시지 보내기")
* [사용자에게 메시지 보내기](#send-to-user "사용자에게 메시지 보내기")

<a name="broadcast-all"></a>

### <a name="broadcast-message-to-entire-hub"></a>전체 허브에 메시지 브로드캐스트

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L47-L47 -->
```java
webPubSubServiceClient.sendToAll("Hello world!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="broadcast-group"></a>

### <a name="broadcast-message-to-a-group"></a>그룹에 메시지 브로드캐스트

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L59-L59 -->
```java
webPubSubServiceClient.sendToGroup("java", "Hello Java!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-connection"></a>

### <a name="send-message-to-a-connection"></a>연결로 메시지 보내기

<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L71-L71 -->
```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!", WebPubSubContentType.TEXT_PLAIN);
```

<a name="send-to-user"></a>

### <a name="send-message-to-a-user"></a>사용자에게 메시지 보내기
<!-- embedme ./src/samples/java/com/azure/messaging/webpubsub/ReadmeSamples.java#L83-L83 -->
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!", WebPubSubContentType.TEXT_PLAIN);
```

## <a name="troubleshooting"></a>문제 해결

### <a name="enable-client-logging"></a>클라이언트 로깅 사용
`AZURE_LOG_LEVEL` 환경 변수를 설정하여 클라이언트 라이브러리에서 작성된 로깅 문을 볼 수 있습니다. 예를 들어, `AZURE_LOG_LEVEL=2`를 설정하면 모든 정보, 경고 및 오류 로그 메시지가 표시됩니다. 로그 수준은 [로그 수준][log_levels]에서 찾을 수 있습니다.

### <a name="default-http-client"></a>기본 HTTP 클라이언트
모든 클라이언트 라이브러리는 기본적으로 Netty HTTP 클라이언트를 사용합니다. 위의 종속성을 추가하면 Netty HTTP 클라이언트를 사용하도록 클라이언트 라이브러리가 자동으로 구성됩니다. HTTP 클라이언트 구성 또는 변경은 [HTTP 클라이언트 wiki](https://github.com/Azure/azure-sdk-for-java/wiki/HTTP-clients)에 자세히 설명되어 있습니다.

### <a name="default-ssl-library"></a>기본 SSL 라이브러리
모든 클라이언트 라이브러리는 기본적으로 Tomcat 네이티브 Boring SSL 라이브러리를 사용하여 SSL 작업에 대한 네이티브 수준 성능을 사용하도록 설정합니다. Boring SSL 라이브러리는 Linux/macOS/Windows용 네이티브 라이브러리를 포함하는 uber jar로, JDK 내에서 기본 SSL 구현에 비해 더 나은 성능을 제공합니다. 종속성 크기를 줄이는 방법을 비롯한 자세한 내용은 wiki의 [성능 튜닝 섹션을][performance_tuning] 참조하세요.

[!INCLUDE [next step](includes/include-next-step.md)]


<!-- LINKS -->

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: ./index.yml
[samples_readme]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/webpubsub/azure-messaging-webpubsub/src/samples/README.md
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[cla]: https://cla.microsoft.com
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/
[coc_contact]: mailto:opencode@microsoft.com
[api]::