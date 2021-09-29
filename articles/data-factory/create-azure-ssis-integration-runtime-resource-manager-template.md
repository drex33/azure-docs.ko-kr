---
title: Azure Resource Manager 템플릿을 사용하여 통합 런타임을 만들기
description: Azure에서 SSIS 패키지를 배포 하 고 실행할 수 있도록 Azure Resource Manager 템플릿을 사용 하 여 Azure Data Factory에서 Azure SSIS 통합 런타임을 만드는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 09/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3825639ce9ffdf568fdb6500b1826a006b035c72
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220528"
---
# <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Azure Resource Manager 템플릿을 사용하여 통합 런타임을 만들기

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 Azure-SSIS 통합 런타임을 만듭니다. 

## <a name="sample-azure-resource-manager-template"></a>샘플 Azure Resource Manager 템플릿

다음은 Azure Resource Manager 템플릿을 사용 하 여 Azure SSIS 통합 런타임을 만드는 단계입니다.

1. 다음 Azure Resource Manager 템플릿을 사용하여 JSON 파일을 만듭니다. 대괄호(자리 표시자)의 값을 사용자 고유 값으로 바꿉니다.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Azure Resource Manager 템플릿을 배포하려면 다음 예제와 같이 `New-AzResourceGroupDeployment` 명령을 실행합니다. 이 예제에서 리소스 그룹의 이름은 `ADFTutorialResourceGroup`입니다. `ADFTutorialARM.json`은 데이터 팩터리 및 Azure-SSIS IR에 대한 JSON 정의를 포함하고 있는 파일입니다.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    이 명령은 데이터 팩터리를 만들고 그 안에 Azure-SSIS IR을 만들지만 IR을 시작하지는 않습니다.

3. Azure-SSIS IR을 시작하려면 `Start-AzDataFactoryV2IntegrationRuntime` 명령을 실행합니다.

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR이 가상 네트워크에 조인하는 데는 20~30분 정도 걸릴 수 있습니다.
>
> SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 이 서비스는 가상 네트워크에 대한 권한 및 설정을 구성하며, 지정된 경우 Azure-SSIS IR을 가상 네트워크에 조인합니다.
> 
> Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](./built-in-preinstalled-components-ssis-integration-runtime.md)를 참조하세요. 설치할 수 있는 추가 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](./how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal를 사용 하 여 Azure-SSIS IR를 프로 비전 하는 방법을 알아봅니다](create-azure-ssis-integration-runtime-portal.md).
- [Azure PowerShell를 사용 하 여 Azure-SSIS IR를 프로 비전 하는 방법을 알아봅니다](create-azure-ssis-integration-runtime-powershell.md).
- [Azure Data Factory에서 SSIS 패키지를 배포 하 고 실행](create-azure-ssis-integration-runtime-deploy-packages.md)합니다.

이 설명서의 다른 Azure-SSIS IR 항목을 참조하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 관한 정보를 제공합니다.
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
