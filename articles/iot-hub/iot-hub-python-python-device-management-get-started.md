---
title: Azure IoT Hub 디바이스 관리 시작(Python) | Microsoft Docs
description: IoT Hub 디바이스 관리를 사용하여 원격 디바이스 재부팅을 시작하는 방법입니다. Python용 Azure IoT SDK를 사용하여 직접 메서드를 포함한 시뮬레이션된 디바이스 앱 및 직접 메서드를 호출하는 서비스 앱을 구현합니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: lizross
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 47aac8108bf1c5cb6bf5c0d36bafc781695ec9af
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551637"
---
# <a name="get-started-with-device-management-python"></a>디바이스 관리 시작(Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 디바이스 ID를 만듭니다.

* 디바이스를 다시 시작하는 직접 메서드가 포함된 시뮬레이트된 디바이스 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.

* IoT Hub를 통해 시뮬레이션된 디바이스 앱에서 재부팅 직접 메서드를 호출하는 Python 콘솔 앱을 만듭니다.

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

* **dmpatterns_getstarted_device.py** - 이전에 만든 디바이스 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이션하며 마지막 재부팅 시간을 보고합니다.

* **dmpatterns_getstarted_service.py** - 시뮬레이션된 디바이스 앱에 직접 메서드를 호출하고 응답을 표시하고 업데이트된 reported 속성을 표시합니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 Python 콘솔 앱 만들기

* 디바이스 다시 부팅 시뮬레이션

* reported 속성을 사용하여 디바이스 및 해당 디바이스가 마지막으로 재부팅한 시간을 확인하는 디바이스 쌍 쿼리를 사용하도록 설정

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 텍스트 편집기를 사용하여 작업 디렉터리에 **dmpatterns_getstarted_device** 라는 파일을 만듭니다.

3. 다음 `import` 문을 **dmpatterns_getstarted_device.py** 파일의 시작 부분에 추가합니다.

    ```python
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. **CONNECTION_STRING** 변수를 추가합니다. `{deviceConnectionString}` 자리 표시자 값을 디바이스 연결 문자열로 바꿉니다. 이전에 [IoT Hub에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 이 연결 문자열을 복사했을 것입니다.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. 다음 함수를 추가하여 장치에서 직접 메서드에 대해 구성된 클라이언트를 인스턴스화합니다.

    ```python
    def create_client():
        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        # Define the handler for method requests
        def method_request_handler(method_request):
            if method_request.name == "rebootDevice":
                # Act on the method by rebooting the device
                print("Rebooting device")
                time.sleep(20)
                print("Device rebooted")

                # ...and patching the reported properties
                current_time = str(datetime.datetime.now())
                reported_props = {"rebootTime": current_time}
                client.patch_twin_reported_properties(reported_props)
                print( "Device twins updated with latest rebootTime")

                # Create a method response indicating the method request was resolved
                resp_status = 200
                resp_payload = {"Response": "This is the response from the device"}
                method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            
            else:
                # Create a method response indicating the method request was for an unknown method
                resp_status = 404
                resp_payload = {"Response": "Unknown method"}
                method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)

            # Send the method response
            client.send_method_response(method_response)

        try:
            # Attach the handler to the client
            client.on_method_request_received = method_request_handler
        except:
            # In the event of failure, clean up
            client.shutdown()

        return client
    ```

6. 직접 메서드 샘플을 시작하고 대기합니다.

    ```python
    def main():
        print ("Starting the IoT Hub Python sample...")
        client = create_client()

        print ("Waiting for commands, press Ctrl-C to exit")
        try:
            # Wait for program exit
            while True:
                time.sleep(1000)
        except KeyboardInterrupt:
            print("IoTHubDeviceClient sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()

    if __name__ == '__main__':
        main()
    ```

7. **dmpatterns_getstarted_device.py** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 디바이스에서 원격 재부팅 트리거

이 섹션에서는 디바이스에서 직접 메서드를 사용하여 원격 다시 부팅을 시작하는 Python 콘솔 앱을 만듭니다. 앱은 디바이스 쌍 쿼리를 사용하여 해당 디바이스에 대한 마지막 다시 시작 시간을 검색합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-hub** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 작업 디렉터리에서 텍스트 편집기를 사용하여 **dmpatterns_getstarted_service.py** 파일을 만듭니다.

3. 다음 `import` 문을 **dmpatterns_getstarted_service.py** 파일의 시작 부분에 추가합니다.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. 다음 변수 선언을 추가합니다. `{IoTHubConnectionString}` 자리 표시자 값을 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT Hub 연결 문자열로 바꿉니다. `{deviceId}` 자리 표시자 값을 [IoT Hub에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 등록한 디바이스 ID로 바꿉니다.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. 디바이스 메서드를 호출하여 대상 디바이스를 다시 부팅한 다음, 디바이스 쌍을 쿼리하고 마지막 다시 부팅 시간을 가져오도록 다음 함수를 추가합니다.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. **dmpatterns_getstarted_service.py** 파일을 저장하고 닫습니다.

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 다시 부팅 직접 메서드에 대한 수신 대기를 시작합니다.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. 다른 명령 프롬프트에서 다음 명령을 실행하여 원격 다시 부팅을 트리거하고 디바이스 쌍을 쿼리하여 마지막 다시 부팅 시간을 찾습니다.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. 콘솔에서 직접 메서드에 대한 디바이스 응답을 확인합니다.

   다음은 재부팅 직접 방법에 대한 디바이스 응답을 보여 줍니다.

   ![시뮬레이트한 디바이스 앱 출력](./media/iot-hub-python-python-device-management-get-started/device.png)

   다음은 재부팅 직접 방법을 호출하고 상태에 대해 디바이스 쌍을 폴링하는 서비스를 보여 줍니다.

   ![재부팅 서비스 트리거 출력](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
