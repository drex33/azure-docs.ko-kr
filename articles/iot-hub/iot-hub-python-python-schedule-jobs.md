---
title: Azure IoT Hub(Python)를 사용하여 작업 예약 | Microsoft Docs
description: 여러 디바이스에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하는 방법입니다. Python용 Azure IoT SDK를 사용하여 시뮬레이션된 디바이스 앱 및 작업을 실행하는 서비스 앱을 구현합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 305d3103e9f0f0bdfb3ce49f5c801ca0f2f975ff
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835244"
---
# <a name="schedule-and-broadcast-jobs-python"></a>작업 예약 및 브로드캐스트(Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub는 백 엔드 앱에서 수백만 개의 디바이스를 예약 및 업데이트하는 작업을 만들고 추적할 수 있게 하는 완전히 관리되는 서비스입니다.  작업(job)은 다음과 같은 작업(action)에 사용될 수 있습니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

개념적으로 작업(job)은 이러한 작업(action) 중 하나를 래핑하고 디바이스 쌍 쿼리로 정의된 디바이스 집합에 대해 실행 진행 상태를 추적합니다.  예를 들어 백 엔드 앱은 작업을 사용하여 디바이스 쌍 쿼리로 지정되고 향후에 예약된 10,000개 디바이스에서 다시 부팅 메서드를 호출할 수 있습니다.  그런 다음 애플리케이션은 해당하는 각 디바이스에서 재부팅 메서드를 수신 및 실행함에 따라 진행 상태를 추적할 수 있습니다.

이러한 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

* 디바이스 쌍 및 속성: [ 디바이스 쌍 시작](iot-hub-python-twin-getstarted.md) 및 [자습서: 디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)

* 직접 메서드: [IoT Hub 개발자 가이드 - 직접 메서드](iot-hub-devguide-direct-methods.md) 및 [빠른 시작: 직접 메서드](./quickstart-control-device.md?pivots=programming-language-python)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법을 보여 줍니다.

* 솔루션 백 엔드에서 **LockDoor** 를 호출할 수 있는 직접 메서드가 포함된 Python 시뮬레이션된 디바이스 앱을 만듭니다.

* 작업을 사용하여 시뮬레이션된 디바이스 앱에서 **LockDoor** 직접 메서드를 호출하고, 디바이스 작업을 사용하여 desired 속성을 업데이트하는 Python 콘솔 앱을 만듭니다.

이 자습서가 끝나면 다음과 같은 두 개의 Python 앱이 생깁니다.

**simDevice.py** - 디바이스 ID로 IoT Hub에 연결하고 **LockDoor** 직접 메서드를 수신합니다.

**scheduleJobService.py** 는 시뮬레이션된 디바이스 앱에서 직접 메서드를 호출하고 작업을 사용하여 디바이스 쌍의 desired 속성을 업데이트합니다.

> [!NOTE]
> **Python용 Azure IoT SDK** 는 **작업** 기능을 직접 지원하지 않습니다. 이 자습서에서는 비동기 스레드 및 타이머를 사용하는 대체 솔루션을 제공합니다. 추가 업데이트는 [Python용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) 페이지에서 **Service Client SDK** 기능 목록을 참조하세요.
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 클라우드에서 호출한 메서드에 응답하는 Python 콘솔 앱을 만듭니다. 이 메서드는 시뮬레이션된 **LockDoor** 메서드를 트리거합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 텍스트 편집기를 사용하여 작업 디렉터리에 새 **simDevice.py** 파일을 만듭니다.

