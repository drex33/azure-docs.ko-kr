---
title: Azure의 Load Balancer TCP 재설정 및 유휴 시간 제한
titleSuffix: Azure Load Balancer
description: 이 문서에서는 유휴 시간 초과 시 양방향 TCP RST 패킷이 포함된 Azure Load Balancer에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 9d18f5e674a5d30049b93a4273df1ecc8a37595c
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634868"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer TCP 재설정 및 유휴 시간 제한

[표준 Load Balancer](./load-balancer-overview.md)를 지정된 규칙에 대해 TCP Reset on Idle을 사용하도록 설정하여 시나리오용으로 예측 가능성이 더 높은 애플리케이션 동작을 만들 수 있습니다. Load Balancer의 기본 동작은 흐름의 유휴 시간이 초과되면 흐름을 자동으로 끊는 것입니다.  이 기능을 사용하도록 설정하면 Load Balancer가 유휴 시간 초과 시 양방향 TCP Reset(TCP RST 패킷)을 전송합니다.  이 패킷은 연결 시간이 초과되어 더 이상 연결을 사용할 수 없음을 애플리케이션 엔드포인트에 알립니다.  엔드포인트는 필요한 경우 새 연결을 즉시 설정할 수 있습니다.

![Load Balancer TCP 재설정](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP 재설정

이 기본 동작을 변경하고 인바운드 NAT 규칙, 부하 분산 규칙 및 [아웃바운드 규칙](./load-balancer-outbound-connections.md#outboundrules)에서 유휴 시간 초과 시 TCP 재설정 보내기를 사용하도록 설정합니다.  규칙에서 사용하도록 설정하면 Load Balancer는 모든 일치 흐름의 유휴 시간 초과 시 클라이언트 및 서버 엔드포인트 둘 다에 양방향 TCP 재설정(TCP RST 패킷)을 보냅니다.

TCP RST 패킷을 수신하는 엔드포인트는 즉시 해당 소켓을 닫습니다. 그러면 연결 해제가 발생했으며 동일한 TCP 연결의 이후 통신은 실패할 것이라는 알림이 곧바로 엔드포인트에 제공됩니다.  애플리케이션은 소켓이 닫히면 연결을 제거할 수 있으며 필요에 따라 TCP 연결 시간이 초과될 때까지 기다리지 않고 연결을 다시 설정할 수 있습니다.

이렇게 하면 많은 시나리오에서 흐름의 유휴 시간 초과를 새로 고치기 위해 TCP(또는 애플리케이션 계층) 연결 유지를 보내는 것을 줄일 수 있습니다. 

유휴 기간이 구성에서 허용하는 기간을 초과하거나 TCP 재설정을 사용하도록 설정하면 애플리케이션이 원치 않는 동작을 보여 주는 경우 계속 TCP 연결 유지(또는 애플리케이션 계층 연결 유지)를 사용하여 TCP 연결의 활동성을 모니터링해야 할 수도 있습니다.  또한 연결이 경로의 특정 위치, 특히 애플리케이션 계층 연결 유지에 프록시 설정된 경우에도 연결 유지가 유용할 수 있습니다.  

종단 간 시나리오 전체를 신중하게 검토하여 TCP 재설정을 사용하도록 설정하고 유휴 시간 초과를 조정하는 것이 유리한지, 애플리케이션이 원하는 대로 동작하게 하려면 추가 조치가 필요한지 여부를 결정합니다.

## <a name="configurable-tcp-idle-timeout"></a>구성 가능한 TCP 유휴 시간 제한

Azure Load Balancer의 유휴 시간 제한 범위는 다음과 같습니다.
-  아웃바운드 규칙의 경우 4분~100분
-  Load Balancer 규칙 및 인바운드 NAT 규칙의 경우 4분~30분

기본적으로 4분으로 설정되어 있습니다. 비활성 기간이 시간 제한 값보다 긴 경우 클라이언트와 클라우드 서비스 간의 TCP 또는 HTTP 세션이 유지되지 않을 수 있습니다.

연결이 닫혀 있는 경우 클라이언트 애플리케이션이 다음 오류 메시지를 수신할 수 있습니다. “기본 연결이 닫혔습니다. 활성 상태로 유지될 것으로 예상된 연결이 서버에서 닫혔습니다.”

일반적인 방법은 TCP 연결 유지를 사용하는 것입니다. 이 방법은 더 오랜 기간 동안 연결을 활성 상태로 유지합니다. 자세한 내용은 이러한 [.NET 예제](/dotnet/api/system.net.servicepoint.settcpkeepalive)를 참조하세요. 연결 유지를 사용하면 연결 비활성화 기간 동안 패킷이 전송됩니다. 연결 유지 패킷은 유휴 시간 제한 값에 도달하지 않고 연결이 장기간 유지되도록 합니다.

이 설정은 인바운드 연결에서만 작동합니다. 연결 끊김을 방지하려면 유휴 시간 제한 설정보다 낮은 간격으로 TCP 연결 유지를 구성하거나 유휴 시간 제한 값을 늘립니다. 이러한 시나리오를 지원하기 위해 구성 가능한 유휴 시간 제한에 대한 지원이 추가되었습니다.

TCP 연결 유지는 배터리 수명이 제한되지 않는 시나리오에서 작동합니다. 모바일 애플리케이션에는 권장되지 않습니다. 모바일 애플리케이션에서 TCP 연결 유지를 사용하면 디바이스 배터리가 더 빨리 방전될 수 있습니다.


## <a name="limitations"></a>제한 사항

- TCP 재설정은 ESTABLISHED 상태에서 TCP 연결 중에만 전송됩니다.
- TCP 유휴 시간 제한은 UDP 프로토콜의 부하 분산 규칙에 영향을 주지 않습니다.
- 네트워크 가상 어플라이언스가 경로에 있는 경우, ILB HA 포트에 대해 TCP 재설정이 지원되지 않습니다. NVA에서 TCP 재설정을 통해 아웃바운드 규칙을 사용하는 것이 해결 방법일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [표준 Load Balancer](./load-balancer-overview.md)에 대해 알아봅니다.
- [아웃바운드 규칙](./load-balancer-outbound-connections.md#outboundrules)에 대해 알아봅니다.
- [유휴 시간 초과 시 TCP RST 구성](load-balancer-tcp-idle-timeout.md)
