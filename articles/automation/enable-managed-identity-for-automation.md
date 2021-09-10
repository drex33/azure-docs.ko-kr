---
title: Azure Automation 계정에 대해 시스템 할당 관리 ID 사용(미리 보기)
description: 이 문서에서는 Azure Automation 계정에 관리 ID를 설정하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 07/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6d381abcc13a5b91d32b4e444e01909c83300e7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528740"
---
# <a name="using-a-system-assigned-managed-identity-for-an-azure-automation-account-preview"></a>Azure Automation 계정에 대해 시스템 할당 관리 ID 사용(미리 보기)

이 문서에서는 Azure Automation 계정에 시스템 할당 관리 ID를 만드는 방법과 이를 사용하여 다른 리소스에 액세스하는 방법을 보여줍니다. 관리 ID가 Azure Automation에서 작동하는 방법에 대한 자세한 내용은 [관리 ID](automation-security-overview.md#managed-identities-preview)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Automation 계정. 자세한 내용은 [Azure Automation 계정 만들기](automation-quickstart-create-account.md)를 참조하세요.

- 최신 버전의 Azure 계정 모듈. 현재 최신 버전은 2.2.8입니다. (이 버전에 대한 자세한 내용은 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/)를 참조하세요.)

- Automation Runbook에서 액세스할 Azure 리소스입니다. 이 리소스에는 Automation Runbook에서 리소스에 대한 액세스를 인증하는 데 도움이 되는 관리 ID에 정의된 역할이 있어야 합니다. 역할을 추가하려면 대응하는 Azure AD 테넌트 리소스의 소유자여야 합니다.

- 관리 ID를 사용하여 하이브리드 작업을 실행하려는 경우에는 Hybrid Runbook Worker를 최신 버전으로 업데이트합니다. 필요한 최소 버전:

  - Windows Hybrid Runbook Worker: 버전 7.3.1125.0
  - Linux Hybrid Runbook Worker: 버전 1.7.4.0

## <a name="enable-a-system-assigned-managed-identity-for-an-azure-automation-account"></a>Azure Automation 계정에 대해 시스템 할당 관리 ID 사용

사용하도록 설정되면 시스템 할당 관리 ID에 다음 속성이 할당됩니다.

|속성(JSON) | 값 | Description|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Azure AD 테넌트에서 Automation 계정을 나타내는 시스템 할당 관리 ID에 대한 서비스 주체 개체의 GUID(Globally Unique Identifier)입니다. 이 GUID는 ‘개체 ID’ 또는 objectID로 표시되기도 합니다. |
| tenantid | \<Azure-AD-tenant-ID\> | Automation 계정이 현재 멤버로 속해 있는 Azure AD 테넌트를 나타내는 GUID(Globally Unique Identifier)입니다. Azure AD 테넌트 내부에서 서비스 주체는 Automation 계정과 이름이 같습니다. |

Azure Portal, PowerShell, Azure REST API 또는 ARM 템플릿을 사용하여 Azure Automation 계정에 대해 시스템 할당 관리 ID를 사용하도록 설정할 수 있습니다. PowerShell과 관련된 예제의 경우 먼저 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount -Identity
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

그런 다음, 예제 전체에서 사용할 변수 세트를 초기화합니다. 아래 값을 수정한 다음, 실행합니다.

```powershell
$subscriptionID = "subscriptionID"
$resourceGroup = "resourceGroupName"
$automationAccount = "automationAccountName"
```

> [!IMPORTANT]
> 새 Automation 계정 수준 ID는 이전 VM 수준 시스템이 할당한 ID를 재정의하며 이는 [관리 ID를 통한 Runbook 인증 사용](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)에 자세히 설명되어 있습니다. VM의 시스템 할당 ID를 이용해 Runbook 리소스에 액세스하는 하이브리드 작업을 Azure VM에서 실행한다면, 하이브리드 작업에는 Automation 계정 ID를 사용합니다. 따라서 Automation 계정의 고객 관리형 키(CMK) 기능을 사용 하면 기존 작업 실행이 영향을 받을 수 있습니다.<br/><br/>VM의 관리 ID를 계속 사용하고 싶다면 Automation 계정 수준 ID를 사용 설정하지 않아야 합니다. 이미 사용 설정한 경우 Automation 계정 시스템 할당 관리 ID를 사용하지 않도록 설정할 수 있습니다. [Azure Automation 계정 관리형 ID 사용 안 함](./disable-managed-identity-for-automation.md)을 참조하세요.

