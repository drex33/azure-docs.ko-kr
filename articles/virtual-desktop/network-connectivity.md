---
title: Azure Virtual Desktop 네트워크 연결 이해
titleSuffix: Azure
description: Azure Virtual Desktop 네트워크 연결에 대해 알아보기
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 409a86883cf010390b3d37c8d3af945ed3702eae
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851853"
---
# <a name="understanding-azure-virtual-desktop-network-connectivity"></a>Azure Virtual Desktop 네트워크 연결 이해

Azure Virtual Desktop은 Azure에서 실행되는 세션 호스트에서 클라이언트 세션을 호스트하는 기능을 제공합니다. Microsoft는 고객을 대신하여 서비스의 일부를 관리하고 클라이언트와 세션 호스트를 연결하기 위한 보안 엔드포인트를 제공합니다. 아래 다이어그램은 Azure Virtual Desktop에서 사용되는 네트워크 연결에 대한 개략적인 개요를 제공합니다.

:::image type="content" source="media/azure-virtual-desktop-network-connections.svg" alt-text="Azure Virtual Desktop 네트워크 연결 다이어그램" lightbox="media/azure-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>세션 연결

Azure Virtual Desktop은 RDP(원격 데스크톱 프로토콜)를 사용하여 네트워크 연결을 통해 원격 표시 및 입력 기능을 제공합니다. RDP는 처음에 Windows NT 4.0 터미널 서버 버전과 함께 릴리스되었으며 Microsoft Windows 및 Windows Server 릴리스마다 지속적으로 발전하고 있습니다. 처음부터 RDP는 기본 전송 스택과는 별도로 개발되었으며 현재는 여러 유형의 전송을 지원합니다.

## <a name="reverse-connect-transport"></a>역방향 연결 전송

Azure Virtual Desktop은 원격 세션을 설정하고 RDP 트래픽을 운반하기 위해 역방향 연결 전송을 사용합니다. 온-프레미스 원격 데스크톱 서비스 배포와 달리, 역방향 연결 전송은 TCP 수신기를 사용하여 들어오는 RDP 연결을 수신하지 않습니다. 대신 HTTPS 연결을 통해 Azure Virtual Desktop 인프라에 대한 아웃바운드 연결을 사용 합니다.

## <a name="session-host-communication-channel"></a>세션 호스트 통신 채널

Azure Virtual Desktop 세션 호스트를 시작할 때 원격 데스크톱 에이전트 로더 서비스는 Azure Virtual Desktop 브로커의 영구 통신 채널을 설정합니다. 이 통신 채널은 보안 TLS(전송 계층 보안) 연결의 맨 위에 계층화되며 세션 호스트와 Azure Virtual Desktop 인프라 간의 서비스 메시지 교환을 위한 버스 역할을 합니다.

## <a name="client-connection-sequence"></a>클라이언트 연결 시퀀스

클라이언트 연결 시퀀스는 아래에 설명되어 있습니다.

1. 사용자는 지원되는 Azure Virtual Desktop 클라이언트를 사용하여 Azure Virtual Desktop 작업 영역을 구독합니다.
2. Azure Active Directory에서 사용자를 인증하고 사용자가 사용할 수 있는 리소스를 열거하는 데 사용되는 토큰을 반환합니다.
3. 클라이언트가 Azure Virtual Desktop 피드 구독 서비스에 토큰을 전달합니다.
4. Azure Virtual Desktop 피드 구독 서비스에서 토큰의 유효성을 검사합니다.
5. Azure Virtual Desktop 피드 구독 서비스는 사용 가능한 데스크톱 및 RemoteApp의 목록을 디지털로 서명된 연결 구성 형식으로 클라이언트에 다시 전달합니다.
6. 클라이언트는 사용할 수 있는 각 리소스에 대한 연결 구성을 .rdp 파일 세트에 저장합니다.
7. 사용자가 연결할 리소스를 선택하면 클라이언트는 연결된 .rdp 파일을 사용하고 가장 가까운 Azure Virtual Desktop 게이트웨이 인스턴스에 대해 보안 TLS 1.2 연결을 설정한 후 연결 정보를 전달합니다.
8. Azure Virtual Desktop 게이트웨이에서 요청의 유효성을 검사하고 Azure Virtual Desktop 브로커에 연결을 오케스트레이션하도록 요청합니다.
9. Azure Virtual Desktop 브로커는 세션 호스트를 식별하고 이전에 설정된 영구 통신 채널을 사용하여 연결을 초기화합니다.
10. 원격 데스크톱 스택은 클라이언트에서 사용하는 것과 동일한 Azure Virtual Desktop 게이트웨이 인스턴스에 대한 TLS 1.2 연결을 시작합니다.
11. 클라이언트 및 세션 호스트가 둘 다 게이트웨이에 연결되면 게이트웨이는 두 엔드포인트 간에 원시 데이터 릴레이를 시작합니다. 그러면 RDP에 대한 기본 역방향 연결 전송이 설정됩니다.
12. 기본 전송이 설정된 후 클라이언트는 RDP 핸드셰이크를 시작합니다.

## <a name="connection-security"></a>연결 보안

TLS 1.2는 클라이언트 및 세션 호스트부터 Azure Virtual Desktop 인프라 구성 요소 사이에서 시작된 모든 연결에 사용됩니다. Azure Virtual Desktop은 [Azure Front Door](../frontdoor/concept-end-to-end-tls.md#supported-cipher-suites)과 동일한 TLS 1.2 암호화를 사용합니다. 클라이언트 컴퓨터와 세션 호스트가 이러한 암호화를 사용할 수 있는지 확인하는 것이 중요합니다.
역방향 연결 전송의 경우 클라이언트 및 세션 호스트는 모두 Azure Virtual Desktop 게이트웨이에 연결합니다. TCP 연결을 설정하고 나면 클라이언트 또는 세션 호스트에서 Azure Virtual Desktop 게이트웨이 인증서의 유효성을 검사합니다.
기본 전송을 설정하면 RDP에서 세션 호스트의 인증서를 사용하여 클라이언트와 세션 호스트 간에 중첩된 TLS 연결을 설정합니다. 기본적으로 RDP 암호화에 사용되는 인증서는 배포하는 동안 OS에서 자체적으로 생성됩니다. 원할 경우 고객은 엔터프라이즈 인증 기관에서 발급한 중앙 관리형 인증서를 배포할 수 있습니다. 인증서 구성에 대한 자세한 내용은 [Windows Server 설명서](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Virtual Desktop의 대역폭 요구 사항에 대한 자세한 내용은 [Azure Virtual Desktop에 대한 RDP(원격 데스크톱 프로토콜) 대역폭 요구 사항 이해](rdp-bandwidth.md)를 참조하세요.
* Azure Virtual Desktop의 QoS(서비스 품질)를 시작하려면 [Azure Virtual Desktop용 QoS(서비스 품질) 구현](rdp-quality-of-service-qos.md)을 참조하세요.
