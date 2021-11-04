---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bc968f7763131c4c12e7d523619029698e4ac09
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131579346"
---
## <a name="create-a-device-provisioning-service-enrollment"></a>디바이스 프로비저닝 서비스 등록 만들기

TPM의 프로비전 정보를 사용하여 디바이스 프로비저닝 서비스에서 개별 등록을 만듭니다.

디바이스 프로비저닝 서비스에서 등록을 만들 때 초기 디바이스 **쌍 상태** 를 선언할 수 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형과 같이 솔루션에서 사용되는 메트릭을 기준으로 디바이스를 그룹화하도록 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](../articles/iot-edge/how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal 위한 것이지만 Azure CLI 사용하여 개별 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)IoT Hub 디바이스 프로비저닝 서비스의 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. **개별 등록 추가를** 선택한 다음, 다음 단계를 완료하여 등록을 구성합니다.

   1. **메커니즘** 의 경우 **TPM** 을 선택합니다.

   1. VM 또는 물리적 디바이스에서 복사한 **인증 키** 및 **등록 ID를** 제공합니다.

   1. 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. **True를** 선택하여 VM 또는 물리적 디바이스가 IoT Edge 디바이스임을 선언합니다.

   1. 디바이스를 연결하려는 연결된 IoT Hub를 선택하거나 **새 IoT Hub 연결을** 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   1. 원하는 경우 초기 **디바이스 쌍 상태에** 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](../articles/iot-edge/how-to-deploy-at-scale.md)를 참조하세요.

   1. **저장** 을 선택합니다.

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.
