---
title: 공유 키로 권한 부여 방지
titleSuffix: Azure Storage
description: 클라이언트가 Azure AD를 사용하여 요청을 승인하도록 하려면 공유 키로 권한이 부여된 저장소 계정에 대한 요청을 허용하지 않으면 됩니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/12/2021
ms.author: tamram
ms.reviewer: dineshm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5e38cfeae5ad8593e5ee69059f4bdb903b04aa42
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537683"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account"></a>Azure Storage 계정에 대한 공유 키 권한 부여 방지

Azure Storage 계정에 대한 모든 보안 요청에는 권한이 있어야 합니다. 기본적으로 요청은 Azure AD(Azure Active Directory) 자격 증명을 사용하거나 공유 키 권한 부여를 위한 계정 액세스 키를 사용하여 권한을 부여할 수 있습니다. 이러한 두 가지 유형의 권한 부여 중 Azure AD는 공유 키보다 뛰어난 보안과 사용 편의성을 제공하며 Microsoft에서 권장합니다. 클라이언트가 Azure AD를 사용하여 요청을 승인하도록 하려면 공유 키로 권한이 부여된 저장소 계정에 대한 요청을 허용하지 않으면 됩니다.

스토리지 계정에 대한 공유 키 권한 부여를 허용하지 않으면 Azure Storage는 계정 액세스 키로 권한이 부여된 계정에 대한 모든 후속 요청을 거부합니다. Azure AD로 권한이 부여된 보안 요청만 성공합니다. Azure AD를 사용하는 방법에 관한 자세한 내용은 [Azure Storage의 데이터에 대한 액세스 권한 부여](authorize-data-access.md)를 참조하세요.

이 문서에서는 공유 키 권한 부여를 사용하여 보낸 요청을 검색하는 방법과 스토리지 계정에 대한 공유 키 권한 부여를 수정하는 방법을 설명합니다.

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>클라이언트 애플리케이션에 사용되는 권한 부여 유형 검색

스토리지 계정에 대한 공유 키 권한 부여를 허용하지 않으면 공유 키 권한 부여에 계정 액세스 키를 사용하는 클라이언트의 요청이 실패합니다. 이렇게 변경하기 전에, 공유 키 권한 부여를 불허하는 경우 클라이언트 애플리케이션에 어떤 영향을 주는지 이해하려면 스토리지 계정에 대한 로깅 및 메트릭을 사용하도록 설정합니다. 그런 다음, 일정 기간 동안 계정에 대한 요청 패턴을 분석하여 요청에 권한이 부여되는 방식을 확인할 수 있습니다.

메트릭을 사용하여 스토리지 계정이 수신하는 공유 키 또는 SAS(공유 액세스 서명)로 권한이 부여된 요청 수를 확인합니다. 로그를 사용하여 어떤 클라이언트가 해당 요청을 보내는지 확인합니다.

