---
title: Azure에서 진단 설정을 사용하여 Azure Cosmos DB 데이터 모니터링
description: Azure 진단 설정을 사용하여 Azure Cosmos DB에 저장된 데이터의 성능 및 가용성을 모니터링하는 방법을 알아봅니다
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 2f25cfa8f2c9c70b6cc97dc96d504b41078f5b5f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114667675"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure에서 진단 설정을 사용하여 Azure Cosmos DB 데이터 모니터링
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure의 진단 설정은 리소스 로그를 수집하는 데 사용됩니다. Azure 리소스 로그를 리소스에서 내보내고, 해당 리소스의 작업에 대한 풍부하고 빈도 높은 데이터를 제공합니다. 이러한 로그는 요청별로 캡처되며 “데이터 평면 로그”라고도 불립니다. 데이터 평면 작업의 몇 가지 예로는 delete, insert 및 readFeed가 있습니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

플랫폼 메트릭과 활동 로그는 자동으로 수집되지만 리소스 로그를 수집하거나 Azure Monitor 외부로 전달 하려면 진단 설정을 만들어야 합니다. Azure Cosmos DB 계정에 대한 진단 설정을 활성화하고 다음 원본에 리소스 로그를 보낼 수 있습니다.
- Log Analytics 작업 영역
  - Log Analytics로 전송된 데이터를 **Azure 진단(레거시)** 또는 **리소스 관련(미리 보기)** 표에 쓸 수 있습니다 
- 이벤트 허브
- 스토리지 계정
  
> [!NOTE]
> [REST API를 통한 진단 설정 생성에 대한 지침](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting)에 따라 리소스별 모드(Table API를 제외한 모든 API에 대해)에서 진단 설정을 생성하는 것이 좋습니다. 이 옵션은 데이터 처리를 위한 향상된 보기를 통해 추가 비용 최소화를 이끌어 냅니다.

## <a name="create-diagnostics-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Azure Portal을 통해 진단 설정 생성하기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Cosmos 계정으로 이동합니다. **모니터링 섹션** 아래에서 **진단 설정** 창을 열고 **진단 설정 추가** 옵션을 선택합니다.

   :::image type="content" source="./media/monitor-cosmos-db/diagnostics-settings-selection.png" alt-text="진단 선택":::


3. **진단 설정** 창에서 원하는 범주로 양식을 채웁니다.

### <a name="choose-log-categories"></a>로그 범주 선택

   |범주  |API   | 정의  | 키 속성   |
   |---------|---------|---------|---------|
   |DataPlaneRequests     |  모든 API        |     백엔드 요청을 데이터 평면 작업으로 로그해 계정 내의 데이터를 작성, 업데이트, 삭제 및 검색 실행하도록 요청합니다.   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
   |MongoRequests     |    Mongo    |   프런트 엔드에서 사용자 개시 요청을 로그해 MongoDB용 Azure Cosmos DB API 요청을 처리합니다. 이 범주를 사용하도록 설정하는 경우 DataPlaneRequests를 비활성화해야 합니다.      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
   |CassandraRequests     |   Cassandra      |    프런트 엔드에서 사용자 개시 요청을 로그해 Cassandra를 위한 Azure Cosmos DB API 요청을 처리합니다. 이 범주를 사용하도록 설정하는 경우 DataPlaneRequests를 비활성화해야 합니다.     |     `operationName`, `requestCharge`, `piiCommandText`    |
   |GremlinRequests     |    Gremlin    |     프런트 엔드에서 사용자 개시 요청을 로그해 Gremlin을 위한 Azure Cosmos DB API 요청을 처리합니다. 이 범주를 사용하도록 설정하는 경우 DataPlaneRequests를 비활성화해야 합니다.    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
   |QueryRuntimeStatistics     |   SQL      |     이 표에서는 SQL API 계정에 대해 실행되는 쿼리 작업에 대해 자세히 설명합니다. 기본값으로, 쿼리 텍스트와 해당 매개 변수는 요청에 의해 사용할 수 있는 전체 텍스트 쿼리 로깅으로 개인 데이터를 로깅하지 않도록 난독 처리됩니다.    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
   |PartitionKeyStatistics     |    모든 API     |   파티션 키의 스토리지 크기(KB)를 나타내 논리 파티션 키의 통계를 로그합니다. 이 표는 스토리지 기울이기 문제 해결에 유용합니다. 이 PartitionKeyStatistics 로그는 다음 조건이 충족되는 경우에만 내보내집니다. <br/><ul><li> 문서의 1% 이상에 동일한 논리 파티션 키가 있습니다. </li><li> 모든 키 중에서 스토리지 크기가 가장 큰 상위 3개 키는 PartitionKeyStatistics 로그에 의해 캡처됩니다. </li></ul> 이전 조건이 충족되지 않으면, 파티션 키 통계 데이터를 사용할 수 없습니다. 위의 조건이 계정에 대해 충족되지 않아도 괜찮습니다. 이는 일반적으로 논리 파티션 스토리지 기울이기가 없음을 의미합니다. |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
   |PartitionKeyRUConsumption     |   SQL API    |     파티션 키의 집계된 초당 RU/s 소비를 로그합니다. 이 표는 핫 파티션 문제 해결에 유용합니다. 현재 Azure Cosmos DB SQL API 계정 및 포인트 읽기/쓰기 및 저장 프로시저 작업에 대해서만 파티션 키를 보고합니다.   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
   |ControlPlaneRequests     |   모든 API       |    컨트롤 플레인 작업(예: 계정 만들기, 지역 추가 또는 제거, 계정 복제 설정 업데이트 등)에 대한 세부 정보를 로그합니다.     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
   |TableApiRequests     |   Table API    |     프런트 엔드에서 사용자가 시작한 요청을 로그하여 테이블에 쓸 Azure Cosmos DB API를 제공합니다. 이 범주를 사용하도록 설정하는 경우 DataPlaneRequests를 비활성화해야 합니다.       |    `operationName`, `requestCharge`, `piiCommandText`     |

