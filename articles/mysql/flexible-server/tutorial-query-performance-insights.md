---
title: '자습서: Azure Database for MySQL - 유연한 서버에 대한 쿼리 성능 Insight'
description: '자습서: Azure Database for MySQL - 유연한 서버에 대한 쿼리 성능 Insight'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: ed78f493021c94c8beeecb8d5470d9a846b19d8d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621501"
---
# <a name="tutorial-query-performance-insights-for-azure-database-for-mysql--flexible-server"></a>자습서: Azure Database for MySQL - 유연한 서버에 대한 쿼리 성능 Insight
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight는 데이터베이스에 대한 인텔리전트 쿼리 분석을 제공하도록 제안합니다. 가장 즐겨찾는 인사이트는 워크로드 패턴과 장기 실행 쿼리입니다. 이렇게 하면 전체 성능을 향상시키고 사용 가능한 리소스를 효율적으로 사용하도록 최적화할 수 있는 쿼리를 찾을 수 있습니다. Query Performance Insight는 다음과 같은 세부 정보를 제공하여 데이터베이스 성능 문제를 해결하는 데 걸리는 시간을 줄일 수 있습니다.
* 상위 N개의 장기 실행 쿼리 및 추세
* 쿼리 텍스트와 실행 기록(최소, 최대, 평균 및 표준 편차 쿼리 시간 포함)을 볼 수 있는 쿼리 세부 정보로 드릴다운하는 기능
* 리소스 사용률(CPU, 메모리 및 스토리지)
 
