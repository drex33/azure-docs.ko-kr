---
title: '자습서: 장애 조치(failover) 그룹에 SQL Managed Instance 추가'
titleSuffix: Azure SQL Managed Instance
description: 이 자습서에서는 기본 및 보조 Azure SQL Managed Instance 간의 장애 조치(failover) 그룹을 만드는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 08/27/2019
ms.openlocfilehash: 5552faa01df1fd21af75c638549770c737920d9d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031445"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>자습서: 장애 조치(failover) 그룹에 SQL Managed Instance 추가
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance의 관리되는 인스턴스를 장애 조치(failover) 그룹에 추가합니다. 이 아티클에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> - 기본 관리형 인스턴스를 만듭니다.
> - [장애 조치(failover) 그룹](../database/auto-failover-group-overview.md)의 일부로 보조 관리형 인스턴스를 만듭니다. 
> - 장애 조치(failover)를 테스트합니다.

  > [!NOTE]
  > - 이 자습서를 진행하는 경우 [SQL Managed Instance에 대한 장애 조치(failover) 그룹을 설정하기 위한 필수 구성 요소](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)로 리소스를 구성해야 합니다. 
  > - 관리되는 인스턴스를 만드는 데 상당한 시간이 걸릴 수 있습니다. 따라서 이 자습서를 완료하는 데 몇 시간 정도 걸릴 수 있습니다. 프로비저닝 시간에 대한 자세한 내용은 [SQL Managed Instance 관리 작업](sql-managed-instance-paas-overview.md#management-operations)을 참조하세요. 
  > - 장애 조치(failover) 그룹에 참여하는 관리되는 인스턴스에는 [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md), 글로벌 VNet 피어링 또는 두 개의 연결된 VPN 게이트웨이가 필요합니다. 이 자습서에서는 VPN 게이트웨이를 만들고 연결하는 단계를 제공합니다. ExpressRoute를 이미 구성한 경우 이 단계를 건너뛰세요. 


## <a name="prerequisites"></a>필수 구성 요소

# <a name="portal"></a>[포털](#tab/azure-portal)
이 자습서를 완료하려면 다음이 설치되어 있어야 합니다. 

- Azure 구독 아직 체험 계정이 없는 경우 [새로 만듭니다](https://azure.microsoft.com/free/).


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
이 자습서를 완료하려면 다음 항목이 필요합니다.

- Azure 구독 아직 체험 계정이 없는 경우 [새로 만듭니다](https://azure.microsoft.com/free/).
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>리소스 그룹 및 기본 관리형 인스턴스 만들기

이 단계에서는 Azure Portal 또는 PowerShell을 사용하여 장애 조치(failover) 그룹에 대한 기본 관리형 인스턴스 및 리소스 그룹을 만듭니다. 

성능 상의 이유로 두 관리형 인스턴스를 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 배포합니다. 지리적으로 쌍을 이루는 지역에 상주하는 관리되는 인스턴스는 쌍을 이루지 않는 지역에 비해 훨씬 더 나은 성능을 제공합니다. 


# <a name="portal"></a>[포털](#tab/azure-portal) 

Azure Portal을 사용하여 리소스 그룹 및 기본 관리형 인스턴스를 만듭니다. 

1. Azure Portal의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색 상자에 `Azure SQL`을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. **+ 추가** 를 선택하여 **SQL 배포 옵션 선택** 페이지를 엽니다. **데이터베이스** 타일에서 **세부 정보 표시** 를 선택하여 다른 데이터베이스에 대한 추가 정보를 볼 수 있습니다.
1. **SQL Managed Instances** 타일에서 **만들기** 를 선택합니다. 

    ![SQL Managed Instance 선택](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. **Azure SQL Managed Instance** 만들기 페이지의 **기본** 탭에서 다음을 수행합니다.
    1. **프로젝트 세부 정보** 아래 드롭다운에서 **구독** 을 선택한 후 **새로 만들기** 리소스 그룹을 선택합니다. 리소스 그룹의 이름을 입력합니다(예: `myResourceGroup`). 
    1. **SQL Managed Instance 세부 정보** 아래에 관리되는 인스턴스의 이름과 관리되는 인스턴스를 배포할 지역을 제공합니다. **컴퓨팅 + 스토리지** 는 기본값으로 둡니다. 
    1. **관리자 계정** 아래에 관리자 로그인(예: `azureuser`)과 복잡한 관리자 암호를 제공합니다. 

    ![기본 관리형 인스턴스 만들기](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. 나머지 설정은 기본값으로 두고 **검토 + 만들기** 를 선택하여 SQL Managed Instance 설정을 검토합니다. 
1. **만들기** 를 선택하여 기본 관리형 인스턴스를 만듭니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 리소스 그룹 및 기본 관리형 인스턴스를 만듭니다. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure 리소스 그룹을 만듭니다.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 가상 네트워크를 만듭니다.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷 구성을 추가합니다. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 리소스 그룹의 가상 네트워크를 가져옵니다. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷을 가져옵니다. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 네트워크 보안 그룹을 만듭니다. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 경로 테이블을 만듭니다. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 가상 네트워크에 대한 서브넷 구성을 업데이트합니다.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 가상 네트워크를 업데이트합니다.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 네트워크 보안 그룹을 가져옵니다. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 네트워크 보안 그룹에 네트워크 보안 규칙 구성을 추가합니다. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 네트워크 보안 그룹을 업데이트합니다.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 경로 테이블에 경로를 추가합니다. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 경로 테이블을 업데이트합니다.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 관리형 인스턴스를 만듭니다.  |

---

## <a name="create-secondary-virtual-network"></a>보조 가상 네트워크 만들기

Azure Portal을 사용하여 관리되는 인스턴스를 만드는 경우에는 기본 및 보조 관리형 인스턴스의 서브넷에 겹치는 범위가 없어야 하기 때문에 가상 네트워크를 별도로 만들어야 합니다. PowerShell을 사용하여 관리되는 인스턴스를 구성하는 경우에는 3단계로 건너뛰세요. 

# <a name="portal"></a>[포털](#tab/azure-portal) 

기본 가상 네트워크의 서브넷 범위를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하고 기본 인스턴스의 가상 네트워크를 선택합니다.  
2. **설정** 에서 **서브넷** 을 선택하고 **주소 범위** 를 확인합니다. 보조 관리형 인스턴스에 대한 가상 네트워크의 서브넷 주소 범위는 이 범위와 겹칠 수 없습니다. 


   ![기본 서브넷](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

가상 네트워크를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택하고 *가상 네트워크* 를 검색합니다. 
1. Microsoft에서 게시한 **Virtual Network** 옵션을 선택한 후 다음 페이지에서 **만들기** 를 선택합니다. 
1. 보조 관리형 인스턴스에 대한 가상 네트워크를 구성하는 데 필요한 필드를 입력한 다음, **만들기** 를 선택합니다. 

   다음 표에서는 보조 가상 네트워크에 필요한 값을 보여줍니다.

    | **필드** | 값 |
    | --- | --- |
    | **이름** |  보조 관리형 인스턴스에서 사용할 가상 네트워크의 이름입니다(예: `vnet-sql-mi-secondary`). |
    | **주소 공간** | 가상 네트워크의 주소 공간입니다(예: `10.128.0.0/16`). | 
    | **구독** | 기본 관리형 인스턴스 및 리소스 그룹이 상주하는 구독입니다. |
    | **지역** | 보조 관리형 인스턴스를 배포할 위치입니다. |
    | **서브넷** | 서브넷의 이름입니다. `default`가 기본적으로 제공됩니다. |
    | **주소 범위**| 서브넷의 주소 범위입니다. 기본 관리형 인스턴스의 가상 네트워크에서 사용하는 서브넷 주소 범위와 달라야 합니다(예: `10.128.0.0/24`).  |
    | &nbsp; | &nbsp; |

    ![보조 가상 네트워크 값](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 단계는 Azure Portal을 사용하여 SQL Managed Instance를 배포하는 경우에만 필요합니다. PowerShell을 사용하는 경우 3단계로 건너뛰세요. 

---

## <a name="create-a-secondary-managed-instance"></a>보조 관리형 인스턴스 만들기
이 단계에서는 Azure Portal에서 보조 관리형 인스턴스를 만들고, 두 관리형 인스턴스 간의 네트워킹도 구성합니다. 

두 번째 관리되는 인스턴스는 다음을 충족해야 합니다.
- 비어 있어야 합니다. 
- 서브넷 및 IP 범위가 기본 관리형 인스턴스와 달라야 합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal) 

Azure Portal을 사용하여 보조 관리형 인스턴스를 만듭니다. 

1. Azure Portal의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색 상자에 `Azure SQL`을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. **+ 추가** 를 선택하여 **SQL 배포 옵션 선택** 페이지를 엽니다. **데이터베이스** 타일에서 **세부 정보 표시** 를 선택하여 다른 데이터베이스에 대한 추가 정보를 볼 수 있습니다.
1. **SQL Managed Instances** 타일에서 **만들기** 를 선택합니다. 

    ![SQL Managed Instance 선택](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. **Azure SQL Managed Instance 만들기** 페이지의 **기본** 탭에서 보조 관리형 인스턴스를 구성하는 데 필요한 필드를 채웁니다. 

   다음 표에서는 보조 관리형 인스턴스에 필요한 값을 보여줍니다.
 
    | **필드** | 값 |
    | --- | --- |
    | **구독** |  기본 관리형 인스턴스가 있는 구독입니다. |
    | **리소스 그룹**| 기본 관리형 인스턴스가 있는 리소스 그룹입니다. |
    | **SQL Managed Instance 이름** | 새 보조 관리형 인스턴스의 이름입니다(예: `sql-mi-secondary`).  | 
    | **지역**| 보조 관리형 인스턴스의 위치입니다.  |
    | **SQL Managed Instance 관리자 로그인** | 새 보조 관리형 인스턴스에 사용하려는 로그인입니다(예: `azureuser`). |
    | **암호** | 새 보조 관리형 인스턴스의 관리자 로그인에 사용할 복잡한 암호입니다.  |
    | &nbsp; | &nbsp; |

1. **네트워킹** 탭의 **Virtual Network** 드롭다운에서 보조 관리형 인스턴스에 대해 만든 가상 네트워크를 선택합니다.

   ![보조 MI 네트워킹](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. **추가 설정** 탭에 있는 **지역에서 복제** 의 보조 장애 조치(failover)로 사용에 대해 **예** 를 선택합니다. 드롭다운에서 기본 관리형 인스턴스를 선택합니다. 
    
   데이터 정렬 및 표준 시간대가 기본 관리형 인스턴스와 일치해야 합니다. 이 자습서에서 만든 기본 관리형 인스턴스에는 기본값으로 `SQL_Latin1_General_CP1_CI_AS` 데이터 정렬 및 `(UTC) Coordinated Universal Time` 표준 시간대를 사용했습니다. 

   ![보조 관리형 인스턴스 네트워킹](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. **검토 + 만들기** 를 선택하여 보조 관리형 인스턴스에 대한 설정을 검토합니다. 
1. **만들기** 를 선택하여 보조 관리형 인스턴스를 만듭니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 보조 관리형 인스턴스를 만듭니다. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure 리소스 그룹을 만듭니다.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 가상 네트워크를 만듭니다.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷 구성을 추가합니다. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 리소스 그룹의 가상 네트워크를 가져옵니다. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷을 가져옵니다. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 네트워크 보안 그룹을 만듭니다. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 경로 테이블을 만듭니다. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 가상 네트워크에 대한 서브넷 구성을 업데이트합니다.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 가상 네트워크를 업데이트합니다.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 네트워크 보안 그룹을 가져옵니다. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 네트워크 보안 그룹에 네트워크 보안 규칙 구성을 추가합니다. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 네트워크 보안 그룹을 업데이트합니다.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 경로 테이블에 경로를 추가합니다. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 경로 테이블을 업데이트합니다.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 관리형 인스턴스를 만듭니다.  |

---

## <a name="create-a-primary-gateway"></a>기본 게이트웨이 만들기 

두 관리형 인스턴스가 장애 조치(failover) 그룹에 참여하려면, 네트워크 통신을 허용하기 위해 두 관리형 인스턴스의 가상 네트워크 간에 ExpressRoute 또는 게이트웨이가 구성되어 있어야 합니다. 두 개의 VPN 게이트웨이를 연결하는 대신 [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)를 구성하도록 선택한 경우 [7단계](#create-a-failover-group)로 건너뜁니다.  

이 문서에서는 두 VPN 게이트웨이를 만들고 연결하는 단계를 제공하지만 대신 ExpressRoute를 구성한 경우 장애 조치(failover) 그룹 만들기로 건너뛸 수 있습니다. 

> [!NOTE]
> 게이트웨이의 SKU는 처리량 성능에 영향을 줍니다. 이 자습서에서는 가장 기본적인 SKU(`HwGw1`)를 사용하여 게이트웨이를 배포합니다. 더 높은 SKU(예 : `VpnGw3`)를 배포하면 더 높은 처리량을 달성할 수 있습니다. 사용 가능한 모든 옵션은 [게이트웨이 SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark)를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 기본 관리형 인스턴스의 가상 네트워크에 대한 게이트웨이를 만듭니다. 


1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동하여 기본 관리형 인스턴스에 대한 **가상 네트워크** 리소스를 선택합니다. 
1. **설정** 에서 **서브넷** 을 선택한 후 새 **게이트웨이 서브넷** 을 추가하도록 선택합니다. 기본값을 그대로 둡니다. 

   ![기본 관리형 인스턴스에 대한 게이트웨이 추가](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. 서브넷 게이트웨이를 만든 후 왼쪽 탐색 창에서 **리소스 만들기** 를 선택한 후 검색 상자에 `Virtual network gateway`를 입력합니다. **Microsoft** 에서 게시한 **가상 네트워크 게이트웨이** 리소스를 선택합니다. 

   ![새 가상 네트워크 게이트웨이 만들기](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. 기본 관리형 인스턴스에 대한 게이트웨이를 구성하는 데 필요한 필드를 입력합니다. 

   다음 표는 기본 관리형 인스턴스의 게이트웨이에 필요한 값을 보여줍니다.
 
    | **필드** | 값 |
    | --- | --- |
    | **구독** |  기본 관리형 인스턴스가 있는 구독입니다. |
    | **이름** | 가상 네트워크 게이트웨이의 이름입니다(예: `primary-mi-gateway`). | 
    | **지역** | 기본 관리형 인스턴스가 있는 지역입니다. |
    | **게이트웨이 유형** | **VPN** 을 선택합니다. |
    | **VPN 유형** | **경로 기반** 을 선택합니다. |
    | **SKU**| `VpnGw1` 기본값을 그대로 둡니다. |
    | **가상 네트워크**| 섹션 2에서 만든 가상 네트워크를 선택합니다(예: `vnet-sql-mi-primary`). |
    | **공용 IP 주소**| **새로 만들기** 를 선택합니다. |
    | **공용 IP 주소 이름**| IP 주소의 이름을 입력합니다(예: `primary-gateway-IP`). |
    | &nbsp; | &nbsp; |

1. 다른 값은 기본값으로 그대로 두고 **검토 + 만들기** 를 선택하여 가상 네트워크 게이트웨이에 대한 설정을 검토합니다.

   ![기본 게이트웨이 설정](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. **만들기** 를 선택하여 새 가상 네트워크 게이트웨이를 만듭니다. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 기본 관리형 인스턴스의 가상 네트워크에 대한 게이트웨이를 만듭니다. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 리소스 그룹의 가상 네트워크를 가져옵니다. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷 구성을 추가합니다. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 가상 네트워크를 업데이트합니다.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷을 가져옵니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 가상 네트워크 게이트웨이에 대한 IP 구성을 만듭니다. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 가상 네트워크 게이트웨이를 만듭니다. |


---


## <a name="create-secondary-gateway"></a>보조 게이트웨이 만들기 
이 단계에서는 Azure Portal을 사용하여 보조 관리형 인스턴스의 가상 네트워크에 대한 게이트웨이를 만듭니다. 


# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 이전 섹션의 단계를 반복하여 보조 관리형 인스턴스에 대한 가상 네트워크 서브넷과 게이트웨이를 만듭니다. 보조 관리형 인스턴스에 대한 게이트웨이를 구성하는 데 필요한 필드를 입력합니다. 

   다음 표는 보조 관리형 인스턴스의 게이트웨이에 필요한 값을 보여줍니다.

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  보조 관리형 인스턴스가 있는 구독입니다. |
   | **이름** | 가상 네트워크 게이트웨이의 이름입니다(예: `secondary-mi-gateway`). | 
   | **지역** | 보조 관리형 인스턴스가 있는 지역입니다. |
   | **게이트웨이 유형** | **VPN** 을 선택합니다. |
   | **VPN 유형** | **경로 기반** 을 선택합니다. |
   | **SKU**| `VpnGw1` 기본값을 그대로 둡니다. |
   | **가상 네트워크**| 보조 관리형 인스턴스의 가상 네트워크를 선택합니다(예: `vnet-sql-mi-secondary`). |
   | **공용 IP 주소**| **새로 만들기** 를 선택합니다. |
   | **공용 IP 주소 이름**| IP 주소의 이름을 입력합니다(예: `secondary-gateway-IP`). |
   | &nbsp; | &nbsp; |

   ![보조 게이트웨이 설정](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 보조 관리형 인스턴스의 가상 네트워크에 대한 게이트웨이를 만듭니다. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 리소스 그룹의 가상 네트워크를 가져옵니다. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷 구성을 추가합니다. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 가상 네트워크를 업데이트합니다.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷을 가져옵니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 가상 네트워크 게이트웨이에 대한 IP 구성을 만듭니다. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 가상 네트워크 게이트웨이를 만듭니다. |

---


## <a name="connect-the-gateways"></a>게이트웨이 연결
이 단계에서는 두 가상 네트워크의 두 게이트웨이 간에 양방향 연결을 만듭니다. 


# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 두 게이트웨이를 연결합니다. 


1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.
1. 검색 상자에 `connection`을 입력한 다음, Enter 키를 눌러 검색하면 Microsoft에서 게시한 **연결** 리소스로 이동합니다.
1. **만들기** 를 선택하여 연결을 만듭니다. 
1. **기본** 페이지에서 다음 값을 선택한 다음, **확인** 을 선택합니다. 
    1. **연결 형식** 에 대해 `VNet-to-VNet`을 선택합니다. 
    1. 드롭다운에서 구독을 선택합니다. 
    1. 드롭다운에서 SQL Managed Instance에 대한 리소스 그룹을 선택합니다. 
    1. 드롭다운에서 기본 관리형 인스턴스의 위치를 선택합니다. 
1. **설정** 페이지에서 다음 값을 선택하거나 입력한 다음, **확인** 을 선택합니다.
    1. **첫 번째 가상 네트워크 게이트웨이** 에 대한 기본 네트워크 게이트웨이를 선택합니다(예: `primaryGateway`).  
    1. **두 번째 가상 네트워크 게이트웨이** 에 대한 보조 네트워크 게이트웨이를 선택합니다(예: `secondaryGateway`). 
    1. **양방향 연결 설정** 옆에 있는 확인란을 선택합니다. 
    1. 기본 연결 이름의 기본값을 그대로 두거나 원하는 값으로 이름을 바꿉니다. 
    1. 연결에 대해 **공유 키(PSK)** 를 제공합니다(예: `mi1m2psk`). 
    1. **확인** 을 선택하여 설정을 저장합니다. 

    ![게이트웨이 연결 만들기](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. **검토 + 만들기** 페이지에서 양방향 연결에 대한 설정을 검토한 다음, **확인** 을 선택하여 연결을 만듭니다. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 두 게이트웨이를 연결합니다. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 두 가상 네트워크 게이트웨이를 연결합니다.   |

---


## <a name="create-a-failover-group"></a>장애 조치 그룹 만들기
이 단계에서는 장애 조치(failover) 그룹을 만들고 두 관리형 인스턴스를 여기에 추가합니다. 


# <a name="portal"></a>[포털](#tab/azure-portal)
Azure Portal을 사용하여 장애 조치(failover) 그룹을 만듭니다. 


1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택합니다. **Azure SQL** 이 목록에 없는 경우 **모든 서비스** 를 선택한 다음, 검색 상자에 `Azure SQL`을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 첫 번째 섹션에서 만든 기본 관리형 인스턴스를 선택합니다(예: `sql-mi-primary`). 
1. **데이터 관리** 에서 **장애 조치(failover) 그룹** 으로 이동한 다음 **그룹 추가** 를 선택하여 **인스턴스 장애 조치(failover) 그룹** 페이지를 엽니다. 

   ![장애 조치(failover) 그룹 추가](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. **인스턴스 장애 조치(Failover) 그룹** 페이지에서 장애 조치(failover) 그룹의 이름을 입력합니다(예: `failovergrouptutorial`). 그런 다음, 드롭다운에서 보조 관리형 인스턴스를 선택합니다(예: `sql-mi-secondary`). **만들기** 를 선택하여 장애 조치(failover) 그룹을 만듭니다. 

   ![장애 조치 그룹 만들기](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. 장애 조치(failover) 그룹 배포가 완료되면 **장애 조치(failover) 그룹** 페이지로 돌아갑니다. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용하여 장애 조치(failover) 그룹을 만듭니다. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 새 Azure SQL Managed Instance 장애 조치(failover) 그룹을 만듭니다.  |


---


## <a name="test-failover"></a>테스트 장애 조치
이 단계에서는 장애 조치(failover) 그룹을 보조 서버로 장애 조치한 다음, Azure Portal을 사용하여 장애 복구합니다. 


# <a name="portal"></a>[포털](#tab/azure-portal)
Azure Portal을 사용하여 장애 조치(failover) 테스트 


1. [Azure Portal](https://portal.azure.com)에서 _보조_ 관리형 인스턴스로 이동하고 설정 아래에서 **인스턴스 장애 조치(Failover) 그룹** 을 선택합니다. 
1. 어떤 관리되는 인스턴스가 기본이고 어떤 관리되는 인스턴스가 보조인지 검토합니다. 
1. **장애 조치(failover)** 를 선택한 다음, TDS 세션 연결이 끊어진다는 경고에서 **예** 를 선택합니다. 

   ![장애 조치(failover) 그룹에 대한 장애 조치(failover)](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. 어떤 관리되는 인스턴스가 기본이고 어떤 관리되는 인스턴스가 보조인지 검토합니다. 장애 조치(failover)에 성공하면 두 인스턴스의 역할이 전환되어야 합니다. 

   ![장애 조치(failover) 후 관리되는 인스턴스의 역할이 전환됨](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. 새 보조 관리형 인스턴스로 이동하고 **장애 조치(failover)** 를 다시 한 번 선택하여 기본 인스턴스를 기본 역할로 다시 장애 조치(failover)합니다. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용하여 장애 조치(failover) 테스트 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | SQL Managed Instance 장애 조치(failover) 그룹을 가져오거나 나열합니다.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | SQL Managed Instance 장애 조치(failover) 그룹의 장애 조치(failover)를 실행합니다. | 

---



## <a name="clean-up-resources"></a>리소스 정리
먼저 관리되는 인스턴스를 삭제한 다음, 가상 클러스터와 나머지 리소스를 차례로 삭제하고 마지막으로 리소스 그룹을 삭제하여 리소스를 정리합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동합니다. 
1. 관리형 인스턴스를 선택한 다음, **삭제** 를 선택합니다. 텍스트 상자에서 `yes`를 입력하여 리소스를 삭제할지 확인한 다음, **삭제** 를 선택합니다. 이 프로세스는 백그라운드에서 완료하는 데 다소 시간이 걸릴 수 있으며, 완료될 때까지 *가상 클러스터* 또는 기타 종속 리소스를 삭제할 수 없습니다. **활동** 탭에서 삭제를 모니터링하여 관리되는 인스턴스가 삭제되었는지 확인합니다. 
1. 관리되는 인스턴스가 삭제되면 리소스 그룹에서 *가상 클러스터* 를 선택한 다음, **삭제** 를 선택하여 해당 클러스터를 삭제합니다. 텍스트 상자에서 `yes`를 입력하여 리소스를 삭제할지 확인한 다음, **삭제** 를 선택합니다. 
1. 나머지 리소스를 삭제합니다. 텍스트 상자에서 `yes`를 입력하여 리소스를 삭제할지 확인한 다음, **삭제** 를 선택합니다. 
1. **리소스 그룹 삭제** 를 선택하고, 리소스 그룹 이름(`myResourceGroup`)을 입력한 다음, **삭제** 를 선택하여 해당 리소스 그룹을 삭제합니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

리소스 그룹을 두 번 제거해야 합니다. 리소스 그룹을 처음으로 제거하면 관리되는 인스턴스와 가상 클러스터가 제거되지만, `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` 오류 메시지와 함께 실패하게 됩니다. Remove-AzResourceGroup 명령을 한 번 더 실행하여 리소스 그룹뿐 아니라 나머지 리소스를 모두 제거합니다.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

이 자습서의 이 부분에서는 다음 PowerShell cmdlet을 사용합니다.

| 명령 | 메모 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹을 제거합니다. |

---

## <a name="full-script"></a>전체 스크립트

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure 리소스 그룹을 만듭니다.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 가상 네트워크를 만듭니다.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷 구성을 추가합니다. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 리소스 그룹의 가상 네트워크를 가져옵니다. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷을 가져옵니다. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 네트워크 보안 그룹을 만듭니다. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 경로 테이블을 만듭니다. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 가상 네트워크에 대한 서브넷 구성을 업데이트합니다.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 가상 네트워크를 업데이트합니다.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 네트워크 보안 그룹을 가져옵니다. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 네트워크 보안 그룹에 네트워크 보안 규칙 구성을 추가합니다. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 네트워크 보안 그룹을 업데이트합니다.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 경로 테이블에 경로를 추가합니다. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 경로 테이블을 업데이트합니다.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 관리형 인스턴스를 만듭니다.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Azure SQL Managed Instance에 대한 정보를 반환합니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 가상 네트워크 게이트웨이에 대한 IP 구성을 만듭니다. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 가상 네트워크 게이트웨이를 만듭니다. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 두 가상 네트워크 게이트웨이를 연결합니다.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 새 SQL Managed Instance 장애 조치(failover) 그룹을 만듭니다.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | SQL Managed Instance 장애 조치(failover) 그룹을 가져오거나 나열합니다.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | SQL Managed Instance 장애 조치(failover) 그룹의 장애 조치(failover)를 실행합니다. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹을 제거합니다. | 

# <a name="portal"></a>[포털](#tab/azure-portal) 

Azure Portal에 사용할 수 있는 스크립트가 없습니다.

---

## <a name="next-steps"></a>다음 단계

이 가이드에서는 두 관리형 인스턴스 간에 장애 조치(failover) 그룹을 구성했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> - 기본 관리형 인스턴스를 만듭니다.
> - [장애 조치(failover) 그룹](../database/auto-failover-group-overview.md)의 일부로 보조 관리형 인스턴스를 만듭니다. 
> - 장애 조치(failover)를 테스트합니다.

SQL Managed Instance에 연결하는 방법 및 SQL Managed Instance로 데이터베이스를 복원하는 방법에 대한 다음 빠른 시작을 진행하세요. 

> [!div class="nextstepaction"]
> [SQL Managed Instance에 연결](connect-vm-instance-configure.md)
> [SQL Managed Instance로 데이터베이스 복원](restore-sample-database-quickstart.md)