SAS는 공유 키 또는 Azure AD를 사용하여 권한을 부여할 수 있습니다. 공유 액세스 서명으로 수행된 요청을 해석하는 방법에 대한 자세한 내용은 [공유 키가 SAS 토큰에 미치는 영향 이해](#understand-how-disallowing-shared-key-affects-sas-tokens)를 참조하세요.

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>공유 키로 권한이 부여된 요청 수 모니터링

스토리지 계정에 대한 요청에 권한이 부여되는 방식을 추적하려면 Azure Portal에서 Azure 메트릭 탐색기를 사용합니다. 메트릭 탐색기에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

공유 키 또는 SAS로 이루어진 요청을 추적하는 메트릭을 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다. **모니터링** 섹션에서 **메트릭** 을 선택합니다.
1. **메트릭 추가** 를 선택합니다. **메트릭** 대화 상자에서 다음 값을 지정합니다.
    1. **범위** 필드는 스토리지 계정의 이름으로 설정된 상태로 둡니다.
    1. **메트릭 네임스페이스** 를 *Account* 로 설정합니다. 이 메트릭은 스토리지 계정에 대한 모든 요청을 보고합니다.
    1. **메트릭** 필드를 *Transactions* 로 설정합니다.
    1. **집계** 필드는 *Sum* 으로 설정합니다.

    새 메트릭은 지정된 시간 간격 동안 스토리지 계정에 대한 트랜잭션 수의 합계를 표시합니다. 결과 메트릭은 다음 이미지와 같이 나타납니다.

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="공유 키 또는 SAS로 이루어진 트랜잭션을 합산하는 메트릭을 구성하는 방법을 보여주는 스크린샷":::

1. 그런 다음, **필터 추가** 단추를 선택하여 권한 부여 유형에 대한 메트릭 필터를 만듭니다.
1. **필터** 대화 상자에서 다음 값을 지정합니다.
    1. **속성** 값을 *Authentication* 으로 설정합니다.
    1. **연산자** 필드를 등호(=)로 설정합니다.
    1. **값** 필드에서 계정 키 및 *SAS* 를 선택합니다.
1. 오른쪽 위 모서리에서 메트릭을 보려는 시간 범위를 선택합니다. 간격을 1분에서 1개월 사이로 지정하여 요청 집계의 세분화 정도를 나타낼 수도 있습니다. 예를 들어, 지난 30일 동안 날짜별로 집계된 요청을 보려면 **시간 범위** 를 30일로 설정하고 **시간 단위** 를 1일로 설정합니다.

메트릭을 구성한 후에는 스토리지 계정에 대한 요청이 그래프에 나타나기 시작합니다. 다음 이미지는 공유 키로 권한이 부여되었거나 SAS 토큰으로 작성된 요청을 보여줍니다. 요청은 지난 30일 동안 날짜별로 집계됩니다.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="공유 키로 권한이 부여된 요청을 보여주는 스크린샷":::

스토리지 계정에 대해 공유 키로 권한이 부여된 특정 수의 요청이 있는 경우 알려주도록 경고 규칙을 구성할 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>로그를 분석하여 공유 키 또는 SAS로 요청에 대한 권한을 부여하는 클라이언트 식별

Azure Storage 로그는 스토리지 계정에 대한 요청의 세부 정보(요청에 권한이 부여된 방법 포함)를 캡처합니다. 로그를 분석하여 어떤 클라이언트가 공유 키 또는 SAS로 요청에 대한 권한을 부여하는지 확인할 수 있습니다.

권한 부여 방법을 평가하기 위해 Azure Storage 계정에 요청을 로그하려면 Azure Monitor(미리 보기)에서 Azure Storage 로깅을 사용하면 됩니다. 자세한 내용은 [Azure Storage 모니터링](../blobs/monitor-blob-storage.md)을 참조하세요.

Azure Monitor의 Azure Storage 로깅은 로그 쿼리를 사용하여 로그 데이터를 분석하도록 지원합니다. 로그를 쿼리하려면 Azure Log Analytics 작업 영역을 사용사면 됩니다. 로그 쿼리에 대해 자세히 알아보려면 [자습서: Log Analytics 쿼리 시작하기](../../azure-monitor/logs/log-analytics-tutorial.md)를 참조하세요.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure Portal에서 진단 설정 만들기

Azure Monitor를 사용하여 Azure Storage 데이터를 로깅하고 Azure Log Analytics를 사용하여 분석하려면 먼저 데이터를 로그할 요청 유형과 스토리지 서비스를 나타내는 진단 설정을 만들어야 합니다. Azure Portal에서 진단 설정을 만들려면 다음 단계를 수행합니다.

1. Azure Storage 계정이 포함된 구독에서 새 Log Analytics 작업 영역을 만들거나 기존 Log Analytics 작업 영역을 사용합니다. 스토리지 계정에 대한 로깅을 구성하면 Log Analytics 작업 영역에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.
1. Azure Portal의 스토리지 계정으로 이동합니다.
1. 모니터링 섹션에서 **진단 설정(미리 보기)** 을 선택합니다.
1. 요청을 로그할 Azure Storage 서비스를 선택합니다. 예를 들어 Blob Storage에 요청을 로그하려면 **Blob** 을 선택합니다.
1. **진단 설정 추가** 를 선택합니다.
1. 진단 설정의 이름을 제공합니다.
1. **범주 세부 정보** 의 **로그** 섹션에서 **StorageRead**, **StorageWrite**, **StorageDelete** 를 선택하여 선택한 서비스에 대한 모든 데이터 요청을 로그합니다.
1. **대상 세부 정보** 에서 **Log Analytics에 보내기** 를 선택합니다. 다음 이미지와 같이, 구독 및 이전에 만든 Log Analytics 작업 영역을 선택합니다.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="로깅 요청에 대한 진단 설정을 만드는 방법을 보여주는 스크린샷":::

스토리지 계정의 각 Azure Storage 리소스 유형에 대한 진단 설정을 만들 수 있습니다.

진단 설정을 만든 후에는 이 설정에 따라 스토리지 계정에 대한 요청이 로그됩니다. 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집하기 위한 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

Azure Monitor의 Azure Storage 로그에서 사용할 수 있는 필드에 대한 참조는 [리소스 로그(미리 보기)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview)를 참조하세요.

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>공유 키 또는 SAS로 이루어진 요청에 대한 로그 쿼리

Azure Monitor의 Azure Storage 로그에는 스토리지 계정에 요청하는 데 사용된 권한 부여 유형이 포함됩니다. 공유 키 또는 SAS로 권한이 부여된 지난 7일 동안 이루어진 요청에 대한 로그를 검색하려면 Log Analytics 작업 영역을 엽니다. 그 후 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행합니다. 이 쿼리는 공유 키 또는 SAS로 권한이 부여된 요청을 가장 자주 보낸 10개의 IP 주소를 표시합니다.

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

이 쿼리를 기반으로, 공유 키 또는 SAS로 권한이 부여된 요청에 대해 알리도록 경고 규칙을 구성할 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-log.md)를 참조하세요.

