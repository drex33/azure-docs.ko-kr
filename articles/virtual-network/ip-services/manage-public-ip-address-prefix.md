---
title: Azure 공용 IP 주소 접두사 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 공용 IP 주소 접두사 및 이를 생성, 변경 또는 삭제하는 방법에 대해 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 824d89126252a0690b93a2129f47d8e02b728694
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560115"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기, 변경 또는 삭제

공용 IP 주소 접두사에 대해 알아봅니다. 공용 IP 주소 접두사는 표준 SKU 공용 IP 주소의 연속 범위입니다.  공용 IP 주소 리소스를 만드는 경우 접두사에서 고정 공용 IP를 할당하고 Azure 리소스에 주소를 연결할 수 있습니다. 자세한 내용은 [공용 IP 주소 접두사 개요](public-ip-address-prefix.md)를 참조하세요.

## <a name="create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기

다음 섹션에서는 공용 IP 접두사를 만들 때의 매개 변수에 대해 자세히 설명합니다.

   |설정|필수 여부|세부 정보|
   |---|---|---|
   |Subscription|예|공용 IP 주소를 연결하려는 리소스와 동일한 [구독](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)에 있어야 합니다.|
   |Resource group|예|공용 IP 주소를 연결하려는 리소스와 동일하거나 다른 [리소스 그룹](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에 있을 수 있습니다.|
   |이름|예|이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |지역|예|범위에서 주소를 할당할 공용 IP 주소와 동일한 [지역](https://azure.microsoft.com/regions)에 있어야 합니다.|
   |IP 버전|Yes| 접두사의 IP 버전(v4 또는 v6)
   |접두사 크기|예| 필요한 접두사의 크기입니다. IP 주소가 16개인 범위(v4의 경우 /28, v6의 경우 /124)가 기본값입니다.

또는 아래 CLI 및 PowerShell 명령을 사용하여 공용 IP 주소 접두사를 만들 수 있습니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>가용성 영역을 사용하는 지역에서는 PowerShell 또는 CLI 명령을 사용하여 공용 IP 주소 접두사를 영역 비 영역, 특정 영역과 연결되도록 만들거나 영역 중복성을 사용할 수 있습니다.  API 버전 2020-08-01 이상에서는 영역 매개 변수가 제공되지 않은 경우 영역 공용 IP 주소 접두사가 생성되지 않습니다. 2020-08-01 보다 오래된 API 버전의 경우 영역 중복 공용 IP 주소 접두사가 생성됩니다. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>접두사로 고정 공용 IP 주소 만들기

다음 섹션에서는 접두사에서 고정 공용 IP 주소를 만들 때 필요한 매개 변수에 대해 자세히 설명합니다.

   |설정|필수 여부|세부 정보|
   |---|---|---|
   |이름|예|공용 IP 주소의 이름은 선택한 리소스 그룹 내에서 고유해야 합니다.|
   |유휴 제한 시간(분)|예|연결 유지 메시지를 보내는 데 클라이언트를 사용하지 않고 TCP 또는 HTTP 연결을 유지하는 데 걸리는 시간(분)입니다. |
   |DNS 이름 레이블|예|이름을 만드는 Azure 지역 내에서(모든 구독 및 모든 고객에서) 고유해야 합니다. Azure는 해당 DNS에서 이름과 IP 주소를 자동으로 등록하므로 해당 이름을 사용하는 리소스에 연결할 수 있습니다. Azure에서는 정규화된 DNS 이름을 만드는 데 제공하는 이름에 기본 서브넷 *location.cloudapp.azure.com* 을 추가합니다. 자세한 내용은 [Azure 공용 IP 주소로 Azure DNS 사용](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)을 참조하세요.|

또는 아래의 CLI 및 PowerShell 명령을 **--public-ip-prefix(CLI)** 및 **-PublicIpPrefix(PowerShell)** 매개 변수와 함께 사용하여 접두사에서 공용 IP 주소 리소스를 만들 수 있습니다. 

|도구|명령|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

>[!NOTE]
>공용 IP 접두사에서 공용 IP 주소를 요청할 때 할당은 결정적이거나 순차적이지 않습니다. 공용 IP 접두사에서 특정 공용 IP 주소가 필요한 경우 PowerShell 또는 CLI 명령을 통해 이를 허용합니다.  PowerShell의 경우 `IpAddress` 매개 변수(뒤에 원하는 IP)를 사용해야 합니다. CLI의 경우 매개 변수(원하는 IP 뒤에)를 `ip-address` 사용해야 합니다.

>[!NOTE]
>표준 SKU로 만든 고정 공용 IP 주소만 접두사의 범위에서 할당할 수 있습니다. 공용 IP 주소 SKU에 대해 자세히 알아보려면 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)를 참조하세요.

## <a name="view-or-delete-a-prefix"></a>접두사 보기 또는 삭제

접두사 보기 또는 삭제를 위해 Azure CLI 및 Azure PowerShell에서 다음 명령을 사용할 수 있습니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list)는 공용 IP 주소를 나열합니다.<br>[az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show)는 설정을 표시합니다.<br> [az network public-ip prefix update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update)는 업데이트합니다.<br>[az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete)는 삭제합니다.|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix)는 공용 IP 주소 개체를 검색하고 해당 설정을 확인합니다.<br>[Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix)는 설정을 업데이트합니다.<br> [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix)는 삭제합니다.|

## <a name="permissions"></a>사용 권한

공용 IP 주소 접두사를 관리하는 권한의 경우 계정을 [네트워크 기여자](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 할당해야 합니다. 

| 작업                                                            | 이름                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | 공용 IP 주소 접두사 읽기                                |
| Microsoft.Network/publicIPPrefixes/write                          | 공용 IP 주소 접두사 만들기 또는 업데이트                    |
| Microsoft.Network/publicIPPrefixes/delete                         | 공용 IP 주소 접두사 삭제                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | 접두사로 공용 IP 주소 만들기 |

## <a name="next-steps"></a>다음 단계

- [공용 IP 접두사](public-ip-address-prefix.md)를 사용하는 경우의 시나리오 및 이점에 대해 알아보기
