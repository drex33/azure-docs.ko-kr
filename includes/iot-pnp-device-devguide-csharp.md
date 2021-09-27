---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 64d97eed1a085ecf9f9fcf6172179aa32691104e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580462"
---
## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리려면 디바이스가 연결 정보에 해당 ID를 포함해야 합니다.

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

연결을 초기화하는 데 사용되는 모든 `DeviceClient` 메서드에서 새 `ClientOptions` 오버로드를 사용할 수 있습니다.

> [!TIP]
> 모듈 및 IoT Edge의 경우 `DeviceClient` 대신 `ModuleClient`를 사용합니다.

> [!TIP]
> 디바이스가 모델 ID를 설정할 수 있는 유일한 시간이며 디바이스 연결 후에는 업데이트할 수 없습니다.

## <a name="dps-payload"></a>DPS 페이로드

[DPS(Device Provisioning Service)를](../articles/iot-dps/about-iot-dps.md) 사용하는 디바이스에는 `modelId` 다음 JSON 페이로드를 사용하여 프로비전 프로세스 중에 사용할 가 포함될 수 있습니다.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="use-components"></a>구성 요소 사용

[IoT 플러그 앤 플레이 모델의 구성 요소 이해](../articles/iot-develop/concepts-modeling-guide.md)에 설명된 대로 디바이스 빌더가 구성 요소를 사용하여 디바이스를 설명할지 결정해야 합니다. 구성 요소를 사용하는 경우 디바이스는 다음 섹션에 설명된 규칙을 따라야 합니다.

## <a name="telemetry"></a>원격 분석

기본 구성 요소에는 원격 분석 메시지에 추가된 특수 속성이 필요하지 않습니다.

중첩된 구성 요소를 사용하는 경우 디바이스는 구성 요소 이름으로 메시지 속성을 설정해야 합니다.

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

## <a name="read-only-properties"></a>읽기 전용 속성

기본 구성 요소에서 속성을 보고하는 데 특별한 구문이 필요하지 않습니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

중첩된 구성 요소를 사용하는 경우 구성 요소 이름 내에 속성을 만들고 표식이 포함되어야 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

## <a name="writable-properties"></a>쓰기 가능한 속성

이러한 속성은 디바이스에서 설정하거나 솔루션에서 업데이트할 수 있습니다. 솔루션에서 속성을 업데이트하면 클라이언트는 `DeviceClient` 또는 `ModuleClient`에서 콜백으로 알림을 받습니다. IoT 플러그 앤 플레이 규칙을 따르려면 디바이스에서 속성이 성공적으로 수신되었음을 서비스에 알려야 합니다.

속성 형식이 인 경우 `Object` 서비스는 개체 필드의 하위 집합만 업데이트하는 경우에도 전체 개체를 디바이스에 보내야 합니다. 디바이스에서 보내는 승인도 완전한 개체여야 합니다.

### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

디바이스에서 쓰기 가능한 속성을 보고하는 경우 규칙에 정의된 `ack` 값을 포함해야 합니다.

기본 구성 요소에서 쓰기 가능한 속성을 보고하려면 다음을 수행합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

중첩된 구성 요소에서 쓰기 가능한 속성을 보고하려면 쌍에 표식이 있어야 합니다.

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>원하는 속성 업데이트 구독

서비스는 연결된 디바이스에 대한 알림을 트리거하는 원하는 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트를 식별하는 버전 번호를 포함하여 업데이트된 원하는 속성이 포함됩니다. 디바이스는 서비스로 다시 전송된 메시지에 이 버전 번호를 포함해야  `ack` 합니다.

기본 구성 요소는 단일 속성을 보고 수신된 버전으로 보고된 `ack`를 만듭니다.

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

중첩된 구성 요소에 대한 디바이스 쌍은 다음과 같이 원하는 섹션과 보고된 섹션을 보여줍니다.

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

중첩된 구성 요소는 구성 요소 이름으로 래핑된 원하는 속성을 받고 `ack`에 의해 보고된 속성을 다시 보고해야 합니다.

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

중첩된 구성 요소에 대한 디바이스 쌍은 다음과 같이 원하는 섹션과 보고된 섹션을 보여줍니다.

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

## <a name="commands"></a>명령

기본 구성 요소는 서비스에서 호출된 명령 이름을 받습니다.

중첩 된 구성 요소는 구성 요소 이름과 `*` 구분 기호가 접두사로 붙은 명령 이름을 받습니다.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

### <a name="request-and-response-payloads"></a>요청 및 응답 페이로드

명령은 형식을 사용하여 요청 및 응답 페이로드를 정의합니다. 디바이스는 들어오는 입력 매개 변수를 역직렬화하고 응답을 직렬화해야 합니다. 다음 예제에서는 페이로드에 정의된 복합 형식을 사용하여 명령을 구현하는 방법을 보여줍니다.

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

다음 코드 조각은 직렬화 및 역직렬화를 사용 설정하는 데 사용되는 형식을 포함하여 디바이스가 이 명령 정의를 구현하는 방법을 보여줍니다.

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> 유선을 통해 전송되는 직렬화된 페이로드에 요청 및 응답 이름이 없습니다.
