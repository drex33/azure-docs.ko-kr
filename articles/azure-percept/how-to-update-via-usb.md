---
title: USB-C 연결을 통해 Azure Percept DK 업데이트
description: USB-C 케이블 연결을 통해 Azure Percept DK를 업데이트하는 방법을 알아봅니다.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 293249c764155aefe16ef23738b1e4efea4255e5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751047"
---
# <a name="update-azure-percept-dk-over-a-usb-c-connection"></a>USB-C 연결을 통해 Azure Percept DK 업데이트

이 가이드에서는 USB 연결을 통해 개발 키트의 운영 체제 및 펌웨어를 성공적으로 업데이트하는 방법을 보여 줍니다. 이 절차에서 수행하는 작업의 개요는 다음과 같습니다.

1. 호스트 컴퓨터에 업데이트 패키지 다운로드
1. 업데이트 패키지를 개발 키트로 전송하는 명령 실행
1. SSH 또는 DIP 스위치를 사용하여 개발 키트를 USB 모드로 설정
1. USB-C 케이블을 통해 개발 키트를 호스트 컴퓨터에 연결
1. 업데이트가 완료될 때까지 대기

> [!WARNING]
> USB를 통해 개발 키트를 업데이트하면 AI 모델 및 컨테이너를 포함하여 디바이스의 기존 데이터가 모두 삭제됩니다.
>
> 모든 지침을 순서대로 수행합니다. 단계를 건너뛰면 개발 키트가 사용할 수 없는 상태가 될 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK
- Wi-Fi 기능 및 사용 가능한 USB-C 또는 USB-A 포트를 사용하는 Windows 또는 Linux 기반 호스트 컴퓨터
- USB-C - USB-A 케이블(선택 사항, 별도로 판매)
- [Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md) 중에 만든 SSH 로그인 계정
- 개발 키트와 함께 제공되는 육각 렌치로, 개발 키트의 뒷면에서 나사 제거(DIP 스위치 방법을 사용하는 경우)

> [!NOTE]
> **Mac 사용자** - MAC을 호스트 컴퓨터로 사용하면 USB 연결을 통해 Azure Percept DK 업데이트할 수 없습니다. 

## <a name="download-software-tools-and-update-files"></a>소프트웨어 도구 다운로드 및 파일 업데이트

