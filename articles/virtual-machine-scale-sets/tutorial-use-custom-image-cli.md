---
title: 자습서 - Azure CLI를 사용하여 확장 집합에서 사용자 지정 VM 이미지 사용
description: Azure CLI를 사용하여 가상 머신 확장 집합을 배포하는 데 사용할 수 있는 사용자 지정 VM 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: mimckitt
ms.openlocfilehash: 64ff90e695b8027ed23da463bdf75a4778675e0a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456806"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>자습서: Azure CLI를 사용하여 가상 머신 확장 집합에 대한 사용자 지정 이미지 만들기 및 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

확장 집합을 만들 때 VM 인스턴스 배포 시 사용할 이미지를 지정합니다. VM 인스턴스가 배포된 후 작업의 수를 줄이려면 사용자 지정 VM 이미지를 사용할 수 있습니다. 이 사용자 지정 VM 이미지에는 필요한 모든 애플리케이션 설치 또는 구성이 포함됩니다. 확장 집합에서 만들어진 모든 VM 인스턴스는 사용자 지정 VM 이미지를 사용하며, 애플리케이션 트래픽을 처리할 준비가 되어 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Compute Gallery 만들기
> * 특수화된 이미지 정의 만들기
> * 이미지 버전 만들기
> * 특수화된 이미지에서 확장 집합 만들기
> * 이미지 갤러리 공유


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.4.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="overview"></a>개요

