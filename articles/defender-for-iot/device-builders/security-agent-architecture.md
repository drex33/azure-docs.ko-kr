---
title: '빠른 시작: 보안 에이전트 개요'
description: 이 빠른 시작에서는 Microsoft Defender for IoT 서비스에서 사용되는 에이전트의 보안 에이전트 아키텍처를 이해하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 0fba4f592bdfa0ca055fcccb4808bb8d5dbd37b5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281763"
---
# <a name="quickstart-security-agent-reference-architecture"></a>빠른 시작: 보안 에이전트 참조 아키텍처

Microsoft Defender for IoT는 IoT Hub를 통해 보안 데이터를 기록, 처리, 집계 및 전송하는 보안 에이전트를 위한 참조 아키텍처를 제공합니다.

보안 에이전트는 제한된 IoT 환경에서 작동하도록 설계되었으며, 사용하는 리소스와 제공하는 가치를 비교했을 때 다양하게 사용자 지정할 수 있습니다.

보안 에이전트는 다음과 같은 기능을 지원합니다.

- 기존 디바이스 ID 또는 전용 모듈 ID로 인증합니다. 자세한 내용은  [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

- 기본 운영 체제(Linux, Windows)에서 원시 보안 이벤트를 수집합니다. 사용 가능한 보안 데이터 수집기에 대한 자세한 내용은 [Defender for IoT 에이전트 구성](how-to-agent-configuration.md)을 참조하세요.

- 원시 보안 이벤트를 IoT Hub를 통해 보낸 메시지로 집계합니다.

- **azureiotsecurity** 모듈 쌍을 사용하여 원격으로 구성합니다. 자세히 알아보려면 [Defender for IoT 에이전트 구성](how-to-agent-configuration.md)을 참조하세요.

Defender for IoT 보안 에이전트는 오픈 소스 프로젝트로 개발되었으며 GitHub에서 사용할 수 있습니다.

- [Defender for IoT C 기반 에이전트](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender for IoT C# 기반 에이전트](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="prerequisites"></a>필수 구성 요소

- 없음

## <a name="agent-supported-platforms"></a>에이전트 지원 플랫폼

Defender for IoT는 32비트 및 64비트 Windows에 대해 서로 다른 설치 관리자 에이전트를 제공하고, 32비트 및 64비트 Linux에 대해서는 같은 설치 관리자 에이전트를 제공합니다. 다음 표에 따라 각 디바이스에 대해 올바른 에이전트 설치 프로그램이 있는지 확인합니다.

| 아키텍처 | Linux | Windows | 세부 정보 |
|--|--|--|--|
| 32비트 | C | C# |  |
| 64비트 | C# 또는 C | C# | 디바이스 리소스가 더 제한되거나 최소한의 디바이스 리소스가 있는 디바이스에는 C 에이전트를 사용하는 것이 좋습니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 Defender for IoT Defender-IoT-micro-agent 아키텍처 및 사용 가능한 설치 프로그램에 대한 개략적인 개요를 제공합니다. Defender for IoT 배포를 계속 시작하려면 사용 가능한 보안 에이전트 인증 방법을 검토하세요.

> [!div class="nextstepaction"]
> [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)
