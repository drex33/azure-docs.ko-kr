---
title: 웹후크에서 Azure Automation Runbook 시작
description: 이 문서에서는 웹후크를 사용하여 HTTP 호출을 통해 Azure Automation Runbook을 시작하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 07/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e79feb7b45500a53bc7e13557aedcd1c0410554b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026452"
---
# <a name="start-a-runbook-from-a-webhook"></a>웹후크에서 Runbook 시작

웹후크를 사용하면 외부 서비스는 단일 HTTP 요청을 통해 Azure Automation에서 특정 Runbook을 시작할 수 있습니다. 외부 서비스에는 Azure DevOps Services, GitHub, Azure Monitor 로그 및 사용자 지정 애플리케이션이 포함됩니다. 해당 서비스는 웹후크를 사용하여 전체 Azure Automation API를 구현하지 않고도 Runbook을 시작할 수 있습니다. [Azure Automation에서 Runbook 시작](./start-runbooks.md)에서 Runhook을 시작하는 다른 방법과 웹후크를 비교할 수 있습니다.

> [!NOTE]
> 웹후크를 사용하여 Python Runbook을 시작하는 것은 지원되지 않습니다.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

웹후크에 TLS 1.2에 대한 클라이언트 요구 사항을 이해하려면 [Azure Automation에 대한 TLS 1.2](automation-managing-data.md#tls-12-for-azure-automation)를 참조하세요.

## <a name="webhook-properties"></a>웹후크 속성

다음 표에서는 Webhook에 대해 구성해야 하는 속성을 설명합니다.

| 속성 | 설명 |
|:--- |:--- |
| 속성 |웹후크의 이름입니다. 클라이언트에 노출되지 않으므로 원하는 이름을 지정할 수 있습니다. Azure Automation에서 Runbook을 식별하는 용도로만 사용됩니다. 가장 좋은 방법은 webhook를 사용할 클라이언트와 관련된 이름을 지정하는 것입니다. |
| URL |웹후크의 URL입니다. 클라이언트가 웹후크에 연결된 Runbook을 시작하기 위해 HTTP POST로 호출하는 고유한 주소입니다. webhook를 만들 때 자동으로 생성됩니다. 사용자 지정 URL을 지정할 수 없습니다. <br> <br> URL에는 타사 시스템이 추가 인증 없이 Runbook을 호출할 수 있는 보안 토큰이 포함됩니다. 따라서 비밀번호와 같은 URL을 처리해야 합니다. 보안상의 이유로 Azure Portal의 URL은 웹후크가 생성될 때만 볼 수 있습니다. 이 URL을 나중에 사용할 수 있도록 안전한 위치에 기록해 둡니다. |
| 만료 날짜 | 더 이상 사용할 수 없는 웹후크의 만료 날짜입니다. 웹후크가 만료되지 않는 한 웹후크를 만든 후에 만료 날짜를 수정할 수 있습니다. |
| 사용 | 웹후크를 만들 때 기본적으로 사용하도록 설정 되어 있는지 여부를 나타내는 설정입니다. 이 속성을 사용 안 함으로 설정하면 클라이언트가 웹후크를 사용할 수 없습니다. 웹후크를 만들 때 또는 만든 후 다른 시간에 이 속성을 설정할 수 있습니다. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>웹후크가 Runbook을 시작할 때 사용되는 매개 변수

웹후크는 Runbook을 시작할 때 사용되는 Runbook 매개 변수 값을 정의할 수 있습니다. 웹후크에는 필수 Runbook 매개 변수 값이 포함되어야 하고 선택적 매개 변수 값이 포함될 수 있습니다. 웹후크에 구성된 매개 변수 값은 웹후크를 만든 후에도 수정할 수 있습니다. 단일 Runbook에 연결된 여러 웹후크는 각각 다른 Runbook 매개 변수 값을 사용할 수 있습니다. 클라이언트는 webhook를 사용하여 Runbook을 시작할 때 webhook에 정의된 매개 변수 값을 재정의할 수 없습니다.

클라이언트에서 데이터를 수신하기 위해 Runbook은 `WebhookData`라는 단일 매개 변수를 지원합니다. 이 매개 변수는 클라이언트가 POST 요청에 포함하는 데이터가 있는 개체를 정의합니다.

![WebhookData 속성](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 매개 변수에는 다음 속성이 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| WebhookName | 웹후크의 이름입니다. |
| RequestHeader | 들어오는 POST 요청의 헤더를 포함한 해시 테이블입니다. |
| RequestBody | 들어오는 POST 요청의 본문입니다. 이 본문은 문자열, JSON, XML 또는 양식 인코딩과 같은 데이터 서식을 유지합니다. Runbook은 예상되는 데이터 형식으로 작동하도록 작성되어야 합니다. |

`WebhookData` 매개 변수를 지원하는 데 필요한 웹후크 구성은 없으며, Runbook은 이를 수락할 필요가 없습니다. Runbook이 매개 변수를 정의하지 않을 경우 클라이언트에서 전송된 요청의 모든 세부 정보가 무시됩니다.

> [!NOTE]
> 웹후크를 호출할 때 클라이언트는 호출이 실패하는 경우에 대비하여 항상 모든 매개 변수 값을 저장해야 합니다. 네트워크 중단 또는 연결 문제가 있는 경우 애플리케이션은 실패한 웹후크 호출을 검색할 수 없습니다.

웹후크를 만들 때 `WebhookData`의 값을 지정하는 경우 웹후크가 클라이언트 POST 요청의 데이터로 Runbook을 시작하면 재정의됩니다. 이는 애플리케이션에 요청 본문의 데이터가 포함되어 있지 않은 경우에도 마찬가지입니다.

웹후크 이외의 메커니즘을 사용하여 `WebhookData`를 정의하는 Runbook을 시작하는 경우 Runbook에서 인식하는 `WebhookData` 값을 제공할 수 있습니다. 이 값은 `WebhookData` 매개 변수와 동일한 [속성](#webhook-properties)을 가진 개체여야 하므로 Runbook이 웹후크에 의해 전달된 실제 `WebhookData` 개체에서 작동하는 것처럼 작동할 수 있습니다.

예를 들어 Azure Portal에서 다음 Runbook을 시작하고 테스트용으로 일부 샘플 웹후크 데이터를 전달하려면 데이터를 사용자 인터페이스의 JSON에 전달해야 합니다.

![UI에서의 WebhookData 매개 변수](media/automation-webhooks/WebhookData-parameter-from-UI.png)

다음 Runbook 예제에서는 `WebhookData`에 대한 다음 속성을 정의하겠습니다.

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

이제 `WebhookData` 매개 변수에 대한 UI에서 다음 JSON 개체를 전달합니다. 캐리지가 있는 이 예제는 반환하고 줄 바꿈 문자는 웹후크에서 전달되는 형식과 일치합니다.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI에서 WebhookData 매개 변수 시작](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation은 Runbook 작업으로 모든 입력 매개 변수의 값을 기록합니다. 웹후크 요청의 클라이언트에서 제공된 입력이 기록되고 자동화 작업에 액세스할 수 있는 어느 사용자든 사용할 수 있음을 의미합니다. 따라서 webhook 호출에 중요한 정보를 포함할 때는 주의해야 합니다.

## <a name="webhook-security"></a>웹후크 보안

웹후크의 보안은 웹후크를 호출할 수 있는 보안 토큰을 포함하는 URL의 개인 정보에 의존합니다. Azure Automation은 올바른 URL로 설정되어 있는 경우에만 요청에 대한 인증을 수행하지 않습니다. 따라서 클라이언트는 요청의 유효성을 검사하는 대체 수단을 사용하지 않고 매우 중요한 작업을 수행하는 Runbook에 웹후크를 사용해서는 안 됩니다.

다음과 같은 전략을 고려해 보세요.

* Runbook에 논리를 포함하여 웹후크에서 호출했는지 여부를 확인할 수 있습니다. Runbook이 `WebhookData` 매개 변수의 `WebhookName` 속성을 확인하도록 합니다. Runbook은 `RequestHeader` 및 `RequestBody` 속성에서 특정 정보를 찾아 추가로 유효성 검사를 수행할 수 있습니다.

* Runbook이 웹후크 요청을 받았을 때 외부 조건의 유효성 검사를 일부 수행하도록 합니다. 예를 들어 GitHub 리포지토리에 대한 새 커밋이 있을 때마다 GitHub에 의해 호출되는 Runbook을 가정해 보겠습니다. 이 Runbook은 계속하기 전에 GitHub에 연결하여 새 커밋이 발생했는지 유효성 검사를 수행할 수 있습니다.

* Azure Automation은 Azure 가상 네트워크 서비스 태그, 특히 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)를 지원합니다. 서비스 태그를 사용하여 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) 또는 [Azure Firewall](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의하고 가상 네트워크 내에서 웹후크를 트리거할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 서비스 태그 이름 **GuestAndHybridManagement** 를 규칙의 적절한 원본 또는 대상 필드에 지정하면 Automation 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. 이 서비스 태그는 IP 범위를 특정 지역으로 제한하여 보다 세부적인 제어를 허용하는 기능을 지원하지 않습니다.

## <a name="create-a-webhook"></a>웹후크 만들기

웹후크에는 게시된 Runbook이 필요합니다. 이 단계에서는 [Azure Automation Runbook 만들기](./learn/powershell-runbook-managed-identity.md)에서 만든 Runbook의 수정 버전을 사용합니다. 이 단계를 수행하려면 다음 코드를 사용하여 PowerShell Runbook을 편집합니다.

```powershell
param
(
    [Parameter(Mandatory=$false)]
    [object] $WebhookData
)

if ($WebhookData.RequestBody) { 
    $names = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        foreach ($x in $names)
        {
            $name = $x.Name
            Write-Output "Hello $name"
        }
}
else {
    Write-Output "Hello World!"
}
```

그런 다음, 수정된 Runbook을 저장하고 게시합니다. 다음 예에서는 Azure Portal, PowerShell 및 REST를 사용하여 웹후크를 만드는 방법을 보여줍니다.

### <a name="from-the-portal"></a>포털에서

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.

1. **프로세스 자동화** 에서 **Runbook** 을 선택하여 **Runbook** 페이지를 엽니다.

1. 목록에서 Runbook을 선택하여 Runbook **개요** 페이지를 엽니다.

1. **웹후크 추가** 를 선택하여 **웹후크 추가** 페이지를 엽니다.

   :::image type="content" source="media/automation-webhooks/add-webhook-icon.png" alt-text="웹후크 추가가 강조 표시된 Runbook 개요 페이지":::

1. **웹후크 추가** 페이지에서 **새 웹후크 만들기** 를 선택합니다.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-create.png" alt-text="만들기가 강조 표시된 웹후크 추가 페이지":::

1. 웹후크의 **이름** 을 입력합니다. **만료** 필드의 만료 날짜는 현재 날짜로부터 1년으로 설정됩니다.

1. 복사 아이콘을 클릭 하거나 <kbd>ctrl + C</kbd> 를 눌러 WEBHOOK의 URL을 복사 합니다. 그런 다음, URL을 안전한 위치에 저장합니다.

    :::image type="content" source="media/automation-webhooks/create-new-webhook.png" alt-text="URL이 강조 표시된 웹후크 만들기 페이지":::

    > [!IMPORTANT]
    > 웹후크를 만들고 나면 URL을 다시 검색할 수 없습니다. 위와 같이 복사하고 기록해야 합니다.

1. **확인** 을 선택하여 **웹후크 추가** 페이지로 돌아갑니다.

1. **웹후크 추가** 페이지에서 **매개 변수 구성 및 설정 실행** 을 선택하여 **매개 변수** 페이지를 엽니다.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-parameters.png" alt-text="매개 변수가 강조 표시된 웹후크 추가 페이지":::

1. **매개 변수** 페이지를 검토합니다. 이 문서에서 사용된 Runbook 예의 경우 변경할 필요가 없습니다. **확인** 을 선택하여 **웹후크 추가** 페이지로 돌아갑니다.

1. **웹후크 추가** 페이지에서 **만들기** 를 선택합니다. 웹후크가 생성되며 Runbook **개요** 페이지로 돌아갑니다.

### <a name="using-powershell"></a>PowerShell 사용

1. PowerShell [Az 모듈](/powershell/azure/new-azureps-module-az)의 최신 버전이 설치되었는지 확인합니다.

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. [New-AzAutomationWebhook](/powershell/module/az.automation/new-azautomationwebhook) cmdlet을 사용하여 Automation Runbook의 웹후크를 만듭니다. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    # Initialize variables with your relevant values
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $runbook = "runbookName"
    $psWebhook = "webhookName"
    
    # Create webhook
    $newWebhook = New-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook `
        -RunbookName $runbook `
        -IsEnabled $True `
        -ExpiryTime "12/31/2022" `
        -Force
    
    # Store URL in variable; reveal variable
    $uri = $newWebhook.WebhookURI
    $uri
    ```

   출력은 `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`와 유사하게 표시되는 URL입니다.

1. PowerShell cmdlet [Get-AzAutomationWebhook](/powershell/module/az.automation/get-azautomationwebhook)를 사용하여 웹후크를 확인할 수도 있습니다.

    ```powershell
    Get-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

### <a name="using-rest"></a>REST 사용

PUT 명령은 [Webhook - 만들기 또는 업데이트](/rest/api/automation/webhook/create-or-update)에 설명되어 있습니다. 이 예제에서는 PowerShell cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod)를 사용하여 PUT 요청을 보냅니다.

1. `webhook.json`이라는 파일을 만든 다음, 다음 코드를 붙여넣습니다.

    ```json
    {
      "name": "RestWebhook",
      "properties": {
        "isEnabled": true,
        "expiryTime": "2022-03-29T22:18:13.7002872Z",
        "runbook": {
          "name": "runbookName"
        }
      }
    }
    ```

   실행하기 전에 **runbook:name** 속성 값을 Runbook 실제 이름으로 수정합니다. 이 속성에 대한 자세한 내용은 [웹후크 속성](#webhook-properties)을 검토하세요.

1. PowerShell [Az 모듈](/powershell/azure/new-azureps-module-az)의 최신 버전이 설치되었는지 확인합니다.

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
    # Initialize variables
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $runbook = "runbookName"
    $restWebhook = "webhookName"
    $file = "path\webhook.json"

    # consume file
    $body = Get-Content $file
    
    # Craft Uri
    $restURI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount/webhooks/$restWebhook`?api-version=2015-10-31"
    ```

1. 다음 스크립트를 실행하여 액세스 토큰을 가져옵니다. 액세스 토큰이 만료되면 스크립트를 다시 실행해야 합니다.

    ```powershell
    # Obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    ```

1. 다음 스크립트를 실행하여 REST API를 사용해 웹후크를 만듭니다.

    ```powershell
    # Invoke the REST API
    # Store URL in variable; reveal variable
    $response = Invoke-RestMethod -Uri $restURI -Method Put -Headers $authHeader -Body $body
    $webhookURI = $response.properties.uri
    $webhookURI
    ```

   출력은 `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`와 유사하게 표시되는 URL입니다.

1. [웹후크 - 가져오기](/rest/api/automation/webhook/get)를 사용하여 이름을 기준으로 식별된 웹후크를 검색할 수도 있습니다. 다음 PowerShell 명령을 실행하면 됩니다.

    ```powershell
    $response = Invoke-RestMethod -Uri $restURI -Method GET -Headers $authHeader
    $response | ConvertTo-Json
    ```

## <a name="use-a-webhook"></a>웹후크 사용

이 예제에서는 PowerShell cmdlet [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest)를 사용하여 POST 요청을 새 웹후크에 보냅니다.

1. Runbook에 전달할 값을 웹후크 호출 본문으로 준비합니다. 비교적 간단한 값의 경우 다음과 같이 값을 스크립트할 수 있습니다.

    ```powershell
    $Names  = @(
                @{ Name="Hawaii"},
                @{ Name="Seattle"},
                @{ Name="Florida"}
            )
    
    $body = ConvertTo-Json -InputObject $Names
    ```

1. 큰 집합의 경우 파일을 사용할 수 있습니다. `names.json`이라는 파일을 만든 다음, 다음 코드를 붙여넣습니다.

    ```json
    [
        { "Name": "Hawaii" },
        { "Name": "Florida" },
        { "Name": "Seattle" }
    ]
    ```

    다음 PowerShell 명령을 실행하기 전에 json 파일에 대한 실제 경로를 사용하여 변수 `$file` 값을 변경합니다.

    ```powershell
    # Revise file path with actual path
    $file = "path\names.json"
    $bodyFile = Get-Content -Path $file 
    ```

1. 다음 PowerShell 명령을 실행하여 REST API를 사용해 웹후크를 호출합니다.

    ```powershell
    $response = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $body -UseBasicParsing
    $response
    
    $responseFile = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $bodyFile -UseBasicParsing
    $responseFile
    ```

   설명하기 위해 본문을 생성하는 서로 다른 메서드 2개에 대한 호출 2개가 생성되었습니다. 프로덕션의 경우 메서드 하나만 사용합니다.  출력은 다음과 유사하게 표시되어야 합니다(출력 하나만 표시됨).

   :::image type="content" source="media/automation-webhooks/webhook-post-output.png" alt-text="웹후크 호출에서 출력":::

    클라이언트는 `POST` 요청으로부터의 다음 반환 코드 중 하나를 받게 됩니다.

    | 코드 | 텍스트 | Description |
    |:--- |:--- |:--- |
    | 202 |수락됨 |요청이 수락되었고 runbook에서 대기합니다. |
    | 400 |잘못된 요청 |다음 이유 중 하나로 인해 요청이 수락되지 않았습니다. <ul> <li>webhook가 만료되었습니다.</li> <li>webhook가 비활성화되었습니다.</li> <li>URL의 토큰이 잘못되었습니다.</li>  </ul> |
    | 404 |찾을 수 없음 |다음 이유 중 하나로 인해 요청이 수락되지 않았습니다. <ul> <li>웹후크를 찾을 수 없습니다.</li> <li>Runbook을 찾을 수 없습니다.</li> <li>계정을 찾을 수 없습니다.</li>  </ul> |
    | 500 |내부 서버 오류 |URL은 유효했지만 오류가 발생했습니다. 요청을 다시 제출합니다. |

    요청이 성공했다고 가정하면 웹후크 응답에는 다음과 같은 JSON 형식의 작업 ID가 포함됩니다. 단일 작업 ID를 포함하지만 잠재적인 이후 향상 기능에 대해 JSON 형식이 허용됩니다.

    ```json
    {"JobIds":["<JobId>"]}
    ```

1. PowerShell cmdlet [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput)을 사용하여 출력을 가져옵니다. [Azure Automation API](/rest/api/automation/job)를 사용할 수도 있습니다.

    ```powershell
    #isolate job ID
    $jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
    
    # Get output
    Get-AzAutomationJobOutput `
        -AutomationAccountName $automationAccount `
        -Id $jobid `
        -ResourceGroupName $resourceGroup `
        -Stream Output
    ```

   출력은 다음과 비슷해야 합니다.

   :::image type="content" source="media/automation-webhooks/webhook-job-output.png" alt-text="웹후크 작업에서 출력":::

## <a name="update-a-webhook"></a>웹후크 업데이트

웹후크를 만들면 유효 기간은 10년이며 그 후에는 자동으로 만료됩니다. 웹후크가 만료된 후에는 다시 활성화할 수 없습니다. 제거했다가 다시 만들기만 가능합니다. 만료 시간에 도달하지 않은 웹후크를 연장할 수 있습니다. 웹후크를 연장하려면 다음 단계를 수행합니다.

1. 웹후크가 포함 된 Runbook으로 이동합니다.
1. **리소스** 에서 **웹후크** 를 선택한 다음, 연장하려는 웹후크를 선택합니다.
1. **웹후크** 페이지에서 새 만료 날짜와 시간을 선택한 다음, **저장** 을 선택합니다.

API 호출 [웹후크 - 업데이트](/rest/api/automation/webhook/update)와 PowerShell cmdlet [Set-AzAutomationWebhook](/powershell/module/az.automation/set-azautomationwebhook)를 검토하여 다른 가능한 수정 사항을 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Automation Runbook에서 웹후크를 제거하는 예는 다음과 같습니다.

- PowerShell을 사용하여 다음과 같이 [Remove-AzAutomationWebhook](/powershell/module/az.automation/remove-azautomationwebhook) cmdlet을 사용할 수 있습니다. 출력은 반환되지 않습니다.

    ```powershell
    Remove-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

- REST를 사용하면 다음과 같이 REST [웹후크 - 삭제](/rest/api/automation/webhook/delete) API를 사용할 수 있습니다.

    ```powershell
    Invoke-WebRequest -Method Delete -Uri $restURI -Headers $authHeader
    ```

   `StatusCode        : 200` 출력은 성공적으로 삭제되었음을 의미합니다.

## <a name="create-runbook-and-webhook-with-arm-template"></a>ARM 템플릿을 사용하여 Runbook 및 웹후크 만들기

[Azure Resource Manager](../azure-resource-manager/templates/overview.md) 템플릿을 사용하여 Automation 웹후크를 만들 수도 있습니다. 이 샘플 템플릿은 명명된 Runbook에 대한 Automation 계정 하나, Runbook 4개 및 웹후크 하나를 만듭니다.

1. `webhook_deploy.json`이라는 파일을 만든 다음, 다음 코드를 붙여넣습니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "automationAccountName": {
                "type": "String",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "webhookName": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Name"
                }
            },
            "runbookName": {
                "type": "String",
                "metadata": {
                    "description": "Runbook Name for which webhook will be created"
                }
            },
            "WebhookExpiryTime": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Expiry time"
                }
            },
            "_artifactsLocation": {
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/",
                "type": "String",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            }
        },
        "resources": [
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "sku": {
                        "name": "Free"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('runbookName')]",
                        "location": "[resourceGroup().location]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "Sample Runbook",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "webhooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('webhookName')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]",
                            "[parameters('runbookName')]"
                        ],
                        "properties": {
                            "isEnabled": true,
                            "expiryTime": "[parameters('WebhookExpiryTime')]",
                            "runbook": {
                                "name": "[parameters('runbookName')]"
                            }
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "webhookUri": {
                "type": "String",
                "value": "[reference(parameters('webhookName')).uri]"
            }
        }
    }
    ```

1. 다음 PowerShell 코드 샘플은 컴퓨터에서 템플릿을 배포합니다. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $resourceGroup = "resourceGroup"
    $templateFile = "path\webhook_deploy.json"
    $armAutomationAccount = "automationAccount"
    $armRunbook = "ARMrunbookName"
    $armWebhook = "webhookName"
    $webhookExpiryTime = "12-31-2022"
    
    New-AzResourceGroupDeployment `
        -Name "testDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $armAutomationAccount `
        -runbookName $armRunbook `
        -webhookName $armWebhook `
        -WebhookExpiryTime $webhookExpiryTime
    ```

   > [!NOTE]
   > 보안상의 이유로 URI는 처음 템플릿이 배포될 때만 반환됩니다.

## <a name="next-steps"></a>다음 단계

* 경고를 통해 Runbook을 트리거하려면 [경고를 사용하여 Azure Automation Runbook 트리거](automation-create-alert-triggered-runbook.md)를 참조하세요.