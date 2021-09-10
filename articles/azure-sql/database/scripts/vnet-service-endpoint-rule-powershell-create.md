---
title: 단일 및 풀링된 데이터베이스에 대한 VNet 엔드포인트 및 규칙을 위한 PowerShell
description: Azure SQL Database 및 Azure Synapse에 대한 가상 서비스 엔드포인트를 만들고 관리할 수 있는 PowerShell 스크립트를 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: deployment-configuration
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, mathoma
ms.date: 04/17/2019
ms.custom: sqldbrb=1, devx-track-azurepowershell
tags: azure-synapse
ms.openlocfilehash: bc00adebe6b60f47dcd2fb5de50508ff4ada2ae7
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110698984"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>PowerShell: Azure SQL Database에 대한 가상 서비스 엔드포인트 및 VNet 규칙 만들기
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

*가상 네트워크 규칙* 은 [Azure SQL Database](../sql-database-paas-overview.md) 데이터베이스, 탄력적 풀 또는 [Azure Synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)의 데이터베이스에 대한 [논리적 SQL Server](../logical-servers.md)가 가상 네트워크의 특정 서브넷에서 보낸 통신을 수락할지 여부를 제어하는 하나의 방화벽 보안 기능입니다.

> [!IMPORTANT]
> 이 문서는 Azure Synapse(이전의 SQL DW)를 포함한 Azure SQL Database에 적용됩니다. 편의상 이 문서에서 Azure SQL Database라는 용어는 Azure SQL Database 또는 Azure Synapse에 속하는 데이터베이스에 적용됩니다. 연결된 서비스 엔드포인트가 없기 때문에 이 문서는 Azure SQL Managed Instance에 적용되지 *않습니다*.

이 문서에서는 다음 작업을 수행하는 PowerShell 스크립트를 설명합니다.

1. 서브넷에서 Microsoft Azure *가상 서비스 엔드포인트* 를 만듭니다.
2. 서버의 방화벽에 엔드포인트를 추가하여 *가상 네트워크 규칙* 을 만듭니다.

자세한 내용은 [Azure SQL Database용 가상 서비스 엔드포인트][sql-db-vnet-service-endpoint-rule-overview-735r]를 참조하세요.

> [!TIP]
> 서브넷에서 Azure SQL Database에 대한 가상 서비스 엔드포인트 *형식 이름* 을 평가하거나 추가하기만 할 경우에는 더 많은 [직접 PowerShell 스크립트](#a-verify-subnet-is-endpoint-ps-100)로 바로 건너뛸 수 있습니다.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후 모든 개발은 [`Az.Sql` cmdlet](/powershell/module/az.sql)을 위한 것입니다. 이전 모듈은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

## <a name="major-cmdlets"></a>주요 cmdlet

이 문서에서는 규칙을 만드는 데 사용되는 서버의 ACL(액세스 제어 목록)에 서브넷 엔드포인트를 추가하는 [**New-AzSqlServerVirtualNetworkRule** cmdlet](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)을 중점적으로 설명합니다.

다음 목록은 **New-AzSqlServerVirtualNetworkRule** 호출을 준비하기 위해 실행해야 하는 *주요* cmdlet의 시퀀스를 보여줍니다. 이 문서에서 이러한 호출은 [스크립트 3 “가상 네트워크 규칙”](#a-script-30)에서 수행됩니다.

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig): 서브넷 개체를 만듭니다.
2. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork): 가상 네트워크를 만들어 가상 네트워크에 서브넷을 제공합니다.
3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): 가상 서비스 엔드포인트를 서브넷에 할당합니다.
4. [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork): 가상 네트워크에 대한 업데이트를 유지합니다.
5. [New-AzSqlServerVirtualNetworkRule](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): 서브넷이 엔드포인트가 된 후 서브넷을 가상 네트워크 규칙으로 서버의 ACL에 추가합니다.
   - 이 cmdlet은 Azure RM PowerShell 모듈 버전 5.1.1에서 시작하는 **-IgnoreMissingVNetServiceEndpoint** 매개 변수를 제공합니다.

## <a name="prerequisites-for-running-powershell"></a>PowerShell을 실행하기 위한 필수 구성 요소

- [Azure Portal][http-azure-portal-link-ref-477t] 등을 통해 Azure에 이미 로그인할 수 있습니다.
- 이미 PowerShell 스크립트를 실행할 수 있습니다.

> [!NOTE]
> 서버에 추가할 VNet/서브넷에 대해 서비스 엔드포인트가 설정되어 있는지 확인하세요. 그렇지 않으면 VNet 방화벽 규칙 만들기는 실패하게 됩니다.

## <a name="one-script-divided-into-four-chunks"></a>4개 청크로 구분된 하나의 스크립트

