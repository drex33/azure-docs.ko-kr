---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: b4ff1f14ac628f28e67f4b619983760d06f60d8b
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516540"
---
[![코드 찾아보기](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. 먼저 디바이스 호스트를 위한 Azure IoT Central 애플리케이션을 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 시뮬레이션된 온도 조절기를 실행하고 IoT Central에 안전하게 연결하고 원격 분석을 전송합니다.

## <a name="prerequisites"></a>사전 요구 사항
- [Visual Studio(Community, Professional 또는 Enterprise) 2019](https://visualstudio.microsoft.com/downloads/).
- [C#(.NET) GitHub 리포지토리에 대한 Microsoft Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp)의 로컬 복사본입니다. [ZIP 다운로드](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)를 통해 리포지토리의 복사본을 다운로드하고 추출합니다.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 로컬 환경을 구성하고 시뮬레이션된 온도 컨트롤러를 만드는 샘플을 실행합니다.

Visual Studio에서 샘플 애플리케이션을 실행하려면 다음을 수행합니다.

1. C#에 대한 Azure IoT 샘플의 압축을 해제한 폴더에 Visual Studio의 *azure-iot-samples-csharp-main\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* 솔루션 파일을 엽니다. 

1. **솔루션 탐색기** 에서 **PnpDeviceSamples > TemperatureController** 프로젝트 파일을 선택하고 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정** 을 선택합니다.

1. **TemperatureController** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하고 **디버그** 탭을 선택한 뒤 다음 환경 변수를 프로젝트에 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 이전에 기록해 둔 ID 범위 값입니다. |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 이전에 기록해 둔 생성된 디바이스 키 값입니다. |

1. 업데이트된 **TemperatureController** 프로젝트 파일을 저장합니다.

1. Ctrl+F5를 눌러 샘플을 실행합니다.

    시뮬레이션된 디바이스가IoT Central 애플리케이션에 연결되면 원격 분석을 보내기 시작합니다. 연결 세부 정보 및 원격 분석 출력이 콘솔에 나타납니다. 
    
    ```output
        [05/04/2021 11:53:50]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Press Control+C to quit the sample.
        [05/04/2021 11:53:50]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set up the device client.
        [05/04/2021 11:53:50]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Initializing via DPS
        [05/04/2021 11:53:56]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler for 'reboot' command.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Connection status change registered - status=Connected, reason=Connection_Ok.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler for "getMaxMinReport" command.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Set handler to receive 'targetTemperature' updates.
        [05/04/2021 11:53:57]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - component = 'deviceInformation', properties update is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - { "serialNumber": "SR-123456" } is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Telemetry: Sent - component="thermostat1", { "temperature": 44.9 } in °C.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 44.9 } in °C is complete.
        [05/04/2021 11:53:58]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
              Telemetry: Sent - component="thermostat2", { "temperature": 40.8 } in °C.
    ```