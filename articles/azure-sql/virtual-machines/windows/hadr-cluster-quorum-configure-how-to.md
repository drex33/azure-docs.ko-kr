---
title: 클러스터 쿼럼 구성
description: 'Azure VM에서 SQL Server의 Windows Server 장애 조치(failover) 클러스터에 대한 쿼럼으로 디스크 감시, 클라우드 감시 또는 파일 공유 감시를 구성하는 방법을 알아봅니다. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 575e8a5ab43e38b723bd0b2084709a1216b477fe
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163431"
---
# <a name="configure-cluster-quorum-for-sql-server-on-azure-vms"></a>Azure VM에서 SQL Server를 위한 클러스터 쿼럼 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VM(Virtual Machines)의 SQL Server에서 실행되는 Windows Server 장애 조치(failover) 클러스터에 대해 디스크 감시, 클라우드 감시 및 파일 공유 감시의 세 가지 쿼럼 옵션 중 하나를 구성하는 방법을 설명합니다.


## <a name="overview"></a>개요

클러스터의 쿼럼은 클러스터가 제대로 시작되거나 계속 실행되기 위해 활성 클러스터 멤버 자격의 일부여야 하는 투표 요소의 수에 따라 결정됩니다. 쿼럼 리소스를 구성하면 2-노드 클러스터는 하나의 노드만 온라인 상태로 계속 실행할 수 있습니다. Windows Server 장애 조치(failover) 클러스터는 Azure VM의 SQL Server 고가용성 옵션인 [FCI(장애 조치(failover) 클러스터 인스턴스)](failover-cluster-instance-overview.md) 및 [AG(가용성 그룹)](availability-group-overview.md)의 기본 기술입니다. 

디스크 감시는 가장 복원력이 높은 쿼럼 옵션이지만 Azure VM의 SQL Server에서 디스크 감시를 사용하려면 고가용성 솔루션에 몇 가지 제한 사항을 적용하는 Azure 공유 디스크를 사용해야 합니다. 따라서 Azure 공유 디스크를 사용하여 장애 조치(failover) 클러스터 인스턴스를 구성하는 경우 디스크 감시를 사용하고, 그 외에는 가능한 경우 클라우드 감시를 사용합니다. 클라우드 감시를 지원하지 않는 Windows Server 2012 R2 이하를 사용하는 경우 파일 공유 감시를 사용할 수 있습니다. 

Azure VM의 SQL Server에 사용할 수 있는 쿼럼 옵션은 다음과 같습니다. 

