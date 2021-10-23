---
title: 공용 IP 주소에 대한 라우팅 기본 설정 구성 - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Azure PowerShell을 사용하여 공용 IP 주소에 대한 라우팅 기본 설정을 구성하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e632f7313582bfe33da59569f25319fbb90b13f0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215018"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Azure PowerShell을 사용하여 공용 IP 주소에 대한 라우팅 기본 설정을 구성합니다.

이 문서에서는 Azure PowerShell을 사용하여 공용 IP 주소에 대해 ISP 네트워크(**인터넷** 옵션)를 통해 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 공용 IP 주소를 만든 후 인터넷에 대한 인바운드 및 아웃바운드 트래픽을 위해 다음 Azure 리소스와 연결할 수 있습니다.

* 가상 머신
* 가상 머신 크기 집합
* AKS(Azure Kubernetes Service)
* 인터넷 연결 부하 분산 장치
* Application Gateway
* Azure Firewall

기본적으로 공용 IP 주소에 대한 라우팅 기본 설정은 모든 Azure 서비스에 대해 Microsoft 글로벌 네트워크로 설정되며 모든 Azure 서비스에 연결할 수 있습니다.

Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]
PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell 모듈 버전 6.9.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>인터넷 라우팅 기본 설정을 사용하여 공용 IP 만들기

다음 명령은 *미국 동부* Azure 지역에서 라우팅 기본 설정 유형이 *인터넷* 인 새 공용 IP를 만듭니다.

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

위에서 만든 공용 IP 주소를 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 자습서 페이지의 CLI 섹션을 사용합니다. [공용 IP 주소를 가상 머신에 연결](./associate-public-ip-address-vm.md#azure-cli)하여 공용 IP를 VM에 연결합니다. 위에서 만든 공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)와 연결할 수도 있습니다. 공용 IP 주소는 부하가 분산된 VIP(가상 IP 주소)로 사용됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [공용 IP 주소의 라우팅 기본 설정](routing-preference-overview.md)에 대해 자세히 알아봅니다.
- [Azure PowerShell을 사용하여 VM에 대한 라우팅 기본 설정을 구성합니다](./configure-routing-preference-virtual-machine-powershell.md).