---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: f97582f2ca181e8ecc443cd9d41ea6a5fa131645
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132876166"
---
[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/main/azure-iot-device/samples/pnp)

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

- [Python](https://www.python.org/) 버전 3.7 이상이 설치된 개발 머신. 명령줄에서 `python --version` 명령을 실행하여 버전을 확인할 수 있습니다. Python은 다양한 운영 체제에 사용할 수 있습니다. 이 자습서의 지침에서는 Windows 명령 프롬프트에서 **python** 명령을 실행한다고 가정합니다.

- 샘플 코드가 포함된 [Python용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub 리포지토리의 로컬 복사본입니다. 다음 링크를 사용하여 리포지토리의 복사본을 다운로드합니다. [ZIP 다운로드](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). 그런 다음, 로컬 머신의 적절한 위치에 파일의 압축을 풉니다.

## <a name="review-the-code"></a>코드 검토

이전에 다운로드한 Python용 Microsoft Azure IoT SDK 복사본의 텍스트 편집기에서 *azure-iot-sdk-python/azure-iot-device/samples/pnp/temp_controller_with_thermostats.py* 파일을 엽니다.

샘플을 실행하여 IoT Central에 연결하면 DPS(Device Provisioning Service)를 사용하여 디바이스를 등록하고 연결 문자열을 생성합니다. 이 샘플은 명령줄 환경에서 필요한 DPS 연결 정보를 검색합니다.

`main` 함수:

* DPS를 사용하여 디바이스를 프로비저닝합니다. 프로비저닝 정보에는 모델 ID가 포함됩니다. IoT Central은 모델 ID를 사용하여 이 디바이스에 대한 디바이스 템플릿을 식별하거나 생성합니다. 자세한 내용은 [디바이스 템플릿과 디바이스 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)을 참조하세요.
* 연결을 열기 전에 `Device_client` 개체를 만들고 `dtmi:com:example:TemperatureController;2` 모델 ID를 설정합니다.
* IoT Central로 초기 속성 값을 보냅니다. `pnp_helper`를 사용하여 패치를 만듭니다.
* `getMaxMinReport` 및 `reboot` 명령에 대한 수신기를 만듭니다. 각 자동 온도 조절기 구성 요소에는 자체 `getMaxMinReport` 명령이 있습니다.
* 쓰기 가능한 속성 업데이트를 수신할 속성 수신기를 만듭니다.
* 2개의 자동 온도 조절기 구성 요소에서 온도 원격 분석을 보내고 8초마다 기본 구성 요소에서 작업 집합 원격 분석을 보내는 루프를 시작합니다.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":
            print("Device was assigned")
            print(registration_result.registration_state.assigned_hub)
            print(registration_result.registration_state.device_id)
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":
        # ...

    # Connect the client.
    await device_client.connect()

    ################################################
    # Update readable properties from various components

    properties_root = pnp_helper.create_reported_properties(serialNumber=serial_number)
    properties_thermostat1 = pnp_helper.create_reported_properties(
        thermostat_1_component_name, maxTempSinceLastReboot=98.34
    )
    properties_thermostat2 = pnp_helper.create_reported_properties(
        thermostat_2_component_name, maxTempSinceLastReboot=48.92
    )
    properties_device_info = pnp_helper.create_reported_properties(
        device_information_component_name,
        swVersion="5.5",
        manufacturer="Contoso Device Corporation",
        model="Contoso 4762B-turbo",
        osName="Mac Os",
        processorArchitecture="x86-64",
        processorManufacturer="Intel",
        totalStorage=1024,
        totalMemory=32,
    )

    property_updates = asyncio.gather(
        device_client.patch_twin_reported_properties(properties_root),
        device_client.patch_twin_reported_properties(properties_thermostat1),
        device_client.patch_twin_reported_properties(properties_thermostat2),
        device_client.patch_twin_reported_properties(properties_device_info),
    )

    ################################################
    # Get all the listeners running
    print("Listening for command requests and property updates")

    global THERMOSTAT_1
    global THERMOSTAT_2
    THERMOSTAT_1 = Thermostat(thermostat_1_component_name, 10)
    THERMOSTAT_2 = Thermostat(thermostat_2_component_name, 10)

    listeners = asyncio.gather(
        execute_command_listener(
            device_client, method_name="reboot", user_command_handler=reboot_handler
        ),
        execute_command_listener(
            device_client,
            thermostat_1_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_command_listener(
            device_client,
            thermostat_2_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    ################################################
    # Function to send telemetry every 8 seconds

    async def send_telemetry():
        print("Sending telemetry from various components")

        while True:
            curr_temp_ext = random.randrange(10, 50)
            THERMOSTAT_1.record(curr_temp_ext)

            temperature_msg1 = {"temperature": curr_temp_ext}
            await send_telemetry_from_temp_controller(
                device_client, temperature_msg1, thermostat_1_component_name
            )

            curr_temp_int = random.randrange(10, 50)  # Current temperature in Celsius
            THERMOSTAT_2.record(curr_temp_int)

            temperature_msg2 = {"temperature": curr_temp_int}

            await send_telemetry_from_temp_controller(
                device_client, temperature_msg2, thermostat_2_component_name
            )

            workingset_msg3 = {"workingSet": random.randrange(1, 100)}
            await send_telemetry_from_temp_controller(device_client, workingset_msg3)

    send_telemetry_task = asyncio.ensure_future(send_telemetry())

    # ...
```

`provision_device` 함수는 DPS를 사용하여 디바이스를 프로비저닝하고 IoT Central에 등록합니다. 이 함수에는 IoT Central이 프로비저닝 페이로드에서 [디바이스를 디바이스 템플릿과 연결](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)하는 데 사용하는 디바이스 모델 ID가 포함됩니다.

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )

    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener` 함수는 명령 요청을 처리하고, 디바이스에서 자동 온도 조절기 구성 요소에 대한 `getMaxMinReport` 명령을 수신할 때 `max_min_handler` 함수를 실행하고 디바이스에서 `reboot` 명령을 수신할 때 `reboot_handler` 함수를 실행합니다. `pnp_helper` 모듈을 사용하여 응답을 작성합니다.

```python
async def execute_command_listener(
    device_client,
    component_name=None,
    method_name=None,
    user_command_handler=None,
    create_user_response_handler=None,
):
    while True:
        if component_name and method_name:
            command_name = component_name + "*" + method_name
        elif method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        values = command_request.payload
        print(values)

        if user_command_handler:
            await user_command_handler(values)
        else:
            print("No handler provided to execute")

        (response_status, response_payload) = pnp_helper.create_response_payload_with_status(
            command_request, method_name, create_user_response=create_user_response_handler
        )

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener`는 자동 온도 조절기 구성 요소에 대한 `targetTemperature`와 같은 쓰기 가능한 속성 업데이트를 처리하고 JSON 응답을 생성합니다. `pnp_helper` 모듈을 사용하여 응답을 작성합니다.

```python
async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        print(patch)
        properties_dict = pnp_helper.create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

`send_telemetry_from_temp_controller` 함수는 원격 분석 메시지를 자동 온도 조절기 구성 요소에서 IoT Central로 보냅니다. `pnp_helper` 모듈을 사용하여 메시지를 작성합니다.

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = pnp_helper.create_telemetry(telemetry_msg, component_name)
    await device_client.send_message(msg)
    print("Sent message")
    print(msg)
    await asyncio.sleep(5)
```

## <a name="get-connection-information"></a>연결 정보 가져오기

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>코드 실행

샘플 애플리케이션을 실행하려면 명령줄 환경을 열고 *temp_controller_with_thermostats.py* 샘플 파일이 포함된 *azure-iot-sdk-python/azure-iot-device/samples/pnp* 폴더로 이동합니다.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

필요한 패키지를 설치합니다.

```cmd/sh
pip install azure-iot-device
```

샘플을 실행합니다.

```cmd/sh
python temp_controller_with_thermostats.py
```

다음 출력은 IoT Central에 등록하고 연결하는 디바이스를 보여줍니다. 샘플은 원격 분석 전송을 시작하기 전에 두 자동 온도 조절기 구성 요소의 `maxTempSinceLastReboot` 속성을 보냅니다.

```cmd/sh
Device was assigned
iotc-60a.....azure-devices.net
sample-device-01
Updating pnp properties for root interface
{'serialNumber': 'alohomora'}
Updating pnp properties for thermostat1
{'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
Updating pnp properties for thermostat2
{'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
Updating pnp properties for deviceInformation
{'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
Listening for command requests and property updates
Press Q to quit
Sending telemetry from various components
Sent message
{"temperature": 27}
Sent message
{"temperature": 17}
Sent message
{"workingSet": 13}
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

다음과 같이 디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다.

```cmd/sh
{'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
the data in the desired properties patch was: {'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
Values received are :-
{'targetTemperature': 67, '__t': 'c'}
Sent message

...

Command request received with payload
2021-03-31T05:00:00.000Z
Will return the max, min and average temperature from the specified time 2021-03-31T05:00:00.000Z to the current time
Done generating
{"avgTemp": 4.0, "endTime": "2021-03-31T12:29:48.322427", "maxTemp": 18, "minTemp": null, "startTime": "2021-03-31T12:28:28.322381"}
```
