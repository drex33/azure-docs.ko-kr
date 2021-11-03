---
title: Azure 디스크 풀(미리 보기) 성능 최적화
description: Azure 디스크 풀에서 성능을 최대한 활용하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: db70740b484290b56d140d6b71d570d61afd138d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082724"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Azure 디스크 풀(미리 보기) 계획 가이드

Azure 디스크 풀(미리 보기)을 배포하기 전에 워크로드의 성능 요구 사항을 이해하는 것이 중요합니다. 요구 사항을 미리 결정하면 디스크 풀에서 성능을 최대한 활용할 수 있습니다. 디스크 풀의 성능은 세 가지 주요 요인에 따라 결정되며, 해당 요인을 조정하면 디스크 풀의 성능이 조정됩니다.

- 디스크 풀의 스케일링 성능 목표
- 디스크 풀에 포함된 개별 디스크의 스케일링 성능 목표
- 디스크 풀에 대한 클라이언트 머신 간 네트워킹 연결

## <a name="optimize-for-low-latency"></a>짧은 대기 시간을 위해 최적화

짧은 대기 시간을 위해 우선 순위를 지정하는 경우 디스크 풀에 울트라 디스크를 추가합니다. 울트라 디스크는 밀리초 미만 디스크 대기 시간을 제공합니다. 가능한 가장 짧은 대기 시간을 얻으려면 네트워크 구성을 평가하고 가장 최적의 경로를 사용하고 있는지도 확인해야 합니다. [ExpressRoute FastPath](../expressroute/about-fastpath.md)를 사용하여 네트워크 대기 시간을 최소화하는 것이 좋습니다.

## <a name="optimize-for-high-throughput"></a>높은 처리량을 위해 최적화

처리량의 우선 순위를 지정 하는 경우 먼저 다른 디스크 풀 Sku의 성능 목표를 평가 하 고 처리량 목표를 전달 하는 데 필요한 디스크 풀 수를 평가 합니다. 성능 요구 사항이 프리미엄 디스크 풀에서 제공할 수 있는 것 보다 많은 경우 배포를 여러 디스크 풀로 분할할 수 있습니다. 그런 다음 각 개별 디스크와 해당 유형 간에 디스크 풀에서 제공 되는 성능을 가장 잘 활용 하는 방법을 결정할 수 있습니다. 디스크 풀의 경우 프리미엄과 표준 Ssd를 혼합 하 고 일치 시킬 수 있으며, 또는 ultra disks를 사용할 수 있습니다. 프리미엄 또는 표준 Ssd에서는 Ultra disks를 사용할 수 없습니다. 사용자의 요구에 가장 적합 한 디스크 유형을 선택 합니다. 또한 클라이언트에서 디스크 풀로 네트워크 연결이 병목 상태가 아닌지, 특히 처리량을 확인합니다.


## <a name="use-cases"></a>사용 사례

다음 표에는 Azure VMware Solution과 권장 구성이 있는 디스크 풀의 몇 가지 일반적인 사용 사례가 나와 있습니다.


|Azure VMware Solution 사용 사례  |제안된 디스크 유형  |제안 된 디스크 풀 SKU  |제안된 네트워크 구성  |
|---------|---------|---------|---------|
|Azure VMware Solution vSAN의 확장과 같은 활성 작업 세트를 위한 블록 스토리지.     |Ultra disks         |Premium         |ExpressRoute 가상 네트워크 게이트웨이: Ultra Performance 또는 ErGw3AZ(10Gbps)를 사용하여 디스크 풀 가상 네트워크를 Azure VMware Solution 클라우드에 연결하고 FastPath를 사용하도록 설정하여 네트워크 대기 시간을 최소화합니다.         |
|계층화 - Azure VMware Solution vSAN에서 디스크 풀로 자주 액세스하지 않은 데이터를 계층화합니다.     |Premium SSD, 표준 SSD         |표준         |ExpressRoute 가상 네트워크 게이트웨이: 표준(1Gbps) 또는 고성능(2Gbps)을 사용하여 디스크 풀 가상 네트워크를 Azure VMware Solution 클라우드에 연결합니다.         |
|Azure VMware Solution의 재해 복구 사이트를 위한 데이터 스토리지: 온-프레미스 또는 기본 VMware 환경에서 보조 사이트로서 디스크 풀에 데이터를 복제합니다.     |Premium SSD, 표준 SSD         |표준, 기본         |ExpressRoute 가상 네트워크 게이트웨이: 표준(1Gbps) 또는 고성능(2Gbps)을 사용하여 디스크 풀 가상 네트워크를 Azure VMware Solution 클라우드에 연결합니다.         |


