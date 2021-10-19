---
title: ARM 템플릿을 사용하여 Azure Healthcare API FHIR 서비스 배포
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 FHIR 서비스를 배포하는 방법을 알아봅니다.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.author: zxue
ms.date: 08/06/2021
ms.openlocfilehash: 97322c917e12e451affc2fe8a67bb8c8f6200a74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782425"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-arm-template"></a>ARM 템플릿을 사용하여 Azure Healthcare API 내에서 FHIR 서비스 배포

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Healthcare API(여기서는 FHIR 서비스라고 함) 내에 FHIR 서비스를 배포하는 방법을 알아봅니다. PowerShell을 사용하거나 CLI를 사용하는 두 가지 옵션을 제공합니다.

[ARM 템플릿은](../../azure-resource-manager/templates/overview.md) 프로젝트의 인프라 및 구성을 정의하는 JSON 파일입니다. 이 템플릿은 선언적 구문을 사용합니다. 선언적 구문에서는 배포를 만들기 위한 프로그래밍 명령의 시퀀스를 작성하지 않고 의도하는 배포를 설명합니다.

## <a name="prerequisites"></a>필수 조건

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)
* 코드를 로컬로 실행하려는 경우:
    * [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)
* 코드를 로컬로 실행하려는 경우:
    * Bash 셸(예: [Windows용 Git](https://gitforwindows.org)에 포함된 Git Bash)
    * [Azure CLI](/cli/azure/install-azure-cli)

---

## <a name="review-the-arm-template"></a>ARM 템플릿 검토

이 문서에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/azure-api-for-fhir/)에서 가져온 것입니다.

템플릿은 다음 세 가지 Azure 리소스를 정의합니다.
- Microsoft.HealthcareApis/workspaces
- Microsoft.HealthcareApis/workspaces/fhirservices      
- Microsoft.Storage/storageAccounts

"Microsoft.HealthcareApis/workspaces/fhirservices" 리소스에서 작업 영역 리소스, 스토리지 리소스 및 속성을 **제거하여** FHIR 서비스 `dependsOn` 리소스를 배포할 수 있습니다.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "authorityurl": {
            "type": "string",
            "defaultValue": "https://login.microsoftonline.com"
        },
        "tagName": {
            "type": "string",
            "defaultValue": "My Deployment"
        },
        "region": {
            "type": "string",
                  "allowedValues": [
                "australiaeast",
                "canadacentral",
                "eastus",
                "eastus2",
                "germanywestcentral",
                "japaneast",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "switzerlandnorth",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus2"
            ]
        },
        "workspaceName": {
            "type": "string"
        },
        "fhirServiceName": {
            "type": "string"
        },
        "tenantid": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountConfirm": {
            "type": "bool",
            "defaultValue": true
        },
        "AccessPolicies": {
            "type": "array",
            "defaultValue": []
        },
        "smartProxyEnabled": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "variables": { 
        "authority": "[Concat(parameters('authorityurl'), '/', parameters('tenantid'))]",
        "createManagedIdentity": true,
        "managedIdentityType": {
            "type": "SystemAssigned"
        },
        "storageBlobDataContributerRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]"
    },
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
       {          
            "type": "Microsoft.HealthcareApis/workspaces/fhirservices",
            "kind": "fhir-R4",
            "name": "[concat(parameters('workspaceName'), '/', parameters('fhirServiceName'))]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "dependsOn": [
                "[resourceId('Microsoft.HealthcareApis/workspaces', parameters('workspaceName'))]"
            ],
            "tags": {
                "environmentName": "[parameters('tagName')]"
            },
            "properties": {
                "accessPolicies": "[parameters('AccessPolicies')]",
                "authenticationConfiguration": {
                    "authority": "[variables('Authority')]",
                    "audience": "[concat('https//', parameters('workspaceName'), '-', parameters('fhirServiceName'), '.fhir.azurehealthcareapis.com')]",
                    "smartProxyEnabled": "[parameters('smartProxyEnabled')]"
                },
                "corsConfiguration": {
                    "allowCredentials": false,
                    "headers": ["*"],
                    "maxAge": 1440,
                    "methods": ["DELETE", "GET", "OPTIONS", "PATCH", "POST", "PUT"],
                    "origins": ["https://localhost:6001"]
                },
                "exportConfiguration": {
                    "storageAccountName": "[parameters('storageAccountName')]"
                }
            },
            "identity": "[if(variables('createManagedIdentity'), variables('managedIdentityType'), json('null'))]"
        },
        {
            "name": "[parameters('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "supportsHttpsTrafficOnly": "true"
            },
            "condition": "[parameters('storageAccountConfirm')]",
            "dependsOn": [
                "[parameters('fhirServiceName')]"
            ],
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "tags": {
                "environmentName": "[parameters('tagName')]",
                "test-account-rg": "true"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="deploy-arm-template"></a>ARM 템플릿 배포

PowerShell 또는 CLI의 두 가지 옵션을 사용하여 ARM 템플릿을 배포할 수 있습니다.

아래에 제공된 샘플 코드는 하위 폴더 "src"의 "templates" 하위 폴더에 있는 템플릿을 사용합니다. 템플릿 파일을 제대로 참조하도록 위치 경로를 변경할 수 있습니다.

배포 프로세스를 완료하는 데 몇 분 정도 걸립니다. 나중에 사용할 FHIR 서비스 및 리소스 그룹의 이름을 기록해 둡다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

### <a name="deploy-the-template-using-powershell"></a>템플릿 배포: PowerShell 사용

PowerShell에서 로컬, Visual Studio Code 또는 Azure Cloud Shell 코드를 실행하여 FHIR 서비스를 배포합니다. 

Azure에 로그인하지 않은 경우 "커넥트-AzAccount"를 사용하여 로그인합니다. 로그인한 후에는 "Get-AzContext"를 사용하여 사용하려는 구독 및 테넌트를 확인합니다. 필요한 경우 구독 및 테넌트 변경이 가능합니다.

새 리소스 그룹을 만들거나, 단계를 건너뛰거나 "New-AzResourceGroup"으로 시작하는 줄을 주석으로 처리하여 기존 리소스 그룹을 사용할 수 있습니다.

```powershell-interactive
### variables
$resourcegroupname="your resource group"
$location="South Central US"
$workspacename="your workspace name"
$servicename="your fhir service name"
$tenantid="xxx"
$subscriptionid="xxx"
$storageaccountname="storage account name"
$storageaccountconfirm=1

### login to azure
Connect-AzAccount 
#Connect-AzAccount SubscriptionId $subscriptionid
Set-AzContext -Subscription $subscriptionid
Connect-AzAccount -Tenant $tenantid -SubscriptionId $subscriptionid
#Get-AzContext 

### create resource group
New-AzResourceGroup -Name $resourcegroupname -Location $location

### deploy the resource
New-AzResourceGroupDeployment -ResourceGroupName $resourcegroupname -TemplateFile "src/templates/fhirtemplate.json" -region $location -workspaceName $workspacename -fhirServiceName $fhirservicename -tenantid $tenantid -storageAccountName $storageaccountname -storageAccountConfirm $storageaccountconfirm
```
# <a name="cli"></a>[CLI](#tab/CLI)

### <a name="deploy-the-template-using-cli"></a>템플릿 배포: CLI 사용

Visual Studio Code 또는 Azure Cloud Shell 코드를 로컬로 실행하여 FHIR 서비스를 배포합니다. 

Azure에 로그인하지 않은 경우 "az login"을 사용하여 로그인합니다. 로그인한 후에는 "az account show --output table"을 사용하여 사용하려는 구독 및 테넌트인지 확인합니다. 필요한 경우 구독 및 테넌트 변경이 가능합니다.

새 리소스 그룹을 만들거나, 단계를 건너뛰거나 "az group create"로 시작하는 줄을 주석으로 처리하여 기존 리소스 그룹을 사용할 수 있습니다.

```azurecli-interactive
### variables
resourcegroupname=your resource group name
location=southcentralus
workspacename=your workspace name
fhirservicename=your fhir service name
tenantid=xxx
subscriptionid=xxx
storageaccountname=your storage account name
storageaccountconfirm=true

### login to azure
az login
az account show --output table
az account set --subscription $subscriptionid

### create resource group
az group create --name $resourcegroupname --location $location

### deploy the resource
az deployment group create --resource-group $resourcegroupname --template-file 'src\\templates\\fhirtemplate.json' --parameters region=$location workspaceName=$workspacename fhirServiceName=$fhirservicename tenantid=$tenantid storageAccountName=$storageaccountname storageAccountConfirm=$storageaccountconfirm
```

---

## <a name="review-the-deployed-resources"></a>배포된 리소스 검토

브라우저를 열고 로 이동하여 FHIR 서비스가 실행 중인지 확인할 수 `https://<yourfhir servic>.azurehealthcareapis.com/metadata` 있습니다. 기능 문이 자동으로 표시되거나 다운로드되면 배포가 성공합니다. 

## <a name="clean-up-the-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 아래 코드를 실행하여 리소스 그룹을 삭제합니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```powershell-interactive
$resourceGroupName = “your resource group name”
Remove-AzResourceGroup -Name $resourceGroupName
```
# <a name="cli"></a>[CLI](#tab/CLI)
```azurecli-interactive
resourceGroupName = “your resource group name”
az group delete --name $resourceGroupName
```
---

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 ARM 템플릿을 사용하여 Azure Healthcare API 내에 FHIR 서비스를 배포했습니다. FHIR 서비스 지원 기능에 대한 자세한 내용은 를 참조하세요.

>[!div class="nextstepaction"]
>[지원되는 FHIR 기능](fhir-features-supported.md)