---
title: Azure Virtual WAN 및 원격 근무
description: Azure Virtual WAN을 활용하여 코로나19 팬데믹에 따른 원격 근무를 수행하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: ad8a365550b25906c7fb4fabda725b21abe97417
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477765"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure Virtual WAN 및 원격 근무 지원

>[!NOTE]
>이 문서에서는 Azure Virtual WAN, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 이용하여 원격으로 근무하고 코로나19 위기로 인해 발생하는 네트워크 문제를 완화하는 방법을 설명합니다.
>

원격 사용자에게 연결을 제공하기 위해 서두르고 있나요?
갑자기 계획한 것보다 많은 사용자 급증을 지원해야 하나요?
사용자가 집에서 연결하고 클라우드에 액세스할 뿐만 아니라 온-프레미스에 연결할 수 있어야 하나요?
원격 사용자가 프라이빗 WAN 뒤에 있는 리소스에 연결할 수 있게 해야 하나요?
사용자가 지역 간에 연결을 설정하지 않고도 클라우드 간 리소스에 액세스해야 하나요?
이 세계적 팬데믹이 초래한 전례 없는 변화로 생겨난 연결 요구 사항을 충족할 수 있도록 지원하기 위해 Azure Virtual WAN 팀이 있습니다.

Azure Virtual WAN은 많은 네트워킹, 보안 및 라우팅 기능을 결합하여 단일 운영 인터페이스를 제공하는 네트워킹 서비스입니다. 이러한 기능에는 분기 연결(SD-WAN 또는 VPN CPE와 같은 Virtual WAN 파트너 디바이스에서 연결 자동화를 통해), 사이트 간 VPN 연결, 원격 사용자 VPN(지점 및 사이트 간) 연결, 프라이빗(ExpressRoute) 연결, 클라우드 간 연결(Virtual Networks에 대한 전이적 연결), VPN ExpressRoute 상호 연결, 라우팅, Azure Firewall, 프라이빗 연결용 암호화 등이 포함됩니다. Virtual WAN 사용을 시작하기 위해 이 사용 사례가 모두 있어야 하는 것은 아닙니다. 단 하나의 사용 사례로 시작하여 네트워크가 발전함에 따라 네트워크를 조정할 수 있습니다.

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwan1.png)

이제 원격 사용자와 관련해서 네트워크를 실행하는 데 필요한 작업을 살펴보겠습니다.

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>원격 사용자 연결 설정

IPsec/IKE(IKEv2) 또는 OpenVPN 연결을 통해 Azure의 리소스에 연결할 수 있습니다. 이 연결 유형은 원격 사용자에 대해 VPN 클라이언트를 구성해야 합니다. 이 클라이언트는 [Azure VPN 클라이언트](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN 클라이언트 또는 IKEv2를 지원하는 임의 클라이언트일 수 있습니다. 자세한 내용은 [지점 및 사이트 간 연결 만들기](virtual-wan-point-to-site-portal.md)를 참조하세요.

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>원격 사용자에서 온-프레미스로 연결

다음 두 가지 옵션이 있습니다.

* 기존 VPN 디바이스를 사용하여 사이트 간 연결을 설정합니다. IPsec VPN 디바이스를 Azure Virtual WAN 허브에 연결하는 경우 지점 및 사이트 간 사용자 VPN(원격 사용자)과 사이트 간 VPN은 자동으로 상호 연결됩니다. 온-프레미스 VPN 디바이스에서 Azure Virtual WAN으로 사이트 간 VPN을 설정하는 방법에 관한 자세한 내용은 [Virtual WAN을 사용하여 사이트 간 연결 만들기](virtual-wan-site-to-site-portal.md)를 참조하세요.

* ExpressRoute 회로를 Virtual WAN 허브에 연결합니다. ExpressRoute 회로를 연결하려면 Virtual WAN에서 ExpressRoute 게이트웨이를 배포해야 합니다. 하나를 배포하는 즉시 지점 및 사이트 간 사용자 VPN과 ExpressRoute 사용자가 자동으로 상호 연결됩니다. ExpressRoute 연결을 만들려면 [Virtual WAN을 사용하여 ExpressRoute 연결 만들기](virtual-wan-expressroute-portal.md)를 참조하세요. 기존 ExpressRoute 회로를 사용하여 Azure Virtual WAN에 연결할 수 있습니다.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>기존 기본 Virtual WAN 고객

기본 Virtual WAN은 사이트 간 VPN만 제공합니다. 원격 사용자가 연결하기 위해서는 Virtual WAN을 표준 Virtual WAN으로 업그레이드해야 합니다. Virtual WAN을 업그레이드하는 단계는 [기본에서 표준으로 Virtual WAN 업그레이드](upgrade-virtual-wan.md)를 참조하세요.

## <a name="additional-information"></a><a name="other considerations"></a>추가 정보

Virtual WAN은 지역/위치당 여러 허브를 지원합니다. 위치 정보는 [Virtual WAN 파트너 및 위치](virtual-wan-locations-partners.md) 문서를 참조하세요. 각 허브는 최대 10만 개의 원격 사용자 연결, 1000 분기 연결, 4 개의 Express 경로 회로 및 최대 500 Virtual Network 연결을 지원 합니다. 원격 사용자를 스케일 업할 때 질문이 있는 경우 주저하지 마시고 azurevirtualwan@microsoft.com으로 메일을 보내 도움을 요청하세요. 기술 지원이 필요한 경우 Azure Portal에서 지원 티켓을 열어야 합니다. 그러면 지원이 제공됩니다.

## <a name="faq"></a><a name="faq"></a>FAQ

[Virtual WAN FAQ](virtual-wan-faq.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Virtual WAN에 관한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.