4. **범주 세부 정보** 를 선택한 후, 원하는 대상으로 로그를 보냅니다. **Log Analytics 작업 영역** 에 로그를 보내는 경우, 대상 테이블로 **특정 리소스** 를 선택해야 합니다.

    :::image type="content" source="./media/monitor-cosmos-db/diagnostics-resource-specific.png" alt-text="리소스별 사용을 선택합니다":::

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
                "category": "DataPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "QueryRuntimeStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyRUConsumption",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ControlPlaneRequests",
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

## <a name="create-diagnostic-setting-via-azure-cli"></a>Azure CLI를 통해 진단 설정 만들기
[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 명령을 사용하여 Azure CLI를 통해 진단 설정을 만듭니다. 매개 변수에 대한 설명은 이 명령에 대한 설명서를 참조하세요.

> [!Note]
> SQL API를 사용하는 경우 **리소스 별 내보내기** 속성을 **true** 로 설정하기를 권합니다.

   ```azurecli-interactive
   az monitor diagnostic-settings create --resource /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/ --name {DIAGNOSTIC_SETTING_NAME} --export-to-resource-specific true --logs '[{"category": "QueryRuntimeStatistics","categoryGroup": null,"enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]' --workspace /subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}"
   ```
## <a name="enable-full-text-query-for-logging-query-text"></a><a id="full-text-query"></a> 쿼리 텍스트 로깅에 전체 텍스트 쿼리 사용

> [!Note]
> 이 기능을 사용하도록 설정하면 추가 로깅 비용이 발생할 수 있습니다. 가격 세부 정보는 [Azure Monitor 가격](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요. 문제 해결 후 이 기능을 사용하지 않도록 설정하는 것이 좋습니다.

Azure Cosmos DB는 자세한 문제 해결을 위한 고급 로깅을 제공합니다. 전체 텍스트 쿼리를 사용하도록 설정하면, Azure Cosmos DB 계정 내의 모든 요청에 대해 난독 분석된 쿼리를 볼 수 있습니다.  또한 Azure Cosmos DB가 로그에 이 데이터에 액세스하고 이 데이터를 표면화할 수 있는 권한을 부여합니다. 

1. 이 기능을 사용하도록 설정하려면 Cosmos DB 계정의 `Features` 블레이드로 이동합니다.
   
   :::image type="content" source="./media/monitor-cosmos-db/full-text-query-features.png" alt-text="기능 블레이드로 이동합니다":::

2. `Enable`을 선택하면 이 설정이 다음 몇 분 내에 적용됩니다. 새로 검색된 모든 로그에는 각 요청에 대한 전체 텍스트 또는 PIICommand 텍스트가 있습니다.
   
    :::image type="content" source="./media/monitor-cosmos-db/select-enable-full-text.png" alt-text="전체 텍스트 사용을 선택합니다":::

새로 활성화된 이 기능을 사용하여 쿼리하는 방법을 알아보려면 [고급 쿼리](cosmos-db-advanced-queries.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* 리소스 관련 테이블을 쿼리하는 방법에 대한 자세한 정보는 [리소스 관련 테이블을 사용하여 문제 해결하는 방법](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries)을 참조하세요.

* AzureDiagnostics 테이블을 쿼리하는 방법에 대한 자세한 정보는 [AzureDiagnostics 테이블을 사용하여 문제 해결](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries)을 참조하세요.

* Azure Portal, CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 방법에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md) 문서를 참조하세요.
