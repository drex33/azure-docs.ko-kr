---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 319567fba40d2412c882685b8d7d3b916b699968
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520197"
---
[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript)

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

- [Node.js](https://nodejs.org/) 버전 6 이상이 설치된 개발 머신. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. 이 자습서의 지침에서는 Windows 명령 프롬프트에서 **node** 명령을 실행한다고 가정합니다. 하지만 여러 운영 체제에서 Node.js를 사용할 수 있습니다.

- 샘플 코드가 포함된 [Node.js용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 리포지토리의 로컬 복사본입니다. 다음 링크를 사용하여 리포지토리의 복사본을 다운로드합니다. [ZIP 다운로드](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). 그런 다음, 로컬 머신의 적절한 위치에 파일의 압축을 풉니다.

## <a name="review-the-code"></a>코드 검토

이전에 다운로드한 Node.js용 Microsoft Azure IoT SDK 복사본의 텍스트 편집기에서 *azure-iot-sdk-node/device/samples/javascript/pnp/pnpTemperatureController.js* 파일을 엽니다.

샘플을 실행하여 IoT Central에 연결하면 DPS(Device Provisioning Service)를 사용하여 디바이스를 등록하고 연결 문자열을 생성합니다. 이 샘플은 명령줄 환경에서 필요한 DPS 연결 정보를 검색합니다.

`main` 메서드는 다음 작업을 수행합니다.

* 연결을 열기 전에 `client` 개체를 만들고 `dtmi:com:example:TemperatureController;2` 모델 ID를 설정합니다. IoT Central은 모델 ID를 사용하여 이 디바이스에 대한 디바이스 템플릿을 식별하거나 생성합니다. 자세한 내용은 [디바이스 템플릿과 디바이스 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)을 참조하세요.
* 세 가지 명령에 대한 명령 처리기를 만듭니다.
* 각 자동 온도 조절기 구성 요소에 대해 5초마다 온도 원격 분석을 보내도록 루프를 시작합니다.
* 기본 구성 요소에 대해 6초마다 작업 세트 크기 원격 분석을 보내도록 루프를 시작합니다.
* 각 자동 온도 조절기 구성 요소에 대한 `maxTempSinceLastReboot` 속성을 보냅니다.
* 디바이스 정보 속성을 보냅니다.
* 세 가지 구성 요소에 대한 쓰기 가능한 속성 처리기를 만듭니다.

```javascript
async function main() {
  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);
  console.log('Connecting using connection string: ' + deviceConnectionString);
  let resultTwin;

  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();
    console.log('Enabling the commands on the client');
    client.onDeviceMethod(commandNameGetMaxMinReport1, commandHandler);
    client.onDeviceMethod(commandNameGetMaxMinReport2, commandHandler);
    client.onDeviceMethod(commandNameReboot, commandHandler);

    // Send Telemetry after some interval
    let index1 = 0;
    let index2 = 0;
    let index3 = 0;
    intervalToken1 = setInterval(() => {
      const data = JSON.stringify(thermostat1.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index1, thermostat1ComponentName).catch((err) => console.log('error ', err.toString()));
      index1 += 1;
    }, 5000);

    intervalToken2 = setInterval(() => {
      const data = JSON.stringify(thermostat2.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index2, thermostat2ComponentName).catch((err) => console.log('error ', err.toString()));
      index2 += 1;
    }, 5500);


    intervalToken3 = setInterval(() => {
      const data = JSON.stringify({ workingset: 1 + (Math.random() * 90) });
      sendTelemetry(client, data, index3, null).catch((err) => console.log('error ', err.toString()));
      index3 += 1;
    }, 6000);

    // attach a standard input exit listener
    exitListener(client);

    try {
      resultTwin = await client.getTwin();
      // Only report readable properties
      const patchRoot = helperCreateReportedPropertiesPatch({ serialNumber: serialNumber }, null);
      const patchThermostat1Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat1.getMaxTemperatureValue(),
      }, thermostat1ComponentName);

      const patchThermostat2Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat2.getMaxTemperatureValue(),
      }, thermostat2ComponentName);

      const patchDeviceInfo = helperCreateReportedPropertiesPatch({
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
      }, deviceInfoComponentName);

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot, null);
      updateComponentReportedProperties(resultTwin, patchThermostat1Info, thermostat1ComponentName);
      updateComponentReportedProperties(resultTwin, patchThermostat2Info, thermostat2ComponentName);
      updateComponentReportedProperties(resultTwin, patchDeviceInfo, deviceInfoComponentName);
      desiredPropertyPatchListener(resultTwin, [thermostat1ComponentName, thermostat2ComponentName, deviceInfoComponentName]);
    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice` 함수는 디바이스에서 DPS를 사용하여 IoT Central에 등록하고 연결하는 방법을 보여줍니다. 페이로드에는 IoT Central이 [디바이스를 디바이스 템플릿과 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)하는 데 사용하는 모델 ID가 포함됩니다.

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
    console.log('registration succeeded');
    console.log('assigned hub=' + result.assignedHub);
    console.log('deviceId=' + result.deviceId);
    console.log('payload=' + JSON.stringify(result.payload));
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry` 함수는 디바이스에서 IoT Central에 온도 원격 분석을 보내는 방법을 보여줍니다. 구성 요소의 원격 분석을 위해 구성 요소 이름을 사용하여 `$.sub`라는 속성을 추가합니다.

```javascript
async function sendTelemetry(deviceClient, data, index, componentName) {
  if (!!(componentName)) {
    console.log('Sending telemetry message %d from component: %s ', index, componentName);
  } else {
    console.log('Sending telemetry message %d from root interface', index);
  }
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main` 메서드는 `helperCreateReportedPropertiesPatch`라는 도우미 메서드를 사용하여 속성 업데이트 메시지를 만듭니다. 이 메서드는 선택적 매개 변수를 사용하여 속성을 보내는 구성 요소를 지정합니다.

```javascript
const helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  if (!!(componentName)) {
    console.log('The following properties will be updated for component: ' + componentName);
  } else {
    console.log('The following properties will be updated for root interface.');
  }
  console.log(patch);
  return patch;
};
```

`main` 메서드는 다음 메서드를 사용하여 IoT Central의 쓰기 가능 속성에 대한 업데이트를 처리합니다. 메서드가 버전 및 상태 코드를 사용하여 응답을 빌드하는 방법을 확인합니다.

```javascript
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    console.log('Received an update for device with value: ' + JSON.stringify(delta));
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            console.log('Will update property: ' + propertyName + ' to value: ' + propertyValue + ' of component: ' + componentName);
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
        console.log('Will update property: ' + key + ' to value: ' + values + ' for root');
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

`main` 메서드는 다음 메서드를 사용하여 IoT Central의 명령을 처리합니다.

```javascript
const commandHandler = async (request, response) => {
  helperLogCommandRequest(request);
  switch (request.methodName) {
  case commandNameGetMaxMinReport1: {
    await sendCommandResponse(request, response, 200, thermostat1.getMaxMinReportObject());
    break;
  }
  case commandNameGetMaxMinReport2: {
    await sendCommandResponse(request, response, 200, thermostat2.getMaxMinReportObject());
    break;
  }
  case commandNameReboot: {
    await sendCommandResponse(request, response, 200, 'reboot response');
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method: ' + request.methodName + ' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' + err.toString());
  }
};
```

## <a name="get-connection-information"></a>연결 정보 가져오기

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>코드 실행

샘플 애플리케이션을 실행하려면 명령줄 환경을 열고 *pnpTemperatureController.js* 샘플 파일이 포함된 *azure-iot-sdk-node/device/samples/pnp* 폴더로 이동합니다.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

필요한 패키지를 설치합니다.

```cmd/sh
npm install
```

샘플을 실행합니다.

```cmd/sh
node pnpTemperatureController.js
```

다음 출력은 IoT Central에 등록하고 연결하는 디바이스를 보여줍니다. 그런 다음, 샘플은 원격 분석 전송을 시작하기 전에 두 자동 온도 조절기 구성 요소의 `maxTempSinceLastReboot` 속성을 보냅니다.

```output
registration succeeded
assigned hub=iotc-....azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string: HostName=iotc-....azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=qdv...IpAo=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface.
{ serialNumber: 'alwinexlepaho8329' }
The following properties will be updated for component: thermostat1
{ thermostat1: { maxTempSinceLastReboot: 1.5902294191855972, __t: 'c' } }
The following properties will be updated for component: thermostat2
{ thermostat2: { maxTempSinceLastReboot: 16.181771928614545, __t: 'c' } }
The following properties will be updated for component: deviceInformation
{ deviceInformation:
   { manufacturer: 'Contoso Device Corporation',
     model: 'Contoso 47-turbo',
     swVersion: '10.89',
     osName: 'Contoso_OS',
     processorArchitecture: 'Contoso_x86',
     processorManufacturer: 'Contoso Industries',
     totalStorage: 65000,
     totalMemory: 640,
     __t: 'c' } }
executed sample
Received an update for device with value: {"$version":1}
Properties have been reported for component: thermostat1
Properties have been reported for component: thermostat2
Properties have been reported for component: deviceInformation
Properties have been reported for root interface.
Sending telemetry message 0 from component: thermostat1 
Sending telemetry message 0 from component: thermostat2 
Sending telemetry message 0 from root interface
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다. `getMaxMinReport` 명령은 `thermostat2` 구성 요소에 있고, `reboot` 명령은 기본 구성 요소에 있습니다. ‘thermostat2’ 구성 요소에 대해 `targetTemperature` 쓰기 가능한 속성이 설정되었습니다.

```output
Received command request for command name: thermostat2*getMaxMinReport
The command request payload is:
2021-03-26T06:00:00.000Z
Response to method: thermostat2*getMaxMinReport sent successfully.

...

Received command request for command name: reboot
The command request payload is:
10
Response to method: reboot sent successfully.

...

Received an update for device with value: {"thermostat2":{"targetTemperature":76,"__t":"c"},"$version":2}
Will update property: targetTemperature to value: 76 of component: thermostat2
Properties have been reported for component: thermostat2
```
