---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 06b4d9f2830bfd9599aee30c68235d3078eb32fa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734698"
---
**OpenVPN**

* [Azure Virtual WAN에 대해 OpenVPN 클라이언트 구성](../articles/virtual-wan/howto-openvpn-clients.md)
* [Azure AD 인증 - Windows 10](../articles/virtual-wan/openvpn-azure-ad-client.md)
* [Azure AD 인증 - macOS](../articles/virtual-wan/openvpn-azure-ad-client-mac.md)

**IKEv2**

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다.

1. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 **추가 정보** 를 선택한 다음, **실행** 을 선택합니다.

1. 클라이언트 컴퓨터에서 **네트워크 설정** 으로 이동하고 **VPN** 을 선택합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.

1. 연결을 시도하기 전에 먼저 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. Azure 기본 인증서 인증 유형을 사용할 때 인증을 위해 클라이언트 인증서가 필요합니다. 인증서 생성에 대한 자세한 내용은 [인증서 생성](../articles/virtual-wan/certificates-point-to-site.md)을 참조하세요. 클라이언트 인증서를 설치하는 방법은 [클라이언트 인증서 설치](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.
