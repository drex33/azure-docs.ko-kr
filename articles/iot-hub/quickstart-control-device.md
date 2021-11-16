---
title: 빠른 시작 - Azure IoT Hub에서 디바이스 제어 | Microsoft Docs
description: 이 빠른 시작에서 두 개의 샘플 애플리케이션을 실행합니다. 그 중 한 애플리케이션은 허브에 연결된 디바이스를 원격으로 제어할 수 있는 서비스 애플리케이션입니다. 또 다른 애플리케이션은 원격으로 제어할 수 있는 허브에 연결된 디바이스를 시뮬레이션 합니다.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 07/26/2021
zone_pivot_groups: iot-hub-set1
ms.openlocfilehash: cae4b2c1b23acc56468d3c7ebe30cf8316842003
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550252"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub"></a>빠른 시작: IoT 허브에 연결된 디바이스 제어

이 빠른 시작에서는 직접 메서드를 사용하여 IoT 허브에 연결된 시뮬레이션된 디바이스를 제어합니다. IoT Hub는 클라우드에서 IoT 디바이스를 관리하고, 스토리지 또는 처리를 위해 클라우드로 다량의 디바이스 원격 분석 데이터를 수집할 수 있도록 하는 Azure 서비스입니다. 직접 메서드를 사용하여 IoT 허브에 연결된 디바이스의 동작을 원격으로 변경할 수 있습니다.

:::zone pivot="programming-language-csharp"

[!INCLUDE [quickstart-control-device-dotnet](../../includes/quickstart-control-device-dotnet.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [quickstart-control-device-java](../../includes/quickstart-control-device-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [quickstart-control-device-node](../../includes/quickstart-control-device-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [quickstart-control-device-python](../../includes/quickstart-control-device-python.md)]

:::zone-end

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 서비스 애플리케이션에서 디바이스에 직접 메서드를 호출하고, 시뮬레이션된 디바이스 애플리케이션에서 직접 메서드 호출에 응답했습니다.

디바이스-클라우드 메시지를 클라우드의 다른 대상으로 라우팅하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 처리를 위해 다른 엔드포인트로 원격 분석 라우팅](tutorial-routing.md)