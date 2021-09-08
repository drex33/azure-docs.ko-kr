---
title: Azure Route Server 문제 해결
description: Azure Route Server에 대한 문제를 해결하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 08/30/2021
ms.author: duau
ms.openlocfilehash: 96e71dbd8a03e66644f0dc754d448a345978d940
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255665"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Azure Route Server 문제 해결

> [!IMPORTANT]
> Azure Route Server(미리 보기)는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="connectivity-issues"></a>연결 문제

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>NVA가 Azure Route Server에 기본 경로(0.0.0.0/0)를 보급한 후 인터넷 연결이 끊기는 이유는 무엇인가요?
NVA에서 기본 경로를 보급하는 경우 Azure Route Server는 NVA 자체를 포함하여 가상 네트워크의 모든 VM에 대해 해당 경로를 프로그래밍합니다. 해당 기본 경로는 NVA를 모든 인터넷에 바인딩된 트래픽에 대한 다음 홉으로 설정합니다. NVA에 인터넷 연결이 필요한 경우 NVA에서 해당 기본 경로를 재정의하고 UDR을 NVA가 호스트되는 서브넷에 연결하도록 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md)를 구성해야 합니다(아래 예제 참조). 그렇지 않으면 NVA 호스트 머신은 NVA에서 보낸 트래픽을 포함하여 인터넷에 바인딩된 트래픽을 NVA에 다시 전송합니다.

| 경로 | 다음 홉 |
|-------|----------|
| 0.0.0.0/0 | 인터넷 |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>NVA에서 Azure Route Server의 BGP 피어 IP로 ping할 수 있는데 둘 사이에서 BGP 피어링을 설정한 후에는 더 이상 같은 IP를 ping할 수 없는 이유는 무엇인가요? BGP 피어링이 중단되는 이유는 무엇인가요?

일부 NVA에서는 Azure Route Server 서브넷에 대한 고정 경로를 추가해야 합니다. 예를 들어 Azure Route Server가 10.0.255.0/27에 있고 NVA가 10.0.1.0/24에 있으면 NVA의 라우팅 테이블에 다음 경로를 추가해야 합니다.

| 경로 | 다음 홉 |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1은 NVA(더 정확히는 NIC 중 하나)가 호스트되는 서브넷의 기본 게이트웨이 IP입니다.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>이미 ExpressRoute 게이트웨이 및/또는 Azure VPN 게이트웨이가 있는 가상 네트워크에 Azure Route Server를 배포할 때 ExpressRoute 및/또는 Azure VPN을 통해 온-프레미스 네트워크에 연결되지 않는 이유는 무엇인가요?
Azure Route Server를 가상 네트워크에 배포하는 경우 게이트웨이와 가상 네트워크 간 컨트롤 플레인을 업데이트해야 합니다. 해당 업데이트를 수행하는 동안 가상 네트워크 VM의 온-프레미스 네트워크에 대한 연결이 끊어지는 기간이 있습니다. 프로덕션 환경에서 Azure Route Server를 배포하기 위해 유지 관리를 예약하는 것이 좋습니다.  

## <a name="control-plane-issues"></a>컨트롤 플레인 문제

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>NVA와 Azure Route Server 간에 BGP 피어링이 업 다운을 반복(“플래핑”)하는 이유는 무엇인가요?

BGP 타이머 설정이 플래핑 발생 원인일 수 있습니다. 기본적으로 Azure Route Server의 연결 유지 타이머는 60초로 설정되고 대기 타이머는 180초로 설정됩니다.

### <a name="why-does-my-on-premises-network-connected-to-azure-vpn-gateway-not-receive-the-default-route-advertised-by-azure-route-server"></a>Azure VPN 게이트웨이에 연결된 온-프레미스 네트워크가 Azure Route Server에서 보급한 기본 경로를 수신하지 못하는 이유는 무엇인가요?

