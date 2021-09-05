---
title: 보안 에이전트
description: IoT 디바이스에서 Azure Defender for IoT 보안 서비스 에이전트를 이해, 구성, 배포, 사용하는 방법을 시작하세요.
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: d25adad695e12d7ee104fdf9af616baefb98150c
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018804"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Azure Defender for IoT 디바이스 마이크로 에이전트 시작

Defender for IoT 보안 에이전트는 운영 체제 구성 모범 사례 모니터링과 같은 향상된 보안 기능을 제공합니다. 단일 서비스에서 디바이스 필드 위협 방지 및 보안 태세를 제어합니다.

Defender for IoT 보안 에이전트는 디바이스 운영 체제의 원시 이벤트 수집, 비용을 줄이기 위한 이벤트 집계 및 디바이스 모듈 쌍을 통한 구성을 처리합니다. 보안 메시지는 IoT Hub를 통해 Defender for IoT 분석 서비스에 전송됩니다.

다음 워크플로를 사용하여 Defender for IoT 보안 에이전트를 배포하고 테스트합니다.

1. [IoT Hub에 대한 Defender for IoT 서비스를 사용하도록 설정](quickstart-onboard-iot-hub.md)합니다.

1. IoT Hub에 등록된 디바이스가 없는 경우 [새 디바이스를 등록](/previous-versions/azure/iot-accelerators/iot-accelerators-device-simulation-overview)합니다.

1. 디바이스에 대해 [DefenderIotMicroAgent 모듈 쌍을 만듭니다](quickstart-create-micro-agent-module-twin.md).

1. 실제 디바이스에 설치하는 대신 시뮬레이션된 Azure 디바이스에 에이전트를 설치하려면 [새 Azure VM(가상 머신)을 스핀업](../../virtual-machines/linux/quick-create-portal.md)합니다.

1. IoT 디바이스 또는 새 VM에 [Defender for IoT 보안 에이전트를 배포](how-to-deploy-linux-cs.md)합니다.

1. [trigger_events](https://aka.ms/iot-security-github-trigger-events)에 대한 지침에 따라 OS 기준 이벤트를 실행합니다.

1. 이전 단계에서 시뮬레이션된 OS 기준 검사 실패에 대한 응답으로 Defender for IoT 권장 사항을 확인합니다. 스크립트를 실행한 후 30분 후에 확인을 시작합니다.

## <a name="next-steps"></a>다음 단계

- [솔루션](quickstart-configure-your-solution.md) 구성
- [Defender-IoT-micro-agents 만들기](quickstart-create-security-twin.md)
- [사용자 지정 경고](quickstart-create-custom-alerts.md) 구성
- [보안 에이전트 배포](how-to-deploy-agent.md)