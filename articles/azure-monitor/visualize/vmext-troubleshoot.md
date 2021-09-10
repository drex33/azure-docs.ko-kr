---
title: Azure Log Analytics VM 확장 문제 해결
description: Windows 및 Linux Azure VM에 대한 Log Analytics VM 확장에서 발생하는 가장 일반적인 문제에 대한 증상, 원인 및 해결 방법에 대해 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 80cec6ad233a6f6dc4771e82a98601f5db774b0f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529971"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Azure Monitor에서 Log Analytics VM 확장 문제 해결
이 문서는 Microsoft Azure에서 실행되는 Windows 및 Linux 가상 머신에 대한 Log Analytics VM 확장에서 발생할 수 있는 오류를 해결하는 데 도움을 주며, 가능한 해결 방법을 제안합니다.

확장의 상태를 확인하려면 Azure Portal에서 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal에서 **모든 서비스** 를 클릭합니다. 리소스 목록에 **가상 머신** 을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **가상 머신** 을 선택합니다.
3. 가상 머신 목록에서 해당 가상 머신을 찾아 선택합니다.
3. 가상 머신에서 **확장** 을 클릭합니다.
4. 목록에서 Log Analytics가 사용되도록 설정되어 있는지 여부를 확인합니다.  Linux의 경우 에이전트가 **OMSAgentforLinux** 로 표시되고, Windows의 경우에는 에이전트가 **MicrosoftMonitoringAgent** 로 표시됩니다.

   ![VM 확장 보기](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. 확장을 클릭하여 세부 정보를 확인합니다. 

   ![VM 확장 세부 정보](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM 확장 문제 해결

*Microsoft Monitoring Agent* VM 확장이 설치되지 않거나 문제를 보고할 경우 다음 절차를 수행하여 문제를 해결할 수 있습니다.

1. [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)의 절차를 통해 Azure VM 에이전트가 올바르게 설치되어 작동하는지 확인합니다.
   * VM 에이전트 로그 파일 `C:\WindowsAzure\logs\WaAppAgent.log`를 검토할 수도 있습니다.
   * 로그가 없는 경우 VM 에이전트가 설치되지 않은 것입니다.
   * [Azure VM 에이전트 설치](../vm/monitor-virtual-machine.md#agents)
2. `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`에서 Microsoft Monitoring Agent VM 확장 로그 파일을 검토합니다.
3. 가상 머신이 PowerShell 스크립트를 실행할 수 있는지 확인합니다.
4. C:\Windows\temp 권한이 변경되지 않았는지 확인합니다.
5. 가상 머신 `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`에 나타나는 PowerShell 창에서 다음을 입력하여Microsoft Monitoring Agent의 상태를 봅니다.
6. `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`에서 Microsoft Monitoring Agent 설정 로그 파일을 검토합니다.

자세한 내용은 [Windows 확장 문제 해결](../../virtual-machines/extensions/oms-windows.md)을 참조하세요.

## <a name="troubleshooting-linux-vm-extension"></a>Linux VM 확장 문제 해결
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
*Linux용 Log Analytics 에이전트* VM 확장이 설치되지 않거나 보고되지 않으면 다음 단계를 수행하여 문제를 해결할 수 있습니다.

1. 확장 상태가 *알 수 없음* 이면 VM 에이전트 로그 파일 `/var/log/waagent.log`를 검토하여 Azure VM 에이전트가 올바르게 설치되어 작동하고 있는지 확인합니다.
   * 로그가 없는 경우 VM 에이전트가 설치되지 않은 것입니다.
   * [Linux VM에 Azure VM 에이전트 설치](../../virtual-machines/extensions/agent-linux.md#installation)
2. 그 밖의 비정상 상태에 대해 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 및 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`에서 Linux용 Log Analytics VM 확장 로그 파일을 검토합니다.
3. 확장 상태가 정상이나 데이터가 업로드되지 않는 경우 `/var/opt/microsoft/omsagent/log/omsagent.log`에서 Linux용 Log Analytics 에이전트 로그 파일을 검토합니다.

자세한 내용은 [Linux 확장 문제 해결](../../virtual-machines/extensions/oms-linux.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 외부의 컴퓨터에서 호스트되는 Linux용 Log Analytics 에이전트와 관련된 추가 문제 해결 지침에 대해서는 [Azure Log Analytics Linux 에이전트 문제 해결](../agents/agent-linux-troubleshoot.md)을 참조하세요.