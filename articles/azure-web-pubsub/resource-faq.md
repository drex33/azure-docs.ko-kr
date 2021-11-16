---
title: Azure Web PubSub 서비스 FAQ
description: Azure Web PubSub 서비스에 대한 질문과 대답을 가져옵니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: dc8dd5e25a6b6aeb94d674156af2edd9dbcd008d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345606"
---
# <a name="azure-web-pubsub-service-faq"></a>Azure Web PubSub 서비스 FAQ

Azure Web PubSub 서비스의 FAQ입니다. 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Azure Web PubSub 서비스가 프로덕션에 사용할 준비가 되었나요?
예, Azure Web PubSub 서비스는 일반 공급됩니다.

## <a name="how-do-i-choose-between-azure-signalr-service-and-azure-web-pubsub-service"></a>Azure SignalR Service와 Azure Web PubSub 서비스 중에서 선택하려면 어떻게 하나요?

[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service) 및 [Azure Web PubSub 서비스](https://azure.microsoft.com/services/web-pubsub)는 고객이 대규모 및 고가용성으로 실시간 웹 애플리케이션을 쉽게 빌드하고, 메시징 인프라를 관리하는 대신 비즈니스 논리에 집중할 수 있도록 도와줍니다. 일반적으로 SignalR 라이브러리를 사용하여 실시간 애플리케이션을 빌드하는 경우 Azure SignalR Service를 선택할 수 있습니다. 대신 WebSocket 및 게시-구독 패턴을 기준으로 실시간 애플리케이션을 빌드하는 일반 솔루션을 찾는 경우 Azure Web PubSub 서비스를 선택할 수 있습니다. Azure Web PubSub 서비스가 Azure SignalR Service의 대안은 **아닙니다**. 이 서비스는 다양한 시나리오를 대상으로 합니다.

다음과 같은 경우 Azure SignalR Service가 더 적합합니다.  

- 이미 ASP.NET 또는 ASP.NET Core SignalR을 사용하고 있으며, 주로 .NET을 사용하거나 .NET 에코시스템(예: Blazor)과 통합해야 합니다.
- 플랫폼에 사용할 수 있는 SignalR 클라이언트가 있습니다. 
- 다양한 호출 패턴(RPC 및 스트리밍), 전송(WebSocket, 서버 전송 이벤트 및 긴 폴링)을 지원하고 사용자 대신 연결 수명을 관리하는 클라이언트에 대한 설정된 프로토콜이 필요합니다. 

다음과 같은 상황에서는 Azure Web PubSub 서비스가 더 적합합니다.  

- WebSocket 기술을 기준으로 실시간 애플리케이션을 빌드하거나 WebSocket을 통해 게시-구독해야 합니다.
- 사용자 고유의 하위 프로토콜을 빌드하거나 WebSocket을 통해 기존 고급 프로토콜(예: MQTT, WebSocket을 통한 AMQP)을 사용하려고 합니다. 
- 예를 들어, 구성된 백 엔드를 거치지 않고 클라이언트에 메시지를 보내는 경량 서버를 찾고 있습니다.  

##  <a name="where-does-my-data-reside"></a>데이터가 어디에 상주하나요?

Azure Web PubSub 서비스는 데이터 프로세서 서비스로 작동합니다. 고객 콘텐츠를 저장하지 않으며 데이터 상주는 디자인에 의해 포함됩니다. 진단을 위해 Azure Storage와 같은 다른 Azure 서비스와 Azure Web PubSub 서비스를 함께 사용하는 경우 Azure 지역에서 데이터 상주를 유지하는 방법에 대한 지침은 [이 백서](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)를 참조하세요.
