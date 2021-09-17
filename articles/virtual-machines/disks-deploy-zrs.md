---
title: ZRS 관리 디스크 배포
description: ZRS(영역 중복 스토리지)를 사용하는 관리 디스크를 배포하는 방법에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 09/01/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 23629a5a15603697eb1feb6e964e63754fbed14c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426738"
---
# <a name="deploy-a-managed-disk-that-uses-zone-redundant-storage"></a>영역 중복 스토리지를 사용하는 관리 디스크 배포

이 문서에서는 ZRS(영역 중복 스토리지)를 중복 옵션으로 사용하는 디스크를 배포하는 방법을 설명합니다. ZRS는 선택된 지역에 있는 3개의 Azure 가용성 영역에서 Azure 관리 디스크를 동기적으로 복제합니다. 각 가용성 영역은 독립적인 전원, 냉각 및 네트워킹을 갖춘 별도의 물리적 위치입니다.

ZRS에 대한 개념 정보는 [관리 디스크에 대한 영역 중복 스토리지를 참조하세요.](disks-redundancy.md#zone-redundant-storage-for-managed-disks)

## <a name="limitations"></a>제한 사항

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="prerequisites"></a>필수 구성 요소

구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계를 사용하여 구독에 대한 기능을 사용하도록 설정합니다.

1.  다음 명령을 실행하여 구독 기능을 등록합니다.

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  기능을 사용해보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 걸릴 수 있음)인지 확인합니다.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```

### <a name="create-a-vm-with-a-zrs-os-disk"></a>ZRS OS 디스크를 사용하여 VM 만들기

1. Azure Portal에 로그인합니다.
1. **가상 머신** 으로 이동하여 일반 VM 생성 프로세스를 따릅니다.
1. 지원되는 지역을 선택하고 **가용성 옵션** 을 **인프라 중복성 필요 없음** 으로 설정합니다.

    :::image type="content" source="media/disks-deploy-zrs/disks-zrs-portal-basic.png" alt-text="VM 만들기 워크플로, 기본 창, 중복성 및 지역의 스크린샷이 강조 표시됩니다." lightbox="media/disks-deploy-zrs/disks-zrs-portal-basic.png":::

1. **디스크** 창으로 이동합니다.
1. 디스크를 선택하고 드롭다운에서 ZRS 디스크 중 하나를 선택합니다.

    :::image type="content" source="media/disks-deploy-zrs/disks-zrs-portal-select-blade.png" alt-text="VM 만들기 워크플로, 디스크 창, OS 디스크 드롭다운의 스크린샷이 강조 표시된 ZRS 프리미엄 SSD 및 표준 SSD 옵션으로 확장됩니다." lightbox="media/disks-deploy-zrs/disks-zrs-portal-select-blade.png":::

1. VM 배포의 나머지 단계를 진행하여 원하는 항목을 선택합니다.

이제 ZRS OS 디스크를 사용하여 VM을 배포했습니다.

### <a name="create-a-zrs-disk"></a>ZRS 디스크 만들기

1. Azure Portal에서 **디스크** 를 검색하여 선택합니다.
1. **+추가** 를 선택하여 새 디스크를 만듭니다.
1. 지원되는 지역 및 **가용성 영역** 을 **없음** 으로 선택합니다.
1. **크기 변경** 을 선택합니다.

    :::image type="content" source="media/disks-deploy-zrs/create-zrs-disk-pane.png" alt-text="디스크 만들기 워크플로, 기본 사항 창의 스크린샷." lightbox="media/disks-deploy-zrs/create-zrs-disk-pane.png":::

1. 사용 가능한 ZRS 디스크 중 하나를 선택하고 **확인** 을 선택합니다.

    :::image type="content" source="media/disks-deploy-zrs/select-zrs-disk-sku.png" alt-text="디스크 만들기 워크플로의 스크린샷에서 디스크 크기 창을 선택하고 ZRS 디스크를 강조 표시합니다." lightbox="media/disks-deploy-zrs/select-zrs-disk-sku.png":::

1. 배포 프로세스 계속 진행

이제 ZRS를 사용하는 관리 디스크를 만들었습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="prerequisites"></a>필수 구성 요소

구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계를 사용하여 구독에 대한 기능을 사용하도록 설정합니다.

1.  다음 명령을 실행하여 구독 기능을 등록합니다.

    ```azurecli
    az feature register --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```
 
2.  기능을 사용해보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 걸릴 수 있음)인지 확인합니다.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```

#### <a name="create-a-vm-with-zrs-disks"></a>ZRS 디스크로 VM 만들기