이 자습서에서는 MySQL 느린 쿼리 로그, Log Analytics 도구 또는 통합 문서 템플릿을 사용하여 Azure Database for MySQL – 유연한 서버에 대한 쿼리 성능 Insight를 시각화하는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항
- Azure Database for MySQL – 유연한 서버의 인스턴스를 만들어야 합니다. 단계별 절차는 [Azure Database for MySQL - 유연한 서버 인스턴스 만들기](./quickstart-create-server-portal.md)를 참조하세요.
- Log Analytics 작업 영역을 만들어야 합니다. 단계별 절차는 [Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.
>[!div class="checklist"]
> * 포털 또는 Azure CLI를 사용하여 느린 쿼리 로그 구성
> * 진단 설정
> * Log Analytics를 사용하여 느린 쿼리 보기 
> * 통합 문서를 사용하여 느린 쿼리 보기 

## <a name="configure-slow-query-logs-from-portal"></a>포털에서 느린 쿼리 로그 구성 


1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버를 선택합니다.

1. 사이드바의 **설정** 섹션에서 **서버 매개 변수** 를 선택합니다.
   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="서버 매개 변수 페이지.":::

1. **slow_query_log** 매개 변수를 **ON** 으로 업데이트 합니다.
   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="느린 쿼리 로그를 활성화합니다.":::

1. 필요한 다른 매개 변수를 변경합니다(예: `long_query_time`, `log_slow_admin_statements`). 더 많은 매개 변수는 [느린 쿼리 로그](./concepts-slow-query-logs.md#configure-slow-query-logging) 문서를 참조하세요.  
   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="느린 쿼리 로그 관련 매개 변수를 업데이트합니다.":::

1. **저장** 을 선택합니다. 
   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="느린 쿼리 로그 매개 변수를 저장합니다.":::

**서버 매개 변수** 페이지에서 페이지를 닫으면 로그 목록으로 돌아갈 수 있습니다.



## <a name="configure-slow-query-logs-from-azure-cli"></a>Azure CLI에서 느린 쿼리 로그 구성
 
Azure CLI를 사용하여 위의 작업을 수행하려는 경우 CLI를 사용하여 서버에 대한 느린 쿼리 로그를 사용하도록 설정하고 구성할 수 있습니다. 

> [!IMPORTANT]
> 서버 성능이 크게 영향을 받지 않도록 감사 목적에 필요한 이벤트 유형과 사용자만 로그하는 것이 좋습니다.

서버에 대한 느린 쿼리 로그를 사용하도록 설정하고 구성합니다.

```azurecli
# Turn on statement level log

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec

# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs



az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>진단 설정

느린 쿼리 로그는 Azure Monitor 진단 설정과 통합되어 로그를 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 파이프할 수 있습니다.

1. 사이드바의 **모니터링** 섹션에서 **진단 설정** > **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="진단 설정 옵션의 스크린샷":::

1. 진단 설정의 이름을 제공합니다.

1. 느린 쿼리 로그를 보낼 대상(Log Analytics 작업 영역, 스토리지 계정 또는 이벤트 허브)을 지정합니다.

    >[!Note]
    > 자습서의 범위에서 느린 쿼리 로그를 Log Analytics 작업 영역에 보내야 합니다.

1. 로그 형식으로 **MySqlSlowLogs** 를 선택합니다.
    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="진단 설정 구성 옵션의 스크린샷":::

1. 느린 쿼리 로그를 전송하도록 데이터 싱크를 구성한 후 **저장** 을 선택합니다.
    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="저장이 강조 표시된 진단 설정 구성 옵션의 스크린샷":::

    >[!Note]
    >진단 설정을 구성하려면 먼저 데이터 싱크(Log Analytics 작업 영역, 스토리지 계정 또는 이벤트 허브)를 만들어야 합니다. 구성한 데이터 싱크(Log Analytics 작업 영역, 스토리지 계정 또는 이벤트 허브)에서 느린 쿼리 로그에 액세스할 수 있습니다. 로그가 표시되는 데 최대 10분이 걸릴 수 있습니다.
 
## <a name="view-query-insights-using-log-analytics"></a>Log Analytics를 사용하여 쿼리 인사이트 보기 

**모니터링** 섹션 아래에서 **로그** 로 이동합니다. **쿼리** 창을 닫습니다.

:::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="로그 분석의 스크린샷":::

실행할 쿼리는 쿼리 창에서 작성할 수 있습니다.  여기서는 쿼리를 사용하여 10초보다 긴 특정 서버에 대한 쿼리를 찾았습니다.

```kusto
 AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
```
    
**시간 범위** 를 선택하고, 쿼리를 **실행** 합니다. 아래와 같은 쿼리 결과가 표시됩니다.  

:::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="느린 쿼리 로그의 스크린샷":::

## <a name="view-query-insights-using-workbooks"></a>통합 문서를 사용하여 쿼리 인사이트 보기 

1.  Azure Portal에서 Azure Database for MySQL – 유연한 서버에 대한 **모니터링** 블레이드로 이동하고, **통합 문서** 를 선택합니다.
2.  템플릿이 표시될 수 있습니다. **쿼리 성능 Insight** 를 선택합니다. 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="통합 문서 템플릿의 스크린샷":::

표시되는 시각화는 다음과 같습니다. 
>[!div class="checklist"]
> * 쿼리 로드
> * 전체 활성 연결
> * 느린 쿼리 추세(10초를 초과하는 쿼리 시간)
> * 느린 쿼리 세부 정보
> * 가장 긴 상위 5개 쿼리 나열
> * 최소, 최대, 평균 및 표준 편차 쿼리 시간을 기준으로 느린 쿼리를 요약합니다.

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="긴 쿼리의 스크린샷":::

>[!Note]
> * 리소스 사용률의 경우 개요 템플릿을 사용할 수 있습니다.
> * 이러한 템플릿은 편집하고 요구 사항에 따라 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure Monitor 통합 문서 개요 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)를 참조하세요.
> * 빠른 보기를 위해 통합 문서 또는 Log Analytics 쿼리를 대시보드에 ping할 수도 있습니다. 자세한 내용은 [Azure Portal에서 대시보드 만들기 - Azure Portal](../../azure-portal/azure-portal-dashboards.md)을 참조하세요. 

잠재적인 병목 상태를 찾는 데 도움이 될 수 있는 Query Performance Insight의 두 가지 메트릭은 기간 및 실행 횟수입니다. 장기 실행 쿼리는 리소스를 더 오래 잠그고, 다른 사용자를 차단하고, 확장성을 제한할 가능성이 가장 높습니다. 일부 경우에 실행 횟수가 높으면 네트워크 왕복이 증가할 수 있습니다. 왕복은 성능에 영향을 줍니다. 네트워크 대기 시간 및 다운스트림 서버 대기 시간이 발생할 수 있습니다. 따라서 실행 횟수는 자주 실행되는("번잡") 쿼리를 찾는 데 도움이 될 수 있습니다. 이러한 쿼리는 최적화에 가장 적합한 후보입니다. 

## <a name="next-steps"></a>다음 단계
- [Azure Monitor 통합 문서를 시작](../../azure-monitor/visualize/workbooks-overview.md#visualizations)하고 통합 문서의 다양한 시각화 옵션에 대해 자세히 알아봅니다.
- [느린 쿼리 로그](concepts-slow-query-logs.md)에 대해 자세히 알아보기


