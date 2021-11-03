---
title: Azure Video Analyzer 모듈 쌍 JSON 스키마
description: 이 문서에서는 Azure Video Analyzer 에지 모듈에 대한 모듈 쌍 JSON 스키마에 대한 개요를 제공합니다.
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f6dd3ae2e188e8179fde0cd3a684e647cbf92fe3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053687"
---
# <a name="module-twin-configuration-schema"></a>모듈 쌍 구성 스키마

[!INCLUDE [header](includes/edge-env.md)]

디바이스 쌍은 메타데이터, 구성, 조건 등 디바이스 상태 정보가 저장된 JSON 문서입니다. Azure IoT Hub는 IoT Hub에 연결하는 디바이스마다 하나의 디바이스 쌍을 유지합니다. 자세한 내용은 [IoT Hub의 모듈 쌍 이해 및 사용](../../../iot-hub/iot-hub-devguide-module-twins.md)을 참조하세요.

이 항목에서는 Azure Video Analyzer 에지 모듈의 모듈 쌍 JSON 스키마에 대해 설명합니다.

## <a name="module-twin-properties"></a>모듈 쌍 속성

Video Analyzer 에지 모듈은 다음 모듈 쌍 속성을 노출합니다.

| 속성                    | 필수 | 동적 | Description                                                  |
| :-------------------------- | :------- | :------ | :----------------------------------------------------------- |
| `ProvisioningToken`          | 예      | 아니요      | 에지 모듈의 유효성을 검사하고 클라우드 서비스를 프로비전하는 인증 토큰(Video Analyzer 계정에 대한 액세스 포함) |
| `ApplicationDataDirectory`    | Yes      | 아니요      | 구성을 유지하기 위해 탑재된 볼륨에 매핑되는 모듈 파일 시스템 내의 경로입니다.       |
| `DiagnosticsEventsOutputName` | 아니요       | 예     | 진단 이벤트에 대한 허브 출력입니다. (비어 있는 경우 진단이 게시되지 않았음을 의미함) |
| `OperationalEventsOutputName` | 아니요       | 예     | 작업 이벤트에 대한 허브 출력입니다. (비어 있는 경우 작업 이벤트가 게시되지 않았음을 의미함) |
| `LogLevel`                    | 아니요       | 예     | 자세한 정보, 정보(기본값), 경고, 오류, 없음 중 하나 |
| `LogCategories`               | 아니요       | 예     | 쉼표로 구분된 애플리케이션, MediaPipeline, 이벤트 목록으로 기본값은 애플리케이션, 이벤트입니다. |
| `AllowUnsecuredEndpoints`     | 아니요       | 예     | 비보안 엔드포인트(예: `#Microsoft.VideoAnalyzer.UnsecuredEndpoint`)를 사용하여 토폴로지를 만들 수 있도록 하는 부울 설정으로 기본값은 true입니다.        |
| `TelemetryOptOut`             | 아니요       | 아니요     | 원격 분석 제출을 옵트아웃하기 위한 부울 설정으로 기본값은 false입니다.       |
| `DebugLogsDirectory`          | 아니요       | 예     | 디버그 로그 디렉터리입니다. 디렉터리가 있으면 로그를 생성하고, 없으면 디버그 로그를 사용하지 않도록 설정합니다.       |

모듈을 다시 시작하지 않고도 동적 속성을 업데이트할 수 있습니다. 

선택적 진단 설정의 역할에 대한 자세한 내용은 [모니터링 및 로깅](monitor-log-edge.md) 문서를 참조하세요.

```json
{
    "properties.desired": {
        "ProvisioningToken": "$AVA_PROVISIONING_TOKEN",
        "ApplicationDataDirectory": "/var/lib/videoanalyzer",
        "DiagnosticsEventsOutputName": "diagnostics",
        "OperationalEventsOutputName": "operational",
        "LogLevel": "information",
        "LogCategories": "Application,Events",
        "DebugLogsDirectory": "/tmp/logs",
        "AllowUnsecuredEndpoints": true,
        "TelemetryOptOut": false    
     
    }
}
```

## <a name="next-steps"></a>다음 단계

[직접 메서드](direct-methods.md)
