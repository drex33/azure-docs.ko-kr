---
title: Azure 디스크 풀(미리 보기) 성능 최적화
description: Azure 디스크 풀에서 성능을 최대한 활용하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 74d50826811198811e6cea671641cae378d1235c
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122539078"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Azure 디스크 풀(미리 보기) 계획 가이드

Azure 디스크 풀(미리 보기)을 배포하기 전에 워크로드의 성능 요구 사항을 이해하는 것이 중요합니다. 요구 사항을 미리 결정하면 디스크 풀에서 성능을 최대한 활용할 수 있습니다. 디스크 풀의 성능은 세 가지 주요 요인에 따라 결정되며, 해당 요인을 조정하면 디스크 풀의 성능이 조정됩니다.

- 디스크 풀의 스케일링 성능 목표
- 디스크 풀에 포함된 개별 디스크의 스케일링 성능 목표
- 디스크 풀에 대한 클라이언트 머신 간 네트워킹 연결

## <a name="optimize-for-low-latency"></a>짧은 대기 시간을 위해 최적화

짧은 대기 시간을 위해 우선 순위를 지정하는 경우 디스크 풀에 울트라 디스크를 추가합니다. 울트라 디스크는 밀리초 미만 디스크 대기 시간을 제공합니다. 가능한 가장 짧은 대기 시간을 얻으려면 네트워크 구성을 평가하고 가장 최적의 경로를 사용하고 있는지도 확인해야 합니다. [ExpressRoute FastPath](../expressroute/about-fastpath.md)를 사용하여 네트워크 대기 시간을 최소화하는 것이 좋습니다.

## <a name="optimize-for-high-throughput"></a>높은 처리량을 위해 최적화

처리량 우선 순위를 지정하는 경우 먼저 처리량 목표를 제공하는 데 필요한 디스크 풀 수를 평가합니다. 필요한 목표가 있으면 각 개별 디스크와 해당 유형 간에 분할할 수 있습니다. 현재 디스크 풀에서는 프리미엄 SSD와 울트라 디스크의 두 가지 디스크 유형을 사용할 수 있습니다. 프리미엄 SSD는 스토리지 용량으로 스케일링되는 높은 IOPS 및 MBps를 제공할 수 있는 반면, 울트라 디스크는 스토리지 용량과 관계없이 성능을 스케일링할 수 있습니다. 비용과 성능 간 균형을 위해 가장 적합한 유형을 선택합니다. 또한 클라이언트에서 디스크 풀로 네트워크 연결이 병목 상태가 아닌지, 특히 처리량을 확인합니다.


## <a name="use-cases"></a>사용 사례

다음 표에는 Azure VMware Solution과 권장 구성이 있는 디스크 풀의 몇 가지 일반적인 사용 사례가 나와 있습니다.


|Azure VMware Solution 사용 사례  |제안된 디스크 유형  |제안된 네트워크 구성  |
|---------|---------|---------|
|Azure VMware Solution vSAN의 확장과 같은 활성 작업 세트를 위한 블록 스토리지.     |Ultra disks         |ExpressRoute 가상 네트워크 게이트웨이: Ultra Performance 또는 ErGw3AZ(10Gbps)를 사용하여 디스크 풀 가상 네트워크를 Azure VMware Solution 클라우드에 연결하고 FastPath를 사용하도록 설정하여 네트워크 대기 시간을 최소화합니다.         |
|계층화 - Azure VMware Solution vSAN에서 디스크 풀로 자주 액세스하지 않은 데이터를 계층화합니다.     |프리미엄 SSD         |ExpressRoute 가상 네트워크 게이트웨이: 표준(1Gbps) 또는 고성능(2Gbps)을 사용하여 디스크 풀 가상 네트워크를 Azure VMware Solution 클라우드에 연결합니다.         |
|Azure VMware Solution의 재해 복구 사이트를 위한 데이터 스토리지: 온-프레미스 또는 기본 VMware 환경에서 보조 사이트로서 디스크 풀에 데이터를 복제합니다.     |프리미엄 SSD         |ExpressRoute 가상 네트워크 게이트웨이: 표준(1Gbps) 또는 고성능(2Gbps)을 사용하여 디스크 풀 가상 네트워크를 Azure VMware Solution 클라우드에 연결합니다.         |

다른 Azure VMware Solution 고려 사항과 함께 네트워킹 설정을 계획하려면 [Azure VMware Solution에 대한 네트워킹 계획 검사 목록](../azure-vmware/tutorial-network-checklist.md)을 참조하세요.

## <a name="disk-pool-scalability-and-performance-targets"></a>디스크 풀 스케일링 수준 및 성능 목표

|리소스  |제한  |
|---------|---------|
|디스크 풀당 최대 디스크 수|8|
|디스크 풀당 최대 IOPS|25,600|
|디스크 풀당 최대 MBps|384|

다음 예제에서는 다양한 성능 요인이 함께 작동하는 방식의 아이디어를 제공합니다.

예를 들어, 1-TiB 프리미엄 SSD 2개(P30, 프로비저닝된 목표가 5000IOPS 및 200Mbps임)를 디스크 풀에 추가하는 경우 2 x 5000 = 10,000IOPS를 달성할 수 있습니다. 그러나 처리량은 디스크 풀별로 384MBps로 제한됩니다. 이 384MBps 한도를 초과하려면 추가 처리량에 맞게 스케일 아웃하기 위해 더 많은 디스크 풀을 배포할 수 있습니다. 네트워크 처리량은 스케일 아웃의 효율성을 제한합니다.

## <a name="availability"></a>가용성

디스크 풀은 현재 미리 보기 상태이며 프로덕션 워크로드에 사용할 수 없습니다.

어떤 이유로 디스크 풀이 Azure VMware Solution 클라우드에 액세스할 수 없는 경우 다음이 발생합니다.

- 디스크 풀에 연결된 모든 데이터 저장소에 더 이상 액세스할 수 없습니다.
- 영향을 받는 데이터 저장소를 사용 중이고 이 Azure VMware Solution 클라우드에서 호스트되는 모든 VMware VM은 비정상 상태가 됩니다.
- 이 Azure VMware Solution 클라우드의 클러스터 상태는 다음 하나의 작업을 제외하고 영향을 받지 않습니다. 호스트를 유지 관리 모드로 전환할 수 없습니다. Azure VMware Solution은 이 오류를 처리하고 영향을 받는 데이터 저장소의 연결을 끊어서 복구를 시도합니다.

## <a name="next-steps"></a>다음 단계

[디스크 풀을 배포합니다](disks-pools-deploy.md).
