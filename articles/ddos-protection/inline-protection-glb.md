---
title: 게이트웨이 Load Balancer 및 파트너 NVA를 통한 인라인 L7 DDoS 보호
description: 게이트웨이 Load Balancer 및 파트너 Nva에서 인라인 L7 DDoS Protection를 만들고 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.author: yitoh
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 49e4bc5dbe2214cafe039ceb0d6163a42f388629
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715324"
---
# <a name="inline-l7-ddos-protection-with-gateway-load-balancer-and-partner-nvas"></a>게이트웨이 Load Balancer와 파트너 Nva 인라인 L7 DDoS Protection

Azure DDoS Protection 항상 켜져 있지만 인라인이 아닙니다. 완화 될 때까지 공격이 검색 된 시간부터 30-60 초 정도 걸립니다. Azure DDoS Protection Standard는 또한 L3/4 (네트워크 계층)에서 작동 하며 패킷 페이로드 (예: 응용 프로그램 계층 (L7))를 검사 하지 않습니다.  
대기 시간에 매우 중요 하며 DDoS 보호를 시작 하는 데 30-60 초 동안 대기 하는 시간을 허용할 수 없는 워크 로드는 인라인 보호가 필요 합니다. 인라인 보호를 위해서는 모든 트래픽이 항상 DDoS 보호 파이프라인을 통과 해야 합니다. 또한 웹 보호 또는 UDP (게임 워크 로드 보호)와 같은 시나리오의 경우 패킷 페이로드를 검사 하 여 응용 프로그램 계층 (L7)의 취약성을 악용 하는 극단적인 낮은 볼륨 공격을 완화 하는 것이 중요 합니다. 

게이트웨이 Load Balancer와 함께 배포 되 고 Azure DDoS Protection 표준과 통합 된 파트너는 고성능 및 고가용성 시나리오에 대 한 포괄적인 인라인 L7 DDoS Protection을 제공 합니다. 인라인 L7 DDoS Protection Azure DDoS Protection 표준과 결합 되어 대규모 및 낮은 볼륨 DDoS 공격에 대 한 포괄적인 L3-L7 보호 기능을 제공 합니다. 

## <a name="what-is-a-gateway-load-balancer"></a>게이트웨이 Load Balancer 이란?
게이트웨이 Load Balancer는 타사 nva (네트워크 가상 어플라이언스)를 사용 하는 고성능 및 고가용성 시나리오를 위해 특별히 제공 되는 Azure Load Balancer의 SKU입니다.

게이트웨이 LB의 기능을 사용 하 여 Nva를 간편 하 게 배포, 확장 및 관리할 수 있습니다. 게이트웨이 LB를 공용 끝점에 연결 하는 것은 한 번만 클릭 하면 됩니다.  방화벽, 고급 패킷 분석, 침입 감지 및 방지 시스템, 요구 사항에 맞는 사용자 지정 시나리오 등 다양 한 시나리오에 대 한 어플라이언스를 게이트웨이 LB의 네트워크 경로에 삽입할 수 있습니다. Nva를 사용 하는 시나리오에서는 흐름이 ' 대칭 '이 되도록 하는 것이 특히 중요 합니다. 이렇게 하면 세션이 유지 되 고 대칭 이동 됩니다. 게이트웨이 LB는 백 엔드 풀의 특정 인스턴스에 대 한 흐름 대칭을 유지 관리 합니다.

게이트웨이 Load Balancer에 대 한 자세한 내용은 [게이트웨이 LB](../load-balancer/gateway-overview.md) 제품 및 설명서를 참조 하세요. 

## <a name="inline-ddos-protection-with-gateway-lb-and-partner-nvas"></a>게이트웨이 LB 및 파트너 Nva를 사용 하 여 인라인 DDoS 보호

대기 시간이 긴 중요 한 작업 (예: 게임)에 대 한 DDoS 공격으로 인해 가동 중지 시간이 2-10 초에서 발생 하 여 가용성이 중단 될 수 있습니다. 게이트웨이 Load Balancer 관련 된 Nva 인터넷 트래픽의 수신 경로에 삽입 되도록 하 여 이러한 워크 로드를 보호할 수 있도록 합니다. 가상 머신에서 표준 공용 Load Balancer 프런트 엔드 또는 IP 구성에 연결 되 면 응용 프로그램 끝점에서 들어오고 나가는 트래픽이 게이트웨이 LB로 전송 되도록 추가 구성이 필요 하지 않습니다. 

인바운드 트래픽은 항상 경로에서 Nva을 통해 검사 되 고 완전 한 트래픽은 백 엔드 인프라 (게이머 서버)로 반환 됩니다. 

트래픽이 소비자 가상 네트워크에서 공급자 가상 네트워크로 전달 된 다음 소비자 가상 네트워크로 돌아갑니다. 소비자 가상 네트워크 및 공급자 가상 네트워크는 다른 구독, 테 넌 트 또는 지역에 있을 수 있으며 유연 하 고 관리를 용이 하 게 해줍니다.

![게이트웨이 부하 분산 장치를 통한 DDoS 인라인 보호](./media/ddos-glb.png)
 
표준 공용 Load Balancer의 VNet 또는 가상 머신의 VNet에서 Azure DDoS Protection Standard를 사용 하도록 설정 하면 L3/4 DDoS 공격 으로부터 보호 됩니다. 
1.  인터넷에서 필터링 되지 않은 게임 트래픽이 Load Balancer 게임 서버의 공용 IP로 전달 됩니다. 
2.  필터링 되지 않은 게임 트래픽은 연결 된 게이트웨이 Load Balancer 개인 IP로 리디렉션됩니다. 
3.  필터링 되지 않은 게임 트래픽은 파트너 Nva를 통해 실시간으로 DDoS 공격에 대해 검사 됩니다. 
4.  필터링 된 게임 트래픽은 최종 처리를 위해 게임 서버로 다시 전송 됩니다.
5.  Load Balancer 게이머 서버에서 Azure DDoS Protection 표준은 L3/4 DDoS 공격 으로부터 보호 하 고, DDoS 보호 정책은 게임 서버 트래픽 프로필 및 응용 프로그램 규모에 맞게 자동으로 조정 됩니다. 

## <a name="next-steps"></a>다음 단계
- [인라인 L7 DDoS protection 파트너](https://aka.ms/inlineddospartners) 에 대 한 자세한 정보
- [Azure DDoS Protection Standard](./ddos-protection-overview.md) 에 대 한 자세한 정보
- [게이트웨이 Load Balancer](../load-balancer/gateway-overview.md) 에 대 한 자세한 정보