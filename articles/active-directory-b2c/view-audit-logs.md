---
title: 감사 로그 액세스 및 검토
titleSuffix: Azure AD B2C
description: Azure AD B2C 감사 로그를 프로그래밍 방식으로 Azure Portal에서 액세스하는 방법
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 16aae510920216a433e985654ac98b4a4a741ba5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043627"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C 감사 로그 액세스

Azure AD B2C(Azure Active Directory B2C)는 B2C 리소스, 발행된 토큰 및 관리자 액세스와 관련하여 작업 정보가 포함된 감사 로그를 전송합니다. 이 문서에서는 감사 로그를 통해 사용할 수 있는 정보 및 Azure AD B2C 테넌트에 대해 이 데이터에 액세스하는 방법에 대한 지침을 간략히 제공합니다.

감사 로그 이벤트는 **7일** 동안만 유지됩니다. 보존 기간이 더 오래 필요한 경우, 아래 표시된 방법 중 하나를 사용하여 로그를 다운로드하고 저장하도록 플랜하세요.

> [!NOTE]
> Azure Portal의 **Azure Active Directory** 또는 **Azure AD B2C** 페이지의 **사용자** 섹션에는 개별 Azure AD B2C 애플리케이션에 대한 사용자 로그인이 표시되지 않습니다. 로그인 이벤트에는 사용자 작업이 표시되지만 사용자가 로그인한 B2C 애플리케이션에는 다시 연관될 수 없습니다. 이 문서에서 설명하는 대로 이에 대한 감사 로그를 사용해야 합니다.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>감사 로그의 B2C 범주에서 사용 가능한 작업 개요

감사 로그의 **B2C** 범주에는 다음 유형의 작업이 포함됩니다.

|활동 유형 |Description  |
|---------|---------|
|권한 부여 |B2C 리소스에 액세스하는 사용자(예: B2C 정책 목록에 액세스하는 관리자)에 대한 권한 부여와 관련된 활동         |
|디렉터리 |관리자가 Azure Portal을 사용하여 로그인할 때 검색되는 디렉터리 특성과 관련된 활동 |
|애플리케이션 | B2C 애플리케이션의 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업 |
|키 |B2C 키 컨테이너에 저장된 키에 대한 CRUD 작업 |
|리소스 |B2C 리소스에 대한 CRUD 작업 예를 들어 정책 및 ID 공급자가 있습니다.
|인증 |사용자 자격 증명 및 토큰 발행에 대한 유효성 검사|

사용자 개체 CRUD 활동의 경우 **핵심 디렉터리** 범주를 참조하세요.

## <a name="example-activity"></a>예제 활동

Azure Portal의 이 이미지 예제에서는 사용자가 외부 ID 공급자(이 경우 Facebook)를 사용하여 로그인할 때 캡처된 데이터를 보여 줍니다.

![Azure Portal에서 감사 로그 작업 세부 정보 페이지의 예](./media/view-audit-logs/audit-logs-example.png)

활동 세부 정보 패널에는 다음과 같은 관련 정보가 포함되어 있습니다.

|섹션|필드|설명|
|-------|-----|-----------|
| 활동 | 이름 | 발생한 작업입니다. 예를 들어, *애플리케이션* 에 대한 id_token을 실행하여 실제 사용자 로그인을 마칩니다. |
| 초기자(작업자) | ObjectId | 사용자가 로그인하는 B2C 애플리케이션의 **개체 ID** 입니다. 이 식별자는 Azure Portal에 표시되지 않지만 Microsoft Graph API를 통해 액세스할 수 있습니다. |
| 초기자(작업자) | Spn | 사용자가 로그인하는 B2C 애플리케이션의 **애플리케이션 ID** 입니다. |
| 대상 | ObjectId | 로그인 중인 사용자의 **개체 ID** 입니다. |
| 추가 정보 | TenantId | Azure AD B2C 테넌트의 **테넌트 ID** 입니다. |
| 추가 정보 | PolicyId | 사용자를 로그인하는 데 사용되는 사용자 흐름(정책)의 **정책 ID** 입니다. |
| 추가 정보 | ApplicationId | 사용자가 로그인하는 B2C 애플리케이션의 **애플리케이션 ID** 입니다. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Azure Portal에서 감사 로그 보기

