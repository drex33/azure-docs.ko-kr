---
title: 디바이스 빌더용 Azure Defender for IoT의 새로운 기능
description: 최신 릴리스와 Defender for IoT 디바이스 빌더의 최신 기능에 대해 알아보세요.
ms.topic: overview
ms.date: 11/02/2021
ms.openlocfilehash: 4d6d931082f55a08667da20283d28c655a9fb584
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438591"
---
# <a name="whats-new-in-azure-defender-for-iot-for-device-builders"></a>디바이스 빌더용 Azure Defender for IoT의 새로운 기능은 무엇인가요?  

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

이 문서에는 Defender for IoT의 새로운 기능과 향상된 기능이 나와 있습니다.

언급된 기능은 미리 보기에 있습니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 기타 법률 용어가 포함되어 있습니다.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Azure Defender for IoT 버전 관리 및 지원

다음에는 Defender for IoT의 지원, 주요 변경 정책, 현재 사용할 수 있는 Azure Defender for IoT의 버전이 나와 있습니다.

## <a name="september-2021"></a>2021년 9월

**버전 3.11**:

- **[로그인 수집기](concept-event-aggregation.md#login-collector-event-based-collector)** - 로그인 수집기는 사용자 로그인, 로그아웃 및 실패한 로그인 시도를 수집합니다. SSH 및 텔넷을 예로 들 수 있습니다.

- **[시스템 정보 수집기](concept-event-aggregation.md#system-information-trigger-based-collector)** - 시스템 정보 수집기는 디바이스의 운영 체제 관련 정보와 하드웨어 세부 정보를 수집합니다.

- **[이벤트 집계](concept-event-aggregation.md#how-does-event-aggregation-work)** - Defender for IoT 에이전트는 디바이스의 보안을 유지하면서도 전송되는 메시지 수와 비용을 줄이는 프로세스, 로그인, 네트워크 이벤트 등의 이벤트를 집계합니다.  

- **[트윈 구성](concept-micro-agent-configuration.md)** - 마이크로 에이전트의 동작은 모듈 쌍 속성 세트를 통해 구성됩니다 (예: 이벤트 전송 빈도 및 집계 모드). 요구 사항에 가장 적합하도록 마이크로 에이전트를 구성할 수 있습니다.

## <a name="march-2021"></a>2021년 3월

### <a name="device-builder---new-micro-agent-public-preview"></a>디바이스 빌더 - 새 마이크로 에이전트(공개 미리 보기)

새 디바이스 빌더 모듈을 사용할 수 있습니다. 마이크로 에이전트라고 하는 모듈에서 수행할 수 있는 기능은 다음과 같습니다.

- **Azure IoT Hub 및 Azure Defender for IoT와 통합** - Azure IoT Hub 및 Azure Defender for IoT에서 제공하는 모니터링 옵션과 통합하여 더 강력한 엔드포인트 보안을 IoT 디바이스에 직접 빌드합니다.

- **표준 IoT 운영 체제를 지원하는 유연한 배포 옵션** - Linux 및 Azure RTOS와 같은 표준 IoT 운영 체제를 지원하여 이진 패키지 또는 수정 가능한 소스 코드로 배포할 수 있습니다.

- **OS 커널 종속성이 없는 최소 리소스 요구 사항** - 공간이 작고, CPU 사용량이 낮으며, OS 커널 종속성이 없습니다.

- **보안 상태 관리** – IoT 디바이스의 보안 상태를 사전에 모니터링합니다.

- **연속 실시간 IoT/OT 위협 탐지** - 봇네트, 무차별 암호 대입 시도, 암호화 마이너 및 의심스러운 네트워크 활동과 같은 위협을 탐지합니다.

더 이상 사용되지 않는 Defender-IoT-micro-agent 설명서는 *디바이스 빌더용 에이전트 기반 솔루션 > 클래식* 폴더로 이동됩니다.

이 기능 세트는 현재 공개 미리 보기 클라우드 릴리스에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[디바이스 빌더를 위한 에이전트 기반 솔루션이란](architecture-agent-based.md)
