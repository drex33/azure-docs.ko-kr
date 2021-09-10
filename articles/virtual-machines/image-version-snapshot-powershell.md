---
title: PowerShell - Shared Image Gallery의 스냅샷 또는 관리 디스크에서 이미지 만들기
description: PowerShell을 사용하여 Shared Image Gallery의 스냅샷 또는 관리 디스크에서 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e437a313b982849e8d22def87774548eaa8ddb8a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688984"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>PowerShell을 사용하여 Shared Image Gallery에서 관리 디스크 또는 스냅샷에서 이미지 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

기존 스냅샷 또는 관리 디스크를 Shared Image Gallery로 마이그레이션하려는 경우, 관리 디스크 또는 스냅샷에서 직접 Shared Image Gallery 이미지를 만들 수 있습니다. 새 이미지 만들기를 테스트한 후에는 원본 관리 디스크 또는 스냅샷을 삭제할 수 있습니다. [Azure CLI](image-version-snapshot-cli.md)를 사용하여 Shared Image Gallery의 관리 디스크 또는 스냅샷에서 이미지를 만들 수도 있습니다.

이미지 갤러리의 이미지에는 두 가지 구성 요소가 있으며, 다음 예제에서는 해당 구성 요소를 생성합니다.
- **이미지 정의** 에는 이미지에 대한 정보 및 이미지 사용에 대한 요구 사항이 포함되어 있습니다. 여기에는 이미지가 Windows인지 Linux인지, 특수한 이미지인지 일반화된 이미지인지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 Shared Image Gallery를 사용할 때 VM을 생성하는 데 사용됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 생성할 때 이미지 버전은 VM의 새 디스크를 만드는 데 사용됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 스냅샷 또는 관리 디스크가 있어야 합니다. 

데이터 디스크를 포함하려면 데이터 디스크 크기가 1TB를 초과할 수 없습니다.

이 문서를 진행하며 필요한 경우 리소스 이름을 바꿉니다.


## <a name="get-the-snapshot-or-managed-disk"></a>스냅샷 또는 관리 디스크 가져오기

[Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot)을 사용하여 리소스 그룹에서 사용할 수 있는 스냅샷 목록을 볼 수 있습니다. 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

스냅샷 이름과 해당 스냅샷이 어떤 리소스 그룹에 들어 있는지 알 수 있으면 `Get-AzSnapshot`를 다시 사용하여 스냅샷 개체를 가져오고 나중에 사용할 수 있게 변수에 저장할 수 있습니다. 이 예제에서는 "myResourceGroup" 리소스 그룹에서 *mySnapshot* 이라는 스냅샷을 가져온 후 변수 *$source* 에 할당합니다. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

스냅샷 대신 관리 디스크를 사용할 수도 있습니다. 관리 디스크를 가져오려면 [Get-AzDisk](/powershell/module/az.compute/get-azdisk)을 사용합니다. 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

그런 다음 관리 디스크를 가져와 `$source` 변수에 할당합니다.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

동일한 cmdlets를 사용하여 이미지에 포함하고 싶은 데이터 디스크를 가져올 수 있습니다. 변수에 할당한 다음, 나중에 이미지 버전을 만들 때 해당 변수를 사용하세요.


## <a name="get-the-gallery"></a>갤러리 가져오기

모든 갤러리 및 이미지 정의를 이름으로 나열할 수 있습니다. 결과는 `gallery\image definition\image version` 형식으로 제공됩니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

올바른 갤러리를 찾았으면 나중에 사용할 변수를 만듭니다. 이 예제에서는 *myResourceGroup* 리소스 그룹에서 *myGallery* 라는 갤러리를 가져옵니다.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기 

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이는 이미지에 대한 정보를 관리하는 데 사용됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의를 만들 때 모든 정보가 올바른지 확인합니다. 해당 예제에서는 사용 중인 VM에 스냅샷 또는 관리 디스크가 있고, 일반화되지 않았다고 가정합니다. 관리 디스크 또는 스냅샷이 일반화된 OS(Windows용 Sysprep이나 Linux용 [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` 또는 `-deprovision+user` 실행 후)에서 생성된 경우 `-OsState`를 `generalized`로 변경합니다. 

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 정의를 만듭니다. 다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 특수 Windows OS 이미지에 대한 것입니다. Linux OS를 사용하여 이미지에 대한 정의를 만들려면 `-OsType Linux`를 사용합니다. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `-PurchasePlanName`, `-PurchasePlanProduct` 및 `-PurchasePlanPublisher`를 추가하여 이미지 정의에 계획 정보를 포함합니다.
>

### <a name="purchase-plan-information"></a>구매 계획 정보

경우에 따라 Azure Marketplace 이미지를 기반으로 하는 이미지에서 VM을 만들 때 구매 계획 정보를 전달해야 합니다. 이러한 경우에는 이미지 정의에 구매 계획 정보를 포함하는 것이 좋습니다. 이 경우, [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.


## <a name="create-an-image-version"></a>이미지 버전 만들기

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 스냅샷에서 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

OS 디스크 외에도 이미지에 데이터 디스크를 포함하려면 `-DataDiskImage` 매개 변수를 추가하고 데이터 디스크 스냅샷 또는 관리 디스크의 ID로 설정합니다.

이 예제에서 이미지 버전은 *1.0.0* 이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제를 위해 대상 지역을 선택할 때 *원본* 지역을 복제 대상으로 포함해야 한다는 점을 주의합니다.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 작업의 진행률을 보려면 `$job.State`를 입력합니다.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 동일한 스냅샷을 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다. 
>
> 또한 이미지 버전을 만들 때 `-StorageAccountType Standard_ZRS`를 추가하여 이미지 버전을 [영역 중복 스토리지](../storage/common/storage-redundancy.md)에 저장할 수 있습니다.
>

## <a name="delete-the-source"></a>원본 삭제

새 이미지 버전이 제대로 작동하는지 확인한 후에는 [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) 또는 [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk)를 사용하여 이미지의 원본을 삭제할 수 있습니다.


## <a name="next-steps"></a>다음 단계

복제가 완료되었는지 확인한 후에는 [특수화된 이미지](vm-specialized-image-version-powershell.md)에서 VM을 만들 수 있습니다.

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
