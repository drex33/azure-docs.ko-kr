---
title: Azure Automation 변경 내용 추적 및 인벤토리 문제 해결
description: 이 문서에서는 Azure Automation 변경 내용 추적 및 인벤토리 기능과 관련된 문제를 해결하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: 21699d306742c2a732155ac8df78608f5c3dd7ae
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346025"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>변경 내용 추적 및 인벤토리 문제 해결

이 문서에서는 Azure Automation 변경 내용 추적 및 인벤토리 문제를 해결하는 방법을 설명합니다. 변경 내용 추적 및 인벤토리에 대한 일반 정보는 [변경 내용 추적 및 인벤토리 개요](../change-tracking/overview.md)를 참조하세요.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>시나리오: 컴퓨터가 이미 다른 계정에 등록되어 있습니다.

### <a name="issue"></a>문제

다음과 같은 오류 메시지가 표시됩니다.

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>원인

컴퓨터가 이미 변경 내용 추적을 위해 다른 작업 영역에 배포되었습니다.

### <a name="resolution"></a>해결 방법

1. 머신이 올바른 작업 영역에 보고하고 있는지 확인합니다. 이러한 측면을 확인하는 방법에 대한 참고 자료는 [Azure Monitor에 대한 에이전트 연결 확인](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)을 참조하세요. 또한 이 작업 영역이 Azure Automation 계정에 연결되어 있는지 확인합니다. 이를 확인하려면 Automation 계정으로 이동하고 **관련 리소스** 에서 **연결된 작업 영역** 을 선택합니다.

