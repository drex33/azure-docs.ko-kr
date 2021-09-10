---
title: Azure Queue Storage 모니터링 모범 사례
description: 모범 사례 지침 및 메트릭과 로그를 사용하여 Azure Queue Storage를 모니터링하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.author: normesta
ms.date: 08/24/2021
ms.custom: monitoring
ms.openlocfilehash: e7344330740bebc41a49547c7602a852b5f7665f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122832356"
---
# <a name="best-practices-for-monitoring-azure-queue-storage"></a>Azure Queue Storage 모니터링 모범 사례

이 문서에서는 일반적인 Queue Storage 모니터링 시나리오 컬렉션을 제공하고 이를 수행하기 위한 모범 사례 지침을 제공합니다.  

## <a name="monitor-message-counts-in-each-queue"></a>각 큐의 메시지 수 모니터링 

`QueueMessageCount` 메트릭을 사용하여 스토리지 계정의 모든 큐에 대한 메시지 수를 모니터링할 수 있습니다. 이 메트릭은 매일 새로 고쳐집니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하는 경우, 다음과 유사한 명령을 사용할 수 있습니다.  

```powershell
(Get-AzMetric -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/queueServices/default -MetricName "QueueMessageCount").data.Average
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 사용하는 경우, 다음과 유사한 명령을 사용할 수 있습니다. 

```azurecli
az monitor metrics list --resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/ --metric "QueueMessageCount" --interval PT1H --query value[0].timeseries[0].data[0].average
```

---

메시지 볼륨을 처리하도록 워크로드를 조정할지 여부를 동적으로 결정해야 하는 경우, 각 큐에서 대략적인 메시지 수를 쿼리한 다음 적절한 작업으로 응답할 수 있습니다. [큐 메타데이터 가져오기](/rest/api/storageservices/get-queue-metadata) REST 작업을 사용하거나, 지원되는 Blob Storage SDK를 사용하여 대략적인 메시지 수를 가져옵니다. 

다음 예제에서는 Azure Storage .NET v12 라이브러리를 사용하여 대략적인 메시지 수를 가져옵니다.

```csharp
        static async Task<string> RetrieveNextMessageAsync(QueueClient theQueue)
        {
            if (await theQueue.ExistsAsync())
            {
                QueueProperties properties = await theQueue.GetPropertiesAsync();

                if (properties.ApproximateMessagesCount > 0)
                {
                    QueueMessage[] retrievedMessage = await theQueue.ReceiveMessagesAsync(1);
                    string theMessage = retrievedMessage[0].MessageText;
                    await theQueue.DeleteMessageAsync(retrievedMessage[0].MessageId, retrievedMessage[0].PopReceipt);
                    return theMessage;
                }

                return null;
            }

            return null;
        }
```

엔터티별 메시지를 지원하는 Service Bus를 사용하는 것도 좋습니다. 자세한 내용은 [Azure Service Bus 데이터 참조 모니터링](../../service-bus-messaging/monitor-service-bus-reference.md)을 참조하세요. 

## <a name="audit-account-activity"></a>계정 활동 감사

대부분의 경우, 보안 및 규정 준수를 위해 스토리지 계정의 활동을 감사해야 합니다. 스토리지 계정에 대한 작업은 *컨트롤 플레인* 과 *데이터 평면* 의 두 가지 범주로 나뉩니다. 

컨트롤 플레인 작업은 스토리지 계정을 만들거나 기존 스토리지 계정의 속성을 업데이트하기 위한 모든 Azure Resource Manager 요청입니다. 자세한 내용은 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)를 참조하세요. 

데이터 평면 작업은 스토리지 서비스 엔드포인트에 대한 요청 때문에 발생하는 스토리지 계정의 데이터에 대한 작업입니다. 예를 들어, 큐에 메시지를 추가할 때 데이터 평면 작업이 실행됩니다. 자세한 내용은 [Azure Storage API](/rest/api/storageservices/)를 참조하세요. 

이 섹션에서는 컨트롤 플레인 및 데이터 평면 작업의 “언제”, “누가”, “무엇을”, “어떻게” 정보를 식별하는 방법을 보여줍니다.

### <a name="auditing-control-plane-operations"></a>컨트롤 플레인 작업 감사

Resource Manager 작업은 [Azure 활동 로그](../../azure-monitor/essentials/activity-log.md)에 캡처됩니다. 활동 로그를 보려면 Azure Portal에서 스토리지 계정을 연 다음, **활동 로그** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![활동 로그](./media/queues-storage-monitoring-scenarios/activity-log.png)


모든 로그 항목을 열어 활동을 설명하는 JSON을 표시합니다. 다음 JSON은 컨트롤 플레인 작업의 “언제”, “무엇을”, “어떻게” 정보를 보여줍니다.

> [!div class="mx-imgBorder"]
> ![활동 로그 JSON](./media/queues-storage-monitoring-scenarios/activity-log-json.png)

“누가” 정보의 가용성은 컨트롤 플레인 작업을 수행하는 데 사용된 인증 메서드에 따라 다릅니다. Azure AD 보안 주체가 권한 부여를 수행한 경우 해당 보안 주체의 개체 식별자도 이 JSON 출력에 나타납니다(예: `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). 이메일 주소나 이름과 같은 기타 ID 관련 정보가 항상 표시되는 것은 아니므로 개체 식별자는 항상 보안 주체를 고유하게 식별하는 가장 좋은 방법입니다. 

