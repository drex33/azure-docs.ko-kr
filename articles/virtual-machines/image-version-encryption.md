---
title: 사용자 고유 키로 암호화된 이미지 버전 만들기
description: 고객 관리형 암호화 키를 사용하여 Azure Compute 갤러리에서 이미지 버전을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 7/1/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bcd214413eb4880219e18c4bb03e4430f31690fd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452056"
---
# <a name="use-customer-managed-keys-for-encrypting-images"></a>고객 관리형 키를 사용하여 이미지 암호화

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Azure Compute 갤러리의 이미지(이전의 Shared Image Gallery)는 스냅샷으로 저장되므로 서버 쪽 암호화를 통해 자동으로 암호화됩니다. 서버 쪽 암호화는 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용합니다. 서버 쪽 암호화는 FIPS 140-2 규격이기도 합니다. 암호화 모듈의 기본 Azure Managed Disks에 대한 자세한 정보는 [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal)을 참조하세요.

플랫폼 관리형 키를 사용하여 이미지를 암호화할 수도 있고 사용자 고유의 키를 사용할 수도 있습니다. 이중 암호화를 위해 함께 사용할 수도 있습니다. 사용자 고유의 키를 사용하여 암호화를 관리하는 경우 이미지의 모든 데이터를 암호화 및 암호 해독하는 데 사용할 고객 관리형 키를 지정할 수 있습니다. 

고객 관리형 키를 통한 서버 쪽 암호화는 Azure Key Vault를 사용합니다. [사용자의 RSA 키](../key-vault/keys/hsm-protected-keys.md)를 키 자격 증명 모음으로 가져오거나 Azure Key Vault에서 새 RSA 키를 생성할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 이미지를 복제하려는 각 영역에 디스크 암호화가 이미 설정되어 있어야 합니다.

- 고객 관리형 키만 사용하려면 [Azure Portal](./disks-enable-customer-managed-keys-portal.md) 또는 [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-optionally-with-automatic-key-rotation)을 사용하여 서버 쪽 암호화에 고객 관리형 키를 사용하는 방법에 대한 문서를 참조하세요.

- 플랫폼 관리형 키 및 고객 관리형 키를 모두 사용하려면(이중 암호화의 경우) [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) 또는 [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md)을 사용하여 미사용 데이터에 이중 암호화를 사용하도록 설정하는 방법에 대한 문서를 참조하세요.

   > [!IMPORTANT]
   > 링크 [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates)을 사용하여 Azure Portal에 액세스해야 합니다. 링크를 사용하지 않으면 미사용 데이터 이중 암호화가 공용 Azure Portal에 표시되지 않습니다.

## <a name="limitations"></a>제한 사항

Azure Compute 갤러리에서 이미지를 암호화하기 위해 고객 관리형 키를 사용하는 경우 다음과 같은 제한 사항을 적용합니다. 

- 암호화 키 집합은 이미지와 동일한 구독에 있어야 합니다.

- 암호화 키 집합은 지역별 리소스이므로 지역마다 서로 다른 암호화 키 집합이 필요합니다.

- 고객 관리형 키를 사용하는 이미지는 복사하거나 공유할 수 없습니다. 

- 사용자 고유의 키를 사용하여 디스크 또는 이미지를 암호화한 후에는 플랫폼 관리형 키를 사용하여 해당 디스크나 이미지를 암호화할 수 없습니다.


## <a name="powershell"></a>PowerShell

이미지 버전에 대한 디스크 암호화 집합을 지정하려면 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)을 `-TargetRegion` 매개 변수와 함께 사용합니다. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>VM 만들기

Azure Compute 갤러리에서 VM(가상 머신)을 만들고 고객 관리형 키를 사용하여 디스크를 암호화할 수 있습니다. 구문은 이미지로부터 [일반화된](vm-generalized-image-version.md) VM 또는 [특수화된](vm-specialized-image-version.md) VM을 만드는 것과 같습니다. 확장 매개 변수 집합을 사용하여 `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage`을(를) VM 구성에 추가합니다.

데이터 디스크의 경우 [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)를 사용할 때 `-DiskEncryptionSetId $setID` 매개 변수를 추가합니다.


## <a name="cli"></a>CLI 

이미지 버전에 대해 디스크 암호화를 지정하려면 [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create)을 `--target-region-encryption` 매개 변수와 함께 사용합니다. `--target-region-encryption`의 형식은 OS 및 데이터 디스크를 암호화하기 위한 콤마로 구분된 키 목록입니다. `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`와 비슷한 형식이어야 합니다. 

OS 디스크의 원본이 관리 디스크 또는 VM인 경우 `--managed-image`를 사용하여 이미지 버전의 원본을 지정합니다. 이 예제에서 원본은 OS 디스크 및 LUN 0 수준 데이터 디스크를 포함하는 관리형 이미지입니다. OS 디스크는 DiskEncryptionSet1을 사용하여 암호화되고 데이터 디스크는 DiskEncryptionSet2를 사용하여 암호화됩니다.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

OS 디스크의 원본이 스냅샷이면 `--os-snapshot`을 사용하여 OS 디스크를 지정합니다. 이미지 버전의 일부로도 사용해야 하는 데이터 디스크 스냅샷이 있는 경우 해당 스냅샷을 추가합니다. `--data-snapshot-luns`을(를) 사용하여 LUN을 지정하고 `--data-snapshots`를 사용하여 스냅샷을 지정합니다.

이 예제에서 원본은 디스크 스냅샷입니다. OS 디스크가 있고 LUN 0 수준 데이터 디스크도 있습니다. OS 디스크는 DiskEncryptionSet1을 사용하여 암호화되고 데이터 디스크는 DiskEncryptionSet2를 사용하여 암호화됩니다.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>VM 만들기

Azure Compute 갤러리에서 VM을 만들고 고객 관리형 키를 사용하여 디스크를 암호화할 수 있습니다. 구문은 이미지로부터 [일반화된](vm-generalized-image-version.md) VM 또는 [특수화된](vm-specialized-image-version.md) VM을 만드는 것과 같습니다. 암호화 집합의 ID를 `--os-disk-encryption-set` 매개 변수에 추가하기만 하면 됩니다. 데이터 디스크의 경우 데이터 디스크에 대한 디스크 암호화 집합의 공백으로 구분된 목록을 사용하여 `--data-disk-encryption-sets`을(를) 추가합니다.


## <a name="portal"></a>포털

포털에서 이미지 버전을 만들 때 **암호화** 탭을 사용하여 스토리지 암호화 집합을 적용할 수 있습니다.

1. **이미지 버전 만들기** 페이지에서 **암호화** 탭을 선택합니다.
2. **암호화 유형** 에서 **고객 관리형 키로 미사용 데이터 암호화** 를 선택하거나 **플랫폼 관리형 및 고객 관리형 키를 사용한 이중 암호화** 를 선택합니다. 
3. 이미지에 있는 각 디스크에 대해 **디스크 암호화 집합** 드롭다운 목록에서 암호화 집합을 선택합니다. 

### <a name="create-the-vm"></a>VM 만들기

이미지 버전에서 VM을 만들고, 고객 관리형 키를 사용하여 디스크를 암호화할 수 있습니다. 포털에서 VM을 만들 때 **디스크** 탭에서 **고객 관리형 키로 미사용 데이터 암호화** 또는 **플랫폼 관리형 키 및 고객 관리형 키를 사용한 이중 암호화** 를 **암호화 유형** 으로 선택합니다. 그런 다음 드롭다운 목록에서 암호화 집합을 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[서버 쪽 디스크 암호화](./disk-encryption.md)에 대해 알아봅니다.

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
