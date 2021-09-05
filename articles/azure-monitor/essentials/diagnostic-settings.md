---
title: 플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기
description: 진단 설정을 사용하여 Azure Monitor 로그, Azure Storage 또는 Azure Event Hubs에 Azure Monitor 플랫폼 메트릭 및 로그를 보냅니다.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2021
ms.openlocfilehash: 0a161c2341137abc047d81b408058ca56e192526
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531110"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기
Azure 활동 로그 및 리소스 로그를 포함한 Azure의 [플랫폼 로그](./platform-logs-overview.md)에서 Azure 리소스 및 이에 따른 Azure 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다. [플랫폼 메트릭](./data-platform-metrics.md)은 기본적으로 수집되며 일반적으로 Azure 모니터 메트릭 데이터베이스에 저장됩니다. 이 문서에서는 플랫폼 메트릭 및 플랫폼 로그를 다른 대상으로 보내기 위한 진단 설정을 만들고 구성하는 방법에 대한 세부 정보를 제공합니다.

> [!IMPORTANT]
> 활동 로그에 대한 진단 설정을 만들기 전에 모든 레거시 구성을 사용하지 않도록 설정해야 합니다. 자세한 내용은 [레거시 컬렉션 메서드](../essentials/activity-log.md#legacy-collection-methods)를 참조하세요.

각 Azure 리소스에는 다음 조건을 정의하는 자체 진단 설정이 필요합니다.

- 설정에 정의된 대상으로 전송되는 로그 및 메트릭 데이터의 범주. 사용 가능한 범주는 리소스 유형에 따라 달라집니다.
- 로그를 보낼 하나 이상의 대상입니다. 현재 대상에는 Log Analytics 작업 영역, Event Hubs 및 Azure Storage가 포함됩니다.

단일 진단 설정으로 각 대상 중 하나만 정의할 수 있습니다. 데이터를 2개 이상의 특정 대상 유형(예: 두 개의 다른 Log Analytics 작업 영역) 으로 보내려면 여러 개의 설정을 만듭니다. 각 리소스는 진단 설정을 5개까지 포함할 수 있습니다.

다음 비디오에서는 진단 설정으로 플랫폼 로그를 라우팅하는 방법을 보여줍니다.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [플랫폼 메트릭](./metrics-supported.md)은 [Azure Monitor Metrics](./data-platform-metrics.md)에 자동으로 전송됩니다. 진단 설정을 사용하면 특정 제한과 함께 [로그 쿼리](../logs/log-query-overview.md)를 사용하여 다른 모니터링 데이터를 분석하기 위해 특정 Azure 서비스에 대한 메트릭을 Azure Monitor 로그에 전송할 수 있습니다. 
>  
>  
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다. 예: Blockchain의 'IOReadBytes' 메트릭은 노드 수준에서 탐색 및 차트 표시가 가능합니다. 하지만 진단 설정을 통해 내보낸 메트릭은 모든 노드에 대한 모든 읽기 바이트로 표시됩니다. 또한 내부 제한으로 인해, 모든 메트릭을 Azure Monitor Logs/Log Analytics로 내보낼 수 있는 것은 아닙니다. 자세한 내용은 [내보낼 수 있는 메트릭 목록](./metrics-supported-export-diagnostic-settings.md)을 참조하세요. 
>  
>  
> 특정 메트릭에 대한 제한을 해결하려면 [Metrics REST API](/rest/api/monitor/metrics/list)를 사용하여 수동으로 추출하고 [Azure Monitor Data collector API](../logs/data-collector-api.md)를 사용하여 Azure Monitor Logs로 가져오는 것이 좋습니다.  


## <a name="destinations"></a>대상
다음 표의 대상에 플랫폼 로그 및 메트릭을 보낼 수 있습니다. 

| 대상 | Description |
|:---|:---|
| [Log Analytics 작업 영역](../logs/design-logs-deployment.md) | Log Analytics 작업 영역에 로그 및 메트릭을 수집하면 강력한 로그 쿼리를 사용하여 Azure Monitor로 보낸 다른 모니터링 데이터로 로그 및 메트릭을 분석할 수 있으며 경고 및 시각화와 같은 기타 Azure Monitor 기능을 활용할 수 있습니다. |
| [Event Hubs](../../event-hubs/index.yml) | 로그 및 메트릭을 이벤트 허브로 보내면 타사 SIEM과 같은 외부 시스템 및 기타 로그 분석 솔루션으로 데이터를 스트리밍할 수 있습니다.  |
| [Azure Storage 계정](../../storage/blobs/index.yml) | Azure Storage 계정에 로그 및 메트릭을 보관하면 감사, 정적 분석, 백업에 유용합니다. Azure Storage는 Azure Monitor 로그 및 Log Analytics 작업 영역보다 비용이 적고 로그를 무기한으로 보관할 수 있습니다.  |


### <a name="destination-requirements"></a>대상 요구 사항

진단 설정을 만들기 전에 진단 설정의 대상을 만들어야 합니다. 설정을 구성하는 사용자가 두 구독에 대한 적절한 Azure RBAC 액세스를 가진 경우 대상은 로그를 전송하는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다. Azure Lighthouse를 사용하여 진달 설정을 다른 Azure Active Directory 테넌트의 작업 영역으로 보낼 수도 있습니다. 다음 표에는 지역 제한을 포함하여 각 대상에 대한 고유한 요구 사항이 나타나 있습니다.

| 대상 | 요구 사항 |
|:---|:---|
| Log Analytics 작업 영역 | 작업 영역이 모니터링되는 리소스와 동일한 지역에 있을 필요는 없습니다.|
| 이벤트 허브(영문) | 네임스페이스에 대한 공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. Event Hubs로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요합니다. 스트리밍을 포함하도록 진단 설정을 업데이트하려면 해당 Event Hubs 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다.<br><br>지역별 리소스인 경우 이벤트 허브 네임스페이스가 모니터링되는 리소스와 동일한 지역에 있어야 합니다. |
| Azure Storage 계정 | 데이터에 대한 액세스를 보다 잘 제어할 수 있도록 다른 비 모니터링 데이터가 저장된 기존 스토리지 계정을 사용하지 않아야 합니다. 활동 로그와 리소스 로그를 함께 보관하는 경우, 동일한 스토리지 계정을 사용하여 모든 모니터링 데이터를 중앙 위치에서 보관할 수 있습니다.<br><br>변경이 불가능한 스토리지로 데이터를 보내려면 [Blob Storage에 대한 불변성 정책 설정 및 관리](../../storage/blobs/immutable-policy-configure-version-scope.md)에서 설명하는 것과 같이 스토리지 계정에 대해 불변성 정책을 설정합니다. 보호된 추가 BLOB 쓰기 사용을 비롯하여 이 문서의 모든 단계를 따라야 합니다.<br><br>지역별 리소스인 경우 스토리지 계정이 모니터링되는 리소스와 동일한 지역에 있어야 합니다. |

> [!NOTE]
> Azure Data Lake Storage Gen2 계정은 현재 Azure Portal에서 유효한 옵션으로 나열될 수 있지만 진단 설정의 대상으로 지원되지 않습니다.

> [!NOTE]
> 가상 네트워크를 사용하는 경우 Azure Monitor(진단 설정)에서 Event Hubs 리소스에 액세스할 수 없습니다. Azure Monitor(진단 설정) 서비스에 Event Hubs 리소스에 대한 액세스 권한이 부여되도록 하려면 Event Hubs에서 신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 우회하도록 허용 설정을 사용해야 합니다. 


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

    ![진단 설정 추가](media/diagnostic-settings/setting-new-blank.png)

4. **범주 세부 정보(라우팅 대상)** - 이후에 지정된 대상으로 보내고자 하는 데이터 범주에 대해 각 확인란을 선택합니다. 범주 목록은 각 Azure 서비스마다 다릅니다.

     - **Allmetrics** 는 리소스의 플랫폼 메트릭을 로그 형식으로 Azure 로그 저장소로 라우팅합니다. 이러한 메트릭은 일반적으로 Azure Monitor 메트릭 시계열 데이터베이스로만 전송됩니다. Azure Monitor 로그 저장소(Log Analytics를 통해 검색 가능)로 보내면 다른 로그를 검색하는 쿼리에 통합하는 데 도움이 됩니다. 일부 리소스 종류에는 이 옵션을 사용하지 못할 수 있습니다. 지원되는 경우 [Azure Monitor 지원되는 메트릭](./metrics-supported.md)은 리소스 종류에 대해 수집되는 메트릭을 나열합니다.

       > [!NOTE]
       > 이 문서의 앞부분에서 Azure Monitor 로그로의 메트릭 라우팅에 대한 제한 사항을 참조하세요.  


     - **로그** 에는 리소스 종류에 따라 사용 가능한 다양한 범주가 나열됩니다. 대상으로 라우팅할 모든 범주를 선택합니다.

5. **대상 세부 정보** - 각 대상에 대한 확인란을 선택합니다. 각 확인란을 선택하면 추가 정보를 추가할 수 있는 옵션이 표시됩니다.

      ![Log Analytics 또는 Event Hubs로 보내기](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - 구독 및 작업 영역을 입력합니다.  작업 영역이 없는 경우 [진행하기 전에 만들어야](../logs/quick-create-workspace.md) 합니다.

    1. **Event hubs** - 다음 조건을 지정합니다.
       - 이벤트 허브가 포함된 구독
       - 이벤트 허브 네임스페이스 - 아직 없는 경우 [만들어야](../../event-hubs/event-hubs-create.md) 합니다.
       - 모든 데이터를 보낼 이벤트 허브 이름(선택 사항)입니다. 이름을 지정하지 않으면 각 로그 범주에 대해 이벤트 허브가 만들어집니다. 여러 범주를 보내는 경우 이름을 지정하여 만들어진 이벤트 허브 수를 제한할 수 있습니다. 자세한 내용은 [Azure Event Hubs 할당량 및 제한](../../event-hubs/event-hubs-quotas.md)을 참조하세요.
       - 이벤트 허브 정책(선택 사항) - 정책은 스트리밍 메커니즘이 보유한 권한을 정의합니다. 자세한 내용은 [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy)를 참조하세요.

    1. **스토리지** -구독, 스토리지 계정 및 보존 정책을 선택합니다.

        ![스토리지로 보내기](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 보존 정책을 0으로 설정하고 예약된 작업을 사용하여 스토리지에서 데이터를 수동으로 삭제하면 이후의 혼동을 피할 수 있습니다.
        >
        > 첫째, 보관에 스토리지를 사용하는 경우 일반적으로 365일을 초과하여 데이터를 보관하고자 합니다. 둘째, 0보다 큰 보존 정책을 선택하는 경우 만료 날짜는 저장 시점에 로그에 연결됩니다. 저장한 후에는 해당 로그의 날짜를 변경할 수 없습니다.
        >
        > 예를 들어, *WorkflowRuntime* 에 대한 보존 정책을 180일로 설정한 다음 24시간 후에 365일로 설정하면, 처음 24시간 동안 저장된 로그는 180일 후 자동으로 삭제되는 반면, 모든 후속 로그는 자동으로 365일 후에 삭제됩니다. 나중에 보존 정책을 변경해도 처음 24시간 동안의 로그가 365일 동안 유지되지 않습니다.

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

## <a name="create-using-azure-policy"></a>Azure Policy를 사용하여 만들기
진단 설정은 각 Azure 리소스에 대해 만들어야 하며, 각 리소스를 만들 때마다 진단 설정을 자동으로 만드는 데 Azure Policy를 사용할 수 있습니다. 자세한 내용은 [Azure Policy를 사용하여 대규모로 Azure Monitor 배포](../deploy-scale.md)를 참조하세요.

## <a name="error-metric-category-is-not-supported"></a>오류: 메트릭 범주가 지원되지 않음
진단 설정을 배포할 때 다음과 같은 오류 메시지가 표시됩니다.

   "메트릭 범주 '*xxxx*' 지원되지 않음"

예를 들면 다음과 같습니다. 

   "메트릭 범주 'ActionsFailed' 지원되지 않음"

이전에는 배포가 성공했습니다. 

Resource Manager 템플릿, 진단 설정 REST API, Azure CLI 또는 Azure PowerShell을 사용하는 경우 문제가 발생합니다. Azure Portal를 통해 만든 진단 설정은 지원되는 범주 이름만 표시되므로 영향을 받지 않습니다.

이 문제는 기본 API의 최근 변경으로 인해 발생합니다. 'AllMetrics' 이외의 메트릭 범주는 지원되지 않으며 몇 가지 매우 특정한 Azure 서비스를 제외하고는 지원되지 않습니다. 이전에는 진단 설정을 배포할 때 다른 범주 이름이 무시되었습니다. Azure Monitor 백엔드는 단순히 이러한 범주를 'AllMetrics'로 리디렉션했습니다.  2021년 2월을 기준으로 제공된 메트릭 범주가 정확한지 확인하기 위해 백엔드가 업데이트되었습니다. 이 변경으로 인해 일부 배포가 실패했습니다.

이 오류가 발생하면 모든 메트릭 범주 이름을 'AllMetrics'로 바꿔 배포를 업데이트하여 문제를 해결합니다. 배포가 이전에 여러 범주를 추가한 경우에는 'AllMetrics' 참조를 포함하는 단 하나만 유지해야 합니다. 문제가 계속되면 Azure Portal을 통해 Azure 지원에 문의하세요. 

## <a name="error-setting-disappears-due-to-non-ascii-characters-in-resourceid"></a>오류: resourceID의 비 ASCII 문자로 인해 설정이 사라짐

진단 설정은 비 ASCII 문자(예: Preproducción)가 포함된 Resourceid를 지원하지 않습니다. Azure에서 리소스의 이름을 바꿀 수 없으므로 유일한 방법은 비 ASCII 문자 없이 새 리소스를 만드는 것입니다. 문자가 리소스 그룹에 있는 경우 그 아래에 있는 리소스를 새 리소스로 이동할 수 있습니다. 이동하지 않으면 리소스를 다시 만들어야 합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure 플랫폼 로그에 대해 자세히 알아보기](./platform-logs-overview.md)
