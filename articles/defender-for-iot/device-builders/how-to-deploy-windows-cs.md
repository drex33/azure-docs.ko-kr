---
title: Windows 디바이스에 C# 에이전트 설치
description: 32비트 또는 64비트 Windows 디바이스에 Defender for IoT 에이전트를 설치하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: f76d9122b4f50d475efbbac465096022261a9ef4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018111"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Windows용 Defender for IoT C# 기반 보안 에이전트 배포

이 가이드에서는 Defender for IoT C# 기반 보안 에이전트를 Windows에 설치하는 방법에 대해 설명합니다.

이 가이드에서는 다음 작업 방법을 배웁니다.

- 설치
- 배포 확인
- 에이전트 제거
- 문제 해결

## <a name="prerequisites"></a>필수 구성 요소

다른 플랫폼과 에이전트 버전은 [적합한 보안 에이전트 선택](how-to-deploy-agent.md)을 참조하세요.

1. 설치하려는 머신에 대한 로컬 관리자 권한.

1. 디바이스에 대한 [Defender-IoT-micro-agent를 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치

보안 에이전트를 설치하려면 다음 워크플로를 사용합니다.

1. 디바이스에 Defender for IoT Windows C# 에이전트를 설치합니다. Defender for IoT [GitHub 리포지토리](https://github.com/Azure/Azure-IoT-Security-Agent-CS)에서 최신 버전을 머신에 다운로드합니다.

1. 패키지의 콘텐츠를 추출하고 /Install 폴더로 이동합니다.

1. 관리자 권한으로 Windows PowerShell을 엽니다.
1. 다음을 실행하여 InstallSecurityAgent 스크립트에 실행 권한을 추가합니다.

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    이어서 다음을 실행합니다.

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    예를 들면 다음과 같습니다.

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    인증 매개 변수에 대한 자세한 내용은 [인증 구성 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

이 스크립트에서 수행하는 작업은 다음과 같습니다.

* 필수 구성 요소를 설치합니다.
* 대화형 로그인이 비활성화된 서비스 사용자를 추가합니다.
* 에이전트를 **시스템 서비스** 로 설치합니다.
* 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다.

추가 도움말을 보려면 PowerShell에서 Get-Help 명령을 사용합니다.

Get-Help 예제: ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>배포 상태 확인

다음을 실행하여 에이전트 배포 상태를 확인합니다.

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거하려면 다음을 수행합니다.

1. **-mode** 매개 변수를 **Uninstall** 로 설정한 상태에서 다음 PowerShell 스크립트를 실행합니다.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>문제 해결

에이전트가 시작되지 않는 경우 자세한 정보를 얻으려면 로깅을 설정합니다(기본적으로 로깅은 *해제* 임).

로깅을 설정하려면 다음을 수행합니다.

1. 표준 파일 편집기를 사용하여 편집할 구성 파일(General.config)을 엽니다.

1. 다음 값을 편집합니다.

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 문제가 해결되면 로깅을 **해제** 하는 것이 좋습니다. 로깅을 **설정** 으로 그대로 두면 로그 파일 크기와 데이터 사용량이 증가합니다.

1. 다음 PowerShell 또는 명령줄을 실행하여 에이전트를 다시 시작합니다.

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   또는

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. 오류에 대한 자세한 내용을 확인하려면 로그 파일을 검토합니다. 로그 파일은 스크립트를 실행하는 작업 디렉터리에 표시됩니다. 

   로그 파일 위치: `.\IoTAgentLog.log`

## <a name="next-steps"></a>다음 단계

* Defender for IoT 서비스 [개요](overview.md) 읽기
* Defender for IoT에 대한 자세한 내용 [디바이스 작성기용 에이전트 기반 솔루션](architecture-agent-based.md)
* [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
* [Azure Defender for IoT 에이전트 질문과 대답](resources-agent-frequently-asked-questions.md) 읽기
* [경고](concept-security-alerts.md)를 살펴봅니다.
