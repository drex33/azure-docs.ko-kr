---
title: 지역 간 부하 분산 장치(미리 보기)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer용 지역 간 부하 분산 장치 계층에 대한 개요.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: d8b8cebfe18b52de7b904989907d587a4519c8e3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254630"
---
# <a name="cross-region-load-balancer-preview"></a>지역 간 부하 분산 장치(미리 보기)

Azure 표준 Load Balancer 지역 간 부하 분산을 지원하여 다음과 같은 지역 중복 고가용성 시나리오를 지원합니다.

* 여러 지역에서 발생하여 들어오는 트래픽.
* 다음으로 최적인 지역 배포로의 [즉각적인 전역 장애 조치(failover)](#regional-redundancy).
* [매우 낮은 대기 시간](#ultra-low-latency)으로 가장 가까운 Azure 지역에 지역 간 로드 분산.
* 단일 엔드포인트 뒤에서의 [스케일 업/스케일 다운](#ability-to-scale-updown-behind-a-single-endpoint) 기능.
* 정적 anycast 전역 IP 주소
* [클라이언트 IP 유지](#client-ip-preservation)
* 학습 곡선 없이 [기존 부하 분산 장치 솔루션에 빌드](#build-cross-region-solution-on-existing-azure-load-balancer)

> [!IMPORTANT]
> 지역 간 부하 분산 장치는 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

지역 간 부하 분산 장치의 프런트 엔드 IP 구성은 정적이며 [대부분의 Azure 지역](#participating-regions)에서 보급됩니다.

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="지역 간 부하 분산 장치의 다이어그램." border="true":::

> [!NOTE]
> 지역 간 부하 분산 장치에 대한 부하 분산 규칙의 백 엔드 포트는 지역 표준 부하 분산 장치에 대한 부하 분산 규칙/인바운드 Nat 규칙의 프런트 엔드 포트와 일치해야 합니다. 

### <a name="regional-redundancy"></a>지역 중복

전역 프런트 엔드 공용 IP 주소를 기존 부하 분산 장치에 추가하여 지역 중복을 구성합니다. 

한 지역이 실패하는 경우 트래픽이 다음의 가장 가까운 정상적인 지역 부하 분산 장치로 라우팅됩니다.  

지역 간 부하 분산 장치의 상태 프로브는 20초마다 가용성에 대한 정보를 수집합니다. 한 지역 부하 분산 장치에서 가용성이 0으로 떨어지면 지역 간 부하 분산 장치에서 실패를 검색합니다. 그러면 해당 지역 부하 분산 장치는 순환에서 제외됩니다. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="전역 지역 트래픽 보기의 다이어그램." border="true":::

### <a name="ultra-low-latency"></a>매우 낮은 대기 시간

지리적 근접 부하 분산 알고리즘은 사용자 및 지역 배포의 지리적 위치를 기반으로 합니다. 

클라이언트에서 시작된 트래픽은 가장 가까운 참여 지역에 도달한 다음 Microsoft 전역 네트워크 백본을 통해 이동하여 가장 가까운 지역 배포에 도착합니다. 

예를 들어 다음 Azure 지역에 표준 부하 분산 장치가 포함된 지역 간 부하 분산 장치가 있습니다.

* 미국 서부
* 북유럽

시애틀에서 흐름이 시작되면 트래픽이 미국 서부에 들어갑니다. 이 지역은 시애틀에서 가장 가까운 참여 지역입니다. 트래픽은 가장 가까운 지역인 미국 서부의 부하 분산 장치로 라우팅됩니다.

Azure 지역 간 부하 분산 장치는 라우팅 의사 결정을 위해 지역 근접 부하 분산 알고리즘을 사용합니다. 

지역 부하 분산 장치에 구성된 부하 배포 모드는 지역 근접을 위해 여러 지역 부하 분산 장치가 사용되는 경우 최종 라우팅 결정을 내리는 데 사용됩니다.

자세한 내용은 [Azure Load Balancer의 배포 모드 구성](./load-balancer-distribution-mode.md)을 참조하세요.


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>단일 엔드포인트 뒤에서 확장/축소할 수 있음

지역 간 부하 분산 장치의 전역 엔드포인트를 고객에게 노출하는 경우 전역 엔드포인트 뒤에서 지역 배포를 중단 없이 추가하거나 제거할 수 있습니다. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-anycast-global-ip-address"></a>정적 anycast 전역 IP 주소
지역 간 부하 분산 장치에는 IP 주소가 동일하게 유지되는 고정 공용 IP가 제공됩니다. 고정 IP에 대해 자세히 알아보려면 [여기](../virtual-network/ip-services/public-ip-addresses.md#ip-address-assignment)를 참조하세요.

### <a name="client-ip-preservation"></a>클라이언트 IP 유지
지역 간 부하 분산 장치는 레이어-4 통과 네트워크 부하 분산 장치입니다. 이 통과는 패킷의 원래 IP를 유지합니다.  원래 IP는 가상 머신에서 실행되는 코드에 사용할 수 있습니다. 이러한 유지는 IP 주소와 관련된 논리를 적용할 수 있게 해 줍니다.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>기존 Azure Load Balancer에 지역 간 솔루션 빌드
지역 간 부하 분산 장치의 백 엔드 풀에는 지역 부하 분산 장치가 하나 이상 포함되어 있습니다. 

가용성이 높은 지역 간 배포를 위해 기존 부하 분산 배포를 지역 간 부하 분산 장치에 추가하세요.

**홈 지역** 은 전역 계층의 지역 간 부하 분산 장치 또는 공용 IP 주소가 배포되는 위치입니다. 이 지역은 트래픽이 라우팅되는 방식에 영향을 주지 않습니다. 홈 지역이 다운되는 경우 트래픽 흐름은 영향을 받지 않습니다.

### <a name="home-regions"></a>홈 지역
* 미국 동부 2
* 미국 서부
* 서유럽
* 동남 아시아
* 미국 중부
* 북유럽
* 동아시아

> [!NOTE]
> 위 7개 지역 중 하나의 전역 계층에서만 지역 간 부하 분산 장치 또는 공용 IP만 배포할 수 있습니다.

**참여하는 지역은** 부하 분산 프로그램의 글로벌 공용 IP가 보급되는 위치입니다.

사용자가 시작한 트래픽은 Microsoft 코어 네트워크를 통해 가장 가까운 참여 지역으로 이동합니다. 

지역 간 부하 분산 장치는 해당 지역 부하 분산 장치로 트래픽을 라우팅합니다.

### <a name="participating-regions"></a>참여 지역
* 미국 동부 
* 서유럽 
* 미국 중부 
* 미국 동부 2 
* 미국 서부 
* 북유럽 
* 미국 중남부 
* 미국 서부 2 
* 영국 남부 
* 동남 아시아 
* 미국 중북부 
* 일본 동부 
* 동아시아 
* 미국 중서부 
* 오스트레일리아 남동부 
* 오스트레일리아 동부 
* 인도 중부 

## <a name="limitations"></a>제한 사항

* 지역 간 프런트 엔드 IP 구성은 퍼블릭으로만 사용됩니다. 내부 프런트 엔드는 현재 지원되지 않습니다.

* 프라이빗 또는 내부 부하 분산 장치는 지역 간 부하 분산 장치의 백 엔드 풀에 추가할 수 없습니다. 

* 지역 간 IPv6 프런트 엔드 IP 구성은 지원되지 않습니다. 

* 지역 간 Load Balancer에서는 UDP 트래픽이 지원되지 않습니다. 

* 상태 프로브는 현재 구성할 수 없습니다. 기본 상태 프로브는 지역 부하 분산 장치에 대한 가용성 정보를 20초마다 자동으로 수집합니다. 

* AKS(Azure Kubernetes Service)와의 통합은 현재 사용할 수 없습니다. 백 엔드에 AKS 클러스터가 배포된 상태에서 지역 간 부하 분산 장치를 표준 부하 분산 장치와 함께 배포하면 연결이 끊어집니다.

## <a name="pricing-and-sla"></a>가격 및 SLA
지역 간 부하 분산 장치는 표준 부하 분산 장치의 [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ )를 공유합니다.

 
## <a name="next-steps"></a>다음 단계

- 지역 간 부하 분산 장치를 만드는 방법은 [자습서: Azure Portal을 사용하여 지역 간 부하 분산 장치 만들기](tutorial-cross-region-portal.md)를 참조하세요.
- [지역 간 부하 분산 장치](https://www.youtube.com/watch?v=3awUwUIv950)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).