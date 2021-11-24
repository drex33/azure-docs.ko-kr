---
title: NXP MIMXRT1060-EVK를 Azure IoT Central에 연결 빠른 시작
description: Azure RTOS 임베디드 소프트웨어를 사용하여 NXP MIMXRT1060-EVK 디바이스를 Azure IoT에 연결하고 원격 분석을 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 11/16/2021
zone_pivot_groups: iot-develop-nxp-toolset
ms.openlocfilehash: b8c51cbe5fd5f4587850ecbd1b6eb4aa5b2c6dd6
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132903652"
---
# <a name="quickstart-connect-an-nxp-mimxrt1060-evk-evaluation-kit-to-iot-central"></a>빠른 시작: NXP MIMXRT1060-EVK 평가 키트를 IoT Central에 연결

**적용 대상**: [임베디드 디바이스 개발](about-iot-develop.md#embedded-device-development)<br>
**총 완료 시간**: 30분

:::zone pivot="iot-toolset-cmake"
[![코드 찾아보기](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK)
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mcuxpresso"
[![코드 찾아보기](media/common/browse-code.svg)](https://github.com/azure-rtos/samples/)
:::zone-end

이 빠른 시작에서는 Azure RTOS를 사용하여 NXP MIMXRT1060-EVK 평가 키트(NXP EVK)를 Azure IoT에 연결합니다.

다음 작업을 완료합니다.

* C에서 NXP EVK를 프로그래밍하기 위한 포함된 개발 도구 세트 설치
* 이미지를 빌드하고 NXP EVK로 플래시
* Azure IoT Central을 사용하여 클라우드 구성 요소 만들기, 속성 보기, 디바이스 원격 분석 보기 및 직접 명령 호출

:::zone pivot="iot-toolset-cmake"
## <a name="prerequisites"></a>사전 요구 사항

* Windows 10을 실행하는 PC
* 리포지토리를 복제하기 위한 [Git](https://git-scm.com/downloads)
* 하드웨어

    * NXP EVK([NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK))
    * USB 2.0 A~마이크로 USB 수 케이블
    * 유선 이더넷 액세스
    * 이더넷 케이블

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

NXP EVK를 Azure에 연결하려면 Azure IoT 설정의 구성 파일을 수정하고, 이미지를 다시 빌드하고, 해당 이미지를 디바이스로 플래시합니다.

### <a name="add-configuration"></a>구성 추가

1. 텍스트 편집기에서 다음 파일을 엽니다.

    *getting-started\NXP\MIMXRT1060-EVK\app\azure_config.h*

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다.

    |상수 이름|값|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*사용자의 ID 범위 값*}|
    |`IOT_DPS_REGISTRATION_ID` |{*사용자의 디바이스 ID 값*}|
    |`IOT_DEVICE_SAS_KEY` |{*사용자의 기본 키 값*}|

1. 파일을 저장하고 닫습니다.

### <a name="build-the-image"></a>이미지 빌드

1. 콘솔 또는 파일 탐색기에서 다음 경로에 있는 *rebuild.bat* 스크립트를 실행하여 이미지를 빌드합니다.

    *getting-started\NXP\MIMXRT1060-EVK\tools\rebuild.bat*

2. 빌드가 완료되면 이진 파일이 다음 경로에 만들어졌는지 확인합니다.

    *getting-started\NXP\MIMXRT1060-EVK\build\app\mimxrt1060_azure_iot.bin*

### <a name="flash-the-image"></a>이미지 플래시

1. NXP EVK에서 **다시 설정** 단추, 마이크로 USB 포트 및 이더넷 포트를 찾습니다.  이러한 구성 요소는 다음 단계에서 사용합니다. 다음 그림에는 세 가지 구성 요소가 모두 강조 표시되어 있습니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/nxp-evk-board.png" alt-text="NXP EVK 보드를 보여주는 사진.":::

1. 마이크로 USB 케이블을 NXP EVK의 마이크로 USB 포트에 연결한 다음, 컴퓨터에 연결합니다. 디바이스가 켜지면 녹색 LED가 전원 상태를 표시합니다.
1. 이더넷 케이블을 사용하여 NXP EVK를 이더넷 포트에 연결합니다.
1. 파일 탐색기에서 이전 섹션에서 만든 이진 파일을 찾습니다.
1. 이진 파일 *mimxrt1060_azure_iot.bin* 복사
1. 파일 탐색기에서 컴퓨터에 연결된 NXP EVK 디바이스를 찾습니다. 디바이스는 드라이브 레이블이 **RT1060-EVK** 인 드라이브로 시스템에 표시됩니다.
1. 이진 파일을 NXP EVK의 루트 폴더에 붙여넣습니다. 깜박임이 자동으로 시작되고 몇 초 후에 완료됩니다.

    > [!NOTE]
    > 플래시 프로세스 중에 NXP EVK에서 빨간색 LED가 빠르게 깜박입니다.

### <a name="confirm-device-connection-details"></a>디바이스 연결 세부 정보 확인

**Termite** 앱을 사용하여 통신을 모니터링하고 디바이스가 올바르게 설정되었는지 확인할 수 있습니다.

1. **Termite** 를 시작합니다.
    > [!TIP]
    > 플래시한 후 디바이스를 초기화하거나 연결하는 데 문제가 있는 경우 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.
1. **설정** 을 선택합니다.
1. **직렬 포트 설정** 대화 상자에서 다음 설정을 확인하고, 필요한 경우 업데이트합니다.
    * **전송 속도**: 115,200
    * **포트**: NXP EVK가 연결된 포트입니다. 드롭다운에 여러 포트 옵션이 있는 경우 사용할 올바른 포트를 찾을 수 있습니다. Windows **디바이스 관리자** 를 열고, **포트** 를 확인하여 사용할 포트를 식별합니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/termite-settings.png" alt-text="Termite 앱의 직렬 포트 설정 스크린샷.":::

1. 확인을 선택합니다.
1. 디바이스의 **다시 설정** 단추를 누릅니다. 이 단추는 디바이스에 대한 레이블을 표시하고 마이크로 USB 커넥터 근처에 있습니다.
1. **Termite** 앱에서 다음 검사점 값을 확인하여 디바이스가 초기화되고 Azure IoT에 연결되었는지 확인합니다.

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 192.168.0.19
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 108.62.122.57
        SNTP time update: May 20, 2021 19:41:20.319 UTC 
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

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"


## <a name="prerequisites"></a>사전 요구 사항

* Windows 10 또는 Windows 11을 실행하는 PC

* 하드웨어

  * NXP EVK([NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK))
  * USB 2.0 A~마이크로 USB 수 케이블
  * 유선 이더넷 액세스
  * 이더넷 케이블

* ARM용 IAR EW(IAR Embedded Workbench). [IAR EW for ARM 14일 평가판](https://www.iar.com/products/architectures/arm/iar-embedded-workbench-for-arm/)을 다운로드하여 설치할 수 있습니다.

* [Azure_RTOS_6.1_MIMXRT1060_IAR_Samples_2021_11_03.zip](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_MIMXRT1060_IAR_Samples_2021_11_03.zip) 파일을 다운로드하여 작업 디렉터리에 압축을 풉니다. 빌드할 때 컴파일러 오류를 방지하려면 경로가 짧은 디렉터리를 선택합니다.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>디바이스 준비

이 섹션에서는 IAR EW IDE를 사용하여 Azure IoT 설정에 대한 구성 파일을 수정하고 샘플 클라이언트 애플리케이션을 빌드한 다음, 디바이스에서 다운로드하여 실행합니다.

### <a name="connect-the-device"></a>디바이스 연결

1. NXP EVK에서 **다시 설정** 단추, 마이크로 USB 포트 및 이더넷 포트를 찾습니다.  이러한 구성 요소는 다음 단계에서 사용합니다. 다음 그림에는 세 가지 구성 요소가 모두 강조 표시되어 있습니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/nxp-evk-board.png" alt-text="NXP EVK 보드의 사진.":::

1. 마이크로 USB 케이블을 NXP EVK의 마이크로 USB 포트에 연결한 다음, 컴퓨터에 연결합니다. 디바이스가 켜지면 녹색 LED가 전원 상태를 표시합니다.
1. 이더넷 케이블을 사용하여 NXP EVK를 이더넷 포트에 연결합니다.

### <a name="configure-build-flash-and-run-the-image"></a>이미지 구성, 빌드, 플래시 및 실행

1. 컴퓨터에서 **IAR EW** 앱을 엽니다.

1. **파일 > 작업 영역 열기** 를 선택하고, zip 파일의 압축을 푼 작업 폴더에서 *mimxrt1060\iar* 폴더로 이동한 다음, ***azure_rtos.eww*** 작업 영역 파일을 엽니다. 

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/open-project-iar.png" alt-text="Open IAR 작업 영역을 보여주는 스크린샷.":::

1. 왼쪽의 **작업 영역** 창에서 마우스 오른쪽 단추로 **sample_azure_iot_embedded_sdk_pnp** 프로젝트를 클릭하고, **활성으로 설정** 을 선택합니다.

1. 프로젝트를 확장한 다음, **샘플** 하위 폴더를 확장하여 *sample_config.h* 파일을 엽니다.

1. 파일 위쪽에 있는 `#define ENABLE_DPS_SAMPLE` 지시문의 주석 처리를 제거합니다.

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다. `ENDPOINT` 상수는 Azure DPS(Device Provisioning Service)에 대한 전역 엔드포인트로 설정됩니다.

    |상수 이름|값|
    |-------------|-----|
    | `ENDPOINT` | "global.azure-devices-provisioning.net" |
    | `ID_SCOPE` | {*사용자의 ID 범위 값*} |
    | `REGISTRATION_ID` | {*사용자의 디바이스 ID 값*} |
    | `DEVICE_SYMMETRIC_KEY` | {*사용자의 기본 키 값*} |

    > [!NOTE]
    > `ENDPOINT`, `ID_SCOPE` 및 `REGISTRATION_ID` 값은 `#ifndef ENABLE_DPS_SAMPLE` 문에 설정됩니다. `ENABLE_DPS_SAMPLE` 값이 정의되면 사용할 값을 `#else` 문에 설정했는지 확인합니다.

1. 파일을 저장합니다.

1. **프로젝트 > 일괄 빌드** 를 차례로 선택합니다. 그런 다음, **build_all** 및 **만들기** 를 선택하여 모든 프로젝트를 빌드합니다. **빌드** 창에 빌드 출력이 표시됩니다. 모든 샘플 프로젝트가 성공적으로 컴파일되고 연결되었는지 확인합니다.

1. 도구 모음에서 녹색 **다운로드 및 디버그** 단추를 선택하여 프로그램을 다운로드합니다.

1. 이미지 다운로드가 완료되면 **이동** 을 선택하여 샘플을 실행합니다.

1. **보기 > 터미널 I/O** 를 선택하여 상태 및 출력 메시지를 인쇄하는 터미널 창을 엽니다.

### <a name="confirm-device-connection-details"></a>디바이스 연결 세부 정보 확인

터미널 창에서 다음과 같은 출력이 표시되어 디바이스가 초기화되고 Azure IoT에 연결되어 있는지 확인합니다.

```output
DHCP In Progress...
IP address: 192.168.1.24
Mask: 255.255.255.0
Gateway: 192.168.1.1
DNS Server address: 192.168.1.1
SNTP Time Sync...0.pool.ntp.org
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-********-****-****-****-************.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Sent properties request.
Telemetry message send: {"temperature":22}.
Received all properties
[INFO] Azure IoT Security Module message is empty
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

다음 단계에서 디바이스 출력을 모니터링하기 위해 터미널을 열어 둡니다.

:::zone-end 
:::zone pivot="iot-toolset-mcuxpresso"

## <a name="prerequisites"></a>사전 요구 사항

* Windows 10 또는 Windows 11을 실행하는 PC

* 하드웨어

  * NXP EVK([NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK))
  * USB 2.0 A~마이크로 USB 수 케이블
  * 유선 이더넷 액세스
  * 이더넷 케이블

* MCUXpresso IDE(MCUXpresso), 버전 11.3.1 이상 [MCUXPresso의 무료 복사본](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)을 다운로드하여 설치합니다.

* [MIMXRT1060-EVK SDK 2.9.0 이상](https://mcuxpresso.nxp.com/en/builder)을 다운로드합니다. 로그인한 후에는 웹 사이트에서 다운로드할 사용자 지정 SDK 아카이브를 빌드할 수 있습니다. EVK MIMXRT1060 보드를 선택하고 SDK를 빌드하는 옵션을 클릭한 후 zip 보관 파일을 다운로드할 수 있습니다.  포함할 유일한 SDK 구성 요소는 미리 선택된 **SDMMC 스택** 입니다.

* [Azure_RTOS_6.1_MIMXRT1060_IAR_Samples_2021_11_03.zip](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_MIMXRT1060_IAR_Samples_2021_11_03.zip) 파일을 다운로드하여 작업 디렉터리에 압축을 풉니다. 빌드할 때 컴파일러 오류를 방지하려면 경로가 짧은 디렉터리를 선택합니다.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-environment"></a>환경 준비

이 섹션에서는 환경을 준비하고 MCUXpresso를 사용하여 디바이스에서 샘플 애플리케이션을 빌드하고 실행합니다.

### <a name="install-the-device-sdk"></a>디바이스 SDK 설치

1. MCUXpresso를 열고 홈 보기에서 **IDE** 를 선택하여 기본 IDE로 전환합니다.

1. **설치된 SDK** 창이 IDE에 표시되는지 확인한 다음, 다운로드 한 MIMXRT1060-EVK SDK zip 보관 파일을 창에 끌어서 놓아 설치합니다. 

    SDK가 설치된 IDE는 다음 스크린샷과 같습니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/mcu-install-sdk.png" alt-text="MCUXpresso에 설치된 MIMXRT 1060 SDK를 보여주는 스크린샷.":::
    
### <a name="import-and-configure-the-sample-project"></a>샘플 프로젝트 가져오기 및 구성

1. IDE의 **빠른 시작 패널** 에서 **파일 시스템에서 프로젝트 가져오기** 를 선택합니다.

1. **프로젝트 가져오기** 대화 상자에서 Azure RTOS 샘플 zip 파일에서 추출한 루트 작업 폴더를 선택한 후, **다음** 을 선택합니다. 

1. **프로젝트를 작업 영역으로 복사** 하는 옵션을 선택 취소합니다.  **프로젝트** 목록의 모든 확인란을 선택된 상태로 둡니다.

1. **마침** 을 선택합니다.   MCUXpresso에서 프로젝트가 열립니다.

1. **Project Explorer** 에서 **sample_azure_iot_embedded_sdk_pnp** 라는 프로젝트를 선택하고 확장한 다음, *sample_config.h* 파일을 엽니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/mcu-load-project.png" alt-text="MCUXpresso에서 로드된 프로젝트를 보여주는 스크린샷.":::

1. 파일 위쪽에 있는 `#define ENABLE_DPS_SAMPLE` 지시문의 주석 처리를 제거합니다.

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Azure IoT 디바이스 정보 상수를 Azure 리소스를 만든 후에 저장한 값으로 설정합니다. `ENDPOINT` 상수는 Azure DPS(Device Provisioning Service)에 대한 전역 엔드포인트로 설정됩니다.

    |상수 이름|값|
    |-------------|-----|
    | `ENDPOINT` | "global.azure-devices-provisioning.net" |
    | `ID_SCOPE` | {*사용자의 ID 범위 값*} |
    | `REGISTRATION_ID` | {*사용자의 디바이스 ID 값*} |
    | `DEVICE_SYMMETRIC_KEY` | {*사용자의 기본 키 값*} |

    > [!NOTE]
    > `ENDPOINT`, `ID_SCOPE` 및 `REGISTRATION_ID` 값은 `#ifndef ENABLE_DPS_SAMPLE` 문에 설정됩니다. `ENABLE_DPS_SAMPLE` 값이 정의되면 사용할 값을 `#else` 문에 설정했는지 확인합니다.

1. 파일을 저장하고 닫습니다.

### <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

1. MCUXpresso에서 **프로젝트 > 프로젝트 빌드** 메뉴 옵션을 선택하거나 **[프로젝트 이름]에 대한 '디버그'** 빌드 도구 모음 단추를 선택하여 프로젝트 **sample_azure_iot_embedded_sdk_pnp** 를 빌드합니다.

1. NXP EVK에서 **다시 설정** 단추, 마이크로 USB 포트 및 이더넷 포트를 찾습니다.  이러한 구성 요소는 다음 단계에서 사용합니다. 다음 그림에는 세 가지 구성 요소가 모두 강조 표시되어 있습니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/nxp-evk-board.png" alt-text="NXP EVK 보드의 구성 요소를 보여주는 사진.":::

1. 마이크로 USB 케이블을 NXP EVK의 마이크로 USB 포트에 연결한 다음, 컴퓨터에 연결합니다. 디바이스가 켜지면 녹색 LED가 전원 상태를 표시합니다.
1. 이더넷 케이블을 사용하여 NXP EVK를 이더넷 포트에 연결합니다.
1. Windows **디바이스 관리자** 를 열고 **포트(COM 및 LPT)** 노드를 확장하고 연결된 디바이스에서 사용 중인 COM 포트를 확인합니다.  다음 단계에서 이 정보를 사용하여 터미널을 구성합니다.

1. MCUXpresso의 도구 모음에서 **터미널 열기** 를 선택하거나 CTRL+ALT+SHIFT+T를 눌러 터미널 창을 구성합니다.

1. **터미널 선택** 드롭다운에서 **직렬 터미널** 을 선택하고 다음 스크린샷에서와 같이 옵션을 구성한 다음, [확인]을 선택합니다. 이 경우 NXP EVK 디바이스는 로컬 컴퓨터의 COM3 포트에 연결됩니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/mcu-configure-terminal.png" alt-text="직렬 터미널 구성의 스크린샷."::: 

    > [!NOTE]
    > 터미널 창은 IDE의 아래쪽에 표시되며, 처음에는 샘플을 다운로드하여 실행할 때까지 가비지 문자를 표시할 수 있습니다.

1. **디버깅 프로젝트 시작 [프로젝트 이름]** 도구 모음 단추를 선택합니다.  그러면 프로젝트가 디바이스에 다운로드되어 실행됩니다.

1. 코드가 IDE에서 중단되면 **다시 시작(F8)** 도구 모음 단추를 선택합니다.

1. IDE의 하단에서 출력을 볼 수 있도록 터미널 창을 선택합니다. NXP EVK의 RESET 단추를 눌러 강제로 다시 연결합니다. 

### <a name="confirm-device-connection-details"></a>디바이스 연결 세부 정보 확인

터미널 창에서 다음과 같은 출력이 표시되어 디바이스가 초기화되고 Azure IoT에 연결되어 있는지 확인합니다.

```output
DHCP In Progress...
IP address: 192.168.1.24
Mask: 255.255.255.0
Gateway: 192.168.1.1
DNS Server address: 192.168.1.1
SNTP Time Sync...0.pool.ntp.org
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-********-****-****-****-************.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Sent properties request.
Telemetry message send: {"temperature":22}.
Received all properties
[INFO] Azure IoT Security Module message is empty
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

다음 단계에서 디바이스 출력을 모니터링하기 위해 터미널을 열어 둡니다.

:::zone-end

## <a name="verify-the-device-status"></a>디바이스 상태 확인

IoT Central 포털에서 디바이스 상태를 보려면
1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. **디바이스 상태** 가 **프로비저닝됨** 으로 업데이트되었는지 확인합니다.
1. **디바이스 템플릿** 값이 명명된 템플릿으로 업데이트되었는지 확인합니다.

    :::zone pivot="iot-toolset-cmake"
    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-view-status.png" alt-text="IoT Central의 디바이스 상태 스크린샷.":::
    :::zone-end
    :::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mcuxpresso" 
    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-view-iar-status.png" alt-text="IoT Central의 NXP 디바이스 상태 스크린샷."::: 
    :::zone-end

## <a name="view-telemetry"></a>원격 분석 보기

IoT Central을 사용하면 디바이스에서 클라우드로의 원격 분석 흐름을 볼 수 있습니다.

IoT Central 포털에서 원격 분석을 보려면

1. 애플리케이션 대시보드의 측면 탐색 메뉴에서 **디바이스** 를 선택합니다.
1. 디바이스 목록에서 디바이스를 선택합니다.
1. **개요** 탭에서 디바이스에서 메시지를 클라우드에 보낼 때 원격 분석을 봅니다.
1. 온도는 MCU 웨이퍼에서 측정됩니다.

    :::zone pivot="iot-toolset-cmake"
    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-telemetry.png" alt-text="IoT Central의 디바이스 원격 분석 스크린샷.":::
    :::zone-end
    :::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mcuxpresso"
    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-telemetry-iar.png" alt-text="IoT Central의 NXP 디바이스 원격 분석 스크린샷.":::
    :::zone-end

## <a name="call-a-direct-method-on-the-device"></a>디바이스에서 직접 메서드 호출

IoT Central을 사용하여 디바이스에서 구현한 직접 메서드를 호출할 수도 있습니다. 직접 메서드에는 이름이 있으며, 필요에 따라 JSON 페이로드, 구성 가능한 연결 및 메서드 시간 제한이 있을 수 있습니다. 

IoT Central 포털에서 메서드를 호출하려면
:::zone pivot="iot-toolset-cmake"

1. 디바이스 페이지에서 **명령** 탭을 선택합니다.
1. **상태** 드롭다운에서 **True** 를 선택한 다음, **실행** 을 선택합니다. 토글할 수 있는 LED가 없기 때문에 디바이스에 변경이 없습니다. 그러나 Termite에서 출력을 보고 메서드의 상태를 모니터링할 수 있습니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-invoke-method.png" alt-text="IoT Central의 디바이스에서 직접 메서드를 호출하는 스크린샷.":::

1. **상태** 드롭다운에서 **False** 를 선택한 다음, **실행** 을 선택합니다.
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mcuxpresso"

1. 디바이스 페이지에서 **명령** 탭을 선택합니다.

1. **다음 이후** 필드에서 날짜 선택기와 시간 선택기를 사용하여 시간을 설정한 다음, **실행** 을 선택합니다.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-invoke-method-iar.png" alt-text="IoT Central의 NXP 디바이스에서 직접 메서드를 호출하는 스크린샷.":::

1. 터미널에서 명령 호출을 볼 수 있습니다. 이 경우 샘플 자동 온도 조절기 애플리케이션이 시뮬레이션된 온도 값을 인쇄하므로 시간 범위 동안 최솟값 또는 최댓값이 없습니다.

    ```output
    Received command: getMaxMinReport
    ```

    > [!NOTE]
    > 명령 호출 및 응답은 IoT Central의 디바이스 페이지에 있는 **원시 데이터** 탭에서도 볼 수 있습니다.

:::zone-end

## <a name="view-device-information"></a>디바이스 정보 보기

IoT Central에서 디바이스 정보를 볼 수 있습니다.

디바이스 페이지에서 **정보** 탭을 선택합니다.

:::zone pivot="iot-toolset-cmake"
:::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-about.png" alt-text="IoT Central의 디바이스 정보 스크린샷.":::
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mcuxpresso"
:::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-about-iar.png" alt-text="IoT Central의 NXP 디바이스 정보 스크린샷.":::
:::zone-end

## <a name="troubleshoot-and-debug"></a>문제 해결 및 디버그

디바이스 코드 빌드, 디바이스 플래시 또는 연결에 문제가 발생하는 경우 [문제 해결](troubleshoot-embedded-device-quickstarts.md)을 참조하세요.

:::zone pivot="iot-toolset-cmake"
애플리케이션 디버깅에 대한 자세한 내용은 [Visual Studio Code를 사용하여 디버깅](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)을 참조하세요.
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"
애플리케이션 디버깅에 대한 도움말은 **IAR EW for ARM** 의 **도움말** 에서 선택 항목을 참조하세요.  
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"
애플리케이션 디버깅에 대한 도움말을 보려면 MCUXpresso에서 **도움말 > MCUXPresso IDE 사용자 가이드** 를 열고 Azure RTOS 디버깅에 대한 콘텐츠를 참조하세요. 
:::zone-end

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 IoT Central 포털에서 해당 리소스를 삭제할 수 있습니다.

전체 Azure IoT Central 샘플 애플리케이션과 모든 해당 디바이스 및 리소스를 제거하려면 다음을 수행합니다.
1. **관리** > **애플리케이션** 을 차례로 선택합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure RTOS 샘플 코드가 포함된 사용자 지정 이미지를 빌드한 다음, 해당 이미지를 NXP EVK 디바이스로 플래시했습니다. 또한 IoT Central 포털을 사용하여 Azure 리소스를 만들고, NXP EVK를 Azure에 안전하게 연결하고, 원격 분석을 보고, 메시지를 보냈습니다.

다음 단계로, 다음 문서에서 IoT 디바이스 SDK를 사용하여 디바이스를 Azure IoT에 연결하는 방법을 자세히 살펴봅니다. 

> [!div class="nextstepaction"]
> [디바이스를 IoT Central에 연결](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [디바이스를 IoT Hub에 연결](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS는 기본 MCU/MPU 하드웨어 보호 메커니즘을 사용하여 통신을 보호하고 코드 및 데이터 격리를 만드는 구성 요소를 OEM에 제공합니다. 그러나 각 OEM은 궁극적으로 디바이스에서 진화하는 보안 요구 사항을 충족하는지 확인해야 합니다.

