---
title: Azure Virtual Desktop(클래식) PowerShell - Azure
description: Azure Virtual Desktop(클래식) 테넌트 환경을 설정할 때 PowerShell 관련 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b90ed4a95103aca2962a9ea886353867668a0390
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818481"
---
# <a name="azure-virtual-desktop-classic-powershell"></a>Azure Virtual Desktop(클래식) PowerShell

> [!IMPORTANT]
> 이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../troubleshoot-powershell.md)를 참조하세요.

이 문서를 사용하여 Azure Virtual Desktop에서 PowerShell을 사용할 때 발생하는 오류 및 문제를 해결합니다. 원격 데스크톱 서비스 PowerShell에 대한 자세한 내용은 [Azure Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview)을 참조하세요.

## <a name="provide-feedback"></a>피드백 제공

제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Azure Virtual Desktop 서비스에 대해 토론하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하세요.

## <a name="powershell-commands-used-during-azure-virtual-desktop-setup"></a>Azure Virtual Desktop 설치 중에 사용되는 PowerShell 명령

이 섹션에서는 Azure Virtual Desktop을 설정하는 동안 일반적으로 사용되는 PowerShell 명령을 나열하고 이를 사용하는 동안 발생할 수 있는 문제를 해결하는 방법을 제공합니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>오류: Add-RdsAppGroupUser 명령 - 지정된 UserPrincipalName이 지정된 호스트 풀의 RemoteApp 앱 그룹에 이미 할당되어 있습니다.

```powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**원인:** 사용된 사용자 이름이 다른 유형의 앱 그룹에 이미 할당되었습니다. 동일한 세션 호스트 풀의 원격 데스크톱 및 원격 앱 그룹 모두에 사용자를 할당할 수 없습니다.

**해결 방법:** 사용자에게 원격 앱과 원격 데스크톱이 모두 필요한 경우, 여러 개의 호스트 풀을 만들거나 사용자에게 원격 데스크톱에 대한 액세스 권한을 부여하여 세션 호스트 VM에서 모든 애플리케이션을 사용하도록 허용합니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>오류: Add-RdsAppGroupUser 명령 - 지정된 UserPrincipalName이 원격 데스크톱 테넌트와 연결된 Azure Active Directory에 없습니다.

```powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**원인:** Azure Virtual Desktop 테넌트에 연결된 Azure Active Directory에서 -UserPrincipalName으로 지정된 사용자를 찾을 수 없습니다.

**해결 방법:** 다음 목록의 항목을 확인합니다.

- 사용자가 Azure Active Directory와 동기화됩니다.
- 사용자가 B2C(Business-to-Consumer) 또는 B2B(Business-to-Business) 상거래에 연결되어 있지 않습니다.
- Azure Virtual Desktop 테넌트가 올바른 Azure Active Directory에 연결되어 있습니다.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>오류: Get-RdsDiagnosticActivities - 사용자에게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

```powershell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**원인:** -TenantName 매개 변수

**해결 방법:** - TenantName \<TenantName>을 사용하여 Get-RdsDiagnosticActivities를 실행합니다.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>오류: Get-RdsDiagnosticActivities - 사용자에게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

```powershell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**원인:** -Deployment 스위치 사용

**해결 방법:** 배포 관리자만 -Deployment 스위치를 사용할 수 있습니다. 이러한 관리자는 일반적으로 원격 데스크톱 서비스/Azure Virtual Desktop 팀의 멤버입니다. -Deployment 스위치를 -TenantName \<TenantName>으로 바꿉니다.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>오류: New-RdsRoleAssignment - 사용자에게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

**원인 1:** 사용 중인 계정에 테넌트에 대한 원격 데스크톱 서비스 소유자 권한이 없습니다.

**해결 방법 1:** 원격 데스크톱 서비스 소유자 권한이 있는 사용자가 역할 할당을 실행해야 합니다.

**원인 2:** 사용 중인 계정에 원격 데스크톱 서비스 소유자 권한이 있지만 테넌트의 Azure Active Directory에 속하지 않거나 사용자가 있는 Azure Active Directory를 쿼리할 수 있는 권한이 없습니다.

**해결 방법 2:** Active Directory 권한이 있는 사용자가 역할 할당을 실행해야 합니다.

> [!NOTE]
> New-RdsRoleAssignment는 AD(Azure Active Directory)에 없는 사용자에게 권한을 부여할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop과 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview-2019.md)을 참조하세요.
- Azure Virtual Desktop 환경에서 테넌트와 호스트 풀을 만드는 동안 발생하는 문제를 해결하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues-2019.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration-2019.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection-2019.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](../troubleshoot-client.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup-2019.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
