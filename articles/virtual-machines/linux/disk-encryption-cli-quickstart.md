---
title: Azure CLI를 사용하여 Linux VM 만들기 및 암호화
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Linux 가상 머신을 만들고 암호화하는 방법을 배웁니다.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli, mode-api
ms.openlocfilehash: 4f1a64fa847417dfa399585f2defa93836ab746d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133043193"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Linux VM 만들기 및 암호화

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Linux VM(가상 머신)을 만들고 암호화하는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

Azure CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 다음 예제에서는 *myVM* 이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다. 다음 예제 출력은 VM 만들기 작업이 완료되었음을 보여줍니다.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>암호화 키에 대해 구성된 Key Vault 만들기

Azure Disk Encryption은 Azure Key Vault에 암호화 키를 저장합니다. [az keyvault create](/cli/azure/keyvault#az_keyvault_create)를 사용하여 Key Vault를 만듭니다. Key Vault를 사용하여 암호화 키를 저장하려면 --enabled-for-disk-encryption 매개 변수를 사용합니다.

> [!Important]
> 모든 Key Vault에는 Azure 전체에서 고유한 이름이 있어야 합니다. 아래 예제에서 \<your-unique-keyvault-name\>을 선택한 이름으로 바꿉니다.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>가상 머신 암호화

[az vm encryption](/cli/azure/vm/encryption)을 사용하여 VM을 암호화하고 --disk-encryption-keyvault 매개 변수에 고유한 Key Vault 이름을 지정합니다.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

잠시 후 프로세스에서 "암호화 요청이 수락되었습니다. 진행률을 모니터링하려면 'show' 명령을 사용하세요.”가 반환됩니다. "Show" 명령은 [az vm show](/cli/azure/vm/encryption#az_vm_encryption_show)입니다.

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

암호화를 사용하는 경우 반환된 출력에 다음이 표시됩니다.

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹, VM 및 Key Vault를 제거할 수 있습니다. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 가상 머신을 만들고 암호화 키에 사용된 Key Vault를 만들고 VM을 암호화했습니다.  Linux VM용 Azure Disk Encryption에 대해 자세히 알아보려면 다음 문서로 진행하세요.

> [!div class="nextstepaction"]
> [Azure Disk Encryption 개요](disk-encryption-overview.md)