데모 PowerShell 스크립트는 더 작은 스크립트 시퀀스로 구분됩니다. 구분을 통해 쉽게 알아볼 수 있고 유연성이 제공됩니다. 스크립트는 지정된 시퀀스로 실행되어야 합니다. 지금 스크립트를 실행할 시간이 없는 경우 실제 테스트 출력이 스크립트 4 뒤에 표시되어 있습니다.

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>스크립트 1: 변수

이 첫 번째 PowerShell 스크립트는 값을 변수에 할당합니다. 후속 스크립트는 이러한 변수에 따라 결정됩니다.

> [!IMPORTANT]
> 이 스크립트를 실행하기 전에 필요한 경우 값을 편집할 수 있습니다. 예를 들어 이미 리소스 그룹이 있는 경우 리소스 그룹 이름을 할당된 값으로 편집할 수 있습니다.
>
> 구독 이름은 스크립트로 편집되어야 합니다.

### <a name="powershell-script-1-source-code"></a>PowerShell 스크립트 1 소스 코드

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName $SubscriptionName

$ResourceGroupName = 'RG-YourNameHere'
$Region = 'westcentralus'

$VNetName = 'myVNet'
$SubnetName = 'mySubnet'
$VNetAddressPrefix = '10.1.0.0/16'
$SubnetAddressPrefix = '10.1.1.0/24'
$VNetRuleName = 'myFirstVNetRule-ForAcl'

$SqlDbServerName = 'mysqldbserver-forvnet'
$SqlDbAdminLoginName = 'ServerAdmin'
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Official type name.

Write-Host 'Completed script 1, the "Variables".'
```

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>스크립트 2: 필수 구성 요소

이 스크립트는 엔드포인트 작업을 나타내는 다음 스크립트를 준비합니다. 이 스크립트는 다음과 같은 나열된 항목이 없는 경우 해당 항목을 만듭니다. 이러한 항목이 이미 있다고 확신할 경우 스크립트 2를 건너뛸 수 있습니다.

- Azure 리소스 그룹
- 논리 SQL 서버

### <a name="powershell-script-2-source-code"></a>PowerShell 스크립트 2 소스 코드

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists."

$gottenResourceGroup = $null
$gottenResourceGroup = Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue

if ($null -eq $gottenResourceGroup) {
    Write-Host "Creating your missing Resource Group - $ResourceGroupName."
    New-AzResourceGroup -Name $ResourceGroupName -Location $Region
} else {
    Write-Host "Good, your Resource Group already exists - $ResourceGroupName."
}

$gottenResourceGroup = $null

###########################################################
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists."

$sqlDbServer = $null
$azSqlParams = @{
    ResourceGroupName = $ResourceGroupName
    ServerName        = $SqlDbServerName
    ErrorAction       = 'SilentlyContinue'
}
$sqlDbServer = Get-AzSqlServer @azSqlParams

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName."
    Write-Host "Gather the credentials necessary to next create a server."

    $sqlAdministratorCredentials = [pscredential]::new($SqlDbAdminLoginName,(ConvertTo-SecureString -String $SqlDbAdminLoginPassword -AsPlainText -Force))

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending."
        return
    }

    Write-Host "Create your server."

    $sqlSrvParams = @{
        ResourceGroupName           = $ResourceGroupName
        ServerName                  = $SqlDbServerName
        Location                    = $Region
        SqlAdministratorCredentials = $sqlAdministratorCredentials
    }
    New-AzSqlServer @sqlSrvParams
} else {
    Write-Host "Good, your server already exists - $SqlDbServerName."
}

$sqlAdministratorCredentials = $null
$sqlDbServer = $null

Write-Host 'Completed script 2, the "Prerequisites".'
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>스크립트 3: 엔드포인트 및 규칙 만들기

이 스크립트는 서브넷이 있는 가상 네트워크를 만듭니다. 그런 다음 **Microsoft.Sql** 엔드포인트 형식을 서브넷에 할당합니다. 마지막으로 이 스크립트는 규칙을 만드는 데 사용되는 ACL(액세스 제어 목록)에 서브넷을 추가합니다.

### <a name="powershell-script-3-source-code"></a>PowerShell 스크립트 3 소스 코드

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network."

$subnetParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$subnet = New-AzVirtualNetworkSubnetConfig @subnetParams

Write-Host "Create a virtual network '$VNetName'.`nGive the subnet to the virtual network that we created."

$vnetParams = @{
    Name              = $VNetName
    AddressPrefix     = $VNetAddressPrefix
    Subnet            = $subnet
    ResourceGroupName = $ResourceGroupName
    Location          = $Region
}
$vnet = New-AzVirtualNetwork @vnetParams

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet."

$vnetSubParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @vnetSubParams

Write-Host "Persist the updates made to the virtual network > subnet."

$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

$vnet.Subnets[0].ServiceEndpoints  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server."

$ruleParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    VirtualNetworkSubnetId = $subnet.Id
}
New-AzSqlServerVirtualNetworkRule @ruleParams 

Write-Host "Verify that the rule is in the SQL Database ACL."

$rule2Params = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
}
Get-AzSqlServerVirtualNetworkRule @rule2Params

Write-Host 'Completed script 3, the "Virtual-Network-Rule".'
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>스크립트 4: 정리

이 마지막 스크립트는 이전 스크립트를 데모용으로 만든 리소스를 삭제합니다. 그러나 이 스크립트는 다음 항목을 삭제하기 전에 확인 메시지를 표시합니다.

- 논리 SQL 서버
- Azure 리소스 그룹

스크립트 1이 완료된 후 언제든지 스크립트 4를 실행할 수 있습니다.

### <a name="powershell-script-4-source-code"></a>PowerShell 스크립트 4 소스 코드

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL."

$removeParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    ErrorAction            = 'SilentlyContinue'
}
Remove-AzSqlServerVirtualNetworkRule @removeParams

Write-Host "Delete the endpoint from the subnet."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

Remove-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Delete the virtual network (thus also deletes the subnet)."

$removeParams = @{
    Name              = $VNetName
    ResourceGroupName = $ResourceGroupName
    ErrorAction       = 'SilentlyContinue'
}
Remove-AzVirtualNetwork @removeParams

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]'
if ('yes' -eq $yesno) {
    Write-Host "Remove the server."

    $removeParams = @{
        ServerName        = $SqlDbServerName
        ResourceGroupName = $ResourceGroupName
        ErrorAction       = 'SilentlyContinue'
    }
    Remove-AzSqlServer @removeParams

    Write-Host "Remove the Azure Resource Group."
    
    Remove-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue
} else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group."
}

Write-Host 'Completed script 4, the "Clean-Up".'
```

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>서브넷이 엔드포인트인지 확인

이미 가상 서비스 엔드포인트임을 의미하는 **Microsoft.Sql** 형식 이름이 할당된 서브넷이 있을 수 있습니다. [Azure Portal][http-azure-portal-link-ref-477t]을 사용하여 엔드포인트에서 가상 네트워크 규칙을 만들 수 있습니다.

또는 서브넷에 **Microsoft.Sql** 형식 이름이 있는지 확실하지 않을 수 있습니다. 다음 PowerShell 스크립트를 실행하여 이러한 작업을 수행합니다.

1. 서브넷에 **Microsoft.Sql** 형식 이름이 있는지 확인합니다.
2. 선택적으로 형식 이름을 할당합니다(없는 경우).
    - 스크립트가 없는 형식 이름을 적용하기 전에 *확인* 메시지를 표시합니다.

### <a name="phases-of-the-script"></a>스크립트의 단계

다음은 PowerShell 스크립트의 단계입니다.

1. Azure 계정에 로그인합니다(PS 세션당 한 번만 필요).  변수를 할당합니다.
2. 가상 네트워크를 검색하고 서브넷을 검색합니다.
3. **Microsoft.Sql** 엔드포인트 서버 형식으로 태그가 지정된 서브넷이 있나요?
4. 서브넷에서 **Microsoft.Sql** 형식 이름의 가상 서비스 엔드포인트를 추가합니다.

> [!IMPORTANT]
> 이 스크립트를 실행하기 전에 스크립트 위쪽에서 $ 변수에 할당된 값을 편집해야 합니다.

### <a name="direct-powershell-source-code"></a>직접 PowerShell 소스 코드

확인 메시지가 표시될 때 예로 응답하지 않으면 이 PowerShell 스크립트는 아무것도 업데이트하지 않습니다. 이 스크립트는 서브넷에 **Microsoft.Sql** 형식 이름을 추가할 수 있습니다. 하지만 서브넷에 형식 이름이 없는 경우에만 추가를 시도합니다.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName "$SubscriptionName"

$ResourceGroupName = 'yourRGName'
$VNetName = 'yourVNetName'
$SubnetName = 'yourSubnetName'
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.' # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null
$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'."
    return
}

$subnet = $null
for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn]
    if ($subnet.Name -eq $SubnetName) { break }
    $subnet = $null
}

if ($null -eq $subnet) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'"
    Return
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null
for ($nn = 0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn]
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql
        break
    }
    $endpointMsSql = $null
}

if ($null -eq $endpointMsSql) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."
    return
} else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]'
    if ('no' -eq $yesno) { return }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$setParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @setParams

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints # Display.
}
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
