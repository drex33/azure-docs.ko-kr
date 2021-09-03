---
title: 'Azure ExpressRoute: 고가용성을 위한 설계'
description: 이 페이지에서는 Azure ExpressRoute를 사용하는 동안 고가용성을 위한 아키텍처 권장 사항을 제공합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 5dcf58dce7b87862c2f01ad76db8aff66366ee4b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528613"
---
# <a name="designing-for-high-availability-with-expressroute"></a>ExpressRoute를 사용한 고가용성을 위한 설계

ExpressRoute는 Microsoft 리소스에 대한 이동 통신 사업자급 프라이빗 네트워크 연결을 제공하는 고가용성을 위해 설계되었습니다. 즉, Microsoft 네트워크의 ExpressRoute 경로에는 단일 실패 지점이 없습니다. 가용성을 최대화하려면 ExpressRoute 회로의 고객 및 서비스 공급자 세그먼트도 고가용성을 위해 설계되어야 합니다. 이 문서에서는 먼저 ExpressRoute를 사용하여 강력한 네트워크 연결을 빌드하기 위한 네트워크 아키텍처 고려 사항을 살펴본 다음, ExpressRoute 회로의 고가용성을 높이는 데 도움이 되는 미세 조정 기능을 살펴봅시다.

>[!NOTE]
>이 문서에 설명된 개념은 Virtual WAN 또는 Virtual WAN 외부에서 ExpressRoute 회로를 만들 때 동일하게 적용됩니다.
>

## <a name="architecture-considerations"></a>아키텍처 고려 사항

다음 그림은 ExpressRoute 회로의 가용성을 최대화하기 위해 ExpressRoute 회로를 사용하여 연결하는 권장 방법을 보여 줍니다.

 [![1]][1]

고가용성을 구현하려면 엔드투엔드 네트워크 전체에서 ExpressRoute 회로의 중복성을 유지 관리하는 것이 중요합니다. 즉, 온-프레미스 네트워크 내에서 중복성을 유지 관리해야 하고 서비스 공급자 네트워크 내에서 중복성이 손상되지 않도록 해야 합니다. 최소한의 중복성 유지 관리는 단일 네트워크 실패 지점을 방지하는 것을 의미합니다. 네트워크 디바이스에 대해 중복된 전원 및 냉각 기능을 사용하면 고가용성을 더욱 향상시킬 수 있습니다.

### <a name="first-mile-physical-layer-design-considerations"></a>퍼스트 마일 물리적 계층 설계 고려 사항

 동일한 CP (고객 프레미스 장비)에서 ExpressRoute 회로의 기본 및 보조 연결을 모두 종료하는 경우 온-프레미스 네트워크 내에서 고가용성이 손상됩니다. 또한 서로 다른 하위 인터페이스에서 두 연결을 종료 하거나 파트너 네트워크 내에서 두 연결을 병합하여 동일한 CPE 포트를 통해 기본 및 보조 연결을 모두 구성하는 경우 파트너가 해당 네트워크 세그먼트의 고가용성도 손상시키게 됩니다. 이 손상은 다음 그림에 설명되어 있습니다.

[![2]][2]

반면에 서로 다른 지리적 위치에서 ExpressRoute 회로의 기본 및 보조 연결을 종료하면 연결의 네트워크 성능이 손상될 수 있습니다. 서로 다른 지리적 위치에서 종료되는 기본 및 보조 연결에서 트래픽 부하를 적극적으로 분산하는 경우 두 경로 간 네트워크 대기 시간에 상당한 차이가 발생하여 네트워크 성능이 최적화되지 않습니다. 

지역 중복 설계 고려 사항은 [ExpressRoute를 사용한 재해 복구를 위한 설계][DR]를 참조하세요.

### <a name="active-active-connections"></a>활성-활성 연결

