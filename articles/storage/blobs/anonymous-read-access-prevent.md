---
title: 컨테이너 및 Blob에 대한 익명 공용 읽기 권한 방지
titleSuffix: Azure Storage
description: 스토리지 계정에 대한 익명 요청을 분석하는 방법과 전체 스토리지 계정 또는 개별 컨테이너에 대한 익명 액세스를 방지하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 884f70367c7e7edaf546b65b4e0e2825dacf0a07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577938"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>컨테이너 및 Blob에 대한 익명 공용 읽기 권한 방지

Azure Storage의 컨테이너 및 Blob에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 익명 액세스를 신중하게 관리하고 데이터에 대한 익명 액세스를 평가하는 방법을 이해하는 것이 중요합니다. 운영상의 복잡성, 사람의 실수 또는 공개적으로 액세스할 수 있는 데이터에 대한 악의적인 공격으로 인해 비용이 많이 드는 데이터 위반이 발생할 수 있습니다. 익명 액세스는 애플리케이션 시나리오에 필요한 경우에만 사용하도록 설정하는 것이 좋습니다.

기본적으로 Blob 데이터에 대한 공용 액세스는 항상 금지됩니다. 하지만, 스토리지 계정의 기본 구성을 사용하면 적절한 권한이 있는 사용자가 스토리지 계정의 컨테이너 및 Blob에 대한 공용 액세스를 구성할 수 있습니다. 보안 강화를 위해, 개별 컨테이너에 대한 공용 액세스 설정에 관계없이 스토리지 계정에 대한 모든 공용 액세스를 불허할 수 있습니다. 스토리지 계정에 대한 공용 액세스를 허용하지 않으면 사용자가 계정의 컨테이너에 대한 공용 액세스를 사용하도록 설정할 수 없습니다. Microsoft는 시나리오에 필요한 경우를 제외하고 스토리지 계정에 대한 공용 액세스를 허용하지 않는 것을 권장합니다. 공용 액세스를 허용하지 않으면 원치 않는 익명 액세스로 인한 데이터 위반을 방지할 수 있습니다.

스토리지 계정에 대한 공용 Blob 액세스를 허용하지 않으면 Azure Storage는 해당 계정에 대한 모든 익명 요청을 거부합니다. 계정에 대한 공용 액세스를 허용하지 않으면 해당 계정의 컨테이너는 나중에 공용 액세스가 가능하도록 구성할 수 없습니다. 이미 공용 액세스가 가능하도록 구성된 컨테이너가 있으면 더 이상 익명 요청을 수락하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 공용 읽기 권한 구성](anonymous-read-access-configure.md)을 참조하세요.

이 문서에서는 DRAG(감지-수정-감사-거버넌스) 프레임워크를 사용하여 스토리지 계정에 대한 공용 액세스를 지속적으로 관리하는 방법을 설명합니다.

## <a name="detect-anonymous-requests-from-client-applications"></a>클라이언트 애플리케이션의 익명 요청 감지

스토리지 계정에 대한 공용 읽기 권한을 허용하지 않으면 현재 공용 액세스용으로 구성된 컨테이너 및 Blob에 대한 요청이 거부될 위험이 있습니다. 스토리지 계정에 대한 공용 액세스를 불허하면 해당 스토리지 계정의 개별 컨테이너에 대한 공용 액세스 설정이 재정의됩니다. 스토리지 계정에 대한 공용 액세스가 허용되지 않으면 해당 계정에 대한 향후 익명 요청은 실패합니다.

공용 액세스를 불허하는 경우 클라이언트 애플리케이션에 미치는 영향을 파악하려면 해당 계정에 대한 로깅 및 메트릭을 사용하도록 설정하고 일정 시간 간격에 대한 익명 요청의 패턴을 분석하는 것이 좋습니다. 메트릭을 사용하여 스토리지 계정에 대한 익명 요청 수를 확인하고 로그를 사용하여 익명으로 액세스되는 컨테이너를 확인합니다.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>메트릭 탐색기를 사용하여 익명 요청 모니터링

