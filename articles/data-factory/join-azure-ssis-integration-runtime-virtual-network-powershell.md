---
title: Azure PowerShell를 사용 하 여 Azure SSIS 통합 런타임을 가상 네트워크에 조인
description: Azure PowerShell를 사용 하 여 azure SSIS 통합 런타임을 Azure virtual network에 조인 하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b9d264bf4e52ec79a70d6efcd326f395685d12b2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403386"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure SSIS 통합 런타임을 가상 네트워크에 조인

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="define-the-variables"></a>변수를 정의합니다.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure-SSIS IR을 가상 네트워크에 조인하기 전에 가상 네트워크를 구성해야 합니다. Azure-SSIS IR이 가상 네트워크에 조인하도록 가상 네트워크 권한과 설정을 자동으로 구성하려면 다음 스크립트를 추가합니다.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR을 만들어 가상 네트워크에 조인

Azure-SSIS IR을 만드는 동시에 가상 네트워크에 조인할 수 있습니다. 전체 스크립트 및 지침은 [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime-powershell.md)를 참조하세요.

## <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>기존 Azure-SSIS IR을 가상 네트워크에 조인

[Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md) 문서는 동일한 스크립트로 Azure-SSIS IR을 만들고 가상 네트워크에 조인하는 방법을 보여 줍니다. Azure-SSIS IR이 이미 있는 경우 다음 단계에 따라 가상 네트워크에 조인합니다. 
1. Azure-SSIS IR을 중지합니다. 
1. 가상 네트워크에 조인하도록 Azure-SSIS IR을 구성합니다. 
1. Azure-SSIS IR을 시작합니다. 

## <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR 중지

가상 네트워크에 조인하려면 먼저 Azure-SSIS IR을 중지해야 합니다. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR이 조인하기 위한 가상 네트워크 설정을 구성합니다.

Azure SSIS가 조인할 가상 네트워크의 설정을 구성하려면 다음 스크립트를 사용합니다. 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

## <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR 구성

Azure-SSIS IR을 가상 네트워크에 조인하려면 `Set-AzDataFactoryV2IntegrationRuntime` 명령을 실행합니다. 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

## <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR 시작

Azure-SSIS IR을 시작하려면 다음 명령을 실행합니다. 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

이 명령을 완료하는 데 20~30분이 걸립니다.

## <a name="next-steps"></a>다음 단계
- [가상 네트워크에 Azure SSIS 통합 런타임 조인-개요](join-azure-ssis-integration-runtime-virtual-network.md)
- [Azure-SSIS 통합 런타임 가상 네트워크 구성 세부 정보](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Azure Data Factory Studio UI를 사용 하 여 Azure SSIS 통합 런타임을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 
- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](./tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크에서 가상 네트워크 서비스 엔드포인트 또는 SQL Managed Instance와 함께 Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트하는 방법에 대한 지침을 제공합니다. 이 문서는 Azure-SSIS IR을 가상 네트워크에 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보를 가져오는 방법을 보여 줍니다. 반환된 정보의 상태 설명도 제공합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR을 규모 확장하는 방법을 보여줍니다.
