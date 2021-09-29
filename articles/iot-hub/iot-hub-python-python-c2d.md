---
title: Azure IoT Hub(Python)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: Python용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 시뮬레이션된 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 921aa2be168261acad12fa8e1a3ae478656b968c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236902"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub(Python)를 사용하여 클라우드-디바이스 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) 빠른 시작에서는 IoT Hub를 만들고 그 안에 디바이스 ID를 프로비저닝하고 디바이스-클라우드 메시지를 보내는 시뮬레이션된 디바이스 앱을 코딩하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python)를 기반으로 합니다. 이 항목에서는 다음 방법을 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

[IoT Hub 개발자 가이드에서 클라우드-디바이스 메시지](iot-hub-devguide-messaging.md)에 대한 자세한 내용을 찾아볼 수 있습니다.

이 자습서의 끝 부분에서 다음의 두 Python 콘솔 앱을 실행합니다.

* **SimulatedDevice.py** - [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python)에서 만든 앱의 수정 버전입니다. IoT Hub에 연결하고 클라우드-디바이스 메시지를 수신합니다.

* **SendCloudToDeviceMessage.py** - IoT Hub를 통해 시뮬레이션된 디바이스 앱에 클라우드-디바이스 메시지를 보냅니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱에서 메시지 수신

이 섹션에서는 디바이스를 시뮬레이션하고 IoT Hub에서 클라우드-디바이스 메시지를 수신하는 Python 콘솔 앱을 만듭니다.

1. 작업 디렉터리의 명령 프롬프트에서 **Python용 Azure IoT Hub 디바이스 SDK** 를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 텍스트 편집기를 사용하여 **SimulatedDevice.py** 파일을 만듭니다.

1. **SimulatedDevice.py** 파일의 시작 부분에 다음 `import` 문 및 변수를 추가합니다.

    ```python
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. 다음 코드를 **SimulatedDevice.py** 파일에 추가합니다. `{deviceConnectionString}` 자리 표시자 값을 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) 빠른 시작에서 만든 디바이스에 대한 디바이스 연결 문자열로 바꿉니다.

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. 수신된 메시지를 콘솔에 인쇄하는 데 사용할 다음 함수를 정의합니다.

    ```python
    def message_handler(message):
        global RECEIVED_MESSAGES
        RECEIVED_MESSAGES += 1
        print("")
        print("Message received:")

        # print data from both system and application (custom) properties
        for property in vars(message).items():
            print ("    {}".format(property))

        print("Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

1. 다음 코드를 추가하여 클라이언트를 초기화하고 클라우드-디바이스 메시지를 수신하기 위해 대기합니다.

    ```python
    def main():
        print ("Starting the Python IoT Hub C2D Messaging device sample...")

        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        print ("Waiting for C2D messages, press Ctrl-C to exit")
        try:
            # Attach the handler to the client
            client.on_message_received = message_handler

            while True:
                time.sleep(1000)
        except KeyboardInterrupt:
            print("IoT Hub C2D Messaging device sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()
    ```

1. 다음 main 함수를 추가합니다.

    ```python
    if __name__ == '__main__':
        main()
    ```

1. **SimulatedDevice.py** 파일을 저장하고 닫습니다.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python)에서 만든 IoT Hub를 통해 클라우드-디바이스 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-디바이스 메시지를 보내려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub는 이 사용 권한을 부여하는 **service** 라는 공유 액세스 정책을 사용하여 만듭니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이 섹션에서는 클라우드-디바이스 메시지를 시뮬레이션된 디바이스 앱으로 보내는 Python 콘솔 응용 프로그램을 만듭니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) 빠른 시작에서 추가한 디바이스의 디바이스 ID가 필요합니다. 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT Hub 연결 문자열도 필요합니다.

1. 작업 디렉터리에서 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK** 를 설치합니다.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. 텍스트 편집기를 사용하여 **SendCloudToDeviceMessage.py** 파일을 만듭니다.

1. **SendCloudToDeviceMessage.py** 파일 앞에 다음 `import` 문 및 변수를 추가합니다.

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. **SendCloudToDeviceMessage.py** 파일에 다음 코드를 추가합니다. `{iot hub connection string}` 및 `{device id}` 자리 표시자 값을 앞에서 기록해 둔 IoT Hub 연결 문자열 및 디바이스 ID로 바꿉니다.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. 다음 코드를 추가하여 디바이스에 메시지를 보냅니다.

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. 다음 main 함수를 추가합니다.

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. **SendCloudToDeviceMessage.py** 파일을 저장한 후 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 작업 디렉터리의 명령 프롬프트에서 다음 명령을 실행하여 클라우드-디바이스 메시지를 수신 대기합니다.

    ```shell
    python SimulatedDevice.py
    ```

    ![시뮬레이션된 디바이스 앱 실행](./media/iot-hub-python-python-c2d/device-1.png)

1. 작업 디렉터리에서 새 명령 프롬프트를 열고 다음 명령을 실행하여 클라우드-디바이스 메시지를 보냅니다.

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![앱을 실행하여 클라우드-디바이스 명령 보내기](./media/iot-hub-python-python-c2d/service.png)

1. 디바이스에서 수신한 메시지를 기록해 둡니다.

    ![수신된 메시지](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.