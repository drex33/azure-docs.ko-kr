---
title: Renesas RX65N-2MB를 Azure IoT Central에 연결 빠른 시작
description: Azure RTOS 포함된 소프트웨어를 사용하여 Renesas RX65N-2MB 디바이스를 Azure IoT에 연결하고 원격 분석을 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/04/2021
ms.openlocfilehash: bc87e79df3a581e667926ab61cb3c3b40c908f7b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259509"
---
# <a name="quickstart-connect-a-renesas-starter-kit-for-rx65n-2mb-to-iot-central"></a>빠른 시작: Renesas Starter Kit+ for RX65N-2MB를 IoT Central에 연결

**적용 대상**: [임베디드 디바이스 개발](about-iot-develop.md#embedded-device-development)<br>
**총 완료 시간**: 30분

[![코드 찾아보기](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Renesas/RSK_RX65N_2MB)

이 빠른 시작에서는 Azure RTOS를 사용하여 Renesas Starter Kit+ for RX65N-2MB(이하 Renesas RX65N)를 Azure IoT에 연결합니다.

다음 작업을 완료합니다.

* C에서 Renesas RX65N을 프로그래밍하기 위한 포함된 개발 도구 세트 설치
* 이미지를 빌드하고 Renesas RX65N으로 플래시
* Azure IoT Central을 사용하여 클라우드 구성 요소 만들기, 속성 보기, 디바이스 원격 분석 보기 및 직접 명령 호출

## <a name="prerequisites"></a>사전 요구 사항

* Windows 10을 실행하는 PC
* 리포지토리를 복제하기 위한 [Git](https://git-scm.com/downloads)
* 하드웨어

    * [Renesas Starter Kit+ for RX65N-2MB](https://www.renesas.com/products/microcontrollers-microprocessors/rx-32-bit-performance-efficiency-mcus/rx65n-2mb-starter-kit-plus-renesas-starter-kit-rx65n-2mb)(Renesas RX65N)
    * The [Renesas E2 Emulator Lite](https://www.renesas.com/software-tool/e2-emulator-lite-rte0t0002lkce00000r)
    * USB 2.0 A 수~미니 USB 수 케이블 2개
    * 포함된 5V 전원 공급 장치
    * 이더넷 케이블
    * 유선 이더넷 액세스

## <a name="prepare-the-development-environment"></a>개발 환경 준비

개발 환경을 설정하려면 먼저 빠른 시작에 필요한 모든 자산이 포함되어 있는 GitHub 리포지토리를 복제합니다. 그런 다음, 프로그래밍 도구 세트를 설치합니다.

### <a name="clone-the-repo-for-the-quickstart"></a>빠른 시작에 사용할 리포지토리를 복제합니다.

모든 샘플 디바이스 코드, 설치 스크립트 및 오프라인 버전 설명서를 다운로드하려면 다음 리포지토리를 복제합니다. 이전에 다른 빠른 시작에서 이 리포지토리를 복제한 경우 다시 복제할 필요가 없습니다.

리포지토리를 복제하려면 다음 명령을 실행합니다.

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>도구 설치

복제된 리포지토리에는 필요한 도구를 설치하고 구성하는 설치 스크립트가 포함되어 있습니다. 다른 포함된 디바이스 빠른 시작에서 이 도구를 설치한 경우 다시 설치할 필요가 없습니다.

> [!NOTE]
> 설치 스크립트에서 설치하는 도구는 다음과 같습니다.
> * [CMake](https://cmake.org): 빌드
> * [RX GCC](http://gcc-renesas.com/downloads/get.php?f=rx/8.3.0.202004-gnurx/gcc-8.3.0.202004-GNURX-ELF.exe): 컴파일
> * [Termite](https://www.compuphase.com/software_termite.htm): 연결된 디바이스의 직렬 포트 출력 모니터링

도구를 설치하려면

1. 파일 탐색기에서 리포지토리의 다음 경로로 이동하여 *get-toolchain.bat* 이라는 설치 스크립트를 실행합니다.

    *getting-started\tools\get-toolchain.bat*

1. Windows 경로에 RX 컴파일러를 추가합니다.

    *%USERPROFILE%\AppData\Roaming\GCC for Renesas RX 8.3.0.202004-GNURX-ELF\rx-elf\rx-elf\bin*

1. 설치 후 새 콘솔 창을 열어 설치 스크립트에서 변경한 구성을 인식합니다. 이 콘솔을 사용하여 빠른 시작의 나머지 프로그래밍 작업을 완료합니다. Windows용 Windows CMD, PowerShell 또는 Git Bash를 사용할 수 있습니다.
1. 다음 명령을 실행하여 CMake 버전 3.14 이상이 설치되고 RX 컴파일러 경로가 올바르게 설정되었는지 확인합니다.

    ```shell
    cmake --version
    rx-elf-gcc
    ```
나머지 도구를 설치하려면:

* [Renesas Flash Programmer](https://www.renesas.com/software-tool/renesas-flash-programmer-programming-gui)를 설치합니다. Renesas Flash Programmer에는 Renesas E2 Lite를 통해 Renesas RX65N을 플래시하는 데 필요한 드라이버와 도구가 포함되어 있습니다.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>디바이스 준비

Renesas RX65N을 Azure에 연결하려면 Wi-Fi 및 Azure IoT 설정의 구성 파일을 수정하고, 이미지를 다시 빌드하고, 해당 이미지를 디바이스로 플래시합니다.

### <a name="add-configuration"></a>구성 추가

1. 텍스트 편집기에서 다음 파일을 엽니다.

    *getting-started\Renesas\RSK_RX65N_2MB\app\azure_config.h*

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*사용자의 ID 범위 값*}|
    |`IOT_DPS_REGISTRATION_ID` |{*사용자의 디바이스 ID 값*}|
    |`IOT_DEVICE_SAS_KEY` |{*사용자의 기본 키 값*}|

1. 파일을 저장하고 닫습니다.

### <a name="build-the-image"></a>이미지 빌드

1. 콘솔 또는 파일 탐색기에서 다음 경로에 있는 *rebuild.bat* 스크립트를 실행하여 이미지를 빌드합니다.

    *getting-started\Renesas\RSK_RX65N_2MB\tools\rebuild.bat*

2. 빌드가 완료되면 이진 파일이 다음 경로에 만들어졌는지 확인합니다.

    *getting-started\Renesas\RSK_RX65N_2MB\build\app\rx65n_azure_iot.hex*

### <a name="connect-the-device"></a>디바이스 연결

> [!NOTE]
> Renesas RX65N을 설정 및 시작하는 방법에 관한 자세한 내용은 [Renesas Starter Kit+ for RX65N-2MB 빠른 시작](https://www.renesas.com/document/man/e2studio-renesas-starter-kit-rx65n-2mb-quick-start-guide)을 참조하세요.

1. 다음 이미지를 참조로 사용하여 다음 단계를 완료합니다.
    
    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/renesas-rx65n.jpg" alt-text="Renesas RX65N 보드에서 초기화, 전원, 이더넷, USB 및 E1/E2Lite 찾기":::

1. 5V 전원 공급 장치를 사용하여 Renesas RX65N의 **전원 입력** 을 콘센트에 연결합니다.

1. 이더넷 케이블을 사용하여 Renesas RX65N의  **이더넷** 을 라우터에 연결합니다.

1. 첫 번째 미니 USB 케이블을 사용하여 Renesas RX65N의 **USB 직렬** 을 컴퓨터에 연결합니다.

1. 두 번째 미니 USB 케이블을 사용하여 Renesas E2 Lite의 **E2 Lite USB 직렬** 을 컴퓨터에 연결합니다.

1. 제공된 리본 케이블을 사용하여 Renesas RX65N의 **E1/E2Lite** 를 Renesas E2 Lite에 연결합니다.

### <a name="flash-the-image"></a>이미지 플래시

1. 시작 메뉴에서 *Renesas Flash Programmer* 애플리케이션을 시작합니다.

2. ‘파일’ 메뉴에서 ‘새 프로젝트...’를 선택하고 다음 설정을 입력합니다. 
    * **마이크로 컨트롤러**: RX65x
    * **프로젝트 이름**: RX65N
    * **도구**: E2 Emulator Lite

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/rfp-new.png" alt-text="Renesas Flash Programmer, 새 프로젝트의 스크린샷":::

3. ‘도구 세부 정보’ 단추를 선택하고 ‘설정 다시 설정’ 탭으로 이동합니다. 

4. ‘PIN을 Hi-Z로 다시 설정’ 단추를 선택하고 ‘설정 다시 설정’ 탭으로 이동합니다. 

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/rfp-reset.png" alt-text="Renesas Flash Programmer, 설정 다시 설정의 스크린샷":::

5. ‘연결’ 단추를 누르고, 메시지가 표시되면 ‘자동 인증’ 확인란을 선택한 다음, ‘확인’을 누릅니다.  

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/rfp-auth.png" alt-text="Renesas Flash Programmer, 인증의 스크린샷":::

6. ‘찾아보기...’ 단추를 선택하고 이전 섹션에서 만든 *rx65n_azure_iot.hex* 파일을 찾습니다.

7. ‘시작’을 누르면 플래시를 시작합니다. 이 프로세스는 10초 정도 걸립니다.

### <a name="confirm-device-connection-details"></a>디바이스 연결 세부 정보 확인

**Termite** 앱을 사용하여 통신을 모니터링하고 디바이스가 올바르게 설정되었는지 확인할 수 있습니다.
> [!TIP]
> 플래시한 후 디바이스를 초기화하거나 연결하는 데 문제가 있는 경우 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.

1. **Termite** 를 시작합니다.
1. **설정** 을 선택합니다.
1. **직렬 포트 설정** 대화 상자에서 다음 설정을 확인하고, 필요한 경우 업데이트합니다.
    * **전송 속도**: 115,200
    * **포트**: Renesas RX65N이 연결된 포트입니다. 드롭다운에 여러 포트 옵션이 있는 경우 사용할 올바른 포트를 찾을 수 있습니다. Windows **디바이스 관리자** 를 열고, **포트** 를 확인하여 사용할 포트를 식별합니다.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/termite-settings.png" alt-text="Termite 앱의 직렬 포트 설정 스크린샷":::

1. 확인을 선택합니다.
1. 디바이스의 **다시 설정** 단추를 누릅니다.
1. **Termite** 앱에서 다음 검사점 값을 확인하여 디바이스가 초기화되고 Azure IoT에 연결되었는지 확인합니다.

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 10.0.0.81
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 104.194.242.237
        SNTP time update: May 28, 2021 22:53:27.54 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsg;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

다음 단계에서 디바이스 출력을 모니터링하려면 Termite를 열어 둡니다.

## <a name="verify-the-device-status"></a>디바이스 상태 확인

IoT Central 포털에서 디바이스 상태를 보려면
1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. **디바이스 상태** 가 **프로비저닝됨** 으로 업데이트되었는지 확인합니다.
1. **디바이스 템플릿** 이 **시작 가이드** 로 업데이트되었는지 확인합니다.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/iot-central-device-view-status.png" alt-text="IoT Central의 디바이스 상태 스크린샷":::

## <a name="view-telemetry"></a>원격 분석 보기

IoT Central을 사용하면 디바이스에서 클라우드로의 원격 분석 흐름을 볼 수 있습니다.

IoT Central 포털에서 원격 분석을 보려면

1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. 디바이스 목록에서 디바이스를 선택합니다.
1. **개요** 탭에서 디바이스에서 메시지를 클라우드에 보낼 때 원격 분석을 봅니다.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/iot-central-device-telemetry.png" alt-text="IoT Central의 디바이스 원격 분석 스크린샷":::

    > [!NOTE]
    > Termite 앱을 사용하여 디바이스에서 원격 분석을 모니터링할 수도 있습니다.

## <a name="call-a-direct-method-on-the-device"></a>디바이스에서 직접 메서드 호출

IoT Central을 사용하여 디바이스에서 구현한 직접 메서드를 호출할 수도 있습니다. 직접 메서드에는 이름이 있으며, 필요에 따라 JSON 페이로드, 구성 가능한 연결 및 메서드 시간 제한이 있을 수 있습니다. 이 섹션에서는 LED를 설정하거나 해제하는 데 사용할 수 있는 메서드를 호출합니다.

IoT Central 포털에서 메서드를 호출하려면

1. 디바이스 페이지에서 **명령** 탭을 선택합니다.
1. **상태** 드롭다운에서 **True** 를 선택한 다음, **실행** 을 선택합니다.  LED 표시등이 켜집니다.

    :::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/iot-central-invoke-method.png" alt-text="IoT Central의 디바이스에서 직접 메서드를 호출하는 스크린샷":::

1. **상태** 드롭다운에서 **False** 를 선택한 다음, **실행** 을 선택합니다. LED 표시등이 꺼집니다.

## <a name="view-device-information"></a>디바이스 정보 보기

IoT Central에서 디바이스 정보를 볼 수 있습니다.

디바이스 페이지에서 **정보** 탭을 선택합니다.

:::image type="content" source="media/quickstart-devkit-renesas-rx65n-2mb/iot-central-device-about.png" alt-text="IoT Central의 디바이스 정보 스크린샷":::

## <a name="troubleshoot"></a>문제 해결

디바이스 코드 빌드, 디바이스 플래시 또는 연결에 문제가 발생하는 경우 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 IoT Central 포털에서 해당 리소스를 삭제할 수 있습니다.

전체 Azure IoT Central 샘플 애플리케이션과 모든 해당 디바이스 및 리소스를 제거하려면 다음을 수행합니다.
1. **관리** > **애플리케이션** 을 차례로 선택합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure RTOS 샘플 코드가 포함된 사용자 지정 이미지를 빌드한 다음, 해당 이미지를 Renesas RX65N 디바이스로 플래시했습니다. 또한 IoT Central 포털을 사용하여 Azure 리소스를 만들고, Renesas RX65N을 Azure에 안전하게 연결하고, 원격 분석을 보고, 메시지를 보냈습니다.

다음 단계로, 다음 문서에서 IoT 디바이스 SDK를 사용하여 디바이스를 Azure IoT에 연결하는 방법을 자세히 살펴봅니다. 

> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS는 기본 MCU/MPU 하드웨어 보호 메커니즘을 사용하여 통신을 보호하고 코드 및 데이터 격리를 만드는 구성 요소를 OEM에 제공합니다. 그러나 각 OEM은 궁극적으로 디바이스에서 진화하는 보안 요구 사항을 충족하는지 확인해야 합니다.

