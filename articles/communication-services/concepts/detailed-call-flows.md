---
title: Azure Communication Services의 전화 흐름 토폴로지
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services의 전화 흐름 토폴로지에 대해 알아봅니다.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 332af91806da4514aad9ccc320a5498539682879
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252245"
---
# <a name="call-flow-topologies"></a>호출 흐름 토폴로지
이 문서에서는 Azure Communication Services 전화 흐름 토폴로지를 설명합니다. 현재 관리하는 네트워크 내에서 Communication Services를 통합하는 엔터프라이즈 고객이라면 이 문서를 검토하는 것이 좋습니다. Communication Services 전화 흐름에 대한 소개는 [전화 흐름 개념 설명서](./call-flows.md)를 참조하세요.

## <a name="background"></a>배경

### <a name="network-concepts"></a>네트워크 개념

전화 흐름 토폴로지를 검토하기 전에, 문서 전체에서 참조되는 몇 가지 용어를 정의하겠습니다.

**고객 네트워크** 에는 고객이 관리하는 모든 네트워크 세그먼트가 포함됩니다. 여기에는 사무실 내부 또는 사무실 간의 유선 및 무선 네트워크, 데이터 센터, 인터넷 서비스 공급자가 포함될 수 있습니다.

일반적으로 고객 네트워크에는 조직의 보안 정책을 적용하는 방화벽 및/또는 프록시 서버를 사용하는 여러 네트워크 경계가 있습니다. 통신 솔루션의 성능과 품질을 최적화할 수 있도록 [포괄적인 네트워크 평가](/microsoftteams/3-envision-evaluate-my-environment)를 수행하는 것이 좋습니다.

**Communication Services 네트워크** 는 Azure Communication Services를 지원하는 네트워크 세그먼트입니다. 이 네트워크는 Microsoft에서 관리하며, 대부분의 고객 네트워크와 가까운 에지를 통해 전 세계에 배포됩니다. 이 네트워크는 전송 릴레이, 그룹 호출용 미디어 처리, 다양한 실시간 미디어 통신을 지원하는 기타 구성 요소를 담당합니다.

### <a name="types-of-traffic"></a>트래픽 유형

Communication Services는 주로 **실시간 미디어** 및 **신호** 라는 두 가지 유형의 트래픽을 기반으로 빌드됩니다.

**실시간 미디어** 는 RTP(Real-time Transport Protocol)를 사용하여 전송됩니다. 이 프로토콜은 오디오, 비디오 및 화면 공유 데이터 전송을 지원합니다. 이 데이터는 네트워크 대기 시간 문제에 민감합니다. TCP 또는 HTTP를 사용하여 실시간 미디어를 전송할 수는 있지만, 고성능 최종 사용자 환경을 지원하기 위해 UDP를 전송 계층 프로토콜로 사용하는 것이 좋습니다. RTP를 통해 전송되는 미디어 페이로드는 SRTP을 사용하여 보호됩니다.

Communication Services 솔루션 사용자는 자신의 클라이언트 디바이스에서 서비스에 연결합니다. 이러한 디바이스와 서버 간의 통신은 **신호** 를 사용하여 처리됩니다. 예를 들어 전화 시작 및 실시간 채팅은 디바이스와 서비스 간의 신호를 통해 지원됩니다. 대부분의 신호 트래픽은 HTTPS REST를 사용하지만, 일부 시나리오에서는 SIP를 신호 트래픽 프로토콜로 사용할 수 있습니다. 이 유형의 트래픽은 대기 시간에 덜 민감하지만, 대기 시간이 짧은 신호는 솔루션 사용자에게 만족스러운 최종 사용자 환경을 제공합니다.

### <a name="interoperability-restrictions"></a>상호 운용성 제한

Communication Services를 통한 미디어 흐름은 다음과 같이 제한됩니다.

**타사 미디어 릴레이.** 타사 미디어 릴레이와의 상호 운용성은 지원되지 않습니다. 미디어 엔드포인트 중 하나가 Communication Services인 경우 미디어는 Microsoft Teams 및 비즈니스용 Skype를 지원하는 미디어 릴레이를 포함하여 Microsoft 네이티브 미디어 릴레이만 트래버스할 수 있습니다.

