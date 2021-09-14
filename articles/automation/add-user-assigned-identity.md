---
title: Azure Automation 계정에 대해 사용자 할당 관리 ID 사용(미리 보기)
description: 이 문서에서는 Azure Automation 계정에 사용자 할당 관리 ID를 설정하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 08/26/2021
ms.topic: conceptual
ms.openlocfilehash: ce409853cddfd0278692e2c6e233331530296d6b
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214264"
---
# <a name="using-a-user-assigned-managed-identity-for-an-azure-automation-account-preview"></a>Azure Automation 계정에 대해 사용자 할당 관리 ID 사용(미리 보기)

이 문서에서는 Azure Automation 계정에 사용자 할당 관리 ID를 만드는 방법과 이를 사용하여 다른 리소스에 액세스하는 방법을 보여줍니다. 관리 ID가 Azure Automation에서 작동하는 방법에 대한 자세한 내용은 [관리 ID](automation-security-overview.md#managed-identities-preview)를 참조하세요.

> [!NOTE]
> 사용자가 할당한 ID는 클라우드 작업에서만 지원됩니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Automation 계정. 자세한 내용은 [Azure Automation 계정 만들기](automation-quickstart-create-account.md)를 참조하세요.

- 시스템이 할당한 관리 ID. 자세한 내용은 [Azure Automation 계정에 대해 시스템 할당 관리 ID 사용(미리 보기)](enable-managed-identity-for-automation.md)을 참조하세요.

- 사용자가 할당한 관리 ID. 자세한 내용은 [사용자 할당 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)을 참조하세요.

- Runbook에서 해당 ID를 사용하여 관리하는 사용자 할당 관리 ID와 대상 Azure 리소스가 같은 Azure 구독에 있어야 합니다.

- 최신 버전의 Azure 계정 모듈. 현재 최신 버전은 2.2.8입니다. (이 버전에 대한 자세한 내용은 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/)를 참조하세요.)

- Automation Runbook에서 액세스할 Azure 리소스입니다. 이 리소스에는 Automation Runbook에서 리소스에 대한 액세스를 인증하는 데 도움이 되는 사용자 할당 관리 ID에 정의된 역할이 있어야 합니다. 역할을 추가하려면 대응하는 Azure AD 테넌트 리소스의 소유자여야 합니다.

- 사용자 할당 관리 ID를 사용하여 하이브리드 작업을 실행하려는 경우에는 Hybrid Runbook Worker를 최신 버전으로 업데이트합니다. 필요한 최소 버전:

  - Windows Hybrid Runbook Worker: 버전 7.3.1125.0
  - Linux Hybrid Runbook Worker: 버전 1.7.4.0

## <a name="add-user-assigned-managed-identity-for-azure-automation-account"></a>Azure Automation 계정에 대해 사용자 할당 관리 ID 추가

Azure Portal, PowerShell, Azure REST API 또는 ARM 템플릿을 사용하여 Azure Automation 계정에 대해 사용자 할당 관리 ID를 추가할 수 있습니다. PowerShell과 관련된 예제의 경우 먼저 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount -Subscription
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

그런 다음, 예제 전체에서 사용할 변수 세트를 초기화합니다. 아래 값을 수정한 다음, 실행합니다.

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
$userAssignedOne = "userAssignedIdentityOne"
$userAssignedTwo = "userAssignedIdentityTwo"
```

### <a name="add-using-the-azure-portal"></a>Azure Portal 사용 추가

다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.

1.  **계정 설정** 에서  **ID** 를 선택합니다.

1. **사용자 할당** 탭을 선택한 다음, **추가** 를 선택합니다.

1. 기존 사용자 할당 관리 ID를 선택한 다음, **추가** 를 선택합니다. 그러면 **사용자 할당** 탭으로 돌아갑니다.

   :::image type="content" source="media/add-user-assigned-identity/user-assigned-managed-identity.png" alt-text="Portal의 출력":::.

### <a name="add-using-powershell"></a>PowerShell 사용 추가

PowerShell cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount)를 사용하여 사용자 할당 관리 ID를 추가합니다. 먼저 기존 시스템 할당 관리 ID가 있는지를 고려해야 합니다. 다음 예제에서는 기존 Automation 계정에 2개의 기존 사용자 할당 관리 ID를 추가하고, 시스템 할당 관리 ID가 있는 경우 이를 사용하지 않도록 설정합니다.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo"

$output
```

기존 시스템 할당 관리 ID를 유지하려면 다음을 사용합니다.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignUserIdentity "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedOne", `
        "/subscriptions/$subscriptionID/resourcegroups/$resourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$userAssignedTwo" `
    -AssignSystemIdentity

