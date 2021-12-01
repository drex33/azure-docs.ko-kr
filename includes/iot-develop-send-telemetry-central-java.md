---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 11/02/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: febd6717e837949f74d6228dbf19837bf82ae52d
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133369004"
---
[![코드 찾아보기](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample)

이 빠른 시작에서는 기본 Azure IoT 애플리케이션 개발 워크플로에 대해 알아봅니다. 먼저 디바이스 호스트를 위한 Azure IoT Central 애플리케이션을 만듭니다. 그런 다음 Azure IoT 디바이스 SDK 샘플을 사용하여 온도 컨트롤러를 만들고 IoT Central에 안전하게 연결하고 원격 분석을 보냅니다. 온도 컨트롤러 샘플 애플리케이션은 로컬 컴퓨터에서 실행되고 IoT Central에 보낼 시뮬레이션된 센서 데이터를 생성합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작은 Windows, Linux 및 Raspberry Pi에서 실행됩니다. 다음 OS 및 디바이스 버전에서 테스트되었습니다.

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi 3 모델 B+에서 실행되는 Raspberry Pi OS(Raspbian) 버전 10

개발 컴퓨터에 다음 필수 구성 요소를 설치합니다.

- [Git](https://git-scm.com/downloads)

운영 체제의 나머지 필수 구성 요소를 설치합니다.

### <a name="windows"></a>Windows

Windows에서 이 빠른 시작을 완료하려면 다음 소프트웨어를 설치합니다.

- Java SE Development Kit 8 이상. 여러 플랫폼용 Java 8(LTS) JDK는 [OpenJDK의 Zulu 빌드 다운로드](https://www.azul.com/downloads/zulu-community/)에서 다운로드할 수 있습니다. 설치 프로그램에서 **경로에 추가** 옵션을 선택합니다.

- [Apache Maven 3](https://maven.apache.org/download.cgi). 다운로드를 로컬 폴더로 추출한 후 Maven */bin* 폴더의 전체 경로를 Windows `PATH` 환경 변수에 추가합니다.

### <a name="linux-or-raspberry-pi-os"></a>Linux 또는 Raspberry Pi OS

Linux 또는 Raspberry Pi OS에서 이 빠른 시작을 완료하려면 다음 소프트웨어를 설치합니다.

> [!NOTE]
> 이 섹션의 단계는 Linux Ubuntu/Debian 배포판을 기준으로 합니다. Raspberry Pi OS는 Debian을 기준으로 합니다. 다른 Linux 배포판을 사용하는 경우 해당 단계를 적절하게 수정해야 합니다.

- OpenJDK(Open Java Development Kit) 8 이상. `java -version` 명령을 사용하여 시스템에 설치된 Java 버전을 확인할 수 있습니다. JRE(Java 런타임) 뿐만 아니라 JDK가 설치되어 있는지 확인합니다.

    1. 사용하는 시스템용 OpenJDK를 설치하려면 다음 명령을 입력합니다.

        사용하는 시스템용 OpenJDK의 기본 버전을 설치하려면(작성 당시 Ubuntu 20.04 및 Raspberry Pi OS 10용 OpenJDK 11) 다음을 수행합니다.

        ```bash
        sudo apt update
        sudo apt install default-jdk
        ```

        또는 설치할 JDK 버전을 지정할 수 있습니다. 예를 들면 다음과 같습니다.

        ```bash
        sudo apt update
        sudo apt install openjdk-8-jdk
        ```

    1. 시스템에 여러 버전의 Java가 설치되어 있는 경우 다음 명령을 사용하여 Java 및 Java 컴파일러의 기본(자동) 버전을 구성할 수 있습니다.

        ```bash
        update-java-alternatives --list          #list the Java versions installed
        sudo update-alternatives --config java   #set the default Java version
        sudo  update-alternatives --config javac #set the default Java compiler version
        ```

    1. `JAVA_HOME` 환경 변수를 JDK 설치 경로로 설정합니다. 일반적으로 **/usr/lib/jvm** 디렉터리의 버전이 지정된 하위 디렉터리입니다.

        ```bash
        export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")
        ```

        > [!IMPORTANT]
        > 이 명령은 현재 셸 환경에서 `JAVA_HOME` 변수를 설정합니다. 새 셸을 열 때마다 사용할 수 있도록 `~/.bashrc` 또는 `/etc/profile` 파일에 이 명령을 추가하는 것이 좋습니다.

    1. 설치된 Java JDK(및 JRE) 버전, Java 컴파일러 버전이 JDK 버전과 일치하는지와 `JAVA_HOME` 환경 변수가 제대로 설정되었는지 확인합니다.

        ```bash
        java -version
        javac -version
        echo $JAVA_HOME
        ```

- Apache Maven 3. `mvn --version` 명령을 사용하여 사용 중인 시스템에 설치된 Maven 버전을 확인할 수 있습니다.

    1. Maven을 설치하려면 다음 명령을 입력합니다.

        ```bash
        sudo apt-get update
        sudo apt-get install maven
        ```

    1. 다음 명령을 입력하여 설치를 확인합니다.

        ```bash
        mvn --version
        ```

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

이 섹션에서는 로컬 환경을 구성하고, Azure IoT Java 디바이스 SDK를 설치하고, 온도 컨트롤러를 만드는 샘플을 실행합니다.

### <a name="configure-your-environment"></a>환경 구성

1. Windows CMD 또는 Bash와 같은 콘솔을 엽니다.

    **Linux 및 Raspberry Pi OS**

    JAVA_HOME(`echo $JAVA_HOME`) 환경 변수가 설정되어 있는지 확인합니다. SDK 및 샘플을 성공적으로 빌드하려면 이 환경 변수를 설정해야 합니다. JAVA_HOME 설정에 대한 내용은 [Linux/Raspberry Pi 필수 조건](#linux-or-raspberry-pi-os)을 참조하세요.

1. 콘솔에 적합한 명령을 사용하여 다음 환경 변수를 설정합니다. 디바이스는 이러한 값을 사용하여 IoT Central에 연결합니다. `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` 및 `IOTHUB_DEVICE_DPS_DEVICE_ID`의 경우 이전에 저장한 디바이스 연결 값을 사용합니다.

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

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="build-and-run-the-code"></a>코드 빌드 및 실행

1. Azure IoT Java 디바이스 SDK를 로컬 머신에 복제합니다.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-java.git
    ```

1. SDK의 루트 폴더로 이동하고 다음 명령을 실행하여 SDK를 빌드한 후 샘플을 업데이트합니다.

    ```console
    cd azure-iot-sdk-java
    mvn install -T 2C -DskipTests
    ```

    이 작업은 몇 분이 걸립니다.

1. 샘플 디렉터리로 이동합니다.

    **Windows**

    ```console
    cd device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample
    ```

    **Linux 또는 Raspberry Pi OS**

    ```bash
    cd device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample
    ```

1. SDK에서 다음 코드 샘플을 실행합니다. 샘플은 온도 조절기 센서가 있는 온도 컨트롤러를 만듭니다.

    ```console
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
    ```

    디바이스가 IoT Central 애플리케이션에 연결되면 애플리케이션에서 만든 디바이스 인스턴스에 연결하고 원격 분석 전송을 시작합니다. 몇 가지 초기 프로비저닝 세부 정보를 제공한 후 콘솔은 온도 컨트롤러에 대한 원격 분석을 출력하기 시작합니다.

    ```output
    2021-05-13 15:39:26.411 DEBUG Mqtt:253 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
    2021-05-13 15:39:26.428 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.432 DEBUG TemperatureController:427 - Property: Update - component = "deviceInformation" is COMPLETED.
    2021-05-13 15:39:26.436 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.438 DEBUG TemperatureController:438 - Telemetry: Sent - {"workingSet": 1024.0KiB }
    2021-05-13 15:39:26.439 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Request Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
    2021-05-13 15:39:26.439 DEBUG TemperatureController:446 - Property: Update - {"serialNumber": SR-123456} is COMPLETED
    2021-05-13 15:39:26.447 INFO  IotHubTransport:540 - Message was queued to be sent later ( Message details: Correlation Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] Message Id [xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx] )
    2021-05-13 15:39:26.447 DEBUG TemperatureController:465 - Telemetry: Sent - {"temperature": 44.4░C} with message Id xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
    ```