---
title: Azure CLI에서 Azure Monitor 로그 관리
description: Azure CLI 명령을 사용하여 Azure Monitor 로그에서 작업 영역을 관리하는 방법(작업 영역이 다른 Azure 서비스와 상호 작용하는 방법 포함)을 알아봅니다.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 530602fdb471b86ba84aea0093d9e6c4d91d3c75
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820607"
---
# <a name="managing-azure-monitor-logs-in-azure-cli"></a>Azure CLI에서 Azure Monitor 로그 관리

여기에 설명된 Azure CLI 명령을 사용하여 Azure Monitor에서 로그 분석 작업 영역을 관리합니다.

> [!NOTE]
> 2024년 8월 31일에 Microsoft에서 Log Analytics 에이전트가 사용 중지될 예정입니다. 그 이후에는 Azure Monitor 에이전트를 사용할 수 있습니다. 자세한 내용은 [Azure Monitor 에이전트 개요](../agents/agents-overview.md)를 참조하세요.

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-workspace-for-monitor-logs"></a>모니터 로그의 작업 영역 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 실행하여 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 작업 영역을 만들려면 [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create) 명령을 사용합니다.

```azurecli
az group create --name ContosoRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

작업 영역에 대한 자세한 내용은 [Azure Monitor 로그 개요](./data-platform-logs.md)를 참조하세요.

## <a name="list-tables-in-your-workspace"></a>작업 영역에 테이블 나열

각 작업 영역에는 여러 행의 데이터가 있는 열이 포함된 테이블이 있습니다. 각 테이블은 데이터 원본에서 제공하는 고유한 데이터 열 집합으로 정의됩니다.

작업 영역에서 테이블을 보려면 [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list) 명령을 사용합니다.

```azurecli
az monitor log-analytics workspace table list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

출력 값 `table`은 더 읽기 쉬운 형식으로 결과를 표시합니다. 자세한 내용은 [출력 서식](/cli/azure/use-cli-effectively#output-formatting)을 참조하세요.

테이블의 보존 시간을 변경하려면 [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace table update --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name Syslog --retention-time 45
```

보존 기간은 30일에서 730일 사이입니다.

테이블에 대한 자세한 내용은 [데이터 구조](./data-platform-logs.md#data-structure)를 참조하세요.

## <a name="export-data-from-selected-tables"></a>선택한 테이블에서 데이터 내보내기

선택한 테이블의 데이터를 Azure Storage 계정 또는 Azure Event Hubs로 계속 내보낼 수 있습니다. [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create) 명령을 사용합니다.

```azurecli
az monitor log-analytics workspace data-export create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --table Syslog \
   --destination /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/exportaccount \
   --enable
```

데이터 내보내기를 보려면 [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace data-export list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

데이터 내보내기를 삭제하려면 [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete) 명령을 실행합니다. `--yes` 매개 변수는 확인을 건너뜁니다.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --yes
```

데이터 내보내기에 대한 자세한 내용은 [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기](./logs-data-export.md)를 참조하세요.

## <a name="manage-a-linked-service"></a>연결된 서비스 관리

연결된 서비스는 작업 영역에서 다른 Azure 리소스로의 관계를 정의합니다. Azure Monitor 로그와 Azure 리소스는 작업에서 이 연결을 사용합니다. 자동화 계정 및 작업 영역을 고객 관리형 키에 연결하는 것이 연결된 서비스를 사용하는 예입니다.

연결된 서비스를 만들려면 [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace linked-service create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation \
   --resource-id /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Web/sites/ContosoWebApp09

az monitor log-analytics workspace linked-service list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

연결된 서비스 관계를 제거하려면 [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation
```

자세한 내용은 [az monitor log-analytics workspace linked-service](/cli/azure/monitor/log-analytics/workspace/linked-service)를 참조하세요.

## <a name="manage-linked-storage"></a>연결된 스토리지 관리

로그 분석을 위해 자체 스토리지 계정을 제공하고 관리하는 경우, 이러한 Azure CLI 명령을 사용하여 관리할 수 있습니다.

작업 영역을 스토리지 계정에 연결하려면 [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace linked-storage create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace \
   --storage-accounts /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/contosostorage \
   --type Alerts

az monitor log-analytics workspace linked-storage list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

스토리지 계정에 대한 연결을 제거하려면 [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace linked-storage delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --type Alerts
```

자세한 내용은 [Azure Monitor Log Analytics에서 고객 관리형 스토리지 계정 사용](./private-storage.md)을 참조하세요.

## <a name="manage-intelligence-packs"></a>인텔리전스 팩 관리

사용 가능한 인텔리전스 팩을 보려면 [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list) 명령을 실행합니다. 이 명령은 팩이 사용하도록 설정되어 있는지 여부도 알려줍니다.

```azurecli
az monitor log-analytics workspace pack list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

[az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable) 또는 [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable) 명령을 사용합니다.

```azurecli
az monitor log-analytics workspace pack enable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow

az monitor log-analytics workspace pack disable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow
```

## <a name="manage-saved-searches"></a>저장된 검색 관리

저장된 검색을 만들려면 [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace saved-search create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 \
   --category "Log Management" --display-name SavedSearch01 \
   --saved-query "AzureActivity | summarize count() by bin(TimeGenerated, 1h)" --fa Function01 --fp "a:string = value"
```

저장한 검색은 [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show) 명령을 사용하여 볼 수 있습니다. 저장된 모든 검색은 [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list)를 사용하여 볼 수 있습니다.

```azurecli
az monitor log-analytics workspace saved-search show --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01
az monitor log-analytics workspace saved-search list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

저장된 검색을 삭제하려면 [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace saved-search delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 --yes
```

## <a name="clean-up-deployment"></a>배포 정리

이 명령을 테스트하기 위해 리소스 그룹을 만들었다면 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 해당 리소스 그룹과 관련 콘텐츠를 모두 제거할 수 있습니다.

```azurecli
az group delete --name ContosoRG
```

기존 리소스 그룹에서 새 작업 영역을 제거하려면 [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete) 명령을 실행합니다.

```azurecli
az monitor log-analytics workspace delete --resource-group ContosoRG 
   --workspace-name ContosoWorkspace --yes
```

Log Analytics 작업 영역에는 일시 삭제 옵션이 있습니다. 삭제된 작업 영역은 삭제 후 2주 동안 복구할 수 있습니다. [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover) 명령을 실행하세요.

```azurecli
az monitor log-analytics workspace recover --resource-group ContosoRG 
   --workspace-name ContosoWorkspace
```

작업 영역을 즉시 삭제하려면 삭제 명령에 `--force` 매개 변수를 추가합니다.

## <a name="azure-cli-commands-used-in-this-article"></a>이 문서에서 사용되는 Azure CLI 명령

- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create)
- [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete)
- [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list)
- [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete)
- [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create)
- [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete)
- [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create)
- [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete)
- [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable)
- [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable)
- [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list)
- [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover)
- [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete)
- [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list)
- [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show)
- [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create)
- [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list)
- [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update)

## <a name="next-steps"></a>다음 단계

[Azure Monitor의 Log Analytics 개요](log-analytics-overview.md)