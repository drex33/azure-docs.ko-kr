---
title: Azure Automation에서 확장 기반 Hybrid Runbook Worker 문제 해결
description: 이 문서에서는 Azure Automation 확장 기반 Hybrid Runbook Worker에서 발생 하는 문제를 해결 하는 방법을 설명 합니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 09/28/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 278f49ab45bd5ed65b39209f2ca81c0e3ff9ad30
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294373"
---
# <a name="troubleshoot-vm-extension-based-hybrid-runbook-worker-issues-in-automation"></a>Automation에서 VM 확장 기반 Hybrid Runbook Worker 문제 해결

이 문서에서는 Azure Automation 확장 기반 Hybrid Runbook Worker를 사용 하 여 문제를 해결 하 고 문제를 해결 하는 방법을 설명 합니다. 에이전트 기반 작업자에 대 한 문제 해결은 [Automation의 에이전트 기반 Hybrid Runbook Worker 문제 해결](./hybrid-runbook-worker.md)을 참조 하세요. 일반 정보는 [Hybrid Runbook Worker 개요](../automation-hybrid-runbook-worker.md)를 참조하세요.

## <a name="general-checklist"></a>일반 검사 목록

확장 기반 Hybrid Runbook Worker 관련 문제를 해결 하려면 다음을 수행 합니다.

- OS가 지원 되 고 필수 구성 요소가 충족 되었는지 확인 합니다.[필수 조건](../extension-based-hybrid-runbook-worker-install.md#prerequisites)을 참조하세요.

- 시스템 할당 관리 id가 VM에서 사용 되는지 여부를 확인 합니다. Azure Vm 및 Arc 사용 Azure 컴퓨터는 시스템 할당 관리 id로 설정 해야 합니다.

- 적절 한 설정으로 확장을 사용할 수 있는지 여부를 확인 합니다.설정 파일에는 권한이 있어야  `AutomationAccountURL` 합니다.Automation 계정 속성-을 사용 하 여 URL을 교차 확인  `AutomationHybridServiceUrl` 합니다.  
  - Windows의 경우:에서 설정 파일을 찾을 수 있습니다  `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin` .
  - Linux:에서 설정 파일을 찾을 수 있습니다  `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/` .

- Hybrid worker 확장 상태/세부 상태에 표시 된 오류 메시지를 확인 합니다.여기에는 오류 메시지 및 문제 해결을 위한 각 권장 사항이 포함 됩니다.

- VM에서 문제 해결사 도구를 실행 하면 출력 파일이 생성 됩니다. 출력 파일을 열고 문제 해결사 도구에서 확인 된 오류를 확인 합니다.
  - Windows의 경우:에서 문제 해결사를 찾을 수 있습니다 `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\TroubleShootWindowsExtension.ps1` .
  - Linux:에서 문제 해결사를 찾을 수 있습니다 `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/troubleshootLinuxExtension.py` .

- Linux 컴퓨터의 경우 사용자가 `hweautomation` 올바른 권한으로 설정 되어 있는지 확인 합니다.  

- Hybrid worker 프로세스가 실행 중인지 확인 합니다.
   - Windows: 서비스를 확인  `Hybrid Worker Service` 합니다.
   - Linux의 경우: 서비스를 확인  `hwd.` 합니다.

- 로그 수집기 도구를 실행 하 고 수집 된 로그를 검토 합니다.
   - Windows: 로그는에 있습니다 `C:\HybridWorkerExtensionLogs` . 도구는에 `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\PullLogs.ps1` 있습니다. 확장 로그는 `HybridWorkerExtensionHandler.log` 입니다. 작업자 로그는 `SME.log` 입니다.
   - Linux의 경우: 로그는에 있습니다 `/home/nxautomation/run` . 도구는에 `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/logcollector.py` 있습니다. 작업자 로그는 `worker.log` 입니다. 확장 등록 로그는 `register_log` 입니다. 확장 시작 로그는 `startup_log` 입니다. 확장 로그는 `extension_out` 입니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 사용해 보세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.