PSTN을 사용하는 경계의 타사 SBC(Session Border Controller)는 RTP/RTCP 스트림을 종료하고, SRTP를 사용하여 보호되고, 다음 홉에 릴레이하지 않아야 합니다. 흐름을 다음 홉으로 릴레이할 경우 흐름을 이해할 수 없습니다.

**타사 SIP 프록시 서버.** 타사 SBC 및/또는 게이트웨이를 사용하는 Communication Services 신호 SIP 대화 상자는 Teams와 마찬가지로 Microsoft 네이티브 SIP 프록시를 트래버스할 수 있습니다. 타사 SIP 프록시와의 상호 운용성은 지원되지 않습니다.

**타사 B2BUA(또는 SBC).** Communication Services와 PSTN 간의 미디어 흐름은 타사 SBC에 의해 종료됩니다. 타사 SBC가 두 Communication Services 엔드포인트를 중재하는 Communication Services 네트워크 내부에서 타사 SBC와의 상호 운용성은 지원되지 않습니다.

### <a name="unsupported-technologies"></a>지원되지 않는 기술

**VPN 네트워크.** Communication Services는 VPN을 통한 미디어 전송을 지원하지 않습니다. 사용자가 VPN 클라이언트를 사용하는 경우 클라이언트는 [Lync 미디어가 VPN 터널을 우회할 수 있도록 설정](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)에 지정된 대로 VPN이 아닌 연결을 통해 미디어 트래픽을 분할하고 라우팅해야 합니다.

*참고. 제목은 Lync이지만 Azure Communication Services 및 Teams에도 적용됩니다.*

**패킷 쉐이퍼.** 패킷 캡처, 패킷 검사 또는 패킷 쉐이핑 디바이스는 지원되지 않으며 품질을 크게 저하시킬 수 있습니다.

### <a name="call-flow-principles"></a>전화 흐름 원칙

Communication Services 전화 흐름의 기반이 되는 네 가지 일반적인 원칙이 있습니다.

* **Communication Services 그룹 전화의 첫 번째 참가자에 따라 전화가 호스트되는 지역이 결정됩니다**. 일부 토폴로지에서는 이 규칙의 예외가 있으며, 아래에 설명되어 있습니다.
* **Communication Services 전화를 지원하는 데 사용되는 미디어 엔드포인트는 미디어 처리 요구에 따라 선택** 되며, 전화 참가자 수의 영향을 받지 않습니다. 예를 들어 지점 간 전화는 클라우드에서 미디어 엔드포인트를 사용하여 대화 내용 기록 또는 녹음을 위한 미디어를 처리할 수 있는 반면, 참가자가 두 명인 전화는 미디어 엔드포인트를 사용할 수 있습니다. 그룹 전화는 혼합 및 라우팅 용도로 미디어 엔드포인트를 사용합니다. 이 엔드포인트는 전화가 호스트되는 지역에 따라 선택됩니다. 클라이언트에서 미디어 엔드포인트로 전송된 미디어 트래픽은 직접 라우팅될 수도 있고, 고객 네트워크 방화벽 제한에 따라 Azure에서 전송 릴레이를 사용할 수도 있습니다.
* 피어 투 피어 전화에 대한 미디어 트래픽은 통화 시 클라우드에서 미디어 엔드포인트가 필요하지 않은 것으로 가정하고 **사용 가능한 가장 직접적인 경로를 사용** 합니다. 사람들이 선호하는 경로는 원격 피어(클라이언트)에 대한 직접 경로입니다. 직접 경로를 사용할 수 없는 경우 하나 이상의 전송 릴레이에서 트래픽을 릴레이합니다. 미디어 트래픽은 패킷 쉐이퍼, VPN 서버 또는 처리를 지연하고 최종 사용자 환경을 저하시킬 수 있는 기타 함수처럼 작동하는 서버를 가로지르면 안 됩니다.
* **신호 트래픽은 항상 사용자와 가장 가까운 서버로 이동합니다.**

선택한 미디어 경로에 대한 자세한 내용은 [전화 흐름 개념 설명서](./call-flows.md)를 참조하세요.


## <a name="call-flows-in-various-topologies"></a>다양한 토폴로지의 전화 흐름

