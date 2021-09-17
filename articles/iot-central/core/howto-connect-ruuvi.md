---
title: Azure IoT Central에서 RuuviTag 연결 | Microsoft Docs
description: RuuviTag 환경 센서를 IoT Central 애플리케이션에 연결하는 방법을 알아봅니다.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 08/20/2021
ms.openlocfilehash: b4d169a46f499e83d0abf5d3145d67a3f6fdf03b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439220"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 RuuviTag 센서 연결

이 문서에서는 RuuviTag 센서를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

Ruuvi 태그란?

RuuviTag는 비즈니스 고객, 개발자, 제조업체, 학생 및 초보 개발자의 요구를 충족하도록 설계된 고급 오픈 소스 센서 비콘 플랫폼입니다. 디바이스는 바로 작동하도록 설정되어 있으며 필요한 곳에 배포할 준비가 되어 있습니다. 환경 센서와 가속도계가 내장된 Bluetooth LE 비콘입니다.

RuuviTag는 BLE(Bluetooth Low Energy)를 통해 통신하며 Azure IoT Central과 통신하려면 게이트웨이 디바이스가 필요합니다. RuuviTag가 IoT Central에 연결할 수 있도록 설정된 Rigado Cascade 500과 같은 게이트웨이 디바이스가 있는지 확인합니다.

Rigado Cascade 500 게이트웨이 디바이스를 설정하려면 [여기의 지침](./howto-connect-rigado-cascade-500.md)을 따르세요.

## <a name="prerequisites"></a>필수 구성 요소

RuuviTag 센서를 연결하려면 다음 리소스가 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- RuuviTag 센서 1개. 자세한 내용을 보려면 [RuuviTag](https://ruuvi.com/)를 방문하세요.

- Rigado Cascade 500 디바이스 또는 다른 BLE 게이트웨이 1개. 자세한 내용을 보려면 [Rigado](https://www.rigado.com/)를 방문하세요.


## <a name="add-a-ruuvitag-device-template"></a>RuuviTag 디바이스 템플릿 추가

RuuviTag 센서를 Azure IoT Central 애플리케이션 인스턴스에 온보딩하려면 애플리케이션 내에서 해당 디바이스 템플릿을 구성해야 합니다.

RuuviTag 디바이스 템플릿을 추가하려면 다음을 수행합니다.

1. 왼쪽 창에서 ***디바이스 템플릿** _ 탭으로 이동하고 _*+ 새로 만들기**: ![새 디바이스 템플릿 만들기](./media/howto-connect-ruuvi/device-template-new.png)를 선택합니다. 페이지에 ***사용자 지정 템플릿 만들기**_ 또는 _ *_미리 구성된 디바이스 템플릿 사용_* 옵션이 있습니다.*
1. 아래와 같이 미리 구성 된 장치 템플릿 목록에서 RuuviTag Multisensor 장치 템플릿을 선택 합니다.  ![ Select RuuviTag device template](./media/howto-connect-ruuvi/device-template-pre-configured.png)
1. ***다음: 사용자 지정*** 을 선택하여 다음 단계로 계속 진행합니다.
1. 다음 화면에서 ***만들기*** 를 선택하여 C500 디바이스 템플릿을 IoT Central 애플리케이션에 온보딩합니다.

## <a name="connect-a-ruuvitag-sensor"></a>RuuviTag 센서 연결

앞에서 설명한 대로 RuuviTag를 IoT Central 애플리케이션과 연결하려면 게이트웨이 디바이스를 설정해야 합니다. 아래 단계에서는 Rigado Cascade 500 게이트웨이 디바이스를 설정했다고 가정합니다.  

1. Rigado Cascade 500 디바이스를 켜고 이더넷 또는 무선을 통해 네트워크에 연결합니다.
1. RuuviTag의 덮개를 열고 플라스틱 탭을 당겨 배터리와의 연결을 고정합니다.
1. IoT Central 애플리케이션에 이미 구성된 Rigado Cascade 500 게이트웨이 가까이에 RuuviTag를 놓습니다.
1. 몇 초만에 IoT Central 내의 디바이스 목록에 RuuviTag가 표시됩니다.  
    ![RuuviTag 디바이스 목록](./media/howto-connect-ruuvi/ruuvi-device-list.png)

이제 IoT Central 애플리케이션 내에서 이 RuuviTag를 사용할 수 있습니다.  

## <a name="create-a-simulated-ruuvitag"></a>시뮬레이션된 RuuviTag 만들기

물리적 RuuviTag 디바이스가 없는 경우 Azure IoT Central 애플리케이션 내에서 테스트에 사용할 시뮬레이션된 RuuviTag 센서를 만들 수 있습니다.

시뮬레이션된 RuuviTag를 만들려면 다음을 수행합니다.

1. **디바이스 > RuuviTag** 를 선택합니다.
1. **+새로 만들기** 를 선택합니다.
1. 고유한 **디바이스 ID** 와 친숙한 **디바이스 이름** 을 입력합니다.  
1. **시뮬레이션된** 설정을 사용하도록 설정합니다.
1. **만들기** 를 선택합니다.  

## <a name="next-steps"></a>다음 단계

몇 가지 제안되는 다음 단계는 다음과 같습니다.

- [Azure IoT Central에서 디바이스 연결](./concepts-get-connected.md)에 대해 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
