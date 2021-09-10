---
title: Azure PowerShell을 사용하여 Azure 전용 호스트 배포
description: Azure PowerShell을 사용하여 전용 호스트에 VM을 배포합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f2511874324a6fafff1170f8764c57a466a7e62
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693428"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Azure PowerShell을 사용하여 전용 호스트에 VM 배포

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서에서는 VM(가상 머신)을 호스팅하는 Azure [전용 호스트](../dedicated-hosts.md)를 만드는 방법을 안내합니다. 

Azure PowerShell 버전 2.8.0 이상을 설치했는지 확인하고 `Connect-AzAccount`를 사용하여 Azure 계정에 로그인합니다.

## <a name="limitations"></a>제한 사항

- 전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="create-a-host-group"></a>호스트 그룹 만들기

**호스트 그룹** 은 전용 호스트의 모음을 나타내는 리소스입니다. 영역 및 가용성 영역에서 호스트 그룹을 만들고 여기에 호스트를 추가합니다. 고가용성을 계획할 때 추가 옵션을 사용할 수 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다를 사용할 수 있습니다.
- 여러 가용성 영역으로 확장. 이 경우에는 사용하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 실제 랙에 매핑된 여러 장애 도메인으로 확장.

두 경우 모두 호스트 그룹의 장애 도메인 수를 제공해야 합니다. 그룹의 장애 도메인을 확장하지 않으려면 장애 도메인 수 1을 사용합니다.

가용성 영역 및 장애 도메인을 모두 사용하도록 결정할 수도 있습니다. 이 예제에서는 2개 장애 도메인을 사용하여 영역 1에 호스트 그룹을 만듭니다.


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


VM 및 확장 집합 인스턴스가 하나의 호스트 그룹 내에서 자동으로 호스트에 배치할 수 있게 `-SupportAutomaticPlacement true` 매개 변수를 추가합니다. 자세한 내용은 [수동 및 자동 배치](../dedicated-hosts.md#manual-vs-automatic-placement)를 참조하세요.


## <a name="create-a-host"></a>호스트 만들기

이제 호스트 그룹에서 전용 호스트를 만들어 보겠습니다. 호스트 이름 외에 호스트의 SKU도 제공해야 합니다. 호스트 SKU는 지원되는 VM 시리즈뿐만 아니라 전용 호스트의 하드웨어 생성도 캡처합니다.

호스트 SKU 및 가격 책정에 대한 자세한 내용은 [Azure Dedicated Host 가격 책정](https://aka.ms/ADHPricing)을 참조하세요.

호스트 그룹의 장애 도메인 수를 설정하면 호스트의 장애 도메인을 지정하라는 메시지가 표시됩니다. 이 예제에서는 호스트의 장애 도메인을 1로 설정합니다.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>VM 만들기

전용 호스트에서 가상 머신을 만듭니다.

호스트 그룹을 만들 때 가용성 영역을 지정한 경우 가상 머신을 만들 때 동일한 영역을 사용해야 합니다. 이 예제에서는 호스트 그룹이 영역 1에 있기 때문에 영역 1에서 VM을 만들어야 합니다.


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 리소스가 부족한 호스트에 가상 머신을 만드는 경우 가상 머신이 FAILED 상태로 생성됩니다.

## <a name="check-the-status-of-the-host"></a>호스트의 상태 확인

`-InstanceView` 매개 변수와 함께 [GetAzHost](/powershell/module/az.compute/get-azhost)를 사용하여 호스트에 배포할 수 있는 가상 머신의 수와 호스트 상태를 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

출력은 다음과 유사합니다.

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           :
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    :
    AllocatableVMs[0]  :
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  :
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  :
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  :
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  :
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  :
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  :
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  :
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  :
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  :
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          :
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          :
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    :
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>확장 집합 만들기

확장 집합을 배포하는 경우, 호스트 그룹을 지정합니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

확장 집합을 배포할 호스트를 수동으로 선택하려면 `--host` 및 해당 호스트 이름을 추가합니다.



## <a name="add-an-existing-vm"></a>기존 VM 추가

전용 호스트에 기존 VM을 추가할 수 있지만 VM이 먼저 Stop\Deallocated에 있어야 합니다. VM을 전용 호스트로 이동하기 전에 VM 구성이 지원되는지 확인합니다.

- VM 크기는 전용 호스트와 동일한 크기의 제품군에 있어야 합니다. 예를 들어 전용 호스트가 DSv3인 경우 VM 크기는 Standard_D4s_v3일 수 있지만 Standard_A4_v2일 수는 없습니다.
- VM은 전용 호스트와 동일한 지역에 있어야 합니다.
- VM은 근접 배치 그룹에 있을 수 없습니다. 전용 호스트로 이동하기 전에 근접 배치 그룹에서 VM을 제거합니다. 자세한 내용은 [근접 배치 그룹에서 VM 이동](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)을 참조하세요.
- VM은 가용성 집합에 있을 수 없습니다.
- VM이 가용성 영역에 있는 경우 호스트 그룹과 동일한 가용성 영역이어야 합니다. VM 및 호스트 그룹의 가용성 영역 설정이 일치해야 합니다.

변수 값을 사용자의 정보로 바꿉니다.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>정리

가상 머신이 배포되지 않은 경우에도 전용 호스트에 대한 요금이 청구됩니다. 비용 절약을 위해 현재 사용하지 않는 호스트를 모두 삭제해야 합니다.

호스트를 사용하는 가상 머신이 더 이상 없는 경우에만 호스트를 삭제할 수 있습니다. [Remove-AzVM](/powershell/module/az.compute/remove-azvm)을 사용하여 VM을 삭제합니다.

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

VM을 삭제한 후 [Remove-AzHost](/powershell/module/az.compute/remove-azhost)를 사용하여 호스트를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

모든 호스트를 삭제한 후에는 [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)을 사용하여 호스트 그룹을 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 그러면 모든 VM, 호스트 및 호스트 그룹을 포함하여 그룹에 생성된 모든 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>다음 단계

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 최대화하기 위해 영역과 오류 도메인을 모두 사용합니다.

- [Azure Portal](../dedicated-hosts-portal.md)을 사용하여 전용 호스트를 배포할 수도 있습니다.
