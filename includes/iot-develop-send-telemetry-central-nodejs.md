---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 8c2e50084fd972cac439a27e54759f281bb56fa4
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368977"
---
[![코드 찾아보기](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/main/device/samples)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. 먼저 디바이스 호스트를 위한 Azure IoT Central 애플리케이션을 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 온도 컨트롤러를 만들고 IoT Central에 안전하게 연결하고 원격 분석을 보냅니다. 온도 컨트롤러 샘플 애플리케이션은 로컬 컴퓨터에서 실행되고 IoT Central에 보낼 시뮬레이션된 센서 데이터를 생성합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 빠른 시작은 Windows, Linux 및 Raspberry Pi에서 실행됩니다. 다음 OS 및 디바이스 버전에서 테스트되었습니다.

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi 3 모델 B+에서 실행되는 Raspberry Pi OS(Raspbian) 버전 10

개발 컴퓨터에 다음 필수 구성 요소를 설치합니다.

- [Node.js](https://nodejs.org/) 버전 6 이상 버전을 확인하려면 콘솔 애플리케이션에서 `node --version`을 실행합니다.
- [Git](https://git-scm.com/downloads)

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>디바이스 샘플 실행
이 섹션에서는 로컬 환경을 구성하고, Azure IoT Node.js 디바이스 SDK를 설치하고, 온도 컨트롤러를 만드는 샘플을 실행합니다.

### <a name="configure-your-environment"></a>환경 구성

1. Windows CMD, PowerShell 또는 Bash와 같은 콘솔을 엽니다.

1. 콘솔에 적합한 명령을 사용하여 다음 환경 변수를 설정합니다. 디바이스는 이러한 값을 사용하여 IoT Central에 연결합니다. `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` 및 `IOTHUB_DEVICE_DPS_DEVICE_ID`의 경우 이전에 저장한 디바이스 연결 값을 사용합니다.

    **CMD(Windows)**

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

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>SDK 및 샘플을 설치합니다.

1. Azure IoT Node.js 디바이스 SDK를 로컬 컴퓨터에 복사합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. 샘플 디렉터리로 이동합니다.

    **Windows**
    ```console
    cd azure-iot-sdk-node\device\samples\javascript
    ```

    **Linux 또는 Raspberry Pi OS**
    ```console
    cd azure-iot-sdk-node/device/samples/javascript
    ```

2. Azure IoT Node.js SDK 및 필요한 종속성을 설치합니다.
    ```console
    npm install
    ```

### <a name="run-the-code"></a>코드 실행

1. 콘솔에서 SDK에서 다음 코드 샘플을 실행합니다. 샘플은 온도 조절기 센서가 있는 온도 컨트롤러를 만듭니다.
    ```console
    node pnp_temperature_controller.js
    ```

    디바이스가 IoT Central 애플리케이션에 연결되면 애플리케이션에서 만든 디바이스 인스턴스에 연결하고 원격 분석 전송을 시작합니다. 연결 세부 정보 및 원격 분석 출력이 콘솔에 표시됩니다. 
    
    ```output
    registration succeeded
    assigned hub=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net
    deviceId=my-sdk-device
    payload=undefined
    Connecting using connection string: HostName=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net;DeviceId=my-sdk-device;SharedAccessKey=xxxxxxxxxxxxxxxxxxxxxxxxxxx
    Enabling the commands on the client
    Please enter q or Q to exit sample.
    The following properties will be updated for root interface.
    { serialNumber: 'alwinexlepaho8329' }
    The following properties will be updated for component: thermostat1
    {
      thermostat1: { maxTempSinceLastReboot: 40.53506261527863, __t: 'c' }
    }
    The following properties will be updated for component: thermostat2
    {
      thermostat2: { maxTempSinceLastReboot: 89.55136974144273, __t: 'c' }
    }
    The following properties will be updated for component: deviceInformation
    {
      deviceInformation: {
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
        __t: 'c'
      }
    }
    executed sample
    Received an update for device with value: {"$version":1}
    Properties have been reported for root interface.
    Properties have been reported for component: thermostat1
    Properties have been reported for component: thermostat2
    Properties have been reported for component: deviceInformation
    Sending telemetry message 0 from component: thermostat1
    Sending telemetry message 0 from component: thermostat2
    ```
