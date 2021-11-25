---
title: 기존 Azure Load Balancer를 사용하여 가상 머신 확장 집합 구성 - Azure PowerShell
description: 기존 Azure Load Balancer를 사용하여 가상 머신 확장 집합을 구성하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 210bf1c01b31acca1cd68e518a5c765fcd5cc50a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110695742"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Azure PowerShell을 사용하여 기존 Azure Load Balancer로 가상 머신 확장 집합 구성

이 문서에서는 기존 Azure Load Balancer를 사용하여 가상 머신 확장 집합을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독
- 가상 머신 확장 집합이 배포될 구독의 기존 표준 sku 부하 분산 장치입니다.
- 가상 머신 확장 집합에 대한 Azure Virtual Network입니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Azure CLI에 로그인

Azure에 로그인합니다.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>기존 부하 분산 장치를 사용하여 가상 머신 확장 집합 배포

대괄호 안의 값을 구성의 리소스 이름으로 바꿉니다.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

아래 예제에서는 다음을 사용하여 가상 머신 확장 집합을 배포합니다:

- 이름이 **myVMSS** 인 가상 머신 확장 집합
- 이름이 **myLoadBalancer** 인 부하 분산 장치
- 이름이 **myBackendPool** 인 부하 분산 장치 백 엔드 풀
- 이름이 **myVnet** 인 Azure Virtual Network
- 이름이 **mySubnet** 인 서브넷
- 이름이 **myResourceGroup** 인 리소스 그룹

```azurepowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> 확장 집합을 만든 후에는 부하 분산 장치의 상태 프로브에서 사용하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다. 포트를 변경하려면 Azure 가상 머신 확장 집합을 업데이트하여 상태 프로브를 제거하고 포트를 업데이트한 다음, 상태 프로브를 다시 구성합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 Azure Load Balancer를 사용하여 가상 머신 확장 집합을 배포했습니다.  가상 머신 확장 집합과 부하 분산 장치에 대한 자세한 내용은 다음을 참조하세요:

- [Azure Load Balancer란?](load-balancer-overview.md)
- [가상 머신 크기 집합이란?](../virtual-machine-scale-sets/overview.md)
