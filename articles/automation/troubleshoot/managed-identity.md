---
title: Azure Automation 관리 ID 문제 해결(미리 보기)
description: 이 문서에서는 Automation 계정으로 관리 ID를 사용할 때 문제를 해결하는 방법을 설명합니다.
services: automation
ms.subservice: ''
ms.date: 06/28/2021
ms.topic: troubleshooting
ms.openlocfilehash: 89d3785ef527eebc822eefbbae2c887dd40108ee
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687500"
---
# <a name="troubleshoot-azure-automation-managed-identity-issues-preview"></a>Azure Automation 관리 ID 문제 해결(미리 보기)

이 문서에서는 Automation 계정으로 관리 ID를 사용할 때 발생할 수 있는 문제에 대한 솔루션에 대해 설명합니다. Automation 계정에서 관리 ID를 사용하는 방법에 대한 일반적인 정보는 [Azure Automation 계정 인증 개요](../automation-security-overview.md#managed-identities-preview)를 참조하세요.

## <a name="scenario-fail-to-get-msi-token-for-account"></a>시나리오: 계정에 대한 MSI 토큰을 가져오지 못함

### <a name="issue"></a>문제

Automation 계정에서 사용자 할당 관리 ID로 작업하는 경우 `Failed to get MSI token for account a123456b-1234-12a3-123a-aa123456aa0b`와 유사한 오류가 표시됩니다.

### <a name="cause"></a>원인

Automation 계정에 대해 시스템 할당 관리 ID를 사용하도록 설정하기 전에 사용자 할당 관리 ID를 사용합니다.

### <a name="resolution"></a>해결 방법

Automation 계정에 대해 시스템 할당 관리 ID를 사용하도록 설정합니다. 그런 다음, 사용자 할당 관리 ID를 사용합니다.  

## <a name="scenario-attempt-to-use-managed-identity-with-automation-account-fails"></a>시나리오: Automation 계정으로 관리되는 ID를 사용하지 못했습니다.

### <a name="issue"></a>문제

Automation 계정에서 관리 ID를 사용하려고 할 때 다음과 같은 오류가 발생합니다.

```error
Connect-AzureRMAccount : An error occurred while sending the request. At line:2 char:1 + Connect-AzureRMAccount -Identity + 
CategoryInfo : CloseError: (:) [Connect-AzureRmAccount], HttpRequestException + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>원인

가장 일반적인 원인은 ID를 사용하기 전에 사용하도록 설정하지 않았기 때문입니다. 이를 확인하려면 영향을 받는 Automation 계정에서 다음 PowerShell runbook을 실행합니다.

```powershell
resource= "?resource=https://management.azure.com/"
$url = $env:IDENTITY_ENDPOINT + $resource
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER)
$Headers.Add("Metadata", "True")

try
{
    $Response = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
}
catch
{
    $StatusCode = $_.Exception.Response.StatusCode.value__
    $stream = $_.Exception.Response.GetResponseStream()
    $reader = New-Object System.IO.StreamReader($stream)
    $responseBody = $reader.ReadToEnd()
    
    Write-Output "Request Failed with Status: $StatusCode, Message: $responseBody"
}
```

ID를 사용하기 전에 사용하도록 설정하지 않은 문제인 경우 다음과 유사한 결과가 표시됩니다.

`Request Failed with Status: 400, Message: {"Message":"No managed identity was found for Automation account xxxxxxxxxxxx"}`

### <a name="resolution"></a>해결 방법

관리 ID 서비스를 사용하려면 먼저 Automation 계정에 대한 ID를 사용하도록 설정해야 합니다. [Azure Automation 계정에 관리 ID 사용(미리 보기)](../enable-managed-identity-for-automation.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 문제가 해결되지 않으면 다음 채널 중 하나를 시도하여 추가 지원을 받습니다.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)에 연결합니다. 이는 Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하기 위한 공식 Microsoft Azure 계정입니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.