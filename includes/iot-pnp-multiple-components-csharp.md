---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 22655f5112323c51613efb5c22b3aa1b703039c6
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516576"
---
이 자습서에서는 구성 요소를 사용하여 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 허브로 전송된 정보를 확인하는 방법을 보여 줍니다. 샘플 애플리케이션은 C#으로 작성되었으며 C#용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 샘플 코드를 다운로드합니다.
> * 샘플 코드를 빌드합니다.
> * 샘플 디바이스 애플리케이션을 실행하고 IoT 허브에 연결되는지 확인합니다.
> * 소스 코드를 검토합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows에서 이 자습서를 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/)

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>샘플 코드를 사용하여 SDK 리포지토리 복제

[자습서: Windows에서 실행되는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 IoT Hub(C#)에 연결](../articles/iot-develop/tutorial-connect-device.md)을 완료한 경우 이미 리포지토리를 복제한 것입니다.

C#용 Azure IoT 샘플 GitHub 리포지토리에서 샘플을 복제합니다. 선택한 폴더에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [.NET용 Microsoft Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리를 복제합니다.

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

이 빠른 시작에서는 C#으로 작성된 샘플 온도 컨트롤러 디바이스를 IoT 플러그 앤 플레이 디바이스로 사용합니다. 샘플 디바이스를 실행하려면 다음을 수행합니다.

1. Visual Studio 2019에서 *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* 프로젝트 파일을 엽니다.

1. Visual Studio에서 **프로젝트 > TemperatureController 속성 > 디버그** 로 이동합니다. 그런 다음, 프로젝트에 다음 환경 변수를 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | [환경 설정](../articles/iot-develop/set-up-environment.md)을 완료했을 때 기록한 값 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | [환경 설정](../articles/iot-develop/set-up-environment.md)을 완료했을 때 기록한 값 |


1. 이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

1. 디바이스에서 일부 정보를 보냈으며 현재 온라인 상태임을 보고했다는 메시지가 표시됩니다. 이 메시지는 디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었음을 나타냅니다. 이 Visual Studio의 인스턴스를 닫지 마세요. 서비스 샘플이 작동하는지 확인하는 데 필요합니다.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>코드 검토

이 샘플은 IoT 플러그 앤 플레이 온도 컨트롤러 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 [여러 구성 요소](../articles/iot-develop/concepts-modeling-guide.md)를 사용합니다. [온도 디바이스용 DTDL(Digital Twins 정의 언어) 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

디바이스 코드는 표준 `CreateFromConnectionString` 메서드를 사용하여 IoT 허브에 연결합니다. 디바이스는 연결 요청에서 구현하는 DTDL 모델의 모델 ID를 보냅니다. 모델 ID를 보내는 디바이스는 IoT 플러그 앤 플레이 디바이스입니다.

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

모델 ID는 다음 코드 조각과 같이 코드에 저장됩니다.

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

디바이스가 IoT 허브에 연결되면 코드에 명령 처리기를 등록합니다. `reboot` 명령은 기본 구성 요소에 정의되어 있습니다. `getMaxMinReport` 명령은 두 개의 온도 조절기 구성 요소 각각에 정의되어 있습니다.

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

두 개의 온도 조절기 구성 요소에 원하는 속성 업데이트에 대한 별도의 처리기가 있습니다.

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

샘플 코드는 각 자동 온도 조절기 구성 요소에서 원격 분석을 보냅니다.

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync` 메서드는 `PnpHhelper` 클래스를 사용하여 각 구성 요소에 대한 메시지를 만듭니다.

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper` 클래스에는 여러 구성 요소 모델에서 사용할 수 있는 다른 예제 메서드가 포함되어 있습니다.

Azure IoT 탐색기 도구를 사용하여 두 가지 자동 온도 조절기 구성 요소의 원격 분석 및 속성을 볼 수 있습니다.

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Azure IoT 탐색기의 여러 구성 요소 디바이스":::

Azure IoT 탐색기 도구를 사용하여 두 가지 자동 온도 조절기 구성 요소 중 하나 또는 기본 구성 요소에서 명령을 호출할 수도 있습니다.
