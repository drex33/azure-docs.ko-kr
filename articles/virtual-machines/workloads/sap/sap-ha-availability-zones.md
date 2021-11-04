---
title: Azure 가용성 영역을 사용하는 SAP 워크로드 구성 | Microsoft Docs
description: Azure 가용성 영역을 사용하는 SAP NetWeaver의 고가용성 아키텍처 및 시나리오
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/02/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e84bd6b69c19a40fe660f17d8b41c38e66bd28d8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448997"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Azure 가용성 영역을 사용하는 SAP 워크로드 구성
Azure 가용성 집합에 다양한 SAP 아키텍처 계층을 배포하는 것 이외에, 좀 더 최근에 도입된 [Azure 가용성 영역](../../../availability-zones/az-overview.md)도 SAP 워크로드 배포에 사용할 수 있습니다. Azure 가용성 영역은 다음과 같이 정의됩니다. “지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다.” Azure 가용성 영역을 모든 지역에서 사용할 수 있는 것은 아닙니다. Azure에서 가용성 영역을 제공하는 지역에 대해서는 [Azure 지역 맵](https://azure.microsoft.com/global-infrastructure/geographies/)에서 확인하세요. 해당 맵은 가용성 영역을 제공하거나 제공하도록 발표한 지역에 대해 보여 줍니다. 

일반적인 SAP NetWeaver 또는 S/4HANA 아키텍처의 경우라면 다음 세 가지 계층을 보호해야 합니다.

- VM 한 개에서 수십 개일 수 있는 SAP 애플리케이션 계층 VM이 동일한 호스트 서버에 배포될 가능성을 최소화하려고 합니다. 또한 DBMS 계층에 적절히 근접한 해당 VM이 적절한 창에서 네트워크 대기 시간을 유지하기를 바랍니다.
- SAP NetWeaver 및 S/4HANA 아키텍처의 단일 실패 지점을 나타내는 SAP ASCS/SCS 계층 일반적으로는 장애 조치 프레임워크를 통해 처리할 VM 두 개를 확인합니다. 따라서 이러한 VM은 서로 다른 인프라 장애 및 업데이트 도메인에 할당되어야 합니다.
- 마찬가지로 단일 실패 지점을 나타내는 SAP DBMS 계층 일반적인 경우라면 장애 조치 프레임워크에 의해 적용되는 VM 두 개로 구성됩니다. 따라서 이러한 VM은 서로 다른 인프라 장애 및 업데이트 도메인에 할당되어야 합니다. VM을 세 개 이상 사용할 수 있는 경우의 SAP HANA 스케일 아웃 배포는 예외입니다.

가용성 집합을 통해 중요한 VM을 배포할 때와 가용성 영역을 통해 이를 배포할 때의 주요 차이점은 다음과 같습니다.

- 가용성 집합을 이용한 배포의 경우, 특정 지역에서 적용되는 것이라면 무엇이든 단일 구역이나 데이터 센터의 집합 내에서 VM을 정렬합니다. 그로 인해 가용성 집합을 이용한 배포는 데이터 센터를 전체 영역으로 삼아 영향을 주는 전력, 냉각 또는 네트워킹 문제에서 보호를 받지 않습니다. 긍정적인 면은 VM가 해당 영역이나 데이터 센터 내의 업데이트 및 장애 도메인에 맞춰진다는 점입니다. 특히 가용성 집합 하나당 두 개의 VM을 보호하는 SAP ASCS나 DBMS 계층의 경우, 장애 및 업데이트 도메인에 맞춰짐으로써 두 VM 모두 같은 호스트 하드웨어에 위치하게 되는 것을 방지합니다. 
- Azure 가용성 영역을 통해 VM을 배포하고 서로 다른 영역(지금까지 최대 3개 영역까지 가능)을 선택하면 각각의 물리적 위치에 VM을 배치하게 되고, 이를 통해 데이터 센터를 전체 영역으로 삼아 영향을 주는 전력, 냉각 또는 네트워킹 문제에서 추가적으로 보호할 수 있습니다. 하지만 동일한 가용성 영역에 같은 VM 제품군의 VM을 둘 이상 배포하는 경우, 동일 호스트에 있게 되는 VM으로부터 보호되지 않습니다. 따라서 일반적으로 VM 두 개를 각각 놓고 볼 때 SAP ASCS 및 DBMS 계층을 위해 가용성 영역을 통해 배포하는 것이 좋습니다. VM 세 개 이상이 될 수도 있는 SAP 애플리케이션 계층의 경우에는 서로 다른 배포 모델로 대체해야 할 수도 있습니다(뒷부분 참조).

중요한 단일 VM의 실패를 처리하거나 중요한 소프트웨어 패치의 가동 중지 시간을 줄이는 기능 외에도 하나 이상의 Azure 데이터 센터의 가용성에 영향을 줄 수 있는 대형 인프라 문제를 방지하고자 하는 것이 Azure 가용성 영역에 걸친 배포 동기여야 합니다. 

## <a name="considerations-for-deploying-across-availability-zones"></a>가용성 영역에 걸쳐 배포할 때의 고려 사항


가용성 영역을 사용하는 경우 다음을 고려합니다.

- Azure 지역 내의 다양한 가용성 영역 간 거리에 대한 보장은 없습니다.
- 가용성 영역은 이상적인 DR 솔루션이 아닙니다. 자연 재해는 전력 인프라의 과도한 손상을 비롯하여 세계 지역에 광범위한 손상을 가져올 수 있습니다. 다양한 영역 간 거리가 적절한 DR 솔루션을 구성할만큼 충분히 크지 않을 수 있습니다.
- 가용성 영역 간의 네트워크 대기 시간이 모든 Azure 지역에서 동일하지는 않습니다. 일부 경우에 하나의 영역에서 활성 DBMS VM으로의 네트워크 대기 시간이 허용 가능하기 때문에 다른 영역에서 SAP 애플리케이션 계층을 배포하고 실행할 수 있습니다. 그렇지만, 일부 Azure 지역에서는 다른 영역에 배포된 활성 DBMS VM과 SAP 애플리케이션 인스턴스 간의 대기 시간이 SAP 비즈니스 프로세스에 용인되는 수준이 아닐 수 있습니다. 이러한 경우 배포 아키텍처는 영역 간 네트워크 대기 시간이 너무 높은 경우 애플리케이션에 대한 활성/활성 아키텍처 또는 능동/수동 아키텍처와 달라야 합니다.
- 가용성 영역을 사용할 위치를 결정할 때는 영역 간 네트워크 대기 시간을 고려합니다. 네트워크 대기 시간은 다음과 같이 두 가지 영역에서 중요한 역할을 합니다.
    - 동기 복제를 유지해야 하는 두 DBMS 인스턴스 간의 대기 시간. 네트워크 대기 시간이 길수록 워크로드의 확장성에 영향을 미칠 가능성이 높아집니다.
    - 활성 DBMS 인스턴스가 있는 영역에서 SAP 대화 상자 인스턴스를 실행하는 VM과 다른 영역에 있는 유사한 VM 간의 네트워크 대기 시간 차이. 이 차이가 클수록, DBMS가 있는 영역에서 실행되는지 또는 다른 영역에서 실행되는지에 따라 비즈니스 프로세스 및 일괄 처리 작업의 실행 시간에 미치는 영향이 증가합니다.

가용성 영역 간에 Azure VM을 배포하고 동일한 Azure 지역 내에서 장애 조치(failover) 솔루션을 설정할 경우 다음과 같은 몇 가지 제한 사항이 있습니다.

- Azure 가용성 영역을 배포하는 경우 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용해야 합니다. 
- 물리적 영역에 대한 영역 열거형 매핑은 Azure 구독을 기준으로 고정됩니다. 다른 구독을 사용하여 SAP 시스템을 배포하는 경우 구독마다 적절한 영역을 정의해야 합니다.
- [Azure 근접 배치 그룹](../../co-location.md)을 사용하지 않으면 Azure 가용성 영역 내에 Azure 가용성 세트를 배포할 수 없습니다. 다양한 영역에 걸쳐 SAP DBMS 계층과 중앙 서비스를 배포하는 동시에 가용성 집합을 이용해 SAP 애플리케이션 계층을 배포하면서도 VM의 근접성을 유지하는 방법은 [SAP 애플리케이션을 이용해 네트워크 대기 시간을 최적화하기 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md) 문서에 설명되어 있습니다. Azure 근접 배치 그룹을 사용하지 않는 경우, 가상 머신용 배포 프레임워크로 둘 중 하나를 선택해야 합니다.
- [Azure 기본 Load Balancer](../../../load-balancer/load-balancer-overview.md)를 사용하여 Windows Server 장애 조치(failover) 클러스터링 또는 Linux Pacemaker를 기반으로 장애 조치(failover) 클러스터 솔루션을 만들 수 없습니다. 대신 [Azure 표준 Load Balancer SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다.



## <a name="the-ideal-availability-zones-combination"></a>이상적인 가용성 영역 조합
여러 영역에 걸쳐 SAP NetWeaver 또는 S/4HANA 시스템을 배포하고자 하는 경우, 배포할 수 있는 두 가지의 원칙적 아키텍처가 있습니다.

- 활성/활성: ASCS/SCS를 실행하는 VM 쌍 및 DBMS 계층을 실행하는 VM 쌍은 두 영역에 걸쳐 분산됩니다. SAP 애플리케이션 계층을 실행하는 VM의 수는 동일한 두 영역에 걸쳐 짝수 번에 배포됩니다. DBMS 또는 ASCS/SCS VM이 장애 조치(failover)되면, 열려 있는 활성 트랜잭션 일부가 롤백될 수 있습니다. 그렇다고 하더라도 사용자는 로그인 상태로 남아 있습니다. 활성 DBMS VM과 애플리케이션 인스턴스가 어떤 영역에서 실행되는지는 중요하지 않습니다. 해당 아키텍처는 여러 영역에 걸쳐 배포할 때 바람직한 아키텍처입니다.
- 활성/비활성: ASCS/SCS를 실행하는 VM 쌍 및 DBMS 계층을 실행하는 VM 쌍은 두 영역에 걸쳐 분산됩니다. SAP 애플리케이션 계층을 실행하는 VM의 수는 가용성 영역 중 하나에 배포됩니다. 애플리케이션 계층은 활성 ASCS/SCS 및 DBMS 인스턴스와 동일한 영역에서 실행합니다. 여러 영역에 걸쳐 있는 네트워크 대기 시간이 너무 길어서 해당 영역들에 배포된 애플리케이션 계층을 실행할 수 없는 경우 해당 배포 아키텍처를 사용합니다. 그 대신, SAP 애플리케이션 계층은 활성 ASCS/SCS 및/또는 DBMS 인스턴스와 동일한 영역에서 실행해야 합니다. ASCS/SCS 또는 DBMS VM이 보조 영역으로 장애 조치(failover)되면 네트워크 대기 시간이 더 길어져 처리량이 감소할 수 있습니다. 처리량을 이전 수준으로 회복하려면 최대한 빨리 이전에 장애 조치(failover)된 VM의 장애를 복구해야 합니다. 영역 중단이 발생할 경우 애플리케이션 계층을 보조 영역으로 장애 조치(failover)해야 합니다. 사용자가 시스템을 완전히 종료할 때 발생하는 작업입니다. Azure 지역 일부에서는 해당 아키텍처가 가용성 영역을 사용하려는 경우에 유일하게 사용할 수 있는 아키텍처입니다. ASCS/SCS 또는 DBMS VMS를 보조 영역으로 장애 조치(failover)함으로써 생길 수 있는 영향을 수락할 수 없는 경우에는 가용성 집합 배포를 유지하는 것이 더 좋을 수도 있습니다.


그러므로 가용성 영역을 사용하는 방법을 결정하기 전에는 다음 사항을 확인해야 합니다.

- Azure 지역의 세 영역 간 네트워크 대기 시간. 영역 내의 구역 간에 발생하는 네트워크 대기 시간에 대해 인지하고 있다면 영역 간 네트워크 트래픽에서 네트워크 대기 시간이 가장 적은 영역을 선택할 수 있습니다.
- 선택한 영역 중 하나의 VM 간 대기 시간과 선택한 두 영역 간 네트워크 대기 시간의 차이
- 선택한 두 영역에서 배포 해야 하는 VM 유형을 사용할 수 있는 여부를 결정합니다. 일부 VM, 특히 M 시리즈 VM을 사용하는 경우 일부 SKU를 세 영역 중 두 영역에서만 사용할 수 있는 경우가 발생할 수 있습니다.

## <a name="network-latency-between-and-within-zones"></a>영역 간 및 영역 내 네트워크 대기 시간
다른 영역 간 대기 시간을 확인하려면 다음을 수행해야 합니다.

- DBMS 인스턴스에 사용하려는 VM SKU를 세 가지 영역 모두에 배포합니다. 이러한 측정을 수행할 때 [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 사용하도록 설정해야 합니다.
- 최소 네트워크 대기 시간을 나타내는 두 영역을 찾으면 세 가용성 영역에 걸쳐 애플리케이션 계층 VM으로 사용하려는 VM SKU의 다른 3개 VM을 배포합니다. 선택한 두 개의 ‘DBMS’ 영역에서 두 ‘DBMS VM’의 네트워크 대기 시간을 측정합니다. 
- **`niping`** 을 측정 도구로 사용합니다. SAP의 이 도구는 SAP support note [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에서 설명합니다. 대기 시간 측정에 대한 명령을 중심으로 참조하세요. **ping** 은 Azure Accelerated Networking 코드 경로를 통해 작동하지 않으므로 사용하지 않는 것이 좋습니다.

해당 테스트는 수동으로 수행하지 않아도 됩니다. 설명했던 대기 시간 테스트를 자동화하는 PowerShell 프로시저 [가용성 영역 대기 시간 테스트](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)를 찾을 수 있습니다. 

가용성 영역의 VM SKU 측정값 및 가용성에 따라, 다음과 같은 사항을 결정해야 합니다.

- DBMS 계층의 이상적인 영역을 정의합니다.
- 영역 내부 및 영역 간 네트워크 대기 시간 차이에 따라, 1개, 2개 또는 3개의 모든 영역에서 활성 SAP 애플리케이션 계층을 배포할 것인지를 결정합니다.
- 애플리케이션 관점에서 액티브/패시브 구성을 배포할지 또는 액티브/액티브 구성을 배포할지를 결정합니다. (이러한 구성은 이 문서의 뒷부분에서 설명합니다.)

이러한 결정을 내릴 때는 SAP note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 설명된 대로 SAP의 네트워크 대기 시간 권장 사항을 고려합니다.

> [!IMPORTANT]
> 수행하는 측정 및 의사 결정은 이러한 측정을 수행할 때 사용한 Azure 구독에 대해 유효합니다. 다른 Azure 구독을 사용하는 경우 측정을 반복해야 합니다. 열거된 영역의 매핑이 다른 Azure 구독과 다를 수 있습니다.


> [!IMPORTANT]
> 앞서 설명한 측정이 [가용성 영역](https://azure.microsoft.com/global-infrastructure/geographies/)을 지원하는 모든 Azure 지역에서 다른 결과를 제공할 수 있습니다. 네트워크 대기 시간에 대한 요구 사항은 같더라도, 영역 간 네트워크 대기 시간이 다를 수 있으므로 Azure 지역마다 다른 배포 전략을 채택해야 할 수 있습니다. 일부 Azure 지역에서는 3개의 다른 영역 간 네트워크 대기 시간이 크게 다를 수 있습니다. 반면에 다른 지역에서는 3개의 다른 영역 간 네트워크 대기 시간이 좀 더 균일할 수 있습니다. 항상 1~2밀리초 사이의 네트워크 대기 시간이 나타난다는 주장은 맞지 않습니다. Azure 지역의 가용성 영역 간 네트워크 대기 시간은 일반화할 수 없습니다.

## <a name="activeactive-deployment"></a>액티브/액티브 배포
해당 배포 아키텍처는 두세 개의 영역 간에 활성 SAP 애플리케이션 서버를 배포한다는 점에서 활성/활성으로 불립니다. 큐에 넣기 복제를 사용하는 SAP Central Services 인스턴스는 두 영역 간에 배포됩니다. SAP Central Services와 동일한 영역 간에 배포되는 DBMS 계층의 경우도 마찬가지입니다. 이러한 구성을 고려하려면 지역에서 사용자의 워크로드와 동기 DBMS 복제에 적합한 영역 간 네트워크 대기 시간을 제공하는 2개의 가용성 영역을 찾아야 합니다. 또한 선택한 영역 내의 네트워크 대기 시간과 영역 간 네트워크 대기 시간 간의 델타를 너무 크게 유지하지 않으려고 할 것입니다. 

SAP 아키텍처의 특징은 달리 구성하지 않는 한 사용자 작업과 일괄 처리 작업을 각기 다른 애플리케이션 인스턴스에서 실행할 수 있다는 것입니다. 활성/활성 배포의 이러한 점으로 인한 부작용은 동일한 영역에서 활성 DBMS을 통해 실행되는지 여부와 무관하게 어떤 SAP 애플리케이션 인스턴스를 통해서도 일괄 처리 작업이 실행될 수 있다는 것입니다. 각기 다른 영역 간의 네트워크 대기 시간이 동일 영역 내의 네트워크 대기 시간에 비해 적은 경우, 일괄 처리 작업의 런타임끼리의 차이는 미미할 수 있습니다. 그러나, 영역 간 네트워크 트래픽에 비해 동일 영역 내 네트워크 대기 시간의 차이가 커지면 커질수록 DBMS 인스턴스가 비활성 상태인 영역에서 작업이 실행되는 경우 해당 일괄 처리 작업의 런타임이 더 크게 영향을 받을 수 있습니다. 런타임의 차이를 어디까지 허용할 지는 고객이 판단할 사항입니다. 영역 간 트래픽에 대한 네트워크 대기 시간을 어디까지 허용할 지도 마찬가지입니다.

다양한 가용성 영역에 걸쳐 배포된 애플리케이션 계층 내에서 이러한 활성/활성 배포가 런타임과 처리량에서 큰 차이를 보이지 않고도 가능한 Azure 지역은 다음과 같습니다.

- 미국 서부2(세 영역 모두)
- 미국 동부2(세 영역 모두)
- 미국 중부(세 영역 모두)
- 북유럽(세 영역 모두)
- 서유럽(세 영역 중 두 영역)
- 미국 동부(세 영역 중 두 영역)
- 미국 중남부(세 영역 중 두 영역)
- 영국 남부(세 영역 중 두 영역)
- 동남 아시아

영역 간 SAP 배포 아키텍처가 권장되지 않는 Azure 지역은 다음과 같습니다.

- 프랑스 중부 
- 남아프리카 북부
- 캐나다 중부
- 일본 동부

런타임 차이에 대해 어느 정도까지 허용할 것인가에 따라 목록에서 빠진 지역도 포함시킬 수 있습니다.


두 영역에 걸친 액티브/액티브 배포의 단순화된 스키마는 다음과 같을 수 있습니다.

![액티브/액티브 영역 배포](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- [Azure 근접 배치 그룹](../../co-location.md)을 사용하지 않으면 가용성 집합을 Azure 가용성 영역에 배포할 수 없으므로 Azure 가용성 영역을 모든 VM의 장애 및 업데이트 도메인으로 취급합니다.
- DBMS 계층과 중앙 서비스를 위한 영역 배포를 결합하면서도 애플리케이션 계층에 대한 Azure 가용성 집합은 사용하고자 하는 경우, [SAP 애플리케이션을 이용해 네트워크 대기 시간을 최적화하기 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md) 문서에 설명한 대로 Azure 근접 그룹을 사용해야 합니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터를 위한 부하 분산 장치는 [표준 SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 필요하지 않습니다.
- 배포하는 모든 가상 머신에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure Premium Storage나 [울트라 SSD 스토리지](../../disks-types.md#ultra-disks) 또는 ANF는 영역 간의 스토리지 복제 중 어떤 유형도 지원하지 않습니다. 애플리케이션(DBMS 또는 SAP Central Services)은 중요한 데이터를 복제해야 합니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 다음과 같은 기술이 지원됩니다.
  - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션
  - SUSE Linux에서, [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)에 설명된 대로 빌드한 NFS 공유
    
    현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)에 설명된 대로 Microsoft SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.
- 세 번째 영역은 [SUSE Linux Pacemaker 클러스터](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)를 빌드하고 SBD 디바이스를 Azure Fencing 에이전트 대신 사용하는 경우에 SBD 디바이스를 호스트하는 데 사용됩니다. 또는 추가적인 애플리케이션 인스턴스에 사용됩니다.
- 중요한 비즈니스 프로세스에 런타임 일관성을 이끌어 내기 위해, SAP 일괄 처리 서버 그룹, SAP 로그온 그룹이나 RFC 그룹을 이용해 활성 DBMS 인스턴스가 있는 영역 내의 애플리케이션 인스턴스로 특정 일괄 처리 작업과 사용자를 유도하도록 할 수 있습니다. 그러나 영역 장애 조치(failover)의 경우 이러한 그룹을 활성 DB VM이 있는 영역 내 VM에서 실행되는 인스턴스로 수동으로 이동해야 합니다.  
- 각 영역에서 유휴 대화 상자 인스턴스를 배포할 수 있습니다. 

> [!IMPORTANT]
> 해당 활성/활성 시나리오에 대한 대역폭 추가 요금은 Microsoft에서 2020년 1월 4일에 발표합니다. [Bandwidth 가격 세부 정보](https://azure.microsoft.com/pricing/details/bandwidth/) 문서를 확인하세요. SAP 애플리케이션 계층과 SAP DBMS 계층 간의 데이터 전송은 매우 집약적입니다. 따라서 활성/활성 시나리오는 비용에 상당히 기여할 수 있습니다. 정확한 비용을 확인하려면 해당 문서를 계속 확인하세요. 


## <a name="activepassive-deployment"></a>액티브/패시브 배포
한 영역 내의 네트워크 대기 시간과 영역 간 네트워크 트래픽의 대기 시간 간에 적합한 델타를 찾을 수 없는 경우 SAP 애플리케이션 계층의 관점에서 액티브/패시브 특성이 있는 아키텍처를 배포할 수 있습니다. 완전한 애플리케이션 계층을 배포하고, 활성 DBMS 및 SAP Central Services 인스턴스를 둘 다 실행하려고 하는 *활성* 영역을 정의합니다. 이러한 구성을 사용할 경우 작업이 활성 DBMS 인스턴스가 있는 영역 내에서 실행되는지 여부에 따라, 비즈니스 트랜잭션 및 일괄 처리 작업의 런타임이 크게 달라지지 않는지 확인해야 합니다.

여러 영역에 걸친 배포 아키텍처로 해당 유형을 선호할 가능성이 있는 Azure 지역은 다음과 같습니다.

- 오스트레일리아 동부
- 브라질 남부
- 독일 중서부
- 남아프리카 북부
- 프랑스 중부 
- 캐나다 중부
- 일본 동부


이 아키텍처의 기본 레이아웃은 다음과 같습니다.

![액티브/패시브 영역 배포](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- Azure 가용성 영역에서 가용성 집합을 배포할 수 없습니다. 이를 보완하고자 [SAP 애플리케이션을 이용해 네트워크 대기 시간을 최적화하기 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md) 문서에서 설명한 대로 Azure 근접 배치 그룹을 사용할 수 있습니다.
- 이러한 아키텍처를 사용할 때는 상태를 면밀히 모니터링하고, 배포한 애플리케이션 계층과 동일한 영역에 활성 DBMS 및 SAP Central Services 인스턴스를 유지해야 합니다. SAP Central Service 또는 DBMS 인스턴스의 장애 조치(failover)가 수행될 경우 가능한 한 빨리, SAP 애플리케이션 계층이 배포된 영역으로 수동으로 장애 복구(failback)할 수 있습니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터를 위한 부하 분산 장치는 [표준 SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 필요하지 않습니다.
- 배포하는 모든 가상 머신에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure Premium Storage나 [울트라 SSD 스토리지](../../disks-types.md#ultra-disks) 또는 ANF는 영역 간의 스토리지 복제 중 어떤 유형도 지원하지 않습니다. 애플리케이션(DBMS 또는 SAP Central Services)은 중요한 데이터를 복제해야 합니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 다음과 같은 기술이 지원됩니다.
    - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션
    - SUSE Linux에서, [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)에 설명된 대로 빌드한 NFS 공유
    
  현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)에 설명된 대로 Microsoft SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.
- 세 번째 영역은 [SUSE Linux Pacemaker 클러스터](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)를 빌드하고 SBD 디바이스를 Azure Fencing 에이전트 대신 사용하는 경우에 SBD 디바이스를 호스트하는 데 사용됩니다. 또는 추가적인 애플리케이션 인스턴스에 사용됩니다.
- 영역 오류 발생 시 애플리케이션 리소스를 시작할 수 있도록 유휴 VM을 패시브 영역(DBMS의 관점에서)에 배포해야 합니다.
    - 현재는 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)를 사용하여 영역 간 유휴 VM에 활성 VM을 복제할 수 없습니다. 
- 영역 중단이 발생할 경우 두 번째 영역에서 SAP 애플리케이션 계층을 자동으로 시작하도록 하는 자동화 기능에 투자해야 합니다.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>높은 가용성 및 재해 복구 구성
Microsoft는 Azure 지역의 다른 Azure 가용성 영역을 호스트하는 시설 간의 지리적 거리에 대한 정보를 공유하지 않습니다. 아직도 일부 고객은 제로의 RPO(복구 지점 목표)를 약속하는 조합된 HA 및 DR 구성을 위해 영역을 사용하고 있습니다. RPO가 없다는 것은 재해 복구의 경우에도 커밋된 데이터베이스 트랜잭션을 손실하지 않게 된다는 것을 의미합니다. 

> [!NOTE]
> 이와 같은 구성은 특정 상황에서만 사용하는 것이 좋습니다. 예를 들어, 보안 또는 규정 준수 때문에 Azure 지역 외부로 데이터를 이동할 수 없는 경우에 이 구성을 사용할 수 있습니다. 

다음은 이러한 구성의 작동 방식 예제입니다.

![영역의 결합된 고가용성 DR](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

이 구성에 대해 다음과 같은 고려 사항이 적용됩니다.

- 가용성 영역을 호스트하는 시설이 서로 상당히 멀리 떨어져 있거나 특정 Azure 영역을 벗어날 수 없다고 가정합니다. Azure 가용성 영역에서 가용성 집합을 배포할 수 없습니다. 이를 보완하고자 [SAP 애플리케이션을 이용해 네트워크 대기 시간을 최적화하기 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md) 문서에서 설명한 대로 Azure 근접 배치 그룹을 사용할 수 있습니다.
- 이러한 아키텍처를 사용할 때는 상태를 면밀히 모니터링하고, 배포한 애플리케이션 계층과 동일한 영역에 활성 DBMS 및 SAP Central Services 인스턴스를 유지해야 합니다. SAP Central Service 또는 DBMS 인스턴스의 장애 조치(failover)가 수행될 경우 가능한 한 빨리, SAP 애플리케이션 계층이 배포된 영역으로 수동으로 장애 복구(failback)할 수 있습니다.
- 프로덕션 애플리케이션 인스턴스를 활성 QA 애플리케이션 인스턴스를 실행하는 VM에 미리 설치해야 합니다.
- 영역 오류 발생 시 QA 애플리케이션 인스턴스를 종료하고, 대신 프로덕션 인스턴스를 시작합니다. 이렇게 하려면 애플리케이션 인스턴스의 가상 이름을 사용해야 합니다.
- SAP Central Services와 DBMS 계층의 장애 조치(failover) 클러스터를 위한 부하 분산 장치는 [표준 SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다. 기본 Load Balancer는 여러 영역에 걸쳐 작동하지 않습니다.
- SAP 시스템을 호스트하기 위해 배포한 Azure Virtual Network 및 해당 서브넷은 영역에 걸쳐 확장됩니다. 각 영역에 대해 별도의 가상 네트워크가 필요하지 않습니다.
- 배포하는 모든 가상 머신에 대해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용해야 합니다. 영역 배포에서 관리되지 않는 디스크는 사용할 수 없습니다.
- Azure Premium Storage 또는 [울트라 SSD 스토리지](../../disks-types.md#ultra-disks)는 영역 간에 어떤 유형의 스토리지 복제도 지원하지 않습니다. 애플리케이션(DBMS 또는 SAP Central Services)은 중요한 데이터를 복제해야 합니다.
- 공유 디스크(Windows), CIFS 공유(Windows) 또는 NFS 공유(Linux)에 해당하는 공유 sapmnt 디렉터리의 경우도 마찬가지입니다. 영역 간에 이러한 공유 디스크 또는 공유를 복제하는 기술을 사용해야 합니다. 다음과 같은 기술이 지원됩니다.
    - Windows에서 [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)에 설명된 것처럼 SIOS Datakeeper를 사용하는 클러스터 솔루션
    - SUSE Linux에서, [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)에 설명된 대로 빌드한 NFS 공유

  현재, [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)에 설명된 대로 Microsoft SOFS(스케일 아웃 파일 서비스)를 사용하는 솔루션은 영역 간에 지원되지 않습니다.
- 세 번째 영역은 [SUSE Linux Pacemaker 클러스터](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)를 빌드하고 SBD 디바이스를 Azure Fencing 에이전트 대신 사용하는 경우에 SBD 디바이스를 호스트하는 데 사용됩니다. 





## <a name="next-steps"></a>다음 단계
Azure 가용성 영역에 걸쳐 배포하기 위한 다음 단계는 다음과 같습니다.

- [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)
- [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)