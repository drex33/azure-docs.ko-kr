---
title: 가상 네트워크 만들기 - 빠른 시작 - Azure CLI
titleSuffix: Azure Virtual Network
description: 이 빠른 시작에서는 Azure CLI를 사용하여 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크를 통해 Azure 리소스가 서로 통신하고 인터넷과 통신할 수 있습니다.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli, mode-other
ms.openlocfilehash: 34169a20d364d2f14d9350c2c6ece28ea2ec820f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076790"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 가상 네트워크 만들기

가상 네트워크를 사용하면 VM(가상 머신)과 같은 Azure 리소스가 서로 인터넷을 통해 비공개로 통신할 수 있습니다. 

이 빠른 시작에서는 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크를 만든 후에 두 개의 VM을 가상 네트워크에 배포합니다. 그런 다음, 인터넷에서 VM에 연결하고 새 가상 네트워크를 통해 비공개로 통신합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group-and-a-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기

가상 네트워크를 만들려면 먼저 가상 네트워크를 호스트할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 **eastus** 위치에 **CreateVNetQS-rg** 라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 **default** 라는 서브넷이 하나 있는 **myVNet** 이라는 기본 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 

기본 키 위치에 SSH 키가 없는 경우 이 명령에서 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. 

`--no-wait` 옵션은 백그라운드에서 VM을 만듭니다. 다음 단계를 계속 진행할 수 있습니다. 

다음 예제에서는 **myVM1** 이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

이전 단계에서 `--no-wait` 옵션을 사용했습니다. 계속해서 **myVM2** 라는 두 번째 VM을 만들 수 있습니다.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Azure CLI 출력 메시지

VM을 만드는 데 몇 분이 걸릴 수 있습니다. Azure에서 VM을 만든 후 Azure CLI는 다음과 같은 출력을 반환합니다.

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>VM 공용 IP

공용 IP 주소 **myVM2** 를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용합니다.

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음 명령에서 `<publicIpAddress>`를 **myVM2** VM의 공용 IP 주소로 바꿉니다.

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>VM 간 통신

**myVM2** 및 **myVM1** VM 간의 프라이빗 통신을 확인하려면 다음 명령을 입력합니다.

```bash
ping myVM1 -c 4
```

*10.0.0.4* 에서 4개의 응답을 받게 됩니다.

**myVM2** VM을 사용하여 SSH 세션을 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 관련 정보는 다음과 같습니다. 

* 기본 가상 네트워크와 두 개의 VM을 만들었습니다. 
* 인터넷에서 하나의 VM에 연결하고 두 VM 간에 비공개로 통신했습니다.

VM 간의 프라이빗 통신은 가상 네트워크에서 제한되지 않습니다. 

다양한 유형의 VM 네트워크 통신 구성에 대해 자세히 알아보려면 다음 문서로 이동하세요.
> [!div class="nextstepaction"]
> [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)
