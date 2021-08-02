---
title: Azure IoT Hub 디바이스 관리 시작(노드) | Microsoft Docs
description: IoT Hub 디바이스 관리를 사용하여 원격 디바이스 재부팅을 시작하는 방법입니다. Node.js용 Azure IoT 디바이스 SDK를 사용하여 직접 메서드를 포함한 시뮬레이션된 디바이스 앱 및 직접 메서드를 호출하는 서비스 앱을 구현합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 8d551de4e467d602c40fc40889bfc36e5cf69a1d
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112075604"
---
# <a name="get-started-with-device-management-nodejs"></a>디바이스 관리 시작(Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* [Azure Portal](https://portal.azure.com)을 사용하여 IoT Hub를 만들고 IoT Hub에 디바이스 ID를 만듭니다.

* 디바이스를 다시 시작하는 직접 메서드가 포함된 시뮬레이트된 디바이스 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.

* IoT 허브를 통해 시뮬레이션된 디바이스 앱에서 재부팅 직접 메서드를 호출하는 Node.js 콘솔 앱을 만듭니다.

이 자습서를 마치면 두 가지 Node.js 콘솔 앱이 만들어집니다.

* **dmpatterns_getstarted_device.js** - 이전에 만든 디바이스 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이션하며 마지막 재부팅 시간을 보고합니다.

* **dmpatterns_getstarted_service.js**, 시뮬레이션된 디바이스 앱에 직접 메서드를 호출하고 응답을 표시하고 업데이트된 reported 속성을 표시합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Node.js 버전 10.0.x 이상. Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)에서 설명합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Node.js 콘솔 앱을 만듭니다.

* 시뮬레이션된 디바이스 재부팅을 트리거합니다.

* reported 속성을 사용하여 디바이스 및 해당 디바이스가 마지막으로 재부팅한 시간을 확인하는 디바이스 쌍 쿼리를 사용하도록 설정합니다.

1. **manageddevice** 라는 빈 폴더를 만듭니다.  **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

2. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 디바이스 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. 텍스트 편집기를 사용하여 **manageddevice** 폴더에 **dmpatterns_getstarted_device.js** 파일을 만듭니다.

4. 다음 'require' 문을 **dmpatterns_getstarted_device.js** 파일의 시작 부분에 추가합니다.

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. **connectionString** 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다.  `{yourdeviceconnectionstring}` 자리 표시자 값을 이전에 [IoT Hub에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 복사한 디바이스 연결 문자열로 바꿉니다.  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. 디바이스에서 직접 메서드를 구현하도록 다음 함수를 추가합니다.

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. IoT Hub에 대한 연결을 열고 직접 메서드 수신기를 시작합니다.

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. **dmpatterns_getstarted_device.js** 파일을 저장 후 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 디바이스에서 원격 재부팅 트리거

이 섹션에서는 디바이스에서 직접 메서드를 사용하여 원격 다시 시작을 시작하는 Node.js 콘솔 앱을 만듭니다. 앱은 디바이스 쌍 쿼리를 사용하여 해당 디바이스에 대한 마지막 다시 시작 시간을 검색합니다.

1. **triggerrebootondevice** 라는 빈 폴더를 만듭니다. **triggerrebootondevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

2. **triggerrebootondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 디바이스 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. 텍스트 편집기를 사용하여 **triggerrebootondevice** 폴더에 **dmpatterns_getstarted_service.js** 파일을 만듭니다.

4. 다음 'require' 문을 **dmpatterns_getstarted_service.js** 파일의 시작 부분에 추가합니다.

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. 다음 변수 선언을 추가하고 `{iothubconnectionstring}` 자리 표시자 값을 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT Hub 연결 문자열로 바꿉니다.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. 대상 디바이스를 재부팅하기 위해 디바이스 메서드를 호출하도록 다음 함수를 추가합니다.

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. 디바이스를 쿼리하고 마지막 재부팅 시간을 가져오도록 다음 함수를 추가합니다.

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. 다시 시작 직접 메서드를 트리거하고 마지막 다시 시작 시간을 쿼리하는 함수를 호출하도록 다음 코드를 추가합니다.

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. **dmpatterns_getstarted_service.js** 파일을 저장 후 닫습니다.

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. **triggerrebootondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 원격 재부팅을 트리거하고 마지막 재부팅 시간을 찾기 위해 디바이스 쌍에 대한 쿼리를 수행합니다.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. 콘솔에서 재부팅 직접 방법에 대한 디바이스 응답 및 재부팅 상태가 표시됩니다.

   다음은 서비스에서 보낸 재부팅 직접 방법에 대한 디바이스 응답을 보여 줍니다.

   ![manageddevice 앱 출력](./media/iot-hub-node-node-device-management-get-started/device.png)

   다음은 재부팅을 트리거하는 서비스와 마지막 재부팅 시간 동안 디바이스 쌍을 폴링하는 경우를 보여 줍니다.

   ![triggerrebootondevice 앱 출력](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
