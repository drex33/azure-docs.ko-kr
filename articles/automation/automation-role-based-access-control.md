---
title: Azure Automation에서 역할 권한 및 보안 관리
description: 이 문서에서는 Azure 리소스의 액세스 관리를 사용하도록 설정하는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하는 방법을 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed699f366336750289134324add0cf0243643297
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585055"
---
# <a name="manage-role-permissions-and-security-in-automation"></a>Automation에서 역할 권한 및 보안 관리

Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하면 Azure 리소스 대한 액세스 권한을 관리할 수 있습니다. [Azure RBAC](../role-based-access-control/overview.md)를 사용하면 팀 내에서 업무를 분장하고 사용자, 그룹 및 애플리케이션에 해당 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. Azure Portal, Azure 명령줄 도구 또는 Azure 관리 API를 사용하여 사용자에게 역할 기반 액세스에 대한 권한을 부여할 수 있습니다.

## <a name="roles-in-automation-accounts"></a>Automation 계정의 역할

Azure Automation의 Automation 계정 범위에서 사용자, 그룹 및 애플리케이션에 적절한 Azure 역할을 할당하여 액세스 권한을 부여합니다. 다음은 Automation 계정에서 지원하는 기본 제공 역할입니다.

| **역할** | **설명** |
|:--- |:--- |
| 소유자 |소유자 역할을 사용하면 Automation 계정을 관리하기 위해 다른 사용자, 그룹 및 애플리케이션에 대한 액세스 권한 제공이 포함된 Automation 계정 내에서 모든 리소스 및 동작에 액세스할 수 있습니다. |
| 참가자 |참가자 역할을 사용하면 Automation 계정에 대한 다른 사용자의 액세스 권한 수정을 제외한 모든 사항을 관리할 수 있습니다. |
| 판독기 |리더 역할을 사용하면 Automation 계정의 모든 리소스를 볼 수 있지만 변경할 수는 없습니다. |
| Automation 기여자 | Automation 기여자 역할을 사용하면 Automation 계정에 대한 다른 사용자의 액세스 권한 수정을 제외하고 Automation 계정의 모든 리소스를 관리할 수 있습니다. |
| Automation 운영자 |Automation 연산자 역할을 사용하면 Runbook 이름 및 속성을 보고 Automation 계정의 모든 Runbook에 대한 작업을 만들고 관리할 수 있습니다. 이 역할은 자격 증명 자산 및 Runnbook 등의 Automation 계정 리소스를 보거나 수정하지 못하도록 보호하며 조직의 구성원이 이러한 Runbook을 여전히 실행하도록 하려는 경우 유용합니다. |
|Automation 작업 연산자|Automation 작업 연산자 역할을 사용하면 Automation 계정의 모든 Runbook에 대한 작업을 만들고 관리할 수 있습니다.|
|Automation Runbook 연산자|Automation Runbook 연산자 역할을 사용하면 Runbook의 이름 및 속성을 볼 수 있습니다.|
| Log Analytics 참가자 | Log Analytics Contributor 역할을 사용하면 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, Automation 계정 생성 및 구성, Azure Automation 기능 추가 및 모든 Azure 리소스에 대한 Azure Diagnostics을 구성하는 기능도 포함되어 있습니다.|
| Log Analytics 독자 | Log Analytics Reader 역할을 사용하면 모니터링 설정을 볼 뿐만 아니라 모든 모니터링 데이터를 보고 검색할 수 있습니다. 여기에는 모든 Azure 리소스에 대한 Azure Diagnostics의 구성 보기가 포함됩니다. |
| Monitoring Contributor | Monitoring Contributor 역할을 사용하면 모든 모니터링 데이터를 읽고 모니터링 설정을 업데이트할 수 있습니다.|
| Monitoring Reader | Montioring Reader 역할을 사용하면 모든 모니터링 데이터를 읽을 수 있습니다. |
| 사용자 액세스 관리자 |사용자 액세스 관리자 역할을 사용하면 Azure Automation 계정에 대한 사용자 액세스 권한을 관리할 수 있습니다. |

## <a name="role-permissions"></a>역할 권한

다음 표에서는 각 역할에 부여되는 특정 권한에 대해 설명합니다. 여기에는 권한을 부여하는 작업과 권한을 제한하는 NotActions이 포함될 수 있습니다.

