---
title: 게이트웨이 부하 분산 장치 (미리 보기)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer에 대 한 게이트웨이 부하 분산 장치 SKU의 개요입니다.
ms.service: load-balancer
author: asudbring
ms.author: allensu
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: ignite-fall-2021
ms.openlocfilehash: 22548d5bb75bb70c20296ddd68fde5d232213403
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103147"
---
# <a name="gateway-load-balancer-preview"></a>게이트웨이 Load Balancer (미리 보기)

게이트웨이 Load Balancer는 타사 네트워크 nva (네트워크 가상 어플라이언스)를 사용 하 여 고성능 및 고가용성 시나리오를 위한 Azure Load Balancer 포트폴리오의 SKU입니다. 게이트웨이 Load Balancer 기능을 사용 하 여 Nva를 쉽게 배포 하 고, 확장 하 고, 관리할 수 있습니다. 게이트웨이 Load Balancer를 공용 끝점에 연결 하려면 한 번만 클릭 하면 됩니다. 

다음과 같은 다양 한 종류의 시나리오에 맞게 장치를 투명 하 게 삽입할 수 있습니다.

* 방화벽
* 고급 패킷 분석
* 침입 감지 및 방지 시스템
* 트래픽 미러링
* 인라인 DDoS
* 사용자 지정 어플라이언스

게이트웨이 Load Balancer을 사용 하면 추가적인 관리 오버 헤드 없이 고급 네트워크 기능을 쉽게 추가 하거나 제거할 수 있습니다. 응용 프로그램 전에 공용 끝점에 대 한 모든 트래픽이 먼저 어플라이언스로 전송 되는지 확인 하는 데 필요한 회선 내 기술을 제공 합니다. Nva를 사용 하는 시나리오에서는 흐름이 대칭으로 이동 하는 것이 특히 중요 합니다. 게이트웨이 Load Balancer는 흐름 대칭과 함께 백 엔드 풀의 특정 인스턴스에 대 한 흐름의 연결이 유지 됩니다. 따라서 추가 수동 구성 없이 네트워크 가상 어플라이언스에 대 한 일관 된 경로가 보장 됩니다. 따라서 패킷이 양방향에서 동일한 네트워크 경로를 통과 하 고이 키 기능이 필요한 어플라이언스는 원활 하 게 작동할 수 있습니다.

상태 프로브는 모든 포트에서 수신 대기 하 고 HA 포트 규칙을 사용 하 여 백 엔드 인스턴스로 트래픽을 라우팅합니다. 게이트웨이 Load Balancer 전송 되는 트래픽은 VXLAN 프로토콜을 사용 합니다. 

> [!IMPORTANT]
> 게이트웨이 Load Balancer는 현재 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="benefits"></a>이점

게이트웨이 Load Balancer에는 다음과 같은 이점이 있습니다.

* 가상 어플라이언스를 네트워크 경로에 투명 하 게 통합 합니다.

* 네트워크 경로에서 네트워크 가상 어플라이언스를 쉽게 추가 하거나 제거 합니다. 

* 비용을 관리 하면서 쉽게 크기를 조정 하세요.

* 네트워크 가상 어플라이언스 가용성 향상.

* 지역 및 구독에서 응용 프로그램 체인 연결

표준 공용 부하 분산 장치 또는 가상 머신의 IP 구성을 게이트웨이 Load Balancer에 연결할 수 있습니다. 가상 컴퓨터의 표준 공용 Load Balancer 프런트 엔드 또는 IP 구성에 연결 되 면 응용 프로그램 끝점에서 들어오고 나가는 트래픽이 게이트웨이 Load Balancer 전송 되도록 하기 위해 추가 구성이 필요 하지 않습니다.

트래픽은 소비자 가상 네트워크에서 공급자 가상 네트워크로 이동 합니다. 그러면 트래픽이 소비자 가상 네트워크로 반환 됩니다. 소비자 가상 네트워크 및 공급자 가상 네트워크는 관리 오버 헤드를 제거 하는 다른 구독, 테 넌 트 또는 지역에 있을 수 있습니다.

