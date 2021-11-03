---
title: Windows Server 및 Linux에서 클러스터 만들기
description: Service Fabric 클러스터는 Windows Server 또는 Linux에서 실행됩니다. Windows Server 또는 Linux를 실행할 수 있는 모든 위치에서 Service Fabric 애플리케이션을 배포하고 호스팅할 수 있습니다.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: c527c80b540f38102792f1e2da27c25e55c7fb77
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003982"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Azure의 Service Fabric 클러스터 개요
Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 컴퓨터나 VM을 클러스터 노드라고 합니다. 클러스터의 규모를 수천 개의 노드로 확장할 수 있습니다. 새 노드를 클러스터에 추가하면 Service Fabric이 증가된 수의 노드에서 서비스 파티션 복제본 및 인스턴스의 균형을 조정합니다. 전반적인 애플리케이션 성능이 향상되고 메모리 액세스에 대한 경합이 감소합니다. 클러스터의 노드가 효율적으로 사용되지 않는 경우 클러스터의 노드 수를 줄일 수 있습니다. Service Fabric은 각 노드의 하드웨어를 보다 효율적으로 사용할 수 있도록 감소된 노드 수에 맞게 파티션 복제본 및 인스턴스의 균형을 다시 조정합니다.

클러스터에 있는 노드(가상 머신) 세트의 크기, 번호 및 속성이 노드 형식에 정의됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. 노드 형식은 "프런트 엔드" 또는 "백 엔드"와 같은 클러스터 노드 집합에 대한 역할을 정의하는 데 사용됩니다. 클러스터에 둘 이상의 노드 형식이 있을 수 있지만 주 노드 형식에는 프로덕션 클러스터에 대한 최소 5개의 VM(또는 테스트 클러스터에 대한 최소 3개의 VM)이 있어야 합니다. [Service Fabric 시스템 서비스](service-fabric-technical-overview.md#system-services)는 주 노드 형식의 노드에 배치됩니다. 

## <a name="cluster-components-and-resources"></a>클러스터 구성 요소 및 리소스
Azure의 Service Fabric 클러스터는 다른 Azure 리소스를 사용하고 상호 작용하는 Azure 리소스입니다.
* VM 및 가상 네트워크 카드
* 가상 머신 확장 집합
* 가상 네트워크
* 부하 분산 장치
* 스토리지 계정
* 공용 IP 주소

![Service Fabric 클러스터][Image]

### <a name="virtual-machine"></a>가상 머신
클러스터의 일부인 [가상 머신](../virtual-machines/index.yml)을 노드라고 하지만, 기술적으로 클러스터 노드는 Service Fabric 런타임 프로세스입니다. 각 노드는 노드 이름(문자열)에 할당됩니다. 노드는 [배치 속성](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)과 같은 특징이 있습니다. 각 컴퓨터 또는 VM에는 자동 시작 서비스인 *FabricHost.exe* 가 있습니다. 이 서비스는 부팅 시에 실행된 다음, 노드를 구성하는 *Fabric.exe* 및 *FabricGateway.exe* 의 두 실행 파일을 시작합니다. 프로덕션 배포는 물리적 또는 가상 머신당 하나의 노드입니다. 테스트 시나리오에서는 *Fabric.exe* 및 *FabricGateway.exe* 의 여러 인스턴스를 실행하여 단일 컴퓨터나 VM에 여러 노드를 호스트할 수 있습니다.

각 VM은 가상 NIC(네트워크 인터페이스 카드)와 연결되고 각 NIC에 개인 IP 주소가 할당됩니다.  VM은 NIC를 통해 가상 네트워크 및 부하 분산 분산에 할당됩니다.

클러스터의 모든 VM은 가상 네트워크에 배치됩니다.  동일한 노드 형식/확장 집합의 모든 노드는 가상 네트워크의 동일한 서브넷에 배치됩니다.  이러한 노드는 개인 IP 주소만 있으며 가상 네트워크 외부에서 직접 주소를 지정할 수 있습니다.  클라이언트는 Azure Load Balancer를 통해 노드의 서비스에 액세스할 수 있습니다.

### <a name="scale-setnode-type"></a>확장 집합/노드 형식
클러스터를 만들 때 하나 이상의 노드 형식을 정의합니다.  한 노드 형식의 노드 또는 VM은 CPU 수, 메모리, 디스크 수 및 디스크 I/O와 같은 동일한 크기 및 특성을 갖습니다.  예를 들어, 한 노드 형식은 인터넷에 열린 포트가 있는 소형 프런트 엔드 VM용이고 다른 노드 형식은 데이터를 처리하는 대형 백 엔드 VM용일 수 있습니다. Azure 클러스터에서 각 노드 유형은 [가상 머신 확장 집합](../virtual-machine-scale-sets/index.yml)에 매핑됩니다.

확장 집합을 사용하여 가상 머신 컬렉션을 배포 및 관리할 수 있습니다. Azure Service Fabric 클러스터에서 정의한 각 노드 형식은 별도의 확장 집합을 설정합니다. Service Fabric 런타임은 Azure VM 확장을 사용하여 확장 집합의 각 가상 머신에 부트스트랩됩니다. 각 노드 형식을 독립적으로 확장 또는 축소하고, 각 클러스터 노드에서 실행되는 OS SKU를 변경하고, 다른 포트의 집합을 열고, 다른 용량 메트릭을 사용할 수 있습니다. 하나의 확장 집합에 5개의 [업그레이드 도메인](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains)과 5개의 [장애 도메인](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)이 있고 최대 100개의 VM이 있을 수 있습니다.  여러 확장 집합/노드 형식을 만들어 100개보다 많은 노드를 포함하는 클러스터를 만듭니다.

> [!IMPORTANT]
> 클러스터의 노드 형식 수와 각 노드 형식의 속성(크기, 기본, 인터넷 연결, VM 수 등)을 선택하는 일은 중요한 작업입니다.  자세한 내용은 [클러스터 용량 계획 고려 사항](service-fabric-cluster-capacity.md)을 참조하세요.

자세한 내용은 [Service Fabric 노드 형식 및 가상 머신 확장 집합](service-fabric-cluster-nodetypes.md)을 참조하세요.

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM 인스턴스는 [공용 IP 주소](../virtual-network/ip-services/public-ip-addresses.md) 및 DNS 레이블과 연결되는 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 뒤에서 조인됩니다.  *&lt;clustername&gt;* , DNS 이름으로 클러스터를 프로비전하면 *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com* 은 확장 집합 앞에서 부하 분산 장치와 연결되는 DNS 레이블입니다.

클러스터의 VM에는 [개인 IP 주소](../virtual-network/ip-services/private-ip-addresses.md)만 있습니다.  관리 트래픽 및 서비스 트래픽은 공용 Load Balancer를 통해 라우팅됩니다.  네트워크 트래픽은 NAT 규칙(클라이언트가 특정 노드/인스턴스에 연결) 또는 부하 분산 규칙(트래픽이 VM으로 왕복 이동)을 통해 이러한 컴퓨터로 라우팅됩니다.  부하 분산 장치에서는 *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com* 형식으로 DNS 이름에 공용 IP가 연결됩니다.  공용 IP는 리소스 그룹의 다른 Azure 리소스입니다.  클러스터에 여러 노드 형식을 정의하면 각 노드 형식/확장 집합에 대해 부하 분산 장치가 만들어집니다. 또는 여러 노드 형식에 대해 단일 부하 분산 장치를 설정할 수도 있습니다.  주 노드 형식은 DNS 레이블이 *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com* 이고, 다른 노드 형식은 DNS 레이블이 *&lt;clustername&gt;-&lt;nodetype&gt;.&lt;location&gt;.cloudapp.azure.com* 입니다.

### <a name="storage-accounts"></a>Storage 계정
각 클러스터 노드 형식은 [Azure Storage 계정](../storage/common/storage-introduction.md) 및 Managed Disks에서 지원됩니다.

## <a name="cluster-security"></a>클러스터 보안
서비스 패브릭 클러스터는 사용자가 소유하는 리소스입니다.  사용자는 권한이 없는 사용자가 연결되는 것을 방지하기 위해 클러스터를 보호해야 합니다. 보안 클러스터는 클러스터에서 프로덕션 워크로드를 실행하는 경우에 특히 중요합니다. 

### <a name="node-to-node-security"></a>노드 간 보안
노드 간 보안은 클러스터의 VM 또는 컴퓨터 간 통신을 보호합니다. 이 보안 시나리오를 통해 클러스터에 가입하도록 인증된 컴퓨터만 호스팅 애플리케이션 및 클러스터의 서비스에 참여할 수 있습니다. 서비스 패브릭은 클러스터에 보안 적용을 하고 애플리케이션 보안 기능을 제공하기 위해 X.509 인증서를 사용합니다.  클러스터 인증서는 클러스터 트래픽의 보안을 유지하고 클러스터 및 서버 인증을 제공하는 데 필요합니다.  자체 서명된 인증서를 테스트 클러스터에 사용할 수 있지만 프로덕션 클러스터 보안 유지에는 신뢰할 수 있는 인증 기관의 인증서를 사용해야 합니다.

자세한 내용은 [노드 간 보안](service-fabric-cluster-security.md#node-to-node-security)을 참조하세요.

### <a name="client-to-node-security"></a>클라이언트-노드 보안
클라이언트-노드 보안은 클라이언트를 인증하고 클라이언트와 클러스터의 개별 노드 간 통신을 보호하도록 합니다. 이 보안 유형을 통해 인증된 사용자만 클러스터 및 클러스터에 배포된 애플리케이션에 액세스할 수 있도록 합니다. 클라이언트는 X.509 인증서 보안 자격 증명을 통해 고유하게 식별됩니다. 개수에 관계없이 선택적 클라이언트 인증서를 사용하여 클러스터에서 관리자 또는 사용자 클라이언트를 인증할 수 있습니다.

클라이언트 인증서 외에, Azure Active Directory도 클러스터에서 클라이언트를 인증하도록 구성할 수 있습니다.

자세한 내용은 [클라이언트-노드 보안](service-fabric-cluster-security.md#client-to-node-security)을 참조하세요.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어
Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하면 Azure 리소스에 대해 세분화된 액세스 제어를 할당할 수 있습니다.  구독, 리소스 그룹 및 리소스에 대해 다른 액세스 규칙을 할당할 수 있습니다.  Azure RBAC 규칙은 더 낮은 수준에서 재정의하지 않는 한, 리소스 계층 구조에 따라 상속됩니다.  지정된 사용자 및 그룹이 클러스터를 수정할 수 있도록 Azure RBAC 규칙을 사용하여 AAD에 사용자 또는 사용자 그룹을 할당할 수 있습니다.  자세한 내용은 [Azure RBAC 개요](../role-based-access-control/overview.md)를 참조하세요.

또한 Service Fabric은 다른 사용자 그룹의 특정 클러스터 작업에 대한 액세스를 제한하기 위해 액세스 제어를 지원합니다. 이를 통해 클러스터의 보안을 강화합니다. 클러스터에 연결하는 클라이언트에 대해 두 가지 액세스 제어 유형인 관리자 역할 및 사용자 역할이 지원됩니다.  

자세한 내용은 [Service Fabric 역할 기반 액세스 제어](service-fabric-cluster-security.md#service-fabric-role-based-access-control)를 참조하세요.

### <a name="network-security-groups"></a>네트워크 보안 그룹 
NSG(네트워크 보안 그룹)은 서브넷, VM 또는 특정 NIC의 인바운드 및 아웃바운드 트래픽을 제어합니다.  기본적으로 여러 VM을 동일한 가상 네트워크에 배치하면 임의 포트를 통해 서로 통신할 수 있습니다.  컴퓨터 간 통신을 제한하려는 경우 NSG를 정의하여 네트워크를 분할하거나 VM을 서로 격리할 수 있습니다.  클러스터에 여러 노드 형식이 있는 경우 서브넷에 NSG를 적용하여 다른 노드 형식에 속하는 컴퓨터가 다른 컴퓨터와 통신하지 못하게 할 수 있습니다.  

자세한 내용은 [보안 그룹](../virtual-network/network-security-groups-overview.md)을 참조하세요.

## <a name="scaling"></a>크기 조정

애플리케이션 수요는 시간이 지남에 따라 달라집니다. 늘어난 애플리케이션 워크로드나 네트워크 트래픽을 충족하기 위해 클러스터 리소스를 늘리고, 수요가 줄어들면 클러스터 리소스를 줄여야 할 수 있습니다. Service Fabric 클러스터를 만든 후에 수평으로(노드 수 변경) 또는 수직으로(노드의 리소스 변경) 클러스터 크기를 조정할 수 있습니다. 클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다. 클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

자세한 내용은 [Azure 클러스터 크기 조정](service-fabric-cluster-scaling.md)을 참조하세요.

## <a name="upgrading"></a>업그레이드 중
Azure 서비스 패브릭 클러스터는 개인이 소유하지만 Microsoft에서 부분적으로 관리하는 리소스입니다. Microsoft는 기본 OS를 패치하고 클러스터에서 Service Fabric 런타임 업그레이드를 수행합니다. Microsoft에서 새로운 버전을 릴리스하거나 원하는 런타임 버전을 선택하도록 한 경우 클러스터가 자동 런타임 업그레이드를 수신하도록 설정할 수 있습니다. 런타임 업그레이드 외에도 인증서 또는 애플리케이션 포트와 같은 클러스터 구성을 업데이트할 수 있습니다.

자세한 내용은 [클러스터 업그레이드](service-fabric-cluster-upgrade.md)를 참조하세요.

## <a name="supported-operating-systems"></a>지원되는 운영 체제
추가 정보는 [Azure에서 지원되는 버전](./service-fabric-versions.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계
Azure 클러스터의 [보호](service-fabric-cluster-security.md), [크기 조정](service-fabric-cluster-scaling.md) 및 [업그레이드](service-fabric-cluster-upgrade.md)에 대해 자세히 알아봅니다.

[Service Fabric 지원 옵션](service-fabric-support.md)에 대해 알아봅니다.

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG