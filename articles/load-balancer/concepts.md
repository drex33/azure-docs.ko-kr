---
title: Azure Load Balancer 개념
description: Azure Load Balancer 개념에 대한 개요입니다.
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
ms.openlocfilehash: 946741c8aa70040dd0186deceab0fb090cf38c11
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271748"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer 알고리즘

Load Balancer는 몇 가지 기능을 UDP 및 TCP 애플리케이션 모두에 제공합니다.

## <a name="load-balancing-algorithm"></a>부하 분산 알고리즘

부하 분산기 규칙을 만들면 부하 분산의 프런트 엔드에서 백 엔드 풀로 인바운드 트래픽 흐름을 분산할 수 있습니다. Azure Load Balancer 인바운드 흐름(바이트)의 분포에 대해 5 튜플 해시 알고리즘을 사용합니다.  Load Balancer는 트래픽을 백 엔드 풀 인스턴스로 안내할 때 TCP/UDP 헤더 흐름의 헤더를 다시 작성합니다(Load Balancer기는 HTTP/HTTPS 헤더를 다시 작성하지 않음). Load Balancer 상태 프로브가 정상 백 엔드 엔드포인트를 나타내는 경우 백 엔드 인스턴스를 사용하여 새 트래픽 흐름을 수신할 수 있습니다.

기본적으로 Load Balancer는 5개의 튜플 해시를 사용합니다.

해시에는 다음이 포함됩니다.

- **원본 IP 주소**
- **원본 포트**
- **대상 IP 주소**
- **대상 포트**
- **사용 가능한 서버에 흐름을 매핑하기 위한 IP 프로토콜 번호**

## <a name="session-persistence"></a>세션 지속성

동일한 흐름의 패킷이 동일한 백 엔드 풀 인스턴스에 도착합니다. 그러나 클라이언트가 동일한 원본 IP에서 새 흐름을 시작하면 원본 포트가 변경됩니다. 결과적으로 5 튜플 해시로 인해 트래픽이 다른 백 엔드 엔드포인트로 이동할 수 있습니다. 

원본 IP 주소에 대한 세션 지속성은 2개 또는 3개의 튜플 해시를 사용하여 생성됩니다. 세션 지속성을 사용하도록 설정하면 동일한 클라이언트 IP 주소의 연속 요청이 동일한 가상 머신에서 처리됩니다. 부하 분산 모드에 대한 자세한 내용은 [Azure Load Balancer 배포 모드 구성을](./load-balancer-distribution-mode.md)참조하세요.

다음 이미지는 해시 기반 배포를 표시합니다.

![해시 기반 배포](./media/load-balancer-overview/load-balancer-distribution.png)

*그림: 해시 기반 배포*

## <a name="application-independence-and-transparency"></a>애플리케이션 독립성 및 투명성

Load Balancer는 TCP/UDP 애플리케이션 시나리오를 지원하며 흐름을 닫거나 시작하지 않습니다. 또한 Load Balancer는 흐름의 페이로드와 상호 작용하지 않습니다. 

- 애플리케이션 페이로드는 Load Balancer에 투명합니다. 모든 UDP 또는 TCP 애플리케이션을 지원할 수 있습니다.

Load Balancer는 계층 4에서 작동하며 애플리케이션 계층 게이트웨이 기능을 제공하지 않습니다. 프로토콜 핸드셰이크는 항상 클라이언트와 백 엔드 풀 인스턴스 사이에서 직접 발생합니다. 

- 부하 분산책은 TCP 페이로드와 상호 작용하지 않고 TLS 오프로드를 제공하지 않으므로 포괄적인 암호화된 시나리오를 빌드할 수 있습니다. 부하 분산 장치를 사용하면 VM 자체에서 TLS 연결을 종료하여 TLS 애플리케이션에 대한 대규모 확장이 구현됩니다. 예를 들어 TLS 세션 키 용량은 백 엔드 풀에 추가하는 VM의 수와 유형으로만 제한됩니다.

인바운드 흐름에 대한 응답은 항상 가상 머신의 응답입니다. 흐름이 가상 머신에 도착하면 원래 원본 IP 주소도 유지됩니다.

- 모든 엔드포인트는 VM을 통해 응답합니다. 예를 들어 TCP 핸드셰이크는 클라이언트와 선택한 백 엔드 VM 간에 발생합니다. 프런트 엔드에 요청에 대한 응답은 백 엔드 VM에서 생성한 응답입니다. 프런트 엔드에 대한 연결의 유효성을 성공적으로 검사하는 경우 하나 이상의 백 엔드 가상 머신에 대한 전체 연결의 유효성을 검사하게 됩니다.


## <a name="next-steps"></a>다음 단계

- Azure Load Balancer를 구성하는 [구성 요소](components.md)에 대해 알아봅니다.
- Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요. 하나의 부하 분산 장치를 만들고, 사용자 지정 IIS 확장이 설치된 VM을 만들고, VM 간에 웹앱의 부하를 분산시킵니다.
- [Azure Load Balancer 아웃바운드 연결](load-balancer-outbound-connections.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)에 대해 자세히 알아보세요.
