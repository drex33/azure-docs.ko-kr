---
title: 보안 에이전트 로컬 구성(C)
description: C의 에이전트 로컬 구성을 위한 Defender에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 9ffe4ae98cd745c4c9362f09a9a29258a8d48a2e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286662"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>LocalConfiguration. json 파일 이해 - C 에이전트

Defender for IoT 보안 에이전트는 로컬 구성 파일의 구성을 사용합니다.
보안 에이전트는 에이전트 시작 시 구성을 한 번 읽습니다.
로컬 구성 파일에 있는 구성에는 인증 구성과 기타 에이전트 관련 구성이 모두 포함됩니다.
이 파일에는 JSON 표기법에서 "키-값" 쌍의 구성이 포함되며 에이전트가 설치될 때 구성이 채워집니다.

기본적으로 파일은 /var/ASCIoTAgent/LocalConfiguration.json에 있습니다.

구성 파일에 대한 변경 내용은 에이전트가 다시 시작될 때 수행됩니다.

## <a name="security-agent-configurations-for-c"></a>C의 보안 에이전트 구성

| 구성 이름 | 가능한 값 | 세부 정보 |
|:-----------|:---------------|:--------|
| AgentId | GUID | 에이전트 고유 식별자 |
| TriggerdEventsInterval | ISO8601 문자열 | 트리거된 이벤트 컬렉션에 대한 스케줄러 간격 |
| ConnectionTimeout | ISO8601 문자열 | IoThub에 대한 연결 시간이 초과될 때까지의 기간 |
| 인증 | JsonObject | 인증 구성. 이 개체는 IoTHub에 대한 인증에 필요한 모든 정보를 포함합니다. |
| ID | "DPS", "SecurityModule", "Device" | 인증 ID - DPS를 통해 인증하는 경우 DPS, Defender-IoT-micro-agentcredentials를 사용하여 인증하는 경우 SecurityModule, 디바이스 자격 증명을 사용하여 인증하는 경우 device |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | 인증에 대한 사용자 비밀 - 사용 비밀이 대칭 키인 경우 SasToken을 선택하고, 비밀이 자체 서명된 인증서인 경우 자체 서명된 인증서를 선택합니다.  |
| FilePath | 파일 경로(문자열) | 인증 비밀을 포함하는 파일의 경로 |
| HostName | 문자열 | Azure IoT 허브의 호스트 이름. 일반적으로 \<my-hub\> azure-devices.net |
| DeviceId | 문자열 | Azure IoT Hub에 등록된 디바이스 ID |
| DPS | JsonObject | DPS 관련 구성 |
| IDScope | 문자열 | DPS의 ID 범위 |
| RegistrationId | 문자열  | DPS 디바이스 등록 ID |
| 로깅 | JsonObject | 에이전트 로거 관련 구성 |
| SystemLoggerMinimumSeverity | 0 <= 숫자 <= 4 | 이 심각도 이상의 로그 메시지는 /var/log/syslog에 로그됨(0이 가장 낮은 심각도) |
| DiagnosticEventMinimumSeverity | 0 <= 숫자 <= 4 | 이 심각도 이상의 로그 메시지는 진단 이벤트에 로그됨(0이 가장 낮은 심각도) |

## <a name="security-agent-configurations-code-example"></a>보안 에이전트 구성 코드 예제

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md) 읽기
- Defender for IoT [에이전트 기반 솔루션 아키텍처](architecture-agent-based.md)에 관해 자세히 알아보기
- Defender for IoT [서비스](quickstart-onboard-iot-hub.md) 사용
- Defender for IoT 서비스 [FAQ](resources-agent-frequently-asked-questions.md) 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- 보안 [경고](concept-security-alerts.md) 이해
