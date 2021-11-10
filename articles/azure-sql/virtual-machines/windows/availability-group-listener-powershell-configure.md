---
title: 가용성 그룹 수신기 및 부하 분산 장치 구성(PowerShell)
description: 하나 이상의 IP 주소를 갖는 내부 부하 분산 장치를 사용하여 Azure Resource Manager 모델에서 가용성 그룹 수신기를 구성합니다.
services: virtual-machines
documentationcenter: na
author: rajeshsetlem
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.reviewer: mathoma
ms.openlocfilehash: a443d65b6e96a96df164a828ac4783966297ab8b
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156296"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners"></a>하나 이상의 Always On 가용성 그룹 수신기 구성

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내의 [여러 서브넷에](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) SQL Server VM을 만들어 AG(Always On 가용성) 그룹에 대한 Azure Load Balancer 필요하지 않습니다.

이 문서에서는 PowerShell을 사용하여 다음 작업 중 하나를 수행하는 방법을 보여 줍니다.
- 부하 분산 장치 만들기
- SQL Server 가용성 그룹의 기존 부하 분산 장치에 IP 주소 추가하기

가용성 그룹 수신기는 데이터베이스 액세스를 위해 클라이언트에서 연결하는 가상 네트워크 이름입니다. 단일 서브넷의 Azure Virtual Machines 부하 분산기는 수신기의 IP 주소를 보유합니다. 부하 분산 장치는 프로브 포트에서 수신 대기하는 SQL Server의 인스턴스로 트래픽을 라우팅합니다. 일반적으로 가용성 그룹은 내부 부하 분산 장치를 사용합니다. Azure 내부 부하 분산 장치는 하나 이상의 IP 주소를 호스트할 수 있습니다. 각 IP 주소는 특정 프로브 포트를 사용합니다. 

내부 부하 분산 장치에 여러 IP 주소를 할당하는 기능은 Azure에 새로 추가되었으며 Resource Manager 모델에서만 사용할 수 있습니다. 이 작업을 완료하려면 Resource Manager 모델의 Azure Virtual Machines에 SQL Server 가용성 그룹이 배포되어야 합니다. 두 SQL Server 가상 머신은 동일한 가용성 집합에 속해야 합니다. [Microsoft 템플릿](./availability-group-quickstart-template-configure.md)을 사용하여 Azure Resource Manager에서 가용성 그룹을 자동으로 만들 수 있습니다. 이 템플릿은 내부 부하 분산 장치를 포함하는 가용성 그룹을 자동으로 만듭니다. 원하는 경우 [수동으로 Always On 가용성 그룹을 구성](availability-group-manually-configure-tutorial-single-subnet.md)할 수 있습니다.

이 문서의 단계를 완료하려면 가용성 그룹이 이미 구성되어 있어야 합니다.  

관련 항목은 다음과 같습니다.