Azure Portal에서는 Azure AD B2C 테넌트의 감사 로그 이벤트에 대한 액세스를 제공합니다.

1. [Azure 포털](https://portal.azure.com)
1. Azure AD B2C 테넌트가 포함된 디렉터리로 전환하고 **Azure AD B2C** 로 이동합니다.
1. 왼쪽 메뉴의 **활동** 에서 **감사 로그** 를 선택합니다.

지난 7일 동안 기록된 활동 이벤트 목록이 표시됩니다.

![Azure Portal의 두 활동 이벤트를 사용하는 예제 필터](./media/view-audit-logs/audit-logs-example-filter.png)

다음을 비롯한 몇 가지 필터링 옵션을 사용할 수 있습니다.

* **활동 리소스 유형** - [사용 가능한 활동 개요](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) 섹션의 표에 나와 있는 활동 유형에 따라 필터링합니다.
* **날짜** - 표시된 활동의 날짜 범위를 필터링합니다.

목록에서 행을 선택하면 해당 이벤트에 대한 활동 세부 정보가 표시됩니다.

쉼표로 구분된 값(CSV) 파일에서 활동 이벤트 목록을 다운로드하려면 **다운로드** 를 선택합니다.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Azure AD Reporting API를 사용하여 감사 로그 가져오기

감사 로그는 Azure Active Directory에 대한 다른 활동과 동일한 파이프라인에 게시되므로 [Azure Active Directory 보고 API](/graph/api/directoryaudit-list)를 통해 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory Reporting API 시작](../active-directory/reports-monitoring/concept-reporting-api.md)을 참조하세요.

### <a name="enable-reporting-api-access"></a>Reporting API 액세스를 사용하도록 설정

Azure AD Reporting API에 대한 스크립트 또는 애플리케이션 기반 액세스를 허용하려면 다음 API 권한을 사용하여 Azure AD B2C 테넌트에 등록된 애플리케이션이 필요합니다. B2C 테넌트 내의 기존 애플리케이션 등록에 대해 이러한 권한을 사용하도록 설정하거나 감사 로그 자동화에 사용하기 위해 특별히 새 애플리케이션을 만들 수 있습니다.

* Microsoft Graph > 애플리케이션 권한 > AuditLog > AuditLog.Read.All

다음 문서의 단계를 수행하여 필요한 권한으로 애플리케이션을 등록합니다.

[Microsoft Graph로 Azure AD B2C 관리](microsoft-graph-get-started.md)

적절한 권한으로 애플리케이션을 등록한 후 스크립트를 사용하여 활동 이벤트를 가져오는 방법에 대한 예제는 이 문서의 뒷부분에 있는 PowerShell 스크립트 섹션을 참조하세요.

### <a name="access-the-api"></a>API에 액세스

API를 통해 Azure AD B2C 감사 로그 이벤트를 다운로드하려면 `B2C` 범주에 대한 로그를 필터링합니다. 범주별로 필터링하려면 Azure AD Reporting API 엔드포인트를 호출할 때 `filter` 쿼리 문자열 매개 변수를 사용합니다.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell 스크립트

다음 PowerShell 스크립트에서는 Azure AD Reporting API를 쿼리하는 방법의 예제를 보여 줍니다. API를 쿼리한 후에는 로깅된 이벤트를 표준 출력으로 출력하고 JSON 출력을 파일에 기록합니다.

[Azure Cloud Shell](overview.md)에서 이 스크립트를 실행할 수 있습니다. 애플리케이션 ID, 클라이언트 암호 및 Azure AD B2C 테넌트의 이름으로 업데이트해야 합니다.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

다음은 이 문서의 앞부분에 표시된 예제 활동 이벤트의 JSON 표현입니다.

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

다른 관리 작업(예: [Microsoft Graph를 사용하여 Azure AD B2C 사용자 계정 관리](microsoft-graph-operations.md))을 자동화할 수 있습니다.