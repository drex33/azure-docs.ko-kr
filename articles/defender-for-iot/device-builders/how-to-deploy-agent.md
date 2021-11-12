---
title: 보안 에이전트 선택 및 배포
description: IoT 디바이스에서 Defender for IoT 보안 에이전트를 선택하고 배포하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 25a879432ff6e20a4f2cda83c9658c79cb4fb0bb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306190"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>IoT 디바이스에서 보안 에이전트 선택 및 배포

Defender for IoT는 IoT 디바이스에서 데이터를 모니터링하고 수집하는 보안 에이전트에 대한 참조 아키텍처를 제공합니다.
자세히 알아보려면 [보안 에이전트 참조 아키텍처](security-agent-architecture.md)를 참조하세요.

에이전트는 오픈 소스 프로젝트로 개발되었으며 <br> [C](https://aka.ms/iot-security-github-c) 및 [C#](https://aka.ms/iot-security-github-cs)의 두 가지 버전으로 제공됩니다.

이 문서에서는 다음 방법을 설명합니다.
- 보안 에이전트 버전 비교
- 지원되는 에이전트 플랫폼 검색
- 솔루션에 적합한 에이전트 버전 선택

## <a name="understand-security-agent-options"></a>보안 에이전트 옵션 이해

모든 Defender for IoT 보안 에이전트 버전은 동일한 기능 세트를 제공하고 유사한 구성 옵션을 지원합니다.

C 기반 보안 에이전트는 메모리 공간이 적으며, 사용 가능한 리소스가 적은 디바이스에 적합합니다.

|     | C 기반 보안 에이전트 | C# 기반 보안 에이전트 |
| --- | ----------- | --------- |
| **오픈 소스** | [GitHub](https://aka.ms/iot-security-github-c)의 [MIT 라이선스](https://en.wikipedia.org/wiki/MIT_License)에서 사용 가능 | [GitHub](https://aka.ms/iot-security-github-cs)의 [MIT 라이선스](https://en.wikipedia.org/wiki/MIT_License)에서 사용 가능 |
| **개발 언어**    | C | C# |
| **지원되는 Windows 플랫폼인가요?** | 예 | 예 |
| **Windows 필수 구성 요소** | --- | [WMI](/windows/desktop/wmisdk/) |
| **지원되는 Linux 플랫폼인가요?** | 예, x64 및 x86 | 예, x64만 해당 |
| **Linux 필수 구성 요소** | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| **디스크 공간** | 10.5MB | 90MB |
| **메모리 공간(평균)** | 5.5MB | 33MB |
| **IoT Hub에 대한 [인증](concept-security-agent-authentication-methods.md)** | 예 | 예 |
| **보안 데이터 [수집](how-to-agent-configuration.md#supported-security-events)** | 예 | 예 |
| **이벤트 집계** | 예 | 예 |
| **[Defender-IoT-micro-agent 쌍](concept-security-module.md)을 통한 원격 구성** | 예 | 예 |

## <a name="security-agent-installation-guidelines"></a>보안 에이전트 설치 지침

**Windows** 의 경우: Install SecurityAgent.ps1 스크립트는 관리자 PowerShell 창에서 실행해야 합니다.

**Linux** 의 경우: InstallSecurityAgent.sh는 수퍼 사용자로 실행해야 합니다. 설치 명령에 "sudo"를 접두사로 지정하는 것이 좋습니다.

## <a name="choose-an-agent-flavor"></a>에이전트 버전 선택

IoT 디바이스에 대한 다음 질문에 답하여 올바른 에이전트를 선택합니다.

- _Windows Server_ 또는 _Windows IoT Core_ 를 사용하고 있나요?

    [Windows용 C# 기반 보안 에이전트를 배포](how-to-deploy-windows-cs.md)합니다.

- x86 아키텍처에서 Linux 배포를 사용하고 있나요?

    [Linux용 C# 기반 보안 에이전트를 배포](how-to-deploy-linux-c.md)합니다.

- x64 아키텍처에서 Linux 배포를 사용하고 있나요?

    두 가지 에이전트 버전을 모두 사용할 수 있습니다. <br>
    [Linux용 C 기반 보안 에이전트 배포](how-to-deploy-linux-c.md) 및/또는 [Linux용 C# 기반 보안 에이전트 배포](how-to-deploy-linux-cs.md).

두 가지 에이전트 버전 모두 동일한 기능 세트를 제공하고 유사한 구성 옵션을 지원합니다.
자세히 알아보려면 [보안 에이전트 비교](how-to-deploy-agent.md#understand-security-agent-options)를 참조하세요.

## <a name="supported-platforms"></a>지원 플랫폼

다음 목록에는 현재 지원되는 모든 플랫폼이 포함되어 있습니다.

|Defender for IoT 에이전트 |운영 체제 |아키텍처 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    X64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |X64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |X64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, 빌드 17763    |X64|
|

## <a name="next-steps"></a>다음 단계

구성 옵션에 대해 자세히 알아보려면 에이전트 구성 방법 가이드를 참조하세요.
> [!div class="nextstepaction"]
> [에이전트 구성 방법 가이드](./how-to-agent-configuration.md)
