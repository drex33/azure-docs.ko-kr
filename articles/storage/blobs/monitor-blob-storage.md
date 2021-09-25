---
title: Azure Blob Storage 모니터링
description: Azure Blob Storage의 성능 및 가용성을 모니터링하는 방법을 알아봅니다. Azure Blob Storage 데이터를 모니터링하고, 구성에 대해 알아보고, 메트릭 및 로그 데이터를 분석합니다.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: subject-monitoring, devx-track-csharp, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: bcfd37ff8c030136e37b4289bc37006012891412
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617548"
---
# <a name="monitoring-azure-blob-storage"></a>Azure Blob Storage 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있으면 이러한 리소스의 가용성, 성능 및 작업을 모니터링해야 합니다. 이 문서에서는 Azure Blob Storage에서 생성하는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이러한 데이터에 대한 경고를 분석하는 방법을 설명합니다.

> [!NOTE]
> Azure Monitor Azure Storage 로그는 공개 미리 보기로 제공되며 모든 퍼블릭 및 미국 정부 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기에서는 Blob(Azure Data Lake Storage Gen2 포함), 파일, 큐 및 테이블에 대한 로그를 사용하도록 설정합니다. 이 기능은 Azure Resource Manager 배포 모델을 사용하여 만든 모든 스토리지 계정에 대해 사용할 수 있습니다. 자세한 내용은 [Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

## <a name="monitor-overview"></a>모니터링 개요

각 Blob Storage 리소스에 대한 Azure Portal의 **개요** 페이지에는 요청 및 시간당 청구와 같은 리소스 사용에 대한 간략한 보기가 있습니다. 이 정보는 유용하지만, 사용할 수 있는 모니터링 데이터의 양이 적습니다. 이 데이터 중 일부는 자동으로 수집되며 리소스를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Blob Storage는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. Azure Monitor는 Azure 리소스와 다른 클라우드 및 온-프레미스의 리소스를 모니터링하는 완전한 기능 세트를 제공합니다.

다음을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md) 문서로 시작하세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

본 문서의 다음 섹션에서는 Azure Storage에서 수집하는 데이터에 대해 설명합니다. 그리고 Azure 도구를 사용하여 데이터 수집을 구성하고 수집한 데이터를 분석하는 방법을 보여주는 예제를 제공합니다.

## <a name="monitoring-data"></a>데이터 모니터링

