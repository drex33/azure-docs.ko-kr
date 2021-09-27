---
title: Azure Automation PowerShell Runbook에 Azure Resource Manager 템플릿 배포
description: 이 문서에서는 PowerShell Runbook에서 Azure Storage에 저장된 Azure Resource Manager 템플릿을 배포하는 방법에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 61ff25cd9878ee94ce0ba6db7b2c4e4ac8e649de
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057760"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-automation-powershell-runbook"></a>Automation PowerShell runbook에서 Azure Resource Manager 템플릿 배포

[Azure Resource Manager 템플릿을](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)사용 하 여 Azure 리소스를 배포 하는 [Automation PowerShell runbook](./learn/automation-tutorial-runbook-textual-powershell.md) 을 작성할 수 있습니다. 템플릿을 사용 하면 Azure Automation를 사용 하 여 Azure 리소스 배포를 자동화할 수 있습니다. Azure Storage와 같은 안전한 중앙 위치에서 Resource Manager 템플릿을 유지 관리할 수 있습니다.

이 문서에서는 [Azure Storage](../storage/common/storage-introduction.md)에 저장된 Resource Manager 템플릿을 사용하여 새 Azure Storage 계정을 배포하는 PowerShell Runbook을 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 사용자 할당 관리 ID가 하나 이상 있는 Azure Automation 계정. 자세한 내용은 [Azure Automation 계정에 대해 사용자 할당 관리 ID 사용](./add-user-assigned-identity.md)을 참조하세요.

* Az modules:,, `Az.Accounts` `Az.ManagedServiceIdentity` `Az.Resources` 및 `Az.Storage` Automation 계정으로 가져옵니다. 자세한 내용은 [Az 모듈 가져오기](./shared-resources/modules.md#import-az-modules)를 참조하세요.

* [Azure Storage 계정](../storage/common/storage-account-create.md). Resource Manager 템플릿을 저장하는 위치입니다.

* 로컬 머신에 설치된 Azure PowerShell. Azure PowerShell을 가져오는 방법에 대한 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 모듈 [Az. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity)도 필요 합니다. `Az.ManagedServiceIdentity` 는 미리 보기 모듈 이며 Az module의 일부로 설치 되지 않습니다. 설치 하려면를 실행 합니다. `Install-Module -Name Az.ManagedServiceIdentity`

## <a name="assign-permissions-to-managed-identities"></a>관리 ID에 권한 할당

Runbook에서 저장소 관련 작업을 수행 하려면 관리 되는 id에 대 한 사용 권한을 할당 합니다.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 아래 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $storageAccount = "storageAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    $storageTemplate = "path\storageTemplate.json"
    $runbookScript = "path\runbookScript.ps1"
    ```

1. `reader`시스템 할당 관리 id에 역할을 할당 하 여 cmdlet을 실행 `Get-AzUserAssignedIdentity` 합니다.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. `Storage Account Contributor`저장소 계정에 대 한 작업에 대 한 사용자 할당 관리 id에 역할을 할당 합니다.

    ```powershell
    $UAMI_ID = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI_ID `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Storage Account Contributor"
    ```


## <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기

이 예제에서는 새 Azure Storage 계정을 배포 하는 리소스 관리자 템플릿을 사용 합니다. 라는 로컬 파일을 만들고 `storageTemplate.json` 다음 코드를 붙여 넣습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="save-the-resource-manager-template-in-azure-files"></a>Azure Files에 Resource Manager 템플릿 저장

PowerShell을 사용 하 여 Azure 파일 공유를 만들고 업로드 `storageTemplate.json` 합니다. Azure Portal에서 파일 공유를 만들고 파일을 업로드하는 방법에 관한 지침은 [Windows에서 Azure File 시작](../storage/files/storage-files-quick-create-use-windows.md)을 참조하세요.

다음 명령을 실행 하 여 파일 공유를 만들고 해당 파일 공유에 리소스 관리자 템플릿을 업로드 합니다.

```powershell
# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName $resourceGroup -Name $storageAccount

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the storageTemplate.json file to the new file share
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $storageTemplate
```

## <a name="create-the-powershell-runbook-script"></a>PowerShell Runbook 스크립트 만들기

`storageTemplate.json`Azure Storage에서 파일을 가져오고 템플릿을 배포 하 여 새 Azure Storage 계정을 만드는 PowerShell 스크립트를 만듭니다.  라는 로컬 파일을 만들고 `runbookScript.ps1` 다음 코드를 붙여 넣습니다.


```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $resourceGroup,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccount,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $storageFileName,

    [Parameter(Mandatory=$true)]
    [string]
    $userAssignedManagedIdentity
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context
$identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
    -Name $userAssignedManagedIdentity `
    -DefaultProfile $AzureContext
$AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageAccountKey

Get-AzStorageFileContent `
    -ShareName 'resource-templates' `
    -Context $Context `
    -path 'storageTemplate.json' `
    -Destination 'C:\Temp' -Force

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $storageFileName

# Deploy the storage account
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroup `
    -TemplateFile $TemplateFile `
    -TemplateParameterObject $Parameters 
```

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Azure Automation 계정으로 Runbook 가져오기 및 게시

PowerShell을 사용 하 여 runbook을 Automation 계정으로 가져온 다음 runbook을 게시 합니다. Azure Portal runbook 가져오기 및 게시에 대 한 자세한 내용은 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조 하세요.

`runbookScript.ps1`을 PowerShell Runbook으로 Automation 계정에 가져오려면 다음 PowerShell 명령을 실행합니다.

```powershell
$importParams = @{
    Path = $runbookScript
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Type = "PowerShell"
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Runbook을 시작합니다.

이제 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet을 호출하여 Runbook을 시작합니다. Azure Portal에서 Runbook을 시작하는 방법에 대한 자세한 내용은 [Azure Automation에서 Runbook 시작](./start-runbooks.md)을 참조하세요.

PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    resourceGroup = $resourceGroup
    storageAccount = $storageAccount
    storageAccountKey = $key[0].Value # We got this key earlier
    storageFileName = "storageTemplate.json"
    userAssignedManagedIdentity = $userAssignedManagedIdentity
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    resourceGroup = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Runbook이 실행된 후에는 작업 개체 `$job.Status`의 속성 값을 검색하여 해당 상태를 확인할 수 있습니다.

Runbook은 Resource Manager 템플릿을 가져와서 새 Azure Storage 계정을 배포하는 데 사용합니다. 다음 명령을 실행하여 새 스토리지 계정이 만들어진 것을 확인할 수 있습니다.

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>다음 단계

* Resource Manager 템플릿에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)를 참조하세요.
* Azure Storage를 시작하려면 [Azure Storage 소개](../storage/common/storage-introduction.md)를 참조하세요.
* 다른 유용한 Azure Automation Runbook을 찾으려면 [Azure Automation에서 Runbook 및 모듈 사용](automation-runbook-gallery.md)을 참조하세요.
