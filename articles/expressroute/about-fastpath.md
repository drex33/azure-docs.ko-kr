---
title: ExpressRoute FastPath 정보
description: 게이트웨이를 우회하여 네트워크 트래픽을 전송하는 Azure ExpressRoute FastPath에 대해 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/10/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 1fcdeaaee2ebafbfd8e97577ed87a5ef0e545cd5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226398"
---
# <a name="about-expressroute-fastpath"></a>ExpressRoute FastPath 정보

ExpressRoute 가상 네트워크 게이트웨이는 네트워크 경로와 네트워크 트래픽을 교환하도록 설계되었습니다. FastPath는 온-프레미스 네트워크와 가상 네트워크 간의 데이터 경로 성능을 향상시키도록 설계되었습니다. FastPath를 사용하도록 설정하면 게이트웨이를 우회하여 가상 네트워크의 가상 머신으로 네트워크 트래픽을 직접 보냅니다.

## <a name="requirements"></a>요구 사항

### <a name="circuits"></a>회로

FastPath는 모든 ExpressRoute 회로에서 사용할 수 있습니다.

### <a name="gateways"></a>게이트웨이

FastPath는 가상 네트워크와 온-프레미스 네트워크 간에 경로를 교환하기 위해 가상 네트워크 게이트웨이를 만들어야 합니다. 성능 정보 및 게이트웨이 SKU를 비롯한 가상 네트워크 게이트웨이와 ExpressRoute에 대한 자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이](expressroute-about-virtual-network-gateways.md)를 참조하세요.

FastPath를 구성하려면 가상 네트워크 게이트웨이가 다음 중 하나여야 합니다.

* 초고성능
* ErGw3AZ

## <a name="limitations"></a>제한 사항

FastPath는 대부분의 구성을 지원하지만 다음 기능은 지원하지 않습니다.

* 게이트웨이 서브넷의 UDR: 이 UDR은 FastPath가 온-프레미스 네트워크에서 Azure 가상 네트워크의 가상 머신으로 직접 보내는 네트워크 트래픽에 영향을 주지 않습니다. 

* 기본 Load Balancer: 가상 네트워크에 기본 내부 부하 분산 디바이스를 배포 하거나 가상 네트워크에 배포하는 Azure PaaS 서비스에서 기본 내부 부하 분산 디바이스를 사용하는 경우, 온-프레미스 네트워크에서 기본 부하 분산 디바이스에 호스팅된 가상 IP로의 네트워크 트래픽이 가상 네트워크 게이트웨이로 전송됩니다. 이 솔루션은 기본 부하 분산 디바이스를 [표준 부하 분산 디바이스](../load-balancer/load-balancer-overview.md)로 업그레이드하는 것입니다.

* 프라이빗 링크: 온-프레미스 네트워크에서 가상 네트워크의 [프라이빗 엔드포인트](../private-link/private-link-overview.md)로 연결하는 경우 연결은 가상 네트워크 게이트웨이를 통해 이동합니다.

## <a name="public-preview"></a>퍼블릭 미리 보기

다음 FastPath 기능은 퍼블릭 미리 보기로 제공됩니다.

**VNet 피어링** - FastPath는 ExpressRoute 가상 네트워크 게이트웨이를 무시하고 ExpressRoute에 연결된 가상 네트워크에 피어링된 가상 네트워크에 배포된 VM으로 직접 트래픽을 전송합니다.

[ExpressRoute FastPath 기능에 등록하는 방법](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview)을 참조하세요.

모든 지역에서 사용 가능합니다.
 
## <a name="next-steps"></a>다음 단계

FastPath를 사용하도록 설정하려면 [가상 네트워크를 ExpressRoute에 연결](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)을 참조하세요.
