---
title: 모니터링 Azure IoT Hub
description: 여기에서 Azure IoT Hub를 모니터링하는 방법 알아보기.
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/26/2021
ms.openlocfilehash: 9782190bb917492770f6870675bff768b80ff61a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061424"
---
# <a name="monitoring-azure-iot-hub"></a>모니터링 Azure IoT Hub

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure 리소스에서 생성되는 모니터링 데이터와 Azure IoT Hub 기능을 사용하여 해당 데이터에 대해 분석하고 경고하는 방법을 설명합니다.

## <a name="monitor-overview"></a>모니터링 개요

각 IoT Hub에 대한 Azure Portal의 **개요** 페이지에는 사용되는 메시지 및 IoT Hub에 연결된 디바이스 수와 같은 일부 사용 현황 메트릭을 제공하는 차트가 포함되어 있습니다.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="IoT Hub 개요 페이지의 기본 메트릭 차트":::

메시지 카운트 값은 1분 지연될 수 있으며, IoT Hub 서비스 인프라와 관련된 이유로 새로 고치게 되면 값이 더 높거나 낮은 값 사이를 바운스하는 경우가 있습니다. 해당 카운터는 최종 1분 간의 계산 값에 대해서만 부정확해야 합니다.

개요 창에 표시되는 정보는 유용하지만 IoT Hub에 사용할 수 있는 적은 양의 모니터링 데이터만 표시합니다. 이 모니터링 데이터 중 일부는 자동으로 수집되며, IoT Hub를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure IoT Hub는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. 이를 통해 다른 클라우드 및 온-프레미스의 리소스 이외에도 Azure 리소스를 모니터링할 수 있는 완전한 기능 세트를 제공합니다.

다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링 문서](../azure-monitor/essentials/monitor-azure-resource.md)로 시작하세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

다음 섹션에서는 Azure IoT Hub에서 수집한 특정 데이터를 설명하고 Azure 도구를 사용하여 데이터 컬렉션을 구성하고 해당 데이터를 분석하기 위한 예제를 제공합니다.

## <a name="monitoring-data"></a>데이터 모니터링

