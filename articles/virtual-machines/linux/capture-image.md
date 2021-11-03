---
title: Azure CLI를 사용하여 Linux VM의 관리형 이미지 캡처
description: Azure CLI를 사용하여 대량 배포에 사용할 Azure VM의 관리형 이미지를 캡처합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 08/27/2021
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: 0aae67dbe347c8299e00d741163370d9097c8114
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432855"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>가상 머신 또는 VHD의 관리형 이미지를 만드는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure에서 사용할 VM(가상 머신)의 복사본을 여러 개 만들려면 VM 또는 OS VHD의 관리형 이미지를 캡처합니다. 대규모로 이미지를 만들고 저장 하 고 공유 하려면 [Azure Compute 갤러리](../create-gallery.md)를 참조 하세요.

하나의 관리형 이미지는 최대 20개의 동시 배포를 지원합니다. 동일한 관리형 이미지에서 20개가 넘는 VM을 동시에 만들려고 하면 단일 VHD의 스토리지 성능 제한으로 인해 프로비저닝 시간이 초과될 수 있습니다. 20 개 이상의 Vm을 동시에 만들려면 20 개의 동시 VM 배포 마다 1 개의 복제본으로 구성 된 [Azure 계산 갤러리](../shared-image-galleries.md) (이전의 공유 이미지 갤러리) 이미지를 사용 합니다.

관리형 이미지를 만들려면 개인 계정 정보를 제거해야 합니다. 다음 단계에서는 기존 VM의 프로비전을 해제하고, 할당을 취소하고, 이미지를 만듭니다. 이 이미지를 사용하여 구독 내의 모든 리소스 그룹에서 VM을 만들 수 있습니다.

백업 또는 디버깅을 위해 기존 Linux VM의 복사본을 만들거나 온-프레미스 VM에서 Linux VHD를 업로드하려면 [사용자 지정 디스크에서 Linux VM 업로드 및 만들기](upload-vhd.md)를 참조하세요.  

**Azure VM Image Builder** 를 사용하여 사용자 지정 이미지를 빌드할 수 있으며, 도구를 배우거나 빌드 파이프라인을 설정할 필요 없이 이미지 구성을 제공하기만 하면 Image Builder에서 이미지를 만듭니다. 자세한 내용은 [Azure VM Image Builder 시작](../image-builder-overview.md)을 참조하세요.

이미지를 만들려면 다음 항목이 필요합니다.

* 관리 디스크를 사용하여 Resource Manager 배포 모델에서 만든 Azure VM. Linux VM을 만들지 않은 경우 [포털](quick-create-portal.md), [Azure CLI](quick-create-cli.md) 또는 [Resource Manager 템플릿](create-ssh-secured-vm-from-template.md)을 사용할 수 있습니다. 필요에 따라 VM을 구성합니다. 예를 들어 [데이터 디스크를 추가하고](add-disk.md), 업데이트를 적용하고, 애플리케이션을 설치합니다. 

* 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인해야 합니다.

## <a name="prefer-a-tutorial-instead"></a>대신 자습서를 선호하나요?

이 문서에 대한 간소화된 버전 및 Azure의 VM에 대한 테스트, 평가 또는 학습에 대해 알아보려면 [CLI를 사용하여 Azure VM의 사용자 지정 이미지 만들기](tutorial-custom-images.md)를 참조하세요.  그렇지 않으면 이 내용을 계속 읽으면서 전반적인 측면을 이해하세요.


## <a name="step-1-deprovision-the-vm"></a>1단계: VM 프로비전 해제
Azure VM 에이전트를 사용하여 VM의 프로비전을 해제하여 머신별 파일 및 데이터를 삭제합니다. 원본 Linux VM에서 `-deprovision+user` 매개 변수와 함께 `waagent` 명령을 사용합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../extensions/agent-linux.md)를 참조하세요. 이 프로세스는 되돌릴 수 없습니다.

