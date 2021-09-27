---
title: Azure Monitor의 역할, 권한 및 보안
description: Azure Monitor에서 역할 및 사용 권한을 사용 하 여 모니터링 리소스에 대 한 액세스를 제한 하는 방법을 알아봅니다.
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b4335f3a6690ef502bdd495ea0de317eacfe738
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788689"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Azure Monitor의 역할, 권한 및 보안

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

많은 팀에서는 모니터링 데이터 및 설정에 대한 액세스를 엄격히 규제할 필요가 있습니다. 예를 들어 모니터링 (지원 엔지니어, DevOps 엔지니어)에 독점적으로 작업 하는 팀 멤버가 있거나 관리 서비스 공급자를 사용 하는 경우 모니터링 데이터에 대 한 액세스 권한만 부여 하는 것이 좋습니다. 리소스를 만들거나, 수정 하거나, 삭제 하는 기능을 제한 하는 것이 좋습니다. 

이 문서에서는 Azure의 사용자에 게 기본 제공 모니터링 역할을 신속 하 게 적용 하거나 제한 된 모니터링 권한이 필요한 사용자에 대해 고유한 사용자 지정 역할을 구축 하는 방법을 보여 줍니다. 그런 다음이 문서에서는 Azure Monitor 관련 리소스에 대 한 보안 고려 사항과 해당 리소스의 데이터에 대 한 액세스를 제한 하는 방법을 설명 합니다.

## <a name="built-in-monitoring-roles"></a>기본 제공 모니터링 역할
Azure Monitor의 기본 제공 역할은 인프라 모니터링 담당자가 필요한 데이터를 가져오고 구성할 수 있도록 하는 동시에 구독의 리소스에 대 한 액세스를 제한 하는 데 도움이 됩니다. Azure Monitor는 두 가지 기본 역할인 모니터링 판독기 및 모니터링 참가자를 제공 합니다.

### <a name="monitoring-reader"></a>Monitoring Reader
모니터링 독자 역할이 할당 된 사용자는 구독에 있는 모든 모니터링 데이터를 볼 수 있지만 리소스를 수정 하거나 모니터링 리소스와 관련 된 설정을 편집할 수 없습니다. 이 역할은 다음이 필요한 지원과 같은 조직의 사용자나 운영 엔지니어에게 적합합니다.

