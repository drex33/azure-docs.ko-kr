---
title: Azure Percept DK 디바이스 문제 해결
description: Azure Percept DK 및 IoT Edge의 몇 가지 일반적인 문제에 대한 문제 해결 팁을 확인하세요.
author: juniem
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: 8db131bd39ae8ebe27720a7d725f6ab8082dfd83
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780018"
---
# <a name="troubleshoot-the-azure-percept-dk-device"></a>Azure Percept DK 디바이스 문제 해결

이 문제 해결 문서의 목적은 Azure Percept DK 사용자가 자신의 개발 키트와 관련된 일반적인 문제를 빠르게 해결할 수 있도록 도와주는 것입니다. 또한 추가 지원이 필요할 때의 로그 수집에 대한 안내도 제공됩니다.

## <a name="log-collection"></a>로그 수집
이 섹션에서는 수집할 로그 및 로그 수집 방법에 대한 안내가 제공됩니다.

### <a name="how-to-collect-logs"></a>로그 수집 방법
1. [SSH를 통해](./how-to-ssh-into-percept-dk.md) 개발 키트에 연결합니다.
1. SSH 터미널 창에서 필요한 명령을 실행합니다. 로그 수집 명령 목록은 다음 섹션을 참조하세요.
1. 추가 분석을 위해 출력을 .txt 파일로 리디렉션하려면 다음 구문을 사용합니다.
    ```console
    sudo [command] > [file name].txt
    ```
1. 복사할 수 있도록 .txt 파일의 사용 권한을 변경합니다.
    ```console
    sudo chmod 666 [file name].txt
    ```
1. SCP를 통해 파일을 호스트 PC에 복사합니다.
    ```console
    scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
    ```

    ```[local host file path]```는 .txt 파일을 복사할 호스트 PC의 위치를 나타냅니다. ```[remote username]```는 [설치 경험](./quickstart-percept-dk-set-up.md) 중 선택한 SSH 사용자 이름입니다.

### <a name="log-types-and-commands"></a>로그 유형 및 명령

|로그 목적      |수집 시기         |명령                     |
|-----------------|---------------------------|----------------------------|
|*지원 번들* - 대부분의 고객 지원 요청에 필요한 로그 세트를 제공합니다.|지원을 요청할 때마다 수집됩니다.|```sudo iotedge support-bundle --since 1h``` <br><br>*"--since 1h"를 "6h"(6시간), "6d"(6일) 또는 "6m"(6분)과 같은 모든 시간 범위로 변경할 수 있습니다.*|
|*OOBE 로그* - 설치 환경에 대한 세부 정보를 기록합니다.|설치 환경 중 문제가 발견될 때 수집됩니다.|```sudo journalctl -u oobe -b```|
|*edgeAgent 로그* - 디바이스에서 실행되는 모든 모듈의 버전 번호를 기록합니다.|하나 이상의 모듈이 작동하지 않을 때 수집됩니다.|```sudo iotedge logs edgeAgent```|
|*모듈 컨테이너 로그* - 특정 IoT Edge 모듈 컨테이너에 대한 세부정보를 기록합니다.|모듈에서 문제가 발견될 때 수집됩니다.|```sudo iotedge logs [container name]```|
|*네트워크 로그* - Wi-Fi 서버 및 네트워크 스택을 포함하는 로그 세트입니다.|Wi-Fi 또는 네트워크 문제가 발견될 때 수집됩니다.|```sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd > network_log.txt```<br><br>```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version && rpm -q ztpd > system_ver.txt```<br><br>두 명령을 모두 실행합니다. 각 명령은 여러 로그를 수집하고 이를 단일 출력에 넣습니다.|

> [!WARNING]
> `support-bundle` 명령의 출력에는 호스트, 디바이스 및 모듈 이름, 모듈에 의해 기록된 정보 등이 포함될 수 있습니다. 공개 포럼의 출력을 공유하는 경우 이 점에 유의하세요.

## <a name="troubleshooting-commands"></a>명령 문제 해결
개발 키트로 찾을 수 있는 문제의 문제 해결에 사용될 수 있는 명령 세트를 보여줍니다. 이러한 명령을 실행하려면 먼저 [SSH를 통해](./how-to-ssh-into-percept-dk.md) 개발 키트에 연결해야 합니다. 

Azure IoT Edge 명령에 대한 자세한 내용은 [Azure IoT Edge 디바이스 문제 해결 설명서](../iot-edge/troubleshoot.md)를 참조하세요. 

