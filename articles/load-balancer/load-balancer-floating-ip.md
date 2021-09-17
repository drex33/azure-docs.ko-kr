---
title: Azure Load Balancer 부동 IP 구성
description: Azure Load Balancer 부동 IP의 개요
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: e103af26d62518dabb5314c79c61335a2791417a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479871"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer 부동 IP 구성

부하 분산 장치는 몇 가지 기능을 UDP 및 TCP 애플리케이션 모두에 제공합니다.

## <a name="floating-ip"></a>부동 IP

일부 애플리케이션 시나리오는 백 엔드 풀의 단일 VM에서 여러 애플리케이션 인스턴스가 동일한 포트를 사용하는 것을 선호하거나 필요로 합니다. 포트 다시 사용의 일반적인 예는 다음과 같습니다. 
- 고가용성을 위한 클러스터링
- 네트워크 가상 어플라이언스
- 다시 암호화 하지 않고 여러 TLS 끝점 노출 

여러 규칙 전반에 백 엔드 포트를 재사용하려면 규칙 정의에 부동 IP를 사용하도록 설정해야 합니다.

부동 IP를 사용 하도록 설정 하면 Azure에서 IP 주소 매핑을 백 엔드 인스턴스의 IP 대신 Load Balancer 프런트 엔드의 프런트 엔드 IP 주소로 변경 합니다. 

부동 IP가 없는 경우 Azure는 VM 인스턴스의 IP를 노출 합니다. 부동 IP를 사용하도록 설정하면 Load Balancer의 프런트 엔드 IP에 매핑되는 IP 주소가 변경되어 추가적인 유연성이 제공됩니다. [여기](load-balancer-multivip-overview.md)를 참조하세요.

## <a name="limitations"></a><a name = "limitations"></a>제한 사항

- 부동 IP는 현재 부하 분산 시나리오의 보조 IP 구성에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요.
- [Azure Load Balancer 아웃바운드 연결](load-balancer-outbound-connections.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)에 대해 자세히 알아보세요.