## <a name="remediate-authorization-via-shared-key"></a>공유 키를 통해 권한 부여 수정

스토리지 계정에 대한 요청에 권한이 부여되는 방식을 분석한 후 공유 키를 통한 액세스를 막는 조치를 취할 수 있습니다. 하지만 먼저, Azure AD를 대신 사용하려면 공유 키 권한 부여를 사용하는 모든 애플리케이션을 업데이트해야 합니다. [클라이언트 애플리케이션에 사용되는 권한 부여 유형 검색](#detect-the-type-of-authorization-used-by-client-applications)의 설명대로 로그 및 메트릭을 모니터링하여 전환을 추적할 수 있습니다. Azure AD를 사용하여 스토리지 계정의 데이터에 액세스하는 방법에 대한 자세한 내용은 [Azure Storage의 데이터에 대한 액세스 권한 부여](authorize-data-access.md)를 참조하세요.

공유 키로 권한이 부여된 요청을 안전하게 거부할 수 있다고 확신하는 경우 스토리지 계정에 대한 **AllowSharedKeyAccess** 속성을 **false** 로 설정할 수 있습니다.

이 **AllowSharedKeyAccess** 속성은 기본적으로 설정되지 않으며 명시적으로 설정할 때까지 값을 반환하지 않습니다. 스토리지 계정은 속성 값이 **null** 이거나 **true** 인 경우 공유 키로 권한이 부여된 요청을 허용합니다.

> [!WARNING]
> 현재 공유 키를 사용하여 스토리지 계정의 데이터에 액세스하는 클라이언트가 있으면, 스토리지 계정에 대한 공유 키 액세스를 불허하기 전에 해당 클라이언트를 Azure AD로 마이그레이션하는 것이 좋습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 스토리지 계정에 대한 공유 키 권한 부여를 허용하지 않으려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 에서 **구성** 설정을 찾습니다.
1. **공유 키 액세스 허용** 을 **사용 안 함** 으로 설정합니다.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="계정에 대한 공유 키 액세스를 허용하지 않는 방법을 보여주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 스토리지 계정에 대한 공유 키 권한 부여를 허용하지 않으려면 [Az.Storage PowerShell 모듈](https://www.powershellgallery.com/packages/Az.Storage), 버전 3.4.0 이상을 설치합니다. 다음으로, 신규 또는 기존 스토리지 계정에 대해 **AllowSharedKeyAccess** 속성을 구성합니다.

다음 예제에서는 PowerShell을 사용하여 기존 스토리지 계정에 대해 공유 키를 사용한 액세스를 허용하지 않는 방법을 보여줍니다. 괄호 안의 자리 표시자 값은 자체적인 값으로 바꿔야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -AllowSharedKeyAccess $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대한 공유 키 권한 부여를 허용하지 않으려면 Azure CLI 버전 2.20.0 이상을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 다음으로, 신규 또는 기존 스토리지 계정에 대해 **allowSharedKeyAccess** 속성을 구성합니다.

다음 예제에서는 Azure CLI를 사용하여 기존 스토리지 계정에 대해 공유 키를 사용한 액세스를 허용하지 않는 방법을 보여줍니다. 괄호 안의 자리 표시자 값은 자체적인 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-shared-key-access false
```

---

공유 키 권한 부여를 허용하지 않으면 공유 키 권한 부여를 사용하여 스토리지 계정에 보낸 요청이 실패하고 오류 코드 403(금지됨)이 표시됩니다. Azure Storage는 키 기반 인증이 스토리지 계정에서 허용되지 않는다는 오류를 반환합니다.

**AllowSharedKeyAccess** 속성은 Azure Resource Manager 배포 모델을 사용하는 저장소 계정에 대해서만 지원됩니다. 어떤 스토리지 계정이 Azure Resource Manager 배포 모델을 사용하는지에 대한 자세한 내용은 [스토리지 계정 유형](storage-account-overview.md#types-of-storage-accounts)을 참조하세요.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>공유 키 액세스가 허용되지 않는지 확인

공유 키 권한 부여가 더 이상 허용되지 않는지 확인하려면 계정 액세스 키를 사용하여 데이터 작업을 호출합니다. 다음 예에서는 액세스 키를 사용하여 컨테이너를 만들려고 시도합니다. 이 호출은 스토리지 계정에 대해 공유 키 권한 부여가 허용되지 않으면 실패합니다. 괄호 안의 자리 표시자 값은 자체적인 값으로 바꿔야 합니다.

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key> \
    --auth-mode key
```

> [!NOTE]
> 익명 요청에는 권한이 부여되지 않으며 스토리지 계정 및 컨테이너에 익명 공용 읽기 권한을 구성한 경우 계속됩니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 공용 읽기 권한 구성](../blobs/anonymous-read-access-configure.md)을 참조하세요.

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>여러 계정에 대한 공유 키 액세스 설정 확인

