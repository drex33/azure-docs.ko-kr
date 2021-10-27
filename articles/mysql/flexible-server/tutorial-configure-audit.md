---
title: '자습서: Azure Database for MySQL 유연한 서버를 사용하여 감사 로그 구성'
description: 이 자습서에서는 Azure Database for MySQL 유연한 서버를 사용하여 감사 로그를 구성하는 방법을 보여 줍니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 4a819814b8c5bb8cf6d4bea949463fddd05405a8
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065866"
---
# <a name="tutorial-configure-audit-logs-by-using-azure-database-for-mysql-flexible-server"></a>자습서: Azure Database for MySQL 유연한 서버를 사용하여 감사 로그 구성

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버를 사용하여 감사 로그를 구성할 수 있습니다. 감사 로그는 연결, 관리, DDL(데이터 정의 언어) 및 DML(데이터 조작 언어) 이벤트를 포함한 데이터베이스 수준 작업을 추적하는 데 사용할 수 있습니다. 이러한 유형의 로그는 일반적으로 규정 준수를 위해 사용됩니다. 일반적으로 데이터베이스 감사를 사용하여 다음을 수행합니다.
* 특정 스키마, 테이블 또는 행 내에서 발생하거나 특정 콘텐츠에 영향을 주는 모든 작업을 고려합니다.
* 사용자(또는 다른 사용자)의 책임에 따라 부적절한 작업을 방지합니다.
* 의심스러운 활동을 조사합니다.
* 특정 데이터베이스 작업에 대한 데이터를 모니터링하고 수집합니다.
 
이 문서에서는 MySQL 감사 로그, Log Analytics 도구 또는 통합 문서 템플릿을 사용하여 Azure Database for MySQL 유연한 서버에 대한 감사 정보를 시각화하는 방법에 대해 설명합니다.

이 자습서에서 학습할 방법은 다음과 같습니다.
>[!div class="checklist"]
> * Azure Portal 또는 Azure CLI를 사용하여 감사 구성
> * 진단 설정
> * Log Analytics를 사용하여 감사 로그 보기 
> * 통합 문서를 사용하여 감사 로그 보기  

## <a name="prerequisites"></a>필수 조건

