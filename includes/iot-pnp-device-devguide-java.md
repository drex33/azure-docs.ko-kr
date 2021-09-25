---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: de1c24fa977ffd13c744aa01d5464295f7ab4691
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580551"
---
## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리려면 디바이스가 연결 정보에 해당 ID를 포함해야 합니다.

```java
ClientOptions options = new ClientOptions();
options.setModelId(MODEL_ID);
deviceClient = new DeviceClient(deviceConnectionString, protocol, options);
```

연결을 초기화하는 데 사용되는 모든 `DeviceClient` 메서드에서 `ClientOptions` 오버로드를 사용할 수 있습니다.

> [!TIP]
> 모듈 및 IoT Edge의 경우 `ModuleClient` 대신 `DeviceClient`를 사용합니다.

> [!TIP]
> 디바이스가 모델 ID를 설정할 수 있는 유일한 시간이며 디바이스 연결 후에는 업데이트할 수 없습니다.

## <a name="dps-payload"></a>DPS 페이로드

[DPS(Device Provisioning Service)](../articles/iot-dps/about-iot-dps.md)를 사용하는 디바이스에는 다음 JSON 페이로드를 사용하는 프로비저닝 프로세스 중에 사용할 `modelId`가 포함될 수 있습니다.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="use-components"></a>구성 요소 사용

[IoT 플러그 앤 플레이 모델의 구성 요소 이해](../articles/iot-develop/concepts-modeling-guide.md)에 설명된 대로 디바이스 빌더가 구성 요소를 사용하여 디바이스를 설명할지 결정해야 합니다. 구성 요소를 사용하는 경우 디바이스는 다음 섹션에 설명된 규칙을 따라야 합니다.

## <a name="telemetry"></a>원격 분석

기본 구성 요소에는 원격 분석 메시지에 추가 된 특별 한 속성이 필요 하지 않습니다.

중첩된 구성 요소를 사용하는 경우 디바이스는 구성 요소 이름으로 메시지 속성을 설정해야 합니다.

```java
private static void sendTemperatureTelemetry(String componentName) {
  double currentTemperature = temperature.get(componentName);

  Map<String, Object> payload = singletonMap("temperature", currentTemperature);

  Message message = new Message(gson.toJson(payload));
  message.setContentEncoding("utf-8");
  message.setContentTypeFinal("application/json");

  if (componentName != null) {
      message.setProperty("$.sub", componentName);
  }
  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
}
```

## <a name="read-only-properties"></a>읽기 전용 속성

기본 구성 요소에서 속성을 보고하는 데 특별한 구문이 필요하지 않습니다.

```java
Property reportedProperty = new Property("maxTempSinceLastReboot", 38.7);

deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
```

장치 쌍은 다음과 같이 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

중첩 된 구성 요소를 사용 하는 경우 속성을 구성 요소 이름 내에 만들고 표식을 포함 해야 합니다.

```java
Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("maxTemperature", 38.7);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty)

deviceClient.sendReportedProperties(reportedProperty);
```

장치 쌍은 다음과 같이 보고 된 속성으로 업데이트 됩니다.

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

속성 형식이 이면 `Object` 서비스는 개체 필드의 하위 집합만 업데이트 하는 경우에도 전체 개체를 장치에 보내야 합니다. 장치에서 전송 하는 승인도 전체 개체 여야 합니다.

### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

디바이스에서 쓰기 가능한 속성을 보고하는 경우 규칙에 정의된 `ack` 값을 포함해야 합니다.

기본 구성 요소에서 쓰기 가능한 속성을 보고하려면 다음을 수행합니다.

```java
@AllArgsConstructor
private static class EmbeddedPropertyUpdate {
  @NonNull
  @SerializedName("value")
  public Object value;
  @NonNull
  @SerializedName("ac")
  public Integer ackCode;
  @NonNull
  @SerializedName("av")
  public Integer ackVersion;
  @SerializedName("ad")
  public String ackDescription;
}

EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(23.2, 200, 3, "Successfully updated target temperature");
Property reportedPropertyCompleted = new Property("targetTemperature", completedUpdate);
deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
```

장치 쌍은 다음과 같이 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

중첩된 구성 요소에서 쓰기 가능한 속성을 보고하려면 쌍에 표식이 있어야 합니다.

