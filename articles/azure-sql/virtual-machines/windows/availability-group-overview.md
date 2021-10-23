---
title: SQL Server Always On 가용성 그룹 개요
description: 이 문서에서는 Azure Virtual Machines의 SQL Server Always On 가용성 그룹을 소개합니다.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: rsetlem
ms.custom: seo-lt-2019
ms.reviewer: mathoma
ms.openlocfilehash: 4196ab27f5b3f4c6ab4897d2df8ad0b2007f8c2b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162802"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM의 SQL Server에 대한 Always On 가용성 그룹
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VMs(Virtual Machines)의 SQL Server에 대한 Always On AG(가용성 그룹)를 소개합니다. 

## <a name="overview"></a>개요

Azure Virtual Machines의 Always On 가용성 그룹은 [온-프레미스의 Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 유사하며 기본 [Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)를 사용합니다. 그러나 가상 머신은 Azure에서 호스팅되므로 VM 중복성 및 Azure 네트워크의 트래픽 라우팅과 같은 몇 가지 추가 고려 사항도 있습니다. 

다음 다이어그램에서는 Azure VM의 SQL Server에 대한 가용성 그룹을 보여 줍니다.

![가용성 그룹](./media/availability-group-overview/00-EndstateSampleNoELB.png)

> [!NOTE]
> 이제 Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 가용성 그룹 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [가용성 그룹 마이그레이션](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)을 참조하세요. 

## <a name="vm-redundancy"></a>VM 중복성 

중복성 및 고가용성을 강화하려면 SQL Server VM이 동일한 [가용성 집합](../../../virtual-machines/availability-set-overview.md) 또는 다른 [가용성 영역](../../../availability-zones/az-overview.md)에 있어야 합니다.

동일한 가용성 집합에 VM 세트를 배치하면 장비 장애(가용성 집합 내의 VM은 리소스를 공유하지 않음)로 인한 데이터 센터 내의 운영 중단이나 업데이트(가용성 집합 내의 VM은 동시에 업데이트되지 않음)로부터 보호됩니다. 

가용성 영역은 영역 내의 데이터 센터 세트를 나타내는 각 영역으로 전체 데이터 센터의 장애로부터 보호합니다.  리소스를 서로 다른 가용성 영역에 배치하면 데이터 센터 수준의 운영 중단으로 인해 모든 VM이 오프라인 상태가 되지는 않습니다.

Azure VM을 만들 때는 가용성 집합과 가용성 영역 중 하나를 선택하여 구성해야 합니다.  한 Azure VM이 두 가지 모두에 있을 수 없습니다. 

가용성 영역은 가용성 집합보다 더 나은 가용성을 제공할 수 있지만(99.99% / 99.95%) 성능도 고려해야 합니다. 가용성 집합 내의 VM은 [근접 배치 그룹](../../../virtual-machines/co-location.md)에 배치되어 서로 간에 가까운 위치가 보장되므로 네트워크 대기 시간을 최소화할 수 있습니다. 서로 다른 가용성 영역에 있는 VM의 경우 네트워크 대기 시간이 더 커집니다. 이로 인해 주 복제본과 보조 복제본 간에 데이터를 동기화하는 데 걸리는 시간이 늘어날 수 있습니다. 이로 인해 주 복제본에 지연이 발생할 뿐만 아니라 계획되지 않은 장애 조치 시 데이터 손실 가능성이 높아질 수 있습니다. 부하 상태에서 제안된 솔루션을 테스트하고 성능 및 가용성 모두에 대해 SLA를 충족하는지 확인하는 것이 중요합니다.

## <a name="connectivity"></a>연결

가용성 그룹에 대한 가상 네트워크 이름 또는 분산 네트워크 이름을 구성할 수 있습니다. [두 이상의 차이점을 검토](hadr-windows-server-failover-cluster-overview.md)한 다음, 가용성 그룹에 대해 [DNN(분산 네트워크 이름)](availability-group-distributed-network-name-dnn-listener-configure.md) 또는 [VNN(가상 네트워크 이름)](availability-group-vnn-azure-load-balancer-configure.md)을 배포합니다. 

DNN를 사용하는 경우 대부분의 SQL Server 기능이 가용성 그룹에서 투명하게 작동하지만 특별한 고려 사항이 필요할 수 있는 특정 기능이 있습니다. 자세히 알아보려면 [AG 및 DNN 상호 운용성](availability-group-dnn-interoperability.md)을 참조하세요. 

또한 VNN 수신기 및 DNN 수신기의 기능 간에는 주의해야 할 몇 가지 동작 차이점이 있습니다. 

- **장애 조치 시간**: 네트워크 부하 분산 장치가 오류 이벤트를 감지하고 라우팅을 변경할 때까지 기다릴 필요가 없으므로 DNN 수신기를 사용할 때 장애 조치 시간이 더 단축됩니다. 
- **기존 연결**: 장애 조치 가용성 그룹 내의 *특정 데이터베이스* 에 대한 연결이 끊어지지만 장애 조치 프로세스 중에 DNN이 온라인 상태를 유지하므로 주 복제본에 대한 다른 연결은 열린 상태로 유지됩니다. 이것은 가용성 그룹이 장애 조치되고 수신기가 오프라인으로 전환되고 주 복제본이 보조 역할로 전환되어 주 복제본에 대한 모든 연결이 닫히게 되는 기존 VNN 환경과는 다릅니다. DNN 수신기를 사용하는 경우 장애 조치 시 연결이 새 주 복제본으로 리디렉션되도록 애플리케이션 연결 문자열을 조정해야 할 수 있습니다.
- **트랜잭션 열기**: 장애 조치 가용성 그룹의 데이터베이스에 대해 열린 트랜잭션이 닫히고 롤백되며 *수동으로* 다시 연결해야 합니다. 예를 들어, SQL Server Management Studio에서 쿼리 창을 닫고 새 창을 엽니다. 

Azure에서 VNN 수신기를 설정하려면 부하 분산 장치가 필요합니다. Azure의 부하 분산 장치에는 외부(퍼블릭) 또는 내부라는 두 가지 기본 옵션이 있습니다. 외부(퍼블릭) 부하 분산 장치는 인터넷에 연결되며 인터넷을 통해 액세스할 수 있는 공용 가상 IP에 연결됩니다. 내부 부하 분산 장치는 동일한 가상 네트워크 내에 있는 클라이언트만 지원합니다. 각 부하 분산 장치 유형에 대해 [Direct Server Return](../../../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip)을 사용하도록 설정해야 합니다. 

서비스 인스턴스에 직접 연결하면 각 가용성 복제본에 개별적으로 연결할 수 있습니다. 또한, 가용성 그룹은 데이터베이스 미러링 클라이언트와 역방향 호환이 가능하므로 복제본이 데이터베이스 미러링과 유사하게 구성된 이상 데이터베이스 미러링 파트너와 같이 가용성 복제본에 연결할 수 있습니다.

* 하나의 주 복제본과 하나의 보조 복제본이 있습니다.
* 보조 복제본은 읽을 수 없도록 구성(**읽기 가능한 보조** 옵션을 **아니요** 로 설정)됩니다.

ADO.NET 또는 SQL Server Native Client를 사용하여 데이터베이스 미러링과 유사한 이 구성에 연결하는 클라이언트 연결 문자열의 예는 다음과 같습니다.

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

클라이언트 연결에 대한 자세한 내용은 다음을 참조하세요.

* [SQL Server Native Client에서 연결 문자열 키워드 사용](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [데이터베이스 미러링 세션(SQL Server)에 클라이언트 연결](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [하이브리드 IT 환경에서 가용성 그룹 수신기에 연결](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [가용성 그룹 수신기, 클라이언트 연결 및 애플리케이션 장애 조치(failover)(SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [가용성 그룹과 데이터베이스 미러링 연결 문자열 사용](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

## <a name="lease-mechanism"></a>임대 메커니즘 

SQL Server의 경우 AG 리소스 DLL은 AG 임대 메커니즘 및 Always On 상태 검색에 따른 AG의 상태를 결정합니다. AG 리소스 DLL은 *IsAlive* 작업을 통해 리소스 상태를 노출합니다. 리소스 모니터는 **CrossSubnetDelay** 및 **SameSubnetDelay** 클러스터 전체 값으로 설정된 클러스터 하트비트 간격으로 IsAlive를 폴링합니다. 리소스 DLL에 대한 IsAlive 호출이 AG의 상태가 정상이 아니라고 반환할 때마다 주 노드에서 클러스터 서비스는 장애 조치를 시작합니다.

AG 리소스 DLL은 내부 SQL Server 구성 요소의 상태를 모니터링합니다. Sp_server_diagnostics는 **HealthCheckTimeout** 에 의해 제어되는 간격으로 SQL Server에 이러한 구성 요소의 상태를 보고합니다.

다른 장애 조치 메커니즘과 달리 SQL Server 인스턴스는 임대 메커니즘에서 활성 역할을 수행합니다. 임대 메커니즘은 클러스터 리소스 호스트와 SQL Server 프로세스 간에 *LooksAlive* 유효성 검사로 사용됩니다. 메커니즘은 서로의 상태를 확인하고 궁극적으로 분리 장애(split-brain) 시나리오를 방지하여 양쪽(클러스터 서비스 및 SQL Server 서비스)이 자주 접촉하는 상태에 있는지 확인하는 데 사용됩니다.

Azure VM에서 AG를 구성할 때 이러한 임계값을 온-프레미스 환경에서 구성할 때와는 다르게 구성해야 하는 경우가 많습니다. Azure VM에 대한 모범 사례에 따라 임계값 설정을 구성하려면 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 참조하세요. 


## <a name="network-configuration"></a>네트워크 구성  

Azure VM 장애 조치 클러스터의 경우 서버(클러스터 노드)당 하나의 NIC 및 단일 서브넷을 사용하는 것이 좋습니다. Azure 네트워킹에는 Azure VM 장애 조치 클러스터에서 추가 NIC 및 서브넷을 불필요하게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure VM 장애 조치 클러스터에서는 이 경고를 무시해도 됩니다. 

## <a name="basic-availability-group"></a>기본 가용성 그룹

기본 가용성 그룹은 두 개 이상의 보조 복제본을 허용하지 않으며 보조 복제본에 대한 읽기 권한이 없으므로 기본 가용성 그룹에 데이터베이스 미러링 연결 문자열을 사용할 수 있습니다. 연결 문자열을 사용하면 수신기가 필요하지 않습니다. Azure VM의 가용성 그룹에서는 추가 데이터베이스에 대한 수신기가 여러 개인 경우 수신기 종속성을 제거하면 부하 분산 장치가 필요하지 않거나 부하 분산 장치에 IP를 더 추가할 필요가 없으므로 유용합니다. 

예를 들어, TCP/IP를 사용하여 기본 AG(또는 보조 복제본이 하나만 있고 보조 복제본에서 읽기 액세스가 허용되지 않는 AG)의 Replica_A 또는 Replica_B에 있는 AG 데이터베이스 AdventureWorks에 명시적으로 연결하려면 클라이언트 애플리케이션은 AG에 성공적으로 연결하기 위해 다음 데이터베이스 미러링 연결 문자열을 제공할 수 있습니다.

`Server=Replica_A; Failover_Partner=Replica_B; Database=AdventureWorks; Network=dbmssocn`


## <a name="deployment-options"></a>배포 옵션

가용성 그룹을 Azure VM의 SQL Server에 배포하는 여러 가지 옵션이 있으며, 일부는 다른 옵션보다 더 많은 자동화를 사용합니다. 

사용 가능한 옵션을 비교한 표는 다음과 같습니다.

| | [Azure Portal](availability-group-azure-portal-configure.md), | [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md) | [빠른 시작 템플릿](availability-group-quickstart-template-configure.md) | [수동](availability-group-manually-configure-prerequisites-tutorial.md) |
|---------|---------|---------|---------|---------|
|**SQL Server 버전** |2016 이상 |2016 이상|2016 이상|2012 이상|
|**SQL Server 에디션** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Windows Server 버전**| 2016 이상 | 2016 이상 | 2016 이상 | 모두|
|**사용자에 대한 클러스터 만들기**|예|예 | 예 |아니요|
|**사용자에 대한 가용성 그룹 만들기** |예 |아니요|아니요|예|
|**독립적으로 수신기 및 부하 분산 장치 만들기** |아니요|아니요|아니요|예|
|**이 메서드를 사용하여 DNN 수신기를 만들 수 있는지 여부**|아니요|아니요|아니요|예|
|**WSFC 쿼럼 구성**|클라우드 감시|클라우드 감시|클라우드 감시|모두|
|**여러 지역이 포함된 DR** |아니요|아니요|아니요|예|
|**다중 서브넷 지원** |예|예|예|예|
|**기존 AD 지원**|예|예|예|예|
|**동일한 지역의 다중 영역이 포함된 DR**|예|예|예|예|
|**AD가 없는 분산 AG**|아니요|아니요|아니요|예|
|**클러스터가 없는 분산 AG** |아니요|아니요|아니요|예|

## <a name="next-steps"></a>다음 단계

[HADR 모범 사례](hadr-cluster-best-practices.md)를 검토한 다음, [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI / PowerShell](./availability-group-az-commandline-configure.md), [빠른 시작 템플릿](availability-group-quickstart-template-configure.md)을 사용하여 가용성 그룹 배포를 시작하거나 [수동](availability-group-manually-configure-prerequisites-tutorial.md)으로 이 작업을 시작합니다.

또는 [클러스터가 없는 가용성 그룹](availability-group-clusterless-workgroup-configure.md) 또는 가용성 그룹을 [여러 지역](availability-group-manually-configure-multiple-regions.md)에 배포할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
