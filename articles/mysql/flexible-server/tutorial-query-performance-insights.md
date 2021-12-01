---
title: '자습서: Azure Database for MySQL 유연한 서버에 대한 Query Performance Insight'
description: 이 문서에서는 Azure Database for MySQL 유연한 서버에 대한 Query Performance Insight를 시각화하는 데 도움이 되는 도구를 보여 줍니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 8df26a5baf87b7d991a656db0e7a634c133f8758
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407858"
---
# <a name="tutorial-query-performance-insight-for-azure-database-for-mysql-flexible-server"></a>자습서: Azure Database for MySQL 유연한 서버에 대한 Query Performance Insight
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight는 데이터베이스에 대한 인텔리전트 쿼리 분석을 제공하도록 제안합니다. 가장 즐겨찾는 인사이트는 워크로드 패턴과 장기 실행 쿼리입니다. 이러한 인사이트를 이해하면 전체 성능을 향상시키고 사용 가능한 리소스를 효율적으로 사용할 수 있도록 최적화할 쿼리를 찾는 데 도움이 될 수 있습니다. 

Query Performance Insight는 다음과 같은 정보를 제공하여 데이터베이스 성능 문제를 해결하는 데 걸리는 시간을 줄이는 데 도움이 되도록 설계되었습니다.
* 상위 *N* 개의 장기 실행 쿼리 및 해당 추세
* 쿼리 세부 정보: 최소, 최대, 평균 및 표준 편차 쿼리 시간으로 실행 기록을 봅니다.
* 리소스 사용률(CPU, 메모리 및 스토리지).
 
이 문서에서는 MySQL 느린 쿼리 로그, Log Analytics 도구 및 통합 문서 템플릿을 사용하여 Azure Database for MySQL 유연한 서버에 대한 Query Performance Insight를 시각화하는 방법에 대해 설명합니다.

이 자습서에서 학습할 방법은 다음과 같습니다.
>[!div class="checklist"]
> * Azure Portal 또는 Azure CLI를 사용하여 느린 쿼리 로그 구성
> * 진단 설정
> * Log Analytics를 사용하여 느린 쿼리 로그 보기 
> * 통합 문서를 사용하여 느린 쿼리 로그 보기 

## <a name="prerequisites"></a>필수 조건

- [Azure Database for MySQL 유연한 서버 인스턴스를 만듭니다](./quickstart-create-server-portal.md).
- [Log Analytics 작업 영역을 만듭니다](../../azure-monitor/logs/quick-create-workspace.md).


## <a name="configure-slow-query-logs-by-using-the-azure-portal"></a>Azure Portal을 사용하여 느린 쿼리 로그 구성 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버 인스턴스를 선택합니다.

1. 왼쪽 창의 **설정** 아래에서 **서버 매개 변수** 를 선택합니다.

   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="'서버 매개 변수' 목록을 보여 주는 스크린샷":::

1. **slow_query_log** 매개 변수에 대해 **ON** 을 선택합니다.

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="'ON'으로 전환된 'slow_query_log' 매개 변수를 보여 주는 스크린샷":::

