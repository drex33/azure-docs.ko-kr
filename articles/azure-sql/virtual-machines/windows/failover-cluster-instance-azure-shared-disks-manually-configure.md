---
title: Azure 공유 디스크를 사용하여 FCI 만들기
description: Azure 공유 디스크를 사용하여 Azure Virtual Machines에서 SQL Server를 사용한 FCI(장애 조치(failover) 클러스터 인스턴스)를 만듭니다.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na, devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: a685dd96ea323d6c7374c719bf20d1735839dad3
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133270874"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Azure 공유 디스크(Azure VM의 SQL Server)를 사용하여 FCI 만들기
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내에서 [여러 서브넷](failover-cluster-instance-prepare-vm.md#subnets) 에 SQL Server vm을 만들어 장애 조치 (failover) 클러스터 인스턴스에 대 한 Azure Load Balancer 또는 DNN (분산 네트워크 이름)의 필요성을 제거 합니다.

이 문서에서는 Azure VM(Virtual Machines)에서 SQL Server를 사용한 Azure 공유 디스크를 사용하여 FCI(장애 조치(failover) 클러스터 인스턴스)를 만드는 방법을 설명합니다. 

자세히 알아보려면 [Azure VMs에서 SQL Server를 사용한 FCI](failover-cluster-instance-overview.md) 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)의 개요를 참조하세요. 

