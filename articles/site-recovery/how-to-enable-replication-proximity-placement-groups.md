---
title: 근접 배치 그룹에서 실행되는 Azure VM 복제
description: Azure Site Recovery를 사용하여 근접 배치 그룹에서 실행되는 Azure VM을 복제하는 방법을 알아봅니다.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: abcb233665d6c6074ff50d1bb53e553ed9eb99ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528955"
---
# <a name="replicate-virtual-machines-running-in-a-proximity-placement-group-to-another-region"></a>근접 배치 그룹에서 실행되는 가상 머신을 다른 지역에 복제

이 문서에서는 근접 배치 그룹에서 실행되는 Azure VM(가상 머신)을 보조 지역으로 복제, 장애 조치(failover), 장애 복구(failback)하는 방법에 대해 설명합니다.

[근접 배치 그룹](../virtual-machines/windows/proximity-placement-groups-portal.md)은 Azure Virtual Machines의 논리적 그룹화 기능입니다. 이를 사용하여 애플리케이션과 관련된 VM 간 네트워크 대기 시간을 줄일 수 있습니다. 

VM이 동일한 근접 배치 그룹 내에 배포되면 서로 물리적으로 최대한 가깝게 배치됩니다. 근접 배치 그룹은 대기 시간이 중요한 워크로드의 요구 사항을 처리하는 데 유용합니다.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>근접 배치 그룹을 통한 재해 복구

일반적인 시나리오에서는 애플리케이션 계층 간의 네트워크 대기 시간을 방지하기 위해 근접 배치 그룹에서 실행되는 가상 머신이 있을 수 있습니다. 이 방법은 최적의 네트워크 대기 시간을 애플리케이션에 제공할 수 있지만, 이러한 애플리케이션은 지역 수준 오류에 대비하여 Azure Site Recovery를 사용하여 보호하는 것이 좋습니다. 

Site Recovery는 데이터를 한 Azure 지역에서 다른 지역으로 복제합니다. 그러면 장애 조치(failover) 이벤트에서 DR(재해 복구) 지역의 머신을 가져옵니다.

## <a name="considerations"></a>고려 사항

- 최선의 노력은 가상 머신을 근접 배치 그룹으로 장애 조치(failover)하고 장애 복구(failback)하는 것입니다. 근접 배치 그룹 내에서 VM을 가져올 수 없어서 용량 할당 제약 조건이 있는 경우 장애 조치 및 장애 복구는 계속 실행되지만 근접 배치 그룹 외부에서 VM이 생성됩니다.
- 가용성 집합이 근접 배치 그룹에 고정되어 있고 장애 조치(failover) 또는 장애 복구(failback) 중에 가용성 집합의 VM에 할당 제약 조건이 있는 경우 VM은 가용성 집합과 근접 배치 그룹 모두의 외부에 만들어집니다.
- 근접 배치 그룹에 대한 Site Recovery는 비관리 디스크를 지원하지 않습니다.

> [!NOTE]
> Azure Site Recovery는 Hyper-V에서 Azure로 이동하는 시나리오에 대해 관리 디스크로부터의 장애 복구(failback)를 지원하지 않습니다. Azure의 근접 배치 그룹에서 Hyper-V로의 장애 복구(failback)는 지원되지 않습니다.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-the-azure-portal"></a>Azure Portal을 통해 근접 배치 그룹의 VM에 대한 재해 복구 설정

### <a name="azure-to-azure"></a>Azure 간

VM 재해 복구 페이지를 통해 가상 머신에 대한 복제를 사용하도록 설정할 수 있습니다. 또는 미리 만든 자격 증명 모음으로 이동하고, Site Recovery 섹션으로 이동한 다음, 복제를 사용하도록 설정할 수 있습니다. 두 가지 방법 모두를 통해 근접 배치 그룹 내에서 Site Recovery VM을 설정하는 방법을 살펴보겠습니다.

IaaS(Infrastructure as a Service) VM DR 페이지를 통해 복제를 사용하도록 설정하면서 DR 지역의 근접 배치 그룹을 선택하려면 다음을 수행합니다.

1. 가상 머신으로 이동합니다. 왼쪽 창의 **작업** 아래에서 **재해 복구** 를 선택합니다.
2. **기본 사항** 탭에서 VM을 복제하려는 DR 지역을 선택합니다. **고급 설정** 으로 이동합니다.
3. VM의 근접 배치 그룹 및 DR 지역의 근접 배치 그룹을 선택할 수 있는 옵션이 표시됩니다. 이 기본 옵션을 사용하도록 선택하는 경우 Site Recovery에서 새로 만드는 근접 배치 그룹을 사용할 수 있는 옵션을 제공합니다. 
 
   원하는 근접 배치 그룹을 선택합니다. 그런 다음, **검토 + 복제 시작** 을 선택합니다.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="복제를 사용하도록 설정하기 위한 고급 설정을 보여 주는 스크린샷":::

