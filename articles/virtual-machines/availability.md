---
title: Azure Virtual Machines에 대한 가용성 옵션
description: Azure에서 가상 머신을 실행하기 위한 가용성 옵션을 알아봅니다.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 77e2ef4a5af250f19e505dfb9f725aa7674f691f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694479"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Azure Virtual Machines에 대한 가용성 옵션

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서에서는 Azure VM(가상 머신)의 가용성 옵션에 대한 개요를 제공합니다.

## <a name="availability-zones"></a>가용성 영역

[가용성 영역](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context)은 VM에 있는 애플리케이션 및 데이터의 가용성을 유지해야 하는 컨트롤 수준을 확장합니다. 가용성 영역은 Azure 지역 내에서 물리적으로 별도의 영역입니다. 지원되는 Azure 지역당 3개의 가용성 영역이 있습니다. 

각 가용성 영역에는 고유한 소스, 네트워크 및 냉각 장치가 있습니다. 영역에 복제된 VM을 사용하는 솔루션을 설계하여 데이터 센터 손실로부터 앱과 데이터를 보호할 수 있습니다. 하나의 영역이 손상되면 다른 영역에서 복제된 앱 및 데이터를 즉시 사용할 수 있습니다. 

## <a name="availability-sets"></a>가용성 집합
[가용성 집합](availability-set-overview.md)은 중복성과 가용성을 제공하기 위해 Azure에서 애플리케이션이 빌드되는 방식을 이해할 수 있도록 하는 VM의 논리적 그룹입니다. 고가용성 애플리케이션을 제공하고 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족할 수 있도록 가용성 집합 내에 둘 이상의 VM을 만드는 것이 좋습니다. 가용성 집합 자체에 대한 비용은 없으므로 만드는 각 VM 인스턴스에 대해서만 요금을 지불합니다.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines 크기 집합 

[Azure 가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context)을 사용하면 부하 분산된 VM의 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 확장 집합은 애플리케이션에 고가용성을 제공하고 많은 수의 VM을 중앙에서 관리, 구성 및 업데이트할 수 있게 합니다. 고가용성 애플리케이션을 제공하고 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족할 수 있도록 확장 집합 내에 둘 이상의 VM을 만드는 것이 좋습니다. 확장 집합 자체에 대한 비용은 없으므로 만드는 각 VM 인스턴스에 대해서만 요금을 지불합니다.

확장 집합의 가상 머신은 단일 가용성 영역에 배포하거나 지역에 배포할 수도 있습니다. 가용성 영역 배포 옵션은 [오케스트레이션 모드](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context)에 따라 다를 수 있습니다.

## <a name="load-balancer"></a>부하 분산 장치
[Azure Load Balancer](../load-balancer/load-balancer-overview.md)를 가용성 영역 또는 가용성 집합과 결합하여 애플리케이션 복원력을 극대화합니다. Azure 부하 분산 장치는 트래픽을 여러 가상 머신에 분산시킵니다. 표준 계층 가상 머신의 경우 Azure 부하 분산 장치가 포함되어 있습니다. 모든 가상 머신 계층에 Azure Load Balancer가 포함되어 있는 것은 아닙니다. 가상 머신 부하 분산에 대한 자세한 내용은 [Linux](linux/tutorial-load-balancer.md) 또는 [Windows](windows/tutorial-load-balancer.md)의 **가상 머신 부하 분산** 을 참조하세요.


## <a name="azure-storage-redundancy"></a>Azure Storage 중복성
Azure Storage는 항상 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 항상 여러 복사본을 저장합니다. 중복성은 스토리지 계정이 오류 발생 시에도 가용성 및 내구성 목표를 충족하는지 확인합니다.

시나리오에 가장 적합한 중복성 옵션을 결정할 때에는 저렴한 비용과 높은 가용성 간의 균형을 고려하세요. 선택해야 하는 중복성 옵션을 결정하는 데 도움이 되는 요소는 다음과 같습니다.
- 기본 지역에서 데이터를 복제하는 방법
- 지역 재해로부터 보호하기 위해 데이터가 주 지역에서 지리적으로 멀리 떨어진 보조 지역으로 복제되는지 여부
- 어떤 이유로든 기본 지역을 사용할 수 없는 경우 보조 지역의 복제된 데이터에 대한 읽기 액세스 권한이 응용 프로그램에 필요한지 여부

자세한 내용은 [Azure Storage 중복 옵션](../storage/common/storage-redundancy.md)을 참조하세요.

## <a name="azure-site-recovery"></a>Azure Site Recovery
조직에서는 계획에 있거나 예기치 못한 중단이 발생했을 때 데이터를 안전하게 유지하고 앱과 워크로드를 온라인으로 유지하는 BCDR(비즈니스 연속성 및 재해 복구) 전략을 도입해야 합니다.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md)는 중단 기간 동안에도 비즈니스 앱과 워크로드를 유지하여 비즈니스 연속성을 보장합니다. Site Recovery는 물리적 컴퓨터와 VM(가상 머신)에서 실행되는 워크로드를 기본 사이트에서 보조 위치로 복제합니다. 기본 사이트에서 중단이 발생하면 보조 위치로 장애 조치(failover)하고 여기에서 앱에 액세스합니다. 기본 위치가 다시 실행되면 장애 복구(failback)할 수 있습니다.

Site Recovery는 다음을 위해 복제를 관리할 수 있습니다.
- Azure 지역 간에 Azure VM 복제
- 온-프레미스 VM, Azure Stack VM 및 실제 서버.

## <a name="next-steps"></a>다음 단계
- [가용성 영역에서 가상 머신 만들기](./linux/create-cli-availability-zone.md)
- [가용성 집합에서 가상 머신 만들기](./linux/tutorial-availability-sets.md)
- [가상 머신 확장 집합 만들기](../virtual-machine-scale-sets/quick-create-portal.md)