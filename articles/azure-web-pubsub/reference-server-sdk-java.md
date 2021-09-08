---
title: 참조 - Azure Web PubSub 서비스에 대한 Java 서버 SDK
description: 참조는 Azure Web PubSub 서비스에 대한 Java 서버 SDK에 대해 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: b36bf3715458fdf66d8c1a113e96e048bd414d0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116370"
---
# <a name="java-server-sdk-for-azure-web-pubsub-service"></a>Azure Web PubSub 서비스에 대한 Java 서버 SDK

라이브러리를 사용하여 다음을 수행합니다.

- 허브 및 그룹에 메시지를 보냅니다.
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여/철회/확인

[소스 코드][source_code] | [API 참조 설명서][api] | [제품 설명서][product_documentation] | [샘플][samples_readme]

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
    <version>1.0.0-beta.2</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-web-pubsub-client-using-connection-string"></a>연결 문자열을 사용하여 Web PubSub 클라이언트 만들기

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-client-using-access-key"></a>액세스 키를 사용하여 Web PubSub 클라이언트 만들기

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-group-client"></a>Web PubSub 그룹 클라이언트 만들기
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .hub("chat")
    .buildClient();
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("java");
```

## <a name="key-concepts"></a>주요 개념

[!INCLUDE [Terms](includes/terms.md)]


## <a name="examples"></a>예제

### <a name="broadcast-message-to-entire-hub"></a>전체 허브에 메시지 브로드캐스트

```java
webPubSubServiceClient.sendToAll("Hello world!");
```

### <a name="broadcast-message-to-a-group"></a>그룹에 메시지 브로드캐스트

```java
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("Java");
javaGroup.sendToAll("Hello Java!");
```

### <a name="send-message-to-a-connection"></a>연결로 메시지 보내기

```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!");
```

### <a name="send-message-to-a-user"></a>사용자에게 메시지 보내기
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!");
```

## <a name="troubleshooting"></a>문제 해결

### <a name="enable-client-logging"></a>클라이언트 로깅 사용
`AZURE_LOG_LEVEL` 환경 변수를 설정하여 클라이언트 라이브러리에서 작성된 로깅 문을 볼 수 있습니다. 예를 들어, `AZURE_LOG_LEVEL=2`를 설정하면 모든 정보, 경고 및 오류 로그 메시지가 표시됩니다. 로그 수준은 [로그 수준][log_levels]에서 찾을 수 있습니다.

### <a name="default-http-client"></a>기본 HTTP 클라이언트
모든 클라이언트 라이브러리는 기본적으로 Netty HTTP 클라이언트를 사용합니다. 위의 종속성을 추가하면 Netty HTTP 클라이언트를 사용하도록 클라이언트 라이브러리가 자동으로 구성됩니다. HTTP 클라이언트 구성 또는 변경은 [HTTP 클라이언트 wiki](/azure/developer/java/sdk/http-client-pipeline)에 자세히 설명되어 있습니다.

### <a name="default-ssl-library"></a>기본 SSL 라이브러리
모든 클라이언트 라이브러리는 기본적으로 Tomcat 네이티브 Boring SSL 라이브러리를 사용하여 SSL 작업에 대한 네이티브 수준 성능을 사용하도록 설정합니다. Boring SSL 라이브러리는 Linux/macOS/Windows용 네이티브 라이브러리를 포함하는 uber jar로, JDK 내에서 기본 SSL 구현에 비해 더 나은 성능을 제공합니다. 종속성 크기를 줄이는 방법을 비롯한 자세한 내용은 Wiki의 [성능 튜닝][performance_tuning] 섹션을 참조하세요.

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[api]: /java/api/com.azure.messaging.webpubsub

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