$output
```

출력은 다음과 비슷해야 합니다.

:::image type="content" source="media/add-user-assigned-identity/set-azautomationaccount-output.png" alt-text="Set-AzAutomationAccount 명령의 출력입니다.":::

추가 출력의 경우 `$output.identity | ConvertTo-Json`을 실행합니다.

### <a name="add-using-a-rest-api"></a>REST API 사용 추가

구문 및 예제 단계는 아래에 나와 있습니다.

#### <a name="syntax"></a>Syntax

아래 샘플 본문 구문은 아직 사용하도록 설정되지 않은 경우 시스템 할당 관리 ID를 사용하도록 설정하고 기존 Automation 계정에 2개의 기존 사용자 할당 관리 ID를 할당합니다.

패치

```json
{
  "identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/firstIdentity": {},
      "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.ManagedIdentity/userAssignedIdentities/secondIdentity": {}
    }
  }
}
```

API의 구문은 다음과 같습니다.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview 
```

#### <a name="example"></a>예제

다음 단계를 수행합니다.

1. 위의 본문 구문을 `body_ua.json` 파일로 수정합니다. 로컬 컴퓨터 또는 Azure 스토리지 계정에 파일을 저장합니다.

1. 아래의 변수 값을 수정한 다음, 실행합니다.

    ```powershell
    $file = "path\body_ua.json"
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
    $response = Invoke-RestMethod -Uri $URI -Method PATCH -Headers $authHeader -Body $body
    
    # Review output
    $response.identity | ConvertTo-Json
    ```

    출력은 다음과 비슷해야 합니다.

    ```json
    {
    "type": "SystemAssigned, UserAssigned",
    "principalId": "00000000-0000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "userAssignedIdentities":  {
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI1":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    },
        "/subscriptions/ContosoID/resourcegroups/ContosoLab/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ContosoUAMI2":  {
                "PrincipalId":  "00000000-0000-0000-0000-000000000000",
                "ClientId":  "00000000-0000-0000-0000-000000000000"
                    }
        }
    }
    ```

### <a name="add-using-an-arm-template"></a>ARM 템플릿 사용 추가

구문 및 예제 단계는 아래에 나와 있습니다.

#### <a name="template-syntax"></a>템플릿 구문

아래 샘플 템플릿 구문은 아직 사용하도록 설정되지 않은 경우 시스템 할당 관리 ID를 사용하도록 설정하고 기존 Automation 계정에 2개의 기존 사용자 할당 관리 ID를 할당합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccountName": {
     "defaultValue": "YourAutomationAccount",
      "type": "String",
      "metadata": {
        "description": "Automation account name"
      }
    },
    "userAssignedOne": {
     "defaultValue": "userAssignedOne",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      },
    "userAssignedTwo": {
     "defaultValue": "userAssignedTwo",
      "type": "String",
      "metadata": {
        "description": "User-assigned managed identity"
      }
      }
   },
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "[parameters('automationAccountName')]",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned, UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedOne'))]": {},
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('userAssignedTwo'))]": {}
        }
      },
      "properties": {
        "sku": {
          "name": "Basic"
        },
        "encryption": {
          "keySource": "Microsoft.Automation",
          "identity": {}
        }
      }
    }
  ]
}
```

#### <a name="example"></a>예제

다음 단계를 수행합니다.

1. 템플릿을 복사하여 `template_ua.json` 파일에 붙여 넣습니다. 로컬 컴퓨터 또는 Azure 스토리지 계정에 파일을 저장합니다.

1. 아래의 변수 값을 수정한 다음, 실행합니다.

    ```powershell
    $templateFile = "path\template_ua.json"
    ```

1. PowerShell cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용하여 템플릿을 배포합니다.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "UserAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $automationAccount `
        -userAssignedOne $userAssignedOne `
        -userAssignedTwo $userAssignedTwo
    ```

   명령이 출력을 생성하지 않습니다. 그러나 아래 코드를 사용하여 확인할 수 있습니다.

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

    출력은 위의 REST API 예제에 대해 표시되는 출력과 비슷합니다.

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>토큰을 얻어 Azure 리소스에 대한 ID 액세스 가져오기

Automation 계정은 해당 사용자 할당 관리 ID를 사용하여 Azure Key Vault와 같이 Azure AD로 보호되는 다른 리소스에 액세스할 수 있는 토큰을 가져올 수 있습니다. 이러한 토큰은 애플리케이션의 특정 사용자를 나타내지 않고 리소스에 액세스하는 애플리케이션을 나타냅니다. 예를 들어 이 경우 토큰은 Automation 계정을 나타냅니다.

