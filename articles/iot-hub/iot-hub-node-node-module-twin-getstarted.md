---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(Node.js)
description: Node.js용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: amqp, devx-track-js
ms.openlocfilehash: 2028bed00624526c021668076c4d7feaba7aa07c
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830111"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>IoT Hub 모듈 ID 및 모듈 쌍 시작(Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 응용 프로그램에서 장치를 구성할 수 있도록 하고 장치 상태에 대한 가시성을 제공하지만, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같이 여러 구성 요소가 있는 가능한 디바이스에서 각 구성 요소에 대해 격리된 구성과 조건을 허용합니다.

이 자습서를 마치면 두 가지 Node.js 앱이 만들어집니다.

* **CreateIdentities**: 디바이스 ID, 모듈 ID 및 관련 보안 키를 만들어 디바이스 및 모듈 클라이언트를 연결합니다.

* **UpdateModuleTwinReportedProperties**: 업데이트된 모듈 쌍 reported 속성을 IoT Hub에 보냅니다.

> [!NOTE]
> 디바이스와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Node.js 버전 10.0.x 이상. Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)에서 설명합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub에서 디바이스 ID 및 모듈 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에 디바이스 ID 및 모듈 ID를 만드는 Node.js 앱을 만듭니다. ID 레지스트리에 항목이 없는 경우 디바이스 또는 모듈을 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-identity-registry.md)의 "ID 레지스트리" 섹션을 참조하세요. 이 콘솔 앱을 실행하면 디바이스 및 모듈 둘 다의 고유한 ID 및 키가 생성됩니다. 디바이스 및 모듈은 IoT Hub에 디바이스-클라우드 메시지를 보낼 때 이러한 값을 사용하여 자신을 식별합니다. ID는 대/소문자를 구분합니다.

1. 코드를 저장할 디렉터리를 만듭니다.

2. 이 디렉터리 내부에서 먼저 **npm init -y** 를 실행하여 기본값으로 빈 package.json을 만듭니다. 코드의 프로젝트 파일입니다.

3. **npm install -S azure-iothub\@modules-preview** 를 실행하여 **node_modules** 하위 디렉터리 내에 서비스 SDK를 설치합니다.

    > [!NOTE]
    > 하위 디렉터리 이름 node_modules는 ‘module’이라는 단어를 사용하여 “노드 라이브러리”를 나타냅니다. 여기서 이 용어는 IoT Hub 모듈과 아무 관계도 없습니다.

4. 디렉터리에 다음 .js 파일을 만듭니다. 이름을 **add.js** 로 지정합니다. 허브 연결 문자열과 허브 이름을 복사하여 붙여넣습니다.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

이 앱은 ID가 **myFirstDevice** 인 디바이스 ID와 ID가 **myFirstModule** 인 모듈 ID를 **myFirstDevice** 디바이스 아래에 만듭니다. (해당 모듈 ID가 이미 ID 레지스트리에 있는 경우 코드는 기존 모듈 정보만 검색합니다.) 그러면 앱에 해당 ID의 기본 키가 표시됩니다. 이 키를 시뮬레이션된 모듈 앱에서 사용하여 IoT Hub에 연결합니다.

node add.js를 사용하여 실행합니다. 그러면 디바이스 ID에 대한 연결 문자열과 모듈 ID에 대한 다른 연결 문자열이 제공됩니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 및 모듈 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. ID 레지스트리는 보안 자격 증명으로 사용할 디바이스 ID 및 키를 저장합니다. 또한 ID 레지스트리는 각 디바이스에 대한 액세스를 사용하지 않도록 설정하는 데 사용할 수 있는 해당 디바이스에 대한 enabled/disabled 플래그를 저장합니다. 애플리케이션이 다른 디바이스별 메타데이터를 저장해야 할 경우 애플리케이션별 스토리지를 사용해야 합니다. 모듈 ID에 대한 enabled/disabled 플래그는 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드에서 IoT Hub의 ID 레지스트리 이해](iot-hub-devguide-identity-registry.md)를 참조하세요.

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Node.js 디바이스 SDK를 사용하여 모듈 쌍 업데이트

이 섹션에서는 시뮬레이션된 디바이스에 보고된 모듈 쌍 속성을 업데이트하는 Node.js 앱을 만듭니다.

1. **모듈 연결 문자열 가져오기** - [Azure Portal](https://portal.azure.com/)에 로그인합니다. IoT Hub로 이동하고 IoT 디바이스를 클릭합니다. myFirstDevice를 찾아서 열면 성공적으로 만들어진 myFirstModule이 표시됩니다. 모듈 연결 문자열을 복사합니다. 이는 다음 단계에서 필요합니다.

   ![Azure Portal 모듈 세부 정보](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. 위 단계에서 수행한 것과 마찬가지로 디바이스 코드에 대한 디렉터리를 만들고 NPM을 사용하여 초기화한 다음, 디바이스 SDK를 설치합니다(**npm install -S azure-iot-device-amqp\@modules-preview**).

   > [!NOTE]
   > npm install 명령의 속도가 느린 것처럼 보일 수도 있습니다. 이 명령은 패키지 리포지토리에서 많은 코드를 끌어오고 있으므로 잠시 기다려 주세요.

   > [!NOTE]
   > JSON을 구문 분석하는 중 npm ERR! 레지스트리 오류가 발생했다는 오류가 표시되는 경우, 무시해도 됩니다. JSON을 구문 분석하는 중 npm ERR! 레지스트리 오류가 발생했다는 오류가 표시되는 경우, 무시해도 됩니다.

3. twin.js라는 파일을 만듭니다. 모듈 ID 문자열을 복사하여 붙여넣습니다.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. 이제 **node twin.js** 명령을 사용하여 실행합니다.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   그러면 다음과 같이 표시됩니다.

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge 시작](../iot-edge/quickstart-linux.md)
