---
title: Azure Monitor 플랫폼 메트릭과 로그를 다른 대상으로 보내는 진단 설정 만들기
description: 진단 설정을 사용하여 Azure Monitor 로그, Azure Storage 또는 Azure Event Hubs에 Azure Monitor 플랫폼 메트릭 및 로그를 보냅니다.
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 6de7ed1acffd7f4c05d912f7f565f4b811ebd357
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402044"
---
# <a name="create-diagnostic-settings-to-send-azure-monitor-platform-logs-and-metrics-to-different-destinations"></a>다른 대상에 Azure Monitor 플랫폼 로그 및 메트릭을 전송 하는 진단 설정 만들기

이 문서에서는 Azure 플랫폼 메트릭과 로그를 다른 대상으로 보내기 위한 진단 설정을 만들고 구성 하는 방법에 대해 자세히 설명 합니다.

[플랫폼 메트릭은](./metrics-supported.md) 구성 없이 기본적으로 [Azure Monitor 메트릭으로](./data-platform-metrics.md) 자동으로 전송 됩니다.

Azure 활동 로그 및 리소스 로그를 포함 한 [플랫폼 로그](./platform-logs-overview.md)는 azure 리소스 및 해당 리소스가 종속 된 azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다. 활동 로그는 자체에 존재 하지만 다른 위치로 라우팅할 수 있습니다.  리소스 로그는 대상으로 라우팅될 때까지 수집 되지 않습니다.

각 Azure 리소스에는 다음 조건을 정의하는 자체 진단 설정이 필요합니다.

- 원본-설정에 정의 된 대상에 보낼 메트릭 및 로그 데이터의 유형입니다. 사용 가능한 형식은 리소스 유형에 따라 달라 집니다.
- 대상-보낼 대상 하나 이상입니다.

단일 진단 설정으로 각 대상 중 하나만 정의할 수 있습니다. 데이터를 2개 이상의 특정 대상 유형(예: 두 개의 다른 Log Analytics 작업 영역) 으로 보내려면 여러 개의 설정을 만듭니다. 각 리소스는 진단 설정을 5개까지 포함할 수 있습니다.

다음 비디오에서는 진단 설정으로 플랫폼 로그를 라우팅하는 방법을 보여줍니다.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

## <a name="sources"></a>원본

원본 옵션은 다음과 같습니다.

### <a name="metrics"></a>메트릭

**Allmetrics** 설정은 리소스의 플랫폼 메트릭을 추가 대상으로 라우팅합니다. 이 옵션은 모든 리소스 공급자에 게 표시 되지 않을 수 있습니다.  

### <a name="logs"></a>로그

로그를 사용 하 여 개별적으로 라우팅할 로그 범주를 선택 하거나 범주 그룹을 선택할 수 있습니다.

> [!NOTE]
> 범주 그룹은 메트릭에 적용 되지 않습니다. 모든 리소스에서 범주 그룹을 사용할 수 있는 것은 아닙니다.

**범주 그룹** 을 사용 하면 개별 로그 범주를 선택 하는 대신 미리 정의 된 그룹을 기반으로 리소스 로그를 동적으로 수집할 수 있습니다. Microsoft는 모든 Azure 서비스에서 특정 사용 사례를 모니터링 하는 데 도움이 되는 그룹화를 정의 합니다. 시간이 지남에 따라 새 로그가 롤아웃 되거나 평가가 변경 될 때 그룹의 범주를 업데이트할 수 있습니다. 범주 그룹에서 로그 범주를 추가 하거나 제거 하면 진단 설정을 업데이트 하지 않고 로그 컬렉션이 자동으로 수정 됩니다.

범주 그룹을 사용 하는 경우 다음을 수행 합니다. 
- 개별 범주 유형에 따라 더 이상 리소스 로그를 개별적으로 선택할 수 없습니다.
- 더 이상 Azure Storage로 전송 되는 로그에 보존 설정을 적용할 수 없습니다.

현재는 두 가지 범주 그룹이 있습니다.
- **모든** -리소스에서 제공 하는 모든 리소스 로그입니다.
- **감사** -고객의 데이터와 서비스의 설정에 대 한 고객 상호 작용을 기록 하는 모든 리소스 로그입니다.

