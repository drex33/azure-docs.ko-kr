---
title: Azure Communication Services의 전화 흐름
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services의 전화 흐름에 대해 알아봅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 70eec46f882e9aedb8a30c6dede2f0b4a420c553
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672308"
---
# <a name="call-flow-basics"></a>호출 흐름 기본 사항

아래 섹션에서는 Azure Communication Services의 전화 흐름에 대한 개요를 제공합니다. 신호와 미디어 흐름은 사용자가 수행하는 전화 종류에 따라 달라집니다. 전화 종류의 예로는 일대일 VoIP, 일대일 PSTN, VoIP 및 PSTN 연결 참가자 조합으로 구성되는 그룹 전화가 있습니다. [전화 종류](./voice-video-calling/about-call-types.md)를 검토하세요.

## <a name="about-signaling-and-media-protocols"></a>신호 및 미디어 프로토콜 정보

피어 투 피어 또는 그룹 전화를 걸 때 내부적으로 두 개의 프로토콜이 사용되는데, 하나는 신호용 HTTP(REST)이고 다른 하나는 미디어용 SRTP입니다.

SDK 간 또는 SDK와 클라이언트 라이브러리와 Communication Services 신호 컨트롤러 간 신호는 HTTP REST(TLS)를 통해 처리됩니다. RTP(실시간 미디어 트래픽)의 경우 UDP(사용자 데이터그램 프로토콜)를 사용하는 것이 좋습니다. 방화벽에서 UDP 사용을 차단하는 경우 SDK는 미디어에 TCP(Transmission Control Protocol)를 사용합니다.

다양한 시나리오에서 신호 및 미디어 프로토콜을 검토하겠습니다.

## <a name="call-flow-cases"></a>전화 흐름 사례

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>사례 1: 두 디바이스를 직접 연결할 수 있는 VoIP

일대일 VoIP 또는 영상 통화에서 트래픽에 좋은 것은 가장 직접적인 경로입니다. “직접적인 경로”란 두 SDK가 서로 직접적으로 연결할 수 있는 경우 직접 연결을 설정한다는 의미입니다. 이는 두 SDK가 동일한 서브넷(예: 서브넷 192.168.1.0/24)에 있는 경우 또는 서로 인식할 수 있는(서브넷 10.10.0.0/16 및 192.168.1.0/24에 있는 SDK가 서로 연결될 수 있음) 서브넷 상에 두 디바이스가 있는 경우 일반적으로 가능합니다.

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="사용자와 Communication Services 간의 직접 VOIP 호출을 보여주는 다이어그램":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>사례 2: 디바이스 간의 직접 연결은 불가능하지만 NAT 디바이스 간 연결이 가능한 VoIP

두 디바이스가 서로 연결할 수 없는 서브넷에 있지만(예: Alice는 커피숍에서 작업하고 Bob은 홈 오피스에서 작업) NAT 디바이스 간 연결이 가능한 경우 클라이언트 쪽 SDK는 NAT 디바이스를 통해 연결을 설정합니다.

Alice의 경우 커피숍의 NAT이고 Bob의 경우 홈 오피스의 NAT가 됩니다. Alice의 디바이스는 NAT의 외부 주소를 보내고 Bob의 디바이스도 마찬가지입니다. SDK는 Azure Communication Services에서 무료로 제공하는 STUN(Session Traversal Utilities for NAT) 서비스에서 외부 주소를 알아봅니다. Alice와 Bob 간의 핸드셰이크를 처리하는 논리는 Azure Communication Services에서 제공한 SDK에 포함되어 있습니다. (추가 구성이 필요 없음)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="STUN 연결을 활용하는 VOIP 호출을 보여주는 다이어그램":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>사례 3: 직접 연결과 NAT 연결 모두 사용할 수 없는 VoIP

