---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aa4ddb316efa3ea4c1bb12d4c6d7b541628bb0e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576767"
---
### <a name="device-registration"></a>디바이스 등록

**Azure Portal**, **Visual Studio Code** 또는 **Azure CLI** 를 사용 하 여 장치를 등록 하는 단계를 수행할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 요소가 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 구독의 무료 또는 표준 [IoT Hub](../articles/iot-hub/iot-hub-create-through-portal.md).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure 구독의 평가판 또는 표준 [IoT 허브](../articles/iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure 구독의 무료 또는 표준 [IoT Hub](../articles/iot-hub/iot-hub-create-using-cli.md).
* 사용자 환경의 [Azure CLI](/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.70 이상이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.

---
