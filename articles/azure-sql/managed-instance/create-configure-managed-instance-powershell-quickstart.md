---
title: Azure SQL Managed Instance 만들기 - 빠른 시작
description: Azure PowerShell을 사용하여 Azure SQL Managed Instance의 인스턴스를 만듭니다.
services: sql-managed-instance
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: contperf-fy21q1, devx-track-azurecli, devx-track-azurepowershell, mode-api
ms.topic: quickstart
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 06/25/2021
ms.openlocfilehash: 60a055dc534b72b6573dec14f67f625612bbfd92
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133064677"
---
# <a name="quickstart-create-a-managed-instance-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 관리되는 인스턴스 만들기

이 빠른 시작에서는 Azure PowerShell을 사용하여 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)의 인스턴스를 만드는 방법을 알아봅니다. 


## <a name="prerequisite"></a>필수 요소

- 활성화된 Azure 구독. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
- 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps) 

## <a name="set-variables"></a>변수 설정

SQL Manged Instance 인스턴스를 만들려면 Azure 내에서 여러 리소스를 만들어야 하므로 Azure PowerShell 명령은 변수를 사용하여 환경을 간소화합니다. 변수를 정의한 다음, 동일한 PowerShell 세션 내의 각 섹션에서 cmdlet을 실행합니다. 

```azurepowershell-interactive
$NSnetworkModels = "Microsoft.Azure.Commands.Network.Models"
$NScollections = "System.Collections.Generic"
# The SubscriptionId in which to create these objects
$SubscriptionId = ''
# Set the resource group name and location for your managed instance
$resourceGroupName = "myResourceGroup-$(Get-Random)"
$location = "eastus2"
# Set the networking values for your managed instance
$vNetName = "myVnet-$(Get-Random)"
$vNetAddressPrefix = "10.0.0.0/16"
$miSubnetName = "myMISubnet-$(Get-Random)"
$miSubnetAddressPrefix = "10.0.0.0/24"
#Set the managed instance name for the new managed instance
$instanceName = "myMIName-$(Get-Random)"
# Set the admin login and password for your managed instance
$miAdminSqlLogin = "SqlAdmin"
$miAdminSqlPassword = "ChangeYourAdminPassword1"
# Set the managed instance service tier, compute level, and license mode
$edition = "General Purpose"
$vCores = 4
$maxStorage = 128
$computeGeneration = "Gen5"
$license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
```

## <a name="create-resource-group"></a>리소스 그룹 만들기 

먼저 Azure에 연결하고, 구독 컨텍스트를 설정하고, 리소스 그룹을 만듭니다. 

이렇게 하려면 다음 PowerShell 스크립트를 실행합니다. 

```azurepowershell-interactive

## Connect to Azure
Connect-AzAccount

# Set subscription context
Set-AzContext -SubscriptionId $SubscriptionId 

# Create a resource group
$resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
```

## <a name="configure-networking"></a>네트워킹 구성

리소스 그룹을 만든 후 가상 네트워크, 서브넷, 네트워크 보안 그룹 및 라우팅 테이블과 같은 네트워킹 리소스를 구성합니다. 이 예제에서는 GitHub에서 [delegate-subnet.ps1](https://github.com/microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet)로 사용할 수 있는 **Managed Instance 배포에 대한 대리자 서브넷** 스크립트를 사용하는 방법을 보여줍니다.

이렇게 하려면 다음 PowerShell 스크립트를 실행합니다. 

```azurepowershell-interactive

# Configure virtual network, subnets, network security group, and routing table
$virtualNetwork = New-AzVirtualNetwork `
                      -ResourceGroupName $resourceGroupName `
                      -Location $location `
                      -Name $vNetName `
                      -AddressPrefix $vNetAddressPrefix

                  Add-AzVirtualNetworkSubnetConfig `
                      -Name $miSubnetName `
                      -VirtualNetwork $virtualNetwork `
                      -AddressPrefix $miSubnetAddressPrefix |
                  Set-AzVirtualNetwork
                  
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = $SubscriptionId
    resourceGroupName = $resourceGroupName
    virtualNetworkName = $vNetName
    subnetName = $miSubnetName
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters

$virtualNetwork = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroupName
$miSubnet = Get-AzVirtualNetworkSubnetConfig -Name $miSubnetName -VirtualNetwork $virtualNetwork
$miSubnetConfigId = $miSubnet.Id
```

## <a name="create-managed-instance"></a>관리되는 인스턴스 만들기 

보안 강화를 위해 SQL Managed Instance 자격 증명에 대해 복잡한 임의 암호를 만듭니다. 

```azurepowershell-interactive
# Create credentials
$secpassword = ConvertTo-SecureString $miAdminSqlPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential ($miAdminSqlLogin, $secpassword)
```

그런 다음, SQL Managed Instance를 만듭니다. 

```azurepowershell-interactive
# Create managed instance
New-AzSqlInstance -Name $instanceName `
                      -ResourceGroupName $resourceGroupName -Location $location -SubnetId $miSubnetConfigId `
                      -AdministratorCredential $credential `
                      -StorageSizeInGB $maxStorage -VCore $vCores -Edition $edition `
                      -ComputeGeneration $computeGeneration -LicenseType $license
```

이 작업은 완료하는 데 다소 시간이 걸릴 수 있습니다. 자세한 내용은 [관리 작업](management-operations-overview.md)을 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹 및 관리형 인스턴스를 유지하여 다음 단계로 이동하고, 클라이언트 가상 머신을 사용하여 SQL Managed Instance에 연결하는 방법을 알아봅니다. 

이러한 리소스의 사용을 마친 후에는 만든 리소스 그룹을 삭제할 수 있습니다. 그러면 해당 리소스 그룹 내에서 서버 및 단일 데이터베이스도 삭제됩니다.

```azurepowershell-interactive
# Clean up deployment 
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```


## <a name="next-steps"></a>다음 단계

SQL Managed Instance가 만들어지면 클라이언트 VM을 배포하여 SQL Managed Instance에 연결하고 샘플 데이터베이스를 복원합니다. 

> [!div class="nextstepaction"]
> [클라이언트 VM 만들기](connect-vm-instance-configure.md)
> [데이터베이스 복원](restore-sample-database-quickstart.md)
