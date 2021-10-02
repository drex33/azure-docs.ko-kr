---
title: 개념 - AKS(Azure Kubernetes Services)의 지속 가능한 소프트웨어 엔지니어링
description: AKS(Azure Kubernetes Services)의 지속 가능한 소프트웨어 엔지니어링에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: 3fb40b278a4107cb1cdba2230602b99e2bdfff85
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355474"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 지속 가능한 소프트웨어 엔지니어링 원칙

지속 가능한 소프트웨어 엔지니어링 원칙은 지속 가능한 애플리케이션을 정의, 빌드, 실행하는 데 도움이 되는 역량 집합입니다. 전반적인 목표는 응용 프로그램의 모든 측면에서 참조 공간을 줄이는 것입니다. [지속 가능한 소프트웨어 엔지니어링 원칙][principles-sse]에는 지속 가능한 소프트웨어 엔지니어링 원칙에 대한 개요가 포함되어 있습니다.

지속 가능한 소프트웨어 엔지니어링은 우선 순위와 초점을 전환합니다. 대부분의 경우에는 소프트웨어를 설계하고 실행할 때 빠른 성능과 낮은 대기 시간이 강조됩니다. 하지만 지속 가능한 소프트웨어 엔지니어링은 가능한 한 탄소 배출량을 줄이는 데에 중점을 둡니다. 고려할 사항은 다음과 같습니다.

* 지속 가능한 소프트웨어 엔지니어링 원칙은 종합적인 네트워크 이동을 감소하는 등의 방식으로 더 빠른 성능 또는 낮은 대기 시간을 제공할 수 있습니다. 
* 탄소 배출량을 줄이기 위해서는 우선 순위가 낮은 워크로드를 지연시키는 등의 방식으로 성능이 느려지거나 대기 시간이 늘어날 수 있습니다. 

지속 가능한 소프트웨어 엔지니어링 원칙을 자신의 애플리케이션에 적용하기 전에 해당 애플리케이션의 우선 순위, 요구, 장단점을 검토하세요.

## <a name="measure-and-optimize"></a>측정 및 최적화

AKS 클러스터의 참조 공간을 줄이려면 클러스터의 리소스를 사용 하는 방법을 이해 해야 합니다. [Azure Monitor][azure-monitor]는 메모리 및 CPU 사용량과 같은 클러스터의 리소스 사용에 대한 세부 정보를 제공합니다. 이 데이터는 클러스터의 탄소 발자국을 줄이기 위한 결정에 도움을 주고 변경 효과를 관찰합니다. 

[Microsoft 지속 가능성 계산기][sustainability-calculator]를 설치하여 모든 Azure 리소스의 탄소 발자국을 확인할 수도 있습니다.

## <a name="increase-resource-utilization"></a>리소스 사용률 증가

탄소 발자국을 줄이기 위한 한 가지 방법은 유휴 시간을 줄이는 것입니다. 유휴 시간을 줄이면 컴퓨팅 리소스의 사용률이 늘어납니다. 예를 들면 다음과 같습니다.
1. 클러스터에 4개 노드가 있고 각 노드가 50% 용량으로 실행됩니다. 따라서 4개 노드 모두 50%에 해당하는 사용되지 않는 용량이 유휴 상태로 유지됩니다. 
1. 클러스터를 3개 노드로 줄이면 각 노드가 67% 용량으로 실행되고 워크로드는 동일합니다. 이렇게 하면 각 노드에서 사용되지 않는 용량을 33%로 줄이고 사용률을 늘릴 수 있습니다.

> [!IMPORTANT]
> 클러스터에서 리소스 변경을 고려할 때는 클러스터의 코어 시스템 구성 요소를 안정적으로 유지할 수 있도록 [시스템 풀][system-pools]에 리소스가 충분한지 확인하세요. 클러스터가 불안정해질 수 있는 수준까지 클러스터의 리소스를 줄일 수는 **없습니다**.

클러스터의 사용률을 검토한 후에는 [여러 노드 풀][multiple-node-pools]에서 제공하는 기능을 사용하는 것이 좋습니다. 

* 노드 크기 조정

    [노드 크기 조정][node-sizing]을 사용하여 특정 CPU 및 메모리 프로필로 노드 풀을 정의하고 워크로드 요구에 맞게 노드를 조정합니다. 워크로드 요구에 맞게 노드 크기를 조절하여 적은 수의 노드를 높은 사용률로 실행할 수 있습니다. 

