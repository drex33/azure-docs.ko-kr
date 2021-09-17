---
title: VM 인사이트 종속성 에이전트를 업그레이드하는 방법
description: 이 문서에서는 명령줄, 설치 마법사 및 기타 방법을 사용하여 VM 인사이트 종속성 에이전트를 업그레이드하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 282df705e8a98a7c236cfff549447cdd36017df8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431443"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>VM 인사이트 종속성 에이전트를 업그레이드하는 방법

VM 인사이트 종속성 에이전트의 초기 배포 후 버그 수정 또는 새로운 기능 지원을 포함하는 업데이트가 릴리스됩니다.  이 문서는 사용 가능한 방법 및 수동으로 또는 자동화를 통해 업그레이드를 수행하는 방법을 이해하는 데 도움이 됩니다.

## <a name="upgrade-options"></a>업그레이드 옵션 

Windows 및 Linux용 종속성 에이전트는 컴퓨터가 실행 중인 배포 시나리오 및 환경에 따라 수동으로 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 다음 메서드를 사용하여 에이전트를 업그레이드할 수 있습니다.

|Environment |설치 방법 |업그레이드 방법 |
|------------|--------------------|---------------|
|Azure VM | [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux용](../../virtual-machines/extensions/agent-dependency-linux.md) 종속성 에이전트 VM 확장 | *autoUpgradeMinorVersion* 속성을 **false로** 설정하여 옵트아웃하도록 Azure Resource Manager 템플릿을 구성하지 않으면 에이전트가 기본적으로 자동으로 업그레이드됩니다. 자동 업그레이드를 사용하지 않도록 설정되고 주 버전 업그레이드가 동일한 방법을 따르는 부 버전에 대한 업그레이드 - 확장을 제거하고 다시 설치합니다. |
| 사용자 지정 Azure VM 이미지 | Windows/Linux용 Dependency Agent 수동 설치 | VM을 최신 버전의 에이전트로 업데이트하려면 Windows 설치 관리자 패키지 또는 Linux 자동 압축 풀기 및 설치 가능한 셸 스크립트 번들을 실행하는 명령줄에서 수행해야 합니다.|
| 비 Azure VM | Windows/Linux용 Dependency Agent 수동 설치 | VM을 최신 버전의 에이전트로 업데이트하려면 Windows 설치 관리자 패키지 또는 Linux 자동 압축 풀기 및 설치 가능한 셸 스크립트 번들을 실행하는 명령줄에서 수행해야 합니다. |

## <a name="upgrade-windows-agent"></a>Windows 에이전트 업그레이드 

Windows VM의 에이전트를 종속성 에이전트 VM 확장을 사용하여 설치되지 않은 최신 버전으로 업데이트하려면 명령 프롬프트, 스크립트 또는 기타 자동화 솔루션에서 또는 InstallDependencyAgent-Windows.exe 설치 마법사 사용하여 를 실행합니다.  

여기에서 최신 버전의 Windows 에이전트를 다운로드할 수 [있습니다.](https://aka.ms/dependencyagentwindows)

### <a name="using-the-setup-wizard"></a>설치 마법사 사용

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **InstallDependencyAgent-Windows.exe** 실행하여 설치 마법사 시작합니다.
   
3. Dependency Agent **설치** 마법사에 따라 이전 버전의 종속성 에이전트를 제거한 다음 최신 버전을 설치합니다.


### <a name="from-the-command-line"></a>명령줄에서

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 다음 명령을 실행합니다.

    ```cmd
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual`매개 변수는 일부 프로세스가 이전 버전의 파일을 사용하고 잠금이 있는 경우 업그레이드가 컴퓨터를 자동으로 다시 부팅하지 못하도록 방지합니다. 

3. 업그레이드가 성공했는지 확인하려면 `install.log` 에서 자세한 설치 정보를 확인합니다. 로그 디렉터리는 *%Programfiles%\Microsoft Dependency Agent\logs* 입니다.

## <a name="upgrade-linux-agent"></a>Linux 에이전트 업그레이드 

이전 버전의 Linux 종속성 에이전트에서 업그레이드가 지원되며 새 설치와 동일한 명령에 따라 수행됩니다.

여기에서 최신 버전의 Linux 에이전트를 다운로드할 수 [있습니다.](https://aka.ms/dependencyagentlinux)

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 다음 명령을 루트로 실행합니다.

    ```bash
    InstallDependencyAgent-Linux64.bin -s
    ```

Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 */var/opt/microsoft/dependency-agent/log* 입니다. 

## <a name="next-steps"></a>다음 단계

일정 기간 동안 VM 모니터링을 중지하거나 VM 인사이트를 완전히 제거하려면 VM [인사이트에서 VM 모니터링 사용 안 함을 참조하세요.](../vm/vminsights-optout.md)
