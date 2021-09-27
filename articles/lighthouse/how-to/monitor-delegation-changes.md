---
title: 관리 테넌트에서 위임 변경 내용 모니터링
description: 관리 테넌트에 대한 모든 Azure Lighthouse 위임 활동을 모니터링하는 방법을 알아봅니다.
ms.date: 09/08/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb38ac8ac7f86fd179663fe7bfb3aa1fb4e8830
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819587"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>관리 테넌트에서 위임 변경 내용 모니터링

서비스 공급자는 [Azure Lighthouse](../overview.md)를 통해 고객 구독 또는 리소스 그룹이 테넌트에 위임되는 시기 또는 이전에 위임된 리소스가 제거되는 시기를 인지하는 것이 좋습니다.

관리 테넌트에서 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)는 테넌트 수준에서 위임 활동을 추적합니다. 이 로그된 활동은 고객 테넌트의 추가 또는 제거된 위임을 포함합니다.

이 토픽에서는 모든 고객에 대한 테넌트로의 위임 활동을 모니터링하는 데 필요한 권한을 설명합니다. 또한 샘플 스크립트를 통해 관련 데이터를 쿼리하고 보고하는 한 가지 방법을 소개합니다.

> [!IMPORTANT]
> 모든 단계는 고객 테넌트가 아닌 관리 테넌트에서 수행해야 합니다.
>
> 이 토픽에서는 서비스 공급자 및 고객만을 언급하지만 [여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에서도 동일한 프로세스를 사용할 수 있습니다.

## <a name="enable-access-to-tenant-level-data"></a>테넌트 수준 데이터에 대한 액세스 부여

테넌트 수준 활동 로그 데이터에 액세스하려면 루트 범위(/)의 [모니터링 리더](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure 기본 제공 역할을 계정에 할당해야 합니다. 이 할당은 상승된 추가 액세스가 있는 전역 관리자 역할을 가진 사용자가 수행해야 합니다.

### <a name="elevate-access-for-a-global-administrator-account"></a>전역 관리자 계정의 액세스 권한 상승

루트 범위(/)에서 역할을 할당하려면 상승된 액세스를 가진 전역 관리자 역할이 있어야 합니다. 상승된 액세스는 역할을 할당하는 경우에만 추가하고 완료되면 제거해야 합니다.

권한 상승을 추가 및 제거하는 방법에 대한 자세한 내용은 [모든 Azure 구독 및 관리 그룹을 관리하기 위한 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md)을 참조하세요.

액세스 권한이 상승되면 계정에는 루트 범위로 Azure에서 사용자 액세스 관리자 역할이 지정됩니다. 이 역할 할당을 통해 모든 리소스를 보고 디렉터리의 모든 구독 또는 관리 그룹에 액세스 권한을 할당하며, 루트 범위에서 역할을 할당할 수 있습니다.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>루트 범위에서 모니터링 리더 역할 할당

액세스 권한을 상승시킨 후에는 테넌트 수준 활동 로그 데이터를 쿼리할 수 있도록 계정에 적절한 권한을 할당할 수 있습니다. 이 계정에 Azure 기본 제공 역할인 [모니터링 리더](../../role-based-access-control/built-in-roles.md#monitoring-reader)를 관리 테넌트의 루트 범위에서 할당해야 합니다.

> [!IMPORTANT]
> 루트 범위에서 역할 할당을 부여하는 것은 테넌트의 모든 리소스에 동일한 권한이 적용됨을 의미합니다. 이는 광범위 한 액세스 이기 때문에 [서비스 주체 계정에이 역할을 할당 하 고 해당 계정을 사용 하 여 데이터를 쿼리](#use-a-service-principal-account-to-query-the-activity-log)하는 것이 좋습니다.
> 
> 또한 루트 범위에서 개별 사용자 또는 사용자 그룹에 모니터링 리더 역할을 할당하여 [Azure Portal에서 직접 위임 정보를 볼](#view-delegation-changes-in-the-azure-portal) 수 있습니다. 광범위한 수준의 액세스이므로 이 작업을 수행할 경우 해당 권한은 가능한 한 최소한의 사용자로 제한해야 합니다.

다음 방법 중 하나를 사용하여 루트 범위를 할당합니다.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>전역 관리자 계정에 대한 상승된 액세스 제거

루트 범위에서 원하는 계정으로 모니터링 리더 역할을 할당한 후에는 이 액세스 수준이 더 이상 필요하지 않으므로 전역 관리자 계정에 대한 [상승된 액세스를 제거](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)해야 합니다.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Azure Portal에서 위임 변경 내용 보기

루트 범위에서 모니터링 리더 역할이 할당된 사용자는 Azure Portal에서 직접 위임 변경 내용을 볼 수 있습니다.

1. **내 고객** 페이지로 이동한 다음 왼쪽 탐색 메뉴에서 **활동 로그** 를 선택합니다.
1. 화면 위쪽의 필터에서 **디렉터리 작업** 이 선택되어 있는지 확인합니다.

위임 변경에 대한 목록이 표시됩니다. **열 편집** 을 선택하여 **상태**, **이벤트 범주**, **시간**, **타임 스탬프**, **구독**, **시작 이벤트**, **리소스 그룹**, **리소스 종류** 및 **리소스** 값을 표시하거나 숨길 수 있습니다.

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Azure Portal에서의 위임 변경 내용 스크린샷":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>서비스 주체 계정을 사용하여 활동 로그 쿼리

루트 범위의 모니터링 리더 역할은 광범위한 수준의 액세스 권한이므로 서비스 주체 계정에 역할을 할당하고 아래 스크립트를 사용하여 해당 계정을 통해 데이터를 쿼리하려고 할 수 있습니다.

> [!IMPORTANT]
> 현재 이 데이터를 쿼리할 때 많은 양의 위임 작업을 포함하는 테넌트가 오류가 발생시킬 수 있습니다.

서비스 주체 계정을 사용하여 활동 로그를 쿼리하는 경우 다음과 같은 모범 사례를 따르는 것이 좋습니다.

- 다른 자동화에 사용되는 기존 서비스 주체에게 이 역할을 할당하는 대신 이 기능에만 사용할 [새 서비스 주체 계정을 만듭니다](../../active-directory/develop/howto-create-service-principal-portal.md).
- 이 서비스 주체에는 위임된 고객 리소스에 대한 액세스 권한이 없어야 합니다.
- [인증서를 사용하여 인증](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)하고 [Azure Key Vault에 안전하게 저장합니다](../../key-vault/general/security-features.md).
- 서비스 주체를 대신하여 작업할 수 있는 액세스 권한이 있는 사용자를 제한합니다.

관리 테넌트의 루트 범위에 대한 모니터링 리더 액세스 권한이 있는 새 서비스 주체 계정을 만든 후 해당 계정을 테넌트의 위임 작업을 쿼리하고 보고하는 데 사용할 수 있습니다.

[이 Azure PowerShell 스크립트](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes)를 사용하여 추가되거나 제거된 위임(또는 성공하지 못한 시도)에 대한 지난 1일간의 활동 및 보고서를 쿼리할 수 있습니다. [테넌트 활동 로그](/rest/api/monitor/TenantActivityLogs/List) 데이터를 쿼리한 다음 추가 또는 제거된 위임에 대해 보고하기 위해 다음 값을 생성합니다.

- **DelegatedResourceId**: 위임된 구독 또는 리소스 그룹의 ID
- **CustomerTenantId**: 고객 테넌트 ID
- **CustomerSubscriptionId**: 위임된 구독 ID이거나 위임된 리소스 그룹을 포함하는 구독 ID
- **CustomerDelegationStatus**: 위임된 리소스의 상태 변경(성공 또는 실패)
- **EventTimeStamp**: 위임 변경이 로그된 날짜 및 시간

이 데이터를 쿼리할 때는 다음 사항을 염두에 두어야 합니다.

- 단일 배포에서 여러 리소스 그룹을 위임하는 경우 각 리소스 그룹에 대해 별도의 항목이 반환됩니다.
- 이전 위임에 대한 변경 내용(예: 권한 구조 업데이트)은 추가된 위임으로 로그됩니다.
- 위에서 언급한 것처럼 이 테넌트 수준 데이터에 액세스하려면 계정에 루트 범위(/)에 모니터링 리더 Azure 기본 제공 역할이 있어야 합니다.
- 이 데이터는 사용자 고유의 워크플로 및 보고에서 사용할 수 있습니다. 예를 들어 [HTTP 데이터 수집기 API(퍼블릭 미리 보기)](../../azure-monitor/logs/data-collector-api.md)를 사용하여 REST API 클라이언트에서 Azure Monitor에 데이터를 로그한 다음, [작업 그룹](../../azure-monitor/alerts/action-groups.md)을 사용하여 알림 또는 경고를 만들 수 있습니다.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Lighthouse](onboard-customer.md)에 고객을 온보딩하는 방법에 대해 알아봅니다.
- [Azure Monitor](../../azure-monitor/index.yml) 및 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)에 대해 알아봅니다.
- [도메인별 활동 로그](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) 샘플 통합 문서를 검토하여 도메인 이름별로 필터링하는 옵션을 통해 구독 간에 Azure 활동 로그를 표시하는 방법을 알아봅니다.