[Azure Compute Gallery](../virtual-machines/shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

Azure Compute Gallery를 사용하면 사용자 지정 VM 이미지를 다른 사용자와 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 

## <a name="create-and-configure-a-source-vm"></a>원본 VM 만들기 및 구성

먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만든 다음, [az vm create](/cli/azure/vm)를 사용하여 VM을 만듭니다. 이 VM은 이미지에 대한 원본으로 사용됩니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myVM* 이라는 VM을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> VM의 **ID** 는 [az vm create](/cli/azure/vm) 명령의 출력에 표시됩니다. 이 자습서의 뒷부분에서 사용할 수 있도록 이를 안전한 위치에 복사합니다.

VM의 공용 IP 주소는 [az vm create](/cli/azure/vm) 명령의 출력에도 표시됩니다. 다음과 같이 SSH를 VM의 공용 IP 주소에 연결합니다.

```console
ssh azureuser@<publicIpAddress>
```

VM을 사용자 지정하기 위해 기본 웹 서버를 설치해 보겠습니다. 확장 집합의 VM 인스턴스가 배포되면 웹 애플리케이션을 실행하는 데 필요한 모든 패키지가 포함되어 있습니다. 다음과 같이 `apt-get`을 사용하여 *NGINX* 를 설치합니다.

```bash
sudo apt-get install -y nginx
```

완료되면 `exit`를 입력하여 SSH 연결의 연결을 끊습니다.

## <a name="create-an-image-gallery"></a>이미지 갤러리 만들기 

이미지 갤러리는 이미지 공유를 활성화하는 데 사용되는 기본 리소스입니다. 

갤러리 이름에 허용되는 문자는 대문자 또는 소문자, 숫자, 점 및 마침표입니다. 갤러리 이름에 대시를 사용할 수 없습니다.   갤러리 이름은 구독 내에서 고유해야 합니다. 

[az sig create](/cli/azure/sig#az_sig_create)를 사용하여 이미지 갤러리를 만드세요. 다음 예제에서는 *미국 동부* 에 *myGalleryRG* 라는 리소스 그룹 및 *myGallery* 라는 갤러리를 만듭니다.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>이미지 정의 만들기

이미지 정의는 이미지에 대한 논리적 그룹화를 만듭니다. 내부에서 생성된 이미지 버전에 대한 정보를 관리하는 데 사용됩니다. 

이미지 정의 이름은 대문자 또는 소문자, 숫자, 점, 대시 및 마침표로 구성될 수 있습니다. 

이미지 정의가 올바른 형식인지 확인합니다. VM을 일반화한 경우(Windows용 Sysprep 사용 또는 Linux용 waagent -프로비전 해제) `--os-state generalized`를 사용하여 일반화된 이미지 정의를 만들어야 합니다. 기존 사용자 계정을 제거하지 않고 VM을 사용하려는 경우 `--os-state specialized`를 사용하여 특수화된 이미지 정의를 만듭니다.

이미지 정의에 대해 지정할 수 있는 값에 대한 자세한 내용은 [이미지 정의](../virtual-machines/shared-image-galleries.md#image-definitions)를 참조하세요.

[az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create)를 사용하여 갤러리에서 이미지 정의를 만듭니다.

다음 예제에서는 이미지 정의의 이름이 *myImageDefinition* 이며 [특수](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) Linux OS 이미지에 대한 것입니다. Windows OS를 사용하여 이미지에 대한 정의를 만들려면 `--os-type Windows`를 사용합니다. 

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

> [!IMPORTANT]
> 이미지 정의의 **ID** 는 명령의 출력에 표시됩니다. 이 자습서의 뒷부분에서 사용할 수 있도록 이를 안전한 위치에 복사합니다.


## <a name="create-the-image-version"></a>이미지 버전 만들기

[az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create)을 사용하여 VM에서 이미지 버전을 만듭니다.  

이미지 버전에 허용되는 문자는 숫자 및 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서 이미지의 버전은 *1.0.0* 이며, *미국 중남부* 지역에서 1개의 복제본, *미국 동부 2* 지역에서 1개의 복제본을 만듭니다. 복제 지역에는 원본 VM이 있는 지역이 포함되어야 합니다.

이 예제의 `--managed-image` 값을 이전 단계의 VM ID로 바꿉니다.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 동일한 관리형 이미지를 사용하여 다른 이미지 버전을 만들려면 먼저 해당 이미지 버전이 완전히 빌드되어 복제될 때까지 기다려야 합니다.
>
> 또한 이미지 버전을 만들 때 `--storage-account-type  premium_lrs`를 추가하여 프리미엄 스토리지에 추가하거나 `--storage-account-type  standard_zrs`를 추가하여 [영역 중복 스토리지](../storage/common/storage-redundancy.md)를 추가하여 이미지를 저장할 수도 있습니다.
>




## <a name="create-a-scale-set-from-the-image"></a>이미지에서 확장 집합 만들기
[`az vmss create`](/cli/azure/vmss#az_vmss_create)를 사용하여 특수화된 이미지에서 확장 집합 만들기 

이미지가 특수 이미지임을 나타내는 --specialized 매개 변수를 사용하는 [`az vmss create`](/cli/azure/vmss#az_vmss_create)를 사용하여 확장 집합을 만듭니다. 

이미지 정의 ID를 `--image`에 사용하여 사용 가능한 최신 버전의 이미지에서 확장 집합 인스턴스를 만듭니다. 또한 `--image`에 대한 이미지 버전 ID를 제공하여 특정 버전에서 확장 집합 인스턴스를 만들 수 있습니다. 

이전에 만든 *myImageDefinition* 이미지의 최신 버전인 *myScaleSet* 이라는 확장 집합을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="test-your-scale-set"></a>확장 집합 테스트
트래픽이 확장 집합에 도달하고 웹 서버가 올바르게 작동하는지 확인하도록 허용하려면 [az network lb rule create](/cli/azure/network/lb/rule)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제에서는 *80* *TCP* 포트에 트래픽을 허용하는 *myLoadBalancerRuleWeb* 이라는 규칙을 만듭니다.

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

작동 중인 확장 집합을 확인하려면 [az network public-ip show](/cli/azure/network/public-ip)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 확장 집합의 일부로 만든 *myScaleSetLBPublicIP* 에 대한 IP 주소를 가져옵니다.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

웹 브라우저에 공용 IP 주소를 입력합니다. 기본 NGINX 웹 페이지가 다음 예제와 같이 표시됩니다.

![사용자 지정 VM 이미지에서 실행된 Nginx](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>갤러리 공유

Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 구독 간에 이미지를 공유할 수 있습니다. 갤러리, 이미지 정의 또는 이미지 버전에서 이미지를 공유할 수 있습니다. 이미지 버전에 대한 읽기 권한이 있는 모든 사용자는 구독 간에도 이미지 버전을 사용하여 VM을 배포할 수 있습니다.

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

Azure RBAC를 사용하여 리소스를 공유하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-cli.md)를 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 추가 리소스를 제거하려면 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `--no-wait` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다. `--yes` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure CLI를 사용하여 확장 집합에 대한 사용자 지정 VM 이미지를 만들고 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Compute Gallery 만들기
> * 특수화된 이미지 정의 만들기
> * 이미지 버전 만들기
> * 특수화된 이미지에서 확장 집합 만들기
> * 이미지 갤러리 공유

애플리케이션을 확장 집합에 배포하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합에 애플리케이션 배포](tutorial-install-apps-cli.md)