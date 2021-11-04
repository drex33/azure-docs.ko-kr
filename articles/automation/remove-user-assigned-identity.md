---
title: Azure Automation 계정에 대 한 사용자 할당 관리 id 제거
description: 이 문서에서는 Azure Automation 계정에 대해 사용자가 할당한 관리 ID를 제거하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: 11db735a21e4d4007c35f1a82f1e5dfadb377fd1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432456"
---
# <a name="remove-user-assigned-managed-identity-for-azure-automation-account"></a>Azure Automation 계정에 대 한 사용자 할당 관리 id 제거

Azure Portal, PowerShell, Azure REST API 또는 ARM(Azure Resource Manager) 템플릿을 사용하여 Azure Automation에서 사용자가 할당한 관리 ID를 제거할 수 있습니다.

## <a name="remove-using-the-azure-portal"></a>Azure Portal을 사용하여 제거

사용자가 할당한 관리 ID가 원래 어떻게 추가되었는지에 관계없이 Azure Portal에서 사용자가 할당한 관리 ID를 제거할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Automation 계정으로 이동하고 **계정 설정** 에서 **ID** 를 선택합니다.

1. **사용자 할당** 탭을 선택합니다.

1. 목록에서 제거할 사용자가 할당한 관리 ID를 선택합니다.

1. **제거** 를 선택합니다. 확인하라는 메시지가 나타나면 **예** 를 선택합니다.

이제 사용자가 할당한 관리 ID가 제거되었고 대상 리소스에 더 이상 액세스할 수 없습니다.

## <a name="remove-using-powershell"></a>PowerShell을 사용하여 제거

PowerShell cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount)를 사용하여 사용자가 할당한 모든 관리 ID를 제거하고 기존 시스템이 할당한 관리 ID를 유지합니다.

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    ```

1. [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount)를 실행합니다.

    ```powershell
    # Removes all UAs, keeps SA
    $output = Set-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount `
        -AssignSystemIdentity 
    
    $output.identity.Type
    ```

    `SystemAssigned`와 같이 출력됩니다.

## <a name="remove-using-rest-api"></a>REST API를 사용하여 제거

다음 REST API 호출 및 예제를 사용하여 Automation 계정에서 사용자가 할당한 관리 ID를 제거할 수 있습니다.

### <a name="request-body"></a>요청 본문

시나리오: 시스템이 할당한 관리 ID를 사용하거나 사용하도록 합니다. 사용자가 할당한 관리 ID 중 하나를 제거해야 합니다. 이 예제에서는 HTTP **PATCH** 메서드를 사용하여 `firstIdentity`라는 사용자가 할당한 관리 ID를 제거합니다.

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}
```

시나리오: 시스템이 할당한 관리 ID를 사용하거나 사용하도록 합니다. 사용자가 할당한 모든 관리 ID는 HTTP **PUT** 메서드를 사용하여 제거됩니다.

```json
{
  "identity": {
    "type": "SystemAssigned"
  }
}
```

시나리오: 시스템이 할당한 관리 ID를 사용하지 않거나 사용하지 않도록 합니다. 사용자가 할당한 관리 ID 중 하나를 제거해야 합니다. 이 예제에서는 HTTP **PATCH** 메서드를 사용하여 `firstIdentity`라는 사용자가 할당한 관리 ID를 제거합니다.

```json
{
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": null
    }
  }
}

```

시나리오: 시스템이 할당한 관리 ID를 사용하지 않거나 사용하지 않도록 합니다. 사용자가 할당한 모든 관리 ID는 HTTP **PUT** 메서드를 사용하여 제거됩니다.

```json
{
  "identity": {
    "type": "None"
  }
}
```

다음은 PATCH 요청을 보내기 위한 서비스의 REST API 요청 URI입니다.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>예

다음 단계를 수행합니다.

1. 수행하려는 작업에 따라 요청 본문을 복사하여 `body_remove_ua.json`이라는 파일에 붙여넣습니다. 필요한 사항을 수정한 다음, 로컬 머신 또는 Azure 스토리지 계정에 파일을 저장합니다.

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    ```

1. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_ua.json"
    ```

1. 이 예제에서는 PowerShell cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod)를 사용하여 Automation 계정에 PATCH 요청을 보냅니다.

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscriptionID/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount`?api-version=2020-01-13-preview"
    
    # build body
    $body = Get-Content $file
    
    # obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    
    # Invoke the REST API
    Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Confirm removal
    (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.Type
    ```

   사용한 구문에 따라 출력은 `SystemAssignedUserAssigned`, `SystemAssigned`, `UserAssigned` 또는 공백이 됩니다.

## <a name="remove-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 제거

Azure Resource Manager 템플릿을 사용하여 Automation 계정에 대해 사용자가 할당한 관리 ID를 추가한 경우 템플릿을 수정한 다음, 다시 실행하여 사용자가 할당한 관리 ID를 제거할 수 있습니다.

시나리오: 시스템이 할당한 관리 ID를 사용하거나 사용하도록 합니다. 사용자가 할당한 두 개의 관리 ID 중 하나를 제거해야 합니다. 이 구문 코드 조각은 템플릿에 매개 변수로 전달된 것을 **제외한** **모든** 사용자가 할당한 관리 ID를 제거합니다.

```json
...
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

시나리오: 시스템이 할당한 관리 ID를 사용하거나 사용하도록 합니다. 사용자가 할당한 모든 관리 ID를 제거해야 합니다.

```json
...
"identity": {
    "type": "SystemAssigned"
},
...
```

시나리오: 시스템이 할당한 관리 ID를 사용하지 않거나 사용하지 않도록 합니다. 사용자가 할당한 두 개의 관리 ID 중 하나를 제거해야 합니다. 이 구문 코드 조각은 템플릿에 매개 변수로 전달된 것을 **제외한** **모든** 사용자가 할당한 관리 ID를 제거합니다.

```json
...
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {}
    }
},
...
```

[Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) cmdlet을 사용하여 확인합니다. 사용한 구문에 따라 출력은 `SystemAssignedUserAssigned`, `SystemAssigned` 또는 `UserAssigned`가 됩니다.

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.Type
```

## <a name="next-steps"></a>다음 단계

- Azure Automation에서 관리 되는 id를 사용 하는 방법에 대 한 자세한 내용은 [자동화를 위해 관리 id 사용 및 사용](enable-managed-identity-for-automation.md)을 참조 하세요.

- Automation 계정 보안의 개요는 [Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.