개체 식별자 값을 사용하고 Azure Portal의 Azure AD 페이지에서 보안 주체를 검색하여 친숙한 해당 보안 주체의 이름을 찾을 수 있습니다. 다음 스크린샷에서는 Azure AD의 검색 결과를 보여줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Active Directory 검색](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>데이터 평면 작업 감사

데이터 평면 작업은 [스토리지용 Azure 리소스 로그](monitor-queue-storage.md#analyzing-logs)에 캡처됩니다. 기본 쿼리 환경을 위해 Log Analytics 작업 영역으로 로그를 내보내도록 [진단 설정을 구성](monitor-queue-storage.md#send-logs-to-azure-log-analytics)할 수 있습니다. 

다음은 로그 항목 목록에서 “언제”, “누가”, “무엇을”, “어떻게” 정보를 검색하는 Log Analytics 쿼리입니다. 

```kusto
StorageQueueLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

감사의 “언제” 부분에서 `TimeGenerated` 필드는 로그 항목이 기록된 시간을 보여줍니다. 

감사의 “무엇” 부분에서 `Uri` 필드는 항목을 수정했거나 읽었는지 보여줍니다. 

감사의 “어떻게” 부분에서 `OperationName` 필드는 실행된 작업을 보여줍니다. 

감사의 “누구” 부분에서 `AuthenticationType`은 요청에 사용된 인증 유형을 보여줍니다. 이 필드는 계정 키, SAS 토큰 또는 Azure AD(Azure Active Directory) 인증 사용을 포함하여 Azure Storage가 지원하는 인증 유형을 표시할 수 있습니다. 

요청이 Azure AD를 사용하여 인증된 경우 `RequesterObjectId` 필드는 보안 주체를 식별하는 가장 안정적인 방법을 제공합니다. `RequesterObjectId` 필드를 사용하고 Azure Portal의 Azure AD 페이지에서 보안 주체를 검색하여 친숙한 해당 보안 주체의 이름을 찾을 수 있습니다. 다음 스크린샷에서는 Azure AD의 검색 결과를 보여줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Active Directory-2 검색](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

경우에 따라 사용자 보안 주체 이름 또는 *UPN* 이 로그에 나타날 수 있습니다. 예를 들어, 보안 주체가 Azure AD 사용자이면 UPN이 표시될 수 있습니다. 사용자가 할당한 관리 ID와 같은 다른 보안 주체 유형의 경우 또는 교차 Azure AD 테넌트 인증과 같은 특정 시나리오에서는 UPN이 로그에 표시되지 않습니다. 

이 쿼리는 OAuth 보안 주체가 수행한 모든 쓰기 작업을 보여줍니다.

```kusto
StorageQueueLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutMessage"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

공유 키 및 SAS 인증에서는 개별 ID를 감사할 수단을 제공하지 않습니다. 따라서 ID 기반 감사 기능을 개선하려면 Azure AD로 전환하고 공유 키와 SAS 인증을 방지하는 것이 좋습니다. 공유 키와 SAS 인증을 방지하는 방법을 알아보려면 [Azure Storage 계정에 대한 공유 키 인증 방지](../common/shared-key-authorization-prevent.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json&tabs=portal)를 참조하세요. Azure AD를 시작하려면 [Azure Active Directory를 사용하여 blob에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)를 참조하세요

## <a name="optimize-cost-for-infrequent-queries"></a>자주 발생하지 않는 쿼리 비용 최적화

풍부한 네이티브 쿼리 기능을 위해 로그를 Log Analytics로 내보낼 수 있습니다. 스토리지 계정에 많은 트랜잭션이 있는 경우, Log Analytics에서 로그를 사용하는 비용이 높을 수 있습니다. [Azure Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요. 가끔 로그를 쿼리하려는 경우(예: 준수 감사에 대한 로그 쿼리), 로그를 스토리지 계정으로 내보낸 다음, 로그 데이터 위에 서버리스 쿼리 솔루션을 사용하여 총 비용을 절감할 수 있습니다(예: Azure Synapse).

Azure Synapse를 사용하면 서버리스 SQL 풀을 만들어 필요할 때 로그 데이터를 쿼리할 수 있습니다. 이렇게 하면 비용을 크게 절감할 수 있습니다. 

1. 스토리지 계정으로 로그를 내보냅니다. [진단 설정 만들기](monitor-queue-storage.md#creating-a-diagnostic-setting)를 참조하세요.

2. Synapse 작업 영역을 만들고 구성합니다. [빠른 시작: Synapse 작업 영역 만들기](../../synapse-analytics/quickstart-create-workspace.md)를 참조하세요.

2. 로그를 쿼리합니다. [Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 JSON 파일 쿼리](../../synapse-analytics/sql/query-json-files.md)를 참조하세요.

   예를 들면 다음과 같습니다.

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,    
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>참고 항목

- [Azure Queue Storage 모니터링](monitor-queue-storage.md).
- [Azure Queue Storage 모니터링 데이터 참조](monitor-queue-storage-reference.md)
- [자습서: Azure Data Explorer 및 Azure Monitor에서 Kusto 쿼리 사용](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).
- [Azure Monitor에서 로그 쿼리 시작](../../azure-monitor/logs/get-started-queries.md).

  

