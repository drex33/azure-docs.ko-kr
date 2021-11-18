---
title: 참조-Azure 웹 PubSub 용 .NET SDK
description: 이 참조는 Azure 웹 PubSub 서비스에 대 한 .NET SDK를 설명 합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: ac940b7760ce94beaca5148bfc4e33a59e14b626
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709788"
---
# <a name="azure-web-pubsub-service-client-library-for-net"></a>.NET 용 Azure 웹 PubSub 서비스 클라이언트 라이브러리

[Azure 웹 PubSub 서비스](./index.yml) 는 개발자가 실시간 기능 및 게시-구독 패턴을 사용 하 여 웹 응용 프로그램을 쉽게 빌드할 수 있도록 하는 azure 관리 서비스입니다. 서버와 클라이언트 간 또는 클라이언트 간에 실시간 게시 구독 메시징이 필요한 시나리오는 Azure 웹 PubSub 서비스를 사용할 수 있습니다. 종종 서버에서 폴링을 수행 하거나 HTTP 요청을 제출 해야 하는 기존의 실시간 기능은 Azure 웹 PubSub 서비스를 사용할 수도 있습니다.

아래 다이어그램에 표시 된 것 처럼 앱 서버 쪽에서이 라이브러리를 사용 하 여 WebSocket 클라이언트 연결을 관리할 수 있습니다.

![오버플로 다이어그램에서는 서비스 클라이언트 라이브러리를 사용 하는 오버플로를 보여 줍니다.](media/sdk-reference/service-client-overflow.png)

이 라이브러리를 사용 하 여 다음을 수행 합니다.
- 허브 및 그룹에 메시지를 보냅니다. 
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여, 철회 및 확인

여기에 사용된 용어에 대한 자세한 내용은 [주요 개념](#key-concepts) 섹션에 설명되어 있습니다.
 
[소스 코드](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/src) |
[패키지](https://www.nuget.org/packages/Azure.Messaging.WebPubSub) |
[API 참조 설명서]() |
[제품 설명서](./index.yml) |
[샘플][samples_ref]

## <a name="getting-started"></a>시작

### <a name="install-the-package"></a>패키지 설치

[NuGet](https://www.nuget.org/)에서 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Azure.Messaging.WebPubSub
```

### <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 서비스 인스턴스.

### <a name="create-and-authenticate-a-webpubsubserviceclient"></a>만들기 및 인증 `WebPubSubServiceClient`

서비스와 상호 작용 하려면 클래스의 인스턴스를 만들어야 `WebPubSubServiceClient` 합니다. 이를 가능하게 하려면 Azure Portal에서 액세스할 수 있는 연결 문자열 또는 키가 필요합니다.

```C# Snippet:WebPubSubAuthenticate
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));
```

## <a name="key-concepts"></a>주요 개념

### <a name="connection"></a>연결

클라이언트 또는 클라이언트 연결이 라고도 하는 연결은 웹 PubSub 서비스에 연결 된 개별 WebSocket 연결을 나타냅니다. 성공적으로 연결되면 Web PubSub 서비스에서 고유한 연결 ID를 이 연결에 할당합니다.

### <a name="hub"></a>허브

허브는 일련의 클라이언트 연결에 대 한 논리적 개념입니다. 일반적으로 하나의 목적에 대해 하나의 허브를 사용합니다(예: 채팅 허브 또는 알림 허브). 클라이언트 연결은 생성 될 때 허브에 연결 되 고 해당 수명 동안 해당 허브에 속합니다. 애플리케이션마다 서로 다른 허브 이름을 사용하여 하나의 Azure Web PubSub 서비스를 공유할 수 있습니다.

### <a name="group"></a>그룹

그룹은 허브에 대 한 연결의 하위 집합입니다. 그룹에 클라이언트 연결을 추가하거나 원하는 경우 그룹에서 클라이언트 연결을 제거할 수 있습니다. 예를 들어 클라이언트가 채팅방에 참가하거나 채팅방에서 나가면 이 채팅방이 그룹일 수 있습니다. 클라이언트는 여러 그룹에 참가할 수 있으며, 한 그룹에 여러 클라이언트가 포함될 수 있습니다.

### <a name="user"></a>사용자

웹 PubSub에 대 한 연결은 한 사용자에 게 속할 수 있습니다. 단일 사용자가 여러 디바이스 또는 여러 브라우저 탭에서 연결된 경우와 같이 사용자에게 여러 연결이 있을 수 있습니다.

### <a name="message"></a>메시지

클라이언트는 연결 되 면 업스트림 응용 프로그램에 메시지를 보내거나 WebSocket 연결을 통해 업스트림 응용 프로그램에서 메시지를 받을 수 있습니다.

## <a name="examples"></a>예제

### <a name="broadcast-a-text-message-to-all-clients"></a>모든 클라이언트에게 문자 메시지 브로드캐스트

```C# Snippet:WebPubSubHelloWorld
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll("Hello World!");
```

### <a name="broadcast-a-json-message-to-all-clients"></a>모든 클라이언트에 JSON 메시지 브로드캐스트

```C# Snippet:WebPubSubSendJson
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll(RequestContent.Create(
        new
        {
            Foo = "Hello World!",
            Bar = 42
        }),
        ContentType.ApplicationJson);
```

### <a name="broadcast-a-binary-message-to-all-clients"></a>모든 클라이언트에 이진 메시지 브로드캐스트

```C# Snippet:WebPubSubSendBinary
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

Stream stream = BinaryData.FromString("Hello World!").ToStream();
serviceClient.SendToAll(RequestContent.Create(stream), ContentType.ApplicationOctetStream);
```

## <a name="troubleshooting"></a>문제 해결

### <a name="setting-up-console-logging"></a>콘솔 로깅 설정
서비스에 대해 수행하는 요청을 더 자세히 알아보려면 쉽게 [콘솔 로깅을 사용하도록 설정](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/core/Azure.Core/samples/Diagnostics.md#logging)할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]


[azure_sub]: https://azure.microsoft.com/free/dotnet/
[samples_ref]: https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/tests/Samples/
[awps_sample]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp