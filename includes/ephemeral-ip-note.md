---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 35b4f49638d64a1de322fc712602a2d6cbad290a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019227"
---
> [!NOTE]
> Azure는 공용 IP 주소가 할당되지 않았거나 내부 기본 Azure Load Balancer의 백 엔드 풀에 있는 Azure Virtual Machines에 대한 기본 아웃바운드 액세스 IP를 제공합니다. 기본 아웃바운드 액세스 IP 메커니즘은 구성할 수 없는 아웃바운드 IP 주소를 제공합니다. 
>
> 기본 아웃바운드 액세스에 대한 자세한 내용은 [Azure에서 기본 아웃바운드 액세스](../articles/virtual-network/ip-services/default-outbound-access.md)를 참조하세요.
>
>공용 IP 주소가 가상 머신에 할당되거나 아웃바운드 규칙이 있거나 없는 표준 Load Balancer의 백 엔드 풀에 가상 머신이 배치되면 기본 아웃바운드 IP가 비활성화됩니다. [Azure Virtual Network NAT](../articles/virtual-network/nat-gateway/nat-overview.md) 게이트웨이 리소스가 가상 머신의 서브넷에 할당되면 기본 아웃바운드 액세스 IP가 비활성화됩니다.
>
> 유연한 오케스트레이션 모드의 Virtual Machine Scale Sets에서 만든 가상 머신에는 기본 아웃바운드 액세스 권한이 없습니다.
>
> Azure의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결에 대한 SNAT(Source Network Address Translation) 사용](../articles/load-balancer/load-balancer-outbound-connections.md)을 참조하세요.