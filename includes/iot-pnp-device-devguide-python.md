---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 14522e6395e40e93c0eb558597ad090521a480a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580536"
---
## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리려면 디바이스가 연결 정보에 해당 ID를 포함해야 합니다.

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> 모듈 및 IoT Edge의 경우 `IoTHubDeviceClient` 대신 `IoTHubModuleClient`을 사용합니다.

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

기본 구성 요소에는 원격 분석 메시지에 추가된 특수 속성이 필요하지 않습니다.

중첩된 구성 요소를 사용하는 경우 디바이스는 구성 요소 이름으로 메시지 속성을 설정해야 합니다.

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

## <a name="read-only-properties"></a>읽기 전용 속성

기본 구성 요소에서 속성을 보고하는 데 특별한 구문이 필요하지 않습니다.

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

중첩된 구성 요소를 사용하는 경우 구성 요소 이름 내에 속성을 만들고 표식이 포함되어야 합니다.

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     }
  }
}
```

## <a name="writable-properties"></a>쓰기 가능한 속성

이러한 속성은 디바이스에서 설정하거나 솔루션에서 업데이트할 수 있습니다. 솔루션에서 속성을 업데이트하면 클라이언트는 `IoTHubDeviceClient` 또는 `IoTHubModuleClient`에서 콜백으로 알림을 받습니다. IoT 플러그 앤 플레이 규칙을 따르려면 디바이스에서 속성이 성공적으로 수신되었음을 서비스에 알려야 합니다.

속성 형식이 인 경우 `Object` 서비스는 개체 필드의 하위 집합만 업데이트하는 경우에도 디바이스에 전체 개체를 보내야 합니다. 디바이스에서 보내는 승인도 완전한 개체여야 합니다.

### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

디바이스에서 쓰기 가능한 속성을 보고하는 경우 규칙에 정의된 `ack` 값을 포함해야 합니다.

기본 구성 요소에서 쓰기 가능한 속성을 보고하려면 다음을 수행합니다.

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
```

디바이스 쌍이 다음 reported 속성으로 업데이트됩니다.

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

중첩된 구성 요소에서 쓰기 가능한 속성을 보고하려면 쌍에 표식이 있어야 합니다.

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
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
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>원하는 속성 업데이트 구독

서비스는 연결된 디바이스에 대한 알림을 트리거하는 원하는 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트를 식별하는 버전 번호를 포함하여 업데이트된 원하는 속성이 포함됩니다. 디바이스는 서비스로 다시 전송된 메시지에 이 버전 번호를 포함해야  `ack` 합니다.

기본 구성 요소는 단일 속성을 보고 수신된 버전으로 보고된 `ack`를 만듭니다.

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
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

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

구성 요소에 대한 디바이스 쌍은 다음과 같이 원하는 섹션과 보고된 섹션을 보여줍니다.

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

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
```

### <a name="request-and-response-payloads"></a>요청 및 응답 페이로드

명령은 형식을 사용하여 요청 및 응답 페이로드를 정의합니다. 디바이스는 들어오는 입력 매개 변수를 역직렬화하고 응답을 직렬화해야 합니다. 다음 예제에서는 페이로드에 정의된 복합 형식을 사용하여 명령을 구현하는 방법을 보여줍니다.

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

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> 유선을 통해 전송되는 직렬화된 페이로드에 요청 및 응답 이름이 없습니다.
