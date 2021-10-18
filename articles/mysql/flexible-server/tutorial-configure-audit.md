---
title: '자습서: Azure Database for MySQL - 유연한 서버 감사'
description: '자습서: Azure Database for MySQL - 유연한 서버 감사'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: b4c8206071ce71cc29a9de45b46fa05c40f9cd91
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621384"
---
# <a name="tutorial-auditing-for-azure-database-for-mysql--flexible-server"></a>자습서: Azure Database for MySQL - 유연한 서버 감사
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 유연한 서버는 사용자에게 감사 로그를 구성할 수 있는 기능을 제공합니다. 감사 로그는 연결, 관리, DDL 및 DML 이벤트를 비롯한 데이터베이스 수준 활동을 추적하는 데 사용할 수 있습니다. 이러한 유형의 로그는 일반적으로 규정 준수를 위해 사용됩니다. 데이터베이스 감사는 일반적으로 다음 용도로 사용됩니다.
* 특정 스키마, 테이블 또는 행 내에서 발생하거나 특정 콘텐츠에 영향을 주는 모든 작업에 대한 보고
* 사용자(또는 다른 사람)가 자신의 책임에 따라 부적절한 작업을 수행하지 않도록 방지
* 의심스러운 활동 조사
* 특정 데이터베이스 작업에 대한 데이터 모니터링 및 수집
 