Azure Blob Storage는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 이에 대한 내용은 [Azure 리소스의 데이터 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명되어 있습니다.

Azure Blob Storage에서 만든 메트릭 및 로그 메트릭에 대한 자세한 내용은 [Azure Blob Storage 모니터링 데이터 참조](monitor-blob-storage-reference.md)에서 확인하세요.

Azure Monitor의 메트릭과 로그는 Azure Resource Manager 스토리지 계정만 지원합니다. Azure Monitor는 클래식 스토리지 계정을 지원하지 않습니다. 클래식 스토리지 계정에서 메트릭 또는 로그를 사용하려면 Azure Resource Manager 스토리지 계정으로 마이그레이션해야 합니다. 자세한 내용은 [Azure Resource Manager 마이그레이션을](../../virtual-machines/migration-classic-resource-manager-overview.md)참조하세요.

원한다면 클래식 메트릭 및 로그를 계속 사용할 수 있습니다. 실제로 클래식 메트릭 및 로그는 Azure Monitor의 메트릭 및 로그와 동시에 사용할 수 있습니다. 이 지원은 Azure Storage가 레거시 메트릭 및 로그에 대한 서비스를 종료할 때까지 유지됩니다.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되지만, 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.

리소스 로그를 수집하려면 진단 설정을 만들어야 합니다. 설정을 만들 때 로그를 사용하도록 설정할 스토리지 유형으로 **blob** 을 선택합니다. 그런 다음, 로그를 수집할 작업에 대해 다음 범주 중 하나를 지정합니다.

| 범주 | Description |
|:---|:---|
| StorageRead | 개체에 대한 읽기 작업입니다. |
| StorageWrite | 개체에 대한 쓰기 작업입니다. |
| StorageDelete | 개체에 대한 삭제 작업입니다. |

> [!NOTE]
> Data Lake Storage Gen2는 스토리지 유형으로 표시되지 않습니다. Data Lake Storage Gen2가 Blob Storage에서 사용할 수 있는 기능 집합이기 때문입니다.

## <a name="creating-a-diagnostic-setting"></a>진단 설정 만들기

Azure Portal, PowerShell, Azure CLI, Azure Resource Manager 템플릿 또는 Azure Policy를 사용하여 진단 설정을 만들 수 있습니다.

일반 지침은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

> [!NOTE]
> Azure Monitor Azure Storage 로그는 공개 미리 보기로 제공되며 모든 퍼블릭 및 미국 정부 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기에서는 Blob(Azure Data Lake Storage Gen2 포함), 파일, 큐 및 테이블에 대한 로그를 사용하도록 설정합니다. 이 기능은 Azure Resource Manager 배포 모델을 사용하여 만든 모든 스토리지 계정에 대해 사용할 수 있습니다. 자세한 내용은 [Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Azure Portal에 로그인합니다.

2. 스토리지 계정으로 이동합니다.

3. **모니터링** 섹션에서 **진단 설정(미리 보기)** 을 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![포털 - 진단 로그](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)

4. 로그를 사용하도록 설정할 스토리지 유형으로 **blob** 을 선택합니다.

5. **진단 설정 추가** 를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![포털 - 리소스 로그 - 진단 설정 추가](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   **진단 설정** 페이지가 표시됩니다.

   > [!div class="mx-imgBorder"]
   > ![리소스 로그 페이지](media/monitor-blob-storage/diagnostic-logs-page.png)

6. 페이지의 **이름** 필드에서 이 리소스 로그 설정에 대한 이름을 입력합니다. 그런 다음, 기록할 작업(읽기, 쓰기 및 삭제 작업)과 로그를 보낼 위치를 선택합니다.

#### <a name="archive-logs-to-a-storage-account"></a>스토리지 계정에 로그 보관

로그를 스토리지 계정에 보관하도록 선택하는 경우 스토리지 계정으로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

1. 스토리지 **계정에 보관 확인란을** 선택한 다음 **구성** 단추를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![진단 설정 페이지 보관 스토리지](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. **스토리지 계정** 드롭다운 목록에서 로그를 보관하려는 스토리지 계정을 선택하고, **확인** 단추, **저장** 단추를 차례로 선택합니다.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > 스토리지 계정을 내보내기 대상으로 선택하기 전에 스토리지 계정에 대한 필수 조건을 파악하려면 [Azure 리소스 로그 보관](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)을 참조하세요.

#### <a name="stream-logs-to-azure-event-hubs"></a>Azure Event Hubs에 로그 스트림

로그를 이벤트 허브에 스트리밍하도록 선택하는 경우 이벤트 허브로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

1. 이벤트 **허브로 스트리밍 확인란을** 선택한 다음 **구성** 단추를 선택합니다.

2. **이벤트 허브 선택** 창에서 로그를 스트림할 이벤트 허브의 네임스페이스, 이름 및 정책 이름을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![진단 설정 페이지 이벤트 허브](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. **확인** 단추, **저장** 단추를 차례로 선택합니다.

#### <a name="send-logs-to-azure-log-analytics"></a>Azure Log Analytics에 로그 보내기

1. Log **Analytics에 보내기** 확인란을 선택하고, 로그 분석 작업 영역을 선택한 다음, **저장** 단추를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![진단 설정 페이지 Log Analytics](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell 명령 창을 열고, `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인합니다. 그런 다음, 화면에 나타나는 지침에 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

2. 활성 구독을 로깅을 사용하도록 설정할 스토리지 계정에 대한 구독으로 설정합니다.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>스토리지 계정에 로그 보관

로그를 스토리지 계정에 보관하도록 선택하는 경우 스토리지 계정으로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

`StorageAccountId` 매개 변수와 함께 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet을 사용하여 로그를 사용하도록 설정합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log>
```

이 코드 조각의 `<storage-service-resource--id>` 자리 표시자를 Blob service의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **엔드포인트** 페이지를 열어 확인할 수 있습니다.

**Category** 매개 변수의 값으로 `StorageRead`, `StorageWrite` 및 `StorageDelete`를 사용할 수 있습니다.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

예를 들면 다음과 같습니다.

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

각 매개 변수에 대한 설명은 [Azure PowerShell을 통해 Azure 리소스 로그 보관](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)을 참조하세요.

#### <a name="stream-logs-to-an-event-hub"></a>이벤트 허브에 로그 스트림

로그를 이벤트 허브에 스트리밍하도록 선택하는 경우 이벤트 허브로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

`EventHubAuthorizationRuleId` 매개 변수와 함께 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet을 사용하여 로그를 사용하도록 설정합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

예를 들면 다음과 같습니다.

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

각 매개 변수에 대한 설명은 [PowerShell cmdlet을 통해 Event Hubs에 데이터 스트림](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)을 참조하세요.

#### <a name="send-logs-to-log-analytics"></a>Log Analytics에 로그 보내기

`WorkspaceId` 매개 변수와 함께 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet을 사용하여 로그를 사용하도록 설정합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

예를 들면 다음과 같습니다.

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역에 Azure 리소스 로그 스트림](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)을 참조하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 먼저 [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. ID가 둘 이상의 구독과 연결된 경우 활성 구독을 로그를 사용하도록 설정할 스토리지 계정에 대한 구독으로 설정합니다.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

#### <a name="archive-logs-to-a-storage-account"></a>스토리지 계정에 로그 보관

로그를 스토리지 계정에 보관하도록 선택하는 경우 스토리지 계정으로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 로그를 사용하도록 설정합니다.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true }]'
```

이 코드 조각의 `<storage-service-resource--id>` 자리 표시자를 Blob Storage 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **엔드포인트** 페이지를 열어 확인할 수 있습니다.

**category** 매개 변수의 값으로 `StorageRead`, `StorageWrite` 및 `StorageDelete`를 사용할 수 있습니다.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

예를 들면 다음과 같습니다.

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite}]'`

각 매개 변수에 대한 설명은 [Azure CLI를 통해 리소스 로그 보관](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)을 참조하세요.

#### <a name="stream-logs-to-an-event-hub"></a>이벤트 허브에 로그 스트림

로그를 이벤트 허브에 스트리밍하도록 선택하는 경우 이벤트 허브로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 로그를 사용하도록 설정합니다.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

예를 들면 다음과 같습니다.

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

각 매개 변수에 대한 설명은 [Azure CLI를 통해 Event Hubs에 데이터 스트림](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)을 참조하세요.

#### <a name="send-logs-to-log-analytics"></a>Log Analytics에 로그 보내기

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 로그를 사용하도록 설정합니다.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

예를 들면 다음과 같습니다.

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역에 Azure 리소스 로그 스트림](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)을 참조하세요.

### <a name="template"></a>[템플릿](#tab/template)

진단 설정을 만드는 Azure Resource Manager 템플릿을 보려면 [Azure Storage에 대한 진단 설정](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)을 참조하세요.

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

정책 정의를 사용하여 진단 설정을 만들 수 있습니다. 이렇게 하면 만들거나 업데이트한 모든 계정에 대해 진단 설정이 생성되었는지 확인할 수 있습니다. 자세한 내용은 [Azure Policy 기본 제공 정의 Azure Storage](../common/policy-reference.md)을 참조 하세요.

---

## <a name="analyzing-metrics"></a>메트릭 분석

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭과 함께 Azure Storage의 메트릭을 분석할 수 있습니다. **Azure Monitor** 메뉴에서 **메트릭** 을 선택하여 메트릭 탐색기를 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

다음 예제는 계정 수준에서 **트랜잭션** 을 보는 방법을 보여줍니다.

![Azure Portal에서 메트릭에 액세스하는 스크린 샷](./media/monitor-blob-storage/access-metrics-portal.png)

차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 메트릭을 필터링할 수 있습니다. 다음 예제는 **API 이름** 차원의 값을 선택하여 특정 작업의 계정 수준에서 **트랜잭션** 을 보는 방법을 보여줍니다.

![Azure Portal에서 차원을 사용하여 메트릭에 액세스하는 스크린 샷](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Azure Storage에서 지원하는 차원의 전체 목록은 [메트릭 차원](monitor-blob-storage-reference.md#metrics-dimensions)을 참조하세요.

Azure Blob Storage에 대한 메트릭은 다음 네임스페이스에 있습니다.

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices

Azure Blob Storage를 비롯한 모든 Azure Monitor 지원 메트릭의 목록은 [Azure Monitor에서 지원하는 메트릭](../../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

### <a name="accessing-metrics"></a>메트릭 액세스

> [!TIP]
> Azure CLI 또는 .NET 예제를 보려면 아래의 탭 중에서 해당 탭을 선택합니다.

### <a name="net-sdk"></a>[.NET SDK](#tab/azure-portal)

Azure Monitor는 메트릭 정의 및 값을 읽는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)를 제공합니다. [샘플 코드](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)는 다른 매개 변수로 SDK를 사용하는 방법을 보여줍니다. 스토리지 메트릭을 사용하려면 `0.18.0-preview` 이상의 버전을 사용해야 합니다.

이 예에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정 또는 Blob Storage 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에 있는 스토리지 계정의 **엔드포인트** 페이지에서 확인할 수 있습니다.

`<subscription-ID>` 변수를 구독의 ID로 바꿉니다. `<tenant-ID>`, `<application-ID>` 및 `<AccessKey>` 값을 얻는 방법에 대한 지침은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

#### <a name="list-the-account-level-metric-definition"></a>계정 수준 메트릭 정의 나열

다음 예제는 계정 수준에서 메트릭 정의를 나열하는 방법을 보여줍니다.

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>계정 수준 메트릭 값 읽기

다음 예제는 계정 수준에서 `UsedCapacity` 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>다차원 메트릭 값 읽기

다차원 메트릭에서 특정 차원 값에 대한 메트릭 데이터를 읽으려면 메타데이터 필터를 정의해야 합니다.

다음 예제는 다차원을 지원하는 메트릭에서 메트릭 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>메트릭 정의 나열

스토리지 계정 또는 Blob Storage 서비스의 메트릭 정의를 나열할 수 있습니다. [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition)을 사용합니다.

이 예에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정의 리소스 ID 또는 Blob Storage 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에 있는 스토리지 계정의 **엔드포인트** 페이지에서 확인할 수 있습니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>메트릭 값 읽기

스토리지 계정 또는 Blob Storage 서비스의 계정 수준 메트릭 값을 읽을 수 있습니다. [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) cmdlet을 사용합니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>계정 수준 메트릭 정의 나열

스토리지 계정 또는 Blob Storage 서비스의 메트릭 정의를 나열할 수 있습니다. [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) 명령을 사용합니다.

이 예에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정의 리소스 ID 또는 Blob Storage 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에 있는 스토리지 계정의 **엔드포인트** 페이지에서 확인할 수 있습니다.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>계정 수준 메트릭 값 읽기

스토리지 계정 또는 Blob Storage 서비스의 메트릭 값을 읽을 수 있습니다. [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) 명령을 사용합니다.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="template"></a>[템플릿](#tab/template)

해당 없음.

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

해당 없음.

---

## <a name="analyzing-logs"></a>로그 분석

리소스 로그는 스토리지 계정의 BLOB으로, 이벤트 데이터로 또는 로그 분석 쿼리를 통해 액세스할 수 있습니다.

이러한 로그에 표시되는 필드에 대한 자세한 참조는 [Azure Blob Storage 모니터링 데이터 참조](monitor-blob-storage-reference.md)에서 확인하세요.

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 공개 미리 보기 상태 이며 모든 공용 및 미국 정부 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기에서는 BLOB(Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 범용 v1의 프리미엄 스토리지 계정, 범용 v2 스토리지 계정에 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 스토리지 계정에 BLOB 엔드포인트의 활동은 있지만 테이블 또는 큐 엔드포인트의 활동이 없는 경우 BLOB 서비스와 관련된 로그만 만들어집니다. Azure Storage는 스토리지 서비스에 대해 성공한 요청과 실패한 요청의 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 스토리지 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다.

### <a name="log-authenticated-requests"></a>인증된 요청 기록

 다음과 같은 유형의 인증된 요청이 기록됩니다.

- 성공한 요청
- 실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류)
- 실패한 요청과 성공한 요청을 포함하여 SAS(공유 액세스 서명) 또는 OAuth를 사용하는 요청
- 분석 데이터( **$logs** 컨테이너의 클래식 로그 데이터 및 **$metric** 테이블의 클래식 메트릭 데이터)에 대한 요청

로그 만들기 또는 삭제처럼 Blob Storage 서비스 자체에서 만든 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [Storage에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)와 [Storage 로그 형식](monitor-blob-storage-reference.md)을 참조하세요.

### <a name="log-anonymous-requests"></a>익명 요청 기록

 다음과 같은 유형의 익명 요청이 기록됩니다.

- 성공한 요청
- 서버 오류
- 클라이언트와 서버 모두에 대한 시간 제한 오류
- 304(수정되지 않음) 오류 코드와 함께 실패한 GET 요청

그 외의 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [Storage에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)와 [Storage 로그 형식](monitor-blob-storage-reference.md)을 참조하세요.

### <a name="accessing-logs-in-a-storage-account"></a>스토리지 계정에서 로그 액세스

로그는 대상 스토리지 계정의 컨테이너에 저장된 BLOB으로 표시됩니다. 데이터는 단일 BLOB 내에 줄로 구분된 JSON 페이로드로 수집 및 저장됩니다. BLOB 이름은 다음가 같은 명명 규칙을 따릅니다.

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

예를 들면 다음과 같습니다.

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>이벤트 허브에서 로그 액세스

이벤트 허브에 전송된 로그는 파일로 저장되지 않지만, 이벤트 허브에서 로그 정보를 받았는지 확인할 수 있습니다. Azure Portal에서 이벤트 허브로 이동하여 **들어오는 메시지** 수가 0보다 큰지 확인하면 됩니다.

![감사 로그](media/monitor-blob-storage/event-hub-log.png)

보안 정보와 이벤트 관리 및 모니터링 도구를 사용하여 이벤트 허브로 전송된 로그 데이터에 액세스하여 읽을 수 있습니다. 자세한 내용은 [내 이벤트 허브로 보내지는 모니터링 데이터를 사용하여 수행할 수 있는 작업은 무엇인가요?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)를 참조하세요.

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 로그 액세스

Azure Monitor 로그 쿼리를 사용하여 Log Analytics 작업 영역으로 전송된 로그에 액세스할 수 있습니다.

자세한 내용은 [Azure Monitor에서 Log Analytics 시작](../../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요.

데이터는 **StorageBlobLog** 테이블에 저장됩니다. Data Lake Storage Gen2에 대한 로그는 전용 테이블에 표시되지 않습니다. Data Lake Storage Gen2가 서비스가 아니기 때문입니다. 스토리지 계정에서 사용할 수 있는 기능 집합입니다. 이러한 기능을 사용하도록 설정하면 로그가 StorageBlobLogs 테이블에 계속 표시됩니다.

#### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

다음 쿼리를 **로그 검색** 창에 입력하면 Blob Storage를 모니터링하는 데 도움이 됩니다. 이러한 쿼리는 [새 언어](../../azure-monitor/logs/log-query-overview.md)에서 작동합니다.

> [!IMPORTANT]
> 스토리지 계정 리소스 그룹 메뉴에서 **로그** 를 선택하면 현재 리소스 그룹으로 설정된 쿼리 범위가 포함된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스 그룹의 데이터만 포함됩니다. 다른 리소스의 데이터 또는 다른 Azure 리소스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../../azure-monitor/logs/scope.md)를 참조하세요.

다음 쿼리를 사용하여 Azure Storage 계정을 모니터링할 수 있습니다.

- 아래는 지난 사흘 동안 가장 많이 발생한 상위 10개 오류를 나열하는 쿼리입니다.

    ```kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```

- 아래는 지난 사흘 동안 가장 많은 오류를 일으킨 상위 10개 작업을 나열하는 쿼리입니다.

    ```kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```

- 아래는 지난 사흘 동안 엔드투엔드 대기 시간이 가장 길었던 상위 10개 작업을 나열하는 쿼리입니다.

    ```kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```

- 아래는 지난 사흘 동안 서버 쪽 제한 오류를 일으킨 모든 작업을 나열하는 쿼리입니다.

    ```kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```

- 아래는 지난 사흘 동안 익명 액세스를 사용한 모든 요청을 나열하는 쿼리입니다.

    ```kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```

- 아래는 지난 사흘 동안 사용된 작업의 원형 차트를 만드는 쿼리입니다.

    ```kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc
    | render piechart
    ```

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

### <a name="logs-in-azure-monitor"></a>Azure Monitor의 로그

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |![예](../media/icons/yes-icon.png)  <sup>2</sup>              | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png)  <sup>2</sup>|![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |

### <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)              | ![예](../media/icons/yes-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

<sup>2</sup>    기능은 미리 보기 수준에서 지원 됩니다.

## <a name="faq"></a>FAQ

**Azure Storage가 Managed Disks 또는 Unmanaged Disks에 대한 메트릭을 지원하나요?**

아니요. Azure Compute는 디스크에 대한 메트릭을 지원합니다. 자세한 내용은 [관리형 디스크와 비관리형 디스크의 디스크당 메트릭](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Blob Storage에서 만든 로그 및 메트릭에 대한 참조는 [Azure Blob Storage 모니터링 데이터 참조](monitor-blob-storage-reference.md)에서 확인하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용한 Azure 리소스 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
- 메트릭 마이그레이션에 대한 자세한 내용은 [Azure Storage 메트릭 마이그레이션](../common/storage-metrics-migration.md)을 참조하세요.
- 공통 시나리오 및 모범 사례는 [Azure Blob Storage 모니터링에 대한 모범 사례](blob-storage-monitoring-scenarios.md)를 참조하세요.
