---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 398959508c7c52d484bb30ae2712d679ac4dccbc
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516682"
---
이 빠른 시작에서는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 전송되는 원격 분석을 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 C#으로 작성되었으며 C#용 Azure IoT 샘플에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows에서 이 빠른 시작을 완료하려면 개발 머신에 다음과 같은 소프트웨어가 설치되어 있어야 합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/)
* [Git](https://git-scm.com/download/)

## <a name="download-the-code"></a>코드 다운로드

이 빠른 시작에서는 C#용 Azure IoT 샘플 리포지토리를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 폴더에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [C#용 Microsoft Azure IoT 샘플(.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리를 다음 위치에 복제합니다.

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>코드 빌드

이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

1. Visual Studio 2019에서 *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* 프로젝트 파일을 엽니다.

1. Visual Studio에서 **프로젝트 > 자동 온도 조절기 속성 > 디버그** 로 이동합니다. 그런 다음, 프로젝트에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [환경 설정](../articles/iot-develop/set-up-environment.md)을 완료했을 때 기록한 값 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [환경 설정](../articles/iot-develop/set-up-environment.md)을 완료했을 때 기록한 값 |

이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

Windows의 Visual Studio에서 코드 실행을 추적하려면 program.cs 파일의 `main` 함수에 중단점을 추가합니다.

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>코드 검토

이 샘플은 간단한 IoT 플러그 앤 플레이 온도 조절 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 IoT 플러그 앤 플레이 [구성 요소](../articles/iot-develop/concepts-modeling-guide.md)를 사용하지 않습니다. [자동 온도 조절기 디바이스용 DTDL(Digital Twins 정의 언어) 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

디바이스 코드는 표준 `CreateFromConnectionString` 메서드를 사용하여 IoT 허브에 연결합니다. 디바이스는 연결 요청에서 구현하는 DTDL 모델의 모델 ID를 보냅니다. 모델 ID를 보내는 디바이스는 IoT 플러그 앤 플레이 디바이스입니다.

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

모델 ID는 다음 코드 조각과 같이 코드에 저장됩니다.

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

속성을 업데이트하고, 명령을 처리하고, 원격 분석을 보내는 코드는 IoT 플러그 앤 플레이 규칙을 사용하지 않는 디바이스의 코드와 동일합니다.

이 샘플에서는 JSON 라이브러리를 사용하여 IoT 허브에서 전송된 페이로드의 JSON 개체를 구문 분석합니다.

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
