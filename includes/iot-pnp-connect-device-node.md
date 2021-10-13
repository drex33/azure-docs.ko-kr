---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 087d5c3e43d1b01754f19978d8b76d012e9c55a7
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725677"
---
이 빠른 시작에서는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 전송되는 원격 분석을 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 Node.js로 작성되었으며 Node.js용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[![코드 찾아보기](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript/pnp)

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

이 빠른 시작을 완료하려면 개발 머신에 Node.js가 필요합니다. [nodejs.org](https://nodejs.org)에서 여러 플랫폼에 권장하는 최신 버전을 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

## <a name="download-the-code"></a>코드 다운로드

이 빠른 시작에서는 Node.js용 Azure IoT Hub 디바이스 SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Node.js용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 리포지토리를 다음 위치에 복제합니다.

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>필수 라이브러리 설치

디바이스 SDK를 사용하여 포함된 샘플 코드를 빌드합니다. 빌드하는 애플리케이션은 IoT Hub에 연결하는 디바이스를 시뮬레이션합니다. 이 애플리케이션은 원격 분석 데이터 및 속성을 보내고 명령을 수신합니다.

1. 로컬 터미널 창에서 복제된 리포지토리의 폴더로 이동하고 */azure-iot-sdk-node/device/samples/pnp* 폴더로 이동합니다. 그런 다음, 다음 명령을 실행하여 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    npm install
    ```

1. 이전에 기록한 디바이스 연결 문자열을 사용하여 환경 변수를 구성합니다.

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

이 샘플은 간단한 IoT 플러그 앤 플레이 온도 조절 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 IoT 플러그 앤 플레이 [구성 요소](../articles/iot-develop/concepts-modeling-guide.md)를 사용하지 않습니다. [온도 조절 디바이스용 DTDL 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

_simple_thermostat.js_ 파일을 엽니다. 이 파일에서 다음을 수행하는 방법을 확인할 수 있습니다.

1. 필요한 인터페이스를 가져옵니다.
1. 속성 업데이트 처리기 및 명령 처리기를 작성합니다.
1. 원하는 속성 패치를 처리하고 원격 분석을 보냅니다.
1. 필요에 따라 Azure DPS(Device Provisioning Service)를 사용하여 디바이스를 프로비저닝합니다.

주 함수에서 모두 함께 제공되는 방법을 확인할 수 있습니다.

1. 연결 문자열에서 디바이스를 만들거나 DPS를 사용하여 디바이스를 프로비저닝합니다.)
1. **modelID** 옵션을 사용하여 IoT 플러그 앤 플레이 디바이스 모델을 지정합니다.
1. 명령 처리기를 사용하도록 설정합니다.
1. 디바이스에서 허브로 원격 분석을 보냅니다.
1. 디바이스 쌍을 가져오고 보고된 속성을 업데이트합니다.
1. 원하는 속성 업데이트 처리기를 사용하도록 설정합니다.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/pnp/readme.md)를 참조하세요.

샘플 애플리케이션을 실행하여 IoT 허브에 원격 분석을 전송하는 IoT 플러그 앤 플레이 디바이스를 시뮬레이트합니다. 샘플 애플리케이션을 실행하려면 다음 명령을 사용합니다.

```cmd\sh
node simple_thermostat.js
```

디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었다는 것을 나타내는 다음 출력이 표시됩니다.

![디바이스 확인 메시지](media/iot-pnp-connect-device-node/device-confirmation-node.png)

다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
