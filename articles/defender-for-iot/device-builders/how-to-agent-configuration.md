---
title: 보안 에이전트 구성
description: Defender for IoT 보안 서비스에 사용할 수 있도록 Defender for IoT 에이전트를 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 8cccb5efc76f0a451cc0de992a0521d5b12978e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335616"
---
# <a name="tutorial-configure-security-agents"></a>자습서: 보안 에이전트 구성

이 문서에서는 Defender for IoT 보안 에이전트를 설명하고 이를 변경 및 구성하는 방법을 자세히 설명합니다.

> [!div class="checklist"]
> * 보안 에이전트 구성
> * 쌍 속성을 편집하여 에이전트 동작 변경
> * 기본 구성 검색

## <a name="agents"></a>에이전트

Defender for IoT 보안 에이전트는 IoT 디바이스에서 데이터를 수집하고 보안 작업을 수행하여 검색된 취약점을 완화합니다. 보안 에이전트 구성은 사용자 지정할 수 있는 모듈 쌍 속성 집합을 사용하여 제어할 수 있습니다. 일반적으로 이러한 속성의 2차 업데이트는 자주 수행되지 않습니다.

Defender for IoT의 보안 에이전트 쌍 구성 개체는 JSON 형식 개체입니다. 구성 개체는 에이전트의 동작을 제어하기 위해 정의할 수 있는 제어 가능한 속성 집합입니다.

이러한 구성을 통해 각 시나리오에 필요한 에이전트를 사용자 지정할 수 있습니다. 예를 들어 이러한 속성을 구성하여 일부 이벤트를 자동으로 제외하거나 전력 소모량을 최소 수준으로 유지할 수 있습니다.

