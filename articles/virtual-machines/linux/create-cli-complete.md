---
title: Azure CLI을 사용하여 Linux 환경 만들기
description: Azure CLI를 사용하여 스토리지, Linux VM, 가상 네트워크 및 서브넷, 부하 분산 디바이스, NIC, 공용 IP, 네트워크 보안 그룹을 모두 처음부터 새로 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 8af41db15e1bea6ac6b032291ba31778bf1cef0e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689901"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Azure CLI를 사용하여 완전한 Linux 가상 머신 만들기

**적용 대상:** :heavy_check_mark: Linux VM 

Azure에서 가상 머신(VM)를 신속하게 만들려면 기본 값을 사용하여 모든 필요한 지원 리소스를 생성하는 단일 Azure CLI 명령을 사용할 수 있습니다. 가상 네트워크, 공용 IP 주소 및 네트워크 보안 그룹 규칙 등의 리소스는 자동으로 생성됩니다. 프로덕션 환경에서의 더 높은 제어를 위해 미리 이 리소스를 만들어 VM을 여기에 추가할 수 있습니다. 이 문서에서는 VM을 만들고 지원 리소스를 하나씩 만드는 방법을 안내합니다.

최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치했고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myVnet*, *myVM* 이 포함됩니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 리소스 그룹은 가상 머신과 지원하는 가상 네트워크 리소스에 앞서 만들어져야 합니다. [az group create](/cli/azure/group)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

기본적으로 Azure CLI 명령의 출력은 JSON(JavaScript Object Notation)으로 제공됩니다. 예를 들어 기본 출력을 목록이나 테이블로 변경하려면 [az config set core.output=table](/cli/azure/reference-index)을 사용합니다. 출력 형식을 1번 변경하기 위해 명령에 `--output`을 추가할 수도 있습니다. 다음 예제에서는 `az group create` 명령에서의 JSON 출력을 표시합니다.

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기
다음에는 Azure에서 실행되는 가상 네트워크와, VM을 만들 수 있는 서브넷을 만듭니다. [az network vnet create](/cli/azure/network/vnet)를 사용하여 이름이 *myVnet* 이고 주소 접두사가 *192.168.0.0/16* 인 가상 네트워크를 만듭니다. 주소 접두사로 *192.168.1.0/24* 를 사용하는 *mySubnet* 이라는 서브넷도 추가합니다.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

출력은 가상 네트워크 내에서 논리적으로 만들어진 서브넷이 표시됩니다.

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기
이제 [az network public-ip create](/cli/azure/network/public-ip)를 사용하여 공용 IP 주소를 만듭니다. 이 공용 IP 주소를 사용하면 인터넷에서 VM에 연결할 수 있습니다. 기본 주소가 동적이므로 `--domain-name-label` 매개 변수를 사용하여 명명된 DNS 항목을 만듭니다. 다음 예제는 *mypublicdns* 라는 DNS 이름으로 *myPublicIP* 라는 공용 IP를 만듭니다. DNS 이름은 고유해야 하므로 자체 DNS 이름을 입력합니다.

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

출력:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기
VM 내/외부 네트워크 트래픽의 흐름을 제어하려면 네트워크 보안 그룹을 가상 NIC 또는 서브넷에 적용합니다. 다음 예제에서는 [az network nsg create](/cli/azure/network/nsg)를 사용하여 *myNetworkSecurityGroup* 이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

특정 트래픽을 허용하거나 거부하는 규칙을 정의합니다. (SSH를 지원하기 위해) 포트 22에 인바운드 연결이 가능하도록 하려면 [az network nsg rule create](/cli/azure/network/nsg/rule)를 사용하여 인바운드 규칙을 만듭니다. 다음 예제에서는 *myNetworkSecurityGroupRuleSSH* 이라는 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

포트 80에서 인바운드 연결을 허용하기 위해(웹 트래픽의 경우) 다른 네트워크 보안 그룹 규칙을 추가합니다. 다음 예제에서는 *myNetworkSecurityGroupRuleHTTP* 라는 규칙을 만듭니다.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

[az network nsg show](/cli/azure/network/nsg)를 사용하여 네트워크 보안 그룹 및 규칙을 검사합니다.

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

출력:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>가상 NIC 만들기
가상 네트워크 인터페이스 카드(NIC)는 사용할 때 규칙을 적용할 수 있으므로 프로그래밍 방식으로 사용할 수 있습니다. [VM 크기](../sizes.md)에 따라 여러 가상 NIC를 VM에 연결할 수 있습니다. 다음 [az network nic create](/cli/azure/network/nic) 명령에서 *myNic* 이라는 NIC를 만들고 네트워크 보안 그룹과 연결합니다. 공용 IP 주소 *myPublicIP* 도 가상 NIC에 연결됩니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

출력:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>가용성 집합 만들기
가용성 집합은 장애 도메인 및 업데이트 도메인에 걸쳐 VM을 분산하는 데 유용합니다. 당장은 하나의 VM만 만든다 하더라도 향후 확장하기 쉽게 가용성 집합을 사용하는 것이 가장 좋습니다. 

