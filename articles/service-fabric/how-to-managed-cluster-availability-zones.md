---
title: 가용성 영역에 Service Fabric 관리형 클러스터 배포
description: 가용성 영역에 Service Fabric 관리형 클러스터를 배포하는 방법과 ARM 템플릿에서 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: b3c4e01ea66aa3a69b14806f2701020f1f529a5e
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544378"
---
# <a name="deploy-a-service-fabric-managed-cluster-across-availability-zones"></a>가용성 영역에 Service Fabric 관리형 클러스터 배포

Azure의 가용성 영역은 데이터 센터 오류로부터 애플리케이션 및 데이터를 보호하는 고가용성 제품입니다. 가용성 영역은 Azure 지역 내에서 독립적인 전원, 냉각 및 네트워킹을 갖춘 고유한 물리적 위치입니다.

Service Fabric 관리형 클러스터는 영역 복원력을 제공하기 위해 여러 가용성 영역에 걸쳐 배포를 지원합니다. 이 구성은 단일 장애 지점으로부터 보호할 중요한 시스템 서비스 및 애플리케이션의 고가용성을 보장합니다. Azure 가용성 영역을 모든 지역에서 사용할 수 있는 것은 아닙니다. 자세한 내용은 [Azure 가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

>[!NOTE]
>가용성 영역 스패닝은 표준 SKU 클러스터에서만 사용할 수 있습니다.

사용 가능한 샘플 템플릿: [Service Fabric 교차 가용성 영역 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommendations-for-zone-resilient-azure-service-fabric-managed-clusters"></a>영역 복원력이 있는 Azure Service Fabric 관리형 클러스터에 대한 권장 사항
가용성 영역에 분산된 Service Fabric 클러스터는 클러스터 상태의 고 가용성을 보장합니다. 

관리형 클러스터에 권장되는 토폴로지에는 아래에 설명된 리소스가 필요합니다.

* 클러스터 SKU는 표준이어야 합니다.
* 주 노드 유형에는 최상의 복원력을 위해 9개 이상의 노드가 있어야 하지만 최소 6개 노드를 지원합니다.
* 보조 노드 유형에는 최상의 복원력을 위해 6개 이상의 노드가 있어야 하지만 최소 3개 노드를 지원합니다.

>[!NOTE]
>3개의 가용성 영역 배포만 지원됩니다.

>[!NOTE]
> 비 스패닝에서 스팬 클러스터로 관리되는 클러스터의 현재 위치 변경을 수행할 수 없습니다.

Azure Service Fabric 가용성 영역 아키텍처 ![Azure Service Fabric 가용성 영역 아키텍처][sf-multi-az-arch]를 보여주는 다이어그램

영역에 걸쳐 있는 가상 머신 확장 집합의 FD/UD 형식을 설명하는 샘플 노드 목록

 ![영역에 걸쳐 있는 가상 머신 확장 집합의 FD/UD 형식을 설명하는 샘플 노드 목록][sfmc-multi-az-nodes]

**영역 간 서비스 복제본 배포**: 서비스가 영역에 걸쳐 있는 nodeTypes에 배포되면 복제본이 별도의 영역에 위치하도록 배치됩니다. 이 분리는 각 nodeTypes에 있는 노드의 장애 도메인이 영역 정보(예: FD = fd:/zone1/1 등)로 구성되므로 보장됩니다. 예를 들어 5개의 복제본 또는 서비스 인스턴스의 경우 배포는 2-2-1이 되고 런타임은 AZ 전체에 동일한 배포를 보장하려고 합니다.

**사용자 서비스 복제본 구성**: 교차 가용성 영역 nodeTypes에 배포된 상태 저장 사용자 서비스는 복제본 수는 대상 = 9, 최소 = 5 구성으로 구성되어야 합니다. 이 구성은 6개의 복제본이 다른 두 영역에서 계속 작동하므로 한 영역이 중단된 경우에도 서비스가 작동하는 데 도움이 됩니다. 이러한 시나리오에서는 애플리케이션 업그레이드도 진행됩니다.

**영역 중단 시나리오**: 영역이 중단되면 해당 영역의 모든 노드가 중단된 것으로 나타납니다. 이러한 노드의 서비스 복제본도 중단됩니다. 다른 영역에 복제본이 있으므로 서비스는 작동 중인 영역으로 장애 조치(failover)되는 주 복제본을 사용하여 계속 응답합니다. 대상 복제본 수가 충족되지 않고 VM 수가 정의된 최소 대상 복제본 크기보다 더 크기 때문에 서비스가 경고 상태로 표시됩니다. 따라서 Service Fabric 부하 분산 장치는 구성된 대상 복제본 개수와 일치하도록 작업 영역에 복제본을 가져옵니다. 이 시점에서 서비스는 정상으로 표시됩니다. 중단된 영역이 복구되면 부하 분산 장치는 다시 모든 서비스 복제본을 모든 영역에 균등하게 분산시킵니다.

## <a name="networking-configuration"></a>네트워킹 구성
자세한 내용은 [Service Fabric 관리형 클러스터에 대한 네트워크 설정 구성](./how-to-managed-cluster-networking.md)을 참조하세요.

## <a name="enabling-a-zone-resilient-azure-service-fabric-managed-cluster"></a>영역 복원력이 있는 Azure Service Fabric 관리형 클러스터 사용
영역 복원력이 있는 Azure Service Fabric 관리형 클러스터를 사용하도록 설정하려면 관리형 클러스터 리소스 정의에 다음을 포함해야 합니다.

* 클러스터가 영역 복원력이 있는지 여부를 지정하는 **ZonalResiliency** 속성입니다.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "zonalResiliency": "true"
}
```
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sfmc-multi-az-nodes]: ./media/how-to-managed-cluster-availability-zones/sfmc-multi-az-nodes.png
