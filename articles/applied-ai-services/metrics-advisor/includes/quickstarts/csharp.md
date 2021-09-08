---
title: Metrics Advisor C# 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 07/07/2021
ms.author: mbullwin
ms.openlocfilehash: 9668c25c4674505df30877f0a303614065fac94a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454186"
---
[참조 설명서](/dotnet/api/overview/azure/ai.metricsadvisor-readme-pre) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor 리소스를 생성"  target="_blank">Metrics Advisor 리소스를 생성</a>하여 Metrics Advisor 인스턴스를 배포합니다.  
* 시계열 데이터가 포함된 사용자 고유의 SQL 데이터베이스.
   
> [!TIP]
> * [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)에서 .NET Metrics Advisor 샘플을 찾을 수 있습니다.
> * Metrics Advisor 리소스에서 사용할 서비스 인스턴스를 배포하는 데 10~30분 정도 걸릴 수 있습니다. 성공적으로 배포되면 **리소스로 이동** 을 클릭합니다. 배포 후에는 웹 포털과 REST API 모두를 사용하여 Metrics Advisor 인스턴스 사용을 시작할 수 있습니다. 
> * 리소스의 **개요** 섹션에서 Azure Portal의 REST API에 대한 URL을 찾을 수 있습니다. 다음과 같이 표시됩니다.
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

새 프로젝트를 만든 후 **솔루션 탐색기** 에서 프로젝트 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기** 를 선택하고, **시험판 포함** 을 선택하고, `Azure.AI.MetricsAdvisor`를 검색합니다. `1.0.0` 버전, **설치** 를 차례로 선택합니다. 

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `metrics-advisor-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs* 라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```console
dotnet new console -n metrics-advisor-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

Visual Studio 이외의 IDE를 사용하는 경우 다음 명령을 사용하여 .NET용 Metrics Advisor 클라이언트 라이브러리를 설치할 수 있습니다.

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

프로젝트 디렉터리에서 *program.cs* 파일을 열고 `using` 지시문을 추가합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor;
using Azure.AI.MetricsAdvisor.Models;
```

애플리케이션의 `Main()` 메서드에서 이 빠른 시작에 사용된 메서드에 대한 호출을 추가합니다. 나중에 만들 것입니다.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>개체 모델

Metrics Advisor C# SDK의 주요 기능 중 일부를 처리하는 클래스는 다음과 같습니다.

|이름|설명|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **용도**: <br> - 인시던트 나열 <br> - 인시던트의 근본 원인 나열 <br> - 서비스에 의해 보강된 원래 시계열 데이터와 시계열 데이터를 검색합니다. <br> - 경고 나열 <br> - 모델 조정을 위한 피드백 추가 |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **다음을 수행할 수 있습니다.** <br> - 데이터 피드 관리 <br> - 변칙 검색 구성 <br> - 변칙 경고 구성 <br> - 후크 관리  |
| [DataFeed](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **Metrics Advisor가 데이터 원본에서 수집하는 항목. `DataFeed`에는**  행이 포함됩니다. <br> - 타임스탬프 <br> - 0개 이상의 차원 <br> - 하나 이상의 측정값  |
| [DataFeedMetric](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeedMetric.cs)| `DataFeedMetric`은 특정 비즈니스 프로세스의 상태를 모니터링하고 평가하는 데 사용되는 정량 측정값입니다. 차원으로 나눈 여러 개의 시계열 값의 조합일 수 있습니다. 예를 들어 웹 상태 메트릭은 사용자 수 및 en-us 시장의 차원을 포함할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 .NET용 Metrics Advisor 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [데이터 피드 추가](#add-a-data-feed)
* [수집 상태 확인](#check-the-ingestion-status)
* [변칙 검색 구성](#configure-anomaly-detection)
* [후크 만들기](#create-a-hook)
* [경고 구성 만들기](#create-an-alert-configuration)
* [경고 쿼리](#query-the-alert)

## <a name="authenticate-the-client"></a>클라이언트 인증

애플리케이션의 `Program` 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Metrics Advisor 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. <br><br>API 키를 검색하려면 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)으로 이동해야 합니다. 적절한 항목을 선택합니다. 리소스에 대한 **디렉터리**, **구독** 및 **작업 영역** 을 선택하고 **시작** 을 선택합니다. 그러면 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key)에서 API 키를 검색할 수 있습니다.   
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](../../../../cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

구독 및 API 키가 있으면 MetricsAdvisorKeyCredential을 만듭니다. 엔드포인트 및 키 자격 증명을 사용하여 [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs)를 만들 수 있습니다.

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

관리 작업을 수행하는 [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)를 만들 수도 있습니다.

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>데이터 피드 추가

Metrics Advisor는 여러 유형의 데이터 원본을 지원합니다. 이 샘플에서는 SQL 서버에서 데이터를 추출하는 `DataFeed`를 만드는 방법을 설명합니다. 

`connection_String`을 사용자 고유의 SQL 서버 연결 문자열로 바꾸고 `query`를 단일 타임스탬프에서 데이터를 반환하는 쿼리로 바꿉니다. 사용자 지정 데이터를 기반으로 `DataFeedMetric` 및 `DataFeedDimension` 값도 조정해야 합니다.

> [!IMPORTANT]
> 각 타임스탬프에서 쿼리는 각 차원 조합에 대해 최대 하나의 레코드를 반환해야 합니다. 그리고 쿼리에서 반환된 모든 레코드의 타임스탬프는 동일해야 합니다. 메트릭 관리자는 각 타임스탬프에 대해 이 쿼리를 실행하여 데이터를 수집합니다. [자습서: 올바른 쿼리 작성](../../tutorials/write-a-valid-query.md)에서 자세한 내용을 참조하고 예제를 참조하세요.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new SqlServerDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<DataFeedDimension>()
{
    new DataFeedDimension("category"),
    new DataFeedDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

var dataFeed = new DataFeed()
{
    Name = dataFeedName,
    DataSource = dataFeedSource,
    Granularity = dataFeedGranularity,
    Schema = dataFeedSchema,
    IngestionSettings = dataFeedIngestionSettings,
};

Response<string> response = await adminClient.CreateDataFeedAsync(dataFeed);

string dataFeedId = response.Value;

Console.WriteLine($"Data feed ID: {dataFeedId}");

// Note that only the ID of the data feed is known at this point. You can perform another
// service call to GetDataFeedAsync or GetDataFeed to get more information, such as status,
// created time, the list of administrators, or the metric IDs.

Response<DataFeed> response = await adminClient.GetDataFeedAsync(dataFeedId);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.AdministratorEmails)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>수집 상태 확인

이전에 만든 `DataFeed`의 수집 상태를 확인합니다.

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime)
{
    MaxPageSize = 5
};

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 5 statuses.
    if (++statusCount >= 5)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>변칙 검색 구성 

변칙 검색 구성을 만들어 변칙으로 간주되어야 하는 데이터 요소를 서비스에 알립니다.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<string> response = await adminClient.CreateDetectionConfigurationAsync(detectionConfiguration);

string detectionConfigurationId = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfigurationId}");
```