|함수         |사용 시기                    |명령                 |
|------------------|----------------------------|---------------------------|
|개발 키트에서 소프트웨어 버전을 확인합니다.|개발 키트의 소프트웨어 버전을 확인해야 할 때마다 사용됩니다.|```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version```|
|개발 키트의 온도를 확인합니다.|개발 키트가 과열될 수 있다고 생각되는 경우에 사용됩니다.|```cat /sys/class/thermal/thermal_zone0/temp```|
|개발 키트의 원격 분석 ID를 확인합니다.|개발 키트의 고유 원격 분석 식별자를 알아야 할 때 사용됩니다.|```sudo azure-device-health-id```|
|IoT Edge의 상태를 확인합니다.|클라우드에 연결되는 IoT Edge 모듈 관련 문제가 있을 때마다 사용됩니다.|```sudo iotedge check```|
|Azure IoT Edge 보안 디먼을 다시 시작합니다.|IoT Edge가 응답하지 않거나 올바르게 작동하지 않을 때 사용됩니다.|```sudo systemctl restart iotedge``` |
|배포된 Azure IoT Edge 모듈을 나열합니다.|개발 키트에 배포된 모든 모듈을 확인해야 할 때 사용됩니다.|```sudo iotedge list``` |
|지정된 파일 시스템의 사용 가능한/총 공간을 표시합니다.|개발 키트에서 사용 가능한 스토리지를 확인해야 할 때 사용됩니다.|```df [option] [file]```|
|개발 키트의 IP 및 인터페이스 정보를 표시합니다.|개발 키트의 IP 주소를 알아야 할 때 사용됩니다.|`ip route get 1.1.1.1`        | 
|개발 키트의 IP 주소만 표시합니다.|개발 키트의 IP 주소만 필요하고 다른 인터페이스 정보는 필요하지 않을 때 사용됩니다.|<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |

## <a name="usb-update-errors"></a>USB 업데이트 오류

|오류:                                    |해결책:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX during USB flash via UUU |이 오류는 UUU 업데이트 중에 USB 연결 오류가 발생한 경우에 발생합니다. USB 케이블이 PC 또는 Percept DK 캐리어 보드의 USB 포트에 제대로 연결되어 있지 않으면, 이 양식의 오류가 발생합니다. USB 케이블의 양쪽 끝을 분리하고 다시 연결한 다음 케이블을 흔들어 안전하게 연결되었는지 확인합니다.|

## <a name="clearing-hard-drive-space-on-the-azure-percept-dk"></a>Azure Percept DK 하드 드라이브 공간 지우기
Azure Percept DK에서 하드 드라이브 공간을 차지하는 두 가지 구성 요소는 docker 컨테이너 로그와 docker 컨테이너 자체입니다. 컨테이너 로그가 하드 디스크 공간을 모두 차지하지 않도록 Azure Percept DK에는 새 로그가 생성될 때 이전 로그를 모두 순환시켜 주는 기본 제공되는 로그 순환이 포함되어 있습니다.

docker 컨테이너 수가 하드 드라이브 공간 문제를 일으키는 상황에서는 다음 단계에 따라 사용되지 않는 컨테이너를 삭제할 수 있습니다.
1. [SSH로 개발 키트에 연결합니다.](./how-to-ssh-into-percept-dk.md)
1. 다음 명령을 실행합니다. `docker system prune`

그러면 사용되지 않는 컨테이너, 네트워크, 이미지 및 선택적으로 볼륨이 모두 제거됩니다. 자세한 내용은 [이 페이지를 참조하세요](https://docs.docker.com/engine/reference/commandline/system_prune/).

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK 캐리어 보드 LED 상태

캐리어 보드 하우징 위에는 세 개의 작은 LED가 있습니다. LED 1 옆에 클라우드 아이콘이 인쇄되고 LED 2 옆에 Wi-Fi 아이콘이 인쇄되며 LED 3 옆에 느낌표 아이콘이 인쇄됩니다. 각 LED 상태에 대한 자세한 내용은 아래 표를 참조하세요.

|LED             |시스템 상태      |Description                      |
|----------------|-----------|---------------------------------|
|LED 1(IoT Hub) |켜기(단색) |디바이스가 IoT Hub에 연결되어 있습니다. |
|LED 2(Wi-fi)   |저속 깜박임 |디바이스를 Wi-Fi Easy Connect로 구성할 준비가 되었으며 구성기에 존재를 알리고 있습니다. |
|LED 2(Wi-fi)   |고속 깜박임 |인증에 성공하고 디바이스를 연결하는 중입니다. |
|LED 2(Wi-fi)   |켜기(단색) |인증 및 연결에 성공했습니다. 디바이스가 Wi-Fi 네트워크에 연결되어 있습니다. |
|LED 3           |해당 없음         |LED를 사용하지 않습니다. |