```azurecli
rgName=yourRGName
vmName=yourVMName
location=westus2
vmSize=Standard_DS2_v2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS


az vm create -g $rgName \
-n $vmName \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```
#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>다른 영역의 VM에 연결된 공유 ZRS 디스크를 사용하여 VM 만들기
```azurecli

location=westus2
rgName=yourRGName
vmNamePrefix=yourVMNamePrefix
vmSize=Standard_DS2_v2
image=UbuntuLTS
osDiskSku=StandardSSD_LRS
sharedDiskName=yourSharedDiskName
sharedDataDiskSku=Premium_ZRS


az disk create -g $rgName \
-n $sharedDiskName \
-l $location \
--size-gb 1024 \
--sku $sharedDataDiskSku \
--max-shares 2


sharedDiskId=$(az disk show -g $rgName -n $sharedDiskName --query 'id' -o tsv)

az vm create -g $rgName \
-n $vmNamePrefix"01" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 1 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

az vm create -g $rgName \
-n $vmNamePrefix"02" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 2 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

```
#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>ZRS 디스크를 사용하여 가상 머신 확장 집합 만들기
```azurecli
location=westus2
rgName=yourRGName
vmssName=yourVMSSName
vmSize=Standard_DS3_V2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)


#### <a name="prerequisites"></a>필수 구성 요소

구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계를 사용하여 구독에 대한 기능을 사용하도록 설정합니다.

1.  다음 명령을 실행하여 구독 기능을 등록합니다.

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  기능을 사용해보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 걸릴 수 있음)인지 확인합니다.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```
    
#### <a name="create-a-vm-with-zrs-disks"></a>ZRS 디스크로 VM 만들기

```powershell
$subscriptionId="yourSubscriptionId"
$vmLocalAdminUser = "yourAdminUserName"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourVMPassword" -AsPlainText -Force
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmName = "yourVMName"
$vmSize = "Standard_DS2_v2"
$osDiskSku = "StandardSSD_ZRS"
$dataDiskSku = "Premium_ZRS"


Connect-AzAccount

Set-AzContext -Subscription $subscriptionId
    
$subnet = New-AzVirtualNetworkSubnetConfig -Name $($vmName+"_subnet") `
                                           -AddressPrefix "10.0.0.0/24"

$vnet = New-AzVirtualNetwork -Name $($vmName+"_vnet") `
                             -ResourceGroupName $rgName `
                             -Location $location `
                             -AddressPrefix "10.0.0.0/16" `
                             -Subnet $subnet

$nic = New-AzNetworkInterface -Name $($vmName+"_nic") `
                              -ResourceGroupName $rgName `
                              -Location $location `
                              -SubnetId $vnet.Subnets[0].Id
    

$vm = New-AzVMConfig -VMName $vmName `
                     -VMSize $vmSize
                     

$credential = New-Object System.Management.Automation.PSCredential ($vmLocalAdminUser, $vmLocalAdminSecurePassword);

$vm = Set-AzVMOperatingSystem -VM $vm `
                              -ComputerName $vmName `
                              -Windows `
                              -Credential $credential

$vm = Add-AzVMNetworkInterface -VM $vm -Id $NIC.Id

$vm = Set-AzVMSourceImage -VM $vm `
                          -PublisherName 'MicrosoftWindowsServer' `
                          -Offer 'WindowsServer' `
                          -Skus '2012-R2-Datacenter' `
                          -Version latest


$vm = Set-AzVMOSDisk -VM $vm `
                     -Name $($vmName +"_OSDisk") `
                     -CreateOption FromImage `
                     -StorageAccountType $osDiskSku

$vm = Add-AzVMDataDisk -VM $vm `
                       -Name $($vmName +"_DataDisk1") `
                       -DiskSizeInGB 128 `
                       -StorageAccountType $dataDiskSku `
                       -CreateOption Empty -Lun 0
    
New-AzVM -ResourceGroupName $rgName `
         -Location $location `
         -VM $vm -Verbose
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>다른 영역의 VM에 연결된 공유 ZRS 디스크를 사용하여 VM 만들기

```powershell
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmNamePrefix = "yourVMPrefix"
$vmSize = "Standard_DS2_v2"
$sharedDiskName = "yourSharedDiskName"
$sharedDataDiskSku = "Premium_ZRS"
$vmLocalAdminUser = "yourVMAdminUserName"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourPassword" -AsPlainText -Force  


$datadiskconfig = New-AzDiskConfig -Location $location `
                                   -DiskSizeGB 1024 `
                                   -AccountType $sharedDataDiskSku `
                                   -CreateOption Empty `
                                   -MaxSharesCount 2 `

$sharedDisk=New-AzDisk -ResourceGroupName $rgName `
            -DiskName $sharedDiskName `
            -Disk $datadiskconfig

$credential = New-Object System.Management.Automation.PSCredential ($vmLocalAdminUser, $vmLocalAdminSecurePassword);
    
