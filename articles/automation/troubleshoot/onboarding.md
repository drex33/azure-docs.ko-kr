---
title: Azure Automation 기능 배포 문제 해결
description: 이 문서에서는 Azure Automation 기능을 배포할 때 발생하는 문제를 해결하는 방법을 설명합니다.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 8e4cdcf91c3978d5849e987aa2992c6b5fc6b834
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494865"
---
# <a name="troubleshoot-feature-deployment-issues"></a>기능 배포 문제 해결

VM에 Azure Automation 업데이트 관리 기능 또는 변경 내용 추적 및 인벤토리 기능을 배포할 때 오류 메시지가 표시될 수 있습니다. 이 문서에서는 발생할 수 있는 오류와 이를 해결하는 방법을 설명합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>시나리오: 등록된 노드의 이름을 바꾸려면 등록을 취소하거나 다시 등록해야 함

#### <a name="issue"></a>문제

노드가 Azure Automation에 등록된 후 운영 체제 컴퓨터 이름이 변경됩니다. 노드의 보고서는 원래 이름을 사용하여 계속 표시됩니다.

#### <a name="cause"></a>원인

등록된 노드의 이름을 변경해도 Azure Automation의 노드 이름은 업데이트되지 않습니다.

#### <a name="resolution"></a>해결 방법

Azure Automation 상태 구성에서 노드의 등록을 취소한 후 다시 등록합니다. 이 시점 이전에 서비스에 게시된 보고서는 더 이상 사용할 수 없게 됩니다.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>시나리오: HTTPS 프록시를 통한 인증서 재서명이 지원되지 않음

#### <a name="issue"></a>문제

HTTPS 트래픽을 종료하는 프록시를 통해 연결하고 새 인증서를 사용하여 트래픽을 다시 암호화하면 서비스가 연결을 허용하지 않습니다.

#### <a name="cause"></a>원인

Azure Automation은 트래픽을 암호화하는 데 사용되는 인증서의 재서명을 지원하지 않습니다.

#### <a name="resolution"></a>해결 방법

현재 이 문제에 대한 해결 방법이 없습니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>시나리오: 기능 배포가 실패하고 "솔루션을 사용할 수 없습니다"는 메시지가 표시됨

#### <a name="issue"></a>문제

VM에서 기능을 사용하도록 설정하려고 할 때 다음 메시지 중 하나가 표시됩니다.

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>원인

VM 또는 작업 영역의 권한이나 사용자의 권한이 잘못되었거나 누락된 경우 이 오류가 발생합니다.

#### <a name="resolution"></a>해결 방법