### <a name="owner"></a>소유자

소유자는 액세스를 제외한 모든 것을 관리할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|동작|Description|
|---|---|
|Microsoft.Automation/automationAccounts/|모든 유형의 리소스를 만들고 관리합니다.|

### <a name="contributor"></a>참가자

Contributor는 액세스를 제외한 모든 것을 관리할 수 있습니다. 다음 표에서는 역할에 부여되거나 거부된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|모든 유형의 리소스 만들기 및 관리|
|**조치 없음**||
|Microsoft.Authorization/*/Delete| 역할 및 역할 할당을 삭제합니다.       |
|Microsoft.Authorization/*/Write     |  역할 및 역할 할당을 만듭니다.       |
|Microsoft.Authorization/elevateAccess/Action    | 사용자 액세스 관리자를 만드는 기능을 거부합니다.       |

### <a name="reader"></a>판독기

리더는 Automation 계정의 모든 리소스를 볼 수 있지만 변경할 수는 없습니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Automation 계정에서 모든 리소스를 봅니다. |

### <a name="automation-contributor"></a>Automation 기여자

Automation 기여자는 액세스를 제외하고 Automation 계정의 모든 리소스를 관리할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/*|Automation 계정으로 모든 종류의 리소스를 만들고 관리합니다.|
|Microsoft.Authorization/*/read|역할 및 역할 할당을 읽습니다.|
|Microsoft.Resources/deployments/*|리소스 그룹 배포를 만들고 관리합니다.|
|Microsoft.Resources/subscriptions/resourceGroups/read|리소스 그룹 배포를 읽습니다.|
|Microsoft.Support/*|지원 티켓을 만들고 관리합니다.|
|Microsoft. Insights/actiongroups//*|작업 그룹을 읽고 쓰고 삭제합니다.|
|Microsoft. Insights/ActivityLogAlerts/*|활동 로그 알림을 읽고 쓰고 삭제합니다.|
|Microsoft.Insights/diagnosticSettings/*|진단 설정 읽기/쓰기/삭제|
|Microsoft. Insights/MetricAlerts/*|거의 실시간 메트릭 경고를 읽고 쓰고 삭제 합니다.|
|Microsoft. Insights/ScheduledQueryRules/*|Azure Monitor에서 로그 경고를 읽기/쓰기/삭제합니다.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Log Analytics 작업 영역에 대 한 키를 나열 합니다.|

> [!NOTE]
> Automation 기여자 역할을 사용하면 관리 ID(대상 리소스에 대한 적절한 권한이 설정된 경우) 또는 실행 계정을 통해 모든 리소스에 액세스할 수 있습니다. Automation 실행 계정은 기본적으로 구독에 대한 기여자 권한으로 구성됩니다. 최소 권한의 원칙을 따르고 Runbook을 실행하는 데 필요한 권한만 신중하게 할당합니다. 예를 들어 Automation 계정이 Azure VM을 시작하거나 중지하는 데만 필요한 경우 실행 계정 또는 관리 ID에 VM을 시작하거나 중지하는 권한만 할당해야 합니다. 마찬가지로, Runbook이 Blob Storage에서 읽는 경우 읽기 전용 권한을 할당합니다.
> 
> 권한을 할당할 때는 관리 ID에 할당된 Azure RBAC(역할 기반 액세스 제어)를 사용하는 것이 좋습니다. 수명 중 관리와 거버넌스를 포함하여 시스템 또는 사용자가 할당한 관리 ID를 사용하는 [가장 적합한 방법](../active-directory/managed-identities-azure-resources/managed-identity-best-practice-recommendations.md)에 관한 권장 사항을 검토합니다.

### <a name="automation-operator"></a>Automation 운영자

Automation 연산자 역할은 작업을 만들고 관리할 수 있으며, Automation 계정의 모든 Runbook에 대한 Runbook 이름 및 속성을 읽을 수 있습니다.

>[!NOTE]
>개별 Runbook에 대한 연산자 액세스를 제어하려는 경우 이 역할을 설정하지 마세요. 대신 **Automation 작업 연산자** 역할과 **Automation Runbook 연산자** 역할을 결합하여 사용하세요.

다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Authorization/*/read|권한 부여를 읽습니다.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Hybrid Runbook Worker 리소스를 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook의 작업을 나열합니다.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|일시 중지된 작업을 다시 시작합니다.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|진행 중인 작업을 취소합니다.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|작업 스트림 및 출력을 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/output/read|작업의 출력을 가져옵니다.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|진행 중인 작업을 일시 중지합니다.|
|Microsoft.Automation/automationAccounts/jobs/write|작업을 만듭니다.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Azure Automation 작업 일정을 가져옵니다.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Azure Automation 작업 일정을 만듭니다.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Automation 계정에 연결된 작업 영역을 가져옵니다.|
|Microsoft.Automation/automationAccounts/read|Azure Automation 계정을 가져옵니다.|
|Microsoft.Automation/automationAccounts/runbooks/read|Azure Automation Runbook을 가져옵니다.|
|Microsoft.Automation/automationAccounts/schedules/read|Azure Automation 일정 자산을 가져옵니다.|
|Microsoft.Automation/automationAccounts/schedules/write|Azure Automation 일정 자산을 만들거나 업데이트합니다.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |역할 및 역할 할당을 읽습니다.         |
|Microsoft.Resources/deployments/*      |리소스 그룹 배포를 만들고 관리합니다.         |
|Microsoft.Insights/alertRules/*      | 경고 규칙을 만들고 관리합니다.        |
|Microsoft.Support/* |지원 티켓을 만들고 관리합니다.|

### <a name="automation-job-operator"></a>Automation 작업 연산자

Automation 작업 연산자 역할은 Automation 계정 범위에서 부여됩니다. 그러면 연산자 권한이 계정의 모든 Runbook에 대한 작업을 만들고 관리할 수 있습니다. Automation 계정이 포함된 리소스 그룹에서 작업 연산자 역할에 읽기 권한을 부여하면 역할 구성원이 Runbook을 시작할 수 있습니다. 그러나 생성, 편집 또는 삭제할 수는 없습니다.

다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Authorization/*/read|권한 부여를 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/read|Runbook의 작업을 나열합니다.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|일시 중지된 작업을 다시 시작합니다.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|진행 중인 작업을 취소합니다.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|작업 스트림 및 출력을 읽습니다.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|진행 중인 작업을 일시 중지합니다.|
|Microsoft.Automation/automationAccounts/jobs/write|작업을 만듭니다.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  역할 및 역할 할당을 읽습니다.       |
|Microsoft.Resources/deployments/*      |리소스 그룹 배포를 만들고 관리합니다.         |
|Microsoft.Insights/alertRules/*      | 경고 규칙을 만들고 관리합니다.        |
|Microsoft.Support/* |지원 티켓을 만들고 관리합니다.|

### <a name="automation-runbook-operator"></a>Automation Runbook 연산자

Automation Runbook 운영자 역할은 Runbook 범위에서 부여됩니다. Automation Runbook 연산자 역할은 Runbook의 이름 및 속성을 볼 수 있습니다.**Automation 작업 연산자** 역할과 결합된 이 역할을 사용하면 연산자가 Runbook에 작업을 만들고 관리할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Runbook을 나열합니다.        |
|Microsoft.Authorization/*/read      | 권한 부여를 읽습니다.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |역할 및 역할 할당을 읽습니다.         |
|Microsoft.Resources/deployments/*      | 리소스 그룹 배포를 만들고 관리합니다.         |
|Microsoft.Insights/alertRules/*      | 경고 규칙을 만들고 관리합니다.        |
|Microsoft.Support/*      | 지원 티켓을 만들고 관리합니다.        |

### <a name="log-analytics-contributor"></a>Log Analytics 참가자

Log Analytics Contributor 역할은 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, Automation 계정 생성 및 구성, 기능 추가 및 모든 Azure 리소스에 대한 Azure Diagnostics을 구성하는 기능도 포함되어 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.Automation/automationAccounts/*|Automation 계정을 관리합니다.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|가상 머신 확장을 만들고 관리합니다.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|클래식 스토리지 계정 키를 나열합니다.|
|Microsoft.Compute/virtualMachines/extensions/*|클래식 가상 머신 확장을 만들고 관리합니다.|
|Microsoft.Insights/alertRules/*|경고 규칙 읽기/쓰기/삭제|
|Microsoft.Insights/diagnosticSettings/*|진단 설정 읽기/쓰기/삭제|
|Microsoft.OperationalInsights/*|Azure Monitor 로그를 관리합니다.|
|Microsoft.OperationsManagement/*|작업 영역에서 Azure Automation 기능을 관리합니다.|
|Microsoft.Resources/deployments/*|리소스 그룹 배포를 만들고 관리합니다.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|리소스 그룹 배포를 만들고 관리합니다.|
|Microsoft.Storage/storageAccounts/listKeys/action|스토리지 계정 키를 나열합니다.|
|Microsoft.Support/*|지원 티켓을 만들고 관리합니다.|

### <a name="log-analytics-reader"></a>Log Analytics 독자

Log Analytics Reader는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. 다음 표에서는 역할에 부여되고 거부된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Azure Monitor 로그에서 쿼리를 관리합니다.|
|Microsoft.OperationalInsights/workspaces/search/action|Azure Monitor 로그 데이터를 검색합니다.|
|Microsoft.Support/*|지원 티켓을 만들고 관리합니다.|
|**조치 없음**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|공유 액세스 키를 읽을 수 없습니다.|

### <a name="monitoring-contributor"></a>Monitoring Contributor

Monitoring Contributor는 모든 모니터링 데이터를 읽고 모니터링 설정을 업데이트할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.AlertsManagement/alerts/*|경고를 관리합니다.|
|Microsoft.AlertsManagement/alertsSummary/*|경고 대시보드를 관리합니다.|
|Microsoft.Insights/AlertRules/*|경고 규칙을 관리합니다.|
|Microsoft.Insights/components/*|Application Insights 구성 요소를 관리합니다.|
|Microsoft.Insights/DiagnosticSettings/*|진단 설정을 관리합니다.|
|Microsoft.Insights/eventtypes/*|구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대한 프로그래밍 방식 및 포털 액세스 모두에 적용 가능합니다.|
|Microsoft.Insights/LogDefinitions/*|이 사용 권한은 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다.|
|Microsoft.Insights/MetricDefinitions/*|메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다.|
|Microsoft.Insights/Metrics/*|리소스에 대한 메트릭을 읽습니다.|
|Microsoft.Insights/Register/Action|Microsoft Insights 공급자 등록|
|Microsoft.Insights/webtests/*|Application Insights 웹 테스트를 관리합니다.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Azure Monitor 로그 솔루션 팩을 관리합니다.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Azure Monitor 로그 저장된 검색을 관리합니다.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics 작업 영역 검색|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Log Analytics 작업 영역에 대한 키를 나열합니다.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Azure Monitor 로그 스토리지 인사이트 구성을 관리합니다.|
|Microsoft.Support/*|지원 티켓을 만들고 관리합니다.|
|Microsoft.WorkloadMonitor/workloads/*|워크로드를 관리합니다.|

### <a name="monitoring-reader"></a>Monitoring Reader

Monitoring Reader는 모든 모니터링 데이터를 읽을 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|암호를 제외한 모든 유형의 리소스를 읽습니다.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics 작업 영역 검색|
|Microsoft.Support/*|지원 티켓 만들기 및 관리|

### <a name="user-access-administrator"></a>사용자 액세스 관리자

사용자 액세스 관리자는 Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. 다음 표에서는 역할에 부여된 사용 권한을 보여줍니다.

|**actions**  |**설명**  |
|---------|---------|
|*/read|모든 리소스 읽기|
|Microsoft.Authorization/*|권한 부여 관리|
|Microsoft.Support/*|지원 티켓 만들기 및 관리|

## <a name="feature-setup-permissions"></a>기능 설정 권한

이어지는 섹션에서는 업데이트 관리 기능과 변경 내용 추적 및 인벤토리 기능을 사용하도록 설정하는 데 필요한 최소 권한에 대해 설명합니다.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>VM에서 업데이트 관리 기능과 변경 내용 추적 및 인벤토리 기능을 사용하도록 설정하는 데 필요한 권한

|**동작**  |**사용 권한**  |**최소 범위**  |
|---------|---------|---------|
|새 배포 쓰기      | Microsoft.Resources/deployments/*          |Subscription          |
|새 리소스 그룹 쓰기      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subscription          |
|새로운 기본 작업 영역 만들기      | Microsoft.OperationalInsights/workspaces/write         | Resource group         |
|새 계정 만들기      |  Microsoft.Automation/automationAccounts/write        |Resource group         |
|작업 영역 및 계정 연결      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|작업 영역</br>Automation 계정
|MMA 확장 만들기      | Microsoft.Compute/virtualMachines/write         | Virtual Machine         |
|저장된 검색 만들기      | Microsoft.OperationalInsights/workspaces/write          | 작업 영역         |
|범위 구성 만들기      | Microsoft.OperationalInsights/workspaces/write          | 작업 영역         |
|온보딩 상태 확인 - 작업 영역 읽기      | Microsoft.OperationalInsights/workspaces/read         | 작업 영역         |
|온보딩 상태 확인 - 계정의 연결된 작업 영역 속성 읽기     | Microsoft.Automation/automationAccounts/read      | Automation 계정        |
|온보딩 상태 확인 - 솔루션 읽기      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | 해결 방법         |
|온보딩 상태 확인 - VM 읽기      | Microsoft.Compute/virtualMachines/read         | Virtual Machine         |
|온보딩 상태 확인 - 계정 읽기      | Microsoft.Automation/automationAccounts/read  |  Automation 계정   |
| VM에 대한 작업 영역 온보딩 확인<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subscription         |
| Log Analytics 공급자 등록 |Microsoft.Insights/register/action | Subscription|

<sup>1</sup> 이 권한은 VM 포털 환경을 통해 기능을 사용하도록 설정하는 데 필요합니다.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Automation 계정에서 업데이트 관리 기능과 변경 내용 추적 및 인벤토리 기능을 사용하도록 설정하는 데 필요한 권한

|**동작**  |**사용 권한** |**최소 범위**  |
|---------|---------|---------|
|새 배포 만들기     | Microsoft.Resources/deployments/*        | Subscription         |
|새 리소스 그룹 만들기     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subscription        |
|AutomationOnboarding 블레이드 - 새 작업 영역 만들기     |Microsoft.OperationalInsights/workspaces/write           | Resource group        |
|AutomationOnboarding 블레이드 - 연결된 작업 영역 읽기     | Microsoft.Automation/automationAccounts/read        | Automation 계정       |
|AutomationOnboarding 블레이드 - 솔루션 읽기     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | 해결 방법        |
|AutomationOnboarding 블레이드 - 작업 영역 읽기     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | 작업 영역        |
|작업 영역 및 계정에 대한 링크 만들기     | Microsoft.OperationalInsights/workspaces/write        | 작업 영역        |
|Shoebox의 계정 쓰기      | Microsoft.Automation/automationAccounts/write        | 계정        |
|저장된 검색 만들기/편집     | Microsoft.OperationalInsights/workspaces/write        | 작업 영역        |
|범위 구성 만들기/편집     | Microsoft.OperationalInsights/workspaces/write        | 작업 영역        |
| Log Analytics 공급자 등록 |Microsoft.Insights/register/action | Subscription|
|**2단계 - 여러 VM을 사용하도록 설정**     |         |         |
|VMOnboarding 블레이드 - MMA 확장 만들기     | Microsoft.Compute/virtualMachines/write           | Virtual Machine        |
|저장된 검색 만들기/편집     | Microsoft.OperationalInsights/workspaces/write           | 작업 영역        |
|범위 구성 만들기/편집  | Microsoft.OperationalInsights/workspaces/write   | 작업 영역|

## <a name="custom-azure-automation-contributor-role"></a>사용자 지정 Azure Automation 기여자 역할

Microsoft는 Log Analytics 기여자 역할에서 Automation 계정 권한을 제거하려고 합니다. 현재 위에서 설명한 기본 제공 [Log Analytics 기여자](#log-analytics-contributor) 역할은 구독 [기여자](./../role-based-access-control/built-in-roles.md#contributor) 역할로 권한을 에스컬레이션할 수 있습니다. Automation 계정 실행 계정은 처음에 구독에 대한 기여자 권한으로 구성되므로 공격자가 새 Runbook을 만들고 구독에 대한 기여자로서 코드를 실행하는 데 사용할 수 있습니다.

이러한 보안 위험으로 인해 Automation 작업을 실행하는 데 Log Analytics 기여자 역할을 사용하지 않는 것이 좋습니다. 대신 Azure Automation 기여자 사용자 지정 역할을 만들고 Automation 계정과 관련된 작업에 사용합니다. 이 사용자 지정 역할을 만들려면 다음 단계를 수행합니다.

### <a name="create-using-the-azure-portal"></a>Azure Portal을 사용하여 만들기

다음 단계를 수행하여 Azure Portal에서 Azure Automation 사용자 지정 역할을 만듭니다. 자세한 알아보려면 [Azure 사용자 지정 역할](./../role-based-access-control/custom-roles.md)을 참조하세요.

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다. 로컬 컴퓨터 또는 Azure 스토리지 계정에 파일을 저장합니다. JSON 파일에서 **assignableScopes** 속성 값을 구독 GUID로 바꿉니다.

   ```json
   {
    "properties": {
        "roleName": "Automation Account Contributor (Custom)",
        "description": "Allows access to manage Azure Automation and its resources",
        "assignableScopes": [
            "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Authorization/*/read",
                    "Microsoft.Insights/alertRules/*",
                    "Microsoft.Insights/metrics/read",
                    "Microsoft.Insights/diagnosticSettings/*",
                    "Microsoft.Resources/deployments/*",
                    "Microsoft.Resources/subscriptions/resourceGroups/read",
                    "Microsoft.Automation/automationAccounts/*",
                    "Microsoft.Support/*"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
      }
   }
   ```

1. [Azure Portal을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](../role-based-access-control/custom-roles-portal.md#start-from-json)에 설명된 대로 나머지 단계를 완료합니다. [3단계: 기본 사항](../role-based-access-control/custom-roles-portal.md#step-3-basics)의 경우 다음 사항에 유의하세요.

    -  **사용자 지정 역할 이름** 필드에 **Automation 계정 기여자(사용자 지정)** 또는 이름 지정 표준과 일치하는 이름을 입력합니다.
    - **기준 권한** 의 경우 **JSON에서 시작** 을 선택합니다. 그런 다음, 이전에 저장한 사용자 지정 JSON 파일을 선택합니다.

1. 나머지 단계를 완료한 다음, 사용자 지정 역할을 검토하고 만듭니다. 사용자 지정 역할이 표시될 때까지 몇 분 정도 걸릴 수 있습니다.

### <a name="create-using-powershell"></a>PowerShell을 사용하여 만들기

다음 단계를 수행하여 PowerShell을 통해 Azure Automation 사용자 지정 역할을 만듭니다. 자세한 알아보려면 [Azure 사용자 지정 역할](./../role-based-access-control/custom-roles.md)을 참조하세요.

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다. 로컬 컴퓨터 또는 Azure 스토리지 계정에 파일을 저장합니다. JSON 파일에서 **AssignableScopes** 속성 값을 구독 GUID로 바꿉니다.

    ```json
    { 
        "Name": "Automation account Contributor (custom)",
        "Id": "",
        "IsCustom": true,
        "Description": "Allows access to manage Azure Automation and its resources",
        "Actions": [
            "Microsoft.Authorization/*/read",
            "Microsoft.Insights/alertRules/*",
            "Microsoft.Insights/metrics/read",
            "Microsoft.Insights/diagnosticSettings/*",
            "Microsoft.Resources/deployments/*",
            "Microsoft.Resources/subscriptions/resourceGroups/read",
            "Microsoft.Automation/automationAccounts/*",
            "Microsoft.Support/*"
        ],
        "NotActions": [],
        "AssignableScopes": [
            "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
        ] 
    } 
    ```

1. [Azure PowerShell을 사용하여 Azure 사용자 지정 역할 만들기 또는 업데이트](./../role-based-access-control/custom-roles-powershell.md#create-a-custom-role-with-json-template)에 설명된 대로 나머지 단계를 완료합니다. 사용자 지정 역할이 표시될 때까지 몇 분 정도 걸릴 수 있습니다.

## <a name="update-management-permissions"></a>업데이트 관리 권한

업데이트 관리를 사용하여 동일한 Azure AD(Azure Active Directory) 테넌트에서 또는 Azure Lighthouse를 사용하여 테넌트 간 여러 구독의 머신에 대해 업데이트 배포를 평가하고 예약할 수 있습니다. 다음 표에는 업데이트 배포를 관리하는 데 필요한 권한이 나와 있습니다.

|**리소스** |**역할** |**범위** |
|---------|---------|---------|
|Automation 계정 |[사용자 지정 Azure Automation 기여자 역할](#custom-azure-automation-contributor-role) |Automation 계정 |
|Automation 계정 |가상 머신 참가자  |계정의 리소스 그룹  |
|Log Analytics 작업 영역 Log Analytics 기여자|Log Analytics 작업 영역 |
|Log Analytics 작업 영역 |Log Analytics 독자|Subscription|
|해결 방법 |Log Analytics 참가자 |해결 방법|
|Virtual Machine |가상 머신 참가자 |Virtual Machine |
|**가상 머신에 대한 작업** | | |
|업데이트 일정 실행 기록 보기([소프트웨어 업데이트 구성 시스템 실행](/rest/api/automation/softwareupdateconfigurationmachineruns)) |읽기 권한자 |Automation 계정 |
|**가상 머신에 대한 작업** |**사용 권한** | |
|업데이트 일정 만들기([소프트웨어 업데이트 구성](/rest/api/automation/softwareupdateconfigurations)) |Microsoft.Compute/virtualMachines/write |정적 VM 목록 및 리소스 그룹인 경우 |
|업데이트 일정 만들기([소프트웨어 업데이트 구성](/rest/api/automation/softwareupdateconfigurations)) |Microsoft.OperationalInsights/workspaces/analytics/query/action |비 Azure 동적 목록을 사용할 때 작업 영역 리소스 ID인 경우|

## <a name="configure-azure-rbac-for-your-automation-account"></a>Automation 계정에 Azure RBAC 구성

다음 섹션에서는 [Azure Portal](#configure-azure-rbac-using-the-azure-portal) 및 [PowerShell](#configure-azure-rbac-using-powershell)을 통해 Automation 계정에서 Azure RBAC를 구성하는 방법을 보여줍니다.

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure RBAC 구성

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Automation 계정 페이지에서 Automation 계정을 엽니다.
2. **액세스 제어(IAM)** 를 클릭하여 액세스 제어(IAM) 페이지를 엽니다. 이 페이지에서는 새 사용자, 그룹 및 애플리케이션을 추가하여 Automation 계정을 관리하고 Automation 계정에 대해 구성할 수 있는 기존 역할을 볼 수 있습니다.
3. **역할 할당** 탭을 클릭합니다.

   ![액세스 단추](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>새 사용자 추가 및 역할 할당

1. 액세스 제어(IAM) 페이지에서 **+ 역할 할당 추가** 를 클릭합니다. 이렇게 하면 사용자, 그룹 또는 애플리케이션을 추가하고 역할을 할당할 수 있는 역할 할당 추가 페이지가 열립니다.

2. 사용 가능한 역할 목록에서 역할을 선택합니다. Automation 계정이 지원하는 기본 제공 역할이나 사용자가 정의한 사용자 지정 역할을 선택할 수 있습니다.

3. **선택** 필드에 권한을 부여하려는 사용자의 이름을 입력합니다. 목록에서 사용자를 선택하고 **저장** 을 클릭합니다.

   ![사용자 추가](media/automation-role-based-access-control/automation-04-add-users.png)

   사용자 페이지에 추가된 사용자가 표시되며, 선택한 역할이 할당된 것을 볼 수 있습니다.

   ![사용자 나열](media/automation-role-based-access-control/automation-05-list-users.png)

   역할 페이지에서 사용자에게 역할을 할당할 수도 있습니다.

4. 액세스 제어(IAM) 페이지에서 **역할** 을 클릭하여 역할 페이지를 엽니다. 역할의 이름과 해당 역할에 할당된 사용자 및 그룹의 수를 볼 수 있습니다.

    ![사용자 페이지에서 역할 할당](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > 역할 기반 액세스 제어는 Automation 계정 범위에서만 설정할 수 있으며 Automation 계정 아래의 리소스에는 설정할 수 없습니다.

#### <a name="remove-a-user"></a>사용자 제거

Automation 계정을 관리하지 않는 사용자 또는 더 이상 조직에서 일하지 않는 사용자의 액세스 권한을 제거할 수 있습니다. 다음은 사용자를 제거하는 단계입니다.

1. 액세스 제어(IAM) 페이지에서 제거할 사용자를 선택하고 **제거** 를 클릭합니다.
2. 할당 세부 정보 창에서 **제거** 단추를 클릭합니다.
3. **예** 를 클릭하여 제거를 확인합니다.

   ![사용자 제거](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>PowerShell을 사용하여 Azure RBAC 구성

다음 [Azure PowerShell cmdlet](../role-based-access-control/role-assignments-powershell.md)을 사용하여 Automation 계정에 역할 기반 액세스를 구성할 수도 있습니다.

[Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition)에 Azure Active Directory에서 사용할 수 있는 모든 Azure 역할이 나열됩니다. 이 cmdlet을 `Name` 매개 변수와 함께 사용하여 특정 역할에서 수행할 수 있는 모든 작업을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

예제 출력은 다음과 같습니다.

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)에 지정된 범위의 Azure 역할 할당이 나열됩니다. 이 cmdlet은 매개 변수 없이 해당 구독에서 이루어진 모든 역할 할당을 반환합니다. `ExpandPrincipalGroups` 매개 변수를 사용하여 지정된 사용자에 대한 액세스 할당과 해당 사용자가 속한 그룹을 나열합니다.

**예:** 다음 cmdlet을 사용하여 해당 Automation 계정에 속한 모든 사용자와 그 역할을 나열합니다.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

예제 출력은 다음과 같습니다.

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment)를 사용하여 특정 범위에서 사용자, 그룹 및 애플리케이션에 액세스 권한을 할당합니다.

**예:** 다음 명령을 사용하여 Automation 계정 범위의 사용자에 "Automation 운영자" 역할을 할당합니다.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

예제 출력은 다음과 같습니다.

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

[Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment)를 사용하여 특정 범위에서 지정된 사용자, 그룹 또는 애플리케이션의 액세스 권한을 제거합니다.

**예:** 다음 명령을 사용하여 Automation 계정 범위의 Automation 운영자 역할에서 사용자를 제거합니다.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

위 예제에서 `sign-in ID of a user you wish to remove`, `SubscriptionID`, `Resource Group Name`, `Automation account name`을 사용자의 계정 정보로 바꿉니다. 사용자 역할 할당 제거로 넘어가기 전에 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Automation 운영자 역할에 대한 사용자 환경 - Automation 계정

Automation 계정 범위에서 Automation 운영자 역할이 할당된 사용자가 본인이 할당된 Automation 계정을 볼 때 사용자는 해당 Automation 계정에서 생성된 Runbook, Runbook 작업 및 일정 목록만 볼 수 있고 이 사용자는 이러한 항목의 정의를 볼 수 없습니다. 사용자는 Runbook 작업을 시작, 중지, 일시 중단, 다시 시작 또는 예약할 수 있습니다. 그러나 사용자는 구성, Hybrid Runbook Worker 그룹 또는 DSC 노드와 같은 다른 Automation 리소스에는 액세스할 수 없습니다.

![리소스에 대한 액세스 권한 없음](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Runbook에 Azure RBAC 구성

Azure Automation을 사용하면 특정 Runbook에 Azure 역할을 할당할 수 있습니다. 이렇게 하려면 다음 스크립트를 실행하여 특정 Runbook에 사용자를 추가합니다. 이 스크립트는 Automation 계정 관리자 또는 테넌트 관리자가 실행할 수 있습니다.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

스크립트가 실행되면 사용자가 Azure Portal에 로그인하고 **모든 리소스** 를 선택하도록 합니다. 사용자는 목록에서 본인이 Automation Runbook 운영자로 추가된 Runbook을 볼 수 있습니다.

![포털에서 Runbook Azure RBAC](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Automation 운영자 역할에 대한 사용자 환경 - Runbook

Runbook 범위에서 Automation 운영자 역할이 할당된 사용자가 할당된 Runbook을 볼 때 사용자는 해당 Runbook을 시작하고 Runbook 작업을 볼 수만 있습니다.

![시작에만 액세스](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>다음 단계

* PowerShell을 사용하는 Azure RBAC에 대한 자세한 내용은 [Azure PowerShell을 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-powershell.md)를 참조하세요.
* Runbook 유형에 대한 자세한 내용은 [Azure Automation Runbook 유형](automation-runbook-types.md)을 참조하세요.
* Runbook을 시작하려면 [Azure Automation에서 Runbook 시작](start-runbooks.md)을 참조하세요.
