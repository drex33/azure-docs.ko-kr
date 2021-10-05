---
title: 여러 지역에서 SQL Server Always On 가용성 그룹 구성
description: 이 문서에서는 다른 지역의 복제본으로 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹을 구성하는 방법을 설명합니다.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: dc516c9631eda2904ff311af6ca779872d9802f0
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537185"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>여러 Azure 지역에서 SQL Server Always On 가용성 그룹 구성

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 원격 Azure 위치의 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹 복제본을 구성하는 방법을 설명합니다. 이 구성을 사용하여 재해 복구를 지원합니다.

이 문서는 Resource Manager 모드의 Azure Virtual Machines에 적용됩니다.

다음 이미지는 Azure Virtual Machines의 일반적인 가용성 그룹 배포를 보여 줍니다.

   !["Windows Server 장애 조치(Failover) 클러스터" 및 "Always On 가용성 그룹"을 사용하여 Azure Load Balancer 및 가용성 집합을 보여 주는 다이어그램입니다.](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

이 배포에서 모든 가상 머신은 하나의 Azure 지역에 있습니다. 가용성 그룹 복제본은 SQL-1 및 SQL-2에 대해 자동 장애 조치로 동기 커밋할 수 있습니다. 이 아키텍처를 작성하려면 [가용성 그룹 템플릿 또는 자습서](availability-group-overview.md)를 참조하세요.

이 아키텍처는 Azure 지역에 액세스할 수 없게 될 경우 가동 중지 시간에 취약합니다. 이 취약점을 해결하려면 다른 Azure 지역에 복제본을 추가합니다. 다음 다이어그램은 새로운 아키텍처 모양을 보여 줍니다.

   ![가용성 그룹 DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

위 다이어그램에서는 SQL-3라는 새 가상 머신을 보여 줍니다. SQL-3는 다른 Azure 지역에 있습니다. SQL-3가 Windows Server 장애 조치 클러스터에 추가됩니다. SQL-3는 가용성 그룹 복제본을 호스트할 수 있습니다. 마지막으로 SQL-3에 대한 Azure 지역에는 새 Azure Load Balancer가 있습니다.

>[!NOTE]
> 둘 이상의 가상 머신이 동일한 지역에 있을 때 Azure 가용성 집합이 필요합니다. 해당 지역에 가상 컴퓨터가 하나만 있으면 가용성 집합이 필요하지 않습니다. 생성 시 가용성 집합에 하나의 가상 머신만 배치할 수 있습니다. 해당 가상 컴퓨터가 가용성 집합에 이미 있으면 나중에 추가 복제본에 대해 가상 컴퓨터를 추가할 수 있습니다.

이 아키텍처에서 원격 지역의 복제본은 일반적으로 비동기 커밋 가용성 모드와 수동 장애 조치 모드로 구성됩니다.

가용성 그룹 복제본이 다른 Azure 지역의 Azure Virtual Machines에 있는 경우에 각 지역에 다음이 필요합니다.

* 가상 네트워크 게이트웨이
* 가상 네트워크 게이트웨이 연결

다음 다이어그램에서는 데이터 센터 간 네트워크 통신 방법을 보여 줍니다.

   ![V P N 게이트웨이를 사용하여 통신하는 서로 다른 Azure 지역에 있는 두 개의 가상 네트워크를 보여 주는 다이어그램입니다.](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>이 아키텍처에서는 Azure 지역 간에 복제되는 데이터에 대해 아웃바운드 데이터 요금이 부과됩니다. [대역폭 가격 책정](https://azure.microsoft.com/pricing/details/bandwidth/)을 참조하세요.  

## <a name="create-remote-replica"></a>원격 복제본 만들기

원격 데이터 센터에서 복제본을 만들려면 다음 단계를 수행합니다.

1. [새 지역에서 가상 네트워크를 만듭니다](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Azure Portal에서 VNet-VNet 연결을 구성합니다](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >경우에 따라 PowerShell을 사용하여 VNet 간 연결을 만들어야 할 수 있습니다. 예를 들어 다른 Azure 계정을 사용하는 경우 포털에서 연결을 구성할 수 없습니다. 이 경우 [Azure Portal에서 VNet-VNet 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 참조하세요.

1. [새 영역에서 도메인 컨트롤러를 만듭니다](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100).

   이 도메인 컨트롤러는 주 사이트의 도메인 컨트롤러를 사용할 수 없는 경우 인증을 제공합니다.

1. [새 지역에서 SQL Server 가상 머신을 만듭니다](create-sql-vm-portal.md).

1. [새 영역의 네트워크에는 Azure Load Balancer를 만듭니다](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

   이 부하 분산 장치는 다음 조건을 충족해야 합니다.

   - 새 가상 머신과 동일한 네트워크 및 서브넷에 있어야 합니다.
   - 가용성 그룹 수신기의 고정 IP 주소를 갖습니다.
   - 부하 분산 장치가 있는 동일한 지역의 가상 머신으로만 구성된 백 엔드 풀을 포함합니다.
   - IP 주소 고유의 TCP 포트 프로브를 사용합니다.
   - 동일한 지역의 SQL Server와 관련된 부하 분산 규칙이 있습니다.  
   - 백 엔드 풀의 가상 머신이 단일 가용성 집합 또는 가상 머신 확장 집합에 포함되지 않는 경우 표준 Load Balancer여야 합니다. 자세한 내용은 [Azure Load Balancer 표준 개요](../../../load-balancer/load-balancer-overview.md)를 참조하세요.
   - 서로 다른 두 지역의 두 개의 가상 네트워크를 글로벌 VNet 피어링을 통해 피어링하는 경우, 표준 Load Balancer이어야 합니다. 자세한 내용은 [Azure Virtual Network FAQ(질문과 대답)](../../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)를 참조하십시오.

1. [장애 조치(Failover) 클러스터링 기능을 새 SQL Server에 추가합니다](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [새 SQL Server를 도메인에 연결합니다](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

1. [도메인 계정을 사용하도록 새 SQL Server 서비스 계정을 설정합니다](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount).

1. [새 SQL Server를 Windows Server 장애 조치 클러스터에 추가합니다](availability-group-manually-configure-tutorial.md#addNode).

1. 클러스터에 IP 주소 리소스를 추가합니다.

   장애 조치 클러스터 관리자에서 IP 주소 리소스를 만들 수 있습니다. 클러스터의 이름을 선택한 다음 **클러스터 코어 리소스** 에서 클러스터 이름을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 

   ![클러스터 이름, "서버 이름" 및 "속성"이 선택된 "장애 조치(Failover) 클러스터 관리자"를 보여 주는 스크린샷](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   **속성** 대화 상자에서 **IP 주소** 아래의 **추가** 를 선택한 다음 원격 네트워크 지역의 클러스터 이름의 IP 주소를 추가합니다. **IP 주소** 대화 상자에서 **확인** 을 선택한 다음 **클러스터 속성** 대화 상자에서 다시 **확인** 을 선택하여 새 IP 주소를 저장합니다. 

   ![클러스터 IP 추가](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. 코어 클러스터 이름에 대한 종속성으로 IP 주소를 추가합니다.

   클러스터 속성을 다시 열고 **종속성** 탭을 선택합니다. 두 개의 IP 주소에 대한 OR 종속성을 구성합니다. 

   ![클러스터 속성](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. 클러스터의 가용성 그룹 역할에 IP 주소 리소스를 추가합니다. 

   장애 조치(failover) 클러스터 관리자에서 가용성 그룹 역할을 마우스 오른쪽 단추로 클릭하고 **리소스 추가**, **기타 리소스** 를 선택한 다음 **IP 주소** 를 선택합니다.

   ![IP 주소 만들기](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   이 IP 주소를 다음과 같이 구성합니다.

   - 원격 데이터 센터의 네트워크를 사용합니다.
   - 새 Azure Load Balancer에서 IP 주소를 할당합니다. 

1. 수신기 클라이언트 액세스 지점(네트워크 이름) 클러스터에 대한 종속성으로 IP 주소 리소스를 추가합니다.

   다음 스크린 샷에는 올바르게 구성된 IP 주소 클러스터 리소스가 표시됩니다.

   ![가용성 그룹](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >클러스터 리소스 그룹에는 두 IP 주소가 포함됩니다. 두 IP 주소는 수신기 클라이언트 액세스 지점에 대한 종속성입니다. 클러스터 종속성 구성에 **OR** 연산자를 사용합니다.

1. [PowerShell에서 클러스터 매개 변수를 설정합니다](availability-group-manually-configure-tutorial.md#setparam).

   새 지역의 부하 분산 장치에 대해 구성한 클러스터 네트워크 이름, IP 주소 및 프로브 포트를 사용하여 PowerShell 스크립트를 실행합니다.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. 새 SQL Server의 SQL Server 구성 관리자에서 [Always On 가용성 그룹을 사용하도록 설정합니다](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. SQL Server Management Studio 새 SQL Server 시스템 계정 권한을 [구성합니다.](availability-group-manually-configure-prerequisites-tutorial.md#configure-system-account-permissions)

1. [새 SQL Server에서 방화벽 포트를 엽니다](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

   열어야 하는 포트 번호는 사용자의 환경에 따라 달라집니다. 미러링 엔드포인트 및 Azure Load Balancer 상태 프로브에 대한 포트를 엽니다.


1. [새 SQL Server에서 가용성 그룹에 복제본을 추가합니다](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   원격 Azure 지역에 있는 복제본의 경우 수동 장애 조치를 사용한 비동기 복제에 대해 설정합니다.  
   

## <a name="set-connection-for-multiple-subnets"></a>여러 서브넷에 대한 연결 설정

원격 데이터 센터의 복제본은 가용성 그룹의 일부이지만 다른 서브넷에 있습니다. 이 복제본이 주 복제본이 되면 애플리케이션 연결 시간 초과가 발생할 수 있습니다. 이 동작은 다중 서브넷 배포의 온-프레미스 가용성 그룹과 동일합니다. 클라이언트 애플리케이션에서의 연결을 허용하려면 클라이언트 연결을 업데이트하거나 클러스터 네트워크 이름 리소스에 대해 이름 확인 캐시를 구성합니다.

가급적 설정할 클러스터 구성과 를 `RegisterAllProvidersIP=1` 설정할 클라이언트 연결 문자열을 업데이트합니다. `MultiSubnetFailover=Yes` [MultiSubnetFailover로 연결](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0)을 참조하세요.

연결 문자열을 수정할 수 없는 경우 이름 확인 캐시를 구성할 수 있습니다. [시간 제한 오류 및 다중 서브넷 환경에서 SQL Server 2012 AlwaysOn 가용성 그룹 수신기에 연결할 수 없음](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av)을 참조하세요.

## <a name="fail-over-to-remote-region"></a>원격 지역으로 장애 조치

원격 지역에 대한 수신기 연결을 테스트하려면 복제본을 원격 지역으로 장애 조치할 수 있습니다. 복제본이 비동기인 경우 장애 조치 시 잠재적 데이터 손실이 발생하기 쉽습니다. 데이터 손실 없이 장애 조치를 수행하려면 가용성 모드를 동기로 변경하고 장애 조치 모드를 자동으로 설정합니다. 다음 단계를 사용합니다.

1. **개체 탐색기** 에서 주 복제본을 호스트하는 SQL Server의 인스턴스에 연결합니다.
1. **Always On 가용성 그룹**, **가용성 그룹** 아래에서 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
1. **일반** 페이지의 **가용성 복제본** 에서 DR 사이트의 보조 복제본을 **동기 커밋** 가용성 모드 및 **자동** 장애 조치 모드를 사용하도록 설정합니다.
1. 보조 복제본의 고가용성을 위해 주 복제본과 같은 사이트에 있는 경우 이 복제본을 **비동기 커밋** 및 **수동** 으로 설정합니다.
1. 확인을 선택합니다.
1. **개체 탐색기** 에서 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **대시보드 표시** 를 선택합니다.
1. 대시보드에서 DR 사이트의 복제본이 동기화되었는지 확인합니다.
1. **개체 탐색기** 에서 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **장애 조치...** 를 선택합니다. SQL Server Management Studio는 SQL Server를 장애 조치하는 마법사를 엽니다.  
1. **다음** 을 클릭하고 DR 사이트의 SQL Server 인스턴스를 선택합니다. **다음** 을 다시 선택합니다.
1. DR 사이트에서 SQL Server 인스턴스에 연결하고 **다음** 을 선택합니다.
1. **요약** 페이지에서 설정을 확인한 다음 **마침** 을 선택합니다.

연결을 테스트한 후에 주 복제본은 기본 데이터 센터로 다시 이동되고 가용성 모드는 일반 작동 설정으로 다시 지정됩니다. 다음 표에는 이 문서에서 설명하는 아키텍처에 대한 일반 작업 설정이 나와 있습니다.

| 위치 | 서버 인스턴스 | 역할 | 가용성 모드 | 장애 조치(Failover) 모드
| ----- | ----- | ----- | ----- | -----
| 주 데이터 센터 | SQL-1 | 주 | 동기 | 자동
| 주 데이터 센터 | SQL-2 | 보조 | 동기 | 자동
| 보조 또는 원격 데이터 센터 | SQL-3 | 보조 | 비동기 | 설명서


### <a name="more-information-about-planned-and-forced-manual-failover"></a>계획된 및 강제 수동 장애 조치에 대한 자세한 내용

자세한 내용은 아래 항목을 참조하세요.

- [가용성 그룹의 계획된 수동 장애 조치(Failover) 수행(SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [가용성 그룹의 강제 수동 장애 조치 수행(SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)
