---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 59e353b7c052d41c1478473c2636fc81200ae327
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132875409"
---
IoT 플러그 앤 플레이를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 모델과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 Python을 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

이 빠른 시작을 완료하려면 개발 머신에 Python 3.7이 필요합니다. [python.org](https://www.python.org/)에서 여러 플랫폼에 권장되는 최신 버전을 다운로드할 수 있습니다. 다음 명령을 사용하여 현재 Python 버전을 확인할 수 있습니다.  

```cmd/sh
python --version
```

**azure-iot-device** 패키지가 PIP로 게시됩니다.

로컬 Python 환경에서 다음과 같이 패키지를 설치합니다.

```cmd/sh
pip install azure-iot-device
```

다음 명령을 실행하여 **azure-iot-hub** 패키지를 설치합니다.

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure/azure-iot-sdk-python/blob/main/azure-iot-device/samples/pnp/README.md)를 참조하세요.

이 빠른 시작에서는 Python으로 작성한 샘플 자동 온도 조절 디바이스를 IoT 플러그 앤 플레이 디바이스로 사용합니다. 샘플 디바이스를 실행하려면 다음을 수행합니다.

1. 원하는 폴더에서 터미널 창을 엽니다. 다음 명령을 실행하여 [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub 리포지토리를 다음 위치에 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. 이 터미널 창은 이제 **디바이스** 터미널로 사용됩니다. 복제된 리포지토리의 폴더로 이동하여 */azure-iot-sdk-python/azure-iot-device/samples/pnp* 폴더로 이동합니다.

1. 다음 명령을 사용하여 샘플 자동 온도 조절 디바이스를 실행합니다.

    ```cmd/sh
    python simple_thermostat.py
    ```

1. 디바이스에서 일부 정보를 보냈으며 현재 온라인 상태임을 보고했다는 메시지가 표시됩니다. 이 메시지는 디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었음을 나타냅니다. 이 터미널을 닫지 마세요. 서비스 샘플이 작동하는지 확인하는 데 필요합니다.

## <a name="run-the-sample-solution"></a>샘플 솔루션 실행

이 빠른 시작에서는 Python에서 샘플 IoT 솔루션을 사용하여 방금 설정한 샘플 디바이스와 상호 작용합니다.

1. **서비스** 터미널로 사용할 또 다른 터미널 창을 엽니다.

1. 복제된 Python SDK 리포지토리의 */azure-iot-sdk-python/azure-iot-hub/samples* 폴더로 이동합니다.

1. *registry_manager_pnp_sample.py* 파일을 열고 코드를 검토합니다. 이 샘플에서는 **IoTHubRegistryManager** 클래스를 사용하여 IoT 플러그 앤 플레이 디바이스와 상호 작용하는 방법을 보여줍니다.

> [!NOTE]
> 이러한 서비스 샘플은 **IoT Hub 서비스 클라이언트** 에서 **IoTHubRegistryManager** 클래스를 사용합니다. 디지털 쌍 API를 비롯한 API에 대한 자세한 내용은 [서비스 개발자 가이드](../articles/iot-develop/concepts-developer-guide-service.md)를 참조하세요.

### <a name="get-the-device-twin"></a>디바이스 쌍 가져오기

[IoT 플러그 앤 플레이 빠른 시작 및 자습서](../articles/iot-develop/set-up-environment.md)에서는 IoT 허브 및 디바이스에 연결하도록 샘플을 구성하는 두 가지 환경 변수를 만들었습니다.

* **IOTHUB_CONNECTION_STRING**: 이전에 기록한 IoT 허브 연결 문자열입니다.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

**서비스** 터미널에서 다음 명령을 사용하여 이 샘플을 실행합니다.

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Linux에서 이 샘플을 실행하는 경우 `set` 대신 `export`를 사용합니다.

출력은 디바이스 쌍을 표시하고 해당 모델 ID를 출력합니다.

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

다음 코드 조각은 *registry_manager_pnp_sample.py* 의 샘플 코드를 보여줍니다.

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>디바이스 쌍 업데이트

이 샘플에서는 디바이스에서 `targetTemperature` 쓰기 가능 속성을 업데이트하는 방법을 보여줍니다.

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

다음과 같은 출력이 표시되는 **디바이스** 터미널에서 업데이트가 적용되었는지 확인할 수 있습니다.

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

**서비스** 터미널에 패치 적용에 성공한 것이 확인됩니다.

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>명령 호출

그런 다음, 이 샘플은 다음 명령을 호출합니다.

**서비스** 터미널에 디바이스의 확인 메시지가 표시됩니다.

```cmd/sh
The device method has been successfully invoked
```

**디바이스** 터미널에, 디바이스가 명령을 수신한 것이 보입니다.

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