스토리지 계정에 대한 익명 요청을 추적하려면 Azure Portal에서 Azure 메트릭 탐색기를 사용합니다. 메트릭 탐색기에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

익명 요청을 추적하는 메트릭을 만들려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다. **모니터링** 섹션에서 **메트릭** 을 선택합니다.
1. **메트릭 추가** 를 선택합니다. **메트릭** 대화 상자에서 다음 값을 지정합니다.
    1. 범위 필드는 스토리지 계정의 이름으로 설정된 상태로 둡니다.
    1. **메트릭 네임스페이스** 를 *Blob* 으로 설정합니다. 이 메트릭은 Blob Storage에 대한 요청만 보고합니다.
    1. **메트릭** 필드를 *Transactions* 로 설정합니다.
    1. **집계** 필드는 *Sum* 으로 설정합니다.

    새 메트릭은 지정된 시간 간격 동안 Blob Storage에 대한 트랜잭션 수의 합계를 표시합니다. 결과 메트릭은 다음 이미지와 같이 나타납니다.

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Blob 트랜잭션을 합산하는 메트릭을 구성하는 방법을 보여주는 스크린샷":::

1. 그런 다음, **필터 추가** 단추를 선택하여 익명 요청에 대한 메트릭에 대한 필터를 만듭니다.
1. **필터** 대화 상자에서 다음 값을 지정합니다.
    1. **속성** 값을 *Authentication* 으로 설정합니다.
    1. **연산자** 필드를 등호(=)로 설정합니다.
    1. 드롭다운에서 선택하거나 입력하여 **값** 필드를 *익명으로* 설정합니다.
1. 오른쪽 위 모서리에서 메트릭을 보려는 시간 간격을 선택합니다. 간격을 1분에서 1개월 사이로 지정하여 요청 집계의 세분화 정도를 나타낼 수도 있습니다.

메트릭을 구성하면 익명 요청이 그래프에 나타나기 시작합니다. 다음 이미지는 지난 30분 동안 집계된 익명 요청을 보여줍니다.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Blob Storage에 대해 집계된 익명 요청을 보여주는 스크린샷":::

스토리지 계정에 대해 특정 수의 익명 요청이 있을 때 알려주도록 경고 규칙을 구성할 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>로그를 분석하여 익명 요청을 받는 컨테이너 식별

Azure Storage 로그는 스토리지 계정에 대한 요청의 세부 정보(요청에 권한이 부여된 방법 포함)를 캡처합니다. 로그를 분석하여 익명 요청을 받은 컨테이너를 확인할 수 있습니다.

익명 요청을 평가하기 위해 Azure Storage 계정에 요청을 로그하려면 Azure Monitor(미리 보기)에서 Azure Storage 로깅을 사용하면 됩니다. 자세한 내용은 [Azure Storage 모니터링](./monitor-blob-storage.md)을 참조하세요.

Azure Monitor의 Azure Storage 로깅은 로그 쿼리를 사용하여 로그 데이터를 분석하도록 지원합니다. 로그를 쿼리하려면 Azure Log Analytics 작업 영역을 사용사면 됩니다. 로그 쿼리에 대해 자세히 알아보려면 [자습서: Log Analytics 쿼리 시작하기](../../azure-monitor/logs/log-analytics-tutorial.md)를 참조하세요.

> [!NOTE]
> Azure Monitor의 Azure Storage 로깅 미리 보기는 Azure 퍼블릭 클라우드에서만 지원됩니다. 정부 클라우드는 Azure Monitor에서 Azure Storage에 대한 로깅을 지원하지 않습니다.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure Portal에서 진단 설정 만들기

Azure Monitor를 사용하여 Azure Storage 데이터를 로깅하고 Azure Log Analytics를 사용하여 분석하려면 먼저 데이터를 로그할 요청 유형과 스토리지 서비스를 나타내는 진단 설정을 만들어야 합니다. Azure Portal에서 진단 설정을 만들려면 다음 단계를 수행합니다.