$vm1 = New-AzVm `
        -ResourceGroupName $rgName `
        -Name $($vmNamePrefix+"01") `
        -Zone 1 `
        -Location $location `
        -Size $vmSize `
        -VirtualNetworkName $($vmNamePrefix+"_vnet") `
        -SubnetName $($vmNamePrefix+"_subnet") `
        -SecurityGroupName $($vmNamePrefix+"01_sg") `
        -PublicIpAddressName $($vmNamePrefix+"01_ip") `
        -Credential $credential `
        -OpenPorts 80,3389


$vm1 = Add-AzVMDataDisk -VM $vm1 -Name $sharedDiskName -CreateOption Attach -ManagedDiskId $sharedDisk.Id -Lun 0

update-AzVm -VM $vm1 -ResourceGroupName $rgName
  
$vm2 =  New-AzVm `
        -ResourceGroupName $rgName `
        -Name $($vmNamePrefix+"02") `
        -Zone 2 `
        -Location $location `
        -Size $vmSize `
        -VirtualNetworkName $($vmNamePrefix+"_vnet") `
        -SubnetName ($vmNamePrefix+"_subnet") `
        -SecurityGroupName $($vmNamePrefix+"02_sg") `
        -PublicIpAddressName $($vmNamePrefix+"02_ip") `
        -Credential $credential `
        -OpenPorts 80,3389


$vm2 = Add-AzVMDataDisk -VM $vm1 -Name $sharedDiskName -CreateOption Attach -ManagedDiskId $sharedDisk.Id -Lun 0

update-AzVm -VM $vm1 -ResourceGroupName $rgName
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>ZRS 디스크를 사용하여 가상 머신 확장 집합 만들기
```powershell
$vmLocalAdminUser = "yourLocalAdminUser"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourVMPassword" -AsPlainText -Force
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmScaleSetName = "yourScaleSetName"
$vmSize = "Standard_DS3_v2"
$osDiskSku = "StandardSSD_ZRS"
$dataDiskSku = "Premium_ZRS"
   
    
$subnet = New-AzVirtualNetworkSubnetConfig -Name $($vmScaleSetName+"_subnet") `
                                                 -AddressPrefix "10.0.0.0/24"

$vnet = New-AzVirtualNetwork -Name $($vmScaleSetName+"_vnet") `
                             -ResourceGroupName $rgName `
                             -Location $location `
                             -AddressPrefix "10.0.0.0/16" `
                             -Subnet $subnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" `
                               -SubnetId $vnet.Subnets[0].Id 


$vmss = New-AzVmssConfig -Location $location `
                         -SkuCapacity 2 `
                         -SkuName $vmSize `
                         -UpgradePolicyMode 'Automatic'

$vmss = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" `
                                                -VirtualMachineScaleSet $vmss `
                                                -Primary $true `
                                                -IpConfiguration $ipConfig

$vmss = Set-AzVmssStorageProfile $vmss -OsDiskCreateOption "FromImage" `
                                       -ImageReferenceOffer 'WindowsServer' `
                                       -ImageReferenceSku '2012-R2-Datacenter' `
                                       -ImageReferenceVersion latest `
                                       -ImageReferencePublisher 'MicrosoftWindowsServer' `
                                       -ManagedDisk $osDiskSku

$vmss = Set-AzVmssOsProfile $vmss -ComputerNamePrefix $vmScaleSetName `
                                  -AdminUsername $vmLocalAdminUser `
                                  -AdminPassword $vmLocalAdminSecurePassword 

$vmss = Add-AzVmssDataDisk -VirtualMachineScaleSet $vmss `
                           -CreateOption Empty `
                           -Lun 1 `
                           -DiskSizeGB 128 `
                           -StorageAccountType $dataDiskSku

New-AzVmss -VirtualMachineScaleSet $vmss `
           -ResourceGroupName $rgName `
           -VMScaleSetName $vmScaleSetName
```

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

Azure Resource Manager 템플릿과 함께 `2020-12-01` API를 사용하여 ZRS 디스크를 만듭니다.

#### <a name="prerequisites"></a>필수 조건

구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계를 사용하여 구독에 대한 기능을 사용하도록 설정합니다.

1.  다음 명령을 실행하여 구독 기능을 등록합니다.

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  기능을 사용해보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 걸릴 수 있음)인지 확인합니다.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```
    
#### <a name="create-a-vm-with-zrs-disks"></a>ZRS 디스크로 VM 만들기

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>다른 영역의 VM에 연결된 공유 ZRS 디스크를 사용하여 VM 만들기

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>ZRS 디스크를 사용하여 가상 머신 확장 집합 만들기

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```
---

## <a name="next-steps"></a>다음 단계

- 더 많은 [ZRS 디스크로 VM을 만드는 Azure Resource Manager 템플릿](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)을 확인하세요.