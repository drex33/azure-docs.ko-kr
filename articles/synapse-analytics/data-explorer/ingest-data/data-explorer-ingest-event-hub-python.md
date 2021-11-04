---
title: Python 수집을 사용하여 Event Hub에서 Azure Synapse 데이터 탐색기(미리 보기)로 데이터 수집
description: Python을 사용하여 Event Hub에서 Azure Synapse 데이터 탐색기 데이터를 수집(로드)하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 64327836a5b2b9559699c4516cfaf4710b2754bb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482973"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-python-preview"></a>Python(미리 보기)을 사용하여 Azure Synapse 데이터 탐색기 대한 이벤트 허브 데이터 연결 만들기

> [!div class="op_single_selector"]
> * [포털](data-explorer-ingest-event-hub-portal.md)
> * [한 번 클릭](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

이 문서에서는 Python을 사용하여 Azure Synapse 데이터 탐색기 대한 이벤트 허브 데이터 연결을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [수집할 데이터가 있는 Event Hub](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).
- [Python 3.4 이상](https://www.python.org/downloads/).
<!-- - [Database and table policies](database-table-policies-python.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-package-python](../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Event Hub 데이터 연결 추가

다음 예에서는 프로그래밍 방식으로 Event Hub 데이터 연결을 추가하는 방법을 보여 줍니다. Azure Portal을 사용하여 Event Hub 데이터 연결을 추가하는 방법은 [이벤트 허브에 연결](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub)을 참조하세요.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | 리소스를 만드는 데 사용하는 구독 ID입니다.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 ID입니다.|
| client_secret | *xxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 암호입니다. |
| resource_group_name | *testrg* | 클러스터가 포함된 리소스 그룹의 이름입니다.|
| cluster_name | *mykustocluster* | 클러스터의 이름입니다.|
| database_name | *mykustodatabase* | 클러스터에 있는 대상 데이터베이스의 이름입니다.|
| data_connection_name | *myeventhubconnect* | 원하는 데이터 연결 이름입니다.|
| table_name | *StormEvents* | 대상 데이터베이스의 대상 테이블 이름입니다.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 대상 테이블과 관련된 열 매핑의 이름입니다.|
| data_format | *csv* | 메시지의 데이터 형식입니다.|
| event_hub_resource_id | *리소스 ID* | 수집할 데이터를 보유하는 Event Hub의 리소스 ID입니다. |
| consumer_group | *$Default* | Event Hub의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../includes/data-explorer-data-connection-clean-resources-python.md)]

## <a name="next-steps"></a>다음 단계

- [데이터 탐색기 사용하여 분석](../../get-started-analyze-data-explorer.md)
- [데이터 탐색기 풀 모니터링](../data-explorer-monitor-pools.md)
