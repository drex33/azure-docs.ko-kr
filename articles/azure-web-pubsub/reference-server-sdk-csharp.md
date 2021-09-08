---
title: 참조 - Azure Web PubSub 서비스용 .NET 서버 SDK
description: 참조는 Azure Web PubSub 서비스용 .NET 서버 SDK에 대해 설명합니다.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4bb4c5d90958a31e26c39ce9d0fe7e22bec769e2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117079"
---
# <a name="net-server-sdk-for-azure-web-pubsub-service"></a>Azure Web PubSub 서비스용 .NET 서버 SDK

이 라이브러리는 다음 작업을 수행하는 데 사용할 수 있습니다. 여기에 사용된 용어에 대한 자세한 내용은 [주요 개념](#key-concepts) 섹션에 설명되어 있습니다.

- 허브 및 그룹에 메시지를 보냅니다. 
- 특정 사용자 및 연결에 메시지를 보냅니다.
- 사용자와 연결을 그룹으로 구성합니다.
- 연결 종료
- 기존 연결에 대한 권한 부여, 철회 및 확인

[소스 코드][code] |
[패키지][package] |
[API 참조 설명서][api] |
[제품 설명서](https://aka.ms/awps/doc) |
[샘플][samples_ref]

## <a name="getting-started"></a>시작
### <a name="install-the-package"></a>패키지 설치

[NuGet](https://www.nuget.org/)에서 클라이언트 라이브러리를 설치합니다.

```PowerShell
dotnet add package Azure.Messaging.WebPubSub --prerelease
```

### <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독][azure_sub].
- 기존 Azure Web PubSub 서비스 인스턴스.

### <a name="authenticate-the-client"></a>클라이언트 인증

서비스와 상호 작용하려면 WebPubSubServiceClient 클래스의 인스턴스를 만들어야 합니다. 이를 가능하게 하려면 Azure Portal에서 액세스할 수 있는 연결 문자열 또는 키가 필요합니다.

### <a name="create-a-webpubsubserviceclient"></a>인증 요청을 처리하는 데 사용하는 `WebPubSubServiceClient`

```csharp
var serviceClient = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", new AzureKeyCredential("<access-key>"));
```

## <a name="key-concepts"></a>주요 개념

[!INCLUDE [Termsc](includes/terms.md)]

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
서비스에 대해 수행하는 요청을 더 자세히 알아보려면 쉽게 [콘솔 로깅을 사용하도록 설정](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)할 수도 있습니다.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
[code]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/Azure.Messaging.WebPubSub/src
[package]: https://www.nuget.org/packages/Azure.Messaging.WebPubSub
[api]: /dotnet/api/azure.messaging.webpubsub

## <a name="next-steps"></a>다음 단계

[!INCLUDE [next step](includes/include-next-step.md)]
