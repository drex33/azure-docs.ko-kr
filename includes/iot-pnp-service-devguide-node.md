---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 7306affd8e0566a9e29daaebb3781de07b4aa835
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132875988"
---
다음 리소스를 사용할 수도 있습니다.

- [Node.js SDK 참조 설명서](/javascript/api/azure-iothub)
- [서비스 클라이언트 샘플](https://github.com/Azure/azure-iot-sdk-node/blob/main/service/samples/javascript/twin.js)
- [Digital Twins 샘플](https://github.com/Azure/azure-iot-sdk-node/blob/main/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>IoT Hub 서비스 클라이언트 예제

이 섹션에서는 IoT Hub 서비스 클라이언트와 **레지스트리** 및 **클라이언트** 클래스를 사용하는 JavaScript 예제를 보여줍니다. **레지스트리** 클래스를 통해 디바이스 쌍을 사용하여 디바이스 상태와 상호 작용할 수 있습니다. **레지스트리** 클래스를 사용하여 IoT Hub에서 [디바이스 등록을 쿼리](../articles/iot-hub/iot-hub-devguide-query-language.md)할 수도 있습니다. **클라이언트** 클래스를 사용하여 디바이스에서 명령을 호출합니다. 디바이스의 [DTDL](../articles/iot-develop/concepts-digital-twin.md) 모델은 디바이스에서 구현하는 속성과 명령을 정의합니다. 코드 조각에서 `deviceId` 변수는 IoT 허브에 등록된 IoT 플러그 앤 플레이 디바이스의 디바이스 ID를 보유합니다.

### <a name="get-the-device-twin-and-model-id"></a>디바이스 쌍 및 모델 ID 가져오기

IoT 허브에 연결된 IoT 플러그 앤 플레이 디바이스의 디바이스 쌍 및 모델 ID를 가져오려면 다음을 수행합니다.

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>디바이스 쌍 업데이트

다음 코드 조각은 디바이스에서 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 이 샘플에서는 업데이트하기 전에 트윈을 가져오는 방법을 보여줍니다. 속성은 디바이스의 기본 구성 요소에 정의되어 있습니다.

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

다음 코드 조각에서는 구성 요소의 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 이 샘플에서는 업데이트하기 전에 트윈을 가져오는 방법을 보여줍니다. 속성은 **thermostat1** 구성 요소에 정의되어 있습니다.

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
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

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

다음 코드 조각에서는 구성 요소에서 `getMaxMinReport` 명령을 호출하는 방법을 보여줍니다. 명령은 **thermostat1** 구성 요소에 정의되어 있습니다.

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub 디지털 트윈 예제

**DigitalTwinClient** 클래스를 사용하여 디지털 트윈을 통해 디바이스와 상호 작용합니다. 디바이스의 [DTDL](../articles/iot-develop/concepts-digital-twin.md) 모델은 디바이스에서 구현하는 속성과 명령을 정의합니다.

이 섹션에서는 Digital Twins API를 사용하는 JavaScript 예제를 보여줍니다.

`digitalTwinId` 변수는 IoT 허브에 등록된 IoT 플러그 앤 플레이 디바이스의 디바이스 ID를 보유합니다.

### <a name="get-the-digital-twin-and-model-id"></a>디지털 트윈 및 모델 ID 가져오기

IoT 허브에 연결된 IoT 플러그 앤 플레이 디바이스의 디지털 트윈 및 모델 ID를 가져오려면 다음을 수행합니다.

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>디지털 트윈 업데이트

다음 코드 조각은 디바이스에서 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 속성은 디바이스의 기본 구성 요소에 정의되어 있습니다.

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

다음 코드 조각에서는 구성 요소의 `targetTemperature` 속성을 업데이트하는 방법을 보여줍니다. 속성은 **thermostat1** 구성 요소에 정의되어 있습니다.

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>호출 명령

다음 코드 조각에서는 기본 구성 요소에 정의된 `getMaxMinReport` 명령을 호출하는 방법을 보여줍니다.

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

다음 코드 조각에서는 구성 요소에서 `getMaxMinReport` 명령을 호출하는 방법을 보여줍니다. 명령은 **thermostat1** 구성 요소에 정의되어 있습니다.

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>디바이스 원격 분석 읽기

IoT 플러그 앤 플레이 디바이스는 DTDL 모델에 정의된 원격 분석을 IoT Hub로 보냅니다. 기본적으로 IoT Hub는 원격 분석을 사용할 수 있는 Event Hubs 엔드포인트로 라우팅합니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트에 보내기](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)를 참조하세요.

다음 코드 조각에서는 기본 Event Hubs 엔드포인트에서 원격 분석을 읽는 방법을 보여줍니다. 이 코드 조각의 코드는 IoT Hub 빠른 시작 [디바이스에서 IoT 허브로 원격 분석을 보내고 백 엔드 애플리케이션으로 읽기](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)에서 가져왔습니다.

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

이전 코드의 다음 출력에서는 다중 구성 요소 **TemperatureController** IoT 플러그 앤 플레이 디바이스에서 보낸 온도 원격 분석을 보여줍니다. `dt-subject` 시스템 속성은 원격 분석을 보낸 구성 요소의 이름을 표시합니다. 이 예제에서 두 구성 요소는 DTDL 모델에 정의된 대로 `thermostat1` 및 `thermostat2`입니다. `dt-dataschema` 시스템 속성은 모델 ID를 표시합니다.

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>디바이스 쌍 변경 알림 읽기

지원되는 엔드포인트로 라우팅할 디바이스 쌍 변경 알림을 생성하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트 > 비원격 분석 이벤트에 보내기](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조하세요.

이전 JavaScript 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 없는 자동 온도 조절 디바이스에 대한 디바이스 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

이전 JavaScript 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 있는 디바이스에 대한 디바이스 쌍 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 디바이스가 알림을 생성할 때의 출력을 보여줍니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>디지털 트윈 변경 알림 읽기

지원되는 엔드포인트로 라우팅할 디지털 트윈 변경 알림을 생성하도록 IoT Hub를 구성할 수 있습니다. 자세한 내용은 [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트 > 비원격 분석 이벤트에 보내기](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조하세요.

이전 JavaScript 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 없는 자동 온도 조절 디바이스에 대한 디지털 트윈 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

이전 JavaScript 코드 조각에 표시된 코드는 IoT Hub가 구성 요소가 있는 디바이스에 대한 디지털 트윈 변경 알림을 생성할 때 다음과 같은 출력을 생성합니다. 이 예제에서는 자동 온도 조절기 구성 요소가 있는 온도 센서 디바이스가 알림을 생성할 때의 출력을 보여줍니다. 애플리케이션 속성 `iothub-message-schema` 및 `opType`은 변경 알림 유형에 대한 정보를 제공합니다.

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```