---
title: 에이전트 기반 권장 사항
description: 보안 권장 사항의 개념과 Defender for IoT 디바이스에 사용되는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/26/2021
ms.openlocfilehash: 41a983f1c5d4e647d83751ea00e444f34e608559
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083109"
---
# <a name="security-recommendations-for-iot-devices"></a>IoT 디바이스에 대한 보안 권장 사항

Defender for IoT는 Azure 리소스 및 IoT 디바이스를 검색하고 보안 권장 사항을 제공하여 공격 노출 영역을 줄입니다.
보안 권장 사항은 실행 가능하며 고객이 보안 모범 사례를 준수하도록 지원하는 것을 목표로 합니다.

이 문서에서는 IoT 디바이스에서 트리거할 수 있는 권장 사항 목록을 찾을 수 있습니다.

## <a name="agent-based-recommendations"></a>에이전트 기반 권장 사항

디바이스 권장 사항은 디바이스 보안 상태를 개선하기 위한 인사이트와 제안을 제공합니다.

| 심각도 | 이름 | 데이터 원본 | Description |
|--|--|--|--|
| 중간 | 디바이스에서 포트 열기 | 클래식 Defender-IoT-micro-agent| 디바이스에서 수신 대기 엔드포인트를 찾았습니다. |
| 중간 | 체인 중 하나에 허용 방화벽 정책이 있습니다. | 클래식 Defender-IoT-micro-agent| 허용되는 방화벽 정책을 찾았습니다(입출력). 방화벽 정책은 기본적으로 모든 트래픽을 거부하고 디바이스와 필요한 통신을 허용하는 규칙을 정의해야 합니다. |
| 중간 | 입력 체인 중 하나에서 허용 방화벽 규칙을 찾았습니다. | 클래식 Defender-IoT-micro-agent| 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. |
| 중간 | 출력 체인 중 하나에서 허용 방화벽 규칙을 찾았습니다. | 클래식 Defender-IoT-micro-agent| 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. |
| 중간 | 운영 체제 기준 유효성 검사에 실패했습니다. | 클래식 Defender-IoT-micro-agent| 디바이스는 [CIS Linux 벤치마크](https://www.cisecurity.org/cis-benchmarks/)를 준수하지 않습니다. |

### <a name="agent-based-operational-recommendations"></a>에이전트 기반 운영 권장 사항

운영 권장 사항은 보안 에이전트 구성을 개선하기 위한 인사이트와 제안을 제공합니다.

| 심각도 | 이름 | 데이터 원본 | Description |
|--|--|--|--|
| 낮음 | 에이전트가 사용되지 않은 메시지를 보냅니다. | 클래식 Defender-IoT-micro-agent | 지난 24시간 동안 10% 이상의 보안 메시지가 4KB보다 작았습니다. |
| 낮음 | 보안 트윈 구성이 최적이 아님 | 클래식 Defender-IoT-micro-agent | 보안 트윈 구성이 최적이 아닙니다. |
| 낮음 | 보안 트윈 구성 충돌 | 클래식 Defender-IoT-micro-agent | 보안 트윈 구성에서 충돌이 확인되었습니다. |

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md)
- [보안 데이터에 액세스](how-to-security-data-access.md)하는 방법 알아보기
- [디바이스 조사](how-to-investigate-device.md)에 대한 자세한 정보