1. Azure Storage 계정을 포함하는 구독에 새 Log Analytics 작업 영역을 만듭니다. 스토리지 계정에 대한 로깅을 구성하면 Log Analytics 작업 영역에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.
1. Azure Portal의 스토리지 계정으로 이동합니다.
1. 모니터링 섹션에서 **진단 설정(미리 보기)** 을 선택합니다.
1. Blob Storage에 대한 요청을 로그하려면 **Blob** 을 선택합니다.
1. **진단 설정 추가** 를 선택합니다.
1. 진단 설정의 이름을 제공합니다.
1. **범주 세부 정보** 의 **로그** 섹션에서 로그할 요청 유형을 선택합니다. 모든 익명 요청은 읽기 요청이므로 익명 요청을 캡처하려면 **StorageRead** 를 선택합니다.
1. **대상 세부 정보** 에서 **Log Analytics에 보내기** 를 선택합니다. 다음 이미지와 같이, 구독 및 이전에 만든 Log Analytics 작업 영역을 선택합니다.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="로깅 요청에 대한 진단 설정을 만드는 방법을 보여주는 스크린샷":::

진단 설정을 만든 후에는 이 설정에 따라 스토리지 계정에 대한 요청이 로그됩니다. 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집하기 위한 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

Azure Monitor의 Azure Storage 로그에서 사용할 수 있는 필드에 대한 참조는 [리소스 로그(미리 보기)](./monitor-blob-storage-reference.md#resource-logs-preview)를 참조하세요.

#### <a name="query-logs-for-anonymous-requests"></a>익명 요청에 대한 로그 쿼리

Azure Monitor의 Azure Storage 로그에는 스토리지 계정에 요청하는 데 사용된 권한 부여 유형이 포함됩니다. 로그 쿼리에서 **AuthenticationType** 속성을 필터링하여 익명 요청을 볼 수 있습니다.

Blob Storage에 대한 익명 요청의 지난 7일간 로그를 검색하려면 Log Analytics 작업 영역을 엽니다. 그 후 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

이 쿼리를 기반으로 익명 요청에 대해 알리도록 경고 규칙을 구성할 수도 있습니다. 자세한 내용은 [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-log.md)를 참조하세요.

## <a name="remediate-anonymous-public-access"></a>익명 공용 액세스 수정

스토리지 계정의 컨테이너 및 Blob에 대한 익명 요청을 평가한 후에는 공용 액세스를 제한하거나 방지하는 조치를 취할 수 있습니다. 스토리지 계정의 일부 컨테이너는 공용 액세스가 가능하도록 설정해야 하는 경우에는 스토리지 계정의 각 컨테이너에 대한 공용 액세스 설정을 구성할 수 있습니다. 이 옵션은 공용 액세스에 대한 가장 세부적인 제어를 제공합니다. 자세한 내용은 [컨테이너에 대한 공용 액세스 수준 설정](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)을 참조하세요.

보안 강화를 위해 전체 스토리지 계정에 대한 공용 액세스를 불허할 수 있습니다. 스토리지 계정에 대한 공용 액세스 설정은 해당 계정의 컨테이너에 대한 개별 설정을 재정의합니다. 스토리지 계정에 대한 공용 액세스를 허용하지 않으면 공용 액세스를 허용하도록 구성된 컨테이너에 더 이상 익명으로 액세스할 수 없습니다. 자세한 내용은 [스토리지 계정에 대한 공용 읽기 권한 허용 또는 허용 안 함](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)을 참조하세요.

특정 컨테이너를 공용 액세스에 사용할 수 있어야 하는 시나리오에서는 해당 컨테이너와 해당 Blob을 공용 액세스용으로 예약된 스토리지 계정으로 이동하는 것이 좋습니다. 그런 다음, 다른 스토리지 계정에 대한 공용 액세스를 허용하지 않으면 됩니다.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Blob에 대한 공용 액세스가 허용되지 않는지 확인

특정 Blob에 대한 공용 액세스가 허용되지 않는지 확인하려면 해당 URL을 통해 Blob 다운로드를 시도하면 됩니다. 다운로드가 성공하면 Blob은 아직 공개적으로 사용이 가능한 것입니다. 스토리지 계정에 대한 공용 액세스가 허용되지 않아서 Blob에 공개적으로 액세스할 수 없는 경우에는 해당 스토리지 계정에 대한 공용 액세스가 허용되지 않는다는 오류 메시지가 표시됩니다.

다음 예제에서는 PowerShell을 사용하여 URL을 통해 Blob을 다운로드하는 방법을 보여줍니다. 괄호 안의 자리 표시자 값은 자체적인 값으로 바꿔야 합니다.

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>컨테이너의 공용 액세스 설정 수정이 허용되지 않는지 확인

스토리지 계정에 대한 공용 액세스를 허용하지 않는 후 컨테이너의 공용 액세스 설정을 수정할 수 없는지 확인하려면 설정을 수정하려고 시도해보면 됩니다. 스토리지 계정에 대한 공용 액세스가 허용되지 않는 경우 컨테이너의 공용 액세스 설정을 변경하면 실패합니다.

다음 예제는 PowerShell을 사용하여 컨테이너의 공용 액세스 설정을 변경하려고 시도하는 방법을 보여줍니다. 괄호 안의 자리 표시자 값은 자체적인 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>공용 액세스를 사용하도록 설정된 컨테이너 만들기가 허용되지 않는지 확인

스토리지 계정에 대한 공용 액세스가 허용되지 않으면 공용 액세스를 사용하도록 설정된 새 컨테이너를 만들 수 없습니다. 확인하려면 공용 액세스를 사용하도록 설정된 컨테이너 만들기를 시도해보면 됩니다.

다음 예제는 PowerShell을 사용하여 공용 액세스를 사용하도록 설정된 컨테이너 만들기를 시도하는 방법을 보여줍니다. 괄호 안의 자리 표시자 값은 자체적인 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>여러 계정에 대한 공용 액세스 설정 확인

최적의 성능을 가진 일련의 스토리지 계정에서 공용 액세스 설정을 확인하려면 Azure Portal에서 Azure Resource Graph Explorer를 사용하면 됩니다. Resource Graph Explorer를 사용하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure Resource Graph Explorer를 사용하여 첫 번째 Resource Graph 쿼리 실행](../../governance/resource-graph/first-query-portal.md)을 참조하세요.

