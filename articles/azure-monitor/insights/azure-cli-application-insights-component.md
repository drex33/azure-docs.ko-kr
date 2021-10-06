---
title: Azure CLI에서 Application Insights 구성 요소 관리
description: 이 샘플 코드를 사용하여 Application Insights에서 구성 요소를 관리합니다. 이 기능은 Azure Monitor의 일부입니다.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/10/2012
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8c7b81847a1ac2484a1eaa0df56bde35eec6385
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709102"
---
# <a name="manage-application-insights-components-by-using-azure-cli"></a>Azure CLI를 사용하여 Application Insights 구성 요소 관리

Azure Monitor에서 구성 요소는 독립적으로 배포 가능한 분산 또는 마이크로 서비스 애플리케이션의 부분입니다. 이러한 Azure CLI 명령을 사용하여 Application Insights에서 구성 요소를 관리합니다.

이 문서의 예제에서 수행하는 관리 작업은 다음과 같습니다.

- 구성 요소 만들기
- 구성 요소를 웹앱에 연결합니다.
- 구성 요소를 사용하는 스토리지 계정에 해당 구성 요소를 연결합니다.
- 구성 요소에 대한 연속 내보내기 구성을 만듭니다.

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-component"></a>구성 요소 만들기

리소스 그룹 및 작업 영역이 아직 없는 경우 [az group create](/cli/azure/group#az_group_create) 및 [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)를 사용하여 만듭니다.

```azurecli
az group create --name ContosoAppInsightRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoAppInsightRG \
   --workspace-name AppInWorkspace
```

구성 요소를 만들려면 [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create) 명령을 실행합니다. [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show) 명령은 구성 요소를 표시합니다.

```azurecli
az monitor app-insights component create --resource-group ContosoAppInsightRG \
   --app ContosoApp --location eastus2 --kind web --application-type web \
   --retention-time 120
az monitor app-insights component show --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="connect-a-webapp"></a>웹앱 연결

다음 예제에서는 구성 요소를 웹앱에 연결합니다. 웹앱은 [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) 및 [az webapp create](/cli/azure/webapp#az_webapp_create) 명령을 사용하여 만들 수 있습니다.

```azurecli
az appservice plan create --resource-group ContosoAppInsightRG --name ContosoAppService
az webapp create --resource-group ContosoAppInsightRG --name ContosoApp \
   --plan ContosoAppService --name ContosoApp8765
```

[az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp) 명령을 실행하여 구성 요소를 웹앱에 연결합니다.

```azurecli
az monitor app-insights component connect-webapp --resource-group ContosoAppInsightRG \
   --app ContosoApp --web-app ContosoApp8765 --enable-debugger false --enable-profiler false
```

대신 [az monitor app-insights component connect-function](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_function) 명령을 사용하여 Azure 함수에 연결할 수 있습니다.

## <a name="link-a-component-to-storage"></a>스토리지에 구성 요소 연결

구성 요소는 스토리지 계정에 연결할 수 있습니다. 스토리지 계정을 만들려면 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용합니다.

```azurecli
az storage account create --resource-group ContosoAppInsightRG \
   --name contosolinkedstorage --location eastus2 --sku Standard_LRS
```

구성 요소를 스토리지 계정에 연결하려면 [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link) 명령을 실행합니다. 기존 링크는 [az monitor app-insights component linked-storage show](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_show) 명령을 사용하여 확인할 수 있습니다.


```azurecli
az monitor app-insights component linked-storage link  --resource-group ContosoAppInsightRG \
   --app ContosoApp --storage-account contosolinkedstorage
az monitor app-insights component linked-storage show --resource-group ContosoAppInsightRG \
   --app ContosoApp
```

스토리지의 연결을 해제하려면 [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink) 명령을 실행합니다.

```AzureCLI
az monitor app-insights component linked-storage unlink  \
   --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="set-up-continuous-export"></a>연속 내보내기 설정

연속 내보내기는 Application Insights 포털의 이벤트를 JSON 형식으로 스토리지 컨테이너에 저장합니다.

> [!NOTE]
> 연속 내보내기는 클래식 Application Insights 리소스에 대해서만 지원됩니다. [작업 영역 기반 Application Insights 리소스](../app/create-workspace-resource.md)는 [진단 설정](../app/create-workspace-resource.md#export-telemetry)을 사용해야 합니다.
>

스토리지 컨테이너를 만들려면 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령을 실행합니다. 

```azurecli
az storage container create --name contosostoragecontainer --account-name contosolinkedstorage \
   --public-access blob 
```

컨테이너에 쓰기 전용 액세스 권한이 있어야 합니다. [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create) cmdlet을 실행합니다.

```azurecli
az storage container policy create --container-name contosostoragecontainer \
   --account-name contosolinkedstorage --name WAccessPolicy --permissions w
```

[az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas) 명령을 사용하여 SAS 키를 만듭니다. `--output tsv` 매개변수 값을 사용하여 따옴표와 같이 원치 않는 형식을 지정하지 않고 키를 저장해야 합니다. 자세한 내용은 [효과적인 Azure CLI 사용](/cli/azure/use-cli-effectively)을 참조하세요.

```azurecli
containersas=$(az storage container generate-sas --name contosostoragecontainer \
   --account-name contosolinkedstorage --permissions w --output tsv)
```

연속 내보내기를 만들려면 [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create) 명령을 실행합니다.

```azurecli
az monitor app-insights component continues-export create --resource-group ContosoAppInsightRG \
   --app ContosoApp --record-types Event --dest-account contosolinkedstorage \
   --dest-container contosostoragecontainer --dest-sub-id 00000000-0000-0000-0000-000000000000 \
   --dest-sas $containersas
```

구성된 연속 내보내기는 [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete) 명령을 사용하여 삭제할 수 있습니다. 

```azurecli
az monitor app-insights component continues-export list \
   --resource-group ContosoAppInsightRG --app ContosoApp
az monitor app-insights component continues-export delete \
   --resource-group ContosoAppInsightRG --app ContosoApp --id abcdefghijklmnopqrstuvwxyz=
```

## <a name="clean-up-deployment"></a>배포 정리

이 명령을 테스트하기 위해 리소스 그룹을 만들었다면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 해당 리소스 그룹과 관련 콘텐츠를 모두 제거할 수 있습니다.

```azurecli
az group delete --name ContosoAppInsightRG 
```

## <a name="azure-cli-commands-used-in-this-article"></a>이 문서에서 사용되는 Azure CLI 명령

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp)
- [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create)
- [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete)
- [az monitor app-insights component continues-export list](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_list)
- [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)
- [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link)
- [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink)
- [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az storage account create](/cli/azure/storage/account#az_storage_account_create)
- [az storage container create](/cli/azure/storage/container#az_storage_container_create)
- [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas)
- [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create)
- [az webapp create](/cli/azure/webapp#az_webapp_create)

## <a name="next-steps"></a>다음 단계

[Azure Monitor CLI 샘플](../cli-samples.md)

[성능 문제 찾기 및 진단](../app/tutorial-performance.md)

[애플리케이션 상태에 대한 모니터링 및 경고](../app/tutorial-alert.md)
