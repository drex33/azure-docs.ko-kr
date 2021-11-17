---
title: '자습서: 여러 서브넷에서 가용성 그룹 구성'
description: '이 자습서에서는 Azure VMs(Virtual Machines)의 SQL Server에 대한 여러 서브넷 내에서 Always On 가용성 그룹을 만드는 방법을 보여 줍니다. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: aeef6f689e9a27b6a813b53e73ceb11ff982af97
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159849"
---
# <a name="tutorial-configure-an-availability-group-in-multiple-subnets-sql-server-on-azure-vms"></a>자습서: 여러 서브넷에서 가용성 그룹 구성(Azure VM의 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> SQL Server VM을 동일한 Azure 가상 네트워크 내의 여러 서브넷에 만들면 AG(Always On 가용성 그룹)에 대한 Azure Load Balancer가 필요하지 않습니다.

이 자습서에서는 Azure VMs(Virtual Machines)의 SQL Server에 대한 여러 서브넷 내에서 Always On 가용성 그룹을 만드는 방법을 보여 줍니다. 전체 자습서에서는 Windows Server 장애 조치(failover) 클러스터와 두 개의 SQL Server 복제본과 수신기가 있는 가용성 그룹을 만듭니다.

 
**예상 시간**: [필수 구성 요소](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)가 완료되었다고 가정하면 이 자습서를 완료하는 데 약 30분이 걸립니다. 


## <a name="prerequisites"></a>필수 구성 요소

다음 표에는 이 자습서를 시작하기 전에 완료해야 하는 [필수 구성 요소](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)가 나와 있습니다.

| 요구 사항 | Description |
|----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **두 개의 SQL Server 인스턴스** | - 서로 다른 두 개의 Azure 가용성 영역 또는 동일한 가용성 집합의 각 VM <br/> - Azure Virtual Network 내의 별도 서브넷에 있음 <br/> - 두 개의 보조 IP가 각 VM에 할당됨 <br/> - 단일 도메인에 <br/> |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **SQL Server 서비스 계정** | 각 컴퓨터에 대해 SQL Server 서비스에서 사용하는 도메인 계정 | 
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **방화벽 포트 열기** | - SQL Server: 기본 인스턴스의 경우 **1433** <br/> - 데이터베이스 미러링 엔드포인트: **5022** 또는 사용 가능한 모든 포트 <br/> |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **도메인 설치 계정** | - 각 SQL Server의 로컬 관리자 <br/> - SQL Server의 각 인스턴스에 대해 SQL Server sysadmin 고정 서버 역할의 멤버  |

이 자습서에서는 사용자가 [SQL Server Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)을 기본적으로 이해하고 있다고 가정합니다. 

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>클러스터 만들기

Always On 가용성 그룹은 Windows Server 장애 조치(failover) 클러스터 인프라를 기반으로 하므로 가용성 그룹을 배포하기 전에 먼저 기능 추가, 클러스터 만들기 및 클러스터 IP 주소 설정을 포함하는 Windows Server 장애 조치(failover) 클러스터를 구성해야 합니다. 

### <a name="add-failover-cluster-feature"></a>장애 조치(failover) 클러스터 기능 추가

장애 조치(failover) 클러스터 기능을 두 SQL Server VM에 추가합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. [필수 구성 요소 문서](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)에서 만든 **CORP\Install** 도메인 계정과 같이 AD에서 개체를 만들 수 있는 권한이 있는 도메인 계정을 사용하여 RDP(원격 데스크톱 프로토콜)를 통해 SQL Server 가상 머신에 연결합니다. 
1. **서버 관리자 대시보드** 를 엽니다.
1. 대시보드에서 **역할 및 기능 추가** 링크를 선택합니다.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="대시보드에서 역할 및 기능 추가 링크 선택":::

1. **서버 기능** 섹션으로 이동할 때까지 **다음** 을 선택합니다.
1. **기능** 에서 **장애 조치 클러스터링** 을 선택합니다.
1. 필요한 추가 기능을 추가합니다.
1. **설치** 를 선택하여 기능을 추가합니다.
1. 다른 SQL Server VM에서도 이 단계를 반복합니다. 


