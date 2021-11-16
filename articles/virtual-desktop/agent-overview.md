---
title: Azure Virtual Desktop 에이전트 시작
description: Azure Virtual Desktop 에이전트 및 업데이트 프로세스에 대한 개요입니다.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: ae07694e72f910945fbb213a448bfd910fef9786
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132483864"
---
# <a name="get-started-with-the-azure-virtual-desktop-agent"></a>Azure Virtual Desktop 에이전트 시작

Azure Virtual Desktop 서비스 프레임워크에는 원격 데스크톱 클라이언트, 서비스 및 가상 머신의 세 가지 주요 구성 요소가 있습니다. 해당 가상 머신은 Azure Virtual Desktop 에이전트 및 에이전트 부팅 로더가 설치된 고객 구독에 있습니다. 에이전트는 서비스와 가상 머신 간의 중간 커뮤니케이터 역할을 하며 연결을 사용하도록 설정합니다. 따라서 에이전트 설치, 업데이트 또는 구성에 문제가 발생하는 경우 가상 머신은 서비스에 연결할 수 없습니다. 에이전트 부팅 로더는 에이전트를 로드하는 실행 파일입니다. 

이 문서에서는 에이전트 설치 및 업데이트 프로세스에 대한 간략한 개요를 제공합니다.

>[!NOTE]
>이 설명서는 FSLogix 에이전트 또는 원격 데스크톱 클라이언트 에이전트에는 적합하지 않습니다.


## <a name="initial-installation-process"></a>초기 설치 프로세스

Azure Virtual Desktop 에이전트는 처음에 두 가지 방법 중 하나로 설치됩니다. Azure Portal 및 Azure Marketplace에서 VM(가상 머신)을 프로비저닝하는 경우 에이전트 및 에이전트 부팅 로더가 자동으로 설치됩니다. PowerShell을 사용하여 VM을 프로비저닝하는 경우 [PowerShell을 사용하여 Azure Virtual Desktop 호스트 풀을 만들](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool) 때 에이전트 및 에이전트 부팅 로더 .msi 파일을 수동으로 다운로드해야 합니다. 에이전트가 설치되면 Azure Virtual Desktop 병렬 스택 및 Geneva 모니터링 에이전트를 설치합니다. 병렬 스택 구성 요소는 사용자가 역방향 서버와 클라이언트 간 연결을 안전하게 설정하는 데 필요합니다. Geneva 모니터링 에이전트는 에이전트의 상태를 모니터링합니다. 이러한 세 가지 구성 요소는 모두 엔드투엔드 사용자 연결이 제대로 작동하는 데 필요합니다.

