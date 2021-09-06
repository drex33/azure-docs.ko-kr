---
title: 스마트폰을 Azure IoT 디바이스로 사용
description: Azure IoT 플러그 앤 플레이 앱을 사용하여 스마트폰을 IoT 디바이스로 전환하는 방법을 보여 주는 방법 가이드입니다.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: how-to
ms.date: 05/27/2021
ms.author: dobett
ms.openlocfilehash: 0daffdab23d5da069598512f1934cfdc04bf01b3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112306137"
---
# <a name="how-to-turn-your-smartphone-into-an-iot-device"></a>스마트폰을 IoT 디바이스로 전환하는 방법

Azure IoT 솔루션을 사용하면 IoT 디바이스를 클라우드 기반 IoT 서비스에 연결할 수 있습니다. 디바이스는 온도와 습도 같은 원격 분석을 보내고, 다시 부팅 및 배달 간격 변경과 같은 명령에 응답합니다. 또한 디바이스는 디바이스 모델과 운영 체제 같은 속성을 공유하여 서비스와 내부 상태를 동기화할 수 있습니다.

IoT 플러그 앤 플레이 휴대폰 앱을 사용하면 전용 IoT 디바이스를 구성하지 않고도 Azure IoT 기능을 빠르게 탐색할 수 있습니다.

## <a name="azure-iot-plug-and-play-app"></a>Azure IoT 플러그 앤 플레이 앱

시작하기 위해 이 문서에서는 IoT 플러그 앤 플레이 스마트폰 앱을 사용합니다. 앱은 휴대폰의 센서에서 수집된 원격 분석을 보내고, 서비스에서 호출된 명령에 응답하며, 속성 값을 보고합니다.

이 스마트폰 앱을 사용하여 다음을 수행할 수 있습니다.

- 기본 IoT 시나리오 탐색
- 원격으로 휴대폰 관리 및 상호 작용
- 구성 테스트
- 사용자 지정 디바이스 개발의 출발점 역할을 담당

## <a name="install-the-app"></a>앱 설치

[!INCLUDE [iot-phoneapp-install](../../includes/iot-phoneapp-install.md)]

## <a name="app-features"></a>앱 기능

### <a name="connect"></a>연결

IoT Central의 QR 코드를 검사하여 IoT Central 애플리케이션에 연결할 수 있습니다.

자세한 내용은 이 가이드 뒷부분의 [커넥트 앱](#connect-the-app)을 참조하세요.

### <a name="telemetry"></a>원격 분석

앱은 휴대폰의 센서에서 데이터를 수집하여 사용 중인 IoT 서비스에 원격 분석으로 보냅니다. 센서 데이터는 기본적으로 5초마다 집계되지만, 앱 설정 페이지에서 변경할 수 있습니다.

:::image type="content" source="media/iot-phone-app-how-to/telemetry.png" alt-text="스마트폰 앱의 원격 분석 페이지 스크린샷.":::

다음 스크린샷에는 디바이스 원격 분석 중 일부를 표시하는 IoT Central 디바이스 보기가 나와 있습니다.

:::image type="content" source="media/iot-phone-app-how-to/central-telemetry.png" alt-text="IoT Central의 디바이스 원격 분석 스크린샷.":::

### <a name="properties"></a>속성

앱은 디바이스 모델과 제조업체 같은 디바이스 상태를 보고합니다. AzureIoT 솔루션에서 변경 내용 동기화를 수정하고 확인할 수 있는, 편집 가능한 속성도 있습니다.

:::image type="content" source="media/iot-phone-app-how-to/properties.png" alt-text="모바일 디바이스 앱의 속성 페이지를 보여 주는 스크린샷.":::

다음 스크린샷에는 속성이 디바이스로 전송된 후 IoT Central에서 쓰기 가능한 속성이 나와 있습니다.

:::image type="content" source="media/iot-phone-app-how-to/central-writable-property.png" alt-text="IoT Central에서 쓰기 가능한 속성을 보여 주는 스크린샷.":::

### <a name="image-upload"></a>이미지 업로드

IoT Central 및 IoT Hub 모두 디바이스에서 Azure Storage로 파일을 업로드할 수 있습니다. 스마트폰 앱을 사용하면 디바이스에서 이미지를 업로드할 수 있습니다.

디바이스에서 파일 업로드를 지원하도록 서비스를 구성하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드](../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [IoT Central을 사용하여 디바이스에서 클라우드로 파일 업로드](../iot-central/core/howto-configure-file-uploads.md)

:::image type="content" source="media/iot-phone-app-how-to/image-upload.png" alt-text="스마트폰 앱의 이미지 업로드 페이지를 보여 주는 스크린샷.":::

### <a name="logs"></a>로그

스마트폰 앱은 앱 내에서 볼 수 있는 로컬 로그 파일에 이벤트를 작성합니다. 로그 파일을 사용하여 문제를 해결하고 앱이 수행하는 작업을 더 잘 이해할 수 있습니다.

:::image type="content" source="media/iot-phone-app-how-to/logs.png" alt-text="스마트폰 앱의 로그 페이지를 보여 주는 스크린샷.":::

### <a name="settings"></a>설정

앱의 설정 페이지에서 다음을 수행할 수 있습니다.

- 앱을 Azure IoT 솔루션에 연결
- 현재 디바이스 등록 정보 검토
- 저장된 데이터를 지워서 앱 초기화
- 셀 모양 사용자 지정
- 앱에서 IoT 서비스로 원격 분석을 보내는 빈도 설정

:::image type="content" source="media/iot-phone-app-how-to/settings.png" alt-text="스마트폰 앱의 설정 페이지 스크린샷.":::

## <a name="connect-the-app"></a>앱 연결

### <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

<!-- To do: does this need an app template? -->
IoT Central 애플리케이션을 만듭니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](../iot-central/core/howto-create-iot-central-application.md)를 참조하세요.

