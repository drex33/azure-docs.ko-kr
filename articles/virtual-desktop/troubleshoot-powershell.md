---
title: Azure Virtual Desktop PowerShell - Azure
description: Azure Virtual Desktop 환경을 설정할 때 PowerShell 관련 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 3426ff64bbaf1d3d4801a8fcc069cb1767091181
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804106"
---
# <a name="azure-virtual-desktop-powershell"></a>Azure Virtual Desktop PowerShell

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md)를 참조하세요.

이 문서를 사용하여 Azure Virtual Desktop에서 PowerShell을 사용할 때 발생하는 오류 및 문제를 해결합니다. 원격 데스크톱 서비스 PowerShell에 대한 자세한 내용은 [Azure Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview)을 참조하세요.

## <a name="provide-feedback"></a>피드백 제공

제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Azure Virtual Desktop 서비스에 대해 토론하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)를 방문하세요.

## <a name="powershell-commands-used-during-azure-virtual-desktop-setup"></a>Azure Virtual Desktop 설치 중에 사용되는 PowerShell 명령

이 섹션에서는 Azure Virtual Desktop을 설정하는 동안 일반적으로 사용되는 PowerShell 명령을 나열하고 이를 사용하는 동안 발생할 수 있는 문제를 해결하는 방법을 제공합니다.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>오류: New-AzRoleAssignment: 제공된 정보가 AD 개체 ID에 매핑되지 않습니다.

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**원인:** *-SignInName* 매개 변수로 지정된 사용자는 Azure Virtual Desktop 환경에 연결된 Azure Active Directory에서 찾을 수 없습니다.

**해결:** 다음 사항을 확인합니다.

- 사용자는 Azure Active Directory에 동기화되어야 합니다.
- 사용자는 B2C(Business-to-Consumer) 또는 B2B(Business-to-Business) 상거래에 얽매여서는 안 됩니다.
- Azure Virtual Desktop 환경은 올바른 Azure Active Directory에 연결되어야 합니다.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>오류: New-AzRoleAssignment: "개체 ID가 있는 클라이언트는 범위를 넘은 작업을 수행할 권한이 없습니다(코드: AuthorizationFailed)."

**원인 1:** 사용 중인 계정에 구독에 대한 소유자 권한이 없습니다.

**해결 1:** 소유자 권한이 있는 사용자가 역할 할당을 실행해야 합니다. 또는 사용자를 애플리케이션 그룹에 할당하려면 사용자 액세스 관리자 역할에 사용자를 할당해야 합니다.

**원인 2:** 사용 중인 계정에는 소유자 권한이 있지만 환경의 Azure Active Directory에 속하지 않거나 사용자가 있는 Azure Active Directory를 쿼리할 수 있는 권한이 없습니다.

**해결 2:** Active Directory 권한이 있는 사용자가 역할 할당을 실행해야 합니다.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>오류: New-AzWvdHostPool -- 리소스 종류에 대한 위치를 사용할 수 없습니다.

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

원인: Azure Virtual Desktop은 특정 위치에 서비스 메타데이터를 저장할 호스트 풀, 애플리케이션 그룹 및 작업 영역의 위치 선택을 지원합니다. 옵션은 이 기능을 사용할 수 있는 위치로 제한됩니다. 이 오류는 선택한 위치에서 기능을 사용할 수 없음을 의미합니다.

해결: 오류 메시지에서 지원되는 지역 목록이 게시됩니다. 대신 지원되는 지역 중 하나를 사용합니다.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>오류: New-AzWvdApplicationGroup은 호스트 풀과 동일한 위치에 있어야 합니다.

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**원인:** 위치가 일치하지 않습니다. 모든 호스트 풀, 애플리케이션 그룹 및 작업 영역에는 서비스 메타데이터를 저장할 위치가 있습니다. 서로 연결된 개체는 모두 동일한 위치에 있어야 합니다. 예를 들어 호스트 풀이 `eastus`에 있는 경우 애플리케이션 그룹도 `eastus`에 생성되어야 합니다. 이러한 애플리케이션 그룹을 등록할 작업 영역을 만드는 경우 해당 작업 영역도 `eastus`에 있어야 합니다.

**해결:** 호스트 풀이 만들어진 위치를 검색한 다음, 생성 중인 애플리케이션 그룹을 동일한 위치에 할당합니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Azure Virtual Desktop 환경 및 호스트 풀을 설정하는 동안 발생하는 문제를 해결하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
