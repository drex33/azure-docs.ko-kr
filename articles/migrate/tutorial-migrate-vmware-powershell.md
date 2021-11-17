---
title: VMware VM을 Azure로 마이그레이션(에이전트 없음) - PowerShell
description: Azure Migrate를 사용하여 PowerShell을 통해 VMware VM을 에이전트 없이 마이그레이션하는 방법에 대해 알아봅니다.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 08/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0f8b01e54fbb7173a6d9dfba8a99a9f9ffb1aeb9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292892"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>VMware VM을 Azure로 마이그레이션(에이전트 없음) - PowerShell

이 문서에서는 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool)에 대한 Azure PowerShell을 사용하는 에이전트 없는 방법을 통해 검색된 VMware VM을 마이그레이션하는 방법에 대해 알아봅니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate 프로젝트에서 검색된 VMware VM을 검색합니다.
> * VM 복제를 시작합니다.
> * VM 복제를 위한 속성을 업데이트합니다.
> * 복제를 모니터링합니다.
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인합니다.
> * 전체 VM 마이그레이션을 실행합니다.

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="1-prerequisites"></a>1. 사전 요구 사항

이 자습서를 시작하기 전에 다음을 수행해야 합니다.

1. [자습서: 서버 평가를 사용하여 VMware VM 검색](tutorial-discover-vmware.md)을 완료하여 마이그레이션할 Azure 및 VMware를 준비합니다.
2. [자습서: Azure VM으로 마이그레이션할 VMware VM 평가](./tutorial-assess-vmware-azure-vm.md)를 완료한 후 Azure로 마이그레이션합니다.
3. [Az PowerShell 모듈 설치](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. Azure Migrate PowerShell 모듈 설치

Azure Migrate PowerShell 모듈은 Azure PowerShell(`Az`)의 일부로 사용할 수 있습니다. `Get-InstalledModule -Name Az.Migrate` 명령을 실행하여 Azure Migrate PowerShell 모듈이 컴퓨터에 설치되어 있는지 확인합니다.  

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Microsoft Azure 구독에 로그인

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 구독에 로그인합니다.

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Azure 구독 선택

[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet을 사용하여 액세스 권한이 있는 Azure 구독 목록을 가져옵니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 작업할 Azure Migrate 프로젝트가 있는 Azure 구독을 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. Azure Migrate 프로젝트 검색

Azure Migrate 프로젝트는 평가하거나 마이그레이션하는 환경에서 수집된 검색, 평가 및 마이그레이션 메타데이터를 저장하는 데 사용됩니다.
프로젝트에서 검색된 자산을 추적하고, 평가를 오케스트레이션하고, 마이그레이션을 수행할 수 있습니다.

필수 구성 요소의 일부로 이미 Azure Migrate 프로젝트를 만들었습니다. [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) cmdlet을 사용하여 Azure Migrate 프로젝트의 세부 정보를 검색합니다. Azure Migrate 프로젝트의 이름(`Name`) 및 Azure Migrate 프로젝트의 리소스 그룹 이름(`ResourceGroupName`)을 지정해야 합니다.

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup
```
```azurepowershell-interactive
# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName
```
```azurepowershell-interactive
# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. Azure Migrate 프로젝트에서 검색된 VM 검색

Azure Migrate는 경량 [Azure Migrate 어플라이언스](migrate-appliance-architecture.md)를 사용합니다. 필수 구성 요소의 일부로 Azure Migrate 어플라이언스를 VMware VM으로 배포했습니다.

Azure Migrate 프로젝트에서 특정 VMware VM을 검색하려면 Azure Migrate 프로젝트의 이름(`ProjectName`), Azure Migrate 프로젝트의 리소스 그룹(`ResourceGroupName`) 및 VM 이름(`DisplayName`)을 지정합니다.


```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM | Format-Table DisplayName, Name, Type

# View discovered server details
Write-Output $DiscoveredServer
```

이 자습서에서 이 VM을 마이그레이션할 것입니다.

(`ProjectName`) 및 (`ResourceGroupName`) 매개 변수를 사용하여 Azure Migrate 프로젝트의 모든 VMware VM을 검색할 수도 있습니다.

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Azure Migrate 프로젝트에 어플라이언스가 여러 개 있는 경우 (`ProjectName`), (`ResourceGroupName`) 및 (`ApplianceName`) 매개 변수를 사용하여 특정 Azure Migrate 어플라이언스를 통해 발견된 모든 VM을 검색할 수 있습니다.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. 복제 인프라 초기화

[Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool)은 여러 Azure 리소스를 활용하여 VM을 마이그레이션합니다. 서버 마이그레이션은 프로젝트와 동일한 리소스 그룹에 다음 리소스를 프로비저닝합니다.

- **서비스 버스**: Server Migration은 서비스 버스를 사용하여 복제 오케스트레이션 메시지를 어플라이언스에 보냅니다.
- **게이트웨이 스토리지 계정**: 서버 마이그레이션은 게이트웨이 스토리지 계정을 사용하여 복제되는 VM에 대한 상태 정보를 저장합니다.
- **로그 스토리지 계정**: Azure Migrate 어플라이언스는 VM에 대한 복제 로그를 로그 스토리지 계정에 업로드합니다. Azure Migrate는 복제 정보를 복제본 관리 디스크에 적용합니다.
- **키 자격 증명 모음**: Azure Migrate 어플라이언스는 키 자격 증명 모음을 사용하여 서비스 버스에 대한 연결 문자열을 관리하고, 복제에 사용되는 스토리지 계정에 대한 키에 액세스합니다.

Azure Migrate 프로젝트의 첫 번째 VM을 복제하기 전에, 다음 명령을 실행하여 복제 인프라를 프로비저닝합니다. 이 명령은 VMware VM 마이그레이션을 시작할 수 있도록 앞서 언급한 리소스를 프로비저닝하고 구성합니다.

> [!NOTE]
> 하나의 Azure Migrate 프로젝트는 하나의 Azure 지역에만 마이그레이션을 지원합니다. 이 스크립트를 실행하면 VMware VM을 마이그레이션할 대상 지역을 변경할 수 없습니다.
> Azure Migrate 프로젝트에서 새 어플라이언스를 구성하는 경우 `Initialize-AzMigrateReplicationInfrastructure` 명령을 실행해야 합니다.

이 문서에서는 VM을 `Central US` 지역으로 마이그레이션할 수 있도록 복제 인프라를 초기화합니다.

```azurepowershell-interactive
# Initialize replication infrastructure for the current Migrate project
Initialize-AzMigrateReplicationInfrastructure -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject. Name -Scenario agentlessVMware -TargetRegion "CentralUS" 

```

## <a name="7-replicate-vms"></a>7. VM 복제

검색을 완료하고 복제 인프라를 초기화 한 후에는 VMware VM을 Azure로 복제할 수 있습니다. 최대 500개의 복제를 동시에 실행할 수 있습니다.

다음과 같이 복제 속성을 지정할 수 있습니다.

**매개 변수** | **형식** | **설명**
--- | --- | ---
 대상 구독 및 리소스 그룹 |  필수 | (`TargetResourceGroupId`) 매개 변수를 사용하여 리소스 그룹 ID를 제공하여 VM을 마이그레이션해야 하는 리소스 그룹을 지정합니다.
 대상 가상 네트워크 및 서브넷 선택 | 필수 | 각각 (`TargetNetworkId`) 및 (`TargetSubnetName`) 매개 변수를 사용하여 Azure Virtual Network의 ID와 VM을 마이그레이션해야 하는 서브넷의 이름을 지정합니다.
 머신 ID | 필수 | 복제 및 마이그레이션해야 하는 검색된 머신의 ID를 지정합니다. (`InputObject`)를 사용하여 복제에 대해 검색된 VM 개체를 지정합니다.  
 대상 VM 이름 | 필수 | (`TargetVMName`) 매개 변수를 사용하여 만들 Azure VM의 이름을 지정합니다. 
 대상 VM 크기 | 필수 | (`TargetVMSize`) 매개 변수를 사용하여 VM 복제에 사용할 Azure VM 크기를 지정합니다. 예를 들어 VM을 Azure의 D2_v2 VM으로 마이그레이션하려면 (`TargetVMSize`)의 값을 "Standard_D2_v2"로 지정합니다. 
 라이선스 | 필수 | 활성 소프트웨어 보증 또는 Windows Server 구독의 범위에 포함되는 Windows Server 머신에 Azure 하이브리드 혜택을 사용하려면 (`LicenseType`) 매개 변수의 값을 **WindowsServer** 로 지정합니다. 그렇지 않으면 값을 **NoLicenseType** 으로 지정합니다. 
 OS 디스크 | 필수 | 운영 체제 부팅 로더 및 설치 프로그램이 있는 디스크의 고유 식별자를 지정합니다. 사용할 디스크 ID는 [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) cmdlet을 사용하여 검색된 디스크의 고유 식별자(UUID) 속성입니다.
 디스크 유형 | 필수 | 생성할 부하 분산 장치의 이름을 지정합니다. 
 인프라 중복성 | 선택 사항 | 다음과 같이 인프라 중복성 옵션을 지정합니다. <br/><br/> 마이그레이션된 머신을 지역의 특정 가용성 영역에 고정하는 - **가용성 영역**. 이 옵션을 사용하여 가용성 영역에서 다중 노드 애플리케이션 계층을 구성하는 서버를 배포합니다. 이 옵션은 마이그레이션을 위해 선택한 대상 지역이 가용성 영역을 지원하는 경우에만 사용할 수 있습니다. 가용성 영역을 사용하려면 (`TargetAvailabilityZone`) 매개 변수의 가용성 영역 값을 지정합니다. <br/> 마이그레이션된 머신을 가용성 집합에 배치하기 위한 - **가용성 집합** 입니다. 이 옵션을 사용하려면 선택한 대상 리소스 그룹에 하나 이상의 가용성 집합이 있어야 합니다. 가용성 집합을 사용하려면 (`TargetAvailabilitySet`) 매개 변수의 가용성 집합 ID를 지정합니다. 
 부팅 진단 스토리지 계정 | 선택 사항 | 부팅 진단 스토리지 계정을 사용하려면 (`TargetBootDiagnosticStorageAccount`) 매개 변수에 대한 ID를 지정합니다. <br/> - 부팅 진단에 사용되는 스토리지 계정은 VM을 마이그레이션하는 것과 동일한 구독에 있어야 합니다. <br/> - 기본적으로 이 매개 변수에는 값이 설정되지 않습니다. 
 태그 | 선택 사항 | 마이그레이션된 가상 머신, 디스크 및 NIC에 태그를 추가합니다. <br/>  (`Tag`)를 사용하여 가상 머신, 디스크 및 NIC에 태그를 추가합니다. <br/> 또는 <br/> (`VMTag`)를 사용하여 마이그레이션된 가상 머신에 태그를 추가합니다.<br/> (`DiskTag`)를 사용하여 디스크에 태그를 추가합니다. <br/> (`NicTag`)를 사용하여 네트워크 인터페이스에 태그를 추가합니다. <br/> 예를 들어 $tags 변수에 필요한 태그를 추가하고 필요한 매개 변수에서 변수를 전달합니다.  $tags = @{Organization=”Contoso”}



### <a name="replicate-vms-with-all-disks"></a>모든 디스크를 사용하여 VM 복제

이 자습서에서는 검색된 VM의 모든 디스크를 복제하고 Azure에서 VM의 새 이름을 지정할 것입니다. 검색된 서버의 첫 번째 디스크를 OS 디스크로 지정하고 모든 디스크를 표준 HDD로 마이그레이션합니다. OS 디스크는 운영 체제 부팅 로더 및 설치 관리자가 있는 디스크입니다. cmdlet은 작업 상태를 모니터링하기 위해 추적할 수 있는 작업을 반환합니다.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```
```azurepowershell-interactive
# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>선택 디스크를 사용하여 VM 복제

[New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) cmdlet을 사용하여 검색된 VM의 디스크를 선별적으로 복제하고 [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) cmdlet의 (`DiskToInclude`) 매개 변수에 대한 입력으로 제공할 수도 있습니다. 또한 [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) cmdlet을 사용하여 각 개별 디스크를 복제할 대상 디스크 유형을 서로 다르게 지정할 수 있습니다.

[New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) cmdlet의 다음 매개 변수 값을 지정합니다.

- **DiskId** - 마이그레이션할 디스크의 고유 식별자를 지정합니다. 사용할 디스크 ID는 [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) cmdlet을 사용하여 검색된 디스크의 고유 식별자(UUID) 속성입니다.
- **IsOSDisk** - 마이그레이션할 디스크가 VM의 OS 디스크이면 "true"로 지정하고, 그렇지 않으면 "false"로 지정합니다.
- **DiskType** - Azure에서 사용할 디스크 유형을 지정합니다.

다음 예제에서는 검색된 VM의 디스크 중 2개만 복제합니다. OS 디스크를 지정하고, 복제할 디스크마다 서로 다른 디스크 유형을 사용할 것입니다. cmdlet은 작업 상태를 모니터링하기 위해 추적할 수 있는 작업을 반환합니다.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk
```

```azurepowershell-interactive
# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk
```

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```

```azurepowershell-interactive
# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```

```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. 복제 모니터링

복제는 다음과 같이 수행됩니다.

- 복제 시작 작업이 완료되면 머신에서 Azure로의 초기 복제를 시작합니다.
- 초기 복제 중에 VM 스냅샷이 만들어집니다. 스냅샷의 디스크 데이터가 Azure의 복제본 관리 디스크에 복제됩니다.
- 초기 복제가 완료되면 델타 복제가 시작됩니다. 온-프레미스 디스크에 대한 증분 변경 내용은 Azure의 복제본 디스크에 주기적으로 복제됩니다.

[Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) cmdlet을 사용하여 복제 상태를 추적합니다.



```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```
```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

출력에서 **마이그레이션 상태** 및 **마이그레이션 상태 설명** 속성을 추적할 수 있습니다.

- 초기 복제의 **마이그레이션 상태** 및 **마이그레이션 상태 설명** 속성의 값은 각각 `InitialSeedingInProgress` 및 `Initial replication`입니다.
- 델타 복제 중에 **마이그레이션 상태** 및 **마이그레이션 상태 설명** 속성의 값은 각각 `Replicating` 및 `Ready to migrate`입니다.
- 마이그레이션 완료 후 **마이그레이션 상태** 및 **마이그레이션 상태 설명** 속성의 값은 각각 `Migration succeeded` 및 `Migrated`입니다.

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

복제 진행률에 대한 자세한 내용을 보려면 다음 cmdlet을 실행합니다.

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

출력에서 **초기 시드 진행률 백분율** 속성을 사용하여 초기 복제 진행률을 추적할 수 있습니다.

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

복제는 다음과 같이 수행됩니다.

- 복제 시작 작업이 완료되면 머신에서 Azure로의 초기 복제를 시작합니다.
- 초기 복제 중에 VM 스냅샷이 만들어집니다. 스냅샷의 디스크 데이터가 Azure의 복제본 관리 디스크에 복제됩니다.
- 초기 복제가 완료되면 델타 복제가 시작됩니다. 온-프레미스 디스크에 대한 증분 변경 내용은 Azure의 복제본 디스크에 주기적으로 복제됩니다.

## <a name="9-retrieve-the-status-of-a-job"></a>9. 작업의 상태 검색

[Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) cmdlet을 사용하여 작업의 상태를 모니터링할 수 있습니다.

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. VM 복제 속성 업데이트

[Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool)을 사용하면 이름, 크기, 리소스 그룹, NIC 구성 등과 같은 복제하는 VM의 대상 속성을 변경할 수 있습니다.

VM의 다음 속성을 업데이트할 수 있습니다.


**매개 변수** | **형식** | **설명**
--- | --- | ---
VM 이름 | 선택 사항 | [`TargetVMName`] 매개 변수를 사용하여 만들 Azure VM의 이름을 지정합니다. 
VM 크기 | 선택 사항 | [`TargetVMSize`] 매개 변수를 사용하여 VM 복제에 사용할 Azure VM 크기를 지정합니다. 예를 들어 VM을 Azure의 D2_v2 VM으로 마이그레이션하려면 [`TargetVMSize`]의 값을 `Standard_D2_v2`로 지정합니다.
Virtual Network | 선택 사항 | [`TargetNetworkId`] 매개 변수를 사용하여 VM을 마이그레이션해야 하는 Azure Virtual Network의 ID를 지정합니다. 
리소스 그룹 | 선택 사항 | [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) cmdlet을 사용하여 IC 구성을 지정할 수 있습니다. 그러면 이 개체에 [Set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) cmdlet의 [`NicToUpdate`] 매개 변수에 대한 입력이 전달됩니다. <br/><br/> - **IP 할당 변경** - NIC에 고정 IP를 지정하려면 [`TargetNicIP`] 매개 변수를 사용하여 VM의 고정 IP로 사용할 IPv4 주소를 입력합니다. NIC의 IP를 동적으로 할당하려면 **TargetNicIP** 매개 변수의 값으로 `auto`를 입력합니다. <br/> - [`TargetNicSelectionType`] 매개 변수에 `Primary`, `Secondary` 또는 `DoNotCreate` 값을 사용하여 NIC가 기본인지 아니면 보조인지, 또는 마이그레이션되는 VM에서 만들지 않을 것인지 여부를 지정합니다. VM의 기본 NIC로 하나의 NIC만 지정할 수 있습니다. <br/> - NIC를 기본으로 만들려면 보조로 설정해야 하거나 마이그레이션된 VM에서 만들지 않을 다른 NIC도 지정해야 합니다. <br/> - NIC의 서브넷을 변경하려면 [`TargetNicSubnet`] 매개 변수를 사용하여 서브넷 이름을 지정합니다.
네트워크 인터페이스 | 선택 사항 | [`TargetVMName`] 매개 변수를 사용하여 만들 Azure VM의 이름을 지정합니다. 
가용성 영역 | 선택 사항 | 가용성 영역을 사용하려면 [`TargetAvailabilityZone`] 매개 변수의 가용성 영역 값을 지정합니다. 
가용성 집합 | 선택 사항 | 가용성 집합을 사용하려면 [`TargetAvailabilitySet`] 매개 변수의 가용성 집합 ID를 지정합니다. 
태그 | 선택 사항 | 태그를 업데이트하려면 매개 변수 [`UpdateTag`] 또는 [`UpdateVMTag`], [`UpdateDiskTag`], [`UpdateNicTag`]와, 태그 작업 업데이트 형식 [`UpdateTagOperation`] 또는 [`UpdateVMTagOperation`], [`UpdateDiskTagOperation`], [`UpdateNicTagOperation`]을 사용합니다.   태그 작업 업데이트는 Merge, Delete 및 Replace 값을 사용합니다. <br/> [`UpdateTag`]를 사용하여 가상 머신, 디스크 및 NIC 전체의 모든 태그를 업데이트합니다. <br/> [`UpdateVMTag`]를 사용하여 가상 머신 태그를 업데이트합니다. <br/> [`UpdateDiskTag`]를 사용하여 디스크 태그를 업데이트합니다. <br/> [`UpdateNicTag`]를 사용하여 NIC 태그를 업데이트합니다. <br/> [`UpdateTagOperation`]를 사용하여 가상 머신, 디스크 및 NIC 전체에서 모든 태그의 작업을 업데이트합니다. <br/>  [`UpdateVMTagOperation`]를 사용하여 가상 머신 태그를 업데이트합니다. <br/> [`UpdateDiskTagOperation`]를 사용하여 디스크 태그를 업데이트합니다. <br/> [`UpdateNicTagOperation`]를 사용하여 NIC 태그를 업데이트합니다. <br/> <br/> *Replace* 옵션은 기존 태그의 전체 집합을 새 집합으로 바꿉니다. <br/> *Merge* 옵션을 사용하면 새 이름으로 태그를 추가하고 기존 이름으로 태그 값을 업데이트합니다. <br/> *Delete* 옵션을 사용하면 지정된 이름 또는 이름/값 쌍을 기반으로 태그를 선별 삭제할 수 있습니다. 
디스크 | 선택 사항 | OS 디스크의 경우: <br/> [`TargetDiskName`] 매개 변수를 사용하여 OS 디스크의 이름을 업데이트합니다.  <br/><br/> 여러 디스크 업데이트의 경우: <br/>  [Set-AzMigrateDiskMapping](/powershell/module/az.migrate/set-azmigratediskmapping)을 사용하여 디스크 이름을 *$DiskMapping* 변수로 설정한 다음, [`DiskToUpdate`] 매개 변수를 사용하여 변수와 함께 전달합니다. <br/> <br/> **참고:** [Set-AzMigrateDiskMapping](/powershell/module/az.migrate/set-azmigratediskmapping)에서 사용할 디스크 ID는  [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) cmdlet을 사용하여 검색된 디스크의 고유 식별자(UUID) 속성입니다. 
NIC 이름 | 선택 사항 | [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping)을 사용하여 NIC 이름을 *$NICMapping* 변수로 설정한 다음, [`NICToUpdate`] 매개 변수를 사용하여 변수와 함께 전달합니다.

[Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) cmdlet은 작업 상태를 모니터링하기 위해 추적할 수 있는 작업을 반환합니다.

```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```

```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

다음 예제에서는 첫 번째 NIC를 기본으로 설정하고 고정 IP를 할당하여 NIC 구성을 업데이트합니다. 마이그레이션의 두 번째 NIC를 삭제하여 대상 VM 이름 및 크기를 업데이트하고 NIC 이름을 사용자 지정합니다.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary TargetNicName "ContosoNic_1"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate - TargetNicName "ContosoNic_2"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2
```
```azurepowershell-interactive
# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping
```

다음 예제에서는 디스크 이름을 사용자 지정합니다.

```azurepowershell-interactive
# Customize the Disk names for a replicating VM
$OSDisk = Set-AzMigrateDiskMapping -DiskID "6000C294-1217-dec3-bc18-81f117220424" -DiskName "ContosoDisk_1" 
$DataDisk1= Set-AzMigrateDiskMapping -DiskID "6000C292-79b9-bbdc-fb8a-f1fa8dbeff84" -DiskName "ContosoDisk_2" 
$DiskMapping = $OSDisk, $DataDisk1 
```

```azurepowershell-interactive
# Update the disk names for a replicating server
$UpdateJob = Set-AzMigrateServerReplication InputObject $ReplicatingServer DiskToUpdate $DiskMapping 
 ```

다음 예제에서는 복제하는 VM에 태그를 추가합니다.

```azurepowershell-interactive
# Update all tags across virtual machines, disks, and NICs.
Set-azmigrateserverreplication UpdateTag $UpdateTag UpdateTagOperation Merge/Replace/Delete

# Update virtual machines tags
Set-azmigrateserverreplication UpdateVMTag $UpdateVMTag UpdateVMTagOperation Merge/Replace/Delete 
```
다음 예제를 사용하여 작업 상태를 추적합니다.

```azurepowershell-interactive
# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```

## <a name="11-run-a-test-migration"></a>11. 테스트 마이그레이션 실행

델타 복제가 시작되면 Azure로 전체 마이그레이션을 실행하기 전에 VM의 테스트 마이그레이션을 실행할 수 있습니다. 각 머신을 마이그레이션하기 전에 테스트 마이그레이션을 적어도 한 번 이상 수행하는 것이 좋습니다. cmdlet은 작업 상태를 모니터링하기 위해 추적할 수 있는 작업을 반환합니다.

- 테스트 마이그레이션을 실행하면 마이그레이션이 예상대로 작동하는지 확인할 수 있습니다. 테스트 마이그레이션은 온-프레미스 머신에 영향을 미치지 않습니다. 온-프레미스 머신은 계속 작동하고 복제를 지속합니다.
- 테스트 마이그레이션은 복제된 데이터를 사용하여 Azure VM을 만들어 마이그레이션을 시뮬레이션합니다(일반적으로 Azure 구독에서 비프로덕션 VNet으로 마이그레이션).
- 복제된 테스트 Azure VM을 사용하여 마이그레이션의 유효성을 검사하고, 애플리케이션 테스트를 수행하며, 전체 마이그레이션을 수행하기 전에 문제를 해결할 수 있습니다.

[`TestNetworkID`] 매개 변수를 통해 가상 네트워크의 ID를 지정하여 테스트에 사용할 Azure Virtual Network를 선택합니다.

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork
```
```azurepowershell-interactive
# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id
```
```azurepowershell-interactive
# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

테스트가 완료되면 [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) cmdlet을 사용하여 테스트 마이그레이션을 정리합니다. cmdlet은 작업 상태를 모니터링하기 위해 추적할 수 있는 작업을 반환합니다.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. VM 마이그레이션

테스트 마이그레이션이 예상대로 작동하는지 확인한 후에는 다음 cmdlet을 사용하여 복제 서버를 마이그레이션할 수 있습니다. cmdlet은 작업 상태를 모니터링하기 위해 추적할 수 있는 작업을 반환합니다.

원본 서버를 끄지 않으려면 [`TurnOffSourceServer`] 매개 변수를 사용하지 마세요.

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. 마이그레이션 완료

1. 마이그레이션이 완료된 후에는 다음 cmdlet을 사용하여 온-프레미스 머신에 대한 복제를 중지하고 VM의 복제 상태 정보를 정리합니다. cmdlet은 작업 상태를 모니터링하기 위해 추적할 수 있는 작업을 반환합니다.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer
   ```
   ```azurepowershell-interactive
   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   # Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. 머신에 Linux OS가 있는 경우 마이그레이션된 머신에 [Linux](../virtual-machines/extensions/agent-linux.md) 에이전트를 설치합니다. 마이그레이션 중에 Windows VM용 VM 에이전트를 자동으로 설치합니다.
1. 데이터베이스 연결 문자열 업데이트, 웹 서버 구성 등의 마이그레이션 후 앱 조정을 수정합니다.
1. 이제 Azure에서 실행 중인 마이그레이션된 애플리케이션에서 최종 애플리케이션 및 마이그레이션 수용 테스트를 수행합니다.
1. 트래픽을 마이그레이션된 Azure VM 인스턴스로 전환합니다.
1. 로컬 VM 인벤토리에서 온-프레미스 VM을 제거합니다.
1. 로컬 백업 작업에서 온-프레미스 VM을 제거합니다.
1. 내부 문서를 업데이트하여 Azure VM의 새 위치 및 IP 주소를 표시합니다.

## <a name="14-post-migration-best-practices"></a>14. 마이그레이션 후 작업 모범 사례

- 복원력 개선:
    - Azure Backup 서비스를 통해 Azure VM을 백업하여 데이터 보안을 유지합니다. [자세히 알아보기](../backup/quick-backup-vm-portal.md).
    - Site Recovery를 통해 Azure VM을 보조 지역에 복제하면 워크로드를 계속 실행하고 지속적으로 사용할 수 있습니다. [자세히 알아보기](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- 보안 강화:
    - [Microsoft Defender for Cloud - Just-In-Time 관리](../security-center/security-center-just-in-time.md)를 사용하여 인바운드 트래픽 액세스를 잠그고 제한합니다.
    - [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 사용하여 관리 엔드포인트에 대한 네트워크 트래픽을 제한합니다.
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)을 배포하여 디스크를 보호하고 데이터를 도난 및 무단 액세스로부터 안전하게 유지합니다.
    - [IaaS 리소스 보호](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)에 대해 자세히 알아보고 [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/)를 방문하세요.
- 모니터링 및 관리 앱:
-  리소스 사용량과 비용을 모니터링하려면 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 배포하는 것이 좋습니다.