* Azure Portal에서 모니터링 대시보드를 봅니다.
* [Azure 경고](alerts/alerts-overview.md)에 정의 된 경고 규칙을 봅니다.
* [Azure Monitor REST API](/rest/api/monitor/metrics), [PowerShell cmdlet](powershell-samples.md)또는 [플랫폼 간 CLI](cli-samples.md)를 사용 하 여 메트릭을 쿼리 합니다.
* 포털, Azure Monitor REST API, PowerShell cmdlet 또는 플랫폼 간 CLI를 사용 하 여 활동 로그를 쿼리 합니다.
* 리소스에 대한 [진단 설정](essentials/diagnostic-settings.md) 을 확인합니다.
* 구독에 대한 [로그 프로필](essentials/activity-log.md#legacy-collection-methods) 을 봅니다.
* 자동 크기 조정 설정을 봅니다.
* 경고 활동 및 설정을 봅니다.
* Application Insights 데이터에 액세스 하 고 Application Insights 분석에서 데이터를 확인 합니다.
* 작업 영역에 대 한 사용 현황 데이터를 포함 하 여 Log Analytics 작업 영역 데이터를 검색 합니다.
* Log Analytics에서 관리 그룹을 봅니다.
* Log Analytics 작업 영역에서 검색 스키마를 검색 합니다.
* Log Analytics 작업 영역에 모니터링 팩을 나열 합니다.
* Log Analytics 작업 영역에서 저장 된 검색을 검색 및 실행 합니다.
* Log Analytics에 대 한 작업 영역 저장소 구성을 검색 합니다.

> [!NOTE]
> 이 역할은 이벤트 허브에 스트리밍되었거나 스토리지 계정에 저장된 로그 데이터에 대한 읽기 액세스를 부여하지 않습니다. 이러한 리소스에 대 한 액세스를 구성 하는 방법에 대 한 자세한 내용은이 문서 뒷부분의 [데이터 모니터링에 대 한 보안 고려 사항](#security-considerations-for-monitoring-data) 섹션을 참조 하세요 

### <a name="monitoring-contributor"></a>Monitoring Contributor
모니터링 참가자 역할이 할당 된 사용자는 구독의 모든 모니터링 데이터를 볼 수 있습니다. 모니터링 설정을 만들거나 수정할 수도 있지만 다른 리소스를 수정할 수는 없습니다. 

이 역할은 모니터링 읽기 권한자 역할의 상위 집합입니다. 이전에 언급 한 권한 외에도 조직의 모니터링 팀 또는 관리 서비스 공급자의 구성원에 게는 다음을 수행할 수 있어야 합니다.

* 포털의 모니터링 대시보드를 확인하고 자체 프라이빗 모니터링 대시보드를 만듭니다.
* 리소스에 대한 [진단 설정](essentials/diagnostic-settings.md)을 구성합니다.\*
* 구독에 대한 [로그 프로필](essentials/activity-log.md#legacy-collection-methods)을 설정합니다.\*
* [Azure 경고](alerts/alerts-overview.md)를 통해 경고 규칙 작업 및 설정을 설정 합니다.
* Application Insights에 대 한 웹 테스트 및 구성 요소를 만듭니다.
* Log Analytics 작업 영역에 대 한 공유 키를 나열 합니다.
* Log Analytics 작업 영역에서 모니터링 팩을 사용 하거나 사용 하지 않도록 설정 합니다.
* Log Analytics 작업 영역에서 저장 된 검색을 만들고 삭제 하 고 실행 합니다.
* Log Analytics에 대 한 작업 영역 저장소 구성을 만들고 삭제 합니다.

\*로그 프로필 또는 진단 설정을 설정 하려면 사용자에 게 대상 리소스 (저장소 계정 또는 이벤트 허브 네임 스페이스)에 대 한 ListKeys 권한도 별도로 부여 해야 합니다.

> [!NOTE]
> 이 역할은 이벤트 허브에 스트리밍되었거나 스토리지 계정에 저장된 로그 데이터에 대한 읽기 액세스를 부여하지 않습니다. 이러한 리소스에 대 한 액세스를 구성 하는 방법에 대 한 자세한 내용은이 문서 뒷부분의 [데이터 모니터링에 대 한 보안 고려 사항](#security-considerations-for-monitoring-data) 섹션을 참조 하세요 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>권한 및 Azure 사용자 지정 역할 모니터링
이전 기본 제공 역할이 팀의 정확한 요구를 충족 하지 않는 경우 더 세분화 된 권한으로 [Azure 사용자 지정 역할을 만들](../role-based-access-control/custom-roles.md) 수 있습니다. Azure Monitor에 대 한 일반적인 Azure RBAC (역할 기반 액세스 제어) 작업은 다음과 같습니다.

| 작업(Operation) | Description |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |작업 그룹을 읽거나, 쓰고, 삭제 합니다. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |활동 로그 경고를 읽거나, 쓰고, 삭제 합니다. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |클래식 경고에서 경고 규칙을 읽거나, 쓰고, 삭제 합니다. |
| Microsoft.Insights/AlertRules/Incidents/Read |경고 규칙에 대한 사건 나열(트리거된 경고 규칙 내역). 이는 포털에만 적용 됩니다. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |자동 크기 조정 설정 읽기, 쓰기 또는 삭제 |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |진단 설정 읽기, 쓰기 또는 삭제 |
| Microsoft.Insights/EventCategories/Read |활동 로그의 가능한 모든 범주를 나열합니다. Azure Portal에서 사용됩니다. |
| Microsoft.Insights/eventtypes/digestevents/Read |이 권한은 포털을 통해 활동 로그에 액세스 해야 하는 사용자에 게 필요 합니다. |
| Microsoft.Insights/eventtypes/values/Read |구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대 한 프로그래밍 방식 및 포털 액세스에 모두 적용 됩니다. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | 네트워크 흐름 로그에 대 한 진단 설정을 읽거나, 쓰고, 삭제 합니다. |
| Microsoft.Insights/LogDefinitions/Read |이 권한은 포털을 통해 활동 로그에 액세스 해야 하는 사용자에 게 필요 합니다. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |로그 프로필 읽기, 쓰기 또는 삭제 (활동 로그를 이벤트 허브 또는 저장소 계정으로 스트리밍) |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |거의 실시간 메트릭 경고를 읽거나, 쓰고, 삭제 합니다. |
| Microsoft.Insights/MetricDefinitions/Read |메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
| Microsoft.Insights/Metrics/Read |리소스에 대한 메트릭을 읽습니다. |
| Microsoft.Insights/Register/Action |Azure Monitor 리소스 공급자를 등록합니다. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Azure Monitor에서 로그 경고를 읽거나, 쓰고, 삭제 합니다. |

> [!NOTE]
> 리소스에 대 한 경고, 진단 설정 및 메트릭에 액세스 하려면 사용자에 게 해당 리소스의 리소스 유형 및 범위에 대 한 읽기 액세스 권한이 있어야 합니다. 저장소 계정 또는 이벤트 허브에 대 한 스트림에 보관 하는 진단 설정 또는 로그 프로필을 작성 (작성) 하려면 사용자에 게 대상 리소스에 대 한 ListKeys 권한도 있어야 합니다. 

예를 들어 앞의 표를 사용 하 여 다음과 같이 활동 로그 판독기에 대 한 Azure 사용자 지정 역할을 만들 수 있습니다.

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>모니터링 데이터에 대한 보안 고려 사항
모니터링 데이터, 특히 로그 파일에는 IP 주소나 사용자 이름 같은 중요 정보가 포함될 수 있습니다. Azure의 모니터링 데이터는 다음 3가지 기본 형태로 제공됩니다.

- 활동 로그는 Azure 구독의 모든 제어 평면 작업을 설명 합니다.
- 리소스 로그는 리소스에서 내보낸 로그입니다.
- 리소스에서 내보내는 메트릭

이러한 모든 데이터 형식은 저장소 계정에 저장 하거나 이벤트 허브로 스트리밍할 수 있으며, 둘 다 범용 Azure 리소스입니다. 범용 리소스이기 때문에 이 항목의 만들기, 삭제 및 액세스는 관리자에게 예약된 권한이 필요한 작업입니다. 오용을 방지하기 위해 모니터링 관련 리소스에는 다음 방법을 적용하는 것이 좋습니다.

* 모니터링 데이터에는 단일 전용 스토리지 계정을 사용합니다. 모니터링 데이터를 여러 스토리지 계정으로 분리해야 하는 경우 모니터링 데이터와 비 모니터링 데이터 간에 스토리지 계정의 사용량을 공유하지 마십시오. 이러한 방식으로 사용량을 공유하면 모니터링 데이터에만 액세스해야 하는 조직에 비모니터링 데이터에 대한 액세스 권한을 실수로 부여할 수 있습니다. 예를 들어 SIEM(보안 정보 및 이벤트 관리)을 위한 타사 조직은 모니터링 데이터에만 액세스해야 합니다.
* 이전 지점에서 설명한 것과 동일한 이유로 모든 진단 설정에서 단일 전용 서비스 버스 또는 이벤트 허브 네임스페이스를 사용합니다.
* 모니터링 관련 스토리지 계정 또는 이벤트 허브를 별도의 리소스 그룹에 유지하여 액세스를 제한합니다. 모니터링 역할의 [범위를 사용하여](../role-based-access-control/overview.md#scope) 해당 리소스 그룹에만 액세스를 제한합니다.
* 사용자가 모니터링 데이터에만 액세스해야 하는 경우 구독 범위에서 스토리지 계정 또는 이벤트 허브에 대한 ListKeys 권한을 부여하지 마십시오. 대신 리소스 또는 리소스 그룹 범위에서 사용자에게 이러한 권한을 부여합니다(전용 모니터링 리소스 그룹이 있는 경우).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>모니터링 관련 스토리지 계정에 대한 액세스 제한
사용자 또는 애플리케이션이 스토리지 계정의 모니터링 데이터에 액세스해야 하는 경우 Blob Storage에 대한 서비스 수준 읽기 전용 액세스 권한이 있는 모니터링 데이터를 포함하는 [SAS(공유 액세스 서명)를](/rest/api/storageservices/create-account-sas) 스토리지 계정에 생성해야 합니다. PowerShell에서 계정 SAS는 다음 코드와 같을 수 있습니다.

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

그런 다음, 해당 스토리지 계정에서 읽어야 하는 엔터티에 토큰을 제공할 수 있습니다. 엔터티는 해당 스토리지 계정의 모든 Blob을 나열하고 읽을 수 있습니다.

또는 Azure RBAC를 사용하여 이 권한을 제어해야 하는 경우 해당 엔터티에 해당 특정 스토리지 계정에 대한 권한을 부여할 수 `Microsoft.Storage/storageAccounts/listkeys/action` 있습니다. 이 권한은 스토리지 계정에 보관할 진단 설정 또는 로그 프로필을 설정할 수 있어야 하는 사용자에게 필요합니다. 예를 들어 하나의 스토리지 계정에서만 읽어야 하는 사용자 또는 애플리케이션에 대해 다음 Azure 사용자 지정 역할을 만들 수 있습니다.

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> ListKeys 권한이 있는 사용자는 기본 및 보조 스토리지 계정 키를 나열할 수 있습니다. 이러한 키는 해당 스토리지 계정의 서명된 모든 서비스(Blob, 큐, 테이블, 파일)에서 모든 서명된 권한(예: 읽기, 쓰기, Blob 만들기 및 Blob 삭제)을 사용자에게 부여합니다. 가능한 경우 계정 SAS를 사용하는 것이 좋습니다. 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>모니터링 관련 이벤트 허브에 대한 액세스 제한
이벤트 허브와 비슷한 패턴을 따를 수 있지만, 먼저 수신 대기를 위한 전용 권한 부여 규칙을 만들어야 합니다. 관련 모니터링 이벤트 허브를 수신 대기 하도록 하는 애플리케이션에 대한 액세스 권한을 부여 하려면 다음을 수행합니다.

1. 포털에서 수신 대기 클레임만 사용하여 모니터링 데이터를 스트리밍하기 위해 만든 이벤트 허브에 공유 액세스 정책을 만듭니다. 예를 들어 "monitoringReadOnly"라고 할 수 있습니다. 가능하면 해당 키를 소비자에게 직접 제공하고 다음 단계를 건너뜁니다.
2. 소비자가 임시로 키를 가져올 수 있어야 할 경우 해당 이벤트 허브에 대해 사용자에게 ListKeys 작업을 부여합니다. 이 단계는 진단 설정 또는 로그 프로필을 이벤트 허브로 스트리밍하도록 설정할 수 있어야 하는 사용자에게도 필요합니다. 예를 들어, Azure RBAC 규칙을 만들 수 있습니다.
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.EventHub/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.EventHub/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>보안 가상 네트워크 내에서 모니터링

Azure Monitor 사용하도록 설정하는 서비스를 제공하려면 Azure 리소스에 액세스해야 합니다. 공용 인터넷에 액세스할 수 없도록 보호하면서 Azure 리소스를 모니터링하려는 경우 보안 스토리지 계정을 사용할 수 있습니다.

모니터링 데이터는 스토리지 계정에 기록되는 경우가 많습니다. 권한이 없는 사용자가 스토리지 계정에 복사된 데이터에 액세스할 수 없도록 할 수 있습니다. 추가 보안을 위해 선택한 네트워크를 사용하도록 스토리지 계정을 제한하여 권한 있는 리소스와 신뢰할 수 있는 Microsoft 서비스 액세스 권한만 부여하도록 네트워크 액세스를 잠글 수 있습니다.

![방화벽 및 가상 네트워크에 대한 설정을 보여 주는 스크린샷.](./media/roles-permissions-security/secured-storage-example.png)

Azure Monitor 신뢰할 수 있는 Microsoft 서비스로 간주됩니다. 신뢰할 수 있는 **Microsoft 서비스 이 스토리지 계정에 액세스하도록 허용 확인란을** 선택하면 Azure Monitor에서 보안 스토리지 계정에 액세스할 수 있습니다. 그런 다음, 이러한 보호된 조건에서 Azure Monitor 리소스 로그, 활동 로그 및 메트릭을 스토리지 계정에 쓸 수 있습니다. 또한 이 설정을 사용하면 Log Analytics가 보안 스토리지에서 로그를 읽을 수 있습니다.   

자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Resource Manager Azure RBAC 및 권한에 대해 읽어보기](../role-based-access-control/overview.md)
* [Azure의 모니터링 개요 읽기](overview.md)

