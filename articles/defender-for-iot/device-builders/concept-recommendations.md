---
title: IoT Hub에 대한 보안 추천 사항
description: 보안 권장 사항의 개념과 Defender for IoT Hub에 사용되는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: c2fcc071c78838f5575c40288db37f51074c12de
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263708"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT Hub에 대한 보안 추천 사항

Defender for IoT는 Azure 리소스 및 IoT 디바이스를 검색하고, 보안 권장 사항을 제공하여 공격 노출 영역을 줄입니다.
보안 권장 사항은 실행 가능하며, 고객이 보안 모범 사례를 준수하도록 지원하는 것을 목표로 합니다.

이 문서에서는 IoT Hub에서 트리거할 수 있는 권장 사항 목록을 찾을 수 있습니다.

## <a name="built-in-recommendations-in-iot-hub"></a>IoT Hub의 기본 제공 권장 사항

권장 사항 경고는 환경의 보안 상태를 개선하기 위한 작업에 대한 인사이트 및 제안을 제공합니다.

### <a name="high-severity"></a>높은 심각도

| 심각도 | 이름 | 데이터 원본 | Description | RecommendationType |
|--|--|--|--|--|
| 높음 | 여러 디바이스에서 사용하는 동일한 인증 자격 증명 | IoT Hub | IoT Hub 인증 자격 증명이 여러 디바이스에서 사용됩니다. 이는 불법적인 디바이스가 합법적인 디바이스를 가장하고 있으며 악의적인 행위자가 디바이스를 가장할 위험도 노출함을 나타낼 수 있습니다. | IoT_SharedCredentials |
| 높음 | IoT Edge 모듈용 IoT Edge 모델 쌍에 구성된 높은 수준의 권한 | IoT Hub | IoT Edge 모듈은 광범위한 Linux 기능 또는 호스트 수준 네트워크 액세스(호스트 컴퓨터에 데이터 보내기/받기)를 사용하여 권한 있는 모드에서 실행되도록 구성됩니다. | IoT_PrivilegedDockerOptions |

### <a name="medium-severity"></a>중간 심각도

| 심각도 | 이름 | 데이터 원본 | Description | RecommendationType |
|--|--|--|--|--|
| 중간 | ACR 리포지토리와 함께 사용되지 않는 서비스 주체 | IoT Hub | ACR 리포지토리에서 IoT Edge 모듈을 끌어오는 데 사용되는 인증 스키마는 서비스 주체 인증을 사용하지 않습니다. | IoT_ACRAuthentication |
| 중간 | TLS 암호 그룹 업그레이드 필요 | IoT Hub | 보안되지 않은 TLS 구성이 검색되었습니다. 즉시 TLS 암호 그룹 업그레이드를 권장합니다. | IoT_VulnerableTLSCipherSuite |
| 중간 | 기본 IP 필터 정책은 거부여야 합니다. | IoT Hub | 기본적으로 IP 필터 구성에는 허용된 트래픽에 대해 정의된 규칙이 필요하며 다른 모든 트래픽을 거부해야 합니다. | IoT_IPFilter_DenyAll |
| 중간 | IP 필터 규칙에 넓은 IP 범위가 포함됨 | IoT Hub | 허용 가능한 IP 범위가 너무 큰 IP 필터 규칙 원본입니다. 지나치게 허용된 규칙은 악의적인 행위자에게 IoT Hub 노출할 수 있습니다. | IoT_IPFilter_PermissiveRule |
| 중간 | IP 필터에 대한 권장 규칙 | IoT Hub | IP 필터를 IotHub 동작에서 얻은 규칙인 다음 규칙으로 변경하는 것이 좋습니다. | IoT_RecommendedIpRulesByBaseLine |
| 중간 | SecurityGroup에 일관되지 않은 모듈 설정이 있습니다. | IoT Hub | 이 디바이스 보안 그룹 내에서 변칙 디바이스는 나머지 보안 그룹과 비교할 때 모듈 설정에 일관되지 IoT Edge. | IoT_InconsistentModuleSettings |

### <a name="low-severity"></a>낮은 심각도

| 심각도 | 이름 | 데이터 원본 | Description | RecommendationType |
|--|--|--|--|--|
| 낮음 | IoT Edge Hub 메모리를 최적화할 수 있습니다. | IoT Hub | 솔루션의 Edge 모듈에서 사용하지 않는 모든 프로토콜에 대해 프로토콜 헤드를 해제하여 IoT Edge Hub 메모리 사용을 최적화합니다. | IoT_EdgeHubMemOptimize |
| 낮음 | IoT Edge 모듈에 대해 구성된 로깅 없음 | IoT Hub | 이 IoT Edge 모듈에서는 로깅을 사용할 수 없습니다. | IoT_EdgeLoggingOptions |

## <a name="next-steps"></a>다음 단계

- 클래식 Defender [for IoT 디바이스 보안 경고에](agent-based-security-alerts.md) 대해 자세히 알아보기