### <a name="communication-services-internet"></a>Communication Services(인터넷)

이 토폴로지는 Azure 직접 라우팅과 같은 온-프레미스 배포 없이 클라우드에서 Communication Services를 사용하는 고객에 의해 사용됩니다. 이 토폴로지에서는 Communication Services로 들어오고 나가는 트래픽이 인터넷을 통해 흐릅니다.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure Communication Services 토폴로지":::

*그림 1 - Communication Services 토폴로지*

위 다이어그램의 화살표 방향은 엔터프라이즈 경계의 연결에 영향을 주는 통신의 시작 방향을 나타냅니다. 미디어에 사용되는 UDP의 경우 첫 번째 패킷이 반대 방향으로 흐를 수 있지만, 다른 방향의 패킷이 흐를 때까지 이러한 패킷이 차단될 수 있습니다.

흐름 설명:
* 흐름 2* – 사용자가 Communication Services 환경의 일부로 고객 네트워크에서 인터넷으로 시작한 흐름을 나타냅니다. 이러한 흐름의 예로는 DNS 및 피어 투 피어 미디어 전송이 있습니다.
* 흐름 2 – 원격 모바일 Communication Services 사용자가 고객 네트워크에 대한 VPN을 사용하여 시작한 흐름을 나타냅니다.
* 흐름 3 – 원격 모바일 Communication Services 사용자가 Communication Services 엔드포인트로 시작한 흐름을 나타냅니다.
* 흐름 4 – 고객 네트워크의 사용자가 Communication Services로 시작한 흐름을 나타냅니다.
* 흐름 5 – 한 Communication Services 사용자와 고객 네트워크의 또 다른 Communication Services 사용자 간의 피어 투 피어 미디어 흐름을 나타냅니다.
* 흐름 6 – 인터넷을 통한 원격 모바일 Communication Services 사용자와 또 다른 원격 모바일 Communication Services 사용자 간의 피어 투 피어 미디어 흐름을 나타냅니다.

### <a name="use-case-one-to-one"></a>사용 사례: 일 대 일

일대일 전화는 호출자가 로컬, 릴레이 및 재귀(릴레이에 표시되는 클라이언트의 공용 IP 주소) 후보를 비롯한 IP 주소/포트로 구성된 후보 세트를 가져오는 공통 모델을 사용합니다. 호출자가 이러한 후보를 호출된 파티에 보내면 호출된 파티 역시 비슷한 후보 세트를 가져와서 호출자에게 보냅니다. STUN 연결 확인 메시지는 작동하는 호출자/호출된 파티 미디어 경로를 찾고 가장 적합한 작업 경로를 선택하는 데 사용됩니다. 그런 다음, 선택한 후보 쌍을 사용하여 미디어(즉, SRTP를 사용하여 보호되는 RTP/RTCP 패킷)가 전송됩니다. 전송 릴레이는 Azure Communication Services의 일부로 배포됩니다.

로컬 IP 주소/포트 후보 또는 재귀적 후보가 연결된 경우 미디어에 대해 클라이언트 간의 직접 경로(또는 NAT 사용)가 선택됩니다. 두 클라이언트가 모두 고객 네트워크에 있으면 직접 경로를 선택해야 합니다. 이렇게 하려면 고객 네트워크 내에서 직접 UDP 연결이 필요합니다. 두 클라이언트가 모두 nomadic 클라우드 사용자이면 NAT/방화벽에 따라 미디어에서 직접 연결을 사용할 수 있습니다.

한 클라이언트가 고객 네트워크의 내부에 있고 다른 클라이언트는 외부(예: 모바일 클라우드 사용자)에 있는 경우 로컬 또는 재귀 후보 간의 직접 연결이 작동하지 않을 가능성이 높습니다. 이 경우 클라이언트의 전송 릴레이 후보 중 하나를 사용하는 옵션이 있습니다. 예를 들어 내부 클라이언트가 Azure의 전송 릴레이에서 릴레이 후보를 획득한 경우 외부 클라이언트는 STUN/RTP/RTCP 패킷을 전송 릴레이로 보낼 수 있어야 합니다. 또 다른 옵션은 내부 클라이언트가 모바일 클라우드 클라이언트에서 얻은 릴레이 후보로 보내는 것입니다. 미디어에는 UDP 연결을 사용하는 것이 좋지만 TCP도 지원됩니다.

