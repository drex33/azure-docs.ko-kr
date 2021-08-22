---
title: Azure Virtual Network NAT에 대한 메트릭 및 경고
titleSuffix: Azure Virtual Network
description: Virtual Network NAT에 사용할 수 있는 Azure Monitor 메트릭 및 경고를 이해합니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 83c701ce38ac148c442efc33a60dae042335971a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297023"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT 메트릭

Azure Virtual Network NAT 게이트웨이 리소스는 다차원 메트릭을 제공합니다. 이러한 메트릭을 사용하여 작업을 관찰하고 [문제를 해결](troubleshoot-nat.md)할 수 있습니다.  SNAT 소모와 같은 중요한 문제에 대해 경고를 구성할 수 있습니다.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="인터넷으로의 아웃바운드를 위한 Virtual Network NAT">
</p>

*그림: 인터넷으로의 아웃바운드를 위한 Virtual Network NAT*

## <a name="metrics"></a>메트릭

NAT 게이트웨이 리소스는 Azure Monitor에서 다음과 같은 다차원 메트릭을 제공합니다.

| 메트릭 | 설명 | 권장 집계 | 차원 |
|---|---|---|---|
| 바이트 | 인바운드 및 아웃바운드 처리된 바이트 | 합계 | 방향(In; Out), 프로토콜(6 TCP; 17 UDP) |
| 패킷 수 | 인바운드 및 아웃바운드 처리된 패킷 | 합계 | 방향(In; Out), 프로토콜(6 TCP; 17 UDP) |
| 삭제된 패킷 수 | NAT 게이트웨이에서 삭제된 패킷 | 합계 | / |
| SNAT 연결 수 | 간격당 상태 전환 횟수 | 합계 | 연결 상태, 프로토콜(6 TCP; 17 UDP) |
| 총 SNAT 연결 수 | 현재 활성 SNAT 연결(~ SNAT 포트 사용 중) | 합계 | 프로토콜(6 TCP; 17 UDP) |


## <a name="alerts"></a>경고

메트릭에 대한 경고는 위의 각 [메트릭](#metrics)에 대해 Azure Monitor에서 구성할 수 있습니다.

## <a name="limitations"></a>제한 사항

Resource Health는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Virtual Network NAT](nat-overview.md)에 대한 자세한 정보
* [NAT 게이트웨이 리소스](nat-gateway-resource.md)에 대한 자세한 정보
* [Azure Monitor](../../azure-monitor/overview.md)에 대한 자세한 정보
* [NAT 게이트웨이 리소스 문제 해결](troubleshoot-nat.md)에 대해 알아봅니다.
* [UserVoice에서 Virtual Network NAT에 대해 다음에 빌드할 항목을 알려줍니다](https://aka.ms/natuservoice).