### <a name="create-a-hook"></a>후크 만들기

Metrics Advisor는 경고 알림을 구독하는 것으로 `EmailNotificationHook` 및 `WebNotificationHook` 클래스를 지원합니다. 이 예제에서는 `EmailNotificationHook`를 만드는 방법을 설명합니다. 알림 수신을 시작하려면 변칙 경고 구성에 후크를 전달해야 합니다. 자세한 내용은 샘플 [변칙 경고 구성 만들기](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration)를 참조하세요.

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<string> response = await adminClient.CreateHookAsync(emailHook);

string hookId = response.Value;

Console.WriteLine($"Hook ID: {hookId}");
```

##  <a name="create-an-alert-configuration"></a>경고 구성 만들기

경고를 트리거해야 하는 변칙을 서비스에 알리기 위해 `AnomalyAlertConfiguration`을 만듭니다.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<string> response = await adminClient.CreateAlertConfigurationAsync(alertConfiguration);

string alertConfigurationId = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfigurationId}");
```

### <a name="query-the-alert"></a>경고 쿼리

지정된 변칙 경고 구성에 의해 생성된 경고를 확인합니다.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, AlertQueryTimeMode.AnomalyTime)
{
    MaxPageSize = 5
};

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert created at: {alert.CreatedTime}");
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine();

    // Print at most 5 alerts.
    if (++alertCount >= 5)
    {
        break;
    }
}
```

경고 ID를 알고 있으면 이 경고를 트리거한 [변칙](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/README.md#data-point-anomaly)을 나열합니다.

```csharp
string alertConfigurationId = "<alertConfigurationId>";
string alertId = "<alertId>";

var options = new GetAnomaliesForAlertOptions() { MaxPageSize = 3 };

int anomalyCount = 0;

await foreach (DataPointAnomaly anomaly in client.GetAnomaliesAsync(alertConfigurationId, alertId, options))
{
    Console.WriteLine($"Anomaly detection configuration ID: {anomaly.AnomalyDetectionConfigurationId}");
    Console.WriteLine($"Metric ID: {anomaly.MetricId}");
    Console.WriteLine($"Anomaly at timestamp: {anomaly.Timestamp}");
    Console.WriteLine($"Anomaly detected at: {anomaly.CreatedTime}");
    Console.WriteLine($"Status: {anomaly.Status}");
    Console.WriteLine($"Severity: {anomaly.Severity}");
    Console.WriteLine("Series key:");

    foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
    {
        Console.WriteLine($"  Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
    }

    Console.WriteLine();

    // Print at most 3 anomalies.
    if (++anomalyCount >= 3)
    {
        break;
    }
}
```


### <a name="run-the-application"></a>애플리케이션 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```
