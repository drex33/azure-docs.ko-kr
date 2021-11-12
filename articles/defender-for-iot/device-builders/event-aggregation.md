---
title: Defender-IoT-micro-agent 클래식 이벤트 집계
description: Defender for IoT 이벤트 집계에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 2e7ad46dd7e94c955b722e0d7a3f06237828c9b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306305"
---
# <a name="defender-iot-micro-agent-classic-event-aggregation"></a>Defender-IoT-micro-agent 클래식 이벤트 집계

Defender for IoT 보안 에이전트는 로컬 디바이스에서 데이터 및 시스템 이벤트를 수집하고 이 데이터를 처리 및 분석을 위해 Azure 클라우드에 보냅니다. 보안 에이전트는 새로운 프로세스 및 새 연결 이벤트를 비롯한 다양한 유형의 디바이스 이벤트를 수집합니다. 새 프로세스와 새 연결 이벤트는 모두 1초 내에 디바이스에서 적절히 자주 발생하고, 강력하고 포괄적인 보안을 위해 중요하지만 보안 에이전트가 강제로 전송하는 메시지 수는 IoT Hub 할당량 및 비용 제한에 빠르게 도달하거나 이 한도를 초과할 수 있습니다. 그러나 이러한 이벤트에는 디바이스를 보호하는 데 매우 중요한 보안 정보가 포함되어 있습니다.

디바이스를 보호하면서 불필요한 할당량 및 비용을 줄이기 위해 Defender for IoT 에이전트는 이러한 유형의 이벤트를 집계합니다.

이벤트 집계는 기본적으로 **설정** 되어 있으며, 권장되지는 않지만 언제든지 수동으로 **해제** 할 수 있습니다.

집계는 현재 다음과 같은 유형의 이벤트에 사용할 수 있습니다.

* ProcessCreate
* ConnectionCreate
* ProcessTerminate(Windows에만 해당)

## <a name="how-does-event-aggregation-work"></a>이벤트 집계는 어떻게 작동하나요?

이벤트 집계가 **설정** 되어 있으면 Defender for IoT 에이전트는 간격 또는 기간에 대한 이벤트를 집계합니다.
간격이 지나면 에이전트는 추가 분석을 위해 집계된 이벤트를 Azure 클라우드로 보냅니다.
집계된 이벤트는 Azure 클라우드로 전송될 때까지 메모리에 저장됩니다.

에이전트의 메모리 공간을 줄이기 위해 에이전트는 이미 메모리에 유지 중인 이벤트와 동일한 이벤트를 수집할 때마다 에이전트는 이 특정 이벤트의 적중 횟수를 늘립니다. 집계 기간이 지나면 에이전트는 발생한 특정 유형의 이벤트 각각에 대한 적중 횟수를 보냅니다. 이벤트 집계는 수집된 각 이벤트 유형의 적중 횟수에 대한 집계일 뿐입니다.

이벤트는 다음 조건이 충족될 때만 동일한 것으로 간주됩니다.

* ProcessCreate 이벤트 - **commandLine**, **executable**, **username** 및 **userid** 가 동일한 경우
* ConnectionCreate 이벤트 - **commandLine**, **userId**, **direction**, **local address**, **remote address**, **protocol** 및 **destination port** 가 동일한 경우
* ProcessTerminate 이벤트 - **executable** 및 **exit status** 가 동일한 경우

### <a name="working-with-aggregated-events"></a>집계 이벤트 작업

집계 중에 집계되지 않은 이벤트 속성은 무시되고 값이 0인 이벤트 속성은 Log Analytics에 표시됩니다.

* ProcessCreate 이벤트 - **processId** 및 **parentprocessid** 가 0으로 설정된 경우
* ConnectionCreate 이벤트 - **processId** 및 **source port** 가 0으로 설정된 경우

## <a name="event-aggregation-based-alerts"></a>이벤트 집계 기반 경고

분석 후에 Defender for IoT에서는 의심스러운 집계 이벤트에 대한 보안 경고를 생성합니다. 집계 이벤트에서 생성된 경고는 집계된 각 이벤트에 대해 한 번만 나타납니다.

각 이벤트에 대한 집계 시작 시간, 종료 시간 및 적중 횟수는 조사 중에 사용할 Log Analytics 내의 이벤트 **ExtraDetails** 필드에 기록됩니다.

집계된 각 이벤트는 24시간 동안 수집된 경고를 나타냅니다. 각 이벤트의 왼쪽 위에 있는 이벤트 옵션 메뉴를 사용하여 각 개별 집계 이벤트를 **해제** 할 수 있습니다.

## <a name="event-aggregation-twin-configuration"></a>이벤트 집계 쌍 구성

**azureiotsecurity** 모듈의 모듈 쌍 ID에 대한 [에이전트 구성 개체](how-to-agent-configuration.md) 내에서 Defender for IoT 이벤트 집계의 구성을 변경합니다.

| 구성 이름 | 가능한 값 | 세부 정보 | 설명 |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | 프로세스 만들기 이벤트에 대한 이벤트 집계 사용/사용 안 함 |
| aggregationIntervalProcessCreate | ISO8601 Timespan 문자열 | 프로세스 만들기 이벤트의 집계 간격 |
| aggregationEnabledConnectionCreate | boolean| 연결 만들기 이벤트에 대한 이벤트 집계 사용/사용 안 함 |
| aggregationIntervalConnectionCreate | ISO8601 Timespan 문자열 | 연결 만들기 이벤트에 대한 집계 간격 |
| aggregationEnabledProcessTerminate | boolean | 프로세스 종료 이벤트에 대한 이벤트 집계 사용/사용 안 함 | Windows만|
| aggregationIntervalProcessTerminate | ISO8601 Timespan 문자열 | 프로세스 종료 이벤트에 대한 집계 간격 | Windows만|
|

## <a name="default-configurations-settings"></a>기본 구성 설정

| 구성 이름 | 기본값 |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | "PT1H"|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | "PT1H"|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Defender for IoT 보안 에이전트 집계와 사용 가능한 이벤트 구성 옵션에 대해 알아보았습니다.

Defender for IoT 개발을 계속 시작하려면 다음 문서를 사용합니다.

- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 이해
- [보안 에이전트](how-to-deploy-agent.md) 선택 및 배포
- [IoT Hub에서 Defender for IoT 서비스를 사용하도록 설정](quickstart-onboard-iot-hub.md)하는 방법을 알아봅니다.
- [Defender for IoT FAQ](resources-agent-frequently-asked-questions.md)에서 서비스에 대해 자세히 알아봅니다.
