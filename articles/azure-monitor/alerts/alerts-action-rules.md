---
title: Azure Monitor 경고에 대 한 경고 처리 규칙
description: Azure Monitor에서 경고 처리 규칙을 이해 하 고 이러한 규칙을 구성 하 고 관리 하는 방법에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 726aaa6c40d430c86882d0ffd902c2de3360ef75
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133302204"
---
# <a name="alert-processing-rules-preview"></a>경고 처리 규칙 (미리 보기)

<a name="configuring-an-action-rule"></a>
<a name="suppression-of-alerts"></a>

> [!NOTE]
> 경고 처리 규칙의 이전 이름은 **작업 규칙** 입니다.

경고 처리 규칙을 사용 하 여 발생 한 **경고** 에 대 한 처리를 적용할 수 있습니다. 새 경고를 생성 하는 규칙 인 Azure Monitor 경고 규칙에 대해 잘 알고 있을 수 있습니다. 경고 처리 규칙은 서로 다릅니다. 이는 발생 한 경고를 자체적으로 수정 하는 규칙입니다. 경고 처리 규칙을 사용 하 여 발생 한 경고에서 [작업 그룹](./action-groups.md) 을 추가 하거나 제거 (표시 안 함) 할 수 있습니다. 경고 처리 규칙은 단일 리소스에서 전체 구독으로 다양 한 리소스 범위에 적용 될 수 있습니다. 또한 다양 한 필터를 적용 하거나 규칙을 미리 정의 된 일정에 따라 수행할 수 있습니다.

## <a name="what-are-alert-processing-rules-useful-for"></a>경고 처리 규칙은 무엇 인가요?

경고 처리 규칙에 대 한 몇 가지 일반적인 사용 사례는 다음과 같습니다.

### <a name="notification-suppression-during-planned-maintenance"></a>계획 된 유지 관리 중 알림 표시 안 함

대부분의 고객은 일회성 또는 정기적인 일정에 따라 해당 리소스에 대 한 계획 된 유지 관리 시간을 설정 합니다. 계획 된 유지 관리는 가상 머신과 같은 단일 리소스 또는 리소스 그룹의 모든 가상 머신과 같은 여러 리소스를 포함할 수 있습니다. 이러한 고객은 유지 관리 기간 동안 해당 리소스에 대 한 경고 알림 수신을 중지 하도록 선택할 수 있습니다. 
  
다른 고객은 업무 시간 외에 경고 알림을 받을 필요가 없습니다.

경고 규칙 자체를 사용 하지 않도록 설정 하 여 경고 알림을 표시 하지 않을 수 있지만이 방법에는 다음과 같은 몇 가지 제한 사항이 있습니다.
   * 유지 관리 기간이 시작 될 때 관련 경고 규칙을 사용 하지 않도록 설정할 수 있습니다. 유지 관리가 완료 되 면 경고 규칙을 다시 사용 하도록 설정할 수 있습니다. 그러나이 방법은 경고 규칙의 범위가 유지 관리 중인 리소스의 범위와 정확히 일치 하는 경우에만 유용 합니다. 예를 들어 단일 경고 규칙은 여러 리소스를 포함할 수 있지만 해당 리소스 중 하나만 유지 관리를 진행 합니다. 따라서 경고 규칙을 사용 하지 않도록 설정 하면 해당 규칙이 적용 되는 나머지 리소스에 대해 유효한 경고가 발생 하지 않습니다.
   * 리소스를 포함 하는 많은 경고 규칙이 있을 수 있습니다. 이러한 모든 항목을 업데이트 하는 것은 시간이 오래 걸리고 오류가 발생 하기 쉽습니다.
   * 일부 경고는 경고 규칙에 의해 생성 되지 않을 수 있습니다.
이러한 모든 경우에 경고 처리 규칙은 알림 억제 목표를 달성 하는 쉬운 방법을 제공 합니다.

### <a name="management-at-scale"></a>대규모 관리

대부분의 고객은 경고 규칙에서 반복적으로 사용 되는 몇 가지 작업 그룹을 정의 하는 경향이 있습니다. 예를 들어 높은 심각도 경고가 발생 될 때마다 특정 작업 그룹을 호출 하는 것이 좋습니다. 경고 규칙의 수가 증가 함에 따라 각 경고 규칙에 올바른 작업 그룹 집합이 있는지 수동으로 확인 하는 것이 더 어려워집니다. 

경고 처리 규칙을 사용 하면 모든 경고 규칙에서 일관 되 게 설정 하는 대신 단일 규칙에서 해당 논리를 지정할 수 있습니다. 또한 경고 규칙에 의해 생성 되지 않은 경고 유형도 다룹니다.

