---
title: 프리미엄 파일 공유를 사용하여 FCI 만들기
description: PFS(프리미엄 파일 공유)를 사용하여 Azure Virtual Machines에서 SQL Server를 사용한 FCI(장애 조치(failover) 클러스터 인스턴스)를 만듭니다.
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
ms.openlocfilehash: 8759452ec5a5f2142171f5f18f15a62033d2669c
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133265820"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>프리미엄 파일 공유를 사용하여 FCI 만들기(Azure VMs의 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내의 [여러 서브넷에](failover-cluster-instance-prepare-vm.md#subnets) SQL Server VM을 만들어 장애 조치(failover) 클러스터 인스턴스에 대한 Azure Load Balancer 또는 DNN(분산 네트워크 이름)이 필요하지 않습니다.


이 문서에서는 [프리미엄 파일 공유](../../../storage/files/storage-how-to-create-file-share.md)를 사용하여 Azure VMs(Virtual Machines)에서 SQL Server를 사용한 FCI(장애 조치(failover) 클러스터 인스턴스)를 만드는 방법을 설명합니다.

Premium 파일 공유는 SSD를 지원하며, Windows Server 2012 이상에서 SQL Server 2012 이상에 대해 장애 조치(failover) 클러스터 인스턴스와 함께 사용할 수 있도록 완벽하게 지원되는 대기 시간이 짧은 파일 공유를 일관되게 제공합니다. 프리미엄 파일 공유는 뛰어난 유연성을 제공하여 가동 중지 시간 없이 파일 공유 크기를 조정하고 크기를 조정할 수 있습니다.

자세히 알아보려면 [Azure VMs에서 SQL Server를 사용한 FCI](failover-cluster-instance-overview.md) 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)의 개요를 참조하세요. 

