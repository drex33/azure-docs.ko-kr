---
title: Azure Storage Analytics 메트릭(클래식) 사용 및 관리 | Microsoft Docs
description: Azure Storage Analytics 메트릭을 활성화, 편집 및 보는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-azurepowershell
ms.openlocfilehash: 3dd0f137a35bc6b505dbc81b509cf345c561104b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551834"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics 메트릭(클래식) 사용 및 관리

[Azure Storage Analytics](storage-analytics.md)는 Blob, 큐 및 테이블에 대한 모든 스토리지에 대한 메트릭을 제공합니다. [Azure Portal](https://portal.azure.com)을 사용하여 계정에 기록되는 메트릭을 구성하고, 메트릭 데이터를 시각적으로 표현하는 차트를 구성할 수 있습니다. 이 문서에서는 메트릭을 활성화하고 관리하는 방법을 보여줍니다. 로그를 사용하도록 설정하는 방법을 알아보려면 [Azure Storage Analytics 로그(클래식) 사용 및 관리](manage-storage-analytics-logs.md)를 참조하세요.

[스토리지용 Azure Monitor](./storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)(미리 보기)를 검토하는 것이 좋습니다. 이는 Azure Monitor의 기능으로 Azure Storage 서비스 성능, 용량 및 가용성에 대한 통합 보기를 제공하여 Azure Storage 계정의 포괄적인 모니터링을 제공합니다. 사용 설정이나 구성이 전혀 필요하지 않으며, 미리 정의된 대화형 차트 및 포함된 기타 시각화에서 이러한 메트릭을 즉시 볼 수 있습니다.

> [!NOTE]
> Azure Portal에서 모니터링 데이터를 검사하는 데 관련된 비용이 있습니다. 자세한 정보는 [스토리지 분석](storage-analytics.md)을 참조하세요.
>
> 프리미엄 성능 블록 Blob Storage 계정은 Storage Analytics 메트릭을 지원하지 않습니다. 프리미엄 성능 블록 Blob 스토리지 계정을 사용하여 메트릭을 보려면 [Azure Monitor에서 Azure Storage 메트릭](../blobs/monitor-blob-storage.md)을 사용하는 것이 좋습니다.
>
> 스토리지 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>메트릭 사용

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 **Storage 계정**, Storage 계정 이름을 차례로 선택하여 계정 대시보드를 엽니다.

2. 메뉴 블레이드의 **모니터링(클래식)** 섹션에서 **진단 설정(클래식)** 을 선택합니다.

   ![모니터링(클래식) 섹션 아래의 진단 설정(클래식) 옵션을 강조 표시하는 스크린샷.](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. 모니터링하려는 각 **서비스** 에 대한 메트릭 데이터 **유형** 과 데이터에 대한 **보존 정책** 을 선택합니다. 또한 **상태** 를 **해제**(Off)로 설정하여 모니터링을 사용하지 않도록 설정할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![Azure Portal에서 로깅 구성](./media/manage-storage-analytics-logs/enable-diagnostics.png)

   데이터 보존 정책을 설정하려면 1-365일 중에서 **보존(일)** 슬라이더를 이동하거나 데이터 보존 기간(일)을 입력합니다. 새 스토리지 계정의 기본값은 7일입니다. 보존 정책을 설정하지 않으려면 0을 입력합니다. 보존 정책이 없는 경우 언제든 모니터링 데이터를 삭제할 수 있습니다.

   > [!WARNING]
   > 메트릭은 계정에 데이터로 저장됩니다. 메트릭 데이터는 시간이 지남에 따라 계정에 누적되어 스토리지 비용을 가중시킬 수 있습니다. 단기간에만 메트릭 데이터가 필요한 경우, 데이터 보존 정책을 수정하여 비용을 줄일 수 있습니다. 오래된 메트릭 데이터(보존 정책의 기간을 초과한 데이터)는 시스템에서 삭제됩니다. 계정에 대한 메트릭 데이터를 얼마나 오래 보존하고자 하는지에 따라 보존 정책을 설정하는 것이 좋습니다. 자세한 내용은 [스토리지 메트릭에 대한 청구](storage-analytics-metrics.md#billing-on-storage-metrics)를 참조하세요.
   >

4. 모니터링 구성을 완료하면 **저장** 을 선택합니다.

기본 메트릭 세트는 **메트릭(클래식) 블레이드** 뿐만 아니라 **개요** 블레이드의 차트에 표시됩니다. 서비스에 대한 메트릭을 사용하도록 설정하면 차트에 데이터를 표시하는 데 최대 1시간이 걸릴 수 있습니다. 메트릭 차트에서 **편집** 을 선택하여 차트에 표시되는 메트릭을 구성할 수 있습니다.

**상태** 를 **해제** 로 설정하여 메트릭 수집 및 로깅을 사용하지 않도록 설정할 수 있습니다.

> [!NOTE]
> Azure Storage는 [테이블 스토리지](storage-introduction.md#table-storage)를 사용하여 스토리지 계정에 대한 메트릭을 저장하고, 계정의 테이블에 메트릭을 저장합니다. 자세한 내용은 다음을 참조하십시오. [메트릭 저장 방법](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell 명령 창을 엽니다.

2. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

3. ID가 둘 이상의 구독과 연결되어 있으면 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

5. 사용하려는 스토리지 계정을 정의하는 스토리지 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

6. 로컬 머신에서 PowerShell을 사용하여 스토리지 계정에서 스토리지 메트릭을 구성할 수 있습니다. Azure PowerShell cmdlet **Set-AzStorageServiceMetricsProperty** 를 사용하여 현재 설정을 변경합니다.

   다음 명령은 보존 기간을 5일로 설정된 스토리지 계정의 Blob 서비스에 대해 분 메트릭을 설정합니다.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```

   이 cmdlet은 다음 매개 변수를 사용합니다.

   - **ServiceType**: 가능한 값은 **Blob**, **Queue**, **Table** 및 **File** 입니다.
   - **MetricsType**: 가능한 값은 **Hour** 및 **Minute** 입니다.
   - **MetricsLevel**: 가능한 값은 다음과 같습니다.
      - **없음**: 모니터링을 해제합니다.
      - **서비스**: 수신 및 송신, 가용성, 대기 시간, 성공 비율 등 Blob, 큐, 테이블 및 파일 서비스에 대해 집계되는 메트릭을 수집합니다.
      - **ServiceAndApi**: 서비스 메트릭뿐 아니라 Azure Storage 서비스 API의 각 스토리지 작업에 대한 동일한 메트릭 집합을 수집합니다.

   다음 명령은 기본 스토리지 계정의 Blob 서비스에 대해 현재 시간 메트릭 수준 및 보존 기간(일)을 검색합니다.

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```

   Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 스토리지 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

.NET 언어를 사용하여 스토리지 메트릭을 구성하는 방법에 대한 자세한 내용은 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)를 참조하세요.

REST API를 사용하여 스토리지 메트릭을 구성하는 방법에 대한 일반적인 내용은 [스토리지 분석 설정 및 구성](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)을 참조하세요.

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;

queueClient.SetServiceProperties(serviceProperties);  
```

.NET 언어를 사용하여 스토리지 메트릭을 구성하는 방법에 대한 자세한 내용은 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)를 참조하세요.

REST API를 사용하여 스토리지 메트릭을 구성하는 방법에 대한 일반적인 내용은 [스토리지 분석 설정 및 구성](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)을 참조하세요.

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>차트에서 메트릭 보기

Storage 계정을 모니터링하도록 스토리지 분석 메트릭을 구성하면 스토리지 분석에서 Storage 계정의 알려진 테이블 집합에 메트릭을 기록합니다. [Azure Portal](https://portal.azure.com)에서 시간별 메트릭을 보도록 차트를 구성할 수 있습니다.

다음 절차를 사용하여 메트릭 차트에 표시할 스토리지 메트릭을 선택합니다.

1. Azure Portal에서 스토리지 메트릭 차트를 표시하여 시작합니다. **스토리지 계정 블레이드** 및 **메트릭(클래식)** 블레이드에서 차트를 찾을 수 있습니다.

   여기서는 **스토리지 계정 블레이드** 에 나타나는 다음과 같은 차트를 사용합니다.

   ![Azure Portal에서 차트 선택](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. 차트를 편집하려면 차트 내의 아무 곳이나 클릭합니다.

3. 다음으로, 차트에 표시할 메트릭의 **시간 범위** 와 표시하려는 메트릭의 **서비스**(Blob, 큐, 테이블, 파일)를 선택합니다. 여기서는 다음과 같이 Blob 서비스에 대해 지난 주의 메트릭을 표시하도록 선택했습니다.

   ![차트 편집 블레이드의 시간 범위 및 서비스 선택](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. 차트에 표시하려는 개별 **메트릭** 을 선택한 다음 **확인** 을 클릭합니다.

   ![차트 편집 블레이드의 개별 메트릭 선택](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

차트 설정은 스토리지 계정에서 모니터링 데이터의 컬렉션, 집계 또는 스토리지에 영향을 주지 않습니다.

#### <a name="metrics-availability-in-charts"></a>차트의 메트릭 가용성

사용 가능한 메트릭 목록은 드롭다운에서 선택한 서비스와 편집 중인 차트의 단위 유형에 따라 달라집니다. 예를 들어 단위를 백분율로 표시하는 차트를 편집하는 경우에만 *PercentNetworkError* 및 *PercentThrottlingError* 와 같은 백분율 메트릭을 선택할 수 있습니다.

![Azure Portal에서 오류 백분율 차트 요청](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>메트릭 해상도

**진단** 에서 선택한 메트릭에 따라 계정에서 사용할 수 있는 메트릭의 해상도가 결정됩니다.

- **집계** 모니터링은 수신/송신, 가용성, 대기 시간 및 성공 비율과 같은 메트릭을 제공합니다. 이러한 메트릭은 Blob, 테이블, 파일 및 큐 서비스에서 집계됩니다.
- **API당** 은 서비스 수준 집계 외에도 개별 스토리지 작업에 사용할 수 있는 메트릭과 함께 보다 세밀한 해상도를 제공합니다.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>로컬로 보관 또는 분석할 메트릭 다운로드

장기 스토리지용 메트릭을 다운로드하거나 메트릭을 로컬에서 분석하려는 경우에는 테이블을 읽는 코드를 작성하거나 도구를 사용해야 합니다. 스토리지 계정의 모든 테이블을 나열해도 테이블은 표시되지 않지만 테이블 이름을 통해 직접 액세스할 수는 있습니다. 스토리지 찾아보기 도구는 대체로 해당 테이블을 인식하며 테이블을 직접 보는 기능을 제공합니다. 사용 가능한 도구의 목록은 [Azure Storage 클라이언트 도구](./storage-explorers.md)를 참조하세요.

|메트릭|테이블 이름|참고|
|-|-|-|
|시간 메트릭|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|2013년 8월 15일 이전 버전에서는 해당 테이블을 다음과 같이 지칭했습니다.<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> 파일 서비스에 대한 메트릭은 2015년 4월 5일 버전부터 사용할 수 있습니다.|
|분 메트릭|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|PowerShell을 사용하거나 프로그래밍 방식으로만 사용하도록 설정할 수 있습니다.<br /><br /> 파일 서비스에 대한 메트릭은 2015년 4월 5일 버전부터 사용할 수 있습니다.|
|용량|$MetricsCapacityBlob|Blob service만 해당합니다.|

해당 테이블의 스키마에 대한 전체 세부 정보는 [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요. 다음 샘플 행에는 사용 가능한 열 중 일부만 나와 있습니다. 그러나 스토리지 메트릭에서 해당 메트릭을 저장하는 방식과 관련한 몇 가지 중요한 기능을 확인할 수 있습니다.

|PartitionKey|RowKey|타임스탬프|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|가용성|AverageE2ELatency|AverageServerLatency|PercentSuccess|
|-|-|-|-|-|-|-|-|-|-|-|
|20140522T1100|user;All|2014-05-22T11:01:16.765025Z|7|7|4003|46801|100|104.4286|6.857143|100|
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.764025Z|5|5|2694|45951|100|143.8|7.8|100|
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.765025Z|1|1|538|633|100|3|3|100|
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.765025Z|1|1|771|217|100|9|6|100|

분 메트릭 데이터의 이 예에서 파티션 키는 분 확인 시의 시간을 사용합니다. 행 키는 행에 저장되는 정보의 유형을 식별합니다. 정보는 액세스 형식 및 요청 형식으로 구성됩니다.

-   액세스 형식은 **user** 또는 **system** 입니다. 여기서 **user** 는 스토리지 서비스에 대한 모든 사용자 요청을 지칭하며 **system** 은 스토리지 분석에서 수행한 요청을 지칭합니다.
-   요청 형식은 **all**(이 경우 요약 줄)이거나 **QueryEntity** 또는 **UpdateEntity** 등의 특정 API를 식별합니다.

이 샘플 데이터는 오전 11시부터 1분 동안의 모든 레코드를 표시하므로 **QueryEntities** 요청의 수 + **QueryEntity** 요청의 수 + **UpdateEntity** 요청의 수 = 7입니다. 이 합계는 **user:All** 행에 표시됩니다. 마찬가지로 ((143.8 * 5) + 3 + 9)/7을 계산하여 **user:All** 행에 평균 엔드투엔드 대기 시간인 104.4286을 표시할 수 있습니다.

## <a name="view-metrics-data-programmatically"></a>프로그래밍 방식으로 메트릭 데이터 보기

다음 목록에서는 특정 시간(분) 범위에 대한 분 메트릭에 액세스하여 결과를 콘솔 창에 표시하는 샘플 C# 코드를 보여 줍니다. 코드 샘플은 스토리지의 메트릭 테이블 액세스를 간소화하는 **CloudAnalyticsClient** 클래스가 포함된 Azure Storage 클라이언트 라이브러리 버전 4.x 이상을 사용합니다.

> [!NOTE]
> **CloudAnalyticsClient** 클래스는 .NET용 Azure Blob 스토리지 클라이언트 라이브러리 v12에 포함되어 있지 않습니다. **2023년 8월 31일** 에 *클래식 메트릭* 이라고도 하는 스토리지 분석 메트릭이 사용 중지됩니다. 자세한 내용은 [공식 공지](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)를 참조하세요. 클래식 메트릭을 사용하는 경우 해당 날짜 이전에 Azure Monitor의 메트릭으로 전환하는 것이 좋습니다.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>포털 대시보드에 메트릭 차트 추가

스토리지 계정에 대한 Azure Storage 메트릭 차트를 포털 대시보드에 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 대시보드를 보면서 **대시보드 편집** 을 클릭합니다.
1. **타일 갤러리** 에서 **타일 찾기 기준** > **유형** 을 차례로 선택합니다.
1. **유형** > **스토리지 계정** 을 차례로 선택합니다.
1. **리소스** 에서 대시보드에 추가하려는 메트릭의 스토리지 계정을 선택합니다.
1. **범주** > **모니터링** 을 차례로 선택합니다.
1. 표시하려는 메트릭에 대한 차트 타일을 대시보드로 끌어서 놓습니다. 대시보드에 표시하려는 모든 메트릭에 대해 반복합니다. 다음 이미지에서는 한 예로 "Blob - 총 요청 수" 차트를 강조 표시했지만 모든 차트를 대시보드에 배치할 수 있습니다.

   ![Azure Portal의 타일 갤러리](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. 차트 추가를 완료했으면 대시보드 위쪽의 **사용자 지정 완료** 를 선택합니다.

대시보드에 차트를 추가하면 메트릭 차트 사용자 지정에서 설명한 대로 차트를 추가로 사용자 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Storage Analytics에 대한 자세한 내용은 스토리지 분석을 위한 [Storage Analytics](storage-analytics.md)를 참조하세요.
- [Storage Analytics 로그를 구성](manage-storage-analytics-logs.md)합니다.
- 메트릭 스키마에 대해 자세히 알아보세요. [Storage Analytics 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요.