> [!NOTE]
> Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 장애 조치(failover) 클러스터 인스턴스 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [장애 조치(failover) 클러스터 인스턴스 마이그레이션](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항 

이 문서의 지침을 완료하려면 먼저 다음이 있어야 합니다.

- Azure 구독 [무료](https://azure.microsoft.com/free/)로 시작할 수 있습니다. 
- 가용성 집합 또는 가용성 영역에서 두 개 이상의 [준비 된 Azure virtual machines를 Windows](failover-cluster-instance-prepare-vm.md) 합니다. 
- Azure 가상 머신과 Active Directory 모두에서 개체를 만들 수 있는 권한이 있는 계정
- 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps) 

## <a name="add-azure-shared-disk"></a>Azure 공유 디스크 추가

[공유 디스크 기능이 사용 하도록 설정 된 관리 되는 프리미엄 SSD 디스크를 배포](../../../virtual-machines/disks-shared-enable.md#deploy-a-premium-ssd-as-a-shared-disk)합니다. 모든 FCI 노드에서 디스크를 공유할 수 있도록 **클러스터 노드 수와 맞추려면** `maxShares`를 설정합니다. 

## <a name="attach-shared-disk-to-vms"></a>공유 디스크를 Vm에 연결

MaxShares가 1 > 공유 디스크를 배포한 후 클러스터에 노드로 참여할 Vm에 디스크를 탑재할 수 있습니다. 

공유 디스크를 SQL Server vm에 연결 하려면 다음 단계를 수행 합니다. 

1. 공유 디스크를 연결할 Azure Portal에서 VM을 선택 합니다. 
1. **설정** 창에서 **디스크** 를 선택 합니다. 
1. **기존 디스크 연결** 을 선택 하 여 공유 디스크를 VM에 연결 합니다. 
1. **디스크 이름** 드롭다운에서 공유 디스크를 선택 합니다. 
1. **저장** 을 선택합니다.
1. VM SQL Server 모든 클러스터 노드에 대해 이러한 단계를 반복 합니다. 

잠시 후 공유 데이터 디스크가 VM에 연결 되 고 해당 VM의 데이터 디스크 목록에 나타납니다.

## <a name="initialize-shared-disk"></a>공유 디스크 초기화

공유 디스크가 모든 Vm에 연결 되 면 클러스터의 노드로 참여 하는 Vm의 디스크를 초기화할 수 있습니다. **모든** vm에서 디스크를 초기화 합니다. 


SQL Server VM에 대 한 디스크를 초기화 하려면 다음 단계를 수행 합니다. 
 
1. Vm 중 하나에 커넥트 합니다.
2. VM 내에서 **시작** 메뉴를 열고 검색 상자에 **diskmgmt.msc** 를 입력 하 여 **디스크 관리** 콘솔을 엽니다.
3. 디스크 관리에서 초기화되지 않은 새 디스크가 있다고 인식하고 **디스크 초기화** 창이 나타납니다.
4. 새 디스크가 선택되었는지 확인한 후 **확인** 을 선택하여 초기화합니다.
5. 새 디스크가 **할당되지 않음** 으로 나타납니다. 디스크의 아무 곳이나 마우스 오른쪽 단추로 클릭하고 **새 단순 볼륨** 을 선택합니다. **새 단순 볼륨 마법사** 창이 열립니다.
6. 모든 기본값을 유지하여 마법사를 계속 진행하고 완료하면 **마침** 을 선택합니다.
7. **디스크 관리** 를 닫습니다.
8. 사용하기 전에 새 디스크를 포맷해야 함을 알리는 팝업 창이 나타납니다. **디스크 포맷** 을 선택합니다.
9. **새 디스크 포맷** 창에서 설정을 확인한 후 **시작** 을 선택합니다.
10. 디스크를 포맷하면 모든 데이터가 지워짐을 알리는 경고가 나타납니다. **확인** 을 선택합니다.
11. 포맷이 완료되면 **확인** 을 선택합니다.
12. fci에 참여 하는 각 SQL Server VM에서 이러한 단계를 반복 합니다. 

## <a name="create-windows-failover-cluster"></a>Windows 장애 조치 (Failover) 클러스터 만들기

Windows 서버 장애 조치 (Failover) 클러스터를 만드는 단계는 SQL Server vm을 단일 서브넷 또는 다중 서브넷에 배포 했는지 여부에 따라 달라 집니다. 클러스터를 만들려면 [다중 서브넷 시나리오](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) 또는 [단일 서브넷 시나리오](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster)에 대 한 자습서의 단계를 따르세요. 이러한 자습서는 가용성 그룹을 만들기 위한 것 이지만 클러스터를 만드는 단계는 동일 합니다. 

## <a name="configure-quorum"></a>쿼럼 구성

디스크 감시는 가장 복원력 있는 쿼럼 옵션이고 FCI 솔루션은 Azure 공유 디스크를 사용하므로 디스크 감시를 쿼럼 솔루션으로 구성하는 것이 좋습니다. 

클러스터에 짝수 투표가 있는 경우 비즈니스 요구에 가장 적합한 [쿼럼 솔루션](hadr-cluster-quorum-configure-how-to.md)을 구성합니다. 자세한 내용은 [SQL Server VM에 대한 쿼럼](hadr-windows-server-failover-cluster-overview.md#quorum)을 참조하세요. 

## <a name="validate-cluster"></a>클러스터 유효성 검사

장애 조치(Failover) 클러스터 관리자 UI 또는 PowerShell을 사용 하 여 가상 컴퓨터 중 하나에서 클러스터의 유효성을 검사 합니다. 

UI를 사용 하 여 클러스터의 유효성을 검사 하려면 다음 단계를 수행 합니다. 

1. **서버 관리자** 에서 **도구** 를 선택한 다음 **장애 조치(failover) 클러스터 관리자** 를 선택합니다.
1. **장애 조치(failover) 클러스터 관리자** 에서 **작업** 을 선택한 다음 **구성 유효성 검사** 를 선택합니다.
1. **다음** 을 선택합니다.
1. **서버 또는 클러스터 선택** 에서 두 가상 머신의 이름을 입력합니다.
1. **테스트 옵션** 에서 **선택한 테스트만 실행** 을 선택합니다. 
1. **다음** 을 선택합니다.
1. **테스트 선택** 에서 **Storage** *제외한* 모든 테스트를 선택 합니다.
1. **다음** 을 선택합니다.
1. **확인** 에서 **다음** 을 선택합니다.  **구성 유효성 검사** 마법사가 유효성 검사 테스트를 실행합니다.


PowerShell을 사용하여 클러스터의 유효성을 검사하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```

## <a name="test-cluster-failover"></a>클러스터 장애 조치(failover) 테스트

클러스터의 장애 조치를 테스트합니다. **장애 조치(Failover) 클러스터 관리자** 에서 클러스터를 마우스 오른쪽 단추로 클릭하고 **추가 작업** > **Move Core Cluster Resource**(코어 클러스터 리소스 이동) > **노드 선택** 을 선택한 다음, 클러스터의 다른 노드를 선택합니다. 코어 클러스터 리소스를 클러스터의 모든 노드로 이동한 다음 다시 기본 노드로 이동합니다. SQL Server를 설치 하기 전에 클러스터를 각 노드로 성공적으로 이동할 수 있는지 확인 합니다.

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="코어 리소스를 다른 노드로 이동하여 클러스터 장애 조치(failover) 테스트":::

## <a name="add-shared-disks-to-cluster"></a>클러스터에 공유 디스크 추가

장애 조치(Failover) 클러스터 관리자를 사용 하 여 연결 된 Azure 공유 디스크를 클러스터에 추가 합니다. 

클러스터에 디스크를 추가 하려면 다음 단계를 수행 합니다. 

1. **서버 관리자** 대시보드에서 **도구** 를 선택한 다음, **장애 조치(Failover) 클러스터 관리자** 를 선택합니다.
1. 클러스터를 선택 하 고 탐색 창에서 확장 합니다. 
1. **Storage** 선택 하 고 **디스크** 를 선택 합니다. 
1. **디스크** 를 마우스 오른쪽 단추로 클릭 하 고 **디스크 추가** 를 선택 합니다. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-add-disk.png" alt-text="디스크 추가":::

1. **클러스터에 디스크 추가** 창에서 Azure 공유 디스크를 선택 합니다.  **확인** 을 선택합니다.
 
    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-select-shared-disk.png" alt-text="디스크 선택":::

1. 공유 디스크가 클러스터에 추가 되 면 장애 조치(Failover) 클러스터 관리자에 표시 됩니다.

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-shared-disk.png" alt-text="클러스터 디스크":::



## <a name="create-sql-server-fci"></a>SQL Server FCI 만들기

장애 조치(failover) 클러스터 및 스토리지를 포함한 모든 클러스터 구성 요소를 구성한 후에는 SQL Server FCI를 만들 수 있습니다.

1. RDP(원격 데스크톱 프로토콜)를 사용하여 첫 번째 가상 머신에 연결합니다.

1. **장애 조치(failover) 클러스터 관리자** 에서 모든 코어 클러스터 리소스가 첫 번째 가상 머신에 있는지 확인합니다. 필요한 경우 디스크를 해당 가상 머신으로 이동 합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. 

1. **Setup** 을 선택합니다.

1. **SQL Server 설치 센터** 에서 **설치** 를 선택합니다.

1. **SQL Server 장애 조치(failover) 클러스터 새로 설치** 를 선택합니다. 마법사의 지침에 따라 SQL Server FCI를 설치합니다.

1. **클러스터 디스크 선택** 페이지에서 VM에 연결 된 모든 공유 디스크를 선택 합니다. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-disk-selection.png" alt-text="클러스터 디스크 선택":::

1. **클러스터 네트워크 구성** 페이지에서 사용자가 제공 하는 IP는 SQL Server vm이 단일 서브넷 또는 다중 서브넷에 배포 되었는지 여부에 따라 달라 집니다. 

   1. **단일 서브넷 환경** 에 대해 [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 에 추가할 IP 주소를 제공 합니다.
   1. **다중 서브넷 환경** 에 대해 이전에 [장애 조치 (failover) 클러스터 인스턴스 네트워크 이름의 IP 주소로](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)지정 했던 _첫 번째_ SQL Server VM의 서브넷에 보조 IP 주소를 제공 합니다.

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="장애 조치 (failover) 클러스터 인스턴스 네트워크 이름에 대 한 ip 주소로 이전에 지정 된 첫 번째 SQL Server VM의 서브넷에 보조 IP 주소를 제공 합니다.":::

1. **데이터베이스 엔진 구성** 페이지에서 데이터베이스 디렉터리가 Azure 공유 디스크에 있는지 확인 합니다. 

1. 마법사의 지침을 완료 한 후 설치 프로그램은 첫 번째 노드에 SQL Server fci를 설치 합니다.

1. 첫 번째 노드에서 FCI 설치가 성공한 후 RDP를 사용 하 여 두 번째 노드에 연결 합니다.

1. **SQL Server 설치 센터** 를 연 다음, **설치** 를 선택합니다.

1. **SQL Server 장애 조치(failover) 클러스터에 노드 추가** 를 선택합니다. 마법사의 지침에 따라 SQL Server를 설치 하 고 fci에 노드를 추가 합니다.

1. 다중 서브넷 시나리오의 경우 **클러스터 네트워크 구성** 에서 이전에 [장애 조치 (failover) 클러스터 인스턴스 네트워크 이름의 IP 주소로](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) 지정 된 _두 번째_ SQL Server VM 서브넷의 서브넷에 보조 IP 주소를 입력 합니다.

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="장애 조치 (failover) 클러스터 인스턴스 네트워크 이름에 대 한 ip 주소로 이전에 지정 된 두 번째 SQL Server VM 서브넷의 서브넷에 보조 IP 주소를 입력 합니다.":::

    **클러스터 네트워크 구성** 에서 **다음** 을 선택한 후 설치 프로그램은 예제 이미지에서와 같이 여러 서브넷을 검색 SQL Server를 나타내는 대화 상자를 표시 합니다.  **예** 를 선택하여 확인합니다. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="다중 서브넷 확인":::

1. 마법사의 지침을 완료 한 후 설치 프로그램은 두 번째 SQL Server fci 노드를 추가 합니다. 

1. SQL Server 장애 조치 (failover) 클러스터 인스턴스에 참여 시킬 다른 SQL Server vm에서 이러한 단계를 반복 합니다. 


>[!NOTE]
> Azure Marketplace 갤러리 이미지는 SQL Server Management Studio 설치 되어 제공 됩니다. marketplace 이미지 [다운로드 SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)를 사용 하지 않은 경우


## <a name="register-with-sql-iaas-extension"></a>SQL IaaS 확장에 등록 

포털에서 SQL Server VM을 관리 하려면 [경량 관리 모드로](sql-agent-extension-manually-register-single-vm.md#lightweight-mode)SQL IaaS 에이전트 확장을 사용 하 여 등록 합니다. 현재는 fci와 Azure vm의 SQL Server 지원 되는 유일한 모드입니다. 

PowerShell을 사용하여 경량 모드로 SQL Server VM을 등록합니다(-LicenseType은 `PAYG` 또는 `AHUB`일 수 있음).

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>연결 구성 

SQL Server vm을 여러 서브넷에 배포한 경우에는이 단계를 건너뜁니다. SQL Server vm을 단일 서브넷에 배포한 경우 fci로 트래픽을 라우팅하도록 추가 구성 요소를 구성 해야 합니다. Azure Load Balancer 또는 장애 조치 (failover) 클러스터 인스턴스의 분산 네트워크 이름으로 vnn (가상 네트워크 이름)을 구성할 수 있습니다. [두 방법 간의 차이점을 검토](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn) 한 후 [분산 네트워크 이름](failover-cluster-instance-distributed-network-name-dnn-configure.md) 또는 [가상 네트워크 이름을](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 배포 하 고 장애 조치 (failover) 클러스터 인스턴스에 대 한 Azure Load Balancer 합니다.  

## <a name="limitations"></a>제한 사항

- Azure 가상 머신은 가상 머신 CSV의 스토리지와 [표준 부하 분산 장치](../../../load-balancer/load-balancer-overview.md)가 있는 Windows Server 2019에서 MSDTC(Microsoft Distributed Transaction Coordinator)를 지원합니다. MSDTC는 Windows Server 2016 및 이전 버전에서는 지원 되지 않습니다. 
- [경량 관리 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)로만 SQL IaaS 에이전트 확장에 등록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 공유 디스크가 적합한 FCI 스토리지 솔루션이 아닌 경우 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md) 또는 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md)를 대신 사용하여 FCI를 만드는 것이 좋습니다. 


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(Failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)
