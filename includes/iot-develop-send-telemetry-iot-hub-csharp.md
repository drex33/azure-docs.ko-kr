---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: a1c4367e735f02a82c599002c294343145f9cda5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744369"
---
[![코드 찾아보기](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. Azure CLI를 사용하여 Azure IoT Hub 및 디바이스를 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 시뮬레이션된 온도 조절기를 실행하고 허브에 안전하게 연결하고 원격 분석을 전송합니다.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio(Community, Professional 또는 Enterprise) 2019](https://visualstudio.microsoft.com/downloads/).
- [C#(.NET) GitHub 리포지토리에 대한 Microsoft Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp)의 로컬 복사본입니다. [ZIP 다운로드](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)를 통해 리포지토리의 복사본을 다운로드하고 추출합니다.
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): Azure IoT 모니터링하고 관리하는 플랫폼 간 유틸리티 
- Azure CLI. 이 빠른 시작에서 Azure CLI 명령을 실행하기 위한 두 가지 옵션이 있습니다.
    - 브라우저에서 CLI 명령을 실행하는 대화형 셸인 Azure Cloud Shell을 사용합니다. 이 옵션은 아무 것도 설치할 필요가 없으므로 권장됩니다. 처음으로 Cloud Shell을 사용하는 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다. [Cloud Shell 빠른 시작](../articles/cloud-shell/quickstart.md)의 단계를 따라 **Cloud Shell을 시작하고** **Bash 환경을 선택합니다**.
    - 선택적으로 로컬 컴퓨터에서 Azure CLI를 실행합니다. Azure CLI가 이미 설치된 경우 `az upgrade`를 실행하여 CLI 및 확장을 현재 버전으로 업그레이드합니다. Azure CLI를 설치하는 방법은 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 C# SDK를 사용하여 시뮬레이션된 디바이스에서 IoT 허브로 메시지를 보냅니다. 두 개의 온도 조절기 센서가 있는 온도 컨트롤러를 구현하는 샘플을 실행합니다.

Visual Studio에서 샘플 애플리케이션을 실행하려면 다음을 수행합니다.

1. C#에 대한 Azure IoT 샘플의 압축을 해제한 폴더에 Visual Studio의 *azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln* 솔루션 파일을 엽니다. 

1. **솔루션 탐색기** 에서 **PnpDeviceSamples > TemperatureController** 프로젝트 파일을 선택하고 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정** 을 선택합니다.

1. **TemperatureController** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하고 **디버그** 탭을 선택한 뒤 다음 환경 변수를 프로젝트에 추가합니다.

    | Name | 값 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | 이전에 저장한 연결 문자열. |

1. 업데이트된 **TemperatureController** 프로젝트 파일을 저장합니다.

1. Visual Studio에서 Ctrl + F5를 눌러 샘플을 실행합니다.

콘솔 창이 열립니다. 샘플은 등록한 디바이스로 IoT Hub에 안전하게 연결하고 원격 분석 메시지를 보내기 시작합니다. 샘플 출력이 콘솔에 나타납니다.

## <a name="view-telemetry"></a>원격 분석 보기

IoT Explorer를 사용하여 디바이스 원격 분석을 볼 수 있습니다. 선택적으로 Azure CLI를 사용하여 원격 분석을 볼 수 있습니다.

Azure IoT Explorer에서 원격 분석을 보려면

1. IoT Explorer의 IoT 허브에서 **이 허브의 디바이스 보기** 를 선택한 다음 목록에서 디바이스를 선택합니다. 
1. 디바이스의 왼쪽 메뉴에서 **원격 분석** 을 선택합니다.
1. **기본 제공 이벤트 허브 사용** 이 *예* 로 설정되어 있는지 확인한 다음 **시작** 을 선택합니다.
1. 디바이스에서 메시지를 클라우드에 보낼 때 원격 분석을 봅니다.

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-csharp/iot-explorer-device-telemetry.png" alt-text="IoT Explorer의 디바이스 원격 분석 스크린샷":::

1. **중지** 를 선택하여 수신 이벤트를 종료합니다.

개별 디바이스 구성 요소에서 보낸 원격 분석을 읽으려면 IoT Explorer의 플러그 앤 플레이 기능을 사용할 수 있습니다. 예를 들어 이 빠른 시작의 온도 컨트롤러에는 thermostat1과 thermostat2의 두 온도 조절기가 있습니다. thermostat1에서 보고한 온도를 보려면 다음 단계를 따르세요. 

1. IoT Explorer의 디바이스 왼쪽 메뉴에서 **IoT 플러그 앤 플레이 구성 요소** 를 선택합니다. 그런 다음 구성 요소 목록에서 **thermostat1** 을 선택합니다.

1. **thermostat1** 구성 요소 창의 상단 메뉴에서 **원격 분석** 을 선택합니다.

1. **원격 분석** 창에서 이전과 동일한 단계를 따릅니다. **기본 제공 이벤트 허브 사용** 이 *예* 로 설정되어 있는지 확인한 다음 **시작** 을 선택합니다.

Azure CLI를 사용하여 디바이스 원격 분석을 보려면 다음을 수행합니다.

1. CLI 앱에서 [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) 명령을 실행하여 시뮬레이션된 디바이스에서 IoT Hub로 전송된 이벤트를 모니터링합니다. 이전에 디바이스 및 IoT Hub에 대해 Azure IoT에서 만든 이름을 사용합니다.

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. 콘솔에서 연결 세부 정보 및 원격 분석 출력을 봅니다.

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 36.7
    ```

1. Ctrl+C를 선택하여 모니터링을 종료합니다.
