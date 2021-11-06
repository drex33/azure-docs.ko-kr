---
title: Azure Communication Services 대한 개념 설명서 - Network Traversal
titleSuffix: An Azure Communication Services Concept Document
description: Azure Communication Services Network Traversal SDK 및 REST API에 대해 자세히 알아보세요.
author: rahulva
manager: shahen
services: azure-communication-services
ms.author: rahulva
ms.date: 09/20/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 02f52069cc9dea270cc8e6a34096af5bd80159d7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894566"
---
#  <a name="network-traversal-concepts"></a>Network Traversal 개념   

실시간 릴레이는 P2P(피어 투 피어) 연결에 대한 NAT(네트워크 주소 변환) 통과 문제를 해결합니다. 현재 인터넷의 대부분의 디바이스에는 내부 LAN 트래픽(홈 또는 회사 네트워크) 또는 외부에 표시되는 주소(라우터 또는 NAT 게이트웨이)에 사용되는 IP 주소가 있습니다. 인터넷에서 두 디바이스를 연결하려면 외부 주소가 필요하지만 일반적으로 NAT 게이트웨이 뒤의 디바이스에서 사용할 수 없습니다. 연결 문제를 해결하기 위해 다음 프로토콜이 사용됩니다.

  TRAVERS(NAT용 세션 순회 유틸리티)는 디바이스가 인터넷에서 외부 IP를 교환할 수 있도록 하는 프로토콜을 제공합니다. 클라이언트가 서로를 볼 수 있는 경우 연결은 피어 투 피어로 만들 수 있기 때문에 일반적으로 TURN 서비스를 통해 릴레이할 필요가 없습니다. 이 경우 디바이스의 외부 IP에 대한 요청에 응답하는 것이 작업입니다.
  
  TURN(NAT를 중심으로 릴레이를 사용하는 순회)은 상호 표시되는 서버를 통해 두 엔드포인트 간에 데이터를 릴레이하는 데에도 사용할 수 있는 것으로, 이 프로토콜의 확장입니다.
        
## <a name="acs-network-traversal-overview"></a>ACS Network Traversal 개요   

WebRTC(Web Real-Time Technologies)를 사용하면 웹 브라우저가 중간에 게이트웨이를 가질 필요 없이 디바이스 간에 오디오, 비디오 및 데이터를 스트리밍할 수 있습니다. 여기서 일반적인 사용 사례 중 일부는 음성, 비디오, 브로드캐스팅 및 화면 공유입니다. 인터넷에서 두 엔드포인트를 연결하려면 외부 IP 주소가 필요합니다. 외부 IP는 일반적으로 회사 방화벽 뒤에 있는 디바이스에서 사용할 수 없습니다. END(NAT용 세션 순회 유틸리티) 및 TURN(NAT를 중심으로 릴레이를 사용한 순회)과 같은 프로토콜은 엔드포인트가 통신하는 데 사용됩니다.

Azure Communication Service는 실시간 통신 시나리오를 위해 피어 간에 높은 대역폭, 짧은 대기 시간 연결을 제공합니다. ACS Network Traversal Service는 NAT 시나리오에 사용할 TURN 서버를 호스트합니다. Azure Real-Time Relay Service는 기존에 PAaS(Platform as a Service) Azure 제품으로 기존에 있는 수고/TURN 인프라를 노출합니다. 이 서비스는 낮은 수준의재정 및 TURN 서비스를 제공합니다.  그러면 사용자는 릴레이되는 데이터 양에 비례하여 요금이 청구됩니다. 


## <a name="next-steps"></a>다음 단계:

* 인증에 대한 소개는 [Azure Communication Services에 인증](./authentication.md)을 참조하세요.
* 릴레이 후보 획득에 대한 소개는 [액세스 토큰 만들기 및 관리를 참조하세요.](../quickstarts/relay-token.md)
