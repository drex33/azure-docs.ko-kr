---
title: '빠른 시작: 공용 IP 만들기 - PowerShell'
titleSuffix: Azure Virtual Network
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 공용 IP를 만드는 방법을 알아봅니다.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.custom: mode-api
ms.openlocfilehash: fd25716c3f6edd54c2aff1f681569d1b484d48c1
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051901"
---
# <a name="quickstart-create-a-public-ip-address-using-powershell"></a>빠른 시작: PowerShell을 사용하여 공용 IP 주소 만들기

이 빠른 시작에서는 Azure 공용 IP 주소를 만드는 방법을 알아봅니다. Azure의 공용 IP 주소는 Azure 리소스에 대한 공용 연결에 사용됩니다. 공용 IP 주소는 기본 및 표준의 두 가지 SKU로 제공됩니다. 공용 IP 주소의 두 계층(지역 및 전역)을 사용할 수 있습니다. 공용 IP 주소의 라우팅 기본 설정은 생성 시 설정됩니다. 인터넷 라우팅 및 Microsoft 네트워크 라우팅은 사용 가능한 선택 항목입니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **QuickStartCreateIP-rg** 라는 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-public-ip"></a>공용 IP 만들기

# <a name="standard-sku"></a>[**표준 SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>표준 SKU 공용 IP는 프로덕션 워크로드용으로 권장됩니다.  SKU에 대한 자세한 내용은 **[공용 IP 주소](public-ip-addresses.md)** 를 참조하세요.
>
>다음 명령은 Az.Network 모듈 버전 4.5.0 이상에서 작동합니다.  현재 사용 중인 Powershell 모듈에 대한 자세한 내용은 [PowerShellGet 설명서](/powershell/module/powershellget/)를 참조하세요.

이 섹션에서는 영역이 있는 공용 IP를 만듭니다. 공용 IP 주소는 영역 중복 또는 영역일 수 있습니다.

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 **QuickStartCreateIP-rg** 에 **myStandardPublicIP** 라는 표준 영역 중복 공용 IPv4 주소를 만듭니다. 

IPv6 주소를 만들려면 **`--IpAddressVersion`** 매개 변수를 **IPv6** 로 수정합니다.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpAddress @ip
```
> [!IMPORTANT]
> 4\.5.0 이전의 Az.Network 모듈의 경우 영역 매개 변수를 지정하지 않고 위의 명령을 실행하여 영역 중복 IP 주소를 만듭니다. 
>

# <a name="basic-sku"></a>[**기본 SKU**](#tab/create-public-ip-basic)

>[!NOTE]
>표준 SKU 공용 IP는 프로덕션 워크로드용으로 권장됩니다.  SKU에 대한 자세한 내용은 **[공용 IP 주소](public-ip-addresses.md)** 를 참조하세요.

이 섹션에서는 기본 IP를 만듭니다. 기본 공용 IP는 가용성 영역을 지원하지 않습니다.

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 **QuickStartCreateIP-rg** 에 **myBasicPublicIP** 라는 기본 고정 공용 IPv4 주소를 만듭니다.  

IPv6 주소를 만들려면 **`--IpAddressVersion`** 매개 변수를 **IPv6** 로 수정합니다. 

```azurepowershell-interactive
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
시간에 따라 IP 주소를 변경할 수 있는 경우 **`-AllocationMethod`** 를 **동적** 으로 변경하여 **동적** IP 할당을 선택할 수 있습니다. 

>[!NOTE]
> 기본 IPv6 주소는 항상 '동적'이어야 합니다.

---

## <a name="create-a-zonal-or-no-zone-public-ip-address"></a>영역 또는 비영역 공용 IP 주소 만들기

이 섹션에서는 영역 또는 비영역 공용 IP 주소를 만드는 방법을 알아봅니다.

# <a name="zonal"></a>[**영역**](#tab/create-public-ip-zonal)

**QuickStartCreateIP-rg** 에서 **myStandardPublicIP-zonal** 이라는 영역 2에 표준 영역 공용 IPv4 주소를 만들려면 다음 명령을 사용합니다.

IPv6 주소를 만들려면 **`--IpAddressVersion`** 매개 변수를 **IPv6** 로 수정합니다.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-zonal'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>위의 영역 옵션은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서만 유효한 옵션입니다.

# <a name="non-zonal"></a>[**비영역**](#tab/create-public-ip-non-zonal)

이 섹션에서는 비영역 IP 주소를 만듭니다.  

>[!NOTE]
>다음 명령은 Az.Network 모듈 버전 4.5.0 이상에서 작동합니다.  현재 사용 중인 PowerShell 모듈에 대한 자세한 내용은 [PowerShellGet 설명서](/powershell/module/powershellget/)를 참조하세요.

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 **QuickStartCreateIP-rg** 에서 **myStandardPublicIP-nozone** 이라는 비영역 리소스로 표준 공용 IPv4 주소를 만듭니다. 

IPv6 주소를 만들려면 **`-IpAddressVersion`** 매개 변수를 **IPv6** 로 수정합니다.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-nozone'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
명령에서 **`-Zone`** 매개 변수를 제거하는 것은 모든 지역에서 유효합니다.  

**`-Zone`** 매개 변수를 제거하는 것은 [가용성 영역](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 없는 지역에서 표준 공용 IP 주소에 대한 기본 선택 항목입니다.

---

## <a name="routing-preference-and-tier"></a>라우팅 기본 설정 및 계층

표준 SKU 정적 공용 IPv4 주소는 라우팅 기본 설정 또는 글로벌 계층 기능을 지원합니다.

# <a name="routing-preference"></a>[**라우팅 기본 설정**](#tab/routing-preference)

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 사용자에게 Microsoft의 글로벌 광역 네트워크를 통해 트래픽을 전달하는 "Microsoft 네트워크"로 설정됩니다.  

**인터넷** 을 선택하면 전송 ISP 네트워크를 사용하여 비용 최적화 속도로 트래픽을 전달하는 대신 Microsoft 네트워크에서의 이동이 최소화됩니다.  

라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](routing-preference-overview.md)을 참조하세요.

이 명령은 **인터넷** 형식의 라우팅 기본 설정을 통해 새 표준 영역 중복 공용 IPv4 주소를 만듭니다.

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP-RP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

# <a name="tier"></a>[**서비스 계층**](#tab/tier)

공용 IP 주소는 단일 지역과 연결됩니다. **글로벌** 계층은 여러 지역에 걸쳐 IP 주소를 확장합니다. 지역 간 부하 분산 장치의 프런트 엔드에는 **글로벌** 계층이 필요합니다.  

자세한 내용은 [지역 간 부하 분산 장치](../../load-balancer/cross-region-overview.md)를 참조하세요.

다음 명령은 글로벌 IPv4 주소를 만듭니다. 이 주소는 지역 간 부하 분산 장치의 프런트 엔드와 연결될 수 있습니다.

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Tier = 'Global'
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>글로벌 계층 주소는 가용성 영역을 지원하지 않습니다.

---

## <a name="clean-up-resources"></a>리소스 정리

가상 머신과 공용 IP 주소 작업을 마쳤으면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>다음 단계

공용 IP 접두사를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [PowerShell을 사용하여 공용 IP 접두사 만들기](create-public-ip-prefix-powershell.md)