**AllowBlobPublicAccess** 속성은 기본적으로 스토리지 계정에 대해 설정되지 않으며 명시적으로 설정할 때까지 값을 반환하지 않습니다. 저장소 계정은 속성 값이 **null** 또는 **true** 인 경우 공용 액세스를 허용합니다.

Resource Graph Explorer에서 다음 쿼리를 실행하면 스토리지 계정 목록이 반환되고 각 계정에 대한 공용 액세스 설정이 표시됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

다음 이미지는 구독에 대한 쿼리 결과를 보여줍니다. **AllowBlobPublicAccess** 속성이 명시적으로 설정된 스토리지 계정의 경우 결과에 **true** 또는 **false** 로 나타납니다. 스토리지 계정에 대해 **AllowBlobPublicAccess** 속성이 설정되지 않은 경우 쿼리 결과에 공백(또는 null)으로 나타납니다.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="스토리지 계정 전체의 공용 액세스 설정에 대한 쿼리 결과를 보여주는 스크린샷":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy를 사용하여 규정 준수 감사

스토리지 계정이 많은 경우, 감사를 수행하여 해당 계정이 공용 액세스를 차단하도록 구성되었는지 확인하는 것이 좋습니다. 일련의 스토리지 계정에서 규정 준수 여부를 감사하려면 Azure Policy를 사용합니다. Azure Policy는 Azure 리소스에 규칙을 적용하는 정책을 만들고 할당하고 관리하는 데 사용할 수 있는 서비스입니다. Azure Policy는 해당 리소스 가 회사 표준 및 서비스 수준 계약을 준수하도록 유지하는 데 도움이 됩니다. 자세한 내용은 [Azure Policy 개요](../../governance/policy/overview.md)를 참조하세요.

