---
title: Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기(미리 보기)
description: Log Analytics 데이터 내보내기를 사용하면 데이터를 수집하는 동안 선택한 테이블의 데이터를 Log Analytics 작업 영역에서 Azure Storage 계정 또는 Azure Event Hubs로 계속 내보낼 수 있습니다.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
author: yossi-y
ms.author: yossiy
ms.date: 10/17/2021
ms.openlocfilehash: f5dc1ad57b745ee26f9edb1b9c31091a1aaec42c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069979"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기(미리 보기)
Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기를 사용하면 데이터를 수집하는 동안 Log Analytics 작업 영역에서 선택한 테이블의 데이터를 Azure Storage 계정 또는 Azure Event Hubs로 계속 내보낼 수 있습니다. 이 문서에서는 이 기능 및 작업 영역에서 데이터 내보내기를 구성하는 단계에 대한 세부 정보를 제공합니다.

## <a name="overview"></a>개요
Log Analytics 작업 영역에 대한 데이터 내보내기가 구성되면 작업 영역에서 선택한 테이블로 전송된 모든 새로운 데이터를 매시간 추가 Blob의 스토리지 계정으로 또는 거의 실시간으로 이벤트 허브로 자동으로 내보냅니다.

![데이터 내보내기 개요](media/logs-data-export/data-export-overview.png)

포함된 테이블의 모든 데이터를 필터 없이 내보냅니다. 예를 들어 *SecurityEvent* 테이블에 대한 데이터 내보내기 규칙을 구성하면 *SecurityEvent* 테이블에 전송된 모든 데이터를 구성 시간부터 내보냅니다.


## <a name="other-export-options"></a>기타 내보내기 옵션
Log Analytics 작업 영역 데이터 내보내기는 Log Analytics 작업 영역에서 데이터를 계속 내보냅니다. 특정 시나리오에 대해 데이터를 내보내기 위한 기타 옵션은 다음과 같습니다.

