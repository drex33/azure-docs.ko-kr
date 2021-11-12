---
title: 에이전트 기반 보안 사용자 지정 경고
description: Defender for IoT 디바이스의 기능 및 서비스를 사용하여 사용자 지정 가능한 보안 경고 및 권장 수정 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 8b1ea13ce6fef5a20c6e172a9de79bec2aa4a93e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289972"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Defender for IoT 디바이스 사용자 지정 보안 경고

Defender for IoT는 고급 분석 및 위협 인텔리전스를 사용하여 IoT 솔루션을 지속적으로 분석하여 악의적인 활동을 경고합니다.

사용자의 고유한 조직 배포 및 환경에서 경고가 잠재적인 손상에 대한 가장 효율적인 지표로서 동작하도록 하려면 예상되는 디바이스 동작에 대한 지식을 기반으로 사용자 지정 경고를 만드는 것이 좋습니다.

다음 Defender for IoT 경고 목록은 예상되는 IoT 디바이스 동작을 기반으로 하여 사용자가 정의할 수 있습니다. 각 경고를 사용자 지정하는 방법에 대한 자세한 내용은 [사용자 지정 경고 만들기](quickstart-create-custom-alerts.md)를 참조하세요.

## <a name="agent-based-security-custom-alerts"></a>에이전트 기반 보안 사용자 지정 경고

| 심각도 | 경고 이름 | 데이터 원본 | Description | 제안된 수정 사항 |
|--|--|--|--|--|
| 낮음 | 사용자 지정 경고 - 활성 연결 수가 허용 범위를 벗어납니다. | 클래식 Defender-IoT-micro-agent, Azure RTOS | 특정 시간 범위 내의 활성 연결 수가 현재 구성된 허용 범위를 벗어납니다. | 디바이스 로그를 조사합니다. 연결이 시작된 위치를 알아보고, 무해하거나 악의적인 위치인지 확인합니다. 악의적인 위치이면 가능한 멀웨어를 제거하고 해당 원본을 파악합니다. 무해한 위치이면 해당 원본을 연결 허용 목록에 추가합니다. |
| 낮음 | 사용자 지정 경고 - 아웃바운드 연결이 허용되지 않는 IP에 만들어졌습니다. | 클래식 Defender-IoT-micro-agent, Azure RTOS | 아웃바운드 연결이 IP 허용 목록을 벗어난 IP에 만들어졌습니다. | 디바이스 로그를 조사합니다. 연결이 시작된 위치를 알아보고, 무해하거나 악의적인 위치인지 확인합니다. 악의적인 위치이면 가능한 멀웨어를 제거하고 해당 원본을 파악합니다. 무해한 위치이면 해당 원본을 IP 허용 목록에 추가합니다. |
| 낮음 | 사용자 지정 경고 - 실패한 로컬 로그인 수가 허용 범위를 벗어납니다. | 클래식 Defender-IoT-micro-agent, Azure RTOS | 특정 시간 범위 내의 실패한 로컬 로그인 수가 현재 구성된 허용 범위를 벗어납니다. |  |
| 낮음 | 사용자 지정 경고 - 사용자 허용 목록에 없는 사용자의 로그인입니다. | 클래식 Defender-IoT-micro-agent, Azure RTOS | 사용자 허용 목록에 없는 로컬 사용자가 디바이스에 로그인했습니다. | 원시 데이터를 저장하는 경우 로그 분석 계정으로 이동하고, 데이터를 사용하여 디바이스를 조사하고, 원본을 확인한 다음, 해당 설정에 대한 허용/차단 목록을 수정합니다. 현재 원시 데이터를 저장하지 않는 경우 디바이스로 이동하여 해당 설정에 대한 허용/차단 목록을 수정합니다. |
| 낮음 | 사용자 지정 경고 - 허용되지 않는 프로세스가 실행되었습니다. | 클래식 Defender-IoT-micro-agent, Azure RTOS | 디바이스에서 허용되지 않는 프로세스가 실행되었습니다. | 원시 데이터를 저장하는 경우 로그 분석 계정으로 이동하고, 데이터를 사용하여 디바이스를 조사하고, 원본을 확인한 다음, 해당 설정에 대한 허용/차단 목록을 수정합니다. 현재 원시 데이터를 저장하지 않는 경우 디바이스로 이동하여 해당 설정에 대한 허용/차단 목록을 수정합니다. |
|

## <a name="next-steps"></a>다음 단계

- [경고를 사용자 지정](quickstart-create-custom-alerts.md)하는 방법 알아보기
- Defender for IoT 서비스 [개요](overview.md)
- [보안 데이터에 액세스](how-to-security-data-access.md)하는 방법 알아보기
- [디바이스 조사](how-to-investigate-device.md)에 대한 자세한 정보