최적의 성능을 가진 일련의 스토리지 계정에서 공유 키 액세스 설정을 확인하려면 Azure Portal에서 Azure Resource Graph Explorer를 사용하면 됩니다. Resource Graph Explorer를 사용하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure Resource Graph Explorer를 사용하여 첫 번째 Resource Graph 쿼리 실행](../../governance/resource-graph/first-query-portal.md)을 참조하세요.

Resource Graph Explorer에서 다음 쿼리를 실행하면 스토리지 계정 목록이 반환되고 각 계정에 대한 공유 키 액세스 설정이 표시됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>공유 키 액세스를 허용하거나 불허하는 권한

스토리지 계정에 대한 **AllowSharedKeyAccess** 속성을 설정하려면 사용자에게 스토리지 계정을 만들고 관리할 수 있는 권한이 있어야 합니다. 이러한 권한을 제공하는 Azure RBAC(Azure 역할 기반 액세스 제어) 역할에는 **Microsoft.Storage/storageAccounts/write** 또는 **Microsoft.Storage/storageAccounts/\*** 작업이 포함됩니다. 이 작업이 포함된 기본 제공 역할은 다음과 같습니다.

- Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할
- Azure Resource Manager [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할
- [스토리지 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 역할

이러한 역할은 Azure AD(Azure Active Directory)를 통해 스토리지 계정의 데이터에 대한 액세스 권한을 제공하지 않습니다. 그러나 계정 액세스 키에 대한 액세스 권한을 부여하는 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함되어 있습니다. 이 권한이 있는 사용자는 계정 액세스 키를 사용하여 스토리지 계정의 모든 데이터에 액세스할 수 있습니다.

스토리지 계정에 대한 공유 키 액세스를 사용자가 허용하거나 불허할 수 있도록 하려면 역할 할당의 범위를 스토리지 계정 수준 이상으로 지정해야 합니다. 역할 범위에 대한 자세한 내용은 [Azure RBAC의 범위 이해](../../role-based-access-control/scope-overview.md)를 참조하세요.

이러한 역할은 스토리지 계정을 만들거나 해당 속성을 업데이트하는 기능이 필요한 사용자에게만 제한적으로 할당해야 합니다. 최소 권한의 원칙을 사용하여 사용자에게 작업을 수행하는 데 필요한 최소 권한을 부여합니다. Azure RBAC를 사용하여 액세스를 관리하는 방법에 대한 자세한 내용은 [Azure RBAC 모범 사례](../../role-based-access-control/best-practices.md)를 참조하세요.

> [!NOTE]
> 클래식 구독 관리자 역할인 서비스 관리자 및 공동 관리자에는 Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할에 해당하는 항목이 포함됩니다. **소유자** 역할은 모든 작업을 포함하므로 이러한 관리 역할 중 하나가 있는 사용자는 스토리지 계정을 만들고 관리할 수도 있습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)을 참조하세요.

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>공유 키를 불허하는 경우 SAS 토큰에 미치는 영향 이해

스토리지 계정에 대해 공유 키 액세스가 허용되지 않으면 Azure Storage는 SAS의 유형 및 요청의 대상인 서비스를 기반으로 SAS 토큰을 처리합니다. 다음 표에서는 각 유형의 SAS에 권한이 부여되는 방식과 스토리지 계정의 **AllowSharedKeyAccess** 속성이 **false** 인 경우 Azure Storage가 해당 SAS를 처리하는 방식을 보여줍니다.

| SAS 유형 | 권한 부여 유형 | AllowSharedKeyAccess가 false인 경우의 동작 |
|-|-|-|
| 사용자 위임 SAS(Blob Storage에만 해당) | Azure AD | 요청이 허용됩니다. Microsoft는 보안을 강화하기 위해 가능한 경우 사용자 위임 SAS를 사용할 것을 권장합니다. |
| 서비스 SAS | 공유 키 | 모든 Azure Storage 서비스에 대한 요청이 거부됩니다. |
| 계정 SAS | 공유 키 | 모든 Azure Storage 서비스에 대한 요청이 거부됩니다. |

Azure Monitor의 Azure 메트릭 및 로깅은 서로 다른 유형의 공유 액세스 서명을 구분하지 않습니다. Azure 메트릭 탐색기의 **SAS** 필터와 Azure Monitor의 Azure Storage 로깅에 있는 **SAS** 필드는 둘 다 모든 유형의 SAS로 권한이 부여된 요청을 보고합니다. 단, 공유 키 액세스가 불허된 경우 서로 다른 유형의 공유 액세스 서명은 다르게 권한이 부여되고 다르게 작동합니다.

- 서비스 SAS 토큰 또는 계정 SAS 토큰은 **AllowSharedKeyAccess** 속성이 **false** 로 설정된 경우 공유 키를 사용하여 권한이 부여되며 Blob Storage에 대한 요청에서 허용되지 않습니다.
- 사용자 위임 SAS는 **AllowSharedKeyAccess** 속성이 **false** 로 설정된 경우 Azure AD를 사용하여 권한이 부여되며 Blob Storage에 대한 요청에서 허용됩니다.

스토리지 계정에 대한 트래픽을 평가할 때는 [클라이언트 애플리케이션에 사용되는 권한 부여 유형 검색](#detect-the-type-of-authorization-used-by-client-applications)에 설명된 메트릭 및 로그에 사용자 위임 SAS를 통해 이루어진 요청이 포함될 수 있다는 점에 유의하세요.

공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)를 참조하세요.

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>다른 Azure 도구 및 서비스와의 호환성 고려

여러 Azure 서비스가 공유 키 인증을 사용하여 Azure Storage와 통신합니다. 스토리지 계정에 대한 공유 키 인증을 허용하지 않으면 이러한 서비스가 해당 계정의 데이터에 액세스할 수 없으며 애플리케이션에 부정적인 영향을 줄 수 있습니다.

일부 Azure 도구는 Azure AD 권한 부여를 사용하여 Azure Storage에 액세스하는 옵션을 제공합니다. 다음 표에는 몇 가지 인기 있는 Azure 도구와 이러한 도구가 Azure AD를 사용하여 Azure Storage에 대한 요청에 권한을 부여할 수 있는지 여부에 대한 메모가 나와 있습니다.

| Azure 도구 | Azure Storage에 대한 Azure AD 권한 부여 |
|-|-|
| Azure Portal | 지원됨. Azure Portal에서 Azure AD 계정을 사용하여 권한을 부여하는 방법에 대한 자세한 내용은 [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택](../blobs/authorize-data-operations-portal.md)을 참조하세요. |
| AzCopy | Blob Storage에 지원됩니다. AzCopy 작업에 권한을 부여하는 방법에 대한 자세한 내용은 AzCopy 설명서에서 [권한 부여 자격 증명을 제공하는 방법 선택](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials)을 참조하세요. |
| Azure Storage Explorer | Blob Storage 및 Azure Data Lake Storage Gen2에만 지원됩니다. Queue Storage에 대한 Azure AD 액세스는 지원되지 않습니다. 올바른 Azure AD 테넌트를 선택해야 합니다. 자세한 내용은 [Storage Explorer 시작](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure)을 참조하세요. |
| Azure PowerShell | 지원됨. Azure AD를 사용하여 Blob 또는 큐 작업에 대한 PowerShell 명령에 권한을 부여하는 방법에 대한 자세한 내용은 [Azure AD 자격 증명으로 PowerShell 명령을 실행하여 Blob 데이터에 액세스](../blobs/authorize-data-operations-powershell.md) 또는 [Azure AD 자격 증명으로 PowerShell 명령을 실행하여 큐 데이터에 액세스](../queues/authorize-data-operations-powershell.md)를 참조하세요. |
| Azure CLI | 지원됨. Blob 및 큐 데이터에 액세스하기 위해 Azure AD를 사용하여 Azure CLI 명령에 권한을 부여하는 방법에 대한 자세한 내용은 [Azure AD 자격 증명으로 Azure CLI 명령을 실행하여 Blob 또는 큐 데이터에 액세스](../blobs/authorize-data-operations-cli.md)를 참조하세요. |
| Azure IoT Hub | 지원됨. 자세한 내용은 [가상 네트워크에 대한 IoT Hub 지원](../../iot-hub/virtual-network-support.md)을 참조하세요. |
| Azure Cloud Shell | Azure Cloud Shell은 Azure Portal의 통합 셸입니다. Azure Cloud Shell은 스토리지 계정의 Azure 파일 공유에서 지속성을 위해 파일을 호스트합니다. 이러한 파일은 해당 스토리지 계정에 대해 공유 키 권한 부여가 허용되지 않으면 액세스할 수 없게 됩니다. 자세한 내용은 [Microsoft Azure Files 스토리지 연결](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage)을 참조하세요. <br /><br /> Azure Cloud Shell에서 명령을 실행하여 공유 키 액세스가 허용되지 않는 스토리지 계정을 관리하려면 먼저 Azure RBAC를 통해 이러한 계정에 필요한 권한이 부여되었는지 확인합니다. 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요. |

## <a name="transition-azure-files-and-table-storage-workloads"></a>Azure Files 및 Table Storage 워크로드 전환

Azure Storage는 Blob 및 Queue Storage에 대한 요청에 대해서만 Azure AD 인증을 지원합니다. 스토리지 계정에 대해 공유 키를 사용한 권한 부여를 불허하면 공유 키 권한 부여를 사용하는 Azure Files 또는 Table Storage에 대한 요청은 실패합니다. Azure Portal은 항상 공유 키 권한 부여를 사용하여 파일 및 테이블 데이터에 액세스하기 때문에 스토리지 계정에 대해 공유 키를 사용한 권한 부여를 불허하면 Azure Portal의 파일 또는 테이블 데이터에 액세스할 수 없습니다.

공유 키를 통한 계정 액세스를 불허하기 전에 Azure Files 또는 Table Storage 데이터를 별도의 스토리지 계정으로 마이그레이션하거나, Azure Files 또는 Table Storage 워크로드를 지원하는 스토리지 계정에는 이 설정을 적용하지 않는 것이 좋습니다.

스토리지 계정에 대한 공유 키 액세스를 불허해도 Azure Files에 대한 SMB 연결에는 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage의 데이터에 대한 액세스 권한 부여](./authorize-data-access.md)
- [Azure Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여](authorize-data-access.md)
- [공유 키로 권한 부여](/rest/api/storageservices/authorize-with-shared-key)