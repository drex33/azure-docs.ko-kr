---
title: Azure PowerShell를 통해 Azure SSIS 통합 런타임을 가상 네트워크에 조인
description: Azure PowerShell를 통해 Azure SSIS integration runtime을 가상 네트워크에 조인 하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a4d06256407fd42bdfa9f92cc2306df7dd33983c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087859"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-powershell"></a>Azure PowerShell를 통해 Azure SSIS 통합 런타임을 가상 네트워크에 조인

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에서는 Azure PowerShell를 통해 Azure Data Factory (ADF)에서 기존 Azure SQL Server Integration Services (SSIS) 통합 런타임 (IR)을 가상 네트워크에 조인 하는 방법을 보여 줍니다. 

## <a name="create-variables"></a>변수 만들기

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Azure Resource Manager or Classic
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server configured with a private endpoint/IP firewall rule/virtual network service endpoint or Azure SQL Managed Instance that joins a virtual network to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend Azure Resource Manager virtual network, because classic virtual network will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for Azure SQL Database server configured with a virtual network service endpoint or a different subnet from the one used for Azure SQL Managed Instance that joins a virtual network
$SubnetId = $VnetId + '/subnets/' + $SubnetName 
# Virtual network injection method: Standard or Express. For comparison, see https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-virtual-network-configuration.
$VnetInjectionMethod = "Standard" # Standard by default, whereas Express lets you use the express virtual network injection method
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

## <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure-SSIS IR을 가상 네트워크에 조인하기 전에 가상 네트워크를 구성해야 합니다. 가상 네트워크에 가입 하기 위해 Azure-SSIS IR에 대 한 가상 네트워크 권한 및 설정을 자동으로 구성 하려면 다음 스크립트를 추가 합니다.

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

[Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime-powershell.md) 문서는 동일한 스크립트로 Azure-SSIS IR을 만들고 가상 네트워크에 조인하는 방법을 보여 줍니다. Azure-SSIS IR 이미 있는 경우 다음 단계에 따라 가상 네트워크에 가입 합니다. 
1. Azure-SSIS IR를 중지 합니다. 
1. 가상 네트워크에 가입 하도록 Azure-SSIS IR를 구성 합니다. 
1. Azure-SSIS IR를 시작 합니다. 

## <a name="stop-your-azure-ssis-ir"></a>Azure-SSIS IR 중지

가상 네트워크에 가입 하려면 먼저 Azure-SSIS IR를 중지 해야 합니다. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

## <a name="configure-your-azure-ssis-ir-to-join-a-virtual-network"></a>가상 네트워크에 가입 하도록 Azure-SSIS IR 구성

Azure-SSIS IR을 가상 네트워크에 조인하려면 `Set-AzDataFactoryV2IntegrationRuntime` 명령을 실행합니다. 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -SubnetId $SubnetId `
    -VNetInjectionMethod $VnetInjectionMethod

# Add public IP address parameters if you use the standard virtual network injection method and bring your own static public IP addresses
if($VnetInjectionMethod -eq "Standard")
{
    if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
    {
        $publicIPs = @($FirstPublicIP, $SecondPublicIP)
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -PublicIPs $publicIPs
    }
}
```

## <a name="start-your-azure-ssis-ir"></a>Azure-SSIS IR 시작

Azure-SSIS IR를 시작 하려면 다음 명령을 실행 합니다. 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Express/standard 가상 네트워크 주입 방법을 사용 하는 경우이 명령을 완료 하는 데는 각각 5/20-30 분이 소요 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS IR를 삽입 하도록 가상 네트워크 구성](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express virtual network 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [표준 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [ADF UI를 통해 Azure-SSIS IR를 가상 네트워크에 가입](join-azure-ssis-integration-runtime-virtual-network-ui.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database server를 사용 하 여 SSISDB를 호스팅합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크를 호스트 SSISDB에 연결 하는 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/개인 끝점 또는 Azure SQL Managed Instance를 사용 하 여 구성 된 Azure SQL Database 서버를 사용 하는 방법에 대 한 지침을 제공 합니다. Azure-SSIS IR를 가상 네트워크에 조인 하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