> [!NOTE]
> Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 장애 조치(failover) 클러스터 인스턴스 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [장애 조치(failover) 클러스터 인스턴스 마이그레이션](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 지침을 완료하려면 먼저 다음이 있어야 합니다.

- Azure 구독
- Azure 가상 머신과 Active Directory 모두에서 개체를 만들 수 있는 권한이 있는 계정
- [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 또는 다른 [가용성 영역](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)에 [준비된 둘 이상의 Microsoft Azure 가상 머신](failover-cluster-instance-prepare-vm.md)
- 데이터 파일용 데이터베이스의 스토리지 할당량을 기준으로 클러스터형 드라이브로 [프리미엄 파일 공유](../../../storage/files/storage-how-to-create-file-share.md)를 사용합니다.
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps) 

## <a name="mount-premium-file-share"></a>프리미엄 파일 공유 탑재

프리미엄 파일 공유를 탑재하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 스토리지 계정으로 이동합니다.
1. **데이터 스토리지** 에서 **파일 공유** 로 이동한 다음 SQL 스토리지에 사용할 프리미엄 파일 공유를 선택합니다.
1. **연결** 을 선택하여 파일 공유에 대한 연결 문자열을 가져옵니다.
1. 드롭다운 목록에서 사용할 드라이브 문자를 선택하고 인증 방법으로 **스토리지 계정 키** 를 선택한 다음 코드 블록을 메모장과 같은 텍스트 편집기에 복사합니다.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="파일 공유 연결 포털에서 PowerShell 명령 복사":::

1. RDP(원격 데스크톱 프로토콜)를 사용하여 SQL Server **FCI가 서비스 계정에 사용할 계정으로 SQL Server VM에 연결합니다.**
1. 관리 PowerShell 명령 콘솔을 엽니다.
1. 파일 공유 포털에서 이전에 텍스트 편집기에 복사한 명령을 실행합니다.
1. 파일 탐색기 또는 **실행** 대화 상자(Windows+R 입력)를 사용하여 공유로 이동합니다. 네트워크 경로 `\\storageaccountname.file.core.windows.net\filesharename`을 사용합니다. 예를 들어 `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`
1. 새로 연결된 파일 공유에서 SQL 데이터 파일을 저장할 폴더를 하나 이상 만듭니다.
1. 클러스터에 참여하는 각 SQL Server VM에 대해 이러한 단계를 반복합니다.

  > [!IMPORTANT]
  > 백업 파일에 별도의 파일 공유를 사용하여 이 데이터 및 로그 파일용 공유에서 IOPS(초당 입출력 작업 수) 및 공간 용량을 절약하는 것이 좋습니다. 백업 파일에 프리미엄 또는 표준 파일 공유 중 하나를 사용할 수 있습니다.

## <a name="create-windows-failover-cluster"></a>Windows 장애 조치(failover) 클러스터 만들기

Windows Server 장애 조치(failover) 클러스터를 만드는 단계는 SQL Server VM을 단일 서브넷 또는 여러 서브넷에 배포했는지에 따라 달라집니다. 클러스터를 만들려면 다중 서브넷 시나리오 또는 [단일](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster) [서브넷 시나리오에](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) 대한 자습서의 단계를 수행합니다. 이러한 자습서는 가용성 그룹을 만들기 위한 것이지만 클러스터를 만드는 단계는 동일합니다. 


## <a name="configure-quorum"></a>쿼럼 구성

클라우드 감시는 Azure VM에서 SQL Server 이러한 유형의 클러스터 구성에 권장되는 쿼럼 솔루션입니다.  

클러스터에 짝수 투표가 있는 경우 비즈니스 요구에 가장 적합한 [쿼럼 솔루션](hadr-cluster-quorum-configure-how-to.md)을 구성합니다. 자세한 내용은 [SQL Server VM에 대한 쿼럼](hadr-windows-server-failover-cluster-overview.md#quorum)을 참조하세요. 

## <a name="validate-cluster"></a>클러스터 유효성 검사

장애 조치(Failover) 클러스터 관리자 UI 또는 PowerShell을 사용하여 가상 머신 중 하나에서 클러스터의 유효성을 검사합니다.

UI를 사용하여 클러스터의 유효성을 검사하려면 가상 머신 중 하나에서 다음을 수행합니다.

1. **서버 관리자** 에서 **도구** 를 선택한 다음 **장애 조치(failover) 클러스터 관리자** 를 선택합니다.
1. **장애 조치(failover) 클러스터 관리자** 에서 **작업** 을 선택한 다음 **구성 유효성 검사** 를 선택합니다.
1. **다음** 을 선택합니다.
1. **서버 또는 클러스터 선택** 에서 두 가상 머신의 이름을 입력합니다.
1. **테스트 옵션** 에서 **선택한 테스트만 실행** 을 선택합니다. 
1. **다음** 을 선택합니다.
1. **테스트 선택** 에서 다음과 같이 **스토리지** 및 **스토리지 공간 다이렉트** 를 선택합니다.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="클러스터 유효성 검사 테스트 선택":::

1. **다음** 을 선택합니다.
1. **확인** 에서 **다음** 을 선택합니다. **구성 유효성 검사** 마법사가 유효성 검사 테스트를 실행합니다.


PowerShell을 사용하여 클러스터의 유효성을 검사하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```



## <a name="test-cluster-failover"></a>클러스터 장애 조치(failover) 테스트

클러스터의 장애 조치를 테스트합니다. **장애 조치(Failover) 클러스터 관리자** 에서 클러스터를 마우스 오른쪽 단추로 클릭하고 **추가 작업** > **Move Core Cluster Resource**(코어 클러스터 리소스 이동) > **노드 선택** 을 선택한 다음, 클러스터의 다른 노드를 선택합니다. 코어 클러스터 리소스를 클러스터의 모든 노드로 이동한 다음 다시 기본 노드로 이동합니다. 클러스터를 각 노드로 성공적으로 이동할 수 있는 경우 SQL Server를 설치할 준비가 된 것입니다.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="코어 리소스를 다른 노드로 이동하여 클러스터 장애 조치(failover) 테스트":::


## <a name="create-sql-server-fci"></a>SQL Server FCI 만들기

장애 조치(failover) 클러스터를 구성한 후에는 SQL Server FCI를 만들 수 있습니다.

1. RDP를 사용하여 첫 번째 가상 머신에 연결합니다.

1. **장애 조치(Failover) 클러스터 관리자** 에서 모든 코어 클러스터 리소스가 첫 번째 가상 머신에 있는지 확인합니다. 필요한 경우 모든 리소스를 이 가상 컴퓨터로 이동합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. 

1. **Setup** 을 선택합니다.

1. **SQL Server 설치 센터** 에서 **설치** 를 선택합니다.

1. **SQL Server 장애 조치(failover) 클러스터 새로 설치** 를 선택한 다음, 마법사의 지침에 따라 SQL Server FCI를 설치합니다.

1. 클러스터 **네트워크 구성** 페이지에서 제공하는 IP는 SQL Server VM이 단일 서브넷 또는 여러 서브넷에 배포되었는지에 따라 달라집니다. 

   1. 단일 **서브넷 환경의** 경우 [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 추가하려는 IP 주소를 제공합니다.
   1. 다중 **서브넷 환경의** 경우 이전에 장애 조치(failover) 클러스터 인스턴스 네트워크 이름의 IP 주소로 지정한 _첫 번째_ SQL Server VM의 서브넷에 보조 [IP 주소를](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)제공합니다.

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="이전에 장애 조치(failover) 클러스터 인스턴스 네트워크 이름의 IP 주소로 지정한 첫 번째 SQL Server VM의 서브넷에 보조 IP 주소를 제공합니다.":::

1. **데이터베이스 엔진 구성에서** 데이터 디렉터리를 프리미엄 파일 공유에 있어야 합니다. 공유의 전체 경로를 `\\storageaccountname.file.core.windows.net\filesharename\foldername` 형식으로 입력합니다. 파일 서버를 데이터 디렉터리로 지정했다는 경고가 표시됩니다. 이 경고는 예상된 것입니다. 파일 공유를 보관할 때 RDP를 통해 VM에 액세스하는 데 사용한 사용자 계정이 SQL Server 서비스가 가능한 오류를 방지하는 데 사용하는 계정과 동일한지 확인합니다.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="파일 공유를 SQL 데이터 디렉터리로 사용":::

1. 마법사의 단계를 완료하면 설치 프로그램에서 첫 번째 노드에 SQL Server FCI를 설치합니다.

1. 첫 번째 노드에서 FCI 설치가 성공한 후 RDP를 사용하여 두 번째 노드에 연결합니다.

1. **SQL Server 설치 센터** 를 연 다음, **설치** 를 선택합니다.

1. **SQL Server 장애 조치(failover) 클러스터에 노드 추가** 를 선택합니다. 마법사의 지침에 따라 SQL Server 설치하고 FCI에 노드를 추가합니다.

1. 다중 서브넷 시나리오의 경우 **클러스터 네트워크 구성** 에서 이전에 장애 조치(failover) 클러스터 인스턴스 네트워크 이름의 IP 주소로 지정한 두 _번째_ SQL Server VM의 서브넷에 보조 [IP 주소를](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses) 입력합니다.

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="이전에 장애 조치(failover) 클러스터 인스턴스 네트워크 이름의 IP 주소로 지정한 두 번째 SQL Server VM 서브넷의 서브넷에 보조 IP 주소를 입력합니다.":::

    **클러스터 네트워크 구성에서** **다음을** 선택한 후 설치 프로그램에서 SQL Server 이미지와 같이 여러 서브넷을 검색했음을 나타내는 대화 상자가 표시됩니다.  **예** 를 선택하여 확인합니다. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="다중 서브넷 확인":::
   

1. 마법사의 지침을 완료하면 설치 프로그램에서 두 번째 SQL Server FCI 노드를 추가합니다. 

1. SQL Server 장애 조치 클러스터 인스턴스에 추가하려는 다른 모든 노드에서 이러한 단계를 반복합니다. 


>[!NOTE]
> Azure Marketplace 갤러리 이미지에는 SQL Server Management Studio 설치되어 있습니다. 마켓플레이스 이미지를 사용하지 않은 경우 [SSMS(SQL Server Management Studio)를 다운로드합니다.](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="register-with-sql-iaas-extension"></a>SQL IaaS 확장에 등록 

포털에서 SQL Server VM을 관리하려면 현재 Azure VM에서 FCI 및 SQL Server 지원되는 유일한 모드인 [경량 관리 모드의](sql-agent-extension-manually-register-single-vm.md#lightweight-mode)SQL IaaS 에이전트 확장에 등록합니다. 

PowerShell을 사용하여 경량 모드로 SQL Server VM을 등록합니다(-LicenseType은 `PAYG` 또는 `AHUB`일 수 있음).

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>연결 구성

SQL Server vm을 여러 서브넷에 배포한 경우에는이 단계를 건너뜁니다. SQL Server vm을 단일 서브넷에 배포한 경우 fci로 트래픽을 라우팅하도록 추가 구성 요소를 구성 해야 합니다. Azure Load Balancer 또는 장애 조치 (failover) 클러스터 인스턴스의 분산 네트워크 이름으로 vnn (가상 네트워크 이름)을 구성할 수 있습니다. [두 방법 간의 차이점을 검토](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn) 한 후 [분산 네트워크 이름](failover-cluster-instance-distributed-network-name-dnn-configure.md) 또는 [가상 네트워크 이름을](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 배포 하 고 장애 조치 (failover) 클러스터 인스턴스에 대 한 Azure Load Balancer 합니다.  

## <a name="limitations"></a>제한 사항

- MSDTC(Microsoft Distributed Transaction Coordinator)는 Windows Server 2016 이전 버전에서 지원되지 않습니다. 
- Filestream은 프리미엄 파일 공유를 사용하는 장애 조치(failover) 클러스터에 대해 지원되지 않습니다. 파일 스트림을 사용하려면 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 또는 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md)를 대신 사용하여 클러스터를 배포합니다.
- [경량 관리 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)로만 SQL IaaS 에이전트 확장에 등록할 수 있습니다. 
- 데이터베이스 스냅샷은 [스파스 파일 제한으로 인해 Azure Files](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)에서 현재 지원되지 않습니다.
- 데이터베이스 스냅숏은 지원되지 않으므로 사용자 데이터베이스에 대한 CHECKDB는 CHECKDB WITH TABLOCK로 대체됩니다. TABLOCK은 수행되는 검사를 제한합니다. 데이터베이스에 대해 DBCC CHECKCATALOG가 실행되지 않으며 Service Broker 데이터의 유효성이 검사되지 않습니다.
- MASTER 및 MSDB 데이터베이스에 대한 CHECKDB는 지원되지 않습니다. 
- 메모리 내 OLTP 기능을 사용하는 데이터베이스는 프리미엄 파일 공유로 배포된 장애 조치(failover) 클러스터 인스턴스에서 지원되지 않습니다. 비즈니스에 메모리 내 OLTP가 필요한 경우 대신 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md) 또는 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md)를 사용하여 FCI를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

프리미엄 파일 공유가 적합한 FCI 스토리지 솔루션이 아닌 경우 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md) 또는 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md)를 대신 사용하여 FCI를 만드는 것이 좋습니다. 

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(Failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)

