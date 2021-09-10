---
title: VM용 Ultra Disk - Azure 관리 디스크
description: Azure VM용 Ultra Disk에 대해 알아보기
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5ea12e47b67f79ab2d5aa559f3afc1db587e1a55
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695916"
---
# <a name="using-azure-ultra-disks"></a>Azure Ultra Disk 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서에서는 Ultra Disk를 배포하고 사용하는 방법을 설명합니다. Ultra Disk에 대한 개념 정보는 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md#ultra-disk)을 참조하세요.

Azure Ultra Disk는 Azure IaaS VM(가상 머신)에 대해 높은 처리량, 높은 IOPS 및 일관성 있는 낮은 대기 시간 디스크 스토리지를 제공합니다. 이 새 제품은 Microsoft의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. Ultra Disk의 주요 장점 중 하나는 VM을 다시 시작하지 않고도 워크로드에 따라 SSD의 성능을 동적으로 변경하는 기능입니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적 워크로드와 같은 데이터 집약적 워크로드에 적합합니다.

## <a name="ga-scope-and-limitations"></a>GA 범위 및 제한 사항

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>VM 크기 및 지역 가용성 확인

### <a name="vms-using-availability-zones"></a>가용성 영역을 사용하는 VM

Ultra Disk를 활용하려면 사용 중인 가용성 영역을 확인해야 합니다. 모든 지역이 Ultra Disk를 사용하는 모든 VM 크기를 지원하는 것은 아닙니다. 지역, 영역, VM 크기가 Ultra Disk를 지원하는지 확인하려면 다음 명령 중 하나를 실행하여 **지역**, **vmSize**, **구독** 값을 먼저 바꾸어야 합니다.

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

결과는 아래 형식과 유사합니다. 여기서 X는 선택한 지역에서 배포하는 데 사용하는 영역입니다. X는 1, 2 또는 3이 될 수 있습니다.

**영역** 값을 유지합니다. 이 값은 가용성 영역을 나타내며, Ultra Disk를 배포하는 데 필요합니다.

|ResourceType  |이름  |Location  |영역  |제한 사항  |기능  |값  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 명령 실행 결과가 없는 경우 선택한 VM 크기는 선택한 지역의 Ultra Disk에서 지원되지 않습니다.

이제 배포할 영역을 알고 있으므로 이 문서의 배포 단계에 따라 Ultra Disk가 연결된 VM을 배포하거나 기존 VM에 Ultra Disk를 연결합니다.

### <a name="vms-with-no-redundancy-options"></a>중복 옵션이 없는 VM

현재는 선택 지역에 배포된 Ultra Disk를 중복 옵션 없이 배포해야 합니다. 그러나 Ultra Disk를 지원하는 모든 디스크 크기가 이러한 지역에 있을 수 있는 것은 아닙니다. Ultra Disk를 지원하는 디스크 크기를 확인하려면 다음 코드 조각 중 하나를 사용합니다. 먼저 `vmSize` 및 `subscription` 값을 바꾸어야 합니다.

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

결과는 다음 양식과 유사하며, `UltraSSDAvailable   True`는 VM 크기가 이 지역의 Ultra Disk를 지원하는지 여부를 나타냅니다.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Ultra Disk 배포

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록에 대해서는 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조하세요.

