---
title: '빠른 시작: REST API를 사용하여 랩 만들기'
description: 이 빠른 시작에서는 Azure REST API를 사용하여 Azure DevTest Labs에서 랩을 만듭니다.
ms.topic: quickstart
ms.date: 10/27/2021
ms.openlocfilehash: 83a1509f36f53d51f63f7dbc39ea2d1f6794dc54
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479784"
---
# <a name="quickstart-create-a-lab-in-azure-devtest-labs-using-azure-rest-api"></a>빠른 시작: Azure REST API를 사용하여 Azure DevTest Labs에서 랩 만들기

Azure REST API를 사용하여 Azure DevTest Labs 시작 Azure DevTest Labs는 Azure VM(가상 머신) 및 네트워크와 같은 리소스 그룹을 포함합니다. 이 인프라를 사용하면 제한 및 할당량을 지정하여 해당 리소스를 더 잘 관리할 수 있습니다.  Azure REST API를 사용하면 Azure 플랫폼에서 호스트되는 서비스에 대한 작업을 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- PowerShell [Az 모듈](/powershell/azure/new-azureps-module-az)이 설치되었습니다. 최신 버전이 있는지 확인합니다. 필요한 경우 `Update-Module -Name Az`를 실행합니다.

## <a name="prepare-request-body"></a>요청 본문 준비

REST 호출에서 사용할 [요청 본문](/rest/api/dtl/labs/create-or-update.md#request-body)을 준비합니다.

다음 JSON 구문을 `body.json`이라는 파일에 복사하여 붙여넣습니다. 로컬 컴퓨터 또는 Azure 스토리지 계정에 파일을 저장합니다.

```json
{
  "properties": {
    "labStorageType": "Standard"
  },
  "location": "westus2",
  "tags": {
    "Env": "alpha"
  }
}
```

## <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

1. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    $file = "path\body.json"
    ```

1. 워크스테이션에서 PowerShell [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Set-AzContext -SubscriptionId $subscription
    ```

## <a name="build-request-body-for-submission"></a>제출을 위한 빌드 요청 본문

PUT 요청에 대한 구문은 다음과 같습니다.<br>
 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{name}?api-version=2018-09-15`.

 다음 PowerShell 스크립트를 실행하여 요청 값을 매개 변수에 전달합니다. 요청 본문의 콘텐츠도 매개 변수에 전달됩니다.

```powershell
# build URI
$URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"

# build body
$body = Get-Content $file
```

## <a name="obtain-an-authentication-token"></a>인증 토큰 얻기

다음 명령을 사용하여 인증 토큰을 검색합니다.

```powershell
$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}
```

## <a name="invoke-the-rest-api"></a>REST API 호출

다음 명령을 사용하여 REST API를 호출하고 응답을 검토합니다.

```powershell
# Invoke the REST API
$response = Invoke-RestMethod -Uri $URI -Method PUT -Headers $authHeader -Body $body

# Review output
$response | ConvertTo-Json
```

응답은 다음 텍스트와 같아야 합니다.

```output
{
    "properties":  {
                       "labStorageType":  "Standard",
                       "mandatoryArtifactsResourceIdsLinux":  [

                                                              ],
                       "mandatoryArtifactsResourceIdsWindows":  [

                                                                ],
                       "createdDate":  "2021-10-27T20:22:49.7495913+00:00",
                       "premiumDataDisks":  "Disabled",
                       "environmentPermission":  "Reader",
                       "announcement":  {
                                            "title":  "",
                                            "markdown":  "",
                                            "enabled":  "Disabled",
                                            "expired":  false
                                        },
                       "support":  {
                                       "enabled":  "Disabled",
                                       "markdown":  ""
                                   },
                       "provisioningState":  "Creating",
                       "uniqueIdentifier":  "uniqueID"
                   },
    "id":  "/subscriptions/ContosoID/resourcegroups/groupcontoso/providers/microsoft.devtestlab/labs/myotherlab",

    "name":  "myOtherLab",
    "type":  "Microsoft.DevTestLab/labs",
    "location":  "westus2",
    "tags":  {
                 "Env":  "alpha"
             }
}
```

## <a name="clean-up-resources"></a>리소스 정리

이 랩을 더 이상 사용하지 않으려면 다음 단계에 따라 삭제합니다.

1. 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroupName"
    $labName = "labName"
    ```

1. 다음 스크립트를 실행하여 Azure DevTest Labs에서 명명된 랩을 제거합니다.

    ```powershell
    # build URI
    $URI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.DevTestLab/labs/$labName`?api-version=2018-09-15"
    
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
    Invoke-RestMethod -Uri $URI -Method DELETE -Headers $authHeader
    ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure REST API를 사용하여 랩을 만들었습니다. 랩에 액세스하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [자습서: 랩에 액세스](../tutorial-use-custom-lab.md)
