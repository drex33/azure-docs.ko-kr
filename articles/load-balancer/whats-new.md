---
title: Azure Load Balancer의 새로운 기능
description: 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능 및 예정된 변경 내용과 같은 Azure Load Balancer의 새로운 기능을 알아봅니다.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: anavin
ms.openlocfilehash: 804181cf5af496245954a46d1bd756d4e971d081
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132763433"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure Load Balancer의 새로운 기능

Azure Load Balancer는 정기적으로 업데이트됩니다. 최신 공지 사항을 최신 상태로 유지합니다. 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능(해당하는 경우)

[여기](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)에서 최신 Azure Load Balancer 업데이트를 찾아 RSS 피드를 구독할 수도 있습니다.

## <a name="recent-releases"></a>최근 릴리스

| Type |Name |Description  |추가된 날짜  |
| ------ |---------|---------|---------|
| SKU | [게이트웨이 Load Balancer 공개 미리 보기](https://azure.microsoft.com/updates/gateway-load-balancer-preview/) | 게이트웨이 Load Balancer은 Azure에서 타사 Nva (네트워크 가상 어플라이언스)의 가용성을 배포 하 고, 확장 하 고, 향상 시킬 수 있도록 하는 완전히 관리 되는 서비스입니다. 한 번의 클릭으로 방화벽, 인라인 DDoS 어플라이언스, 심층 패킷 검사 시스템 또는 고유한 사용자 지정 어플라이언스를 네트워크 경로에 투명 하 게 추가할 수 있습니다.| 2021년 11월 |
| 기능 | [IP 기반 백 엔드 풀에 대 한 지원 (일반 공급)](https://azure.microsoft.com/updates/iplbga/) | Azure Load Balancer는 IPv4 또는 IPv6 주소를 통해 백 엔드 풀에서 리소스 추가 및 제거를 지원합니다. 이렇게 하면 Load Balancer와 연결된 컨테이너, 가상 머신 및 가상 머신 확장 집합을 쉽게 관리할 수 있습니다. 또한 관련 리소스를 만들기 전에 IP 주소를 백 엔드 풀의 일부로 예약할 수 있습니다. [여기](backend-pool-management.md)에서 자세히 알아보세요.|2021년 3월 |
| 기능 | [표준 SKU Load Balancer 및 공용 IP에 대한 인스턴스 메타데이터 지원](https://azure.microsoft.com/updates/standard-load-balancer-and-ip-addresses-metadata-now-available-through-azure-instance-metadata-service-imds/)|이제 IMDS(Azure Instance Metadata Service)를 통해 표준 공용 IP 주소 및 표준 Load Balancer 메타데이터를 검색할 수 있습니다. 메타 데이터는 실행 중인 Vm (가상 머신) 인스턴스 및 virtual machine scale sets 인스턴스 내에서 사용할 수 있습니다. 메타데이터를 활용하여 가상 머신을 관리할 수 있습니다. [여기](instance-metadata-service-load-balancer.md)에서 자세히 알아보기| 2021년 2월 |
| 기능 | [IP 주소 손실 없이 기본에서 표준으로 공용 IP SKU 업그레이드](https://azure.microsoft.com/updates/public-ip-sku-upgrade-generally-available/) | 기본에서 표준 Load Balancer로 이동할 때 공용 IP 주소를 유지합니다. [여기](../virtual-network/ip-services/public-ip-upgrade-portal.md)에서 자세히 알아보세요.| 2021년 1월|
| 기능 | 리소스 그룹 간 이동 지원 | [리소스 그룹 이동](https://azure.microsoft.com/updates/standard-resource-group-move/)에 대한 표준 Load Balancer 및 표준 공용 IP 지원. | 2020년 10월 |
| 기능 | [표준 LB의 글로벌 계층과 지역 간 부하 분산](https://azure.microsoft.com/updates/preview-azure-load-balancer-now-supports-crossregion-load-balancing/) | Azure Load Balancer는 지역 간 부하 분산을 지원 합니다. 이전에는 표준 Load Balancer 지역 범위가 있었습니다. 이 릴리스에서는 단일 정적, 글로벌 애니캐스트 공용 IP 주소를 통해 여러 Azure 지역 간에 부하를 분산할 수 있습니다. | 2020년 9월 |
| 기능| Azure Monitor를 사용하는 Azure Load Balancer Insights | 네트워크용 Azure Monitor의 일부로 빌드된 고객은 이제 Azure Portal에서 메트릭으로 미리 구성된 표준 Load Balancers용 모든 Load Balancer 구성과 상태 대시보드에 대한 토폴로지 맵을 보유하고 있습니다. [시작 및 자세한 정보](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020년 6월 |
| 유효성 검사 | HA 포트에 대한 유효성 검사 추가 | 부동 IP가 활성화된 경우 HA 포트 규칙과 비 HA 포트 규칙만 구성할 수 있도록 유효성 검사가 추가되었습니다. 이전에는 이 구성이 진행되었지만 의도한 대로 작동하지 않았습니다. 기능이 변경되지 않았습니다. [여기](load-balancer-ha-ports-overview.md#limitations)에서 자세히 알아볼 수 있습니다.| 2020년 6월 |
| 기능| Azure Load Balancer에 대한 IPv6 지원(일반적으로 사용 가능) | Azure Load Balancers에 대한 프런트 엔드로 IPv6 주소를 사용할 수 있습니다. [여기에서 이중 스택 애플리케이션](./virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)을 만드는 방법에 대해 알아봅니다. |2020년 4월|
| 기능| 유휴 시간 제한 시 TCP 다시 설정(일반적으로 사용 가능)| TCP 다시 설정을 사용하여 보다 예측 가능한 애플리케이션 동작을 만듭니다. [자세히 알아보기](load-balancer-tcp-reset.md)| 2020년 2월 |

## <a name="known-issues"></a>알려진 문제

현재 제품 그룹에서 다음과 같은 알려진 문제를 해결하기 위해 적극적으로 작업 중입니다.

|문제 |Description  |완화 방법  |
| ---------- |---------|---------|
| IP 기반 LB 아웃바운드 IP | IP 기반 LB는 아웃 바운드 규칙이 구성 되지 않은 경우 아웃 바운드에 대 한 Azure의 기본 아웃 바운드 액세스 IP를 활용 합니다. | 이 IP에서 아웃바운드 액세스를 방지하려면 예측 가능한 IP 주소에 아웃바운드 규칙 또는 NAT Gateway를 활용하고 SNAT 포트가 소진되지 않도록 합니다. |

  

## <a name="next-steps"></a>다음 단계

Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](load-balancer-overview.md) 및 [질문과 대답](load-balancer-faqs.yml)을 참조하세요.
