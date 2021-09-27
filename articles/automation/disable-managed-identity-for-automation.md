---
title: Azure Automation 계정에 대해 시스템 할당 관리 ID 사용 안 함(미리 보기)
description: 이 문서에서는 Azure Automation 계정의 시스템 할당 관리 ID를 사용하지 않도록 설정하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 07/24/2021
ms.topic: conceptual
ms.openlocfilehash: 178da223b5d5f14cc27034c39bd4cc3a05f82631
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606592"
---
# <a name="disable-system-assigned-managed-identity-for-azure-automation-account-preview"></a>Azure Automation 계정에 대해 시스템 할당 관리 ID 사용 안 함(미리 보기)

Azure Portal 또는 REST API를 사용하여 Azure Automation에서 시스템 할당 관리 ID를 사용하지 않도록 설정할 수 있습니다.

## <a name="disable-using-the-azure-portal"></a>Azure Portal에서 사용하지 않도록 설정

시스템 할당 관리 ID가 원래 어떻게 설정되었는지에 관계없이 Azure Portal에서 시스템 할당 관리 ID를 사용하지 않도록 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Automation 계정으로 이동하고 **계정 설정** 에서 **ID** 를 선택합니다.

1. **시스템 할당** 탭의 **상태** 단추에서 **끄기** 를 선택한 다음 **저장** 을 선택합니다. 확인하라는 메시지가 나타나면 **예** 를 선택합니다.

시스템 할당 관리 ID가 사용하지 않도록 설정되어 더 이상 대상 리소스에 액세스할 수 없습니다.

## <a name="disable-using-rest-api"></a>REST API를 통해 사용하지 않도록 설정

구문 및 예 단계는 아래에 나와 있습니다.

### <a name="request-body"></a>요청 본문

다음 요청 본문은 시스템 할당 관리 ID를 사용하지 않도록 설정하고 HTTP **PATCH** 메서드를 사용하여 사용자 할당 관리 ID를 제거합니다.

```json
{ 
 "identity": { 
   "type": "None" 
  } 
}

```

여러 사용자 할당 ID가 정의된 경우 이를 유지하고 시스템 할당 ID만 제거하려면 쉼표로 구분된 목록을 사용하여 각 사용자 할당 ID를 지정해야 합니다. 아래 예에서는 HTTP **PATCH** 메서드를 사용합니다.

```json
{ 
"identity" : {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
        }
    }
}
```

다음은 PATCH 요청을 보내기 위한 서비스의 REST API 요청 URI입니다.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

### <a name="example"></a>예

다음 단계를 수행합니다.

1. 수행하려는 작업에 따라 요청 본문을 복사하여 `body_remove_sa.json`이라는 파일에 붙여넣습니다. 로컬 컴퓨터 또는 Azure Storage 계정에 파일을 저장합니다.

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    ```

1. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $subscriptionID = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $file = "path\body_remove_sa.json"
    ```

1. 이 예에서는 PowerShell cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod)를 사용하여 Automation 계정에 PATCH 요청을 보냅니다.

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

    사용한 구문에 따라 출력은 `UserAssigned` 또는 공백이 됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Automation에서 관리 ID를 활성화하는 방법에 대한 자세한 내용은 [Automation의 관리 ID 활성화 및 사용(미리 보기)](enable-managed-identity-for-automation.md)을 참조하세요.

- Automation 계정 보안의 개요는 [Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.