사용자 할당 관리 ID를 인증에 사용하려면 먼저 ID를 사용하려는 Azure 리소스에서 해당 ID에 대한 액세스 권한을 설정합니다. 이 작업을 완료하려면 대상 Azure 리소스에서 해당 ID에 적절한 역할을 할당합니다.

최소 권한의 원칙을 따르고 Runbook을 실행하는 데 필요한 권한만 신중하게 할당합니다. 예를 들어 Automation 계정이 Azure VM을 시작하거나 중지하는 데만 필요한 경우 실행 계정 또는 관리 ID에 VM을 시작하거나 중지하는 권한만 할당해야 합니다. 마찬가지로, Runbook이 Blob Storage에서 읽는 경우 읽기 전용 권한을 할당합니다.

이 예제에서는 Azure PowerShell을 사용하여 구독의 기여자 역할을 대상 Azure 리소스에 할당하는 방법을 보여줍니다. 기여자 역할은 예제이며 사용자의 경우에는 필요할 수도 있고 필요하지 않을 수도 있습니다. 또는 [Azure Portal](../role-based-access-control/role-assignments-portal.md)에서 대상 Azure 리소스에 역할을 할당할 수도 있습니다.

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-user-assigned-managed-identity"></a>사용자 할당 관리 ID로 액세스 인증

Automation 계정에 사용자 할당 관리 ID를 사용 설정하고 대상 리소스에 ID 액세스를 부여하면, 관리 ID를 지원하는 리소스를 대상으로 해당 ID를 Runbook에서 지정할 수 있습니다. ID를 지원하려면 Az cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)를 사용합니다.

```powershell
Connect-AzAccount -Identity `
    -AccountId <user-assigned-identity-ClientId> 
```

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Azure cmdlet을 사용 하지 않고 액세스 토큰 생성

HTTP 엔드포인트의 경우에는 다음을 확인합니다.
- 메타데이터 헤더가 있고 ‘true’로 설정되어야 합니다.
- 리소스는 GET 요청에 대한 쿼리 매개 변수로서, 그리고 POST 요청에 대한 양식 데이터로서 요청과 함께 전달되어야 합니다.
- Post 요청에 대한 콘텐츠 형식은 `application/x-www-form-urlencoded`여야 합니다.

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-get"></a>Http Get을 사용하여 사용자 할당 관리 ID에 대한 액세스 토큰 가져오기  

```powershell
$resource= "?resource=https://management.azure.com/"
$client_id="&client_id=<ClientId of USI>"
$url = $env:IDENTITY_ENDPOINT + $resource + $client_id 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"  
$Headers.Add("Metadata", "True")
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token 
```

### <a name="get-access-token-for-user-assigned-managed-identity-using-http-post"></a>Http Post를 사용하여 사용자 할당 관리 ID에 대한 액세스 토큰 가져오기

```powershell
$url = $env:IDENTITY_ENDPOINT
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Metadata", "True")
$body = @{'resource'='https://management.azure.com/' 
'client_id'='<ClientId of USI>'}
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body
Write-Output $accessToken.access_token 
```

### <a name="using-user-assigned-managed-identity-in-azure-powershell"></a>Azure PowerShell에서 사용자 할당 관리 ID 사용

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity -AccountId <ClientId of USI>"  
Connect-AzAccount -Identity -AccountId <ClientId of USI> 
Write-Output "Successfully connected with Automation account's Managed Identity"  
Write-Output "Trying to fetch value from key vault using User Assigned Managed identity. Make sure you have given correct access to Managed Identity"  
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>'  
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)  
try {  
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)  
    Write-Output $secretValueText  
} finally {  
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)  
} 
```

### <a name="using-user-assigned-managed-identity-in-python-runbook"></a>Python Runbook에서 사용자 할당 관리 ID 사용

```python
#!/usr/bin/env python3  
import os  
import requests   

resource = "?resource=https://management.azure.com/" 
client_id = "&client_id=<ClientId of USI>" 
endPoint = os.getenv('IDENTITY_ENDPOINT')+ resource +client_id 
payload={}  
headers = {  
  'Metadata': 'True'  
}  
response = requests.request("GET", endPoint, headers=headers, data=payload)  
print(response.text) 
```

## <a name="next-steps"></a>다음 단계

- Runbook이 성공적으로 완료되지 않으면 [Azure Automation 관리 ID 문제 해결(미리 보기)](troubleshoot/managed-identity.md)을 검토합니다.

- 관리 ID를 사용하지 않도록 설정해야 한다면, [Azure Automation 계정 관리 ID 사용 안 함(미리 보기)](disable-managed-identity-for-automation.md)을 참조하세요.

- Azure Automation 계정 보안에 대한 개요는 [Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.
