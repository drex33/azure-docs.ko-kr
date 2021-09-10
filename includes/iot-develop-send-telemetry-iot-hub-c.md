---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: cf398acc35f309218d123a0c8fc60e7d6f4a3a81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744354"
---
[![코드 찾아보기](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. Azure CLI를 사용하여 Azure IoT Hub 및 디바이스를 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 시뮬레이션된 온도 조절기를 실행하고 허브에 안전하게 연결하고 원격 분석을 전송합니다.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독이 아직 없는 경우 시작하기 전에 [무료 구독을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Git](https://git-scm.com/downloads)
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): Azure IoT 모니터링하고 관리하는 플랫폼 간 유틸리티 
- Azure CLI. 이 빠른 시작에서 Azure CLI 명령을 실행하기 위한 두 가지 옵션이 있습니다.
    - 브라우저에서 CLI 명령을 실행하는 대화형 셸인 Azure Cloud Shell을 사용합니다. 이 옵션은 아무 것도 설치할 필요가 없으므로 권장됩니다. 처음으로 Cloud Shell을 사용하는 경우 [Azure Portal](https://portal.azure.com)에 로그인합니다. [Cloud Shell 빠른 시작](../articles/cloud-shell/quickstart.md)의 단계를 따라 **Cloud Shell을 시작하고** **Bash 환경을 선택합니다**.
    - 선택적으로 로컬 컴퓨터에서 Azure CLI를 실행합니다. Azure CLI가 이미 설치된 경우 `az upgrade`를 실행하여 CLI 및 확장을 현재 버전으로 업그레이드합니다. Azure CLI를 설치하는 방법은 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

운영 체제의 나머지 필수 구성 요소를 설치합니다.

### <a name="linux"></a>Linux
이 자습서의 단계는 Ubuntu Linux 18.04를 사용하여 테스트했습니다.

Linux에서 이 빠른 시작을 완료하려면 로컬 Linux 환경에 다음 소프트웨어를 설치해야 합니다.

`apt-get` 명령을 사용하여 **GCC**, **Git**, **cmake** 및 필요한 종속성을 설치합니다.

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` 버전이 **2.8.12** 보다 크고, **GCC** 버전이 **4.4.7** 보다 큰지 확인합니다.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows
Windows 에서 이 빠른 시작을 완료하려면 Visual Studio 2019를 설치하고 C 및 C++ 개발에 필요한 구성 요소를 추가합니다.

1. 새로운 사용자의 경우 [Visual Studio(Community, Professional 또는 Enterprise) 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다. 설치하려는 버전을 다운로드하고 설치 프로그램을 시작합니다.
    > [!NOTE]
    > 기존 Visual Studio 2019 사용자의 경우 Windows **시작** 을 선택하고 *Visual Studio 설치 프로그램* 을 입력하고 설치 프로그램을 시작합니다.
1. 설치 프로그램 **워크로드** 탭에서 **C++로 데스크탑 개발** 워크로드를 선택합니다.
1. 설치를 실행합니다.

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 C SDK를 사용하여 시뮬레이션된 디바이스에서 IoT 허브로 메시지를 보냅니다. 두 개의 온도 조절기 센서가 있는 온도 컨트롤러를 구현하는 샘플을 실행합니다.

### <a name="build-the-sample"></a>샘플 빌드
1. 콘솔을 열어 Azure IoT C 디바이스 SDK를 설치하고 코드 샘플을 실행합니다. Windows의 경우 **시작** 메뉴에서 *Developer Command Prompt for VS 2019* 를 선택하여 콘솔을 엽니다. Linux의 경우 Bash를 엽니다.

    > [!NOTE]
    > Azure CLI의 로컬 설치를 사용하는 경우 이제 두 개의 콘솔 창이 열려 있을 수 있습니다. CLI에 사용한 콘솔이 아니라 방금 연 콘솔에서 이 섹션의 명령을 입력해야 합니다.

1. C 콘솔에서 Azure IoT C 디바이스 SDK를 로컬 컴퓨터에 복제합니다.
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```
1. SDK의 루트 폴더로 이동하고 다음 명령을 실행하여 종속성을 업데이트합니다.
    ```console
    cd azure-iot-sdk-c
    git submodule update --init
    ```
    이 작업은 몇 분 정도 걸립니다.
1. SDK 및 샘플을 빌드하려면 다음 명령을 실행합니다.

    ```console
    cmake -Bcmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF
    cmake --build cmake
    ```
1. 시뮬레이션된 디바이스에서 Azure IoT에 연결할 수 있도록 다음 환경 변수를 설정합니다.
    * `IOTHUB_DEVICE_CONNECTION_STRING`이라는 환경 변수를 설정합니다. 변수 값의 경우 이전 섹션에서 저장한 디바이스 연결 문자열을 사용합니다.
    * `IOTHUB_DEVICE_SECURITY_TYPE`이라는 환경 변수를 설정합니다. 변수의 경우 리터럴 문자열 값 `connectionString`을 사용합니다.

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Windows CMD의 경우 각 변수의 문자열 값을 묶는 따옴표가 없습니다.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

### <a name="run-the-code"></a>코드 실행
1. 콘솔에 적합한 명령을 사용하여 샘플 코드를 실행합니다.

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/Debug/pnp_temperature_controller
    ```
    > [!NOTE]
    > 이 코드 샘플에서는 수동 구성 없이 솔루션에 스마트 디바이스를 통합할 수 있도록 하는 Azure IoT 플러그 앤 플레이를 사용합니다.  기본적으로 이 설명서에 있는 대부분의 샘플은 IoT 플러그 앤 플레이를 사용합니다. IoT PnP의 장점과 사용 여부에 대한 자세한 내용은 [IoT 플러그 앤 플레이란?](../articles/iot-develop/overview-iot-plug-and-play.md)을 참조하세요.

샘플은 등록한 디바이스로 IoT Hub에 안전하게 연결하고 원격 분석 메시지를 보내기 시작합니다. 샘플 출력이 콘솔에 나타납니다. 

## <a name="view-telemetry"></a>원격 분석 보기

IoT Explorer를 사용하여 디바이스 원격 분석을 볼 수 있습니다. 선택적으로 Azure CLI를 사용하여 원격 분석을 볼 수 있습니다.

Azure IoT Explorer에서 원격 분석을 보려면

1. IoT Explorer의 IoT 허브에서 **이 허브의 디바이스 보기** 를 선택한 다음 목록에서 디바이스를 선택합니다. 
1. 디바이스의 왼쪽 메뉴에서 **원격 분석** 을 선택합니다.
1. **기본 제공 이벤트 허브 사용** 이 *예* 로 설정되어 있는지 확인한 다음 **시작** 을 선택합니다.
1. 디바이스에서 메시지를 클라우드에 보낼 때 원격 분석을 봅니다.

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-c/iot-explorer-device-telemetry.png" alt-text="IoT Explorer의 디바이스 원격 분석 스크린샷":::

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
      component: ''
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: mydevice
      payload: '{"workingSet":1251}'
    
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: mydevice
      payload: '{"temperature":22.00}'
    ```

