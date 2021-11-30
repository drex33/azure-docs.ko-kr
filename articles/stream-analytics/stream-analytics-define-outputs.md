---
title: Azure Stream Analytics의 출력
description: 이 문서에서는 Azure Stream Analytics에 사용할 수 있는 데이터 출력 옵션을 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 26b37079eb4a397f5a4d9463cfa59ed6f18b40f0
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133302584"
---
# <a name="outputs-from-azure-stream-analytics"></a>Azure Stream Analytics의 출력

Azure Stream Analytics 작업은 입력, 쿼리 및 출력으로 구성됩니다. 변환된 데이터를 보낼 수 있는 몇 가지 출력 형식이 있습니다. 이 문서에는 지원되는 Stream Analytics 출력이 나와 있습니다. Stream Analytics 쿼리를 설계하는 경우 [INTO 절](/stream-analytics-query/into-azure-stream-analytics)을 사용하여 출력의 이름을 참조합니다. 여러 INTO 절을 쿼리에 추가하여 작업당 단일 출력 또는 스트리밍 작업당 여러 출력(필요한 경우)을 사용할 수 있습니다.

Stream Analytics 작업 출력을 만들고, 편집하고, 테스트하려면 [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), [REST API](/rest/api/streamanalytics/) 및 [Visual Studio](stream-analytics-quick-create-vs.md)를 사용할 수 있습니다.

일부 출력 형식은 처리량을 최적화하기 위해 [분할](#partitioning) 및 다양한 [출력 일괄 처리 크기](#output-batch-size)를 지원합니다. 다음 표에서는 각 출력 형식에 대해 지원되는 기능을 보여줍니다.

| 출력 형식 | 분할 | 보안 | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|예|Azure Active Directory 사용자 </br> , 관리 ID|
|[Azure Database Explorer](azure-database-explorer-output.md)|예|관리 ID|
|[Azure Database for PostgreSQL](postgresql-database-output.md)|예|사용자 이름 및 암호 인증|
|[Azure SQL Database](sql-database-output.md)|예. 선택 사항입니다.|SQL 사용자 인증, </br> 관리 ID|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|예|SQL 사용자 인증, </br> 관리 ID(미리 보기)|
|[Blob Storage 및 Azure Data Lake Gen2](blob-storage-azure-data-lake-gen2-output.md)|예|액세스 키, </br> 관리 ID|
|[Azure Event Hubs](event-hubs-output.md)|예. 출력 구성에서 파티션 키 열을 설정해야 합니다.|액세스 키, </br> 관리 ID|
|[Power BI](power-bi-output.md)|예|Azure Active Directory 사용자, </br> 관리 ID|
|[Azure Table Storage](table-storage-output.md)|예|계정 키|
|[Azure Service Bus 큐](service-bus-queues-output.md)|예|액세스 키|
|[Azure Service Bus 토픽](service-bus-topics-output.md)|예|액세스 키|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|예|액세스 키|
|[Azure Functions](azure-functions-output.md)|예|액세스 키|

## <a name="partitioning"></a>분할

Stream Analytics는 Power BI를 제외한 모든 출력에 대한 파티션을 지원합니다. 파티션 키 및 출력 기록기 수에 대한 자세한 내용은 관심 있는 특정 출력 유형에 대한 문서를 참조하세요. 모든 출력 문서는 이전 섹션에서 연결됩니다.  

또한 파티션의 고급 튜닝을 위해 쿼리에서 `INTO <partition count>`([INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 참조) 절을 사용하여 출력 기록기 수를 제어할 수 있으며, 이는 원하는 작업 토폴로지를 달성하는 데 도움이 될 수 있습니다. 출력 어댑터가 분할되지 않은 경우 한 입력 파티션에 데이터가 없으면 지연 도착 시간까지 지연될 수 있습니다. 이러한 경우 출력이 단일 기록기에 병합되어 파이프라인에서 병목 현상이 발생할 수 있습니다. 지연 도착 정책에 대해 알아보려면 [Azure Stream Analytics 이벤트 순서 고려 사항](./stream-analytics-time-handling.md)을 참조하세요.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

모든 출력은 일괄 처리를 지원하지만 일부는 일괄 처리 크기를 명시적으로 지원합니다. Azure Stream Analytics는 가변 크기 일괄 처리를 사용하여 이벤트를 처리하고 출력에 씁니다. 일반적으로 Stream Analytics 엔진은 효율성을 위해 한 번에 하나의 메시지를 쓰지 않고 일괄 처리를 사용합니다. 들어오는 이벤트와 나가는 이벤트의 비율이 높으면 Stream Analytics에서 더 큰 일괄 처리를 사용합니다. 송신 속도가 낮은 경우 대기 시간을 낮게 유지하기 위해 더 작은 일괄 처리를 사용합니다.

## <a name="parquet-output-batching-window-properties"></a>Parquet 출력 일괄 처리 창 속성

Azure Resource Manager 템플릿 배포 또는 REST API를 사용하는 경우 두 가지 일괄 처리 창 속성은 다음과 같습니다.

1. *timeWindow*

   일괄 처리당 최대 대기 시간입니다. 값은 시간 간격의 문자열이어야 합니다. 예를 들어 2분은 "00:02:00"입니다. 이 시간이 지나면 최소 행 수 요구 사항이 충족되지 않아도 일괄 처리가 출력에 기록됩니다. 기본값은 1분이며 허용되는 최대값은 2시간입니다. Blob 출력에 경로 패턴 빈도가 있는 경우 대기 시간은 파티션 시간 범위보다 클 수 없습니다.

2. *sizeWindow*

   일괄 처리당 최소 행 수입니다. Parquet의 경우 일괄 처리마다 새 파일을 만듭니다. 현재 기본값은 2,000개 행이고, 허용되는 최댓값은 10,000개 행입니다.

이러한 일괄 처리 창 속성은 API 버전 **2017-04-01-미리 보기** 에서만 지원됩니다. 다음은 REST API 호출에 대한 JSON 페이로드의 예제입니다.

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