- 논리 앱을 사용하여 로그 쿼리에서 예약된 내보내기. 이는 데이터 내보내기 기능과 유사하지만 필터링된 데이터 또는 집계된 데이터를 Azure Storage로 보낼 수 있습니다. 그러나 이 방법에는 [로그 쿼리 한도](../service-limits.md#log-analytics-workspaces)가 적용됩니다. [논리 앱을 사용하여 Log Analytics 작업 영역에서 Azure Storage로 데이터 보관](logs-export-logic-app.md)을 참조하세요.
- PowerShell 스크립트를 사용하여 로컬 컴퓨터에 일회성 내보내기. [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)를 참조하세요.

## <a name="limitations"></a>제한 사항

- 구성은 현재 CLI 또는 REST 요청을 사용하여 수행할 수 있습니다. Azure Portal 또는 PowerShell은 아직 지원되지 않습니다.
- CLI 및 REST의 `--export-all-tables` 옵션은 지원되지 않으며 제거될 예정입니다. 내보내기 규칙에서 테이블 목록을 명시적으로 제공해야 합니다.
- 지원되는 테이블은 아래 [지원되는 테이블](#supported-tables) 섹션에 지정된 테이블로 제한됩니다. 
- 기존 사용자 지정 로그 테이블은 내보내기에서 지원되지 않습니다. 2022년 3월에 사용할 수 있는 새 사용자 지정 로그 버전이 지원됩니다.
- 데이터 내보내기 규칙에 지원되지 않는 테이블이 포함되어 있으면 작업이 성공하지만 테이블이 지원될 때까지 해당 테이블에 대한 데이터를 내보내지 않습니다. 
- 데이터 내보내기 규칙에 존재하지 않는 테이블이 포함되어 있으면 `Table <tableName> does not exist in the workspace` 오류와 함께 실패합니다.
- 작업 영역에 활성화된 규칙을 최대 10개까지 정의할 수 있습니다. 사용하지 않도록 설정하면 추가 규칙이 허용됩니다. 
- 대상은 작업 영역에 있는 모든 내보내기 규칙에서 고유해야 합니다.
- 대상은 Log Analytics 작업 영역과 동일한 지역에 있어야 합니다.
- 스토리지 계정으로 내보낼 때 테이블 이름은 60자, 이벤트 허브로 47자를 초과할 수 없습니다. 이름이 긴 테이블은 내보내지 않습니다.
- 데이터 내보내기 는 모든 지역에서 사용할 수 있지만 현재 다음에서 지원됩니다. 
    - 오스트레일리아 중부
    - 오스트레일리아 동부
    - 오스트레일리아 남동부
    - 브라질 남부
    - 캐나다 중부
    - 인도 중부
    - 미국 중부
    - 동아시아
    - 미국 동부
    - 미국 동부 2
    - 프랑스 중부
    - 독일 중서부
    - 일본 동부
    - 한국 중부
    - 미국 중북부
    - 북유럽
    - 남아프리카 북부
    - 미국 중남부
    - 동남아시아
    - 스위스 북부
    - 스위스 서부
    - 아랍에미리트 북부
    - 영국 남부
    - 영국 서부
    - 미국 중서부
    - 서유럽
    - 미국 서부
    - 미국 서부 2

## <a name="data-completeness"></a>데이터 완성도
데이터 내보내기는 대상을 사용할 수 없는 경우 최대 30분 동안 데이터 전송을 계속 다시 시도합니다. 30분 후에도 계속 사용할 수 없는 경우 대상을 사용할 수 있을 때까지 데이터를 버리게 됩니다.

## <a name="cost"></a>비용
현재 데이터 내보내기 기능에 대한 추가 요금은 없습니다. 데이터 내보내기에 대한 가격 책정은 나중에 발표될 예정이며 청구 시작 전에 미리 공지될 예정입니다. 알림 기간 후에도 계속 데이터 내보내기를 사용하도록 선택하면 해당되는 요금이 청구됩니다.

## <a name="export-destinations"></a>내보내기 대상

작업 영역에서 내보내기 규칙을 만들기 전에 데이터 내보내기 대상을 만들어야 합니다. 대상이 작업 영역과 동일한 구독에 있을 필요는 없습니다. Azure Lighthouse 사용하는 경우 다른 Azure Active Directory 테넌트에서 대상으로 데이터를 보낼 수도 있습니다.

### <a name="storage-account"></a>스토리지 계정

데이터 내보내기 규칙을 구성하려면 작업 영역과 대상 모두에 대한 '쓰기' 권한이 있어야 합니다. 데이터에 대한 액세스를 더 잘 제어하고 스토리지 수집 속도 제한 및 제한에 도달하지 않도록 모니터링하지 않는 다른 데이터가 저장된 기존 스토리지 계정을 사용하면 안 됩니다. 

데이터를 불변 가능한 스토리지로 보내려면 Blob Storage에 대한 불변성 정책 설정 및 관리에 설명된 대로 스토리지 계정에 [대한 불변성 정책을 설정합니다.](../../storage/blobs/immutable-policy-configure-version-scope.md) 보호된 추가 BLOB 쓰기 사용을 비롯하여 이 문서의 모든 단계를 따라야 합니다.

스토리지 계정은 작업 영역과 동일한 지역에 StorageV1 이상이어야 합니다. 다른 지역의 다른 스토리지 계정에 데이터를 복제해야 하는 경우 GRS 및 GZRS를 비롯한 [Azure Storage 중복성 옵션을](../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 사용할 수 있습니다.

데이터는 Azure Monitor에 도달하면 스토리지 계정으로 전송되고 매시간 추가 Blob에 저장됩니다. 스토리지 계정의 각 테이블에 대해 *am-* 이름 뒤에 테이블 이름이 오는 컨테이너가 만들어집니다. 예를 들어 *SecurityEvent* 테이블은 *am-SecurityEvent* 라는 이름의 컨테이너로 전송됩니다.

> [!NOTE]
> 적절한 수신 속도 할당을 위해 별도의 스토리지 계정을 사용하고 제한, 실패 및 대기 시간 이벤트를 줄이는 것이 좋습니다.

2021년 10월 15일부터 Blob은 *WorkspaceResourceId=/subscriptions/subscription-id/resourcegroups/ \<resource-group\> /providers/microsoft.operationalinsights/workspaces/ \<workspace\> /y= \<four-digit numeric year\> /m= \<two-digit numeric month\> /d= \<two-digit numeric day\> /h= \<two-digit 24-hour clock hour\> /m= \<two-digit 60-minute clock minute\> /PT05M.json* 경로 구조의 5분 폴더에 저장됩니다. 추가 Blob은 스토리지에서 50K 쓰기로 제한되므로 추가 수가 많으면 내보낸 Blob 수가 확대될 수 있습니다. 이러한 경우 Blob의 명명 패턴은 PT05M_#.json*입니다. 여기서 #은 증분 Blob 수입니다.

스토리지 계정 데이터 형식은 [JSON 줄](../essentials/resource-logs-blob-format.md)입니다. 즉, 각 레코드는 외부 레코드 배열이 없고 JSON 레코드 간에 쉼표가 없는 새줄로 구분됩니다. 

[![스토리지 샘플 데이터](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

### <a name="event-hub"></a>이벤트 허브

데이터 내보내기 규칙을 구성하려면 작업 영역과 대상 모두에 대한 '쓰기' 권한이 있어야 합니다. 이벤트 허브 네임스페이스에 대한 공유 액세스 정책은 스트리밍 메커니즘에 있는 권한을 정의합니다. 이벤트 허브로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요합니다. 내보내기 규칙을 업데이트하려면 해당 Event Hubs 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다.

이벤트 허브 네임스페이스는 작업 영역과 동일한 지역에 있어야 합니다.

데이터가 Azure Monitor 도달하면 이벤트 허브로 전송됩니다. 이벤트 허브는 내보내는 각 데이터 형식에 대해 생성되며 이름은 *am-* 뒤에 테이블 이름이 지정됩니다. 예를 들어 *SecurityEvent* 테이블은 *am-SecurityEvent* 라는 이름의 이벤트 허브로 전송됩니다. 내보낸 데이터를 특정 이벤트 허브에 연결하려는 경우 또는 이름이 47자 제한을 초과하는 테이블이 있는 경우, 고유한 이벤트 허브 이름을 제공하고 정의된 테이블의 모든 데이터를 내보낼 수 있습니다.

> [!NOTE]
> - '기본' 이벤트 허브 계층은 더 낮은 이벤트 크기 [제한을](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-vs-premium-vs-dedicated-tiers) 지원하며 작업 영역의 일부 로그가 이를 초과하여 삭제될 수 있습니다. 내보내기 대상에는 '표준', 'Premium' 또는 '전용' 계층을 사용합니다.
> - 내보낸 데이터의 볼륨은 시간이 지남에 따라 증가하며, 더 높은 수신 속도를 위해서는 결과 크기 조정이 필요합니다. 자동 **확장** 기능을 사용하여 사용량 요구 사항에 맞게 처리량 단위 수를 자동으로 확장하고 늘림 처리량 [단위 Azure Event Hubs 자동 강화를](../../event-hubs/event-hubs-auto-inflate.md)참조하세요.
> - 적절한 수신 속도 할당 및 제한, 실패 및 대기 시간 이벤트를 줄이기 위해 별도의 이벤트 허브 네임스페이스를 사용합니다.
> - 가상 네트워크를 사용하도록 설정하면 데이터 내보내기에서 이벤트 허브 리소스에 연결할 수 없습니다. Event Hubs 리소스에 대한 액세스 권한을 부여하려면 신뢰할 수 있는 **Microsoft 서비스** 허용 이벤트 허브에서 이 방화벽 설정을 무시하도록 설정해야 합니다.

> [!IMPORTANT]
> ['기본' 및 '표준' 네임스페이스 계층당 지원되는 이벤트 허브 수는 10개입니다](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). 10개가 넘는 테이블을 내보내는 경우, 여러 이벤트 허브 네임스페이스에 대한 여러 내보내기 규칙으로 테이블을 분할하거나, 내보내기 규칙에 이벤트 허브 이름을 지정하고 모든 테이블을 해당 이벤트 허브로 내보냅니다.

## <a name="enable-data-export"></a>데이터 내보내기 사용
Log Analytics 데이터 내보내기를 사용하도록 설정하려면 다음 단계를 수행해야 합니다. 각각에 대한 더 자세한 내용은 다음 섹션을 참조하세요.

- 리소스 공급자 등록
- 신뢰할 수 있는 Microsoft 서비스 허용
- 내보낼 테이블과 대상을 정의하는 하나 이상의 데이터 내보내기 규칙 만들기

### <a name="register-resource-provider"></a>리소스 공급자 등록
Log Analytics 데이터 내보내기를 사용하도록 설정하려면 다음 Azure 리소스 공급자를 구독에 등록해야 합니다. 

- Microsoft.Insights

이 리소스 공급자는 대부분의 Azure Monitor 사용자에 대해 이미 등록되어 있을 것입니다. 이를 확인하려면 Azure Portal에서 **구독** 으로 이동합니다. 구독을 선택한 다음, 메뉴의 **설정** 섹션에서 **리소스 공급자** 를 클릭합니다. **Microsoft.Insights** 를 찾습니다. 상태가 **등록됨** 이면 이미 등록되어 있는 것입니다. 그렇지 않은 경우 **등록** 을 클릭하여 등록합니다.

또한 [Azure 리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md)에 설명된 대로 사용 가능한 방법 중 하나를 사용하여 리소스 공급자를 등록할 수도 있습니다. 다음은 PowerShell을 사용하는 샘플 명령입니다.

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스 허용
선택한 네트워크에서 액세스할 수 있도록 스토리지 계정을 구성한 경우 예외를 추가하여 Azure Monitor가 계정에 쓸 수 있도록 해야 합니다. 스토리지 계정에 대한 **방화벽 및 가상 네트워크** 에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 을 선택합니다.

[![스토리지 계정 방화벽 및 가상 네트워크](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>데이터 내보내기 규칙 만들기 또는 업데이트
데이터 내보내기 규칙은 데이터를 내보내고 대상을 지정하는 테이블을 정의합니다. 작업 영역에서 10개 사용 규칙을 사용할 수 있으며, 추가 규칙을 추가할 수 있지만 '사용 안 함' 상태입니다. 대상은 작업 영역의 모든 내보내기 규칙에서 고유해야 합니다.

데이터 내보내기 대상에는 제한이 있으며 내보내기 제한, 오류 및 대기 시간을 최소화하기 위해 모니터링해야 합니다. [스토리지 계정 확장성](../../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts) 및 [이벤트 허브 네임스페이스 할당량을](../../event-hubs/event-hubs-quotas.md)참조하세요.

#### <a name="recommendations-for-storage-account"></a>스토리지 계정에 대한 권장 사항 

1. 내보내기를 위해 별도의 스토리지 계정 사용
1. 다음 설정을 사용하여 아래 메트릭에 대한 경고를 구성합니다. 

    | 범위 | 메트릭 네임스페이스 | 메트릭 | 집계 | 임계값 |
    |:---|:---|:---|:---|:---|
    | storage-name | 계정 | 수신 | 합계 | 최대 스토리지 수신 속도의 80%입니다. 예를 들어 미국 서부의 범용 v2의 경우 60Gbps입니다. |
  
1. 경고 수정 작업
    - 내보내기를 위해 별도의 스토리지 계정 사용
    - Azure Storage 표준 계정은 요청에 따라 더 높은 수신 제한을 지원합니다. 증가를 요청하려면 [Azure 지원](https://azure.microsoft.com/support/faq/) 문의합니다.
    - 추가 스토리지 계정 간에 테이블 분할

#### <a name="recommendations-for-event-hub"></a>이벤트 허브에 대한 권장 사항

1. [메트릭 경고](../../event-hubs/monitor-event-hubs-reference.md)구성:
  
    | 범위 | 메트릭 네임스페이스 | 메트릭 | 집계 | 임계값 |
    |:---|:---|:---|:---|:---|
    | namespaces-name | 이벤트 허브 표준 메트릭 | 들어오는 바이트 | 합계 | 5분당 최대 수신의 80%입니다. 예를 들어 단위당 1MB/s(TU 또는 PU)입니다. |
    | namespaces-name | 이벤트 허브 표준 메트릭 | 들어오는 요청 | 개수 | 5분당 최대 이벤트의 80%입니다. 예를 들어 단위당 1000/s(TU 또는 PU)입니다. |
    | namespaces-name | 이벤트 허브 표준 메트릭 | 할당량 초과 오류 | 개수 | 요청의 1%~5% 사이 |

1. 경고 수정 작업
   - [자동 확장](../../event-hubs/event-hubs-auto-inflate.md) 기능을 구성하여 사용량 요구 사항에 맞게 처리량 단위 수를 자동으로 확장하고 늘림
   - 부하를 수용하기 위해 처리량 단위 증가 확인
   - 추가 네임스페이스 간에 테이블 분할
   - 더 높은 처리량을 위해 'Premium' 또는 '전용' 계층 사용

내보내기 규칙은 작업 영역에 있는 테이블을 포함해야 합니다. 작업 영역에서 사용 가능한 테이블 목록에 대해 이 쿼리를 실행합니다.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal **Log Analytics 작업 영역** 메뉴의 **설정** 섹션에서 데이터 **내보내기를** 선택하고 가운데 창 위쪽에서 **새 내보내기 규칙을** 클릭합니다.

![create 내보내기](media/logs-data-export/export-create-1.png)

단계를 따라 **만들기를** 클릭합니다. 

<img src="media/logs-data-export/export-create-2.png" alt="export rule configuration" title="규칙 내보내기 구성" width="80%"/>


# <a name="powershell"></a>[PowerShell](#tab/powershell)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용하여 스토리지 계정으로 데이터 내보내기 규칙을 만들려면 다음 명령을 사용합니다.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

CLI를 사용하여 이벤트 허브로 데이터 내보내기 규칙을 만들려면 다음 명령을 사용합니다. 각 테이블에 대해 별도의 이벤트 허브가 생성됩니다.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

CLI를 사용하여 특정 이벤트 허브로 데이터 내보내기 규칙을 만들려면 다음 명령을 사용합니다. 제공된 이벤트 허브 이름으로 모든 테이블을 내보냅니다. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventhubs/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

REST API를 사용하여 데이터 내보내기 규칙을 만들려면 다음 요청을 사용합니다. 요청은 전달자 토큰 권한 부여 및 콘텐츠 형식 application/json을 사용해야 합니다.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

요청 본문은 테이블 대상을 지정합니다. 다음은 스토리지 계정에 대한 REST 요청의 샘플 본문입니다.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

다음은 이벤트 허브에 대한 REST 요청의 샘플 본문입니다.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

다음은 이벤트 허브 이름이 제공되는 이벤트 허브에 대한 REST 요청의 샘플 본문입니다. 이 경우 내보낸 모든 데이터가 이 이벤트 허브로 전송됩니다.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[템플릿](#tab/json)

템플릿을 사용하여 스토리지 계정으로 데이터 내보내기 규칙을 만들려면 다음 명령을 사용합니다.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

템플릿을 사용하여 이벤트 허브로 데이터 내보내기 규칙을 만들려면 다음 명령을 사용합니다. 각 테이블에 대해 별도의 이벤트 허브가 생성됩니다.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

템플릿을 사용하여 특정 이벤트 허브로 데이터 내보내기 규칙을 만들려면 다음 명령을 사용합니다. 제공된 이벤트 허브 이름으로 모든 테이블을 내보냅니다.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>데이터 내보내기 규칙 구성 보기

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal **Log Analytics 작업 영역** 메뉴의 **설정** 섹션에서 **데이터 내보내기** 를 선택 합니다.

![규칙 내보내기 보기](media/logs-data-export/export-view-1.png)

구성 보기에 대 한 규칙을 클릭 합니다.

<img src="media/logs-data-export/export-view-2.png" alt="export rule settings" title= "규칙 설정 내보내기" width="65%"/>


# <a name="powershell"></a>[PowerShell](#tab/powershell)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용하여 데이터 내보내기 규칙의 구성을 보려면 다음 명령을 사용합니다.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

REST API를 사용하여 데이터 내보내기 규칙의 구성을 보려면 다음 요청을 사용합니다. 요청은 전달자 토큰 권한 부여를 사용해야 합니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[템플릿](#tab/json)

해당 없음

---

## <a name="disable-an-export-rule"></a>내보내기 규칙 사용 안 함

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

테스트가 수행되는 경우와 같이 특정 기간 동안 데이터를 유지할 필요가 없는 경우 내보내기를 중지할 수 있도록 내보내기 규칙을 비활성화할 수 있습니다. Azure Portal **Log Analytics 작업 영역** 메뉴의 **설정** 섹션에서 **데이터 내보내기** 를 선택 하 고 상태 토글을 클릭 하 여 규칙 내보내기를 사용 하지 않거나 사용 하도록 설정 합니다.

![규칙 사용 안 함 내보내기](media/logs-data-export/export-disable.png)


# <a name="powershell"></a>[PowerShell](#tab/powershell)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

테스트가 수행되는 경우와 같이 특정 기간 동안 데이터를 유지할 필요가 없는 경우 내보내기를 중지할 수 있도록 내보내기 규칙을 비활성화할 수 있습니다. CLI를 사용하여 데이터 내보내기 규칙을 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --enable false
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

테스트가 수행되는 경우와 같이 특정 기간 동안 데이터를 유지할 필요가 없는 경우 내보내기를 중지할 수 있도록 내보내기 규칙을 비활성화할 수 있습니다. REST API를 사용하여 데이터 내보내기 규칙을 사용하지 않도록 설정하려면 다음 요청을 사용합니다. 요청은 전달자 토큰 권한 부여를 사용해야 합니다.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[템플릿](#tab/json)

테스트가 수행되는 경우와 같이 특정 기간 동안 데이터를 유지할 필요가 없는 경우 내보내기를 중지할 수 있도록 내보내기 규칙을 비활성화할 수 있습니다. 데이터 내보내기를 사용하지 않도록 설정하려면 템플릿에서 ```"enable": false```를 설정합니다.

---

## <a name="delete-an-export-rule"></a>내보내기 규칙 삭제

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal의 **Log Analytics 작업 영역** 메뉴에서 **설정** 섹션에서 *데이터 내보내기* 를 선택한 다음 규칙의 오른쪽에 있는 줄임표를 클릭 하 고 **삭제** 를 클릭 합니다. 

![규칙 삭제 내보내기](media/logs-data-export/export-delete.png)


# <a name="powershell"></a>[PowerShell](#tab/powershell)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용하여 데이터 내보내기 규칙을 삭제하려면 다음 명령을 사용합니다.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

REST API를 사용하여 데이터 내보내기 규칙을 삭제하려면 다음 요청을 사용합니다. 요청은 전달자 토큰 권한 부여를 사용해야 합니다.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[템플릿](#tab/json)

해당 없음

---


## <a name="view-all-data-export-rules-in-a-workspace"></a>작업 영역에서 모든 데이터 내보내기 규칙 보기

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal **Log Analytics 작업 영역** 메뉴의 **설정** 섹션에서 **데이터 내보내기** 를 선택 하 여 작업 영역에 있는 모든 내보내기 규칙을 봅니다.

![규칙 내보내기](media/logs-data-export/export-view.png)


# <a name="powershell"></a>[PowerShell](#tab/powershell)

해당 없음

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용하여 작업 영역에서 모든 데이터 내보내기 규칙을 보려면 다음 명령을 사용합니다.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST (영문)](#tab/rest)

REST API를 사용하여 작업 영역에서 모든 데이터 내보내기 규칙을 보려면 다음 요청을 사용합니다. 요청은 전달자 토큰 권한 부여를 사용해야 합니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[템플릿](#tab/json)

해당 없음

---


## <a name="unsupported-tables"></a>지원되지 않는 테이블
데이터 내보내기 규칙에 지원되지 않는 테이블이 포함되어 있으면 구성이 성공하지만 해당 테이블에 대한 데이터를 내보내지 않습니다. 테이블이 나중에 지원되면 해당 시점에서 데이터를 내보냅니다.

데이터 내보내기 규칙에 존재하지 않는 테이블이 포함되어 있으면 "테이블 \<tableName\>이(가) 작업 영역에 없습니다"라는 오류와 함께 실패합니다.


## <a name="supported-tables"></a>지원되는 테이블
지원되는 테이블은 현재 아래에 지정된 테이블로 제한됩니다. 제한이 지정되지 않은 경우 테이블의 모든 데이터를 내보냅니다. 이 목록은 추가 테이블에 대한 지원이 추가되면 업데이트됩니다.

| 테이블 | 제한 사항 |
|:---|:---|
| AACAudit |  |
| AACHttpRequest |  |
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADRiskyUsers |  |
| AADServicePrincipalSignInLogs |  |
| AADUserRiskEvents |  |
| ABSBotRequests |  |
| ACSAuthIncomingOperations |  |
| ACSBillingUsage |  |
| ACRConnectedClientList |  |
| ACRConnectedClientList |  |
| ACSCallDiagnostics |  |
| ACSCallSummary |  |
| ACSChatIncomingOperations |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFSSignInLogs |  |
| ADFTriggerRun |  |
| ADPAudit |  |
| ADPDiagnostics |  |
| ADPRequests |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| AEWAuditLogs |  |
| AgriFoodApplicationAuditLogs |  |
| AgriFoodApplicationAuditLogs |  |
| AgriFoodFarmManagementLogs |  |
| AgriFoodFarmManagementLogs |  |
| AgriFoodFarmOperationLogs |  |
| AgriFoodInsightLogs |  |
| AgriFoodJobProcessedLogs |  |
| AgriFoodModelInferenceLogs |  |
| AgriFoodProviderAuthLogs |  |
| AgriFoodSatelliteLogs |  |
| AgriFoodWeatherLogs |  |
| 경고 |  |
| AlertEvidence |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| ATCExpressRouteCircuitIpfix |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AWSGuardDuty |  |
| AWSVPCFlow |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CDBCassandraRequests |  |
| CDBControlPlaneRequests |  |
| CDBDataPlaneRequests |  |
| CDBGremlinRequests |  |
| CDBMongoRequests |  |
| CDBPartitionKeyRUConsumption |  |
| CDBPartitionKeyStatistics |  |
| CDBQueryRuntimeStatistics |  |
| CloudAppEvents |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | 부분 지원 – 일부 데이터는 내보내기에서 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerLogV2 |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DeviceNetworkInfo |  |
| DnsEvents |  |
| DnsInventory |  |
| DummyHydrationFact |  |
| Dynamics365Activity |  |
| EmailAttachmentInfo |  |
| EmailEvents |  |
| EmailPostDeliveryEvents |  |
| EmailUrlInfo |  |
| 이벤트 | 부분 지원 – Log Analytics 에이전트(MMA) 또는 Azure Monitor 에이전트(AMA)에서 수신된 데이터는 내보내기에 완벽하게 지원됩니다. 진단 확장 에이전트를 통해 수신된 데이터는 스토리지를 통해 수집되지만 내보내기에 지원되지는 않습니다.2 |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| HDInsightAmbariClusterAlerts |  |
| HDInsightAmbariSystemMetrics |  |
| HDInsightHadoopAndYarnLogs |  |
| HDInsightHadoopAndYarnMetrics |  |
| HDInsightHBaseLogs |  |
| HDInsightHBaseMetrics |  |
| HDInsightHiveAndLLAPLogs |  |
| HDInsightHiveAndLLAPMetrics |  |
| HDInsightHiveTezAppStats |  |
| HDInsightJupyterNotebookEvents |  |
| HDInsightKafkaMetrics |  |
| HDInsightOozieLogs |  |
| HDInsightRangerAuditLogs |  |
| HDInsightSecurityLogs |  |
| HDInsightSparkApplicationEvents |  |
| HDInsightSparkBlockManagerEvents |  |
| HDInsightSparkEnvironmentEvents |  |
| HDInsightSparkExecutorEvents |  |
| HDInsightSparkJobEvents |  |
| HDInsightSparkLogs |  |
| HDInsightSparkSQLExecutionEvents |  |
| HDInsightSparkStageEvents |  |
| HDInsightSparkStageTaskAccumulables |  |
| HDInsightSparkTaskEvents |  |
| 하트비트 |  |
| HuntingBookmark |  |
| InsightsMetrics | 부분 지원 – 일부 데이터는 내보내기에서 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MCCEventLogs |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftDataShareShareLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | 정부 클라우드의 부분 지원 – 일부 데이터가 O365에서 LA로 웹후크를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| 작업 | 부분 지원 – 일부 데이터는 내보내기에서 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| Perf | 부분 지원 – 현재 Windows 성능 데이터만 지원됩니다. 현재 Linux 성능 데이터는 내보내기에서 누락됩니다. |
| PowerBIDatasetsWorkspace |  |
| HDInsightRangerAuditLogs |  |
| PurviewScanStatusLogs |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | 부분 지원 – Log Analytics 에이전트(MMA) 또는 Azure Monitor 에이전트(AMA)에서 수신된 데이터는 내보내기에 완벽하게 지원됩니다. 진단 확장 에이전트를 통해 수신된 데이터는 스토리지를 통해 수집되지만 내보내기에 지원되지는 않습니다. |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SentinelHealth |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SQLSecurityAuditEvents |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| syslog | 부분 지원 – Log Analytics 에이전트(MMA) 또는 Azure Monitor 에이전트(AMA)에서 수신된 데이터는 내보내기에 완벽하게 지원됩니다. 진단 확장 에이전트를 통해 수신된 데이터는 스토리지를 통해 수집되지만 내보내기에 지원되지는 않습니다. |
| ThreatIntelligenceIndicator |  |
| 업데이트 | 부분 지원 – 일부 데이터는 내보내기에서 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| 사용량 |  |
| UserAccessAnalytics |  |
| UserPeerAnalytics |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | 부분 지원 – 일부 데이터는 내보내기에서 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| WorkloadDiagnosticLogs |  |
| WVDAgentHealthStatus |  |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |

## <a name="next-steps"></a>다음 단계

- [Azure Data Explorer에서 내보낸 데이터를 쿼리합니다](../logs/azure-data-explorer-query-storage.md).
