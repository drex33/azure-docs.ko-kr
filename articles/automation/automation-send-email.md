---
title: Azure Automation Runbook에서 이메일 보내기
description: 이 문서에서는 Runbook 내에서 이메일을 보내는 방법에 대해 알아봅니다.
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 622bff79d48ae707e2b32556e05dad658a0322bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528464"
---
# <a name="send-an-email-from-a-runbook"></a>Runbook에서 이메일 보내기

PowerShell을 통해 [SendGrid](https://sendgrid.com/solutions)를 사용하여 Runbook에서 이메일을 보낼 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)할 수 있습니다.
* [SendGrid 계정](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-sendgrid-account)
* Send Grid에서 보낸 사람 확인이 구성되었습니다. [도메인 또는 단일 보낸 사람](https://sendgrid.com/docs/for-developers/sending-email/sender-identity/) 
* **Az** 모듈이 있는 [Automation 계정](./index.yml)
* Runbook을 저장하고 실행하는 [실행 계정](./automation-security-overview.md#run-as-accounts)

## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

다음 PowerShell 스크립트를 사용하여 Azure Key Vault를 만들 수 있습니다. 변수 값을 사용자 환경에 맞는 값으로 바꿉니다. 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보기** 단추를 통해 포함된 Azure Cloud Shell을 사용합니다. [Az 모듈](/powershell/azure/install-az-ps)이 로컬 머신에 설치되어 있는 경우 로컬에서 코드를 복사하고 실행할 수도 있습니다. 또한 이 스크립트는 실행 계정이 지정된 키 자격 증명 모음에서 키 자격 증명 모음 비밀을 가져오고 설정할 수 있도록 하는 [Key Vault 액세스 정책](../key-vault/general/assign-access-policy-portal.md)을 만듭니다.

> [!NOTE]
> API 키를 검색하려면 [SendGrid API 찾기](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#to-find-your-sendgrid-api-key)에 있는 단계를 사용합니다.

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Azure Key Vault를 만들고 비밀을 저장하는 다른 방법은 [Key Vault 빠른 시작](../key-vault/index.yml)을 참조하세요.

## <a name="import-required-modules-into-your-automation-account"></a>Automation 계정에 필요한 모듈 가져오기

Runbook 내에서 Azure Key Vault를 사용하려면 Automation 계정으로 다음 모듈을 가져와야 합니다.

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

자세한 내용은 [Az 모듈 가져오기](shared-resources/modules.md#import-az-modules)를 참조하세요.

## <a name="create-the-runbook-to-send-an-email"></a>이메일을 보내는 Runbook 만들기

Key Vault를 만들고 `SendGrid` API 키를 저장한 후에는 API 키를 검색하고 이메일을 보내는 Runbook을 만들 수 있습니다. `AzureRunAsConnection`을 [실행 계정](./automation-security-overview.md#run-as-accounts)으로 사용하여 Azure Key Vault에서 비밀을 검색하도록 Azure를 인증하는 Runbook을 사용해봅니다. Runbook **Send-GridMailMessage** 를 호출합니다. 예제 목적으로 사용되는 PowerShell 스크립트를 수정하여 다른 시나리오에서 재사용할 수 있습니다.

1. Azure Automation 계정으로 이동합니다.
2. **프로세스 자동화** 아래에서 **Runbook** 을 선택합니다.
3. Runbook의 목록 맨 위에서 **+ Runbook 만들기** 를 선택합니다.
4. Runbook 추가 페이지에서 Runbook 이름에 **Send-GridMailMessage** 를 입력합니다. Runbook 형식으로 **PowerShell** 을 선택합니다. 그런 다음 **만들기** 를 선택합니다.
   ![Runbook 만들기](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook이 만들어지고 PowerShell Runbook 편집 페이지가 열립니다.
   ![Runbook 편집](./media/automation-send-email/automation-send-email-edit.png)
6. 다음 PowerShell 예제를 편집 페이지에 복사합니다. `VaultName`에서 Key Vault에 대해 선택한 이름을 지정하는지 확인합니다.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey" -AsPlainText
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

7. **게시** 를 선택하여 Runbook을 저장하고 게시합니다.

Runbook이 성공적으로 실행되는지 확인하려면 [Runbook 테스트](manage-runbooks.md#test-a-runbook) 또는 [Runbook 시작](start-runbooks.md) 아래의 단계를 수행할 수 있습니다.

처음에 테스트 이메일이 보이지 않으면 **정크** 및 **스팸** 폴더를 확인하세요.

## <a name="clean-up-resources-after-the-email-operation"></a>이메일 작업 후 리소스 정리

1. Runbook이 더 이상 필요하지 않은 경우 Runbook 목록에서 선택하고 **삭제** 를 클릭합니다.

2. [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault) cmdlet을 사용하여 Key Vault를 삭제합니다.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

* Runbook 작업 데이터를 Log Analytics 작업 영역에 보내려면 [Azure Monitor 로그에 Azure Automation 작업 데이터 전달](automation-manage-send-joblogs-log-analytics.md)을 참조하세요.
* 기본 수준 메트릭과 로그를 모니터링하려면 [경고를 사용하여 Azure Automation Runbook 트리거](automation-create-alert-triggered-runbook.md)를 참조하세요.
* Runbook 작업 중 발생하는 문제를 해결하려면 [Runbook 문제 해결](./troubleshoot/runbooks.md)을 참조하세요.