3. **simDevice.py** 파일의 시작 부분에 다음 `import` 문 및 변수를 추가합니다. `deviceConnectionString`을 위에서 만든 디바이스의 연결 문자열로 바꿉니다.

    ```python
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 클라이언트를 인스턴스화하고, **lockDoor** 메서드에 응답하며 디바이스 쌍 업데이트를 수신하도록 클라이언트를 구성하는 다음 함수를 정의합니다.

    ```python
    def create_client():
        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        # Define behavior for responding to the lockDoor direct method
        def method_request_handler(method_request):
            if method_request.name == "lockDoor":
                print("Locking Door!")

                resp_status = 200
                resp_payload = {"Response": "lockDoor called successfully"}
                method_response = MethodResponse.create_from_method_request(
                    method_request=method_request,
                    status=resp_status,
                    payload=resp_payload
                )
                client.send_method_response(method_response)

        # Define behavior for receiving a twin patch
        def twin_patch_handler(twin_patch):
            print("")
            print("Twin desired properties patch received:")
            print(twin_patch)

        # Set the handlers on the client
        try:
            print("Beginning to listen for 'lockDoor' direct method invocations...")
            client.on_method_request_received = method_request_handler
            print("Beginning to listen for updates to the Twin desired properties...")
            client.on_twin_desired_properties_patch_received = twin_patch_handler
        except:
            # If something goes wrong while setting the handlers, clean up the client
            client.shutdown()
            raise
    ```

5. 다음 코드를 추가하여 샘플을 실행합니다.

    ```python
    def main():
        print ("Starting the IoT Hub Python jobs sample...")
        client = create_client()

        print ("IoTHubDeviceClient waiting for commands, press Ctrl-C to exit")
        try:
            while True:
                time.sleep(100)
        except KeyboardInterrupt:
            print("IoTHubDeviceClient sample stopped!")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()


    if __name__ == '__main__':
        main()
    ```

6. **simDevice.py** 파일을 저장하고 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
>

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 디바이스에서 직접 메서드를 호출하고 디바이스 쌍을 업데이트하는 백 엔드 서비스를 만듭니다. 서비스는 디바이스에서 직접 메서드를 호출하려면 **서비스 연결** 권한이 필요합니다. 또한 서비스에는 ID 레지스트리를 읽고 쓰기 위한 **레지스트리 읽기** 및 **레지스트리 쓰기** 권한이 필요합니다. 이러한 권한만 포함하는 기본 공유 액세스 정책은 없으므로, 만들어야 합니다.

**서비스 연결** 및 **레지스트리 읽기** 및 **레지스트리 쓰기** 권한을 부여하는 공유 액세스 정책을 만들고, 이 정책에 대한 연결 문자열을 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT 허브를 엽니다. IoT 허브로 가져오는 가장 쉬운 방법은 **리소스 그룹** 을 선택하고 IoT 허브가 위치한 리소스 그룹에서 리소스 그룹을 선택한 다음, 리소스 목록에서 IoT 허브를 선택하는 것입니다.

2. IoT Hub의 왼쪽 창에서 **공유 액세스 정책** 을 선택합니다.

3. 정책 목록 위의 상단 메뉴에서 **추가** 를 선택합니다.

4. **공유 액세스 정책 추가** 창에서 정책에 대한 설명형 이름(예: *serviceAndRegistryReadWrite*)을 입력합니다. **사용 권한** 아래에서 **서비스 연결** 및 **레지스트리 쓰기** 를 선택합니다 (**레지스트리 쓰기** 를 선택하면 **레지스트리 읽기** 가 자동으로 선택됨). 그런 다음 **생성** 를 선택합니다.

    ![새 공유 액세스 정책을 추가하는 방법 표시](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. 다시 **공유 액세스 정책** 창의 정책 목록에서 새 정책을 선택합니다.

6. **공유 액세스 키** 에서 **연결 문자열 -- 기본 키** 에 대한 복사 아이콘을 선택하고 값을 저장합니다.

    ![연결 문자열을 검색하는 방법 표시](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

IoT Hub 공유 액세스 정책 및 사용 권한에 대한 자세한 내용은 [액세스 제어 및 권한](./iot-hub-dev-guide-sas.md#access-control-and-permissions)을 참조하세요.

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>직접 메서드를 호출하고 디바이스 쌍의 속성을 업데이트하기 위한 작업 예약

이 섹션에서는 직접 메서드를 사용하여 디바이스에서 원격 **LockDoor** 를 시작하는 Python 콘솔 앱을 만들고 디바이스 쌍의 desired 속성을 업데이트합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-hub** 패키지를 설치합니다.

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. 텍스트 편집기를 사용하여 작업 디렉터리에 새 **scheduleJobService.py** 파일을 만듭니다.

3. **scheduleJobService.py** 파일의 시작 부분에 다음 `import` 문 및 변수를 추가합니다. `{IoTHubConnectionString}` 자리 표시자 값을 이전에 [IoT 허브 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT 허브 연결 문자열로 바꿉니다. `{deviceId}` 자리 표시자 값을 [IoT 허브에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 등록한 디바이스 ID로 바꿉니다.

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. 디바이스를 조회하는 데 사용되는 다음 함수를 추가합니다.

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. 직접 메서드와 디바이스 쌍을 호출하는 작업을 실행하는 다음 메서드를 추가합니다.

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. 작업을 예약하고 작업 상태를 업데이트하도록 다음 코드를 추가합니다. `main` 루틴도 포함하세요.

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. **scheduleJobService.py** 파일을 저장하고 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 작업 디렉터리의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신을 시작합니다.

    ```cmd/sh
    python simDevice.py
    ```

2. 작업 디렉터리의 명령 프롬프트에서 다음 명령을 실행하여 도어를 잠그고 쌍을 업데이트하는 작업을 트리거합니다.
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. 콘솔에서 직접 메서드와 디바이스 쌍 업데이트에 대한 디바이스 응답을 확인합니다.

    ![Azure IoT Hub Job 샘플 1 -- 디바이스 출력](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Azure IoT Hub Job 샘플 2 -- 디바이스 출력](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에 대한 직접 메서드를 예약하고 디바이스 쌍의 속성을 업데이트하는 데 작업을 사용했습니다.

IoT Hub 및 디바이스 관리 패턴(예: 원격 무선 펌웨어 업데이트)을 계속 시작하려면 [자습서: 펌웨어 업데이트를 수행하는 방법](tutorial-firmware-update.md)을 참조하세요.