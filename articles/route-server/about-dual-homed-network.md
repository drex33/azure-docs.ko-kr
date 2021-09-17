---
title: 'Azure Route Server를 사용하는 이중 홈 네트워크 정보 '
description: Azure Route Server가 이중 홈 네트워크에서 작동하는 방식에 대해 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 76c1a1e424e4f8ae372f53ceab5da43ca9d40629
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434577"
---
# <a name="about-dual-homed-network-with-azure-route-server"></a>Azure Route Server를 사용하는 이중 홈 네트워크 정보

Azure Route Server는 일반적인 허브 및 스포크 네트워크 토폴로지를 지원합니다. 이 구성은 Route Server와 NVA(네트워크 가상 어플라이언스)가 모두 허브 가상 네트워크에 있는 경우입니다. 라우터 서버를 사용하면 이중 홈 네트워크라는 다른 토폴로지를 구성할 수도 있습니다. 이 구성은 스포크 가상 네트워크가 둘 이상의 허브 가상 네트워크와 피어링되어 있는 경우입니다. 스포크 가상 네트워크의 가상 머신은 허브 가상 네트워크를 통해 온-프레미스 또는 인터넷과 통신할 수 있습니다.

## <a name="how-to-set-it-up"></a>설정하는 방법

아래 다이어그램에서 볼 수 있듯이 다음 작업을 수행해야 합니다.

* 각 허브 가상 네트워크에 NVA를 배포하고 스포크 가상 네트워크에 경로 서버를 배포합니다.
* 허브와 스포크 가상 네트워크 간에 VNet 피어링을 사용하도록 설정합니다.
* Route Server와 배포된 각 NVA 간에 BGP 피어링을 구성합니다.

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology.png" alt-text="이중 홈 토폴로지의 Route Server 다이어그램":::

### <a name="how-does-it-work"></a>작동 원리

컨트롤 플레인에서 NVA와 Route Server는 동일한 가상 네트워크에 배포되어 있는 것처럼 경로를 교환합니다. NVA는 Route Server에서 스포크 가상 네트워크 주소에 대해 학습합니다. Route Server는 각 NVA에서 경로를 학습합니다. 그런 다음, Route Server는 학습한 경로를 사용하여 스포크 가상 네트워크의 모든 가상 머신을 프로그래밍합니다. 

데이터 평면에서 스포크 가상 네트워크의 가상 머신은 허브의 보안 NVA 또는 VPN NVA를 다음 홉으로 확인합니다. 하이브리드 크로스-프레미스 트래픽 또는 인터넷에 바인딩된 트래픽으로 향하는 트래픽은 이제 허브 가상 네트워크의 NVA를 통해 라우팅됩니다. 두 허브 모두를 액티브/액티브 또는 액티브/패시브로 구성할 수 있습니다. 액티브 허브에 장애가 발생하면 가상 머신을 오가는 트래픽이 다른 허브로 장애 조치(failover)됩니다. 이러한 장애에는 NVA 장애 또는 서비스 연결 장애가 포함되지만 이에 국한되지는 않습니다. 이렇게 설정하면 네트워크가 고가용성을 위해 구성됩니다.

## <a name="integration-with-expressroute"></a>ExpressRoute와 통합

둘 이상의 ExpressRoute 연결을 포함하는 이중 홈 네트워크를 빌드할 수 있습니다. 위에서 설명한 단계와 함께 다음을 수행해야 합니다.

* 각 허브 가상 네트워크에 ExpressRoute 게이트웨이가 있는 다른 Route Server를 만듭니다.
* 허브 가상 네트워크에서 NVA와 Route Server 간에 BGP 피어링을 구성합니다.
* 허브 가상 네트워크에서 ExpressRoute 게이트웨이와 Route Server 간의 [경로 교환을 사용](quickstart-configure-route-server-portal.md#configure-route-exchange)하도록 설정합니다.
* 스포크 가상 네트워크 VNet 피어링 구성에서 "원격 게이트웨이 또는 원격 Route Server 사용"이 **사용하지 않도록 설정** 되어 있는지 확인합니다.

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology-expressroute.png" alt-text="ExpressRoute를 사용하는 이중 홈 토폴로지의 Route Server 다이어그램":::

### <a name="how-does-it-work"></a>작동 원리

컨트롤 플레인에서 허브 가상 네트워크의 NVA는 허브의 Route Server와 [경로 교환](quickstart-configure-route-server-portal.md#configure-route-exchange)을 통해 ExpressRoute 게이트웨이의 온-프레미스 경로에 대해 학습합니다. 반대로 NVA는 동일한 Route Server를 사용하여 스포크 가상 네트워크 주소를 ExpressRoute 게이트웨이로 보냅니다. 그러면 스포크 및 허브 가상 네트워크의 Route Server는 온-프레미스 네트워크 주소를 해당 가상 네트워크의 가상 머신에 프로그래밍합니다.

> [!IMPORTANT]
> BGP는 AS 경로에서 AS 번호를 확인하여 루프를 방지합니다. 수신 라우터는 받은 BGP 패킷의 AS 경로에서 자체 AS 번호를 발견하면 패킷을 삭제합니다. 이 예제에서 두 Route Server의 AS 번호는 65515입니다. 각 Route Server가 상대편 Route Server의 경로를 삭제하지 않도록 하려면 NVA는 각 Route Server와 피어링할 때 **as-override** BGP 정책을 적용해야 합니다. 
>

데이터 평면에서 스포크 가상 네트워크의 가상 머신은 온-프레미스 네트워크로 향하는 모든 트래픽을 먼저 허브 가상 네트워크의 NVA로 보냅니다. 그런 다음, NVA는 ExpressRoute를 통해 온-프레미스 네트워크로 트래픽을 전달합니다. 온-프레미스의 트래픽은 동일한 데이터 경로를 반대 방향으로 통과합니다. Route Server 중 어느 것도 데이터 경로에 없다는 것을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Route Server가 ExpressRoute를 통해 작동하는 방식 알아보기](expressroute-vpn-support.md)
* [Azure Route Server가 네트워크 가상 어플라이언스를 통해 작동하는 방식 알아보기](resource-manager-template-samples.md)