자격 증명 모음 페이지를 통해 복제를 사용하도록 설정하면서 DR 지역의 근접 배치 그룹을 선택하려면 다음을 수행합니다.

1. Recovery Services 자격 증명 모음으로 이동한 다음, **Site Recovery** 탭으로 이동합니다.
2. **+ Site Recovery 사용** 을 선택합니다. 그런 다음, Azure VM을 복제하려고 하므로 **Azure 가상 머신** 아래에서 **1: 복제 사용** 을 선택합니다.
3. **원본** 탭에서 필수 필드를 입력하고, **다음** 을 선택합니다.
4. **가상 머신** 탭에서 복제를 사용하도록 설정하려는 VM의 목록을 선택하고, **다음** 을 클릭합니다.
5. DR 지역의 근접 배치 그룹을 선택할 수 있는 옵션이 표시됩니다. 이 기본 옵션을 사용하도록 선택하는 경우 Site Recovery에서 새로 만드는 근접 배치 그룹을 사용할 수 있는 옵션을 제공합니다. 

   원하는 근접 배치 그룹을 선택한 다음, 계속 진행하여 복제를 사용하도록 설정합니다.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="대상 설정을 사용자 지정하기 위한 선택 항목을 보여 주는 스크린샷":::

VM에 대한 복제가 사용하도록 설정되면 DR 지역의 근접 배치 그룹에 대한 선택 항목을 쉽게 업데이트할 수 있습니다.

1. 가상 머신으로 이동합니다. 왼쪽 창의 **작업** 아래에서 **재해 복구** 를 선택합니다.
2. **컴퓨팅 및 네트워크** 창으로 이동하여 **편집** 을 선택합니다.
3. 대상 근접 배치 그룹을 포함하여 여러 대상 설정을 편집할 수 있는 옵션이 표시됩니다. VM이 장애 조치(failover)되도록 하려는 근접 배치 그룹을 선택한 다음, **저장** 을 선택합니다.

### <a name="vmware-to-azure"></a>VMware에서 Azure로

VM에 대한 복제가 사용하도록 설정되면 대상 VM에 대한 근접 배치 그룹을 설정할 수 있습니다. 요구 사항에 따라 근접 배치 그룹을 대상 지역에 별도로 만들어야 합니다. 

VM에 대한 복제가 사용하도록 설정되면 DR 지역의 근접 배치 그룹에 대한 선택 항목을 쉽게 업데이트할 수 있습니다.

1. 자격 증명 모음에서 가상 머신을 선택합니다. 왼쪽 창의 **작업** 아래에서 **재해 복구** 를 선택합니다.
2. **컴퓨팅 및 네트워크** 창으로 이동하여 **편집** 을 선택합니다.
3. 대상 근접 배치 그룹을 포함하여 여러 대상 설정을 편집할 수 있는 옵션이 표시됩니다. VM이 장애 조치(failover)되도록 하려는 근접 배치 그룹을 선택한 다음, **저장** 을 선택합니다.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="VMware에서 Azure로의 재해 복구 설정에 대한 컴퓨팅 및 네트워크 선택 항목을 보여 주는 스크린샷":::

### <a name="hyper-v-to-azure"></a>Hyper-V에서 Azure로

VM에 대한 복제가 사용하도록 설정되면 대상 VM에 대한 근접 배치 그룹을 설정할 수 있습니다. 요구 사항에 따라 근접 배치 그룹을 대상 지역에 별도로 만들어야 합니다. 

VM에 대한 복제가 사용하도록 설정되면 DR 지역의 근접 배치 그룹에 대한 선택 항목을 쉽게 업데이트할 수 있습니다.

1. 자격 증명 모음에서 가상 머신을 선택합니다. 왼쪽 창의 **작업** 아래에서 **재해 복구** 를 선택합니다.
2. **컴퓨팅 및 네트워크** 창으로 이동하여 **편집** 을 선택합니다.
3. 대상 근접 배치 그룹을 포함하여 여러 대상 설정을 편집할 수 있는 옵션이 표시됩니다. VM이 장애 조치(failover)되도록 하려는 근접 배치 그룹을 선택한 다음, **저장** 을 선택합니다.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Hyper-V에서 Azure로의 재해 복구 설정에 대한 컴퓨팅 및 네트워크 선택 항목을 보여 주는 스크린샷":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>PowerShell을 통해 근접 배치 그룹의 VM에 대한 재해 복구 설정