### <a name="register-a-device"></a>디바이스 등록

휴대폰 앱을 연결하기 전에 디바이스를 IoT Central 애플리케이션에 등록해야 합니다. 디바이스 등록을 만들 때 IoT Central에서 디바이스 연결 정보를 생성합니다.

IoT Central에서 디바이스를 등록하려면 다음을 수행합니다.

1. IoT Central 애플리케이션에 로그인하고 **디바이스** 페이지로 이동합니다.

1. **디바이스 만들기** 를 선택합니다.

1. **새 디바이스 만들기** 페이지에서 **만들기** 를 선택합니다.

    :::image type="content" source="media/iot-phone-app-how-to/iot-central-create-device.png" alt-text="IoT Central에서 디바이스를 만드는 방법을 보여 주는 스크린샷.":::

1. 디바이스 목록에서 디바이스 이름을 클릭한 다음 **연결** 을 선택합니다. **디바이스 연결** 페이지에서는 스마트폰 앱에서 스캔할 QR 코드를 볼 수 있습니다.

    :::image type="content" source="media/iot-phone-app-how-to/device-connection-qr-code.png" alt-text="QR 코드가 있는 디바이스 연결 페이지를 보여 주는 스크린샷.":::

### <a name="connect-the-device"></a>디바이스 연결

IoT Central에서 디바이스를 등록한 후 QR 코드를 검사하여 스마트폰 앱을 연결할 수 있습니다. 앱을 연결하려면 다음을 수행합니다.

1. 스마트폰에서 **IoT PnP** 앱을 엽니다.

1. 시작 페이지에서 **QR 코드 스캔** 을 선택합니다. QR 코드에서 휴대폰 카메라를 가리킵니다. 그런 다음, 연결이 설정되는 동안 몇 초 동안 기다립니다.

1. 앱의 원격 분석 페이지에서 앱이 IoT Central로 보내는 데이터를 볼 수 있습니다. 로그 페이지에서 디바이스 연결 및 여러 초기화 메시지를 볼 수 있습니다.

1. **설정 > 등록** 페이지에서 앱이 IoT Central에 연결하는 데 사용된 디바이스 ID와 ID 범위를 볼 수 있습니다.

디바이스를 IoT Central에 연결하는 방법에 대한 자세한 내용은 [Azure IoT Central에 연결](../iot-central/core/concepts-get-connected.md)을 참조하세요.

### <a name="verify-the-connection"></a>연결 확인

디바이스가 IoT Central 애플리케이션에서 보내는 데이터를 보려면 다음을 수행합니다.

1. IoT Central 애플리케이션에 로그인하고 **디바이스** 페이지로 이동합니다. 디바이스가 **스마트폰** 디바이스 템플릿에 자동으로 할당되었습니다.

    > [!TIP]
    > 디바이스가 **스마트폰** 디바이스 템플릿에 할당된 시기를 확인하려면 웹 브라우저에서 페이지를 새로 고쳐야 할 수도 있습니다.

1. 디바이스 목록에서 디바이스 이름을 클릭한 다음 **개요** 를 선택합니다. **개요** 페이지에는 스마트폰 센서의 원격 분석이 나와 있습니다.

    :::image type="content" source="media/iot-phone-app-how-to/smartphone-overview.png" alt-text="스마트폰 센서의 원격 분석을 보여 주는 IoT Central 디바이스 개요 페이지의 스크린샷.":::

1. 디바이스에서 보낸 속성 값을 보려면 **정보** 페이지를 확인합니다.

1. **명령** 페이지에서 **LightOn** 명령을 실행하여 휴대폰의 손전등을 켭니다.

> [!TIP]
> **원시 데이터** 페이지에는 디바이스에서 들어오는 데이터가 모두 표시됩니다.

## <a name="next-steps"></a>다음 단계

이제 스마트폰 앱을 IoT Central 연결했으므로, 다음 단계로 [IoT Central](../iot-central/core/overview-iot-central.md)에 대해 자세히 알아보는 것이 좋습니다.