* 클러스터 크기 조정

    클러스터 [크기 조정][scale] 방법을 구성합니다. [수평 Pod 자동 크기 조정기][scale-horizontal] 및 [클러스터 자동 크기 조정기][scale-auto]를 사용하여 구성에 따라 자동으로 클러스터 크기를 조정합니다. 클러스터의 워크로드에 변경 사항을 동기화된 상태로 유지하면서 모든 노드가 높은 사용률로 실행되도록 클러스터 크기 조정 방법을 제어합니다. 

* 스폿 풀

    워크로드에 갑작스러운 중단 또는 종료에 대한 내결함성이 있는 경우 [스폿 풀][spot-pools]을 사용할 수 있습니다. 스폿 풀은 Azure 내에서 유휴 용량을 활용합니다. 예를 들어, 일괄 작업 또는 개발 환경에서 스폿 풀이 원활하게 작동할 수 있습니다.

> [!NOTE]
>사용률을 높이면 과도한 노드를 줄일 수 있으므로 [각 노드의 리소스 예약][resource-reservations]에 사용되는 에너지를 줄일 수 있습니다.

마지막으로 애플리케이션의 Kubernetes 매니페스트에서 CPU 및 메모리 *요청* 과 *한도* 를 검토합니다. 
* 메모리 및 CPU 값을 줄이면 클러스터에서 더 많은 메모리 및 CPU를 사용하여 다른 워크로드를 실행할 수 있습니다. 
* 낮은 CPU와 메모리를 사용하여 더 많은 워크로드를 실행하는 경우 클러스터는 더 조밀하게 할당되므로 사용률이 높아집니다. 

애플리케이션의 CPU 및 메모리를 줄일 때 CPU 및 메모리 값을 너무 낮게 설정하면 애플리케이션의 동작이 저하되거나 불안정해질 수 있습니다. CPU 및 메모리 *요청* 과 *한도* 를 변경하기 전 몇 가지 벤치마킹 테스트를 실행하여 해당 값이 올바르게 설정되었는지 확인합니다. 이러한 값을 애플리케이션이 불안정해지는 지점까지 줄여서는 안 됩니다.

## <a name="reduce-network-travel"></a>네트워크 이동 감소

클러스터에 대한 요청 및 응답 이동 거리를 줄이면 네트워킹 디바이스에 의한 탄소 배출 및 전기 소비를 줄일 수 있습니다. 네트워크 트래픽을 검토한 후 네트워크 트래픽 소스에 가까운 [지역에][regions] 클러스터를 만드는 것이 좋습니다. [Azure Traffic Manager][azure-traffic-manager]를 사용하여 가장 가까운 클러스터 및 [근접 배치 그룹][proiximity-placement-groups]으로 트래픽을 라우팅하고 Azure 리소스 간의 거리를 줄일 수 있습니다.

> [!IMPORTANT]
> 클러스터의 네트워킹 변경을 고려할 때 워크로드 요구 사항 충족을 포기하면서까지 네트워크 이동을 줄이지 마세요. 예를 들어 [가용성 영역][availability-zones]을 사용하면 클러스터에서 더 많은 네트워크 이동이 발생하지만 워크로드 요구 사항을 처리하기 위해 가용성 영역이 필요할 수 있습니다.

## <a name="demand-shaping"></a>수요 셰이핑

가능하면 클러스터의 리소스에 대한 수요를 초과 용량을 사용할 수 있는 시간 또는 지역으로 이동하는 것이 좋습니다. 예를 들어 다음을 고려하세요.
* 일괄 작업 실행을 위한 시간 또는 지역을 변경합니다.
* [스폿 풀][spot-pools]을 사용합니다. 
* 즉각적인 처리가 필요하지 않은 워크로드 실행을 연기하기 위해 큐를 사용하도록 애플리케이션을 리팩터링합니다.

## <a name="next-steps"></a>다음 단계

이 문서에 설명된 AKS의 기능에 대해 자세히 알아보세요.

* [여러 노드 풀][multiple-node-pools]
* [노드 크기 조정][node-sizing]
* [클러스터 크기 조정][scale]
* [Horizontal Pod Autoscaler][scale-horizontal]
* [클러스터 자동 크기 조정기][scale-auto]
* [스폿 풀][spot-pools]
* [시스템 풀][system-pools]
* [리소스 예약][resource-reservations]
* [근접 배치 그룹][proiximity-placement-groups]
* [가용성 영역][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: /learn/modules/sustainable-software-engineering-overview/
