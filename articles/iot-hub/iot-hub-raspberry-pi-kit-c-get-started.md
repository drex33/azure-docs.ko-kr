---
title: C를 사용하여 Azure IoT Hub에 Raspberry Pi 연결 | Microsoft Docs
description: Azure 클라우드 플랫폼으로 데이터를 보내기 위해 Raspberry Pi를 설정하고 Raspberry Pi용 Azure IoT Hub에 연결하는 방법을 알아봅니다.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 06/14/2021
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 845a2d7af740b804d25250753c02c2791da82944
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414938"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi를 Azure IoT Hub에 연결(C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

이 자습서에서는 먼저 Raspberry Pi OS를 실행하는 Raspberry Pi 작업의 기본 사항을 알아봅니다. 그런 다음 [Azure IoT Hub](about-iot-hub.md)를 사용하여 디바이스를 클라우드에 원활하게 연결하는 방법을 알아봅니다. Windows 10 IoT Core 샘플이 필요하면 [Windows 개발자 센터](https://www.windowsondevices.com/)로 이동하세요.

아직 키트가 없으세요? [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md)를 사용해 보세요. 또는 새 키트를 [여기](https://azure.microsoft.com/develop/iot/starter-kits)에서 구입합니다.

## <a name="what-you-do"></a>수행할 작업

* IoT Hub를 만듭니다.

* IoT Hub에 Pi용 디바이스를 등록합니다.

* Raspberry Pi를 설치합니다.

* Pi에서 샘플 애플리케이션을 실행하여 IoT Hub로 센서 데이터를 보냅니다.

앞에서 만든 IoT Hub에 Raspberry Pi를 연결합니다. 그런 다음 Pi에서 샘플 애플리케이션을 실행하여 BME280에서 온도 및 습도 데이터를 수집합니다. 마지막으로 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-learn"></a>학습 내용

* Azure IoT Hub를 만들고 새 디바이스 연결 문자열을 가져오는 방법.

* Pi를 BME280 센서와 연결하는 방법.

* Pi에서 샘플 애플리케이션을 실행하여 센서 데이터를 수집하는 방법.

* IoT Hub로 센서 데이터를 보내는 방법.

## <a name="what-you-need"></a>필요한 작업

![필요한 작업](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2 또는 Raspberry Pi 3 보드.

* 활성화된 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

* Pi에 연결할 모니터, USB 키보드 및 마우스.

* Windows 또는 Linux를 실행하는 Mac 또는 PC.

* 인터넷 연결

* 16GB 이상의 microSD 카드.

* 운영 체제 이미지를 microSD 카드에 굽기 위한 USB-SD 어댑터 또는 microSD 카드.

* 6피트 마이크로 USB 케이블과 5볼트 2암페어 전원 공급 장치.

다음 항목은 선택 사항입니다.

* 조립된 Adafruit BME280 온도, 압력 및 습도 센서.

* 실험용 회로판

* F/M 점퍼 와이어 6개.

* 확산형 10mm LED.

> [!NOTE]
> 코드 샘플이 시뮬레이션된 센서 데이터를 지원하므로 이러한 항목은 선택 사항입니다.
>

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi 설정

이제 Raspberry Pi를 설정합니다.

### <a name="install-the-raspberry-pi-os"></a>Raspberry Pi OS 설치

Raspberry Pi OS 이미지를 설치하기 위해 microSD 카드를 준비합니다.

1. Raspberry Pi OS를 다운로드합니다.

   1. [Raspberry Pi OS with Desktop을 다운로드](https://www.raspberrypi.org/software/)합니다(.zip 파일).

   2. 컴퓨터의 폴더에 이미지의 압축을 풉니다.

2. microSD 카드에 Raspberry Pi OS를 설치합니다.

   1. [Etcher SD 카드 버너 유틸리티를 다운로드하여 설치합니다](https://etcher.io/).

   2. Etcher를 실행하고 1단계에서 압축을 푼 Raspberry Pi OS 이미지를 선택합니다.

   3. microSD 카드 드라이브를 선택합니다. Etcher가 이미 올바른 드라이브를 선택했을 수 있습니다.

   4. Flash를 클릭하여 microSD 카드에 Raspberry Pi OS를 설치합니다.

   5. 설치가 완료되면 컴퓨터에서 microSD 카드를 제거합니다. 완료되면 Etcher가 microSD 카드를 자동으로 배출하거나 탑재를 해제하므로 microSD 카드를 바로 제거하는 것이 안전합니다.

   6. Pi에 microSD 카드를 삽입합니다.

### <a name="enable-ssh-and-spi"></a>SSH 및 SPI를 사용하도록 설정

1. 사용자 이름으로 `pi`, 암호로 `raspberry`를 사용하여 Pi를 모니터, 키보드, 마우스에 연결하고, Pi를 시작한 다음, Raspberry Pi OS에 로그인합니다.
 
2. Raspberry 아이콘 > **기본 설정** > **Raspberry Pi 구성** 을 클릭합니다.

   ![Raspberry Pi OS 기본 설정 메뉴](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. **인터페이스** 탭에서 **SPI** 및 **SSH** 를 **사용** 으로 설정한 다음 **확인** 을 클릭합니다. 실제 센서가 없고 시뮬레이트된 센서 데이터를 사용하려는 경우 이 단계는 선택 사항입니다.

   ![Raspberry Pi에서 SPI 및 SSH를 사용하도록 설정](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> SSH 및 SPI를 사용하려는 경우 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 및 [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md)에서 더 많은 참조 문서를 찾을 수 있습니다.
>

### <a name="connect-the-sensor-to-pi"></a>Pi에 센서 연결

실험용 회로판과 점퍼 와이어를 사용하여 LED 및 BME280 Pi를 다음과 같이 연결합니다. 센서가 없는 경우 [이 섹션을 건너뛰세요](#connect-pi-to-the-network).

![Raspberry Pi와 센서 연결](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

BME280 센서는 온도 및 습도 데이터를 수집할 수 있습니다. 그리고 디바이스와 클라우드 간에 통신이 있으면 LED가 깜박입니다.

센서 핀의 경우 다음 배선을 사용합니다.

| 시작(센서 및 LED)     | 끝(보드)            | 케이블 색   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD(5G 핀)         | GPIO 4(7 핀)         | 흰색 케이블   |
| LED GND(6G 핀)         | GND(6 핀)            | 검은색 케이블   |
| VDD(18F 핀)            | 3.3V PWR(17 핀)      | 흰색 케이블   |
| GND(20F 핀)            | GND(20 핀)           | 검은색 케이블   |
| SCK(21F 핀)            | SPI0 SCLK(23 핀)     | 주황색 케이블  |
| SDO(22F 핀)            | SPI0 MISO(21 핀)     | 노란색 케이블  |
| SDI(23F 핀)            | SPI0 MOSI(19 핀)     | 녹색 케이블   |
| CS(24F 핀)             | SPI0 CS(24 핀)       | 파란색 케이블    |

참조용으로 [Raspberry Pi 2 및 3 핀 매핑](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi)을 보려면 클릭하세요.

BME280이 Raspberry Pi에 성공적으로 연결되면 아래 이미지처럼 보여야 합니다.

![Pi와 BME280 연결](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>네트워크에 Pi 연결

마이크로 USB 케이블 및 전원 공급 장치를 사용하여 Pi를 켭니다. 이더넷 케이블을 사용하여 Pi를 유선 네트워크에 연결하거나 [Raspberry Pi Foundation의 지침](https://www.raspberrypi.org/documentation/configuration/wireless/)에 따라 Pi를 무선 네트워크에 연결합니다. Pi가 네트워크에 성공적으로 연결된 후 [Pi의 IP 주소](https://www.raspberrypi.org/documentation/remote-access/ip-address.md)를 적어 두어야 합니다.

![유선 네트워크에 연결](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Pi에서 샘플 애플리케이션 실행

### <a name="sign-into-your-raspberry-pi"></a>Raspberry Pi에 로그인

1. 호스트 컴퓨터에서 다음 SSH 클라이언트 중 하나를 사용하여 Raspberry Pi에 연결합니다.
   
   **Windows 사용자**
   1. Windows용 [PuTTY](https://www.putty.org/)를 다운로드 및 설치합니다. 
   1. 호스트 이름(또는 IP 주소) 섹션에 Pi의 IP 주소를 복사하고 연결 형식으로 SSH를 선택합니다.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac 및 Ubuntu 사용자**

   Ubuntu 또는 macOS에서 기본 제공되는 SSH 클라이언트를 사용합니다. SSH를 통해 Pi를 연결하려면 `ssh pi@<ip address of pi>`를 실행해야 할 수도 있습니다.
   > [!NOTE]
   > 기본 사용자 이름은 `pi`이며 암호는 `raspberry`입니다.


### <a name="configure-the-sample-application"></a>샘플 애플리케이션 구성

1. 다음 명령을 실행하여 샘플 애플리케이션을 복제합니다.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. 개발 환경을 준비하고 샘플을 빌드하기 위한 샘플이 포함된 설치 스크립트가 제공됩니다. 설치 스크립트를 실행합니다.

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > **실제 BME280이 없는** 경우 '--simulated-data'를 명령줄 매개 변수로 사용하여 온도 및 습도 데이터를 시뮬레이션할 수 있습니다. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>애플리케이션 빌드 및 실행

1. 설치 스크립트에서는 이미 샘플을 빌드했습니다. 그러나 변경을 수행하고 샘플 애플리케이션을 다시 빌드해야 하는 경우 다음 명령을 실행합니다.

   ```bash
   cmake . && make
   ```
   
   ![빌드 출력](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. 다음 명령을 실행하여 샘플 애플리케이션을 실행합니다.

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > 디바이스 연결 문자열을 복사하여 작은따옴표 안에 붙여 넣습니다.
   >

IoT Hub로 전송되는 센서 데이터와 메시지를 보여 주는 다음 출력이 표시됩니다.

![출력 - Raspberry Pi에서 IoT Hub로 전송된 센서 데이터](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>허브에서 받은 메시지 읽기

IoT 허브가 디바이스에서 수신한 메시지를 모니터링하는 한 가지 방법은 Visual Studio Code용 Azure IoT Tools를 사용하는 것입니다. 자세히 알아보려면 [Visual Studio Code용 Azure IoT Tools를 사용하여 디바이스와 IoT Hub 간에 메시지 보내고 받기](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)를 참조하세요.

디바이스에서 전송되는 데이터를 처리하는 방법에 대한 자세한 내용은 다음 섹션을 계속 진행하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 항목에 생성된 리소스는 이 문서 세트의 다른 자습서와 빠른 시작을 통해 사용할 수 있습니다. 다른 빠른 시작이나 자습서를 계속 사용하려면 이 항목에서 만든 리소스를 정리하지 마세요. 계속하지 않으려면 다음 단계를 사용하여 이 항목에서 만든 모든 리소스를 Azure Portal에서 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 직접 만든 IoT Hub를 선택합니다. 
1. IoT Hub 개요 창 위쪽에서 **삭제** 를 클릭합니다.
1. 허브 이름을 입력하고 **삭제** 를 다시 클릭하여 IoT Hub를 영구적으로 삭제하는 것을 확인합니다.


## <a name="next-steps"></a>다음 단계

샘플 애플리케이션을 실행하여 센서 데이터를 수집하고 IoT Hub로 전송했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]