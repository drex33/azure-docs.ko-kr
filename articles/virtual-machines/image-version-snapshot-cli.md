---
title: CLI - Shared Image Gallery의 스냅샷 또는 관리 디스크에서 이미지 만들기
description: Azure CLI를 사용하여 Shared Image Gallery의 스냅샷 또는 관리 디스크에서 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1e836366223a7a283a103375b2888afce71311c9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689892"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Azure CLI를 사용하여 Shared Image Gallery에서 관리 디스크 또는 스냅샷에서 이미지 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

기존 스냅샷 또는 관리 디스크를 Shared Image Gallery로 마이그레이션하려는 경우, 관리 디스크 또는 스냅샷에서 직접 Shared Image Gallery 이미지를 만들 수 있습니다. 새 이미지 만들기를 테스트한 후에는 원본 관리 디스크 또는 스냅샷을 삭제할 수 있습니다. [Azure PowerShell](image-version-snapshot-powershell.md)을 사용하여 Shared Image Gallery의 관리 디스크 또는 스냅샷에서 이미지를 만들 수도 있습니다.

이미지 갤러리의 이미지에는 두 가지 구성 요소가 있으며, 다음 예제에서는 해당 구성 요소를 생성합니다.
- **이미지 정의** 에는 이미지에 대한 정보 및 이미지 사용에 대한 요구 사항이 포함되어 있습니다. 여기에는 이미지가 Windows인지 Linux인지, 특수한 이미지인지 일반화된 이미지인지, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. 
- **이미지 버전** 은 Shared Image Gallery를 사용할 때 VM을 생성하는 데 사용됩니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. VM을 생성할 때 이미지 버전은 VM의 새 디스크를 만드는 데 사용됩니다. 이미지 버전은 여러 번 사용할 수 있습니다.


## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 스냅샷 또는 관리 디스크가 있어야 합니다. 

데이터 디스크를 포함하려면 데이터 디스크 크기가 1TB를 초과할 수 없습니다.

이 문서를 진행하며 필요한 경우 리소스 이름을 바꿉니다.

## <a name="find-the-snapshot-or-managed-disk"></a>스냅샷 또는 관리 디스크 찾기 

[az snapshot list](/cli/azure/snapshot#az_snapshot_list)를 사용하여 리소스 그룹에서 사용할 수 있는 스냅샷 목록을 볼 수 있습니다. 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

스냅샷 대신 관리 디스크를 사용할 수도 있습니다. 관리 디스크를 가져오려면 [az disk list](/cli/azure/disk#az_disk_list)를 사용합니다. 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

스냅샷 또는 관리 디스크의 ID가 있으면 이를 `$source`라는 변수에 할당하여 나중에 사용합니다.

동일한 프로세스를 사용하여 이미지에 포함하고 싶은 데이터 디스크를 가져올 수 있습니다. 변수에 할당한 다음, 나중에 이미지 버전을 만들 때 해당 변수를 사용하세요.


## <a name="find-the-gallery"></a>갤러리 찾기

이미지 정의를 만들려면 이미지 갤러리에 대한 정보가 필요합니다.

[az sig list](/cli/azure/sig#az_sig_list)를 사용하여 사용 가능한 이미지 갤러리에 대한 정보를 나열합니다. 나중에 사용할 갤러리 이름이 어느 리소스 그룹에 있는지 확인합니다.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>이미지 정의 만들기

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 이는 이미지에 대한 정보를 관리하는 데 사용됩니다. 이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의를 만들 때 모든 정보가 올바른지 확인합니다. 해당 예제에서는 사용 중인 VM에 스냅샷 또는 관리 디스크가 있고, 일반화되지 않았다고 가정합니다. 관리 디스크 또는 스냅샷이 일반화된 OS(Windows용 Sysprep이나 Linux용 [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` 또는 `-deprovision+user` 실행 후)에서 생성된 경우 `-OsState`를 `generalized`로 변경합니다. 

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](./shared-image-galleries.md#image-definitions)를 참조하세요.

[az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create)를 사용하여 갤러리에서 이미지 정의를 만듭니다.

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 [특수](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS 이미지에 대한 것입니다. Windows OS를 사용하여 이미지에 대한 정의를 만들려면 `--os-type Windows`를 사용합니다. 

해당 예제에서 갤러리의 이름은 *myGallery* 이고 *myGalleryRG* 리소스 그룹에 있으며 이미지 정의 이름은 *mImageDefinition* 이 됩니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> 타사 이미지에서 파생된 이미지를 포함할 이미지 정의의 경우 계획 정보는 타사 이미지의 계획 정보와 정확히 일치해야 합니다. 이미지 정의를 만들 때 `--plan-name`, `--plan-product`, `--plan-publisher`를 추가하여 이미지 정의에 플랜 정보를 포함합니다.
>

## <a name="create-the-image-version"></a>이미지 버전 만들기

[az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create)을 사용하여 이미지 버전을 만듭니다. 

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

해당 예제에서 이미지의 버전은 *1.0.0* 이며, 영역 중복 스토리지를 사용하여 ‘미국 중남부’ 지역에 복제본 1개, ‘미국 동부 2’ 지역에 복제본 1개를 만들 것입니다.  복제를 위해 대상 지역을 선택할 때 관리 디스크 또는 스냅샷의 ‘원본’ 지역을 복제 대상으로 포함해야 한다는 점을 주의합니다.

`--os-snapshot` 매개 변수에서 스냅샷 또는 관리 디스크의 ID를 전달합니다.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

이미지에 데이터 디스크를 포함하려면 LUN 번호에 설정된 `--data-snapshot-luns` 매개 변수와 데이터 디스크 또는 스냅샷의 ID에 설정된 `--data-snapshots` 매개 변수를 모두 포함해야 합니다.

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `--storage-account-type standard_zrs`를 추가하여 모든 이미지 버전 복제본을 [영역 중복 스토리지](../storage/common/storage-redundancy.md)에 저장할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

[특수 이미지 버전](vm-specialized-image-version-cli.md)에서 VM을 만듭니다.

구매 계획 정보에 대한 자세한 내용은 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.
