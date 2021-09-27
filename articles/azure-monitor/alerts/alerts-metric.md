---
title: Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리
description: Azure Portal 또는 CLI를 사용하여 메트릭 경고 규칙을 만들고, 보고, 관리하는 방법을 알아봅니다.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 7a76b4b37760e5e320ab62c2660e97c9fcabdbb0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815694"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리

Azure Monitor의 메트릭 경고를 사용하면 메트릭 중 하나가 임계값을 초과할 경우 알림을 받을 수 있습니다. 메트릭 경고는 다양한 다차원 플랫폼 메트릭, 사용자 지정 메트릭, Application Insights 표준 및 사용자 지정 메트릭에서 작동합니다. 이 문서에서는 Azure Portal 및 Azure CLI를 통해 메트릭 경고 규칙을 만들고, 보고, 관리하는 방법을 설명합니다. 또한 Azure Resource Manager 템플릿을 사용하여 메트릭 경고 규칙을 만들 수 있으며, 이 방법은 [별도의 문서](./alerts-metric-create-templates.md)에 설명되어 있습니다.

메트릭 경고의 작동 원리는 [메트릭 경고 개요](./alerts-metric-overview.md)에서 자세히 알아볼 수 있습니다.

## <a name="create-with-azure-portal"></a>Azure Portal을 사용하여 만들기

다음은 Azure Portal에서 메트릭 경고 규칙을 만드는 절차입니다.

1. [Azure Portal](https://portal.azure.com)에서 **모니터** 를 클릭합니다. [모니터] 블레이드는 모든 모니터링 설정 및 데이터를 하나의 뷰에 통합합니다.

2. **경고** 를 클릭한 다음, **+ 새로운 경고 규칙** 을 클릭합니다.

    > [!TIP]
    > 대부분의 리소스 블레이드도 **모니터링** 아래의 리소스 메뉴에 **경고** 가 있으며, 여기서 경고를 만들 수 있습니다.

3. **대상 선택** 을 클릭하고, 로드되는 컨텍스트 창에서 경고를 사용할 대상 리소스를 선택합니다. **구독** 및 **리소스 종류** 드롭다운을 사용하여 모니터링하려는 리소스를 찾습니다. 검색 창을 사용하여 리소스를 검색할 수도 있습니다.

4. 선택한 리소스에 경고를 만들 수 있는 메트릭이 있으면 오른쪽 아래의 **사용 가능한 신호** 에 메트릭이 포함됩니다. 이 [문서](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)의 메트릭 경고에 지원되는 리소스 종류의 전체 목록을 볼 수 있습니다.

5. 대상 리소스를 선택한 후에는 **조건 추가** 를 클릭합니다.

6. 리소스에 지원되는 신호 목록이 표시되면 경고를 만들 메트릭을 선택합니다.

7. 지난 6시간의 메트릭에 대한 차트가 표시됩니다. **차트 기간** 드롭다운을 사용하여 더 긴 메트릭 기록을 표시하도록 선택합니다.

8. 메트릭에 차원이 포함된 경우 차원 테이블이 표시됩니다. 차원당 하나 이상의 값을 선택하세요.
    - 표시되는 차원 값은 과거의 메트릭 데이터를 기준으로 합니다.
    - 찾고 있는 차원 값이 표시되지 않으면 "사용자 지정 값 추가"를 클릭하여 사용자 지정 차원 값을 추가합니다.
    - 모든 차원에 대한 **현재 및 미래 값을 모두 선택** 할 수도 있습니다. 이 경우 선택 항목이 동적으로 차원의 모든 현재 및 미래 값으로 조정됩니다.

    메트릭 경고 규칙은 선택된 모든 값 조합에 대한 조건 평가를 실행합니다. [다차원 메트릭에 대한 경고의 작동 방식에 대해 자세히 알아보세요](./alerts-metric-overview.md).
    
    > [!NOTE]
    > "모두"를 차원 값으로 사용하는 것은 "모든 현재 및 미래 값"을 선택하는 것과 같습니다.

9. **임계값** 유형, **연산자** 및 **집계 형식** 을 선택합니다. 이는 메트릭 경고 규칙이 평가할 논리를 결정합니다.
    - **정적** 임계값을 사용하는 경우 **임계값** 을 계속 정의합니다. 메트릭 차트는 적절한 임계값을 결정하는 데 도움이 될 수 있습니다.
    - **동적** 임계값을 사용하는 경우 **임계값 민감도** 를 계속 정의합니다. 최근 데이터에 기반하여 계산된 임계값이 메트릭 차트에 표시됩니다. [동적 임계값 조건 형식 및 민감도 옵션에 대해 자세히 알아보세요](../alerts/alerts-dynamic-thresholds.md).

10. 필요에 따라 **집계 세분성** 및 **평가 빈도** 를 조정하여 조건을 구체화합니다. 

11. **Done** 을 클릭합니다.

12. 필요에 따라 복잡한 경고 규칙을 모니터링하려면 다른 조건을 추가합니다. 현재 사용자가 동적 임계값을 단일 조건으로 적용하는 경고 규칙을 사용할 수 있습니다.

13. **경고 규칙 이름**, **설명** 및 **심각도** 와 같은 **경고 세부 정보** 를 입력합니다.

14. 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어서 경고에 작업 그룹을 추가합니다.

15. **완료** 를 클릭하여 메트릭 경고 규칙을 저장합니다.


## <a name="view-and-manage-with-azure-portal"></a>Azure Portal을 사용하여 보기 및 관리

[경고] 아래에서 [규칙 관리] 블레이드를 사용하여 메트릭 경고 규칙을 살펴보고 관리할 수 있습니다. 아래 절차는 메트릭 경고 규칙을 살펴보고 편집하는 방법을 보여줍니다.

1. Azure Portal에서 **모니터** 로 이동합니다.

2. **경고** 를 클릭하고 **규칙 관리** 를 클릭합니다.

3. **규칙 관리** 블레이드에서 구독의 모든 경고 규칙을 볼 수 있습니다. **리소스 그룹**, **리소스 종류**, **리소스** 를 사용하여 규칙을 추가로 필터링할 수 있습니다. 메트릭 경고만 보려면 **신호 유형** 을 메트릭으로 선택합니다.

    > [!TIP]
    > **규칙 관리** 블레이드에서 여러 경고 규칙을 선택하고 활성화/비활성화할 수 있습니다. 이 기능은 특정 대상 리소스를 유지 관리해야 하는 경우에 유용하게 사용할 수 있습니다.

4. 편집하려는 메트릭 경고 규칙의 이름을 클릭합니다.

5. [규칙 편집]에서 편집하려는 **경고 조건** 을 클릭합니다. 메트릭, 임계값 조건 및 기타 필드를 필요한 대로 변경할 수 있습니다.

    > [!NOTE]
    > 메트릭 경고 규칙이 만들어진 후에는 **경고 규칙 이름** 을 편집할 수 없습니다.

6. **완료** 를 클릭하여 편집 내용을 저장합니다.


## <a name="with-azure-cli"></a>Azure CLI 사용

이전 섹션에서는 Azure Portal을 사용하여 메트릭 경고 규칙을 만들고, 보고, 관리하는 방법을 설명했습니다. 이번 섹션에서는 플랫폼 간 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 동일한 작업을 수행하는 방법을 설명합니다. Azure CLI를 가장 빠르게 사용하는 방법은 [Azure Cloud Shell](../../cloud-shell/overview.md)입니다. 이 문서에서는 Cloud shell을 사용하겠습니다.

1. Azure Portal로 이동하여 **Cloud Shell** 을 클릭합니다.

2. 프롬프트에서 ``--help`` 옵션이 있는 명령을 사용하여 명령 및 사용 방법에 대해 자세히 알아볼 수 있습니다. 예를 들어 다음 명령은 메트릭 경고를 만들고, 살펴보고, 관리하는 데 사용할 수 있는 명령 목록을 보여줍니다.

    ```azurecli
    az monitor metrics alert --help
    ```

3. VM의 평균 백분율 CPU가 90을 초과하는지 모니터링하는 간단한 메트릭 경고 규칙을 만들 수 있습니다.

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. 다음 명령을 사용하여 리소스 그룹의 모든 메트릭 경고를 볼 수 있습니다.

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. 규칙의 이름 또는 리소스 ID를 사용하여 특정 메트릭 경고 규칙의 세부 정보를 볼 수 있습니다.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. 다음 명령을 사용하여 메트릭 경고 규칙을 비활성화할 수 있습니다.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. 다음 명령을 사용하여 메트릭 경고 규칙을 삭제할 수 있습니다.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>PowerShell 사용

메트릭 경고 규칙에서는 다음과 같은 전용 PowerShell cmdlet을 사용할 수 있습니다.

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2): 새 메트릭 경고 규칙을 만들거나 기존 규칙을 업데이트합니다.
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2): 하나 이상의 메트릭 경고 규칙을 가져옵니다.
- [Remove-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2): 메트릭 경고 규칙을 삭제합니다.