Azure IoT Hub는 [Azure 리소스의 모니터링 데이터](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다.

모니터링 Azure IoT Hub에서 만든 메트릭 및 로그에 대한 세부 정보는 [Azure IoT Hub 모니터링 데이터 참조](monitor-iot-hub-reference.md)를 참조하세요.

> [!IMPORTANT]
> Azure Monitor 리소스 로그를 사용하여 IoT Hub 서비스에서 내보내는 이벤트는 안정 또는 정렬이 보장되지 않습니다. 일부 이벤트는 손실되거나 순서 없이 제공될 수 있습니다. 또한 리소스 로그는 실시간으로 제공되지 않으며, 사용자가 선택한 대상에 이벤트를 기록하기까지 몇 분 정도 걸릴 수 있습니다.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

메트릭 및 로그는 다음을 비롯한 여러 위치로 라우팅할 수 있습니다.
- Azure Monitor 로그는 연결된 Log Analytics 작업 영역을 통해 저장됩니다. 거기서 Log Analytics를 사용하여 분석할 수 있습니다.
- 보관 및 오프라인 분석을 위한 Azure Storage 
- 외부 애플리케이션(예: 타사 SIEM 도구)에서 읽을 수 있는 Event Hubs 엔드포인트입니다.

Azure Portal에서 IoT Hub의 왼쪽 창에 있는 **모니터링** 에서 **진단 설정** 을 선택한 다음, **진단 설정 추가** 를 선택하여 IoT Hub에서 내보낸 로그 및 플랫폼 메트릭으로 범위가 지정된 진단 설정을 만들 수 있습니다.

다음 스크린샷은 리소스 로그 형식 *연결 작업* 및 모든 플랫폼 메트릭을 Log Analytics 작업 영역으로 라우팅하는 진단 설정을 보여 줍니다.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="IoT Hub에 대한 진단 설정 창":::

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure IoT Hub에 대한 범주는 [모니터링 Azure IoT Hub 데이터의 리소스 로그 참조](monitor-iot-hub-reference.md#resource-logs)에 나열됩니다.

IoT Hub 플랫폼 메트릭을 다른 위치로 라우팅하는 경우 다음에 유의하세요.

- *연결된 디바이스(미리 보기)* 및 *총 디바이스(미리 보기)* 의 플랫폼 메트릭은 진단 설정을 통해 내보낼 수 없습니다.

- 다차원 메트릭(예: 일부 [라우팅 메트릭](monitor-iot-hub-reference.md#routing-metrics))은 현재 차원 값에서 집계된 평면화된 단일 차원 메트릭으로 내보내게 됩니다. 세부 정보는 [플랫폼 메트릭을 다른 위치로 내보내기](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations)를 참조하세요.

## <a name="analyzing-metrics"></a>메트릭 분석

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 Azure IoT Hub에 대한 메트릭을 분석하려면 **Azure Monitor** 메뉴에서 **메트릭** 을 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

Azure Portal에서 IoT Hub의 왼쪽 창에 있는 **모니터링** 의 **메트릭** 을 선택하여 IoT Hub가 내보낸 플랫폼 메트릭에서 메트릭 탐색기 범위를 기본적으로 열 수 있습니다.

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="IoT Hub에 대한 메트릭 탐색기 페이지를 보여주는 스크린샷." border="true":::

Azure IoT Hub에 대해 수집되는 플랫폼 메트릭의 목록은 [모니터링 Azure IoT Hub 데이터의 메트릭 참조](monitor-iot-hub-reference.md#metrics)를 확인하세요. 모든 Azure 서비스에 대해 수집되는 플랫폼 메트릭 목록은 [Azure Monitor에서 지원되는 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

카운트 단위로 수집되는 IoT Hub 플랫폼 메트릭의 경우 일부 집계를 사용하지 않거나 사용할 수 없는 경우도 있습니다. 자세한 정보는 [모니터링 Azure IoT Hub 데이터에서 지원되는 집계 참조](monitor-iot-hub-reference.md#supported-aggregations)에 있습니다.

[라우팅 메트릭과](monitor-iot-hub-reference.md#routing-metrics)같은 일부 IoT Hub 메트릭은 다차원입니다. 해당 메트릭에서는 차원을 기반으로 하는 차트에 [필터](../azure-monitor/essentials/metrics-charts.md#filters)와 [분할](../azure-monitor/essentials/metrics-charts.md#apply-splitting)을 적용할 수 있습니다.

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다. 해당 테이블의 데이터는 Log Analytics 작업 영역과 연결되며 Log Analytics에서 쿼리할 수 있습니다. Azure Monitor 로그에 대한 자세한 정보는 Azure Monitor 설명서의 [Azure Monitor 로그 개요](../azure-monitor/logs/data-platform-logs.md)를 참조하세요. 

Azure Monitor 로그에 데이터를 라우팅하려면 Log Analytics 작업 영역에 리소스 로그 또는 플랫폼 메트릭을 전송하는 진단 설정을 만들어야 합니다. 자세한 정보는 [수집 및 라우팅](#collection-and-routing)을 참조하세요.

Azure Portal에서 IoT Hub의 왼쪽 창에 있는 **모니터링** 의 **로그** 를 선택하여, IoT Hub에 대해 Azure Monitor 로그에서 수집된 로그 및 메트릭에 관한 범위가 지정된 Log Analytics 쿼리를 기본적으로 수행할 수 있습니다.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="IoT Hub에 대한 로그 페이지.":::

Azure Monitor 로그 및 Log Analytics에서 쿼리할 때 사용하는 테이블 목록은 [모니터링 Azure IoT Hub 데이터의 Azure Monitor 로그 테이블 참조](monitor-iot-hub-reference.md#azure-monitor-logs-tables)에 있습니다.

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에서 설명합니다. [모니터링 Azure IoT Hub 데이터의 리소스 로그 참조](monitor-iot-hub-reference.md#resource-logs)에서 Azure IoT Hub에 대해 수집된 리소스 로그의 스키마 및 범주를 찾을 수 있습니다.

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준의 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

IoT Hub 플랫폼 메트릭을 Azure Monitor 로그에 라우팅하는 경우 다음에 유의하세요.

- *연결된 디바이스(미리 보기)* 및 *총 디바이스(미리 보기)* 의 플랫폼 메트릭은 진단 설정을 통해 내보낼 수 없습니다.

- 다차원 메트릭(예: 일부 [라우팅 메트릭](monitor-iot-hub-reference.md#routing-metrics))은 현재 차원 값에서 집계된 평면화된 단일 차원 메트릭으로 내보내게 됩니다. 세부 정보는 [플랫폼 메트릭을 다른 위치로 내보내기](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations)를 참조하세요.

IoT Hub의 몇 가지 일반적인 쿼리는 [샘플 Kusto 쿼리](#sample-kusto-queries)를 참조하세요. Log Analytics 쿼리 사용에 대한 세부 정보는 [Azure Monitor의 로그 쿼리 개요](../azure-monitor/logs/log-query-overview.md)를 참조하세요.

### <a name="sdk-version-in-iot-hub-logs"></a>IoT Hub 로그의 SDK 버전

IoT Hub 리소스 로그의 일부 작업은 `properties` 개체의 `sdkVersion`속성을 반환합니다. 해당 작업의 경우 디바이스 또는 백 엔드 앱이 Azure IoT SDK 중 하나를 사용할 때 해당 속성은 사용되는 SDK, SDK 버전 및 SDK가 실행되는 플랫폼에 대한 정보를 포함합니다. 다음 예제에서는 Node.js 디바이스 SDK를 사용하는 경우 [`deviceConnect`](monitor-iot-hub-reference.md#connections) 작업에 대한 내보내기 `sdkVersion` 속성을 보여 줍니다. `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"`. .NET (C#) SDK에 대해 내보내는 값의 예입니다`".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"`.

다음 표는 다양한 Azure IoT SDK에 사용되는 SDK 이름을 보여 줍니다.

| sdkVersion 속성의 SDK 이름 | 언어 |
|----------|----------|
| .NET | .NET (C#) |
| microsoft.azure.devices | .NET(C#) 서비스 SDK |
| microsoft.azure.devices.client | .NET(C#) 디바이스 SDK |
| iothubclient | C 또는 Python v1(사용 되지 않음) 디바이스 SDK |
| iothubserviceclient | C 또는 Python v1(사용 되지 않음) 서비스 SDK |
| azure-iot-device-iothub-py | Python 디바이스 SDK |
| azure-iot-device | Node.js 디바이스 SDK |
| azure-iothub | Node.js 서비스 SDK |
| com.microsoft.azure.iothub-java-client | Java 디바이스 SDK |
| com.microsoft.azure.iothub.service.sdk | Java 서비스 SDK |
| com.microsoft.azure.sdk.iot.iot-device-client | Java 디바이스 SDK |
| com.microsoft.azure.sdk.iot.iot-service-client | Java 서비스 SDK |
| C | 임베디드 C |
| C +(OSSimplified = Azure RTOS) | Azure RTOS |

IoT Hub 리소스 로그에 대해 쿼리를 수행하는 경우 SDK 버전 속성을 추출할 수 있습니다. 예를 들어, 다음 쿼리는 연결 작업에서 반환된 속성에서 SDK 버전 속성(및 디바이스 ID)를 추출합니다. 이 두 속성은 디바이스가 연결되는 IoT Hub의 작업 시간 및 리소스 ID와 함께 결과에 기록됩니다.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> IoT Hub 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 IoT Hub로 설정된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 Azure 서비스의 데이터 또는 다른 IoT Hub의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/logs/scope.md)를 참조하세요.

다음은 IoT Hub를 모니터링하는 데 사용할 수 있는 쿼리입니다.

- 연결 오류: 디바이스 연결 오류 식별하기.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- 제한 오류: 제한 오류를 발생시키는 요청을 가장 많이 한 디바이스 식별하기.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- 중지된 엔드포인트: 문제가 보고된 횟수 및 원인을 통해 중지된 또는 비정상 엔드포인트 식별하기.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- 오류 요약: 모든 작업에서 발생한 형식별 오류의 수.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- 최근에 연결된 디바이스: IoT Hub 에서 지정된 기간 동안 연결한 디바이스 목록.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- 디바이스의 SDK 버전: 디바이스 연결 또는 디바이스-클라우드 쌍 작업을 위한 디바이스 및 해당 SDK 버전 목록.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Azure Event Hubs의 로그 읽기

진단 설정을 통해 이벤트 로깅을 설정한 후에는 로그의 정보를 기반으로 조치를 취할 수 있도록 로그를 읽는 애플리케이션을 만들 수 있습니다. 다음 샘플 코드는 이벤트 허브에서 로그를 검색합니다.

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다.

플랫폼 메트릭을 기반으로 경고 규칙을 만들 때 카운트 단위로 수집되는 IoT Hub 플랫폼 메트릭에 대해 일부 집계를 사용하지 않거나 사용할 수 없는 경우가 있습니다. 자세한 정보는 [모니터링 Azure IoT Hub 데이터에서 지원되는 집계 참조](monitor-iot-hub-reference.md#supported-aggregations)에 있습니다.

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Event Grid와 디바이스 당 모니터의 연결 끊김

Azure Monitor는 IoT Hub에 연결된 디바이스 수를 모니터링하고 연결된 디바이스 수가 임계값 아래로 떨어지면 경고를 트리거하는 데 사용할 수 있는 메트릭인 *연결된 디바이스* 를 제공합니다. 일부 시나리오에서는 해당 방법이면 충분할 수 있지만, [Azure Event Grid](../event-grid/index.yml)는 짧은 대기 시간으로 중요한 디바이스 및 인프라에 대한 디바이스 연결을 추적하는 데 사용할 수 있는 디바이스 단위 모니터링 솔루션을 제공합니다.

Event Grid를 사용하면 IoT Hub [**DeviceConnected** 및 **DeviceDisconnected** 이벤트](iot-hub-event-grid.md#event-types)를 구독하여 경고를 트리거하고 디바이스 연결 상태를 모니터링할 수 있습니다. Event Grid는 Azure Monitor 보다 훨씬 짧은 이벤트 대기 시간을 제공하고, 연결된 디바이스의 총 수가 아니라 디바이스별로 모니터링할 수 있습니다. 해당 요소들은 중요한 디바이스 및 인프라에 대한 연결을 모니터링하는 메서드로 Event Grid를 선호하게 만듭니다. 프로덕션 환경에서 디바이스 연결을 모니터링하려면 Event Grid를 사용하는 것이 좋습니다.

Event Grid 및 Azure Monitor를 사용하여 디바이스 연결을 모니터링하는 방법에 대한 세부 정보는 [Azure IoT Hub를 통한 모니터링, 진단 및 연결 끊김 문제 해결](iot-hub-troubleshoot-connectivity.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Service name]으로 만든 메트릭, 로그 및 기타 중요한 값에 대한 참조로서 [모니터링 Azure IoT Hub 데이터 참조](monitor-iot-hub-reference.md)를 확인하세요.

- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