- [Azure Database for MySQL 유연한 서버 인스턴스를 만듭니다](./quickstart-create-server-portal.md).
- [Log Analytics 작업 영역을 만듭니다](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="configure-auditing-by-using-the-azure-portal"></a>Azure Portal을 사용하여 감사 구성 

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버 인스턴스를 선택합니다.

1. 왼쪽 창의 **설정** 아래에서 **서버 매개 변수** 를 선택합니다.

    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="'서버 매개 변수' 목록을 보여 주는 스크린샷":::

1. **audit_log_enabled** 매개 변수에 대해 **ON** 을 선택합니다.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="'ON'으로 전환된 'audit_log_enabled' 매개 변수를 보여 주는 스크린샷":::

1. **audit_log_events** 매개 변수에 대해 드롭다운 목록에서 기록할 [이벤트 유형](concepts-audit-logs.md#configure-audit-logging)을 선택합니다.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="'audit_log_events' 드롭다운 목록의 이벤트 옵션에 대한 스크린샷":::

1. **audit_log_exclude_users** 및 **audit_log_include_users** 매개 변수에 대해 MySQL 사용자 이름을 제공하여 로깅에 포함하거나 제외할 MySQL 사용자를 지정합니다.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="로깅에 포함하거나 제외할 MySQL 사용자 이름을 보여 주는 스크린샷":::

1. **저장** 을 선택합니다.

    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="매개 변수 값의 변경 내용을 저장하기 위한 '저장' 단추의 스크린샷":::


## <a name="configure-auditing-by-using-the-azure-cli"></a>Azure CLI를 사용하여 감사 구성
 
또는 다음 명령을 실행하여 Azure CLI에서 유연한 서버에 대한 감사를 사용 하도록 설정하고 구성할 수 있습니다. 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>진단 설정

감사 로그는 Azure Monitor 진단 설정과 통합되어 로그를 다음 세 가지 데이터 싱크 중 하나에 파이프할 수 있습니다.
* Log Analytics 작업 영역
* 이벤트 허브
* 스토리지 계정

>[!Note]
>진단 설정을 구성하기 전에 먼저 데이터 싱크를 만들어야 합니다. 구성한 데이터 싱크에 있는 감사 로그에 액세스할 수 있습니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

1. 왼쪽 창의 **모니터링** 아래에서 **진단 설정** 을 선택합니다.

1. **진단 설정** 창에서 **진단 설정 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="'진단 설정' 창의 '진단 설정 추가' 링크에 대한 스크린샷":::

1. **이름** 상자에서 진단 설정의 이름을 입력합니다.

    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="구성 옵션을 선택하기 위한 '진단 설정' 창의 스크린샷":::

1. 해당 확인란을 선택하여 감사 로그를 보낼 대상(Log Analytics 작업 영역, 이벤트 허브 또는 스토리지 계정)을 지정합니다.

    >[!Note]
    > 이 자습서에서는 감사 로그를 Log Analytics 작업 영역에 보냅니다.
    
1. **로그** 아래에서 로그 유형에 대해 **MySqlAuditLogs** 확인란을 선택합니다.

1. 감사 로그를 파이프하도록 데이터 싱크가 구성되면 **저장** 을 선택합니다.

    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="'진단 설정' 창 위쪽의 '저장' 단추에 대한 스크린샷":::

## <a name="view-audit-logs-by-using-log-analytics"></a>Log Analytics를 사용하여 감사 로그 보기 

1. Log Analytics의 왼쪽 창에 있는 **모니터링** 아래에서 **로그** 를 선택합니다.

1. **쿼리** 창을 닫습니다.  

   :::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Log Analytics '쿼리' 창의 스크린샷":::

1. 실행할 쿼리는 쿼리 창에서 작성할 수 있습니다. 예를 들어 특정 서버에서 감사된 이벤트의 요약을 찾기 위해 다음 쿼리를 사용했습니다. 

    ```kusto
    AzureDiagnostics
        |where Category =='MySqlAuditLogs' 
        |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
        |summarize count() by event_class_s,event_subclass_s 
        |order by event_class_s 
    ```
    
    :::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="특정 서버에서 감사된 이벤트의 요약을 찾으려는 Log Analytics 쿼리 예제의 스크린샷":::

## <a name="view-audit-logs-by-using-workbooks"></a>통합 문서를 사용하여 감사 로그 보기 
 
감사에 사용하는 통합 문서 템플릿을 사용하려면 플랫폼 로그를 보내는 진단 설정을 만들어야 합니다. 

1. Azure Monitor의 왼쪽 창에서 **활동 로그** 를 선택한 다음, **진단 설정** 을 선택합니다. 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="Azure Monitor '활동 로그' 창의 '진단 설정' 탭을 보여 주는 스크린샷":::

1. **진단 설정** 창에서 새 설정을 추가하거나 기존 설정을 편집할 수 있습니다. 각 설정에는 하나의 대상 유형만 있을 수 있습니다.

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="로그 대상을 선택하기 위한 Azure Monitor '진단 설정' 창의 스크린샷":::

    > [!Note]
    > 이미 구성한 데이터 싱크(Log Analytics 작업 영역, 스토리지 계정 또는 이벤트 허브)에서 느린 쿼리 로그에 액세스할 수 있습니다. 로그가 표시되는 데에는 최대 10분이 걸릴 수 있습니다.

1. Azure Portal의 왼쪽 창에 있는 Azure Database for MySQL 유연한 서버 인스턴스에 대한 **모니터링** 아래에서 **통합 문서** 를 선택합니다.
1. **감사** 통합 문서를 선택합니다. 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="통합 문서 갤러리의 모든 통합 문서를 보여 주는 스크린샷":::
    
통합 문서에서 다음 시각화를 볼 수 있습니다. 
>[!div class="checklist"]
> * 서비스에 대한 관리 작업
> * 감사 요약
> * 연결 이벤트 감사 요약
> * 연결 이벤트 감사
> * 테이블 액세스 요약
> * 식별된 오류


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="'서비스에 대한 관리 작업' 통합 문서 템플릿의 스크린샷":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="'연결 이벤트 감사' 통합 문서 템플릿의 스크린샷":::

>[!Note]
> * 이러한 템플릿을 편집하고 요구 사항에 따라 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure Monitor 통합 문서 개요](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)의 "편집 모드" 섹션을 참조하세요.
> * 빠른 보기를 위해 통합 문서 또는 Log Analytics 쿼리를 대시보드에 고정할 수도 있습니다. 자세한 내용은 [Azure Portal에서 대시보드 만들기](../../azure-portal/azure-portal-dashboards.md)를 참조하세요. 

*서비스에 대한 관리 작업* 보기는 서비스에서 수행된 작업에 대한 세부 정보를 제공합니다. 구독의 리소스에 대해 수행된 쓰기 작업(PUT, POST, DELETE)의 *대상, 사용자 및 시기* 를 확인하는 데 도움이 됩니다. 

다른 시각화를 사용하여 데이터베이스 작업의 세부 정보를 이해할 수 있습니다. 데이터베이스 보안의 4가지 요소는 다음과 같습니다. 
* **서버 보안**: 권한 없는 담당자가 데이터베이스에 액세스하지 못하도록 방지해야 합니다.  
* **데이터베이스 연결**: 관리자는 권한 있는 담당자가 데이터베이스 업데이트를 수행했는지 여부를 확인해야 합니다.
* **테이블 액세스 제어**: 권한 있는 사용자의 액세스 키와 각 사용자가 처리할 권한이 있는 데이터베이스 내의 테이블을 표시합니다.
* **데이터베이스 액세스 제한**: 데이터베이스를 인터넷에 업로드한 사용자에게 특히 중요하며, 외부 원본에서 데이터베이스에 액세스하지 못하도록 방지하는 데 도움이 됩니다. 

## <a name="next-steps"></a>다음 단계
- [Azure Monitor 통합 문서](../../azure-monitor/visualize/workbooks-overview.md#visualizations) 및 풍부한 시각화 옵션에 대해 자세히 알아봅니다.
- [감사 로그에 대해 자세히 알아봅니다](concepts-audit-logs.md).
