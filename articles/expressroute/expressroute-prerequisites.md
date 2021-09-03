---
title: 'Azure ExpressRoute: 필수 조건'
description: 이 페이지에서는 Azure ExpressRoute 회로를 주문하기 전에 충족해야 하는 요구 사항 목록을 제공합니다. 검사 목록을 포함합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 6f5b3cc109d3583defce78c3ae52848188fca900
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284554"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 필수 구성 요소 및 검사 목록
ExpressRoute를 사용하여 Microsoft 클라우드 서비스에 연결하려면 다음 섹션에 나열된 다음 요구 사항을 충족하는지 확인해야 합니다.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure 계정
* 유효한 활성 Microsoft Azure 계정 ExpressRoute 회로를 설정하려면 이 계정이 필요합니다. ExpressRoute 회로는 Azure 구독 내의 리소스입니다. Azure 구독은 Microsoft 365와 같은 Azure 이외의 Microsoft 클라우드 서비스에 대한 연결로 제한된 경우에도 필요합니다.
* 활성 Microsoft 365 구독(Microsoft 365 서비스를 사용하는 경우). 자세한 내용은 이 문서의 Microsoft 365 특정 요구 사항 섹션을 참조하세요.

## <a name="connectivity-provider"></a>연결 공급자

* Microsoft 클라우드에 연결하는 데 [ExpressRoute 연결 파트너](expressroute-locations.md#partners) 와 작업할 수 있습니다. [세 가지 방법](expressroute-introduction.md)으로 온-프레미스 네트워크와 Microsoft 간에 연결을 설정할 수 있습니다.
* 공급자가 ExpressRoute 연결 파트너가 아닌 경우 [클라우드 Exchange 공급자](expressroute-locations.md#connectivity-through-exchange-providers)를 통해 Microsoft 클라우드에 계속 연결할 수 있습니다.

## <a name="network-requirements"></a>네트워크 요구 사항
* **각 피어링 위치에서의 중복성**: [클라우드 교환에 대한 물리적 연결이 하나](expressroute-faqs.md#onep2plink)뿐인 경우에도 각 ExpressRoute 회로에서 Microsoft 라우터와 피어링 라우터 간에 중복 BGP 세션을 설정해야 합니다.
* **재해 복구를 위한 중복성**: 단일 실패 지점을 방지하기 위해 서로 다른 피어링 위치에 ExpressRoute 회로를 두 개 이상 설정하는 것이 좋습니다.
* **라우팅**: Microsoft Cloud에 연결하는 방법에 따라 사용자와 공급자는 [라우팅 도메인](expressroute-circuit-peerings.md)에 대한 BGP 세션을 설정 및 관리해야 합니다. 일부 이더넷 연결 공급자 또는 클라우드 Exchange 공급자는 가치 추가 서비스로 BGP 관리를 제공할 수 있습니다.
* **NAT**: Microsoft만 Microsoft 피어링을 통해 공용 IP 주소를 허용합니다. 온-프레미스 네트워크에서 개인 IP 주소를 사용하는 경우 사용자 또는 공급자는 [NAT를 사용](expressroute-nat.md)하여 개인 IP 주소를 공용 IP 주소로 번역해야 합니다.
* **QoS**: 비즈니스용 Skype에는 차별화된 QoS 처리를 필요로 하는 다양한 서비스(예: 음성, 비디오, 텍스트)가 있습니다. 사용자와 공급자는 [QoS 요구 사항](expressroute-qos.md)을 따라야 합니다.
* **네트워크 보안**: ExpressRoute를 통해 Microsoft Cloud에 연결할 때 [네트워크 보안](/azure/cloud-adoption-framework/reference/networking-vdc)을 고려합니다.

## <a name="microsoft-365"></a>Microsoft 365
ExpressRoute에서 Microsoft 365를 사용하도록 설정하려는 경우 Microsoft 365 요구 사항에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Microsoft 365용 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [Microsoft 365용 ExpressRoute를 사용하여 라우팅](/microsoft-365/enterprise/routing-with-expressroute)
* [ExpressRoute를 사용한 고가용성 및 장애 조치(failover)](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365 URL 및 IP 주소 범위](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Microsoft 365에 대한 네트워크 계획 및 성능 튜닝](/microsoft-365/enterprise/network-planning-and-performance)
* [Microsoft 365에 대한 네트워크 및 마이그레이션 계획](/microsoft-365/enterprise/network-and-migration-planning)
* [온-프레미스 환경과 Microsoft 365 통합](/microsoft-365/enterprise/microsoft-365-integration)
* [Office 365 IP 주소 변경 사항에 대한 최신 정보 유지](/microsoft-365/enterprise/microsoft-365-ip-web-service?view=o365-worldwide)
* [Office 365 고급 교육 비디오의 ExpressRoute](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* ExpressRoute 연결 공급자를 찾습니다. [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md)를 확인하세요.
* [라우팅](expressroute-routing.md), [NAT](expressroute-nat.md) 및 [QoS](expressroute-qos.md)에 대한 요구 사항을 참조합니다.
* ExpressRoute 연결을 구성합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)
  * [라우팅 구성](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)