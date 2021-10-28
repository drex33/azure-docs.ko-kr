---
title: 자습서 - 일반 Azure IoT 플러그 앤 플레이 모듈 연결 | Microsoft Docs
description: 자습서 - 일반 모듈에서 샘플 C# IoT 플러그 앤 플레이 디바이스 코드를 사용합니다.
author: RamIoTMalhotra
ms.author: ramal
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: a0a5402f4947da149609f9563c7f28c6581cd14a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259452"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>자습서: IoT 플러그 앤 플레이 모듈 연결(C#)

이 자습서에서는 일반 IoT 플러그 앤 플레이 [모듈](../iot-hub/iot-hub-devguide-module-twins.md)을 연결하는 방법을 보여줍니다.

디바이스가 IoT 허브에 연결할 때 해당 모델 ID를 게시하고 모델 ID로 식별되는 DTDL(Digital Twins Definition Language) 모델에 설명된 속성 및 메서드를 구현하는 경우 IoT 플러그 앤 플레이 디바이스입니다. 디바이스에서 DTDL 및 모델 ID를 사용하는 방법에 대해 자세히 알아보려면 [IoT 플러그 앤 플레이 개발자 가이드](./concepts-developer-guide-device.md)를 참조하세요. 모듈은 모델 ID와 DTDL 모델을 같은 방식으로 사용합니다.

IoT 플러그 앤 플레이 모듈을 구현하는 방법을 보여주기 위해 이 자습서에서는 다음을 수행하는 방법을 보여줍니다.

> [!div class="checklist"]
> * 모듈이 있는 디바이스를 IoT 허브에 추가합니다.
> * 온도 조절기 C# 디바이스 샘플을 제네릭 모듈로 변환합니다.
> * 서비스 SDK를 사용하여 모듈과 상호 작용합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Windows에서 이 자습서를 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/)

Azure IoT 탐색기 도구를 사용하여 **my-module-device** 라는 새로운 디바이스를 IoT 허브에 추가합니다.

**my-module** 이라는 모듈을 **my-module-device** 에 추가합니다.

1. Azure IoT 탐색기 도구에서 **my-module-device** 디바이스로 이동합니다.

1. **모듈 ID** 를 선택한 다음, **+추가** 를 선택합니다.

1. 모듈 ID 이름으로 **my-module** 을 입력하고 **저장** 을 선택합니다.

1. 모듈 ID 목록에서 **my-module** 을 선택합니다. 그런 다음, 기본 연결 문자열을 복사합니다. 이 모듈 연결 문자열은 이 자습서에서 뒷부분에서 사용합니다.

1. **모듈 쌍** 탭을 선택하고 원하는 속성이나 보고된 속성이 없음을 확인합니다.

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>코드 다운로드

아직 수행하지 않은 경우 Azure IoT Hub 디바이스 C# SDK GitHub 리포지토리를 로컬 머신에 복제합니다.

선택한 폴더에서 명령 프롬프트를 엽니다. 다음 명령을 사용하여 [Azure IoT C# 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리를 다음 위치에 복제합니다.

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>프로젝트 준비

샘플 프로젝트를 열고 준비하려면 다음을 수행합니다.

1. Visual Studio 2019에서 *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* 프로젝트를 엽니다.

1. Visual Studio에서 **프로젝트 > 자동 온도 조절기 속성 > 디버그** 로 이동합니다. 그런 다음, 프로젝트에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | 이전에 기록해 둔 모듈 연결 문자열입니다. |

    샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/main/iot-hub/Samples/device/PnpDeviceSamples/readme.md)를 참조하세요.

## <a name="modify-the-code"></a>코드 수정

디바이스가 아닌 모듈로 작동하도록 코드를 수정하려면 다음을 수행합니다.

1. Visual Studio에서 *Parameter.cs* 를 열고 **PrimaryConnectionString** 변수를 설정하는 줄을 다음과 같이 수정합니다.

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. Visual Studio에서 *Program.cs* 를 열고 `DeviceClient` 클래스의 7개 인스턴스를 `ModuleClient` 클래스로 바꿉니다.

    > [!TIP]
    > Visual Studio 검색 및 바꾸기 기능을 사용하여 **대/소문자 일치** 및 **전체 단어 일치** 로 `DeviceClient`를 `ModuleClient`로 바꿉니다.

1. Visual Studio에서 *Thermostat.cs* 를 열고 다음과 같이 `DeviceClient` 클래스의 두 개 인스턴스를 모두 `ModuleClient` 클래스로 바꿉니다.

1. 수정한 파일에 변경 내용을 저장합니다.

코드를 실행한 다음, Azure IoT 탐색기를 사용하여 업데이트된 모듈 쌍을 보는 경우 업데이트된 디바이스 쌍이 모델 ID 및 모듈 reported 속성으로 표시됩니다.

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
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
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>디바이스 모듈과 상호 작용

서비스 SDK를 사용하면 연결된 IoT 플러그 앤 플레이 디바이스 및 모듈의 모델 ID를 검색할 수 있습니다. 서비스 SDK를 사용하여 writable 속성을 설정하고 명령을 호출할 수 있습니다.

1. Visual Studio의 다른 인스턴스에서 *azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* 프로젝트를 엽니다.

1. Visual Studio에서 **프로젝트 > 자동 온도 조절기 속성 > 디버그** 로 이동합니다. 그런 다음, 프로젝트에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | [환경 설정](set-up-environment.md)을 완료했을 때 기록한 값 |

    > [!TIP]
    > Azure IoT 탐색기 도구를 사용하여 IoT 허브 연결 문자열을 찾을 수도 있습니다.

1. *Program.cs* 파일을 열고 다음과 같이 명령을 호출하는 줄을 수정합니다.

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. *Program.cs* 파일에서 다음과 같이 디바이스 쌍을 검색하는 줄을 수정합니다.

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. 모듈 클라이언트 샘플이 아직 실행되고 있는지 확인한 다음, 이 서비스 샘플을 실행합니다. 서비스 샘플의 출력에서는 디바이스 쌍의 모델 ID와 명령 호출을 보여 줍니다.

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    모듈 클라이언트의 출력에는 명령 처리기의 응답이 표시됩니다.

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>IoT Edge 모듈로 변환

이 샘플을 IoT 플러그 앤 플레이 IoT Edge 모듈로 작동하도록 변환하려면 애플리케이션을 컨테이너화해야 합니다. 추가 코드를 변경하지 않아도 됩니다. 연결 문자열 환경 변수는 시작 시 IoT Edge 런타임에서 삽입됩니다. 자세한 내용은 [Visual Studio 2019를 사용하여 Azure IoT Edge용 모듈 개발 및 디버그](../iot-edge/how-to-visual-studio-develop-module.md)를 참조하세요.

컨테이너화된 모듈을 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Ubuntu Virtual Machines에서 Azure IoT Edge 실행](../iot-edge/how-to-install-iot-edge-ubuntuvm.md)
* [Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치](../iot-edge/how-to-provision-single-device-linux-symmetric.md)

Azure IoT 탐색기 도구를 사용하여 다음을 확인할 수 있습니다.

* 모듈 쌍에 있는 IoT Edge 디바이스의 모델 ID.
* IoT Edge 디바이스의 원격 분석.
* IoT 플러그 앤 플레이 알림을 트리거하는 IoT Edge 모듈 쌍 속성 업데이트.
* IoT 플러그 앤 플레이 명령에 대응하는 IoT Edge 모듈.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 모듈을 통해 IoT 플러그 앤 플레이 디바이스를 IoT 허브에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 모델링 개발자 가이드](./concepts-developer-guide-device.md)