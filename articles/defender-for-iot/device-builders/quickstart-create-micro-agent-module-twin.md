---
title: '빠른 시작: Defender for IoT 마이크로 에이전트 모듈 쌍 생성(미리 보기)'
description: 이 빠른 시작에서는 새 디바이스의 개별 DefenderIotMicroAgent 모듈 쌍을 만드는 방법에 대해 알아봅니다.
ms.date: 11/09/2021
ms.topic: quickstart
ms.custom: mode-other
ms.openlocfilehash: 5926e7e84df3abd508e420258ead6caaf0f23d0e
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069998"
---
# <a name="quickstart-create-a-defender-for-iot-micro-agent-module-twin-preview"></a>빠른 시작: Defender for IoT 마이크로 에이전트 모듈 쌍 생성(미리 보기)

새 디바이스의 개별  **DefenderIotMicroAgent** 모듈 쌍을 만들 수 있습니다. IoT Hub의 모든 디바이스에 대한 모듈 쌍을 일괄적으로 만들 수도 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

없음

## <a name="device-twins"></a>디바이스 쌍 

Azure에 구축된 IoT 솔루션의 경우 디바이스 쌍은 디바이스 관리와 프로세스 자동화 모두에서 중요한 역할을 수행합니다. 

Defender for IoT는 기존 IoT 디바이스 관리 플랫폼과 완전히 통합하는 기능을 제공합니다. 전체 통합을 사용하면 디바이스의 보안 상태를 관리할 수 있으며 모든 기존 디바이스 제어 기능을 사용할 수 있습니다. 통합은 IoT Hub 쌍 메커니즘을 사용하여 구현됩니다. 

 [IoT Hub에서 디바이스 쌍 이해 및 사용](../../iot-hub/iot-hub-devguide-device-twins.md) 개념에 대해 자세히 알아봅니다. 

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-micro-agent 쌍 

Defender for IoT는 각 디바이스에 대해 Defender-IoT-micro-agent 쌍을 사용합니다. Defender-IoT-micro-agent 쌍에는 솔루션의 각 특정 디바이스에 대한 디바이스 보안과 관련된 모든 정보가 포함되어 있습니다. 디바이스 보안 속성은 보다 안전한 통신을 위해 전용 Defender-IoT-micro-agent 쌍을 통해 구성되며, 업데이트와 리소스가 적게 드는 유지 관리를 지원합니다. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>DefenderIotMicroAgent 모듈 쌍 이해 

Azure에 기본 제공되는 IoT 솔루션에서 디바이스 쌍은 디바이스 관리와 프로세스 자동화 모두에 중요한 역할을 수행합니다.

Defender for IoT는 기존 IoT 디바이스 관리 플랫폼을 완벽하게 통합하는 기능을 제공하여 디바이스 보안 상태를 관리하고 기존 디바이스 제어 기능을 사용할 수 있도록 지원합니다. IoT Hub 쌍 메커니즘을 사용하여 Defender for IoT를 통합할 수 있습니다.  

Azure IoT Hub에 있는 모듈 쌍의 일반적인 개념에 대한 자세한 내용은  [IoT Hub에서 모듈 쌍 이해 및 사용](../../iot-hub/iot-hub-devguide-module-twins.md)을 참조하세요.

Defender for IoT는 모듈 쌍 메커니즘을 사용하고 각 디바이스에 대해 `DefenderIotMicroAgent`라는 Defender-IoT-micro-agent 쌍을 유지 관리합니다. 

모든 Defender for IoT 기능을 최대한 활용하려면 서비스의 모든 디바이스에 대해 Defender-IoT-micro-agent 쌍을 만들고, 구성하고, 사용해야 합니다. 

## <a name="create-defenderiotmicroagent-module-twin"></a>DefenderIotMicroAgent 모듈 쌍 만들기 

각 디바이스의 특정 구성을 포함하도록 각 모듈 쌍을 수동으로 편집하여 **DefenderIotMicroAgent** 모듈 쌍을 만들 수 있습니다. 

디바이스의 새  **DefenderIotMicroAgent** 모듈 쌍을 수동으로 만들려면 다음을 수행합니다. 

1. IoT Hub에서 Defender-IoT-micro-agent 쌍을 만들려는 디바이스를 찾아서 선택합니다. 

1.  **모듈 ID 추가** 를 선택합니다. 

1.  **모듈 ID 이름** 필드에  `DefenderIotMicroAgent`를 입력합니다. 

1.  **저장** 을 선택합니다. 

## <a name="verify-the-creation-of-a-module-twin"></a>모듈 쌍 생성 확인 

특정 디바이스에 대한 Defender-IoT-micro-agent 쌍이 있는지 확인하려면 다음을 수행합니다. 

1. Azure IoT Hub의  **탐색기** 메뉴에서  **IoT 디바이스** 를 선택합니다. 

1.  **디바이스 쿼리 필드** 에서 디바이스 ID를 입력하거나 옵션을 선택하고,  **디바이스 쿼리** 를 선택합니다.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="디바이스 목록을 가져오려면 디바이스 쿼리를 선택합니다.":::

1. 디바이스를 선택하고 **디바이스 세부 정보** 페이지를 엽니다. 

1.  **모듈 ID** 메뉴를 선택하고, 디바이스와 연결된 모듈 ID 목록에  **DefenderIotMicroAgent** 모듈이 있는지 확인합니다.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="탭에서 모듈 ID를 선택합니다.":::

## <a name="next-steps"></a>다음 단계 

> [!div class="nextstepaction"]
> [보안 권장 사항 조사](quickstart-investigate-security-recommendations.md)
