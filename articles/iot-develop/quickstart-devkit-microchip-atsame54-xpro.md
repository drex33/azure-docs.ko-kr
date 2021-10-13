---
title: Microchip ATSAME54-XPro를 Azure IoT Central에 연결 빠른 시작
description: Azure RTOS 포함된 소프트웨어를 사용하여 Microchip ATSAME54-XPro 디바이스를 Azure IoT에 연결하고 원격 분석을 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 09/22/2021
zone_pivot_groups: iot-develop-toolset
ms.openlocfilehash: e9ac53521c89764f73bc234a66de7e12ceb1a7ae
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858675"
---
# <a name="quickstart-connect-a-microchip-atsame54-xpro-evaluation-kit-to-iot-central"></a>빠른 시작: Microchip ATSAME54-XPro 평가 키트를 IoT Central에 연결

**적용 대상**: [임베디드 디바이스 개발](about-iot-develop.md#embedded-device-development)<br>
**총 완료 시간**: 45분

[![코드 찾아보기](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO)

빠른 시작에서는 Azure RTOS를 사용하여 Microchip ATSAME54-XPro(이하 Microchip E54)를 Azure IoT에 연결합니다.

다음 작업을 완료합니다.

* C에서 Microchip E54를 프로그래밍하기 위한 포함된 개발 도구 세트 설치
* 이미지를 빌드하고 Microchip E54로 플래시
* Azure IoT Central을 사용하여 클라우드 구성 요소 만들기, 속성 보기, 디바이스 원격 분석 보기 및 직접 명령 호출

## <a name="prerequisites"></a>사전 요구 사항

* Microsoft Windows 10을 실행하는 PC
* 리포지토리를 복제하기 위한 [Git](https://git-scm.com/downloads)
* 하드웨어

    * [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)(Microchip E54)
    * USB 2.0 A~마이크로 USB 수 케이블
    * 유선 이더넷 액세스
    * 이더넷 케이블
    * 선택 사항: [Weather Click](https://www.mikroe.com/weather-click) 센서. 이 센서를 디바이스에 추가하여 날씨 조건을 모니터링할 수 있습니다. 이 센서가 없는 경우에도 빠른 시작을 완료할 수 있습니다.
    * 선택 사항: [mikroBUS Xplained Pro](https://www.microchip.com/Developmenttools/ProductDetails/ATMBUSADAPTER-XPRO) 어댑터. 이 어댑터를 사용하여 Weather Click 센서를 Microchip E54에 연결합니다. 센서와 이 어댑터가 없는 경우에도 빠른 시작을 완료할 수 있습니다.

:::zone pivot="iot-toolset-iar-ewarm"

* IAR의 EWARM. [IAR의 EWARM 30일 평가판](https://www.iar.com/products/architectures/arm/iar-embedded-workbench-for-arm/)을 다운로드할 수 있습니다.

:::zone-end
:::zone pivot="iot-toolset-mplab"

* [MPLAB X IDE 5.35](https://www.microchip.com/mplab/mplab-x-ide)
* [MPLAB XC32/32++ 컴파일러 2.4.0 이상](https://www.microchip.com/mplab/compilers)

:::zone-end

## <a name="prepare-the-development-environment"></a>개발 환경 준비

개발 환경을 설정하려면 먼저 빠른 시작에 필요한 모든 자산이 포함되어 있는 GitHub 리포지토리를 복제합니다. 그런 다음, 프로그래밍 도구 세트를 설치합니다.

### <a name="clone-the-repo-for-the-quickstart"></a>빠른 시작에 사용할 리포지토리를 복제합니다.

모든 샘플 디바이스 코드, 설치 스크립트 및 오프라인 버전 설명서를 다운로드하려면 다음 리포지토리를 복제합니다. 이전에 다른 빠른 시작에서 이 리포지토리를 복제한 경우 다시 복제할 필요가 없습니다.

리포지토리를 복제하려면 다음 명령을 실행합니다.

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>도구 설치

:::zone pivot="iot-toolset-cmake"

복제된 리포지토리에는 필요한 도구를 설치하고 구성하는 설치 스크립트가 포함되어 있습니다. 다른 포함된 디바이스 빠른 시작에서 이 도구를 설치한 경우 다시 설치할 필요가 없습니다.

> [!NOTE]
> 설치 스크립트에서 설치하는 도구는 다음과 같습니다.
>
> * [CMake](https://cmake.org): 빌드
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): 컴파일
> * [Termite](https://www.compuphase.com/software_termite.htm): 연결된 디바이스의 직렬 포트 출력 모니터링

도구를 설치하려면

1. 파일 탐색기에서 리포지토리의 다음 경로로 이동하여 ***get-toolchain.bat*** 이라는 설치 스크립트를 실행합니다.

    *getting-started\tools\get-toolchain.bat*

1. 설치 후 새 콘솔 창을 열어 설치 스크립트에서 변경한 구성을 인식합니다. 이 콘솔을 사용하여 빠른 시작의 나머지 프로그래밍 작업을 완료합니다. Windows용 Windows CMD, PowerShell 또는 Git Bash를 사용할 수 있습니다.
1. 다음 코드를 실행하여 CMake 버전 3.14 이상이 설치되어 있는지 확인합니다.

    ```shell
    cmake --version
    ```

1. [AVR&reg; 및 SAM 디바이스용 Microchip Studio](https://www.microchip.com/en-us/development-tools-tools-and-software/microchip-studio-for-avr-and-sam-devices#)를 설치합니다. Microchip Studio는 Microchip E54를 프로그래밍하고 플래시하는 도구를 포함하는 디바이스 개발 환경입니다. 이 자습서에서는 Microchip E54를 플래시하는 데 Microchip Studio만 사용합니다. 설치는 몇 분 정도 걸리며 구성 요소 설치를 승인하라는 메시지가 여러 번 표시됩니다.

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"

도구 세트 제조업체에서 제공하는 설치 지침을 사용하여 개발 도구 세트를 설치하고 준비합니다.

:::zone-end

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>디바이스 준비

Microchip E54를 Azure에 연결하려면 Azure IoT 설정의 구성 파일을 수정하고, 이미지를 다시 빌드하고, 해당 이미지를 디바이스로 플래시합니다.

### <a name="add-configuration"></a>구성 추가

:::zone pivot="iot-toolset-cmake"

1. 텍스트 편집기에서 다음 파일을 엽니다.

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    | `IOT_DPS_ID_SCOPE` | {*사용자의 ID 범위 값*} |
    | `IOT_DPS_REGISTRATION_ID` | {*사용자의 디바이스 ID 값*} |
    | `IOT_DEVICE_SAS_KEY` | {*사용자의 기본 키 값*} |

1. 파일을 저장하고 닫습니다.

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"

1. 압축을 푼 zip 파일에서 IAR의 ***azure_rtos.eww*** EWARM 작업 영역을 엽니다.

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    | `IOT_DPS_ID_SCOPE` | {*사용자의 ID 범위 값*} |
    | `IOT_DPS_REGISTRATION_ID` | {*사용자의 디바이스 ID 값*} |
    | `IOT_DEVICE_SAS_KEY` | {*사용자의 기본 키 값*} |

1. 파일을 저장하고 닫습니다.

:::zone-end
:::zone pivot="iot-toolset-mplab"

1. MPLab을 열고, **파일 > 프로젝트 열기** 를 선택하고, 압축을 푼 zip 파일에서 모든 프로젝트를 선택합니다.

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    | `IOT_DPS_ID_SCOPE` | {*사용자의 ID 범위 값*} |
    | `IOT_DPS_REGISTRATION_ID` | {*사용자의 디바이스 ID 값*} |
    | `IOT_DEVICE_SAS_KEY` | {*사용자의 기본 키 값*} |

1. 파일을 저장하고 닫습니다.

:::zone-end

### <a name="connect-the-device"></a>디바이스 연결

1. Microchip E54에서 **다시 설정** 단추, **이더넷** 포트 및 마이크로 USB 포트(**디버그 USB** 레이블이 지정됨)를 찾습니다. 다음 그림에서는 각 구성 요소가 강조 표시되어 있습니다.

    ![Microchip E54 평가 키트 보드에서 주요 구성 요소 찾기](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. 마이크로 USB 케이블을 Microchip E54의 **디버그 USB** 포트에 연결한 다음, 컴퓨터에 연결합니다.

    > [!NOTE]
    > 필요에 따라 Microchip E54를 설정하고 시작하는 방법에 관한 자세한 내용은 [SAM E54 Xplained Pro 사용자 가이드](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf)를 참조하세요.

1. 이더넷 케이블을 사용하여 Microchip E54를 이더넷 포트에 연결합니다.

### <a name="optional-install-a-weather-sensor"></a>선택 사항: 날씨 센서 설치

Weather Click 센서 및 mikroBUS Xplained Pro 어댑터가 있는 경우 이 섹션의 단계를 따릅니다. 그렇지 않으면 [이미지 빌드](#build-the-image)로 건너뜁니다. 센서가 없는 경우에도 빠른 시작을 완료할 수 있습니다. 디바이스의 샘플 코드는 실제 센서가 없는 경우 시뮬레이션된 데이터를 반환합니다.

1. Weather Click 센서 및 mikroBUS Xplained Pro 어댑터가 있는 경우 다음 사진에서 보는 바와 같이 Microchip E54에 설치합니다.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/sam-e54-sensor.png" alt-text="Microchip ES4에 Weather Click 센서 및 mikroBUS Xplained Pro 어댑터 설치":::

1. 이전에 편집한 구성 파일을 다시 엽니다.

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. 헤더 파일에서 다음 코드에 표시된 대로 상수 `__SENSOR_BME280__` 값을 **1** 로 설정합니다. 이 값을 설정하면 디바이스가 Weather Click 센서의 실제 센서 데이터를 사용할 수 있습니다.

    `#define __SENSOR_BME280__ 1`

1. 파일을 저장하고 닫습니다.

### <a name="build-the-image"></a>이미지 빌드

:::zone pivot="iot-toolset-cmake"

1. 콘솔 또는 파일 탐색기에서 다음 경로에 있는 ***rebuild.bat*** 스크립트를 실행하여 이미지를 빌드합니다.

    *getting-started\Microchip\ATSAME54-XPRO\tools\rebuild.bat*

1. 빌드가 완료되면 이진 파일이 다음 경로에 만들어졌는지 확인합니다.

    *getting-started\Microchip\ATSAME54-XPRO\build\app\atsame54_azure_iot.bin*

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"

IAR에서 **Project(프로젝트) > Batch Build(빌드 일괄 처리)** _를 선택하고, _ *build_all**을 선택하고, **Make(만들기)** 를 선택하여 모든 프로젝트를 빌드합니다. 모든 샘플 프로젝트의 컴파일 및 연결을 관찰합니다.

:::zone-end
:::zone pivot="iot-toolset-mplab"

_ *Projects(프로젝트)* * 창에서 프로젝트를 선택하여 샘플 프로젝트의 모든 종속 라이브러리(***azure_iot, threadx, netxduo, same54_lib** _)가 빌드되었는지 확인하고, 마우스 오른쪽 단추로 클릭하고, **Build(빌드)** 를 선택합니다.

:::zone-end

### <a name="flash-the-image"></a>이미지 플래시

:::zone pivot="iot-toolset-cmake"

1. **Windows 시작 > Microchip Studio 명령 프롬프트** 콘솔을 열고 빌드한 Microchip E54 이진 파일의 폴더로 이동합니다.

    *getting-started\Microchip\ATSAME54-XPRO\build\app*

1. *atprogram* 유틸리티를 통해 이진 이미지를 사용하여 Microchip E54를 플래시합니다.

    ```shell
    atprogram --tool edbg --interface SWD --device ATSAME54P20A program --chiperase --file atsame54_azure_iot.bin --verify
    ```

    > [!NOTE]
    > Microchip E54에서 Atmel-ICE 및 atprogram 도구를 사용하는 방법에 관한 자세한 내용은 [대량 프로덕션에서 AVR 프로그래밍에 Atmel-ICE 사용](http://ww1.microchip.com/downloads/en/AppNotes/00002466A.pdf)을 참조하세요.

    플래시 프로세스가 완료된 후 콘솔은 프로그래밍이 성공했는지 확인합니다.

    ```output
    Firmware check OK
    Programming and verification completed successfully.
    ```

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"

IAR의 도구 모음에서 녹색 **Download and Debug(다운로드 및 디버그)** 단추를 눌러 프로그램을 다운로드하고 실행합니다. 그런 다음, **Go(이동)** 를 누릅니다.

:::zone-end
:::zone pivot="iot-toolset-mplab"

MPLAB에서 **Debug > Debug Main Project(디버그 > 주 프로젝트 디버그)** 를 선택합니다.

:::zone-end

### <a name="confirm-device-connection-details"></a>디바이스 연결 세부 정보 확인

**Termite** 앱을 사용하여 통신을 모니터링하고 디바이스가 올바르게 설정되었는지 확인할 수 있습니다.

1. **Termite** 를 시작합니다.

    > [!TIP]
    > 플래시한 후 디바이스를 초기화하거나 연결하는 데 문제가 있는 경우 [문제 해결](roubleshoot-embedded-device-quickstarts.md)에서 추가 단계를 참조하세요.

1. **설정** 을 선택합니다.

1. **직렬 포트 설정** 대화 상자에서 다음 설정을 확인하고, 필요한 경우 업데이트합니다.
    * **전송 속도**: 115,200
    * **포트**: Microchip E54가 연결된 포트입니다. 드롭다운에 여러 포트 옵션이 있는 경우 사용할 올바른 포트를 찾을 수 있습니다. Windows **디바이스 관리자** 를 열고, **포트** 를 확인하여 사용할 포트를 식별합니다.
    * **흐름 제어**: DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Termite 앱의 직렬 포트 설정 스크린샷":::

1. 확인을 선택합니다.

1. 디바이스의 **다시 설정** 단추를 누릅니다. 이 단추는 디바이스에 대한 레이블을 표시하고 마이크로 USB 커넥터 근처에 있습니다.

1. **Termite** 앱에서 다음 검사점 값을 확인하여 디바이스가 초기화되고 Azure IoT에 연결되었는지 확인합니다.

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 192.168.0.21
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 45.55.58.103
        SNTP time update: Jun 5, 2021 20:2:46.32 UTC 
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
1. **디바이스 상태** 가 *프로비저닝됨* 으로 업데이트되었는지 확인합니다.
1. **디바이스 템플릿** 이 *시작 가이드* 로 업데이트되었는지 확인합니다.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status.png" alt-text="IoT Central의 디바이스 상태 스크린샷":::

## <a name="view-telemetry"></a>원격 분석 보기

IoT Central을 사용하면 디바이스에서 클라우드로의 원격 분석 흐름을 볼 수 있습니다.

IoT Central 포털에서 원격 분석을 보려면

1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. 디바이스 목록에서 디바이스를 선택합니다.
1. **개요** 탭에서 디바이스에서 메시지를 클라우드에 보낼 때 원격 분석을 봅니다.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry.png" alt-text="IoT Central의 디바이스 원격 분석 스크린샷":::

    > [!NOTE]
    > Termite 앱을 사용하여 디바이스에서 원격 분석을 모니터링할 수도 있습니다.

## <a name="call-a-direct-method-on-the-device"></a>디바이스에서 직접 메서드 호출

IoT Central을 사용하여 디바이스에서 구현한 직접 메서드를 호출할 수도 있습니다. 직접 메서드에는 이름이 있으며, 필요에 따라 JSON 페이로드, 구성 가능한 연결 및 메서드 시간 제한이 있을 수 있습니다. 이 섹션에서는 LED를 설정하거나 해제하는 데 사용할 수 있는 메서드를 호출합니다.

IoT Central 포털에서 메서드를 호출하려면

1. 디바이스 페이지에서 **명령** 탭을 선택합니다.
1. **상태** 드롭다운에서 **True** 를 선택한 다음, **실행** 을 선택합니다.  LED 표시등이 켜집니다.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method.png" alt-text="IoT Central의 디바이스에서 직접 메서드를 호출하는 스크린샷":::

1. **상태** 드롭다운에서 **False** 를 선택한 다음, **실행** 을 선택합니다. LED 표시등이 꺼집니다.

## <a name="view-device-information"></a>디바이스 정보 보기

IoT Central에서 디바이스 정보를 볼 수 있습니다.

디바이스 페이지에서 **정보** 탭을 선택합니다.

:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about.png" alt-text="IoT Central의 디바이스 정보 스크린샷":::

## <a name="troubleshoot-and-debug"></a>문제 해결 및 디버그

디바이스 코드 빌드, 디바이스 플래시 또는 연결에 문제가 발생하는 경우 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.

애플리케이션 디버깅에 대한 자세한 내용은 [Visual Studio Code를 사용하여 디버깅](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 IoT Central 포털에서 해당 리소스를 삭제할 수 있습니다.

전체 Azure IoT Central 샘플 애플리케이션과 모든 해당 디바이스 및 리소스를 제거하려면 다음을 수행합니다.

1. **관리** > **애플리케이션** 을 차례로 선택합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure RTOS 샘플 코드가 포함된 사용자 지정 이미지를 빌드한 다음, 해당 이미지를 Microchip E54 디바이스로 플래시했습니다. 또한 IoT Central 포털을 사용하여 Azure 리소스를 만들고, Microchip E54를 Azure에 안전하게 연결하고, 원격 분석을 보고, 메시지를 보냈습니다.

다음 단계로, 다음 문서에서 IoT 디바이스 SDK를 사용하여 디바이스를 Azure IoT에 연결하는 방법을 자세히 살펴봅니다. 

> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [IoT Hub에 시뮬레이션된 디바이스 연결](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS는 기본 MCU/MPU 하드웨어 보호 메커니즘을 사용하여 통신을 보호하고 코드 및 데이터 격리를 만드는 구성 요소를 OEM에 제공합니다. 그러나 각 OEM은 궁극적으로 디바이스에서 진화하는 보안 요구 사항을 충족하는지 확인해야 합니다.