[기능 배포 권한](../automation-role-based-access-control.md#feature-setup-permissions)이 올바른지 확인한 후 기능을 다시 배포하세요. `The solution cannot be enabled on this VM because the permission to read the workspace is missing` 오류 메시지가 표시되면 다음 [문제 해결 정보](update-management.md#failed-to-enable-error)를 참조하세요.

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>시나리오: 기능 배포가 실패하고 "진단 로깅을 위한 자동화 계정을 구성하지 못했습니다"라는 메시지가 표시됨

#### <a name="issue"></a>문제

VM에서 기능을 사용하도록 설정하려고 할 때 다음 메시지가 표시됩니다.

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>원인

가격 책정 계층이 구독의 청구 모델과 일치하지 않는 경우 이 오류가 발생할 수 있습니다. 자세한 내용은 [Azure Monitor의 모니터링 사용량 및 예상 비용](../../azure-monitor//usage-estimated-costs.md)을 참조하세요.

#### <a name="resolution"></a>해결 방법

수동으로 Log Analytics 작업 영역을 만들고 기능 배포 프로세스를 반복하여 생성된 작업 영역을 선택합니다.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>시나리오: ComputerGroupQueryFormatError

#### <a name="issue"></a>문제

이 오류 코드는 기능을 대상으로 사용되는 저장된 검색 컴퓨터 그룹 쿼리 형식이 올바르지 않음을 의미합니다. 

#### <a name="cause"></a>원인

사용자가 쿼리를 변경했거나 시스템이 쿼리를 변경했을 수 있습니다.

#### <a name="resolution"></a>해결 방법

기능에 대한 쿼리를 삭제한 다음, 이 기능을 다시 사용하도록 설정할 수 있습니다. 그러면 쿼리가 다시 생성됩니다. 쿼리는 작업 영역 내의 **저장된 검색** 에서 찾을 수 있습니다. 쿼리의 이름은 **MicrosoftDefaultComputerGroup** 이고, 쿼리의 범주는 연결된 기능의 이름입니다. 여러 기능이 사용되도록 설정되면 **MicrosoftDefaultComputerGroup** 쿼리가 **저장된 검색** 아래에 여러 번 표시됩니다.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>시나리오: PolicyViolation

#### <a name="issue"></a>문제

이 오류 코드는 하나 이상의 Azure Policy 할당 위반으로 인해 배포가 실패했음을 의미합니다.

#### <a name="cause"></a>원인 

Azure Policy 할당 때문에 작업이 완료되지 못합니다.

#### <a name="resolution"></a>해결 방법

이 기능을 성공적으로 배포하려면 표시된 정책 정의를 변경하는 것이 좋습니다. 정의할 수 있는 정책 정의 유형이 다양하기 때문에 필요한 변경 내용은 위반된 정책 정의에 따라 달라집니다. 예를 들어 일부 포함된 리소스의 콘텐츠를 변경하는 권한을 거부하는 정책 정의가 리소스 그룹에 할당된 경우 다음과 같은 해결 방법 중 하나를 선택할 수 있습니다.

* 정책 할당을 제거합니다.
* 다른 리소스 그룹에 기능을 사용하도록 설정해 봅니다.
* 정책 할당의 대상을 특정 리소스(예: Automation 계정)로 변경합니다.
* 정책 정의가 거부하도록 구성된 리소스 세트를 수정합니다.

실패한 배포를 확인하려면 Azure Portal의 오른쪽 상단 모서리에서 알림을 확인하거나 Automation 계정을 포함하는 리소스 그룹으로 이동하여 **설정** 에 있는 **배포** 를 선택합니다. Azure Policy에 대해 자세히 알아보려면 [Azure Policy 개요](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)를 참조하세요.

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>시나리오: 작업 영역의 연결을 끊는 중 오류 발생

#### <a name="issue"></a>문제

작업 영역의 연결을 끊으려고 하면 다음과 같은 오류 메시지가 나타납니다.

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>원인

이 오류는 연결된 Log Analytics 작업 영역 및 Automation 계정에 종속된 Log Analytics 작업 영역에 활성화된 기능이 아직 있는 경우에 발생합니다.

### <a name="resolution"></a>해결 방법

다음 기능에 대한 리소스를 사용 중인 경우 작업 영역에서 제거합니다.

* 업데이트 관리
* 변경 내용 추적 및 인벤토리
* 작업이 없는 동안 VM 시작/중지

기능 리소스를 제거하면 작업 영역의 연결을 끊을 수 있습니다. 작업 영역 및 Automation 계정의 이러한 기능에서 기존 아티팩트를 정리하는 것이 중요합니다.

* 업데이트 관리의 경우 Automation 계정에서 **업데이트 배포(일정)** 를 제거합니다.
* 작업 시간 외 VM 시작/중지의 경우 **설정** > **잠금** 에서 Automation 계정의 기능 구성 요소에 대한 잠금을 제거합니다. 자세한 내용은 [기능 제거](../automation-solution-vm-management-remove.md)를 참조하세요.

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows용 Log Analytics 확장 오류

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Windows용 Log Analytics 에이전트 확장 설치는 여러 가지 이유로 실패할 수 있습니다. 다음 섹션에서는 Windows용 Log Analytics 에이전트 확장을 배포하는 동안 오류를 발생시킬 수 있는 기능 배포 문제에 대해 설명합니다.

>[!NOTE]
>Windows용 Log Analytics 에이전트는 Microsoft Monitoring Agent(MMA)용 Azure Automation에서 현재 사용되는 이름입니다.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>시나리오: WebClient 요청 중 예외가 발생했습니다.

VM의 Windows용 Log Analytics 확장이 외부 리소스와 통신할 수 없으며 배포가 실패합니다.

#### <a name="issue"></a>문제

다음은 반환되는 오류 메시지의 예입니다.

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>원인

이러한 오류의 잠재적인 원인은 다음과 같습니다.

* VM에 특정 포트만 허용하는 프록시가 구성되어 있습니다.
* 방화벽 설정이 필요한 포트 및 주소에 대한 액세스를 차단했습니다.

#### <a name="resolution"></a>해결 방법

통신을 위해 적절한 포트와 주소가 열려 있는지 확인합니다. 포트와 주소 목록은 [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>시나리오: 일시적인 환경 문제로 인해 설치가 실패했습니다.

다른 설치 또는 설치를 차단하는 작업으로 인해 배포 중 Windows용 Log Analytics 확장을 설치하는 데 실패했습니다.

#### <a name="issue"></a>문제

다음은 반환될 수 있는 오류 메시지의 예입니다.

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>원인

이러한 오류의 잠재적인 원인은 다음과 같습니다.

* 다른 설치 프로그램이 실행 중입니다.
* 템플릿 배포 중에 시스템이 다시 부팅되도록 트리거되었습니다.

#### <a name="resolution"></a>해결 방법

기본적으로 이 오류는 일시적입니다. 배포를 다시 시도하여 확장 프로그램을 설치하십시오.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>시나리오: 설치 시간 제한

시간 초과로 인해 Windows용 Log Analytics 확장 설치가 완료되지 않았습니다.

#### <a name="issue"></a>문제

다음은 반환될 수 있는 오류 메시지의 예입니다.

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>원인

이 유형의 오류는 설치하는 동안 VM의 부하가 높을 때 발생합니다.

### <a name="resolution"></a>해결 방법

VM의 부하가 낮을 때 Windows용 Log Analytics 에이전트 확장을 설치해 보세요.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.