1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
2. SSH 창에서 다음 명령을 입력합니다.
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 이 명령이 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다. 또한 `+user` 매개 변수는 마지막 프로비전된 사용자 계정을 제거합니다. VM에 사용자 계정 자격 증명을 유지하려면 `-deprovision`만 사용합니다.
 
3. 계속하려면 **y** 를 입력합니다. `-force` 매개 변수를 추가하여 이 확인 단계를 피할 수 있습니다.
4. 명령이 완료되면 **종료** 를 입력하여 SSH 클라이언트를 닫습니다.  이 시점에서 VM은 계속 실행되고 있습니다.

## <a name="step-2-create-vm-image"></a>2단계: VM 이미지 만들기
Azure CLI를 사용하여 VM을 일반화된 항목으로 표시하고 이미지를 캡처합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myVnet*, *myVM* 이 포함됩니다.

1. [az vm deallocate](/cli/azure/vm)로 프로비전 해제한 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup* 에서 *myVM* 이라는 VM의 할당을 취소합니다.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    계속하기 전에 VM이 완전히 할당 취소될 때까지 기다립니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.  할당을 취소하는 동안 VM이 종료됩니다.

2. [az vm generalize](/cli/azure/vm)를 사용하여 VM을 일반화된 항목으로 표시합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup* 에서 *myVM* 이라는 VM을 일반화된 항목으로 표시합니다.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    일반화된 VM은 더 이상 다시 시작할 수 없습니다.

3. 이제 [az image create](/cli/azure/image#az_image_create)로 VM 리소스의 이미지를 만듭니다. 다음 예제에서는 *myVM* 이라는 VM 리소스를 사용하여 *myResourceGroup* 이라는 리소스 그룹에서 *myImage* 라는 이미지를 만듭니다.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
    --name myImage --source myVM
    ```
   
   > [!NOTE]
   > 이미지는 원본 VM과 동일한 리소스 그룹에 만들어집니다. 이 이미지에서 구독 내의 모든 리소스 그룹에 VM을 만들 수 있습니다. 관리 측면에서 VM 리소스 및 이미지에 대한 특정 리소스 그룹을 만들 수도 있습니다.
   >
   > 2세대 VM의 이미지를 캡처하는 경우 `--hyper-v-generation V2` 매개 변수도 사용합니다. 자세한 내용은 [2세대 VM](../generation-2.md)을 참조하세요.
   > 
   > 이미지를 영역 중복 스토리지에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 `--zone-resilient true` 매개 변수를 포함하는 지역에 만들어야 합니다.
   
이 명령은 VM 이미지를 설명하는 JSON을 반환합니다. 나중에 참조할 수 있도록 이 출력을 저장해 둡니다.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3단계: 캡처한 이미지에서 VM 만들기
[az vm create](/cli/azure/vm)로 만든 이미지를 사용하여 VM을 만듭니다. 다음 예제에서는 *myImage* 라는 이미지에서 *myVMDeployed* 라는 VM을 만듭니다.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>다른 리소스 그룹에서 VM 만들기 

구독 내 모든 리소스 그룹의 이미지에서 VM을 만들 수 있습니다. 이미지와 다른 리소스 그룹에 VM을 만들려면 이미지에 완전한 리소스 ID를 지정합니다. [az image list](/cli/azure/image#az_image_list)를 사용하여 이미지 목록을 봅니다. 다음 예제와 유사하게 출력됩니다.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

다음 예제에서는 이미지 리소스 ID를 지정하여 원본 이미지가 아닌 리소스 그룹에 VM을 만드는 [az vm create](/cli/azure/vm#az_vm_create)를 사용합니다.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4단계: 배포 확인

생성한 가상 머신에 SSH를 실행하여 배포를 확인하고 새 VM 사용을 시작합니다. SSH를 통해 연결하려면 [az vm show](/cli/azure/vm#az_vm_show)로 VM의 IP 주소 또는 FQDN을 찾습니다.

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>다음 단계
대규모로 이미지를 만들고 저장 하 고 공유 하려면 [Azure Compute 갤러리](../create-gallery.md)를 참조 하세요.
