---
title: Azure Percept DK에 직렬 연결
description: USB-TTL 직렬 케이블을 사용하여 Azure Percept DK에 직렬 연결을 설정하는 방법
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 47c3c4d5a696087164ef4992ae75e5433de86722
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006319"
---
# <a name="connect-to-azure-percept-dk-over-serial"></a>Azure Percept DK에 직렬 연결

아래 단계를 따라 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)를 통해 Azure Percept DK에 대한 직렬 연결을 설정합니다.

> [!WARNING]
> **프라이빗 미리 보기 개발자 키트가 있는 경우** 극단적인 실패 사례(예: 디바이스가 손상됨)를 제외하고는 디바이스 키트를 직렬로 연결하지 **않는** 것이 좋습니다. 직렬을 통해 연결하려면 프라이빗 미리 보기 개발자 키트를 디스어셈블하여 GPIO 핀에 액세스해야 합니다. 캐리어 보드 인클로저를 분리하는 것은 매우 어려운 작업이며 Wi-Fi 안테나 케이블이 끊어질 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- 호스트 PC
- Azure Percept DK
- [USB-TTL 직렬 케이블](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB-TTL 직렬 케이블.":::

## <a name="start-the-serial-connection"></a>직렬 연결 시작

1. 아래와 같이 [USB-TTL 직렬 케이블](https://www.adafruit.com/product/954)을 마더보드의 GPIO 핀 세 개에 연결합니다.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/apdk-serial-pins.jpg" alt-text="캐리어 보드 직렬 핀 연결.":::

1. 개발 키트를 켜고 직렬 케이블의 USB 쪽을 PC에 연결합니다.

1. Windows에서 **시작** -> **Windows 업데이트 설정** -> **옵션 업데이트 보기** -> **드라이버 업데이트** 로 이동합니다. 목록에서 직렬-USB 업데이트를 찾아 옆에 있는 확인란을 선택하고 **다운로드 및 설치** 를 선택합니다.  

1. 이어서, Windows 디바이스 관리자(**시작** -> **디바이스 관리자**)를 엽니다. **포트** 로 이동하고 **USB에서 UART** 를 선택하여 **속성** 을 엽니다. 디바이스가 연결되어 있는 COM 포트를 확인합니다.

1. **포트 설정** 탭을 선택합니다. **초당 비트 수** 가 115200으로 설정되어 있는지 확인합니다.

1. PuTTY를 엽니다. 다음 내용을 입력하고 **열기** 를 선택해 직렬로 devkit을 연결합니다.

    1. 직렬 회선: COM[포트 번호]
    1. 속도: 115200
    1. 연결 형식: 직렬

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="직렬 매개 변수가 선택된 PuTTY 세션 창.":::
