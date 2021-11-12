---
title: Azure Automation 확장 기반 Hybrid Runbook Worker 문제 해결
description: 이 문서에서는 Azure Automation 확장 기반 Hybrid Runbook Workers에서 발생하는 문제를 해결하고 해결하는 방법을 알려줍니다.
services: automation
author: mgoedtel
ms.author: v-ssudhir
ms.date: 09/28/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34cefa5929b2d77e3acffa1248647eef7a428e9e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400588"
---
# <a name="troubleshoot-vm-extension-based-hybrid-runbook-worker-issues-in-automation"></a>Automation에서 VM 확장 기반 Hybrid Runbook Worker 문제 해결

이 문서에서는 Azure Automation 확장 기반 Hybrid Runbook Workers의 문제 해결 및 해결에 대한 정보를 제공합니다. 에이전트 기반 작업자 문제 해결은 [Automation에서 에이전트 기반 Hybrid Runbook Worker 문제 해결을 참조하세요.](./hybrid-runbook-worker.md) 일반 정보는 [Hybrid Runbook Worker 개요](../automation-hybrid-runbook-worker.md)를 참조하세요.

## <a name="general-checklist"></a>일반 검사 목록

확장 기반 Hybrid Runbook Workers 문제를 해결하려면 다음을 수행합니다.

- OS가 지원되고 필수 구성 조건이 충족되었는지 확인합니다.[필수 조건](../extension-based-hybrid-runbook-worker-install.md#prerequisites)을 참조하세요.

- VM에서 시스템 할당 관리 ID를 사용할 수 있는지 확인합니다. 시스템 할당 관리 ID를 사용하여 Azure VM 및 Arc 지원 Azure Machines를 사용하도록 설정해야 합니다.

- 올바른 설정으로 확장을 사용할 수 있는지 확인합니다.설정 파일에는 올바른 가 있어야  `AutomationAccountURL` 합니다.Automation 계정 속성을 통해 URL을 교차  `AutomationHybridServiceUrl` 확인합니다( ).  
  - 창의 경우: 에서 설정 파일을 찾을 수  `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin` 있습니다.
  - Linux의 경우: 에서 설정 파일을 찾을 수  `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/` 있습니다.

- Hybrid Worker 확장 상태/세부 상태에 표시된 오류 메시지를 확인합니다.이 파일에는 문제를 해결하기 위한 오류 메시지와 해당 권장 사항이 포함되어 있습니다.

- VM에서 문제 해결사 도구를 실행하면 출력 파일이 생성됩니다. 출력 파일을 열고 문제 해결사 도구로 식별된 오류를 확인합니다.
  - 창의 경우: 에서 문제 해결사를 찾을 수 `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\TroubleShootWindowsExtension.ps1` 있습니다.
  - Linux의 경우: 에서 문제 해결사를 찾을 수 `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/troubleshootLinuxExtension.py` 있습니다.

- Linux 머신의 경우 사용자가 `hweautomation` 올바른 권한으로 설정되었는지 확인합니다.  

- Hybrid Worker 프로세스가 실행 중인지 확인합니다.
   - Windows:  `Hybrid Worker Service` 서비스를 확인합니다.
   - Linux의 경우:  `hwd.` 서비스를 확인합니다.

- 로그 수집기 도구를 실행하고 수집된 로그를 검토합니다.
   - Windows 경우 로그는 에 `C:\HybridWorkerExtensionLogs` 있습니다. 도구는 에 `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\PullLogs.ps1` 있습니다. 확장 로그는 `HybridWorkerExtensionHandler.log` 입니다. 작업자 로그는 `SME.log` 입니다.
   - Linux의 경우: 로그는 에 `/home/nxautomation/run` 있습니다. 도구는 에 `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/logcollector.py` 있습니다. 작업자 로그는 `worker.log` 입니다. 확장 등록 로그는 `register_log` 입니다. 확장 시작 로그는 `startup_log` 입니다. 확장 로그는 `extension_out` 입니다.

## <a name="next-steps"></a>다음 단계

여기에서 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 시도하여 추가 지원을 요청하세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.