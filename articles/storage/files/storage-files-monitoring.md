---
title: Azure Files 모니터링 | Microsoft Docs
description: Azure Files의 성능 및 가용성을 모니터링하는 방법을 알아봅니다. Azure Files 데이터를 모니터링하고, 구성에 대해 알아보고, 메트릭 및 로그 데이터를 분석합니다.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 69724711421182b2a530a2411b6ecc31b95a1388
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823599"
---
# <a name="monitoring-azure-files"></a>Azure Files 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있으면 이러한 리소스의 가용성, 성능 및 작업을 모니터링해야 합니다. 이 문서에서는 Azure Files에서 생성하는 모니터링 데이터 및 Azure Monitor 기능을 사용하여 이러한 데이터에 대한 경고를 분석하는 방법에 대해 설명합니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

## <a name="monitor-overview"></a>모니터링 개요

각 Azure Files 리소스에 대한 Azure Portal의 **개요** 페이지에는 요청 및 시간당 청구와 같은 리소스 사용에 대한 간략한 보기가 있습니다. 이 정보는 유용하지만, 사용할 수 있는 모니터링 데이터의 양이 적습니다. 이 데이터 중 일부는 자동으로 수집되며 리소스를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
Azure Files는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. Azure Monitor는 Azure 리소스와 다른 클라우드 및 온-프레미스의 리소스를 모니터링하는 완전한 기능 세트를 제공합니다. 

다음을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md) 문서로 시작하세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

이 문서의 다음 섹션에서는 Azure Files에서 수집된 데이터에 대해 설명합니다. 그리고 Azure 도구를 사용하여 데이터 수집을 구성하고 수집한 데이터를 분석하는 방법을 보여주는 예제를 제공합니다.

## <a name="monitoring-data"></a>데이터 모니터링

