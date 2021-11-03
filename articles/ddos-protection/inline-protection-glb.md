---
title: 게이트웨이 Load Balancer 및 파트너 NLA를 통해 인라인 L7 DDoS 보호
description: 게이트웨이 Load Balancer 및 파트너 NLA를 사용하여 인라인 L7 DDoS Protection 만들고 사용하도록 설정하는 방법을 알아봅니다.
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
ms.openlocfilehash: 79e4507cb573dfe6893081df737a0e604810fbc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053653"
---
# <a name="inline-l7-ddos-protection-with-gateway-load-balancer-and-partner-nvas"></a>게이트웨이 Load Balancer 및 파트너 NLA를 통해 인라인 L7 DDoS Protection

Azure DDoS Protection 항상 켜지지만 인라인은 아니며 공격이 감지된 시간부터 완화될 때까지 30-60초가 걸립니다. Azure DDoS Protection Standard는 L3/4(네트워크 계층)에서도 작동하며 패킷 페이로드(예: 애플리케이션 계층(L7))를 검사하지 않습니다.  
대기 시간에 매우 민감하고 DDoS 보호가 시작되려면 30-60초의 진입 시간 동안 허용되지 않는 워크로드에는 인라인 보호가 필요합니다. 인라인 보호에는 모든 트래픽이 항상 DDoS 보호 파이프라인을 통과해야 합니다. 또한 웹 보호 또는 UDP(게임 워크로드 보호)와 같은 시나리오의 경우 패킷 페이로드를 검사하여 애플리케이션 계층(L7)의 취약성을 악용하는 매우 낮은 볼륨 공격을 완화하는 것이 중요합니다. 

게이트웨이 Load Balancer 배포되고 Azure DDoS Protection Standard와 통합된 파트너 NVA는 고성능 및 고가용성 시나리오를 위한 포괄적인 인라인 L7 DDoS Protection 제공합니다. Azure DDoS Protection Standard와 결합된 인라인 L7 DDoS Protection 볼륨뿐만 아니라 볼륨이 적은 DDoS 공격에 대한 포괄적인 L3-L7 보호를 제공합니다. 

## <a name="what-is-a-gateway-load-balancer"></a>게이트웨이 Load Balancer 무엇인가요?
게이트웨이 Load Balancer 타사 NLA(네트워크 가상 어플라이언스)를 사용하여 고성능 및 고가용성 시나리오를 위해 특별히 제공된 Azure Load Balancer SKU입니다.

게이트웨이 LB의 기능을 사용하면 쉽게 NLA를 배포, 크기 조정 및 관리할 수 있습니다. 게이트웨이 LB를 퍼블릭 엔드포인트에 연결하려면 한 번 클릭만 하면 됩니다.  방화벽, 고급 패킷 분석, 침입 검색 및 방지 시스템 또는 게이트웨이 LB를 사용하여 네트워크 경로에 요구 사항에 맞는 사용자 지정 시나리오와 같은 다양한 시나리오에 대한 어플라이언스를 삽입할 수 있습니다. NLA가 있는 시나리오에서는 흐름이 '대칭'인 것이 특히 중요합니다. 이렇게 하면 세션이 유지 관리되고 대칭이 유지됩니다. 게이트웨이 LB는 백 엔드 풀의 특정 인스턴스에 대한 흐름 대칭을 유지 관리합니다.

게이트웨이 Load Balancer 대한 자세한 내용은 게이트웨이 [LB](https://aka.ms/gatewaylb) 제품 및 설명서를 참조하세요. 

## <a name="inline-ddos-protection-with-gateway-lb-and-partner-nvas"></a>게이트웨이 LB 및 파트너 NLA를 통해 인라인 DDoS 보호

대기 시간이 중요한 워크로드(예: 게임)에 대한 DDoS 공격은 2~10초 범위의 중단으로 인해 가용성이 중단됩니다. 게이트웨이 Load Balancer 관련 NLA가 인터넷 트래픽의 수신 경로에 삽입되도록 하여 이러한 워크로드를 보호할 수 있습니다. 가상 머신의 표준 공용 Load Balancer 프런트 엔드 또는 IP 구성에 연결되면 애플리케이션 엔드포인트에서 오는 트래픽이 게이트웨이 LB로 전송되도록 하기 위해 추가 구성이 필요하지 않습니다. 

인바운드 트래픽은 항상 경로의 NLA를 통해 검사되고 클린 트래픽은 백 엔드 인프라(게임러 서버)로 반환됩니다. 

트래픽은 소비자 가상 네트워크에서 공급자 가상 네트워크로 흐른 다음 소비자 가상 네트워크로 돌아갑니다. 소비자 가상 네트워크 및 공급자 가상 네트워크는 서로 다른 구독, 테넌트 또는 지역에 있을 수 있으므로 유연성과 관리 용이성이 향상됩니다.

![게이트웨이 부하 분산 장치에서 DDoS 인라인 보호](./media/ddos-glb.png)
 
가상 머신의 표준 공용 Load Balancer 프런트 엔드 또는 VNet의 VNet에서 Azure DDoS Protection 표준을 사용하도록 설정하면 L3/4 DDoS 공격으로부터 보호할 수 있습니다. 
1.  인터넷에서 필터링되지 않은 게임 트래픽은 Load Balancer 게임 서버의 공용 IP로 이동합니다. 
2.  필터링되지 않은 게임 트래픽은 연결된 게이트웨이 Load Balancer 개인 IP로 리디렉션됩니다. 
3.  필터링되지 않은 게임 트래픽은 파트너 NLA를 통해 실시간으로 DDoS 공격에 대해 검사됩니다. 
4.  필터링된 게임 트래픽은 최종 처리를 위해 게임 서버로 다시 전송됩니다.
5.  Azure DDoS Protection Standard는 L3/4 DDoS 공격으로부터 보호할 Load Balancer 있으며, DDoS 보호 정책은 게임 서버 트래픽 프로필 및 애플리케이션 규모에 맞게 자동으로 조정됩니다. 

## <a name="next-steps"></a>다음 단계
- [인라인 L7 DDoS 보호 파트너에](https://aka.ms/inlineddospartners) 대해 자세히 알아보기
- [Azure DDoS Protection Standard에](https://aka.ms/ddosprotectiondocs) 대해 자세히 알아보기
- [게이트웨이 Load Balancer](https://aka.ms/gatewaylb) 대해 자세히 알아보기
