---
title: Azure Monitor의 로그 쿼리
description: Azure Monitor에서 사용되는 Kusto 쿼리 언어에 대한 참조 정보입니다. Azure Monitor와 관련된 추가 요소와 Azure Monitor 로그 쿼리에 지원되지 않는 요소가 포함됩니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 272c146c71e9caf6d7ba6a1ba165a6157f5b6de0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531174"
---
# <a name="log-queries-in-azure-monitor"></a>Azure Monitor의 로그 쿼리
Azure Monitor 로그는 Azure Data Explorer를 기반으로 하며 로그 쿼리는 동일한 KQL(Kusto 쿼리 언어)을 사용하여 작성됩니다. 쉽게 읽고 제작할 수 있도록 설계된 풍부한 언어이므로 몇 가지 기본 지침에 따라 쿼리 작성을 시작할 수 있어야 합니다.

쿼리를 사용할 Azure Monitor 영역은 다음과 같습니다.

- [Log Analytics](../logs/log-analytics-overview.md). 로그 쿼리를 편집하고 대화형으로 결과를 분석하기 위한 Azure Portal의 기본 도구입니다. Azure Monitor의 다른 위치에서 로그 쿼리를 사용하려는 경우에도 일반적으로 로그 쿼리를 최종 위치로 복사하기 전에 Log Analytics에서 작성한 후 테스트합니다.
- [로그 경고 규칙](../alerts/alerts-overview.md). 작업 영역에서 데이터의 문제를 사전에 식별합니다.  각 경고 규칙은 일정한 간격으로 자동 실행되는 로그 쿼리를 기반으로 합니다.  경고를 만들어야 하는지 여부를 결정하도록 결과를 검사합니다.
- [통합 문서](../visualize/workbooks-overview.md). Azure Portal의 대화형 시각적 보고서에서 다양한 시각화를 사용하는 로그 쿼리의 결과가 포함됩니다.
- [Azure 대시보드](../visualize/tutorial-logs-dashboards.md). 쿼리 결과를 Azure 대시보드에 고정하여 로그 및 메트릭 데이터를 함께 시각화하고 다른 Azure 사용자와 선택적으로 공유합니다.
- [Logic Apps](../logs/logicapp-flow-connector.md).  Logic Apps를 사용하여 자동화된 워크플로에서 로그 쿼리의 결과를 사용합니다.
- [PowerShell](/powershell/module/az.operationalinsights/invoke-azoperationalinsightsquery). Invoke-AzOperationalInsightsQuery를 사용하는 Azure Automation Runbook 또는 명령줄에서 PowerShell 스크립트의 로그 쿼리 결과를 사용합니다.
- [Azure Monitor Logs API](https://dev.loganalytics.io). REST API 클라이언트의 작업 영역에서 로그 데이터를 검색합니다.  API 요청에는 검색할 데이터를 확인하기 위해 Azure Monitor에 대해 실행되는 쿼리가 포함됩니다.

## <a name="getting-started"></a>시작
KQL을 사용하여 로그 쿼리를 작성하는 학습을 시작하는 가장 좋은 방법은 사용 가능한 자습서 및 샘플을 활용하는 것입니다.

- [Log Analytics 자습서](./log-analytics-tutorial.md) - Azure Portal에서 쿼리를 편집하고 실행하는 데 사용할 도구인 Log Analytics 기능 사용에 대한 자습서입니다. Log Analytics를 사용하면 쿼리 언어를 직접 사용하지 않고도 간단한 쿼리를 작성할 수 있습니다. 이전에 Log Analytics를 사용해 본 적이 없는 경우 여기서 시작하여 다른 자습서 및 샘플에서 사용할 도구를 이해할 수 있습니다.
- [KQL 자습서](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) - 기본 KQL 개념과 일반 연산자를 단계별로 안내하며, 언어 자체 및 로그 쿼리 구조를 이해하기 시작하기에 가장 적합합니다. 
- [쿼리 예](../logs/queries.md) - Log Analytics에서 사용할 수 있는 쿼리 예에 대해 설명합니다. 쿼리를 수정하지 않고 사용하거나 샘플로 활용하여 KQL를 배울 수 있습니다.
- [쿼리 샘플](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) - 다양한 개념을 보여 주는 샘플 쿼리입니다.



## <a name="reference-documentation"></a>참조 설명서
모든 명령 및 연산자에 대한 참조를 포함한 [KQL 문서](/azure/data-explorer/kusto/query/)는 Azure Data Explorer 문서에서 사용할 수 있습니다. KQL을 능숙하게 사용하더라도 이전에 사용하지 않은 새로운 명령과 시나리오를 조사하기 위해 참조를 계속 사용하는 것이 좋습니다.


## <a name="language-differences"></a>언어 차이
Azure Monitor는 Azure Data Explorer와 동일한 KQL을 사용하지만 몇 가지 차이점이 있습니다. KQL 설명서에서는 Azure Monitor에서 지원하지 않거나 기능이 다른 연산자가 지정됩니다. Azure Monitor 관련 연산자는 Azure Monitor 콘텐츠에 설명되어 있습니다. 다음 섹션에서는 빠른 참조를 위해 언어 버전 간의 차이 목록을 제공합니다.

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 명령문

* [별칭](/azure/kusto/query/aliasstatement)
* [쿼리 매개 변수](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 함수

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 연산자

* [클러스터 간 조인](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 플러그 인

* [Python 플러그인](/azure/kusto/query/pythonplugin)
* [sql_request 플러그 인](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Azure Monitor의 추가 연산자
다음 연산자는 특정 Azure Monitor 기능을 지원하며 Azure Monitor 외에는 사용할 수 없습니다.

* [app()](../logs/app-expression.md)
* [resource()](./resource-expression.md)
* [workspace()](../logs/workspace-expression.md)

## <a name="next-steps"></a>다음 단계
- [쿼리 작성에 대한 자습서](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)를 살펴봅니다.
- [Kusto 쿼리 언어에 대한 전체 참조 문서](/azure/kusto/query/)에 액세스합니다.