클라이언트 디바이스 중 하나 또는 둘 다 대칭 NAT 뒤에 있는 경우 두 SDK 간에 미디어를 릴레이할 별도의 클라우드 서비스가 필요합니다. 이 서비스를 TURN(Traversal Using Relays around NAT)이라고 하며 Communication Services에서도 제공합니다. Communication Services Calling SDK는 검색된 네트워크 조건에 따라 자동으로 TURN 서비스를 사용합니다. Microsoft의 TURN 서비스 사용 요금은 별도로 청구됩니다.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="TURN 연결을 활용하는 VOIP 호출을 보여주는 다이어그램":::

### <a name="case-4-group-calls-with-pstn"></a>사례 4: PSTN을 사용한 그룹 통화

PSTN 전화에 대한 신호와 미디어는 모두 Azure Communication Services 전화 통신 리소스를 사용합니다. 이 리소스는 다른 이동 통신 사업자와 상호 연결되어 있습니다.

PSTN 미디어 트래픽은 미디어 프로세서라고 하는 구성 요소를 통해 흐릅니다.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Communication Services를 사용한 PSTN 그룹 통화를 보여주는 다이어그램":::

> [!NOTE]
> 미디어 처리에 익숙한 분들을 위해, Microsoft의 미디어 프로세서 역시 [RFC 3261 SIP: 세션 시작 프로토콜](https://tools.ietf.org/html/rfc3261)에 정의된 대로 Microsoft와 이동 통신 사업자 네트워크 간의 호출을 처리할 때 코덱을 변환할 수 있는 백투백 사용자 에이전트입니다. Azure Communication Services 신호 컨트롤러는 동일한 RFC에 따라 Microsoft에서 SIP 프록시를 구현한 것입니다.

그룹 통화의 경우 미디어 및 신호는 항상 Azure Communication Services 백 엔드를 통해 흐릅니다. 모든 참가자의 오디오 및/또는 비디오는 미디어 프로세서 구성 요소에 혼합됩니다. 그룹 통화의 모든 멤버는 오디오 및/또는 비디오 스트림을 미디어 프로세서로 보내고, 미디어 프로세서는 혼합된 미디어 스트림을 반환합니다.

그룹 통화의 기본 RTP(실시간 프로토콜)는 UDP(사용자 데이터그램 프로토콜)입니다.

> [!NOTE]
> 미디어 프로세서는 MCU(Multipoint Control Unit) 또는 SFU(Selective Forwarding Unit)로 작동할 수 있습니다.

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Communication Services 내의 UDP 미디어 프로세스 흐름을 보여주는 다이어그램":::

방화벽 제한으로 인해 SDK가 미디어에 UDP를 사용할 수 없는 경우 TCP(전송 제어 프로토콜)를 사용하려고 시도합니다. 미디어 프로세서 구성 요소에는 UDP가 필요하므로, 위와 같은 경우 TCP를 UDP로 변환하기 위해 Communication Services TURN 서비스가 그룹 통화에 추가됩니다 이 경우 TURN 기능을 수동으로 해제하지 않으면 TURN 요금이 부과됩니다.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Communication Services 내의 TCP 미디어 프로세스 흐름을 보여주는 다이어그램":::

### <a name="case-5-communication-services-sdk-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>사례 5: 예약된 Teams 미팅의 Communication Services SDK 및 Microsoft Teams

신호는 신호 컨트롤러를 통해 흐릅니다. 미디어는 미디어 프로세서를 통해 흐릅니다. 신호 컨트롤러와 미디어 프로세서는 Communication Services와 Microsoft Teams 간에 공유됩니다.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="예약된 Teams 미팅의 Communication Services SDK 및 Teams 클라이언트를 보여 주는 다이어그램.":::



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [전화 시작](../quickstarts/voice-video-calling/getting-started-with-calling.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [전화 유형](../concepts/voice-video-calling/about-call-types.md)에 대한 자세한 정보
- [클라이언트-서버 아키텍처](./client-and-server-architecture.md)에 대한 자세한 정보
- [호출 흐름 토폴로지](./detailed-call-flows.md)에 대해 알아보기
