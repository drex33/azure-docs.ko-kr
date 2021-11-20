---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: cfd250e4826bf0675763b4e5c19c72f1088ef467
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132876054"
---
다음 리소스를 사용할 수도 있습니다.

- [Python SDK 참조 설명서](/python/api/azure-iot-hub/azure.iot.hub)
- [서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-python/blob/main/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Digital Twins 샘플](https://github.com/Azure/azure-iot-sdk-python/blob/main/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>IoT Hub 서비스 클라이언트 예제

이 섹션에서는 IoT Hub 서비스 클라이언트와 **IoTHubRegistryManager** 및 **CloudToDeviceMethod** 클래스를 사용하는 Python 예제를 보여줍니다. **IoTHubRegistryManager** 클래스를 사용하여 디바이스 쌍을 통해 디바이스 상태와 상호 작용합니다. **IoTHubRegistryManager** 클래스를 사용하여 IoT Hub에서 [디바이스 등록을 쿼리](../articles/iot-hub/iot-hub-devguide-query-language.md)할 수도 있습니다. **CloudToDeviceMethod** 클래스를 사용하여 디바이스에서 명령을 호출합니다. 디바이스의 [DTDL](../articles/iot-develop/concepts-digital-twin.md) 모델은 디바이스에서 구현하는 속성과 명령을 정의합니다. 코드 조각에서 `device_id` 변수는 IoT 허브에 등록된 IoT 플러그 앤 플레이 디바이스의 디바이스 ID를 보유합니다.

### <a name="get-the-device-twin-and-model-id"></a>디바이스 쌍 및 모델 ID 가져오기

IoT 허브에 연결된 IoT 플러그 앤 플레이 디바이스의 디바이스 쌍 및 모델 ID를 가져오려면 다음을 수행합니다.

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>디바이스 쌍 업데이트

다음 코드 조각은 디바이스에서 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 이 샘플에서는 업데이트하기 전에 트윈의 `etag`를 가져오는 방법을 보여줍니다. 속성은 디바이스의 기본 구성 요소에 정의되어 있습니다.

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

다음 코드 조각에서는 구성 요소의 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 이 샘플에서는 업데이트하기 전에 트윈의 `ETag`를 가져오는 방법을 보여줍니다. 속성은 **thermostat1** 구성 요소에 정의되어 있습니다.

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

구성 요소의 속성에 대한 속성 패치는 다음 예제와 같습니다.

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>호출 명령

다음 코드 조각에서는 기본 구성 요소에 정의된 `getMaxMinReport` 명령을 호출하는 방법을 보여줍니다.

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

다음 코드 조각에서는 구성 요소에서 `getMaxMinReport` 명령을 호출하는 방법을 보여줍니다. 명령은 **thermostat1** 구성 요소에 정의되어 있습니다.

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub 디지털 트윈 예제

**DigitalTwinClient** 클래스를 사용하여 디지털 트윈을 통해 디바이스와 상호 작용합니다. 디바이스의 [DTDL](../articles/iot-develop/concepts-digital-twin.md) 모델은 디바이스에서 구현하는 속성과 명령을 정의합니다.

`device_id` 변수는 IoT 허브에 등록된 IoT 플러그 앤 플레이 디바이스의 디바이스 ID를 보유합니다.

### <a name="get-the-digital-twin-and-model-id"></a>디지털 트윈 및 모델 ID 가져오기

IoT 허브에 연결된 IoT 플러그 앤 플레이 디바이스의 디지털 트윈 및 모델 ID를 가져오려면 다음을 수행합니다.

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>디지털 트윈 업데이트

다음 코드 조각은 디바이스에서 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 속성은 디바이스의 기본 구성 요소에 정의되어 있습니다.

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

다음 코드 조각에서는 구성 요소의 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 속성은 **thermostat1** 구성 요소에 정의되어 있습니다.

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>호출 명령

다음 코드 조각에서는 기본 구성 요소에 정의된 `getMaxMinReport` 명령을 호출하는 방법을 보여줍니다.

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

다음 코드 조각에서는 구성 요소에서 `getMaxMinReport` 명령을 호출하는 방법을 보여줍니다. 명령은 **thermostat1** 구성 요소에 정의되어 있습니다.

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>디바이스 원격 분석 읽기

IoT 플러그 앤 플레이 디바이스는 DTDL 모델에 정의된 원격 분석을 IoT Hub로 보냅니다. 기본적으로 IoT Hub는 원격 분석을 사용할 수 있는 Event Hubs 엔드포인트로 라우팅합니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트에 보내기](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)를 참조하세요.

다음 코드 조각에서는 기본 Event Hubs 엔드포인트에서 원격 분석을 읽는 방법을 보여줍니다. 이 코드 조각의 코드는 IoT Hub 빠른 시작 [디바이스에서 IoT 허브로 원격 분석을 보내고 백 엔드 애플리케이션으로 읽기](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python)에서 가져왔습니다.

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

이전 코드의 다음 출력에서는 기본 구성 요소만 있는 구성 요소가 없는 **자동 온도 조절기** IoT 플러그 앤 플레이 디바이스에서 보낸 온도 원격 분석을 보여줍니다. `dt-dataschema` 시스템 속성은 모델 ID를 표시합니다.

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

이전 코드의 다음 출력에서는 다중 구성 요소 **TemperatureController** IoT 플러그 앤 플레이 디바이스에서 보낸 온도 원격 분석을 보여줍니다. `dt-subject` 시스템 속성은 원격 분석을 보낸 구성 요소의 이름을 표시합니다. 이 예제에서 두 구성 요소는 DTDL 모델에 정의된 대로 `thermostat1` 및 `thermostat2`입니다. `dt-dataschema` 시스템 속성은 모델 ID를 표시합니다.

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>디바이스 쌍 변경 알림 읽기

지원되는 엔드포인트로 라우팅할 디바이스 쌍 변경 알림을 생성하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트 > 비원격 분석 이벤트에 보내기](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조하세요.

이전 Python 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 없는 자동 온도 조절 디바이스에 대한 디바이스 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

이전 Python 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 있는 디바이스에 대한 디바이스 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 디바이스가 알림을 생성할 때의 출력을 보여줍니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>디지털 트윈 변경 알림 읽기

지원되는 엔드포인트로 라우팅할 디지털 트윈 변경 알림을 생성하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트 > 비원격 분석 이벤트에 보내기](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조하세요.

이전 Python 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 없는 자동 온도 조절 디바이스에 대한 디지털 트윈 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

이전 Python 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 있는 디바이스에 대한 디지털 트윈 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 디바이스가 알림을 생성할 때의 출력을 보여줍니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```