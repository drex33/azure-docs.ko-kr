---
title: Azure Virtual Desktop(클래식) 진단 문제 - Azure
description: Azure Virtual Desktop(클래식) 진단 기능을 사용하여 문제를 진단하는 방법.
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 3a9d7f4f6c3413fe82cd1dde52ebde866d77eedf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262910"
---
# <a name="identify-and-diagnose-issues-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop(클래식)의 문제 식별 및 진단

>[!IMPORTANT]
>이 내용은 Azure Resource Manager의 Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../troubleshoot-set-up-overview.md)를 참조하세요.

Azure Virtual Desktop은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있도록 지원하는 진단 기능을 제공합니다. Azure Virtual Desktop 역할은 사용자가 시스템과 상호 작용할 때마다 진단 작업을 로깅합니다. 각 로그에는 트랜잭션에 개입한 Azure Virtual Desktop 역할, 오류 메시지, 테넌트 정보, 사용자 정보와 같은 관련 정보가 포함됩니다. 진단 작업은 최종 사용자 작업과 관리자 작업 양쪽에 의해 생성되며, 다음과 같은 세 가지 버킷으로 구분됩니다.

* 피드 구독 작업: 최종 사용자가 Microsoft 원격 데스크톱 애플리케이션을 통해 피드에 연결하려고 시도할 때마다 이 작업이 트리거됩니다.
* 연결 작업: 최종 사용자가 Microsoft 원격 데스크톱 애플리케이션을 통해 데스크톱 또는 RemoteApp에 연결하려고 시도할 때마다 이 작업이 트리거됩니다.
* 관리 작업: 관리자가 시스템에서 호스트 풀 만들기, 앱 그룹에 사용자 할당, 역할 할당 만들기와 같은 관리 작업을 수행할 때마다 이 작업이 트리거됩니다.

진단 역할 서비스 자체가 Azure Virtual Desktop의 일부이므로 Azure Virtual Desktop에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. Azure Virtual Desktop 연결 문제는 최종 사용자가 네트워크 연결 문제를 경험할 때 발생할 수 있습니다.

