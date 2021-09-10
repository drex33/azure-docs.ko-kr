---
title: Azure Resource Manager 마이그레이션에 대한 클래식 중 일반 오류
description: 이 문서에서는 Azure 서비스 관리에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션 중에 가장 일반적인 오류 및 해결 방법을 소개합니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d1364d8b408cc6a42e2c87f6748ab36558bb389
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112322569"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>클래식에서 Azure Resource Manager로 마이그레이션하는 동안 일반적으로 발생하는 오류

> [!IMPORTANT]
> 현재 IaaS VM의 90%가 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)를 사용하고 있습니다. 2020년 2월 28일부터 클래식 VM은 더 이상 사용되지 않으며 2023년 3월 1일에 완전히 사용 중지될 예정입니다. 사용 중지 및 [그 영향](classic-vm-deprecation.md#how-does-this-affect-me)에 대한 [자세한 내용을 확인하세요]( https://aka.ms/classicvmretirement).

이 문서에는 Azure 클래식 배포 모델에서 Azure Resource Manager 스택으로의 IaaS 리소스 마이그레이션 중에 가장 일반적인 오류 및 해결 방법이 포함됩니다.


## <a name="list-of-errors"></a>오류 목록

| 오류 문자열 | 완화 방법 |
| --- | --- |
| 내부 서버 오류 |어떤 경우에는 다시 시도하면 사라지는 일시적인 오류입니다. 계속 유지하는 경우 플랫폼 로그를 조사해야 하므로 [Azure 지원에 문의](../azure-portal/supportability/how-to-create-azure-support-request.md)하세요. <br><br> **참고:** 인시던트가 지원 팀에서 추적되면 환경에서 의도하지 않은 결과가 발생할 수 있으므로 자체적으로 완화하려고 하지 않습니다. |
| 마이그레이션은 PaaS 배포(웹/작업자)이기 때문에 HostedService {hosted-service-name}에서 배포 {deployment-name}에 지원되지 않습니다. |배포가 웹/작업자 역할을 포함하는 경우에 발생합니다. 마이그레이션이 Virtual Machines에 대해서만 지원되므로 배포에서 웹/작업자 역할을 제거하고 마이그레이션을 다시 시도하세요. |
| 템플릿 {template-name} 배포에 실패했습니다. CorrelationId={guid} |마이그레이션 서비스의 백 엔드에서 Azure Resource Manager 템플릿을 사용하여 Azure Resource Manager 스택에서 리소스를 만듭니다. 템플릿이 idempotent이기 때문에 일반적으로 마이그레이션 작업을 안전하게 다시 시도하여 이 오류를 넘길 수 있습니다. 이 오류가 계속 지속되는 경우 [Azure 지원에 문의](../azure-portal/supportability/how-to-create-azure-support-request.md)하고 CorrelationId를 제공하세요. <br><br> **참고:** 인시던트가 지원 팀에서 추적되면 환경에서 의도하지 않은 결과가 발생할 수 있으므로 자체적으로 완화하려고 하지 않습니다. |
| 가상 네트워크 {virtual-network-name}가 존재하지 않습니다. |새로운 Azure Portal에서 Virtual Network를 만든 경우에 발생할 수 있습니다. 실제 Virtual Network 이름은 "Group * \<VNET name>" 패턴을 따릅니다. |
| HostedService {hosted-service-name}에서 VM {vm-name}에는 Azure Resource Manager에서 지원되지 않는 확장 {extension-name}이 포함되어 있습니다. 마이그레이션을 계속하기 전에 VM에서 제거하는 것이 좋습니다. |BGInfo 1.\*과 같은 XML 확장은 Azure Resource Manager에서 지원되지 않습니다. 따라서 이러한 확장을 마이그레이션할 수 없습니다. 이러한 확장을 가상 머신에 설치된 대로 두면 마이그레이션을 완료하기 전에 자동으로 제거됩니다. |
| HostedService {hosted-service-name}에서 VM {vm-name}은 현재 마이그레이션에 지원되지 않는 VMSnapshot/VMSnapshotLinux 확장을 포함합니다. VM에서 제거하고 마이그레이션을 완료한 후에 Azure Resource Manager를 사용하여 다시 추가합니다. |가상 머신이 Azure Backup에 대해 구성되어 있는 시나리오입니다. 현재 지원되지 않는 시나리오이므로 https://aka.ms/vmbackupmigration에서 해결 방법을 따르세요. |
| HostedService {hosted-service-name}에서 VM {vm-name}에는 상태가 VM에서 보고되지 않는 확장 {extension-name}이 포함되어 있습니다. 따라서 이 VM은 마이그레이션할 수 없습니다. 확장 상태가 보고되고 있는지 확인하거나 VM에서 확장을 제거하고 마이그레이션을 다시 시도합니다. <br><br> HostedService {hosted-service-name}에서 VM {vm-name}에는 확장 {extension-name} 보고 처리기 상태 {handler-status}가 포함되어 있습니다. 따라서 VM은 마이그레이션할 수 없습니다. 보고되고 있는 확장 처리기 상태가 {handler-status}인지 확인하거나 VM에서 확장을 제거하고 마이그레이션을 다시 시도합니다. <br><br> HostedService {hosted-service-name}에서 VM {vm-name}의 VM 에이전트는 전반적인 에이전트 상태를 준비되지 않음으로 보고합니다. 따라서 VM은 마이그레이션할 수 있는 확장명이 있는 경우 마이그레이션되지 않을 수 있습니다. VM 에이전트가 전반적인 에이전트 상태를 준비됨으로 보고하는지 확인합니다. https://aka.ms/classiciaasmigrationfaqs을 참조하세요. |Azure 게스트 에이전트 및 VM 확장은 해당 상태를 채우기 위해 VM 스토리지 계정에 대한 아웃바운드 인터넷 액세스가 필요합니다. 상태 실패의 일반적인 원인은 다음과 같습니다. <li> 인터넷에 대한 아웃바운드 액세스를 차단하는 네트워크 보안 그룹 <li> VNET에 온-프레미스 DNS 서버가 있고 DNS 연결이 손실된 경우 <br><br> 지원되지 않는 상태를 계속 표시하면 확장을 제거하여 이 검사를 건너뛰고 마이그레이션으로 이동할 수 있습니다. |
| 마이그레이션에는 여러 가용성 집합이 있기 때문에 HostedService {hosted-service-name}에서 배포 {deployment-name}에 지원되지 않습니다. |현재, 1 이하의 가용성 집합을 가진 호스티드 서비스만 마이그레이션할 수 있습니다. 이 문제를 해결하려면 해당 가용성 집합에서 추가 가용성 집합과 가상 머신을 다른 호스트된 서비스로 이동시킵니다. |
| HostedService에 가용성 집합이 있지만 마이그레이션에는 가용성 집합의 일부가 아닌 VM이 있기 때문에 HostedService {hosted-service-name}에서 배포 {deployment-name}에 지원되지 않습니다. |이 시나리오에 대한 해결 방법은 단일 가용성 집합에 있는 모든 가상 머신을 이동하거나 호스티드 서비스의 가용성 집합에서 모든 가상 머신을 제거하는 것입니다. |
| Storage 계정/호스티드 서비스/Virtual Network {virtual-network-name}은 마이그레이션되는 프로세스이며 따라서 변경할 수 없습니다. |리소스에 대한 마이그레이션 작업 "준비"가 완료되고 리소스를 변경하는 작업이 트리거되면 이 오류가 발생합니다. "준비" 작업 후에 관리 평면에 대해 잠겨지기 때문에 리소스에 대한 변경 내용이 차단됩니다. 관리 평면 잠금을 해제하려면 마이그레이션을 완료하는 마이그레이션 작업을 "커밋"하거나 "준비" 작업을 롤백하는 "중단" 마이그레이션 작업을 실행할 수 있습니다. |
| RoleStateUnknown 상태인 VM {vm-name}이 있기 때문에 HostedService {hosted-service-name}에 대한 마이그레이션이 허용되지 않습니다. VM이 실행 중, 중지, 할당 취소 중지 상태 중 하나인 경우에만 마이그레이션이 허용됩니다. |VM에 상태 전환 작업이 진행 중일 수 있습니다. 일반적으로 다시 부팅, 확장 설치 등의 작업을 수행하는 경우에 상태 전환 작업이 발생합니다. 마이그레이션을 시도하기 전에 HostedService에서 업데이트 작업을 완료하는 것이 좋습니다. |
| HostedService {hosted-service-name}의 Deployment {deployment-name}은 물리적 블록 크기 {size-of-the-vhd-blob-backing-the-data-disk} 바이트가 VM 데이터 디스크 논리적 크기 {size-of-the-data-disk-specified-in-the-vm-api} 바이트와 일치하지 않는 데이터 디스크 {data-disk-name}을 포함합니다. 마이그레이션에는 Azure Resource Manager VM에 대한 데이터 디스크 크기를 지정하지 않고 계속 진행됩니다. | 이 오류는 VM API 모델의 크기를 업데이트하지 않고 VHD blob 크기를 조정할 경우에 발생합니다. 자세한 마이그레이션 단계는 [다음](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes)과 같습니다.|
| 클라우드 서비스 {클라우드 서비스 이름}에서 {VM 이름} VM에 대해 미디어 링크 {데이터 디스크 URI}를 사용하여 데이터 디스크 {데이터 디스크 이름}에 대해 유효성 검사를 수행하는 동안 스토리지 예외가 발생합니다. 이 가상 머신에 대해 VHD 미디어 링크에 액세스할 수 있는지 확인하세요. | 이 오류는 VM의 디스크가 삭제되었거나 더 이상 액세스할 수 없는 경우에 발생할 수 있습니다. VM에 대한 디스크가 있는지 확인합니다.|
| HostedService {cloud-service-name}의 VM {vm-name}에는 Azure Resource Manager에서 지원되지 않는 BLOB 이름 {vhd-blob-name}의 MediaLink {vhd-uri}가 있는 디스크가 포함되어 있습니다. | 이 오류는 Blob의 이름에 현재 Compute 리소스 공급자에서 지원되지 않는 "/"가 있어 발생합니다. |
| HostedService {cloud-service-name}의 Depoyment {deployment-name}은 지역 범위에 없으므로 마이그레이션이 허용되지 않습니다. 이 배포를 지역 범위로 이동하려면 https:\//aka.ms/regionalscope를 참조하세요. | 2014년, Azure에서는 네트워킹 리소스가 클러스터 수준 범위에서 지역 범위로 이동할 것이라고 발표했습니다. 자세한 내용은 [https://aka.ms/regionalscope](https://aka.ms/regionalscope)를 참조하세요. 이 오류는 마이그레이션 중인 배포에서 지역 범위로 자동으로 이동하는 업데이트 작업이 없을 때 발생합니다. 가장 좋은 해결 방법은 VM에 엔드포인트를 추가하거나 VM에 데이터 디스크를 추가한 다음, 마이그레이션을 다시 시도하는 것입니다. <br> [Azure에서 클래식 가상 머신에 엔드포인트를 설정하는 방법](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) 또는 [클래식 배포 모델을 사용하여 만든 가상 머신에 데이터 디스크 연결](./linux/attach-disk-portal.md)을 참조하세요.|
| 비게이트웨이 PaaS 배포가 있으므로 가상 네트워크{vnet-name}에 대한 마이그레이션이 지원되지 않습니다. | 이 오류는 가상 네트워크에 연결된 Application Gateway 또는 API Management 서비스 같은 비게이트웨이 PaaS 배포가 있는 경우 발생 합니다.|


## <a name="detailed-mitigations"></a>자세한 마이그레이션

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>물리적 blob 크기가 VM 데이터 디스크 논리적 크기 바이트와 일치하지 않는 데이터 디스크가 있는 VM입니다.

데이터 디스크 논리적 크기가 실제 VHD blob 크기와 동기화되지 않을 때 발생할 수 있습니다. 다음 명령을 사용하여 쉽게 확인할 수 있습니다.

#### <a name="verifying-the-issue"></a>문제 확인

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>문제 완화

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>마이그레이션 완료 후 VM을 다른 구독으로 이동

마이그레이션 프로세스를 완료한 후에 VM을 다른 구독으로 이동하려고 할 수 있습니다. 그러나 Key Vault 리소스를 참조하는 VM에 암호/인증서가 있으면 이러한 이동이 현재 지원되지 않습니다. 아래 지침에 따라 해당 문제를 해결할 수 있습니다. 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>다음 단계

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-ps.md)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-cli.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](migration-classic-resource-manager-community-tools.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.yml)
