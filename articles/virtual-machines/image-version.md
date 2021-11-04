---
title: 이미지 정의 및 이미지 버전 만들기
description: Azure Compute 갤러리에서 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e52457a67e2c653d1fa4e191ee568b35cb7e365f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471519"
---
# <a name="create-an-image-definition-and-an-image-version"></a>이미지 정의 및 이미지 버전 만들기 

[Azure Compute](shared-image-galleries.md) 갤러리(이전의 Shared Image Gallery)는 조직 전체에서 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 배포 작업에 사용자 지정 이미지를 사용할 수 있습니다. 

Azure Compute 갤러리를 사용하면 AAD 테넌트 내 또는 지역 간에 조직의 다른 사용자와 사용자 지정 VM 이미지를 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 이미지를 논리적으로 그룹화할 수 있도록 여러 갤러리를 만들 수 있습니다. 

Azure Compute 갤러리 기능에는 여러 리소스 종류가 있습니다.

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 기존 Azure Compute 갤러리와 Azure에서 사용할 수 있는 이미지 원본이 있어야 합니다. 이미지 원본은 다음과 같습니다.
- 구독의 VM. [특수화된 VM과 일반화된](shared-image-galleries.md#generalized-and-specialized-images) VM 모두에서 이미지를 캡처할 수 있습니다. 
- 관리형 이미지,
- 관리되는 OS 및 데이터 디스크.
- 스토리지 계정에서 OS 및 데이터 디스크를 VHD로 사용합니다.
- 동일한 갤러리 또는 동일한 구독의 다른 갤러리에 있는 다른 이미지 버전입니다.

이미지에 데이터 디스크가 포함될 경우 데이터 디스크 크기는 1 TB를 초과할 수 없습니다.

이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](shared-image-galleries.md#image-definitions)를 참조하세요.

이미지 버전에 허용되는 문자는 숫자와 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 문서를 진행하며 필요한 경우 리소스 이름을 바꿉니다.

## <a name="create-an-image"></a>이미지 만들기 

아래 옵션을 선택하여 이미지 정의 및 이미지 버전을 만듭니다.

### <a name="portal"></a>[포털](#tab/portal)

포털의 VM에서 이미지를 만들려면 [VM의 이미지 캡처를 참조하세요.](capture-image-portal.md) 

VM 이외의 원본을 사용하여 이미지를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)으로 이동한 **다음, Azure Compute 갤러리를** 검색하여 선택합니다.
1. 목록에서 사용하려는 갤러리를 선택합니다.
1. 갤러리 페이지의 위쪽에서 **추가를** 선택한 다음, 드롭다운에서 **VM 이미지 정의를** 선택합니다. 
1. Azure Compute **갤러리에 새 이미지 정의 추가** 페이지의 **기본 탭에서** **지역을** 선택합니다. 
1. **이미지 정의 이름** 에 *myImageDefinition* 과 같은 이름을 입력합니다.
1. **운영 체제** 의 경우 원본에 따라 올바른 옵션을 선택합니다.  
1. **VM 생성의** 경우 원본에 따라 옵션을 선택합니다. 대부분의 경우 *Gen 1* 입니다. 자세한 내용은 [Gen2 VM에 대한 지원](generation-2.md)을 참조하세요.
1. **운영 체제 상태** 의 경우 원본에 따라 옵션을 선택합니다. 자세한 내용은 [일반화 및 특수화](shared-image-galleries.md#generalized-and-specialized-images)를 참조하세요.
1. **Publisher** 경우 *myPublisher* 와 같은 고유한 이름을 입력합니다. 
1. **제안에** myOffer 와 같은 고유한 이름을 *입력합니다.*
1. **SKU의** 경우 mySKU 와 같은 고유한 이름을 *입력합니다.*
1. 페이지의 아래쪽에서 **검토 + 생성** 를 선택합니다.
1. 이미지 정의가 유효성 검사를 통과하면 **만들기** 를 선택합니다.
1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.
1. 이미지 정의 페이지의 **시작** 탭에서 **버전 만들기를** 선택합니다.
1. **지역** 에서 이미지를 만들 지역을 선택합니다. 경우에 따라 원본은 이미지가 만들어지는 동일한 지역에 있어야 합니다. 이후 드롭다운에 나열된 원본이 표시되지 않는 경우 이미지의 지역을 변경해 보세요. 나중에 언제든지 다른 지역에 이미지를 복제할 수 있습니다.
1. **버전 번호** 에 대해 *1.0.0* 과 같은 번호를 입력합니다. 이미지 버전 이름은 정수를 사용하여 정.부.패치 형식을 따라야 합니다. 
1. **원본** 의 드롭다운에서 원본에 사용 중인 파일 형식을 선택합니다. 각 원본 유형에 대한 구체적인 정보는 아래 표를 참조하세요.

    | 원본 | 기타 필드 |
    |---|---|
    | 디스크 또는 스냅샷 | - **OS 디스크** 에 대해 드롭다운에서 디스크 또는 스냅샷을 선택합니다. <br> - 데이터 디스크를 추가하려면 LUN 번호를 입력한 다음, 드롭다운에서 데이터 디스크를 선택합니다. |
    | 이미지 버전 | - 드롭다운에서 **원본 갤러리** 를 선택합니다. <br> - 드롭다운에서 올바른 이미지 정의를 선택합니다. <br>- 드롭다운에서 사용하려는 기존 이미지 버전을 선택합니다. |
    | 관리형 이미지 | 드롭다운에서 **원본 이미지** 를 선택합니다. <br>관리형 이미지는 **인스턴스 세부 정보** 에서 선택한 것과 동일한 지역에 있어야 합니다.
    | 스토리지 계정의 VHD | **찾아보기** 를 선택하여 VHD에 대한 스토리지 계정을 선택합니다. |

1. **최신에서 제외에서** 버전 번호 대신 를 사용하여 VM을 만들 때 이 버전을 사용하지 않으려면 기본값인 *아니요를* 그대로 둡니다. `latest`
1. **수명 종료 날짜 의** 경우 달력에서 이 버전 사용을 중지해야 하는 날짜를 선택합니다.
1. **복제** 탭의 드롭다운에서 스토리지 유형을 선택합니다.
1. **기본 복제본 수** 를 설정합니다. 추가하는 각 지역에 대해 이를 재정의할 수 있습니다. 
1. 원본 지역에 복제해야 하므로 목록의 첫 번째 복제본이 이미지를 만든 지역에 있게 됩니다. 드롭다운에서 영역을 선택하고 필요에 따라 복제본 수를 조정하여 복제본을 더 추가할 수 있습니다.
1. 완료되면 **검토 + 만들기** 를 선택합니다. Azure가 구성의 유효성을 검사합니다.
1. 이미지 버전이 유효성 검사를 통과하면 **만들기** 를 선택합니다.
1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

이미지를 모든 대상 영역에 복제하는 데 시간이 걸릴 수 있습니다.

포털에서 기존 VM을 이미지로 캡처할 수도 있습니다. 자세한 내용은 [포털에서 VM 이미지 만들기](capture-image-portal.md)를 참조하세요.

### <a name="cli"></a>[CLI](#tab/cli)

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 내부에서 생성된 이미지 버전에 대한 정보를 관리하는 데 사용됩니다.

[az sig image-definition create를](/cli/azure/sig/image-definition#az_sig_image_definition_create)사용하여 갤러리에서 이미지 정의를 만듭니다. 이미지 정의가 올바른 형식인지 확인합니다. VM을 일반화한 경우(Windows용 Sysprep 사용 또는 Linux용 waagent -프로비전 해제) `--os-state generalized`를 사용하여 일반화된 이미지 정의를 만들어야 합니다. 기존 사용자 계정을 제거하지 않고 VM을 사용하려는 경우 `--os-state specialized`를 사용하여 특수화된 이미지 정의를 만듭니다.

이미지 정의에 대해 지정할 수 있는 매개 변수에 대한 자세한 내용은 [이미지 정의를 참조하세요.](shared-image-galleries.md#image-definitions)

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 [특수](shared-image-galleries.md#generalized-and-specialized-images) Linux OS 이미지에 대한 것입니다. Windows OS를 사용하여 이미지에 대한 정의를 만들려면 `--os-type Windows`를 사용합니다. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> 타사 마켓플레이스 이미지의 내림차순 이미지를 포함하는 이미지 정의의 경우 계획 정보가 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `--plan-name`, `--plan-product` 및 `--plan-publisher`를 추가하여 이미지 정의에 계획 정보를 포함합니다.
>

**이미지 버전 만들기**

[az sig image version create 를](/cli/azure/sig/image-version#az_sig_image_version_create)사용하여 이미지 버전을 만듭니다.  

이미지를 만들기 위한 구문은 원본으로 사용하는 내용에 따라 변경됩니다. OS 원본이 하나만 있는 한 원본 유형을 혼합할 수 있습니다. 각 데이터 디스크에 대해 서로 다른 원본을 사용할 수도 있습니다.

| 원본  | 매개 변수 집합 |
|---|---|
| **OS 디스크**| |
| Vm ID를 사용 하는 VM| `--managed-image <Resource ID of the VM>` |
| 관리 되는 이미지 또는 다른 이미지 버전 | `--managed-image <Resource ID of the managed image or image version` |
| 스냅숏 또는 관리 디스크 | `--os-snapshot <Resource ID of the snapshot or managed disk>` |
| 스토리지 계정의 VHD | `--os-vhd-uri <URI> --os-vhd-storage-account <storage account name>`.  | 
| **데이터 디스크** |
| 스냅숏 또는 관리 디스크 | `--data-snapshots <Resource ID of the snapshot or managed disk> --data-snapshot-luns <LUN number>` |
| 스토리지 계정의 VHD | `--data-vhds-sa <storageaccountname> --data-vhds-uris <URI> --data-vhds-luns <LUN number>` |

이미지에 대해 서로 다른 소스를 지정 하는 방법에 대 한 자세한 예제는 [az sig 이미지-버전 만들기 예제](/cli/azure/sig/image-version#az_sig_image_version_create-examples)를 참조 하세요.

아래 예제에서는 **VM** 에서 이미지를 만듭니다. 이 이미지의 버전은 *1.0.0* 이며, 미국 동부 2 지역에는 미국 *서 부* *지역의 복제본* 2 개, 영역 중복 저장소를 사용 하는 *미국 동부 2* 지역에는 복제본 2 개를 만듭니다. 복제 지역에는 원본 VM이 있는 지역이 포함되어야 합니다.

이미지를 만들기 전에 VM의 할당을 취소 하는 것이 좋습니다.

이 예제의 값을 `--managed-image` VM의 ID로 바꿉니다.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `--storage-account-type  premium_lrs`를 추가하여 프리미엄 스토리지에 추가하거나 `--storage-account-type  standard_zrs`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이미지 정의를 만들 때 모든 정보가 올바른지 확인합니다. 이미지에 대 한 원본 (Windows 용 Sysprep 사용 또는 Linux 용 waagent 프로 비전 해제)을 일반화 한 경우를 사용 하 여 이미지 정의를 만들어야 합니다 `-OsState generalized` . 소스를 일반화 하지 않은 경우을 사용 하 여 이미지 정의를 만듭니다 `-OsState specialized` .

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 정의를 만듭니다. 

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 Windows를 실행하는 특수 VM에 대한 것입니다. Linux를 사용하여 이미지에 대한 정의를 만들려면 `-OsType Linux`를 사용합니다. 

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

**이미지 버전 만들기**

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion)을 사용하여 이미지 버전을 만듭니다. 

이미지를 만드는 구문은 원본으로 사용 하는 항목에 따라 변경 됩니다. 

| 원본  | 매개 변수 집합 |
|---|---|
| **OS 디스크**| |
| Vm ID를 사용 하는 VM| `-SourceImageId <Resource ID of the VM>` |
| 관리 되는 이미지 또는 다른 이미지 버전 | `-SourceImageId <Resource ID of the managed image or image version` |
| 스냅숏 또는 관리 디스크 | `-OSDiskImage <Resource ID of the snapshot or managed disk>` |
| **데이터 디스크** |
| 스냅숏 또는 관리 디스크 | `-DataDiskImage @{Source = @{Id=<source_id>}; Lun=<LUN>; SizeInGB = <Size in GB>; HostCaching = <Caching> }` |


아래 예제에서는 VM에서 이미지 버전을 만듭니다. [Stop-AzVM](/powershell/module/az.compute/stop-azvm)을 사용하여 이미지를 만들기 전에 VM을 중지/할당 취소하는 것이 좋습니다.

이 예제에서 이미지 버전은 *1.0.0* 이며, *미국 중서부* 및 *미국 중남부* 데이터 센터 둘 다에 복제됩니다. 복제를 위해 대상 지역을 선택할 때 *원본* 지역을 복제 대상으로 포함해야 한다는 점을 주의합니다.


```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

이미지를 모든 대상 지역에 복제하는 데는 다소 시간이 소요되므로 진행 상태를 추적할 수 있게 작업을 만들었습니다. 작업의 진행률을 보려면 `$job.State`를 입력합니다.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `-StorageAccountType Premium_LRS`를 추가하여 프리미엄 스토리지에 추가하거나 `-StorageAccountType Standard_ZRS`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>

### <a name="rest"></a>[REST (영문)](#tab/rest)

[REST API](/rest/api/compute/gallery-images/create-or-update) 를 사용 하 여 이미지 정의 만들기

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}?api-version=2019-12-01

{
    "location": "eastus",
    "properties": {
        "hyperVGeneration": "V1",
        "identifier": {
            "offer": "myOffer",
            "publisher": "myPublisher",
            "sku": "mySKU"
        },
        "osState": "Specialized",
        "osType": "Linux",
    },
}
```

[REST API](/rest/api/compute/gallery-image-versions/create-or-update)를 사용 하 여 이미지 버전을 만듭니다. 이 예제에서는 VM에서 이미지 버전을 만듭니다. 다른 원본을 사용 하려면 원본에 대 한 리소스 ID (예: OS 디스크 스냅숏의 ID를 전달)를 전달 합니다.

```rest
# @name imageVersion
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}/versions/{galleryImageVersionName}?api-version=2019-12-01

{
    "location": "{region}",
    "properties": {
        "publishingProfile": {
            "endOfLifeDate": "2024-12-02T00:00:00+00:00",
            "replicaCount": 1,
            "storageAccountType": "Standard_ZRS",
            "targetRegions": [
                {
                    "name": "eastus",
                    "regionalReplicaCount": 2,
                    "storageAccountType": "Standard_LRS",
                },
                {
                    "name": "westus2",
                }
            ]
        },
        "storageProfile": {
            "source": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}"
            }
        }
    }
}
```

---


## <a name="next-steps"></a>다음 단계

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
