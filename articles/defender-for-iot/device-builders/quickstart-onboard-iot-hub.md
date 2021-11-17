---
title: '빠른 시작: 에이전트 기반 솔루션에 대한 Defender for IoT 등록'
description: 이 빠른 시작에서는 Azure IoT Hub에서 Defender for IoT 보안 서비스를 등록하고 활성화하는 방법에 대해 알아봅니다.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: f22b319a409b481fdeb26a68cdd4b58809b8d015
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281857"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>빠른 시작: 에이전트 기반 솔루션에 대한 Defender for IoT 등록

이 문서에서는 기존 IoT Hub에서 Defender for IoT 서비스를 사용하도록 설정하는 방법을 설명합니다. 현재 IoT Hub가 없는 경우 시작하려면 [Azure Portal을 사용하여 IoT 허브 만들기](../../iot-hub/iot-hub-create-through-portal.md)를 참조하세요.

Defender for IoT의 IoT Hub를 통해 IoT 보안을 관리할 수 있습니다. IoT Hub에 있는 관리 포털에서 다음을 수행할 수 있습니다. 

- IoT Hub 보안을 관리합니다.

- IoT Hub 원격 분석에 기반하여 에이전트를 설치하지 않고 IoT 디바이스의 보안 기본 관리 

- 마이크로 에이전트 기반의 IoT 디바이스의 보안에 대한 고급 관리

> [!NOTE]
> Defender for IoT는 현재 표준 계층 IoT Hub만 지원합니다.

## <a name="prerequisites"></a>필수 조건

없음

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>IoT Hub에 Defender for IoT 등록

모든 새 IoT 허브의 경우 Defender for IoT는 기본적으로 **On** 으로 설정됩니다. IoT Hub 만들기 프로세스 중에 Defender for IoT가 **On** 으로 전환되었는지 확인할 수 있습니다.

설정/해제가 **On** 으로 설정되었는지 확인하려면 다음을 수행합니다.

1. Azure Portal로 이동합니다.

1. Azure 서비스 목록에서 **IoT Hub** 를 선택합니다.

1. **만들기** 를 선택합니다.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="상단 도구 모음에서 만들기 단추를 선택합니다." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. **관리** 탭을 선택하고, **Defender for IoT** 설정/해제가 **On** 으로 설정되었는지 확인합니다.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Defender for IoT 설정/해제가 켜짐으로 설정되었는지 확인합니다.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>기존 IoT Hub에 Defender for IoT 온보딩

Defender for IoT를 기존 IoT Hub에 등록하면 디바이스 ID 관리, 디바이스에서 클라우드로, 클라우드에서 디바이스로의 통신 패턴을 모니터링할 수 있습니다.

기존 IoT Hub에 Defender for IoT를 등록하려면 다음을 수행합니다.

1. IoT Hub로 이동합니다. 

1. 등록할 IoT Hub를 선택합니다.

1. **보안** 섹션에서 옵션을 선택합니다.

1.  **IoT 솔루션 보안** 을 클릭하고 온보딩 양식을 완료합니다. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="IoT 솔루션 보안 단추를 선택하여 솔루션을 보호합니다.":::

**IoT 솔루션 보안** 단추는 IoT Hub가 아직 등록되지 않았거나 등록 중에 Defender for IoT 토글을 **해제** 한 경우에만 나타납니다.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="등록 중에 토글이 해제로 설정된 경우":::

## <a name="next-steps"></a>다음 단계

솔루션을 구성하려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Defender for Cloud IoT 마이크로 에이전트 모듈 쌍 만들기(미리 보기)](quickstart-create-micro-agent-module-twin.md)