Microsoft 네트워크는 ExpressRoute 회로의 기본 및 보조 연결을 활성-활성 모드로 작동하도록 구성되었습니다. 그러나 경로 보급 알림을 통해 ExpressRoute 회로의 중복 연결이 활성-수동 모드로 작동하도록 강제 적용할 수 있습니다. 더 구체적인 경로 보급 및 BGP AS 경로 앞에 추가와 같은 일반적인 기술을 사용하여 특정 경로가 다른 경로보다 선호되도록 만들 수 있습니다.

고가용성을 높이려면 ExpressRoute 회로의 연결을 둘 다 활성-활성 모드로 작동하는 것이 좋습니다. 연결이 활성-활성 모드로 작동하도록 하면 Microsoft 네트워크에서 흐름별로 연결 간에 트래픽 부하를 분산합니다.

ExpressRoute 회로의 기본 및 보조 연결을 활성-수동 모드로 실행하면 활성 경로에서 오류가 발생한 후에 두 연결이 모두 실패할 위험이 있습니다. 장애 조치(failover) 시 오류가 발생하는 일반적인 원인은 수동 연결의 활성 관리 부족 및 부실 경로를 보급하는 수동 연결입니다.

또는 ExpressRoute 회로의 기본 및 보조 연결을 활성-활성 모드로 실행하면 ExpressRoute 연결 실패 후에 흐름의 절반만 실패하고 다시 라우팅됩니다. 따라서 활성-활성 모드는 MTTR(평균 복구 시간)을 개선하는 데 큰 도움이 됩니다.

> [!NOTE]
> 유지 관리 작업 중이거나 하나의 연결에 영향을 미치는 계획되지 않은 이벤트의 경우 트래픽을 정상 연결로 드레이닝하기 위해 AS 경로 앞에 추가하는 것이 좋습니다. Microsoft에서 경로 앞에 추가를 구성하고 서비스 중단을 방지하기 위해 필요한 경로 보급을 적절하게 구성한 경우 트래픽이 정상적인 경로를 통해 라우팅할 수 있는지 확인해야 합니다. 
> 

### <a name="nat-for-microsoft-peering"></a>Microsoft 피어링을 위한 NAT 

Microsoft 피어링은 퍼블릭 엔드포인트 간 통신을 위해 설계되었습니다. 따라서 일반적으로 온-프레미스 프라이빗 엔드포인트는 Microsoft 피어링을 통해 통신하기 전에 고객 또는 파트너 네트워크의 공용 IP를 사용하여 NAT(Network Address Translation)로 연결됩니다. 기본 및 보조 연결을 모두 활성-활성 모드로 사용한다고 가정할 경우 NAT 연결 위치와 방법은 ExpressRoute 연결 중 하나에서 오류 발생 후의 복구 속도에 영향을 줍니다. 다음 그림에는 두 가지 NAT 옵션이 나와 있습니다.

[![3]][3]

#### <a name="option-1"></a>옵션 1:

ExpressRoute 회로의 기본 연결과 보조 연결 간에 트래픽을 분할한 후 NAT가 적용됩니다. NAT의 상태 저장 요구 사항을 충족하기 위해 기본 및 보조 디바이스에 독립 NAT 풀이 사용됩니다. 반환 트래픽은 흐름이 송신된 동일한 에지 디바이스에 도착합니다.

ExpressRoute 연결이 실패하면 해당 NAT 풀에 연결하는 기능이 중단됩니다. 그렇기 때문에 끊어진 모든 네트워크 흐름은 해당 시간 초과 후 TCP 또는 애플리케이션 계층에 의해 다시 설정되어야 합니다. 오류가 발생하는 동안 Azure는 ExpressRoute 회로의 기본 또는 보조 연결에 대한 연결이 복원될 때까지 해당 NAT를 사용하여 온-프레미스 서버에 연결할 수 없습니다.

#### <a name="option-2"></a>옵션 2:

