---
title: Azure Monitor
description: Azure Monitor
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: dcb4dcd6037f8efcbac33e970566babf10c7d262
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131859302"
---
# <a name="monitor-azure-managed-instance-for-apache-cassandra-using-azure-monitor"></a>Azure Monitor 사용하여 Apache Cassandra용 Azure Managed Instance 모니터링

Apache Cassandra용 Azure Managed Instance [Azure Monitor](../azure-monitor/overview.md)사용하여 메트릭 및 진단 로깅을 제공합니다. 

## <a name="azure-metrics"></a>Azure Metrics

클러스터 리소스로 이동하여 메트릭 탭을 선택하여 Apache Cassandra용 Azure Managed Instance 대한 메트릭을 시각화할 수 있습니다. 그런 다음 사용 가능한 메트릭 및 집계 중에서 선택할 수 있습니다.

:::image type="content" source="./media/azure-monitor/metrics.png" alt-text="메트릭 시각화":::

## <a name="diagnostic-settings-in-azure"></a>Azure의 진단 설정

Azure의 진단 설정은 리소스 로그를 수집하는 데 사용됩니다. Azure 리소스 로그를 리소스에서 내보내고, 해당 리소스의 작업에 대한 풍부하고 빈도 높은 데이터를 제공합니다. 이러한 로그는 요청별로 캡처되며 “데이터 평면 로그”라고도 불립니다. 데이터 평면 작업의 몇 가지 예로는 delete, insert 및 readFeed가 있습니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

플랫폼 메트릭과 활동 로그는 자동으로 수집되지만 리소스 로그를 수집하거나 Azure Monitor 외부로 전달 하려면 진단 설정을 만들어야 합니다. Apache Cassandra 클러스터 리소스에 대한 Azure Managed Instance 대한 진단 설정을 켜고 리소스 로그를 다음 원본으로 보낼 수 있습니다.
- Log Analytics 작업 영역
  - Log Analytics로 전송된 데이터를 **Azure 진단(레거시)** 또는 **리소스 관련(미리 보기)** 표에 쓸 수 있습니다 
- 이벤트 허브
- 스토리지 계정
  
> [!NOTE]
> 리소스별 모드에서 진단 설정을 만드는 것이 좋습니다.

## <a name="create-diagnostic-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Azure Portal 통해 진단 설정 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Apache Cassandra용 Azure Managed Instance 클러스터 리소스로 이동합니다. 

    :::image type="content" source="./media/azure-monitor/cluster.png" alt-text="클러스터 선택":::
 
1. **모니터링 섹션** 아래에서 **진단 설정** 창을 열고 **진단 설정 추가** 옵션을 선택합니다.

    :::image type="content" source="./media/azure-monitor/settings.png" alt-text="진단 설정 추가":::


1. 진단 **설정** 창에서 설정의 이름을 선택하고 범주 **세부 정보** 를 선택합니다. **CassandraAudit** 범주는 감사 및 CQL 작업을 기록합니다. **CassandraLogs** 범주는 Cassandra 서버 작업을 기록합니다. 그런 다음, 원하는 대상으로 로그를 보냅니다. **Log Analytics 작업 영역** 에 로그를 보내는 경우, 대상 테이블로 **특정 리소스** 를 선택해야 합니다. 

    :::image type="content" source="./media/azure-monitor/preferred-categories.png" alt-text="범주 선택":::

   > [!WARNING]
   > Log Analytics 작업 영역으로 로그를 보내는 경우 로그가 처음 표시되는 데 최대 **20분이** 걸릴 수 있습니다. 그때까지 리소스별 테이블(아래 Apache Cassandra용 Azure Managed Instance 아래에 표시)은 표시되지 않습니다.  


1. 진단 로깅이 설정되고 데이터가 흐르면 **로그** 탭으로 이동하여 Azure Data Explorer 사용하여 사용 가능한 진단 로그를 쿼리할 수 있습니다. Azure Monitor 및 Kusto 쿼리 언어에 대한 자세한 내용은 [이 문서를](../azure-monitor/logs/log-query-overview.md) 살펴보세요. 

    :::image type="content" source="./media/azure-monitor/query.png" alt-text="로그 쿼리":::

## <a name="create-diagnostic-setting-via-azure-cli"></a><a id="create-setting-cli"></a> Azure CLI 통해 진단 설정 만들기
[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 Azure CLI를 통해 진단 설정을 만듭니다. 매개 변수에 대한 설명은 이 명령에 대한 설명서를 참조하세요.

```azurecli-interactive
    logs='[{"category":"CassandraAudit","enabled":true,"retentionPolicy":{"enabled":true,"days":3}},{"category":"CassandraLogs","enabled":true,"retentionPolicy":{"enabled":true,"days":3}}]'
    resourceId='/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDB/cassandraClusters/{CLUSTER_NAME}'
    workspace='/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}'

    az monitor diagnostic-settings create  --name tvk-doagnostic-logs-cassandra --resource $resourceId --logs  $logs --workspace $workspace --export-to-resource-specific true
```

## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> REST API를 통해 진단 설정 만들기
대화형 콘솔을 통해 진단 설정을 만드는 데 [Azure Monitor REST API](/rest/api/monitor/diagnosticsettings/createorupdate)를 사용합니다.
> [!Note]
> 리소스별 테이블을 사용하도록 설정하기 위해 **logAnalyticsDestinationType** 속성을 **Dedicated** 로 설정하는 것이 좋습니다.

### <a name="request"></a>요청

   ```HTTP
   PUT
   https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
   ```

### <a name="headers"></a>헤더

   |매개 변수/헤더  | 값/설명  |
   |---------|---------|
   |name     |  진단 설정의 이름입니다.      |
   |resourceUri     |   subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}      |
   |api-version     |    2017-05-01-미리 보기     |
   |콘텐츠 형식     |    application/json     |

### <a name="body"></a>본문

```json
{
    "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}",
    "type": "Microsoft.Insights/diagnosticSettings",
    "name": "name",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": null,
        "serviceBusRuleId": null,
        "workspaceId": "/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "logs": [
            {
                "category": "CassandraAudit",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "CassandraLogs",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ],
        "logAnalyticsDestinationType": "Dedicated"
    },
    "identity": null
}
```


## <a name="next-steps"></a>다음 단계

* Azure Portal, CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 방법에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md) 문서를 참조하세요.