>[!IMPORTANT]
>Azure Virtual Desktop 에이전트, 병렬 스택 및 Geneva 모니터링 에이전트를 성공적으로 설치하려면 [필요한 URL 목록](safe-url-list.md#virtual-machines)에 나열된 모든 URL을 차단 해제해야 합니다. Azure Virtual Desktop 서비스를 사용하려면 해당 URL의 차단을 해제해야 합니다.

## <a name="agent-update-process"></a>에이전트 업데이트 프로세스

Azure Virtual Desktop 서비스는 업데이트를 사용할 수 있을 때마다 에이전트를 업데이트합니다. 에이전트 업데이트에는 새로운 기능이나 이전 문제에 대한 수정 사항이 포함될 수 있습니다. VM의 연결 또는 보안이 손실되지 않도록 항상 안정적인 최신 버전의 에이전트가 설치되어 있어야 합니다. Azure Virtual Desktop 에이전트의 초기 버전이 설치되면 에이전트는 Azure Virtual Desktop 서비스를 정기적으로 쿼리하여 최신 버전의 에이전트, 스택 또는 모니터링 구성 요소를 사용할 수 있는지 확인합니다. 최신 버전의 구성 요소를 이미 배포한 경우 업데이트된 구성 요소는 플라이팅 시스템에 의해 자동으로 설치됩니다.

에이전트의 새 버전은 5일 기간 중 모든 Azure 구독에 정기적으로 배포됩니다. 해당 업데이트 기간을 “플라이트”라고 합니다. 단일 브로커 지역의 모든 VM이 플라이트의 에이전트 업데이트를 수신하는 데는 24시간이 걸립니다. 이로 인해 플라이트가 발생하면 호스트 풀의 VM이 서로 다른 시간에 에이전트 업데이트를 수신하는 것을 볼 수 있습니다. 또한 VM이 다른 지역에 있는 경우 5일 기간 중 다른 날에 업데이트될 수 있습니다. 플라이트는 모든 구독의 모든 VM 에이전트를 배포 기간이 끝날 때까지 업데이트합니다. Azure Virtual Desktop 플라이팅 시스템은 에이전트 업데이트의 안정성 및 품질을 보장하여 서비스의 안정성을 향상합니다.


다음은 유의해야 할 다른 중요한 사항입니다.

- 에이전트 업데이트가 Azure Virtual Desktop 인프라 빌드 업데이트에 연결되지 않았습니다. Azure Virtual Desktop 인프라가 업데이트되는 경우 에이전트가 함께 업데이트되었다는 의미는 아닙니다.
- 호스트 풀의 VM은 서로 다른 시간에 에이전트 업데이트를 수신할 수 있으므로, 플라이팅 문제와 실패한 에이전트 업데이트 간의 차이점을 알 수 있어야 합니다. **이벤트 뷰어**  >  **Windows 로그** > **애플리케이션** 에서 VM에 대한 이벤트 로그로 이동하여 "ID 3277" 레이블이 지정된 이벤트가 있으면 에이전트 업데이트가 작동하지 않았음을 의미합니다. 해당 이벤트가 표시되지 않으면 VM이 다른 플라이트에 있으며 나중에 업데이트 됩니다.
- Geneva 모니터링 에이전트가 최신 버전으로 업데이트되면 새 모니터링 에이전트에 대한 새 작업을 만들기 전에 이전 GenevaTask 작업을 찾아 사용하지 않도록 설정합니다. 최신 버전의 모니터링 에이전트에 문제가 있어 해결하려면 이전 버전으로 되돌려야 하는 경우 이전 버전의 모니터링 에이전트가 삭제되지 않습니다. 최신 버전에 문제가 있는 경우 모니터링 데이터를 계속 전달하기 위해 이전 모니터링 에이전트가 다시 사용하도록 설정됩니다. 업데이트 전에 마지막으로 설치한 모니터보다 이전 버전의 모든 모니터는 VM에서 삭제됩니다.
- VM은 한 번에 세 가지 버전의 에이전트와 side-by-side 스택을 유지합니다. 이렇게 하면 업데이트에 문제가 발생할 경우 빠른 복구를 수행할 수 있습니다. 에이전트 또는 스택이 업데이트될 때마다 가장 오래된 버전의 에이전트 또는 스택이 VM에서 제거됩니다. 이러한 구성 요소를 조기에 삭제하고 에이전트 또는 스택에 오류가 발생하면 에이전트 또는 스택이 이전 버전으로 롤백할 수 없어 VM을 사용할 수 없는 상태가 됩니다.

에이전트 업데이트는 일반적으로 새 VM에서 2-3분 동안 지속되며 VM의 연결이 끊어지거나 종료되어서는 안 됩니다. 이 업데이트 프로세스는 Azure Virtual Desktop(클래식) 및 Azure Resource Manager를 사용하는 최신 버전의 Azure Virtual Desktop에 적용됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Virtual Desktop 에이전트에 대해 더 잘 이해했습니다. 다음은 도움이 될 수 있는 몇 가지 리소스입니다.

- 에이전트 또는 연결 관련 문제가 발생하는 경우 [Azure Virtual Desktop 에이전트 문제 해결 가이드](troubleshoot-agent.md)를 확인하세요.