### <a name="add-action-groups-to-all-alert-types"></a>모든 경고 유형에 작업 그룹 추가

경고 규칙을 사용 하면 경고가 발생 하는 경우 트리거할 작업 그룹을 선택할 수 있습니다. Azure Monitor 그러나 모든 Azure 경고 소스에서 작업 그룹을 지정할 수 있는 것은 아닙니다. 이러한 경고의 예로는 [Azure Backup 경고](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview.md), [VM Insights 게스트 상태 경고](/azure/azure-monitor/vm/vminsights-health-alerts#configure-notifications.md), [Azure Stack Edge](/azure/databox-online/azure-stack-edge-gpu-manage-device-event-alert-notifications.md)및 Azure Stack 허브가 있습니다.

이러한 경고 유형에 대해 경고 처리 규칙을 사용 하 여 작업 그룹을 추가할 수 있습니다.

> [!NOTE]
> 경고 처리 규칙은 [Azure Service Health](/azure/service-health/service-health-overview.md) 경고에 영향을 주지 않습니다.

## <a name="alert-processing-rule-properties"></a>경고 처리 규칙 속성
<a name="filter-criteria"></a>

경고 처리 규칙 정의는 다음과 같은 몇 가지 측면을 다룹니다.

### <a name="which-fired-alerts-are-affected-by-this-rule"></a>이 규칙의 영향을 받는 경고가 발생 한 경우 

각 경고 처리 규칙에는 **범위가** 있습니다. 범위는 하나 이상의 특정 Azure 리소스 또는 특정 리소스 그룹 또는 전체 구독 목록입니다. 경고 처리 규칙은 해당 범위 내의 리소스에 대해 발생 하는 경고에 적용 됩니다.  

**필터** 를 정의 하 여 영향을 받는 특정 경고 하위 집합의 범위를 좁힐 수도 있습니다. 사용 가능한 필터는 다음과 같습니다.  

* **경고 컨텍스트 (페이로드)** -경고의 [경고 컨텍스트](./alerts-common-schema-definitions.md#alert-context) 섹션 내에서 필터의 문자열을 포함 하는 경고에만 규칙이 적용 됩니다. 이 섹션에는 각 경고 유형에 특정 한 필드가 포함 됩니다.
* **경고 규칙 id** -규칙은 특정 경고 규칙의 경고에만 적용 됩니다. 값은 전체 리소스 ID (예: "/subscriptions/SUB1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/MY-API-LATENCY") 여야 합니다.  
포털에서 특정 경고 규칙을 열고 "속성"을 클릭 한 다음 "리소스 ID" 값을 복사 하 여 경고 규칙 ID를 찾을 수 있습니다. CLI/PowerShell에서 경고 규칙을 나열 하 여 찾을 수도 있습니다.
* **경고 규칙 이름** -이 경고 규칙 이름을 사용 하는 경고에만 규칙이 적용 됩니다. "Contains" 연산자에도 유용할 수 있습니다.
* **설명** -규칙은 경고 규칙 설명 필드에 지정 된 문자열을 포함 하는 경고에만 적용 됩니다.
* **모니터 조건** -이 규칙은 지정 된 모니터 조건이 "발생" 또는 "해결 됨" 인 경고에만 적용 됩니다.
*  **서비스 모니터링** -지정 된 모니터 서비스의 경고에만 규칙이 적용 됩니다.  
예를 들어 "Platform"을 사용 하 여 규칙을 메트릭 경고에만 적용 합니다.
* **리소스** -지정 된 Azure 리소스의 경고에만 규칙이 적용 됩니다.  
이 필터는 "같지 않음" 연산자와 함께 사용 하거나 "Contains"/"포함 하지 않음" 연산자와 함께 사용 하면 유용 합니다.
* **리소스 그룹** -지정 된 리소스 그룹의 경고에만 규칙이 적용 됩니다.  
이 필터는 "같지 않음" 연산자와 함께 사용 하거나 "Contains"/"포함 하지 않음" 연산자와 함께 사용 하면 유용 합니다.
* **리소스 종류** -지정 된 리소스 유형의 리소스 (예: 가상 머신)에 대 한 경고에만 규칙이 적용 됩니다.
* **심각도** -규칙이 선택한 심각도의 경고에만 적용 됩니다.  

규칙에 여러 필터를 정의 하는 경우 모든 필터가 적용 됩니다. 예를 들어 **리소스 유형 = "Virtual Machines"** 및 **심각도 = "Sev0"** 로 설정 하는 경우 해당 규칙은 범위의 가상 컴퓨터에 대 한 Sev0 경고에만 적용 됩니다.

> [!NOTE]
> 각 필터에는 최대 5개의 값이 포함될 수 있습니다.  

### <a name="what-should-this-rule-do"></a>이 규칙의 용도

다음 작업 중 하나를 선택합니다.

* **표시 안 함**  
이 작업을 수행 하면 영향을 받는 경고에서 모든 작업 그룹이 제거 됩니다. 따라서 발생 한 경고는 유지 관리 기간이 끝날 때 뿐만 아니라 해당 작업 그룹을 호출 하지 않습니다. 이러한 발생 된 경고는 포털, Azure 리소스 Graph, API, PowerShell 등에서 경고를 나열할 때 계속 표시 됩니다. 비 표시 오류 (suppression) 작업은 "작업 그룹 적용" 작업 보다 우선 순위가 높습니다. 단일 발생 한 경고가 두 유형의 서로 다른 경고 처리 규칙의 영향을 받는 경우 해당 경고의 작업 그룹이 표시 되지 않습니다.

* **작업 그룹 적용**  
이 작업을 수행 하면 영향을 받는 경고에 하나 이상의 작업 그룹이 추가 됩니다.

### <a name="when-should-this-rule-apply"></a>이 규칙이 적용 되는 시기

규칙이 적용 되는 시기를 선택적으로 제어할 수도 있습니다. 기본적으로 규칙은 사용 하도록 설정 되어 있는 동안 무조건 적용 됩니다. 그러나이 규칙을 적용 하기 위해 일회용 창을 선택 하거나 주간 되풀이와 같은 되풀이 기간을 선택할 수 있습니다. 

## <a name="configuring-an-alert-processing-rule"></a>경고 처리 규칙 구성

### <a name="portal"></a>[포털](#tab/portal)

Azure Monitor의 **경고** 홈 페이지로 이동 하 여 경고 처리 규칙에 액세스할 수 있습니다.  
이 작업을 완료 한 후에는 **경고 처리 규칙 (미리 보기)** 을 클릭 하 여 기존 규칙을 보고 관리 하거나,  -->  **경고 처리 규칙 만들기 (미리 보기)** 를 클릭 하 여 새 경고 처리 규칙 마법사를 열 수 있습니다.

![Azure Monitor 방문 페이지에서 경고 처리 규칙에 액세스 합니다.](media/alerts-action-rules/action-rules-alerts-landing-page.png)

새 경고 처리 규칙 마법사를 검토할 수 있습니다.  
첫 번째 탭 (**범위**)에서이 규칙이 적용 되는 발생 한 경고를 선택 합니다. 경고를 포함 하는 리소스의 **범위** 선택-여러 리소스 및 리소스 그룹 또는 전체 구독을 선택할 수 있습니다. 필요에 따라 위에 설명 된 대로 **필터** 를 추가할 수도 있습니다.

![경고 처리 규칙 마법사-범위 탭](media/alerts-action-rules/action-rules-wizard-scope-tab.png)

두 번째 탭 (**규칙 설정**)에서 영향을 받는 경고에 적용할 작업을 선택 합니다. **비** 표시 안 함 또는 **작업 그룹 적용** 을 선택 합니다. 작업 적용 그룹을 선택 하는 경우 **작업 그룹 추가** 를 클릭 하 여 기존 작업 그룹을 선택 하거나 새 작업 그룹을 만들 수 있습니다.

![경고 처리 규칙 마법사-규칙 설정 탭](media/alerts-action-rules/action-rules-wizard-rule-settings-tab.png)

세 번째 탭 (**일정**)에서 규칙에 대 한 선택적 일정을 선택 합니다. 기본적으로 규칙은 사용 하지 않도록 설정 되어 있는 동안 항상 작동 합니다. 그러나 **특정 시간에** 작동 하도록 설정 하거나 **되풀이 일정을 설정할** 수 있습니다.  
일 대 일 밤 계획 된 유지 관리에 대 한 일정의 예를 살펴보겠습니다. 특정 표준 시간대에서 다음 날까지 저녁 후에 시작 됩니다.

![경고 처리 규칙 마법사-일정 탭-일회용 일정.](media/alerts-action-rules/action-rules-wizard-scheduling-tab-once.png)

"업무 시간 외" 사례를 다루는 더 복잡한 일정의 예를 살펴보겠습니다. 되풀이 일정에는 두 개의 되풀이 일정이 있습니다. 즉, 매일 오후부터 아침까지의 되풀이 일정과 토요일과 일요일(1일)을 포함하는 주별 일정이 있습니다.

![경고 처리 규칙 마법사 - 일정 탭 - 되풀이 일정.](media/alerts-action-rules/action-rules-wizard-scheduling-tab-recurring.png)

네 번째 탭(**세부 정보)에서** 이 규칙의 이름을 지정하고, 저장할 위치를 선택하고, 필요에 따라 참조에 대한 설명을 추가합니다. 다섯 번째 탭(**태그)에서** 필요에 따라 규칙에 태그를 추가하고 마지막으로 마지막 탭에서 경고 처리 규칙을 검토하고 만들 수 있습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Azure CLI 최신 미리 보기 버전의 경고 처리 규칙을 활용하도록 업데이트 중입니다. 그때까지 **작업 규칙** 명령에서 기존 CLI 기능을 사용하여 경고 처리 규칙을 만들 수 있습니다. 해당 CLI는 최신 경고 처리 규칙 기능 중 일부를 지원하지 않습니다.

[az monitor action-rule create](/cli/azure/monitor/action-rule#az_monitor_action_rule_create) 명령을 사용하여 Azure CLI 경고 처리 규칙을 만들 수 있습니다.  `az monitor action-rule` 참조는 여러 [Azure Monitor용 Azure CLI 참조](/cli/azure/azure-cli-reference-for-monitor)중 하나일 뿐입니다.

### <a name="prepare-your-environment"></a>환경 준비

1. [Azure CLI 설치](/cli/azure/install-azure-cli)

   원한다면 Azure Cloud Shell을 사용하여 이 문서의 단계를 완료할 수도 있습니다.  Azure Cloud Shell은 브라우저를 통해 사용할 수 있는 대화형 셸 환경입니다.  다음 방법 중 하나를 사용하여 Cloud Shell을 시작합니다.

   - [https://shell.azure.com](https://shell.azure.com)으로 이동하여 Cloud Shell 열기

   - [Azure Portal](https://portal.azure.com)의 오른쪽 위 모서리에 있는 메뉴 모음에서 **Cloud Shell** 단추 선택

1. 로그인합니다.

   CLI를 로컬로 설치한 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 로그인합니다.  터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

    ```azurecli
    az login
    ```

1. `alertsmanagement` 확장 설치

   `az monitor action-rule` 명령은 핵심 Azure CLI의 실험적 확장입니다. 확장 참조에 대한 자세한 내용은 [Azure CLI 확장 사용하기](/cli/azure/azure-cli-extensions-overview?)를 참조하세요.

   ```azurecli
   az extension add --name alertsmanagement
   ```

   다음 경고가 표시될 수 있습니다.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-an-alert-processing-rule-with-the-azure-cli"></a>Azure CLI 사용하여 경고 처리 규칙 만들기

필수 및 선택적 매개 변수에 대한 자세한 내용은 [az monitor action-rule create](/cli/azure/monitor/action-rule#az_monitor_action_rule_create)에 대한 Azure CLI 참조 콘텐츠를 참조하세요.

리소스 그룹에서 알림을 표시하지 않을 경고 처리 규칙을 만듭니다.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

주말마다 구독 내의 모든 VM에서 모든 Sev4 경고에 대한 알림을 표시하지 않을 경고 처리 규칙을 만듭니다.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="managing-alert-processing-rules"></a>경고 처리 규칙 관리

### <a name="portal"></a>[포털](#tab/portal)

목록 보기에서 경고 처리 규칙을 보고 관리할 수 있습니다.

![포털의 경고 처리 규칙 목록입니다.](media/alerts-action-rules/action-rules-new-list-view.png)

여기에서 옆에 있는 확인란을 선택하여 대규모로 경고 처리 규칙을 사용하거나 사용하지 않도록 설정하거나 삭제할 수 있습니다. 경고 처리 규칙을 클릭하면 편집용으로 열립니다. 네 번째 탭(**세부 정보)에서** 규칙을 사용하거나 사용하지 않도록 설정할 수 있습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Azure CLI 최신 미리 보기 버전의 경고 처리 규칙을 활용하도록 업데이트 중입니다. 그때까지 **작업 규칙** 명령에서 기존 CLI capabilies를 사용하여 경고 처리 규칙을 만들 수 있습니다. 해당 CLI는 최신 경고 처리 규칙 기능 중 일부를 지원하지 않습니다.

Azure CLI [az monitor action-rule](/cli/azure/monitor) 명령을 사용하여 경고 처리 규칙을 보고 관리할 수 있습니다.

Azure CLI 사용하여 경고 처리 규칙을 관리하기 전에 경고 처리 규칙 [구성에](#configuring-an-alert-processing-rule)제공된 지침을 사용하여 환경을 준비합니다.

```azurecli
# List all alert processing rules for a subscription
az monitor action-rule list

# Get details of an alert processing rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an alert processing rule
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an alert processing rule
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="next-steps"></a>다음 단계

- [Azure의 경고에 대해 자세히 알아보기](./alerts-overview.md)
