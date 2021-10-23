---
title: Azure Virtual Desktop 진단 로그 분석 - Azure
description: Azure Virtual Desktop 진단 기능에 로그 분석을 사용하는 방법을 설명합니다.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 3a33c9e1542e6abbebfdf251864877112230af83
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261465"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>진단 기능에 Log Analytics 사용

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체가 있는 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)를 참조하세요.

Azure Virtual Desktop은 다른 많은 Azure 서비스와 마찬가지로 모니터링 및 경고를 위해 [Azure Monitor](../azure-monitor/overview.md)를 사용합니다. 이를 통해 관리자는 단일 인터페이스에서 문제를 파악할 수 있습니다. 이 서비스는 사용자 및 관리 작업 모두에 대한 활동 로그를 만듭니다. 각 활동 로그는 다음 범주에 속합니다.

- 관리 작업:
    - API 또는 PowerShell을 사용하여 Azure Virtual Desktop 개체를 변경하려는 시도가 성공했는지 여부를 추적합니다. 예를 들어 PowerShell을 사용하여 호스트 풀을 성공적으로 만들 수 있나요?
- 피드:
    - 사용자가 작업 영역을 성공적으로 구독할 수 있나요?
    - 사용자가 원격 데스크톱 클라이언트에 게시된 모든 리소스를 볼 수 있나요?
- 연결:
    - 사용자가 서비스 연결을 시작하고 완료하는 경우입니다.
- 호스트 등록:
    - 세션 호스트가 연결 시 서비스에 성공적으로 등록되었나요?
- Errors:
    - 사용자에게 특정 활동의 문제가 발생하나요? 이 기능은 해당 정보가 활동과 연결되어 있는 한 활동 데이터를 추적하는 테이블을 생성할 수 있습니다.
- 검사점:
    - 연결된 활동의 수명의 특정 단계입니다. 예를 들어 세션 중 사용자가 특정 호스트로 부하 분산된 다음 연결 중에 사용자가 로그온된 경우 등입니다.

진단 역할 서비스 자체가 Azure Virtual Desktop의 일부이기 때문에 Azure Virtual Desktop에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. Azure Virtual Desktop 연결 문제는 사용자에게 네트워크 연결 문제가 발생할 때 나타날 수 있습니다.

Azure Monitor를 사용하면 동일한 도구 내에서 Azure Virtual Desktop 데이터를 분석하고 VM(가상 머신) 성능 카운터를 검토할 수 있습니다. 이 문서에서는 Azure Virtual Desktop 환경에 대해 진단을 사용하도록 설정하는 방법을 보여줍니다.

>[!NOTE]
>Azure에서 VM을 모니터하는 방법은 [Azure Monitor로 Azure 가상 머신 모니터링](../azure-monitor/vm/monitor-vm-azure.md)을 참조하세요. 또한 세션 호스트에서의 사용자 환경을 더 잘 이해하기 위해 [성능 카운터 임계값을 검토](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds)하세요.

## <a name="before-you-get-started"></a>시작하기 전에

Log Analytics를 사용하려면 먼저 작업 영역을 만들어야 합니다. 이렇게 하려면 다음 두 문서 중 하나의 지침을 따르세요.

- Azure Portal 사용을 선호할 경우 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.
- PowerShell을 선호할 경우 [PowerShell로 Log Analytics 작업 영역 만들기](../azure-monitor/logs/powershell-workspace-configuration.md)를 참조하세요.

작업 영역을 만든 후 [Azure Monitor에 Windows 컴퓨터 연결](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)의 지침에 따라 다음 정보를 가져옵니다.

- 작업 영역 ID
- 작업 영역의 기본 키

이 정보는 나중에 설정 프로세스에서 필요합니다.

