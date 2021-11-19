---
title: Azure IoT Hub(노드)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: Node.js용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 시뮬레이션된 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 741599ddc93ed7fe201bf95daa8f0731056e53f1
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132807582"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>IoT Hub를 사용하여 클라우드-디바이스 메시지 보내기(Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) 빠른 시작에서는 IoT Hub를 만들고 그 안에 디바이스 ID를 프로비저닝하고 디바이스-클라우드 메시지를 보내는 시뮬레이션된 디바이스 앱을 코딩하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)를 기반으로 합니다. 이 항목에서는 다음 방법을 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.
* 디바이스에서 클라우드-디바이스 메시지를 받습니다.
* 솔루션 백 엔드에서, IoT Hub에서 디바이스로 보낸 메시지에 대한 배달 확인(*피드백*)을 요청합니다.

[IoT Hub 개발자 가이드에서 클라우드-디바이스 메시지](iot-hub-devguide-messaging.md)에 대한 자세한 내용을 찾아볼 수 있습니다.

이 자습서의 끝 부분에서는 다음 두 개의 Node.js 콘솔 앱을 실행합니다.

* **SimulatedDevice** - [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)에서 만든 앱의 수정 버전입니다. IoT Hub에 연결하고 클라우드-디바이스 메시지를 수신합니다.

* **SendCloudToDeviceMessage**, IoT Hub를 통해 시뮬레이션된 디바이스 앱에 클라우드-디바이스 메시지를 보낸 다음, 배달 승인을 수신합니다.

> [!NOTE]
> IoT Hub는 Azure IoT 디바이스 SDK를 통해 많은 디바이스 플랫폼 및 언어(C, Java, Python 및 Javascript 포함)를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot)를 참조하세요.
>

## <a name="prerequisites"></a>필수 구성 요소

* Node.js 버전 10.0.x 이상. Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-node/tree/main/doc/node-devbox-setup.md)에서 설명합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial)을 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱에서 메시지 수신

이 섹션에서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)에서 만든 시뮬레이션된 디바이스 앱을 수정하고 IoT Hub로부터 클라우드-디바이스 메시지를 수신합니다.

1. 텍스트 편집기를 사용하여 **SimulatedDevice.js** 파일을 엽니다. 이 파일은 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) 빠른 시작에서 다운로드한 Node.js 샘플 코드의 루트 폴더에 있는 **iot-hub\Quickstarts\simulated-device** 폴더에 있습니다.

2. IoT Hub에서 보낸 메시지를 수신하려면 디바이스 클라이언트에 처리기를 등록합니다. 다음 코드 조각과 같이 디바이스 클라이언트를 만드는 줄 바로 뒤에 `client.on`에 대한 호출을 추가합니다.

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

이 예제에서 디바이스는 메시지를 처리하고 디바이스 큐에서 안전하게 제거할 수 있음을 IoT Hub에 알리기 위해 **complete** 함수를 호출합니다. MQTT 전송을 사용하고 있는 경우 **complete** 호출이 필요하지 않으므로 건너 뛰어도 됩니다. HTTPS 및 AMQP에 필요합니다.

AMQP 및 HTTPS를 사용하지만 MQTT를 사용하지 않는 경우에는 디바이스가 다음을 수행할 수도 있습니다.

* 메시지 중단 - IoT Hub가 나중에 사용하기 위해 디바이스 큐에 메시지를 보관합니다.
* 메시지 거부 - 디바이스 큐에서 메시지가 영구적으로 제거됩니다.

디바이스에서 메시지를 완료, 중단 또는 거부할 수 없도록 하는 문제가 발생할 경우 IoT Hub는 정해진 시간 제한 기간이 지나면 메시지를 다시 배달하도록 큐에 넣습니다. 이런 이유로 디바이스 앱의 메시지 처리 논리는 *idempotent* 이므로 같은 메시지를 여러 번 수신해도 동일한 결과가 생성됩니다.

클라우드-디바이스 메시지 수명 주기의 세부 정보를 포함하여 IoT Hub에서 클라우드-디바이스 메시지를 처리하는 방법에 대한 자세한 내용은 [IoT Hub에서 클라우드-디바이스 메시지 보내기](iot-hub-devguide-messages-c2d.md)를 참조하세요.
  
> [!NOTE]
> MQTT 또는 AMQP 대신 HTTPS를 전송으로 사용하는 경우 **DeviceClient** 인스턴스는 IoT Hub의 메시지를 자주(최소 25분 간격) 확인합니다. MQTT, AMQP, HTTPS 지원 간 차이점에 대한 자세한 내용은 [클라우드-디바이스 통신 지침](iot-hub-devguide-c2d-guidance.md) 및 [통신 프로토콜 선택](iot-hub-devguide-protocols.md)을 참조하세요.
>

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)에서 만든 IoT Hub를 통해 클라우드-디바이스 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-디바이스 메시지를 보내려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub는 이 사용 권한을 부여하는 **service** 라는 공유 액세스 정책을 사용하여 만듭니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이 섹션에서는 클라우드-디바이스 메시지를 시뮬레이트된 디바이스 앱으로 보내는 Node.js 콘솔 앱을 만듭니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) 빠른 시작에서 추가한 디바이스의 디바이스 ID가 필요합니다. 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT Hub 연결 문자열도 필요합니다.

1. **sendcloudtodevicemessage** 라는 빈 폴더를 만듭니다. **sendcloudtodevicemessage** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```shell
    npm init
    ```

2. **sendcloudtodevicemessage** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.

    ```shell
    npm install azure-iothub --save
    ```

3. 텍스트 편집기를 사용하여 **sendcloudtodevicemessage** 폴더에 **SendCloudToDeviceMessage.js** 파일을 만듭니다.

4. **SendCloudToDeviceMessage.js** 파일 앞에 다음 `require` 문을 추가합니다.

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. **SendCloudToDeviceMessage.js** 파일에 다음 코드를 추가합니다. "{iot hub connection string}" 및 "{device id}" 자리 표시자 값을 앞에서 적어 둔 IoT Hub 연결 문자열 및 디바이스 ID로 바꿉니다.

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 다음 함수를 추가하여 작업 결과를 콘솔에 출력합니다.

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 다음 함수를 추가하여 배달 피드백 메시지를 콘솔에 출력합니다.

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 다음 코드를 추가하여 디바이스에 메시지를 보내고 디바이스가 클라우드-디바이스 메시지를 승인할 때 피드백 메시지를 처리합니다.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. **SendCloudToDeviceMessage.js** 파일을 저장한 후 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. **simulated-device** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에 원격 분석을 보내고 클라우드-디바이스 메시지를 수신합니다.

    ```shell
    node SimulatedDevice.js
    ```

    ![시뮬레이션된 디바이스 앱 실행](./media/iot-hub-node-node-c2d/receivec2d.png)

2. 명령 프롬프트의 **sendcloudtodevicemessage** 폴더에서 다음 명령을 실행하여 클라우드-장치 메시지를 보내고 승인 피드백을 대기합니다.

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![앱을 실행하여 클라우드-디바이스 명령 보내기](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.
   >

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.