**대략적인 단계:**
1.  Communication Services 사용자 A가 흐름 2를 사용하여 URL 도메인 이름을 확인합니다(DNS).
2.  사용자 A가 흐름 4를 사용하여 Teams 전송 릴레이에서 미디어 릴레이 포트를 할당합니다.
3.  Communication Services 사용자 A가 흐름 4를 사용하여 ICE 후보가 포함된 Communication Services "초대"를 보냅니다.
4.  Communication Services는 흐름 4를 사용하여 사용자 B에게 알립니다.
5.  사용자 B가 흐름 4를 사용하여 Teams 전송 릴레이에서 미디어 릴레이 포트를 할당합니다.
6.  사용자 B가 흐름 4를 사용하여 ICE 후보가 포함된 "답변"을 보냅니다. 그러면 이 답변은 흐름 4를 사용하여 다시 사용자 A에게 전달됩니다.
7.  사용자 A와 사용자 B가 ICE 연결 테스트를 호출하면 사용 가능한 최적의 미디어 경로가 선택됩니다(다양한 사용 사례는 아래 다이어그램 참조).
8.  두 사용자는 모두 흐름 4를 사용하여 Communication Services에 원격 분석 데이터를 보냅니다.

### <a name="customer-network-intranet"></a>고객 네트워크(인트라넷)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="고객 네트워크 내의 트래픽 흐름":::

*그림 2 - 고객 네트워크 내부*

7단계에서 피어 투 피어 미디어 흐름 5가 선택됩니다.

이 미디어 전송은 양방향입니다. 흐름 5의 방향은 연결 관점에서 볼 때 한 쪽이 통신을 시작한다는 것을 나타냅니다. 여기서는 두 엔드포인트가 모두 고객 네트워크 내부에 있기 때문에 어떤 방향을 사용해도 문제가 되지 않습니다.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>고객 네트워크에서 외부 사용자로(Teams 전송 릴레이를 통해 릴레이되는 미디어)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="릴레이를 통한 일대일 전화 흐름":::

*그림 3 - 고객 네트워크에서 외부 사용자로(Azure 전송 릴레이를 통해 릴레이되는 미디어)*

7단계에서 흐름 4(고객 네트워크에서 Communication Services로) 및 흐름 3(원격 모바일 Communication Services 사용자에서 Azure Communication Services로)이 선택됩니다.

두 흐름은 Azure 내부에서 Teams 전송 릴레이에 의해 릴레이됩니다.

이 미디어 전송은 양방향입니다. 방향은 연결 관점에서 볼 때 어느 쪽에서 통신을 시작하는지 나타냅니다. 여기서는 흐름이 다른 전송 프로토콜 및 주소를 통해 신호 및 미디어에 사용됩니다.

### <a name="customer-network-to-external-user-direct-media"></a>고객 네트워크에서 외부 사용자로(직접 미디어)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="외부 사용자와 일대일 전화 흐름":::

*그림 4 - 고객 네트워크에서 외부 사용자로(직접 미디어)*

7단계에서 흐름 2(인터넷을 통해 고객 네트워크에서 클라이언트의 피어로)가 선택됩니다.

원격 모바일 사용자를 사용하는 직접 미디어(Azure를 통해 릴레이되지 않음)는 선택 사항입니다. 즉, Azure의 전송 릴레이를 통해 미디어 경로를 적용하도록 이 경로를 차단할 수 있습니다.

이 미디어 전송은 양방향입니다. 원격 모바일 사용자로 흐르는 흐름 2의 방향은 연결 관점에서 볼 때 한 쪽이 통신을 시작한다는 것을 나타냅니다.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>VPN 사용자에서 내부 사용자로(Teams 전송 릴레이를 통해 릴레이되는 미디어)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="릴레이를 통해 VPN 사용자와 일대일 전화 흐름":::

