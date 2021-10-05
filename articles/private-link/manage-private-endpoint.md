---
title: Azure에서 프라이빗 엔드포인트 연결 관리
titleSuffix: Azure Private Link
description: Azure에서 프라이빗 엔드포인트 연결을 관리하는 방법 알아보기
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 10/04/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7923e35165f756878ed52e807a1e0106e1660e59
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535960"
---
# <a name="manage-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 관리

Azure 개인 링크는 개인 링크 서비스 소비자가 서비스를 사용 하기 위해 서비스 공급자에 대 한 연결을 요청할 수 있는 승인 모델에서 작동 합니다. 

그런 다음 서비스 공급자는 소비자의 연결을 허용할지 여부를 결정할 수 있습니다. Azure 개인 링크를 통해 서비스 공급자는 해당 리소스에 대 한 개인 끝점 연결을 관리할 수 있습니다. 

이 문서에서는 프라이빗 엔드포인트 연결을 관리하는 방법에 대한 지침을 제공합니다.

![프라이빗 엔드포인트 관리](media/manage-private-endpoint/manage-private-endpoint.png)

Private Link 서비스 소비자가 선택할 수 있는 두 가지 연결 승인 방법이 있습니다.

- **자동**: 서비스 소비자가 서비스 공급자 리소스에 대 한 Access Control 사용 권한을 기반으로 하는 Azure 역할을 사용 하는 경우 소비자는 자동 승인 방법을 선택할 수 있습니다. 요청이 서비스 공급자 리소스에 도달 하면 서비스 공급자의 작업이 필요 하지 않으며 연결이 자동으로 승인 됩니다. 

- **수동**: 서비스 소비자가 서비스 공급자 리소스에 대 한 Access Control 사용 권한을 기반으로 하는 Azure 역할을가지고 있지 않은 경우에는 소비자가 수동 승인 방법을 선택할 수 있습니다. 연결 요청이 **보류 중** 으로 서비스 리소스에 나타납니다. 연결이 설정되려면 서비스 공급자가 수동으로 요청을 승인해야 합니다. 수동의 경우 서비스 소비자는 서비스 공급자에게 더 많은 컨텍스트를 제공하라는 요청과 함께 메시지를 지정할 수도 있습니다. 서비스 공급자는 모든 프라이빗 엔드포인트 연결에 대해 **승인됨**, **거부**, **제거** 옵션을 선택할 수 있습니다.

아래 표는 다양한 서비스 공급자 작업과 프라이빗 엔드포인트에 대한 결과적인 연결 상태를 보여 줍니다. 서비스 공급자는 소비자 개입 없이 연결 상태를 나중에 변경할 수 있습니다. 이 작업은 소비자 측 엔드포인트의 상태를 업데이트합니다. 


| 서비스 공급자 작업  | 서비스 소비자 프라이빗 엔드포인트 상태 | 설명 |
|---------|---------|---------|
| None    |    Pending     |    연결이 수동으로 만들어지고, Private Link 리소스 소유자의 승인이 보류 중입니다.       |
| 승인    |  승인됨       |  연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다.     |
| 거부     | 거부됨        | Private Link 리소스 소유자가 연결을 거부했습니다.        |
| 제거    |  연결 끊김       | 개인 링크 리소스 소유자가 연결을 제거 했습니다. 개인 끝점은 정보를 제공 하 고 정리를 위해 삭제 해야 합니다.        |

## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Azure PaaS 리소스에서 개인 끝점 연결 관리

Azure Portal는 Azure PaaS 리소스에서 개인 끝점 연결을 관리 하는 데 선호 되는 방법입니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **개인 링크** 를 입력 합니다. 검색 결과에서 **개인 링크** 를 선택 합니다.

3. **개인 링크 센터** 에서 **개인 끝점** 또는 **개인 링크 서비스** 를 선택 합니다.

4. 각 끝점에 대해 연결 된 개인 끝점 연결의 수를 볼 수 있습니다. 필요에 따라 리소스를 필터링할 수 있습니다.

5. 개인 끝점을 선택 합니다.  나열된 연결 아래에서 관리할 연결을 선택합니다. 

6. 상단의 옵션에서 선택하여 연결 상태를 변경할 수 있습니다.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>고객/파트너 소유 Private Link 서비스에서 프라이빗 엔드포인트 연결 관리

Azure PowerShell 및 Azure CLI는 Microsoft 파트너 서비스 또는 고객 소유 서비스에서 프라이빗 엔드포인트 연결을 관리하는 데 선호되는 방법입니다. 
 
### <a name="powershell"></a>PowerShell 
  
다음 PowerShell 명령을 사용하여 프라이빗 엔드포인트 연결을 관리합니다.  

#### <a name="get-private-link-connection-states"></a>Private Link 연결 상태 가져오기 

[AzPrivateEndpointConnection](/powershell/module/az.network/get-azprivateendpointconnection) 를 사용 하 여 개인 끝점 연결 및 해당 상태를 가져옵니다.  

```azurepowershell
Get-AzPrivateEndpointConnection -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
```
 
#### <a name="approve-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인 
 
[AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) cmdlet을 사용 하 여 개인 끝점 연결을 승인 합니다. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부 
 
[AzPrivateEndpointConnection](/powershell/module/az.network/deny-azprivateendpointconnection) cmdlet을 사용 하 여 개인 끝점 연결을 거부 합니다. 

```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```

#### <a name="remove-private-endpoint-connection"></a>개인 끝점 연결 제거 
 
개인 끝점 연결을 제거 하려면 [AzPrivateEndpointConnection](/powershell/module/az.network/remove-azprivateendpointconnection) cmdlet을 사용 합니다. 

```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
### <a name="azure-cli"></a>Azure CLI 
 
#### <a name="get-private-link-connection-states"></a>Private Link 연결 상태 가져오기 

[Az network private-endpoint-connection show](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_show) 를 사용 하 여 개인 끝점 연결 및 해당 상태를 가져옵니다.  

```azurecli
  az network private-endpoint-connection show \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```
 
#### <a name="approve-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인 
 
[Az network private-endpoint-connection approve](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_approve) cmdlet을 사용 하 여 개인 끝점 연결을 승인 합니다. 
 
```azurecli
  az network private-endpoint-connection approve \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```
 
#### <a name="deny-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부 
 
[Az network private-endpoint-connection reject](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_reject) cmdlet을 사용 하 여 개인 끝점 연결을 거부 합니다. 

```azurecli
  az network private-endpoint-connection reject \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```

#### <a name="remove-private-endpoint-connection"></a>개인 끝점 연결 제거 
 
[Az network private-endpoint-connection delete](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_delete) cmdlet을 사용 하 여 개인 끝점 연결을 제거 합니다. 

```azurecli
  az network private-endpoint-connection delete \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계
- [프라이빗 엔드포인트에 대해 자세히 알아보기](private-endpoint-overview.md)
 