1. [NXP UUU 도구](https://github.com/NXPmicro/mfgtools/releases)입니다. **자산** 탭에서 **최신 릴리스** uuu.exe 파일(Windows용) 또는 uuu 파일(Linux용)을 다운로드합니다. UUU는 NXP 개발 보드를 업데이트하기 위해 사용되는 NXP에서 만든 도구입니다.

1. [업데이트 파일을 다운로드합니다](./how-to-select-update-package.md). 다음 섹션에서 추출할 zip 파일에 모두 포함되어 있습니다.

1. 세 개의 빌드 아티팩트가 모두 있는지 확인합니다.
    - Azure-Percept-DK- *&lt;버전 번호&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>환경 설정

1. 호스트 컴퓨터에서 명령줄을 통해 쉽게 액세스할 수 있는 위치에 폴더/디렉터리를 만듭니다.

1. UUU 도구(**uuu.exe** 또는 **uuu**)를 새 폴더에 복사합니다.

1. 이전에 다운로드한 업데이트 파일을 UUU 도구가 포함된 새 폴더로 추출합니다.

## <a name="run-the-command-that-transfers-the-update-package-to-the-dev-kit"></a>업데이트 패키지를 개발 키트로 전송하는 명령 실행

1. Windows 명령 프롬프트(시작 > cmd) 또는 Linux 터미널을 열고 **업데이트 파일 및 UUU 도구가 저장된 폴더로 이동** 합니다. 

1. 명령 프롬프트 또는 터미널에 다음 명령을 입력합니다.

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. 명령 프롬프트 창에 "**알려진 USB 디바이스가 표시되길 기다리는 중...** " 메시지가 표시됩니다. UUU 도구가 이제 호스트 컴퓨터에서 개발 키트가 감지되기를 기다리는 중입니다. **다음 단계로 진행하여 개발 키트를 USB 모드로 전환합니다.**

## <a name="set-the-dev-kit-into-usb-mode"></a>개발 키트를 USB 모드로 설정
개발 키트를 "USB 모드"로 설정하는 방법에는 SSH를 통하거나 개발 키트에서 DIP 스위치를 변경하는 두 가지 방법이 있습니다. 상황에 가장 적합한 방법을 선택합니다.

### <a name="using-ssh"></a>SSH 사용
SSH는 개발 키트를 USB 모드로 설정하는 가장 안전하고 선호되는 방법입니다. 그러나 개발 키트의 Wi-Fi 액세스 지점에 연결할 수 있어야 합니다. 개발 키트의 Wi-Fi 액세스 지점에 연결할 수 없는 경우 DIP 스위치 방법을 사용해야 합니다.

1. 제공된 USB-C 케이블을 개발 키트의 USB-C 포트와 호스트 컴퓨터의 USB-C 포트에 연결합니다. 컴퓨터에 USB-A 포트만 있으면 USB-C-USB-A 케이블(별도 판매)을 개발 키트와 호스트 컴퓨터에 연결합니다.

1. SSH를 통해 개발 키트에 연결합니다. SSH에 대한 도움이 필요하면 [다음 지침을 따르세요](./how-to-ssh-into-percept-dk.md).

1. SSH 터미널에서 다음 명령을 입력합니다.

    1. 디바이스를 USB 업데이트 모드로 설정합니다.

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. 디바이스를 다시 부팅합니다. 업데이트 설치가 시작됩니다.

        ```bash
        sudo reboot -f
        ```

### <a name="using-the-dip-switch-method"></a>DIP 스위치 방법 사용
디바이스에 SSH할 수 없는 경우 DIP 스위치 방법을 사용합니다.

1. 전원 케이블에 연결된 경우 개발 보드를 분리합니다.
1. 개발 키트와 함께 제공된 육각 렌치를 사용하여 개발 보드의 뒷면에서 4개의 나사를 제거합니다.

    :::image type="content" source="media/how-to-usb-update/dip-switch-01.jpg" alt-text="개발 보드의 뒷면에서 4개의 나사 제거":::

1. LED 방향으로 개발 보드를 부드럽게 밀어줍니다. 열 싱크는 개발 보드의 맨 위에 계속 연결됩니다. 케이블 연결이 끊어지는 것을 방지하기 위해 개발 보드를 2~3cm만 밀어줍니다.

    :::image type="content" source="media/how-to-usb-update/dip-switch-02.jpg" alt-text="보드를 몇 cm 정도 밀어주기":::

1. DIP 스위치는 보드 모서리에서 찾을 수 있습니다. 각각 두 개의 위치(위(1) 또는 아래(0))가 있는 4개의 스위치가 있습니다. 스위치의 기본 위치는 위-아래-아래-위(1001)입니다. 용지 클립 또는 기타 끝이 뾰족한 도구를 사용하여 스위치의 위치를 아래-위-아래-위(0101)로 변경합니다.

    :::image type="content" source="media/how-to-usb-update/dip-switch-03.jpg" alt-text="보드의 아래쪽 모서리에서 스위치 찾기":::

1. 이제 개발 키트가 USB 모드이며 다음 단계를 계속할 수 있습니다. **업데이트가 완료되면 DIP 스위치를 다시 위-아래-아래-위(1001)의 기본 위치로 변경합니다.** 그런 다음, 개발 보드를 원래 위치로 다시 밀고 4개의 나사를 뒷면에 다시 꽂습니다.

## <a name="connect-the-dev-kit-to-the-host-computer-via-a-usb-c-cable"></a>USB-C 케이블을 통해 개발 키트를 호스트 컴퓨터에 연결
이 절차에서는 업데이트를 위해 개발 키트의 단일 USB-C 포트를 사용합니다.  컴퓨터에 USB-C 포트가 있는 경우 개발 키트와 함께 제공된 USB-C 간 케이블을 사용할 수 있습니다.  컴퓨터에 USB-A 포트만 있으면 USB-C와 USB-A 간 케이블(별도 판매)을 사용해야 합니다.

1. 해당 USB-C 케이블을 사용하여 개발 키트를 호스트 컴퓨터에 연결합니다.
1. 이제 호스트 컴퓨터에서 개발 키트를 USB 디바이스로 검색해야 합니다. 업데이트 패키지를 개발 키트로 전송하는 명령을 성공적으로 실행했고 명령 프롬프트에 Waiting for Known USB Device to Appear...가 표시되면 업데이트가 약 10초 이내에 자동으로 시작됩니다.

## <a name="wait-for-the-update-to-complete"></a>업데이트가 완료될 때까지 대기

1. 다른 명령 프롬프트 또는 터미널로 다시 이동합니다. 업데이트가 완료되면 ```Success 1    Failure 0```를 포함하는 메시지가 표시됩니다.

    > [!NOTE]
    > 업데이트 후 디바이스가 공장 설정으로 다시 설정되며 Wi-Fi 연결 및 SSH 로그인이 손실됩니다.

1. 업데이트가 완료되면 개발 키트의 전원을 끕니다. PC에서 USB 케이블을 분리합니다.
1. DIP 스위치 방법을 사용하여 개발 키트를 USB 모드로 전환한 경우 DIP 스위치를 기본 위치로 다시 전환해야 합니다. 그런 다음, 개발 보드를 원래 위치로 다시 밀고 4개의 나사를 뒷면에 다시 꽂습니다.   

## <a name="next-steps"></a>다음 단계

[Azure Percept DK 설치 환경](./quickstart-percept-dk-set-up.md)을 통해 디바이스를 다시 구성합니다.