1. **long_query_time** 및 **log_slow_admin_statements** 와 같은 다른 매개 변수는 [느린 쿼리 로그](./concepts-slow-query-logs.md#configure-slow-query-logging) 설명서를 참조하세요.  

   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="나머지 느린 쿼리 로그와 관련된 매개 변수에 대한 업데이트된 값을 보여 주는 스크린샷":::

1. **저장** 을 선택합니다. 

   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="매개 변수 값의 변경 내용을 저장하기 위한 '저장' 단추의 스크린샷":::

**서버 매개 변수** 페이지를 닫으면 로그 목록으로 돌아갈 수 있습니다.

## <a name="configure-slow-query-logs-by-using-the-azure-cli"></a>Azure CLI를 사용하여 느린 쿼리 로그 구성
 
또는 다음 명령을 실행하여 Azure CLI에서 유연한 서버에 대한 느린 쿼리 로그를 사용 하도록 설정하고 구성할 수 있습니다. 

> [!IMPORTANT]
> 유연한 서버의 성능에 크게 영향을 주지 않도록 하려면 감사 용도에 필요한 이벤트 유형 및 사용자만 기록하는 것이 좋습니다.

```azurecli
# Turn on statement level log.

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 seconds. This setting will log all queries that execute for more than 10 seconds. Adjust this threshold based on your definition for slow queries.

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable slow query logs.

az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>진단 설정

느린 쿼리 로그는 Azure Monitor 진단 설정과 통합되어 로그를 다음 세 가지 데이터 싱크 중 하나에 파이프할 수 있습니다.
* Log Analytics 작업 영역
* 이벤트 허브
* 스토리지 계정

>[!Note]
>진단 설정을 구성하기 전에 먼저 데이터 싱크를 만들어야 합니다. 구성한 데이터 싱크에 있는 느린 쿼리 로그에 액세스할 수 있습니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

1. 왼쪽 창의 **모니터링** 아래에서 **진단 설정** 을 선택합니다.

1. **진단 설정** 창에서 **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="'진단 설정' 창의 '진단 설정 추가' 링크에 대한 스크린샷":::

1. **이름** 상자에서 진단 설정의 이름을 입력합니다.

1. 해당 확인란을 선택하여 느린 쿼리 로그를 보낼 대상(Log Analytics 작업 영역, 이벤트 허브 또는 스토리지 계정)을 지정합니다.

    >[!Note]
    > 이 자습서에서는 느린 쿼리 로그를 Log Analytics 작업 영역에 보냅니다.

1. **로그** 아래에서 로그 유형에 대해 **MySqlSlowLogs** 확인란을 선택합니다.

    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="구성 옵션을 선택하기 위한 '진단 설정' 창의 스크린샷":::

1. 느린 쿼리 로그를 전송하도록 데이터 싱크를 구성한 후 **저장** 을 선택합니다.

    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="저장이 강조 표시된 진단 설정 구성 옵션의 스크린샷":::

## <a name="view-query-insights-by-using-log-analytics"></a>Log Analytics를 사용하여 쿼리 인사이트 보기 

1. Log Analytics의 왼쪽 창에 있는 **모니터링** 아래에서 **로그** 를 선택합니다.

1. 열리는 **쿼리** 창을 닫습니다.

   :::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Log Analytics '쿼리' 창의 스크린샷":::

1. 실행할 쿼리는 쿼리 창에서 작성할 수 있습니다. 특정 서버에서 10초보다 긴 쿼리를 찾기 위해 다음 코드를 사용했습니다.

   ```kusto
   AzureDiagnostics
      | where Category == 'MySqlSlowLogs'
      | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
      | where query_time_d > 10
   ```
    
1. **시간 범위** 를 선택한 다음, 쿼리를 실행합니다. 결과는 다음 이미지에 표시됩니다.  

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="느린 쿼리 로그의 스크린샷":::

## <a name="view-query-insights-by-using-workbooks"></a>통합 문서를 사용하여 쿼리 인사이트 보기 

1. Azure Portal의 왼쪽 창에 있는 Azure Database for MySQL 유연한 서버 인스턴스에 대한 **모니터링** 아래에서 **통합 문서** 를 선택합니다.

1.  **Query Performance Insight** 템플릿을 선택합니다. 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="통합 문서 갤러리의 모든 통합 문서를 보여 주는 스크린샷":::

통합 문서에서 다음 시각화를 볼 수 있습니다. 
>[!div class="checklist"]
> * 쿼리 로드
> * 전체 활성 연결
> * 느린 쿼리 추세(10초를 초과하는 쿼리 시간)
> * 느린 쿼리 세부 정보
> * 가장 긴 상위 5개 쿼리 나열
> * 최소, 최대, 평균 및 표준 편차 쿼리 시간을 기준으로 느린 쿼리를 요약합니다.

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="두 개의 긴 쿼리를 보여 주는 스크린샷":::

>[!Note]
> * 리소스 사용률을 보려면 [개요] 템플릿을 사용할 수 있습니다.
> * 이러한 템플릿을 편집하고 요구 사항에 따라 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure Monitor 통합 문서 개요](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)를 참조하세요.
> * 빠른 보기를 위해 통합 문서 또는 Log Analytics 쿼리를 대시보드에 고정할 수도 있습니다. 자세한 내용은 [Azure Portal에서 대시보드 만들기](../../azure-portal/azure-portal-dashboards.md)를 참조하세요. 

Query Performance Insight에서 잠재적인 병목 상태를 찾는 데 도움이 될 수 있는 두 가지 메트릭은 *기간* 및 *실행 횟수* 입니다. 장기 실행 쿼리는 리소스를 더 오래 잠그고, 다른 사용자를 차단하고, 확장성을 제한할 가능성이 가장 높습니다. 

일부 경우에 실행 횟수가 높으면 네트워크 왕복이 증가할 수 있습니다. 왕복은 성능에 영향을 줍니다. 네트워크 대기 시간 및 다운스트림 서버 대기 시간의 영향을 받습니다. 따라서 실행 횟수는 자주 실행되는("번잡") 쿼리를 찾는 데 도움이 될 수 있습니다. 이러한 쿼리는 최적화에 가장 적합한 후보입니다. 

## <a name="next-steps"></a>다음 단계
- [Azure Monitor 통합 문서](../../azure-monitor/visualize/workbooks-overview.md#visualizations) 및 풍부한 시각화 옵션에 대해 자세히 알아봅니다.
- [느린 쿼리 로그에 대해 자세히 알아봅니다](concepts-slow-query-logs.md).