여러 개의 Ultra Disk로 VM을 만들려면 [여러 Ultra Disk로 VM 만들기](https://aka.ms/ultradiskArmTemplate) 샘플을 참조하세요.

고유한 템플릿을 사용하려는 경우 `Microsoft.Compute/virtualMachines` 및 `Microsoft.Compute/Disks`에 대한 **apiVersion** 이 `2018-06-01`(이상)로 설정되어 있는지 확인합니다.

디스크 sku를 **UltraSSD_LRS** 로 설정하고 디스크 용량, IOPS, 가용성 영역, 처리량을 MBps 단위로 설정하여 Ultra Disk를 만듭니다.

VM을 프로비전한 후 데이터 디스크를 분할 및 포맷하고 워크로드용 데이터 디스크를 구성할 수 있습니다.


## <a name="deploy-an-ultra-disk"></a>Ultra Disk 배포

# <a name="portal"></a>[포털](#tab/azure-portal)

이 섹션에서는 데이터 디스크로 Ultra Disk를 사용하는 가상 머신을 배포하는 방법을 설명합니다. 가상 머신을 배포하는 방법을 잘 알고 있다고 가정합니다. 잘 모를 경우에는 [빠른 시작: Azure Portal에서 Windows 가상 머신 만들기](./windows/quick-create-portal.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, VM(가상 머신)으로 이동하여 배포합니다.
1. [지원되는 VM 크기 및 지역](#ga-scope-and-limitations)을 선택해야 합니다.
1. **가용성 옵션** 에서 **가용성 영역** 을 선택합니다.
1. 나머지 항목을 선택한 항목으로 채웁니다.
1. **디스크** 를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png" alt-text="VM 만들기 흐름, 기본 사항 블레이드 스크린샷" lightbox="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png":::

1. 디스크 블레이드에서 **Ultra Disk 호환성 사용** 에 대해 **예** 를 선택합니다.
1. **새 디스크 만들기 및 연결** 을 선택하여 이제 Ultra Disk를 연결합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-disk-enable.png" alt-text="VM 만들기 흐름, 디스크 블레이드, Ultra를 사용하도록 설정되고, 새 디스크 만들기 및 연결이 강조 표시된 스크린샷" :::

1. **새 디스크 만들기** 블레이드에서 이름을 입력하고 **크기 변경** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-create-disk.png" alt-text="새 디스크 만들기 블레이드, 크기 변경이 강조 표시된 스크린샷":::


1. **디스크 SKU** 를 **Ultra Disk** 로 변경합니다.
1. **사용자 지정 디스크 크기(GiB)** , **디스크 IOPS**, **디스크 처리량** 을 원하는 값으로 변경합니다.
1. 두 블레이드에서 **확인** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-select-ultra-disk-size.png" alt-text="디스크 크기 선택 블레이드, 스토리지 유형으로 선택된 Ultra Disk, 기타 값이 강조 표시된 스크린샷":::

1. VM 배포를 계속 진행합니다. 진행 방법은 다른 VM을 배포할 때와 동일합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록에 대해서는 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조하세요.

Ultra Disk를 연결하려면 Ultra Disk를 사용할 수 있는 VM을 만들어야 합니다.

**$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** 변수를 고유한 값으로 바꾸거나 설정합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 을 설정합니다. 그런 다음 아래 CLI 명령을 실행하여 Ultra를 사용한 VM을 만듭니다.

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록에 대해서는 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조하세요.

Ultra Disk를 사용하려면 Ultra Disk를 사용할 수 있는 VM을 만들어야 합니다. **$resourcegroup** 및 **$vmName** 변수를 고유한 값으로 바꾸거나 설정합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 을 설정합니다. 그런 다음 아래 [New-AzVm](/powershell/module/az.compute/new-azvm) 명령을 실행하여 Ultra를 사용한 VM을 만듭니다.

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>디스크 만들기 및 연결

VM을 배포한 후에는 Ultra Disk를 만들어 VM에 연결할 수 있습니다. 다음 스크립트를 사용합니다.

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>Ultra Disk - 512바이트 섹터 크기 배포

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인한 다음 **디스크** 를 검색하여 선택합니다.
1. **+ 새로 만들기** 를 선택하여 새 디스크를 만듭니다.
1. Ultra Disk를 지원하는 지역을 선택하고 가용성 영역을 선택한 다음 나머지 값을 원하는 대로 입력합니다.
1. **크기 변경** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/create-managed-disk-basics-workflow.png" alt-text="디스크 만들기 블레이드, 지역, 가용성 영역, 크기 변경이 강조 표시된 스크린샷":::

1. **디스크 SKU** 에 대해 **Ultra Disk** 를 선택하고 원하는 성능에 대한 값을 입력한 다음 **확인** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-disk-size-ultra.png" alt-text="Ultra Disk를 만드는 스크린샷":::

1. **기본 사항** 블레이드에서 **고급** 탭을 선택합니다.
1. **논리 섹터 크기** 에 대해 **512** 를 선택하고 **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-different-sector-size-ultra.png" alt-text="Ultra Disk 논리 섹터 크기를 512로 변경하는 선택기 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록에 대해서는 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조하세요.

Ultra Disk를 연결하려면 Ultra Disk를 사용할 수 있는 VM을 만들어야 합니다.

**$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** 변수를 고유한 값으로 바꾸거나 설정합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 을 설정합니다. 그런 다음 아래 CLI 명령을 실행하여 512바이트 섹터 크기의 Ultra Disk를 사용하는 VM을 만듭니다.

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록에 대해서는 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조하세요.

Ultra Disk를 사용하려면 Ultra Disk를 사용할 수 있는 VM을 만들어야 합니다. **$resourcegroup** 및 **$vmName** 변수를 고유한 값으로 바꾸거나 설정합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 을 설정합니다. 그런 다음 아래 [New-AzVm](/powershell/module/az.compute/new-azvm) 명령을 실행하여 Ultra를 사용한 VM을 만듭니다.

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

512바이트 섹터 크기의 Ultra Disk를 만들고 연결하려면 다음 스크립트를 사용합니다.

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>Ultra Disk 연결

# <a name="portal"></a>[포털](#tab/azure-portal)

또는 Ultra Disk를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 Ultra Disk를 사용할 수 있습니다. Ultra Disk를 기존 VM에서 사용하도록 설정하고 데이터 디스크로 연결합니다. Ultra Disk 호환성을 사용하려면 VM을 중지해야 합니다. VM을 중지한 후에 호환성을 사용하도록 설정한 다음 VM을 다시 시작할 수 있습니다. 호환성을 사용하도록 설정하면 Ultra Disk를 연결할 수 있습니다.

1. VM으로 이동하여 중지하고 할당이 취소될 때까지 기다립니다.
1. VM 할당이 취소되면 **디스크** 를 선택합니다.
1. **추가 설정** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-disk-additional-settings.png" alt-text="추가 설정이 강조 표시된 디스크 블레이드 스크린샷":::

1. **Ultra Disk 호환성 사용** 에 대해 **예** 를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Ultra Disk 호환성 사용 스크린샷":::

1. **저장** 을 선택합니다.
1. **새 디스크 만들기 및 연결** 을 선택하고 새 디스크의 이름을 입력합니다.
1. **스토리지 유형** 으로 **Ultra Disk** 를 선택합니다.
1. **크기(GiB)** , **최대 IOPS**, **최대 처리량** 값을 원하는 값으로 변경합니다.
1. 디스크 블레이드로 돌아가면 **저장** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-create-ultra-disk-existing-vm.png" alt-text="새 Ultra Disk를 추가하는 디스크 블레이드 스크린샷":::

1. VM을 다시 시작합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

또는 Ultra Disk를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 Ultra Disk를 사용할 수 있습니다.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>기존 VM에서 Ultra Disk 호환성 사용 - CLI

VM이 [GA 범위 및 제한 사항](#ga-scope-and-limitations)에 설명된 요구 사항을 충족하고 [계정에 적절한 영역](#determine-vm-size-and-region-availability)에 있는 경우 VM에서 Ultra Disk 호환성을 사용하도록 설정할 수 있습니다.

Ultra Disk 호환성을 사용하려면 VM을 중지해야 합니다. VM을 중지한 후에 호환성을 사용하도록 설정한 다음 VM을 다시 시작할 수 있습니다. 호환성을 사용하도록 설정하면 Ultra Disk를 연결할 수 있습니다.

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Ultra Disk 만들기 - CLI

이제 Ultra Disk를 연결할 수 있는 VM이 있으므로 Ultra Disk를 만들어 VM에 연결할 수 있습니다.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>디스크 연결 - CLI

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

또는 Ultra Disk를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 Ultra Disk를 사용할 수 있습니다.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>기존 VM에서 Ultra Disk 호환성 사용 - PowerShell

VM이 [GA 범위 및 제한 사항](#ga-scope-and-limitations)에 설명된 요구 사항을 충족하고 [계정에 적절한 영역](#determine-vm-size-and-region-availability)에 있는 경우 VM에서 Ultra Disk 호환성을 사용하도록 설정할 수 있습니다.

Ultra Disk 호환성을 사용하려면 VM을 중지해야 합니다. VM을 중지한 후에 호환성을 사용하도록 설정한 다음 VM을 다시 시작할 수 있습니다. 호환성을 사용하도록 설정하면 Ultra Disk를 연결할 수 있습니다.

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Ultra Disk 만들기 및 연결 - PowerShell

이제 Ultra Disk를 사용할 수 있는 VM이 있으므로 Ultra Disk를 만들어 VM에 연결할 수 있습니다.

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Ultra Disk의 성능 조정

# <a name="portal"></a>[포털](#tab/azure-portal)

Ultra Disk는 성능을 조정할 수 있는 고유한 기능을 제공합니다. 디스크 자체의 Azure Portal에서 이러한 조정을 수행할 수 있습니다.

1. VM으로 이동하여 **디스크** 를 선택합니다.
1. 성능을 수정할 Ultra Disk를 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-ultra-disk-to-modify.png" alt-text="Ultra Disk가 강조 표시된 VM의 디스크 블레이드 스크린샷":::

1. **크기 + 성능** 을 선택한 후 수정합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/modify-ultra-disk-performance.png" alt-text="디스크 크기, iops, 처리량이 강조 표시되고 저장이 강조 표시된 Ultra Disk의 구성 블레이드 스크린샷":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ultra Disk는 성능을 조정하는 데 사용할 수 있는 고유한 기능을 제공합니다. 다음 명령은 이 기능을 사용하는 방법을 보여 줍니다.

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>PowerShell을 사용하여 Ultra Disk의 성능 조정

Ultra Disk에는 성능을 조정할 수 있는 고유한 기능이 있습니다. 다음 명령은 디스크를 분리하지 않고도 성능을 조정하는 예제입니다.

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>다음 단계

- [Azure Kubernetes Service(미리 보기)에서 Azure Ultra Disk를 사용](../aks/use-ultra-disks.md)합니다.
- [로그 디스크를 Ultra Disk로 마이그레이션](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md)합니다.
