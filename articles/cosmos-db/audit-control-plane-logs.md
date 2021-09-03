---
title: Azure Cosmos DB 컨트롤 플레인 작업을 감사하는 방법
description: Azure Cosmos DB에서 지역 추가, 처리량 업데이트, 지역 장애 조치(failover), VNet 추가 등의 컨트롤 플레인 작업을 감사하는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/13/2021
ms.author: sngun
ms.openlocfilehash: 3ce2d934c335099d07bbe5621a8aa363bf97583c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530070"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure Cosmos DB 컨트롤 플레인 작업을 감사하는 방법
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB에서 컨트롤 플레인은 Azure Cosmos 계정으로 다양한 작업을 수행할 수 있게 해주는 RESTful 서비스입니다. 리소스 모델에 대해 작업을 수행할 수 있도록 공용 리소스 모델(예: 데이터베이스, 계정) 및 여러 작업을 최종 사용자에게 노출합니다. 컨트롤 플레인 작업에는 Azure Cosmos 계정 또는 컨테이너에 대한 변경 내용이 포함됩니다. 예를 들어 Azure Cosmos 계정 만들기, 지역 추가, 처리량 업데이트, 지역 장애 조치(failover), VNet 추가 등의 작업은 컨트롤 플레인 작업에 포함됩니다. 이 문서에서는 Azure Cosmos DB에서 컨트롤 플레인 작업을 감사하는 방법을 설명합니다. Azure Cosmos 계정에서는 Azure CLI, PowerShell 또는 Azure Portal을 사용하고, 컨테이너의 경우에는 Azure CLI 또는 PowerShell을 사용하여 컨트롤 플레인 작업을 실행할 수 있습니다.

다음은 컨트롤 플레인 작업 감사가 유용한 몇 가지 예제 시나리오입니다.

* Azure Cosmos 계정의 방화벽 규칙이 수정되었을 때 알림을 표시합니다. 이 알림은 Azure Cosmos 계정의 네트워크 보안을 제어하는 규칙이 허가 없이 수정되었을 때 이를 찾아내고 빠르게 조치를 취하기 위해 필요합니다.

* Azure Cosmos 계정에서 새 지역이 추가 또는 제거될 경우 알림을 표시합니다. 지역을 추가하거나 제거하면 청구 및 데이터 주권 요구 사항에 영향을 줍니다. 이 경고는 계정에서 실수로 인한 지역 추가 또는 제거를 감지하는 데 도움이 됩니다.

* 진단 로그에서 변경된 내용에 대해 더 자세한 정보를 확인할 수 있습니다. 예를 들어 VNet이 변경되었습니다.

## <a name="disable-key-based-metadata-write-access"></a>키 기반 메타데이터 쓰기 액세스 사용 안 함

