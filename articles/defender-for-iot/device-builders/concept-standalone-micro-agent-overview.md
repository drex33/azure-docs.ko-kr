---
title: 독립 실행형 마이크로 에이전트 개요(미리 보기)
description: Azure Defender for IoT 보안 에이전트를 사용하면 새 IoT 디바이스 및 Azure IoT 프로젝트에 직접 보안을 구축할 수 있습니다.
ms.date: 11/04/2021
ms.topic: article
ms.openlocfilehash: 2dfdc4b1d3696a3887e251d6ccd3213f5bc2bc0c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577076"
---
# <a name="standalone-micro-agent-overview-preview"></a>독립 실행형 마이크로 에이전트 개요(미리 보기)

보안은 IoT를 구현하는 거의 모든 사람이 고민하는 문제입니다. IoT 디바이스는 엔드포인트 모니터링, 보안 상태 관리 및 위협 탐지에 대한 고유한 요구 사항이 있으며, 각각 매우 구체적인 성능 요구 사항이 있습니다.

Azure Defender for IoT 보안 에이전트를 사용하면 새 IoT 디바이스 및 Azure IoT 프로젝트에 직접 보안을 구축할 수 있습니다. 마이크로 에이전트는 이진 패키지로 배포하거나 소스 코드를 수정하는 기능을 비롯한 유연한 배포 옵션을 제공합니다. 그리고 마이크로 에이전트는 Linux나 Azure RTOS 같은 표준 IoT 운영 체제에 사용할 수 있습니다.

Azure Defender for IoT 마이크로 에이전트는 보안 상태 관리, 위협 탐지 및 통합 보안 관리를 위한 기타 Microsoft 보안 도구와의 통합에 대한 엔드포인트 가시성을 제공합니다.

## <a name="security-posture-management"></a>보안 상태 관리

IoT 디바이스의 보안 상태를 선제적으로 모니터링합니다. Azure Defender for IoT는 디바이스 관련 권장 사항과 함께 CIS 벤치마크를 기반으로 보안 상태 권장 사항을 제공합니다. OS 구성, 방화벽 구성 및 사용 권한을 비롯한 운영 체제 보안에 대한 가시성을 제공합니다.

## <a name="endpoint-iot-and-ot-threat-detection"></a>엔드포인트 IoT 및 OT 위협 탐지

봇네트, 무차별 암호 대입 시도, 크립토 마이너, 의심스러운 네트워크 활동 등의 위협을 탐지합니다. 조직 내에서 가장 중요한 위협을 대상으로 하는 사용자 지정 경고를 만듭니다.

## <a name="flexible-distribution-and-deployment-models"></a>유연한 배급 및 배포 모델

Azure Defender for IoT 마이크로 에이전트에 소스 코드가 포함되어 있으므로 마이크로 에이전트를 펌웨어에 통합하거나 필요한 것만 포함하도록 사용자 지정할 수 있습니다. 마이크로 에이전트는 이진 패키지로 사용하거나 다른 Azure IoT 솔루션에 직접 통합할 수도 있습니다.

## <a name="meets-the-needs-of-your-iot-devices-with-minimal-impact"></a>최소한의 영향으로 IoT 디바이스의 요구 사항 충족

Azure Defender for IoT 마이크로 에이전트는 쉽게 배포할 수 있으며 엔드포인트에 대한 성능 영향은 최소화합니다. Defender for IoT 마이크로 에이전트로 다음을 수행할 수 있습니다.

- **성능 최적화**: Azure Defender for IoT 마이크로 에이전트는 메모리 공간을 적게 차지하며 CPU 사용량이 적습니다.  

- **플러그 앤 플레이**: 특정 OS 커널 종속성이 없거나 모든 주요 IoT 운영 체제에 대한 지원이 필요합니다. Azure Defender for IoT 마이크로 에이전트는 디바이스의 위치와 일치합니다.

- **유연한 배포** : 독립 실행형 에이전트인 Azure Defender for IoT의 마이크로 에이전트는 다양한 배포 모델과 유연한 배포를 지원합니다.

## <a name="data-processing-and-residency"></a>데이터 처리 및 상주

> [!Note]
> Azure Defender for IoT 데이터 처리 및 상주는 IoT Hub 지역과 다른 지역에서 발생할 수 있습니다. Azure Defender for IoT 보안 검색 논리의 일부로 디바이스 쌍, 마스크되지 않은 IP 및 추가 구성 데이터를 사용합니다.

Azure Defender for IoT 데이터 처리 및 상주는 IoT Hub 지역과 다른 지역에서 발생할 수 있습니다. IoT Hub 지역과 Azure Defender for IoT 영역 간의 매핑은 다음과 같습니다.

- 유럽 에 있는 허브의 경우 데이터는 *서유럽* 지역에 저장됩니다.

- 유럽 외부에 있는 허브의 경우 데이터는 *미국 동부* 지역에 저장됩니다.

Azure Defender for IoT 는 기본적으로 디바이스 쌍, 마스크되지 않은 IP 주소 및 추가 구성 데이터를 보안 검색 논리의 일부로 사용합니다. 디바이스 쌍을 사용하지 않도록 설정하고 IP 주소 컬렉션의 마스크를 해제하려면 데이터 컬렉션의 설정 페이지로 이동합니다.

:::image type="content" source="media/data-collection-settings.png" alt-text="데이터 컬렉션 설정 페이지의 스크린샷.":::

자세한 내용은 [Defender for IoT 솔루션을 사용자 지정하는 방법을 참조하세요.](concept-micro-agent-configuration.md)

## <a name="next-steps"></a>다음 단계

[마이크로 에이전트 인증 방법(미리 보기)](concept-security-agent-authentication.md)을 확인합니다.
