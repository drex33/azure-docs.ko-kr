---
title: Defender for IoT azureiotsecurity for IoT Edge
description: IoT Edge azureiotsecurity를 Azure Defender for IoT 아키텍처 및 기능을 이해합니다.
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: dddd45c857537cbb1af8032c6e53590e9a8ff4cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604895"
---
# <a name="azure-defender-for-iot-edge-azureiotsecurity"></a>Azure Defender for IoT Edge azureiotsecurity

[Azure IoT Edge](../../iot-edge/index.yml)는 엣지에서 비즈니스 워크플로를 관리하고 수행할 수 있는 강력한 기능을 제공합니다.
IoT 환경에서 IoT Edge를 재생하는 주요 부분은 악의적인 행위자에게 특히 매력적인 표적이 됩니다.

Defender for IoT azureiotsecurity는 IoT Edge 디바이스에 대한 포괄적인 보안 솔루션을 제공합니다.
Defender for IoT 모듈은 운영 체제 및 컨테이너 시스템에서 원시 보안 데이터를 수집하고 집계하고 분석하여 실행 가능한 보안 권장 사항 및 경고로 변환합니다.

IoT 디바이스에 대한 Defender for IoT 보안 에이전트와 마찬가지로 Defender for IoT Edge 모듈은 모듈 쌍을 통해 사용자 지정할 수 있습니다.
자세한 내용은 [에이전트 구성](how-to-agent-configuration.md)을 참조하세요.

Defender for IoT azureiotsecurity for IoT Edge 다음과 같은 기능을 제공합니다.

- 기본 운영 체제(Linux) 및 IoT Edge 컨테이너 시스템에서 원시 보안 이벤트를 수집합니다.

  사용 가능한 보안 데이터 수집기에 대한 자세한 내용은 [Defender for IoT 에이전트 구성](how-to-agent-configuration.md)을 참조하세요.

- IoT Edge 배포 매니페스트를 분석합니다.

- 원시 보안 이벤트를 [IoT Edge Hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)를 통해 보낸 메시지로 집계합니다.

- azureiotsecurity 쌍을 사용하여 구성을 제거합니다.

  자세한 내용은 [Defender for IoT 에이전트 구성](how-to-agent-configuration.md)을 참조하세요.

defender for IoT azureiotsecurity for IoT Edge IoT Edge 권한 있는 모드로 실행됩니다.
모듈에서 운영 체제 및 기타 IoT Edge 모듈을 모니터링할 수 있도록 하려면 권한 있는 모드가 필요합니다.

## <a name="module-supported-platforms"></a>모듈 지원 플랫폼

defender for IoT azureiotsecurity for IoT Edge 현재 Linux에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 defender for IoT azureiotsecurity for IoT Edge 아키텍처 및 기능에 대해 배웠습니다.

Defender for IoT 개발을 계속 시작하려면 다음 문서를 사용합니다.

- [IoT Edge 대한 azureiotsecurity](how-to-deploy-edge.md) 배포
- [Defender-IoT-micro-agent 구성](how-to-agent-configuration.md) 방법 알아보기
- [IoT Hub에서 Defender for IoT 서비스를 사용하도록 설정](quickstart-onboard-iot-hub.md)하는 방법을 알아봅니다.
- [Defender for IoT FAQ](resources-agent-frequently-asked-questions.md)에서 서비스에 대해 자세히 알아봅니다.