Azure Cosmos DB에서 컨트롤 플레인 작업을 감사하기 전에 계정에 대해 키 기반 메타데이터 쓰기 액세스를 사용하지 않도록 설정합니다. 키 기반 메타데이터 쓰기 액세스가 사용하지 않도록 설정되었으면 계정 키를 통해 Azure Cosmos 계정에 연결하는 클라이언트가 계정에 액세스할 수 없습니다. `disableKeyBasedMetadataWriteAccess` 속성을 true로 설정하여 쓰기 액세스를 사용하지 않도록 설정할 수 있습니다. 이 속성을 설정한 후에는 적절한 Azure 역할 및 자격 증명이 있는 사용자로부터 리소스 변경이 발생할 수 있습니다. 이 속성을 설정하는 방법은 [SDK 변경 방지](role-based-access-control.md#prevent-sdk-changes) 문서를 참조하세요. 

`disableKeyBasedMetadataWriteAccess`가 설정된 후 SDK 기반 클라이언트가 만들기 또는 업데이트 작업을 실행하면 *“’ContainerNameorDatabaseName’ 리소스에 대한 ‘POST’ 작업이 Azure Cosmos DB 엔드포인트를 통해 허용되지 않음”* 오류가 반환됩니다. 계정에 대해 이러한 작업 액세스를 설정하거나 Azure Resource Manager, Azure CLI 또는 Azure PowerShell을 통해 만들기/업데이트 작업을 수행해야 합니다. 다시 전환하려면 [Cosmos SDK에서 변경 방지](role-based-access-control.md#prevent-sdk-changes) 문서에 설명된 대로 Azure CLI를 사용하여 disableKeyBasedMetadataWriteAccess를 **false** 로 설정합니다. `disableKeyBasedMetadataWriteAccess` 값을 true 대신 false로 변경해야 합니다.

메타데이터 쓰기 액세스를 해제할 때 다음 사항을 고려하세요.

* SDK 또는 계정 키를 사용하여 위 리소스(예: 컬렉션 만들기, 처리량 업데이트 등)를 변경하는 메타데이터 호출이 애플리케이션에서 수행되지 않는지 평가하고 확인합니다.

* 현재까지 Azure Portal은 메타데이터 작업에 계정 키를 사용하므로, 이러한 작업이 차단됩니다. 또는 Azure CLI, SDK 또는 Resource Manager 템플릿 배포를 사용하여 이러한 작업을 수행합니다.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>컨트롤 플레인 작업에 진단 로그 사용

Azure Portal을 사용하여 컨트롤 플레인 작업에 대해 진단 로그를 사용하도록 설정할 수 있습니다. 사용하도록 설정한 후 진단 로그는 관련 세부 정보와 함께 시작 및 완료 이벤트 쌍으로 작업을 기록합니다. 예를 들어 *RegionFailoverStart* 및 *RegionFailoverComplete* 로 지역 장애 조치(failover) 이벤트를 완료합니다.

다음 단계에 따라 컨트롤 플레인 작업에 대해 로깅을 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Cosmos 계정으로 이동합니다.

1. **진단 설정** 창을 열고 만들려는 로그 **이름** 을 제공합니다.

1. 로그 유형으로 **ControlPlaneRequests** 를 선택하고 **Log Analytics에 보내기** 옵션을 선택합니다.

또한 스토리지 계정 또는 스트림의 로그를 이벤트 허브에 저장할 수 있습니다. 이 문서에서는 Log Analytics에 로그를 보낸 후 이를 쿼리하는 방법을 보여줍니다. 사용하도록 설정한 후에는 진단 로그가 적용될 때까지 몇 분 정도 걸립니다. 해당 시점 이후에 수행되는 모든 컨트롤 플레인 작업이 추적될 수 있습니다. 다음 스크린샷은 컨트롤 플레인 로그를 사용하도록 설정하는 방법을 보여줍니다.

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="컨트롤 플레인 요청 로깅 사용":::

## <a name="view-the-control-plane-operations"></a>컨트롤 플레인 작업 보기

로깅을 설정한 후 다음 단계에 따라 특정 계정의 작업을 추적합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 탐색에서 **모니터** 탭을 연 후 **로그** 창을 선택합니다. 해당 범위에서 특정 계정으로 쿼리를 쉽게 실행할 수 있는 UI가 열립니다. 다음 쿼리를 실행하여 컨트롤 플레인 로그를 확인합니다.

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

   다음 스크린샷은 Azure Cosmos 계정에 대해 일관성 수준이 변경될 때 로그를 캡처합니다. `activityId_g` 값 결과는 작업의 활동 ID와 다릅니다.

   :::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="VNet이 추가될 때 컨트롤 플레인 로그":::

   다음 스크린샷은 Cassandra 계정의 키스페이스 또는 테이블이 생성될 때 그리고 처리량이 업데이트될 때 로그를 캡처합니다. 데이터베이스 및 컨테이너에서 만들기 및 업데이트 작업의 컨트롤 플레인 로그는 다음 스크린샷에 표시된 것처럼 개별적으로 기록됩니다.

   :::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="처리량이 업데이트될 때 컨트롤 플레인 로그":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>특정 작업과 연관된 ID 식별

추가로 디버그하려면 `activityId_g` 또는 작업의 타임스탬프를 사용하여 **활동 로그** 에서 특정 작업을 식별할 수 있습니다. 타임스탬프는 활동 ID가 명시적으로 전달되지 않은 일부 Resource Manager 클라이언트에 사용됩니다. 활동 로그는 해당 작업이 시작된 ID에 대한 세부 정보를 제공합니다. 다음 스크린샷은 `activityId_g`를 사용하여 활동 로그에서 관련된 작업을 찾는 방법을 보여줍니다.

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="활동 ID 사용 및 작업 찾기":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Azure Cosmos 계정의 컨트롤 플레인 작업

다음은 계정 수준에서 사용 가능한 컨트롤 플레인 작업입니다. 대부분의 작업은 계정 수준으로 추적됩니다. 이러한 작업은 Azure Monitor에서 메트릭으로 제공됩니다.

* 지역 추가
* 지역 제거
* 계정 삭제
* 지역 장애 조치(failover)
* 만든 계정
* 가상 네트워크 삭제
* 계정 네트워크 설정 업데이트
* 계정 복제 설정 업데이트
* 계정 키 업데이트
* 계정 백업 설정 업데이트
* 계정 진단 설정 업데이트

## <a name="control-plane-operations-for-database-or-containers"></a>데이터베이스 또는 컨테이너에 대한 컨트롤 플레인 작업

다음은 데이터베이스 및 컨테이너 수준에서 사용 가능한 컨트롤 플레인 작업입니다. 이러한 작업은 Azure Monitor에서 메트릭으로 제공됩니다.

* SQL 데이터베이스 만들기
* SQL 데이터베이스 업데이트
* SQL 데이터베이스 처리량 업데이트
* SQL 데이터베이스 삭제
* SQL 컨테이너 만들기
* SQL 컨테이너 업데이트
* SQL 컨테이너 처리량 업데이트
* SQL 컨테이너 삭제
* Cassandra 키스페이스 만들기
* Cassandra 키스페이스 업데이트
* Cassandra 키스페이스 처리량 업데이트
* Cassandra 키스페이스 삭제
* Cassandra 테이블 만들기
* Cassandra 테이블 업데이트
* Cassandra 테이블 처리량 업데이트
* Cassandra 테이블 삭제
* Gremlin 데이터베이스 만들기
* Gremlin 데이터베이스 업데이트
* Gremlin 데이터베이스 처리량 업데이트
* Gremlin 데이터베이스 삭제
* Gremlin 그래프 만들기
* Gremlin 그래프 업데이트
* Gremlin 그래프 처리량 업데이트
* Gremlin 그래프 삭제
* Mongo 데이터베이스 만들기
* Mongo 데이터베이스 업데이트
* Mongo 데이터베이스 처리량 업데이트
* Mongo 데이터베이스 삭제
* Mongo 컬렉션 만들기
* Mongo 컬렉션 업데이트
* Mongo 컬렉션 처리량 업데이트
* Mongo 컬렉션 삭제
* AzureTable 테이블 만들기
* AzureTable 테이블 업데이트
* AzureTable 테이블 처리량 업데이트
* AzureTable 테이블 삭제

## <a name="diagnostic-log-operations"></a>진단 로그 작업

다음은 다른 작업에 대해 진단 로그에 있는 작업 이름입니다.

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

API 특정 작업의 경우 다음 형식으로 작업 이름이 지정됩니다.

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "Throughput" + operationType

**예제** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

*ResourceDetails* 속성에는 요청 페이로드로 전체 리소스 본문이 포함되고 업데이트하도록 요청된 모든 속성이 포함됩니다.

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>컨트롤 플레인 작업의 진단 로그 쿼리

다음은 컨트롤 플레인 작업에 대해 진단 로그를 가져오기 위한 몇 가지 예제입니다.

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersThroughputUpdate"
```

컨테이너 삭제 작업을 시작한 호출자 및 activityId를 가져오기 위한 쿼리:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

인덱스 또는 ttl 업데이트를 가져오기 위한 쿼리입니다. 그런 후 이 쿼리의 결과를 이전 업데이트와 비교하여 인덱스 또는 ttl의 변경 사항을 확인할 수 있습니다.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**출력:**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB용 Azure Monitor 살펴보기](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그](use-metrics.md)
