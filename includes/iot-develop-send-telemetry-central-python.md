---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: d4a571e1553e3a9e3dd9f5df63c8ecc16fa07abf
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713021"
---
[![코드 찾아보기](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. 먼저 디바이스 호스트를 위한 Azure IoT Central 애플리케이션을 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 시뮬레이션된 온도 조절기를 실행하고 IoT Central에 안전하게 연결하고 원격 분석을 전송합니다.

## <a name="prerequisites"></a>사전 요구 사항
- [Python](https://www.python.org/downloads/) 버전 3.7 이상 Python 버전을 확인하려면 `python --version`을 실행합니다.
- [Git](https://git-scm.com/downloads)
- Linux 또는 Windows에서 이 빠른 시작을 실행할 수 있습니다. 셸 명령은 표준 Linux 경로 구분 기호 `/`를 사용합니다. Windows를 사용하는 경우 이 구분 기호를 Windows 경로 구분 기호 `\`로 바꿉니다.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-a-simulated-device"></a>시뮬레이트된 디바이스 실행
이 섹션에서는 로컬 환경을 구성하고, Azure IoT Python 디바이스 SDK를 설치하고, 시뮬레이션된 온도 컨트롤러를 만드는 샘플을 실행합니다.

### <a name="configure-your-environment"></a>환경 구성

1. Windows CMD, PowerShell 또는 Bash를 사용하여 콘솔을 엽니다.

1. 콘솔에 적합한 명령을 사용하여 다음 환경 변수를 설정합니다. 시뮬레이션된 디바이스는 이 값을 사용하여 IoT Central에 연결합니다. `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` 및 `IOTHUB_DEVICE_DPS_DEVICE_ID`의 경우 이전에 저장한 디바이스 연결 값을 사용합니다.

    **Windows CMD**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > Windows CMD의 경우 변수 값을 묶는 따옴표가 없습니다.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash(Linux 또는 Windows)**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>SDK 및 샘플을 설치합니다.

1. Azure IoT Python 디바이스 SDK를 로컬 컴퓨터에 복사합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. 샘플 디렉터리로 이동합니다.
    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Azure IoT Python SDK를 설치합니다.
    ```console
    pip3 install azure-iot-device
    ```

### <a name="run-the-code"></a>코드 실행

1. 콘솔에서 SDK에서 다음 코드 샘플을 실행합니다. 샘플은 자동 온도 조절기 센서가 있는 시뮬레이션된 온도 컨트롤러를 만듭니다.
    ```console
    python temp_controller_with_thermostats.py
    ```

    시뮬레이션된 디바이스가 IoT Central 애플리케이션에 연결되면 애플리케이션에서 만든 디바이스 인스턴스에 연결하고 원격 분석 전송을 시작합니다. 연결 세부 정보 및 원격 분석 출력이 콘솔에 표시됩니다. 
    
    ```output
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
    Updating pnp properties for root interface
    {'serialNumber': 'alohomora'}
    Updating pnp properties for thermostat1
    {'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
    Updating pnp properties for thermostat2
    {'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
    Updating pnp properties for deviceInformation
    {'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry from various components
    Sent message
    {"temperature": 33}
    ```