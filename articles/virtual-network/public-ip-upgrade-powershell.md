---
title: 공용 IP 주소 업그레이드 - Azure PowerShell
description: 이 문서에서는 Azure PowerShell을 사용하여 기본 SKU 공용 IP 주소를 업그레이드하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 396122ce5200f6b0ed8fe95eb402c9d60947d5fc
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439265"
---
# <a name="upgrade-a-public-ip-address-using-azure-powershell"></a>Azure PowerShell을 사용하여 공용 IP 주소 업그레이드

Azure 공용 IP 주소는 기본 또는 표준 SKU를 사용하여 만듭니다. SKU는 할당 방법, 기능 지원, 연결할 수 있는 리소스를 포함한 관련 기능을 확인합니다. 

이 문서에서는 Azure PowerShell을 사용하여 고정 기본 SKU 공용 IP 주소를 표준 SKU로 업그레이드하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* 구독의 **고정** 기본 SKU 공용 IP 주소. 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](create-public-ip-portal.md#create-a-basic-sku-public-ip-address)을 참조하세요.
* 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="upgrade-public-ip-address"></a>공용 IP 주소 업그레이드

이 섹션에서는 Azure CLI를 사용하여 고정 기본 SKU 공용 IP를 표준 SKU로 업그레이드합니다.

```azurepowershell-interactive
### Place the public IP address into a variable. ###
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
$pubIP = Get-AzPublicIpAddress @ip

### Set the SKU to standard. ###
$pubIP.Sku.Name = 'Standard'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

```
> [!NOTE]
> 업그레이드하는 기본 공용 IP에는 정적 할당 유형이 있어야 합니다. 동적으로 할당된 IP 주소를 업그레이드하려고 하면 IP를 업그레이드할 수 없다는 경고가 표시됩니다.

> [!WARNING]
> 기본 공용 IP를 표준 SKU로 업그레이드하는 작업은 되돌릴 수 없습니다. 기본에서 표준 SKU로 업그레이드된 공용 IP는 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)을 보장하지 않습니다.

## <a name="verify-upgrade"></a>업그레이드 확인

이 섹션에서는 공용 IP 주소가 이제 표준 SKU인지 확인합니다.

```azurepowershell-interactive
### Place the public IP address into a variable. ###
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
$pubIP = Get-AzPublicIpAddress @ip

### Display setting. ####
$pubIP.Sku.Name
```
이 명령은 **표준** 을 표시합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 SKU 공용 IP 주소를 표준 SKU로 업그레이드했습니다.

Azure의 공용 IP 주소에 관한 자세한 내용은 다음을 참조하세요.

- [Azure의 공용 IP 주소](public-ip-addresses.md)
- [공용 IP 만들기 - Azure Portal](create-public-ip-portal.md)

