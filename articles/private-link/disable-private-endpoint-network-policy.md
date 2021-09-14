---
title: 프라이빗 엔드포인트에 대한 네트워크 정책 관리
titleSuffix: Azure Private Link
description: 프라이빗 엔드포인트에 대한 네트워크 정책을 관리하는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 08/26/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c06ad374e46e9900ba99a95708e19a63498719ec
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101550"
---
# <a name="manage-network-policies-for-private-endpoints"></a>프라이빗 엔드포인트에 대한 네트워크 정책 관리

이전에는 NSG(네트워크 보안 그룹)와 같은 네트워크 정책이 프라이빗 엔드포인트에 대해 지원되지 않았습니다. 지정된 서브넷에 프라이빗 엔드포인트를 배포하려면 해당 서브넷에서 명시적으로 사용하지 않도록 설정해야 했습니다. 해당 설정은 프라이빗 엔드포인트에만 적용됩니다. 서브넷의 다른 리소스에 대한 액세스는 네트워크 보안 그룹의 보안 규칙을 기반으로 제어됩니다.

> [!IMPORTANT]
> 프라이빗 엔드포인트에 대한 NSG 및 UDR 지원은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

포털을 사용하여 프라이빗 엔드포인트를 만들 때 만들기 프로세스의 일부로 `PrivateEndpointNetworkPolicies` 설정은 자동으로 사용하지 않도록 설정됩니다. 다른 클라이언트를 사용하는 배포에는 해당 설정을 변경하는 추가 단계가 필요합니다. 

다음을 사용하여 설정을 비활성화했다가 활성화할 수 있습니다.

* Azure Portal의 Cloud Shell
* Azure PowerShell
* Azure CLI
* Azure 리소스 관리자 템플릿
 
다음 예제에서는 **기본** 서브넷이 **myResourceGroup** 리소스 그룹에 호스트된 **myVNet** 가상 네트워크에 대해 `PrivateEndpointNetworkPolicies`를 사용하지 않도록 설정했다가 사용하도록 설정하는 방법을 설명합니다.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="disable-network-policy"></a>네트워크 정책 사용 안 함

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

### <a name="enable-network-policy"></a>네트워크 정책 사용

이 섹션에서는 Azure PowerShell을 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하도록 설정하는 방법을 설명합니다. [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 및 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)를 사용하여 정책을 사용하도록 설정합니다.

```azurepowershell
$net =@{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

($vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq 'default'}).PrivateEndpointNetworkPolicies = "Enabled"

$vnet | Set-AzVirtualNetwork
```
## <a name="azure-cli"></a>Azure CLI

### <a name="disable-network-policy"></a>네트워크 정책 사용 안 함

이 섹션에서는 Azure CLI를 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다. [az 네트워크 vnet 서브넷 업데이트](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 정책을 사용하지 않게 설정합니다.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies true \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```

### <a name="enable-network-policy"></a>네트워크 정책 사용

이 섹션에서는 Azure CLI를 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하도록 설정하는 방법을 설명합니다. [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 정책을 사용하도록 설정합니다.

```azurecli
az network vnet subnet update \ 
  --disable-private-endpoint-network-policies false \
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  
```
## <a name="resource-manager-template"></a>Resource Manager 템플릿

### <a name="disable-network-policy"></a>네트워크 정책 사용 안 함

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

### <a name="enable-network-policy"></a>네트워크 정책 사용

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하도록 설정하는 방법을 설명합니다.

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
                                    "privateEndpointNetworkPolicies": "Enabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>다음 단계
- [Azure 프라이빗 엔드포인트](private-endpoint-overview.md)에 대해 자세히 알아보기
 
