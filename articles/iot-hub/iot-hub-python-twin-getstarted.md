---
title: Azure IoT Hub 디바이스 쌍 시작(Python) | Microsoft Docs
description: Azure IoT Hub 디바이스 쌍을 사용하여 태그를 추가한 다음, IoT Hub 쿼리를 사용하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 디바이스 앱 및 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: lizross
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 3d014e49e7d3c00ac0d2cda0b27c3102b592d58a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551690"
---
# <a name="get-started-with-device-twins-python"></a>디바이스 쌍 시작(Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서의 끝 부분에 다음의 두 Python 콘솔 앱이 설치됩니다.

* **AddTagsAndQuery.py** 는 태그를 추가하고 디바이스 쌍을 쿼리하는 Python 백 엔드 앱입니다.

* **ReportConnectivity.py** 는 앞에서 만든 디바이스 ID와 IoT Hub를 연결하고 연결 상태를 보고하는 디바이스를 시뮬레이트하는 Python 앱입니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기

이 섹션에서는 **{디바이스 ID}** 와 연결된 디바이스 쌍에 위치 메타데이터를 추가하는 Python 콘솔 앱을 만듭니다. 그런 다음 IoT Hub에 저장된 디바이스 쌍을 쿼리하여 Redmond에 있는 디바이스를 선택한 다음 셀룰러 연결을 보고하는 디바이스를 선택합니다.

1. 작업 디렉터리에서 명령 프롬프트를 열고 **Python용 Azure IoT Hub 서비스 SDK** 를 설치합니다.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. 텍스트 편집기를 사용하여 새 **AddTagsAndQuery.py** 파일을 만듭니다.

3. SDK 서비스에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. 다음 코드를 추가합니다. `[IoTHub Connection String]`을 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT hub 연결 문자열로 바꿉니다. `[Device Id]`를 [IoT 허브에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 등록한 디바이스 ID로 바꿉니다.
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. 다음 코드를 **AddTagsAndQuery.py** 파일에 추가합니다.

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    **IoTHubRegistryManager** 개체는 서비스의 디바이스 쌍과 상호 작용하는 데 필요한 모든 메서드를 표시합니다. 코드는 먼저 **IoTHubRegistryManager** 개체를 초기화한 다음, **DEVICE_ID** 에 대한 디바이스 쌍을 업데이트하고, 마지막으로 두 개의 쿼리를 실행합니다. 첫 번째는 **Redmond43** 공장에 있는 디바이스의 디바이스 쌍만 선택하고, 두 번째는 셀룰러 네트워크를 통해 연결된 디바이스만 선택하도록 쿼리를 구체화합니다.

6. **AddTagsAndQuery.py** 의 끝부분에 다음 코드를 추가하고 **iothub_service_sample_run** 함수를 구현합니다.

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. 다음으로 애플리케이션을 실행합니다.

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    **Redmond43** 에 위치한 모든 디바이스를 요청하는 쿼리에 대한 결과로는 하나의 디바이스를 보고 셀룰러 네트워크를 사용하는 디바이스에 대해서는 결과를 제한하는 쿼리에 대한 결과로는 아무 디바이스도 볼 수 없어야 합니다.

    ![Redmond의 모든 디바이스를 보여 주는 첫 번째 쿼리](./media/iot-hub-python-twin-getstarted/service-1.png)

다음 섹션에서는 연결 정보를 보고하고 이전 섹션의 쿼리 결과를 변경하는 디바이스 앱을 만듭니다.

## <a name="create-the-device-app"></a>디바이스 앱 만들기

이 섹션에서는 **{디바이스 ID}** 로 허브에 연결하는 Python 콘솔 앱을 만들고 셀룰러 네트워크를 사용하여 연결된 정보를 포함하도록 디바이스 쌍의 reported 속성을 업데이트합니다.

1. 작업 디렉터리의 명령 프롬프트에서 **Python용 Azure IoT Hub 디바이스 SDK** 를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 텍스트 편집기를 사용하여 새 **ReportConnectivity.py** 파일을 만듭니다.

3. 디바이스 SDK에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

    ```python
    import time
    from azure.iot.device import IoTHubModuleClient
    ```

4. 다음 코드를 추가합니다. `[IoTHub Device Connection String]` 자리 표시자 값을 [IoT 허브에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 복사한 디바이스 연결 문자열로 바꿉니다.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. 다음 코드를 **ReportConnectivity.py** 파일에 추가하여 클라이언트를 인스턴스화하고 디바이스 쌍 기능을 구현합니다.

    ```python
    def create_client():
        # Instantiate client
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

        # Define behavior for receiving twin desired property patches
        def twin_patch_handler(twin_patch):
            print("Twin patch received:")
            print(twin_patch)

        try:
            # Set handlers on the client
            client.on_twin_desired_properties_patch_received = twin_patch_handler
        except:
            # Clean up in the event of failure
            client.shutdown()

        return client
    ```

6. **ReportConnectivity.py** 의 끝부분에 다음 코드를 추가하여 애플리케이션을 실행합니다.

    ```python
    def main():
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        client = create_client()
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        try:
            # Update reported properties with cellular information
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            # Wait for program exit
            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ("IoT Hub Device Twin device sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()

    if __name__ == '__main__':
        main()
    ```

7. 디바이스 앱 실행:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    디바이스 쌍 reported 속성이 업데이트되었다는 확인 메시지가 표시됩니다.

    ![디바이스 앱에서 reported 속성 업데이트](./media/iot-hub-python-twin-getstarted/device-1.png)

8. 디바이스가 연결 정보를 보고했으므로 두 쿼리 모두에 나타나야 합니다. 뒤로 돌아가서 쿼리를 다시 실행합니다.

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    이번에는 **{디바이스 ID}** 가 두 쿼리 결과에 모두 나타나야 합니다.

    ![서비스 앱의 두 번째 쿼리](./media/iot-hub-python-twin-getstarted/service-2.png)

    디바이스 앱에서 서비스 앱이 보낸 desired 속성 쌍 패치가 수신되었다는 확인 메시지가 표시됩니다.

    ![디바이스 앱에서 desired 속성 수신](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 디바이스 ID를 만들었습니다. 백 엔드 앱에서 tags로 디바이스 메타데이터를 추가하고, 디바이스 쌍에서 디바이스 연결 정보를 보고하는 시뮬레이션된 디바이스 앱을 작성했습니다. 또한 레지스트리를 사용하여 이 정보를 쿼리하는 방법도 살펴보았습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) 자습서를 참조하여 디바이스에서 원격 분석을 보냅니다.

* [desired 속성을 사용하여 디바이스 구성](tutorial-device-twins.md) 자습서를 통해 디바이스 쌍의 desired 속성을 사용하여 디바이스를 구성합니다.

* [직접 메서드 사용](./quickstart-control-device.md?pivots=programming-language-python) 빠른 시작을 참조하여 대화형으로(예: 사용자가 제어하는 앱에서 팬을 켬) 디바이스를 제어합니다.