---
title: Azure Automation Runbook에서 이메일 보내기
description: 이 문서에서는 Runbook 내에서 이메일을 보내는 방법에 대해 알아봅니다.
services: automation
ms.subservice: process-automation
ms.date: 09/21/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c5f89a755cecd2dcf36d3a8a41711fe941108ffa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427269"
---
# <a name="send-an-email-from-am-automation-runbook"></a>Am Automation Runbook에서 이메일 보내기

PowerShell을 통해 [SendGrid](https://sendgrid.com/solutions)를 사용하여 Runbook에서 이메일을 보낼 수 있습니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* [SendGrid 계정](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account)
* SendGrid 발신자 확인. [도메인 또는 단일 보낸 사람](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/) 
* [SendGrid API 키](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key)입니다.

* 사용자 할당 관리 ID가 하나 이상 있는 Azure Automation 계정. 자세한 내용은 [관리 ID 사용](./quickstarts/enable-managed-identity.md)을 참조하세요.
* Automation 계정으로 가져온 Az 모듈 `Az.Accounts` 및 `Az.KeyVault`. 자세한 내용은 [Az 모듈 가져오기](./shared-resources/modules.md#import-az-modules)를 참조하세요.
* 로컬 컴퓨터에 설치된 [Azure Az PowerShell 모듈](/powershell/azure/new-azureps-module-az)입니다. 설치 또는 업그레이드하려면 [Azure Az PowerShell 모듈을 설치하는 방법](/powershell/azure/install-az-ps)을 참조하세요.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

자격 증명이 지정된 키 자격 증명 모음에서 키 자격 증명 모음 비밀을 얻고 설정할 수 있도록 하는 Azure Key Vault 및 Key Vault [액세스 정책을](../key-vault/general/assign-access-policy-portal.md) 만듭니다.

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
    $resourceGroup = "<Resource group>"
    $automationAccount = "<Automation account>"
    $region = "<Region>"
    $SendGridAPIKey = "<SendGrid API key>"
    $VaultName = "<A universally unique vault name>"

    $userAssignedManagedIdentity = "<User-assigned managed identity>"
    ```

1. Key Vault 만들기 및 권한 할당

    ```powershell
    # Create the new key vault
    $newKeyVault = New-AzKeyVault `
        -VaultName $VaultName `
        -ResourceGroupName $resourceGroup `
        -Location $region

    $resourceId = $newKeyVault.ResourceId
    
    # Convert the SendGrid API key into a SecureString
    $Secret = ConvertTo-SecureString -String $SendGridAPIKey `
        -AsPlainText -Force

    Set-AzKeyVaultSecret -VaultName $VaultName `
        -Name 'SendGridAPIKey' `
        -SecretValue $Secret
    
    # Grant Key Vault access to the Automation account's system-assigned managed identity.
    $SA_PrincipalId = (Get-AzAutomationAccount `
        -ResourceGroupName $resourceGroup `
        -Name $automationAccount).Identity.PrincipalId

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $SA_PrincipalId `
        -PermissionsToSecrets Set, Get

    # Grant Key Vault access to the user-assigned managed identity.
    $UAMI = Get-AzUserAssignedIdentity `
        -ResourceGroupName $resourceGroup `
        -Name $userAssignedManagedIdentity

    Set-AzKeyVaultAccessPolicy `
        -VaultName $vaultName `
        -ObjectId $UAMI.PrincipalId `
        -PermissionsToSecrets Set, Get
    ```

   Azure Key Vault를 만들고 비밀을 저장하는 다른 방법은 [Key Vault 빠른 시작](../key-vault/index.yml)을 참조하세요.

## <a name="assign-permissions-to-managed-identities"></a>관리 ID에 권한 할당

적절한 관리 ID 에 사용 권한을 [할당합니다.](./automation-security-overview.md#managed-identities) Runbook은 Automation 계정 시스템 할당 관리 ID 또는 사용자 할당 관리 ID를 사용할 수 있습니다. 각 ID에 권한을 할당하는 단계가 제공됩니다. 아래 단계에서는 PowerShell을 사용합니다. 포털을 사용하려면 Azure Portal [사용하여 Azure 역할 할당을](./../role-based-access-control/role-assignments-portal.md)참조하세요.

1. PowerShell cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용하여 시스템 할당 관리 ID에 역할을 할당합니다.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $SA_PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. 사용자 할당 관리 ID에 역할을 할당합니다.

    ```powershell
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId`
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. 시스템 할당 관리 ID의 경우 `ClientId` 나중에 사용할 값을 표시하고 기록합니다.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-the-runbook-to-send-an-email"></a>이메일을 보내는 Runbook 만들기

Key Vault 만들고 API 키를 저장한 후에는 `SendGrid` API 키를 검색하고 이메일을 보내는 Runbook을 만들어야 합니다. [시스템 할당 관리 ID를](./automation-security-overview.md#managed-identities) 사용하여 Azure에서 인증하는 Runbook을 사용하여 Azure Key Vault 비밀을 검색해 보겠습니다. Runbook **Send-GridMailMessage** 를 호출합니다. 다양한 시나리오에 사용되는 PowerShell 스크립트를 수정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Automation 계정으로 이동합니다.

1. 열려 있는 Automation 계정 페이지의 **프로세스 자동화** 에서 **Runbook** 을 선택합니다.

1. **+ Runbook 만들기** 를 선택합니다.
    1. Runbook 이름을 `Send-GridMailMessage`로 지정합니다.
    1. **Runbook 유형** 드롭다운 목록에서 **PowerShell** 을 선택합니다.
    1. **만들기** 를 선택합니다.

   ![Runbook 만들기](./media/automation-send-email/automation-send-email-runbook.png)

1. Runbook이 만들어지고 PowerShell Runbook 편집 페이지가 열립니다.
   ![Runbook 편집](./media/automation-send-email/automation-send-email-edit.png)

1. 다음 PowerShell 예제를 편집 페이지에 복사합니다. `VaultName`에서 Key Vault에 대해 선택한 이름을 지정하는지 확인합니다.

    ```powershell
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content,
      [Parameter(Mandatory=$True)]
      [String] $ResourceGroupName
    )

    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context

    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext 

    $VaultName = "<Enter your vault name>"

    $SENDGRID_API_KEY = Get-AzKeyVaultSecret `
        -VaultName $VaultName `
        -Name "SendGridAPIKey" `
        -AsPlainText -DefaultProfile $AzureContext

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

1. 시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.
    1. 줄 18에서 를 제거합니다. `$AzureContext = (Connect-AzAccount -Identity).context`
    1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
    1. 앞에서 얻은 클라이언트 ID를 입력합니다.

1. **저장,** **게시를** 선택한 다음, 메시지가 표시되면 **예를** 선택합니다.

Runbook이 성공적으로 실행되는지 확인하려면 [Runbook 테스트](manage-runbooks.md#test-a-runbook) 또는 [Runbook 시작](start-runbooks.md) 아래의 단계를 수행할 수 있습니다.

처음에 테스트 이메일이 보이지 않으면 **정크** 및 **스팸** 폴더를 확인하세요.

## <a name="clean-up-resources"></a>리소스 정리

1. Runbook이 더 이상 필요하지 않은 경우 Runbook 목록에서 Runbook을 선택하고 **삭제를** 선택합니다.

1. [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault) cmdlet을 사용하여 Key Vault를 삭제합니다.

    ```powershell
    $VaultName = "<your KeyVault name>"
    $resourceGroup = "<your ResourceGroup name>"
    Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $resourceGroup
    ```

## <a name="next-steps"></a>다음 단계

* Runbook 작업 데이터를 Log Analytics 작업 영역에 보내려면 [Azure Monitor 로그에 Azure Automation 작업 데이터 전달](automation-manage-send-joblogs-log-analytics.md)을 참조하세요.
* 기본 수준 메트릭과 로그를 모니터링하려면 [경고를 사용하여 Azure Automation Runbook 트리거](automation-create-alert-triggered-runbook.md)를 참조하세요.
