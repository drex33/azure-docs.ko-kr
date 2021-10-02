---
title: 고정 공용 IP 주소를 사용 하 여 VM 만들기-Azure CLI
titlesuffix: Azure Virtual Network
description: Azure CLI를 사용 하 여 고정 공용 IP 주소를 사용 하는 VM (가상 머신)을 만듭니다. 고정 공용 IP 주소는 변경되지 않는 주소입니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ea60d6b2784972f2a4e3210ad815c4823a27a19
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373027"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Azure CLI를 사용하여 고정 공용 IP 주소로 가상 머신 만들기

이 문서에서는 고정 공용 IP 주소를 사용 하 여 VM을 만듭니다. 공용 IP 주소를 사용 하면 인터넷에서 가상 컴퓨터로 통신할 수 있습니다. 주소가 변경 되지 않도록 동적 주소 대신 고정 공용 IP 주소를 할당 합니다. 

공용 IP 주소에는 [명목 요금](https://azure.microsoft.com/pricing/details/ip-addresses)이 부과됩니다. 구독당 사용할 수 있는 공용 IP 주소의 수는 [제한](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)되어 있습니다.

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

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

[Az network 공용-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 를 사용 하 여 표준 공용 IPv4 주소를 만듭니다.

다음 명령은 **Myresourcegroup** 에 **myPublicIP** 이라는 영역 중복 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)로 가상 머신을 만듭니다. 

다음 명령은 Windows Server 가상 컴퓨터를 만듭니다. 매개 변수에서 이전에 만든 공용 IP 주소의 이름을 입력 합니다 **`-PublicIPAddressName`** . 메시지가 표시 되 면 가상 컴퓨터에 대 한 자격 증명으로 사용할 사용자 이름 및 암호를 제공 합니다.

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group TutorVMRoutePref-rg \
    --public-ip-address myPublicIP \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

공용 IP Sku에 대 한 자세한 내용은 [공용 ip 주소 sku](public-ip-addresses.md#sku)를 참조 하세요. 가상 머신은 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 공용 IP 주소의 SKU는 부하 분산 장치의 공용 IP의 SKU와 일치 해야 합니다. 자세한 내용은 [Azure 부하 분산 장치](../../load-balancer/skus.md)(영문)를 참조하세요.

할당 된 공용 IP 주소를 보고, [az network 공용 ip show](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용 하 여 고정 주소로 만들어졌는지 확인 합니다.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress,publicIpAllocationMethod,sku] \
    --output table
```

> [!WARNING]
> 가상 머신의 운영 체제 내에서 IP 주소 설정을 수정하지 마십시오. 운영 체제는 Azure 공용 IP 주소를 인식하지 않습니다. 운영 체제에 개인 IP 주소 설정을 추가할 수 있지만 필요하지 않는 한 그리고 [운영 체제에 개인 IP 주소 추가](virtual-network-network-interface-addresses.md#private)를 읽기 전까지 추가하지 않는 것이 좋습니다.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

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