---
title: 프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정
titleSuffix: Azure Private Link
description: 프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정하는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 07/14/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90bb1584c94f58d96151e32c7b9c9e02989a840d
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634425"
---
# <a name="disable-network-policies-for-private-endpoints"></a>프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정

프라이빗 엔드포인트에는 NSG(네트워크 보안 그룹)와 같은 네트워크 정책이 지원되지 않습니다. 지정된 서브넷에 프라이빗 엔드포인트를 배포하려면 해당 서브넷에 명시적 비활성화 설정이 필요합니다. 해당 설정은 프라이빗 엔드포인트에만 적용됩니다. 서브넷의 다른 리소스에 대한 액세스는 네트워크 보안 그룹의 보안 규칙을 기반으로 제어됩니다.
 
포털을 사용하여 프라이빗 엔드포인트를 만들 때 만들기 프로세스의 일부로 `PrivateEndpointNetworkPolicies` 설정은 자동으로 사용하지 않도록 설정됩니다. 다른 클라이언트를 사용하는 배포에는 해당 설정을 변경하는 추가 단계가 필요합니다. 

다음을 사용하여 설정을 사용하지 않게 설정할 수 있습니다.

* Azure Portal의 Cloud Shell
* Azure PowerShell
* Azure CLI
* Azure 리소스 관리자 템플릿
 
다음 예제에서는 **myResourceGroup** 이라는 리소스 그룹에서 호스트된 **기본** 서브넷을 사용하여 **myVNet** 이라는 가상 네트워크에 대해 `PrivateEndpointNetworkPolicies`을 사용하지 않도록 설정하는 방법을 설명합니다.

## <a name="azure-powershell"></a>Azure PowerShell

이 섹션에서는 Azure PowerShell을 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다. [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 및 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)를 사용하여 정책을 사용하지 않게 설정합니다.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Disabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI
이 섹션에서는 Azure CLI를 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다. [az 네트워크 vnet 서브넷 업데이트](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 정책을 사용하지 않게 설정합니다.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Resource Manager 템플릿
이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다.

```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>다음 단계
- [Azure 프라이빗 엔드포인트](private-endpoint-overview.md)에 대해 자세히 알아보기
 