:::image type="content" source="./media/gateway-overview/gateway-load-balancer-diagram.png" alt-text="게이트웨이 부하 분산 장치 다이어그램":::

*그림: 게이트웨이 부하 분산 장치 다이어그램*

## <a name="components"></a>구성 요소

게이트웨이 Load Balancer는 다음과 같은 구성 요소로 구성 됩니다.

* **프런트 엔드 ip 구성** -게이트웨이의 ip 주소 Load Balancer입니다. 이 IP는 전용입니다. 

* **부하 분산 규칙** -부하 분산 장치 규칙은 들어오는 트래픽이 백 엔드 풀 내의 모든 인스턴스에 배포 되는 방식을 정의 하는 데 사용 됩니다. 부하 분산 규칙은 지정된 프런트 엔드 IP 구성 및 포트를 여러 백 엔드 IP 주소 및 포트에 매핑합니다. 

    * 게이트웨이 Load Balancer 규칙은 HA 포트 규칙만 될 수 있습니다. 

    * 게이트웨이 Load Balancer 규칙은 최대 2 개의 백 엔드 풀과 연결할 수 있습니다. 

* **백 엔드 풀 (s)** -들어오는 요청을 처리 하는 가상 머신 확장 집합의 가상 머신 또는 인스턴스 그룹입니다. 대량의 들어오는 트래픽을 충족하도록 비용 효율적으로 크기를 조정하기 위해 컴퓨팅 지침에서는 일반적으로 백 엔드 풀에 더 많은 인스턴스를 추가하는 것을 권장합니다. Load Balancer는 인스턴스의 크기를 확장하거나 축소하는 경우 자동 재구성을 통해 자체적으로 즉시 재구성됩니다. 백 엔드 풀에서 Vm을 추가 하거나 제거 하면 추가 작업 없이 부하 분산 장치를 재구성 합니다. 백 엔드 풀의 범위는 단일 가상 네트워크의 임의 가상 머신입니다. 

* **Tunnel 인터페이스** -게이트웨이 부하 분산 장치 백 엔드 풀에는 터널 인터페이스 라는 또 다른 구성 요소가 있습니다. 터널 인터페이스를 사용 하면 백 엔드의 어플라이언스에서 네트워크 흐름이 예상 대로 처리 되는지 확인할 수 있습니다. 각 백 엔드 풀에는 최대 2 개의 터널 인터페이스가 있을 수 있습니다. Tunnel 인터페이스는 내부 또는 외부 일 수 있습니다. 백 엔드 풀에 들어오는 트래픽의 경우 외부 유형을 사용 해야 합니다. 어플라이언스에서 응용 프로그램으로 이동 하는 트래픽의 경우 내부 형식을 사용 해야 합니다.

* **체인** -게이트웨이 Load Balancer는 표준 공용 Load Balancer 프런트 엔드 또는 가상 컴퓨터의 공용 IP 구성에서 참조할 수 있습니다. 특정 시퀀스에 고급 네트워킹 기능을 추가 하는 것을 서비스 체인 이라고 합니다. 따라서이 참조를 체인 이라고 합니다.

## <a name="pricing"></a>가격 책정

미리 보기 중에는 게이트웨이 Load Balancer에 대 한 요금이 부과 되지 않습니다. 

일반 공급 릴리스 중에 적용 되는 가격은 [Load Balancer 가격 책정](https://azure.microsoft.com/pricing/details/load-balancer/)을 참조 하세요.

## <a name="limitations"></a>제한 사항

* 게이트웨이 Load Balancer 글로벌 Load Balancer 계층에서 작동 하지 않습니다.
* 게이트웨이 Load Balancer 포털 지원은 현재 중국 클라우드 및 정부 클라우드 지역에서 사용할 수 없습니다. CLI, PowerShell, 템플릿 또는 대안을 사용할 수 있습니다.
* 교차 테 넌 트 체인은 Azure Portal를 통해 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- 게이트웨이 부하 분산 장치를 만들려면 [Azure Portal를 사용 하 여 게이트웨이 Load Balancer 만들기](tutorial-gateway-portal.md) 를 참조 하세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
