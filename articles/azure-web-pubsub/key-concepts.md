---
title: Azure Web PubSub의 허브, 그룹, 연결에 대한 기본 개념
description: Azure Web PubSub에서 사용되는 용어를 더 잘 이해합니다.
author: lianwei
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.custom: mode-other
ms.openlocfilehash: 8461950974b61a371816e1f9d3b853d9d4a81cc9
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133059874"
---
# <a name="azure-web-pubsub-basic-concepts"></a>Azure Web PubSub 기본 개념

Azure Web PubSub 서비스를 사용하면 실시간 메시징 웹 애플리케이션을 빌드할 수 있습니다. 클라이언트는 [표준 WebSocket 프로토콜](https://datatracker.ietf.org/doc/html/rfc6455)을 사용하여 서비스에 연결하고, 서비스는 이러한 클라이언트를 관리할 수 있는 [REST API](/rest/api/webpubsub) 및 SDK를 노출합니다.

## <a name="terms"></a>사용 약관

다음은 서비스에서 사용되는 몇 가지 중요한 용어입니다.

[!INCLUDE [Terms](includes/terms.md)]

## <a name="workflow"></a>워크플로

서비스를 사용하는 일반적인 워크플로는 다음과 같습니다.

![Web PubSub 서비스 워크플로를 보여 주는 다이어그램](./media/concept-service-internals/workflow.png)

위의 워크플로 그래프에서 설명한 대로 다음이 구성됩니다.

1. *클라이언트* 는 WebSocket 전송을 사용하여 서비스 `/client` 엔드포인트에 연결합니다. 서비스는 모든 WebSocket 프레임을 구성된 업스트림(서버)으로 전달합니다. WebSocket 연결은 서버가 처리할 사용자 지정 하위 프로토콜과 연결하거나 클라이언트가 직접 게시/구독을 수행할 수 있는 서비스 지원 하위 프로토콜 `json.webpubsub.azure.v1`과 연결할 수 있습니다. 세부 정보는 [클라이언트 프로토콜](concept-service-internals.md#client_protocol)에 설명되어 있습니다.

2. 서비스는 다양한 클라이언트 이벤트에서 **CloudEvents HTTP 프로토콜** 을 사용하여 서버를 호출합니다. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)는 CNCF(Cloud Native Computing Foundation)에서 호스트하는 이벤트의 구조 및 메타데이터 설명에 대한 표준화된 프로토콜 독립적 정의입니다. 세부 정보는 [서버 프로토콜](concept-service-internals.md#server_protocol)에 설명되어 있습니다.

3. 서버는 REST API로 서비스를 호출하여 클라이언트에 메시지를 보내거나 연결된 클라이언트를 관리할 수 있습니다. 세부 정보는 [서버 프로토콜](concept-service-internals.md#server_protocol)에 설명되어 있습니다.
