---
title: STMicroelectronics B-L475E-IOT01A 또는 B-L4S5I-IOT01A를 Azure IoT Central에 연결 빠른 시작
description: Azure RTOS 임베디드 소프트웨어를 사용하여 STMicroelectronics B-L475E-IOT01A 또는 B-L4S5I-IOT01A 디바이스를 Azure IoT에 연결하고 원격 분석을 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: 30e64f70f713540574545200dc188342b63e2f01
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242164"
---
# <a name="quickstart-connect-an-stmicroelectronics-b-l475e-iot01a-or-b-l4s5i-iot01a-discovery-kit-to-iot-central"></a>빠른 시작: STMicroelectronics B-L475E-IOT01A 또는 B-L4S5I-IOT01A 검색 키트를 IoT Central에 연결

**적용 대상**: [임베디드 디바이스 개발](about-iot-develop.md#embedded-device-development)<br>
**총 완료 시간**: 30분

[![코드 찾아보기](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+)

이 빠른 시작에서는 Azure RTOS를 사용하여 STMicroelectronics [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) 또는 [B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) 검색 키트(STM DevKit)를 Azure IoT에 연결합니다.

다음 작업을 완료합니다.

* C에서 STM DevKit를 프로그래밍하기 위한 임베디드 개발 도구 세트 설치
* 이미지를 빌드하고 STM DevKit로 플래시
* Azure IoT Central을 사용하여 클라우드 구성 요소 만들기, 속성 보기, 디바이스 원격 분석 보기 및 직접 명령 호출

## <a name="prerequisites"></a>사전 요구 사항

* Windows 10을 실행하는 PC
* 리포지토리를 복제하기 위한 [Git](https://git-scm.com/downloads)
* 하드웨어

    * [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) 또는 [B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html)(STM DevKit)
    * Wi-Fi 2.4GHz
    * USB 2.0 A~마이크로 USB 수 케이블

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
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): 컴파일
> * [Termite](https://www.compuphase.com/software_termite.htm): 연결된 디바이스의 직렬 포트 출력 모니터링

도구를 설치하려면

1. 파일 탐색기에서 리포지토리의 다음 경로로 이동하여 *get-toolchain.bat* 이라는 설치 스크립트를 실행합니다.

    *getting-started\tools\get-toolchain.bat*

1. 설치 후 새 콘솔 창을 열어 설치 스크립트에서 변경한 구성을 인식합니다. 이 콘솔을 사용하여 빠른 시작의 나머지 프로그래밍 작업을 완료합니다. Windows용 Windows CMD, PowerShell 또는 Git Bash를 사용할 수 있습니다.
1. 다음 코드를 실행하여 CMake 버전 3.14 이상이 설치되어 있는지 확인합니다.

    ```shell
    cmake --version
    ```
[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>디바이스 준비

STM DevKit를 Azure에 연결하려면 Wi-Fi 및 Azure IoT 설정에 대한 구성 파일을 수정하고, 이미지를 다시 빌드하고, 해당 이미지를 디바이스로 플래시합니다.

### <a name="add-configuration"></a>구성 추가

1. 텍스트 편집기에서 다음 파일을 엽니다.

    *getting-started\STMicroelectronics\STM32L4_L4+\app\azure_config.h*

1. Wi-Fi 상수를 로컬 환경의 다음 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    |`WIFI_SSID` |{*사용자의 Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*사용자의 Wi-Fi 암호*}|
    |`WIFI_MODE` |{*파일에 열거된 Wi-Fi 모드 값 중 하나*}|

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*사용자의 ID 범위 값*}|
    |`IOT_DPS_REGISTRATION_ID` |{*사용자의 디바이스 ID 값*}|
    |`IOT_DEVICE_SAS_KEY` |{*사용자의 기본 키 값*}|

1. 파일을 저장하고 닫습니다.

### <a name="build-the-image"></a>이미지 빌드

1. 콘솔 또는 파일 탐색기에서 다음 경로에 있는 *rebuild.bat* 스크립트를 실행하여 이미지를 빌드합니다.

    *getting-started\STMicroelectronics\STM32L4_L4+\tools\rebuild.bat*

2. 빌드가 완료된 후 두 이진 파일이 생성되었는지 확인합니다. 각 STM Devkit에 대한 이진 이미지가 있습니다. 빌드가 수행되면 다음 경로에 이미지가 저장됩니다.

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l475_azure_iot.bin*

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l4S5_azure_iot.bin*

### <a name="flash-the-image"></a>이미지 플래시

1. STM DevKit MCU에서 **다시 설정** 단추, **USB STLink** 라는 레이블이 지정된 마이크로 USB 포트, 보드 부품 번호를 찾습니다. 이러한 항목은 다음 단계에서 참조합니다. 다음 그림에는 이러한 구성 요소가 모두 강조 표시되어 있습니다.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/stm-devkit-board.png" alt-text="STM DevKit 보드에서 주요 구성 요소 찾기":::

1. 마이크로 USB 케이블을 STM DevKit의 **USB STLINK** 포트에 연결한 다음, 컴퓨터에 연결합니다.

    > [!NOTE]
    > STM DevKit에 대한 자세한 설치 정보는 패키지에 대한 지침이나 [B-L475E-IOT01A 리소스](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html#resource) 또는 [B-L4S5I-IOT01A 리소스](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html#resource)를 참조하세요.

1. 파일 탐색기에서 이전 섹션에서 만든 이진 파일을 찾습니다.

1. 파일 이름이 사용 중인 STM Devkit의 파트 번호와 일치하는 이진 파일을 복사합니다. 예를 들어, 보드 부품 번호가 **B-L475E-IOT01A1** 인 경우 *stm32l475_azure_iot.bin* 이라는 이진 파일을 복사합니다.

1. 파일 탐색기에서 컴퓨터에 연결된 STM Devkit를 찾습니다. 디바이스는 드라이브 레이블이 **DIS_L4IOT** 인 드라이브로 시스템에 표시됩니다.

1. 이진 파일을 STM Devkit의 루트 폴더에 붙여넣습니다. 깜박임이 자동으로 시작되고 몇 초 후에 완료됩니다.

    > [!NOTE]
    > 깜박이는 동안 LED가 STM DevKit에서 빨간색과 녹색 사이를 전환합니다.

### <a name="confirm-device-connection-details"></a>디바이스 연결 세부 정보 확인

**Termite** 앱을 사용하여 통신을 모니터링하고 디바이스가 올바르게 설정되었는지 확인할 수 있습니다.

1. **Termite** 를 시작합니다.
    > [!TIP]
    > Termite를 DevKit에 연결할 수 없는 경우 [ST-LINK 드라이버](https://www.st.com/en/development-tools/stsw-link009.html)를 설치하고 다시 시도합니다. 추가 단계는 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.
1. **설정** 을 선택합니다.
1. **직렬 포트 설정** 대화 상자에서 다음 설정을 확인하고, 필요한 경우 업데이트합니다.
    * **전송 속도**: 115,200
    * **포트**: STM DevKit가 연결된 포트입니다. 드롭다운에 여러 포트 옵션이 있는 경우 사용할 올바른 포트를 찾을 수 있습니다. Windows **디바이스 관리자** 를 열고, **포트** 를 확인하여 사용할 포트를 식별합니다.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/termite-settings.png" alt-text="Termite 앱의 직렬 포트 설정 스크린샷":::

1. 확인을 선택합니다.
1. 디바이스의 **다시 설정** 단추를 누릅니다. 이 단추는 디바이스에서 검은색이며 레이블이 지정되어 있습니다.
1. **Termite** 앱에서 다음 검사점 값을 확인하여 디바이스가 초기화되고 Azure IoT에 연결되었는지 확인합니다.

    ```output
    Starting Azure thread

    Initializing WiFi
        Module: ISM43362-M3G-L44-SPI
        MAC address: C4:7F:51:8F:67:F6
        Firmware revision: C3.5.2.5.STM
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.22
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 108.62.122.57
        SNTP time update: May 21, 2021 22:42:8.394 UTC 
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
    > [!IMPORTANT]
    > DNS 클라이언트 초기화가 실패하여 Wi-Fi 펌웨어가 최신 버전이 아니라는 사실을 나타낼 경우 Wi-Fi 모듈 펌웨어를 업데이트해야 합니다. [Inventek ISM 43362 Wi-Fi 모듈 펌웨어 업데이트](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip)를 다운로드하여 설치합니다. 그런 다음, 디바이스에서 **다시 설정** 단추를 눌러 연결을 다시 확인하고 이 빠른 시작을 계속 진행합니다.


다음 단계에서 디바이스 출력을 모니터링하려면 Termite를 열어 둡니다.

## <a name="verify-the-device-status"></a>디바이스 상태 확인

IoT Central 포털에서 디바이스 상태를 보려면
1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. **디바이스 상태** 가 **프로비저닝됨** 으로 업데이트되었는지 확인합니다.
1. **디바이스 템플릿** 이 **시작 가이드** 로 업데이트되었는지 확인합니다.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-view-status.png" alt-text="IoT Central의 디바이스 상태 스크린샷":::

## <a name="view-telemetry"></a>원격 분석 보기

IoT Central을 사용하면 디바이스에서 클라우드로의 원격 분석 흐름을 볼 수 있습니다.

IoT Central 포털에서 원격 분석을 보려면

1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. 디바이스 목록에서 디바이스를 선택합니다.
1. **개요** 탭에서 디바이스에서 메시지를 클라우드에 보낼 때 원격 분석을 봅니다.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-telemetry.png" alt-text="IoT Central의 디바이스 원격 분석 스크린샷":::

    > [!NOTE]
    > Termite 앱을 사용하여 디바이스에서 원격 분석을 모니터링할 수도 있습니다.

## <a name="call-a-direct-method-on-the-device"></a>디바이스에서 직접 메서드 호출

IoT Central을 사용하여 디바이스에서 구현한 직접 메서드를 호출할 수도 있습니다. 직접 메서드에는 이름이 있으며, 필요에 따라 JSON 페이로드, 구성 가능한 연결 및 메서드 시간 제한이 있을 수 있습니다. 이 섹션에서는 LED를 설정하거나 해제하는 데 사용할 수 있는 메서드를 호출합니다.

IoT Central 포털에서 메서드를 호출하려면

1. 디바이스 페이지에서 **명령** 탭을 선택합니다.
1. **상태** 드롭다운에서 **True** 를 선택한 다음, **실행** 을 선택합니다.  LED 표시등이 켜집니다.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-invoke-method.png" alt-text="IoT Central의 디바이스에서 직접 메서드를 호출하는 스크린샷":::

1. **상태** 드롭다운에서 **False** 를 선택한 다음, **실행** 을 선택합니다. LED 표시등이 꺼집니다.

## <a name="view-device-information"></a>디바이스 정보 보기

IoT Central에서 디바이스 정보를 볼 수 있습니다.

디바이스 페이지에서 **정보** 탭을 선택합니다.

:::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-about.png" alt-text="IoT Central의 디바이스 정보 스크린샷":::

## <a name="troubleshoot-and-debug"></a>문제 해결 및 디버그

디바이스 코드 빌드, 디바이스 플래시 또는 연결에 문제가 발생하는 경우 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.

애플리케이션 디버깅에 대한 자세한 내용은 [Visual Studio Code를 사용하여 디버깅](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 IoT Central 포털에서 해당 리소스를 삭제할 수 있습니다.

전체 Azure IoT Central 샘플 애플리케이션과 모든 해당 디바이스 및 리소스를 제거하려면 다음을 수행합니다.
1. **관리** > **애플리케이션** 을 차례로 선택합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure RTOS 샘플 코드가 포함된 사용자 지정 이미지를 빌드한 다음, 해당 이미지를 STM DevKit 디바이스로 플래시했습니다. 또한 IoT Central 포털을 사용하여 Azure 리소스를 만들고, STM DevKit를 Azure에 안전하게 연결하고, 원격 분석을 보고, 메시지를 보냈습니다.

다음 단계로, 다음 문서에서 IoT 디바이스 SDK를 사용하여 디바이스를 Azure IoT 연결하는 방법을 자세히 알아봅니다. 

> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-iot-hub.md)


> [!IMPORTANT]
> Azure RTOS는 기본 MCU/MPU 하드웨어 보호 메커니즘을 사용하여 통신을 보호하고 코드 및 데이터 격리를 만드는 구성 요소를 OEM에 제공합니다. 그러나 각 OEM은 궁극적으로 디바이스에서 진화하는 보안 요구 사항을 충족하는지 확인해야 합니다.