Azure Monitor의 권한 관리를 검토하여 Azure Virtual Desktop 환경을 모니터 및 유지 관리하는 사용자에 대해 데이터 액세스 권한을 사용합니다. 자세한 내용은 [Azure Monitor로 역할, 권한 및 보안 시작하기](../azure-monitor/roles-permissions-security.md)를 참조하세요.

## <a name="push-diagnostics-data-to-your-workspace"></a>작업 영역에 진단 데이터 푸시

Azure Virtual Desktop 개체에서 작업 영역의 Log Analytics로 진단 데이터를 푸시할 수 있습니다. 개체를 처음 만들 때 이 기능을 즉시 설정할 수 있습니다.

새 개체에 대해 Log Analytics를 설정하려면 다음을 수행합니다.

1. Azure Portal에 로그인하고  **Virtual Desktop** 으로 이동합니다.

2. 로그 및 이벤트를 캡처하려는 개체(예: 호스트 풀, 앱 그룹 또는 작업 영역)로 이동합니다.

3. 화면의 왼쪽 메뉴에서 **진단 설정** 을 선택합니다.

4. 화면의 오른쪽 메뉴에서 **진단 설정 추가** 를 선택합니다.

    진단 설정 페이지에 표시되는 옵션은 편집 중인 개체 종류에 따라 달라집니다.

    예를 들어 앱 그룹에 대해 진단을 사용하도록 설정하는 경우 검사점, 오류 및 관리 구성 옵션이 표시됩니다. 작업 영역의 경우 이러한 범주는 사용자가 앱 목록을 구독하는 시간을 추적하기 위한 피드를 구성합니다. 진단 설정에 대한 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집하기 위한 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

     >[!IMPORTANT]
     >모니터하려는 각 Azure Resource Manager 개체에 대해 진단을 사용해야 합니다. 진단이 사용하도록 설정된 후 활동에 데이터를 사용할 수 있습니다. 처음 설정된 후 몇 시간 정도 걸릴 수 있습니다.

5. 설정 구성 이름을 입력한 후 **Log Analytics에 보내기** 를 선택합니다. 사용하는 이름은 공백을 포함하지 않고 [Azure 명명 규칙](../azure-resource-manager/management/resource-name-rules.md)을 준수해야 합니다. 로그의 일부로 검사점, 오류, 관리 등 Log Analytics에 추가하려는 모든 옵션을 선택할 수 있습니다.

6. **저장** 을 선택합니다.

>[!NOTE]
>Log Analytics는 [Event Hub](../event-hubs/event-hubs-about.md)로 데이터를 스트리밍하거나 스토리지 계정에 보관하는 옵션을 제공합니다. 이 기능에 대한 자세한 내용은 [Event Hub에 Azure 모니터링 데이터 스트리밍](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) 및 [스토리지 계정에 Azure 리소스 로그 아카이브](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)를 참조하세요.

## <a name="how-to-access-log-analytics"></a>Log Analytics 액세스 방법

Azure Portal 또는 Azure Monitor에서 Log Analytics 작업 영역에 액세스할 수 있습니다.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 Log Analytics 액세스

1. Azure Portal에 로그인합니다.

2. **Log Analytics 작업 영역** 을 검색합니다.

3. 서비스에서 **Log Analytics 작업 영역** 을 선택합니다.

4. 목록에서 Azure Virtual Desktop 개체에 구성한 작업 영역을 선택합니다.

5. 작업 영역에서 **로그** 를 선택합니다. **검색** 기능을 사용하여 메뉴 목록을 필터링할 수 있습니다.

### <a name="access-log-analytics-on-azure-monitor"></a>Azure Monitor에서 Log Analytics에 액세스

1. Azure Portal에 로그인합니다.

2. **모니터** 를 검색하고 선택합니다.

3. **로그** 를 선택합니다.

4. 로깅 페이지의 지침에 따라 쿼리 범위를 설정합니다.

5. 진단을 쿼리할 준비가 되었습니다. 모든 진단 테이블에는 "WVD" 접두사가 포함됩니다.

