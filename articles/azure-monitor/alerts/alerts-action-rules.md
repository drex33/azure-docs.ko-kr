---
title: Azure Monitor 경고에 대한 경고 처리 규칙
description: Azure Monitor 경고 처리 규칙 및 이를 구성하고 관리하는 방법을 이해합니다.
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: beaeb8158259a03272a58d3efa20bd8b784a5a1b
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936072"
---
# <a name="alert-processing-rules-preview"></a>경고 처리 규칙(미리 보기)

<a name="configuring-an-action-rule"></a>
<a name="suppression-of-alerts"></a>

> [!NOTE]
> 경고 처리 규칙의 이전 이름은 **작업 규칙** 입니다.

경고 처리 규칙을 사용하면 **발생된 경고에** 대한 처리를 적용할 수 있습니다. 새 경고를 생성하는 규칙인 Azure Monitor 경고 규칙에 익숙할 수 있습니다. 경고 처리 규칙은 다릅니다. 발생된 경고가 발생될 때 자체 경고를 수정하는 규칙입니다. 경고 처리 규칙을 사용하여 [작업 그룹을](./action-groups.md) 추가하거나 실행된 경고에서 작업 그룹을 제거(표시 안 함)할 수 있습니다. 경고 처리 규칙은 단일 리소스에서 전체 구독에 이르기까지 다양한 리소스 범위에 적용할 수 있습니다. 또한 다양한 필터를 적용하거나 규칙이 미리 정의된 일정에 따라 작동하도록 할 수 있습니다.

## <a name="what-are-alert-processing-rules-useful-for"></a>경고 처리 규칙이 유용한 것은 무엇인가요?

경고 처리 규칙에 대한 몇 가지 일반적인 사용 사례는 다음과 같습니다.

### <a name="notification-suppression-during-planned-maintenance"></a>계획된 유지 관리 중 알림 표시 안 함

많은 고객이 일회성 또는 정기적인 일정으로 리소스에 대한 계획된 유지 관리 시간을 설정합니다. 계획된 유지 관리는 가상 머신과 같은 단일 리소스 또는 리소스 그룹의 모든 가상 머신과 같은 여러 리소스를 다룰 수 있습니다. 이러한 고객은 유지 관리 기간 동안 해당 리소스에 대한 경고 알림 수신을 중지하도록 선택할 수 있습니다. 
  
다른 고객은 업무 시간 외에는 경고 알림을 받을 필요가 없습니다.

경고 규칙 자체를 사용하지 않도록 하여 경고 알림을 표시하지 않을 수 있지만 이 방법은 몇 가지 제한 사항입니다.
   * 유지 관리 기간을 시작할 때 관련 경고 규칙을 사용하지 않도록 설정할 수 있습니다. 유지 관리가 끝나면 경고 규칙을 다시 사용하도록 설정할 수 있습니다. 그러나 이 방법은 경고 규칙의 범위가 유지 관리 중인 리소스의 범위인 경우에만 실용적입니다. 예를 들어 단일 경고 규칙은 여러 리소스를 처리할 수 있지만 해당 리소스 중 하나만 유지 관리를 진행합니다. 따라서 경고 규칙을 사용하지 않도록 설정하면 해당 규칙이 적용되는 나머지 리소스에 대한 유효한 경고가 누락됩니다.
   * 리소스를 다루는 많은 경고 규칙이 있을 수 있습니다. 모든 업데이트는 시간이 오래 걸리고 오류가 발생하기 쉽습니다.
   * 경고 규칙에 의해 생성되지 않은 일부 경고가 있을 수 있습니다.
이러한 모든 경우에 경고 처리 규칙은 알림 표시 안 함 목표를 달성하는 쉬운 방법을 제공합니다.

### <a name="management-at-scale"></a>대규모 관리

대부분의 고객은 경고 규칙에서 반복적으로 사용되는 몇 가지 작업 그룹을 정의하는 경향이 있습니다. 예를 들어 심각도가 높은 경고가 발생할 때마다 특정 작업 그룹을 호출할 수 있습니다. 경고 규칙 수가 증가함에 따라 각 경고 규칙에 올바른 작업 그룹 집합이 있는지 수동으로 확인하는 것이 점점 더 어려워집니다. 