### <a name="enable-using-the-azure-portal"></a>Azure Portal을 사용하도록 설정

다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.

1.  **계정 설정** 에서  **ID** 를 선택합니다.

1. **시스템 할당** 옵션을 **On** 으로 설정하고 **저장** 을 누릅니다. 확인하라는 메시지가 나타나면 **예** 를 선택합니다.

   :::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Azure Portal에서 시스템이 할당한 ID 사용.":::

   이제 Automation 계정에서 시스템이 할당한 ID를 사용할 수 있습니다. ID는 Azure Active Directory(Azure AD)에 등록되고 개체 ID로 표시됩니다.

   :::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="관리 ID 개체 ID.":::

### <a name="enable-using-powershell"></a>PowerShell을 사용하여 사용

PowerShell cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount)를 사용하여 시스템 할당 관리 ID를 사용하도록 설정합니다.

```powershell
$output = Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity

$output
```

출력은 다음과 비슷해야 합니다.

:::image type="content" source="media/enable-managed-identity-for-automation/set-azautomationaccount-output.png" alt-text="set-azautomationaccount 명령의 출력입니다.":::

추가 출력의 경우 `$output.identity | ConvertTo-Json`을 실행합니다.

### <a name="enable-using-a-rest-api"></a>REST API를 사용하도록 설정

구문 및 예제 단계는 아래에 나와 있습니다.

#### <a name="syntax"></a>Syntax

아래 본문 구문은 HTTP **PATCH** 메서드를 사용하여 기존 Automation 계정에 시스템 할당 관리 ID를 사용하도록 설정합니다. 그러나 이 구문은 Automation 계정과 연결된 기존 사용자 할당 관리 ID를 제거합니다.

```json
{ 
 "identity": { 
   "type": "SystemAssigned" 
  } 
}
```

여러 사용자 할당 ID가 정의된 경우 이를 유지하고 시스템 할당 ID만 제거하려면 쉼표로 구분된 목록을 사용하여 각 사용자 할당 ID를 지정해야 합니다. 아래 예제에서는 HTTP **PATCH** 메서드를 사용합니다.

```json
{ 
  "identity" : {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID": {},
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/cmkID2": {}
    }
  }
}

```

API의 구문은 다음과 같습니다.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

#### <a name="example"></a>예

다음 단계를 수행합니다.

1. 본문 구문을 복사하여 `body_sa.json`이라는 파일에 붙여넣습니다. 로컬 컴퓨터 또는 Azure 스토리지 계정에 파일을 저장합니다.

1. 아래의 변수 값을 업데이트한 다음, 실행합니다.

    ```powershell
    $file = "path\body_sa.json"
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
        "PrincipalId":  "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "TenantId":  "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "Type":  0,
        "UserAssignedIdentities":  null
    }
    ```

### <a name="enable-using-an-arm-template"></a>ARM 템플릿을 사용하도록 설정

구문 및 예제 단계는 아래에 나와 있습니다.

#### <a name="template-syntax"></a>템플릿 구문

아래 샘플 템플릿 구문은 기존 Automation 계정에 시스템 할당 관리 ID를 사용하도록 설정합니다. 그러나 이 구문은 Automation 계정과 연결된 기존 사용자 할당 관리 ID를 제거합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2020-01-13-preview",
      "name": "yourAutomationAccount",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
        },
      "properties": {
        "sku": {
          "name": "Basic"
        }
      }
    }
  ]
}
```

#### <a name="example"></a>예

다음 단계를 수행합니다.

1. 위의 템플릿 구문을 수정하여 Automation 계정을 사용하고 `template_sa.json`이라는 파일에 저장합니다.

1. 아래의 변수 값을 업데이트한 다음, 실행합니다.

    ```powershell
    $templateFile = "path\template_sa.json"
    ```

1. PowerShell cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용하여 템플릿을 배포합니다.

    ```powershell
    New-AzResourceGroupDeployment `
        -Name "SystemAssignedDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile
    ```

   명령이 출력을 생성하지 않습니다. 그러나 아래 코드를 사용하여 확인할 수 있습니다.

    ```powershell
    (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity | ConvertTo-Json
    ```

   출력은 위의 REST API 예제에 대해 표시되는 출력과 비슷합니다.

## <a name="give-access-to-azure-resources-by-obtaining-a-token"></a>토큰을 가져와 Azure 리소스에 대한 액세스 권한 부여