>[!NOTE]
>Azure Monitor 로그에 저장된 테이블에 대한 자세한 내용은 [Azure Monitor 데이터 참조](/azure/azure-monitor/reference/)를 확인하세요. Azure Virtual Desktop과 관련된 테이블에는 모두 "WVD" 레이블이 지정됩니다.

## <a name="cadence-for-sending-diagnostic-events"></a>진단 이벤트 전송 주기

완료되었으면 진단 이벤트가 Log Analytics로 전송됩니다.

Log Analytics는 연결 활동에 대해 이러한 중간 상태만 보고합니다.

- 시작됨: 사용자가 원격 데스크톱 클라이언트에서 앱 또는 데스크톱을 선택하고 연결했습니다.
- 연결됨: 앱 또는 데스크톱이 호스트되는 VM에 사용자가 성공적으로 연결되었습니다.
- 완료됨: 사용자 또는 서버가 작업이 수행된 세션 연결을 해제했습니다.

## <a name="example-queries"></a>쿼리 예

Azure Monitor Log Analytics UI를 통해 예시 쿼리에 액세스합니다.
1. Log Analytics 작업 영역으로 이동한 후 **로그** 를 선택합니다. 예제 쿼리 UI가 자동으로 표시됩니다.
1. 필터를 **범주** 로 변경합니다.
1. **Virtual Desktop** 을 선택하여 사용 가능한 쿼리를 검토합니다.
1. **실행** 을 선택하여 선택한 쿼리를 실행합니다.

[Azure Monitor Log Analytics에 저장된 쿼리](../azure-monitor/logs/queries.md)에서 샘플 쿼리 인터페이스에 대해 자세히 알아보세요.

다음 쿼리 목록을 통해 단일 사용자의 연결 정보 또는 문제를 검토할 수 있습니다. 이러한 쿼리는 [Log Analytics 쿼리 편집기](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)에서 실행할 수 있습니다. 각 쿼리에 대해 `userupn`을 조회하려는 사용자의 UPN으로 바꿉니다.


단일 사용자의 모든 연결을 찾으려면 다음을 수행합니다.

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


사용자가 하루 동안 연결한 횟수를 찾으려면 다음을 수행합니다.

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

사용자별 세션 기간을 찾으려면 다음을 수행합니다.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

특정 사용자의 오류를 찾으려면 다음을 수행합니다.

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

특정 오류가 다른 사용자에 대해 발생했는지 여부를 확인하려면 다음을 수행합니다.

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- 사용자가 전체 데스크톱을 열 때 세션의 해당 앱 사용은 WVDCheckpoints 테이블에서 검사점으로 추적되지 않습니다.
>- WVDConnections 테이블의 ResourcesAlias 열은 사용자가 전체 데스크톱 또는 게시된 앱에 연결되었는지 여부를 보여줍니다. 이 열에는 연결 중 열리는 첫 번째 앱만 표시됩니다. 사용자는 여는 모든 게시된 앱은 WVDCheckpoints에서 추적됩니다.
>- WVDErrors 테이블은 관리 오류, 호스트 등록 문제 및 사용자가 앱 또는 데스크톱 목록에 등록할 때 발생하는 기타 문제를 보여줍니다.
>- WVDErrors는 관리 작업으로 해결될 수 있는 문제를 식별하는 데 도움이 됩니다. ServiceError의 값은 이러한 문제 유형에 대해 항상 “false”를 표시합니다. ServiceError = “true”이면 문제를 Microsoft에 에스컬레이션해야 합니다. 에스컬레이션하는 오류에는 CorrelationID를 제공해야 합니다.

## <a name="next-steps"></a>다음 단계

진단 기능으로 사용자를 식별할 수 있는 일반적인 오류 시나리오를 검토하려면 [ID 및 진단 문제](./troubleshoot-set-up-overview.md)를 참조하세요.
