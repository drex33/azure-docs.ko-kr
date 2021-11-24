---
title: '빠른 시작: VM 네트워크 트래픽 필터 문제 진단 - Azure CLI'
titleSuffix: Azure Network Watcher
description: Azure CLI를 사용하여 Azure Network Watcher의 IP 흐름 확인 기능을 통해 가상 머신 네트워크 트래픽 필터 문제를 진단하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: kumud
ms.custom: mvc, devx-track-azurecli, mode-other
ms.openlocfilehash: 9b4a69abdef6cb6931867097cbacd6cea5b41640
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071305"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>빠른 시작: 가상 머신 네트워크 트래픽 필터 문제 진단 - Azure CLI

이 빠른 시작에서 VM(가상 머신)을 배포한 다음, IP 주소와 URL 및 IP 주소에서 통신을 확인합니다. 통신 오류의 원인 및 해결 방법을 확인합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 

- 이 빠른 시작에서 Azure CLI 명령은 Bash 셸에서 실행하도록 형식이 지정됩니다.

## <a name="create-a-vm"></a>VM 만들기

VM을 만들려면 먼저 VM이 포함될 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vm create](/cli/azure/vm)로 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. 다음 예제에서는 *myVm* 이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분이 걸립니다. VM이 만들어지고 Azure CLI에서 출력을 반환할 때까지 나머지 단계를 진행하지 마세요.

## <a name="test-network-communication"></a>네트워크 통신 테스트

Network Watcher와의 네트워크 통신을 테스트하려면 먼저 테스트하려는 VM이 있는 지역에서 Network Watcher를 사용하도록 설정한 다음, Network Watcher의 IP 흐름 확인 기능을 사용하여 통신을 테스트해야 합니다.

### <a name="enable-network-watcher"></a>네트워크 감시자 사용