### <a name="create-a-policy-with-an-audit-effect"></a>감사 효과를 사용하여 정책 만들기

Azure Policy는 리소스에 대해 정책 규칙을 평가할 때 발생하는 작업을 결정하는 효과를 지원합니다. 감사 효과는 리소스가 정책을 준수하지 않는 경우 경고를 생성하지만 요청을 중지하지는 않습니다. 효과에 대한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조하세요.

Azure Portal에서 스토리지 계정의 공용 액세스 설정에 대해 감사 효과가 있는 정책을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **작성** 섹션에서 **정의** 를 선택합니다.
1. **정책 정의 추가** 를 선택하여 새 정책 정의를 만듭니다.
1. **정의 위치** 필드의 경우 **자세히** 단추를 선택하여 감사 정책 리소스가 있는 위치를 지정합니다.
1. 정책의 이름을 지정합니다. 설명 및 범주를 선택적으로 지정할 수 있습니다.
1. **정책 규칙** 에서 다음 정책 정의를 **policyRule** 섹션에 추가합니다.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 해당 정책을 저장합니다.

### <a name="assign-the-policy"></a>정책 할당

다음으로 리소스에 정책을 할당합니다. 정책의 범위는 해당 리소스 및 그 아래에 있는 모든 리소스에 해당합니다. 정책 할당에 대한 자세한 내용은 [Azure Policy 할당 구조](../../governance/policy/concepts/assignment-structure.md)를 참조하세요.

Azure Portal에서 정책을 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **작성** 섹션에서 **할당** 을 선택합니다.
1. **정책 할당** 을 선택하여 새 정책 할당을 만듭니다.
1. **범위** 필드에서 정책 할당의 범위를 선택합니다.
1. **정책 정의** 필드에서 **자세히** 단추를 선택한 다음, 이전 섹션에서 정의한 정책을 목록에서 선택합니다.
1. 정책 할당의 이름을 제공합니다. 설명은 선택 사항입니다.
1. **정책 적용** 을 *사용 가능* 으로 유지합니다. 이 설정은 감사 정책에 영향을 주지 않습니다.
1. **검토 + 만들기** 를 선택하여 할당을 만듭니다.

### <a name="view-compliance-report"></a>규정 준수 보고서 보기

정책을 할당한 후에는 규정 준수 보고서를 볼 수 있습니다. 감사 정책에 대한 규정 준수 보고서는 정책을 준수하지 않는 스토리지 계정에 대한 정보를 제공합니다. 자세한 내용은 [정책 규정 준수 데이터 가져오기](../../governance/policy/how-to/get-compliance-data.md)를 참조하세요.

정책 할당을 만든 후 규정 준수 보고서를 사용할 수 있게 되는 데 몇 분 정도 걸릴 수 있습니다.

Azure Portal에서 규정 준수 보고서를 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **규정 준수** 를 선택합니다.
1. 이전 단계에서 만든 정책 할당의 이름에 대한 결과를 필터링합니다. 이 보고서에는 정책을 준수하지 않는 리소스의 수가 표시됩니다.
1. 정책을 준수하지 않는 스토리지 계정 목록을 포함하여 추가 세부 정보에 대해 보고서를 드릴다운할 수 있습니다.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Blob 공용 액세스에 대한 감사 정책의 준수 보고서를 보여주는 스크린샷":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Azure Policy를 사용하여 권한 있는 액세스 적용

Azure Policy는 Azure 리소스가 요구 사항 및 표준을 준수하도록 하여 클라우드 거버넌스를 지원합니다. 조직의 스토리지 계정이 권한 있는 요청만 허용하도록 하려면 익명 요청을 허용하는 공용 액세스 설정을 사용하여 새 스토리지 계정을 만들 수 없도록 하는 정책을 만들면 됩니다. 또한 이 정책은 해당 계정의 공용 액세스 설정이 정책을 준수하지 않는 경우 기존 계정에 대한 모든 구성 변경을 방지합니다.