## <a name="destinations"></a>대상
다음 표의 대상에 플랫폼 로그 및 메트릭을 보낼 수 있습니다. 

| 대상 | Description |
|:---|:---|
| [Log Analytics 작업 영역](../logs/design-logs-deployment.md) | 메트릭은 로그 형식으로 변환 됩니다. 일부 리소스 종류에는 이 옵션을 사용하지 못할 수 있습니다. Azure Monitor 로그 저장소 (Log Analytics를 통해 검색 가능)로 보내면 기존 로그 데이터를 사용 하 여 쿼리, 경고 및 시각화에 통합 하는 데 도움이 됩니다.  
| [Azure Storage 계정](../../storage/blobs/index.yml) | Azure Storage 계정에 로그 및 메트릭을 보관하면 감사, 정적 분석, 백업에 유용합니다. Azure Storage는 Azure Monitor 로그 및 Log Analytics 작업 영역보다 비용이 적고 로그를 무기한으로 보관할 수 있습니다.  | 
| [Event Hubs](../../event-hubs/index.yml) | 로그 및 메트릭을 Event Hubs로 보내면 타사 SIEMs 및 기타 Log Analytics 솔루션과 같은 외부 시스템으로 데이터를 스트리밍할 수 있습니다.  |
| [Azure Monitor 파트너 통합](/azure/partner-solutions/overview/)| Azure Monitor 및 기타 타사 모니터링 플랫폼 간의 특수 통합. 파트너 중 하나를 이미 사용 하 고 있는 경우에 유용 합니다.  |

## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항:

### <a name="metrics-as-a-source"></a>원본으로 메트릭
메트릭 내보내기에는 몇 가지 제한 사항이 있습니다.

- **진단 설정을 통해 다차원 메트릭을 전송 하는 것은 현재 지원 되지** 않습니다. 차원이 포함 된 메트릭은 차원 값에 걸쳐 집계 된 평면화 된 단일 차원 메트릭으로 내보내집니다. 예: Blockchain의 'IOReadBytes' 메트릭은 노드 수준에서 탐색 및 차트 표시가 가능합니다. 그러나 진단 설정을 통해 내보낸 메트릭은 모든 노드에 대 한 모든 읽기 바이트를 표시 합니다.
- 모든 메트릭을 Azure Monitor 로그/Log Analytics으로 내보낼 수 있는 것은 아니므로 **모든 메트릭을 진단 설정으로 내보낼 수 있는 것은 아닙니다** . 자세한 내용은 [지원 되는 메트릭 목록](./metrics-supported.md) 에서 내보낼 수 있는 열을 참조 하세요.

특정 메트릭에 대 한 이러한 제한 사항을 해결 하기 위해 [REST API 메트릭을](/rest/api/monitor/metrics/list) 사용 하 여 수동으로 추출 하 고 [AZURE MONITOR 데이터 수집기 API](../logs/data-collector-api.md)를 사용 하 여 Azure Monitor 로그로 가져올 수 있습니다.

### <a name="activity-log-as-a-source"></a>활동 로그를 원본으로

