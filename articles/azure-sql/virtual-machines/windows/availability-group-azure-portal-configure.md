---
title: 가용성 그룹 구성(Azure Portal)
description: Azure Portal을 사용하여 Azure의 SQL Server VM에 Windows 장애 조치(failover) 클러스터, 가용성 그룹 수신기 및 내부 부하 분산 장치를 만듭니다.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: aeaeffec318ccf44e1557d592bd470556275ebe4
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158776"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Azure Portal을 사용하여 Azure VM에서 SQL Server에 대한 가용성 그룹(미리 보기) 구성 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내의 [여러 서브넷에](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) SQL Server VM을 만들어 AG(Always On 가용성) 그룹에 대한 Azure Load Balancer 필요하지 않습니다.

이 문서에서는 [Azure Portal](https://portal.azure.com) 사용하여 단일 서브넷 내의 Azure VM에서 SQL Server 가용성 그룹을 구성하는 방법을 설명합니다. 

Azure Portal를 사용하여 새 클러스터를 만들거나 기존 클러스터를 온보딩한 다음 가용성 그룹, 수신기 및 내부 부하 분산 장치를 만듭니다. 

이 기능은 현재 미리 보기로 제공됩니다. 

이 문서에서는 Azure Portal을 사용하여 가용성 그룹 환경을 구성하지만, [PowerShell 또는 Azure CLI](availability-group-az-commandline-configure.md), [Azure 빠른 시작 템플릿](availability-group-quickstart-template-configure.md)을 사용하거나 [수동으로](availability-group-manually-configure-tutorial-single-subnet.md)도 구성할 수 있습니다. 

> [!NOTE]
> 이제 Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 가용성 그룹 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [가용성 그룹 마이그레이션](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)을 참조하세요. 


## <a name="prerequisites"></a>사전 요구 사항

Azure Portal을 사용하여 Always On 가용성 그룹을 구성하려면 다음 사전 요구 사항이 있어야 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- *동일한* 가용성 집합 또는 [전체 관리 모드에서 SQL IaaS 에이전트 확장으로 등록](sql-agent-extension-manually-register-single-vm.md)되고 각 VM의 SQL Server 서비스에 대해 동일한 도메인 계정을 사용하는 *다른* 가용성 영역에서 [SQL Server 2016 이상 Enterprise Edition을 실행하는 Azure의 하나 이상의 도메인 가입 VM](./create-sql-vm-portal.md).
- 2개의 사용 가능한(엔터티에서 사용하지 않음) IP 주소. 하나는 내부 부하 분산 장치용입니다. 다른 하나는 가용성 그룹과 동일한 서브넷 내의 가용성 그룹 수신기용입니다. 기존 부하 분산 장치를 사용하는 경우 가용성 그룹 수신기에 사용 가능한 IP 주소가 하나만 필요합니다. 

## <a name="permissions"></a>권한

Azure Portal을 사용하여 가용성 그룹을 구성하려면 다음 계정 권한이 필요합니다. 

- 도메인에서 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정. 예를 들어 도메인 관리자 계정에는 일반적으로 충분한 권한이 있습니다(예: account@domain.com). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.
- SQL Server를 제어하는 도메인 사용자 계정. 이 계정은 가용성 그룹에 추가하려는 모든 SQL Server VM에 대해 동일한 계정이어야 합니다. 

## <a name="configure-cluster"></a>클러스터 구성

Azure Portal을 사용하여 클러스터 구성. 새 클러스터를 만들거나 기존 클러스터가 이미 있는 경우 포털 관리 효율성을 위해 SQL IaaS 에이전트 확장에 이를 온보딩할 수 있습니다.


### <a name="create-a-new-cluster"></a>새 클러스터 만들기

클러스터가 이미 있는 경우 이 섹션을 건너뛰고 대신 [기존 클러스터 온보딩](#onboard-existing-cluster)으로 이동합니다. 

기존 클러스터가 아직 없는 경우 다음 단계에 따라 Azure Portal를 사용하여 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 머신](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동합니다. 
1. **설정** 아래에서 **고가용성** 을 선택합니다. 
1. **+ 새 Windows Server 장애 조치(failover) 클러스터** 를 선택하여 **Windows 장애 조치(Failover) 클러스터 구성** 페이지를 엽니다.  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="포털에서 + 새 클러스터를 선택하여 새 클러스터 만들기":::

1. 클러스터 이름을 지정하고 클라우드 감시로 사용할 스토리지 계정을 제공합니다. 기존 스토리지 계정을 사용하거나 **새로 만들기** 를 선택하여 새 스토리지 계정을 만듭니다. 스토리지 계정 이름은 3자에서 24자 사이여야 하며 숫자와 소문자만 사용해야 합니다.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="클러스터에 대한 이름, 스토리지 계정 및 자격 증명 제공":::

1. **Windows Server 장애 조치(Failover) 클러스터 자격 증명** 을 확장하여 SQL Server 서비스 계정에 대한 [자격 증명](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile)을 제공하고, SQL Server 서비스에 사용되는 계정과 다른 경우 클러스터 운영자 및 부트스트랩 계정도 제공합니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="SQL 서비스 계정, 클러스터 운영자 계정 및 클러스터 부트스트랩 계정에 대한 자격 증명 제공":::

1. 클러스터에 추가하려는 SQL Server VM을 선택합니다. 다시 시작해야 하는지 여부를 확인하고 주의해서 진행합니다. 완전한 관리 효율성 모드에서 SQL IaaS 에이전트 확장을 사용하여 등록되고 기본 SQL Server VM과 동일한 위치, 도메인 및 동일한 가상 네트워크에 있는 VM만 표시됩니다. 
1. **적용** 을 선택하여 클러스터를 만듭니다. 위쪽 탐색 모음의 종 모양 아이콘에서 액세스할 수 있는 **활동 로그** 에서 배포 상태를 확인할 수 있습니다. 
1. Microsoft에서 지원하는 장애 조치(failover) 클러스터를 사용하려면 클러스터 유효성 검사를 통과해야 합니다. RDP(원격 데스크톱 프로토콜)와 같은 선호하는 방법을 사용하여 VM에 연결하고 클러스터가 유효성 검사를 통과했는지 확인한 다음 계속 진행합니다. 이렇게 하지 않으면 클러스터가 지원되지 않는 상태가 됩니다. FCM(장애 조치(Failover) 클러스터 관리자)를 사용하거나 다음 PowerShell 명령을 사용하여 클러스터 유효성을 검사할 수 있습니다.

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>기존 클러스터 온보딩

SQL Server VM 환경에 구성된 클러스터가 이미 있는 경우 Azure Portal에서 온보딩할 수 있습니다.

이렇게 하려면 다음 단계를 따르십시오.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 머신](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동합니다. 
1. **설정** 아래에서 **고가용성** 을 선택합니다. 
1. **기존 Windows Server 장애 조치(failover) 클러스터 온보딩** 을 선택하여 **Windows Server 장애 조치(failover) 클러스터 온보딩** 페이지를 엽니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="SQL 가상 머신 리소스의 고가용성 페이지에서 기존 클러스터를 온보딩합니다.":::

1. 클러스터의 설정을 검토합니다. 
1. 클러스터를 온보딩하려면 **적용** 을 선택한 다음 프롬프트에서 **예** 를 선택하여 계속합니다.

## <a name="create-availability-group"></a>가용성 그룹 만들기

클러스터가 만들어지거나 온보딩된 후 Azure Portal를 사용하여 가용성 그룹을 만듭니다. 이렇게 하려면 다음 단계를 따르십시오.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 머신](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동합니다. 
1. **설정** 아래에서 **고가용성** 을 선택합니다. 
1. **+ 새 Always On 가용성 그룹** 을 선택하여 **가용성 그룹 만들기** 페이지를 엽니다.

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="새 Always On 가용성 그룹을 선택하여 가용성 그룹 만들기 페이지를 엽니다.":::

1. 가용성 그룹의 이름을 입력합니다. 
1. **수신기 구성** 을 선택하여 **가용성 그룹 수신기 구성** 페이지를 엽니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="가용성 그룹의 이름 제공 및 수신기 구성":::

1. 값을 입력하고 기존 부하 분산 장치를 사용하거나 **새로 만들기** 를 선택하여 새 부하 분산 장치를 만듭니다.  **적용** 을 선택하여 설정을 저장하고 수신기 및 부하 분산 장치를 만듭니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="양식의 값을 입력하여 새 수신기 및 부하 분산 장치를 만듭니다.":::

1. **+ 복제본 선택** 을 선택하여 **가용성 그룹 복제본 구성** 페이지를 엽니다.
1. 가용성 그룹에 추가하려는 가상 머신을 선택하고 비즈니스 요구에 가장 적합한 가용성 그룹 설정을 선택합니다. **적용** 을 선택하여 설정을 저장합니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="가용성 그룹에 추가할 VM 선택 및 비즈니스에 적합한 설정 구성":::

1. 가용성 그룹 설정을 확인한 다음 **적용** 을 선택하여 가용성 그룹을 만듭니다. 

위쪽 탐색 모음의 종 모양 아이콘에서 액세스할 수 있는 **활동 로그** 에서 배포 상태를 확인할 수 있습니다. 

  > [!NOTE]
  > 가용성 그룹에 데이터베이스를 추가할 때까지 Azure Portal의 **고가용성** 페이지에서 **동기화 상태** 가 **정상이 아님** 으로 표시됩니다. 


## <a name="add-database-to-availability-group"></a>가용성 그룹에 데이터베이스 추가

배포가 완료된 후 가용성 그룹에 데이터베이스를 추가합니다. 아래 단계에서는 SSMS(SQL Server Management Studio)를 사용하지만 [Transact-SQL 또는 PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database)도 사용할 수 있습니다. 

SQL Server Management Studio를 사용하여 가용성 그룹에 데이터베이스를 추가하려면 다음 단계를 수행합니다.

1. RDP(원격 데스크톱 연결)와 같은 선호하는 방법을 사용하여 SQL Server VM 중 하나에 연결합니다. 
1. SSMS(SQL Server Management Studio)를 엽니다.
1. SQL Server 인스턴스에 연결합니다. 
1. **개체 탐색기** 에서 **Always On 고가용성** 을 확장합니다.
1. **가용성 그룹** 을 확장하고 가용성 그룹을 마우스 오른쪽 단추로 클릭한 다음 **데이터베이스 추가...** 를 선택합니다.

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="개체 탐색기에서 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 데이터베이스 추가 선택":::

1. 프롬프트에 따라 가용성 그룹에 추가하려는 데이터베이스를 선택합니다. 
1. **확인** 을 선택하여 설정을 저장하고 데이터베이스를 가용성 그룹에 추가합니다. 
1. 데이터베이스가 추가된 후 **개체 탐색기** 를 새로 고쳐 데이터베이스 상태를 `synchronized`로 확인합니다. 

데이터베이스가 추가된 후 Azure Portal에서 가용성 그룹의 상태를 확인할 수 있습니다. 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="데이터베이스가 동기화된 후 Azure Portal의 고가용성 페이지에서 가용성 그룹의 상태 확인":::

## <a name="add-more-vms"></a>VM 추가

클러스터에 SQL Server VM을 더 추가하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 머신](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동합니다. 
1. **설정** 아래에서 **고가용성** 을 선택합니다. 
1. **Windows Server 장애 조치(failover) 클러스터 구성** 를 선택하여 **Windows Server 장애 조치(failover) 클러스터 구성** 페이지를 엽니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Windows Server 장애 조치(failover) 클러스터 구성을 선택하여 클러스터에 VM을 추가합니다.":::

1. **Windows Server 장애 조치(failover) 클러스터 자격 증명** 을 확장하고 SQL Server 서비스, 클러스터 운영자 및 클러스터 부트스트랩 계정에 사용되는 계정을 입력합니다. 
1. 클러스터에 추가하려는 SQL Server VM을 선택합니다. 
1. **적용** 을 선택합니다. 

위쪽 탐색 모음의 종 모양 아이콘에서 액세스할 수 있는 **활동 로그** 에서 배포 상태를 확인할 수 있습니다. 

## <a name="configure-quorum"></a>쿼럼 구성

디스크 감시는 가장 탄력적으로 수행되는 쿼럼 옵션이지만 가용성 그룹에 몇 가지 제한을 적용하는 Azure 공유 디스크가 필요합니다. 따라서 클라우드 감시는 Azure VM의 SQL Server에 대한 가용성 그룹을 호스트하는 클러스터에 권장되는 쿼럼 솔루션입니다. 

클러스터에 짝수 투표가 있는 경우 비즈니스 요구에 가장 적합한 [쿼럼 솔루션](hadr-cluster-quorum-configure-how-to.md)을 구성합니다. 자세한 내용은 [SQL Server VM에 대한 쿼럼](hadr-windows-server-failover-cluster-overview.md#quorum)을 참조하세요. 


## <a name="modify-availability-group"></a>가용성 그룹 수정 


가용성 그룹 옆에 있는 줄임표(...)를 선택하여 Azure Portal의 **고가용성** 페이지에서 가용성 그룹에 **복제본을 추가** 하고, **수신기를 구성** 하거나, **수신기를 삭제** 할 수 있습니다. 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="가용성 그룹 옆에 있는 줄임표를 선택한 다음 복제본 추가를 선택하여 가용성 그룹에복제본을 추가합니다.":::

## <a name="remove-cluster"></a>클러스터 제거

클러스터에서 모든 SQL Server VM을 제거하여 삭제한 다음 SQL IaaS 에이전트 확장에서 클러스터 메타데이터를 제거합니다. 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 PowerShell을 사용하여 이 작업을 수행할 수 있습니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 클러스터에서 모든 SQL Server VM을 제거합니다. 이렇게 하면 클러스터에서 노드가 물리적으로 제거되고 클러스터가 삭제됩니다.  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

이것이 클러스터에 있는 유일한 VM이면 클러스터가 삭제됩니다. 제거된 SQL Server VM 외에 클러스터에 다른 VM이 있으면 다른 VM은 제거되지 않고 클러스터도 삭제되지 않습니다. 

다음으로 SQL IaaS 에이전트 확장에서 클러스터 메타데이터를 제거합니다. 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

먼저 클러스터에서 모든 SQL Server VM을 제거합니다. 이렇게 하면 클러스터에서 노드가 물리적으로 제거되고 클러스터가 삭제됩니다. 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

이것이 클러스터에 있는 유일한 VM이면 클러스터가 삭제됩니다. 제거된 SQL Server VM 외에 클러스터에 다른 VM이 있으면 다른 VM은 제거되지 않고 클러스터도 삭제되지 않습니다. 


다음으로 SQL IaaS 에이전트 확장에서 클러스터 메타데이터를 제거합니다. 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>문제 해결

문제가 발생하는 경우 배포 기록을 확인하고 일반적인 오류 및 해결 방법을 검토할 수 있습니다. 

### <a name="check-deployment-history"></a>배포 기록 확인

포털을 통해 클러스터 및 가용성 그룹이 변경된 내용은 배포를 통해 완료됩니다. 클러스터를 만들거나 온보딩하거나 가용성 그룹을 만드는 데 문제가 있는 경우 배포 기록은 더 자세한 정보를 제공할 수 있습니다.

배포에 대한 로그를 확인하고 배포 기록을 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 리소스 그룹으로 이동합니다.
1. **설정** 아래에서 **배포** 를 선택합니다.
1. 배포에 대한 자세한 내용을 알아보려면 관심 있는 배포를 선택합니다. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="자세히 알아보려는 배포를 선택합니다." :::

### <a name="common-errors"></a>일반 오류

다음과 같은 일반적인 오류 및 해결 방법을 검토합니다. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>sql 서비스를 시작하는 데 사용되는 계정이 도메인 계정이 아님

이는 리소스 공급자가 제공된 자격 증명을 사용하여 SQL Server 서비스에 액세스할 수 없다는 의미입니다. 몇 가지 일반적인 해결 방법은 다음과 같습니다.
- 도메인 컨트롤러가 실행 중인지 확인합니다.
- 포털에 제공된 자격 증명이 SQL Server 서비스의 자격 증명과 일치하는지 확인합니다. 


## <a name="next-steps"></a>다음 단계

가용성 그룹이 배포되면 [Azure VM에서 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)을 최적화하는 것이 좋습니다. 


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
