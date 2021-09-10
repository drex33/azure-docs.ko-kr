---
title: Azure CLI에서 메트릭 경고 모니터 만들기
description: Azure CLI 명령을 사용하여 Azure Monitor에서 메트릭 경고를 만드는 방법을 알아봅니다. 이 샘플은 가상 머신과 App Service 플랜에 대한 경고를 만듭니다.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 60d2b7f706c985eaa13c634e0323927eb39446ad
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117853"
---
# <a name="create-metric-alert-monitors-in-azure-cli"></a>Azure CLI에서 메트릭 경고 모니터 만들기

이 샘플은 Azure CLI 명령을 사용하여 Azure Monitor에서 메트릭 경고 모니터를 만듭니다. 첫 번째 샘플은 가상 머신에 대한 경고를 만듭니다. 두 번째 명령은 App Service 플랜에 대한 차원을 포함하는 경고를 만듭니다.  

[!INCLUDE [Prepare your Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-alert"></a>경고 만들기

이 경고는 `ContosoVMRG`라는 리소스 그룹에 있는 가상 머신(이름: `VM07`)을 모니터링합니다.

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만들 수 있습니다. 가상 머신을 만드는 방법에 대한 정보는 [Azure CLI를 사용하여 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-cli.md), [Azure CLI를 사용하여 Linux 가상 머신 만들기](../virtual-machines/linux/quick-create-cli.md) 및 [az vm Create](/cli/azure/vm#az-vm-create) 명령을 참조하세요.

```azurecli
# resource group name: ContosoVMRG
# virtual machine name: VM07

# Create scope
scope=$(az vm show --resource-group ContosoVMRG --name VM07 --output tsv --query id)

# Create action
action=$(az monitor action-group create --name ContosoWebhookAction \
  --resource-group ContosoVMRG --output tsv --query id \
  --action webhook https://alerts.contoso.com usecommonalertschema)

# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
  --metric "Percentage CPU" --op GreaterThan --type static --threshold 90 --output tsv)

# Create metrics alert
az monitor metrics alert create --name alert-01 --resource-group ContosoVMRG \
  --scopes $scope --action $action --condition $condition --description "Test High CPU"
```

이 샘플에서는 인용 부호와 같은 원치 않는 기호를 포함하지 않는 `tsv` 출력 형식을 사용합니다. 자세한 내용은 [효과적인 Azure CLI 사용](/cli/azure/use-cli-effectively)을 참조하세요.

## <a name="create-an-alert-with-a-dimension"></a>차원을 사용하여 경고 만들기

이 샘플에서는 App Service 플랜을 만든 다음, 해당 플랜에 대한 메트릭 경고를 만듭니다. 이 예제에서는 차원을 사용하여 App Service 플랜의 모든 인스턴스가 이 메트릭에 해당함을 지정합니다. 샘플에서는 리소스 그룹 및 애플리케이션 서비스 플랜을 만듭니다.

```azurecli
# Create resource group
az group create --name ContosoRG --location eastus2
 
# Create application service plan
az appservice plan create --resource-group ContosoRG --name ContosoAppServicePlan \
   --is-linux --number-of-workers 4 --sku S1 
 
# Create scope
scope=$(az appservice plan show --resource-group ContosoRG --name ContosoAppServicePlan \
   --output tsv --query id) 
 
# Create dimension
dim01=$(az monitor metrics alert dimension create --name Instance --value * --op Include --output tsv)
 
# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
   --metric CpuPercentage --op GreaterThan --type static --threshold 90 \
   --dimension $dim01 --output tsv)
```

가능한 메트릭의 목록을 보려면 [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) 명령을 실행합니다. `--output` 매개 변수는 읽을 수 있는 형식으로 값을 표시합니다.


```azurecli
az monitor metrics list-definitions --resource $scope --output table 
 
# Create metrics alert
az monitor metrics alert create --name alert-02 --resource-group ContosoRG \
   --scopes $scope --condition $condition --description "Service Plan High CPU"
```

## <a name="clean-up-deployment"></a>배포 정리

이 명령을 테스트하기 위해 리소스 그룹을 만들었다면 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 해당 리소스 그룹과 관련 콘텐츠를 모두 제거할 수 있습니다.

```azurecli
az group delete --name ContosoVMRG

az group delete --name ContosoRG
```

그대로 유지하려는 기존 리소스 그룹을 사용했다면 [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete) 명령을 사용하여 연습 경고를 삭제합니다.

```azurecli
az monitor metrics alert delete --name alert-01

az monitor metrics alert delete --name alert-02
```

## <a name="azure-cli-commands-used-in-this-article"></a>이 문서에서 사용되는 Azure CLI 명령

이 문서에서는 다음 Azure CLI 명령을 사용합니다.

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az appservice plan show](/cli/azure/appservice/plan#az_appservice_plan_show)
- [az group create](/cli/azure/group#az-group-create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor action-group create](/cli/azure/monitor/action-group#az_monitor_action_group_create)
- [az monitor metrics alert condition create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-condition-create)
- [az monitor metrics alert create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-create)
- [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete)
- [az monitor metrics alert dimension create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-dimension-create)
- [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>다음 단계

- [Azure Monitor CLI 샘플](cli-samples.md)
- [Azure Monitor에서 메트릭 경고가 작동하는 방식 이해](alerts/alerts-metric-overview.md)