변경하려면 Defender for IoT 보안 에이전트 구성 [스키마](https://aka.ms/iot-security-github-module-schema)를 사용하세요.

## <a name="configuration-objects"></a>구성 개체

모든 Defender for IoT 보안 에이전트와 관련된 속성은 **azureiotsecurity** 모듈의 원하는 속성 섹션에 있는 에이전트 구성 개체에 있습니다.

구성을 수정하려면 **azureiotsecurity** 모듈 쌍 ID 내에서 이 개체를 만들고 수정합니다.

에이전트 구성 개체가 **azureiotsecurity** 모듈 쌍에 없으면 모든 보안 에이전트 속성 값이 기본값으로 설정됩니다.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>구성 스키마 및 유효성 검사

이 [스키마](https://aka.ms/iot-security-github-module-schema)와 비교하여 에이전트 구성의 유효성을 검사해야 합니다. 구성 개체가 스키마와 일치하지 않으면 에이전트가 시작되지 않습니다.

에이전트가 실행되는 동안 구성 개체가 유효하지 않은 구성(구성이 스키마와 일치하지 않음)으로 변경되면 에이전트는 잘못된 구성을 무시하고 현재 구성을 계속 사용합니다.

### <a name="configuration-validation"></a>구성 유효성 검사

Defender for IoT 보안 에이전트는 **azureiotsecurity** 모듈 쌍 ID의 보고된 속성 섹션 내에 있는 현재 구성을 보고합니다.
에이전트는 사용 가능한 모든 속성을 보고하며, 사용자가 속성을 설정하지 않았다면 기본 구성을 보고합니다.

구성의 유효성을 검사하려면 원하는 섹션에 설정된 값과 보고된 섹션에 보고된 값을 비교합니다.

원하는 속성과 보고된 속성이 일치하지 않으면 에이전트가 구성을 구문 분석할 수 없습니다.

[스키마](https://aka.ms/iot-security-github-module-schema)와 비교하여 원하는 속성의 유효성을 검사하고, 오류를 수정하고, 원하는 속성을 다시 설정하세요.

> [!NOTE]
> 에이전트가 원하는 구성을 구문 분석할 수 없는 경우 에이전트에서 구성 오류 경고가 발생합니다.
> 보고된 섹션과 원하는 섹션을 비교하여 경고가 여전히 적용되는지 확인합니다.

## <a name="editing-a-property"></a>속성 편집

모든 사용자 지정 속성은 **azureiotsecurity** 모듈 쌍 내의 에이전트 구성 개체 안에서 설정해야 합니다.
기본 속성 값을 사용하려면 구성 개체에서 속성을 제거합니다.

### <a name="setting-a-property"></a>속성 설정

1. IoT Hub에서 변경하려는 디바이스를 찾아서 선택합니다.

1. 디바이스를 클릭한 후 **azureiotsecurity** 모듈을 클릭합니다.

1. **모듈 ID 쌍** 을 클릭합니다.

1. Defender-IoT-micro-agent에서 변경하려는 속성을 편집합니다.

   예를 들어 연결 이벤트를 높은 우선 순위로 구성하고 7분마다 높은 우선 순위 이벤트를 수집하려면 다음 구성을 사용합니다.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. **저장** 을 클릭합니다.

### <a name="using-a-default-value"></a>기본값 사용

기본 속성 값을 사용하려면 구성 개체에서 속성을 제거합니다.

## <a name="default-properties"></a>기본 속성

다음 표에는 Defender for IoT 보안 에이전트의 제어 가능 속성이 나와 있습니다.

기본값은 [GitHub](https\://aka.ms/iot-security-module-default)의 적절한 스키마에서 사용할 수 있습니다.

| 이름| 상태 | 유효한 값| 기본값| Description |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|필수: false |유효한 값: ISO 8601 형식의 기간 |기본값: PT7M |우선 순위가 높은 메시지가 전송되기 전의 최대 시간 간격입니다.|
|lowPriorityMessageFrequency |필수: false|유효한 값: ISO 8601 형식의 기간 |기본값: PT5H |우선 순위가 높은 메시지가 전송되기 전의 최대 시간입니다.|
|snapshotFrequency |필수: false|유효한 값: ISO 8601 형식의 기간 |기본값: PT13H |디바이스 상태 스냅샷을 만드는 시간 간격입니다.|
|maxLocalCacheSizeInBytes |필수: false |유효한 값은 |기본값: 2560000, 8192보다 큼 | 에이전트의 메시지 캐시에 허용되는 최대 스토리지(바이트)입니다. 메시지를 전송하기 전에 디바이스에 메시지를 저장할 수 있는 공간의 최대 크기입니다.|
|maxMessageSizeInBytes |필수: false |유효한 값: 8192보다 크고 262144보다 작은 양수 |기본값: 204800 |클라우드 메시지에 대한 에이전트의 최대 허용 크기입니다. 이 설정은 각 메시지에서 전송되는 최대 데이터 양을 제어합니다. |
|eventPriority${EventName} |필수: false |유효한 값: 높음, 낮음, 꺼짐 |기본값: |에이전트에서 생성한 모든 이벤트의 우선 순위 |

### <a name="supported-security-events"></a>지원되는 보안 이벤트

|이벤트 이름| PropertyName | 기본값| 스냅샷 이벤트| 세부 정보 상태  |
|----------|-|---------|----|----|
|진단 이벤트|eventPriorityDiagnostic| 꺼짐| False| 에이전트 관련 진단 이벤트입니다. 자세한 정보 로깅에 이 이벤트를 사용합니다.|
|구성 오류 |eventPriorityConfigurationError |낮음 |False |에이전트가 구성 파일을 구문 분석하지 못했습니다. 스키마와 비교하여 구성을 확인합니다.|
|삭제된 이벤트 통계 |eventPriorityDroppedEventsStatistics |낮음 |True|에이전트 관련 이벤트 통계입니다. |
|연결된 하드웨어|eventPriorityConnectedHardware |낮음 |True |디바이스에 연결된 모든 하드웨어의 스냅샷입니다.|
|수신 대기 포트|eventPriorityListeningPorts |높음 |True |디바이스에서 열려 있는 모든 수신 포트의 스냅샷입니다.|
|프로세스 만들기 |eventPriorityProcessCreate |낮음 |False |디바이스에서 프로세스 생성을 감사합니다.|
|프로세스 종료|eventPriorityProcessTerminate |낮음 |False |디바이스에서 프로세스 종료를 감사합니다.|
|시스템 정보 |eventPrioritySystemInformation |낮음 |True |시스템 정보(예: OS 또는 CPU)의 스냅샷입니다.|
|로컬 사용자| eventPriorityLocalUsers |높음 |True|시스템 내에 등록된 로컬 사용자의 스냅샷입니다. |
|로그인|  eventPriorityLogin |높음|False|디바이스 로그인 이벤트(로컬 및 원격 로그인)를 감사합니다.|
|연결 만들기 |eventPriorityConnectionCreate|낮음|False|생성된 디바이스와의 TCP 연결을 감사합니다. |
|방화벽 구성| eventPriorityFirewallConfiguration|낮음|True|디바이스 방화벽 구성(방화벽 규칙)의 스냅샷입니다. |
|OS 기준선| eventPriorityOSBaseline| 낮음|True|디바이스 OS 기준선 검사의 스냅샷입니다.|
|

## <a name="next-steps"></a>다음 단계

- [Defender for IoT 권장 사항 이해](concept-recommendations.md)
- [Defender for IoT 경고 탐색](concept-security-alerts.md)
- [원시 보안 데이터 액세스](how-to-security-data-access.md)
