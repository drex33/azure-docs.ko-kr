---
title: '빠른 시작: 공용 IP 주소 접두사 만들기 - PowerShell'
titlesuffix: Azure Virtual Network
description: PowerShell을 사용하여 공용 IP 주소 접두사를 만드는 방법을 알아봅니다.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 24c046aa7aab0932065f87ba3179185e9c5ed2fc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369654"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-powershell"></a>빠른 시작: PowerShell을 사용하여 공용 IP 주소 접두사 만들기

공용 IP 주소 접두사 및 해당 IP 주소를 생성, 변경 및 삭제하는 방법에 대해 알아봅니다. 공용 IP 주소 접두사는 표준 SKU 공용 IP 주소의 연속 범위입니다. 

공용 IP 주소 리소스를 만들 때 접두사에서 고정 공용 IP 주소를 할당하고 주소를 가상 머신, 부하 분산 장치 또는 기타 리소스에 연결할 수 있습니다. 자세한 내용은 [공용 IP 주소 접두사 개요](public-ip-address-prefix.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **myResourceGroup** 이라는 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```

## <a name="create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사 만들기

이 섹션에서는 Azure PowerShell을 사용하여 영역 중복, 영역 및 비 영역 공용 IP 접두사를 만듭니다. 

예제의 접두사는 다음과 같습니다.

* **IPv4** - /28(16개 주소)

* **IPv6** - /124(16개 주소)

사용 가능한 접두사 크기에 대한 자세한 내용은 [접두사 크기](public-ip-address-prefix.md#prefix-sizes)를 참조하세요.

**myPublicIpPrefix** 라는 [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)를 사용하여 공용 IP 접두사를 **eastus2** 위치에 만듭니다.

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**영역 중복 IPv4 접두사**](#tab/ipv4-zone-redundant)

IPv4 공용 IP 접두사를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv4** 를 입력합니다. 영역 중복 IPv4 접두사를 만들려면 **`-Zone`** 매개 변수에 **1,2,3** 을 입력합니다.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv4
```

# <a name="zonal-ipv4-prefix"></a>[**영역 IPv4 접두사**](#tab/ipv4-zonal)

IPv4 공용 IP 접두사를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv4** 를 입력합니다. **`-Zone`** 매개 변수에 **2** 를 입력하여 영역 2에 영역 IP 접두사를 만듭니다.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix-zonal'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpPrefix @ipv4
```

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="non-zonal-ipv4-prefix"></a>[**비 영역 IPv4 접두사**](#tab/ipv4-non-zonal)

IPv4 공용 IP 접두사를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv4** 를 입력합니다. **`-Zone`** 매개 변수를 제거하여 비영역 IP 접두사를 만듭니다.

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix-nozone'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpPrefix @ipv4
```

명령에서 **`-Zone`** 매개 변수를 제거하는 것은 모든 지역에서 유효합니다.  

**`-Zone`** 매개 변수 제거는 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 선택 영역입니다.

---

## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**영역 중복 IPv6 접두사**](#tab/ipv6-zone-redundant)

IPv4 공용 IP 접두사를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv6** 을 입력합니다. 영역 중복 IPv6 접두사를 만들려면 **`-Zone`** 매개 변수에 **1,2,3** 을 입력합니다.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv6
```

# <a name="zonal-ipv6-prefix"></a>[**영역 IPv6 접두사**](#tab/ipv6-zonal)

IPv6 공용 IP 접두사를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv6** 을 입력합니다. **`-Zone`** 매개 변수에 **2** 를 입력하여 영역 2에 영역 IP 접두사를 만듭니다.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix-zonal'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 2
}
New-AzPublicIpPrefix @ipv6
```

>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="non-zonal-ipv6-prefix"></a>[**비 영역 IPv6 접두사**](#tab/ipv6-non-zonal)

IPv6 공용 IP 접두사를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv6** 을 입력합니다. **`-Zone`** 매개 변수를 제거하여 비영역 IP 접두사를 만듭니다.

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix-nozone'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
}
New-AzPublicIpPrefix @ipv6
```

명령에서 **`-Zone`** 매개 변수를 제거하는 것은 모든 지역에서 유효합니다.  

**`-Zone`** 매개 변수 제거는 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 선택 영역입니다.

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>접두사로 고정 공용 IP 주소 만들기

접두사를 만들었으면 접두사로 고정 IP 주소를 만들어야 합니다. 이 섹션에서는 앞에서 만든 접두사에서 고정 IP 주소를 만듭니다.

**myPublicIpPrefix** 접두사에서 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다.

# <a name="ipv4-address"></a>[**IPv4 주소**](#tab/ipv4-address)

IPv4 공용 IP 주소를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv4** 를 입력합니다.

```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv4 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv4
```

# <a name="ipv6-address"></a>[**IPv6 주소**](#tab/ipv6-address)

IPv6 공용 IP 주소를 만들려면 **`-IpAddressVersion`** 매개 변수에 **IPv6** 을 입력합니다.
```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv6 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv6'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv6
```

---

>[!NOTE]
>표준 SKU로 만든 고정 공용 IP 주소만 접두사의 범위에서 할당할 수 있습니다. 공용 IP 주소 SKU에 대해 자세히 알아보려면 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)를 참조하세요.

## <a name="delete-a-prefix"></a>접두사 삭제

이 섹션에서는 접두사를 삭제하는 방법을 알아봅니다.

공용 IP 접두사를 삭제하려면 [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix)를 사용합니다.

```azurepowershell-interactive
$pr =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'QuickStartCreateIPPrefix-rg'
}
Remove-AzPublicIpPrefix @pr
```

>[!NOTE]
>접두사 내 주소가 공용 IP 주소 리소스에 연결되어 있으면 공용 IP 주소 리소스를 먼저 삭제해야 합니다. [공용 IP 주소 삭제](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 공용 IP 접두사와 해당 접두사의 공용 IP를 만들었습니다. 

공용 IP 접두사 작업을 마쳤으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName 'QuickStartCreateIPPrefix-rg'
```

## <a name="next-steps"></a>다음 단계

다음 문서(Azure CLI:
> [!div class="nextstepaction"]
> [PowerShell로 공용 IP 만들기](create-public-ip-powershell.md))로 진행하여 공용 IP 접두사를 만드는 방법을 알아봅니다.
