---
title: PowerShell을 사용하여 Azure Spot Virtual Machines 배포
description: Azure PowerShell을 통해 비용을 Azure Spot Virtual Machines를 배포하여 비용을 절감하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee1f915045ed3272d8f93559efcf06c0c2bcc011
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689318"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Spot Virtual Machines 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

[Azure Spot Virtual Machines](../spot-vms.md)를 사용하면 사용되지 않는 용량을 활용하여 비용을 크게 절감할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 인프라에서 Azure 스폿 가상 머신을 제거합니다. 따라서 Azure Spot Virtual Machines는 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같이 중단을 처리할 수 있는 워크로드에 매우 적합합니다.

Azure Spot Virtual Machines의 가격 책정은 지역과 SKU에 따라 다릅니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대한 VM 가격 책정을 참조하세요. 최고 가격을 설정하는 방법에 대한 자세한 내용은 [Azure Spot Virtual Machines - 가격 책정](../spot-vms.md#pricing)을 참조하세요.

VM에 대해 시간당 지불할 최고 가격을 설정하는 옵션이 있습니다. Azure Spot Virtual Machine 한 대당 최고 가격을 미국 달러(USD)로 최대 소수 자릿수 5자리까지 설정할 수 있습니다. 예를 들어 `0.98765` 값은 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정하는 경우 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과하는 경우 스폿의 현재 가격과 표준 VM의 가격 중에서 더 작은 가격이 됩니다.


## <a name="create-the-vm"></a>VM 만들기

[New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig)를 통해 spotVM을 만들어 구성을 만듭니다. `-Priority Spot`을 포함하고 `-MaxPrice`를 다음 중 하나로 설정합니다.
- 가격에 따라 VM이 제거되지 않도록 하려면 `-1`.
- 최대 5자리 달러 금액입니다. 예를 들어 `-MaxPrice .98765`로 설정하면 spotVM에 대한 가격이 시간당 $0.98765가 되면 VM의 할당이 취소됩니다.


이 예시에서는 가격 책정에 따라 할당이 취소되지 않는 spotVM을 만듭니다(Azure에 용량이 다시 필요한 경우에만). 제거 정책은 나중에 다시 시작할 수 있도록 VM의 할당을 취소하도록 설정됩니다. VM을 제거할 때 VM 및 기본 디스크를 삭제하려면 `New-AzVMConfig`에서 `-EvictionPolicy`를 `Delete`로 설정합니다.


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

VM을 만든 후에 쿼리를 통해 리소스 그룹에 속한 모든 VM에 대한 최고 가격을 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>제거 시뮬레이션

REST, PowerShell 또는 CLI를 사용하여 Azure Spot Virtual Machine 제거를 시뮬레이션하여 애플리케이션이 갑작스러운 제거에 얼마나 잘 대응하는지 테스트할 수 있습니다.

대부분의 경우 REST API [가상 머신 - 제거 시뮬레이션](/rest/api/compute/virtualmachines/simulateeviction)을 사용하면 애플리케이션의 자동화된 테스트에 도움이 됩니다. REST의 경우 `Response Code: 204`가 나타나면 제거 시뮬레이션이 성공적으로 완료되었음을 의미합니다. 제거 시뮬레이션을 [ 서비스](scheduled-events.md)와 결합하면 VM 제거 시 앱의 대응 방식을 자동화할 수 있습니다.

작동 중인 예약된 이벤트를 보려면 [Azure Friday - Azure Scheduled Events를 사용하여 VM 유지 관리에 대비](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)합니다.


### <a name="quick-test"></a>빠른 테스트

제거 시뮬레이션의 사용 방법을 간략히 테스트하기 위해, 예약된 이벤트 서비스를 쿼리하여 PowerShell을 통해 제거를 시뮬레이션하면 어떻게 되는지 확인하는 방법을 살펴보겠습니다.

예약된 이벤트 서비스는 이벤트에 대한 요청을 처음 수행할 때 서비스에 대해 사용 설정됩니다. 

VM에 원격으로 연결한 다음 명령 프롬프트를 엽니다. 

VM의 명령 프롬프트에서 다음을 입력합니다.

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

이 첫 번째 응답은 최대 2분 정도 걸릴 수 있습니다. 지금부터 출력이 거의 즉시 표시되어야 합니다.

Az PowerShell 모듈이 설치된 컴퓨터(예: 로컬 컴퓨터)에서 [Set-AzVM](/powershell/module/az.compute/set-azvm)을 사용하여 제거를 시뮬레이션합니다. 리소스 그룹 이름 및 VM 이름을 고유의 이름으로 바꿉니다. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

요청이 성공적으로 수행되면 응답 출력에 `Status: Succeeded`가 나타납니다.

신속하게 Spot Virtual MachineS에 대한 원격 연결로 돌아가서 Scheduled Events 엔드포인트를 다시 쿼리합니다. 추가 정보를 포함하는 출력이 반환될 때까지 다음 명령을 반복합니다.

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

예약된 이벤트 서비스에 제거 알림이 수신되면 다음과 유사한 응답이 반환됩니다.

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

`"EventType":"Preempt"`가 있으며 리소스는 VM 리소스 `"Resources":["myspotvm"]`입니다. 

`"NotBefore"` 값을 확인하여 VM이 제거되는 시점도 확인할 수 있습니다. VM은 `NotBefore`에 지정된 시간 전에는 제거되지 않으므로 이 값이 애플리케이션이 정상적으로 종료될 수 있는 기간입니다.


## <a name="next-steps"></a>다음 단계

[Azure CLI](../linux/spot-cli.md), [포털](../spot-portal.md) 또는 [템플릿](../linux/spot-template.md)을 사용하여 Azure 스폿 가상 머신을 만들 수도 있습니다.

Azure 스폿 가상 머신 가격 책정에 대한 자세한 내용은 [Azure 소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices)를 사용하여 현재 가격 책정 정보를 쿼리하세요. `meterName`과 `skuName`에는 둘 다 `Spot`이 포함됩니다.

오류가 발생하는 경우 [오류 코드](../error-codes-spot.md)를 참조하세요.