### <a name="create-cluster"></a>클러스터 만들기

클러스터 기능이 각 SQL Server VM에 추가되면 Windows Server 장애 조치(failover) 클러스터를 만들 준비가 되었습니다. 

클러스터를 만들려면 다음 단계를 수행합니다. 

1. [필수 구성 요소 문서](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)에서 만든 **CORP\Install** 도메인 계정과 같이 AD에서 개체를 만들 수 있는 권한이 있는 도메인 계정을 사용하여 RDP(원격 데스크톱 프로토콜)를 통해 SQL Server 첫 번째 가상 머신(예: **SQL-VM-1**)에 연결합니다. 
2. **서버 관리자** 대시보드에서 **도구** 를 선택한 다음, **장애 조치(Failover) 클러스터 관리자** 를 선택합니다.
3. 왼쪽 창에서 마우스 오른쪽 단추로 **장애 조치(Failover) 클러스터 관리자** 를 클릭한 다음, **클러스터 만들기** 를 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/01-create-cluster.png" alt-text="클러스터 만들기":::

4. **클러스터 만들기 마법사** 에서 다음 표에 제공된 설정을 사용하여 페이지를 단계별로 실행하여 2노드 클러스터를 만듭니다.

   | 호출 | 설정 |
   | --- | --- |
   | 시작하기 전에 |기본값을 사용합니다. |
   | 서버 선택 |**서버 이름 입력** 에서 첫 번째 SQL Server 이름(예: **SQL-VM-1**)을 입력하고, **추가** 를 선택합니다.<br/>**서버 이름 입력** 에서 두 번째 SQL Server 이름(예: **SQL-VM-2**)을 입력하고, **추가** 를 선택합니다. |
   | 유효성 검사 경고 |**예. [다음]을 클릭하면 구성 유효성 검사 테스트를 실행한 다음, 클러스터를 만드는 과정으로 돌아갑니다.** 를 선택합니다. |
   | 시작하기 전에 | 다음을 선택합니다. |
   | 선택한 테스트만 실행 | **선택한 테스트만 실행** 을 선택합니다. |
   | 테스트 선택 | 스토리지를 선택 취소합니다. **인벤토리**, **네트워크** 및 **시스템 구성** 이 선택되어 있는지 확인합니다. 
   | 확인 | 다음을 선택합니다.<br/>유효성 검사가 완료될 때까지 기다립니다.<br/>**보고서 보기** 를 선택하여 보고서를 검토합니다. 하나의 네트워크 인터페이스에서만 연결할 수 있는 VM에 대한 경고는 안전하게 무시할 수 있습니다. Azure 인프라에는 물리적 중복성이 있으므로 추가 네트워크 인터페이스를 추가할 필요가 없습니다.<br/> **마침** 을 선택합니다.|
   | 클러스터 관리를 위한 액세스 지점 |클러스터 이름(예: **SQLAGCluster1**)을 **클러스터 이름** 에 입력합니다.|
   | 확인 | **클러스터에 사용할 수 있는 모든 스토리지를 추가하세요.** 를 선택 취소하고, **다음** 을 선택합니다. |
   | 요약 | **마침** 을 선택합니다. | 

   >[!WARNING]
   >**클러스터에 사용할 수 있는 모든 스토리지를 추가하세요.** 를 선택 취소하지 않으면 Windows에서 클러스터링 프로세스 중에 가상 디스크를 분리합니다. 결과적으로 클러스터에서 스토리지가 제거되고 PowerShell을 사용하여 다시 연결될 때까지 디스크 관리자 또는 탐색기에 표시되지 않습니다. 
   > 
   

### <a name="set-the-failover-cluster-ip-address"></a>장애 조치(failover) 클러스터 IP 주소 설정

일반적으로 클러스터에 할당된 IP 주소는 VM에 할당된 것과 동일한 IP 주소입니다. 즉, Azure에서 클러스터 IP 주소는 실패 상태가 되며 온라인으로 전환할 수 없습니다. 클러스터 IP 주소를 변경하여 IP 리소스를 온라인 상태로 만듭니다. 

