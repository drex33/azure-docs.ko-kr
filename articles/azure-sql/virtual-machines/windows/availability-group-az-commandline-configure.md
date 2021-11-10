---
title: 가용성 그룹 구성(PowerShell 및 Az CLI)
description: PowerShell 또는 Azure CLI를 사용하여 Azure의 SQL Server VM에 Windows 장애 조치(failover) 클러스터, 가용성 그룹 수신기 및 내부 부하 분산 장치를 만듭니다.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 6607760ad11290daf0488a06357432bf5eb32306
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157832"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>PowerShell 또는 Azure CLI를 사용하여 Azure VM에 SQL Server에 대한 가용성 그룹 구성 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내에서 [여러 서브넷](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) 에 SQL Server vm을 만들어 AG (Always On availability) 그룹에 대 한 Azure Load Balancer 필요성을 제거 합니다.

이 문서에서는 [PowerShell](/powershell/scripting/install/installing-powershell) 또는 [Azure CLI](/cli/azure/sql/vm) 를 사용 하 여 Windows 장애 조치 (failover) 클러스터를 배포 하 고, 클러스터에 SQL Server vm을 추가 하 고, 단일 서브넷 내에서 Always On 가용성 그룹에 대 한 내부 부하 분산 장치 및 수신기를 만드는 방법을 설명 합니다. 

가용성 그룹 배포는 SSMS(SQL Server Management Studio) 또는 T-SQL(Transact-SQL)을 통해 계속 수동으로 수행됩니다. 

이 문서에서는 PowerShell 및 AZ CLI를 사용하여 가용성 그룹 환경을 구성하는 반면, [Azure Portal](availability-group-azure-portal-configure.md)에서 [Azure 빠른 시작 템플릿](availability-group-quickstart-template-configure.md)을 사용하거나 [수동으로](availability-group-manually-configure-tutorial-single-subnet.md) 구성할 수도 있습니다. 

> [!NOTE]
> 이제 Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 가용성 그룹 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [가용성 그룹 마이그레이션](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)을 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

Always On 가용성 그룹을 구성하려면 다음 필수 구성 요소가 있어야 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- [SQL IaaS 에이전트 확장에 등록](sql-agent-extension-manually-register-single-vm.md)된 *동일한* 가용성 집합 또는 *다른* 가용성 영역에서 [SQL Server 2016 이상 Enterprise Edition](./create-sql-vm-portal.md)을 실행하는 Azure의 하나 이상의 도메인 조인 VM.  
- 최신 버전의 [PowerShell](/powershell/scripting/install/installing-powershell) 또는 [Azure CLI](/cli/azure/install-azure-cli). 
- 2개의 사용 가능한(엔터티에서 사용하지 않음) IP 주소. 하나는 내부 부하 분산 장치용입니다. 다른 하나는 가용성 그룹과 동일한 서브넷 내의 가용성 그룹 수신기용입니다. 기존 부하 분산 장치를 사용하는 경우 가용성 그룹 수신기에 사용 가능한 IP 주소가 하나만 필요합니다. 

## <a name="permissions"></a>사용 권한

Azure CLI를 사용하여 Always On 가용성 그룹을 구성하려면 다음 계정 권한이 필요합니다. 

- 도메인에서 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정. 예를 들어 도메인 관리자 계정에는 일반적으로 충분한 권한이 있습니다(예: account@domain.com). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.
- SQL Server를 제어하는 도메인 사용자 계정. 
 
## <a name="create-a-storage-account"></a>스토리지 계정 만들기 

클러스터에는 클라우드 감시 역할을 할 스토리지 계정이 필요합니다. 기존 스토리지 계정을 사용하거나 새 스토리지 계정을 만들 수 있습니다. 기존 스토리지 계정을 사용하려면 다음 섹션으로 건너뜁니다. 

다음은 스토리지 계정을 만드는 코드 조각입니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 오래된 버전의 Azure CLI를 사용하는 경우 `az sql: 'vm' is not in the 'az sql' command group`이라는 오류가 표시될 수 있습니다. 이 오류를 해결하려면 [최신 버전의 Azure CLI](/cli/azure/install-azure-cli-windows)를 다운로드합니다.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly $true

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly $true
```

---

## <a name="define-cluster-metadata"></a>클러스터 메타데이터 정의

Azure CLI [az sql vm group](/cli/azure/sql/vm/group) 명령 그룹은 가용성 그룹을 호스팅하는 WSFC(Windows Server 장애 조치(Failover) 클러스터) 서비스의 메타데이터를 관리합니다. 클러스터 메타데이터에는 Active Directory 도메인, 클러스터 계정, 클라우드 감시로 사용되는 스토리지 계정 및 SQL Server 버전이 포함됩니다. [az sql vm group create](/cli/azure/sql/vm/group#az_sql_vm_group_create)를 사용하여 첫 번째 SQL Server VM이 추가될 때 클러스터가 정의된 대로 만들어지도록 WSFC의 메타데이터를 정의합니다. 

다음은 클러스터의 메타데이터를 정의하는 코드 조각입니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$storageAccountPrimaryKey = ConvertTo-SecureString -String "<PublicKey>" -AsPlainText -Force
$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey $storageAccountPrimaryKey
```

