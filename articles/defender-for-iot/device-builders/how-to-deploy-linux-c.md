---
title: Linux C 에이전트 설치 및 배포
description: IoT용 Defender C 기반 보안 에이전트를 Linux에 설치하고 배포하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 46677a971165c65440310e21933f586cc685b771
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584871"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Linux용 Defender for IoT C 기반 보안 에이전트 배포

이 가이드에서는 Linux에 IoT용 Defender C 기반 보안 에이전트를 설치하고 배포하는 방법을 설명합니다.

- 설치
- 배포 확인
- 에이전트 제거
- 문제 해결

## <a name="prerequisites"></a>필수 구성 요소

다른 플랫폼과 에이전트 버전은 [적합한 보안 에이전트 선택](how-to-deploy-agent.md)을 참조하세요.

1. 보안 에이전트를 배포하려면 설치하려는 머신에 대한 로컬 관리자 권한이 필요합니다 (sudo).

1. 디바이스에 대한 [Defender-IoT-micro-agent를 생성합니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치

보안 에이전트를 설치 및 배포하려면 다음 워크플로를 사용합니다.

1. [GitHub](https://aka.ms/iot-security-github-c)에서 최신 버전을 머신에 다운로드합니다.

1. 패키지의 콘텐츠를 추출하고 _/src/installation_ 폴더로 이동합니다.

1. 다음 명령을 실행하여 **InstallSecurityAgent 스크립트** 에 실행 권한을 추가합니다.

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 다음으로, 다음을 실행합니다.

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   인증 매개 변수에 대한 자세한 내용은 [인증 구성 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

이 스크립트는 다음 기능을 수행합니다:

1. 필수 구성 요소를 설치합니다.

1. (대화형 서명을 사용하지 않는) 설정된 서비스 사용자를 추가합니다.

1. 에이전트를 **디먼** 으로 설치합니다. 여기서는 서비스 관리를 위해 디바이스에서 **systemd** 를 사용한다고 가정합니다.

1. 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다.

추가 도움말을 보려면 -help 매개 변수를 사용하여 스크립트를 실행합니다.

`./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거하려면 --u 매개 변수를 사용하여 스크립트를 실행합니다.

`./InstallSecurityAgent.sh -–uninstall`

## <a name="troubleshooting"></a>문제 해결

다음을 실행하여 배포 상태를 확인합니다.

`systemctl status ASCIoTAgent.service`

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md) 읽기
- Defender for IoT에 대한 자세한 내용 [디바이스 작성기용 에이전트 기반 솔루션](architecture-agent-based.md)
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [Azure Defender for IoT 에이전트 질문과 대답](resources-agent-frequently-asked-questions.md)을 참조하세요.
- [보안 경고](concept-security-alerts.md)를 살펴봅니다.
