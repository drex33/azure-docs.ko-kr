---
title: Azure의 기본 아웃 바운드 액세스
titleSuffix: Azure Virtual Network
description: Azure에서 리소스 액세스에 대해 알아보기
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: 7bf14494052cb425a8bdbe3650cf75a6b7aaed5c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372924"
---
# <a name="default-outbound-access-in-azure"></a>Azure의 기본 아웃 바운드 액세스

Azure에서 명시적인 아웃 바운드 연결을 사용 하지 않고 가상 네트워크에서 생성 된 가상 컴퓨터에는 기본 아웃 바운드 공용 IP 주소가 할당 됩니다. 이 IP 주소는 리소스에서 인터넷으로의 아웃 바운드 연결을 사용하도록 설정합니다. 이 액세스를 기본 아웃 바운드 액세스라고 합니다. 

명시적 아웃 바운드 연결의 예는 가상 머신입니다.

* NAT 게이트웨이에 연결된 서브넷 내에서 생성됩니다.
* 아웃 바운드 규칙이 정의된 표준 load balancer의 백 엔드 풀에 있습니다.
* 기본 공용 부하 분산 장치의 백 엔드 풀에 있습니다.
* 공용 IP 주소가 있는 가상 머신은 명시적으로 연결됩니다.

## <a name="how-is-default-outbound-access-provided"></a>기본 아웃 바운드 액세스는 어떻게 제공되나요?

액세스에 사용 되는 공용 IPv4 주소를 기본 아웃 바운드 액세스 IP라고 합니다. 이 IP는 암시적이며 Microsoft에 속합니다. 이 IP 주소는 변경될 수 있으며 프로덕션 워크 로드에 의존하지 않는 것이 좋습니다.

## <a name="when-is-default-outbound-access-provided"></a>기본 아웃 바운드 액세스를 제공 하는 경우는 언제인가요?

Azure에 가상 머신을 배포하고 명시적인 아웃바운드 연결이 없는 경우 기본 아웃바운드 액세스 IP가 할당됩니다.
## <a name="why-is-disabling-default-outbound-access-recommended"></a>기본 아웃 바운드 액세스를 사용하지 않도록 설정하는 이유는 무엇인가요?

* 기본적으로 보안 적용
    
    * 제로 트러스트 네트워크 보안 원칙을 사용하여 기본적으로 인터넷에 가상 네트워크를 여는 것은 권장되지 않습니다.

* 명시적 및 암시적

    * 가상 네트워크의 리소스에 대한 액세스 권한을 부여 하는 경우 암시적이 아닌 명시적 연결 메서드를 사용하는 것이 좋습니다.

* IP 주소 손실

    * 기본 아웃 바운드 액세스 IP는 고객이 소유하지 않습니다. 이 IP는 변경될 수 있습니다.  이 IP대한 모든 종속성은 나중에 문제를 일으킬 수 있습니다.

## <a name="how-can-i-disable-default-outbound-access"></a>기본 아웃 바운드 액세스를 사용하지 않도록 설정하려면 어떻게 해야 하나요?

기본 아웃 바운드 액세스를 해제하는 방법에는 여러 가지가 있습니다.

1.  명시적인 아웃 바운드 연결 방법을 추가합니다.

    * NAT 게이트웨이를 가상 머신의 서브넷에 연결합니다.

    * 구성된 아웃 바운드 규칙과 표준 load balancer를 연결합니다.

    * 가상 컴퓨터의 네트워크 인터페이스에 공용 IP를 연결합니다.

2.  Virtual Machine Scale Sets에 대해 유연한 오케스트레이션 모드를 사용합니다.

    * 유연한 확장 집합은 기본적으로 안전합니다. 유연한 확장 집합을 통해 만든 모든 인스턴스에는 연결된 기본 아웃 바운드 액세스 IP가 없습니다. 자세한 내용은 [Virtual Machine Scale Sets에 대한 유연한 오케스트레이션 모드](../../virtual-machines/flexible-virtual-machine-scale-sets.md)를 참조하세요.

## <a name="if-i-need-outbound-access-what-is-the-recommended-way"></a>아웃 바운드 액세스가 필요한 경우 권장 되는 방법은 무엇인가요?

NAT 게이트웨이는 명시적인 아웃 바운드 연결을 사용하는 것이 좋습니다. 방화벽을 사용하여 이 액세스를 제공할 수도 있습니다.

## <a name="limitations"></a>제한 사항

* Windows 업데이트에 대한 연결이 필요할 수도 있습니다.
* 기본 아웃 바운드 액세스 IP는 조각화 된 패킷을 지원하지 않습니다. 

## <a name="next-steps"></a>다음 단계

Azure의 아웃 바운드 연결 및 Azure Virtual Network NAT (NAT 게이트웨이)에 대 한 자세한 내용은 다음을 참조하세요.

* [아웃바운드 연결을 위한 SNAT](../../load-balancer/load-balancer-outbound-connections.md).

* [Azure Virtual Network NAT란?](../nat-gateway/nat-overview.md)
