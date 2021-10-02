---
title: 고정 개인 IP 주소를 사용 하 여 VM 만들기-Azure CLI
description: Azure CLI를 사용 하 여 고정 개인 IP 주소를 사용 하는 가상 머신을 만드는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 900010fd454e356b43eb7cb5b2ee0379bdf90beb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372824"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-cli"></a>Azure CLI를 사용 하 여 고정 개인 IP 주소를 사용 하는 가상 컴퓨터 만들기

VM (가상 컴퓨터)에는 사용자가 지정한 범위에서 개인 IP 주소가 자동으로 할당 됩니다. 이 범위는 VM이 배포 되는 서브넷을 기반으로 합니다. Vm은 VM이 삭제 될 때까지 주소를 유지 합니다. Azure는 VM을 만드는 서브넷에서 사용 가능한 다음 개인 IP 주소를 동적으로 할당합니다. 서브넷의 특정 IP 주소를 원하는 경우 VM에 고정 IP 주소를 할당 합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **myResourceGroup** 이라는 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)로 가상 머신을 만듭니다. 

다음 명령은 Windows Server 가상 컴퓨터를 만듭니다. 메시지가 표시 되 면 가상 컴퓨터에 대 한 자격 증명으로 사용할 사용자 이름 및 암호를 제공 합니다.

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group myResourceGroup \
    --public-ip-address myPublicIP \
    --public-ip-sku Standard \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

## <a name="change-private-ip-address-to-static"></a>개인 IP 주소를 고정으로 변경

이 섹션에서는 이전에 만든 가상 컴퓨터에 대 한 개인 IP 주소를 **동적** 에서 **정적** 으로 변경 합니다. 

[Az network nic ip 구성 업데이트](/cli/azure/network/nic/ipconfig#az_network_nic_ip_config_update) 를 사용 하 여 네트워크 인터페이스 구성을 업데이트 합니다.

다음 명령은 가상 머신의 개인 IP 주소를 고정으로 변경 합니다.

```azurecli-interactive
  az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --private-ip-address 10.0.0.4
```

> [!WARNING]
> 운영 체제에 개인 IP 주소 설정을 추가할 수 있지만 [운영 체제에 개인 IP 주소 추가](virtual-network-network-interface-addresses.md#private)를 읽기 전까지 추가하지 않는 것이 좋습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
  az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

- Azure에서 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
- [개인 IP 주소](private-ip-addresses.md), 및 Azure 가상 머신에 [고정 개인 IP 주소](virtual-network-network-interface-addresses.md#add-ip-addresses) 할당에 대해 알아봅니다.
- [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 만들기에 대해 자세히 알아봅니다.