이 자습서에서는 MySQL 감사 로그, Log Analytics 도구 또는 통합 문서 템플릿을 사용하여 Azure Database for MySQL – 유연한 서버에 대한 감사 정보를 시각화하는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항
- Azure Database for MySQL – 유연한 서버의 인스턴스를 만들어야 합니다. 단계별 절차는 [Azure Database for MySQL - 유연한 서버 인스턴스 만들기](./quickstart-create-server-portal.md)를 참조하세요.
- Log Analytics 작업 영역을 만들어야 합니다. 단계별 절차는 [Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.
>[!div class="checklist"]
> * 포털 또는 Azure CLI를 사용하여 감사 구성
> * 진단 설정
> * Log Analytics를 사용하여 감사 로그 보기 
> * 통합 문서를 사용하여 감사 로그 보기 

## <a name="configure-auditing-from-portal"></a>포털에서 감사 구성 


1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 유연한 서버를 선택합니다.

1. 사이드바의 **설정** 섹션 아래에서 **서버 매개 변수** 를 선택합니다.
    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="서버 매개 변수":::

1. **Audit_log_enabled** 매개 변수를 ON으로 업데이트합니다.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="감사 로그 사용":::

1. **audit_log_events** 매개 변수를 업데이트하여 [이벤트 유형](concepts-audit-logs.md#configure-audit-logging)을 로그되도록 선택합니다.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="감사 로그 이벤트":::

1. **Audit_log_exclude_users** 및 **audit_log_include_users** 매개 변수를 업데이트하여 로깅에 포함하거나 제외할 MySQL 사용자를 추가합니다. MySQL 사용자 이름을 제공하여 사용자를 지정합니다.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="감사 로그 사용자 제외":::

1. 매개 변수를 변경한 다음 **저장** 을 클릭합니다. 또는 변경 사항을 **취소** 할 수 있습니다.
    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="저장":::



## <a name="configure-auditing--from-azure-cli"></a>Azure CLI에서 감사 구성
 
Azure CLI를 사용하여 위의 작업을 수행하려는 경우 CLI를 사용하여 서버에 대한 감사를 사용하도록 설정하고 구성할 수 있습니다. 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>진단 설정

감사 로그는 Azure Monitor 진단 설정과 통합되어 로그를 Azure Monitor 로그, Event Hubs 또는 Azure Storage로 파이프할 수 있습니다.

1. 사이드바의 **모니터링** 섹션에서 **진단 설정** 을 선택합니다.

1. "+ 진단 설정 추가" :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="진단 설정 추가":::를 클릭합니다.

1. 진단 설정 이름을 입력합니다.

1. 감사 로그를 보낼 대상(Log Analytics 작업 영역, 스토리지 계정 및/또는 이벤트 허브)을 지정합니다.
    >[!Note]
    > 자습서의 범위에서 느린 쿼리 로그를 Log Analytics 작업 영역에 보내야 합니다.
    
1. 로그 형식으로 **MySqlAuditLogs** 를 선택합니다.
    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="진단 설정 구성":::

1. 감사 로그를 파이프하도록 데이터 싱크를 구성한 후에는 **저장** 을 클릭하면 됩니다.
    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="진단 설정 저장":::

    >[!Note]
    >진단 설정을 구성하려면 먼저 데이터 싱크(Log Analytics 작업 영역, 스토리지 계정 또는 이벤트 허브)를 만들어야 합니다. 구성한 데이터 싱크(Log Analytics 작업 영역, 스토리지 계정 또는 이벤트 허브)에서 느린 쿼리 로그에 액세스할 수 있습니다. 로그가 표시되는 데 최대 10분이 걸릴 수 있습니다.

## <a name="view-audit-logs-using-log-analytics"></a>Log Analytics를 사용하여 감사 로그 보기 

**모니터링** 섹션 아래에서 **로그** 로 이동합니다. **쿼리** 창을 닫습니다.  

:::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="로그 분석의 스크린샷":::

실행할 쿼리는 쿼리 창에서 작성할 수 있습니다.  여기서는 쿼리를 사용하여 특정 서버에 대해 감사된 이벤트 요약을 찾았습니다.

```kusto
AzureDiagnostics
    |where Category =='MySqlAuditLogs' 
    |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
    |summarize count() by event_class_s,event_subclass_s 
    |order by event_class_s 
```
  
:::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="로그 분석 쿼리의 스크린샷":::

## <a name="view-audit-logs-using-workbooks"></a>통합 문서를 사용하여 감사 로그 보기 
 
감사에 사용하는 통합 문서 템플릿을 사용하려면 플랫폼 로그를 보내는 진단 설정을 만들어야 합니다. 

1.  플랫폼 로그를 보내려면 Azure Monitor 메뉴에서 활동 로그를 클릭한 다음, **진단 설정** 을 클릭합니다. 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="진단 설정의 스크린샷":::

2.  새 설정을 추가하거나 [설정 편집]을 선택하여 기존 설정을 편집합니다. 각 설정에는 대상 유형이 하나만 있을 수 있습니다.

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="진단 설정 선택 항목의 스크린샷":::

    >[!Note]
    >구성한 데이터 싱크(Log Analytics 작업 영역, 스토리지 계정, 이벤트 허브)에서 느린 쿼리 로그에 액세스할 수 있습니다. 로그가 표시되는 데 최대 10분이 걸릴 수 있습니다.

3.  Azure Portal에서 Azure Database for MySQL – 유연한 서버에 대한 **모니터링** 블레이드로 이동하고, **통합 문서** 를 선택합니다.
4.  템플릿이 표시됩니다. **감사** 를 선택합니다. 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="통합 문서 템플릿의 스크린샷":::
    
표시되는 시각화는 다음과 같습니다. 
>[!div class="checklist"]
> * 서비스에 대한 관리 작업
> * 감사 요약
> * 연결 이벤트 감사 요약
> * 연결 이벤트 감사
> * 테이블 액세스 요약
> * 식별된 오류


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="통합 문서 템플릿 관리 이벤트의 스크린샷":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="통합 문서 템플릿 감사 요약 이벤트의 스크린샷":::

>[!Note]
> * 이러한 템플릿은 편집하고 요구 사항에 따라 사용자 지정할 수도 있습니다. 자세한 내용은 [Azure Monitor 통합 문서 개요 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)를 참조하세요.
> * 빠른 보기를 위해 통합 문서 또는 Log Analytics 쿼리를 대시보드에 ping할 수도 있습니다. 자세한 내용은 [Azure Portal에서 대시보드 만들기 - Azure Portal](../../azure-portal/azure-portal-dashboards.md)을 참조하세요. 

서비스에 대한 관리 작업은 서비스에서 수행된 작업에 대한 세부 정보를 제공합니다. 구독의 리소스에 대해 수행된 쓰기 작업(PUT, POST, DELETE)의 대상, 사용자 및 시기를 확인하는 데 도움이 됩니다. 

다른 시각화는 데이터베이스 작업에 대한 세부 정보를 얻는 데 도움이 됩니다. 데이터베이스 보안은 네 부분으로 구성됩니다. 이러한 부분은 서버 보안, 데이터베이스 연결, 테이블 액세스 제어, 데이터베이스 액세스 제한입니다. 서버 보안은 권한 없는 담당자가 데이터베이스에 액세스하지 못하도록 방지하는 책임이 있습니다. 데이터베이스 연결 측면에서 관리자는 권한 있는 담당자가 데이터베이스에 대한 업데이트를 수행하는지 여부도 확인해야 합니다. 데이터베이스 액세스를 제한하는 것은 특히 인터넷에 데이터베이스를 업로드한 사용자에게 중요합니다. 이렇게 하면 외부 원본에서 데이터베이스에 들어가거나 액세스하지 못하도록 방지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
- [Azure Monitor 통합 문서를 시작](../../azure-monitor/visualize/workbooks-overview.md#visualizations)하고 통합 문서의 다양한 시각화 옵션에 대해 자세히 알아봅니다.
- [감사 로그](concepts-audit-logs.md)에 대해 자세히 알아봅니다