Automation 계정은 해당 시스템 할당 관리 ID를 사용하여 Azure Key Vault와 같이 Azure AD로 보호되는 다른 리소스에 액세스할 수 있는 토큰을 가져올 수 있습니다. 이러한 토큰은 애플리케이션의 특정 사용자를 나타내지 않고 리소스에 액세스하는 애플리케이션을 나타냅니다. 예를 들어 이 경우 토큰은 Automation 계정을 나타냅니다.

시스템이 할당한 관리 ID를 인증에 사용하려면 먼저 ID를 사용하려는 Azure 리소스에서 해당 ID에 대한 액세스 권한을 설정합니다. 이 작업을 완료하려면 대상 Azure 리소스에서 해당 ID에 적절한 역할을 할당합니다.

이 예제에서는 Azure PowerShell을 사용하여 구독의 기여자 역할을 대상 Azure 리소스에 할당하는 방법을 보여줍니다. 기여자 역할은 예시이며 사용자의 경우에는 필요하지 않을 수도 있습니다.

```powershell
New-AzRoleAssignment `
    -ObjectId <automation-Identity-object-id> `
    -Scope "/subscriptions/<subscription-id>" `
    -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-system-assigned-managed-identity"></a>시스템 할당 관리 ID로 액세스 인증

Automation 계정에 관리 ID를 사용 설정하고 대상 리소스에 ID 액세스를 부여하면, 관리 ID를 지원하는 리소스를 대상으로 해당 ID를 Runbook에서 지정할 수 있습니다. ID를 지원하려면 Az cmdlet `Connect-AzAccount` cmdlet를 사용합니다. PowerShell 참조에서 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)를 참조하세요. `SubscriptionID`를 실제 구독 ID로 바꾸고 다음 명령을 실행합니다.

```powershell
Connect-AzAccount -Identity
$AzureContext = Set-AzContext -SubscriptionId "SubscriptionID"
```

> [!NOTE]
> 조직에서 아직 전용 AzureRM cmdlet을 사용한다면 `Connect-AzureRMAccount -Identity`를 사용할 수 있습니다.

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Azure cmdlet을 사용 하지 않고 액세스 토큰 생성

HTTP 엔드포인트의 경우에는 다음을 확인합니다.

- 메타데이터 헤더가 있고 ‘true’로 설정되어야 합니다.
- 리소스는 GET 요청에 대한 쿼리 매개 변수로서, 그리고 POST 요청에 대한 양식 데이터로서 요청과 함께 전달되어야 합니다.
- X-IDENTITY-HEADER는 Hybrid Runbook Worker용 환경 변수인 IDENTITY_HEADER의 값으로 설정되어야 합니다.
- Post 요청의 콘텐츠 형식은 'application/x-www-form-urlencoded'여야 합니다.

### <a name="get-access-token-for-system-assigned-identity-using-http-get"></a>HTTP Get을 사용하여 시스템 할당 ID에 대한 액세스 토큰 가져오기

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="get-access-token-for-system-assigned-identity-using-http-post"></a>HTTP Post를 사용하여 시스템 할당 ID에 대한 액세스 토큰 가져오기

```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

### <a name="using-system-assigned-managed-identity-in-azure-powershell"></a>Azure PowerShell에서 시스템 할당 관리 ID 사용

자세한 내용은 [AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret)을 참조하세요.

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="using-system-assigned-managed-identity-in-python-runbook"></a>Python Runbook에서 시스템 할당 관리 ID 사용

```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

### <a name="using-system-assigned-managed-identity-to-access-sql-database"></a>시스템 할당 관리 ID를 사용하여 SQL Database에 액세스

Azure SQL 데이터베이스에 대한 액세스를 프로비저닝하는 방법에 대한 자세한 내용은 [Azure AD 관리자(SQL Database) 프로비저닝](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)을 참조하세요.

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

## <a name="next-steps"></a>다음 단계

- Runbook이 성공적으로 완료되지 않으면 [Azure Automation 관리 ID 문제 해결(미리 보기)](troubleshoot/managed-identity.md)을 검토합니다.

- 관리 ID를 사용하지 않도록 설정해야 한다면, [Azure Automation 계정 관리 ID 사용 안 함(미리 보기)](disable-managed-identity-for-automation.md)을 참조하세요.

- Azure Automation 계정 보안에 대한 개요는 [Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.