Azure VPN 게이트웨이는 Azure Route Server를 포함한 BGP 피어로부터 기본 경로를 수신할 수 있지만 다른 피어에 [기본 경로를 보급하지 않습니다](../vpn-gateway/vpn-gateway-vpn-faq.md#what-address-prefixes-will-azure-vpn-gateways-advertise-to-me). 

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>BGP 피어링이 작동 중인 경우에도 NVA가 Azure Route Server에서 경로를 수신하지 않는 이유는 무엇인가요?

Azure Route Server에서 사용하는 ASN은 65515입니다. 경로 전파가 자동으로 발생할 수 있도록 NVA와 Azure Route Server 간에 “eBGP” 세션을 설정할 수 있도록 NVA에 대해 다른 ASN을 구성해야 합니다. NVA와 Azure Route Server가 가상 네트워크의 다른 서브넷에 있으므로 BGP 구성에서 “멀티 홉”을 사용하도록 설정해야 합니다.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>NVA와 Azure Route Server 간 BGP 피어링이 작동 중입니다. NVA와 Azure Route Server 간 경로가 올바르게 교환되고 있는 것을 확인할 수 있습니다. 그런데도 VM의 유효 라우팅 테이블에 NVA 경로가 없는 이유는 무엇인가요? 

* VM이 NVA 및 Azure Route Server와 동일한 VNet에 있는 경우:

     Azure Route Server는 가상 네트워크에서 실행되는 다른 모든 VM에 경로를 전송할 책임을 공유하는 두 개의 VM에서 호스트되는 두 개의 BGP 피어 IP를 노출합니다. 각 NVA는 가상 네트워크의 VM이 Azure Route Server로부터 일관된 라우팅 정보를 얻을 수 있도록 두 VM에 대해 두 개의 동일한 BGP 세션을 설정해야 합니다. 예를 들어 동일한 AS 번호, 동일한 AS 경로를 사용하고 동일한 경로 집합을 보급해야 합니다. 다음 다이어그램을 참조하세요.

    ![Route Server를 사용하는 네트워크 가상 어플라이언스를 보여 주는 다이어그램.](./media/faq/network-virtual-appliances.png)

    NVA 인스턴스가 두 개 이상 있고 NVA 인스턴스 하나를 활성으로 지정하고 다른 하나는 수동으로 지정하려는 경우 다른 NVA 인스턴스로부터 오는 동일한 경로에 대해 다른 AS 경로를 보급할 수 ‘있습니다’.

* VM이 NVA 및 Azure Route Server를 호스트하는 가상 네트워크와 다른 가상 네트워크에 있는 경우 두 VNet 간에 VNet 피어링이 사용하도록 설정되어 ‘있고’ VM의 VNet에서 원격 경로 서버 사용이 사용하도록 설정되어 있는지 확인합니다.

### <a name="why-is-the-equal-cost-multi-path-ecmp-function-of-my-expressroute-turned-off-after-i-deploy-azure-route-server-to-the-virtual-network"></a>Azure Route Server를 가상 네트워크에 배포한 후 ExpressRoute의 ECMP(Equal-Cost Multi-Path) 기능이 꺼져 있는 이유가 무엇인가요?

여러 ExpressRoute 연결을 통해 온-프레미스 네트워크에서 Azure로 동일한 경로를 보급할 때 일반적으로 ECMP는 Azure에서 다시 온-프레미스로의 이러한 경로를 대상으로 하는 트래픽에 대해 기본적으로 사용하도록 설정됩니다. 그러나 경로 서버가 배포된 후 ExpressRoute와 Azure Route Server 간의 BGP 교환에서 다중 경로 정보가 손실되고 결과적으로 Azure의 트래픽은 ExpressRoute 연결 중 하나에서만 통과합니다. 이 제한은 Azure Route Server의 향후 릴리스에서 해제됩니다.  

## <a name="next-steps"></a>다음 단계

[Azure Route Server를 구성](quickstart-configure-route-server-powershell.md)하는 방법 알아보기