## <a name="with-rest-api"></a>REST API 사용

- [만들기 또는 업데이트](/rest/api/monitor/metricalerts/createorupdate): 새 메트릭 경고 규칙을 만들거나 기존 규칙을 업데이트합니다.
- [가져오기](/rest/api/monitor/metricalerts/get): 특정 메트릭 경고 규칙을 가져옵니다.
- [리소스 그룹별 목록](/rest/api/monitor/metricalerts/listbyresourcegroup): 특정 리소스 그룹에서 메트릭 경고 규칙의 목록을 가져옵니다.
- [구독별 목록](/rest/api/monitor/metricalerts/listbysubscription): 특정 구독에서 메트릭 경고 규칙의 목록을 가져옵니다.
- [업데이트](/rest/api/monitor/metricalerts/update): 메트릭 경고 규칙을 업데이트합니다.
- [삭제](/rest/api/monitor/metricalerts/delete): 메트릭 경고 규칙을 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 템플릿을 사용하여 메트릭 경고 만들기](./alerts-metric-create-templates.md).
- [메트릭 경고의 작동 원리 이해](./alerts-metric-overview.md).
- [동적 임계값 조건을 사용하는 메트릭 경고의 작동 원리 이해](../alerts/alerts-dynamic-thresholds.md).
- [메트릭 경고에 대한 웹후크 스키마를 이해합니다](./alerts-metric-near-real-time.md#payload-schema).
- [메트릭 경고 문제 해결](./alerts-troubleshoot-metric.md)
