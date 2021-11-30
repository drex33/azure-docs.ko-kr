---
title: Azure Network Watcher에서 IP 흐름 확인 소개 | Microsoft Docs
description: 이 페이지는 Network Watcher IP 흐름 확인 기능에 대한 개요를 제공합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: fe22bd68e6f9f943b345a34dad47e3d3d408983d
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133288907"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Azure Network Watcher에서 IP 흐름 확인 소개

IP 흐름 확인은 가상 머신 간 패킷이 허용 또는 거부되는지를 확인합니다. 해당 정보는 방향, 프로토콜, 로컬 IP, 원격 IP, 로컬 포트 및 원격 포트로 구성됩니다. 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙의 이름이 반환됩니다. 모든 원본 또는 대상 IP를 선택할 수 있는 동안 IP 흐름 확인을 통해 관리자는 인터넷 간 및 온-프레미스 환경 간 연결 문제를 신속하게 진단할 수 있습니다.

IP 흐름 확인은 서브넷 또는 가상 머신 NIC와 같은 네트워크 인터페이스에 적용되는 모든 NSG(네트워크 보안 그룹)에 대한 규칙을 검토합니다. 그런 다음 해당 네트워크 인터페이스 간에서 구성된 설정에 따라 트래픽 흐름이 확인됩니다. IP 흐름 확인은 네트워크 보안 그룹의 규칙이 가상 머신과의 수신 또는 송신 트래픽을 차단 하는지 확인 하는 데 유용 합니다. 이제 NSG 규칙 평가와 함께 Azure Virtual Network 관리자 규칙도 평가 됩니다.

[AVNM (Azure Virtual Network Manager)](../virtual-network-manager/overview.md) 은 사용자가 구독에서 가상 네트워크를 전역적으로 그룹화, 구성, 배포 및 관리할 수 있도록 하는 관리 서비스입니다. AVNM 보안 구성을 통해 사용자는 전역 수준에서 하나 이상의 네트워크 보안 그룹에 적용할 수 있는 규칙의 컬렉션을 정의할 수 있습니다. 이러한 보안 규칙은 NSG (네트워크 보안 그룹) 규칙 보다 우선 순위가 높습니다. 중요 한 차이점은 관리자 규칙은 각 vnet으로 버블링 되는 거 버 넌 스 및 보안 팀에서 제어 하는 중앙 위치에서 ANM이 제공 하는 리소스입니다. NSGs는 각 서브넷 또는 NIC 수준에서 적용 되는 vnet 소유자가 제어 하는 리소스입니다.

Network Watcher의 인스턴스는 IP 흐름을 실행하려는 모든 지역에서 만들어져야 합니다. Network Watcher는 지역 서비스이며 동일한 지역의 리소스에 대해서만 실행할 수 있습니다. 사용되는 인스턴스는 IP 흐름 확인의 결과에 영향을 주지 않습니다. NIC 또는 서브넷과 연결된 경로가 여전히 반환되기 때문입니다.

![1][1]

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 포털을 통해 특정 가상 머신에 대해 패킷이 허용되거나 거부되는 경우를 알아봅니다. [포털을 사용하여 IP 흐름 확인으로 VM에서 트래픽이 허용되는지 확인](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