다른 Azure VMware Solution 고려 사항과 함께 네트워킹 설정을 계획하려면 [Azure VMware Solution에 대한 네트워킹 계획 검사 목록](../azure-vmware/tutorial-network-checklist.md)을 참조하세요.

## <a name="disk-pool-scalability-and-performance-targets"></a>디스크 풀 스케일링 수준 및 성능 목표

|리소스  |기본 디스크 풀  |표준 디스크 풀  |Premium 디스크 풀  |
|---------|---------|---------|---------|
|디스크 풀당 최대 디스크 수     |16         |32         |32         |
|디스크 풀당 최대 IOPS     |12,800         |25,600         |51,200         |
|디스크 풀당 최대 MBps     |192         |384         |768         |

다음 예제에서는 다양한 성능 요인이 함께 작동하는 방식의 아이디어를 제공합니다.

예를 들어 2 1-TiB premium Ssd (P30, 프로 비전 된 대상 5000 IOPS 및 200 Mbps)를 표준 디스크 풀에 추가 하는 경우 2 x 5000 = 1만 IOPS를 달성할 수 있습니다. 그러나 처리량은 디스크 풀별로 384MBps로 제한됩니다. 이 384MBps 한도를 초과하려면 추가 처리량에 맞게 스케일 아웃하기 위해 더 많은 디스크 풀을 배포할 수 있습니다. 네트워크 처리량은 스케일 아웃의 효율성을 제한합니다.

## <a name="availability"></a>가용성

디스크 풀은 현재 미리 보기 상태이며 프로덕션 워크로드에 사용할 수 없습니다. 기본적으로 디스크 풀은 premium 및 standard Ssd만 지원 합니다. 대신 디스크 풀의 ultra disks에 대 한 지원을 사용 하도록 설정할 수 있지만, ultra 디스크가 포함 된 디스크 풀은 프리미엄 또는 표준 Ssd와 호환 되지 않습니다.

프리미엄 및 표준 Ssd를 지 원하는 디스크 풀은 항상 사용 가능한 아키텍처를 기반으로 하며,이는 iSCSI 끝점을 호스트 하는 다중 항목입니다. 단일 인스턴스 배포에서 ultra disks를 지 원하는 디스크 풀을 호스팅합니다.

어떤 이유로 디스크 풀이 Azure VMware Solution 클라우드에 액세스할 수 없는 경우 다음이 발생합니다.

- 디스크 풀에 연결된 모든 데이터 저장소에 더 이상 액세스할 수 없습니다.
- 영향을 받는 데이터 저장소를 사용 하는 Azure VMware 솔루션 클라우드에서 호스트 되는 모든 VMware Vm은 비정상 상태에 있습니다.
- 단일 작업을 제외 하 고 Azure VMware Solution cloud의 클러스터 상태는 영향을 받지 않습니다. 호스트를 유지 관리 모드로 설정할 수 없습니다. Azure VMware Solution은 이 오류를 처리하고 영향을 받는 데이터 저장소의 연결을 끊어서 복구를 시도합니다.

## <a name="next-steps"></a>다음 단계

- [디스크 풀을 배포합니다](disks-pools-deploy.md).
- Azure VMware 솔루션이 디스크 풀을 통합 하는 방법에 대해 알아보려면 [Azure Vmware 솔루션 호스트에 디스크 풀 연결 (미리 보기)](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md)을 참조 하세요.
