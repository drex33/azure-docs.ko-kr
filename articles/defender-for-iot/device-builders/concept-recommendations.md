---
title: IoT Hub에 대한 보안 추천 사항
description: 보안 권장 사항의 개념과 Defender for IoT Hub에 사용되는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/26/2021
ms.openlocfilehash: 6e4af6e4cadbd45e91219d2fce6d87c4fb555ece
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083337"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT Hub에 대한 보안 추천 사항

Defender for IoT는 Azure 리소스 및 IoT 디바이스를 검색하고, 보안 권장 사항을 제공하여 공격 노출 영역을 줄입니다.
보안 권장 사항은 실행 가능하며, 고객이 보안 모범 사례를 준수하도록 지원하는 것을 목표로 합니다.

이 문서에서는 IoT Hub에서 트리거할 수 있는 권장 사항 목록을 찾을 수 있습니다.

## <a name="built-in-recommendations-in-iot-hub"></a>IoT Hub의 기본 제공 권장 사항

권장 사항 경고는 환경의 보안 상태를 개선하기 위한 작업에 대한 인사이트 및 제안을 제공합니다.

| 심각도 | 이름 | 데이터 원본 | Description |
|--|--|--|--|
| 높음 | 동일한 인증 자격 증명이 여러 디바이스에서 사용됩니다. | IoT Hub | IoT Hub 인증 자격 증명이 여러 디바이스에서 사용됩니다. 이 프로세스는 합법적인 디바이스를 가장한 불법 디바이스를 표시할 수 있습니다. 중복 자격 증명 사용은 악의적인 행위자의 디바이스 가장 위험을 증가합니다. |
| 중간 | 기본 IP 필터 정책은 거부여야 합니다. | IoT Hub | IP 필터 구성에서는 허용된 트래픽에 대한 규칙이 정의되어야 하고, 기본값으로 다른 모든 트래픽을 거부해야 합니다. |
| 중간 | IP 필터 규칙은 큰 IP 범위를 포함합니다. | IoT Hub | IP 필터 허용 규칙의 원본 IP 범위가 너무 큽니다. 허용 범위가 과도하게 큰 규칙은 IoT 허브를 악의적인 행위자에게 노출할 수 있습니다. |
| 낮음 | IoT Hub에서 진단 로그 사용 | IoT Hub | 로그를 사용하도록 설정하고 최대 1년간 보존합니다. 로그를 보존하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
| 높음 | 여러 디바이스에서 사용하는 동일한 인증 자격 증명 | IoT Hub | IoT Hub 인증 자격 증명이 여러 디바이스에서 사용됩니다. 이는 불법적인 디바이스가 합법적인 디바이스를 가장하고 있으며 악의적인 행위자가 디바이스를 가장할 위험도 노출함을 나타낼 수 있습니다. |
| 높음 | IoT Edge 모듈용 IoT Edge 모델 쌍에 구성된 높은 수준의 권한 | IoT Hub | IoT Edge 모듈은 광범위한 Linux 기능 또는 호스트 수준 네트워크 액세스(호스트 컴퓨터에 데이터 보내기/받기)를 사용하여 권한 있는 모드로 실행되도록 구성됩니다. |
| 중간 | ACR 리포지토리와 함께 사용되지 않는 서비스 주체 | IoT Hub | ACR 리포지토리에서 IoT Edge 모듈을 끌어오는 데 사용되는 인증 스키마는 서비스 주체 인증을 사용하지 않습니다. |
| 중간 | TLS 암호 그룹 업그레이드 필요 | IoT Hub | 보안되지 않은 TLS 구성이 검색되었습니다. 즉시 TLS 암호 그룹 업그레이드를 권장합니다. |
| 중간 | 기본 IP 필터 정책은 거부여야 합니다. | IoT Hub | 기본적으로 IP 필터 구성에는 허용된 트래픽에 대해 정의된 규칙이 필요하며 다른 모든 트래픽을 거부해야 합니다. |
| 중간 | IP 필터 규칙에 넓은 IP 범위가 포함됨 | IoT Hub | 허용 가능한 IP 범위가 너무 큰 IP 필터 규칙 원본입니다. 지나치게 허용된 규칙은 악의적인 행위자에게 IoT Hub 노출할 수 있습니다. |
| 중간 | SecurityGroup에 일관되지 않은 모듈 설정이 있습니다. | IoT Hub | 이 디바이스 보안 그룹 내에서 변칙 디바이스는 나머지 보안 그룹과 비교할 때 모듈 설정이 일관되지 IoT Edge. |
| 낮음 | IoT Edge Hub 메모리를 최적화할 수 있습니다. | IoT Hub | 솔루션의 Edge 모듈에서 사용하지 않는 프로토콜에 대해 프로토콜 헤드를 해제하여 IoT Edge Hub 메모리 사용을 최적화합니다. |
| 낮음 | IoT Edge 모듈에 대해 구성된 로깅 없음 | IoT Hub | 이 IoT Edge 모듈에서는 로깅을 사용할 수 없습니다. |

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md)
- [보안 데이터에 액세스](how-to-security-data-access.md)하는 방법 알아보기
- [디바이스 조사](how-to-investigate-device.md)에 대한 자세한 정보