*그림 5 - VPN 사용자에서 외부 사용자로(Azure Relay에서 릴레이된 미디어*

사용자 네트워크에 대한 VPN 간 신호는 흐름 2*를 사용합니다. 고객 네트워크와 Azure 간의 신호는 흐름 4를 사용합니다. 그러나 미디어는 VPN을 우회하고 흐름 3 및 흐름 4를 사용하여 Azure 미디어 릴레이를 통해 라우팅됩니다.

### <a name="vpn-user-to-internal-user-direct-media"></a>VPN 사용자에서 내부 사용자로(직접 미디어)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="직접 미디어를 통해 VPN 사용자와 일대일 전화 흐름(내부 사용자)":::

*그림 6 - VPN 사용자에서 내부 사용자로(직접 미디어)*

고객 네트워크에 대한 VPN 간 신호는 흐름 2를 사용합니다. 고객 네트워크와 Azure 간의 신호는 흐름 4를 사용합니다. 그러나 미디어는 VPN을 우회하고 흐름 2를 사용하여 고객 네트워크에서 인터넷으로 라우팅됩니다.

이 미디어 전송은 양방향입니다. 원격 모바일 사용자로 흐르는 흐름 2의 방향은 연결 관점에서 볼 때 한 쪽이 통신을 시작한다는 것을 나타냅니다.

### <a name="vpn-user-to-external-user-direct-media"></a>VPN 사용자에서 외부 사용자로(직접 미디어)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="직접 미디어를 통해 VPN 사용자와 일대일 전화 흐름(외부 사용자)":::

*그림 7 - VPN 사용자에서 외부 사용자로(직접 미디어)*

고객 네트워크에 대한 VPN 사용자 간 신호는 흐름 2* 및 흐름 4를 사용하여 Azure로 라우팅됩니다. 그러나 미디어는 VPN을 우회하고 흐름 6을 사용하여 라우팅됩니다.

이 미디어 전송은 양방향입니다. 원격 모바일 사용자로 흐르는 흐름 6의 방향은 연결 관점에서 볼 때 한 쪽이 통신을 시작한다는 것을 나타냅니다.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>사용 사례: Communication Services 트렁크를 통해 Communication Services 클라이언트에서 PSTN으로

Communication Services를 사용하면 PSTN(Public Switched Telephone Network)에서 호출을 배치하고 받을 수 있습니다. Communication Services에서 제공하는 전화 번호를 사용하여 PSTN 트렁크가 연결된 경우에는 특별한 연결 요구 사항이 없습니다. 자체 온-프레미스 PSTN 트렁크를 Azure Communication Services에 연결하려면 Azure 직접 라우팅(CY2021에서 사용 가능)을 사용하면 됩니다.

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="PSTN 참가자와 일대일 전화":::

*그림 8 - Azure 트렁크를 통해 Communication Services 사용자에서 PSTN으로*

여기서는 고객 네트워크에서 Azure로의 신호 및 미디어에 흐름 4를 사용합니다.

### <a name="use-case-communication-services-group-calls"></a>사용 사례: Communication Services 그룹 전화

VBSS(오디오/비디오/화면 공유) 서비스는 Azure Communication Services에 포함되어 있습니다. 고객 네트워크에서 연결할 수 있어야 하고 Nomadic 클라우드 클라이언트에서 연결할 수 있어야 하는 공용 IP 주소를 사용합니다. 각 클라이언트/엔드포인트가 서비스에 연결할 수 있어야 합니다.

내부 클라이언트는 일대일 전화에 대해 설명된 내용과 동일한 방식으로 로컬, 재귀 및 릴레이 후보를 획득합니다. 클라이언트는 초대를 통해 서비스에 이러한 후보를 보냅니다. 서비스는 공개적으로 연결할 수 있는 IP 주소를 사용하기 때문에 릴레이를 사용하지 않고, 로컬 IP 주소 후보로 응답합니다. 클라이언트와 서비스는 일대일 전화에 대해 설명한 내용과 동일한 방식으로 연결을 확인합니다.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="OACS 그룹 전화":::

*그림 9 - Communication Services 그룹 전화*

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [전화 시작](../quickstarts/voice-video-calling/getting-started-with-calling.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [전화 유형](../concepts/voice-video-calling/about-call-types.md)에 대한 자세한 정보
- [클라이언트-서버 아키텍처](./client-and-server-architecture.md)에 대한 자세한 정보