미국 동부 지역에서 이미 Network Watcher를 사용하도록 설정한 경우 [IP 흐름 확인 사용](#use-ip-flow-verify)으로 건너뜁니다. [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure) 명령을 사용하여 미국 동부 지역의 네트워크 감시자를 만듭니다.

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>IP 흐름 확인 사용

VM을 만들 때 Azure는 기본적으로 VM 간 네트워크 트래픽을 허용하고 거부합니다. 추가 트래픽 형식을 허용하거나 거부하여 나중에 Azure의 기본값을 재정의할 수 있습니다. 트래픽이 다른 대상 및 원본 IP 주소에서 허용되거나 거부되는지 여부를 테스트하려면 [az network watcher test-ip-flow](/cli/azure/network/watcher#az_network_watcher_test_ip_flow) 명령을 사용합니다.

VM에서 www.bing.com 에 대한 IP 주소 중 하나로 아웃바운드 통신을 테스트합니다.

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

몇 초 후 반환되는 결과는 액세스가 **AllowInternetOutbound** 라는 보안 규칙을 통해 허용됨을 알립니다.

VM에서 172.31.0.100으로 아웃바운드 통신을 테스트합니다.

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

반환되는 결과는 액세스가 **DefaultOutboundDenyAll** 이라는 보안 규칙을 통해 거부됨을 알립니다.

172.31.0.100에서 VM으로 인바운드 통신을 테스트합니다.

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

반환되는 결과는 액세스가 **DefaultInboundDenyAll** 이라는 보안 규칙으로 인해 거부됨을 알립니다. 이제 VM 간 트래픽을 허용하거나 거부하는 보안 규칙을 알고 있으므로 문제를 해결하는 방법을 결정할 수 있습니다.

## <a name="view-details-of-a-security-rule"></a>보안 규칙의 세부 정보 보기

[IP 흐름 확인 사용](#use-ip-flow-verify)의 규칙이 통신을 허용하거나 방지하는 이유를 확인하려면 [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg) 명령을 사용하여 네트워크 인터페이스에 대한 효과적인 보안 규칙을 검토합니다.

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

반환된 출력은 [IP 흐름 확인 사용](#use-ip-flow-verify)의 이전 단계에서 www.bing.com 에 대한 아웃바운드 액세스를 허용한 **AllowInternetOutbound** 규칙에 대한 다음 텍스트를 포함합니다.

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

**destinationAddressPrefix** 가 **인터넷** 인 이전 출력에서 확인할 수 있습니다. 그러나 13.107.21.200이 **인터넷** 과 어떻게 관련되어 있는지 분명하지 않습니다. **expandedDestinationAddressPrefix** 아래에 여러 가지 주소 접두사가 나열됩니다. 목록의 접두사 중 하나는 IP 주소의 12.0.0.1-15.255.255.254 범위를 포함하는 **12.0.0.0/6** 입니다. 13.107.21.200은 해당 주소 범위에 있으므로 **AllowInternetOutBound** 규칙은 아웃바운드 트래픽을 허용합니다. 또한 이 규칙을 재정의하는 이전 출력에 표시된 더 높은 우선 순위(낮은 수) 규칙이 없습니다. IP 주소에 대한 아웃바운드 통신을 거부하려면 IP 주소에 대한 포트 80 아웃바운드를 거부하는 더 높은 우선 순위를 가진 보안 규칙을 추가할 수 있습니다.

`az network watcher test-ip-flow` 명령을 실행하여 [IP 흐름 확인 사용](#use-ip-flow-verify)에서 172.131.0.100에 대한 아웃바운드 통신을 테스트한 경우 출력은 **DefaultOutboundDenyAll** 규칙이 통신을 거부했음을 알렸습니다. **DefaultOutboundDenyAll** 규칙은 `az network nic list-effective-nsg` 명령에서 다음 출력에 나열된 **DenyAllOutBound** 규칙과 동일합니다.

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

규칙은 **destinationAddressPrefix** 로 **0.0.0.0/0** 을 나열합니다. 규칙은 주소가 `az network nic list-effective-nsg` 명령의 출력에서 다른 아웃바운드 규칙의 **destinationAddressPrefix** 내에 없기 때문에 172.131.0.100에 대한 아웃바운드 통신을 거부합니다. 아웃바운드 통신을 허용하려면 172.131.0.100에서 포트 80에 대한 아웃바운드 트래픽을 허용하는 더 높은 우선 순위를 가진 보안 규칙을 추가할 수 있습니다.

`az network watcher test-ip-flow` 명령을 실행하여 [IP 흐름 확인 사용](#use-ip-flow-verify)에서 172.131.0.100의 인바운드 통신을 테스트한 경우 출력은 **DefaultInboundDenyAll** 규칙이 통신을 거부했음을 알렸습니다. **DefaultInboundDenyAll** 규칙은 `az network nic list-effective-nsg` 명령에서 다음 출력에 나열된 **DenyAllInBound** 규칙과 동일합니다.

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

출력에 표시된 것과 같이 172.131.0.100에서 VM에 대한 포트 80 인바운드를 허용하는 `az network nic list-effective-nsg` 명령에서 출력에 더 높은 우선 순위 규칙이 없으므로 **DenyAllInBound** 규칙이 적용됩니다. 인바운드 통신을 허용하려면 172.131.0.100에서 포트 80 인바운드를 허용하는 더 높은 우선 순위를 가진 보안 규칙을 추가할 수 있습니다.

이 빠른 시작에서 검사는 Azure 구성을 테스트했습니다. 검사가 예상된 결과를 반환하고 여전히 네트워크 문제가 있는 경우 VM과 통신하고 있는 엔드포인트 간에 방화벽이 없고 VM의 운영 체제에 통신을 허용하거나 거부하는 방화벽이 없는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 VM을 만들고 인바운드 및 아웃바운드 네트워크 트래픽 필터를 진단했습니다. VM 간 트래픽을 허용하거나 거부하는 네트워크 보안 그룹 규칙을 배웠습니다. [보안 규칙](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 및 [보안 규칙을 만드는](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule) 방법에 대해 자세히 알아봅니다.

적절한 네트워크 트래픽 필터가 준비되어 있더라도 라우팅 구성으로 인해 VM에 대한 통신이 여전히 실패할 수 있습니다. VM 네트워크 라우팅 문제를 진단하는 방법을 알아보려면 [VM 라우팅 문제 진단](diagnose-vm-network-routing-problem-cli.md)을 참조하거나 하나의 도구로 아웃바운드 라우팅, 대기 시간 및 트래픽 필터링 문제를 진단하려면 [연결 문제 해결](network-watcher-connectivity-cli.md)을 참조하세요.