---

## <a name="add-vms-to-the-cluster"></a>클러스터에 VM 추가

클러스터에 첫 번째 SQL Server VM을 추가하면 클러스터가 만들어집니다. [az sql vm add-to-group](/cli/azure/sql/vm#az_sql-vm_add_to_group) 명령은 이전에 지정한 이름으로 클러스터를 만들고, SQL Server VM에 클러스터 역할을 설치하고, 클러스터에 SQL Server VM을 추가합니다. 이후에 `az sql vm add-to-group` 명령을 사용하면 새로 만든 클러스터에 더 많은 SQL Server VM이 추가됩니다. 

다음은 클러스터를 만들고 클러스터에 첫 번째 SQL Server VM를 추가하는 코드 조각입니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
다른 SQL Server VM을 클러스터에 추가하려면 이 명령을 사용합니다. SQL Server VM 이름에 대한 `-n` 매개 변수만 수정합니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---

## <a name="configure-quorum"></a>쿼럼 구성

디스크 감시는 가장 탄력적으로 수행되는 쿼럼 옵션이지만 가용성 그룹에 몇 가지 제한을 적용하는 Azure 공유 디스크가 필요합니다. 따라서 클라우드 감시는 Azure VM의 SQL Server에 대한 가용성 그룹을 호스트하는 클러스터에 권장되는 쿼럼 솔루션입니다. 

클러스터에 짝수 투표가 있는 경우 비즈니스 요구에 가장 적합한 [쿼럼 솔루션](hadr-cluster-quorum-configure-how-to.md)을 구성합니다. 자세한 내용은 [SQL Server VM에 대한 쿼럼](hadr-windows-server-failover-cluster-overview.md#quorum)을 참조하세요. 


## <a name="validate-cluster"></a>클러스터 유효성 검사 

Microsoft에서 지원하는 장애 조치(failover) 클러스터를 사용하려면 클러스터 유효성 검사를 통과해야 합니다. RDP(원격 데스크톱 프로토콜)와 같은 선호하는 방법을 사용하여 VM에 연결하고 클러스터가 유효성 검사를 통과했는지 확인한 다음 계속 진행합니다. 이렇게 하지 않으면 클러스터가 지원되지 않는 상태가 됩니다. 

FCM(장애 조치(Failover) 클러스터 관리자)를 사용하거나 다음 PowerShell 명령을 사용하여 클러스터 유효성을 검사할 수 있습니다.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>가용성 그룹 만들기

[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) 또는 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)을 사용하여 일반적인 방법으로 가용성 그룹을 직접 만듭니다. 

>[!IMPORTANT]
> 수신기는 다음 섹션에서 Azure CLI를 통해 만들어지므로 만들지 *않습니다*.  

## <a name="create-internal-load-balancer"></a>내부 부하 분산 장치 만들기

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Always On 가용성 그룹 수신기를 사용하려면 Azure Load Balancer의 내부 인스턴스가 필요합니다. 내부 부하 분산 장치는 더 빠른 장애 조치(failover) 및 다시 연결을 허용하는 가용성 그룹 수신기에 대한 "부동" IP 주소를 제공합니다. 가용성 그룹의 SQL Server VM이 동일한 가용성 집합의 일부인 경우 기본 부하 분산 장치를 사용할 수 있습니다. 그렇지 않으면 표준 부하 분산 장치를 사용해야 합니다.  

> [!NOTE]
> 내부 부하 분산 장치는 SQL Server VM 인스턴스와 동일한 가상 네트워크에 있어야 합니다. 

다음은 내부 부하 분산 장치를 만드는 코드 조각입니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> 각 SQL Server VM에 대한 공용 IP 리소스에 표준 부하 분산 장치와 호환되는 표준 SKU가 있어야 합니다. VM 공용 IP 리소스의 SKU를 확인하려면 **리소스 그룹** 으로 이동하여 원하는 SQL Server VM에 대한 **공용 IP 주소** 리소스를 선택하고 **개요** 창의 **SKU** 아래에서 값을 찾습니다.  

## <a name="create-listener"></a>수신기 만들기

수동으로 가용성 그룹을 만든 후 [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create)를 사용하여 수신기를 만들 수 있습니다. 

*서브넷 리소스 ID* 는 가상 네트워크 리소스의 리소스 ID에 추가된 `/subnets/<subnetname>`의 값입니다. 서브넷 리소스 ID를 확인하려면 다음을 수행합니다.
   1. [Azure Portal](https://portal.azure.com)의 리소스 그룹으로 이동합니다. 
   1. 가상 네트워크 리소스를 선택합니다. 
   1. **설정** 창에서 **속성** 을 선택합니다. 
   1. 가상 네트워크의 리소스 ID를 확인하고 그 끝에 `/subnets/<subnetname>`을 추가하여 서브넷 리소스 ID를 만듭니다. 예를 들면 다음과 같습니다.
      - 가상 네트워크 리소스 ID는 `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`입니다.
      - 서브넷 이름은 `default`입니다.
      - 따라서 서브넷 리소스 ID는 `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`입니다.


다음은 가용성 그룹 수신기를 만드는 코드 조각입니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>복제본 수 수정 
Azure에서 호스팅되는 SQL Server VM에 가용성 그룹을 배포하면 더 복잡해집니다. 이제 리소스 공급자와 가상 머신 그룹이 리소스를 관리합니다. 따라서 가용성 그룹에서 복제본을 추가하거나 제거할 때 SQL Server VM에 대한 정보로 수신기 메타데이터를 업데이트하는 추가 단계가 있습니다. 가용성 그룹의 복제본 수를 수정하는 경우 [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) 명령을 사용하여 SQL Server VM의 메타데이터로 수신기도 업데이트해야 합니다. 


### <a name="add-a-replica"></a>복제본 추가

가용성 그룹에 새 복제본을 추가하려면 다음을 수행합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 클러스터 그룹에 SQL Server VM을 추가합니다.
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. SQL Server Management Studio를 사용하여 가용성 그룹 내의 복제본으로 SQL Server 인스턴스를 추가합니다.
1. 수신기에 SQL Server VM 메타데이터를 추가합니다.

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 클러스터 그룹에 SQL Server VM을 추가합니다.

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. SQL Server Management Studio를 사용하여 가용성 그룹 내의 복제본으로 SQL Server 인스턴스를 추가합니다.
1. 수신기에 SQL Server VM 메타데이터를 추가합니다.

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>복제본 제거

가용성 그룹에서 복제본을 제거하려면 다음을 수행합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. SQL Server Management Studio를 사용하여 가용성 그룹에서 복제본을 제거합니다. 
1. 수신기에서 SQL Server VM 메타데이터를 제거합니다.
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 클러스터에서 SQL Server VM을 제거하려면 다음을 수행합니다.
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. SQL Server Management Studio를 사용하여 가용성 그룹에서 복제본을 제거합니다. 
1. 수신기에서 SQL Server VM 메타데이터를 제거합니다.

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. 클러스터에서 SQL Server VM을 제거하려면 다음을 수행합니다.

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>수신기 제거
나중에 Azure CLI로 구성된 가용성 그룹 수신기를 제거해야 하는 경우 SQL IaaS 에이전트 확장을 이용해야 합니다. 수신기가 SQL IaaS 에이전트 확장을 통해 등록되었으므로 SQL Server Management Studio를 통해 수신기를 삭제하는 것만으로는 충분하지 않습니다. 

가장 좋은 방법은 Azure CLI에서 다음 코드 조각을 사용하여 SQL IaaS 에이전트 확장을 통해 삭제하는 것입니다. 이렇게 하면 SQL IaaS 에이전트 확장에서 가용성 그룹 수신기 메타데이터가 제거됩니다. 또한 가용성 그룹에서 수신기가 물리적으로 삭제됩니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>클러스터 제거

클러스터의 모든 노드를 제거하여 삭제한 다음 SQL IaaS 에이전트 확장에서 클러스터 메타데이터를 제거합니다. 이 작업은 Azure CLI 또는 PowerShell을 사용하여 수행할 수 있습니다. 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 클러스터의 모든 SQL Server VM을 제거합니다. 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

해당 VM이 클러스터에 있는 유일한 VM이면 클러스터가 삭제됩니다. 제거된 SQL Server VM 외에 클러스터에 다른 VM이 있으면 다른 VM은 제거되지 않고 클러스터도 삭제되지 않습니다. 

다음으로 SQL IaaS 에이전트 확장에서 클러스터 메타데이터를 제거합니다. 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
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

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>다음 단계

가용성 그룹이 배포되면 [Azure VM에서 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)을 최적화하는 것이 좋습니다. 


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
