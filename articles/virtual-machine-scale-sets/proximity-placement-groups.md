---
title: 가상 머신 확장 집합에 대한 근접 배치 그룹
description: Azure에서 Windows 가상 머신 확장 집합에 대한 근접 배치 그룹을 만들고 사용하는 방법을 알아봅니다.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: b5533f14a239e3e2d01c597d57ab2b2726e930b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693267"
---
# <a name="creating-and-using-proximity-placement-groups-using-powershell"></a>PowerShell을 사용하여 근접 배치 그룹 만들기 및 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일 확장 집합

VM을 가능한 가깝게 유지하여 가장 낮은 대기 시간을 달성하려면 [근접 배치 그룹](../virtual-machines/co-location.md#proximity-placement-groups) 내에 확장 집합을 배포해야 합니다.

근접 배치 그룹은 Azure 컴퓨팅 리소스가 물리적으로 서로 가까운 위치에 있도록 하는 데 사용되는 논리적 그룹화입니다. 근접 배치 그룹은 낮은 대기 시간을 요구하는 작업에 유용합니다.


## <a name="create-a-proximity-placement-group"></a>근접 배치 그룹 만들기
[New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet을 사용하여 근접 배치 그룹을 만듭니다. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 나열

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet을 사용하여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>확장 집합 만들기

[New-AzVMSS](/powershell/module/az.compute/new-azvmss)를 사용하여 확장 집합을 만들 경우 `-ProximityPlacementGroup $ppg.Id`로 근접 배치 그룹 ID를 참조하여 근접 배치 그룹에 확장 집합을 만듭니다.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)을 사용하여 배치 그룹에서 인스턴스를 볼 수 있습니다.

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>다음 단계

또한 [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md)를 사용하여 근접 배치 그룹을 만들 수도 있습니다.
