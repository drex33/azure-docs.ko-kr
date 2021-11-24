---
title: 빠른 시작 - Azure IoT Central 애플리케이션 만들기 및 사용 | Microsoft Docs
description: 빠른 시작 - 새 Azure IoT Central 애플리케이션을 만들고 첫 번째 디바이스를 연결합니다. 이 빠른 시작에서는 Google Play 또는 Apple App Store의 스마트폰 앱을 IoT 디바이스로 사용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mode-other
ms.openlocfilehash: d81500a622ceb72422f1536af9e8b0e0ecf334bf
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133058770"
---
# <a name="quickstart---create-an-azure-iot-central-application-and-use-your-smartphone-to-send-telemetry"></a>빠른 시작 - Azure IoT Central 애플리케이션 만들기 및 스마트폰을 사용하여 원격 분석 전송

이 빠른 시작에서는 Azure IoT Central 애플리케이션을 만들고 첫 번째 디바이스를 연결하는 방법을 보여 줍니다. 빠르게 시작하려면 스마트폰에 디바이스 역할을 하는 앱을 설치합니다. 앱은 원격 분석을 보내고, 속성을 보고하고, 명령에 응답합니다.

:::image type="content" source="media/quick-deploy-iot-central/overview.png" alt-text="스마트폰 앱을 IoT Central에 연결하는 빠른 시작 시나리오 개요입니다." border="false":::

## <a name="prerequisites"></a>필수 구성 요소

활성화된 Azure 구독. Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!TIP]
> Azure 구독에서 **기여자** 이상의 액세스 권한이 있어야 합니다. 구독을 직접 만든 경우 자동으로 충분한 액세스 권한이 있는 관리자가 됩니다. 자세한 내용은 [Azure 역할 기반 액세스 제어란?](../../role-based-access-control/overview.md)을 참조하세요.

공식 앱 스토어 중 하나에서 무료 앱을 설치할 수 있는 Android 또는 iOS 휴대폰.

## <a name="create-an-application"></a>애플리케이션 만들기

[Azure IoT Central 빌드](https://aka.ms/iotcentral) 사이트로 이동합니다. 그런 다음, Azure 구독과 연결된 Microsoft 개인, 직장 또는 학교 계정으로 로그인합니다.

IoT Central은 시작하는 데 도움이 되는 다양한 업계 중심 애플리케이션 템플릿을 제공합니다. 이 빠른 시작에서는 **사용자 지정 애플리케이션** 템플릿을 사용하여 애플리케이션을 처음부터 만듭니다.

1. **빌드** 페이지로 이동하고 **사용자 지정** 앱 타일에서 **앱 만들기** 를 선택합니다.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="IoT 애플리케이션 빌드 페이지":::

1. **새 애플리케이션** 페이지의 **애플리케이션 템플릿** 에서 **사용자 지정 애플리케이션** 이 선택되어 있는지 확인합니다.

1. Azure IoT Central은 선택한 애플리케이션 템플릿을 기반으로 **애플리케이션 이름** 을 자동으로 제안합니다. *Contoso 빠른 시작 앱* 과 같이 사용자 고유의 애플리케이션 이름을 입력합니다.

1. 또한 Azure IoT Central은 애플리케이션 이름을 기반으로 고유한 **URL** 접두사를 자동으로 생성합니다. 이 URL을 사용하여 애플리케이션에 액세스합니다. 원하는 경우 이 URL 접두사를 더욱 기억하기 쉬운 것으로 자유롭게 변경할 수 있습니다. 이 URL은 고유해야 합니다.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central 애플리케이션 만들기 페이지":::

1. 이 빠른 시작에서는 가격 책정 계획을 **표준 2** 로 설정된 상태로 둡니다.

1. **Azure 구독** 드롭다운에서 구독을 선택합니다.

1. **위치** 드롭다운에서 가장 가까운 위치를 선택합니다.

1. 사용 약관을 검토하고, 페이지 맨 아래에서 **만들기** 를 선택합니다. 몇 분 후 IoT Central 애플리케이션이 사용할 준비가 됩니다.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central 애플리케이션":::

## <a name="register-a-device"></a>디바이스 등록

디바이스를 IoT Central 애플리케이션에 연결하려면 몇 가지 연결 정보가 필요합니다. 이 연결 정보를 쉽게 얻을 수 있는 방법은 디바이스를 등록하는 것입니다.

디바이스를 등록하려면 다음을 수행합니다.

1. IoT Central에서 **디바이스** 페이지로 이동하여 **디바이스 만들기** 를 선택합니다.

    :::image type="content" source="media/quick-deploy-iot-central/create-device.png" alt-text="IoT Central에서 디바이스 만들기를 보여 주는 스크린샷.":::

1. **새 디바이스 만들기** 페이지에서 기본값을 수락한 다음, **만들기** 를 선택합니다.

1. 디바이스 목록에서 다음과 같이 디바이스 이름을 클릭합니다.

    :::image type="content" source="media/quick-deploy-iot-central/device-name.png" alt-text="선택할 수 있는 강조 표시된 디바이스 이름을 보여 주는 스크린샷.":::

1. 디바이스 페이지에서 **연결** 을 선택한 다음, **QR 코드** 를 선택합니다.

    :::image type="content" source="media/quick-deploy-iot-central/device-registration.png" alt-text="휴대폰 앱을 연결하는 데 사용할 수 있는 QR 코드를 보여 주는 스크린샷.":::

이 페이지를 열린 상태로 유지합니다. 다음 섹션에서는 휴대폰 앱을 사용하여 이 QR 코드를 스캔하여 IoT Central에 연결합니다.

## <a name="connect-your-device"></a>디바이스 연결

빠르게 시작하기 위해 이 문서에서는 **IoT 플러그 앤 플레이** 스마트폰 앱을 IoT 디바이스로 사용합니다. 앱은 휴대폰의 센서에서 수집된 원격 분석을 보내고, IoT Central에서 호출된 명령에 응답하고, 속성 값을 IoT Central에 보고합니다.

[!INCLUDE [iot-phoneapp-install](../../../includes/iot-phoneapp-install.md)]

**IoT 플러그 앤 플레이** 앱을 Iot Central 애플리케이션에 연결하려면 다음을 수행합니다.

1. 스마트폰에서 **IoT PnP** 앱을 엽니다.

1. 시작 페이지에서 **QR 코드 스캔** 을 선택합니다. QR 코드에서 휴대폰 카메라를 가리킵니다. 그런 다음, 연결이 설정되는 동안 몇 초 동안 기다립니다.

1. 앱의 원격 분석 페이지에서 앱이 IoT Central로 보내는 데이터를 볼 수 있습니다. 로그 페이지에서 디바이스 연결 및 여러 초기화 메시지를 볼 수 있습니다.

IoT Central의 스마트폰 앱에서 원격 분석을 보려면 다음을 수행합니다.

1. IoT Central에서 **디바이스** 페이지로 이동합니다.

1. 디바이스 목록에서 디바이스 이름을 클릭한 다음, **개요** 를 선택합니다.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-telemetry.png" alt-text="원격 분석 플롯이 있는 개요 페이지의 스크린샷.":::

> [!TIP]
> 스마트폰 앱은 화면이 켜진 경우에만 데이터를 보냅니다.
## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 원격 분석을 보내는 IoT Central 애플리케이션과 연결된 디바이스를 만들었습니다. 이 빠른 시작에서는 스마트폰 앱을 IoT Central에 연결하는 IoT 디바이스로 사용했습니다. IoT Central에 대해 계속 학습하기 위해 제안된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [IoT Central 애플리케이션에 규칙 추가](./quick-configure-rules.md)
