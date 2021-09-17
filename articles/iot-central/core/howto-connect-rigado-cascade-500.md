---
title: Azure IoT Central에서 Rigado Cascade 500 연결 | Microsoft Docs
description: Rigado Cascade 500 게이트웨이 디바이스를 IoT Central 애플리케이션에 연결하는 방법을 알아봅니다.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 08/18/2021
ms.openlocfilehash: 1a255336d958ea4d4d027d533c80bfb493d88f8b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435497"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Rigado Cascade 500 게이트웨이 디바이스를 Azure IoT Central 애플리케이션에 연결

이 문서에서는 Rigado Cascade 500 게이트웨이 디바이스를 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="what-is-cascade-500"></a>Cascade 500이란?

Cascade 500 IoT 게이트웨이는 Edge-as-a-Service 솔루션에 포함된 Rigado의 하드웨어 제품입니다. 상용 IoT 프로젝트 및 제품 팀에 유연한 에지 컴퓨팅 능력, 강력한 컨테이너화된 애플리케이션 환경 및 다양한 무선 디바이스 연결 옵션(Bluetooth 5, LTE 및 Wi-Fi 등)을 제공합니다.

Cascade 500은 Azure IoT 플러그 앤 플레이 인증을 받았으며 엔드투엔드 소루션에 디바이스를 쉽게 온보딩할 수 있게 해줍니다. Cascade 게이트웨이를 사용하면 게이트웨이 디바이스와 인접한 거리에 있는 다양한 조건 모니터링 센서에 무선으로 연결할 수 있습니다. 이러한 센서는 게이트웨이 디바이스를 통해 IoT Central에 온보딩될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드의 단계를 완료하려면 다음이 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- Rigado Cascade 500 디바이스. 자세한 내용을 보려면 [Rigado](https://www.rigado.com/)를 방문하세요.

## <a name="add-a-device-template"></a>디바이스 템플릿 추가하기

Cascade 500 게이트웨이 디바이스를 Azure IoT Central 애플리케이션 인스턴스에 온보딩하려면 애플리케이션 내에서 해당 디바이스 템플릿을 구성해야 합니다.

Cascade 500 디바이스 템플릿을 추가하려면 다음과 같이 하십시오. 

1. 왼쪽 창에서 ***디바이스 템플릿** _ 탭으로 이동하고 _*+ 새로 만들기**: ![새 디바이스 템플릿 만들기](./media/howto-connect-rigado-cascade-500/device-template-new.png)를 선택합니다.
1. 페이지에 ***사용자 지정 템플릿 만들기** _ 또는 _ *_미리 구성된 디바이스 템플릿 사용_** 옵션이 제공됩니다.
1. 아래 표시된 것처럼 미리 구성된 디바이스 템플릿 목록에서 C500 디바이스 템플릿을 선택합니다. ![C500 디바이스 템플릿 선택](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. ***다음: 사용자 지정*** 을 선택하여 다음 단계로 계속 진행합니다. 
1. 다음 화면에서 ***만들기*** 를 선택하여 C500 디바이스 템플릿을 IoT Central 애플리케이션에 온보딩합니다.

## <a name="retrieve-application-connection-details"></a>애플리케이션 연결 세부 정보 검색

이제 Cascade 500 디바이스를 연결하기 위해 Azure IoT Central 애플리케이션에 대해 **범위 ID** 및 **기본 키** 를 검색해야 합니다. 

1. 왼쪽 창에서 **관리** 로 이동하여 **디바이스 연결** 을 클릭합니다. 
2. IoT Central 애플리케이션의 **범위 ID** 를 기록해 둡니다.
![앱 범위 ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. 이제 **키 보기** 를 클릭하고 **기본 키**
![기본 키](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)를 기록해 둡니다.  

## <a name="contact-rigado-to-connect-the-gateway"></a>게이트웨이 연결을 위해 Rigado에 문의 

Cascade 500 디바이스를 IoT Central 애플리케이션에 연결하려면 Rigado에 문의하고 위 단계에서 얻은 애플리케이션 연결 세부 정보를 제공해야 합니다. 

디바이스가 인터넷에 연결된 다음, Rigado가 보안 채널을 통해 구성 업데이트를 Cascade 500 게이트웨이 디바이스로 전송할 수 있습니다. 

이 업데이트는 IoT Central 연결 세부정보를 Cascade 500 디바이스에 적용하고 디바이스 목록에 표시됩니다. 

![디바이스 목록](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

이제 IoT Central 애플리케이션에서 C500 디바이스를 사용할 준비가 되었습니다!

## <a name="next-steps"></a>다음 단계

몇 가지 제안되는 다음 단계는 다음과 같습니다.

- [Azure IoT Central에서 디바이스 연결](./concepts-get-connected.md)에 대해 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
