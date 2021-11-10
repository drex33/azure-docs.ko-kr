---
title: FCI에 대한 가상 머신 준비
description: Azure 가상 머신에서 FCI(장애 조치(failover) 클러스터 인스턴스) 및 SQL Server와 함께 사용하도록 Azure 가상 머신을 준비합니다.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: ac7b5617cf81b2845701360cf1d7af3fbc4858c0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156166"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>FCI용 가상 머신 준비(Azure VM의 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 SQL Server FCI(장애 조치(failover) 클러스터 인스턴스)와 함께 사용하도록 Azure VM(가상 머신)을 준비하는 방법을 설명합니다. 구성 설정은 FCI 스토리지 솔루션에 따라 다르므로 환경과 비즈니스에 적합한 올바른 구성을 선택하고 있는지 확인합니다. 

자세한 내용은 [Azure VM에서 SQL Server를 사용하는 FCI](failover-cluster-instance-overview.md) 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)의 개요를 참조하세요. 

> [!NOTE]
> Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 장애 조치(failover) 클러스터 인스턴스 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [장애 조치(failover) 클러스터 인스턴스 마이그레이션](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항 

- Microsoft Azure 구독. [무료](https://azure.microsoft.com/free/)로 시작할 수 있습니다. 
- azure 가상 컴퓨터의 Windows 도메인 또는 가상 네트워크 페어링을 사용 하 여 azure로 확장 된 온-프레미스 active directory
- Azure 가상 머신과 Active Directory에서 개체를 만들 수 있는 권한이 있는 계정
- Azure 가상 네트워크와 이러한 구성 요소에 대해 충분 한 IP 주소 공간이 있는 하나 이상의 서브넷:
   - 두 가상 머신
   - Windows 장애 조치 (failover) 클러스터에 대 한 IP 주소
   - 각 FCI에 대한 IP 주소
- 도메인 컨트롤러를 가리키는 Azure 네트워크에 구성된 DNS



## <a name="choose-an-fci-storage-option"></a>FCI 스토리지 옵션 선택

가상 머신의 구성 설정은 SQL Server 장애 조치(failover) 클러스터 인스턴스에 사용할 스토리지 옵션에 따라 다릅니다. 가상 머신을 준비하기 전에 [사용 가능한 FCI 스토리지 옵션](failover-cluster-instance-overview.md#storage)을 검토하고 환경 및 비즈니스 요구에 가장 적합한 옵션을 선택하세요. 그런 다음 스토리지 선택에 따라 이 문서 전체에서 적절한 VM 구성 옵션을 신중하게 선택합니다. 

## <a name="choose-vm-availability"></a>VM 가용성 선택 

장애 조치(failover) 클러스터 기능을 사용하려면 가상 머신을 [가용성 집합](../../../virtual-machines/linux/tutorial-availability-sets.md) 또는 [가용성 영역](../../../availability-zones/az-overview.md#availability-zones)에 배치해야 합니다.

원하는 클러스터 구성과 일치하는 VM 가용성 옵션을 신중하게 선택합니다. 

- **Azure 공유 디스크**: 가용성 옵션은 프리미엄 SSD 또는 UltraDisk를 사용 하는 경우에 따라 다릅니다.
   - **프리미엄 SSD 영역 중복 Storage (ZRS)**: 다른 영역의 [가용성 영역](../../../availability-zones/az-overview.md#availability-zones) . [프리미엄 SSD ZRS](../../../virtual-machines/disks-redundancy.md#zone-redundant-storage-for-managed-disks) 는 선택 된 지역에서 3 개의 azure 가용성 영역을 통해 azure 관리 디스크를 동기적으로 복제 합니다. 장애 조치 (failover) 클러스터의 vm은 서로 다른 가용성 영역에 배치 될 수 있으므로 vm 가용성 SLA 99.99%를 제공 하는 영역 중복 SQL Server fci를 달성할 수 있습니다. 데이터의 교차 영역 복사본 때문에 ZRS에 대 한 디스크 대기 시간이 더 높습니다.
   - **LRS (프리미엄 SSD 로컬 중복 Storage)**: [프리미엄 SSD LRS](../../../virtual-machines/disks-redundancy.md#locally-redundant-storage-for-managed-disks)에 대해 서로 다른 장애/업데이트 도메인에 [가용성 집합을 설정](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 합니다. [근접 배치 그룹](../../../virtual-machines/windows/proximity-placement-groups-portal.md) 내에 vm을 배치 하 여 서로 가까이 배치 하도록 선택할 수도 있습니다. 데이터를 하나의 데이터 센터 내에서 로컬로 복제 하 고 99.95%의 VM 가용성 SLA를 제공 하므로 가용성 집합 및 근접 배치 그룹을 결합 하면 공유 디스크에 대해 가장 낮은 대기 시간을 제공 합니다.
   - **Ultra Disk LRS (로컬 중복 Storage)**: [가용성 영역](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) 이지만 vm은 동일한 가용성 영역에 배치 해야 합니다. [Ultra disks](../../../virtual-machines/disks-enable-ultra-ssd.md) 는 가장 낮은 디스크 대기 시간을 제공 하며 IO 집약적 작업에 가장 적합 합니다. FCI의 모든 Vm은 동일한 가용성 영역에 있으므로 VM 가용성은 99.9%입니다. 
- **Premium 파일 공유**: [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 또는 [가용성 영역](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **저장소 공간 Direct**: [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)입니다.

> [!IMPORTANT]
> 가상 머신을 만든 후에는 가용성 집합을 설정하거나 변경할 수 없습니다.

## <a name="subnets"></a>서브넷 

Azure vm에 대 한 SQL Server 단일 서브넷 또는 여러 서브넷에 SQL Server vm을 배포 하는 옵션이 있습니다. 

Vm을 여러 서브넷에 배포 하면 IP 주소에 대 한 클러스터 또는 종속성이 활용 되 고 장애 조치 (failover) 클러스터 인스턴스에 연결할 때 온-프레미스 환경과 일치 하 게 됩니다. 다중 서브넷 접근 방식은 보다 간단한 관리 효율성을 위해 Azure vm에서 SQL Server 하는 데 권장 되며 장애 조치 (failover) 시간을 단축 합니다. 

단일 서브넷에 vm을 배포 하려면 DNN (분산 네트워크 이름) Azure Load Balancer에 대 한 추가 종속성이 있어야 fci로 트래픽을 라우팅합니다. 

SQL Server vm을 여러 서브넷에 배포 하는 경우이 섹션의 단계에 따라 추가 서브넷을 사용 하 여 가상 네트워크를 만든 다음 SQL Server vm을 만든 후 해당 서브넷 내의 [보조 IP 주소](#assign-secondary-ip-addresses) 를 vm에 할당 합니다. SQL Server vm을 단일 서브넷에 배포 하는 경우 추가 네트워크 구성이 필요 하지 않습니다. 

# <a name="single-subnet"></a>[단일 서브넷](#tab/single-subnet)

가상 컴퓨터와 클러스터에 설치할 수 있는 모든 FCIs에 대해 충분 한 IP 주소를 가진 단일 서브넷에 두 가상 컴퓨터를 둘 다 포함 합니다. 이 접근 방식을 사용 하려면 Azure Load Balancer 또는 분산 네트워크 이름 (DNN)과 같이 fci에 대 한 연결을 라우팅하는 추가 구성 요소가 필요 합니다. 

SQL Server vm을 단일 서브넷에 배포 하도록 선택 하는 경우에는 [Azure Load Balancer과 DNN 연결 옵션 간의 차이점을 검토](hadr-windows-server-failover-cluster-overview.md#distributed-network-name-dnn) 하 고 fci에 대 한 나머지 환경을 준비 하기 전에 가장 적합 한 옵션을 결정 합니다.

SQL Server vm을 단일 서브넷에 배포 하는 경우 추가 네트워크 구성이 필요 하지 않습니다. 

# <a name="multi-subnet"></a>[다중 서브넷](#tab/multi-subnet)

연결을 SQL Server fci로 직접 라우팅하려면 가상 네트워크 내에서 별도의 서브넷에 두 가상 머신을 둘 수 있습니다. 장애 조치 (failover) 클러스터 인스턴스에 대 한 SQL Server VM에 보조 ip 주소를 할당 합니다. Windows Server 2016 아래에 있는 경우 Windows 서버 장애 조치 (failover) 클러스터에 대 한 추가 보조 ip 주소도 할당 합니다. Windows 서버 2019 이상에서는 클러스터 이름에 분산 네트워크 이름 (DNN)을 사용 하므로 클러스터의 보조 IP 주소가 필요 하지 않습니다. 

이 방법을 사용 하면 SQL Server fci에 연결할 때 Azure Load Balancer 또는 분산 네트워크 이름 (DNN)이 필요 하지 않습니다. 

SQL Server vm을 여러 서브넷에 배포 하도록 선택 하는 경우 먼저 두 개의 추가 서브넷을 사용 하 여 가상 네트워크를 만들고, SQL Server vm을 만든 후 [vm에 보조 IP 주소를 할당](#assign-secondary-ip-addresses)해야 합니다.  자세히 알아보려면 [가상 네트워크 개요](../../../virtual-network/virtual-networks-overview.md)를 참조 하세요. 이 섹션의 서브넷 이름 및 IP 주소는 예제로만 제공 되며 사용자 환경에서 다를 수 있습니다. Azure Portal에서 가상 네트워크를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에서 리소스 그룹으로 이동 하 고 **+ 만들기** 를 선택 합니다.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="리소스 그룹에서 새 리소스 만들기":::

1. **Marketplace** 검색 상자에서 **virtual network** 를 검색 하 고 Microsoft에서 **가상 네트워크** 타일을 선택 합니다. **가상 네트워크** 페이지에서 **만들기** 를 선택 합니다.  
1. **가상 네트워크 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력 합니다. 
   1. **Project 세부 정보** 에서 적절 한 Azure **구독** 을 선택 하 고, SQL Server vm을 배포할 **리소스 그룹** 을 선택 합니다. 
   1. **인스턴스 세부 정보** 에서 가상 네트워크의 이름을 입력 하 고 드롭다운에서 리소스 그룹과 동일한 지역을 선택 합니다.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="이전에 만든 리소스 그룹을 선택한 다음 가상 네트워크의 이름을 입력 합니다.":::

1. **IP 주소** 탭에서 **+ 서브넷 추가** 를 선택 하 여 첫 SQL Server VM에 대 한 추가 서브넷을 추가 하 고 다음 값을 입력 합니다. 
   1. **서브넷 이름의** 값을 입력 합니다 (예: **SQL-서브넷-1**). 
   1. 가상 네트워크 주소 공간 내에서 고유한 서브넷 주소 범위를 제공 합니다. 예를 들어 DC 서브넷 주소 범위의 세 번째 옥텟을 1로 반복할 수 있습니다. 
      - 예를 들어 **기본** 범위가 *10.38.0.0/24* 인 경우 `10.38.1.0/24` **SQL-서브넷-1** 의 IP 주소 범위를 입력 합니다. 
      - 마찬가지로 **기본** IP 범위가 *10.5.0.0/24* 이면 `10.5.1.0/24` 새 서브넷에 대해를 입력 합니다. 
   1. **추가** 를 선택 하 여 새 서브넷을 추가 합니다. 

     :::image type="content" source="./media/failover-cluster-instance-prepare-vm/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="첫 번째 서브넷 (예: sql-서브넷-1)의 이름을 지정 하 고 세 번째 8 진수를 1로 반복 하 여 DC 서브넷 IP 주소가 10.5.0.0 인 경우 새 서브넷을 10.5.1.0 해야 합니다.":::

1. 이전 단계를 반복 하 여 두 번째 SQL Server VM에 대해 **SQL-서브넷-2** 와 같은 이름으로 다른 고유한 서브넷 범위를 추가 합니다. 세 번째 8 진수를 다시 반복할 수 있습니다. 
   - 예를 들어 **기본** IP 범위가 *10.38.0.0/24* 이 고 **SQL-서브넷-1** 이 *10.38.1.0/24* 이면 `10.38.2.0/24` 새 서브넷에 대해를 입력 합니다.
   - 마찬가지로 **기본** IP 범위가 *10.5.0.0/24* 이 고 **SQL-서브넷-1** 이 *10.5.1.0/24* 인 경우 `10.5.2.0/24` 에는 **SQL-서브넷-2** 에 대 한 IP 주소 범위를 입력 합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="두 번째 서브넷 (예: sql-서브넷-2)의 이름을 지정 하 고 세 번째 8 진수를 2로 반복 하 여 DC 서브넷 IP 주소가 10.38.0.0/24 인 경우 새 서브넷은 10.38.2.0/24 여야 합니다.":::

1. 두 번째 서브넷을 추가한 후에는 서브넷 이름 및 범위를 검토 합니다. IP 주소 범위는 이미지와 다를 수 있습니다. 모든 항목이 올바르면 **검토 + 만들기**, **만들기** 를 차례로 선택 하 여 새 가상 네트워크를 만듭니다. 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/07-create-vnet-ip-address.png" alt-text="두 번째 서브넷을 추가한 후에는 이미지 예제와 같이 서브넷 이름과 범위를 검토 합니다 (IP 주소는 다를 수 있음). 모든 항목이 올바르면 검토 + 만들기, 만들기를 차례로 선택 하 여 새 가상 네트워크를 만듭니다.":::

   포털 대시보드가 반환되고 새 네트워크가 만들어지는 시점을 사용자에게 알립니다.

---

## <a name="configure-dns"></a>DNS 구성

DNS 서버를 사용 하도록 가상 네트워크를 구성 합니다. 먼저 DNS IP 주소를 확인 하 고 가상 네트워크에 추가 합니다. 

### <a name="identify-dns-ip-address"></a>DNS IP 주소 확인

DNS 서버의 IP 주소를 확인 한 다음 가상 네트워크 구성에 추가 합니다. 이 섹션에서는 DNS 서버가 Azure의 가상 컴퓨터에 있는 경우 DNS IP 주소를 식별 하는 방법을 보여 줍니다. 

Azure Portal에서 DNS 서버 VM의 IP 주소를 확인 하려면 다음 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com) 에서 리소스 그룹으로 이동 하 고 DNS 서버 VM을 선택 합니다. 
1. VM 페이지의 **설정** 창에서 **네트워킹** 을 선택 합니다. 
1. DNS 서버의 IP 주소는 **NIC 개인 ip** 주소를 적어둡니다. 예제 이미지에서 개인 IP 주소는 **10.38.0.4** 입니다. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="DC-VM-1 페이지의 설정 창에서 네트워킹을 선택 하 고 NIC 개인 IP 주소를 확인 합니다. 이 IP 주소를 DNS 서버로 사용 합니다.":::

### <a name="configure-virtual-network-dns"></a>가상 네트워크 DNS 구성

이 DNS 서버 IP 주소를 사용 하도록 가상 네트워크를 구성 합니다. 

DNS에 대 한 가상 네트워크를 구성 하려면 다음 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com)리소스 그룹으로 이동하여 가상 네트워크를 선택합니다. 
1. **설정** 창에서 **DNS 서버를** 선택한 **다음, 사용자 지정** 을 선택합니다. 
1. IP 주소 필드에 이전에 식별한  개인 IP 주소(예: `10.38.0.4` )를 입력하거나 내부 DNS 서버의 내부 IP 주소를 제공합니다. 
1. **저장** 을 선택합니다. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text="설정 창에서 DNS 서버를 선택한 다음, 사용자 지정을 선택합니다. IP 주소 필드에 이전에 식별한 개인 IP 주소(예: 10.38.0.4)를 입력합니다.":::

## <a name="create-the-virtual-machines"></a>가상 머신 만들기

VM 가상 네트워크를 구성하고 VM 가용성을 선택하면 가상 머신을 만들 준비가 된 것입니다. SQL Server가 이미 설치되어 있거나 설치되어 있지 않은 Azure Marketplace 이미지를 사용하도록 선택할 수 있습니다. 그러나 Azure VM에서 SQL Server용 이미지를 선택하는 경우 장애 조치(failover) 클러스터 인스턴스를 구성하기 전에 가상 머신에서 SQL Server를 제거해야 합니다. 

### <a name="nic-considerations"></a>NIC 고려 사항

Azure VM 게스트 장애 조치(failover) 클러스터에서는 서버당 단일 NIC(클러스터 노드)를 사용하는 것이 좋습니다. Azure 네트워킹에는 물리적 중복성이 있어 Azure IaaS VM 게스트 클러스터에서 추가 NIC를 불필요하게 만듭니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시해도 됩니다.

두 가상 머신을 다음에 배치합니다.

- 가용성 집합을 사용하는 경우 가용성 집합과 동일한 Azure 리소스 그룹에 있습니다.
- 도메인 컨트롤러 및 DNS 서버와 동일한 가상 네트워크 또는 도메인 컨트롤러에 적합한 연결이 있는 가상 네트워크에 있습니다.
- Azure 가용성 집합 또는 가용성 영역에 있습니다.

사전 설치된 SQL Server를 [포함하는 이미지](sql-vm-create-portal-quickstart.md) 또는 [포함하지 않는 이미지](../../../virtual-machines/windows/quick-create-portal.md)를 사용하여 Azure 가상 머신을 만들 수 있습니다. SQL Server 이미지를 선택하는 경우 장애 조치(failover) 클러스터 인스턴스를 설치하기 전에 SQL Server 인스턴스를 수동으로 제거해야 합니다.  

### <a name="assign-secondary-ip-addresses"></a>보조 IP 주소 할당

SQL Server VM을 단일 서브넷에 배포한 경우 이 단계를 건너뜁니다. FCI에 대한 연결 향상을 위해 여러 서브넷에 SQL Server VM을 배포한 경우 각 VM에 보조 IP 주소를 할당해야 합니다. 

장애 조치(failover) 클러스터 인스턴스 네트워크 이름에 사용할 각 SQL Server VM에 보조 IP 주소를 할당하고, Windows Server 2016 및 이전의 경우 클러스터 네트워크 이름에 대한 각 SQL Server VM에 보조 IP 주소를 할당합니다. 이렇게 하면 단일 서브넷 환경의 요구 사항처럼 Azure Load Balancer 대한 필요성이 무효화됩니다.  

Windows Server 2016 및 이전에서는 클러스터가 Windows Server 2019에 도입된 기본 DNN(분산 네트워크 이름) 대신 **클러스터 네트워크 이름을** 사용하므로 windows 클러스터 IP에 사용할 각 SQL Server VM에 추가 보조 IP 주소를 할당해야 합니다. DNN을 사용할 경우 CNO(클러스터 이름 개체)가 클러스터의 모든 노드에 대한 IP 주소에 자동으로 등록되어 전용 Windows 클러스터 IP 주소가 필요하지 않습니다.

Windows Server 2016 이전의 경우 이 섹션의 단계에 따라 FCI 네트워크 이름과 클러스터 *모두에* 대해 각 SQL Server VM에 보조 IP  주소를 할당합니다. 

Windows Server 2019 이상에 있는 경우 VNN(가상 네트워크 이름)으로 클러스터를 구성하려는 경우가 아니면 FCI 네트워크 이름에 대한 보조 IP 주소만 할당하고 Windows 클러스터 IP를 할당하는 단계를 건너뜁니다. 이 경우 Windows Server 2016 것처럼 각 SQL Server VM에 두 IP 주소를 모두 할당합니다. 

VM에 추가 보조 IP를 할당하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com/) 리소스 그룹으로 이동하여 첫 번째 SQL Server VM을 선택합니다. 
1. **설정** 창에서 **네트워킹을** 선택한 다음, 네트워크 **인터페이스** 를 선택합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="설정 창에서 네트워킹을 선택한 다음, 네트워크 인터페이스를 선택합니다.":::

1. 네트워크 **인터페이스** 페이지의 **설정** 창에서 **IP 구성을** 선택한 **다음, + 추가를** 선택하여 추가 IP 주소를 추가합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="IP 구성":::

1. IP **구성 추가** 페이지에서 다음을 수행합니다. 
   1. Windows  2016 및 이전 Windows 클러스터 IP 주소의 이름(예: **windows-cluster-ip)을** 지정합니다. Windows Server 2019 이상인 경우 이 단계를 건너뜁니다. 
   1. **할당을** **정적** 로 설정합니다.
   1. 과 같은 SQL Server VM과 동일한 서브넷(**SQL-subnet-1)에** 사용되지 않는 **IP 주소를** `10.38.1.10` 입력합니다. 
   1. 공용 **IP 주소는** 기본값인 **분리로 둡니다.** 
   1. **확인을** 선택하여 IP 구성 추가를 완료합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="첫 번째 SQL Server VM의 서브넷에 사용된 IP 주소를 입력하여 클러스터 IP 추가":::

1. **+ 추가를** 다시 선택하여 FCI 네트워크 이름에 대한 추가 IP 주소(예: **FCI-network-name)를** 구성하고 와 같은 **SQL-subnet-1에서** 사용되지 않는 IP 주소를 다시 지정합니다. `10.38.1.11` 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/22-add-fci-ip-address.png" alt-text="+ 추가를 다시 선택하여 가용성 그룹 수신기에 대한 추가 IP 주소(예: availability-group-listener)를 구성하고 SQL-subnet-1에서 사용하지 않는 IP 주소(예: 10.31.1.11)를 사용하여 다시 구성합니다.":::

1. 두 번째 SQL Server VM에 대해 이 단계를 다시 반복합니다. **SQL-subnet-2** 내에 사용되지 않는 두 개의 보조 IP 주소를 할당합니다. 다음 표의 값을 사용하여 IP 구성을 추가합니다(IP 주소는 예제일 뿐이지만 사용자의 경우 다를 수 있음). 

   
    | **필드** | 입력 | 입력 | 
    | --- | --- | --- |
    | **이름** |windows-cluster-ip | FCI-network-name |
    | **Allocation** | 정적 | 정적 |
    | **IP 주소** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |



## <a name="uninstall-sql-server"></a>SQL Server 제거

FCI 생성 프로세스의 일부로 SQL Server를 장애 조치(failover) 클러스터에 클러스터된 인스턴스로 설치합니다. *SQL Server 없이 Azure Marketplace 이미지가 있는 가상 머신을 배포한 경우 이 단계를 건너뛸 수 있습니다.* SQL Server가 사전 설치된 이미지를 배포한 경우 SQL IaaS 에이전트 확장에서 SQL Server VM을 등록 취소한 다음 SQL Server를 제거해야 합니다. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>SQL IaaS 에이전트 확장에서 등록 취소

Azure Marketplace의 SQL Server VM 이미지는 SQL IaaS 에이전트 확장에 자동으로 등록됩니다. 사전 설치된 SQL Server 인스턴스를 제거하기 전에 먼저 [SQL IaaS 에이전트 확장에서 각 SQL Server VM을 등록 취소](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)해야 합니다. 

### <a name="uninstall-sql-server"></a>SQL Server 제거

확장에서 등록을 취소한 후 SQL Server를 제거할 수 있습니다. 각 가상 머신에서 다음 단계를 따르세요. 

1. RDP를 사용하여 가상 머신에 연결 RDP를 사용하여 가상 머신에 처음 연결하는 경우 네트워크에서 PC를 검색할 수 있도록 허용할지 묻는 메시지가 표시됩니다. **예** 를 선택합니다.
1. 제어판 **프로그램 및 기능을** **엽니다.** 
1. **프로그램 및 기능** 에서 **Microsoft SQL Server 201_(64비트)** 을 마우스 오른쪽 단추로 클릭하고 **제거/변경** 을 선택합니다.
1. **제거** 를 선택합니다.
1. 기본 인스턴스를 선택합니다.
1. **데이터베이스 엔진 Services, Analysis Services** 및 **Reporting Services** **- 네이티브** 에서 모든 기능을 제거합니다. **SharedFeatures** 에서 아무것도 제거하지 마세요. 다음 스크린샷과 같은 내용이 ![ 표시됩니다. 기능 선택](./media/failover-cluster-instance-prepare-vm/remove-features-updated.png)
1. **다음** 을 선택한 다음, **제거** 를 선택합니다.
1. 인스턴스가 성공적으로 제거되면 가상 머신을 다시 시작합니다. 

## <a name="open-the-firewall"></a>방화벽 열기 

각 가상 머신에서 SQL Server가 사용하는 Windows 방화벽 TCP 포트를 엽니다. 기본적으로 SQL Server 포트 1433을 사용하지만 사용자 환경에서 이를 변경한 경우 사용할 SQL Server 인스턴스를 구성한 포트를 엽니다. 포트 1433은 Azure Marketplace 배포된 SQL Server 이미지에서 자동으로 열립니다. 

단일 서브넷 시나리오에 [부하 분산](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 장치 를 사용하는 경우 상태 프로브에서 사용하는 포트도 열어야 합니다. 기본적으로 상태 프로브는 포트 59999를 사용하지만 부하 분산 장치를 만들 때 지정하는 모든 TCP 포트일 수 있습니다. 

다음 표에서는 FCI 구성에 따라 열어야 할 수 있는 포트에 대해 자세히 설명합니다. 

   | 목적 | 포트 | 메모
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | SQL Server의 기본 인스턴스에 대한 표준 포트입니다. 갤러리에서 이미지를 사용한 경우 이 포트는 자동으로 열립니다. </br> </br> **사용 대상**: 모든 FCI 구성. |
   | 상태 프로브 | TCP 59999 | 모든 공개 TCP 포트입니다. 이 포트를 사용하려면 부하 분산 장치 [상태 프로브](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) 및 클러스터를 구성합니다. </br> </br> **사용**: 단일 서브넷 시나리오에서 부하 분산 장치를 사용 하는 fci |
   | 파일 공유 | UDP 445 | 파일 공유 서비스에서 사용하는 포트입니다. </br> </br> **사용 대상**: 프리미엄 파일 공유를 사용하는 FCI. |

## <a name="join-the-domain"></a>도메인 가입

또한 가상 머신을 도메인에 가입시켜야 합니다. [빠른 시작 템플릿](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)을 사용하면 됩니다. 

## <a name="review-storage-configuration"></a>스토리지 구성 검토

Azure Marketplace에서 만든 가상 머신은 연결된 스토리지와 함께 제공됩니다. 프리미엄 파일 공유 또는 Azure 공유 디스크를 사용하여 FCI 스토리지를 구성하려는 경우 로컬 스토리지가 장애 조치(failover) 클러스터 인스턴스에 사용되지 않으므로 연결된 스토리지를 제거하여 비용을 절감할 수 있습니다. 그러나 스토리지 공간 다이렉트 FCI 솔루션에 연결된 스토리지를 사용할 수 있으므로 이 경우 제거하는 것은 도움이 되지 않을 수 있습니다. FCI 스토리지 솔루션을 검토하여 연결된 스토리지 제거가 비용 절감에 가장 적합한 것인지 확인합니다. 


## <a name="next-steps"></a>다음 단계

이제 가상 머신 환경을 준비했으므로 장애 조치(failover) 클러스터 인스턴스를 구성할 준비가 되었습니다. 

다음 가이드 중 하나를 선택하여 비즈니스에 적합한 FCI 환경을 구성합니다. 
- [Azure 공유 디스크로 FCI 구성](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [프리미엄 파일 공유로 FCI 구성](failover-cluster-instance-premium-file-share-manually-configure.md)
- [스토리지 공간 다이렉트로 FCI 구성](failover-cluster-instance-storage-spaces-direct-manually-configure.md)


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)
