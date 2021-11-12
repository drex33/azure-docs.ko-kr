---
title: Defender-IoT-micro-agent 및 디바이스 쌍
description: Defender-IoT-micro-agent 쌍의 개념과 Defender for IoT에서 사용되는 방법에 대해 알아 봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: f8a634eaa3947d08e6ab88b8f05e119fbcf54665
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278933"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-micro-agent

이 문서에서는 Defender for IoT에서 디바이스 쌍 및 모듈을 사용하는 방법을 설명합니다.

## <a name="device-twins"></a>디바이스 쌍

Azure에 구축된 IoT 솔루션의 경우 디바이스 쌍은 디바이스 관리와 프로세스 자동화 모두에서 중요한 역할을 수행합니다.

Defender for IoT는 기존 IoT 디바이스 관리 플랫폼과 완벽하게 통합되어 디바이스 보안 상태를 관리하고 기존 디바이스 제어 기능을 활용할 수 있도록 지원합니다. 통합은 IoT Hub 쌍 메커니즘을 사용하여 구현됩니다.

Azure IoT Hub에서 [디바이스 쌍](../../iot-hub/iot-hub-devguide-device-twins.md#device-twins)의 개념에 대해 자세히 알아봅니다.

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-micro-agent 쌍

Defender for IoT는 서비스의 각 디바이스에 대한 Defender-IoT-micro-agent 쌍을 유지 관리합니다.
Defender-IoT-micro-agent 쌍에는 솔루션의 각 특정 디바이스에 대한 디바이스 보안과 관련된 모든 정보가 포함되어 있습니다.
디바이스 보안 속성은 더 안전한 통신을 위해 전용 Defender-IoT-micro-agent 쌍에서 유지되어 더 적은 리소스가 필요한 업데이트 및 유지 관리를 가능케 합니다.

쌍을 만들고, 사용자 지정하고, 구성하는 방법에 대해 알아보려면 [Defender-IoT-micro-agent 쌍 만들기](quickstart-create-security-twin.md) 및 [보안 에이전트 구성](how-to-agent-configuration.md)을 참조하세요. IoT Hub에 있는 모듈 쌍의 개념에 대한 자세한 내용은 [IoT Hub에서 모듈 쌍 이해 및 사용](../../iot-hub/iot-hub-devguide-module-twins.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [Defender for IoT 개요](overview.md)
- [보안 에이전트 배포](how-to-deploy-agent.md)
- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)
