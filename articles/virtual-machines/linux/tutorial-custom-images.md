---
title: 자습서 - Azure CLI를 사용하여 사용자 지정 VM 이미지 만들기
description: 이 자습서에서는 Azure CLI를 사용하여 Azure에서 사용자 지정 가상 머신 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: mimckitt
ms.openlocfilehash: 8bf4c2842f6ec3cecc6e6cc014bb225115a1655f
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450416"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure VM의 사용자 지정 이미지 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 이 자습서에서는 Azure Virtual Machines의 사용자 지정 이미지를 만듭니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Shared Image Gallery 만들기
> * 이미지 정의 만들기
> * 이미지 버전 만들기
> * 이미지에서 VM 만들기 
> * 이미지 갤러리 공유


이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 [Azure Cloud Shell](../../cloud-shell/overview.md) 내의 CLI를 사용합니다. Cloud Shell을 열려면 코드 블록 상단에서 **사용해 보세요** 를 선택합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.4.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="overview"></a>개요

[공유 이미지 갤러리](../shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

Shared Image Gallery를 사용하면 사용자 지정 VM 이미지를 다른 사용자와 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다.

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 새 VM 인스턴스를 만드는 데 사용할 수 있는 재사용 가능 사용자 지정 이미지로 변환하는 방법을 자세히 설명합니다.

이 자습서의 예제를 완료하려면 기존 가상 머신이 있어야 합니다. 필요한 경우 이 자습서에 사용할 VM을 만들기 위해 [CLI 빠른 시작](quick-create-cli.md)을 참조할 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 이름을 바꿉니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 

갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에 대시를 사용할 수 없습니다.   갤러리 이름은 구독 내에서 고유해야 합니다. 

[az sig create](/cli/azure/sig#az_sig_create)를 사용하여 이미지 갤러리를 만드세요. 다음 예제에서는 *미국 동부* 에 *myGalleryRG* 라는 리소스 그룹 및 *myGallery* 라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>VM 관련 정보 가져오기

[az vm list](/cli/azure/vm#az_vm_list)를 사용하여 사용할 수 있는 VM 목록을 볼 수 있습니다. 

```azurecli-interactive
az vm list --output table
```

VM 이름과 해당 VM이 속한 리소스 그룹을 알고 있으면 [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)를 사용하여 VM ID를 가져옵니다. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

나중에 사용할 수 있도록 VM ID를 복사합니다.

## <a name="create-an-image-definition"></a>이미지 정의 만들기

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 내부에서 생성된 이미지 버전에 대한 정보를 관리하는 데 사용됩니다. 

이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의에 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../shared-image-galleries.md#image-definitions)를 참조하세요.

[az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create)를 사용하여 갤러리에서 이미지 정의를 만듭니다. 

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 [특수](../shared-image-galleries.md#generalized-and-specialized-images) Linux OS 이미지에 대한 것입니다. 

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

나중에 사용할 수 있도록 출력에서 이미지 정의 ID를 복사합니다.

## <a name="create-the-image-version"></a>이미지 버전 만들기

[az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create)을 사용하여 VM에서 이미지 버전을 만듭니다.  

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch* 입니다.

다음 예제에서는 이미지 버전이 *1.0.0* 이며, 영역 중복 스토리지를 사용하여 두 복제본을 *미국 중서부* 지역에, 하나의 복제본을 각각 *미국 중남부* 지역 및 *미국 동부 2* 지역에 만듭니다. 복제 지역에는 원본 VM이 있는 지역이 포함되어야 합니다.

이 예제의 `--managed-image` 값을 이전 단계의 VM ID로 바꿉니다.

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
> 동일한 관리 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `--storage-account-type  premium_lrs`를 추가하여 프리미엄 스토리지에 추가하거나 `--storage-account-type  standard_zrs`를 추가하여 [영역 중복 스토리지](../../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>

 
## <a name="create-the-vm"></a>VM 만들기

이미지가 특수 이미지임을 나타내는 --specialized 매개 변수를 사용하는 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 

이미지 정의 ID를 `--image`에 사용하여 사용 가능한 최신 버전의 이미지에서 VM을 만듭니다. 또한 `--image`에 대한 이미지 버전 ID를 제공하여 특정 버전에서 VM을 만들 수 있습니다. 

다음 예제에서는 최신 버전의 *myImageDefinition* 이미지에서 VM을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>갤러리 공유

Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 구독 간에 이미지를 공유할 수 있습니다. 갤러리, 이미지 정의 또는 이미지 버전 수준에서 이미지를 공유할 수 있습니다. 이미지 버전에 대한 읽기 권한이 있는 모든 사용자는 구독 간에도 이미지 버전을 사용하여 VM을 배포할 수 있습니다.

갤러리 수준에서 다른 사용자와 공유하는 것이 좋습니다. 갤러리의 개체 ID를 가져오려면 [az sig show](/cli/azure/sig#az_sig_show)를 사용합니다.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

이메일 주소 및 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)와 함께 개체 ID를 범위로 사용하여 사용자에게 공유 이미지 갤러리에 대한 액세스 권한을 부여합니다. `<email-address>` 및 `<gallery iD>`를 사용자 고유의 정보로 바꿉니다.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Azure RBAC를 사용하여 리소스를 공유하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조하세요.

## <a name="azure-image-builder"></a>Azure 이미지 작성기

Azure는 [Azure VM 이미지 작성기](../image-builder-overview.md)인 Packer를 기반으로 하는 서비스도 제공합니다. 템플릿에서 사용자 지정을 설명하기만 하면 이미지 만들기가 처리됩니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 VM 이미지를 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Shared Image Gallery 만들기
> * 이미지 정의 만들기
> * 이미지 버전 만들기
> * 이미지에서 VM 만들기 
> * 이미지 갤러리 공유

고가용성 가상 머신에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [고가용성 VM 만들기](tutorial-availability-sets.md)