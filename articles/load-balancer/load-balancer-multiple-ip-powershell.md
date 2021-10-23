---
title: 여러 IP 구성의 부하 분산 - Azure CLI
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure CLI를 사용하여 기본 IP 구성과 보조 IP 구성에 걸쳐 부하를 분산하는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 154c22734bb39f5cb27232b2aaa0e4c5677a9892
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246646"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>PowerShell을 사용하여 여러 IP 구성의 부하 분산

> [!div class="op_single_selector"]
> * [포털](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

이 문서에서는 보조 NIC(네트워크 인터페이스)에 여러 IP 주소가 있는 Azure Load Balancer를 사용하는 방법을 설명합니다. 이 시나리오에는 Windows를 실행하는 VM이 둘 있고 각각 기본 및 보조 NIC가 있습니다. 각 보조 NIC에는 두 가지 IP 구성이 있습니다. 각 VM은 contoso.com 및 fabrikam.com 웹 사이트를 둘 다 호스트합니다. 각 웹 사이트는 보조 NIC의 IP 구성 중 하나에 바인딩됩니다. Azure Load Balancer를 사용하여 웹 사이트의 각 IP 구성에 트래픽을 분산하기 위해 각 웹 사이트에 하나씩 두 개의 프런트 엔드 IP 주소를 노출합니다. 이 시나리오는 양 쪽 프런트 엔드는 물론 양쪽 백 엔드 풀 IP 주소에 동일한 포트 번호를 사용합니다.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>여러 IP 구성의 부하를 분산하는 단계

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

아래 단계에 따라 이 문서에 설명된 시나리오를 수행합니다.

1. Azure PowerShell을 설치합니다. 최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.
2. 다음 설정을 사용하여 리소스 그룹을 만듭니다.

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    자세한 내용은 [리소스 그룹 만들기](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json)의 2단계를 참조하세요.

3. VM을 포함할 [가용성 집합을 만듭니다](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json). 이 시나리오에서는 다음 명령을 사용합니다.

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. NIC가 하나인 VM 만들기를 준비하려면 [Windows VM 만들기](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json)에 있는 3 ~ 5단계의 지침을 수행합니다. 6.1단계를 실행하고 6.2단계 대신 다음을 사용합니다.

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    그런 다음 [Windows VM 만들기](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json)의 6.3 ~ 6.8단계를 완료합니다.

5. 각각의 VM에 두 번째 IP 구성을 추가합니다. [가상 머신에 여러 IP 주소 할당](../virtual-network/ip-services/virtual-network-multiple-ip-addresses-powershell.md#add) 문서의 지침을 따릅니다. 다음 구성 설정을 사용합니다.

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    이 자습서의 경우 보조 IP 구성을 공용 IP와 연결할 필요가 없습니다. 명령을 편집하여 공용 IP 연결 부분을 제거합니다.

6. VM2에 대해 4 ~ 6단계를 다시 완료합니다. 이 단계를 수행할 때 VM 이름을 VM2로 바꿔야 합니다. 두 번째 VM에 대해 가상 네트워크를 만들 필요가 없습니다. 사용 사례를 기반으로 새 서브넷을 만들거나 만들지 않을 수 있습니다.

7. 두 개의 공용 IP 주소를 만들고 다음과 같이 적절한 변수에 저장합니다.

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. 두 개의 프런트 엔드 IP 구성을 만듭니다.

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. 백 엔드 주소 풀, 프로브, 부하 분산 규칙을 만듭니다.

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. 이러한 리소스를 만들었으면 부하 분산 장치를 만듭니다.

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. 두 번째 백 엔드 주소 풀 및 프런트 엔드 IP 구성을 새로 만든 부하 분산 장치에 추가합니다.

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. 아래 명령은 NIC를 가져온 다음 보조 NIC 각각의 IP 구성 두 개를 부하 분산 장치의 백 엔드 주소 풀에 추가합니다.

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. 마지막으로 DNS 리소스 레코드가 부하 분산 장치의 각 프런트 엔드 IP 주소를 가리키도록 구성해야 합니다. 도메인을 Azure DNS에 호스트할 수 있습니다. Load Balancer와 Azure DNS를 사용하는 방법에 대한 자세한 내용은 [다른 Azure 서비스와 함께 Azure DNS 사용](../dns/dns-for-azure-services.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- Azure에서 부하 분산 서비스를 결합하는 방법에 대한 자세한 내용은 [Azure에서 부하 분산 서비스 사용](../traffic-manager/traffic-manager-load-balancing-azure.md)을 참조하세요.
- [Azure Load Balancer에 대한 Azure Monitor 로그](./monitor-load-balancer.md)를 통해 Azure에서 부하 분산 장치를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용하는 방법에 대해 알아보세요.