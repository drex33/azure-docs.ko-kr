---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: f8257e95b3f8e65e63b84fa56f0042592bf19231
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132876307"
---
IoT 플러그 앤 플레이를 사용하면 기본 디바이스 구현에 대한 지식이 없어도 디바이스 기능과 상호 작용할 수 있으므로 IoT가 간소화됩니다. 이 빠른 시작에서는 Node.js를 사용하여 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스에 연결하고 제어하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

이 빠른 시작을 완료하려면 개발 머신에 Node.js가 필요합니다. [nodejs.org](https://nodejs.org)에서 여러 플랫폼에 권장하는 최신 버전을 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>샘플 코드를 사용하여 SDK 리포지토리 복제

[노드 SDK 리포지토리](https://github.com/Azure/azure-iot-sdk-node)에서 샘플을 복제합니다. 원하는 폴더에서 터미널 창을 엽니다. 다음 명령을 실행하여 [Node.js용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 리포지토리를 복제합니다.

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure/azure-iot-sdk-node/tree/main/device/samples#iot-plug-and-play-device-samples)를 참조하세요.

이 빠른 시작에서는 Node.js로 작성된 샘플 자동 온도 조절 디바이스를 IoT 플러그 앤 플레이 디바이스로 사용합니다. 샘플 디바이스를 실행하려면 다음을 수행합니다.

1. 터미널 창을 열고 GitHub에서 복제한 Node.js용 Microsoft Azure IoT SDK 리포지토리가 포함된 로컬 폴더로 이동합니다.

1. 이 터미널 창은 이제 **디바이스** 터미널로 사용됩니다. 복제된 리포지토리의 폴더로 이동하여 */azure-iot-sdk-node/device/samples/pnp* 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 다음 명령을 사용하여 샘플 자동 온도 조절 디바이스를 실행합니다.

    ```cmd/sh
    node pnp_simple_thermostat.js
    ```

1. 디바이스에서 일부 정보를 보냈으며 현재 온라인 상태임을 보고했다는 메시지가 표시됩니다. 이 메시지는 디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었음을 나타냅니다. 이 터미널을 닫지 마세요. 서비스 샘플이 작동하는지 확인하는 데 필요합니다.

## <a name="run-the-sample-solution"></a>샘플 솔루션 실행

[IoT 플러그 앤 플레이 빠른 시작 및 자습서](../articles/iot-develop/set-up-environment.md)에서는 IoT 허브 및 디바이스에 연결하도록 샘플을 구성하는 두 가지 환경 변수를 만들었습니다.

* **IOTHUB_CONNECTION_STRING**: 이전에 기록한 IoT 허브 연결 문자열입니다.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

이 빠른 시작에서는 Node.js에서 샘플 IoT 솔루션을 사용하여 방금 설정한 샘플 디바이스와 상호 작용합니다.

1. **서비스** 터미널로 사용할 또 다른 터미널 창을 엽니다.

1. 복제된 노드 SDK 리포지토리에서 */azure-iot-sdk-node/service/samples/javascript* 폴더로 이동합니다. 다음 명령을 실행하여 모든 종속 요소를 설치합니다.

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>속성 읽기

1. **디바이스** 터미널에서 샘플 자동 온도 조절 디바이스를 실행하면 온라인 상태를 나타내는 다음 메시지가 표시됩니다.

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. **서비스** 터미널로 이동한 후 다음 명령을 사용하여 디바이스 정보를 읽는 샘플을 실행합니다.

    ```cmd/sh
    node twin.js
    ```

1. **서비스** 터미널 출력에서 디바이스 쌍의 응답을 확인합니다. 보고된 디바이스의 모델 ID 및 관련 속성이 다음과 같이 표시됩니다.

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. 다음 코드 조각은 디바이스 쌍의 모델 ID를 검색하는 *twin.js* 의 코드를 보여줍니다.

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

이 시나리오에서는 `Model Id: dtmi:com:example:Thermostat;1`을 출력합니다.

> [!NOTE]
> 이러한 서비스 샘플은 **IoT Hub 서비스 클라이언트** 에서 **Registry** 클래스를 사용합니다. 디지털 쌍 API를 비롯한 API에 대한 자세한 내용은 [서비스 개발자 가이드](../articles/iot-develop/concepts-developer-guide-service.md)를 참조하세요.

### <a name="update-a-writable-property"></a>쓰기 가능 속성 업데이트

1. 코드 편집기에서 *twin.js* 파일을 엽니다.

1. 샘플 코드를 검토합니다. 디바이스 쌍을 업데이트하는 두 가지 방법을 보여줍니다. 첫 번째 방법을 사용하려면 `twinPatch` 변수를 다음과 같이 수정합니다.

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature` 속성은 자동 온도 조절기 디바이스 모델에서 쓰기 가능한 속성으로 정의됩니다.

1. **서비스** 터미널에서 다음 명령을 사용하여 속성을 업데이트하는 샘플을 실행합니다.

    ```cmd/sh
    node twin.js
    ```

1. **디바이스** 터미널로 이동하면 디바이스가 업데이트를 수신한 것을 볼 수 있습니다.

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. **서비스** 터미널에서 다음 명령을 실행하여 속성이 업데이트되었는지 확인합니다.

    ```cmd/sh
    node twin.js
    ```

1. **서비스** 터미널 출력의 `reported` 속성 섹션에서 업데이트된 대상 온도가 보고된 것을 확인할 수 있습니다. 디바이스가 업데이트를 완료하는 데 시간이 걸릴 수 있습니다. 디바이스가 속성 업데이트를 처리할 때까지 이 단계를 반복합니다.

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>명령 호출

1. *device_method.js* 파일을 열고 코드를 검토합니다.

1. **서비스** 터미널로 이동합니다. 다음 명령을 사용하여 명령을 호출하는 샘플을 실행합니다.

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. **서비스** 터미널의 출력에 다음과 같은 확인이 표시됩니다.

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. **디바이스** 터미널에서 명령이 승인된 것을 볼 수 있습니다.

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```