Azure Files는 [Azure 리소스의 데이터 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 

Azure Files에서 만든 메트릭 및 로그 메트릭에 대한 자세한 내용은 [Azure Files 모니터링 데이터 참조](storage-files-monitoring-reference.md)를 참조하세요.

Azure Monitor의 메트릭과 로그는 Azure Resource Manager 스토리지 계정만 지원합니다. Azure Monitor는 클래식 스토리지 계정을 지원하지 않습니다. 클래식 스토리지 계정에서 메트릭 또는 로그를 사용하려면 Azure Resource Manager 스토리지 계정으로 마이그레이션해야 합니다. [Azure Resource Manager로 마이그레이션](../../virtual-machines/migration-classic-resource-manager-overview.md)을 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되지만, 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다. 

리소스 로그를 수집하려면 진단 설정을 만들어야 합니다. 설정을 만들 때 로그를 사용하도록 설정하려는 스토리지 유형으로 **파일** 을 선택합니다. 그런 다음, 로그를 수집하려는 작업에 대한 다음 범주 중 하나를 지정합니다. 

| 범주 | Description |
|:---|:---|
| StorageRead | 개체에 대한 읽기 작업입니다. |
| StorageWrite | 개체에 대한 쓰기 작업입니다. |
| StorageDelete | 개체에 대한 삭제 작업입니다. |

기록되는 SMB 및 REST 작업의 목록을 가져오려면 [스토리지에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [Azure Files 모니터링 데이터 참조](storage-files-monitoring-reference.md)를 참조하세요.

## <a name="creating-a-diagnostic-setting"></a>진단 설정 만들기

Azure Portal, PowerShell, Azure CLI, Azure Resource Manager 템플릿 또는 Azure Policy를 사용하여 진단 설정을 만들 수 있습니다.

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 공개 미리 보기 상태 이며 모든 공용 및 미국 정부 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기에서는 Blob(Azure Data Lake Storage Gen2 포함), 파일, 큐 및 테이블에 대한 로그를 사용하도록 설정합니다. 이 기능은 Azure Resource Manager 배포 모델을 사용하여 만든 모든 스토리지 계정에 대해 사용할 수 있습니다. [스토리지 계정 개요](../common/storage-account-overview.md)를 참조하세요.

일반적인 지침은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Azure Portal에 로그인합니다.

2. 스토리지 계정으로 이동합니다.

3. **모니터링** 섹션에서 **진단 설정(미리 보기)** 을 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![포털 - 진단 로그](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. 로그를 사용하도록 설정하려는 스토리지 유형으로 **파일** 을 선택합니다.

5. **진단 설정 추가** 를 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![포털 - 리소스 로그 - 진단 설정 추가](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   **진단 설정** 페이지가 표시됩니다.

   > [!div class="mx-imgBorder"]
   > ![리소스 로그 페이지](media/storage-files-monitoring/diagnostic-logs-page.png)

6. 페이지의 **이름** 필드에서 이 리소스 로그 설정에 대한 이름을 입력합니다. 그런 다음, 기록할 작업(읽기, 쓰기 및 삭제 작업)과 로그를 보낼 위치를 선택합니다.

#### <a name="archive-logs-to-a-storage-account"></a>스토리지 계정에 로그 보관

로그를 스토리지 계정에 보관하도록 선택하는 경우 스토리지 계정으로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 페이지의 **플랫폼 로그** 섹션을 참조하세요.

1. **스토리지 계정에 보관** 확인란을 선택한 다음, **구성** 단추를 클릭합니다.

   > [!div class="mx-imgBorder"]   
   > ![진단 설정 페이지 보관 스토리지](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. **스토리지 계정** 드롭다운 목록에서 로그를 보관할 스토리지 계정을 선택하고, **확인** 단추를 클릭한 다음, **저장** 단추를 클릭합니다.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > 스토리지 계정을 내보내기 대상으로 선택하기 전에 스토리지 계정에 대한 사전 요구 사항을 파악하려면 [Azure 리소스 로그 보관](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)을 참조하세요.

#### <a name="stream-logs-to-azure-event-hubs"></a>Azure Event Hubs에 로그 스트림

로그를 이벤트 허브에 스트리밍하도록 선택하는 경우 이벤트 허브로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

1. **이벤트 허브에 스트림** 확인란을 선택한 다음, **구성** 단추를 클릭합니다.

2. **이벤트 허브 선택** 창에서 로그를 스트림할 이벤트 허브의 네임스페이스, 이름 및 정책 이름을 선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![진단 설정 페이지 이벤트 허브](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. **확인** 단추를 클릭한 다음, **저장** 단추를 클릭합니다.

#### <a name="send-logs-to-azure-log-analytics"></a>Azure Log Analytics에 로그 보내기

1. **Log Analytics에 보내기** 확인란을 선택하고, Log Analytics 작업 영역을 선택한 다음, **저장** 단추를 클릭합니다.

   > [!div class="mx-imgBorder"]   
   > ![진단 설정 페이지 Log Analytics](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

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

이 코드 조각의 `<storage-service-resource--id>` 자리 표시자를 Azure File 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지를 열어 확인할 수 있습니다.

**Category** 매개 변수의 값으로 `StorageRead`, `StorageWrite` 및 `StorageDelete`를 사용할 수 있습니다.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

예를 들면 다음과 같습니다.

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

각 매개 변수에 대한 설명은 [Azure PowerShell을 통해 Azure 리소스 로그 보관](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)을 참조하세요.

#### <a name="stream-logs-to-an-event-hub"></a>이벤트 허브에 로그 스트림

로그를 이벤트 허브에 스트리밍하도록 선택하는 경우 이벤트 허브로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

`EventHubAuthorizationRuleId` 매개 변수와 함께 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet을 사용하여 로그를 사용하도록 설정합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

예를 들면 다음과 같습니다.

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

각 매개 변수에 대한 설명은 [PowerShell cmdlet을 통해 Event Hubs에 데이터 스트림](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)을 참조하세요.

#### <a name="send-logs-to-log-analytics"></a>Log Analytics에 로그 보내기

`WorkspaceId` 매개 변수와 함께 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet을 사용하여 로그를 사용하도록 설정합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

예를 들면 다음과 같습니다.

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

이 코드 조각의 `<storage-service-resource--id>` 자리 표시자를 Blob Storage 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지를 열어 확인할 수 있습니다.

**category** 매개 변수의 값으로 `StorageRead`, `StorageWrite` 및 `StorageDelete`를 사용할 수 있습니다.

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

예를 들면 다음과 같습니다.

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

각 매개 변수에 대한 설명은 [Azure CLI를 통해 리소스 로그 보관](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)을 참조하세요.

#### <a name="stream-logs-to-an-event-hub"></a>이벤트 허브에 로그 스트림

로그를 이벤트 허브에 스트리밍하도록 선택하는 경우 이벤트 허브로 보내는 로그 볼륨에 대한 비용을 지불하게 됩니다. 특정 가격 책정에 대해서는 **Azure Monitor 가격 책정** 페이지의 [플랫폼 로그](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) 섹션을 참조하세요.

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 로그를 사용하도록 설정합니다.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

예를 들면 다음과 같습니다.

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

각 매개 변수에 대한 설명은 [Azure CLI를 통해 Event Hubs에 데이터 스트림](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)을 참조하세요.

#### <a name="send-logs-to-log-analytics"></a>Log Analytics에 로그 보내기

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 로그를 사용하도록 설정합니다.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

예를 들면 다음과 같습니다.

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역에 Azure 리소스 로그 스트림](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)을 참조하세요.

### <a name="template"></a>[템플릿](#tab/template)

진단 설정을 만드는 Azure Resource Manager 템플릿을 보려면 [Azure Storage에 대한 진단 설정](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)을 참조하세요.

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

정책 정의를 사용하여 진단 설정을 만들 수 있습니다. 이렇게 하면 만들거나 업데이트한 모든 계정에 대해 진단 설정이 생성되었는지 확인할 수 있습니다. [Azure Storage에 대한 Azure Policy 기본 제공 정의](../common/policy-reference.md)를 참조하세요.

---

## <a name="analyzing-metrics"></a>메트릭 분석

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭과 함께 Azure Storage의 메트릭을 분석할 수 있습니다. **Azure Monitor** 메뉴에서 **메트릭** 을 선택하여 메트릭 탐색기를 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 

차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 메트릭을 필터링할 수 있습니다.  Azure Storage에서 지원하는 차원의 전체 목록은 [메트릭 차원](storage-files-monitoring-reference.md#metrics-dimensions)을 참조하세요. Azure Files에 대한 메트릭은 다음 네임스페이스에 있습니다. 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

Azure Files를 포함한 모든 Azure Monitor 지원 메트릭의 목록은 [Azure Monitor에서 지원하는 메트릭](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)을 참조하세요.

### <a name="accessing-metrics"></a>메트릭 액세스

> [!TIP]
> Azure CLI 또는 .NET 예제를 보려면 아래의 탭 중에서 해당 탭을 선택합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>메트릭 정의 나열

스토리지 계정 또는 Azure Files 서비스의 메트릭 정의를 나열할 수 있습니다. [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition)을 사용합니다.

다음 예제에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정 또는 Azure Files 서비스의 리소스 ID로 바꿉니다.  리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지에서 확인할 수 있습니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>메트릭 값 읽기

스토리지 계정 또는 Azure Files 서비스의 계정 수준 메트릭 값을 읽을 수 있습니다. [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) cmdlet을 사용합니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>계정 수준 메트릭 정의 나열

스토리지 계정 또는 Azure Files 서비스의 메트릭 정의를 나열할 수 있습니다. [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) 명령을 사용합니다.
 
다음 예제에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정 또는 Azure Files 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지에서 확인할 수 있습니다.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>계정 수준 메트릭 값 읽기

스토리지 계정 또는 Azure Files 서비스의 메트릭 값을 읽을 수 있습니다. [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) 명령을 사용합니다.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net-sdk"></a>[.NET SDK](#tab/azure-portal)

Azure Monitor는 메트릭 정의 및 값을 읽는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)를 제공합니다. [샘플 코드](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)는 다른 매개 변수로 SDK를 사용하는 방법을 보여줍니다. 스토리지 메트릭을 사용하려면 `0.18.0-preview` 이상의 버전을 사용해야 합니다.
 
다음 예제에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정 또는 Azure Files 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지에서 확인할 수 있습니다.

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
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

# <a name="template"></a>[템플릿](#tab/template)

해당 없음.

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

해당 없음.

---

## <a name="analyzing-logs"></a>로그 분석

리소스 로그는 스토리지 계정의 BLOB으로, 이벤트 데이터로 또는 로그 분석 쿼리를 통해 액세스할 수 있습니다.

기록되는 SMB 및 REST 작업의 목록을 가져오려면 [스토리지에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [Azure Files 모니터링 데이터 참조](storage-files-monitoring-reference.md)를 참조하세요.

> [!NOTE]
> Azure Monitor Azure Storage 로그는 공개 미리 보기로 제공되며 모든 퍼블릭 및 미국 정부 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기에서는 BLOB(Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 범용 v1의 프리미엄 스토리지 계정, 범용 v2 스토리지 계정에 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 스토리지 계정에 해당 파일 엔드포인트의 활동은 있지만 테이블 또는 큐 엔드포인트의 활동이 없는 경우 Azure File 서비스와 관련된 로그만 만들어집니다. Azure Storage는 스토리지 서비스에 대해 성공한 요청과 실패한 요청의 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 스토리지 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다.

### <a name="log-authenticated-requests"></a>인증된 요청 기록

 다음과 같은 유형의 인증된 요청이 기록됩니다.

- 성공한 요청
- 실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류)
- Kerberos, NTLM 또는 SAS(공유 액세스 서명)를 사용하는 요청(실패한 요청 및 성공한 요청 포함)
- 분석 데이터( **$logs** 컨테이너의 클래식 로그 데이터 및 **$metric** 테이블의 클래식 메트릭 데이터)에 대한 요청

로그 만들기 또는 삭제와 같이 스토리지 Azure Files 서비스 자체에서 만든 요청은 기록되지 않습니다. 기록되는 SMB 및 REST 요청의 전체 목록은 [스토리지에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [Azure Files 모니터링 데이터 참조](storage-files-monitoring-reference.md)를 참조하세요.

### <a name="accessing-logs-in-a-storage-account"></a>스토리지 계정에서 로그 액세스

로그는 대상 스토리지 계정의 컨테이너에 저장된 BLOB으로 표시됩니다. 데이터는 단일 BLOB 내에 줄로 구분된 JSON 페이로드로 수집 및 저장됩니다. BLOB 이름은 다음가 같은 명명 규칙을 따릅니다.

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

예를 들면 다음과 같습니다.

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>이벤트 허브에서 로그 액세스

이벤트 허브에 전송된 로그는 파일로 저장되지 않지만, 이벤트 허브에서 로그 정보를 받았는지 확인할 수 있습니다. Azure Portal에서 이벤트 허브로 이동하여 **들어오는 메시지** 수가 0보다 큰지 확인하면 됩니다. 

![감사 로그](media/storage-files-monitoring/event-hub-log.png)

보안 정보와 이벤트 관리 및 모니터링 도구를 사용하여 이벤트 허브로 전송된 로그 데이터에 액세스하여 읽을 수 있습니다. 자세한 내용은 [내 이벤트 허브로 보내지는 모니터링 데이터를 사용하여 수행할 수 있는 작업은 무엇인가요?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)를 참조하세요.

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 로그 액세스

Azure Monitor 로그 쿼리를 사용하여 Log Analytics 작업 영역으로 전송된 로그에 액세스할 수 있습니다. 데이터는 **StorageFileLogs** 테이블에 저장됩니다. 

자세한 내용은 [Log Analytics 자습서](../../azure-monitor/logs/log-analytics-tutorial.md)를 참조하세요.

#### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

Azure Files를 모니터링하는 데 도움이 되는 **로그 검색** 창에 입력할 수 있는 몇 가지 쿼리는 다음과 같습니다. 이러한 쿼리는 [새 언어](../../azure-monitor/logs/log-query-overview.md)에서 작동합니다.

> [!IMPORTANT]
> 스토리지 계정 리소스 그룹 메뉴에서 **로그** 를 선택하면 현재 리소스 그룹으로 설정된 쿼리 범위가 포함된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스 그룹의 데이터만 포함됩니다. 다른 리소스의 데이터 또는 다른 Azure 리소스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../../azure-monitor/logs/scope.md)를 참조하세요.

다음 쿼리를 사용하여 Azure 파일 공유를 모니터링할 수 있습니다.

- 지난 주에 발생한 SMB 오류 보기

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- 지난 주에 수행한 SMB 작업에 대한 원형 차트 만들기

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- 지난 주에 발생한 REST 오류 보기

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- 지난 주에 수행한 REST 작업에 대한 원형 차트 만들기

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Azure Files에 대한 열 이름 및 설명의 목록을 보려면 [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs)를 참조하세요.

쿼리를 작성하는 방법에 대한 자세한 내용은 [Log Analytics 자습서](../../azure-monitor/logs/log-analytics-tutorial.md)를 참조하세요.

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../../azure-monitor/alerts/alerts-metric-overview.md), [로그](../../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 

다음 표에는 모니터링할 몇 가지 예제 시나리오와 경고에 사용할 적절한 메트릭이 나와 있습니다.

| 시나리오 | 경고에 사용할 메트릭 |
|-|-|
| 파일 공유가 제한되었습니다. | 메트릭: 트랜잭션 수<br>차원 이름: 응답 형식 <br>차원 이름: FileShare(프리미엄 파일 공유에만 해당) |
| 파일 공유 크기가 용량의 80%입니다. | 메트릭: 파일 용량<br>차원 이름: FileShare(프리미엄 파일 공유에만 해당) |
| 파일 공유 송신이 하루에 500GiB을 초과했습니다. | 메트릭: 송신<br>차원 이름: FileShare(프리미엄 파일 공유에만 해당) |

### <a name="how-to-create-alerts-for-azure-files"></a>Azure Files에 대한 경고를 만드는 방법

1. **Azure Portal** 에서 **스토리지 계정** 으로 이동합니다. 

2. **경고**, **+ 새 경고 규칙** 을 차례로 클릭합니다.

3. **리소스 편집** 을 클릭하고, **파일 리소스 종류** 를 선택한 다음, **완료** 를 클릭합니다. 

4. **조건 추가** 를 클릭하고 경고에 대한 다음 정보를 제공합니다. 

    - **메트릭**
    - **차원 이름**
    - **경고 논리**

5. **작업 그룹 추가** 를 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 작업 그룹(이메일, SMS 등)을 경고에 추가합니다.

6. **경고 세부 정보**(예: **경고 규칙 이름**, **설명** 및 **심각도**)를 입력합니다.

7. **경고 규칙 만들기** 를 클릭하여 경고를 만듭니다.

> [!NOTE]  
> 경고를 만들었지만 노이즈가 너무 많은 경우 임계값 및 경고 논리를 조정합니다.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>파일 공유가 제한되는 경우 경고를 만드는 방법

1. **Azure portal** 에서 **스토리지 계정** 으로 이동합니다.
2. **모니터링** 섹션에서 **경고** 를 클릭한 다음, **+ 새 경고 규칙** 을 클릭합니다.
3. **리소스 편집** 을 클릭하고 스토리지 계정에 대 한 **파일 리소스 유형을** 선택한 다음, **완료** 를 클릭합니다. 예를 들어 스토리지 계정 이름이 `contoso`인 경우 `contoso/file` 리소스를 선택합니다.
4. **조건 추가** 를 클릭하여 조건을 추가합니다.
5. 스토리지 계정에 지원되는 신호 목록이 표시되면 **트랜잭션** 메트릭을 선택합니다.
6. **신호 논리 구성** 블레이드에서 **차원 이름** 드롭다운을 클릭하고, **응답 형식** 을 선택합니다.
7. **차원 값** 드롭다운을 클릭하고 파일 공유에 대한 적절한 응답 형식을 선택합니다.

    표준 파일 공유의 다음 응답 형식을 선택합니다.

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    프리미엄 파일 공유의 경우 다음과 같은 응답 형식을 선택합니다.

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > 응답 형식이 **차원 값** 드롭다운에 나열되지 않는 경우는 리소스가 제한되지 않았음을 의미합니다. 차원 값을 추가하려면 **차원 값** 드롭다운 목록 옆에 있는 **사용자 지정 값 추가** 를 선택하고 응답 형식(예: **SuccessWithThrottling**)을 입력한 다음, **확인** 을 선택하고 이 단계를 반복하여 해당 파일 공유에 적용 가능한 모든 응답 형식을 추가합니다.

8. **프리미엄 파일 공유** 의 경우 **차원 이름** 드롭다운을 클릭하고 **파일 공유** 를 선택합니다. **표준 파일 공유** 의 경우 **#10단계** 로 건너뜁니다.

   > [!NOTE]
   > 파일 공유가 표준 파일 공유인 경우 표준 파일 공유에 대한 공유 메트릭을 사용할 수 없으므로 **파일 공유** 차원에 파일 공유가 나열되지 않습니다. 표준 파일 공유에 대한 제한 경고는 스토리지 계정 내의 파일 공유가 제한되는 경우 트리거되며 이 경고는 제한된 파일 공유를 식별하지 않습니다. 표준 파일 공유에는 공유별 메트릭을 사용할 수 없으므로 스토리지 계정마다 하나의 파일 공유를 사용하는 것이 좋습니다.

9. **차원 값** 드롭다운을 클릭하고 경고를 표시할 파일 공유를 선택합니다.
10. **경고 매개 변수**(임계값, 연산자, 집계 세분성 및 평가 빈도)를 정의하고 **완료** 를 클릭합니다.

    > [!TIP]
    > 정적 임계값을 사용하는 경우 메트릭 차트는 파일 공유가 현재 제한되는 경우 적절한 임계값을 결정하는 데 도움이 될 수 있습니다. 동적 임계값을 사용하는 경우 메트릭 차트는 최근 데이터를 기반으로 계산한 임계값을 표시합니다.

11. **작업 그룹 추가** 를 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 **작업 그룹**(이메일, SMS 등)을 경고에 추가합니다.
12. **경고 세부 정보**(예: **경고 규칙 이름**, **설명** 및 **심각도**)를 입력합니다.
13. **경고 규칙 만들기** 를 클릭하여 경고를 만듭니다.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Azure 파일 공유 크기가 용량의 80%인 경우 경고를 만드는 방법

1. **Azure Portal** 에서 **스토리지 계정** 으로 이동합니다.
2. **모니터링** 섹션에서 **경고**, **+ 새 경고 규칙** 을 차례로 클릭합니다.
3. **리소스 편집** 을 클릭하고, 스토리지 계정에 대한 **파일 리소스 종류** 를 선택한 다음, **완료** 를 클릭합니다. 예를 들어 스토리지 계정 이름이 `contoso`인 경우 `contoso/file` 리소스를 선택합니다.
4. **조건 추가** 를 클릭하여 조건을 추가합니다.
5. 스토리지 계정에 지원되는 신호 목록이 표시되면 **파일 용량** 메트릭을 선택합니다.
6. **프리미엄 파일 공유** 의 경우 **차원 이름** 드롭다운을 클릭하고, **파일 공유** 를 선택합니다. **표준 파일 공유** 의 경우 **#8 단계** 로 건너뜁니다.

   > [!NOTE]
   > 파일 공유가 표준 파일 공유인 경우 공유당 메트릭을 표준 파일 공유에 사용할 수 없으므로 **파일 공유** 차원에 파일 공유가 나열되지 않습니다. 표준 파일 공유에 대한 경고는 스토리지 계정의 모든 파일 공유를 기반으로 합니다. 표준 파일 공유에는 공유당 메트릭을 사용할 수 없으므로 스토리지 계정당 하나의 파일 공유를 사용하는 것이 좋습니다.

7. **차원 값** 드롭다운을 클릭하고, 경고를 표시하려는 파일 공유를 선택합니다.
8. **임계값**(바이트)을 입력합니다. 예를 들어 파일 공유 크기가 100TiB이고 파일 공유 크기가 용량의 80%일 때 경고를 받으려는 경우 임계값(바이트)은 87960930222080입니다.
9. 나머지 **경고 매개 변수**(집계 세분성 및 평가 빈도)를 정의하고, **완료** 를 클릭합니다.
10. **작업 그룹 추가** 를 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 **작업 그룹**(이메일, SMS 등)을 경고에 추가합니다.
11. **경고 세부 정보**(예: **경고 규칙 이름**, **설명** 및 **심각도**)를 입력합니다.
12. **경고 규칙 만들기** 를 클릭하여 경고를 만듭니다.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Azure 파일 공유 송신이 하루에 500GiB를 초과하는 경우 경고를 만드는 방법

1. **Azure Portal** 에서 **스토리지 계정** 으로 이동합니다.
2. [모니터링] 섹션에서 **경고**, **+ 새 경고 규칙** 을 차례로 클릭합니다.
3. **리소스 편집** 을 클릭하고, 스토리지 계정에 대한 **파일 리소스 종류** 를 선택한 다음, **완료** 를 클릭합니다. 예를 들어 스토리지 계정 이름이 contoso인 경우 contoso/file 리소스를 선택합니다.
4. **조건 추가** 를 클릭하여 조건을 추가합니다.
5. 스토리지 계정에 지원되는 신호 목록이 표시되면 **송신** 메트릭을 선택합니다.
6. **프리미엄 파일 공유** 의 경우 **차원 이름** 드롭다운을 클릭하고, **파일 공유** 를 선택합니다. **표준 파일 공유** 의 경우 **#8 단계** 로 건너뜁니다.

   > [!NOTE]
   > 파일 공유가 표준 파일 공유인 경우 공유당 메트릭을 표준 파일 공유에 사용할 수 없으므로 **파일 공유** 차원에 파일 공유가 나열되지 않습니다. 표준 파일 공유에 대한 경고는 스토리지 계정의 모든 파일 공유를 기반으로 합니다. 표준 파일 공유에는 공유당 메트릭을 사용할 수 없으므로 스토리지 계정당 하나의 파일 공유를 사용하는 것이 좋습니다.

7. **차원 값** 드롭다운을 클릭하고, 경고를 표시하려는 파일 공유를 선택합니다.
8. [임계값]에 대해 **536870912000** 바이트를 입력합니다. 
9. **집계 세분성** 드롭다운을 클릭하고, **24시간** 을 선택합니다.
10. **평가 빈도** 를 선택하고, **완료** 를 클릭합니다.
11. **작업 그룹 추가** 를 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 **작업 그룹**(이메일, SMS 등)을 경고에 추가합니다.
12. **경고 세부 정보**(예: **경고 규칙 이름**, **설명** 및 **심각도**)를 입력합니다.
13. **경고 규칙 만들기** 를 클릭하여 경고를 만듭니다.

## <a name="next-steps"></a>다음 단계

- [Azure Files 모니터링 데이터 참조](storage-files-monitoring-reference.md)
- [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure Storage 메트릭 마이그레이션](../common/storage-metrics-migration.md)
- [Azure 파일 배포에 대한 계획](./storage-files-planning.md)
- [Azure Files를 배포하는 방법](./storage-how-to-create-file-share.md)
- [Windows에서 Azure Files 문제 해결](./storage-troubleshoot-windows-file-connection-problems.md)
- [Linux에서 Azure Files 문제 해결](./storage-troubleshoot-linux-file-connection-problems.md)
