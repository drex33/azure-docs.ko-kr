---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: f5763606d289c679ea9d1883e97ab30b47368ac8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580537"
---
## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리려면 디바이스가 연결 정보에 해당 ID를 포함해야 합니다.

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> 모듈 및 IoT Edge의 경우 `Client` 대신 `ModuleClient`을 사용합니다.

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

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

## <a name="read-only-properties"></a>읽기 전용 속성

기본 구성 요소에서 속성을 보고하는 데 특별한 구문이 필요하지 않습니다.

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
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

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

장치 쌍은 다음과 같이 보고 된 속성으로 업데이트 됩니다.

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

이러한 속성은 디바이스에서 설정하거나 솔루션에서 업데이트할 수 있습니다. 솔루션에서 속성을 업데이트하면 클라이언트는 `Client` 또는 `ModuleClient`에서 콜백으로 알림을 받습니다. IoT 플러그 앤 플레이 규칙을 따르려면 디바이스에서 속성이 성공적으로 수신되었음을 서비스에 알려야 합니다.

속성 형식이 이면 `Object` 서비스는 개체 필드의 하위 집합만 업데이트 하는 경우에도 전체 개체를 장치에 보내야 합니다. 장치에서 전송 하는 승인도 전체 개체 여야 합니다.

### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

디바이스에서 쓰기 가능한 속성을 보고하는 경우 규칙에 정의된 `ack` 값을 포함해야 합니다.

기본 구성 요소에서 쓰기 가능한 속성을 보고하려면 다음을 수행합니다.

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

장치 쌍은 다음과 같이 보고 된 속성으로 업데이트 됩니다.

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

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
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
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>원하는 속성 업데이트 구독

서비스는 연결된 디바이스에 대한 알림을 트리거하는 원하는 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트를 식별하는 버전 번호를 포함하여 업데이트된 원하는 속성이 포함됩니다. 장치는  `ack` 서비스로 다시 보낸 메시지에이 버전 번호를 포함 해야 합니다.

기본 구성 요소는 단일 속성을 보고 수신된 버전으로 보고된 `ack`를 만듭니다.

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
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

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
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

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
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

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> 유선을 통해 전송되는 직렬화된 페이로드에 요청 및 응답 이름이 없습니다.