```java
Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
    put("value", 23.2);
    put("ac", 200);
    put("av", 3);
    put("ad", "complete");
}};

Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("targetTemperature", embeddedProperty);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty));

deviceClient.sendReportedProperties(reportedProperty);
```

장치 쌍은 다음과 같이 보고 된 속성으로 업데이트 됩니다.

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

서비스는 연결된 디바이스에 대한 알림을 트리거하는 원하는 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트를 식별하는 버전 번호를 포함하여 업데이트된 원하는 속성이 포함됩니다. 장치는  `ack` 서비스로 다시 보낸 메시지에이 버전 번호를 포함 해야 합니다.

기본 구성 요소는 단일 속성을 보고 수신된 버전으로 보고된 `ack`를 만듭니다.

```java
private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, 200, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
  Collections.singletonMap(
    new Property("targetTemperature", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), null));
deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
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
          "ad": "Successfully updated target temperature"
      }
  }
}
```

중첩된 구성 요소는 구성 요소 이름으로 래핑된 원하는 속성을 받고 `ack`에 의해 보고된 속성을 다시 보고해야 합니다.

```java
private static final Map<String, Double> temperature = new HashMap<>();

private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        String componentName = (String) context;

        if (property.getKey().equalsIgnoreCase(componentName)) {
            double targetTemperature = (double) ((TwinCollection) property.getValue()).get(propertyName);

            Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
                put("value", temperature.get(componentName));
                put("ac", 200);
                put("av", property.getVersion().longValue());
                put("ad", "Successfully updated target temperature.");
            }};

            Map<String, Object> componentProperty = new HashMap<String, Object>() {{
                put("__t", "c");
                put(propertyName, embeddedProperty);
            }};

            Set<Property> completedPropertyPatch = new Property(componentName, componentProperty));

            deviceClient.sendReportedProperties(completedPropertyPatch);
        } else {
            log.debug("Property: Received an unrecognized property update from service.");
        }
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat1", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat1")),
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat2", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat2"))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
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

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);

// ...
private static final Map<String, Double> temperature = new HashMap<>();

private static class MethodCallback implements DeviceMethodCallback {
  final String reboot = "reboot";
  final String getMaxMinReport1 = "thermostat1*getMaxMinReport";
  final String getMaxMinReport2 = "thermostat2*getMaxMinReport";

  @Override
  public DeviceMethodData call(String methodName, Object methodData, Object context) {
    String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);

    switch (methodName) {
      case reboot:
        int delay = gson.fromJson(jsonRequest, Integer.class);

        Thread.sleep(delay * 1000);

        temperature.put("thermostat1", 0.0d);
        temperature.put("thermostat2", 0.0d);

        return new DeviceMethodData(200, null);

      // ...

      default:
        log.debug("Command: command=\"{}\" is not implemented, no action taken.", methodName);
          return new DeviceMethodData(404, null);
    }
  }
}
```

### <a name="request-and-response-payloads"></a>요청 및 응답 페이로드

명령은 형식을 사용하여 요청 및 응답 페이로드를 정의합니다. 디바이스는 들어오는 입력 매개 변수를 역직렬화하고 응답을 직렬화해야 합니다.

다음 예제에서는 페이로드에 정의된 복합 형식을 사용하여 명령을 구현하는 방법을 보여줍니다.

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

다음 코드 조각은 직렬화 및 역직렬화를 사용 설정하는 데 사용되는 형식을 포함하여 디바이스가 이 명령 정의를 구현하는 방법을 보여줍니다.

```java
deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), "getMaxMinReport", new MethodIotHubEventCallback(), "getMaxMinReport");

// ...

private static class GetMaxMinReportMethodCallback implements DeviceMethodCallback {
    String commandName = "getMaxMinReport";

    @Override
    public DeviceMethodData call(String methodName, Object methodData, Object context) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = gson.fromJson(jsonRequest, Date.class);

        String responsePayload = String.format(
                "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                maxTemp,
                minTemp,
                avgTemp,
                since,
                endTime);

        return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
    }
}
```

> [!Tip]
> 유선을 통해 전송되는 직렬화된 페이로드에 요청 및 응답 이름이 없습니다.
