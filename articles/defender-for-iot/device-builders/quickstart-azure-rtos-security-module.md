---
title: '빠른 시작: Azure RTOS용 Defender-IoT-micro-agent 구성 및 사용'
description: 이 빠른 시작에서는 Azure IoT Hub에서 Azure RTOS용 Defender-IoT-micro-agent 서비스를 온보딩하고 활성화하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: mode-other
ms.openlocfilehash: 0b75746159fcd49540ce4011f93f51aba25b1738
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133070037"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos"></a>빠른 시작: Azure RTOS용 Defender-IoT-micro-agent

이 문서에서는 시작하기 전에 필수 구성 요소에 대해 설명하고 IoT Hub에서 Azure RTOS용 Defender-IoT-micro-agent 서비스를 활성화하는 방법을 설명합니다. 현재 IoT Hub가 없는 경우 [Azure Portal을 사용하여 IoT 허브 만들기](../../iot-hub/iot-hub-create-through-portal.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소 

### <a name="supported-devices"></a>지원되는 디바이스

- ST STM32F746G 검색 키트
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

[Azure RTOS GitHub용 Defender-IoT-micro-agent 리소스](https://github.com/azure-rtos/azure-iot-preview/releases)에서 선택한 특정 보드 및 도구(IAR, 세미 IDE 또는 PC)에 대한 .zip 파일 중 하나를 다운로드, 컴파일 및 실행합니다.

### <a name="azure-resources"></a>Azure 리소스

시작하기 위한 다음 단계는 Azure 리소스를 준비하는 것입니다. IoT Hub가 필요하며 Log Analytics 작업 영역을 제안합니다. IoT Hub의 경우 디바이스에 연결하기 위한 IoT Hub 연결 문자열이 필요합니다. 
  
### <a name="iot-hub-connection"></a>IoT Hub 연결

시작하려면 IoT Hub 연결이 필요합니다. 

1. Azure Portal에서 **IoT Hub** 를 엽니다.

1. **IoT 디바이스** 로 이동합니다.

1. **만들기** 를 선택합니다.

1. IoT 연결 문자열을 [구성 파일](how-to-azure-rtos-security-module.md)에 복사합니다.

연결 자격 증명은 사용자 애플리케이션 구성 **HOST_NAME**, **DEVICE_ID** 및 **DEVICE_SYMMETRIC_KEY** 에서 가져옵니다.

Azure RTOS용 Defender-IoT-micro-agent는 **MQTT** 프로토콜을 기반으로 하는 Azure IoT 미들웨어 연결을 사용합니다.

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 솔루션 구성 및 사용자 지정을 완료합니다.

> [!div class="nextstepaction"]
> [Azure RTOS용 Defender-IoT-micro-agent 구성 및 사용자 지정(미리 보기)](how-to-azure-rtos-security-module.md)