필수 구성 요소 중에 아래의 예제 표와 같이 각 SQL Server VM에 [보조 IP 주소를 할당](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms)해야 합니다(특정 IP 주소는 다를 수 있음). 

   | VM 이름 | 서브넷 이름 | 서브넷 주소 범위 | 보조 IP 이름 | 보조 IP 주소 |
   | --- | --- | --- | --- | --- |
   | SQL-VM-1 | SQL-subnet-1 | 10.38.1.0/24 | windows-cluster-ip | 10.38.1.10 |
   | SQL-VM-2 | SQL-subnet-2 | 10.38.2.0/24 | windows-cluster-ip | 10.38.2.10

이러한 IP 주소를 각 관련 서브넷에 대한 클러스터 IP 주소로 할당합니다. 

> [!NOTE]
> Windows Server 2019에서 클러스터는 **클러스터 네트워크 이름** 대신 **분산 서버 이름** 을 만들고, CNO(클러스터 이름 개체)는 클러스터의 모든 노드에 대한 IP 주소에 자동으로 등록되므로 전용 Windows 클러스터 IP 주소가 필요하지 않습니다. Windows Server 2019를 사용하는 경우 이 섹션과 **클러스터 코어 리소스** 를 참조하는 다른 단계를 건너뛰거나, [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-windows-failover-cluster)을 사용하여 VNN(가상 네트워크 이름) 기반 클러스터를 만듭니다. 자세한 내용은 [장애 조치(failover) 클러스터: 클러스터 네트워크 개체](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) 블로그를 참조하세요. 


클러스터 IP 주소를 변경하려면 다음 단계를 수행합니다. 

1. **장애 조치(Failover) 클러스터 관리자** 에서 **클러스터 코어 리소스** 로 아래로 스크롤하여 클러스터 세부 정보를 확장합니다. **실패** 상태의 각 서브넷에서 **이름** 과 두 개의 **IP 주소** 리소스가 표시됩니다.
1. 마우스 오른쪽 단추로 첫 번째 실패한 **IP 주소** 리소스를 클릭한 다음, **속성** 을 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/02-failed-ip-address.png" alt-text="클러스터 속성":::

1. **고정 IP 주소** 를 선택하고, IP 주소를 첫 번째 SQL Server VM(예: **SQL-VM-1**)에 할당한 서브넷의 [전용 Windows 클러스터 IP 주소](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms)로 업데이트합니다. **확인** 을 선택합니다.
   
    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/03-first-static-ip-address.png" alt-text=" **고정 IP 주소**를 선택하고, IP 주소를 필수 구성 요소 문서에서 SQL Server VM에 할당한 것과 동일한 서브넷의 전용 Windows 클러스터 IP 주소로 업데이트함":::

1. 두 번째 SQL Server VM(예: **SQL-VM-2**)의 서브넷에 대한 [전용 Windows 클러스터 IP 주소](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms)를 사용하여 두 번째 실패한 **IP 주소** 리소스에 대해 단계를 반복합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/04-second-static-ip-address.png" alt-text="다른 SQL Server VM의 서브넷에 대한 전용 Windows 클러스터 IP 주소를 사용하여 두 번째 실패한 **IP 주소** 리소스에 대해 단계를 반복함":::

1. **클러스터 코어 리소스** 섹션에서 마우스 오른쪽 단추로 클러스터 이름을 클릭하고, **온라인 상태로 전환** 을 선택합니다. 이름과 IP 주소 리소스 중 하나가 온라인 상태가 될 때까지 기다립니다. 

SQL Server VM은 서로 다른 서브넷에 있으므로 클러스터는 두 개의 전용 Windows 클러스터 IP 주소에 대한 OR 종속성을 갖습니다. 클러스터 이름 리소스가 온라인 상태가 되면 새 AD(Active Directory) 컴퓨터 계정을 사용하여 DC(도메인 컨트롤러) 서버를 업데이트합니다. 클러스터 코어 리소스에서 노드를 이동하는 경우 한 IP 주소는 오프라인이 되지만 다른 IP 주소는 온라인 상태가 되어 DC 서버를 새 IP 주소 연결로 업데이트합니다.  