### <a name="prerequisites"></a>사전 요구 사항 

- Azure PowerShell Az 모듈이 있어야 합니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성 가이드](/powershell/azure/install-az-ps)를 따릅니다.
- 최소 Azure PowerShell Az 버전은 4.1.0이어야 합니다. 현재 버전을 확인하려면 다음 명령을 사용합니다.

    ```
    Get-InstalledModule -Name Az
    ```

> [!NOTE]
> 대상 근접 배치 그룹의 고유 ID를 편리하게 사용할 수 있는지 확인하세요. 사용하는 명령은 [새 근접 배치 그룹을 만드는지](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group), 아니면 [기존 근접 배치 그룹을 사용하는지](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)에 따라 달라집니다.

### <a name="azure-to-azure"></a>Azure 간

1. [계정에 로그인하고, 구독을 설정합니다](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription).
2. [복제하려는 가상 머신의 세부 정보를 가져옵니다](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Recovery Services 자격 증명 모음을 만들고](./azure-to-azure-powershell.md#create-a-recovery-services-vault), [자격 증명 모음 컨텍스트를 설정합니다](./azure-to-azure-powershell.md#set-the-vault-context).
4. 복제 가상 머신을 시작하기 위한 자격 증명 모음을 준비합니다. 이 단계에는 주 지역과 복구 지역 모두에 대한 [Azure Service Fabric 개체를 만드는 작업](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region)이 포함됩니다.
5. 주 지역 및 복구 지역 패브릭 모두에 대한 [Site Recovery 보호 컨테이너를 만듭니다](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric).
6. [복제 정책 만들기](./azure-to-azure-powershell.md#create-a-replication-policy).
7. [주 지역 및 복구 지역 보호 컨테이너 간의 보호 컨테이너 매핑을 만들고](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container), [장애 복구(failback)에 대한 보호 컨테이너 매핑을 만듭니다](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. [캐시 스토리지 계정을 만듭니다](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account).
9. [필요한 네트워크 매핑을 만듭니다](./azure-to-azure-powershell.md#create-network-mappings).
10. 관리 디스크를 사용하여 Azure 가상 머신을 복제하려면 다음 PowerShell cmdlet을 사용합니다.

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    #Make sure to replace the variable $datadiskName with the data disk name.

    #Data disk
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $datadiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

    #Create a list of disk replication configuration objects for the disks of the virtual machine that will be replicated.

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    복제를 여러 데이터 디스크에 사용하도록 설정하는 경우 다음 PowerShell cmdlet을 사용합니다.

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig

    #Data disk

    # Add data disks
    Foreach( $disk in $VM.StorageProfile.DataDisks)
    {
        $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
        $dataDiskId1 = $datadisk[0].Id
        $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
        $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
        $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
             -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
             -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
        $diskconfigs += $DataDisk1ReplicationConfig
    }

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    근접 배치 그룹을 사용하여 영역 간 복제를 사용하도록 설정하는 경우 복제를 시작하는 명령이 PowerShell cmdlet과 교환됩니다.

    ```azurepowershell
    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
    ```

    복제를 시작하는 작업이 성공하면 가상 머신 데이터가 복구 지역에 복제됩니다.

    복제 프로세스는 처음에 복구 영역에 있는 가상 머신의 복제 디스크의 복사본을 시드하면서 시작됩니다. 이 단계를 *초기 복제* 단계라고 합니다.

    초기 복제가 완료되면 복제가 *차등 동기화* 단계로 이동합니다. 이 시점에서 가상 머신이 보호되고 테스트 장애 조치(failover) 작업을 수행할 수 있습니다. 초기 복제가 완료되면 가상 머신을 나타내는 복제된 항목의 복제 상태는 '보호됨' 상태가 됩니다.

    가상 머신에 해당하는 복제 보호된 항목의 세부 정보를 가져와서 해당 가상 머신에 대한 복제 상태를 모니터링합니다.

    ```azurepowershell
    Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
    ```

11. [테스트 장애 조치(failover)를 수행, 유효성 검사 및 정리합니다](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover).
12. [가상 머신을 장애 조치(fail over)합니다](./azure-to-azure-powershell.md#fail-over-to-azure).
13. 다음 PowerShell cmdlet을 사용하여 다시 보호하고 원본 지역으로 장애 복구(failback)합니다.

    ```azurepowershell
    #Create a cache storage account for replication logs in the primary region.
    $WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


    #Use the recovery protection container, the new cache storage account in West US, and the source region VM resource group. 
    Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
    ```

14. [복제를 사용하지 않도록 설정합니다](./azure-to-azure-powershell.md#disable-replication).

### <a name="vmware-to-azure"></a>VMware에서 Azure로

1. [Azure로 재해 복구할 온-프레미스 VMware 서버를 준비합니다](./vmware-azure-tutorial-prepare-on-premises.md).
2. [계정에 로그인하고, 구독을 설정합니다](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Recovery Services 자격 증명 모음을 설정하고](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault), [자격 증명 모음 컨텍스트를 설정합니다](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [자격 증명 모음 등록의 유효성을 검사합니다](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration).
5. [복제 정책 만들기](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy).
6. [vCenter 서버를 추가하고, 가상 머신을 검색하고](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms), [복제를 위한 스토리지 계정을 만듭니다](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication).
7. 다음 PowerShell cmdlet을 사용하여 VMware 가상 머신을 복제하고 세부 정보를 확인합니다.

   ```azurepowershell
   #Get the target resource group to be used.
   $ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

   #Get the target virtual network to be used.
   $RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

   #Get the protection container mapping for the replication policy named ReplicationPolicy.
   $PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

   #Get the protectable item that corresponds to the virtual machine CentOSVM1.
   $VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

   # Enable replication for virtual machine CentOSVM1 by using the Az.RecoveryServices module 2.0.0 onward to replicate to managed disks.
   # The name specified for the replicated item needs to be unique within the protection container. Use a random GUID to ensure uniqueness.
   $Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
   ```

8. `Get-ASRReplicationProtectedItem` cmdlet을 사용하여 가상 머신에 대한 복제 상태를 확인합니다.

   ```azurepowershell
   Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
   ```

9. [장애 조치(failover) 설정을 구성합니다](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [테스트 장애 조치(failover)를 실행합니다](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover).
11. [Azure로 장애 조치(failover)합니다](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure).

### <a name="hyper-v-to-azure"></a>Hyper-V에서 Azure로

1. [Azure로 재해 복구할 온-프레미스 Hyper-V 서버를 준비합니다](./hyper-v-prepare-on-premises-tutorial.md).
2. [Azure에 로그인](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account)합니다.
3. [자격 증명 모음을 설정하고](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault), [Recovery Services 자격 증명 모음 컨텍스트를 설정합니다](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context).
4. [Hyper-V 사이트를 만듭니다](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site).
5. [공급자 및 에이전트를 설치합니다](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent).
6. [복제 정책 만들기](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy).
7. 다음 단계를 사용하여 복제를 사용하도록 설정합니다. 
    
   a. 보호하려는 VM에 해당하는 보호 가능한 항목을 검색합니다.

      ```azurepowershell
      $VMFriendlyName = "Fabrikam-app"          #Name of the VM
      $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
      ```
   b. VM을 보호합니다. 둘 이상의 디스크가 보호하는 VM에 연결된 경우 `OSDiskName` 매개 변수를 사용하여 운영 체제 디스크를 지정합니다.
    
      ```azurepowershell
      $OSType = "Windows"          # "Windows" or "Linux"
      $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId     $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
      ```
   다. VM이 초기 복제 후 보호되는 상태에 도달할 때까지 기다립니다. 이 프로세스에는 복제할 데이터의 양 및 Azure에 사용 가능한 업스트림 대역폭과 같은 요인에 따라 시간이 다소 걸릴 수 있습니다. 
   
      '보호됨' 상태가 되면 작업에 대한 `State` 및 `StateDescription`이 다음과 같이 업데이트됩니다. 
    
      ```azurepowershell
      $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
      $DRjob | Select-Object -ExpandProperty State

      $DRjob | Select-Object -ExpandProperty StateDescription
      ```
   d. 장애 조치(failover) 후 복구 속성(예: VM 역할 크기) 및 VM NIC를 연결할 Azure 네트워크를 업데이트합니다.

      ```azurepowershell
      $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

      $VMFriendlyName = "Fabrikam-App"

      $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

      $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

      $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

      $UpdateJob | Select-Object -ExpandProperty state

      Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
      ```
8. [테스트 장애 조치(failover)를 실행합니다](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover).


## <a name="next-steps"></a>다음 단계

VMware를 다시 보호하고 Azure로 장애 복구(failback)하려면 [VMware VM에 대한 다시 보호 및 장애 복구(failback) 준비](./vmware-azure-prepare-failback.md)를 참조하세요.

Hyper-V에서 Azure로 장애 조치(failover)하려면 [온-프레미스에서 Azure로 장애 조치(failover) 실행](./site-recovery-failover.md)을 참조하세요. 장애 복구(failback)하려면 [Hyper-V VM에 대한 장애 복구(failback) 실행](./hyper-v-azure-failback.md)을 참조하세요.

자세한 내용은 [Site Recovery에서 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.