ExpressRoute 회로의 기본 연결과 보조 연결 간에 트래픽을 분할하기 전에 공통 NAT 풀이 사용됩니다. 트래픽을 분할하기 전에 공통 NAT 풀이 고가용성을 손상시키는 것과 같은 단일 실패 지점을 도입하는 것은 아닙니다.

기본 또는 보조 연결이 실패한 후에도 NAT 풀에 연결할 수 있습니다. 따라서 네트워크 계층 자체가 패킷을 다시 라우팅할 수 있고 오류 발생 후 보다 신속한 복구에 도움이 됩니다. 

> [!NOTE]
> * NAT 옵션 1(기본 및 보조 ExpressRoute 연결에 독립 NAT 풀 사용)을 사용하고 NAT 풀 중 하나에서 온-프레미스 서버로 IP 주소의 포트를 매핑하는 경우 해당 연결이 실패할 때 ExpressRoute 회로를 통해 서버에 연결할 수 없습니다.
> * 상태 저장 디바이스에서 ExpressRoute BGP 연결을 종료하면 Microsoft 또는 ExpressRoute 공급자의 계획되거나 계획되지 않은 유지 관리 중에 장애 조치(failover) 문제가 발생할 수 있습니다. 트래픽이 올바르게 장애 조치(failover)되는지 확인하고 가능한 경우 상태 비저장 디바이스에서 BGP 세션을 종료하도록 설정을 테스트해야 합니다.

## <a name="fine-tuning-features-for-private-peering"></a>프라이빗 피어링을 위한 미세 조정 기능

이 섹션에서는 ExpressRoute 회로의 고가용성을 높이는 데 도움이 되는 선택적(Azure 배포 및 MTTR에 대한 민감도에 따라) 기능을 검토해 봅시다. 특히 ExpressRoute 가상 네트워크 게이트웨이의 영역 인식 배포 및 BFD(양방향 전달 검색)를 검토하겠습니다.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>가용성 영역 인식 ExpressRoute 가상 네트워크 게이트웨이

Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 영역 중복 Azure IaaS 배포를 선택하는 경우 ExpressRoute 프라이빗 피어링을 종료하는 영역 중복 가상 네트워크 게이트웨이를 구성할 수도 있습니다. 자세한 내용은 [Azure 가용성 영역의 영역 중복 가상 네트워크 게이트웨이 정보][zone redundant vgw]를 참조하세요. 영역 중복 가상 네트워크 게이트웨이를 구성하려면 [Azure 가용성 영역에서 영역 중복 가상 네트워크 게이트웨이 만들기][conf zone redundant vgw]를 참조하세요.

### <a name="improving-failure-detection-time"></a>오류 검색 시간 향상

ExpressRoute는 프라이빗 피어링을 통해 BFD를 지원합니다. BFD는 MSEE(Microsoft Enterprise Edge)와 온-프레미스 쪽 BGP 인접 항목 간 계층 2 네트워크를 통한 오류 검색 시간을 약 3분(기본값)에서 1초 미만으로 줄입니다. 빠른 오류 검색 시간은 신속한 오류 복구에 도움이 됩니다. 자세한 내용은 [ExpressRoute를 통한 BFD 구성][BFD]을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 ExpressRoute 회로 연결의 고가용성을 위해 설계하는 방법을 설명했습니다. ExpressRoute 회로 피어링 지점은 지리적 위치에 고정되므로 전체 위치에 영향을 주는 치명적 오류의 영향을 받을 수 있습니다. 

전체 지역에 영향을 주는 치명적 오류를 견딜 수 있는 지역 중복 네트워크 연결을 Microsoft 백본에 대해 빌드하기 위한 설계 고려 사항은 [ExpressRoute 프라이빗 피어링을 사용한 재해 복구를 위한 설계][DR]를 참조하세요.

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "ExpressRoute를 사용하여 연결하는 권장 방법"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "최적이 아닌 라스트 마일 연결"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 옵션"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
