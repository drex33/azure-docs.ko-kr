---
title: Express 경로 및 Azure VPN에 대 한 Azure 경로 서버 지원 정보
description: Azure Route Server가 ExpressRoute 및 Azure VPN 게이트웨이와 상호작용하는 방법에 대하여 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: duau
ms.openlocfilehash: 61a712fa747252b07a513cfa6e18e3f3b4a67e6c
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400727"
---
# <a name="about-azure-route-server-support-for-expressroute-and-azure-vpn"></a>Express 경로 및 Azure VPN에 대 한 Azure 경로 서버 지원 정보

Azure Route Server는 Azure에서 실행되는 타사 NVA(네트워크 가상 어플라이언스)를 지원할 뿐 아니라 ExpressRoute 및 Azure VPN 게이트웨이와도 원활하게 통합합니다. 게이트웨이와 Azure Route Server 간의 BGP 피어링을 구성하거나 관리할 필요가 없습니다. 간단한 [구성 변경](quickstart-configure-route-server-powershell.md#route-exchange)을 통하여 게이트웨이와 Azure Route Server 사이의 경로 교환을 사용하도록 설정할 수 있습니다.


## <a name="how-does-it-work"></a>작동 원리

가상 네트워크에서 Azure Route Server를 ExpressRoute 게이트웨이 및 NVA와 함께 배포하는 경우, Azure Route Server는 NVA에서 수신한 경로를 ExpressRoute 게이트웨이에 전파하지 않고, ExpressRoute 게이트웨이에서 수신한 경로를 NVA에 전파하지 않는 것이 기본 설정입니다. 경로 교환을 사용하도록 설정하면 ExpressRoute와 NVA는 상대측의 경로를 확인합니다.

다음 다이어그램을 통하여 일례를 살펴봅시다.

* SDWAN 어플라이언스는 Azure Route Server에서 “On-prem 2”로부터의 경로를 수신하며, 해당 경로는 가상 네트워크 경로와 함께 ExpressRoute에 연결됩니다.

* ExpressRoute 게이트웨이는 “On-prem 1”로부터의 경로를 수신하는데, 이는 Azure Route Server의 가상 네트워크 경로와 함께 SDWAN 어플라이언스에도 연결되는 것입니다.

    ![Route Server로 구성된 ExpressRoute를 보여 주는 다이어그램](./media/expressroute-vpn-support/expressroute-with-route-server.png)

SDWAN 어플라이언스를 Azure VPN 게이트웨이로 대체할 수도 있습니다. Azure VPN 게이트웨이와 ExpressRoute 모두 완전 관리형이므로, 양쪽의 온-프레미스 네트워크가 서로 소통하도록 경로 교환을 설정하기만 하면 됩니다.

> [!IMPORTANT] 
> Azure VPN gateway는 [**활성-활성**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) 모드로 구성 되 고 ASN은 65515로 설정 되어야 합니다.
>

![Route Server로 구성된 ExpressRoute와 VPN 게이트웨이를 보여 주는 다이어그램](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>다음 단계

- [Azure Route Server](route-server-faq.md)에 대한 자세한 정보 알아보기
- [Azure Route Server 구성](quickstart-configure-route-server-powershell.md) 방법 알아보기
- [Azure ExpressRoute 및 Azure VPN 공존](../expressroute/expressroute-howto-coexist-resource-manager.md)에 대하여 자세한 정보 알아보기