>[!TIP]
> 프로덕션 환경의 Azure VM에서 클러스터를 실행하는 경우 클러스터 설정을 더 완화된 모니터링 상태로 변경하여 클라우드 환경에서 클러스터 안정성과 신뢰도를 향상시킵니다. 자세한 내용은 [SQL Server VM - HADR 구성 모범 사례](hadr-cluster-best-practices.md#checklist)를 참조하세요.

## <a name="configure-quorum"></a>쿼럼 구성

2노드 클러스터에서는 클러스터의 신뢰도와 안정성을 위해 쿼럼 디바이스가 필요합니다. Azure VM에서는 클라우드 감시가 권장되는 쿼럼 구성이지만 [사용 가능한 다른 옵션](hadr-cluster-quorum-configure-how-to.md)이 있습니다. 이 섹션의 단계에서는 쿼럼에 대한 클라우드 감시를 구성합니다. 스토리지 계정에 대한 액세스 키를 식별한 다음, 클라우드 감시를 구성합니다. 

## <a name="get-access-keys-for-storage-account"></a>스토리지 계정에 대한 액세스 키 가져오기 

Microsoft Azure Storage 계정을 만들면 자동으로 생성되는 두 개의 액세스 키(기본 액세스 키 및 보조 액세스 키)와 연결됩니다. 클라우드 감시를 처음 만들 때 기본 액세스 키를 사용하지만, 이후에는 클라우드 감시에 사용할 키에 대한 제한이 없습니다. 

Azure Portal을 사용하여 [필수 구성 요소 문서](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#create-azure-storage-account)에서 만든 Azure Storage 계정에 대한 스토리지 액세스 키를 확인하고 복사합니다. 


스토리지 액세스 키를 확인하고 복사하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)의 리소스 그룹으로 이동하여 만든 스토리지 계정을 선택합니다. 
1. **보안 + 네트워킹** 아래에서 **액세스 키** 를 선택합니다. 
1. **키 표시** 를 선택하고, 키를 복사합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/05-storage-account-keys.png" alt-text="**키 표시**를 선택하고 키를 복사함":::

### <a name="configure-cloud-witness"></a>클라우드 감시 구성

액세스 키가 복사되었으면 클러스터 쿼럼에 대한 클라우드 감시를 만듭니다. 

클라우드 감시를 만들려면 다음 단계를 수행합니다. 

1. 원격 데스크톱을 사용하여 첫 번째 SQL Server VM인 **SQL-VM-1** 에 연결합니다.
1. **Windows PowerShell** 을 관리자 모드에서 엽니다.
1. PowerShell 스크립트를 실행하여 연결에 대한 TLS(전송 계층 보안) 값을 1.2로 설정합니다.

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    ```
    
1. PowerShell을 사용하여 클라우드 감시를 구성합니다. 스토리지 계정 이름 및 액세스 키 값을 특정 정보로 바꿉니다.  

    ```powershell
    Set-ClusterQuorum -CloudWitness -AccountName "Storage_Account_Name&quot; -AccessKey &quot;Storage_Account_Access_Key"
    ```
    
1. 다음 예제 출력에서 성공을 나타냅니다.  

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/06-configure-quorum.png" alt-text="이 예제와 같이 출력에 클라우드 감시가 표시됨":::

클러스터 코어 리소스가 클라우드 감시로 구성됩니다.

## <a name="enable-ag-feature"></a>AG 기능 사용 

Always On 가용성 그룹 기능은 기본적으로 사용하지 않도록 설정되어 있습니다. **SQL Server 구성 관리자** 를 사용하여 두 SQL Server 인스턴스에서 이 기능을 사용하도록 설정합니다. 

가용성 그룹 기능을 사용하도록 설정하려면 다음 단계를 수행합니다. 

1. [필수 구성 요소 문서](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)에서 만든 **CORP\Install** 도메인 계정과 같이 sysadmin 고정 서버 역할의 멤버인 도메인 계정을 사용하여 RDP 파일을 첫 번째 SQL Server VM(예: **SQL-VM-1**)에 시작합니다.
1. SQL Server VM 중 하나의 **시작** 화면에서 **SQL Server 구성 관리자** 를 시작합니다.
1. 브라우저 트리에서 **SQL Server 서비스** 를 강조 표시하고, 마우스 오른쪽 단추로 **SQL Server(MSSQLSERVER)** 서비스를 클릭한 다음, **속성** 을 선택합니다.
1. **AlwaysOn 고가용성** 탭을 선택한 다음, **AlwaysOn 가용성 그룹 사용** 확인란을 선택합니다.

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/08-enable-always-on.png" alt-text="AlwaysOn 가용성 그룹 사용":::

1. **적용** 을 선택합니다. 팝업 대화 상자에서 **확인** 을 선택합니다.
1. SQL Server 서비스를 다시 시작합니다.
1. 이러한 단계를 다른 SQL Server 인스턴스에 대해 반복합니다.


## <a name="create-database"></a>데이터베이스 만들기

데이터베이스의 경우 이 섹션의 단계에 따라 새 데이터베이스를 만들거나 [AdventureWorks 데이터베이스](/sql/samples/sql-samples-where-are)를 복원할 수 있습니다. 또한 데이터베이스를 백업하여 로그 체인을 초기화해야 합니다. 백업되지 않은 데이터베이스는 가용성 그룹의 필수 구성 요소를 충족하지 않습니다. 

데이터베이스를 만들려면 다음 단계를 수행합니다. 

1. [필수 구성 요소 문서](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)에서 만든 **CORP\Install** 도메인 계정과 같이 sysadmin 고정 서버 역할의 멤버인 도메인 계정을 사용하여 RDP 파일을 첫 번째 SQL Server VM(예: **SQL-VM-1**)에 시작합니다.
1. **SQL Server Management Studio** 를 열고, SQL Server 인스턴스에 연결합니다.
1. **개체 탐색기** 에서 마우스 오른쪽 단추로 **데이터베이스** 를 클릭하고, **새 데이터베이스** 를 선택합니다.
1. **데이터베이스 이름** 에서 **MyDB1** 을 입력합니다. 
1. 기본적으로 가득 차지 않은 경우 **옵션** 페이지를 선택하고, **복구 모델** 드롭다운에서 **전체** 를 선택합니다. 가용성 그룹에 참여하기 위한 필수 구성 요소를 충족하려면 데이터베이스가 전체 복구 모드에 있어야 합니다. 
1. **확인** 을 선택하여 **새 데이터베이스** 페이지를 닫고, 새 데이터베이스를 만듭니다. 


데이터베이스를 백업하려면 다음 단계를 수행합니다. 

1. **개체 탐색기** 에서 마우스 오른쪽 단추로 데이터베이스를 클릭하고, **작업** 을 강조 표시한 다음, **백업...** 을 선택합니다.

1. **확인** 을 선택하여 데이터베이스의 전체 백업을 기본 백업 위치로 가져옵니다.

## <a name="create-file-share"></a>파일 공유 만들기

SQL Server VM과 해당 서비스 계정 모두에서 액세스할 수 있는 백업 파일 공유를 만듭니다. 

백업 파일 공유를 만들려면 다음 단계를 수행합니다. 


1. **서버 관리자** 의 첫 번째 SQL Server VM에서 **도구** 를 선택합니다. **컴퓨터 관리** 를 엽니다.

2. **공유 폴더** 를 선택합니다.

3. 마우스 오른쪽 단추로 **공유** 를 클릭하고, **새 공유...** 를 선택한 다음, **공유 폴더 만들기 마법사** 를 사용하여 공유를 만듭니다.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/09-new-share.png" alt-text="새 공유 선택":::

4. **폴더 경로** 에 대해 **찾아보기** 를 선택하고 `C:\Backup`과 같은 데이터베이스 백업 공유 폴더의 경로를 찾거나 만듭니다. **다음** 을 선택합니다.

5. **이름, 설명 및 설정** 에서 공유 이름 및 경로를 확인합니다. **다음** 을 선택합니다.

6. **공유 폴더 사용 권한** 에서 **사용 권한 사용자 지정** 을 설정합니다. **사용자 지정...** 을 선택합니다.

7. **사용 권한 사용자 지정** 에서 **추가...** 를 선택합니다.

8. **모든 권한** 을 선택하여 공유에 대한 전체 액세스 권한을 *두* SQL Server 서비스 계정(`Corp\SQLSvc1` 및 `Corp\SQLSvc2`)에 부여합니다.  

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/10-backup-share-permission.png" alt-text="모든 권한이 두 서버의 SQL Server 서비스 계정에 있는지 확인":::

9. **확인** 을 선택합니다.

10. **공유 폴더 사용 권한** 에서 **마침** 을 선택합니다. 다시 **마침** 을 선택합니다.  

## <a name="create-availability-group"></a>가용성 그룹 만들기 

데이터베이스가 백업되면 가용성 그룹을 만들 준비가 되었습니다. 이 그룹은 주 SQL Server 복제본에서 자동으로 전체 백업 및 트랜잭션 로그 백업을 가져와서 **NORECOVERY** 옵션을 사용하여 보조 SQL Server 인스턴스에 복원합니다. 

가용성 그룹을 만들려면 다음 단계를 수행합니다. 

1. 첫 번째 SQL Server VM(예: **SQL-VM-1**)에 있는 SSMS(SQL Server Management Studio)의 **개체 탐색기** 에서 마우스 오른쪽 단추로 **Always On 고가용성** 을 클릭하고, **새 가용성 그룹 마법사** 를 선택합니다.

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/11-new-ag-wizard.png" alt-text="새 가용성 그룹 마법사 시작":::

1. **소개** 페이지에서 **다음** 을 선택합니다. **가용성 그룹 이름 지정** 페이지의 **가용성 그룹 이름** 에서 가용성 그룹 이름(예: **AG1**)을 입력합니다. **다음** 을 선택합니다.

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/12-new-ag-name.png" alt-text="새 가용성 그룹 마법사, 가용성 그룹 이름 지정":::

1. **데이터베이스 선택** 페이지에서 데이터베이스를 선택하고, **다음** 을 선택합니다. 데이터베이스가 필수 구성 요소를 충족하지 않는 경우 전체 복구 모드인지 확인하고, [백업](#create-database)을 수행합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/13-new-ag-select-database.png" alt-text="새 가용성 그룹 마법사, 데이터베이스 선택":::

1. **복제본 지정** 페이지에서 **복제본 추가** 를 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/14-new-ag-add-replica.png" alt-text="새 가용성 그룹 마법사, 복제본 지정":::

1. **서버에 연결** 대화 상자가 나타납니다. **서버 이름** 에서 두 번째 서버의 이름(예: **SQL-VM-2**)을 입력합니다. **연결** 을 선택합니다.
1. **복제본 지정** 페이지에서 **자동 장애 조치(Failover)** 확인란을 선택하고, 드롭다운에서 가용성 모드에 대해 **동기 커밋** 을 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/15-new-ag-replica.png" alt-text=" **복제본 지정** 페이지에서 자동 장애 조치(Failover) 확인란을 선택하고 가용성 모드에 대해 동기 커밋을 선택함":::

1. **엔드포인트** 탭을 선택하여 데이터베이스 미러링 엔드포인트에 사용되는 포트가 [방화벽에서 연](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#configure-the-firewall) 포트인지 확인합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/16-endpoint.png" alt-text="새 가용성 그룹 마법사, 초기 데이터 동기화 선택":::

1. **수신기** 탭을 선택하고, 수신기에 대해 다음 값을 사용하여 **가용성 그룹 수신기 만들기** 를 선택합니다. 

   |필드 | 값 |
   | --- | --- |
   | 수신기 DNS 이름: | AG1-Listener |
   | 포트 | 기본 SQL Server 포트를 사용합니다. 1433 |
   | 네트워크 모드: | 고정 IP |

1. **추가** 를 선택하여 두 SQL Server VM의 수신기에 대한 보조 전용 IP 주소를 제공합니다. 

   다음 표에서는 [필수 조건 문서](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)에서 수신기에 대해 만든 예제 IP 주소를 보여 줍니다(특정 IP 주소는 다를 수 있음).
   
   | VM 이름 | 서브넷 이름 | 서브넷 주소 범위 | 보조 IP 이름 | 보조 IP 주소 |
   | --- | --- | --- | --- | --- |
   | SQL-VM-1 | SQL-subnet-1 | 10.38.1.0/24 | availability-group-listener | 10.38.1.11 |
   | SQL-VM-2 | SQL-subnet-2 | 10.38.2.0/24 | availability-group-listener | 10.38.2.11

1. **IP 주소 추가** 대화 상자의 드롭다운에서 첫 번째 서브넷(예: 10.38.1.0/24)을 선택한 다음, 보조 전용 수신기 **IPv4 주소**(예`10.38.1.11`)를 제공합니다. **확인** 을 선택합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/18-add-listener-ip-subnet-1.png" alt-text="IP 주소 추가 대화 상자의 드롭다운에서 첫 번째 서브넷(예: 10.38.1.0/24)을 선택한 다음, 10.38.1.11과 같은 보조 전용 수신기 IPv4 주소를 제공함":::

1. 이 단계를 다시 반복하지만 드롭다운에서 다른 서브넷(예: 10.38.2.0/24)을 선택하고, 다른 SQL Server VM의 보조 전용 수신기 **IPv4 주소**(예: `10.38.2.11`)를 제공합니다. **확인** 을 선택합니다. 
    

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/19-add-listener-ip-subnet-2.png" alt-text="수신기 IP 추가":::    

1. **수신기** 페이지에서 값을 검토한 후 **다음** 을 선택합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/20-listener.png" alt-text="수신기 페이지에서 값을 검토한 후 다음을 선택함":::

1. **초기 데이터 동기화 선택** 페이지에서 **전체 데이터베이스 및 로그 백업** 을 선택하고, [이전에 만든 네트워크 공유 위치](#create-file-share)(예: `\\SQL-VM-1\Backup`)를 제공합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/21-full-data-sync.png" alt-text="전체 데이터 동기화 선택":::

   > [!NOTE]
   > 전체 동기화는 SQL Server의 첫 번째 인스턴스에서 데이터베이스의 전체 백업을 수행하고 두 번째 인스턴스로 복원합니다. 대형 데이터베이스의 경우 전체 동기화는 시간이 오래 걸릴 수 있으므로 권장되지 않습니다. 수동으로 데이터베이스의 백업을 수행하고 `NO RECOVERY`를 통해 복원하여 이 시간을 줄일 수 있습니다. 가용성 그룹을 구성하기 전에 이미 두 번째 SQL Server에서 `NO RECOVERY`를 사용하여 데이터베이스를 복원한 경우 **조인만** 을 선택합니다. 가용성 그룹을 구성한 후 백업을 수행하려면 **초기 데이터 동기화 건너뛰기** 를 선택합니다.
   

1. **유효성 검사** 페이지에서 모든 유효성 검사가 통과했는지 확인하고, **다음** 을 선택합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/22-validation.png" alt-text="새 가용성 그룹 마법사, 유효성 검사":::

   
1. **요약** 페이지에서 **마침** 을 선택하고, 마법사에서 새 가용성 그룹을 구성할 때까지 기다립니다. **진행률** 페이지에서 **자세한 내용** 을 선택하여 자세한 진행 상황을 확인합니다. **결과** 페이지에서 **마법사가 완료되었습니다.** 가 표시되면 요약을 검사하여 가용성 그룹과 수신기가 성공적으로 만들어졌는지 확인합니다. 

     :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/23-results.png" alt-text="새 가용성 그룹 마법사, 결과":::

1. **닫기** 를 선택하여 마법사를 종료합니다.

## <a name="check-availability-group"></a>가용성 그룹 확인

**SQL Server Management Studio** 및 **장애 조치(failover) 클러스터 관리자** 를 사용하여 가용성 그룹의 상태를 확인할 수 있습니다.  

가용성 그룹의 상태를 확인하려면 다음 단계를 수행합니다. 

1. **개체 탐색기** 에서 **Always On 고가용성**, **가용성 그룹** 을 차례로 펼칩니다. 이 컨테이너에 새 가용성 그룹이 표시됩니다. 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **대시보드 표시** 를 선택합니다.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/24-show-dashboard.png" alt-text="가용성 그룹 대시보드 표시":::

   가용성 그룹 대시보드에는 다음 예와 같이 복제본, 각 복제본의 장애 조치(failover) 모드 및 동기화 상태가 표시됩니다. 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/25-ag-dashboard.png" alt-text="가용성 그룹 대시보드":::


2. **장애 조치(failover) 클러스터 관리자** 를 열고, 클러스터를 선택한 다음, **역할** 을 선택하여 클러스터 내에서 만든 가용성 그룹 역할을 확인합니다. **AG1** 역할을 선택하고, **리소스** 탭을 선택하여 다음 예와 같이 수신기 및 연결된 IP 주소를 확인합니다. 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/26-cluster-manager.png" alt-text="장애 조치(failover) 클러스터 관리자의 가용성 그룹":::

이 시점에서 복제본이 SQL Server의 두 인스턴스에 있는 가용성 그룹과 해당 가용성 그룹 수신기도 있습니다. 수신기를 사용하여 연결할 수 있으며, **SQL Server Management Studio** 를 사용하여 가용성 그룹을 인스턴스 간에 이동할 수 있습니다. 

> [!WARNING]
> 장애 조치(failover) 클러스터 관리자를 사용하여 가용성 그룹을 장애 조치(failover)하지 마세요. 모든 장애 조치(failover) 작업은 **Always On 대시보드** 또는 Transact-SQL(T-SQL)을 사용하는 것과 같이 **SQL Server Management Studio** 내에서 수행해야 합니다. 자세한 내용은 [가용성 그룹에서 장애 조치(failover) 클러스터 관리자 사용에 대한 제한 사항](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server)을 참조하세요.



## <a name="test-listener-connection"></a>수신기 연결 테스트

가용성 그룹이 준비되고 수신기가 적절한 보조 IP 주소로 구성되면 수신기에 대한 연결을 테스트합니다.  

연결을 테스트하려면 다음 단계를 수행합니다. 

1. RDP를 사용하여 동일한 가상 네트워크에 있지만 복제본을 소유하지 않는 SQL Server(예: 클러스터 내의 다른 SQL Server 인스턴스 또는 **SQL Server Management Studio** 가 설치된 다른 VM)에 연결합니다.  

2. **SQL Server Management Studio** 를 열고, **서버에 연결** 대화 상자의 **서버 이름:** 에서 수신기 이름(예: **AG1-Listener**)을 입력한 다음, **옵션** 을 선택합니다. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/27-ssms-listener-connect.png" alt-text="SQL Server Management Studio를 열고, 서버 이름:에서 AG1-Listener와 같은 수신기 이름을 입력함":::

3. **추가 연결 매개 변수** 창에 `MultiSubnetFailover=True`를 입력한 다음, **연결** 을 선택하여 주 SQL Server 복제본을 호스트하는 인스턴스에 자동으로 연결합니다.  

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/28-ssms-connection-parameters.png" alt-text="SSMS 연결":::

> [!NOTE]
> - 다른 서브넷의 가용성 그룹에 연결하는 동안 `MultiSubnetFailover=true`를 설정하면 현재 주 복제본을 더 빠르게 검색하여 연결할 수 있습니다. [MultiSubnetFailover로 연결](/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#connecting-with-multisubnetfailover)을 참조하세요.
> - .NET Framework 4.6.1 이상 버전에는 `MultiSubnetFailover=True` 설정이 필요하지 않습니다.

## <a name="next-steps"></a>다음 단계


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)