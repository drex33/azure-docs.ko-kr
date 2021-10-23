---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 966232f893ef404c3c4d6e5d3bc4929f74d7bd62
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215143"
---
VNet 뒤에서 보호되는 작업 영역에 연결하려면 다음 방법 중 하나를 사용합니다.

* [Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md) - 프라이빗 연결을 통해 VNet에 온-프레미스 네트워크를 연결합니다. 공용 인터넷을 통해 연결됩니다. 사용할 수 있는 VPN 게이트웨이의 두 가지 유형은 다음과 같습니다.

    * [지점 및 사이트 간](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md): 각 클라이언트 컴퓨터는 VPN 클라이언트를 사용하여 VNet에 연결합니다.
    * [사이트 간](../articles/vpn-gateway/tutorial-site-to-site-portal.md): VPN 디바이스는 VNet을 온-프레미스 네트워크에 연결합니다.

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/) - 프라이빗 연결을 통해 온-프레미스 네트워크를 클라우드에 연결합니다. 연결 공급자를 사용하여 연결합니다.
* [Azure Bastion](../articles/bastion/bastion-overview.md) - 이 시나리오에서는 VNet 내에 Azure 가상 머신(점프 상자라고도 함)을 만듭니다. 그런 다음, Azure Bastion을 사용하여 VM에 연결합니다. Bastion을 사용하면 로컬 웹 브라우저에서 RDP 또는 SSH 세션을 사용하여 VM에 연결할 수 있습니다. 그런 다음, 점프 상자를 개발 환경으로 사용합니다. VNet 내부에 있기 때문에 작업 영역에 직접 액세스할 수 있습니다. 점프 상자를 사용하는 예제는 [자습서: 보안 작업 영역 만들기](../articles/machine-learning/tutorial-create-secure-workspace.md)를 참조하세요.

> [!IMPORTANT]
> __VPN Gateway__ 또는 __ExpressRoute__ 를 사용하는 경우 온-프레미스 리소스와 VNet 간에 이름 확인이 작동하는 방식을 계획해야 합니다. 자세한 내용은 [사용자 지정 DNS 서버 사용](../articles/machine-learning/how-to-custom-dns.md)을 참조하세요.