경고 처리 규칙을 사용하면 모든 경고 규칙에서 일관되게 설정하는 대신 단일 규칙에서 해당 논리를 지정할 수 있습니다. 또한 경고 규칙에 의해 생성되지 않는 경고 유형도 다룹니다.

### <a name="add-action-groups-to-all-alert-types"></a>모든 경고 유형에 작업 그룹 추가

Azure Monitor 경고 규칙을 사용하면 경고가 발생할 때 트리거될 작업 그룹을 선택할 수 있습니다. 그러나 모든 Azure 경고 원본에서 작업 그룹을 지정할 수 있는 것은 아닙니다. 이러한 경고의 [예로는 Azure Backup 경고,](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview.md) [VM Insights 게스트 상태 경고,](/azure/azure-monitor/vm/vminsights-health-alerts#configure-notifications.md) [Azure Stack Edge](/azure/databox-online/azure-stack-edge-gpu-manage-device-event-alert-notifications.md)및 Azure Stack Hub 있습니다.

이러한 경고 유형의 경우 경고 처리 규칙을 사용하여 작업 그룹을 추가할 수 있습니다.

> [!NOTE]
> 경고 처리 규칙은 [Azure Service Health](/azure/service-health/service-health-overview.md) 경고에 영향을 미치지 않습니다.

## <a name="alert-processing-rule-properties"></a>경고 처리 규칙 속성
<a name="filter-criteria"></a>

경고 처리 규칙 정의는 다음과 같은 몇 가지 측면을 다룹니다.

### <a name="which-fired-alerts-are-affected-by-this-rule"></a>이 규칙의 영향을 받는 발생한 경고는 무엇인가요? 

각 경고 처리 규칙에는 **범위가** 있습니다. 범위는 하나 이상의 특정 Azure 리소스, 특정 리소스 그룹 또는 전체 구독의 목록입니다. 경고 처리 규칙은 해당 범위 내의 리소스에서 발생된 경고에 적용됩니다.  

**필터를** 정의하여 영향을 받는 특정 경고 하위 집합의 범위를 좁힐 수도 있습니다. 사용 가능한 필터는 다음과 같습니다.  

* **경고 컨텍스트(페이로드)** - 규칙은 경고의 경고 컨텍스트 섹션 내에 필터의 문자열이 포함된 [경고에만](./alerts-common-schema-definitions.md#alert-context) 적용됩니다. 이 섹션에는 각 경고 유형에 특정한 필드가 포함되어 있습니다.
* **경고 규칙 ID** - 규칙은 특정 경고 규칙의 경고에만 적용됩니다. 값은 전체 리소스 ID여야 합니다(예: "/subscriptions/SUB1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/MY-API-LATENCY").  
포털에서 특정 경고 규칙을 열고 "속성"을 클릭한 다음 "리소스 ID" 값을 복사하여 경고 규칙 ID를 찾을 수 있습니다. CLI/PowerShell에서 경고 규칙을 나열하여 찾을 수도 있습니다.
* **경고 규칙 이름** - 이 경고 규칙 이름을 가진 경고에만 규칙이 적용됩니다. "Contains" 연산자에서도 유용할 수 있습니다.
* **설명** - 규칙은 경고 규칙 설명 필드 내에 지정된 문자열을 포함하는 경고에만 적용됩니다.
* **모니터 조건** - 규칙은 지정된 모니터 조건("발생됨" 또는 "해결됨")이 있는 경고에만 적용됩니다.
*  **모니터 서비스** - 규칙은 지정된 모니터 서비스의 경고에만 적용됩니다.  
예를 들어 "플랫폼"을 사용하여 메트릭 경고에만 규칙을 적용합니다.
* **리소스** - 규칙은 지정된 Azure 리소스의 경고에만 적용됩니다.  
이 필터는 "같지 않음" 연산자 또는 "Contains" / "포함 안 함" 연산자에서 유용합니다.
* **리소스 그룹** - 규칙은 지정된 리소스 그룹의 경고에만 적용됩니다.  
이 필터는 "같지 않음" 연산자 또는 "Contains" / "포함 안 함" 연산자에서 유용합니다.
* **리소스 종류** - 규칙은 가상 머신과 같은 지정된 리소스 유형의 리소스에 대한 경고에만 적용됩니다.
* **심각도** - 규칙은 선택한 심각도의 경고에만 적용됩니다.  

규칙에서 여러 필터를 정의하는 경우 모두 적용됩니다. 예를 들어 리소스 **종류 = "Virtual Machines"** 및 **심각도 = "Sev0"** 을 설정하는 경우 규칙은 범위의 가상 머신에 대한 Sev0 경고에만 적용됩니다.

> [!NOTE]
> 각 필터에는 최대 5개의 값이 포함될 수 있습니다.  

### <a name="what-should-this-rule-do"></a>이 규칙은 무엇을 해야 합니까?

다음 작업 중 하나를 선택합니다.

* **표시 안 함**  
제거는 발생된 경고에서 모든 작업 그룹을 제거합니다. 발생된 경고는 계속 표시되지만 해당 작업 그룹을 호출하지는 않습니다.  
제거 작업은 "작업 그룹 적용" 작업보다 우선 순위가 높습니다. 발생한 단일 경고가 두 유형의 서로 다른 경고 처리 규칙의 영향을 받는 경우 해당 경고의 작업 그룹은 표시되지 않습니다.

* **작업 그룹 적용**  
이 작업은 발생된 경고에 하나 이상의 작업 그룹을 추가합니다.

### <a name="when-should-this-rule-apply"></a>이 규칙은 언제 적용해야 합니까?

규칙이 적용되는 시기를 선택적으로 제어할 수 있습니다. 기본적으로 규칙은 사용하도록 설정된 한 무조건 적용됩니다. 그러나 이 규칙을 적용할 일회성 창을 선택하거나 매주 되풀이와 같은 되풀이 기간을 가질 수 있습니다. 

## <a name="configuring-an-alert-processing-rule"></a>경고 처리 규칙 구성

### <a name="portal"></a>[포털](#tab/portal)

Azure Monitor 경고 홈페이지로 이동하여 **경고** 처리 규칙에 액세스할 수 있습니다.  
그런 다음 경고 **처리 규칙(미리 보기)을** 클릭하여 기존 규칙을 보고 관리하거나 경고 처리 규칙   -->  **만들기(미리 보기)를** 클릭하여 새 경고 처리 규칙 마법사를 열 수 있습니다.

![Azure Monitor 방문 페이지에서 경고 처리 규칙에 액세스합니다.](media/alerts-action-rules/action-rules-alerts-landing-page.png)

새 경고 처리 규칙 마법사를 검토합니다.  
첫 번째 탭(**범위)에서** 이 규칙에서 적용되는 발생한 경고를 선택합니다. 경고가 적용되는 **리소스의 범위를** 선택합니다. 여러 리소스 및 리소스 그룹 또는 전체 구독을 선택할 수 있습니다. 위에서 설명한 대로 필요에 따라 **필터 를** 추가할 수도 있습니다.

![경고 처리 규칙 마법사 - 범위 탭](media/alerts-action-rules/action-rules-wizard-scope-tab.png)

두 번째 탭(**규칙 설정)에서** 영향을 받는 경고에 적용할 작업을 선택합니다. 표시 **안 함** 또는 **작업 그룹 적용** 중에서 선택합니다. 작업 그룹 적용을 선택하는 경우 작업 그룹 **추가를** 클릭하여 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들 수 있습니다.

![경고 처리 규칙 마법사 - 규칙 설정 탭](media/alerts-action-rules/action-rules-wizard-rule-settings-tab.png)

세 번째 **탭(일정 예약)에서** 규칙에 대한 선택적 일정을 선택합니다. 기본적으로 규칙은 사용하지 않도록 설정되지 않은 한 항상 작동합니다. 그러나 **특정 시간에** 작동하도록 설정하거나 되풀이 **일정을 설정할** 수 있습니다.  
일회성, 야간, 계획된 유지 관리 일정의 예를 살펴보겠습니다. 저녁부터 다음 아침까지 특정 표준 시간대에서 시작됩니다.

![경고 처리 규칙 마법사 - 일정 탭 - 일회성 일정.](media/alerts-action-rules/action-rules-wizard-scheduling-tab-once.png)

"업무 외 시간" 사례를 포함 하 여 보다 복잡 한 일정의 예를 살펴보겠습니다. 매일 오전 1 시에 오전 1 시와 토요일 및 일요일 (전체 일)을 포함 하는 주간 일정의 되풀이 일정을 사용 합니다.

![경고 처리 규칙 마법사-일정 탭-되풀이 일정](media/alerts-action-rules/action-rules-wizard-scheduling-tab-recurring.png)

네 번째 탭 (**세부 정보**)에서이 규칙의 이름을 지정 하 고, 저장 위치를 선택 하 고, 필요에 따라 참조에 대 한 설명을 추가 합니다. 다섯 번째 탭 (**태그**)에서 필요에 따라 규칙에 태그를 추가 하 고 마지막 탭에서 경고 처리 규칙을 검토 하 고 만들 수 있습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> 최신 미리 보기 버전의 경고 처리 규칙을 활용 하도록 Azure CLI 업데이트 하는 중입니다. 그때까지 **작업 규칙** 명령에서 기존 CLI 기능을 사용 하 여 경고 처리 규칙을 만들 수 있습니다. 이러한 CLI는 최신 경고 처리 규칙 기능 중 일부를 지원 하지 않습니다.

[Az monitor action-rule create](/cli/azure/monitor/action-rule#az_monitor_action_rule_create) 명령을 사용 하 여 Azure CLI를 사용 하 여 경고 처리 규칙을 만들 수 있습니다.  `az monitor action-rule` 참조는 여러 [Azure Monitor용 Azure CLI 참조](/cli/azure/azure-cli-reference-for-monitor)중 하나일 뿐입니다.

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

### <a name="create-an-alert-processing-rule-with-the-azure-cli"></a>Azure CLI를 사용 하 여 경고 처리 규칙 만들기

필수 및 선택적 매개 변수에 대한 자세한 내용은 [az monitor action-rule create](/cli/azure/monitor/action-rule#az_monitor_action_rule_create)에 대한 Azure CLI 참조 콘텐츠를 참조하세요.

리소스 그룹에서 알림을 표시 하지 않도록 경고 처리 규칙을 만듭니다.

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

주말 마다 구독 내의 모든 Vm에 대 한 모든 Sev4 경고에 대 한 알림을 표시 하지 않도록 경고 처리 규칙을 만듭니다.

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

목록 뷰에서 경고 처리 규칙을 보고 관리할 수 있습니다.

![포털의 경고 처리 규칙 목록입니다.](media/alerts-action-rules/action-rules-new-list-view.png)

여기에서 옆의 확인란을 선택 하 여 크기 조정에서 경고 처리 규칙을 사용 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다. 경고 처리 규칙을 클릭 하면 편집을 위해 열립니다. 네 번째 탭 (**세부 정보**)에서 규칙을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> 최신 미리 보기 버전의 경고 처리 규칙을 활용 하도록 Azure CLI 업데이트 하는 중입니다. 그때까지 **작업 규칙** 명령에서 기존 CLI 크롤러를 사용 하 여 경고 처리 규칙을 만들 수 있습니다. 이러한 CLI는 최신 경고 처리 규칙 기능 중 일부를 지원 하지 않습니다.

Azure CLI에서 [az monitor action-rule](/cli/azure/monitor) 명령을 사용 하 여 경고 처리 규칙을 보고 관리할 수 있습니다.

Azure CLI를 사용 하 여 경고 처리 규칙을 관리 하기 전에 [경고 처리 규칙 구성](#configuring-an-alert-processing-rule)에 제공 된 지침을 사용 하 여 환경을 준비 합니다.

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
