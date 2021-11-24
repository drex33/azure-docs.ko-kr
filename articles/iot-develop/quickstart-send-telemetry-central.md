---
title: 빠른 시작 - 디바이스를 연결하고 Azure IoT Central에 원격 분석 보내기
description: 이 빠른 시작은 디바이스 개발자에게 디바이스를 Azure IoT Central에 안전하게 연결하는 방법을 보여 줍니다. C, C#, Python, Node.js 또는 Java용 Azure IoT 디바이스 SDK를 사용하여 디바이스에서 클라이언트 앱을 실행한 다음 IoT Central에 연결하고 원격 분석을 전송합니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 04/27/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.custom: mode-other
ms.openlocfilehash: 4f6d3c6349ca9cdf37a38329963f017e15672956
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133058653"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central"></a>빠른 시작: 디바이스에서 Azure IoT Central로 원격 분석 보내기

**적용 대상**: [디바이스 애플리케이션 개발자](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-central-c](../../includes/iot-develop-send-telemetry-central-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-central-csharp](../../includes/iot-develop-send-telemetry-central-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-central-java](../../includes/iot-develop-send-telemetry-central-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-central-nodejs](../../includes/iot-develop-send-telemetry-central-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-central-python](../../includes/iot-develop-send-telemetry-central-python.md)]

:::zone-end

## <a name="view-telemetry"></a>원격 분석 보기
디바이스가 IoT Central에 연결되면 원격 분석 전송을 시작합니다. IoT Central에서 연결된 디바이스에 대한 원격 분석 및 기타 세부 정보를 볼 수 있습니다. 

IoT Central에서 **디바이스** 를 선택하고 디바이스 이름을 클릭한 다음 **개요** 탭을 선택합니다. 이 보기는 두 개의 자동 온도 조절기 디바이스의 온도 그래프를 표시합니다.

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-overview.png" alt-text="IoT Central 디바이스 원격 분석 개요":::

**원시 데이터** 탭을 선택합니다. 이 보기는 자동 온도 조절기 읽기가 전송될 때마다 원격 분석을 표시합니다.

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-raw.png" alt-text="IoT Central 디바이스 원격 분석 원시 출력":::

이제 디바이스가 안전하게 연결되어 원격 분석을 Azure IoT에 보내고 있습니다.
    
## <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작에서 만든 IoT Central 리소스가 더 이상 필요하지 않은 경우 이를 삭제할 수 있습니다. 필요에 따라 이 가이드의 설명서를 계속 따르려는 경우 만든 애플리케이션을 유지하고 다른 샘플에 다시 사용할 수 있습니다.

Azure IoT Central 샘플 애플리케이션과 모든 해당 디바이스 및 리소스를 제거하려면 다음을 수행합니다.
1. **관리** > **애플리케이션** 을 차례로 선택합니다.
1. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 디바이스를 클라우드에 안전하게 연결하고 디바이스-클라우드 원격 분석을 보내는 데 사용할 수 있는 기본 Azure IoT 애플리케이션 워크플로를 알아보았습니다. Azure IoT Central을 사용하여 애플리케이션과 디바이스 인스턴스를 만들었습니다. 그런 다음 Azure IoT 디바이스 SDK를 사용하여 온도 컨트롤러를 만들고 IoT Central에 연결하고 원격 분석을 보냅니다. 또한 IoT Central을 사용하여 원격 분석을 모니터링했습니다.

다음 단계로, 다음 문서를 통해 Azure IoT를 사용하여 디바이스 솔루션을 빌드하는 방법에 대해 자세히 알아봅니다. 

> [!div class="nextstepaction"]
> [Azure IoT Hub에 원격 분석 데이터 보내기](./quickstart-send-telemetry-iot-hub.md)
> [!div class="nextstepaction"]
> [IoT Central 애플리케이션 만들기](../iot-central/core/quick-deploy-iot-central.md)