시작하려면 PowerShell 세션에서 사용할 수 있도록 [Azure Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>PowerShell 관련 진단 문제

Azure Virtual Desktop 진단은 하나의 PowerShell cmdlet만 사용하지만 문제의 범위를 좁히고 문제를 분리할 수 있도록 여러 선택적 매개 변수를 포함합니다. 다음 섹션에서는 문제 진단을 위해 실행할 수 있는 cmdlet을 안내합니다. 대부분의 필터는 함께 적용할 수 있습니다. `<tenantName>`과 같이 괄호로 묶인 값은 사용자의 상황에 맞는 값으로 바꾸어야 합니다.

>[!IMPORTANT]
>진단 기능은 단일 사용자의 문제 해결을 위한 기능입니다. PowerShell을 사용하는 모든 쿼리에는 *-UserName* 또는 *-ActivityID* 매개 변수를 포함해야 합니다. 모니터링용으로는 Log Analytics를 사용하세요. 작업 영역으로 진단 데이터를 보내는 방법은 [진단 기능에 Log Analytics 사용](diagnostics-log-analytics-2019.md)을 참조하세요.

### <a name="filter-diagnostic-activities-by-user"></a>사용자를 기준으로 진단 작업 필터링

**-UserName** 매개 변수는 다음 예제 cmdlet에서 볼 수 있듯이 지정된 사용자에 의해 시작된 진단 작업 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName** 매개 변수는 다른 선택적 필터링 매개 변수와 함께 조합할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-time"></a>시간을 기준으로 진단 작업 필터링

**-StartTime** 및 **-EndTime** 매개 변수를 사용하여, 반환된 진단 작업 목록을 필터링할 수 있습니다. **-StartTime** 매개 변수는 다음 예제에서 볼 수 있듯이 특정 날짜에 시작하는 진단 작업 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

cmdlet에 **-StartTime** 매개 변수와 함께 **-EndTime** 매개 변수를 추가하여 결과를 수신할 특정 기간을 지정할 수 있습니다. 다음 예제 cmdlet은 8월 1일과 8월 10일 사이의 진단 작업 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime** 및 **-EndTime** 매개 변수는 다른 선택적 필터링 매개 변수와 함께 조합할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-activity-type"></a>작업 유형을 기준으로 진단 작업 필터링

**-ActivityType** 매개 변수를 사용하여 작업 유형을 기준으로 진단 작업을 필터링할 수도 있습니다. 다음 cmdlet은 최종 사용자 연결 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

다음 cmdlet은 관리자 관리 작업 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

현재 **Get-RdsDiagnosticActivities** cmdlet은 ActivityType으로 Feed를 지정하는 것을 지원하지 않습니다.

### <a name="filter-diagnostic-activities-by-outcome"></a>결과를 기준으로 진단 작업 필터링

**-Outcome** 매개 변수를 사용하여, 반환된 진단 작업 목록을 결과를 기준으로 필터링할 수 있습니다. 다음 예제 cmdlet은 성공한 진단 작업 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

다음 예제 cmdlet은 실패한 진단 작업 목록을 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-Outcome** 매개 변수는 다른 선택적 필터링 매개 변수와 함께 조합할 수도 있습니다.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>작업 ID를 기준으로 특정 진단 작업 검색

**-ActivityId** 매개 변수는 다음 예제 cmdlet에서 볼 수 있듯이 특정 진단 작업이 존재하는 경우 반환합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>작업 ID를 기준으로 실패한 작업의 오류 메시지 보기

실패한 작업의 오류 메시지를 보려면 **-Detailed** 매개 변수를 사용하여 cmdlet을 실행해야 합니다. **Select-Object** cmdlet을 실행하여 오류 목록을 볼 수 있습니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>상세한 진단 작업 검색

**-Detailed** 매개 변수는 반환되는 각 진단 작업에 대한 추가 세부 정보를 제공합니다. 각 작업의 형식은 작업 유형에 따라 다릅니다. 다음 예제에서 볼 수 있듯이 모든 **Get-RdsDiagnosticActivities** 쿼리에 **-Detailed** 매개 변수를 추가할 수 있습니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

오류 시나리오는 서비스 내부 오류와 Azure Virtual Desktop 외부 오류로 분류됩니다.

* 내부 문제: 테넌트 관리자가 완화할 수 없고 지원 문제로서 해결해야 하는 시나리오를 지정합니다. [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 통해 피드백을 제공할 때는 작업 ID와 문제가 발생한 대략적인 시간 프레임을 포함합니다.
* 외부 문제: 시스템 관리자가 완화할 수 있는 시나리오와 관련이 있습니다. 이러한 문제는 Azure Virtual Desktop 외부적인 오류입니다.

다음 표에는 관리자가 직면할 수 있는 일반적인 오류가 나와 있습니다.

>[!NOTE]
>이 목록은 가장 일반적인 오류를 포함하며, 정기적으로 업데이트됩니다. 최신 정보를 살펴보려면 한 달에 한 번 이상 이 문서를 확인하세요.

### <a name="external-management-error-codes"></a>외부 관리 오류 코드

|숫자 코드|오류 코드|추천 솔루션|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|사용자가 Azure Active Directory의 구성원이 아닙니다. [Active Directory 관리 센터](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)의 지침에 따라 사용자를 추가하세요.|
|3|UnauthorizedAccess|관리 PowerShell cmdlet을 실행하려고 시도한 사용자가 사용자 이름을 잘못 입력했거나 사용자에게 권한이 없습니다.|
|1000|TenantNotFound|입력한 테넌트 이름이 기존 테넌트와 일치하지 않습니다. 테넌트 이름에 오타가 있는지 확인한 후 다시 시도하세요.|
|1006|TenantCannotBeRemovedHasSessionHostPools|개체를 포함하는 테넌트는 삭제할 수 없습니다. 먼저 세션 호스트 풀을 삭제한 후 다시 시도하세요.|
|2000|HostPoolNotFound|입력한 호스트 풀 이름이 기존 호스트 풀과 일치하지 않습니다. 호스트 풀 이름에 오타가 있는지 확인한 후 다시 시도하세요.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|개체를 포함하는 호스트 풀은 삭제할 수 없습니다. 먼저 호스트 풀에 있는 모든 앱 그룹을 제거하세요.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|세션 호스트 풀을 삭제하기 전에 먼저 모든 세션 호스트를 제거하세요.|
|5001|SessionHostNotFound|쿼리한 세션 호스트가 오프라인 상태일 수 있습니다. 호스트 풀의 상태를 확인하세요.|
|5008|SessionHostUserSessionsExist |원하는 관리 작업을 실행하기 전에 세션 호스트에서 모든 사용자를 로그아웃해야 합니다.|
|6000|AppGroupNotFound|입력한 앱 그룹 이름이 기존 앱 그룹과 일치하지 않습니다. 앱 그룹 이름에 오타가 있는지 확인한 후 다시 시도하세요.|
|6022|RemoteAppNotFound|입력한 RemoteApp 이름이 기존 RemoteApp과 일치하지 않습니다. RemoteApp 이름에 오타가 있는지 확인한 후 다시 시도하세요.|
|6010|PublishedItemsExist|게시하려는 리소스의 이름이 이미 있는 리소스와 같습니다. 리소스 이름을 변경한 후 다시 시도하세요.|
|7002|NameNotValidWhiteSpace|이름에 공백을 사용할 수 없습니다.|
|8000|InvalidAuthorizationRoleScope|입력한 역할 이름이 기존 역할 이름과 일치하지 않습니다. 역할 이름에 오타가 있는지 확인한 후 다시 시도하세요. |
|8001|UserNotFound |입력한 사용자 이름이 기존 사용자 이름과 일치하지 않습니다. 이름에 오타가 있는지 확인한 후 다시 시도하세요.|
|8005|UserNotFoundInAAD |입력한 사용자 이름이 기존 사용자 이름과 일치하지 않습니다. 이름에 오타가 있는지 확인한 후 다시 시도하세요.|
|8008|TenantConsentRequired|[이](tenant-setup-azure-active-directory.md#grant-permissions-to-azure-virtual-desktop) 지침에 따라 테넌트에 대한 동의를 제공하세요.|

### <a name="external-connection-error-codes"></a>외부 연결 오류 코드

|숫자 코드|오류 코드|추천 솔루션|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|사용자가 Active Directory의 구성원이 아닙니다. [Active Directory 관리 센터](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)의 지침에 따라 사용자를 추가하세요.|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|세션 호스트가 Active Directory에 올바르게 가입되어 있지 않습니다.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|세션 호스트를 사용할 수 없기 때문에 연결이 실패했습니다. 세션 호스트의 상태를 확인하세요.|
|-2146233088|ConnectionFailedClientDisconnect|이 오류가 자주 발생하면 사용자의 컴퓨터가 네트워크에 연결되어 있는지 확인하세요.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|호스트 사용자가 연결하려는 세션이 정상이 아닙니다. 가상 머신을 디버그하세요.|
|-2146233088|ConnectionFailedUserNotAuthorized|사용자가 게시된 앱 도는 데스크톱에 액세스할 권한이 없습니다. 이 오류는 관리자가 게시된 리소스를 제거한 경우 발생할 수 있습니다. 사용자에게 원격 데스크톱 애플리케이션에서 피드 새로 고침을 요청하세요.|
|2|FileNotFound|사용자가 액세스하려는 애플리케이션이 잘못 설치되었거나 잘못된 경로로 설정되었습니다.|
|3|InvalidCredentials|사용자가 입력한 사용자 이름 또는 암호가 기존 사용자 이름 또는 암호와 일치하지 않습니다. 자격 증명에 오타가 있는지 확인한 후 다시 시도하세요.|
|8|ConnectionBroken|클라이언트와 게이트웨이 또는 서버 사이의 연결이 끊어졌습니다. 예기치 않게 발생한 경우가 아닌 이상 조치가 필요하지 않습니다.|
|14|UnexpectedNetworkDisconnect|네트워크 연결이 끊어졌습니다. 사용자에게 다시 연결하라고 요청하세요.|
|24|ReverseConnectFailed|호스트 가상 머신에 RD 게이트웨이에 대한 직접적인 시야가 없습니다. 게이트웨이 IP 주소를 확인할 수 있는지 확인하세요.|
|1322|ConnectionFailedNoMappingOfSIDinAD|사용자가 Active Directory의 구성원이 아닙니다. [Active Directory 관리 센터](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)의 지침에 따라 사용자를 추가하세요.|

## <a name="next-steps"></a>다음 단계

Azure Virtual Desktop 내의 역할에 대해 자세히 알아보려면 [Azure Virtual Desktop 환경](environment-setup-2019.md)을 참조하세요.

Azure Virtual Desktop에 사용할 수 있는 PowerShell cmdlet 목록을 보려면 [PowerShell 참조](/powershell/windows-virtual-desktop/overview)를 참조하세요.