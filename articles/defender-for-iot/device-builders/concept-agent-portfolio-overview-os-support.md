---
title: 에이전트 포트폴리오 개요 및 OS 지원(미리 보기)
description: Azure Defender for IoT는 디바이스 유형을 기반으로 하는 에이전트의 많은 포트폴리오를 제공합니다.
ms.date: 10/03/2021
ms.topic: conceptual
ms.openlocfilehash: 9cd87f9d332ca56da57a823886fa0ebdb93011fa
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399568"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>에이전트 포트폴리오 개요 및 OS 지원(미리 보기)

Azure Defender for IoT는 디바이스 유형을 기반으로 하는 에이전트의 많은 포트폴리오를 제공합니다.

## <a name="standalone-agent"></a>독립 실행형 에이전트

독립 실행형 에이전트는 대부분의 Linux OS(운영 체제)를 포함하며, 이를 이진 패키지로 배포하거나 펌웨어의 일부로 통합될 수 있는 소스 코드로 배포하고 고객의 요구에 따라 수정 및 사용자 지정할 수 있습니다. 다음은 지원되는 OS의 몇 가지 예입니다.

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
|--|--|--|--|
| Debian 9 | ✓ | ✓ | |
| Ubuntu 18.04 | ✓ |  | ✓ |
| Ubuntu 20.04 | ✓ |  | |

마이크로 에이전트 운영 체제에 대한 보다 세부적인 보기는 [Linux 의존성 을 참조하세요.](concept-micro-agent-linux-dependencies.md#linux-dependencies)

추가 정보, 지원되는 운영 체제 또는 디바이스 펌웨어의 일부로 통합할 수 있도록 소스 코드에 대한 액세스를 요청하려면 계정 관리자에게 문의하거나 으로 메일을 <defender_micro_agent@microsoft.com> 보냅니다.

## <a name="azure-rtos-micro-agent"></a>Azure RTOS 마이크로 에이전트

Azure Defender for IoT 마이크로 에이전트는 Azure RTOS를 사용하는 디바이스에 대한 포괄적이고 간단한 보안 솔루션을 제공합니다. Azure Defender for IoT 마이크로 에이전트는 일반적인 위협에 대한 범위 및 RTOS(실시간 운영 체제) 디바이스에서 잠재적 악성 작업을 제공합니다. 마이크로 에이전트는 Azure RTOS NetX 듀어 구성 요소의 일부로 기본 제공되며 디바이스의 네트워크 작업을 모니터링합니다.

Azure Defender for IoT 마이크로 에이전트는 Azure RTOS NetX 듀어 구성 요소의 일부로 기본 제공되며 디바이스의 네트워크 작업을 모니터링합니다. 마이크로 에이전트는 일반 위협에 대한 범위 및 RTOS(실시간 운영 체제) 디바이스에서 잠재적 악성 작업을 제공하는 포괄적이고 간단한 보안 솔루션으로 구성됩니다.

## <a name="next-steps"></a>다음 단계

[Micro agent Linux dependencies(미리 보기)에](concept-micro-agent-linux-dependencies.md)대해 자세히 알아보세요.

[독립 실행형 마이크로 에이전트 개요(미리 보기)](concept-standalone-micro-agent-overview.md)에 대해 자세히 알아보세요.