1. Automation 계정에 연결된 Log Analytics 작업 영역에 머신이 표시되는지 확인합니다. Log Analytics 작업 영역에서 다음 쿼리를 실행합니다.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   사용자 컴퓨터가 쿼리 결과에 표시되지 않는다면 최근에 체크인하지 않은 것입니다. 로컬 구성 문제가 있을 수 있습니다. Log Analytics 에이전트를 다시 설치해야 합니다.

   컴퓨터가 쿼리 결과에 나열되는 경우, **솔루션** 속성에 changeTracking이 나열되어 있는지 확인합니다. 그러면 변경 내용 추적 및 인벤토리에 등록되었는지 확인합니다. 등록되지 않았으면 범위 구성 문제를 확인합니다. 범위 구성은 변경 내용 추적 및 인벤토리에 구성되는 컴퓨터를 결정합니다. 대상 컴퓨터에 범위 구성을 구성하려면, [Automation 계정에서 변경 내용 추적 및 인벤토리 사용](../change-tracking/enable-from-automation-account.md)을 참조하세요.

   작업 영역에서 이 쿼리를 실행합니다.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` 결과를 얻는 경우 작업 영역에 정의된 할당량에 도달하여 중지된 데이터를 저장하지 못한 것입니다. 작업 영역에서 **사용량 및 예상 비용** 으로 이동합니다. 더 많은 데이터를 사용할 수 있는 새로운 **가격 책정 계층** 을 선택하거나, **일일 한도** 를 클릭하고 한도를 제거합니다.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="사용량 및 예상 비용." lightbox="./media/change-tracking/change-tracking-usage.png":::

그래도 문제가 해결되지 않으면 [Windows Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md)의 단계에 따라 Windows용 Hybrid Worker를 다시 설치합니다. Linux의 경우 [Linux Hybrid Runbook Worker 배포](../automation-linux-hrw-install.md)의 단계를 따르세요.

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>시나리오: 변경 내용 추적 및 인벤토리 레코드가 Windows 머신에 표시되지 않음

#### <a name="issue"></a>문제

기능에 대해 사용하도록 설정된 Windows 머신에 대한 변경 내용 추적 및 인벤토리 결과가 표시되지 않습니다.

#### <a name="cause"></a>원인

이 오류는 다음과 같은 경우에 발생할 수 있습니다.

* Windows용 Azure Log Analytics 에이전트가 실행되고 있지 않습니다.
* Automation 계정과의 통신이 차단되었습니다.
* 변경 내용 추적 및 인벤토리 관리 팩이 다운로드되지 않습니다.
* 활성화되는 VM은 설치된 Windows용 Log Analytics 에이전트를 사용하여 시스템 준비(sysprep)로 준비되지 않은 복제된 머신에서 가져온 것일 수 있습니다.

#### <a name="resolution"></a>해결 방법

Log Analytics 에이전트 머신에서 **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** 로 이동하고 다음 명령을 실행합니다.

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

여전히 도움이 필요한 경우 진단 정보를 수집하고 지원 담당자에게 문의할 수 있습니다.

> [!NOTE]
> Log Analytics 에이전트는 기본적으로 오류 추적을 사용하도록 설정합니다. 앞의 예제와 같이 자세한 오류 메시지를 사용하려면 `VER` 매개 변수를 사용합니다. 정보 추적의 경우 `StartTracing.cmd`를 호출할 때 `INF`를 사용합니다.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Windows용 Log Analytics 에이전트가 실행되고 있지 않음

Windows용 Log Analytics 에이전트(**HealthService.exe**)가 머신에서 실행되고 있는지 확인합니다.

##### <a name="communication-to-automation-account-blocked"></a>Automation 계정에 대한 통신이 차단됨

머신에서 이벤트 뷰어를 확인하여 단어가 `changetracking`인 이벤트를 검색합니다.

변경 내용 추적 및 인벤토리가 작동하도록 허용해야 하는 주소 및 포트에 대한 자세한 내용은 [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

##### <a name="management-packs-not-downloaded"></a>관리 팩이 다운로드되지 않음

다음 변경 내용 추적 및 인벤토리 관리 팩이 로컬로 설치되어 있는지 확인합니다.

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>sysprep되지 않은 복제된 머신의 VM

복제된 이미지를 사용하는 경우 이미지에 sysprep을 수행한 다음, Windows용 Log Analytics 에이전트를 설치합니다.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>시나리오: Linux 머신에서 변경 내용 추적 및 인벤토리 결과 없음

#### <a name="issue"></a>문제

기능에 대해 사용하도록 설정된 Linux 머신에 대한 변경 내용 추적 및 인벤토리 결과가 표시되지 않습니다. 

#### <a name="cause"></a>원인
이 문제와 관련된 가능한 원인은 다음과 같습니다.
* Linux용 Log Analytics 에이전트가 실행되고 있지 않습니다.
* Linux용 Log Analytics 에이전트가 올바르게 구성되지 않았습니다.
* FIM(파일 무결성 모니터링) 충돌이 있습니다.

#### <a name="resolution"></a>해결 방법 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux용 Log Analytics 에이전트가 실행되고 있지 않음

Linux용 Log Analytics 에이전트(**omsagent**)의 디먼이 머신에서 실행되고 있는지 확인합니다. Automation 계정에 연결된 Log Analytics 작업 영역에서 다음 쿼리를 실행합니다.

```loganalytics
Copy
Heartbeat
| summarize by Computer, Solutions
```

쿼리 결과에 머신이 표시되지 않으면 최근에 체크인되지 않은 것입니다. 로컬 구성 문제가 있는 것이므로 에이전트를 다시 설치해야 합니다. 설치 및 구성에 대한 자세한 내용은 [Log Analytics 에이전트를 사용하여 로그 데이터 수집](../../azure-monitor/agents/log-analytics-agent.md)을 참조하세요.

머신이 쿼리 결과에 표시되면 범위 구성을 확인합니다. [Azure Monitor의 모니터링 솔루션 대상 지정](../../azure-monitor/insights/solution-targeting.md)을 참조하세요.

이 문제의 해결 방법에 대한 자세한 내용은 [문제: Linux 데이터가 보이지 않음](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data)을 참조하세요.

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux용 Log Analytics 에이전트가 올바르게 구성되지 않았습니다.

OMS 로그 수집기 도구를 사용하여 로그 및 명령줄 출력 컬렉션에 대해 Linux용 Log Analytics 에이전트가 올바르게 구성되어 있지 않을 수 있습니다. [변경 내용 추적 및 인벤토리 개요](../change-tracking/overview.md)를 참조하세요.

##### <a name="fim-conflicts"></a>FIM 충돌

클라우드용 Microsoft Defender의 FIM 기능이 Linux 파일의 무결성에 대한 유효성을 잘못 검사할 수 있습니다. FIM이 작동하고 Linux 파일 모니터링에 대해 올바르게 구성되어 있는지 확인합니다. [변경 내용 추적 및 인벤토리 개요](../change-tracking/overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.
