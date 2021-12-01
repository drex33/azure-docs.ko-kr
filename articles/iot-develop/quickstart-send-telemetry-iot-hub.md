---
title: Azure IoT Hub에 디바이스 원격 분석 보내기 빠른 시작
description: 이 빠른 시작에서는 디바이스 개발자에게 디바이스를 Azure IoT Hub에 안전하게 연결하는 방법을 보여 줍니다. C, C#, Python, Node.js 또는 Java용 Azure IoT 디바이스 SDK를 사용하여 Windows, Linux 또는 Raspberry Pi(Raspbian)용 디바이스 클라이언트를 빌드한 다음 원격 분석을 연결하고 보냅니다.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 08/03/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.custom: mode-other
ms.openlocfilehash: dcb1e321f56bfd77c76874553b31c85dda02b0e8
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133358069"
---
# <a name="quickstart-send-telemetry-from-an-iot-plug-and-play-device-to-azure-iot-hub"></a>빠른 시작: IoT 플러그 앤 플레이 디바이스에서 Azure IoT Hub로 원격 분석 전송

**적용 대상**: [디바이스 애플리케이션 개발자](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-c](../../includes/iot-develop-send-telemetry-iot-hub-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-csharp](../../includes/iot-develop-send-telemetry-iot-hub-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-java](../../includes/iot-develop-send-telemetry-iot-hub-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-node](../../includes/iot-develop-send-telemetry-iot-hub-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-python](../../includes/iot-develop-send-telemetry-iot-hub-python.md)]

:::zone-end
    
## <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작에서 만든 Azure 리소스가 더 이상 필요하지 않은 경우 Azure CLI를 사용하여 삭제할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다.

리소스 그룹을 이름으로 삭제하려면:
1. [az group delete](/cli/azure/group#az_group_delete) 명령을 실행합니다. 이 명령을 실행하면 만든 리소스 그룹, IoT Hub 및 디바이스 등록이 제거됩니다.

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```
1. [az group list](/cli/azure/group#az_group_list) 명령을 실행하여 리소스 그룹을 삭제했는지 확인합니다.  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 디바이스를 클라우드에 안전하게 연결하고 디바이스-클라우드 원격 분석을 보내는 데 사용할 수 있는 기본 Azure IoT 애플리케이션 워크플로를 알아보았습니다. Azure CLI를 사용하여 Azure IoT Hub 및 디바이스 인스턴스를 작성했습니다. 그런 다음 Azure IoT 디바이스 SDK를 사용하여 온도 컨트롤러를 만들고 허브에 연결하고 원격 분석을 보냅니다. 또한 Azure CLI를 사용하여 원격 분석을 모니터링했습니다.

다음 단계로, 다음 문서를 통해 Azure IoT를 사용하여 디바이스 솔루션을 빌드하는 방법에 대해 자세히 알아봅니다. 

> [!div class="nextstepaction"]
> [IoT 허브에 연결된 디바이스 제어](../iot-hub/quickstart-control-device.md)
> [!div class="nextstepaction"]
> [IoT Central로 원격 분석 전송](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [IoT Central에 MXCHIP AZ3166 devkit 연결](quickstart-devkit-mxchip-az3166.md)