* [Azure VM의 Always On 가용성 그룹 구성(GUI)](availability-group-manually-configure-tutorial-single-subnet.md)   
* [Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>PowerShell 버전 확인

이 문서의 예제는 Azure PowerShell 모듈 버전 5.4.1에서 테스트되었습니다.

PowerShell 모듈이 5.4.1 이상인지 확인합니다.

[Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

## <a name="configure-the-windows-firewall"></a>Windows 방화벽 구성

SQL Server 액세스를 허용하도록 Windows 방화벽을 구성합니다. 방화벽 규칙에서 SQL Server 인스턴스 및 수신기 프로브에서 사용하는 포트에 TCP 연결을 허용합니다. 자세한 지침은 [데이터베이스 엔진 액세스를 위해 Windows 방화벽 구성](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1)을 참조하세요. SQL Server 포트 및 프로브 포트에 대한 인바운드 규칙을 만듭니다.

Azure 네트워크 보안 그룹을 사용하여 액세스를 제한하는 경우 허용 규칙에 백 엔드 SQL Server VM IP 주소, AG 수신기에 대한 Load Balancer 부동 IP 주소 및 클러스터 코어 IP 주소가 포함되는지 확인합니다.

## <a name="determine-the-load-balancer-sku-required"></a>필요한 부하 분산 장치 SKU 확인

[Azure 부하 분산 장치](../../../load-balancer/load-balancer-overview.md)는 기본 및 표준, 이 두 가지 SKU에서 사용할 수 있습니다. 표준 부하 분산 장치를 사용하는 것이 좋습니다. 가상 머신이 가용성 세트에 있는 경우 기본 부하 분산 장치가 허용됩니다. 가상 머신이 가용성 영역에 있는 경우 표준 부하 분산 장치가 필요합니다. 표준 부하 분산 장치에는 표준 IP 주소를 사용하는 모든 VM IP 주소가 필요합니다.

현재 가용성 그룹을 위한 [Microsoft 템플릿](./availability-group-quickstart-template-configure.md)은 기본 IP 주소를 사용하는 기본 부하 분산 장치를 사용합니다.

   > [!NOTE]
   > 클라우드 감시에 표준 부하 분산 장치 및 Azure Storage를 사용하는 경우 [서비스 엔드포인트](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)를 구성해야 합니다. 
   > 

이 문서의 예제에서는 표준 부하 분산 장치를 지정합니다. 예제에서 스크립트는 `-sku Standard`를 포함합니다.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

기본 부하 분산 장치를 만들려면 부하 분산 장치를 만드는 줄에서 `-sku Standard`를 제거합니다. 예를 들면 다음과 같습니다.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>예제 스크립트: PowerShell을 사용하여 내부 부하 분산 장치 만들기

> [!NOTE]
> [Microsoft 템플릿](./availability-group-quickstart-template-configure.md)으로 가용성 그룹을 만든 경우 내부 부하 분산 장치는 이미 만들어져 있습니다.

다음 PowerShell 스크립트는 내부 부하 분산 장치를 만들고, 부하 분산 규칙을 구성하고, 부하 분산 장치의 IP 주소를 설정합니다. 이 스크립트를 실행하려면 Windows PowerShell ISE를 연 다음, 스크립트 창으로 스크립트를 붙여 넣습니다. `Connect-AzAccount`를 사용하여 PowerShell에 로그인합니다. 여러 Azure 구독이 있는 경우 `Select-AzSubscription` 사용하여 구독을 설정합니다. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> 예제 스크립트: PowerShell을 사용하여 기존 부하 분산 장치에 IP 주소 추가

둘 이상의 가용성 그룹을 사용하려면 추가 IP 주소를 부하 분산 장치에 추가합니다. 각 IP 주소에는 자체 부하 분산 규칙, 프로브 포트 및 프런트 엔드 포트가 필요합니다. 보조 VM IP 주소가 부동 IP 를 지원하지 않으므로 VM의 기본 [IP 주소만](../../../load-balancer/load-balancer-floating-ip.md)부하 분산기의 백 엔드 풀에 추가합니다.

프런트 엔드 포트는 애플리케이션이 SQL Server 인스턴스에 연결하는 데 사용하는 포트입니다. 다른 가용성 그룹에 대한 IP 주소는 동일한 프런트 엔드 포트를 사용할 수 있습니다.

> [!NOTE]
> SQL Server 가용성 그룹의 경우 각 IP 주소에 특정 프로브 포트가 필요합니다. 예를 들어 부하 분산 장치에 있는 하나의 IP 주소가 프로브 포트 59999를 사용하는 경우 해당 부하 분산 장치의 다른 IP 주소는 프로브 포트 59999를 사용할 수 없습니다.

* 부하 분산 장치 제한에 대한 자세한 내용은 [네트워킹 제한 - Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)에서 **부하 분산 장치당 프라이빗 프런트 엔드 ID** 를 참조하세요.
* 가용성 그룹 제한에 대한 자세한 내용은 [제한 사항(가용성 그룹)](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG)을 참조하세요.

다음 스크립트는 기존 부하 분산 장치에 새 IP 주소를 추가합니다. ILB는 부하 분산 프런트 엔드 포트에 수신기 포트를 사용합니다. 이 포트는 SQL Server에서 수신 대기 중인 포트일 수 있습니다. SQL Server의 기본 인스턴스의 경우 포트는 1433입니다. 가용성 그룹에 대한 부하 분산 규칙에는 부동 IP(Direct Server Return)가 필요하므로 백 엔드 포트는 프런트 엔드 포트와 동일합니다. 사용자 환경에 맞게 변수를 업데이트합니다. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>수신기 구성

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>SQL Server Management Studio에서 수신기 포트 설정

1. SQL Server Management Studio를 시작하고 주 복제본에 연결합니다.

1. **AlwaysOn 고가용성** > **가용성 그룹** > **가용성 그룹 수신기** 로 이동합니다. 

1. 이제 장애 조치(Failover) 클러스터 관리자에서 만든 수신기 이름이 표시됩니다. 마우스 오른쪽 단추로 수신기 이름을 클릭하고, **속성** 을 선택합니다.

1. **포트** 상자에서 이전에 사용한 $EndpointPort를 사용하여 가용성 그룹 수신기의 포트 번호를 지정한 다음(기본값: 1433), **확인** 을 선택합니다.

## <a name="test-the-connection-to-the-listener"></a>수신기에 대한 연결 테스트

연결을 테스트하려면

1. RDP(원격 데스크톱 프로토콜)를 사용하여 동일한 가상 네트워크에 있지만 복제본을 소유하지 않는 SQL Server에 연결합니다. 클러스터의 다른 SQL Server가 될 수도 있습니다.

1. **sqlcmd** 유틸리티를 사용하여 연결을 테스트합니다. 예를 들어 다음 스크립트는 Windows 인증을 사용하는 수신기를 통해 주 복제본에 대한 **sqlcmd** 연결을 설정합니다.
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    수신기가 기본 포트(1433) 이외의 포트를 사용하는 경우 연결 문자열에서 포트를 지정합니다. 예를 들어 다음 sqlcmd 명령은 포트 1435에서 수신기에 연결합니다. 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD 연결은 주 복제본을 호스트하는 SQL Server 인스턴스에 자동으로 연결합니다. 

> [!NOTE]
> 지정한 포트가 두 SQL Server의 방화벽에서 열려 있는지 확인합니다. 두 서버 모두 사용하는 TCP 포트에 대한 인바운드 규칙이 필요합니다. 자세한 내용은 [방화벽 규칙 추가 또는 편집](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11))을 참조하세요. 
> 

## <a name="guidelines-and-limitations"></a>지침 및 제한 사항

내부 부하 분산 장치를 사용하는 Azure에서는 가용성 그룹 수신기에 다음과 같은 지침이 적용됩니다.

* 내부 부하 분산 장치를 사용할 경우 동일한 가상 네트워크 내에서만 수신기에 액세스합니다.

* Azure 네트워크 보안 그룹을 사용하여 액세스를 제한하는 경우 허용 규칙은 다음을 포함해야 합니다.
  - 백 엔드 SQL Server VM IP 주소
  - AG 수신기의 부하 분산 장치 부동 IP 주소
  - 클러스터 코어 IP 주소(해당하는 경우)

* 클라우드 감시에 Azure Storage와 함께 표준 부하 분산 장치를 사용하는 경우 서비스 엔드포인트를 만듭니다. 자세한 내용은 [가상 네트워크에서 액세스 권한 얻기](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)를 참조하세요.

## <a name="powershell-cmdlets"></a>PowerShell cmdlet

다음 PowerShell cmdlet을 사용하여 Azure Virtual Machines에 대한 내부 부하 분산 장치를 만듭니다.

* [New-AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer)는 부하 분산 장치를 만듭니다. 
* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig)는 부하 분산 장치에 대한 프런트 엔드 IP 구성을 만듭니다. 
* [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig)는 부하 분산 장치에 대한 규칙 구성을 만듭니다. 
* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig)는 부하 분산 장치에 대한 백 엔드 주소 풀 구성을 만듭니다. 
* [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig)는 부하 분산 장치에 대한 프로브 구성을 만듭니다.
* [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer)는 Azure 리소스 그룹에서 부하 분산 장치를 제거합니다.

## <a name="next-steps"></a>다음 단계 


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)