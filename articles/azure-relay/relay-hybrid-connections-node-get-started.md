---
title: Azure Relay 하이브리드 연결 -Node에서 WebSocket
description: Azure Relay 하이브리드 연결 WebSockets에 대한 Node.js 콘솔 애플리케이션 작성
ms.topic: conceptual
ms.date: 06/23/2021
ms.custom: devx-track-js
ms.openlocfilehash: ceca28f7873757aafd72e72c9ad8c1c64035ab87
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666105"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Node.js에서 Relay 하이브리드 연결 WebSockets 시작

[!INCLUDE [relay-selector-hybrid-connections](./includes/relay-selector-hybrid-connections.md)]

이 빠른 시작에서는 Azure Relay에서 하이브리드 연결 WebSockets를 사용하여 메시지를 보내고 받는 Node.js 받는 사람 및 보낸 사람 애플리케이션을 만듭니다. Azure Relay에 대한 일반적인 내용은 [Azure Relay](relay-what-is-it.md)를 참조하세요. 

이 빠른 시작에서 수행하는 단계는 다음과 같습니다. 

1. Azure Portal을 사용하여 Relay 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 해당 네임스페이스에 하이브리드 연결을 만듭니다.
3. 메시지를 수신하는 서버(수신기) 콘솔 애플리케이션을 작성합니다.
4. 메시지를 보내는 클라이언트(발신자) 콘솔 애플리케이션을 작성합니다.
5. 애플리케이션을 실행합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [Node.js](https://nodejs.org/en/).
- Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

## <a name="create-a-namespace"></a>네임스페이스 만들기
[!INCLUDE [relay-create-namespace-portal](./includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>하이브리드 연결 만들기
[!INCLUDE [relay-create-hybrid-connection-portal](./includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>서버 애플리케이션(수신기) 만들기
릴레이에서 메시지를 수신 대기하고 받으려면 Node.js 콘솔 애플리케이션을 작성합니다.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](./includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>클라이언트 애플리케이션(보낸 사람) 만들기
릴레이에 메시지를 보내려면 Node.js 콘솔 애플리케이션을 작성합니다.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](./includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>애플리케이션 실행

1. Node.js 명령 프롬프트 형식 `node listener.js`의 서버 애플리케이션을 실행합니다.
2. Node.js 명령 프롬프트 형식 `node sender.js`의 클라이언트 애플리케이션을 실행하고 일부 텍스트를 입력합니다.
3. 서버 애플리케이션 콘솔이 클라이언트 애플리케이션에 입력된 텍스트를 출력하는지 확인합니다.

    ![서버 및 클라이언트 애플리케이션을 모두 테스트하는 콘솔 창.](./media/relay-hybrid-connections-node-get-started/running-applications.png)

축하합니다. Node.js를 사용하여 엔드투엔드 하이브리드 연결 애플리케이션을 만들었습니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 메시지를 보내고 받는 데 WebSockets를 사용한 Node.js 클라이언트 및 서버 애플리케이션을 만들었습니다. Azure Relay의 하이브리드 연결 기능은 HTTP를 사용하여 메시지를 보내고 받을 수도 있도록 지원합니다. Azure Relay 하이브리드 연결에 HTTP를 사용하는 방법에 대한 자세한 내용은 [Node.js HTTP 빠른 시작](relay-hybrid-connections-http-requests-node-get-started.md)을 참조하세요.

이 빠른 시작에서는 클라이언트 및 서버 애플리케이션을 만드는 데 Node.js를 사용했습니다. .NET Framework를 사용하여 클라이언트 및 서버 애플리케이션을 작성하는 방법은 [.NET WebSockets 빠른 시작](relay-hybrid-connections-dotnet-get-started.md) 또는 [.NET HTTP 빠른 시작](relay-hybrid-connections-http-requests-dotnet-get-started.md)을 참조하세요.


