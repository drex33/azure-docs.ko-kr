---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/10/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 0665035b31142f5be7fa825a83d8575b49e8e717
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909137"
---
[![코드 찾아보기](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. 먼저 디바이스 호스트를 위한 Azure IoT Central 애플리케이션을 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 시뮬레이션된 온도 조절기를 실행하고 IoT Central에 안전하게 연결하고 원격 분석을 전송합니다.

## <a name="prerequisites"></a>필수 조건
이 빠른 시작은 Windows, Linux 및 Raspberry Pi에서 실행됩니다. 다음 OS 및 디바이스 버전에서 테스트되었습니다.

- Windows 10
- WSL(Linux용 Windows 하위 시스템)에서 실행되는 Ubuntu 20.04 LTS
- Raspberry Pi 3 Model B+에서 실행되는 Raspberry Pi OS 버전 10(buster)

운영 체제의 나머지 필수 구성 요소를 설치합니다.

### <a name="linux-or-raspberry-pi-os"></a>Linux 또는 Raspberry Pi OS
Linux 및 Raspberry Pi OS에서 이 빠른 시작을 완료하려면 다음 소프트웨어를 설치합니다.

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
1. 설치 프로그램 **개별 구성 요소** 탭에서 **Windows용 Git** 을 선택합니다.
1. 설치를 실행합니다.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 로컬 환경을 구성하고, Azure IoT C 디바이스 SDK를 설치하고, 시뮬레이션된 온도 컨트롤러를 만드는 샘플을 실행합니다.

### <a name="configure-your-environment"></a>환경 구성

1. 콘솔을 열어 Azure IoT C 디바이스 SDK를 설치하고 코드 샘플을 실행합니다. Windows의 경우 **시작** 메뉴에서 *Developer Command Prompt for VS 2019* 를 선택하여 콘솔을 엽니다. Linux 및 Raspberry Pi OS의 경우 Bash 명령에 대한 터미널을 엽니다. 

1. 콘솔에 적합한 명령을 사용하여 다음 환경 변수를 설정합니다. 시뮬레이션된 디바이스는 이 값을 사용하여 IoT Central에 연결합니다. `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` 및 `IOTHUB_DEVICE_DPS_DEVICE_ID`의 경우 이전에 저장한 디바이스 연결 값을 사용합니다.

    **CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Windows CMD의 경우 변수 값을 묶는 따옴표가 없습니다.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>SDK 및 샘플을 설치합니다.

1. 샘플 리포지토리를 복제하려는 로컬 폴더로 이동합니다.

1. Azure IoT C 디바이스 SDK를 로컬 컴퓨터에 복사합니다.

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

### <a name="run-the-code"></a>코드 실행

1. 콘솔에 적합한 명령을 사용하여 샘플 코드를 실행합니다.

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/pnp_temperature_controller
    ```

    시뮬레이션된 디바이스가 IoT Central 애플리케이션에 연결되면 애플리케이션에서 만든 디바이스 인스턴스에 연결하고 원격 분석 전송을 시작합니다. 연결 세부 정보 및 원격 분석 출력이 콘솔에 표시됩니다. 
    
    ```output
    Info: Initiating DPS client to retrieve IoT Hub connection information
    -> 17:03:08 CONNECT | VER: 4 | KEEPALIVE: 0 | FLAGS: 194 | USERNAME: xxxxxxxxxxxxxxx/registrations/my-sdk-device/api-version=2019-03-31&ClientVersion=1.6.0 | PWD: XXXX | CLEAN: 1
    <- 17:03:09 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
    -> 17:03:10 SUBSCRIBE | PACKET_ID: 1 | TOPIC_NAME: $dps/registrations/res/# | QOS: 1
    <- 17:03:11 SUBACK | PACKET_ID: 1 | RETURN_CODE: 1
    Info: Provisioning callback indicates success.  iothubUri=iotc-xxxxxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net, deviceId=my-sdk-device
    -> 17:03:27 DISCONNECT
    Info: DPS successfully registered.  Continuing on to creation of IoTHub device client handle.
    Info: Successfully created device client.  Hit Control-C to exit program
    
    Info: Sending serialNumber property to IoTHub
    Info: Sending device information property to IoTHub.  propertyName=swVersion, propertyValue="1.0.0.0"
    Info: Sending device information property to IoTHub.  propertyName=manufacturer, propertyValue="Sample-Manufacturer"
    Info: Sending device information property to IoTHub.  propertyName=model, propertyValue="sample-Model-123"
    Info: Sending device information property to IoTHub.  propertyName=osName, propertyValue="sample-OperatingSystem-name"
    Info: Sending device information property to IoTHub.  propertyName=processorArchitecture, propertyValue="Contoso-Arch-64bit"
    Info: Sending device information property to IoTHub.  propertyName=processorManufacturer, propertyValue="Processor Manufacturer(TM)"
    Info: Sending device information property to IoTHub.  propertyName=totalStorage, propertyValue=10000
    Info: Sending device information property to IoTHub.  propertyName=totalMemory, propertyValue=200
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat1
    Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat2
    ```