> [!IMPORTANT]
> 활동 로그에 대한 진단 설정을 만들기 전에 모든 레거시 구성을 사용하지 않도록 설정해야 합니다. 자세한 내용은 [레거시 컬렉션 메서드](../essentials/activity-log.md#legacy-collection-methods)를 참조하세요.


### <a name="destination-limitations"></a>대상 제한 사항

진단 설정을 만들기 전에 진단 설정의 대상을 만들어야 합니다. 설정을 구성하는 사용자가 두 구독에 대한 적절한 Azure RBAC 액세스를 가진 경우 대상은 로그를 전송하는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다. Azure Lighthouse를 사용하여 진달 설정을 다른 Azure Active Directory 테넌트의 작업 영역으로 보낼 수도 있습니다. 다음 표에는 지역 제한을 포함하여 각 대상에 대한 고유한 요구 사항이 나타나 있습니다.

| 대상 | 요구 사항 |
|:---|:---|
| Log Analytics 작업 영역 | 작업 영역이 모니터링되는 리소스와 동일한 지역에 있을 필요는 없습니다.|
| Azure Storage 계정 | 데이터에 대 한 액세스를 더 효율적으로 제어할 수 있도록 저장 된 다른 데이터를 모니터링 하지 않는 기존 저장소 계정을 사용 하지 마세요. 활동 로그와 리소스 로그를 함께 보관하는 경우, 동일한 스토리지 계정을 사용하여 모든 모니터링 데이터를 중앙 위치에서 보관할 수 있습니다.<br><br>변경이 불가능한 스토리지로 데이터를 보내려면 [Blob Storage에 대한 불변성 정책 설정 및 관리](../../storage/blobs/immutable-policy-configure-version-scope.md)에서 설명하는 것과 같이 스토리지 계정에 대해 불변성 정책을 설정합니다. 보호 된 추가 blob 쓰기를 사용 하는 것을 포함 하 여이 연결 된 문서의 모든 단계를 수행 해야 합니다.<br><br>지역별 리소스인 경우 스토리지 계정이 모니터링되는 리소스와 동일한 지역에 있어야 합니다.|
| Event Hubs | 네임스페이스에 대한 공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. Event Hubs로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요합니다. 스트리밍을 포함하도록 진단 설정을 업데이트하려면 해당 Event Hubs 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다.<br><br>지역별 리소스인 경우 이벤트 허브 네임스페이스가 모니터링되는 리소스와 동일한 지역에 있어야 합니다. <br><br> 가상 네트워크를 사용 하는 경우 진단 설정에서 Event Hubs 리소스에 액세스할 수 없습니다. Azure Monitor (진단 설정) 서비스에 Event Hubs 리소스에 대 한 액세스 권한이 부여 되도록 하려면 *신뢰할 수 있는 Microsoft 서비스* 에서이 방화벽 설정을 무시 하도록 허용 해야 합니다.|
| 파트너 통합 | 파트너에 따라 다릅니다.  자세한 내용은 [Azure Monitor 파트너 통합 설명서](/azure/partner-solutions/overview/) 를 확인 하세요.  

### <a name="azure-data-lake-storage-gen2-as-a-destination"></a>대상으로 Azure Data Lake Storage Gen2

> [!NOTE]
> Azure Data Lake Storage Gen2 계정은 현재 Azure Portal에서 유효한 옵션으로 나열될 수 있지만 진단 설정의 대상으로 지원되지 않습니다.

## <a name="create-in-azure-portal"></a>Azure Portal에서 만들기

Azure Monitor 메뉴 또는 리소스의 메뉴에서 Azure Portal 진단 설정을 구성할 수 있습니다.

1. Azure Portal에서 진단 설정을 구성하는 위치는 리소스에 따라 다릅니다.

   - 단일 리소스의 경우 리소스 메뉴의 **모니터** 에서 **진단 설정** 을 클릭합니다.

        ![진단 설정이 강조 표시된 Azure Portal 리소스 메뉴의 모니터링 섹션 스크린샷](media/diagnostic-settings/menu-resource.png)

   - 하나 이상의 리소스인 경우 Azure Monitor 메뉴의 **설정** 에서 **진단 설정** 을 클릭합니다.

        ![진단 설정이 강조 표시된 Azure Monitor 메뉴의 설정 섹션 스크린샷](media/diagnostic-settings/menu-monitor.png)

   - 활동 로그의 경우 **Azure Monitor** 메뉴에서 **활동 로그** 를 클릭한 다음 **진단 설정** 을 클릭합니다. 활동 로그에 대한 레거시 구성을 사용하지 않도록 설정해야 합니다. 자세한 내용은 [기존 설정 사용 안 함](./activity-log.md#legacy-collection-methods)을 참조하세요.

        ![모니터-활동 로그 메뉴 모음에서 활동 로그가 선택되어 있고 진단 설정이 강조 표시된 Azure Monitor 메뉴 스크린샷.](media/diagnostic-settings/menu-activity-log.png)

2. 선택한 리소스에 설정이 없는 경우, 설정을 만들라는 메시지가 표시됩니다. **진단 설정 추가** 를 클릭합니다.

   ![진단 설정 추가 - 기존 설정 없음](media/diagnostic-settings/add-setting.png)

   리소스에 기존 설정이 있는 경우 이미 구성된 설정의 목록이 표시됩니다. **진단 설정 추가** 를 클릭해 새 설정을 추가하거나, **설정 편집** 을 선택해 기존 설정을 편집합니다. 각 설정에는 대상 유형이 하나만 있을 수 있습니다.

   ![진단 설정 추가 - 기존 설정](media/diagnostic-settings/edit-setting.png)

3. 아직 없는 경우 설정에 이름을 지정합니다.

      :::image type="Add diagnostic setting" source="media/diagnostic-settings/setting-new-blank.png" alt-text="새 진단 설정 추가":::

4. **경로에 대 한 로그 및 메트릭** -로그의 경우 범주 그룹을 선택 하거나 나중에 지정 된 대상으로 보내려는 각 데이터 범주의 개별 상자를 확인 합니다. 범주 목록은 각 Azure 서비스마다 다릅니다. Azure Monitor 로그에 메트릭을 저장 하려는 경우 *Allmetrics* 을 선택 합니다. 

5. **대상 세부 정보** - 각 대상에 대한 확인란을 선택합니다. 각 확인란을 선택하면 추가 정보를 추가할 수 있는 옵션이 표시됩니다.

      ![Log Analytics 또는 Event Hubs로 보내기](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - 구독 및 작업 영역을 입력합니다.  작업 영역이 없는 경우 [진행하기 전에 만들어야](../logs/quick-create-workspace.md) 합니다.

    1. **Event Hubs** -다음 조건을 지정 합니다.
       - 이벤트 허브가 포함 된 구독
       - 이벤트 허브 네임 스페이스-아직 없는 경우 [하나 만들어야](../../event-hubs/event-hubs-create.md) 합니다.
       - 모든 데이터를 보낼 이벤트 허브 이름 (선택 사항)입니다. 이름을 지정하지 않으면 각 로그 범주에 대해 이벤트 허브가 만들어집니다. 여러 범주를 보내는 경우 이름을 지정하여 만들어진 이벤트 허브 수를 제한할 수 있습니다. 자세한 내용은 [Azure Event Hubs 할당량 및 제한](../../event-hubs/event-hubs-quotas.md)을 참조하세요.
       - 이벤트 허브 정책(선택 사항) - 정책은 스트리밍 메커니즘이 보유한 권한을 정의합니다. 자세한 내용은 [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy)를 참조하세요.

    1. **스토리지** -구독, 스토리지 계정 및 보존 정책을 선택합니다.

        ![스토리지로 보내기](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 보존 정책을 0으로 설정 하 고 [Azure Storage 수명 주기 정책을](/azure/storage/blobs/lifecycle-management-policy-configure) 사용 하거나 예약 된 작업을 사용 하 여 저장소에서 데이터를 삭제 하는 것이 좋습니다. 이러한 전략은 보다 일관 된 동작을 제공할 가능성이 높습니다. 
        >
        > 첫째, 보관에 스토리지를 사용하는 경우 일반적으로 365일을 초과하여 데이터를 보관하고자 합니다. 둘째, 0보다 큰 보존 정책을 선택하는 경우 만료 날짜는 저장 시점에 로그에 연결됩니다. 저장한 후에는 해당 로그의 날짜를 변경할 수 없습니다. 예를 들어, *WorkflowRuntime* 에 대한 보존 정책을 180일로 설정한 다음 24시간 후에 365일로 설정하면, 처음 24시간 동안 저장된 로그는 180일 후 자동으로 삭제되는 반면, 모든 후속 로그는 자동으로 365일 후에 삭제됩니다. 나중에 보존 정책을 변경해도 처음 24시간 동안의 로그가 365일 동안 유지되지 않습니다.

     1. **파트너 통합** -먼저 구독에 파트너 통합을 설치 해야 합니다. 구성 옵션은 파트너에 따라 다릅니다. 자세한 내용은 [Azure Monitor 파트너 통합](/azure/partner-solutions/overview/)을 참조 하세요. 
    
6. **저장** 을 클릭합니다.

몇 분 후 새 설정이 이 리소스에 대한 설정 목록에 표시되고, 새 이벤트 데이터가 생성되는 즉시 로그가 지정된 대상에 스트리밍됩니다. 이벤트를 내보내는 시점과 [Log Analytics 작업 영역에 표시](../logs/data-ingestion-time.md)되는 시점 사이에 최대 15분이 걸릴 수 있습니다.

## <a name="create-using-powershell"></a>PowerShell을 사용하여 만들기

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet을 사용하여 [Azure PowerShell](../powershell-samples.md)로 진단 설정을 만듭니다. 매개 변수에 대한 설명은 이 cmdlet에 대한 설명서를 참조하세요.

> [!IMPORTANT]
> Azure 활동 로그에는 이 방법을 사용할 수 없습니다. 대신 [Resource Manager 템플릿을 사용하여 Azure Monitor에서 진단 설정 만들기](./resource-manager-diagnostic-settings.md)를 통해 Resource Manager 템플릿을 만들고 PowerShell을 사용하여 배포합니다.

다음은 세 가지 대상을 모두 사용하여 진단 설정을 만드는 예제 PowerShell cmdlet입니다.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Azure CLI를 사용하여 만들기

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 [Azure CLI](/cli/azure/monitor)를 사용하는 진단 설정을 만듭니다. 매개 변수에 대한 설명은 이 명령에 대한 설명서를 참조하세요.

> [!IMPORTANT]
> Azure 활동 로그에는 이 방법을 사용할 수 없습니다. 대신 [Resource Manager 템플릿을 사용하여 Azure Monitor에서 진단 설정 만들기](./resource-manager-diagnostic-settings.md)를 통해 Resource Manager 템플릿을 만들고 CLI를 사용하여 배포합니다.

다음은 세 가지 대상을 모두 사용하여 진단 설정을 만드는 예제 CLI 명령입니다. 구문은 클라이언트에 따라 약간 다릅니다.

# <a name="cmd"></a>[CMD](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 만들기
Resource Manager 템플릿을 사용하여 진단 설정을 만들거나 업데이트하려면 [Azure Monitor의 진단 설정용 Resource Manager 템플릿 샘플](./resource-manager-diagnostic-settings.md)을 참조하세요.

## <a name="create-using-rest-api"></a>REST API를 사용하여 만들기
[Azure Monitor REST API](/rest/api/monitor/)를 사용하여 진단 설정을 만들거나 업데이트하려면 [진단 설정](/rest/api/monitor/diagnosticsettings)을 참조하세요.

## <a name="create-at-scale-using-azure-policy"></a>Azure Policy를 사용 하 여 대규모로 만들기

진단 설정은 각 Azure 리소스에 대해 만들어야 하며, 각 리소스를 만들 때마다 진단 설정을 자동으로 만드는 데 Azure Policy를 사용할 수 있습니다. 각 Azure 리소스 종류에는 진단 설정에 나열해야 하는 고유한 범주 세트가 있습니다. 이러한 이유로 각 리소스 종류에는 별도의 정책 정의가 필요 합니다. 일부 리소스 종류에는 수정 없이 할당할 수 있는 기본 제공 정책 정의가 있습니다. 다른 리소스 종류의 경우 사용자 지정 정의를 만들어야 합니다. 

리소스 로그 범주 그룹을 추가 하 여 이제 로그 범주가 변경 될 때 동적으로 업데이트 되는 옵션을 선택할 수 있습니다.  자세한 내용은이 문서의 앞부분에 나열 된 [진단 설정 원본](#sources) 을 참조 하세요. 

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor에 대한 기본 제공 정책 정의
각 리소스 유형에 대 한 기본 제공 정책 정의에는 두 가지가 있습니다. 하나는 Log Analytics 작업 영역으로 보내고 다른 하나는 이벤트 허브로 보냅니다. 위치가 하나만 필요한 경우 리소스 종류에 대해 해당 정책을 할당 합니다. 둘 다 필요한 경우 리소스에 대 한 두 정책 정의를 모두 할당 합니다.

예를 들어 다음 이미지는 Azure Data Lake Analytics에 대 한 기본 제공 진단 설정 정책 정의를 보여 줍니다.

![Data Lake Analytics에 대한 두 가지 기본 제공 진단 설정 정책 정의를 보여 주는 Azure Policy 정의 페이지의 부분 스크린샷](media/diagnostic-settings/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>사용자 지정 정책 정의
기본 제공 정책이 없는 리소스 종류의 경우 사용자 지정 정책 정의를 만들어야 합니다. 기존 기본 제공 정책을 복사한 후 리소스 형식에 맞게 수정 하 여 Azure Portal에서이 작업을 수동으로 수행할 수 있습니다. 그러나 PowerShell 갤러리 스크립트를 사용 하 여 프로그래밍 방식으로 정책을 만드는 것이 더 효율적입니다.

[AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) 스크립트는 PowerShell 또는 Azure CLI를 사용 하 여 설치할 수 있는 특정 리소스 종류에 대 한 정책 파일을 만듭니다. 진단 설정에 대 한 사용자 지정 정책 정의를 만들려면 다음 절차를 따르십시오.

1. [Azure PowerShell](/powershell/azure/install-az-ps)이 설치되어 있는지 확인합니다.
2. 다음 명령을 사용 하 여 스크립트를 설치 합니다.
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. 매개 변수를 사용 하 여 스크립트를 실행 하 여 로그를 보낼 위치를 지정 합니다. 구독 및 리소스 종류를 지정 하 라는 메시지가 표시 됩니다. 

   예를 들어 Log Analytics 작업 영역 및 이벤트 허브에 로그를 전송 하는 정책 정의를 만들려면 다음 명령을 사용 합니다.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

   또는 명령에서 구독 및 리소스 종류를 지정할 수 있습니다. 예를 들어 Log Analytics 작업 영역에 로그를 보내고 SQL Server 데이터베이스용 이벤트 허브를 전송 하는 정책 정의를 만들려면 다음 명령을 사용 합니다.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. 스크립트는 각 정책 정의에 대해 별도의 폴더를 만듭니다. 각 폴더에는 *azurepolicy. json*, *azurepolicy. rules* 및 *azurepolicy* 라는 세 개의 파일이 있습니다. Azure Portal에서 정책을 수동으로 만들려면 전체 정책 정의가 포함 되어 있으므로 *azurepolicy. json* 의 내용을 복사 하 여 붙여 넣을 수 있습니다. PowerShell 또는 Azure CLI에서 다른 두 파일을 사용 하 여 명령줄에서 정책 정의를 만듭니다.

   다음 예에서는 PowerShell과 Azure CLI 모두에서 정책 정의를 설치 하는 방법을 보여 줍니다. 각 예제에는 기본 제공 정책 정의를 사용 하 여 새 정책 정의를 그룹화 하는 **모니터링** 범주를 지정 하는 메타 데이터가 포함 되어 있습니다.

   ```azurepowershell
   New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
   ```

   ```azurecli
   az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
   ```

### <a name="initiative"></a>이니셔티브
각 정책 정의에 대한 할당을 만드는 대신, 일반적인 전략은 각 Azure 서비스에 대한 진단 설정을 만드는 정책 정의를 포함하는 이니셔티브를 만드는 것입니다. 환경을 관리 하는 방법에 따라 이니셔티브와 관리 그룹, 구독 또는 리소스 그룹 간의 할당을 만듭니다. 이 전략은 다음과 같은 이점을 제공합니다.

- 리소스 종류마다 여러 할당을 만드는 대신 이니셔티브에 대해 단일 할당을 만듭니다. 여러 모니터링 그룹, 구독 또는 리소스 그룹에 동일한 이니셔티브를 사용합니다.
- 새 리소스 유형 또는 대상을 추가해야 하는 경우 이니셔티브를 수정합니다. 예를 들어 Log Analytics 작업 영역에만 데이터를 전송 하 고 나중에 이벤트 허브를 추가 하려면 초기 요구 사항을 충족 해야 합니다. 새 할당을 만드는 대신 이니셔티브를 수정 합니다.

이니셔티브를 만드는 방법에 대 한 자세한 내용은 [이니셔티브 정의 만들기 및 할당](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition)을 참조 하세요. 다음 권장 사항을 고려할 수 있습니다.

- **범주** 를 **모니터링** 으로 설정 하 여 관련 기본 제공 및 사용자 지정 정책 정의로 그룹화 합니다.
- Log Analytics 작업 영역에 대 한 세부 정보와 이니셔티브에 포함 된 정책 정의에 대 한 이벤트 허브를 지정 하는 대신 일반적인 이니셔티브 매개 변수를 사용 합니다. 이 매개 변수를 사용 하면 모든 정책 정의에 대 한 공통 값을 쉽게 지정 하 고 필요한 경우 해당 값을 변경할 수 있습니다.

![이니셔티브 정의에 대 한 설정을 보여 주는 스크린샷](media/diagnostic-settings/initiative-definition.png)

### <a name="assignment"></a>할당 
모니터링할 리소스의 범위에 따라 Azure 관리 그룹, 구독 또는 리소스 그룹에 이니셔티브를 할당 합니다. [관리 그룹](../../governance/management-groups/overview.md) 은 범위 지정 정책, 특히 조직에 여러 구독이 있는 경우에 유용 합니다.

![Azure Portal에서 Log Analytics 작업 영역에 대 한 진단 설정의 이니셔티브 할당 섹션에 있는 기본 사항 탭에 대 한 설정의 스크린샷](media/diagnostic-settings/initiative-assignment.png)

이니셔티브 매개 변수를 사용하여 이니셔티브의 모든 정책 정의에 대해 작업 영역 또는 다른 세부 정보를 한 번 지정할 수 있습니다. 

![매개 변수 탭에 이니셔티브 매개 변수를 표시 하는 스크린샷](media/diagnostic-settings/initiative-parameters.png)

### <a name="remediation"></a>수정
이니셔티브는 생성되는 각 가상 머신에 적용됩니다. [수정 작업](../../governance/policy/how-to/remediate-resources.md) 은 이니셔티브의 정책 정의를 기존 리소스에 배포 하므로 이미 생성 된 모든 리소스에 대 한 진단 설정을 만들 수 있습니다.

Azure Portal를 사용 하 여 할당을 만들 때 재구성 작업을 동시에 만들 수 있습니다. 수정에 대한 자세한 내용은 [Azure Policy을 사용하여 비준수 리소스 수정](../../governance/policy/how-to/remediate-resources.md)을 참조하세요.

![Log Analytics 작업 영역에 대 한 이니셔티브 재구성을 보여 주는 스크린샷](media/diagnostic-settings/initiative-remediation.png)

## <a name="troubleshooting"></a>문제 해결

### <a name="metric-category-is-not-supported"></a>메트릭 범주가 지원 되지 않습니다.

진단 설정을 배포할 때 *메트릭 범주 ' xxxx '* 와 유사한 오류 메시지가 표시 됩니다. 이전 배포에 성공한 경우에도이 오류가 나타날 수 있습니다. 

리소스 관리자 템플릿, 진단 설정 REST API, Azure CLI 또는 Azure PowerShell를 사용 하는 경우 문제가 발생 합니다. Azure Portal를 통해 만든 진단 설정은 지원되는 범주 이름만 표시되므로 영향을 받지 않습니다.

이 문제는 기본 API의 최근 변경으로 인해 발생합니다. ' AllMetrics ' 이외의 메트릭 범주는 지원 되지 않으며 몇 가지 특정 Azure 서비스를 제외 하 고는 그렇지 않습니다. 이전에는 진단 설정을 배포할 때 다른 범주 이름이 무시되었습니다. Azure Monitor 백 엔드가 이러한 범주를 ' AllMetrics '로 리디렉션 했습니다.  2021년 2월을 기준으로 제공된 메트릭 범주가 정확한지 확인하기 위해 백엔드가 업데이트되었습니다. 이 변경으로 인해 일부 배포가 실패했습니다.

이 오류가 발생하면 모든 메트릭 범주 이름을 'AllMetrics'로 바꿔 배포를 업데이트하여 문제를 해결합니다. 배포가 이전에 여러 범주를 추가한 경우에는 'AllMetrics' 참조를 포함하는 단 하나만 유지해야 합니다. 문제가 계속 되 면 Azure Portal를 통해 Azure 지원에 문의 하세요. 

### <a name="setting-disappears-due-to-non-ascii-characters-in-resourceid"></a>ResourceID의 비 ASCII 문자로 인해 설정이 사라진다

진단 설정은 비 ASCII 문자(예: Preproducción)가 포함된 Resourceid를 지원하지 않습니다. Azure에서 리소스의 이름을 바꿀 수 없으므로 유일한 방법은 비 ASCII 문자 없이 새 리소스를 만드는 것입니다. 문자가 리소스 그룹에 있는 경우 그 아래에 있는 리소스를 새 리소스로 이동할 수 있습니다. 이동하지 않으면 리소스를 다시 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 플랫폼 로그에 대해 자세히 알아보기](./platform-logs-overview.md)
