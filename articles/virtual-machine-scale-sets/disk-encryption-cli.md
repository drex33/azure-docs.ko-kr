---
title: Azure CLI를 사용하여 Azure 확장 집합의 디스크 암호화
description: Azure CLI를 사용하여 Windows 가상 머신 확장 집합에서 VM 인스턴스 및 연결된 디스크를 암호화하는 방법 알아보기
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 2d711c646fc4b1783ed9f6c609a4094ddfc605d7
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691410"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI를 사용하여 가상 머신 확장 집합에서 OS 및 연결된 데이터 디스크 암호화

**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 빠른 시작에서는 Azure CLI를 사용하여 가상 머신 확장 집합을 만들고 암호화하는 방법을 보여줍니다. 가상 머신 확장 집합에 Azure Disk Encryption을 적용하는 방법에 대한 자세한 내용은 [Virtual Machine Scale Sets를 위한 Azure Disk Encryption](disk-encryption-overview.md)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.31 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-scale-set"></a>확장 집합 만들기

확장 집합을 만들려면 먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

이제 [az vmss create](/cli/azure/vmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 변경 내용이 적용될 때 자동으로 업데이트하도록 설정된 *myScaleSet* 이라는 확장 집합을 만들고, *~/.ssh/id_rsa* 에 없는 경우 SSH 키를 생성합니다. 32Gb 데이터 디스크가 각 VM 인스턴스에 연결되며, Azure [사용자 지정 스크립트 확장](../virtual-machines/extensions/custom-script-linux.md)을 사용하여 [az vmss extension set](/cli/azure/vmss/extension)로 데이터 디스크를 준비합니다.

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>디스크 암호화가 활성화된 Azure 키 자격 증명 모음 만들기

Azure Key Vault는 애플리케이션 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다.

사용자 고유의 *keyvault_name* 을 정의합니다. 그런 다음, [az keyvault create](/cli/azure/keyvault#az_keyvault_create)를 사용하여 확장 집합과 동일한 구독 및 영역에 KeyVault를 만들고 *--enabled-for-disk-encryption* 액세스 정책을 설정합니다.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>기존 Key Vault 사용

이 단계는 디스크 암호화로 사용하려는 기존 Key Vault가 있는 경우에만 필요합니다. 이전 섹션에서 Key Vault를 만든 경우 이 단계를 건너뜁니다.

사용자 고유의 *keyvault_name* 을 정의합니다. 그런 다음, [az keyvault update](/cli/azure/keyvault#az_keyvault_update)를 사용하여 KeyVault를 업데이트하고 *--enabled-for-disk-encryption* 액세스 정책을 설정합니다.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>암호화 사용

확장 집합에서 VM 인스턴스를 암호화하려면 먼저 [az keyvault show](/cli/azure/keyvault#az_keyvault_show)를 사용하여 Key Vault 리소스 ID에 대한 정보를 얻습니다. 이러한 변수는 [az vmss encryption enable](/cli/azure/vmss/encryption#az_vmss_encryption_enable)로 암호화 프로세스를 시작하는 데 사용됩니다.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

암호화 프로세스를 시작하는 데 1~2분 정도 걸릴 수 있습니다.

확장 집합이 이전 단계에 만든 확장 집합에 대한 업그레이드 정책이며 “자동”으로 설정되어 있으므로 VM 인스턴스가 암호화 프로세스를 자동으로 시작합니다. 업그레이드 정책이 수동으로 설정된 확장 집합의 경우에는 [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)를 사용하여 VM 인스턴스에 대해 암호화 정책을 시작합니다.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>KEK를 사용하여 암호화를 사용하여 키 래핑

가상 머신 확장 집합을 암호화할 때 보안을 강화하기 위해 키 암호화 키를 사용할 수도 있습니다.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  disk-encryption-keyvault 매개 변수 값에 대한 구문은 다음과 같은 전체 식별자 문자열입니다.</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> key-encryption-key 매개 변수의 값 구문은 다음과 같이 KEK의 전체 URI입니다.</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>암호화 진행 확인

디스크 암호화의 상태를 확인하려면 [az vmss encryption show](/cli/azure/vmss/encryption#az_vmss_encryption_show)를 사용합니다.

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

VM 인스턴스가 암호화되어 있으면 다음 예제 출력에 나온 것처럼 “EncryptionState/encrypted”가 보고됩니다.

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>암호화 사용 안 함

암호화된 VM 인스턴스 디스크를 더 이상 사용하지 않으려는 경우 다음과 같이 [az vmss encryption disable](/cli/azure/vmss/encryption#az_vmss_encryption_disable)을 사용하여 암호화를 비활성화할 수 있습니다.

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>다음 단계

- 이 문서에서는 Azure CLI를 사용하여 가상 머신 확장 집합을 암호화했습니다. [Azure PowerShell](disk-encryption-powershell.md) 또는 [Azure Resource Manager 템플릿](disk-encryption-azure-resource-manager.md)을 사용할 수도 있습니다.
- 다른 확장이 프로비저닝된 후 Azure Disk Encryption을 적용하려는 경우 [확장 시퀀싱](virtual-machine-scale-sets-extension-sequencing.md)을 사용할 수 있습니다. 
- Linux 확장 집합 데이터 디스크 암호화에 대한 엔드투엔드 배치 파일 예제는 [여기](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)서 찾을 수 있습니다. 이 예제에서는 리소스 그룹, Linux 확장 집합을 만들고, 5GB 데이터 디스크를 탑재하고, 가상 머신 확장 집합을 암호화합니다.