|  |[클라우드 감시](/windows-server/failover-clustering/deploy-cloud-witness) |[디스크 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[파일 공유 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**지원되는 OS**| Windows Server 2016+ |모두 | 모두|

쿼럼에 대해 자세히 알아보려면 [Windows Server 장애 조치(failover) 클러스터 개요](hadr-windows-server-failover-cluster-overview.md)를 참조하세요. 

## <a name="cloud-witness"></a>클라우드 감시

클라우드 감시는 Microsoft Azure Storage를 사용하여 클러스터 쿼럼에 대한 투표를 제공하는 장애 조치(failover) 클러스터 쿼럼 감시의 한 유형입니다. 


다음 표에는 클라우드 감시에 대한 추가 정보 및 고려 사항이 나와 있습니다. 

| 감시 유형  | Description  | 요구 사항 및 권장 사항  |
| ---------    |---------        |---------                        |
| 클라우드 감시     |  <ul><li> Azure Storage를 클라우드 감시로 사용하며 타임스탬프를 포함합니다. </li><li> 여러 사이트, 여러 영역 및 여러 지역의 배포에 이상적입니다.</li> <li> Microsoft 스토리지 계정 아래에 잘 알려진 `msft-cloud-witness` 컨테이너를 만듭니다. </li> <li> 컨테이너 아래 Blob 파일의 파일 이름으로 사용되는 해당 클러스터의 고유 ID를 사용하여 단일 Blob 파일을 씁니다. </li>      |  <ul><li>기본 크기는 1MB입니다.</li><li> 계정 종류에 **범용** 을 사용합니다. Blob Storage는 지원되지 않습니다. </li><li> 표준 스토리지를 사용합니다. Azure Premium Storage는 지원되지 않습니다. </li><li> 장애 조치(failover) 클러스터링에서는 Blob 파일을 중재 지점으로 사용하므로 데이터를 읽을 때 일관성을 보장해야 합니다. 따라서 **복제** 유형에 대해 **로컬 중복 스토리지** 를 선택해야 합니다.</li><li> 백업 및 바이러스 백신 검사에서 제외해야 함</li><li> 디스크 감시는 스토리지 공간 다이렉트에서 지원되지 않습니다.</li> <li> 클라우드 감시는 HTTPS(기본 포트 443)를 사용하여 Azure Blob Service와의 통신을 설정합니다. 네트워크 프록시를 통해 HTTPS 포트에 액세스할 수 있는지 확인합니다. </li>|

장애 조치(failover) 클러스터에 대한 클라우드 감시 쿼럼 리소스를 구성할 때 다음을 고려합니다.
- 액세스 키를 저장하는 대신 장애 조치(failover) 클러스터는 SAS(공유 액세스 보안) 토큰을 생성하고 안전하게 저장합니다.
- 생성된 SAS 토큰은 액세스 키가 유효한 상태로 유지되는 한 유효합니다. 기본 액세스 키를 회전하는 경우 기본 액세스 키를 다시 생성하기 전에 먼저 보조 액세스 키로 클라우드 감시(해당 스토리지 계정을 사용하는 모든 클러스터에서)를 업데이트해야 합니다.
- 클라우드 감시는 Azure Storage 계정 서비스의 HTTPS REST 인터페이스를 사용합니다. 즉, 모든 클러스터 노드에서 HTTPS 포트를 열어야 합니다.


클라우드 감시에는 Azure Storage 계정이 필요합니다. 스토리지 계정을 구성하려면 다음 단계를 따릅니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 새로 만들기 -> 데이터 + 저장소 -> 저장소 계정을 선택합니다.
3. 스토리지 계정 만들기 페이지에서 다음을 수행합니다.
    1. 스토리지 계정의 이름을 입력합니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 스토리지 계정 이름은 Azure 내에서 고유해야 합니다.
    2. **계정 종류** 에서 **범용** 을 선택합니다.
    3. **성능** 은 **표준** 을 선택합니다.
    2. **복제** 의 경우 **LRS(로컬 중복 스토리지)** 를 선택합니다.


스토리지 계정이 만들어지면 다음 단계에 따라 장애 조치(failover) 클러스터에 대한 클라우드 감시 쿼럼 리소스를 구성합니다. 


# <a name="powershell"></a>[PowerShell](#tab/powershell)

기존 Set-ClusterQuorum PowerShell 명령에는 클라우드 감시에 해당하는 새 매개 변수가 있습니다.

PowerShell 명령을 사용하여 cmdlet [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum)으로 클라우드 감시를 구성할 수 있습니다.

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey>
```

드문 경우지만 다른 엔드포인트를 사용해야 하는 경우 다음 PowerShell 명령을 사용합니다. 

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey> -Endpoint <servername>
```

스토리지 계정 액세스 키를 찾는 데 도움이 필요한 경우 [클라우드 감시 설명서](/windows-server/failover-clustering/deploy-cloud-witness)를 참조하세요. 


# <a name="failover-cluster-manager"></a>[장애 조치(failover) 클러스터 관리자](#tab/fcm-gui)

장애 조치(Failover) 클러스터 관리자 기본 제공 쿼럼 구성 마법사를 사용하여 클라우드 감시를 구성합니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. 장애 조치(failover) 클러스터 관리자를 엽니다.

2. 클러스터 -> **추가 작업** -> **클러스터 쿼럼 설정 구성** 을 마우스 오른쪽 단추로 클릭합니다. 그러면 클러스터 쿼럼 구성 마법사가 시작됩니다.

    ![장애 조치(failover) 클러스터 관리자 UI에서 클러스터 쿼럼 설정 구성의 메뉴 경로 스냅샷](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-7.png)
    
3. **쿼럼 구성 선택** 페이지에서 **쿼럼 감시 선택** 을 선택합니다.

    ![클러스터 쿼럼 마법사의 ‘쿼럼 감시 선택’ 라디오 단추의 스냅샷](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-8.png)
   
4. **쿼럼 감시 선택** 페이지에서 **클라우드 감시 구성** 을 선택합니다.

    ![클라우드 감시를 선택하는 적절한 라디오 단추의 스냅샷](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-9.png)
    
5. **클라우드 감시 구성** 페이지에서 Azure Storage 계정 정보를 입력합니다. 이 정보를 찾는 데 도움이 필요한 경우 [클라우드 감시 설명서](/windows-server/failover-clustering/deploy-cloud-witness)를 참조하세요. 
   1. (필수 매개 변수) Azure Storage 계정 이름.
   2. (필수 매개 변수) 스토리지 계정에 해당하는 액세스 키.
       1. 처음으로 만들 때 기본 액세스 키를 사용합니다. 
       2. 기본 액세스 키를 회전할 때 보조 액세스 키를 사용합니다.
   3. (선택적 매개 변수) 다른 Azure 서비스 엔드포인트(예: 중국의 Microsoft Azure 서비스)를 사용하려는 경우 엔드포인트 서버 이름을 업데이트합니다.

    ![클러스터 쿼럼 마법사의 클라우드 감시 구성 창의 스냅샷](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-10.png)
      

6. 클라우드 감시가 성공적으로 구성되면 장애 조치(failover) 클러스터 관리자 스냅인에서 새로 만든 감시 리소스를 볼 수 있습니다.

    ![클라우드 감시의 성공적인 구성](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-11.png)
    


---


## <a name="disk-witness"></a>디스크 감시

디스크 감시는 클러스터에서 사용 가능한 스토리지 그룹의 작은 클러스터형 디스크입니다. 이 디스크는 항상 사용 가능하며 노드 간에 장애 조치(failover)가 가능합니다. 

디스크 감시는 Azure 공유 디스크를 사용하는 장애 조치(failover) 클러스터 인스턴스와 같은 공유 스토리지 고가용성 솔루션과 함께 사용할 때 권장되는 쿼럼 옵션입니다. 

다음 표에는 쿼럼 디스크 감시에 대한 추가 정보 및 고려 사항이 나와 있습니다. 

| 감시 유형  | Description  | 요구 사항 및 권장 사항  |
| ---------    |---------        |---------                        |
| 디스크 감시     |  <ul><li> 클러스터 데이터베이스의 복사본을 저장하는 전용 LUN</li><li> 복제되지 않은 공유 스토리지를 사용하는 클러스터에 가장 유용함</li>       |  <ul><li>LUN 크기는 최소 512MB여야 함</li><li> 클러스터에만 사용되고 클러스터된 역할에 할당되지 않아야 함</li><li> 클러스터된 스토리지에 포함되고 스토리지 유효성 검사 테스트를 통과해야 함</li><li> CSV(클러스터 공유 볼륨)인 디스크일 수 없음</li><li> 단일 볼륨이 있는 기본 디스크</li><li> 드라이브 문자가 필요 없음</li><li> NTFS 또는 ReFS로 포맷할 수 있음</li><li> 필요한 경우 내결함성을 위해 하드웨어 RAID로 구성할 수 있음</li><li> 백업 및 바이러스 백신 검사에서 제외해야 함</li><li> 디스크 감시는 스토리지 공간 다이렉트에서 지원되지 않습니다.</li>|

디스크 감시에 Azure 공유 디스크를 사용하려면 먼저 디스크를 만들고 탑재해야 합니다. 이렇게 하려면 Azure 공유 디스크 장애 조치(failover) 클러스터 인스턴스 가이드의 [디스크 탑재](failover-cluster-instance-azure-shared-disks-manually-configure.md#add-azure-shared-disk) 섹션에 있는 단계를 수행합니다. 디스크는 프리미엄일 필요가 없습니다. 

디스크가 탑재된 후 다음 단계에 따라 클러스터 스토리지에 추가합니다. 

1. 장애 조치(failover) 클러스터 관리자를 엽니다. 
1. 왼쪽 탐색 창의 **스토리지** 아래에서 **디스크** 를 선택합니다. 
1. 오른쪽 탐색 창의 **작업** 아래에서 **디스크 추가** 를 선택합니다. 
1. 방금 탑재한 Azure 공유 드라이브를 선택하고 이름을 적어둡니다(예: `Cluster Disk 3`). 

디스크가 클러스터형 스토리지로 추가되면 PowerShell을 사용하여 디스크 감시로 구성합니다.  


기존 Set-ClusterQuorum PowerShell 명령에는 클라우드 감시에 해당하는 새 매개 변수가 있습니다.

PowerShell cmdlet [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum)을 사용할 때 파일 공유의 경로를 디스크 감시의 매개 변수로 사용합니다.

```PowerShell
Set-ClusterQuorum -NodeAndDiskMajority "Cluster Disk 3"
```

장애 조치(failover) 클러스터 관리자를 사용할 수도 있습니다. 클라우드 감시와 동일한 단계를 따르지만 대신 디스크 감시를 쿼럼 옵션으로 선택합니다. 


## <a name="file-share-witness"></a>파일 공유 감시

파일 공유 감시는 일반적으로 Windows Server를 실행하는 파일 서버에 구성되는 SMB 파일 공유입니다. 이는 witness.log 파일에 클러스터링 정보를 유지하지만 클러스터 데이터베이스의 복사본은 저장하지 않습니다. Azure에서는 개별 가상 머신에 파일 공유를 구성할 수 있습니다. 

사용자 환경에서 디스크 감시 또는 클라우드 감시를 사용할 수 없거나 지원되지 않는 경우 파일 공유 감시를 구성합니다. 

다음 표에는 쿼럼 파일 공유 감시에 대한 추가 정보 및 고려 사항이 나와 있습니다. 

| 감시 유형  | Description  | 요구 사항 및 권장 사항  |
| ---------    |---------        |---------                        |
| 파일 공유 감시     | <ul><li>Windows Server를 실행하는 파일 서버에 구성되는 SMB 파일 공유</li><li> 클러스터 데이터베이스의 복사본을 저장하지 않음</li><li> witness.log 파일에만 클러스터 정보를 유지 관리함</li><li> 복제된 스토리지를 사용하는 다중 사이트 클러스터에 가장 유용함 </li>       |  <ul><li>최소 5MB의 사용 가능한 공간이 있어야 함</li><li> 단일 클러스터에만 사용해야 하며, 사용자 또는 애플리케이션 데이터를 저장할 수 없음</li><li> 컴퓨터 개체에 클러스터 이름에 대한 쓰기 권한이 있어야 함</li></ul><br>파일 공유 감시를 호스트하는 파일 서버에 대한 추가 고려 사항:<ul><li>여러 클러스터에 대한 파일 공유 감시로 단일 파일 서버를 구성할 수 있습니다.</li><li> 파일 서버는 클러스터 작업과 별도의 사이트에 있어야 합니다. 이렇게 하면 사이트 간 네트워크 통신이 끊어진 경우 모든 클러스터 사이트에 동등한 존속 기회가 제공됩니다. 파일 서버가 동일한 사이트에 있는 경우 해당 사이트가 기본 사이트가 되며 이 사이트에서만 파일 공유에 연결할 수 있습니다.</li><li> 가상 컴퓨터가 파일 공유 감시를 사용하는 동일한 클러스터에서 호스트되지 않는 경우 가상 컴퓨터에서 파일 서버를 실행할 수 있습니다.</li><li> 고가용성을 위해 별도의 장애 조치(failover) 클러스터에서 파일 서버를 구성할 수 있습니다. </li>      |

파일 공유를 만들고 권한을 올바르게 구성한 후에는 클러스터형 노드에 파일 공유를 탑재합니다. 프리미엄 파일 공유 장애 조치(failover) 클러스터 인스턴스 방법 가이드의 [파일 공유 탑재](failover-cluster-instance-premium-file-share-manually-configure.md) 섹션에 설명된 대로 동일한 일반 단계에 따라 파일 공유를 탑재할 수 있습니다. 

파일 공유가 제대로 구성되고 탑재된 후 PowerShell을 사용하여 파일 공유를 쿼럼 감시 리소스로 추가합니다. 

```powershell
Set-ClusterQuorum -FileShareWitness <UNC path to file share> -Credential $(Get-Credential)
```

공유에 대한 전체 관리자 권한이 있는 관리자가 아닌 로컬 계정(파일 공유)에 대한 계정 및 암호를 입력하라는 메시지가 표시됩니다.  클러스터는 이름과 암호를 암호화된 상태로 유지하며 다른 사용자가 액세스할 수 없습니다.

장애 조치(failover) 클러스터 관리자를 사용할 수도 있습니다. 클라우드 감시와 동일한 단계를 따르지만 대신 파일 공유를 쿼럼 옵션으로 선택합니다. 

## <a name="change-quorum-voting"></a>쿼럼 투표 변경


Windows Server 장애 조치(Failover) 클러스터에 참가하는 노드의 쿼럼 투표를 변경할 수 있습니다. 

노드 투표 설정을 수정하는 경우 다음 지침을 따르세요. 

| 쿼럼 투표 지침 |
|-|
| 기본적으로 투표가 없는 각 노드에서 시작합니다. 각 노드에는 명시적 타당성을 갖춘 투표만 있어야 합니다.|
| 가용성 그룹의 주 복제본 또는 장애 조치(failover) 클러스터 인스턴스의 기본 설정 소유자를 호스트하는 클러스터 노드에 대한 투표를 사용하도록 설정합니다. |
| 자동 장애 조치(failover) 소유자에 투표를 사용하도록 설정합니다. 자동 장애 조치(failover)로 인해 주 복제본 또는 FCI를 호스팅할 수 있는 각 노드에는 투표가 있어야 합니다. | 
| 가용성 그룹에 보조 복제본이 두 개 이상 있는 경우 자동 장애 조치(failover)가 있는 복제본에 대해서만 투표를 사용하도록 설정합니다. | 
| 보조 재해 복구 사이트에 있는 노드에 대한 투표를 사용하지 않도록 설정합니다. 기본 사이트에 문제가 없는 경우 보조 사이트의 노드가 클러스터를 오프라인으로 전환하는 의사 결정에 영향을 주지 않아야 합니다. | 
| 최소 3개의 쿼럼 투표를 포함하여 투표 수를 홀수로 합니다. 2노드 클러스터에서 필요한 경우 추가 투표를 위해 [쿼럼 감시](hadr-cluster-quorum-configure-how-to.md)를 추가합니다. | 
| 투표 할당 사후 장애 조치(failover)를 다시 평가합니다. 정상 상태의 쿼럼을 지원하지 않는 클러스터 구성에 대해서는 장애 조치(failover)를 수행할 필요가 없습니다. |




## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)
- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
