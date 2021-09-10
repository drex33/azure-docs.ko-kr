---
title: Azure CLI를 사용하여 Windows VM 만들기 및 암호화
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Windows 가상 머신을 만들고 암호화하는 방법을 배웁니다.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6db23229cf0f3521c5855226ac96ad7d52c0fc87
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693439"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Windows VM 만들기 및 암호화

**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Windows Server 2016 VM(가상 머신)을 만들고 암호화하는 방법을 보여 줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.30 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 다음 예제에서는 *myVM* 이라는 VM을 만듭니다. 이 예제에서는 관리 사용자 이름으로 *azureuser* 를 사용하고 암호로 *myPassword12* 를 사용합니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다. 다음 예제 출력은 VM 만들기 작업이 완료되었음을 보여줍니다.

```console
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
> 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서는 이름이 *myKV* 인 Key Vault를 만들지만 원하는 이름을 지정해야 합니다.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>가상 머신 암호화

[az vm encryption](/cli/azure/vm/encryption)을 사용하여 VM을 암호화하고 --disk-encryption-keyvault 매개 변수에 고유한 Key Vault 이름을 지정합니다.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

[az vm show](/cli/azure/vm/encryption#az_vm_encryption_show)를 사용하여 암호화가 VM에서 활성화되었는지 확인할 수 있습니다.

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

반환된 출력에 다음이 표시됩니다.

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹, VM 및 Key Vault를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 가상 머신을 만들고 암호화 키에 사용된 Key Vault를 만들고 VM을 암호화했습니다.  IaaS VM용 Azure Disk Encryption 필수 구성 요소에 대해 자세히 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Disk Encryption 개요](disk-encryption-overview.md)
