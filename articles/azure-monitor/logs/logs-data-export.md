---
title: Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기(미리 보기)
description: Log Analytics 데이터 내보내기를 사용하면 데이터를 수집하는 동안 선택한 테이블의 데이터를 Log Analytics 작업 영역에서 Azure Storage 계정 또는 Azure Event Hubs로 계속 내보낼 수 있습니다.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
author: bwren
ms.author: bwren
ms.date: 05/07/2021
ms.openlocfilehash: 04662b734f86905f0064bad43ecbecd84bc48042
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401392"
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

- 현재 CLI 또는 REST 요청을 사용 하 여 구성을 수행할 수 있습니다. Azure Portal 또는 PowerShell은 아직 지원되지 않습니다.
- CLI 및 REST의 `--export-all-tables` 옵션은 지원되지 않으며 제거될 예정입니다. 내보내기 규칙에서 테이블 목록을 명시적으로 제공해야 합니다.
- 지원 되는 테이블은 아래의 [지원 되는 테이블](#supported-tables) 섹션에 지정 된 테이블에서 제한 됩니다. 
- 기존 사용자 지정 로그 테이블은 내보내기에서 지원 되지 않습니다. 3 월 2022에 제공 되는 새로운 사용자 지정 로그 버전이 지원 됩니다.
- 데이터 내보내기 규칙에 지원 되지 않는 테이블이 포함 되어 있으면 작업이 성공 하지만 테이블이 지원 될 때까지 해당 테이블에 대 한 데이터는 내보내지 않습니다. 
- 데이터 내보내기 규칙에 존재하지 않는 테이블이 포함되어 있으면 `Table <tableName> does not exist in the workspace` 오류와 함께 실패합니다.
- 작업 영역에 활성화된 규칙을 최대 10개까지 정의할 수 있습니다. 사용 하지 않도록 설정 하면 추가 규칙이 허용 됩니다. 
- 대상은 작업 영역에 있는 모든 내보내기 규칙에서 고유해야 합니다.
- 대상은 Log Analytics 작업 영역과 동일한 지역에 있어야 합니다.
- 저장소 계정으로 내보낼 때 테이블 이름은 60 자이 하 여야 하 고, 47 문자는 이벤트 허브로 변환할 수 없습니다. 이름이 긴 테이블은 내보내지 않습니다.
- 데이터 내보내기는 모든 지역에서 사용할 수 있지만 현재에서 지원 됩니다. 
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

작업 영역에서 내보내기 규칙을 만들기 전에 데이터 내보내기 대상을 만들어야 합니다. 대상이 작업 영역과 동일한 구독에 있을 필요는 없습니다. Azure Lighthouse를 사용 하는 경우 다른 Azure Active Directory 테 넌 트의 대상으로 데이터가 전송 될 수도 있습니다.

### <a name="storage-account"></a>스토리지 계정

데이터 내보내기 규칙을 구성 하려면 작업 영역 및 대상 모두에 대 한 ' 쓰기 ' 권한이 있어야 합니다. 데이터에 대 한 액세스를 더 효율적으로 제어 하 고 저장소 수집 빈도 제한 및 제한에 도달 하지 않도록 방지 하기 위해 저장 된 다른 데이터를 모니터링 하지 않는 기존 저장소 계정을 사용 하지 않아야 합니다. 

데이터를 변경할 수 없는 저장소로 보내려면 [Blob 저장소에 대 한 불변성 정책 설정 및 관리](../../storage/blobs/immutable-policy-configure-version-scope.md)에 설명 된 대로 저장소 계정에 대 한 변경할 수 없는 정책을 설정 합니다. 보호된 추가 BLOB 쓰기 사용을 비롯하여 이 문서의 모든 단계를 따라야 합니다.

저장소 계정은 작업 영역과 동일한 지역에 StorageV1 여야 합니다. 다른 지역에 있는 다른 저장소 계정에 데이터를 복제 해야 하는 경우 GRS 및 GZRS를 비롯 한 [Azure Storage 중복성 옵션](../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 중 하나를 사용할 수 있습니다.

데이터는 Azure Monitor에 도달하면 스토리지 계정으로 전송되고 매시간 추가 Blob에 저장됩니다. 내보내기 규칙 설정은 저장소 계정의 각 테이블에 대 한 컨테이너를 *am* 이름으로 만든 다음 테이블 이름을 사용 하 여 만듭니다. 예를 들어 *SecurityEvent* 테이블은 *am-SecurityEvent* 라는 이름의 컨테이너로 전송됩니다.

2021 년 10 월 15 일부 터 blob은 5 분 안에 *WorkspaceResourceId =/subscriptions/subscription-id/resourcegroups/ \<resource-group\> /providers/microsoft.operationalinsights/workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /H = \<two-digit 24-hour clock hour\> /M = \<two-digit 60-minute clock minute\> /pt05m.json* 에 저장 됩니다. 추가 Blob은 스토리지에서 50K 쓰기로 제한되므로 추가 수가 많으면 내보낸 Blob 수가 확대될 수 있습니다. 이러한 경우 blob에 대 한 명명 패턴은 PT05M_ #. json *입니다. 여기서 #은 증분 blob 수입니다.

저장소 계정 데이터 형식은 [JSON 줄](../essentials/resource-logs-blob-format.md)에 있습니다. 즉, 각 레코드는 바깥쪽 레코드는 없고 JSON 레코드 사이에는 쉼표가 없는 줄 바꿈으로 구분 됩니다. 

[![스토리지 샘플 데이터](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

### <a name="event-hub"></a>이벤트 허브

데이터 내보내기 규칙을 구성 하려면 작업 영역 및 대상 모두에 대 한 ' 쓰기 ' 권한이 있어야 합니다. 이벤트 허브 네임 스페이스에 대 한 공유 액세스 정책은 스트리밍 메커니즘이 포함 하는 사용 권한을 정의 합니다. 이벤트 허브로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요 합니다. 내보내기 규칙을 업데이트 하려면 해당 Event Hubs 권한 부여 규칙에 대 한 ListKey 권한이 있어야 합니다.

이벤트 허브 네임 스페이스는 작업 영역과 동일한 지역에 있어야 합니다.

Azure Monitor에 도달 하면 이벤트 허브로 데이터가 전송 됩니다. 이벤트 허브는 내보내는 각 데이터 형식에 대해 생성되며 이름은 *am-* 뒤에 테이블 이름이 지정됩니다. 예를 들어 *SecurityEvent* 테이블은 *am-SecurityEvent* 라는 이름의 이벤트 허브로 전송됩니다. 내보낸 데이터를 특정 이벤트 허브에 연결하려는 경우 또는 이름이 47자 제한을 초과하는 테이블이 있는 경우, 고유한 이벤트 허브 이름을 제공하고 정의된 테이블의 모든 데이터를 내보낼 수 있습니다.

> [!IMPORTANT]
> ['기본' 및 '표준' 네임스페이스 계층당 지원되는 이벤트 허브 수는 10개입니다](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). 10개가 넘는 테이블을 내보내는 경우, 여러 이벤트 허브 네임스페이스에 대한 여러 내보내기 규칙으로 테이블을 분할하거나, 내보내기 규칙에 이벤트 허브 이름을 지정하고 모든 테이블을 해당 이벤트 허브로 내보냅니다.

이벤트 허브 네임스페이스에 대한 고려 사항:
1. '기본' 이벤트 허브 SKU는 더 낮은 이벤트 크기 [제한을](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-vs-premium-vs-dedicated-tiers) 지원하며 작업 영역의 일부 로그가 이를 초과하여 삭제될 수 있습니다. '표준' 또는 '전용' 이벤트 허브를 내보내기 대상으로 사용하는 것이 좋습니다.
2. 내보낸 데이터의 볼륨은 시간이 지남에 따라 증가하는 경우가 많으며, 높은 전송 속도를 처리하고 제한 시나리오 및 데이터 대기 시간을 방지하려면 이벤트 허브 규모를 늘려야 합니다. Event Hubs의 자동 확장 기능을 사용하여 처리량 단위 수를 자동으로 스케일 업하여 늘려서 사용량 요구 사항을 충족해야 합니다. 자세한 내용은 [Azure Event Hubs 처리량 단위 자동 확장](../../event-hubs/event-hubs-auto-inflate.md)을 참조하세요.

> [!NOTE]
> Azure Monitor 데이터 내보내기에서는 가상 네트워크를 사용할 때 이벤트 허브 리소스에 액세스할 수 없습니다. Azure Monitor 데이터 내보내기에서 Event Hubs 리소스에 대한 액세스 권한이 부여되도록 신뢰할 수 있는 Microsoft 서비스 허용이 Event Hub에서 이 방화벽을 무시하도록 허용 설정을 사용하도록 설정해야 합니다. 

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
데이터 내보내기 규칙은 데이터를 내보낼 테이블과 대상을 정의합니다. 작업 영역에 활성화된 규칙이 10개 있을 수 있으며, 이때 10개를 초과하는 추가 규칙은 비활성 상태가 되어야 합니다. 대상은 작업 영역에 있는 모든 내보내기 규칙에서 고유해야 합니다.

> [!NOTE]
> 데이터 내보내기는 [스토리지 계정 확장성](../../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts), [이벤트 허브 네임스페이스 할당량](../../event-hubs/event-hubs-quotas.md) 등의 몇 가지 제한 사항이 있지만 사용자가 소유한 대상에 로그를 보냅니다. 제한에 거의 도달한 경우에는 제한할 대상을 모니터링하고 측정값을 적용하는 것이 좋습니다. 예를 들면 다음과 같습니다. 
> - 이벤트 허브의 자동 확장 기능을 자동으로 스케일 업되도록 설정하고 TU(처리량 단위) 수를 늘립니다. 자동 확장이 최대치일 경우 추가 TU를 요청할 수 있습니다.
> - 서로 다른 대상에 대한 여러 내보내기 규칙으로 테이블 분할

내보내기 규칙은 작업 영역에 있는 테이블을 포함해야 합니다. 작업 영역에서 사용 가능한 테이블 목록에 대해 이 쿼리를 실행합니다.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

해당 없음

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

해당 없음

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

해당 없음

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

해당 없음

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

해당 없음

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
| 경고 |  |
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
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | 부분 지원 – 일부 데이터가 내보내기에 대해 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
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
| HDInsightKafkaLogs |  |
| HDInsightKafkaMetrics |  |
| HDInsightOozieLogs |  |
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
| InsightsMetrics | 부분 지원 – 일부 데이터가 내보내기에 대해 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
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
| 작업 | 부분 지원 – 일부 데이터가 내보내기에 대해 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| Perf | 부분 지원 – 현재 Windows 성능 데이터만 지원 됩니다. 현재 Linux 성능 데이터는 내보내기에서 누락됩니다. |
| PowerBIDatasetsWorkspace |  |
| PurviewScanStatusLogs |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityCef |  |
| SecurityDetection |  |
| SecurityEvent | 부분 지원 – Log Analytics 에이전트(MMA) 또는 Azure Monitor 에이전트(AMA)에서 수신된 데이터는 내보내기에 완벽하게 지원됩니다. 진단 확장 에이전트를 통해 수신된 데이터는 스토리지를 통해 수집되지만 이 경로는 내보내기 2에서 지원되지 않습니다. |
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
| syslog | 부분 지원 – Log Analytics 에이전트(MMA) 또는 Azure Monitor 에이전트(AMA)에서 수신된 데이터는 내보내기에 완벽하게 지원됩니다. 진단 확장 에이전트를 통해 수신된 데이터는 스토리지를 통해 수집되지만 이 경로는 내보내기 2에서 지원되지 않습니다. |
| ThreatIntelligenceIndicator |  |
| 업데이트 | 부분 지원 – 일부 데이터가 내보내기에 대해 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| 사용량 |  |
| UserAccessAnalytics |  |
| UserPeerAnalytics |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | 부분 지원 – 일부 데이터가 내보내기에 대해 지원되지 않는 내부 서비스를 통해 수집됩니다. 현재 이 부분은 내보내기에서 누락됩니다. |
| WorkloadDiagnosticLogs |  |
| WVDAgentHealthStatus |  |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |

## <a name="next-steps"></a>다음 단계

- [Azure Data Explorer에서 내보낸 데이터를 쿼리합니다](../logs/azure-data-explorer-query-storage.md).
