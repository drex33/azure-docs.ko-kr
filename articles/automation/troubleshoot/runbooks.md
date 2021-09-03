---
title: Azure Automation Runbook 문제 해결
description: 이 문서에서는 Azure Automation Runbook과 관련된 문제를 해결하는 방법을 설명합니다.
services: automation
ms.date: 07/27/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: a7711d30a71cc5b637a1fc755609d3f5c48683d8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528802"
---
# <a name="troubleshoot-runbook-issues"></a>Runbook 문제 해결

 이 문서에서는 발생할 수 있는 Runbook 문제 및 이를 해결하는 방법에 대해 설명합니다. 일반 정보는 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)을 참조하세요.

## <a name="diagnose-runbook-issues"></a>Runbook 문제 진단

Azure Automation에서 Runbook을 실행하는 동안 오류가 발생하면 다음 단계를 수행하여 문제를 진단할 수 있습니다.

1. Runbook 스크립트가 로컬 머신에서 성공적으로 실행되는지 확인합니다.

    언어 참조 및 학습 모듈은 [PowerShell 문서](/powershell/scripting/overview) 또는 [Python 문서](https://docs.python.org/3/)를 참조하세요. 스크립트를 로컬로 실행하면 다음과 같은 일반적인 오류를 검색하고 해결할 수 있습니다.

      * 누락된 모듈
      * 구문 오류
      * 논리 오류

1. Runbook [오류 스트림](../automation-runbook-output-and-messages.md#runbook-output)을 조사합니다.

    이러한 스트림에서 특정 메시지를 확인하고 이 문서에서 설명하는 오류와 비교합니다.

1. 노드와 Automation 작업 영역에 필요한 모듈이 있는지 확인합니다.

    Runbook에서 모듈을 가져오는 경우 [모듈 가져오기](../shared-resources/modules.md#import-modules)의 단계를 사용하여 해당 모듈을 Automation 계정에 사용할 수 있는지 확인합니다. [Azure Automation에서 Azure PowerShell 모듈 업데이트](../automation-update-azure-modules.md)의 지침에 따라 PowerShell 모듈을 최신 버전으로 업데이트합니다. 자세한 문제 해결 정보는 [모듈 문제 해결](shared-resources.md#modules)을 참조하세요.

1. Runbook이 일시 중단되거나 예기치 않게 실패하는 경우,

    * 실행 계정이 만료되었으면 [인증서를 갱신](../manage-runas-account.md#cert-renewal)합니다.
    * 만료된 웹후크를 사용하여 Runbook을 시작하려면 [웹후크를 갱신](../automation-webhooks.md#update-a-webhook)합니다.
    * 현재 Runbook 상태 및 문제의 가능한 원인을 확인하려면 [작업 상태를 확인](../automation-runbook-execution.md#job-statuses)합니다.
    * Runbook이 일시 중단되기 전에 수행되는 작업을 식별하려면 [추가 출력을 Runbook에 추가](../automation-runbook-output-and-messages.md#working-with-message-streams)합니다.
    * 작업에서 throw된 [예외를 처리](../automation-runbook-execution.md#exceptions)합니다.

1. Runbook 작업 또는 Hybrid Runbook Worker의 환경에서 응답하지 않는 경우 이 단계를 수행합니다.

    Azure Automation 대신 Hybrid Runbook Worker에서 Runbook을 실행하는 경우 [하이브리드 작업자 자체의 문제를 해결](hybrid-runbook-worker.md)해야 할 수도 있습니다.

## <a name="scenario-access-blocked-to-azure-storage-or-azure-key-vault-or-azure-sql"></a>시나리오: Azure Storage, Azure Key Vault 또는 Azure SQL에 대한 액세스가 차단됨

이 시나리오에서는 [Azure Storage](../../storage/common/storage-network-security.md)를 예로 사용합니다. 그러나 이 정보는 [Azure Key Vault](../../key-vault/general/network-security.md) 및 [Azure SQL](../../azure-sql/database/firewall-configure.md)에 동일하게 적용됩니다.

### <a name="issue"></a>문제

Runbook에서 Azure Storage에 액세스하려고 하면 다음 메시지와 유사한 오류가 발생합니다. `The remote server returned an error: (403) Forbidden. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation.`

### <a name="cause"></a>원인

Azure Storage의 Azure Firewall이 사용하도록 설정되어 있습니다.

### <a name="resolution"></a>해결 방법

[Azure Storage](../../storage/common/storage-network-security.md), [Azure Key Vault](../../key-vault/general/network-security.md) 또는 [Azure SQL](../../azure-sql/database/firewall-configure.md)에서 Azure Firewall을 사용하도록 설정하면 해당 서비스에 대한 Azure Automation Runbook의 액세스가 차단됩니다. Automation이 신뢰할 수 있는 서비스 목록에 포함되어 있지 않기 때문에, 신뢰할 수 있는 Microsoft 서비스를 허용하는 방화벽 예외가 사용하도록 설정된 경우에도 액세스가 차단됩니다. 방화벽을 사용하도록 설정한 경우에는 Hybrid Runbook Worker 및 [가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 통해서만 액세스할 수 있습니다.

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>시나리오: 403 권한 없음 또는 금지됨 오류로 인해 Runbook이 실패함

### <a name="issue"></a>문제

403 권한 없음 또는 금지됨 오류 또는 이와 동등한 오류로 인해 Runbook이 실패합니다.

### <a name="cause"></a>원인

실행 계정에 현재 Automation 계정과 동일한 Azure 리소스 권한이 없을 수 있습니다. 

### <a name="resolution"></a>해결 방법

실행 계정에 스크립트에 사용된 [리소스에 액세스할 수 있는 권한](../../role-based-access-control/role-assignments-portal.md)이 있는지 확인합니다.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>시나리오: Azure 계정에 로그인하지 못함

### <a name="issue"></a>문제

`Connect-AzAccount` cmdlet을 사용하면 다음 오류 중 하나가 표시됩니다.

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>원인

자격 증명 자산 이름이 유효하지 않으면 이러한 오류가 발생합니다. 또한 Automation 자격 증명 자산을 설정하는 데 사용한 사용자 이름과 암호가 유효하지 않은 경우에도 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

잘못된 부분을 확인하려면 다음 단계를 수행합니다.

1. 특수 문자를 포함하지 않았는지 확인합니다. 이러한 문자에는 Azure에 연결하는 데 사용하는 Automation 자격 증명 자산 이름의 `\@` 문자가 포함됩니다.
1. 로컬 PowerShell ISE 편집기에서 Azure Automation 자격 증명에 저장된 사용자 이름 및 암호를 사용할 수 있는지 확인합니다. PowerShell ISE에서 다음 cmdlet을 실행합니다.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. 인증이 로컬로 실패하면 Azure AD(Azure Active Directory) 자격 증명을 올바르게 설정하지 않은 것입니다. Azure AD 계정을 올바르게 설정하려면 [Azure Active Directory를 사용하여 Azure에 인증](../automation-use-azure-ad.md) 문서를 참조하세요.

1. 일시적인 오류로 표시되면 다시 시도 논리를 인증 루틴에 추가하여 인증을 더 강력하게 합니다.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>시나리오: Login-AzureRMAccount를 실행하여 로그인

### <a name="issue"></a>문제

Runbook을 실행하면 다음 오류가 표시됩니다.

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>원인

실행 계정을 사용하지 않거나 실행 계정이 만료되었으면 이 오류가 발생할 수 있습니다. 자세한 내용은 [Azure Automation 실행 계정 개요](../automation-security-overview.md#run-as-accounts)를 참조하세요.

이 오류에는 다음 두 가지 주요 원인이 있습니다.

* 다른 버전의 AzureRM 또는 Az 모듈이 있습니다.
* 별도의 구독에 있는 리소스에 액세스하려고 합니다.

### <a name="resolution"></a>해결 방법

하나의 AzureRM 또는 Az 모듈을 업데이트한 후에 이 오류가 발생하면 모든 모듈을 동일한 버전으로 업데이트합니다.

다른 구독의 리소스에 액세스하려면 다음 단계에 따라 권한을 구성합니다.

1. Automation 실행 계정으로 이동하여 **애플리케이션 ID** 및 **지문** 을 복사합니다.

    ![애플리케이션 ID 및 지문 복사](../media/troubleshoot-runbooks/collect-app-id.png)

1. Automation 계정이 호스팅되지 *않은* 구독의 **액세스 제어** 로 이동하여 새 역할 할당을 추가합니다.

    ![Access Control](../media/troubleshoot-runbooks/access-control.png)

1. 이전에 수집된 **애플리케이션 ID** 를 추가합니다. **기여자** 권한을 선택합니다.

    ![역할 할당 추가](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 구독 이름을 복사합니다.

1. 이제 다음 Runbook 코드를 사용하여 Automation 계정에서 다른 구독으로의 권한을 테스트할 수 있습니다. `<CertificateThumbprint>`를 1단계에서 복사한 값으로 바꿉니다. `"<SubscriptionName>"`을 4단계에서 복사한 값으로 바꿉니다.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>시나리오: Azure 구독을 찾을 수 없음

### <a name="issue"></a>문제

`Select-AzureSubscription`, `Select-AzureRMSubscription` 또는 `Select-AzSubscription` cmdlet을 사용하면 다음 오류가 표시됩니다.

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Error

이 오류가 발생할 수 있는 경우는 다음과 같습니다.

* 구독 이름이 잘못되었습니다.
* 구독 세부 정보를 가져오려는 Azure AD 사용자가 구독의 관리자로 구성되지 않았습니다.
* cmdlet을 사용할 수 없습니다.

### <a name="resolution"></a>해결 방법

다음 단계에 따라 Azure에서 인증되고 선택하려고 하는 구독에 액세스할 수 있는 권한이 있는지 확인합니다.

1. 스크립트가 독립 실행형으로 작동하는지 확인하기 위해 Azure Automation 외부에서 테스트합니다.
1. `Select-*` cmdlet을 실행하기 전에 스크립트에서 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 실행하는지 확인합니다.
1. Runbook의 시작 부분에 `Disable-AzContextAutosave -Scope Process`를 추가합니다. 이 cmdlet은 모든 자격 증명이 현재 Runbook의 실행에만 적용되도록 합니다.
1. 오류 메시지가 계속 표시되면 `Connect-AzAccount`에 대한 `AzContext` 매개 변수를 추가하여 코드를 수정한 다음, 코드를 실행합니다.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>시나리오: 여러 구독으로 처리하는 경우 Runbook 실패

### <a name="issue"></a>문제

Runbook을 실행하면 Runbook에서 Azure 리소스를 관리하지 못합니다.

### <a name="cause"></a>원인

Runbook이 실행 시 올바른 컨텍스트를 사용하지 않습니다. Runbook에서 실수로 잘못된 구독에 액세스하려고 했기 때문일 수 있습니다.

다음과 같은 오류가 표시될 수 있습니다.

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>해결 방법

Runbook에서 여러 Runbook을 호출하면 구독 컨텍스트가 손실될 수 있습니다. 실수로 잘못된 구독에 액세스하려고 하지 않도록 하려면 아래 지침을 따라야 합니다.

* 잘못된 구독을 참조하지 않으려면 각 Runbook을 시작할 때 다음 코드를 사용하여 Automation Runbook에서 컨텍스트 저장을 사용하지 않도록 설정합니다.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* Azure PowerShell cmdlet은 `-DefaultProfile` 매개 변수를 지원합니다. 동일한 프로세스에서 여러 PowerShell 스크립트를 실행하도록 지원하기 위해 모든 Az 및 AzureRm cmdlet에 추가되었습니다. 이를 통해 컨텍스트와 각 cmdlet에 사용할 구독을 지정할 수 있습니다. Runbook을 사용하면 Runbook을 만들 때(즉, 계정이 로그인할 때) 및 변경될 때마다 Runbook에 컨텍스트 개체를 저장하고 Az cmdlet을 지정할 때 컨텍스트를 참조해야 합니다.

   > [!NOTE]
   > [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) 또는 [Select-AzSubscription](/powershell/module/servicemanagement/azure.service/set-azuresubscription)과 같은 cmdlet을 사용하여 직접 컨텍스트를 조작하는 경우에도 컨텍스트 개체를 전달해야 합니다.

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>시나리오: 다단계 인증을 사용하도록 설정하여 Azure에 대한 인증이 실패함

### <a name="issue"></a>문제

Azure 사용자 이름 및 암호를 사용하여 Azure에 인증하면 다음 오류가 표시됩니다.

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>원인

Azure 계정에서 다단계 인증을 사용하면 Azure Active Directory 사용자를 사용하여 Azure에 인증할 수 없습니다. 대신 인증서 또는 서비스 주체를 사용하여 인증해야 합니다.

### <a name="resolution"></a>해결 방법

Azure 클래식 배포 모델 cmdlet과 함께 클래식 실행 계정을 사용하려면 [Azure 서비스를 관리하는 클래식 실행 계정 만들기](../automation-create-standalone-account.md#create-a-classic-run-as-account)를 참조하세요. Azure Resource Manager cmdlet에 서비스 주체를 사용하려면 [Azure Portal을 사용하여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 및 [Azure Resource Manager를 사용하여 서비스 주체 인증](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)을 참조하세요.

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>시나리오: "작업이 취소되었습니다." 오류 메시지로 인해 Runbook이 실패함

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
Exception: A task was canceled.
```

### <a name="cause"></a>원인

이 오류는 오래된 Azure 모듈을 사용하여 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류는 Azure 모듈을 최신 버전으로 업데이트하여 해결할 수 있습니다.

1. Automation 계정에서 **모듈**, **Azure 모듈 업데이트** 를 차례로 선택합니다.
1. 업데이트하는 데 약 15분 정도 걸립니다. 완료되면 실패한 Runbook을 다시 실행합니다.

모듈 업데이트에 대해 자세히 알아 보려면 [Azure Automation에서 Azure 모듈 업데이트](../automation-update-azure-modules.md)를 참조하세요.

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>시나리오: 용어가 cmdlet, 함수 또는 스크립트의 이름으로 인식되지 않음

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* cmdlet이 포함된 모듈을 Automation 계정으로 가져오지 않습니다.
* cmdlet이 포함된 모듈을 가져왔지만 최신 상태가 아닙니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하려면 다음 작업 중 하나를 수행합니다.

* Azure 모듈인 경우 Automation 계정에서 모듈을 업데이트하는 방법을 알아보려면 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md)을 참조하세요.
* Azure 모듈이 아닌 경우 모듈을 Automation 계정으로 가져와야 합니다.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>시나리오: Azure Automation의 PnP PowerShell Runbook에서 cmdlet이 실패함

### <a name="issue"></a>문제

Runbook에서 PnP PowerShell이 생성한 개체를 Azure Automation 출력에 직접 기록하면 cmdlet 출력이 Automation으로 다시 스트림할 수 없습니다.

### <a name="cause"></a>원인

일반적으로 Azure Automation에서 반환 개체를 catch하지 않고 PnP PowerShell cmdlet(예: `add-pnplistitem`)을 호출하는 Runbook을 처리하면 이 문제가 발생합니다.

### <a name="resolution"></a>해결 방법

cmdlet에서 전체 개체를 표준 출력에 쓰지 않도록 스크립트를 편집하여 반환 값을 변수에 할당합니다. 스크립트는 다음과 같이 출력 스트림을 cmdlet으로 리디렉션할 수 있습니다.

```azurecli
  $null = add-pnplistitem
```

스크립트에서 cmdlet 출력을 구문 분석하는 경우 스크립트는 단순히 출력을 스트림하는 대신 출력을 변수에 저장하여 이 변수를 조작해야 합니다.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>시나리오: Runbook을 실행해도 cmdlet이 인식되지 않음

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>원인

PowerShell 엔진을 통해 Runbook에서 사용하는 cmdlet을 찾을 수 없는 경우에 이 오류가 발생합니다. cmdlet이 포함된 모듈이 계정에서 누락되었거나, Runbook 이름과 충돌하는 이름이 있거나, cmdlet이 다른 모듈에도 있어 Automation에서 이름을 확인할 수 없을 수 있습니다.

### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 해결 방법 중 하나를 사용합니다.

* cmdlet 이름을 올바르게 입력했는지 확인합니다.
* cmdlet이 Automation 계정에 있고 충돌하지 않는지 확인합니다. cmdlet이 있는지 확인하려면 Runbook을 편집 모드에서 열고 라이브러리에서 찾으려는 cmdlet을 검색하거나 `Get-Command <CommandName>`을 실행합니다. 계정에서 cmdlet을 사용할 수 있고 이름이 다른 cmdlet 또는 Runbook과 충돌하지 않는지를 확인한 후 해당 cmdlet을 캔버스에 추가합니다. Runbook에 설정된 올바른 매개 변수를 사용하고 있는지 확인하세요.
* 이름이 충돌하고 서로 다른 두 모듈에서 cmdlet을 사용할 수 있는 경우 이 문제는 정규화된 이름을 cmdlet에 사용하여 해결합니다. 예를 들어 `ModuleName\CmdletName`을 사용할 수 있습니다.
* 온-프레미스의 하이브리드 작업자 그룹에서 Runbook을 실행하는 경우 모듈 및 cmdlet이 하이브리드 작업자를 호스팅하는 머신에 설치되어 있는지 확인합니다.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>시나리오: Add-AzAccount 호출에 대한 개체 참조가 잘못되었음

### <a name="issue"></a>문제

`Connect-AzAccount` cmdlet의 별칭인 `Add-AzAccount`를 사용하면 이 오류가 표시됩니다.

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>원인

`Add-AzAccount`를 호출하여 Automation 계정을 추가하기 전에 Runbook에서 적절한 단계를 수행하지 않으면 이 오류가 발생할 수 있습니다. 필요한 단계 중 한 가지 예는 실행 계정으로 로그인하는 것입니다. Runbook에 사용할 올바른 작업은 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)을 참조하세요.

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>시나리오: 개체 참조가 개체의 인스턴스로 설정되지 않았음

### <a name="issue"></a>문제

`Wait` 매개 변수를 사용하여 자식 Runbook을 호출하고 개체가 출력 스트림에 포함되면 다음 오류가 표시됩니다.

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>원인

개체가 스트림에 포함되면 `Start-AzAutomationRunbook`에서 출력 스트림을 올바르게 처리하지 않습니다.

### <a name="resolution"></a>해결 방법

폴링 논리를 구현하고 [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) cmdlet을 사용하여 출력을 검색합니다. 이 논리의 샘플은 다음과 같이 정의되어 있습니다.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>시나리오: 역직렬화된 개체로 인해 Runbook 실패

### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>원인

Runbook이 PowerShell 워크플로인 경우, 워크플로가 일시 중단되더라도 Runbook 상태를 유지하기 위해 복합 개체를 역직렬화된 형식으로 저장합니다.

### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 해결 방법 중 하나를 사용합니다.

* 복합 개체를 한 cmdlet에서 다른 cmdlet으로 파이프하는 경우 이러한 cmdlet을 `InlineScript` 활동에 래핑합니다.
* 개체 전체를 전달하지 말고 복합 개체에서 필요한 이름 또는 값만 전달합니다.
* PowerShell 워크플로 Runbook 대신 PowerShell Runbook을 사용합니다.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>시나리오: 웹후크 호출 시 400 잘못된 요청 상태

### <a name="issue"></a>문제

Azure Automation Runbook에 대한 웹후크를 호출하려고 하면 다음 오류가 표시됩니다.

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>원인

호출하려는 웹후크가 사용되지 않거나 만료되었습니다. 

### <a name="resolution"></a>해결 방법

웹후크를 사용하지 않도록 설정되어 있으면 Azure Portal을 통해 다시 사용하도록 설정할 수 있습니다. 웹후크가 만료되었으면 이를 삭제한 다음, 다시 만들어야 합니다. 아직 만료되지 않은 경우에만 [웹후크를 갱신](../automation-webhooks.md#update-a-webhook)할 수 있습니다. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>시나리오: 429: 요청 속도가 현재 너무 높음

### <a name="issue"></a>문제

`Get-AzAutomationJobOutput` cmdlet을 실행할 때 다음 오류 메시지가 나타납니다.

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>원인

[자세한 정보 스트림](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream)이 많은 Runbook에서 작업 출력을 검색하면 이 오류가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하려면 다음 중 하나를 수행합니다.

* Runbook을 편집하고 내보내는 작업 스트림 수를 줄입니다.
* cmdlet을 실행할 때 검색할 스트림 수를 줄입니다. 이렇게 하려면 출력 스트림만 검색하도록 [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) cmdlet에 대한 `Stream` 매개 변수의 값을 설정할 수 있습니다. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>시나리오: 할당된 할당량을 초과하여 Runbook 작업이 실패함

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>원인

작업 실행 시간이 계정에 할당된 무료 500분을 초과하면 이 오류가 발생합니다. 이 할당량은 모든 유형의 작업 실행 태스크에 적용됩니다. 이러한 태스크 중 일부는 작업을 테스트하거나, 포털에서 작업을 시작하거나, 웹후크를 사용하여 작업을 실행하거나, Azure Portal 또는 데이터 센터를 사용하여 실행할 작업을 예약하는 것입니다. Automation 가격 책정에 대한 자세한 내용은 [Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

### <a name="resolution"></a>해결 방법

매월 500분을 초과하여 처리하려면 구독을 체험 계층에서 기본 계층으로 변경합니다.

1. Azure 구독에 로그인합니다.
1. 업그레이드할 Automation 계정을 선택합니다.
1. **설정**, **가격 책정** 을 차례로 선택합니다.
1. 페이지 아래쪽에서 **사용** 을 선택하여 계정을 기본 계층으로 업그레이드합니다.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>시나리오: Runbook 출력 스트림이 1MB를 초과함

### <a name="issue"></a>문제

Azure 샌드박스에서 실행되는 Runbook에 다음 오류가 발생하면서 실패합니다.

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>원인

Runbook에서 너무 많은 예외 데이터를 출력 스트림에 쓰려고 했습니다.

### <a name="resolution"></a>해결 방법

작업 출력 스트림에는 1MB 제한이 있습니다. Runbook에서 `try` 및 `catch` 블록을 사용하여 실행 파일 또는 하위 프로세스에 대한 호출을 포함해야 합니다. 작업에서 예외를 throw하는 경우 코드에서 예외의 메시지를 Automation 변수에 쓰도록 합니다. 이 방법은 메시지를 작업 출력 스트림에 쓰지 않도록 방지합니다. 실행된 Hybrid Runbook Worker 작업의 경우 1MB로 잘린 출력 스트림이 오류 메시지 없이 표시됩니다.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>시나리오: Runbook 작업 시작을 세 번 시도했지만 매번 시작하지 못함

### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>원인

이 오류는 다음 문제 중 하나로 인해 발생합니다.

* **메모리 제한.** 400MB를 초과하는 메모리를 사용하면 작업이 실패할 수 있습니다. 샌드박스에 할당된 메모리에 대해 문서화된 제한은 [Automation 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)에서 확인할 수 있습니다. 

* **네트워크 소켓.** Azure 샌드박스는 1,000개의 동시 네트워크 소켓으로 제한됩니다. 자세한 내용은 [Automation 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)을 참조하세요.

* **호환되지 않는 모듈.** 모듈 종속성이 올바르지 않을 수 있습니다. 이 경우 Runbook에서 일반적으로 `Command not found` 또는 `Cannot bind parameter` 메시지를 반환합니다.

* **샌드박스에 대한 Active Directory 인증 없음.** Azure 샌드박스에서 실행되는 실행 파일 또는 하위 프로세스를 Runbook에서 호출하려고 했습니다. Azure ADAL(Active Directory 인증 라이브러리)을 사용하여 Azure AD에서 인증하도록 Runbook을 구성하는 것은 지원되지 않습니다.

### <a name="resolution"></a>해결 방법

* **메모리 제한, 네트워크 소켓.** 메모리 제한 내에서 작업하도록 하는 데 추천되는 방법은 워크로드를 여러 Runbook으로 분할하고, 메모리에서 더 적은 데이터를 처리하며, Runbook에서 불필요한 출력을 기록하지 않고, PowerShell 워크플로 Runbook에 작성되는 검사점의 수를 고려하는 것입니다. `$myVar.clear`와 같은 clear 메서드를 사용하여 변수를 지우고, `[GC]::Collect`를 사용하여 가비지 수집을 즉시 실행합니다. 이러한 작업은 런타임 중에 Runbook의 메모리 공간을 줄입니다.

* **호환되지 않는 모듈.** [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md)의 단계에 따라 Azure 모듈을 업데이트합니다.

* **샌드박스에 대한 Active Directory 인증 없음.** Runbook을 사용하여 Azure AD에 인증하는 경우 Automation 계정에서 Azure AD 모듈을 사용할 수 있는지 확인합니다. Runbook에서 자동화하는 작업을 수행하는 데 필요한 권한을 부여해야 합니다.

  Runbook이 Azure 샌드박스에서 실행되는 실행 파일 또는 하위 프로세스를 호출할 수 없는 경우 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 사용합니다. 하이브리드 작업자는 Azure 샌드박스의 메모리 및 네트워크 제한으로 제한되지 않습니다.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>시나리오: "메서드를 호출할 수 없습니다." 오류 메시지로 인해 PowerShell 작업이 실패함

### <a name="issue"></a>문제

Azure에서 실행되는 Runbook에서 PowerShell 작업을 시작하면 다음과 같은 오류 메시지가 표시됩니다.

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>원인

이 오류는 Azure 샌드박스에서 실행되는 Runbook을 [모든 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)에서 실행할 수 없음을 나타낼 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하는 방법은 두 가지가 있습니다.

* [Start-Job](/powershell/module/microsoft.powershell.core/start-job)을 사용하는 대신 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook)을 사용하여 Runbook을 시작합니다.
* Hybrid Runbook Worker에서 Runbook 실행을 시도합니다.

Azure Automation Runbook의 이 동작 및 다른 동작에 대한 자세한 내용은 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)을 참조하세요.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>시나리오: 장기 실행 Runbook이 완료되지 않음

### <a name="issue"></a>문제

3시간 동안 실행된 후에 Runbook이 [중지됨] 상태로 표시됩니다. 다음 오류도 표시될 수 있습니다.

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Azure Automation 내에서 프로세스를 [공평 분배](../automation-runbook-execution.md#fair-share) 방식으로 모니터링하므로 이 동작이 Azure 샌드박스에 설계되었습니다. 프로세스가 3시간 넘게 실행되면 공평 분배에서 Runbook을 자동으로 중지합니다. 공평 분배 시간 제한을 초과하는 Runbook의 상태는 Runbook 유형에 따라 다릅니다. PowerShell 및 Python Runbook은 [중지됨] 상태로 설정되며, PowerShell 워크플로 Runbook은 [실패] 상태로 설정됩니다.

### <a name="cause"></a>원인

Runbook이 Azure 샌드박스의 공평 분배에서 허용되는 3시간 제한을 초과하여 실행되었습니다.

### <a name="resolution"></a>해결 방법

권장되는 해결 방법 중 하나는 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행하는 것입니다. 하이브리드 작업자는 Azure 샌드박스의 3시간 공평 분배 Runbook 제한으로 제한되지 않습니다. 예기치 않은 로컬 인프라 문제가 있는 경우 Hybrid Runbook Worker에서 실행되는 Runbook에서 다시 시작 동작을 지원하도록 개발되어야 합니다.

또 다른 해결 방법은 [자식 Runbook](../automation-child-runbooks.md)을 만들어 Runbook을 최적화하는 것입니다. Runbook이 여러 리소스에서 동일한 함수를 반복하는 경우(예: 여러 데이터베이스의 데이터베이스 작업에서) 해당 함수를 자식 Runbook으로 이동할 수 있습니다. 각 자식 Runbook은 별도의 프로세스에서 병렬로 실행됩니다. 이 동작은 부모 Runbook이 완료되는 총 기간을 감소시킵니다.

자식 Runbook 시나리오를 사용하도록 설정하는 PowerShell cmdlet은 다음과 같습니다.

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). 이 cmdlet을 사용하면 Runbook을 시작하고 매개 변수를 Runbook에 전달할 수 있습니다.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). 자식 Runbook이 완료된 후에 수행해야 하는 작업이 있는 경우 이 cmdlet을 사용하면 각 자식에 대한 작업 상태를 확인할 수 있습니다.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>시나리오: get_SerializationSettings 메서드에 대한 작업 스트림 오류

### <a name="issue"></a>문제

Runbook에 대한 작업 스트림에서 다음 오류가 표시됩니다.

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>원인

이 오류는 AzureRM에서 Az 모듈로의 불완전한 Runbook의 마이그레이션으로 인해 발생할 수 있습니다. 이 경우 Azure Automation에서 AzureRM 모듈만 사용하여 Runbook 작업을 시작한 다음, Az 모듈만 사용하여 다른 작업을 시작하며, 이로 인해 샌드박스가 중단될 수 있습니다.

### <a name="resolution"></a>해결 방법

동일한 Runbook에서 Az 및 AzureRM cmdlet을 사용하지 않는 것이 좋습니다. 모듈을 올바르게 사용하는 방법에 대한 자세한 내용은 [Az 모듈로 마이그레이션](../shared-resources/modules.md#migrate-to-az-modules)을 참조하세요.

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>시나리오: Azure 샌드박스를 Runbook 또는 애플리케이션에 사용하면 액세스가 거부됨

### <a name="issue"></a>문제

Runbook 또는 애플리케이션이 Azure 샌드박스에서 실행하려고 하면 환경에서 액세스를 거부합니다.

### <a name="cause"></a>원인

Azure 샌드박스에서 모든 out-of-process COM 서버에 대한 액세스를 차단하므로 이 문제가 발생할 수 있습니다. 예를 들어 샌드박스를 작동하는 애플리케이션 또는 Runbook은 WMI(Windows Management Instrumentation) 또는 Windows Installer 서비스(msiserver.exe)로 호출할 수 없습니다.

### <a name="resolution"></a>해결 방법

Azure 샌드박스를 사용하는 방법에 대한 자세한 내용은 [Runbook 실행 환경](../automation-runbook-execution.md#runbook-execution-environment)을 참조하세요.

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>시나리오: Runbook 내에서 Key Vault를 사용하는 경우 잘못된 금지됨 상태 코드

### <a name="issue"></a>문제

Azure Automation Runbook을 통해 Azure Key Vault에 액세스하려고 하면 다음 오류가 표시됩니다.

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>원인

이 문제의 가능한 원인은 다음과 같습니다.

* 실행 계정을 사용하지 않음
* 사용 권한 부족

### <a name="resolution"></a>해결 방법

#### <a name="not-using-a-run-as-account"></a>실행 계정을 사용하지 않음

[5단계 - Azure 리소스를 관리하기 위한 인증 추가](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources)에 따라 실행 계정을 사용하여 Key Vault에 액세스하도록 합니다.

#### <a name="insufficient-permissions"></a>권한 부족

Key Vault에 액세스하는 데 충분한 권한이 실행 계정에 있도록 하는 [권한을 Key Vault에 추가](../manage-runas-account.md#add-permissions-to-key-vault)합니다.

## <a name="recommended-documents"></a>권장되는 문서

* [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)
* [Azure Automation에서 Runbook 시작](../start-runbooks.md)

## <a name="next-steps"></a>다음 단계

문제가 여기에 표시되지 않았거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 지원에서 답변, 지원 및 전문가를 위해 Azure 커뮤니티에 연결합니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.
