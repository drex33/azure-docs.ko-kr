---
title: Azure CLI를 사용하여 관리 디스크 스토리지를 여러 디스크 유형으로 변환
description: Azure CLI를 사용하여 관리 디스크 스토리지를 여러 디스크 유형으로 변환하는 방법입니다.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 385d801d43a41bb836e04398427fff6c01b1e357
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692132"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure 관리 디스크 스토리지를 표준에서 프리미엄으로, 또는 프리미엄에서 표준으로 변환

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure 관리 디스크에는 Azure 울트라 디스크, 프리미엄 SSD, 표준 SSD, 표준 HDD의 네 가지 디스크 유형이 있습니다. 성능 요구 사항에 따라 프리미엄 SSD, 표준 SSD, 표준 HDD 간에 전환할 수 있습니다. 아직 울트라 디스크와의 전환은 불가능하기 때문에 새 디스크를 배포해야 합니다.

이 기능은 현재 비관리 디스크에 지원되지 않습니다. 하지만 [비관리 디스크를 관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)하면 디스크 유형을 간편하게 전환할 수 있습니다.

이 문서에서는 Azure CLI를 사용하여 관리 디스크 유형을 변환하는 방법을 보여줍니다. 도구를 설치하거나 업그레이드하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

* 디스크 변환은 기존 유지 관리 기간 동안 디스크 스토리지의 마이그레이션을 예약하도록 가상 머신(VM)의 재시작이 필요합니다.
* 비관리 디스크의 경우 먼저 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)하여 스토리지 옵션을 전환할 수 있습니다.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>VM의 모든 관리 디스크를 한 계정에서 다른 계정으로 전환

이 예제에서는 VM의 모든 디스크를 Premium Storage로 변환하는 방법을 보여줍니다. 그러나 이 예제에서는 sku 변수를 변경하여 VM의 디스크 유형을 표준 SSD 또는 표준 HDD로 변환할 수 있습니다. 프리미엄 관리 디스크를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](../sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>개별 관리 디스크 유형 전환

개발/테스트 워크로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합하는 것이 좋습니다. 더 나은 성능이 필요한 디스크만 업그레이드하도록 선택할 수 있습니다. 이 예제에서는 단일 VM 디스크를 표준에서 Premium Storage로 변환하는 방법을 보여줍니다. 그러나 이 예제에서는 sku 변수를 변경하여 VM의 디스크 유형을 표준 SSD 또는 표준 HDD로 변환할 수 있습니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](../sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>관리 디스크 형식 전환

다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **가상 머신** 목록에서 VM을 선택합니다.
3. VM이 중지되지 않는 경우 VM **개요** 창 상단에서 **중지** 를 선택하고 VM이 중지될 때까지 기다립니다.
4. VM 창 메뉴에서 **디스크** 를 선택합니다.
5. 변환할 디스크를 선택합니다.
6. 메뉴에서 **구성** 을 선택합니다.
7. **계정 유형** 을 원래 디스크 유형에서 원하는 디스크 유형으로 변경합니다.
8. **저장** 을 선택하고 디스크 창을 닫습니다.

즉시 디스크 형식이 업데이트됩니다. 변환 후 VM을 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[스냅샷](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.