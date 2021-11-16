---
title: 이중 스택 네트워크를 사용하여 Azure 가상 머신 만들기 - Azure CLI
titleSuffix: Azure Virtual Network
description: 이 문서에서는 Azure CLI 사용하여 Azure에서 이중 스택 가상 네트워크를 사용하여 가상 머신을 만드는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 11/11/2021
ms.custom: template-how-to
ms.openlocfilehash: ae8f7fef465b8ba42a058b43ca95fd2334f437c8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495049"
---
# <a name="create-an-azure-virtual-machine-with-a-dual-stack-network-using-the-azure-cli"></a>Azure CLI 사용하여 이중 스택 네트워크로 Azure Virtual Machine 만들기

이 문서에서는 Azure CLI 사용하여 Azure에서 가상 머신을 만듭니다. 가상 머신은 절차의 일부로 이중 스택 네트워크와 함께 만들어집니다.  완료되면 가상 머신은 IPv4 및 IPv6 통신을 지원합니다.  

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **myResourceGroup** 이라는 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 가상 머신에 대한 이중 스택 가상 네트워크를 만듭니다.

[az network vnet create를](/cli/azure/network/vnet#az_network_vnet_create) 사용하여 가상 네트워크를 만듭니다.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 2404:f800:8000:122::/63 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24 2404:f800:8000:122::/64
```

## <a name="create-public-ip-addresses"></a>공용 IP 주소 만들기

이 섹션에서는 IPv4 및 IPv6이라는 두 개의 공용 IP 주소를 만듭니다. 

[az network public-ip create를](/cli/azure/network/public-ip#az_network_public_ip_create) 사용하여 공용 IP 주소를 만듭니다.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv4 \
    --sku Standard \
    --version IPv4

  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv6 \
    --sku Standard \
    --version IPv6

```
## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

이 섹션에서는 가상 머신 및 가상 네트워크에 대한 네트워크 보안 그룹을 만듭니다.

[az network nsg create를](/cli/azure/network/nsg#az_network_nsg_create) 사용하여 네트워크 보안 그룹을 만듭니다.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroup \
    --name myNSG
```

### <a name="create-network-security-group-rules"></a>네트워크 보안 그룹 규칙 만들기

SSH용 포트 22에서 가상 머신에 대한 연결을 허용하는 규칙을 만듭니다. 아웃바운드 연결에 대한 모든 포트를 허용하는 추가 규칙이 만들어집니다.

[az network nsg rule create를](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleSSH \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 200

  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleAllOUT \
    --protocol '*' \
    --direction outbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range '*' \
    --access allow \
    --priority 200
```

## <a name="create-virtual-machine"></a>가상 머신 만들기

이 섹션에서는 가상 머신 및 해당 지원 리소스를 만듭니다.

### <a name="create-network-interface"></a>네트워크 인터페이스 만들기

[az network nic create를](/cli/azure/network/nic#az_network_nic_create) 사용하여 가상 머신에 대한 네트워크 인터페이스를 만듭니다. 공용 IP 주소 및 이전에 만든 NSG는 NIC와 연결됩니다. 네트워크 인터페이스는 이전에 만든 가상 네트워크에 연결됩니다.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroup \
    --name myNIC1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --public-ip-address myPublicIP-IPv4
```

### <a name="create-ipv6-ip-configuration"></a>IPv6 IP 구성 만들기

[az network nic ip-config create를](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) 사용하여 NIC에 대한 IPv6 구성을 만듭니다.

```azurecli-interactive
  az network nic ip-config create \
    --resource-group myResourceGroup \
    --name myIPv6config \
    --nic-name myNIC1 \
    --private-ip-address-version IPv6 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --public-ip-address myPublicIP-IPv6
```

### <a name="create-vm"></a>VM 만들기

[az vm create를](/cli/azure/vm#az_vm_create) 사용하여 가상 머신을 만듭니다.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNIC1 \
    --image UbuntuLTS \
    --admin-username azureuser \
    --authentication-type ssh \
    --generate-ssh-keys
```

## <a name="test-ssh-connection"></a>SSH 연결 테스트

[az network public-ip show를](/cli/azure/network/public-ip#az_network_public_ip_show) 사용하여 가상 머신의 IP 주소를 표시합니다.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv4 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv4 \
>     --query ipAddress \
>     --output tsv
20.119.201.208
```

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv6 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv6 \
>     --query ipAddress \
>     --output tsv
2603:1030:408:6::9d
```

다음 명령을 사용하여 가상 머신에 대한 SSH 연결을 엽니다. IP 주소를 가상 머신의 IP 주소로 대체합니다.

```bash
  ssh azureuser@20.119.201.208
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 가상 머신 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 이중 스택 네트워크를 사용하여 Azure 가상 머신을 만드는 방법을 배웠습니다.

Azure의 IPv6 및 IP 주소에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Virtual Network IPv6 개요입니다.](ipv6-overview.md)

- [Azure Virtual Network IP 서비스란?](ip-services-overview.md)