장애 도메인은 공통의 전원 및 네트워크 스위치를 공유하는 가상 머신 그룹을 정의합니다. 기본적으로 가용성 집합 안에 구성된 가상 머신은 최대 3개의 장애 도메인에 분산되어 있습니다. 이러한 장애 도메인 중 하나에서 발생한 하드웨어 문제가 앱을 실행 중인 모든 VM에 영향을 미치지 않습니다.

업데이트 도메인은 동시에 다시 부팅할 수 있는 가상 머신 그룹과 기본 물리적 하드웨어를 나타냅니다. 계획된 유지 보수 중에 업데이트 도메인의 재부팅 순서는 순차적으로 진행되지 않을 수 있으며, 한 번에 하나의 업데이트 도메인만 재부팅됩니다.

Azure는 가용성 집합에 VM을 배치할 때 VM을 전체 장애 및 업데이트 도메인에 자동으로 분산합니다. 자세한 내용은 [VM의 가용성 관리](../availability.md)를 참조하세요.

[az vm availability-set create](/cli/azure/vm/availability-set)를 사용하여 VM에 대한 가용성 집합을 만듭니다. 다음 예제는 *myAvailabilitySet* 이라는 가용성 집합을 만듭니다.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

출력에서 장애 도메인과 업데이트 도메인을 알립니다.

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>VM 만들기
인터넷에서 액세스 가능한 VM을 지원하기 위해 네트워크 리소스를 만들었습니다. 이제 VM을 만들어 SSH 키로 보호합니다. 이 예에서는 가장 최근의 LTS를 기반으로 Ubuntu VM을 만들겠습니다. [Azure VM 이미지 찾기](cli-ps-findimage.md)에서 설명한 대로 [az vm image list](/cli/azure/vm/image)를 통해 추가적인 이미지를 찾을 수 있습니다.

인증에 사용할 SSH 키를 지정합니다. SSH 공개 키 쌍이 없는 경우 [만들거나 ](mac-create-ssh-keys.md)`--generate-ssh-keys` 매개 변수를 사용하여 만들 수 있습니다. 키 쌍이 있으면 이 매개 변수는 `~/.ssh`의 기존 키를 사용합니다.

[az vm create](/cli/azure/vm) 명령으로 모든 리소스 및 정보를 결합하여 VM을 만듭니다. 다음 예제에서는 *myVM* 이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

공용 IP 주소를 만들 때 제공한 DNS 항목이 있는 VM에 대한 SSH. 이 `fqdn`은 VM을 만들 때 출력에 표시됩니다.

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

출력:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

NGINX를 설치하고 VM에 대한 트래픽 흐름을 확인할 수 있습니다. 다음과 같이 NGINX를 설치합니다.

```bash
sudo apt-get install -y nginx
```

작동 중인 기본 NGINX 사이트를 확인하려면 웹 브라우저를 열고 FQDN을 입력합니다.

![VM의 기본 NGINX 사이트](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>템플릿으로 내보내기
동일한 매개 변수를 사용하여 추가 개발 환경을 만들려고 하거나 일치하는 프로덕션 환경을 만들려면 어떻게 해야 할까요? Resource Manager는 사용자 환경에 대한 모든 매개 변수를 정의하는 JSON 템플릿을 사용합니다. 이 JSON 템플릿을 참조하여 전체 환경을 빌드합니다. [JSON 템플릿을 수동으로 빌드](../../azure-resource-manager/templates/syntax.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하거나 기존 환경을 내보내 JSON 템플릿을 만들 수 있습니다. 다음과 같이 [az group export](/cli/azure/group)를 사용하여 리소스 그룹을 내보냅니다.

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

이 명령을 실행하면 `myResourceGroup.json` 파일이 현재 작업 디렉터리에 만들어집니다. 이 템플릿에서 환경을 만들면 모든 리소스 이름을 입력하라는 메시지가 표시됩니다. `az group export` 명령에 `--include-parameter-default-value` 매개 변수를 추가하여 템플릿 파일에 이러한 이름을 입력할 수 있습니다. JSON 템플릿을 편집하여 리소스 이름을 지정하거나 리소스 이름을 지정하는 [parameters.json 파일을 만듭니다](../../azure-resource-manager/templates/syntax.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

템플릿에서 환경을 만들려면 다음과 같이 [az deployment group create](/cli/azure/deployment/group)를 사용합니다.

```azurecli
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

[템플릿에서 배포하는 방법에 대해 자세히 읽어볼 수 있습니다](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json). 환경을 점진적으로 업데이트하고, 매개 변수 파일을 사용하고, 단일 스토리지 위치에서 템플릿에 액세스하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계
이제 여러 네트워킹 구성 요소 및 VM을 사용할 준비가 되셨습니다. 이 샘플 환경에 사용하여 여기에 소개된 핵심 구성 요소로 애플리케이션을 빌드할 수 있습니다.