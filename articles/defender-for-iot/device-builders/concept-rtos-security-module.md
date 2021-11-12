---
title: Azure RTOS용 Defender-IoT-micro-agent에 대한 기본 개념 설명
description: Azure RTOS 개념 및 워크플로용 Defender-IoT-micro-agent에 대한 기본 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 084c7130c9fe033fb96fcb0a2e3c2820ef2c77e5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278952"
---
# <a name="defender-iot-micro-agent-for-azure-rtos"></a>Azure RTOS용 Defender-IoT-micro-agent

이 문서를 사용하면 기능 및 이점과 관련 구성 및 참조 리소스에 대한 링크를 비롯하여 Azure RTOS용 Defender-IoT-micro-agent를 보다 잘 이해할 수 있습니다. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agent

Azure RTOS용 Defender-IoT-micro-agent는 NetX Duo 제품에 속하는 Azure RTOS 디바이스를 위한 종합적인 보안 솔루션을 제공합니다. NetX Duo 제품 내에서 Azure RTOS는 Azure IoT Defender-IoT-micro-agent에 탑재되며, 활성화되면 실시간 운영 체제 디바이스를 대상으로 한 일반적인 위협을 감지합니다.

Azure RTOS용 Defender-IoT-micro-agent는 백그라운드에서 실행되며, 각 고객의 고유 연결을 사용하여 IoT Hub에 보안 메시지를 전송하는 동안 원활한 사용자 환경을 제공합니다. Azure RTOS용 Defender-IoT-micro-agent는 기본적으로 사용됩니다.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo는 긴밀하게 포함된 실시간 및 IoT 애플리케이션을 위해 특별히 설계된 고급, 산업용 TCP/IP 네트워크 스택입니다. Azure RTOS NetX Duo는 보안 및 클라우드를 비롯한 다양한 프로토콜 집합을 제공하는 이중 IPv4 및 IPv6 네트워크 스택입니다. [Azure RTOS NetX Duo](/azure/rtos/netx-duo/) 솔루션에 대해 자세히 알아보세요.

이 모듈에는 다음과 같은 기능을 제공합니다.

- **악의적인 네트워크 활동 감지**
- **사용자 지정 경고를 기반으로 하는 디바이스 동작 기준**
- **디바이스 보안 예방 강화**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Azure RTOS용 Defender-IoT-micro-agent 아키텍처

Azure RTOS용 Defender-IoT-micro-agent는 Azure IoT 미들웨어 플랫폼에 의해 초기화되며 IoT Hub 클라이언트를 사용하여 Hub로 보안 원격 분석을 보냅니다.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-IoT-micro-agent 상태 다이어그램 및 정보 흐름":::

Azure RTOS용 Defender-IoT-micro-agent는 세 개의 수집기를 사용하여 다음 디바이스 활동 및 정보를 모니터링합니다.
- 디바이스 네트워크 활동 **TCP**, **UDP**, **ICM**
- **Threadx** 및 **Netx Duo** 버전과 같은 시스템 정보
- 하트비트 이벤트

각 수집기는 우선 순위 그룹에 연결되고 각 우선 순위 그룹에는 **낮음**, **중간**, **높음** 의 값을 적용할 수 있는 고유 간격이 있습니다. 이 간격은 데이터를 수집하고 전송하는 시간 간격에 영향을 줍니다.

각 시간 간격은 구성 가능하며, 추가로 [솔루션을 사용자 지정](how-to-azure-rtos-security-module.md)하기 위해 IoT 커넥터를 사용하거나 사용하지 않을 수 있습니다. 

## <a name="supported-security-alerts-and-recommendations"></a>지원되는 보안 경고 및 권장 사항

Azure RTOS용 Defender-IoT-micro-agent는 특정 보안 경고 및 권장 사항을 지원합니다. 초기 구성을 완료한 후 서비스에 대한 [관련 경고 및 권장 값을 검토하고 사용자 지정](concept-rtos-security-alerts-recommendations.md)해야 합니다.

## <a name="ready-to-begin"></a>시작할 준비가 되셨나요?

Azure RTOS용 Defender-IoT-micro-agent는 IoT 디바이스에 무료 다운로드로 제공됩니다. Defender for IoT 클라우드 서비스는 Azure 구독당 30일 평가판으로 제공됩니다. [지금 Defender-IoT-micro-agent를 다운로드](https://github.com/azure-rtos/azure-iot-preview/releases)하여 시작하세요. 

## <a name="next-steps"></a>다음 단계

- Azure RTOS용 Defender-IoT-micro-agent [필수 구성 요소 및 설정](quickstart-azure-rtos-security-module.md)을 시작하세요.
- Azure RTOS용 Defender-IoT-micro-agent [보안 경고 및 권장 사항 지원](concept-rtos-security-alerts-recommendations.md)에 대해 자세히 알아보세요. 
- Azure RTOS용 Defender-IoT-micro-agent [참조 API](azure-rtos-security-module-api.md)를 사용하세요.