적용 정책은 거부 효과를 사용하여 공용 액세스를 허용하기 위해 스토리지 계정을 만들거나 수정하는 요청을 방지합니다. 효과에 대한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조하세요.

익명 요청을 허용하는 공용 액세스 설정에 대해 거부 효과가 적용된 정책을 만들려면 [Azure Policy를 사용하여 규정 준수 감사](#use-azure-policy-to-audit-for-compliance)에 설명된 것과 동일한 단계를 수행하지만 정책 정의의 **policyRule** 섹션에는 다음 JSON을 제공합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

거부 효과가 있는 정책을 만들어서 범위에 할당한 후에는 공용 액세스를 허용하는 스토리지 계정을 사용자가 만들 수 없습니다. 또한, 현재 공용 액세스를 허용하는 기존 스토리지 계정의 구성을 사용자가 변경할 수 없습니다. 그렇게 하려고 하면 오류가 발생합니다. 계정 만들기 또는 구성을 계속하려면 스토리지 계정에 대한 공용 액세스 설정을 **false** 로 설정해야 합니다.

다음 이미지는 거부 효과가 있는 정책에 따라 공용 액세스를 허용하지 않아야 하는데, 공용 액세스를 허용하는(새 계정의 기본값) 스토리지 계정을 만들려고 시도할 때 발생하는 오류를 보여줍니다.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="정책을 위반하여 스토리지 계정을 만들 때 발생하는 오류를 보여주는 스크린샷":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>공용 액세스를 허용하거나 불허하는 권한

스토리지 계정에 대한 **AllowBlobPublicAccess** 속성을 설정하려면 사용자에게 스토리지 계정을 만들고 관리할 수 있는 권한이 있어야 합니다. 이러한 권한을 제공하는 Azure RBAC(Azure 역할 기반 액세스 제어) 역할에는 **Microsoft.Storage/storageAccounts/write** 또는 **Microsoft.Storage/storageAccounts/\*** 작업이 포함됩니다. 이 작업이 포함된 기본 제공 역할은 다음과 같습니다.

- Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할
- Azure Resource Manager [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할
- [스토리지 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 역할

이러한 역할은 Azure AD(Azure Active Directory)를 통해 스토리지 계정의 데이터에 대한 액세스 권한을 제공하지 않습니다. 그러나 계정 액세스 키에 대한 액세스 권한을 부여하는 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함되어 있습니다. 이 권한이 있는 사용자는 계정 액세스 키를 사용하여 스토리지 계정의 모든 데이터에 액세스할 수 있습니다.

스토리지 계정에 대한 공용 액세스를 사용자가 허용하거나 불허할 수 있도록 하려면 역할 할당의 범위를 스토리지 계정 수준 이상으로 지정해야 합니다. 역할 범위에 대한 자세한 내용은 [Azure RBAC의 범위 이해](../../role-based-access-control/scope-overview.md)를 참조하세요.

이러한 역할은 스토리지 계정을 만들거나 해당 속성을 업데이트하는 기능이 필요한 사용자에게만 제한적으로 할당해야 합니다. 최소 권한의 원칙을 사용하여 사용자에게 작업을 수행하는 데 필요한 최소 권한을 부여합니다. Azure RBAC를 사용하여 액세스를 관리하는 방법에 대한 자세한 내용은 [Azure RBAC 모범 사례](../../role-based-access-control/best-practices.md)를 참조하세요.

> [!NOTE]
> 클래식 구독 관리자 역할인 서비스 관리자 및 공동 관리자에는 Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할에 해당하는 항목이 포함됩니다. **소유자** 역할은 모든 작업을 포함하므로 이러한 관리 역할 중 하나가 있는 사용자는 스토리지 계정을 만들고 관리할 수도 있습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [컨테이너 및 Blob에 대한 익명 공용 읽기 권한 구성](anonymous-read-access-configure.md)
- [.NET을 사용하여 공용 컨테이너 및 Blob에 익명으로 액세스](anonymous-read-access-client.md)
